---
title: 在 Azure Cosmos DB 中使用預存程序、觸發程序及使用者定義函數
description: 本文說明 Azure Cosmos DB 中的預存程序、觸發程序及使用者定義函數等概念。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 89cc7906c0503daa11f0a34520c17552a4e6b5af
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454210"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>預存程序、觸發程序和使用者定義函式

Azure Cosmos DB 提供 JavaScript 的語言整合式、交易式執行。 在 Azure Cosmos DB 中使用 SQL API 時，您可以使用 JavaScript 語言撰寫**預存程序** **觸發程序**和**使用者定義函數 (UDF)**。 您可以使用 JavaScript 寫入在資料庫引擎內執行的邏輯。 您可以透過使用 [Azure 入口網站](https://portal.azure.com/)、[Azure Cosmos DB 中的 JavaScript 語言整合式查詢 API](javascript-query-api.md)，或 [Cosmos DB SQL API 用戶端 SDK](how-to-use-stored-procedures-triggers-udfs.md)，來建立與執行觸發程序、預存程序及 UDF。

## <a name="benefits-of-using-server-side-programming"></a>使用伺服器端程式設計的好處

以 JavaScript 撰寫預存程序、觸發程序和使用者定義函數 (UDF)，可讓您建置豐富應用程式，其優點如下：

* **程序邏輯：** JavaScript 是高階程式設計語言，可提供豐富且熟悉的介面來表達商務邏輯。 您可以對資料執行一連串的複雜作業。

* **不可部分完成的交易：** Azure Cosmos DB 可確保在單一預存程序或觸發程序內執行的資料庫作業為不可部分完成的作業。 這個不可部分完成的功能可讓應用程式將相關作業合併在單一批次中，讓所有作業不是一起成功就是一起失敗。

- **效能：** JSON 資料本身就對應至 JavaScript 語言類型系統。 此對應可提供許多最佳化功能，例如在緩衝集區中對 JSON 文件執行滯後具體化，並讓執行中程式碼可以依需求使用這些文件。 除此之外，還有其他與將商務邏輯傳送至資料庫相關聯的效能優點，包括：

   * *批次處理：* 您可以群組多個作業 (例如插入)，然後大量提交作業。 因此，網路流量延遲成本以及建立個別交易的額外儲存負荷得以大幅降低。

   * *預先編譯：* 預存程序、觸發程序和 UDF 會隱含地預先編譯成位元組程式碼格式，以避免在每次叫用指令碼時產生編譯成本。 由於預先編譯之故，因此能夠快速叫用預存程序，且只需耗費少量資源。

   * *排序：* 有時，操作需要一種觸發機制，可以對資料執行一次或多次更新。 在伺服器端上執行時，除了不可部分完成性外，還會有效能方面的優點。

- **封裝：** 預存程序可以用來將邏輯群組在一個位置。 封裝會在資料上方新增抽象層，讓您能夠發展您的應用程式，而不會動到資料。 當資料無結構描述，且您不需要管理直接在應用程式中新增的其他邏輯時，這個抽象層是很有幫助的。 這個抽象層讓您得以透過指令碼簡化存取來確保資料安全。

> [!TIP]
> 預存程序最適合頻繁寫入的作業。 當決定在何處使用預存程序時，盡可能將最大量的寫入封裝，進而最佳化。 一般而言，預存程序不是執行大量讀取作業最有效率的方法，因此使用預存程序來批次處理大量要傳回給用戶端的讀取，並不會產生所需的優點。

## <a name="transactions"></a>交易

一般資料庫中的交易可以定義為以單一工作邏輯單位執行的一連串作業。 每個交易都提供 **ACID 屬性保證**。 ACID 是眾所周知的縮寫，代表著：**A**tomicity (不可部分完成性)、**C**onsistency (一致性)、**I**solation (隔離性) 及 **D**urability (耐久性)。 

* 「不可部分完成性」保證會將交易內完成的所有作業視為單一單位，所有工作不是全部認可就是一個都不認可。 

* 「一致性」確保資料在交易中一律處於有效的狀態。 

* 「隔離性」保證兩個交易不會彼此干擾；許多商業系統都會提供多個可以根據應用程式的需要來使用的隔離等級。 

* 「耐久性」確保資料庫中所認可的任何變更一律會存在。

在 Azure Cosmos DB 中，JavaScript 執行階段會裝載在資料庫引擎中。 因此，在預存程序和觸發程序內提出的要求會與資料庫工作階段相同的範圍中執行。 此功能讓 Azure Cosmos DB 能夠為屬於預存程序或觸發程序一部分的所有作業保證 ACID 屬性。 如需範例，請參閱[如何實作交易](how-to-write-stored-procedures-triggers-udfs.md#transactions)文章。

### <a name="scope-of-a-transaction"></a>交易範圍

如果預存程序與 Azure Cosmos 容器相關聯，則預存程序會在邏輯資料分割索引鍵的交易範圍內執行。 每個預存程序執行必須包含邏輯分割索引鍵值，該值會對應交易的範圍。 如需詳細資訊，請參閱 [Azure Cosmos DB 資料分割](partition-data.md)文章。

### <a name="commit-and-rollback"></a>認可和回復

交易原本就整合至 Azure Cosmos DB JavaScript 程式設計模型。 在 JavaScript 函數內，會將所有作業自動包裝在單一交易內。 如果預存程序中儲存的 JavaScript 邏輯完成，而且沒有任何例外狀況，則會將交易內所有的作業認可至資料庫。 如 `BEGIN TRANSACTION` 與 `COMMIT TRANSACTION` (熟悉關聯式資料庫) 等陳述式在 Azure Cosmos DB 中是隱含的。 如果指令碼有任何的例外狀況，則 Azure Cosmos DB JavaScript 執行階段將會復原整個交易。 因此，擲回例外狀況其效力等同於 Azure Cosmos DB 中的 `ROLLBACK TRANSACTION`。

### <a name="data-consistency"></a>資料一致性

預存程序和觸發程序一律會在 Azure Cosmos 容器的主要複本上執行。 此功能確保從預存程序讀取的資料有[強式一致性](consistency-levels-tradeoffs.md)。 使用「使用者定義函數」的查詢可以在主要或任何次要複本上執行。 預存程序和觸發程序的目的是支援交易式寫入 – 而唯讀邏輯最適合做為應用程式端邏輯，而使用 [Azure Cosmos DB SQL API Sdk](sql-api-dotnet-samples.md) 查詢可幫助您將資料庫輸送量達成飽和。 

## <a name="bounded-execution"></a>界限執行

所有 Azure Cosmos DB 作業都必須在指定的逾時期間內完成。 此條件約束適用於 JavaScript 函數 - 預存程序、觸發程序和使用者定義函數。 如果作業未在該時限內完成，則會回復交易。

您可以確定 JavaScript 函數會在時限內完成，或實作接續式模型來批次處理/繼續執行。 為了簡化預存程序和觸發程序的開發流程以因應此時限，Azure Cosmos 容器下的所有函數 (例如建立、讀取、更新與刪除項目) 都會傳回布林值，以指出該作業是否會完成。 如果此值為 false，就表示此程序必須包裝執行，因為指令碼會比設定的值耗用更多時間或佈建的輸送量。 如果預存程序及時完成，而且佇列中已無其他要求，則在第一個不被接受之儲存作業之前排入佇列的作業保證會完成。 因此，應透過使用 JavaScript 的回呼慣例來管理指令碼的控制流程，一次將一個作業排入佇列。 由於指令碼是在伺服器端環境中執行的，因此受到嚴格控管。 一再違反執行界限的指令碼會標示為非使用中且無法執行，應該將它們重新建立以符合執行界限。

JavaScript 函數也受限於[佈建的輸送量容量](request-units.md)。 JavaScript 函數可能會在短時間內使用大量要求單位，如果達到佈建的輸送量容量限制，速率便會受到限制。 請務必注意，指令碼除了使用花費在執行資料庫作業的輸送量外，還有使用額外的輸送量，雖然與從用戶端執行相同的作業相比，這些資料庫作業還是稍微便宜一些。

## <a name="triggers"></a>觸發程序

本節說明兩種觸發程序：

### <a name="pre-triggers"></a>預先觸發程序

Azure Cosmos DB 提供的觸發程序可透過在 Azure Cosmos DB 項目上執行作業來叫用。 例如，當您正在建立項目時，可以指定預先觸發程序。 在此情況下，在建立項目之前，會先執行預先觸發程序。 預先觸發程序不能有任何輸入參數。 如有必要，要求物件可用來從原始要求來更新文件內文。 註冊觸發程序時，使用者可以指定與之搭配執行的作業。 如果觸發程序是使用 `TriggerOperation.Create` 建立的，這表示將不會允許在取代作業中使用觸發程序。 如需範例，請參閱[如何撰寫觸發程序](how-to-write-stored-procedures-triggers-udfs.md#triggers)文章。

### <a name="post-triggers"></a>後續觸發程序

後續觸發程序與預先觸發程序類似，都與 Azure Cosmos DB 項目上的作業相關聯，而且不需要任何輸入參數。 它們是在作業完成*之後*執行的，而且可以存取傳送給用戶端的回應訊息。 如需範例，請參閱[如何撰寫觸發程序](how-to-write-stored-procedures-triggers-udfs.md#triggers)文章。

## <a id="udfs"></a>使用者定義函數

使用者定義函數 (UDF) 可用來擴充 SQL API 查詢語言語法，並輕鬆地實作自訂商務邏輯。 只能在查詢內呼叫它們。 UDF 無法存取內容物件，只能做為計算用途的 JavaScript。 因此，UDF 可以在次要複本上執行。 如需範例，請參閱[如何撰寫使用者定義函數](how-to-write-stored-procedures-triggers-udfs.md#udfs)文章。

## <a id="jsqueryapi"></a>JavaScript 語言整合式查詢 API

除了使用 SQL API 查詢語法發出查詢外，[伺服器端 SDK](https://azure.github.io/azure-cosmosdb-js-server) 還可讓您使用 JavaScript 介面執行查詢，不需具備任何 SQL 的知識。 JavaScript 的查詢 API 可讓您將述詞函式依序傳遞至函式呼叫，藉此以程式設計方式建立查詢。 查詢由 JavaScript 執行階段剖析，並透過 Azure Cosmos DB 有效地執行。 若要深入了解 JavaScript 查詢 API 支援，請參閱[使用 JavaScript 語言整合式查詢 API](javascript-query-api.md) 文章。 如需範例，請參閱[如何使用 Javascript 查詢 API 寫入預存程序和解發程序](how-to-write-javascript-query-api.md)文章。

## <a name="next-steps"></a>後續步驟

透過下列文章了解如何在 Azure Cosmos DB 中撰寫和使用預存程序、觸發程序和使用者定義函數：

* [如何撰寫預存程序、觸發程序和使用者定義函數](how-to-write-stored-procedures-triggers-udfs.md)

* [如何使用預存程序、觸發程序和使用者定義函數](how-to-use-stored-procedures-triggers-udfs.md)

* [使用 JavaScript 語言整合式查詢 API](javascript-query-api.md)
