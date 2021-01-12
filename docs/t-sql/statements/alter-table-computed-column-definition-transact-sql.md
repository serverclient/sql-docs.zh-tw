---
description: ALTER TABLE computed_column_definition (Transact-SQL)
title: computed_column_definition (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/05/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- ALTER TABLE statement
ms.assetid: 746eabda-3b4f-4940-b0b5-1c379f5cf7a5
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 1ba855c43f68cc06cf8c9b1d5fc89168713cfaa2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100217"
---
# <a name="alter-table-computed_column_definition-transact-sql"></a>ALTER TABLE computed_column_definition (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  指定計算資料行的屬性；這些計算資料行是使用 [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md) 新增至資料表。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
column_name AS computed_column_expression  
[ PERSISTED [ NOT NULL ] ]  
[   
    [ CONSTRAINT constraint_name ]  
    { PRIMARY KEY | UNIQUE }  
        [ CLUSTERED | NONCLUSTERED ]  
        [ WITH FILLFACTOR = fillfactor ]  
        [ WITH ( <index_option> [, ...n ] ) ]  
        [ ON { partition_scheme_name ( partition_column_name ) | filegroup   
            | "default" } ]  
    | [ FOREIGN KEY ]   
        REFERENCES ref_table [ ( ref_column ) ]   
        [ ON DELETE { NO ACTION | CASCADE } ]   
        [ ON UPDATE { NO ACTION } ]   
        [ NOT FOR REPLICATION ]   
    | CHECK [ NOT FOR REPLICATION ] ( logical_expression )  
]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*column_name*  
 要改變、加入或卸除的資料欄名稱。 *column_name* 可以是 1 至 128 個字元。 若是以 **timestamp** 資料類型建立的新資料行，則可以省略 *column_name*。 如果沒有為 **timestamp** 資料類型資料行指定任何 *column_name*，則會使用 **timestamp** 這個名稱。  
  
*computed_column_expression*  
 這是定義計算資料行值的運算式。 計算資料行是一個虛擬資料行，並未實際儲存在資料表中，而是從使用相同資料表之其他資料行的運算式計算出來的。 運算式必須產生值。 例如，計算資料行可能會有 cost AS price * qty 這類定義。位元運算子的另一個範例：is_finalised AS is_checked | is_approved。 這個運算式可以是非計算的資料行名稱、常數、函式、變數，以及一或多個運算子所連接這些項目的任何組合。 運算式不能是搜尋條件、子查詢，也不能包括別名資料類型。  
  
 計算資料行可用在選取清單、WHERE 子句、ORDER BY 子句中，或任何能夠使用規則運算式的其他位置中，但下列狀況例外：  
  
-   計算資料行不能用來做為 DEFAULT 或 FOREIGN KEY 條件約束定義，也不能搭配 NOT NULL 條件約束定義來使用。 不過，如果計算資料行值是由具決定性的運算式所定義，且索引資料行接受結果的資料類型，計算資料行便可用來做為索引中的索引鍵資料行，也可用在任何 PRIMARY KEY 或 UNIQUE 條件約束中。  
  
     例如，如果資料表有整數資料行 a 和 b，您可以建立計算資料行 a + b 的索引，但不能建立計算資料行 a +DATEPART(dd, GETDATE()) 的索引，因為在後續叫用時，值可能會改變。  
  
-   計算資料行不能是 INSERT 或 UPDATE 陳述式的目標。  
  
    > [!NOTE]  
    >  因為對於計算資料行所涉及的資料行，資料表中的每個資料列都可能有不同的值，所以每個資料列的計算資料行可能各有不同的結果。  
  
PERSISTED  
 指定 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會實際將計算值儲存在資料表中，以及在計算資料行所依賴的任何其他資料行有了更新時，也會更新這些值。 將計算資料行標示為 PERSISTED，可讓您在具決定性但不精確的計算資料行上建立索引。 如需詳細資訊，請參閱 [計算資料行的索引](../../relational-databases/indexes/indexes-on-computed-columns.md)。 任何用為分割資料表之分割資料行的計算資料行，都必須明確標示為 PERSISTED。 指定 PERSISTED 時，*computed_column_expression* 必須具決定性。 

NULL | NOT NULL  
 指定資料行是否允許使用 Null 值。 NULL 並不是嚴格的條件約束，但您也可以依照 NOT NULL 的相同方式來指定它。 只有在也指定了 PERSISTED 時，計算資料行才能指定 NOT NULL。  
  
CONSTRAINT  
 指定開始定義 PRIMARY KEY 或 UNIQUE 條件約束。  
  
*constraint_name*  
 這是新的條件約束。 條件約束名稱必須遵照[識別碼](../../relational-databases/databases/database-identifiers.md)的規則，不過，名稱開頭不能是數字符號 (#)。 如果未提供 *constraint_name*，就會將系統產生的名稱指派給條件約束。  
  
PRIMARY KEY  
 這是一個條件約束，它會利用唯一索引來強制執行一個或多個指定之資料行的實體完整性。 每份資料表都只能建立一個 PRIMARY KEY 條件約束。  
  
UNIQUE  
 這是一個條件約束，它利用唯一索引來提供一個或多個特定資料行的實體完整性。  
  
CLUSTERED | NONCLUSTERED  
 指定建立 PRIMARY KEY 或 UNIQUE 條件約束的叢集或非叢集索引。 PRIMARY KEY 條件約束預設為 CLUSTERED。 UNIQUE 條件約束預設為 NONCLUSTERED。  
  
 如果叢集條件約束或索引已在資料表中，就無法指定 CLUSTERED。 如果叢集條件約束或索引已在資料表中，PRIMARY KEY 條件約束便預設為 NONCLUSTERED。  
  
WITH FILLFACTOR =*fillfactor*  
 指定用來儲存索引資料的每個索引頁面，[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 所應加以填滿的程度。 使用者指定的 *fillfactor* 可以是從 1 到 100 的值。 如果未指定值，預設值為 0。  
  
> [!IMPORTANT]  
>  為了與舊版相容，我們保持將 WITH FILLFACTOR = *fillfactor* 記載為適用於 PRIMARY KEY 或 UNIQUE 條件約束的唯一索引選項，但未來版本的文件不會再依照這個方式來說明。 您可以在 ALTER TABLE 的 [index_option &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-index-option-transact-sql.md) 子句中，指定其他索引選項。  
  
FOREIGN KEY REFERENCES  
 這是一個條件約束，它提供一個或多個資料行中之資料的參考完整性。 FOREIGN KEY 條件約束要求資料行中的每個值存在於所參考之資料表中的一個或多個對應的被參考資料行中。 FOREIGN KEY 條件約束所參考的資料行，必須是所參考的資料表中的 PRIMARY KEY 或 UNIQUE 條件約束，或是所參考的資料表之 UNIQUE INDEX 中所參考的資料行。 計算資料行的外部索引鍵也必須標示為 PERSISTED。  
  
*ref_table*  
 這是 FOREIGN KEY 條件約束所參考的資料表名稱。  
  
(*ref_column* )  
 這是 FOREIGN KEY 條件約束所參考之資料表中的資料行。  
  
ON DELETE { **NO ACTION** | CASCADE }  
 指定如果資料表中之資料列有參考關聯性，且在父資料表中刪除了所參考的資料列，資料表中之資料列會發生什麼動作。 預設值是 NO ACTION。  
  
NO ACTION  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會產生一則錯誤，且會回復父資料表中之資料列的刪除動作。

CASCADE  
 如果從父資料表中刪除資料列，便會從進行參考的資料表中刪除對應的資料列。  
  
 例如，在 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫中，ProductVendor 資料表與 Vendor 資料表有參考關聯性。 ProductVendor.BusinessEntityID 外部索引鍵會參考 Vendor.BusinessEntityID 主索引鍵。  
  
 如果在 Vendor 資料表的某個資料列上執行 DELETE 陳述式，且指定了 ProductVendor.BusinessEntityID 的 ON DELETE CASCADE 動作，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 便會檢查 ProductVendor 資料表中的一個或多個相依資料列。 如果有任何相依的資料列存在，除了 Vendor 資料表中所參考的資料列，還會刪除 ProductVendor 資料表中的相依資料列。  
  
 相反地，如果指定了 NO ACTION，當 ProductVendor 資料表中至少有一個資料列參考 Vendor 資料列時，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 便會產生一則錯誤，並會回復該資料列的刪除動作。  
  
 如果資料表要包含在使用邏輯記錄的合併式發行集中，請勿指定 CASCADE。 如需邏輯記錄的詳細資訊，請參閱[使用邏輯記錄分組相關資料列的變更](../../relational-databases/replication/merge/group-changes-to-related-rows-with-logical-records.md)。  
  
ON UPDATE { **NO ACTION** }  
 指定當建立的資料表中之資料列有參考關聯性，且在父資料表中所參考的資料列有了更新時，這些資料列會發生什麼動作。 當指定了 NO ACTION 時，如果 ProductVendor 資料表中至少有一個資料列參考 Vendor 資料列，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 便會產生一則錯誤，並會回復 Vendor 資料列的更新動作。  
  
NOT FOR REPLICATION  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 可以指定給 FOREIGN KEY 條件約束和 CHECK 條件約束。 如果條件約束指定了這個子句，當複寫代理程式執行插入、更新或刪除作業時，不會強制執行這個條件約束。  
  
CHECK  
 這是一個條件約束，藉由限制可能輸入一個或多個資料行的值，強制執行範圍完整性。 計算資料行的 CHECK 條件約束也必須標示 PERSISTED。  
  
*logical_expression*  
 這是一個傳回 TRUE 或 FALSE 的邏輯運算式。 這個運算式不能包含指向別名資料類型的參考。  
  
ON { *partition_scheme_name*(*partition_column_name*) | *filegroup*| "default"}  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指定條件約束所建立之索引的儲存位置。 如果指定 *partition_scheme_name*，索引就會進行資料分割，這些資料分割會對應於 *partition_scheme_name* 所指定的檔案群組。 如果指定了 *filegroup*，就會在具名檔案群組中建立索引。 如果指定了 "default"，或完全未指定 ON，索引就會建立在資料表的相同檔案群組中。 如果加入 PRIMARY KEY 或 UNIQUE 條件約束的叢集索引時指定了 ON，則建立叢集索引時，會將整份資料表移到指定的檔案群組中。  
  
> [!NOTE]  
>  在此內容中，default 不是關鍵字。 它是預設檔案群組的識別碼，必須加以分隔，例如 ON "default" 或 ON [default]。 如果指定了 "default"，目前工作階段的 QUOTED_IDENTIFIER 選項就必須是 ON。 這是預設值。 如需詳細資訊，請參閱 [SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/set-quoted-identifier-transact-sql.md)。  
  
## <a name="remarks"></a>備註  
 每個 PRIMARY KEY 和 UNIQUE 條件約束都會產生一個索引。 UNIQUE 和 PRIMARY KEY 條件約束數目無法使資料表的索引數目超出 999 個非叢集索引和 1 個叢集索引。  
  
## <a name="see-also"></a>另請參閱  
 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md)  
