---
description: ALTER PROCEDURE (Transact-SQL)
title: ALTER PROCEDURE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/01/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_PROCEDURE_TSQL
- ALTER_PROC_TSQL
- ALTER PROC
- ALTER PROCEDURE
dev_langs:
- TSQL
helpviewer_keywords:
- ALTER PROCEDURE statement
- stored procedure modifications [SQL Server]
- modifying stored procedures
- stored procedures [SQL Server], modifying
ms.assetid: ed9b2f76-11ec-498d-a95e-75b490a75733
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 46d43653b505f679d326ad86c866ee6997f037f7
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092129"
---
# <a name="alter-procedure-transact-sql"></a>ALTER PROCEDURE (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  修改先前在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中執行 CREATE PROCEDURE 陳述式所建立的程序。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例 (Transact-SQL)](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
-- Syntax for SQL Server and Azure SQL Database
  
ALTER { PROC | PROCEDURE } [schema_name.] procedure_name [ ; number ]   
    [ { @parameter [ type_schema_name. ] data_type }   
        [ VARYING ] [ = default ] [ OUT | OUTPUT ] [READONLY]  
    ] [ ,...n ]   
[ WITH <procedure_option> [ ,...n ] ]  
[ FOR REPLICATION ]   
AS { [ BEGIN ] sql_statement [;] [ ...n ] [ END ] }  
[;]  
  
<procedure_option> ::=   
    [ ENCRYPTION ]  
    [ RECOMPILE ]  
    [ EXECUTE AS Clause ]  
```  
  
```syntaxsql  
-- Syntax for SQL Server CLR Stored Procedure  
  
ALTER { PROC | PROCEDURE } [schema_name.] procedure_name [ ; number ]   
    [ { @parameter [ type_schema_name. ] data_type }   
        [ = default ] [ OUT | OUTPUT ] [READONLY]  
    ] [ ,...n ]   
[ WITH EXECUTE AS Clause ]  
AS { EXTERNAL NAME assembly_name.class_name.method_name }  
[;]  
```  
  
```syntaxsql  
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
ALTER { PROC | PROCEDURE } [schema_name.] procedure_name  
    [ { @parameterdata_type } [= ] ] [ ,...n ]  
AS { [ BEGIN ] sql_statement [ ; ] [ ,...n ] [ END ] }  
[;]  
```  
  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *schema_name*  
 程序所屬之結構描述的名稱。  
  
 *procedure_name*  
 要變更之程序的名稱。 程序名稱必須符合 [識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
 **;** *number*  
 現有的選擇性整數，用來分組名稱相同的程序，以便能夠利用單一 DROP PROCEDURE 陳述式來同時卸除它們。  
  
> [!NOTE]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]  
  
 **@** *parameter*  
 程序中的參數。 您最多可以指定 2,100 個參數。  
  
 [ _type\_schema\_name_ **.** ] _data\_type_  
 這是參數的資料類型及其所屬的結構描述。  
  
 如需有關資料類型限制的詳細資訊，請參閱 [CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md)。  
  
 VARYING  
 指定支援做為輸出參數的結果集。 這個參數是預存程序所動態建構的，可能會有不同的內容。 只適用於 cursor 參數。 這個選項不適用於 CLR 程序。  
  
 *預設值*  
 這是參數的預設值。  
  
 OUT | OUTPUT  
 指出參數是一個傳回參數。  
  
 READONLY  
 指示無法在程序的主體內更新或修改參數。 如果參數類型是資料表值類型，就必須指定 READONLY。  
  
 RECOMPILE  
 指出 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 不會快取這個程序的計畫，而執行階段會重新編譯程序。  
  
 ENCRYPTION  
 **適用於**：SQL Server ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本) 和 [!INCLUDE[ssSDSFull](../../includes/sssdsfull-md.md)]。  
  
 指出 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會將 ALTER PROCEDURE 陳述式的原始文字轉換為混亂格式。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，無法直接從任何目錄檢視中看見混亂格式的輸出。 對系統資料表或資料庫檔案沒有存取權的使用者，無法擷取混亂格式的文字。 不過，可透過 [DAC 連接埠](../../database-engine/configure-windows/diagnostic-connection-for-database-administrators.md)存取系統資料表或直接存取資料庫檔案的具特殊權限使用者，則可使用該文字。 另外，可將偵錯工具附加至伺服器處理序的使用者，可以在執行階段從記憶體擷取原始程序。 如需如何存取系統中繼資料的詳細資訊，請參閱[中繼資料可見性組態](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
 使用這個選項建立的程序，不能發行為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 複寫的一部分。  
  
 Common Language Runtime (CLR) 預存程序不能指定這個選項。  
  
> [!NOTE]  
>  在升級期間，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會利用 **sys.sql_modules** 所儲存的混亂格式註解來重新建立程序。  
  
 EXECUTE AS  
 指定在存取預存程序之後，用來執行預存程序的安全性內容。  
  
 如需詳細資訊，請參閱 [EXECUTE AS 子句 &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-clause-transact-sql.md)。  
  
 FOR REPLICATION  

  
 指定無法在訂閱者執行為了複寫而建立的預存程序。 利用 FOR REPLICATION 選項來建立的預存程序，用來做為預存程序篩選，只有在複寫期間才會執行它。 如果指定了 FOR REPLICATION，就不能宣告參數。 這個選項不適用於 CLR 程序。 使用 FOR REPLICATION 建立的程序，會忽略 RECOMPILE 選項。  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
 { [ BEGIN ] *sql_statement* [;] [ ...*n* ] [ END ] }  
 包含程序主體的一個或多個 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 您可以使用選用的 BEGIN 和 END 關鍵字來括住陳述式。 如需詳細資訊，請參閱 [CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md) 中的＜最佳作法＞、＜一般備註＞以及＜限制事項＞這幾節。  
  
 EXTERNAL NAME _assembly\_name_ **.** _class\_name_ **.** _method\_name_  
 **適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。  
  
 指定 CLR 預存程序所要參考之 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 組件的方法。 *class_name* 必須是有效的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別碼，且必須是組件中的類別。 如果該類別的名稱符合命名空間規定，且該名稱利用句點 (**.**) 來分隔命名空間的各個部分，您就必須使用方括號 (**[]**) 或引號 (**""**) 來分隔類別名稱。 指定的方法必須是類別的靜態方法。  
  
 依預設，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不能執行 CLR 程式碼。 您可以建立、修改和卸除參考通用語言執行平台模組的資料庫物件；不過，必須等到您啟用 [clr enabled 選項](../../database-engine/configure-windows/clr-enabled-server-configuration-option.md)之後，才能在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中執行這些參考。 若要啟用這個選項，請使用 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)。  
  
> [!NOTE]  
>  自主資料庫不支援 CLR 程序。  
  
## <a name="general-remarks"></a>一般備註  
 [!INCLUDE[tsql](../../includes/tsql-md.md)] 預存程序無法修改成 CLR 預存程序，反之亦然。  
  
 ALTER PROCEDURE 不會變更權限，也不會影響任何相依的預存程序或觸發程序。 不過，當修改預存程序時，會將 QUOTED_IDENTIFIER 和 ANSI_NULLS 的目前工作階段設定併入預存程序中。 如果這些設定不同於最初建立預存程序時的有效設定，便有可能改變預存程序的行為。  
  
 如果先前的程序定義是利用 WITH ENCRYPTION 或 WITH RECOMPILE 來建立的，只有在 ALTER PROCEDURE 包括這些選項時，才會啟用這些選項。  
  
 如需有關預存程序的詳細資訊，請參閱 [CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md)。  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要程序的 **ALTER** 權限，或 **db_ddladmin** 固定資料庫角色的成員資格。  
  
## <a name="examples"></a>範例  
 下列範例會建立 `uspVendorAllInfo` 預存程序。 這個程序會傳回提供 [!INCLUDE[ssSampleDBCoFull](../../includes/sssampledbcofull-md.md)] 的所有供應商名稱，以及他們提供的產品、他們的信用等級，以及是否能聯繫到他們。 建立這個程序之後，再加以修改來傳回不同的結果集。  
  
```sql
IF OBJECT_ID ( 'Purchasing.uspVendorAllInfo', 'P' ) IS NOT NULL   
    DROP PROCEDURE Purchasing.uspVendorAllInfo;  
