---
description: sys.sysprotects (Transact-SQL)
title: sys.sys保護 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysprotects
- sys.sysprotects_TSQL
- sys.sysprotects
- sysprotects_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.sysprotects compatibility view
- sysprotects system table
ms.assetid: 49c9658d-fb51-4c77-94a0-fba699b0102d
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 417ef61ac045151c4ea2399d1192fdbff0519736
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095368"
---
# <a name="syssysprotects-transact-sql"></a>sys.sysprotects (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含利用 GRANT 和 DENY 陳述式，套用至資料庫中安全性帳戶之權限的相關資訊。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**id**|**int**|這些權限所要套用至的物件識別碼。|  
|**Uid**|**smallint**|這些權限所要套用至的使用者或群組識別碼。 如果使用者和角色數目超過 32,767 個，則會造成溢位或傳回 NULL。|  
|**action**|**tinyint**|可以有下列權限之一：<br /><br /> 26 = REFERENCES <br /><br /> 178 = CREATE FUNCTION <br /><br /> 193 = SELECT <br /><br /> 195 = INSERT<br /><br /> 196 = 刪除<br /><br /> 197 = 更新<br /><br /> 198 = CREATE TABLE <br /><br /> 203 = CREATE DATABASE <br /><br /> 207 = CREATE VIEW <br /><br /> 222 = CREATE PROCEDURE <br /><br /> 224 = EXECUTE <br /><br /> 228 = BACKUP DATABASE <br /><br /> 233 = CREATE DEFAULT <br /><br /> 235 = BACKUP LOG <br /><br /> 236 = CREATE RULE|  
|**protecttype**|**tinyint**|其值如下：<br /><br /> 204 = GRANT_W_GRANT <br /><br /> 205 = GRANT <br /><br /> 206 = DENY|  
|**資料行**|**varbinary(8000)**|這些 SELECT 或 UPDATE 權限所要套用至的資料行之點陣圖。<br /><br /> Bit 0 = 所有的資料行。<br /><br /> Bit 1 = 將權限套用至該資料行。<br /><br /> NULL = 沒有資訊。|  
|**grantor**|**smallint**|發出 GRANT 或 DENY 權限之使用者的使用者識別碼。 如果使用者和角色數目超過 32,767 個，則會造成溢位或傳回 NULL。|  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
