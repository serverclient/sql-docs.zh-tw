---
description: sys.message_type_xml_schema_collection_usages (Transact-SQL)
title: sys.message_type_xml_schema_collection_usages (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- message_type_xml_schema_collection_usages_TSQL
- sys.message_type_xml_schema_collection_usages_TSQL
- sys.message_type_xml_schema_collection_usages
- message_type_xml_schema_collection_usages
dev_langs:
- TSQL
helpviewer_keywords:
- sys.message_type_xml_schema_collection_usages catalog view
ms.assetid: 544f61a1-c7b7-44b4-bf8d-980ba87d0665
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 0d6e1077bb4977dc80d423496da3f4dca5a2066a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095522"
---
# <a name="sysmessage_type_xml_schema_collection_usages-transact-sql"></a>sys.message_type_xml_schema_collection_usages (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  這份目錄檢視會針對由 XML 結構描述集合驗證的每個服務訊息類型，各傳回一個資料列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**message_type_id**|**int**|服務訊息類型的識別碼。 不是 NULLABLE。|  
|**xml_collection_id**|**int**|包含驗證 XML 結構描述命名空間的集合識別碼。 不是 NULLABLE。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
  
