---
title: 在 Azure Active Directory 中撰寫屬性對應的運算式 | Microsoft Docs
description: 了解在 Azure Active Directory 中自動化佈建 SaaS 應用程式物件的期間，如何使用運算式對應將屬性值轉換成可接受的格式。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83a0685f75111a5552645d487589734846b05968
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56164629"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>在 Azure Active Directory 中撰寫屬性對應的運算式
當您設定佈建到 SaaS 應用程式時，您可以指定的其中一種屬性對應類型是運算式對應。 您必須撰寫類似指令碼的運算式，以便讓您將使用者的資料轉換成 SaaS 應用程式更能接受的格式。

## <a name="syntax-overview"></a>語法概觀
屬性對應的運算式語法是 Visual Basic for Applications (VBA) 函式。

* 整個運算式必須以函式定義，由函式名稱後面接著以括號括住的引數組成： <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* 您可以在函式內互相巢狀函式。 例如︰ <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* 您可以將三種不同類型的引數傳入函式：
  
  1. 屬性，必須以方括弧括住。 例如：[attributeName]
  2. 字串常數，必須以雙引號括住。 例如︰"United States"
  3. 其他函式。 例如︰FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* 對於字串常數，如果您在字串中需要反斜線 ( \ ) 或引號 ( " ) ，則必須使用反斜線 ( \ ) 符號逸出。 例如︰"Company name:\\"Contoso\\""

## <a name="list-of-functions"></a>函式的清單
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)

- - -
### <a name="append"></a>Append
**函式：**<br> Append(source, suffix)

**說明：**<br> 取出 source 字串值並在結尾附加尾碼。

**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |字串 |通常為 source 物件的屬性名稱。 |
| **suffix** |必要 |字串 |您想要附加至 source 值結尾的字串。 |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**函式：**<br>  FormatDateTime(source, inputFormat, outputFormat)

**說明：**<br> 從一種格式取出日期字串，並將它轉換成不同的格式。

**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |字串 |通常為 source 物件的屬性名稱。 |
| **inputFormat** |必要 |字串 |source 值的預期格式。 如需支援的格式，請參閱[https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)。 |
| **outputFormat** |必要 |字串 |輸出日期的格式。 |

- - -
### <a name="join"></a>Join
**函式：**<br> Join(separator, source1, source2, …)

**說明：**<br> Join() 類似 Append()，不過前者可以將多個 **source** 字串值合併成單一字串，且每個值會以 **separator** 字串分隔。

如果其中一個 source 值是多重值屬性，則該屬性中的每個值會聯結在一起，並以分隔符號值分隔。

**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **separator** |必要 |字串 |用來分隔串連成一個字串的 source 值的字串。 如果不需要分隔符號，可以是 ""。 |
| **source1  … sourceN** |必要，變動次數 |字串 |要聯結在一起的字串值。 |

- - -
### <a name="mid"></a>Mid
**函式：**<br> Mid(source, start, length)

**說明：**<br> 傳回 source 值的子字串。 子字串是只包含 source 字串某些字元的字串。

**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |字串 |通常為屬性的名稱。 |
| **start** |必要 |integer |子字串在 **source** 字串中起始位置的索引。 字串第一個字元的索引為 1，第二個字元的索引為 2，依此類推。 |
| **length** |必要 |integer |子字串的長度。 如果長度超出 **source** 字串結尾，函式會傳回從 **start** 索引一直到 **source** 字串結尾的子字串。 |

- - -
### <a name="normalizediacritics"></a>NormalizeDiacritics
**函式：**<br> NormalizeDiacritics(source)

**說明：**<br> 需要一個字串引數。 傳回字串，但是當中所有的變音符號字元皆被同等的非變音符號字元取代。 通常用於將包含變音符號字元 (重音符號) 的名字和姓氏，轉換成可用於如使用者主體名稱、SAM 帳戶名稱，與電子郵件地址等各種使用者識別碼中的有效值。

**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |字串 | 通常是名字或姓氏屬性。 |

- - -
### <a name="not"></a>否
**函式：**<br> Not(source)

**說明：**<br> 翻轉 **source** 的布林值。 如果 **source** 值為 "*True*"，則傳回 "*False*"。 反之則傳回 "*True*"。

**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |Boolean String |預期的 **source** 值為 "True" 或 "False"。 |

- - -
### <a name="replace"></a>Replace
**函式：**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**說明：**<br>
取代字串內的值。 根據提供的參數而以不同的方式運作：

* 提供 **oldValue** 和 **replacementValue** 時：
  
  * 以 replacementValue 取代 source 中所有的 oldValue 項目
* 提供 **oldValue** 和 **template** 時：
  
  * 以 **source** 值取代 **template** 中所有的 **oldValue** 項目
* 提供 **regexPattern**、**regexGroupName**、**replacementValue** 時：
  
  * 以 replacementValue 取代 source 字串中符合 oldValueRegexPattern 的所有值
