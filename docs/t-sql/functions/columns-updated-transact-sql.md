---
description: COLUMNS_UPDATED (Transact-SQL)
title: COLUMNS_UPDATED (Transact-SQL)
ms.custom: ''
ms.date: 07/25/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- COLUMNS_UPDATED_TSQL
- COLUMNS_UPDATED
dev_langs:
- TSQL
helpviewer_keywords:
- COLUMNS_UPDATED function
- testing columns
- column testing [SQL Server]
- updated columns
ms.assetid: 765fde44-1f95-4015-80a4-45388f18a42c
author: cawrites
ms.author: chadam
ms.openlocfilehash: c927fbad08c222aad4e284a66d3b3e61061e82e9
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101276"
---
# <a name="columns_updated-transact-sql"></a>COLUMNS_UPDATED (Transact-SQL)

[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

此函式會傳回 **varbinary** 位元模式，指出資料表或檢視的已插入或更新資料行。 在 [!INCLUDE[tsql](../../includes/tsql-md.md)] INSERT 或 UPDATE 觸發程序主體內的任何位置使用 `COLUMNS_UPDATED`，來測試觸發程序是否應該執行特定動作。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
COLUMNS_UPDATED ( )   
```  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-types"></a>傳回類型
**varbinary**
  
## <a name="remarks"></a>備註  
`COLUMNS_UPDATED` 會測試多個資料行所執行的 UPDATE 或 INSERT 動作。 若要在一個資料行上測試 UPDATE 或 INSERT 作業，請使用 [UPDATE()](../../t-sql/functions/update-trigger-functions-transact-sql.md)。
  
`COLUMNS_UPDATED` 會傳回從左到右排序的一或多個位元組。 每個位元組的最右邊位元都是最不重要的位元。 最左邊位元組的最右邊位元代表資料表的第一個資料表資料行，而左邊的下一個位元代表第二個資料行，依此類推。 如果建立觸發程序的資料表包含超出八個資料行，則 `COLUMNS_UPDATED` 會傳回多個位元組，而最不重要的位元組在最左邊。 `COLUMNS_UPDATED` 會針對 INSERT 動作中的所有資料行傳回 TRUE，因為資料行已插入明確值或隱含 (NULL) 值。
  
若要進行特定資料行的更新或插入測試，請遵循含有位元運算子和所測試資料行之整數位元遮罩的語法。 例如，假設 **t1** 資料表包含 **C1**、**C2**、**C3**、**C4** 和 **C5** 資料行。 若要確認已全部成功更新 **C2**、**C3** 和 **C4** 資料行 (**t1** 資料表具有 UPDATE 觸發程序)，請遵循含有 **& 14** 的語法。 若要測試是否僅有 **C2** 獲得更新，請指定 **& 2**。 如需實際範例，請參閱[範例 A](#a-using-columns_updated-to-test-the-first-eight-columns-of-a-table) 和[範例 B](#b-using-columns_updated-to-test-more-than-eight-columns)。
  
在 [!INCLUDE[tsql](../../includes/tsql-md.md)] INSERT 或 UPDATE 觸發程序內的任何位置使用 `COLUMNS_UPDATED`。
  
INFORMATION_SCHEMA.COLUMNS 檢視的 ORDINAL_POSITION 資料行與 `COLUMNS_UPDATED` 所傳回之資料行的位元模式不相容。 若要取得與 `COLUMNS_UPDATED` 相容的位元模式，請在查詢 `INFORMATION_SCHEMA.COLUMNS` 檢視時，依下列範例所示來參考 `COLUMNPROPERTY` 系統函式的 `ColumnID` 屬性。
  
```sql
SELECT TABLE_NAME, COLUMN_NAME,  
    COLUMNPROPERTY(OBJECT_ID(TABLE_SCHEMA + '.' + TABLE_NAME),  
    COLUMN_NAME, 'ColumnID') AS COLUMN_ID  
FROM AdventureWorks2012.INFORMATION_SCHEMA.COLUMNS  
WHERE TABLE_NAME = 'Person';  
```  

如果觸發程序套用至資料行，則 `COLUMNS_UPDATED` 會傳回 `true` 或 `1`，即使資料行的值維持不變也是如此。 這是根據設計的行為，且觸發程序應該實作商務邏輯，以決定是否可允許插入/更新/刪除作業。 
  
## <a name="column-sets"></a>資料行集
針對資料表定義資料行集之後，`COLUMNS_UPDATED` 函式就會遵循下列方式運作：
-   明確更新資料行集的成員資料行時，該資料行的對應位元會設定為 1，而資料行集位元設定為 1。  
-   明確更新資料行集時，資料行集位元會設定為 1，而該資料表中所有疏鬆資料行的位元會設定為 1。  
-   若為插入作業，所有位元都會設定為 1。  
  
     因為資料行集變更會導致資料行集中所有資料行的位元都重設為 1，所以資料行集中未變更的資料行會看起來像已修改過。 如需資料行集的詳細資訊，請參閱[使用資料行集](../../relational-databases/tables/use-column-sets.md)。  
  
## <a name="examples"></a>範例  
  
### <a name="a-using-columns_updated-to-test-the-first-eight-columns-of-a-table"></a>A. 利用 COLUMNS_UPDATED 來測試資料表的前八個資料行  
此範例會建立兩份資料表：`employeeData` 和 `auditEmployeeData`。 `employeeData` 資料表保留機密的員工薪資資訊，而人力資源部門成員可以修改這項資訊。 如果員工的社會保險號碼 (SSN)、年薪或銀行帳戶變更，就會產生一筆稽核記錄，並將其插入 `auditEmployeeData` 稽核資料表。
  
使用 `COLUMNS_UPDATED()` 函式，即可快速測試包含對機密員工資訊的資料行進行的任何變更。 只有在嘗試偵測資料表中前八個資料行的變更時，則以這個方式來使用 `COLUMNS_UPDATED()` 才有效。
  
```sql
USE AdventureWorks2012;  
GO  
IF EXISTS(SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES  
   WHERE TABLE_NAME = 'employeeData')  
   DROP TABLE employeeData;  
IF EXISTS(SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES  
   WHERE TABLE_NAME = 'auditEmployeeData')  
   DROP TABLE auditEmployeeData;  
GO  
CREATE TABLE dbo.employeeData (  
   emp_id INT NOT NULL PRIMARY KEY,  
   emp_bankAccountNumber CHAR (10) NOT NULL,  
   emp_salary INT NOT NULL,  
   emp_SSN CHAR (11) NOT NULL,  
   emp_lname NCHAR (32) NOT NULL,  
   emp_fname NCHAR (32) NOT NULL,  
   emp_manager INT NOT NULL  
   );  
GO  
CREATE TABLE dbo.auditEmployeeData (  
   audit_log_id uniqueidentifier DEFAULT NEWID() PRIMARY KEY,  
   audit_log_type CHAR (3) NOT NULL,  
   audit_emp_id INT NOT NULL,  
   audit_emp_bankAccountNumber CHAR (10) NULL,  
   audit_emp_salary INT NULL,  
   audit_emp_SSN CHAR (11) NULL,  
   audit_user sysname DEFAULT SUSER_SNAME(),  
   audit_changed DATETIME DEFAULT GETDATE()  
   );  
GO  
CREATE TRIGGER dbo.updEmployeeData   
ON dbo.employeeData   
AFTER UPDATE AS  
/* Check whether columns 2, 3 or 4 have been updated. If any or all  
columns 2, 3 or 4 have been changed, create an audit record.
The bitmask is: power(2, (2-1)) + power(2, (3-1)) + power(2, (4-1)) = 14.
This bitmask translates into base_10 as: 2 + 4 + 8 = 14.
To test whether all columns 2, 3, and 4 are updated, use = 14 instead of > 0  
(below). */
  
   IF (COLUMNS_UPDATED() & 14) > 0  
/* Use IF (COLUMNS_UPDATED() & 14) = 14 to see whether all columns 2, 3,   
and 4 are updated. */  
      BEGIN  
-- Audit OLD record.  
      INSERT INTO dbo.auditEmployeeData  
         (audit_log_type,  
         audit_emp_id,  
         audit_emp_bankAccountNumber,  
         audit_emp_salary,  
         audit_emp_SSN)  
         SELECT 'OLD',   
            del.emp_id,  
            del.emp_bankAccountNumber,  
            del.emp_salary,  
            del.emp_SSN  
         FROM deleted del;  
  
-- Audit NEW record.  
      INSERT INTO dbo.auditEmployeeData  
         (audit_log_type,  
         audit_emp_id,  
         audit_emp_bankAccountNumber,  
         audit_emp_salary,  
         audit_emp_SSN)  
         SELECT 'NEW',  
            ins.emp_id,  
            ins.emp_bankAccountNumber,  
            ins.emp_salary,  
            ins.emp_SSN  
         FROM inserted ins;  
   END;  
GO  
  
/* Inserting a new employee does not cause the UPDATE trigger to fire. */  
INSERT INTO employeeData  
   VALUES ( 101, 'USA-987-01', 23000, 'R-M53550M', N'Mendel', N'Roland', 32);  
GO  
  
/* Updating the employee record for employee number 101 to change the   
salary to 51000 causes the UPDATE trigger to fire and an audit trail to   
be produced. */  
  
UPDATE dbo.employeeData  
   SET emp_salary = 51000  
   WHERE emp_id = 101;  
GO  
SELECT * FROM auditEmployeeData;  
GO  
  
/* Updating the employee record for employee number 101 to change both   
the bank account number and social security number (SSN) causes the   
UPDATE trigger to fire and an audit trail to be produced. */  
  
UPDATE dbo.employeeData  
   SET emp_bankAccountNumber = '133146A0', emp_SSN = 'R-M53550M'  
   WHERE emp_id = 101;  
GO  
SELECT * FROM dbo.auditEmployeeData;  
  
GO  
```  
  
### <a name="b-using-columns_updated-to-test-more-than-eight-columns"></a>B. 利用 COLUMNS_UPDATED 來測試八個以上資料行  
若要測試會影響前八個資料表資料行以外之資料行的更新，請使用 `SUBSTRING` 函式來測試 `COLUMNS_UPDATED` 所傳回的正確位元。 此範例會測試影響 `AdventureWorks2012.Person.Person` 資料表中第 `3` 個、第 `5` 個和第 `9` 個資料行的更新。
  
```sql
USE AdventureWorks2012;  
GO  
IF OBJECT_ID (N'Person.uContact2', N'TR') IS NOT NULL  
    DROP TRIGGER Person.uContact2;  
GO  
CREATE TRIGGER Person.uContact2 ON Person.Person  
AFTER UPDATE AS  
    IF ( (SUBSTRING(COLUMNS_UPDATED(), 1, 1) & 20 = 20)   
        AND (SUBSTRING(COLUMNS_UPDATED(), 2, 1) & 1 = 1) )   
    PRINT 'Columns 3, 5 and 9 updated';  
GO  
  
UPDATE Person.Person   
   SET NameStyle = NameStyle,  
      FirstName=FirstName,  
      EmailPromotion=EmailPromotion;  
GO  
```  
  
## <a name="see-also"></a>另請參閱
[位元運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/bitwise-operators-transact-sql.md)  
[CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md)  
[UPDATE&#40;&#41; &#40;Transact-SQL&#41;](../../t-sql/functions/update-trigger-functions-transact-sql.md)
  
  
