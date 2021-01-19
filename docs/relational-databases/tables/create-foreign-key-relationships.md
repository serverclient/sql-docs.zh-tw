---
description: 建立外部索引鍵關聯性
title: 建立外部索引鍵關聯性 | Microsoft Docs
ms.custom: ''
ms.date: 06/19/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: vanto
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- relationships [SQL Server], creating
ms.assetid: 867a54b8-5be4-46e6-9702-49ae6dabf67c
author: stevestein
ms.author: sstein
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 933532a516f18aab01e1a32584590d65940c75b6
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170200"
---
# <a name="create-foreign-key-relationships"></a>建立外部索引鍵關聯性


[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

此文章說明如何使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 或 [!INCLUDE[tsql](../../includes/tsql-md.md)]，在 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中建立外部索引鍵關聯性。 當想要將一個資料表的資料列，與其他資料表的資料列建立相關時，可以建立兩者間的關聯性。

## <a name="permissions"></a>權限

建立具有外部索引鍵的新資料表，需要資料庫中的 [CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md) 權限及建立資料表的結構描述之 [ALTER](../../t-sql/statements/alter-schema-transact-sql.md) 權限。

在現有資料表中建立外部索引鍵需要此資料表的 [ALTER](../../t-sql/statements/alter-table-transact-sql.md) 權限。

## <a name="limits-and-restrictions"></a><a name="BeforeYouBegin"></a> 限制事項

- 外部索引鍵條件約束不一定只能連結到另一個資料表中的主索引鍵條件約束。 外部索引鍵也可以定義成參考另一個資料表中 UNIQUE 條件約束的資料行。
- 在 FOREIGN KEY 條件約束的資料行中輸入 NULL 以外的值時，值必須在參考的資料行中。 否則，系統會傳回外部索引鍵違規錯誤訊息。 若要確定會驗證複合外部索引鍵條件約束的所有值，請對所有參與的資料行指定 NOT NULL。
- FOREIGN KEY 條件約束只能參考在相同伺服器之相同資料庫內的資料表。 跨資料庫參考完整性必須利用觸發程序來實作。 如需詳細資訊，請參閱 [CREATE TRIGGER](../../t-sql/statements/create-trigger-transact-sql.md)。
- FOREIGN KEY 條件約束可以參考相同資料表中的另一個資料行，這稱為自我參考。
- 資料行層級上指定的 FOREIGN KEY 條件約束只能列出一個參考資料行。 這個資料行必須有定義了條件約束的資料行之相同資料類型。
- 資料表層級上指定的 FOREIGN KEY 條件約束，必須有與條件約束資料行清單中資料行一樣多的參考資料行。 每個參考資料行的資料類型，也必須與資料行清單中的對應資料行相同。
- [!INCLUDE[ssDE](../../includes/ssde-md.md)] 沒有預先定義可能包含資料表參考其他資料表的 FOREIGN KEY 條件約束數目限制。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 也不會限制參考特定資料表的其他資料表所擁有的 FOREIGN KEY 條件約束數目。 不過，FOREIGN KEY 條件約束的實際可用數目，會受到硬體設定及資料庫與應用程式設計的限制。 一個資料表最多可以參考其他 253 個資料表和資料行作為外部索引鍵 (連出參考)。 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 接著將單一資料表中資料行可以參考的其他資料表和資料行數目限制 (連入參考) 從 253 提高至 10,000。 (至少需要 130 相容性層級)。此增加具有下列限制：

  - DELETE 和 UPDATE DML 作業支援大於 253 的外部索引鍵參考數目。 但是，不支援 MERGE 作業。
  - 如果資料表具有參考本身的外部索引鍵，則仍會限制為 253 個外部索引鍵參考。
  - 目前，253 個以上的外部索引鍵參考數目不適用於資料行存放區索引、記憶體最佳化資料或 Stretch Database。

- 暫存資料表不會強制執行 FOREIGN KEY 條件約束。
- 如果在 CLR 使用者定義的類型資料行上定義外部索引鍵，類型的實作必須支援二進位順序。 如需詳細資訊，請參閱 [CLR 使用者定義型別](../../relational-databases/clr-integration-database-objects-user-defined-types/clr-user-defined-types.md)。
- 只有在所參考的主索引鍵也定義成 **varchar(max)** 類型時， **varchar(max)** 類型的資料行才能夠參與外部索引鍵條件約束。

## <a name="create-a-foreign-key-relationship-in-table-designer"></a>在資料表設計工具建立外部索引鍵關聯性

### <a name="using-sql-server-management-studio"></a>使用 SQL Server Management Studio

1. 在物件總管中，以滑鼠右鍵按一下位於關聯性之外部索引鍵端上的資料表，然後按一下 [設計]。

   資料表會在 [**資料表設計工具**](../../ssms/visual-db-tools/design-tables-visual-database-tools.md)中開啟。
2. 從 **[資料表設計工具]** 功能表中，按一下 **[關聯性]** 。
3. 在 [外部索引鍵關聯性] 對話方塊中，按一下 [加入]。

   關聯性會出現在 [選取的關聯性] 清單中，並顯示系統提供的名稱，格式為 FK_\<*tablename*>_\<*tablename*>，其中 *tablename* 為外部索引鍵資料表的名稱。
4. 在 [ **選取的關聯性** ] 清單中，按一下關聯性。
5. 按一下方格右邊的 [資料表及資料行規格]，然後按一下屬性右邊的省略符號 ( **...** )。
6. 在 [資料表和資料行] 對話視窗的 [主索引鍵] 下拉式清單中，選擇將要成為關聯性主索引鍵端的資料表。
7. 在方格的下方，選擇組成資料表主索引鍵的資料行。 在每個資料行右側的鄰近方格資料格，選擇對應到外部索引鍵資料表的外部索引鍵資料行。

   **[資料表設計工具]** 會提供關聯性的建議名稱。 若要變更這個名稱，請編輯 **[關聯性名稱]** 文字方塊的內容。
8. 選擇 **[確定]** 建立關聯性。
9. 關閉資料表設計工具視窗，並 **儲存** 您所做的變更，外部索引鍵關聯性變更才會生效。

## <a name="create-a-foreign-key-in-a-new-table"></a>在新的資料表建立外部索引鍵

### <a name="using-transact-sql"></a>使用 TRANSACT-SQL

下列範例會建立資料表並在 `TempID` 資料行上定義外部索引鍵條件約束，而此資料行會參考 AdventureWorks 資料庫 `Sales.SalesReason` 資料表中的 `SalesReasonID` 資料行。 ON DELETE CASCADE 和 ON UPDATE CASCADE 子句用來確定對 `Sales.SalesReason` 資料表所做的變更會自動傳播至 `Sales.TempSalesReason` 資料表。    

```sql
CREATE TABLE Sales.TempSalesReason 
   (
      TempID int NOT NULL, Name nvarchar(50)
      , CONSTRAINT PK_TempSales PRIMARY KEY NONCLUSTERED (TempID)
      , CONSTRAINT FK_TempSales_SalesReason FOREIGN KEY (TempID)
        REFERENCES Sales.SalesReason (SalesReasonID)
        ON DELETE CASCADE
        ON UPDATE CASCADE
   )
;
```

## <a name="create-a-foreign-key-in-an-existing-table"></a>在現有的資料表建立外部索引鍵

### <a name="using-transact-sql"></a>使用 TRANSACT-SQL
下列範例會在 `TempID` 資料行上建立外部索引鍵，並參考 AdventureWorks 資料庫 `Sales.SalesReason` 資料表中的 `SalesReasonID` 資料行。

```sql
ALTER TABLE Sales.TempSalesReason
   ADD CONSTRAINT FK_TempSales_SalesReason FOREIGN KEY (TempID)
      REFERENCES Sales.SalesReason (SalesReasonID)
      ON DELETE CASCADE
      ON UPDATE CASCADE
;
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱

- [主要與外部索引鍵條件約束](primary-and-foreign-key-constraints.md)
- [GRANT 資料庫權限](../../t-sql/statements/grant-database-permissions-transact-sql.md)
- [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)
- [CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md)
- [ALTER TABLE table_constraint](../../t-sql/statements/alter-table-table-constraint-transact-sql.md)。
