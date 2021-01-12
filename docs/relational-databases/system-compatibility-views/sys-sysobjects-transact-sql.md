---
description: sys.sysobjects (Transact-SQL)
title: sys.sys(Transact-sql) 的物件 |Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.sysobjects_TSQL
- sysobjects
- sysobjects_TSQL
- sys.sysobjects
dev_langs:
- TSQL
helpviewer_keywords:
- sys.sysobjects compatibility view
- sysobjects system table
ms.assetid: 44fdc387-67b0-4139-8bf5-ed26cf640cd1
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 30d2b16bb9e0366752418fb3e958cdc0600db14b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099092"
---
# <a name="syssysobjects-transact-sql"></a>sys.sysobjects (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  針對資料庫內所建立的每個物件，如條件約束、預設值、記錄、規則和預存程序，各包含一個資料列。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|NAME|**sysname**|物件名稱|  
|id|**int**|物件識別碼。|  
|xtype|**char(2)**|物件類型。 可以是下列其中一種物件類型：<br /><br /> AF = 彙總函式 (CLR)<br /><br /> C = CHECK 條件約束<br /><br /> D = 預設值或 DEFAULT 條件約束<br /><br /> F = FOREIGN KEY 條件約束<br /><br /> L = 記錄<br /><br /> FN = 純量函數<br /><br /> FS = 組件 (CLR) 純量函數<br /><br /> FT = 組件 (CLR) 資料表值函式<br /><br /> IF = 內嵌資料表函數<br /><br /> IT = 內部資料表<br /><br /> P = 預存程序<br /><br /> PC = 組件 (CLR) 預存程序<br /><br /> PK = PRIMARY KEY 條件約束 (類型是 K)<br /><br /> RF = 複寫篩選預存程序<br /><br /> S = 系統資料表<br /><br /> SN = 同義字<br /><br /> SQ = 服務佇列<br /><br /> TA = 組件 (CLR) DML 觸發程序<br /><br /> TF = 資料表函數<br /><br /> TR = SQL DML 觸發程序<br /><br /> TT = 資料表類型<br /><br /> U = 使用者資料表<br /><br /> UQ = UNIQUE 條件約束 (類型是 K)<br /><br /> V = 檢視<br /><br /> X = 擴充預存程序|  
|uid|**smallint**|物件擁有者的結構描述識別碼。 如果是從舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 升級而來的資料庫，結構描述識別碼會等於擁有者的使用者識別碼。 如果使用者和角色數目超過 32,767 個，則會造成溢位或傳回 NULL。<br /><br /> **\* \* 重要 \* 事項 \*** ：如果您使用下列任何一個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] DDL 語句，則必須使用 [sys. objects](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)目錄檢視，而不是 sys.sys物件。<br /><br /> 建立 &#124; ALTER &#124; DROP USER<br /><br /> 建立 &#124; ALTER &#124; DROP ROLE<br /><br /> 建立 &#124; ALTER &#124; DROP APPLICATION ROLE<br /><br /> CREATE SCHEMA<br /><br /> ALTER AUTHORIZATION ON OBJECT|  
|info|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|status|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|base_schema_ver|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|replinfo|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|parent_obj|**int**|父物件的物件識別碼。 例如，如果它是觸發程序或條件約束，便是資料表識別碼。|  
|crdate|**datetime**|物件的建立日期。|  
|ftcatid|**smallint**|登錄了全文檢索索引的所有使用者資料表之全文檢索目錄識別碼，所有未登錄的使用者資料表都是 0。|  
|schema_ver|**int**|每次資料表的結構描述變更時，都會遞增的版本號碼。 永遠傳回 0。|  
|stats_schema_ver|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|類型|**char(2)**|物件類型。 可以是下列值之一：<br /><br /> AF = 彙總函式 (CLR)<br /><br /> C = CHECK 條件約束<br /><br /> D = 預設值或 DEFAULT 條件約束<br /><br /> F = FOREIGN KEY 條件約束<br /><br /> FN = 純量函數<br /><br /> FS = 組件 (CLR) 純量函數<br /><br /> FT = 組件 (CLR) 資料表值函式 IF = 內嵌資料表函數<br /><br /> IT - 內部資料表<br /><br /> K = PRIMARY KEY 或 UNIQUE 條件約束<br /><br /> L = 記錄<br /><br /> P = 預存程序<br /><br /> PC = 組件 (CLR) 預存程序<br /><br /> R = 規則<br /><br /> RF = 複寫篩選預存程序<br /><br /> S = 系統資料表<br /><br /> SN = 同義字<br /><br /> SQ = 服務佇列<br /><br /> TA = 組件 (CLR) DML 觸發程序<br /><br /> TF = 資料表函數<br /><br /> TR = SQL DML 觸發程序<br /><br /> TT = 資料表類型<br /><br /> U = 使用者資料表<br /><br /> V = 檢視<br /><br /> X = 擴充預存程序|  
|userstat|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|sysstat|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|indexdel|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|refdate|**datetime**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|version|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|deltrig|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|instrig|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|updtrig|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|seltrig|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|category|**int**|用於發行集、條件約束和識別。|  
|快取|**smallint**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  
