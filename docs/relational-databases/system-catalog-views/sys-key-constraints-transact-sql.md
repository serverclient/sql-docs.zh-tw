---
description: sys.key_constraints (Transact-SQL)
title: sys.key_constraints (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.key_constraints
- key_constraints
- sys.key_constraints_TSQL
- key_constraints_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.key_constraints catalog view
ms.assetid: 0f782d2f-7126-46ab-85b7-bcba44862231
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 2464dab4bfc754b6ef293ecc185858090430b56b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094535"
---
# <a name="syskey_constraints-transact-sql"></a>sys.key_constraints (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  針對每個本身是主索引鍵或唯一條件約束的物件，各包含一個資料列。 包含 **sys. objects。請輸入** PK 和 UQ。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**\<Columns inherited from sys.objects>**||如需此視圖所繼承之資料行的清單，請參閱 [sys. objects &#40;transact-sql&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)。|  
|**unique_index_id**|**int**|在為了強制執行這個條件約束而建立的父物件中，對應的唯一索引鍵之識別碼。|  
|**is_system_named**|**bit**|1 = 名稱是系統所產生。<br /><br /> 0 = 名稱是使用者所提供。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [查詢 SQL Server 系統目錄 FAQ](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)  
  
  