* 提供 **regexPattern**、**regexGroupName**、**replacementPropertyName** 時：
  
  * 如果 **source** 沒有值，則傳回 **source**
  * 如果 **source** 有值，則使用 **regexPattern** 和 **regexGroupName** 從有 **replacementPropertyName** 的屬性擷取取代值。 結果會傳回取代值

**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |字串 |通常為 source 物件的屬性名稱。 |
| **oldValue** |選用 |字串 |在 **source** 或 **template** 中要被取代的值。 |
| **regexPattern** |選用 |字串 |在 **source**中要被取代的值的規則運算式模式。 或者，如果使用了 replacementPropertyName，要從取代屬性擷取值的模式。 |
| **regexGroupName** |選用 |字串 |**regexPattern**內的群組名稱。 只有當使用了 replacementPropertyName 時，我們才會從取代屬性擷取此群組的值做為 replacementValue。 |
| **replacementValue** |選用 |字串 |要取代舊值的新值。 |
| **replacementAttributeName** |選用 |字串 |當 source 沒有任何值時，要用於取代值的屬性名稱。 |
| **template** |選用 |字串 |提供 **template** 值時，我們會尋找 template 內的 **oldValue** 並以 source 值取代。 |

- - -
### <a name="selectuniquevalue"></a>SelectUniqueValue
**函式：**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**說明：**<br> 至少需要兩個引數，也就是使用運算式定義的唯一值產生規則。 此函式會評估每個規則，接著檢查所產生的值在目標應用程式/目錄中的唯一性。 將會傳回所找到的第一個唯一值。 如果所有值都已存在於目標中，則項目會進行委付且原因會記錄於稽核記錄中。 可提供的引數數目沒有上限。

> [!NOTE]
>1. 這是最上層函式，無法巢狀處理。
>2. 此函式只能用於建立項目。 搭配屬性使用此函式時，請將 [套用對應] 屬性設定為 [僅限物件建立期間]。


**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |至少需要 2 個，沒有上限 |字串 | 要評估的唯一值產生規則清單。 |


- - -
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**函式：**<br> SingleAppRoleAssignment([appRoleAssignments])

**說明：**<br> 需要一個字串引數。 傳回字串，但是當中所有的變音符號字元皆被同等的非變音符號字元取代。

**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |必要 |字串 |**[appRoleAssignments]** 物件。 |

- - -
### <a name="split"></a>Split
**函式：**<br> Split(source, delimiter)

**說明：**<br> 使用指定的分隔符號字元將字串分割成多重值陣列。

**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |字串 |要更新的 **source** 值。 |
| **delimiter** |必要 |字串 |指定將用來分割字串的字元 (範例：",") |

- - -
### <a name="stripspaces"></a>StripSpaces
**函式：**<br> StripSpaces(source)

**說明：**<br> 移除 source 字串中的所有空格 (" ") 字元。

**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |字串 |要更新的 **source** 值。 |

- - -
### <a name="switch"></a>Switch
**函式：**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**說明：**<br> 當 **source** 值符合某個 **key** 時，傳回該 **key** 的 **value**。 如果 **source** 值不符合任何 key，則傳回 **defaultValue**。  **key** 和 **value** 參數必須永遠成對出現。 函式必須要有偶數數目的參數。

**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |字串 |要更新的 **source** 值。 |
| **defaultValue** |選用 |字串 |當 source 不符合任何 key 時要使用的預設值。 可以是空字串 ("")。 |
| **key** |必要 |字串 |要與 **source** 值比較的 **key**。 |
| **value** |必要 |字串 |符合 key 的 **source** 的取代值。 |

- - -
### <a name="tolower"></a>ToLower
**函式：**<br> ToLower(source, culture)

**說明：**<br> 採用 *source* 字串值，並使用所指定的文化特性 (Culture) 規則將其轉換成小寫。 如果未指定任何 *culture* 資訊，則會使用不因文化特性而異。

**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |字串 |通常為 source 物件的屬性名稱 |
| **culture** |選用 |字串 |根據 RFC 4646，文化特性 (Culture) 名稱的格式為 *languagecode2-country/regioncode2*，其中 *languagecode2* 是兩個字母的語言代碼，而 *country/regioncode2* 則是兩個字母的子文化特性代碼。 範例包括 ja-JP 代表日文 (日本)，en-US 代表英文 (美國)。 如果沒有兩個字母的語言代碼可供使用，則會使用衍生自 ISO 639-2 的三個字母代碼。|

- - -
### <a name="toupper"></a>ToUpper
**函式：**<br> ToUpper(source, culture)

**說明：**<br> 採用 *source* 字串值，並使用所指定的文化特性 (Culture) 規則將其轉換成大寫。 如果未指定任何 *culture* 資訊，則會使用不因文化特性而異。

**參數：**<br> 