GO  
CREATE PROCEDURE Purchasing.uspVendorAllInfo  
WITH EXECUTE AS CALLER  
AS  
    SET NOCOUNT ON;  
    SELECT v.Name AS Vendor, p.Name AS 'Product name',   
      v.CreditRating AS 'Rating',   
      v.ActiveFlag AS Availability  
    FROM Purchasing.Vendor v   
    INNER JOIN Purchasing.ProductVendor pv  
      ON v.BusinessEntityID = pv.BusinessEntityID   
    INNER JOIN Production.Product p  
      ON pv.ProductID = p.ProductID   
    ORDER BY v.Name ASC;  
GO    
```  
  
 下列範例會改變 `uspVendorAllInfo` 預存程序。 它會移除 EXECUTE AS CALLER 子句，並修改程序主體，使其只傳回提供指定之產品的供應商。 `LEFT` 和 `CASE` 函數可自訂結果集的外觀。  
  
```sql  
USE AdventureWorks2012;  
GO  
ALTER PROCEDURE Purchasing.uspVendorAllInfo  
    @Product VARCHAR(25)   
AS  
    SET NOCOUNT ON;  
    SELECT LEFT(v.Name, 25) AS Vendor, LEFT(p.Name, 25) AS 'Product name',   
    'Rating' = CASE v.CreditRating   
        WHEN 1 THEN 'Superior'  
        WHEN 2 THEN 'Excellent'  
        WHEN 3 THEN 'Above average'  
        WHEN 4 THEN 'Average'  
        WHEN 5 THEN 'Below average'  
        ELSE 'No rating'  
        END  
    , Availability = CASE v.ActiveFlag  
        WHEN 1 THEN 'Yes'  
        ELSE 'No'  
        END  
    FROM Purchasing.Vendor AS v   
    INNER JOIN Purchasing.ProductVendor AS pv  
      ON v.BusinessEntityID = pv.BusinessEntityID   
    INNER JOIN Production.Product AS p   
      ON pv.ProductID = p.ProductID   
    WHERE p.Name LIKE @Product  
    ORDER BY v.Name ASC;  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```   
 Vendor               Product name  Rating    Availability  
-------------------- ------------- -------   ------------  
Proseware, Inc.      LL Crankarm   Average   No  
Vision Cycles, Inc.  LL Crankarm   Superior  Yes  
(2 row(s) affected)`  
```  

## <a name="see-also"></a>另請參閱  
 [CREATE PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/create-procedure-transact-sql.md)   
 [DROP PROCEDURE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-procedure-transact-sql.md)   
 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)   
 [EXECUTE AS &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [預存程序 &#40;Database Engine&#41;](../../relational-databases/stored-procedures/stored-procedures-database-engine.md)   
 [sys.procedures &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-procedures-transact-sql.md)  
  
  
