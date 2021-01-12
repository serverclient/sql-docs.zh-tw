---
description: sys.types (Transact-SQL)
title: sys. 類型 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- types
- types_TSQL
- sys.types_TSQL
- sys.types
dev_langs:
- TSQL
helpviewer_keywords:
- sys.types catalog view
- table-valued parameters,sys.types
ms.assetid: a5dbc842-71a0-4f62-b5e0-f560a99b7f8c
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 586c115a7223a7c01cbafa334dc07ca450a1c640
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094308"
---
# <a name="systypes-transact-sql"></a>sys.types (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對每個系統和使用者定義型別，各包含一個資料列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|類型的名稱。 在結構描述中，這是唯一的。|  
|**system_type_id**|**tinyint**|類型的內部系統類型識別碼。|  
|**user_type_id**|**int**|類型的識別碼。 在資料庫中，這是唯一的。 若為系統資料類型，請 **user_type_id**  =  **system_type_id**。|  
|**schema_id**|**int**|類型所屬的結構描述識別碼。|  
|**principal_id**|**int**|個別擁有者的識別碼 (如果與結構描述擁有者不同的話)。 依預設，結構描述包含的物件就是結構描述擁有者所擁有的物件。 不過，您也可以利用 ALTER AUTHORIZATION 陳述式來變更擁有權，指定替代的擁有者。<br /><br /> NULL (如果沒有替代的個別擁有者)。|  
|**max_length**|**smallint**|類型的最大長度 (以位元組為單位)。<br /><br /> -1 = 資料行資料類型是 **Varchar (max)**、 **Nvarchar (max)**、 **Varbinary (max)** 或 **xml**。<br /><br /> 若為 **文字** 資料行， **max_length** 值將會是16。|  
|**有效位數**|**tinyint**|如果是以數值為基礎，便是類型的最大有效位數；否則，便是 0。|  
|**scale**|**tinyint**|如果是以數值為基礎，便是類型的最大小數位數；否則，便是 0。|  
|**collation_name**|**sysname**|如果是以字元為基礎，便是類型的定序名稱；否則，便是 NULL。|  
|**is_nullable**|**bit**|類型可為 Null。|  
|**is_user_defined**|**bit**|1 = 使用者定義型別。<br /><br /> 0 = [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統資料類型。|  
|**is_assembly_type**|**bit**|1 = 類型的實作定義在 CLR 組件中。<br /><br /> 0 = 類型是以 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統資料類型為基礎。|  
|**default_object_id**|**int**|使用 [sp_bindefault](../../relational-databases/system-stored-procedures/sp-bindefault-transact-sql.md)系結至類型之獨立預設值的識別碼。<br /><br /> 0 = 沒有預設值。|  
|**rule_object_id**|**int**|使用 [sp_bindrule](../../relational-databases/system-stored-procedures/sp-bindrule-transact-sql.md)系結至類型之獨立規則的識別碼。<br /><br /> 0 = 沒有規則。|  
|**is_table_type**|**bit**|表示類型為資料表。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [純量類型目錄檢視 &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/scalar-types-catalog-views-transact-sql.md)   
 [ALTER AUTHORIZATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-authorization-transact-sql.md)   
 [OBJECTPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/objectproperty-transact-sql.md)   
 [查詢 SQL Server 系統目錄 FAQ](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)  
  
  
