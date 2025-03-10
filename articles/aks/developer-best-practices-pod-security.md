---
title: 開發人員最佳做法 - Azure Kubernetes Services (AKS) 中的 Pod 安全性
description: 了解如何在 Azure Kubernetes Services (AKS) 中保護 Pod 的開發人員最佳做法
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: 412f27c572953b3f44ddca54a99f75895f438f21
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2018
ms.locfileid: "53559071"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Services (AKS) 中的 Pod 安全性最佳做法

當您在 Azure Kubernetes Service (AKS) 中開發和執行應用程式時，Pod 安全性是主要考量。 應用程式應設計為供需要最少量權限的主體使用。 保持私人資料安全是客戶最關切之事。 您不希望向外界公開資料庫連接字串、金鑰或祕密及憑證等認證，讓攻擊者將這些祕密用於惡意用途。 請不要將它們新增至程式碼，或內嵌於容器映像中。 因為需要重建容器映像，採用這種方法會暴露在風險下，並限制輪替這些認證的能力。

此最佳做法文章著重於如何保護AKS 中的 Pod。 您會了解如何：

> [!div class="checklist"]
> * 使用 Pod 資訊安全內容，限制存取處理序與服務或提升權限
> * 使用 Pod 受控身分識別，向其他 Azure 資源進行驗證
> * 向數位保存庫要求和擷取認證 (例如 Azure Key Vault)

您也可以閱讀適用於[叢集安全性][best-practices-cluster-security]與[容器映像管理][best-practices-container-image-management]的最佳做法。

## <a name="secure-pod-access-to-resources"></a>保護資源的 Pos 存取

**最佳做法指引** - 若要以不同使用者或群組來執行，並限制基礎節點處理序與服務的存取，請定義 Pod 資訊安全內容設定。 指派所需的最少量權限。

Pod 的執行身分應為定義的使用者或群組，而非「根」，您的應用程式才能正確執行。 Pod 或容器的 `securityContext` 可讓您定義如 *runAsUser* 或 *fsGroup* 的設定，取得適當的權限。 僅指派所需的使用者或群組的權限，並不使用資訊安全內容作為取得其他權限的方式。 當您的執行身分為非根使用者時，容器無法繫結至具特殊權限的連接埠 1024。 在此案例中，Kubernetes 服務可用來偽裝應用程式在特定連接埠上執行的事實。

Pod 資訊安全內容也可以定義存取處理序和服務的其他功能或權限。 您可以設定下列常見資訊安全內容定義：

* **allowPrivilegeEscalation** 定義 Pod 能否取得「根」權限。 設計應用程式時，此設定一律設為 *false*。
* **Linux 功能**可讓 Pod 存取基礎節點處理序。 請謹慎地指派這些功能。 指派所需的最少量權限。 如需詳細資訊，請參閱 [Linux 功能][linux-capabilities]。
* **SELinux 標籤**是 Linux 核心安全模組，可讓您定義服務、處理序及檔案系統存取的存取原則。 請再次指派所需的最少量權限。 如需詳細資訊，請參閱 [Kubernetes 中的 SELinux 選項][selinux-labels]

下列範例 Pod YAML 資訊清單會設定資訊安全內容來定義：

* Pod 的執行身分為使用者識別碼 *1000* 與群組識別碼 *2000* 的一部分
* 無法提升權限以使用 `root`
* 允許 Linux 功能存取網路介面和主機的系統 (硬體) 時鐘

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
    securityContext:
      runAsUser: 1000
      fsGroup: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

和叢集操作員一起決定您需要哪些資訊安全內容設定。 嘗試設計應用程式，以將 Pod 需要的其他權限和存取權降至最少。 使用叢集操作員可實作的 AppArmor 與 seccomp (安全運算)，還有其他安全性功能可限制存取。 如需詳細資訊，請參閱[保護容器對資源的存取][apparmor-seccomp]。

## <a name="limit-credential-exposure"></a>限制認證公開程度

**最佳做法指引** - 不在應用程式程式碼中定義認證。 使用 Azure 資源的受控身分識別，可讓 Pod 要求存取其他資源。 如 Azure Key Vault 的數位保存庫也應可用來儲存和擷取數位金鑰與認證。

若要限制在應用程式程式碼中公開認證的風險，請避免使用固定或共用認證。 認證或金鑰不應直接包含在程式碼中。 如果公開這些認證，就需要更新和重新部署應用程式。 較好的方法是提供 Pod 自己的身分識別和自我驗證方式，或從數位保存庫自動擷取認證。

AKS 包含自動驗證 Pod 或向數位保存庫要求認證與金鑰的兩種方式：

* Azure 資源的受控身分識別，以及
* Azure Key Vault FlexVol 驅動程式

### <a name="use-pod-managed-identities"></a>使用 Pod 受控身分識別

Azure 資源的受控身分識別可讓 Pod 向 Azure 中任何支援此功能的服務自我驗證，例如儲存體、SQL。 獲指派 Azure 身分識別的 Pod 可向 Azure Active Directory 進行驗證，並接收數位權杖。 此數位權杖可向其他 Azure 服務顯示，供其檢查是否已授權 Pod 存取服務和執行所需動作。 這種方法代表資料庫連接字串不需要任何祕密。 Pod 受控身分識別的簡化工作流程如下圖所示：

![Azure 中 Pod 受控身分識別的簡化工作流程](media/developer-best-practices-pod-security/basic-pod-identity.png)

使用受控身分識別，應用程式程式碼就不需要包含認證以存取服務時，例如 Azure 儲存體。 每個 Pod 都以自己的身分識別驗證，因此您可以稽核和檢閱存取權。 如果應用程式會與其他 Azure 服務連線，請使用受控身分識別來限制重複使用和公開認證的風險。

如需 Pod 身分識別的詳細資訊，請參閱[設定 AKS 叢集以使用 Pod 受控身分識別][aad-pod-identity]與[在程式碼中使用指派和使用 Pod 受控身分識別][aad-pod-identity]。

### <a name="use-azure-key-vault-with-flexvol"></a>搭配使用 Azure Key Vault 與 FlexVol

受控 Pod 身分識別非常適合用來向支援的 Azure 服務進行驗證。 您自己的服務或應用程式若沒有Azure 資源的受控身分識別，仍可使用認證或金鑰來進行驗證。 數位保存庫可用來儲存這些認證。

當應用程式需要認證時會與數位保存庫通訊，擷取最新的認證，然後再連線到所需的服務。 這個數位保存庫可以是 Azure Key Vault。 下圖顯示使用 Pod 受控身分識別從 Azure Key Vault 擷取認證的簡化工作流程：

![使用 Pod 受控身分識別從 Azure Key Vault 擷取認證的簡化工作流程](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

有了 Key Vault，您就可以儲存並定期輪替使用祕密，例如認證、儲存體帳戶金鑰或憑證。 您可以使用 FlexVolum 整合 Azure Key Vault 與 AKS 叢集。 FlexVolume 驅動程式可讓 AKS 叢集從 Key Vault 原生擷取認證，並只會將認證安全地提供給提出要求的 Pod。 請和叢集操作員一起將 Key Vault FlexVol 驅動程式部署到 AKS 節點。 您可以使用 Pod 受控身分識別向 Key Vault 要求存取權，並透過 FlexVolume 驅動程式擷取所需的認證。

## <a name="next-steps"></a>後續步驟

本文著重在如何保護您的 Pod。 若要實作這些部分的一些內容，請參閱下列文章：

* [搭配使用 Azure 資源的受控識別與 AKS][aad-pod-identity]
* [整合 Azure Key Vault 與 AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo-pod
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
