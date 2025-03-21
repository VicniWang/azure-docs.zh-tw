---
title: 在 Azure 上的 Jupyter Notebook 中安裝套件
description: 如何從 Azure 上執行的 Jupyter Notebook 內安裝 Python、R 和 F# 套件。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 5baa392d098b0b0e40986bc426c88785db025a29
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255280"
---
# <a name="install-packages-from-within-a-notebook"></a>從 Notebook 內安裝套件

雖然您可以設定[適用於專案層級上 Notebook 的環境](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)，還是建議您直接在個別 Notebook 內安裝套件。

從 Notebook 安裝的套件只會套用至目前的伺服器工作階段。 一旦伺服器關閉，就不會保存套件安裝。

## <a name="python"></a>Python

Python 中的套件可以使用 pip 或 conda，在程式碼資料格內使用命令來安裝：

```bash
!pip install <package_name>

!conda install <package_name> -y
```

如果命令輸出中表示已符合需求，則根據預設，Azure Notebooks 會包含套件。 套件也可以透過[專案環境設定步驟](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)來安裝。

## <a name="r"></a>R

R 中的套件可以透過 CRAN 或 GitHub，在程式碼資料格中使用 `install.packages` 函式來安裝：

```r
install.packages("package_name")
```

您也可以從 GitHub 使用 devtools 程式庫，安裝發行前版本和其他開發套件：

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

F# 中的套件可以藉由從程式碼資料格內呼叫 Paket 相依性管理員，從 [nuget.org](https://www.nuget.org) 安裝。 首先，載入 Paket 管理員：

```fsharp
#load "Paket.fsx"
```

然後安裝套件：

```fsharp
Paket.Package
[ "MathNet.Numerics"
"MathNet.Numerics.FSharp"
]
```

## <a name="next-steps"></a>後續步驟

- [操作說明：設定和管理專案](configure-manage-azure-notebooks-projects.md)
- [操作說明：放映投影片](present-jupyter-notebooks-slideshow.md)
