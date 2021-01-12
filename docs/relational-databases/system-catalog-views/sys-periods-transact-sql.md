---
description: 'sys. 句點 (Transact-sql) '
title: sys. 句點 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: 25e66ed3-2270-4c5c-9f5a-2c0f165a57ca
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 3f932c00e569c57c951f2a708c810ff3dba8c08d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094471"
---
# <a name="sysperiods-transact-sql"></a>sys. 句點 (Transact-sql) 
[!INCLUDE [sqlserver2016](../../includes/applies-to-version/sqlserver2016.md)]

  針對已定義期間的每個資料表，各傳回一個資料列。  
  
|資料行標頭|資料類型|描述|  
|-------------------|---------------|-----------------|  
|NAME|**sysname**|期間的名稱|  
|period_type|**tinyint**|代表期間類型的數值：<br /><br /> 1 = 系統時間週期|  
|period_type_desc|**nvarchar(60)**|資料行類型的文字描述：<br /><br /> SYSTEM_TIME_PERIOD|  
|object_id|**int**|包含 period_type 資料行之資料表的識別碼|  
|start_column_id|**int**|定義較低期間界限之資料行的識別碼|  
|end_column_id|**int**|定義上方句號界限的資料行識別碼|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;的系統檢視 ](../../t-sql/language-reference.md)   
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [sys.all_columns &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-all-columns-transact-sql.md)   
 [sys.system_columns &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/sys-system-columns-transact-sql.md)   
 [查詢 SQL Server 系統目錄常見問題](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)   
 [時態表](../../relational-databases/tables/temporal-tables.md)  
  
