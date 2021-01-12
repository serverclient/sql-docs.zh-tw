---
description: SET SHOWPLAN_TEXT (Transact-SQL)
title: SET SHOWPLAN_TEXT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SHOWPLAN_TEXT
- SET_SHOWPLAN_TEXT_TSQL
- SET SHOWPLAN_TEXT
- SHOWPLAN_TEXT_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- statements [SQL Server], estimates
- execution information and estimates [SQL Server]
- statements [SQL Server], information without processing
- SET SHOWPLAN_TEXT statement
- canceling statement execution
- SHOWPLAN_TEXT option
- stopping statement execution
- estimated execution information [SQL Server]
ms.assetid: 2c4f3fc8-ff2c-4790-8b74-e7e8ef58f9a6
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: a3bb68bfe4f6a1feeb43e1c82041cb2db11be384
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093308"
---
# <a name="set-showplan_text-transact-sql"></a>SET SHOWPLAN_TEXT (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  使 Microsoft [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 相反地，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回如何執行這些陳述式的詳細資料。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
SET SHOWPLAN_TEXT { ON | OFF }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註
 SET SHOWPLAN_TEXT 的設定是在執行階段進行設定，而不是在剖析階段進行設定。  
  
 當 SET SHOWPLAN_TEXT 是 ON 時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 無需執行每一項 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，即可傳回其執行資訊。 在這個選項設為 ON 之後，會傳回所有後續 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 陳述式的執行計畫相關資訊，直到這個選項設為 OFF 為止。 例如，如果執行 CREATE TABLE 陳述式時，SHOWPLAN_TEXT 是 ON，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會從包含這份相同資料表的後續 SELECT 陳述式傳回錯誤訊息，通知使用者指定的資料表並不存在。 因此，後來參考這份資料表都會失敗。 當 SET SHOWPLAN_TEXT 是 OFF 時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會直接執行陳述式，而不會產生內含執行計畫資訊的報表。  
  
 SET SHOWPLAN_TEXT 是要用來傳回 Microsoft Win32 命令提示字元應用程式 (例如 **sqlcmd** 公用程式) 的可讀取輸出。 SET SHOWPLAN_ALL 會傳回詳細的輸出，用來搭配專為了處理其輸出而設計的程式。  
  
 在預存程序中，無法指定 SET SHOWPLAN_TEXT 和 SET SHOWPLAN_ALL。 它們必須是批次中僅有的陳述式。  
  
 SET SHOWPLAN_TEXT 會在一組資料列中傳回資訊，使這些資料列形成階層式樹狀結構，呈現出 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 查詢處理器在執行每個陳述式時所採取的步驟。 輸出中所反映的每個陳述式都包含單一資料列，其中含有陳述式的文字，後面再接著幾個資料列，其中含有執行步驟的詳細資料。 下表顯示輸出所包含的資料行。  
  
|資料行名稱|描述|  
|-----------------|-----------------|  
|**StmtText**|對於每個類型不是 PLAN_ROW 的資料列，這個資料行都包含 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的文字。 對於類型是 PLAN_ROW 的資料列，這個資料行包含作業的說明。 這個資料行包含實體運算子，也可能選擇性地包含邏輯運算子。 這個資料行後面可能接著取決於實體運算子的說明。 如需實體運算子的詳細資訊，請參閱 [SET SHOWPLAN_ALL &#40;Transact-SQL&#41;](../../t-sql/statements/set-showplan-all-transact-sql.md) 中 **引數** 資料行的詳細資訊。|  
|||

 如需在顯示計畫輸出中所能見到之實體和邏輯運算子的詳細資訊，請參閱[執行程序表邏輯及實體運算子參考](../../relational-databases/showplan-logical-and-physical-operators-reference.md)  
  
## <a name="permissions"></a>權限  
 若要使用 SET SHOWPLAN_TEXT，您必須有執行 SET SHOWPLAN_TEXT 所針對的陳述式之適當權限，且您必須有包含所參考物件的所有資料庫之 SHOWPLAN 權限。  
  
 對於 SELECT、INSERT、UPDATE、DELETE、EXEC *stored_procedure* 和 EXEC *user_defined_function* 陳述式，若要產生執行程序表，使用者必須：  
  
-   有執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的適當權限。  
  
-   有包含 Transact-SQL 陳述式所參考的物件 (如資料表、檢視等等) 之所有資料庫的 SHOWPLAN 權限。  
  
 至於所有其他陳述式，例如 DDL、USE *database_name*、SET、DECLARE、動態 SQL 等等，只需要有執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的適當權限。  
  
## <a name="examples"></a>範例  
 這個範例會顯示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在處理陳述式時，如何使用索引。  
  
 這是使用索引的查詢：  
  
```sql
USE AdventureWorks2012;  
GO  
SET SHOWPLAN_TEXT ON;  
GO  
SELECT *  
FROM Production.Product   
WHERE ProductID = 905;  
GO  
SET SHOWPLAN_TEXT OFF;  
GO  
```  
  
 以下為結果集：  
  
```  
StmtText                                             
---------------------------------------------------  
SELECT *  
FROM Production.Product   
WHERE ProductID = 905;   
  
StmtText                                                                                                                                                                                        
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------  
|--Clustered Index Seek(OBJECT:([AdventureWorks2012].[Production].[Product].[PK_Product_ProductID]), SEEK:([AdventureWorks2012].[Production].[Product].[ProductID]=CONVERT_IMPLICIT(int,[@1],0)) ORDERED FORWARD)   
```  
  
 這是不使用索引的查詢：  
  
```sql
USE AdventureWorks2012;  
GO  
SET SHOWPLAN_TEXT ON;  
GO  
SELECT *  
FROM Production.ProductCostHistory  
WHERE StandardCost < 500.00;  
GO  
SET SHOWPLAN_TEXT OFF;  
GO  
```  
  
 以下為結果集：  
  
```  
StmtText                                                                  
------------------------------------------------------------------------  
SELECT *  
FROM Production.ProductCostHistory  
WHERE StandardCost < 500.00;   
  
StmtText                                                                                                                                                                                                  
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------  
|--Clustered Index Scan(OBJECT:([AdventureWorks2012].[Production].[ProductCostHistory].[PK_ProductCostHistory_ProductCostID]), WHERE:([AdventureWorks2012].[Production].[ProductCostHistory].[StandardCost]<[@1]))  
```  
  
## <a name="see-also"></a>另請參閱  
 [運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SET SHOWPLAN_ALL &#40;Transact-SQL&#41;](../../t-sql/statements/set-showplan-all-transact-sql.md)  
  
  