| Name | 必要 / 重複 | 型別 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |字串 |通常為 source 物件的屬性名稱。 |
| **culture** |選用 |字串 |根據 RFC 4646，文化特性 (Culture) 名稱的格式為 *languagecode2-country/regioncode2*，其中 *languagecode2* 是兩個字母的語言代碼，而 *country/regioncode2* 則是兩個字母的子文化特性代碼。 範例包括 ja-JP 代表日文 (日本)，en-US 代表英文 (美國)。 如果沒有兩個字母的語言代碼可供使用，則會使用衍生自 ISO 639-2 的三個字母代碼。|

## <a name="examples"></a>範例
### <a name="strip-known-domain-name"></a>刪去已知的網域名稱
您必須從使用者的電子郵件刪去已知的網域名稱，得到使用者名稱。 <br>
例如，如果網域是 "contoso.com"，您可以使用下列運算式：

**運算式：** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**範例輸入/輸出：** <br>

* **輸入** (mail)："john.doe@contoso.com"
* **輸出**："john.doe"

### <a name="append-constant-suffix-to-user-name"></a>附加常數尾碼到使用者名稱
如果您使用 Salesforce 沙箱，您可能需要附加額外的尾碼到您所有的使用者名稱，才能進行同步處理。

**運算式：** <br>
`Append([userPrincipalName], ".test")`

**範例輸入/輸出：** <br>

* **輸入**：(userPrincipalName)："John.Doe@contoso.com"
* **輸出**："John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>串連部分名字和姓氏產生使用者別名
您必須取出使用者名字的前 3 個字母和使用者姓氏的前 5 個字母來產生使用者別名。

**運算式：** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**範例輸入/輸出：** <br>

* **輸入** (givenName)："John"
* **輸入** (surname)："Doe"
* **輸出**："JohDoe"

### <a name="remove-diacritics-from-a-string"></a>移除字串中的變音符號
您必須以不含重音符號的同等字元取代含重音符號的字元。

**運算式：** <br>
NormalizeDiacritics([givenName])

**範例輸入/輸出：** <br>

* **輸入** (givenName)："Zoë"
* **輸出**："Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>將字串分割成多重值陣列
您必須採用以逗號分隔的字串清單，然後將其分割成可插入到多重值屬性 (例如 Salesforce 的 PermissionSets 屬性) 的陣列。 在此範例中，已在 Azure AD 的 extensionAttribute5 中填入權限集合清單。

**運算式：** <br>
Split([extensionAttribute5], ",")

**範例輸入/輸出：** <br>

* **輸入** (extensionAttribute5)："PermissionSetOne, PermisionSetTwo"
* **輸出**：["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>以特定格式將日期輸出為字串
您想要以特定格式傳送日期到 SaaS 應用程式。 <br>
例如，您要格式化 ServiceNow 的日期。

**運算式：** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**範例輸入/輸出：**

* **輸入** (extensionAttribute1)："20150123105347.1Z"
* **輸出**："2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>根據預先定義的一組選項取代值

您必須根據儲存在 Azure AD 中的狀態碼定義使用者的時區。 <br>
如果狀態碼不符合任何預先定義的選項，則使用 "Australia/Sydney" 的預設值。

**運算式：** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**範例輸入/輸出：**

* **輸入** (state)："QLD"
* **輸出**："Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>使用規則運算式來取代字元
您需要尋找符合規則運算式的字元，然後將它們移除。

**運算式：** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**範例輸入/輸出：**

* **輸入** (mailNickname: "john_doe72"
* **輸出**："72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>將產生的 userPrincipalName (UPN) 值轉換成小寫
在以下範例中，會將 PreferredFirstName 與 PreferredLastName 來源欄位串連來產生 UPN 值，然後 ToLower 函式會對產生的字串進行操作以將所有字元轉換成小寫。 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**範例輸入/輸出：**

* **輸入** (PreferredFirstName)："John"
* **輸入** (PreferredLastName)："Smith"
* **輸出**："john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>產生 userPrincipalName (UPN) 屬性的唯一值
根據使用者的名字、中間名和姓氏，您必須先產生 UPN 屬性的值並檢查其在目標 AD 目錄中的唯一性，再將此值指派給 UPN 屬性。

**運算式：** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com")
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**範例輸入/輸出：**

* **輸入** (PreferredFirstName)："John"
* **輸入** (PreferredLastName)："Smith"
* **輸出**：如果 John.Smith@contoso.com 的 UPN 值尚未存在於目錄中，則為 "John.Smith@contoso.com"
* **輸出**：如果 John.Smith@contoso.com 的 UPN 值已存在於目錄中，則為 "J.Smith@contoso.com"
* **輸出**：如果以上兩個 UPN 值已存在於目錄中，則為 "Jo.Smith@contoso.com"

## <a name="related-articles"></a>相關文章
* [自動化 SaaS 應用程式使用者佈建/解除佈建](user-provisioning.md)
* [自訂使用者佈建的屬性對應](customize-application-attributes.md)
* [適用於使用者佈建的範圍篩選器](define-conditional-rules-for-provisioning-user-accounts.md)
* [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](use-scim-to-provision-users-and-groups.md)
* [帳戶佈建通知](user-provisioning.md)
* [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)
