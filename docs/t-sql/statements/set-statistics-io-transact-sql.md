---
description: SET STATISTICS IO (Transact-SQL)
title: SET STATISTICS IO (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 11/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SET_STATISTICS_IO_TSQL
- IO
- IO_TSQL
- SET STATISTICS IO
dev_langs:
- TSQL
helpviewer_keywords:
- disk I/O statistics [SQL Server]
- I/O [SQL Server], disk activity information
- disks [SQL Server], statement statistics
- STATISTICS IO option
- statements [SQL Server], statistical information
- SET STATISTICS IO statement
- statistical information [SQL Server], disk activity
ms.assetid: 7033aac9-a944-4156-9ff4-6ef65717a28b
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 7b1f3fac5ab29e44dd3150cba5882cd1dfc4e271
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98079489"
---
# <a name="set-statistics-io-transact-sql"></a>SET STATISTICS IO (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  使 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 顯示 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式所產生之磁碟活動量的相關資訊。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
SET STATISTICS IO { ON | OFF }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註
 當 STATISTICS IO 為 ON 時，會顯示統計資訊，而其為 OFF 時，則不會顯示資訊。   
  
 在這個選項設為 ON 之後，所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式都會傳回統計資訊，直到這個選項設為 OFF 為止。  
  
 下表列出和描述輸出項目。  
  
|輸出項目|意義|  
|-----------------|-------------|  
|**Table**|資料表的名稱。|  
|**掃描計數**|為了建構輸出的最終資料集，在達到分葉層級之後朝任何方向啟動以擷取所有值的搜尋或掃描次數。<br /><br /> 如果使用的索引是主索引鍵的唯一索引或叢集索引，且您只要搜尋一個值，掃描計數就是 0。 例如： `WHERE Primary_Key_Column = <value>` 。<br /><br /> 當您使用非主索引鍵資料行上定義的非唯一叢集索引來搜尋一個值時，掃描計數就是 1。 執行此處理序是為了檢查您所搜尋索引鍵值是否有重複的值。 例如： `WHERE Clustered_Index_Key_Column = <value>` 。<br /><br /> 當 N 是使用索引鍵找出索引鍵值之後，朝向分葉層級左側或右側啟動的不同搜尋或掃描次數時，掃描計數就是 N。|  
|**邏輯讀取**|從資料快取中讀取的頁數。|  
|**實體讀取**|從磁碟中讀取的頁數。|  
|**讀取前讀取**|放入查詢快取中的頁數。|  
|**LOB 邏輯讀取**|從資料快取中讀取的頁數。 包含 **text**、**ntext**、**image**、**varchar(max)**、**nvarchar(max)**、**varbinary(max)** 或資料行存放區索引頁。|  
|**LOB 實體讀取**|從磁碟中讀取的頁數。 包含 **text**、**ntext**、**image**、**varchar(max)**、**nvarchar(max)**、**varbinary(max)** 或資料行存放區索引頁。|  
|**LOB 讀取前讀取**|放入查詢快取中的頁數。 包含 **text**、**ntext**、**image**、**varchar(max)**、**nvarchar(max)**、**varbinary(max)** 或資料行存放區索引頁。|

 SET STATISTICS IO 的設定是在執行階段進行設定，而不是在剖析階段進行設定。

> [!NOTE]  
> 當 Transact-SQL 陳述式擷取 LOB 資料行時，有些 LOB 擷取作業可能需要往返 LOB 樹狀結構多次。 這可能造成 SET STATISTICS IO 報告的數字高於預期的邏輯讀取次數。

## <a name="permissions"></a>權限  
 若要使用 SET STATISTICS IO，使用者必須有執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的適當權限。 不需要 SHOWPLAN 權限。  
  
## <a name="examples"></a>範例  
 這個範例會顯示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在處理陳述式時，使用多少邏輯和實體讀取。  
  
```sql
USE AdventureWorks2012;  
GO         
SET STATISTICS IO ON;  
GO  
SELECT *   
FROM Production.ProductCostHistory  
WHERE StandardCost < 500.00;  
GO  
SET STATISTICS IO OFF;  
GO  
```  
  
 以下為結果集：  
  
```  
Table 'ProductCostHistory'. Scan count 1, logical reads 5, physical   
reads 0, read-ahead reads 0, lob logical reads 0, lob physical reads 0,   
lob read-ahead reads 0.  
```  
  
## <a name="see-also"></a>另請參閱  
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SET SHOWPLAN_ALL &#40;Transact-SQL&#41;](../../t-sql/statements/set-showplan-all-transact-sql.md)   
 [SET STATISTICS TIME &#40;Transact-SQL&#41;](../../t-sql/statements/set-statistics-time-transact-sql.md)  
  
  
