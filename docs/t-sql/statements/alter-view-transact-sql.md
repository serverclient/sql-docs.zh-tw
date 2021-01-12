---
description: ALTER VIEW (Transact-SQL)
title: ALTER VIEW (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/25/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_VIEW_TSQL
- ALTER VIEW
dev_langs:
- TSQL
helpviewer_keywords:
- indexed views [SQL Server], modifying
- views [SQL Server], modifying
- modifying views
- ALTER VIEW statement
ms.assetid: 03eba220-13e2-49e3-bd9d-ea9df84dc28c
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 76a16ec32341e54c66b7ba72edc50f4d9944b900
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098457"
---
# <a name="alter-view-transact-sql"></a>ALTER VIEW (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  修改先前建立的檢視。 其中包括索引檢視。 ALTER VIEW 不會影響相依的預存程序或觸發程序，且不會變更權限。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER VIEW [ schema_name . ] view_name [ ( column [ ,...n ] ) ]   
[ WITH <view_attribute> [ ,...n ] ]   
AS select_statement   
[ WITH CHECK OPTION ] [ ; ]  
  
<view_attribute> ::=   
{   
    [ ENCRYPTION ]  
    [ SCHEMABINDING ]  
    [ VIEW_METADATA ]       
}   
```

```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
ALTER VIEW [ schema_name . ] view_name [  ( column_name [ ,...n ] ) ]   
AS <select_statement>   
[;]  
``` 
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *schema_name*  
 這是檢視所屬的結構描述名稱。  
  
 *view_name*  
 這是要變更的檢視。  
  
 *column*  
 這是要放在指定檢視中之一或多個資料行的名稱 (以逗號分隔)。  
  
> [!IMPORTANT]  
>  只有在執行 ALTER VIEW 之前和之後資料行名稱相同時，才會維護資料行權限。  
  
> [!NOTE]  
>  在檢視的資料行中，資料行名稱的權限適用於整個 CREATE VIEW 或 ALTER VIEW 陳述式，不論基礎資料來源為何都是如此。 例如，如果是在 CREATE VIEW 陳述式中授與 **SalesOrderID** 資料行的權限，ALTER VIEW 陳述式可以重新命名 **SalesOrderID** 資料行 (例如，將它改成 **OrderRef**)，同時保有使用 **SalesOrderID** 之檢視的相關權限。  
  
 ENCRYPTION  
 **適用於**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
 加密 [sys.syscomments](../../relational-databases/system-compatibility-views/sys-syscomments-transact-sql.md) 中包含 ALTER VIEW 陳述式文字的項目。 WITH ENCRYPTION 可防止在 SQL Server 複寫中發行檢視。  
  
 SCHEMABINDING  
 將檢視繫結於一或多份基礎資料表的結構描述。 當指定 SCHEMABINDING 時，無法依照會影響檢視定義的方式來修改基底資料表。 您必須先修改或卸除檢視定義來移除對於要修改之資料表的相依性。 當您使用 SCHEMABINDING 時，_select\_statement_ 必須包括參考的資料表、檢視表或使用者定義函式的兩部分名稱 (_schema_ **.** _object_)。 所有參考的物件都必須在相同的資料庫中。  
  
 您無法卸除參與 SCHEMABINDING 子句所建立之檢視的檢視或資料表，除非這份檢視已經卸除或有了改變，不再擁有結構描述繫結。 否則，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會引發錯誤。 另外，如果 ALTER TABLE 陳述式會影響到檢視定義，在參與擁有結構描述繫結的檢視之資料表上執行這些陳述式也會失敗。  
  
 VIEW_METADATA  
 指定當針對參考檢視的查詢來要求瀏覽模式的中繼資料時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體將檢視 (而不是一份或多份基底資料表) 的中繼資料資訊傳回 DB-Library、ODBC 和 OLE DB API。 瀏覽模式中繼資料是 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體傳回給用戶端 DB-Library、ODBC 和 OLE DB API 的附加中繼資料。 這個中繼資料使得用戶端 API 能夠實作可更新的用戶端資料指標。 瀏覽模式中繼資料包括結果集中的資料行所屬之基底資料表的相關資訊。  
  
 如果是 VIEW_METADATA 所建立的檢視，當描述結果集中檢視的資料行時，瀏覽模式中繼資料會傳回檢視名稱，而不是基底資料表名稱。  
  
 當檢視是使用 WITH VIEW_METADATA 來建立時，如果該檢視具有 INSERT 或 UPDATE INSTEAD OF 觸發程序，則除了 **timestamp** 資料行之外，所有資料行都可以更新。 如需詳細資訊，請參閱 [CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md) 中的＜備註＞一節。  
  
 AS  
 這些是檢視要採取的動作。  
  
 *select_statement*  
 這是定義檢視的 SELECT 陳述式。  
  
 WITH CHECK OPTION  
 強制針對檢視執行的所有資料修改陳述式必須遵循 *select_statement* 內所設定的準則。  
  
## <a name="remarks"></a>備註  
 如需 ALTER VIEW 的詳細資訊，請參閱 [CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)中的＜備註＞一節。  
  
> [!NOTE]  
>  如果先前的檢視定義是利用 WITH ENCRYPTION 或 CHECK OPTION 來建立的，只有在 ALTER VIEW 包括這些選項時，才會啟用這些選項。  
  
 如果目前所用的檢視是利用 ALTER VIEW 來修改的， [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會取得檢視的獨佔結構描述鎖定。 當授與鎖定時，檢視並沒有使用中的使用者， [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會從程序快取中刪除檢視的所有副本。 現有參考這份檢視的計畫會保留在快取中，但在叫用它時，會重新編譯。  
  
 您可以將 ALTER VIEW 套用在索引檢視上；不過，ALTER VIEW 會無條件地卸除檢視的所有索引。  
  
## <a name="permissions"></a>權限  
 若要執行 ALTER VIEW，至少需要 OBJECT 的 ALTER 權限。  
  
## <a name="examples"></a>範例  
 下列範例會建立一份包含所有員工及其雇用日期 (稱為 `EmployeeHireDate`) 的檢視。 授與檢視的權限，但需求改成選取在特定日期之前雇用的員工。 之後，再利用 `ALTER VIEW` 來取代檢視。  
  
```sql 
USE AdventureWorks2012 ;  
GO  
CREATE VIEW HumanResources.EmployeeHireDate  
AS  
SELECT p.FirstName, p.LastName, e.HireDate  
FROM HumanResources.Employee AS e JOIN Person.Person AS  p  
ON e.BusinessEntityID = p.BusinessEntityID ;  
GO  
```  
  
 檢視必須改成只包括在 `2002` 之前雇用的員工。 如果未使用 ALTER VIEW，而是卸除再重建檢視，您就必須重新輸入先前所用的 GRANT 陳述式及處理這份檢視相關權限的任何其他陳述式。  
  
```sql  
ALTER VIEW HumanResources.EmployeeHireDate  
AS  
SELECT p.FirstName, p.LastName, e.HireDate  
FROM HumanResources.Employee AS e JOIN Person.Person AS p  
ON e.BusinessEntityID = p.BusinessEntityID  
WHERE HireDate < CONVERT(DATETIME,'20020101',101) ;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [CREATE VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/create-view-transact-sql.md)   
 [DROP VIEW &#40;Transact-SQL&#41;](../../t-sql/statements/drop-view-transact-sql.md)   
 [建立預存程序](../../relational-databases/stored-procedures/create-a-stored-procedure.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [對發行集資料庫進行結構描述變更](../../relational-databases/replication/publish/make-schema-changes-on-publication-databases.md)  
  
  
