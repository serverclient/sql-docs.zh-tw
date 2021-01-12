---
description: 架構目錄檢視-sys. 架構
title: sys. 架構 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- schemas_TSQL
- sys.schemas_TSQL
- schemas
- sys.schemas
dev_langs:
- TSQL
helpviewer_keywords:
- sys.schemas catalog view
ms.assetid: 29af5ce5-2af7-4103-8f08-3ec92603ba05
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d41745e41db18f720f85fbc4aef0b8dcec89892f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100654"
---
# <a name="schemas-catalog-views---sysschemas"></a>架構目錄檢視-sys. 架構
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  針對每個資料庫結構描述，各包含一個資料列。  
  
> [!NOTE]  
>  資料庫結構描述與 XML 結構描述不同，它是用來定義 XML 文件的內容模型。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|結構描述的名稱。 在資料庫中，這是唯一的。|  
|**schema_id**|**int**|結構描述的識別碼。 在資料庫中，這是唯一的。|  
|**principal_id**|**int**|擁有這個結構描述之主體的識別碼。|  
  
## <a name="remarks"></a>備註  
資料庫架構可作為物件的命名空間或容器（例如資料表、視圖、程式和函式），這些物件可在 **sys.databases** 目錄檢視中找到。  

每個架構都有一個擁有者。 擁有者是安全性 [主體](../../relational-databases/security/authentication-access/principals-database-engine.md)。
  
## <a name="permissions"></a>權限  
 需要 **public** 角色的成員資格。 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
[主體](../../relational-databases/security/authentication-access/principals-database-engine.md)

[目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   

[&#40;Transact-sql&#41;的架構目錄檢視 ](./catalog-views-transact-sql.md)   

[sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)  
  
