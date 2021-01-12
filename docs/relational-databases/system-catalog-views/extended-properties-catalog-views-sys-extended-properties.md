---
description: 擴充屬性目錄檢視-sys.extended_properties
title: sys.extended_properties (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.extended_properties
- sys.extended_properties_TSQL
- extended_properties
- extended_properties_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.extended_properties catalog view
ms.assetid: 439b7299-dce3-4d26-b1c7-61be5e0df82a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5a70bfd5e379dae4a68fa871ba7792cdf038d2ad
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092681"
---
# <a name="extended-properties-catalog-views---sysextended_properties"></a>擴充屬性目錄檢視-sys.extended_properties
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對目前資料庫中每個擴充屬性，各傳回一個資料列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|Class - 類別|**tinyint**|可識別內容所在的項目類別。 可以是下列其中一項：<br /><br /> 0 = 資料庫<br /><br /> 1 = 物件或資料行<br /><br /> 2 = 參數<br /><br /> 3 = 結構描述<br /><br /> 4 = 資料庫主體<br /><br /> 5 = 組件<br /><br /> 6 = 類型<br /><br /> 7 = 索引<br /><br /> 10 = XML 結構描述集合<br /><br /> 15 = 訊息類型<br /><br /> 16 = 服務合約<br /><br /> 17 = 服務<br /><br /> 18 = 遠端服務繫結<br /><br /> 19 = 路由<br /><br /> 20 = 資料空間 (檔案群組或資料分割配置)<br /><br /> 21 = 資料分割函數<br /><br /> 22 = 資料庫檔案<br /><br /> 27 = 計畫指南|  
|class_desc|**nvarchar(60)**|擴充屬性所在的類別的描述。 可以是下列其中一項：<br /><br /> DATABASE<br /><br /> OBJECT_OR_COLUMN<br /><br /> 參數<br /><br /> SCHEMA<br /><br /> DATABASE_PRINCIPAL<br /><br /> ASSEMBLY<br /><br /> TYPE<br /><br /> INDEX<br /><br /> XML_SCHEMA_COLLECTION<br /><br /> MESSAGE_TYPE<br /><br /> SERVICE_CONTRACT<br /><br /> SERVICE<br /><br /> REMOTE_SERVICE_BINDING<br /><br /> ROUTE<br /><br /> DATASPACE<br /><br /> PARTITION_FUNCTION<br /><br /> DATABASE_FILE<br /><br /> PLAN_GUIDE|  
|major_id|**int**|擴充屬性所在的項目識別碼，它是根據其類別加以解譯的。 對大部分的項目來說，這是套用至類別代表的識別碼。 下面是非標準主要識別碼的解譯：<br /><br /> 如果 class 是 0，則 major_id 一律為 0。<br /><br /> 如果 class 是 1、2 或 7，則 major_id 就是 object_id。|  
|minor_id|**int**|擴充屬性所在項目的次要識別碼，它是根據其類別加以解譯的。 對於大部分的項目來說，這個值為 0；如果不是，則識別碼如下：<br /><br /> 如果 class = 1， minor_id 就是 column_id (資料行)，否則就是 0 (物件)。<br /><br /> 如果 class = 2，minor_id 就是 parameter_id。<br /><br /> 如果 class = 7，minor_id 就是 index_id。|  
|NAME|**sysname**|內容名稱，另外加上的 class、major_id 和 minor_id，使它成為唯一名稱。|  
|value|**sql_variant**|擴充屬性的值。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [&#40;Transact-sql&#41;的擴充屬性目錄檢視 ](./catalog-views-transact-sql.md)   
 [sys.fn_listextendedproperty &#40;Transact-sql&#41;](../../relational-databases/system-functions/sys-fn-listextendedproperty-transact-sql.md)   
 [sp_addextendedproperty &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql.md)   
 [sp_dropextendedproperty &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-dropextendedproperty-transact-sql.md)   
 [sp_updateextendedproperty &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-updateextendedproperty-transact-sql.md)  
  
