---
description: $PARTITION (Transact-SQL)
title: $PARTITION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- $partition_TSQL
- $partition
dev_langs:
- TSQL
helpviewer_keywords:
- $PARTITION function
- partitions [SQL Server], numbers
ms.assetid: abc865d0-57a8-49da-8821-29457c808d2a
author: julieMSFT
ms.author: jrasnick
ms.openlocfilehash: e32332d4b258a4fb37b49aa4d6715483998cd83d
ms.sourcegitcommit: 23649428528346930d7d5b8be7da3dcf1a2b3190
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98241872"
---
# <a name="partition-transact-sql"></a>$PARTITION (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回資料分割編號，做為一組資料分割資料行值針對 [!INCLUDE[ssnoversion](../../includes/ssnoversion-md.md)] 中任何指定的資料分割函數對應的目標。
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
[ database_name. ] $PARTITION.partition_function_name(expression)  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *database_name*  
 這是包含資料分割函數的資料庫名稱。  
  
 *partition_function_name*  
 這是要套用一組資料分割資料行值的任何現有資料分割函數的名稱。  
  
 *expression*  
 為資料類型必須完全符合或可隱含轉換成對應分割資料行之資料類型的[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。 *expression* 也可以是目前參與 *partition_function_name* 之分割資料行的名稱。  
  
## <a name="return-types"></a>傳回型別  
 **int**  
  
## <a name="remarks"></a>備註  
 $PARTITION 會傳回一個 1 和資料分割函式的分割區數目之間的 **int** 值。  
  
 $PARTITION 會傳回任何有效值的資料分割編號，不論使用資料分割函數的資料分割資料表或索引中目前的值為何，都是如此。  
  
## <a name="examples"></a>範例  
  
### <a name="a-getting-the-partition-number-for-a-set-of-partitioning-column-values"></a>A. 取得一組資料分割資料行值的資料分割編號  
 下列範例建立 `RangePF1` 資料分割函數，將資料表或索引分割成四份資料分割。 $PARTITION 用來決定代表 `10` 之資料分割資料行的 `RangePF1` 值，將放在資料表的第 1 個資料分割中。  
  
```sql  
USE AdventureWorks2012;  
GO  
CREATE PARTITION FUNCTION RangePF1 ( INT )  
AS RANGE FOR VALUES (10, 100, 1000) ;  
GO  
SELECT $PARTITION.RangePF1 (10) ;  
GO  
```  
  
### <a name="b-getting-the-number-of-rows-in-each-nonempty-partition-of-a-partitioned-table-or-index"></a>B. 取得在資料分割資料表或索引的每個非空白資料分割中之資料列數目  
 下列範例會傳回包含資料之 `TransactionHistory` 資料表的每個資料分割中之資料列數目。 `TransactionHistory` 資料表使用資料分割函數 `TransactionRangePF1`，它在 `TransactionDate` 資料行上進行資料分割。  
  
 若要執行這個範例，您必須先針對 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 範例資料庫執行 PartitionAW.sql 指令碼。 如需詳細資訊，請參閱 [PartitioningScript](https://go.microsoft.com/fwlink/?LinkId=201015)。  
  
```sql
USE AdventureWorks2012;  
GO  
SELECT $PARTITION.TransactionRangePF1(TransactionDate) AS Partition,   
COUNT(*) AS [COUNT] FROM Production.TransactionHistory   
GROUP BY $PARTITION.TransactionRangePF1(TransactionDate)  
ORDER BY Partition ;  
GO  
```  
  
### <a name="c-returning-all-rows-from-one-partition-of-a-partitioned-table-or-index"></a>C. 從資料分割資料表或索引的某份資料分割中，傳回所有資料列  
 下列範例會傳回 `5` 資料表之第 `TransactionHistory` 個資料分割的所有資料列。  
  
> [!NOTE]  
>  若要執行這個範例，您必須先針對 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 範例資料庫執行 PartitionAW.sql 指令碼。 如需詳細資訊，請參閱 [PartitioningScript](https://go.microsoft.com/fwlink/?LinkId=201015)。  
  
```sql  
SELECT * FROM Production.TransactionHistory  
WHERE $PARTITION.TransactionRangePF1(TransactionDate) = 5 ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-function-transact-sql.md)  
  
  
