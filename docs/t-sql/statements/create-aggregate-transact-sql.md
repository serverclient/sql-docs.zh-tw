---
description: CREATE AGGREGATE (Transact-SQL)
title: CREATE AGGREGATE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE_AGGREGATE_TSQL
- CREATE AGGREGATE
- AGGREGATE_TSQL
- AGGREGATE
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE AGGREGATE statement
- aggregate functions [SQL Server], user-defined
- user-defined functions [CLR integration]
ms.assetid: 62eebc19-9f15-4245-94fa-b3fcd64a9d42
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 773c2010cdefa2455e0800ed0370cbc118c2dab9
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098428"
---
# <a name="create-aggregate-transact-sql"></a>CREATE AGGREGATE (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  建立使用者定義的彙總函式 (其實作定義在 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 的組件類別中)。 若要使 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 將彙總函式繫結到它的實作，您必須先利用 CREATE ASSEMBLY 陳述式，將包含這項實作的 [!INCLUDE[dnprdnshort](../../includes/dnprdnshort-md.md)] 組件上傳到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
CREATE AGGREGATE [ schema_name . ] aggregate_name  
        (@param_name <input_sqltype>   
        [ ,...n ] )  
RETURNS <return_sqltype>  
EXTERNAL NAME assembly_name [ .class_name ]  
  
<input_sqltype> ::=  
        system_scalar_type | { [ udt_schema_name. ] udt_type_name }  
  
<return_sqltype> ::=  
        system_scalar_type | { [ udt_schema_name. ] udt_type_name }  
  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *schema_name*  
 這是使用者定義彙總函式所屬的結構描述名稱。  
  
 *aggregate_name*  
 這是您要建立之彙總函式的名稱。  
  
 **@** _param_name_  
 使用者定義彙總中的一或多個參數。 在執行彙總函式時，使用者必須提供參數的值。 使用 "at" 記號 ( **@** ) 當作第一個字元來指定參數名稱。 參數名稱必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。 這些參數是函數的本機參數。  
  
 *system_scalar_type*  
 這是用來保留輸入參數值或傳回值的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統純量資料類型。 除了 **text**、**ntext** 和 **image**，所有純量資料類型都可用來當作使用者定義彙總的參數。 不能指定非純量類型，例如 **cursor** 和 **table**。  
  
 *udt_schema_name*  
 這是 CLR 使用者自訂類型所屬的結構描述名稱。 若未指定，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 是以下列順序參考 *udt_type_name*：  
  
-   原生 SQL 類型命名空間。  
  
-   目前資料庫中之目前使用者的預設結構描述。  
  
-   目前資料庫中的 **dbo** 結構描述。  
  
 *udt_type_name*  
 這是目前資料庫中已建立之 CLR 使用者自訂類型的名稱。 如果未指定 *udt_schema_name*，則 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會假設類型屬於目前使用者的結構描述。  
  
 *assembly_name* [ **.** _class_name_ ]  
 指定要繫結使用者定義彙總函式的組件，以及 (選擇性) 組件所屬的結構描述名稱和實作使用者自訂彙總的組件中之類別名稱。 這個組件必須已利用 CREATE ASSEMBLY 陳述式建立在資料庫中。 *class_name* 必須是有效的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 識別碼，且符合組件中現有的類別名稱。 如果用來撰寫類別的程式設計語言使用命名空間 (如 C#)，則 *class_name* 可能是符合命名空間的名稱。 如果未指定 *class_name*，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會假設它和 *aggregate_name* 一樣。  
  
## <a name="remarks"></a>備註  
 依預設，會關閉 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行 CLR 程式碼的能力。 您可以建立、修改和卸除參考受控碼模組的資料庫物件，但除非使用 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md) 來啟用 [clr enabled 選項](../../database-engine/configure-windows/clr-enabled-server-configuration-option.md)，否則，這些模組中的程式碼不會在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中執行。  
  
 *assembly_name* 及其方法所參考的組件類別，應該滿足在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中實作使用者定義彙總函式的所有需求。 如需詳細資訊，請參閱 [CLR 使用者定義彙總](../../relational-databases/clr-integration-database-objects-user-defined-functions/clr-user-defined-aggregates.md)。  
  
## <a name="permissions"></a>權限  
 需要 CREATE AGGREGATE 權限，以及 EXTERNAL NAME 子句所指定之組件的 REFERENCES 權限。  
  
## <a name="examples"></a>範例  
 下列範例會假設 StringUtilities.csproj 範例應用程式已編譯。 如需詳細資訊，請參閱[字串公用程式函數範例](/previous-versions/sql/sql-server-2016/ff878119(v=sql.130))。  
  
 下列範例會建立彙總 `Concatenate`。 在建立這項彙總之前，會將 `StringUtilities.dll` 組件註冊在本機資料庫中。  
  
```sql  
USE AdventureWorks2012;  
GO  
DECLARE @SamplesPath nvarchar(1024)  
-- You may have to modify the value of the this variable if you have  
--installed the sample some location other than the default location.  
  
SELECT @SamplesPath = REPLACE(physical_name, 'Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\master.mdf', 'Microsoft SQL Server\130\Samples\Engine\Programmability\CLR\')   
     FROM master.sys.database_files   
     WHERE name = 'master';  
  
CREATE ASSEMBLY StringUtilities FROM @SamplesPath + 'StringUtilities\CS\StringUtilities\bin\debug\StringUtilities.dll'  
WITH PERMISSION_SET=SAFE;  
GO  
  
CREATE AGGREGATE Concatenate(@input nvarchar(4000))  
RETURNS nvarchar(4000)  
EXTERNAL NAME [StringUtilities].[Microsoft.Samples.SqlServer.Concatenate];  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [DROP AGGREGATE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-aggregate-transact-sql.md)  
  
