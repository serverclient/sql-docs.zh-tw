---
description: 'sys.time_zone_info (Transact-sql) '
title: sys.time_zone_info (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/06/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.time_zone_info
- sys.time_zone_info_TSQL
- time_zone_info
- time_zone_info_TSQL
helpviewer_keywords:
- sys.time_zone_info system table
ms.assetid: 3f51a9a4-75f8-4a11-9552-8bf6118b68da
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 56d3bd8f387d0ed9b75e9073dba9c60f39267fe5
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094412"
---
# <a name="systime_zone_info-transact-sql"></a>sys.time_zone_info (Transact-sql) 
[!INCLUDE [sqlserver2016-asdb-asdbmi-asa](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi-asa.md)]

  傳回受支援時區的相關資訊。 電腦上安裝的所有時區都會儲存在下列登錄 hive 中：  
`KEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Time Zones`.  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|Windows standard 格式的時區名稱。 例如， **澳大利亞標準時間** 或 **中歐標準時間**。|  
|**current_utc_offset**|**Nvarchar (12)**|UTC 的目前位移。 例如， **+ 01:00** 或 **-07:00**。|  
|**is_currently_dst**|**bit**|如果目前觀察日光節約時間，則為 True。|  
  
## <a name="see-also"></a>另請參閱  
 [GETUTCDATE &#40;Transact-sql&#41;](../../t-sql/functions/getutcdate-transact-sql.md)   
 [AT TIME ZONE &#40;Transact-SQL&#41;](../../t-sql/queries/at-time-zone-transact-sql.md)   
 [日期和時間資料類型與函式 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)   
 [&#40;Transact-sql&#41;的全伺服器設定目錄檢視 ](../../relational-databases/system-catalog-views/server-wide-configuration-catalog-views-transact-sql.md)  
