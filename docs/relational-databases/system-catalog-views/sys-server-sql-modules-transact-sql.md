---
description: sys.server_sql_modules (Transact-SQL)
title: sys.server_sql_modules (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.server_sql_modules
- sys.server_sql_modules_TSQL
- server_sql_modules_TSQL
- server_sql_modules
dev_langs:
- TSQL
helpviewer_keywords:
- sys.server_sql_modules catalog view
ms.assetid: 9ef9a8b9-c470-4a61-b0c4-ee24ad871d63
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 7ffae9eb88425f849b30d7b2b8fc8e63cb840870
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094484"
---
# <a name="sysserver_sql_modules-transact-sql"></a>sys.server_sql_modules (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含 TR 類型伺服器層級觸發程序的 SQL 模組集。 您可以將這個關聯性聯結到 sys.server_triggers。 Tuple (object_id) 是關聯性的索引鍵。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|object_id|**int**|這是對定義這個模組的伺服器層級觸發程序的 FOREIGN KEY 參考。|  
|**definition**|**nvarchar(max)**|定義這個模組的 SQL 文字。<br /><br /> NULL = 已加密。|  
|**uses_ansi_nulls**|**bit**|建立模組時，是將 ANSI NULLS 設定選項設為 ON。|  
|**uses_quoted_identifier**|**bit**|建立模組時，是將 QUOTED IDENTIFIER 設定選項設為 ON。|  
|**execute_as_principal_id**|**int**|EXECUTE AS 伺服器主體的識別碼。<br /><br /> 在預設或 EXECUTE AS CALLER 的情況下為 NULL。<br /><br /> 如果 EXECUTE AS SELF EXECUTE AS principal-2 = EXECUTE AS OWNER，則為指定之主體的識別碼。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)  
  
  
