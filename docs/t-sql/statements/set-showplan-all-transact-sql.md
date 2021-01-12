---
description: SET SHOWPLAN_ALL (Transact-SQL)
title: SET SHOWPLAN_ALL (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/16/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SET SHOWPLAN_ALL
- SET_SHOWPLAN_ALL_TSQL
- SHOWPLAN_ALL
- SHOWPLAN_ALL_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- statements [SQL Server], estimates
- execution information and estimates [SQL Server]
- statements [SQL Server], information without processing
- SET SHOWPLAN_ALL statement
- SHOWPLAN_ALL option
- canceling statement execution
- stopping statement execution
- estimated execution information [SQL Server]
ms.assetid: a500b682-bae4-470f-9e00-47de905b851b
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 6a8cef73df3a6ced1cac91c997848cf28b966042
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093328"
---
# <a name="set-showplan_all-transact-sql"></a>SET SHOWPLAN_ALL (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  使 Microsoft [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 相反地，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回如何執行這些陳述式的詳細資訊，且會提供陳述式的資源需求估計。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
SET SHOWPLAN_ALL { ON | OFF }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註
 SET SHOWPLAN_ALL 的設定是在執行階段進行設定，而不是在剖析階段進行設定。  
  
 當 `SET SHOWPLAN_ALL` 為 ON 時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會在未執行陳述式的情況下，傳回每個陳述式的執行資訊，且不會執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 在這個選項設為 ON 之後，會傳回所有後續 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的相關資訊，直到這個選項設為 OFF 為止。 例如，如果執行 CREATE TABLE 陳述式時，`SET SHOWPLAN_ALL` 為 ON，則 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會從包含這份相同資料表的後續 SELECT 陳述式傳回錯誤訊息，通知使用者指定的資料表並不存在。 因此，後來參考這份資料表都會失敗。 當 SET SHOWPLAN_ALL 是 OFF 時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會在未產生報表的情況下，執行這些陳述式。  
  
 `SET SHOWPLAN_ALL` 旨在供為了處理其輸出而撰寫的應用程式使用。 請利用 SET SHOWPLAN_TEXT 來傳回 Microsoft Win32 命令提示字元應用程式 (如 **osql** 公用程式) 之可讀取的輸出。  
  
 在預存程序中，無法指定 SET SHOWPLAN_TEXT 和 SET SHOWPLAN_ALL；它們必須是批次中僅有的陳述式。  
  
 SET SHOWPLAN_ALL 會在一組資料列中傳回資訊，使這些資料列形成階層式樹狀結構，呈現出 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢處理器在執行每個陳述式時所採取的步驟。 輸出中所反映的每個陳述式都包含單一資料列，其中含有陳述式的文字，後面再接著幾個資料列，其中含有執行步驟的詳細資料。 下表顯示輸出所包含的資料行。  
  
|資料行名稱|描述|  
|-----------------|-----------------|  
|**StmtText**|對於每個類型不是 PLAN_ROW 的資料列，這個資料行都包含 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的文字。 對於類型是 PLAN_ROW 的資料列，這個資料行包含作業的說明。 這個資料行包含實體運算子，也可能選擇性地包含邏輯運算子。 這個資料行後面可能接著取決於實體運算子的說明。 如需詳細資訊，請參閱[執行程序邏輯和實體運算子參考](../../relational-databases/showplan-logical-and-physical-operators-reference.md)。|  
|**StmtId**|目前批次中的陳述式號碼。|  
|**NodeId**|目前查詢中的節點識別碼。|  
|**父系**|父步驟的節點識別碼。|  
|**PhysicalOp**|節點的實體實作演算法。 只適用於 PLAN_ROWS 類型的資料列。|  
|**LogicalOp**|這個節點所代表的關聯式代數運算子。 只適用於 PLAN_ROWS 類型的資料列。|  
|**Argument**|提供所執行之作業的補充資訊。 這個資料行的內容會隨著實體運算子而不同。|  
|**DefinedValues**|包含這個運算子導入的值清單 (以逗號分隔)。 這些值可能是在目前查詢中的計算運算式 (如在 SELECT 清單或 WHERE 子句中)，也可能是查詢處理器為了處理這項查詢而導入的內部值。 之後，就可以在這項查詢內的其他位置參考這些已定義的值。 只適用於 PLAN_ROWS 類型的資料列。|  
|**EstimateRows**|這個運算子產生的估計輸出資料列數。 只適用於 PLAN_ROWS 類型的資料列。|  
|**EstimateIO**|這個運算子的估計 I/O 成本*。 只適用於 PLAN_ROWS 類型的資料列。|  
|**EstimateCPU**|這個運算子的估計 CPU 成本*。 只適用於 PLAN_ROWS 類型的資料列。|  
|**AvgRowSize**|這個運算子所處理之資料列的估計平均資料列大小 (以位元組為單位)。|  
|**TotalSubtreeCost**|這項運算和所有子運算的估計 (累加) 成本*。|  
|**OutputList**|包含目前作業所保護之資料行的清單 (以逗號分隔)。|  
|**:::no-loc text="Warnings":::**|包含目前作業的相關警告訊息清單 (以逗號分隔)。 警告訊息可能包括含有一份資料行清單的 "NO STATS:()" 字串。 這則警告訊息表示查詢最佳化工具嘗試根據這個資料行的統計資料來做決策，但找不到任何統計資料。 目前，查詢最佳化工具必須進行猜測，結果可能會選取無效的查詢計畫。 如需如何建立或更新資料行統計資料 (可協助查詢最佳化工具選擇更有效的查詢計劃) 的詳細資訊，請參閱 [UPDATE STATISTICS](../../t-sql/statements/update-statistics-transact-sql.md)。 這個資料行可能選擇性地包括 "MISSING JOIN PREDICATE" 字串，表示正在聯結 (包含資料表)，但沒有聯結述詞。 意外卸除聯結述詞可能使查詢的執行時間遠遠超出預期，並傳回巨大的結果集。 如果出現這個警告，請確認是有意使聯結述詞不存在。|  
|**:::no-loc text="Type":::**|節點類型。 對每項查詢的父節點而言，這都是 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式類型 (如 SELECT、INSERT、EXECUTE 等等)。 對於代表執行計畫的子節點而言，類型是 PLAN_ROW。|  
|**Parallel**|**0** = 運算子並未並行執行。<br /><br /> **1** = 運算子正在並行執行。|  
|**EstimateExecutions**|執行目前查詢時，將執行這個運算子的估計次數。|  
|||

 *表示成本單位是根據內部時間度量，而不是根據時鐘的時間。 成本單位是用來判斷計畫的相對成本 (相較於其他計畫而言)。  
  
## <a name="permissions"></a>權限  
 若要使用 SET SHOWPLAN_ALL，您必須對 SET SHOWPLAN_ALL 執行所在之陳述式有適當的執行權限，且必須對包含所參考之物件的所有資料庫擁有 SHOWPLAN 權限。  
  
 對於 SELECT、INSERT、UPDATE、DELETE、EXEC *stored_procedure* 和 EXEC *user_defined_function* 陳述式，若要產生執行程序表，使用者必須：  
  
-   有執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的適當權限。  
  
-   有包含 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式所參考的物件 (如資料表、檢視等) 之所有資料庫的 SHOWPLAN 權限。  
  
 至於所有其他陳述式，例如 DDL、USE *database_name*、SET、DECLARE、動態 SQL 等等，只需要有執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的適當權限。  
  
## <a name="examples"></a>範例  
 下面兩個陳述式利用 SET SHOWPLAN_ALL 設定來顯示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 分析和最佳化查詢中之索引使用情況的方式。  
  
 第一個查詢在索引資料行的 WHERE 子句中，使用等於比較運算子 (=)。 如此一來，會在 **LogicalOp** 資料行中得出「叢集索引搜尋」值，而在 **Argument** 資料行中得出索引名稱。  
  
 第二個查詢在 WHERE 子句中使用 LIKE 運算子。 這會強制 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用叢集索引掃描，以及尋找符合 WHERE 子句條件的資料。 如此一來，會讓 **LogicalOp** 資料行中含「叢集檢索掃描」值、**Argument** 資料行中含索引名稱，以及 **LogicalOp** 資料行中含「篩選」值、**Argument** 資料行中含 WHERE 子句條件。  
  
 第一個索引查詢的 **EstimateRows** 和 **TotalSubtreeCost** 資料行中的值比較小，這表示它的處理速度比較快，使用的資源比非索引查詢少。  
  
```sql
USE AdventureWorks2012;  
GO  
SET SHOWPLAN_ALL ON;  
GO  
-- First query.  
SELECT BusinessEntityID   
FROM HumanResources.Employee  
WHERE NationalIDNumber = '509647174';  
GO  
-- Second query.  
SELECT BusinessEntityID, EmergencyContactID   
FROM HumanResources.Employee  
WHERE EmergencyContactID LIKE '1%';  
GO  
SET SHOWPLAN_ALL OFF;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SET SHOWPLAN_TEXT &#40;Transact-SQL&#41;](../../t-sql/statements/set-showplan-text-transact-sql.md)   
 [SET SHOWPLAN_XML &#40;Transact-SQL&#41;](../../t-sql/statements/set-showplan-xml-transact-sql.md)  
  
  
