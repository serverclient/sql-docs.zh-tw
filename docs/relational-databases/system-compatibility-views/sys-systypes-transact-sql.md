---
description: sys.systypes (Transact-SQL)
title: sys.sys類型 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.systypes_TSQL
- systypes_TSQL
- sys.systypes
- systypes
dev_langs:
- TSQL
helpviewer_keywords:
- sys.systypes compatibility view
- systypes system table
ms.assetid: 1b0b1d0c-5f7b-470b-bd52-8bfa922d7889
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: b00fc15f26b54acf7de52684949473c0bbd6ed19
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095348"
---
# <a name="syssystypes-transact-sql"></a>sys.systypes (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對資料庫中所定義的每個系統提供資料類型和每個使用者自訂資料類型，各傳回一個資料列。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|資料類型名稱。|  
|**xtype**|**tinyint**|實體儲存類型。|  
|**status**|**tinyint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**xusertype**|**smallint**|擴充使用者類型。 如果資料類型的數目超過 32,767，則會造成溢位或傳回 NULL。|  
|**length** (長度)|**smallint**|資料類型的實際長度。|  
|**xprec**|**tinyint**|符合伺服器所用的內部有效位數。 不會用在查詢中。|  
|**xscale**|**tinyint**|符合伺服器所用的內部小數位數。 不會用在查詢中。|  
|**tdefault**|**int**|包含這個資料類型之完整性檢查的預存處理序識別碼。|  
|**域**|**int**|包含這個資料類型之完整性檢查的預存處理序識別碼。|  
|**Uid**|**smallint**|類型擁有者的結構描述識別碼。<br /><br /> 如果是從舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 升級而來的資料庫，結構描述識別碼會等於擁有者的使用者識別碼。<br /><br /> **\* \* 重要 \* 事項 \*** ：如果您使用下列任何一個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] DDL 語句，則必須使用 [sys. 類型](../../relational-databases/system-catalog-views/sys-types-transact-sql.md)目錄檢視，而不是 **sys.sys類型**。<br /><br /> ALTER AUTHORIZATION ON TYPE<br /><br /> CREATE TYPE<br /><br /> 如果使用者和角色數目超過 32,767 個，則會造成溢位或傳回 NULL。|  
|**保留**|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**collationid**|**int**|如果是以字元為基礎， **collationid** 是目前資料庫之定序的識別碼;否則，它會是 Null。|  
|**usertype**|**smallint**|使用者類型識別碼。 如果資料類型的數目超過 32,767，則會造成溢位或傳回 NULL。|  
|**variable**|**bit**|可變長度資料類型。<br /><br /> 1 = True<br /><br /> 0 = False|  
|**allownulls**|**bit**|指出這項資料類型的預設 Null 屬性。 如果使用 [CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md) 或 [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)來指定 null 屬性，就會覆寫這個預設值。|  
|**type**|**tinyint**|實體儲存體資料類型。|  
|**printfmt**|**varchar(255)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**prec**|**smallint**|這個資料類型的有效位數層級。<br /><br /> -1 = **xml** 或大數數值型別。|  
|**scale**|**tinyint**|這個資料類型的小數位數 (以有效位數為基礎)。<br /><br /> NULL = 資料類型是非數值。|  
|**整理**|**sysname**|如果是以字元為基礎，定 **序** 就是目前資料庫的定序。否則，它會是 Null。|  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;的相容性檢視 ](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)   
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)  
  
  
