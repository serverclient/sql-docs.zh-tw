---
description: sys.all_objects (Transact-SQL)
title: sys.all_objects (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 05/20/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.all_objects
- all_objects_TSQL
- all_objects
- sys.all_objects_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.all_objects catalog view
ms.assetid: 547e4be4-a8e4-48ce-9d8d-37b169985081
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 39395b18422259219f43ddc34ac96af389490b84
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102183"
---
# <a name="sysall_objects-transact-sql"></a>sys.all_objects (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  顯示所有結構描述範圍使用者定義物件和系統物件的 UNION。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|NAME|**sysname**|物件名稱。|  
|object_id|**int**|物件識別碼。 在資料庫中，這是唯一的。|  
|principal_id|**int**|如果個別擁有者不是結構描述擁有者，這便是個別擁有者的識別碼。 依預設，結構描述包含的物件就是結構描述擁有者所擁有的物件。 不過，您也可以利用 ALTER AUTHORIZATION 陳述式來變更擁有權，指定另一個擁有者。<br /><br /> 如果沒有替代的個別擁有者，這便是 NULL。<br /><br /> 如果物件類型是下列其中一項，便是 NULL：<br /><br /> C = CHECK 條件約束<br /><br /> D = DEFAULT (條件約束或獨立式)<br /><br /> F = FOREIGN KEY 條件約束<br /><br /> PK = PRIMARY KEY 條件約束<br /><br /> R = 規則 (舊式、獨立式)<br /><br /> TA = 組件 (CLR) 觸發程序<br /><br /> TR = SQL 觸發程序<br /><br /> UQ = UNIQUE 條件約束|  
|schema_id|**int**|包含物件的結構描述識別碼。<br /><br /> 對於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所包含的所有結構描述範圍系統物件而言，這個值一律會在 (schema_id('sys'), schema_id('INFORMATION_SCHEMA')) 中。|  
|parent_object_id|**int**|這個物件所屬的物件識別碼。<br /><br /> 0 = 不是子物件。|  
|類型|**char(2)**|物件類型：<br /><br /> AF = 彙總函式 (CLR)<br /><br /> C = CHECK 條件約束<br /><br /> D = DEFAULT (條件約束或獨立式)<br /><br /> F = FOREIGN KEY 條件約束<br /><br /> FN = SQL 純量函數<br /><br /> FS = 組件 (CLR) 純量函數<br /><br /> FT = 組件 (CLR) 資料表值函式<br /><br /> IF = SQL 嵌入資料表值函式<br /><br /> IT = 內部資料表<br /><br /> P = SQL 預存程序<br /><br /> PC = 組件 (CLR) 預存程序<br /><br /> PG = 計畫指南<br /><br /> PK = PRIMARY KEY 條件約束<br /><br /> R = 規則 (舊式、獨立式)<br /><br /> RF = 複寫篩選程序<br /><br /> S = 系統基底資料表<br /><br /> SN = 同義字<br /><br /> SO = 序列物件<br /><br /> SQ = 服務佇列<br /><br /> TA = 組件 (CLR) DML 觸發程序<br /><br /> TF = SQL 資料表值函式<br /><br /> TR = SQL DML 觸發程序<br /><br /> TT = 資料表類型<br /><br /> U = 資料表 (使用者定義)<br /><br /> UQ = UNIQUE 條件約束<br /><br /> V = 檢視<br /><br /> X = 擴充預存程序|  
|type_desc|**nvarchar(60)**|物件類型的描述。 AGGREGATE_FUNCTION<br /><br /> CHECK_CONSTRAINT<br /><br /> DEFAULT_CONSTRAINT<br /><br /> FOREIGN_KEY_CONSTRAINT<br /><br /> SQL_SCALAR_FUNCTION<br /><br /> CLR_SCALAR_FUNCTION<br /><br /> CLR_TABLE_VALUED_FUNCTION<br /><br /> SQL_INLINE_TABLE_VALUED_FUNCTION<br /><br /> INTERNAL_TABLE<br /><br /> SQL_STORED_PROCEDURE<br /><br /> CLR_STORED_PROCEDURE<br /><br /> PLAN_GUIDE<br /><br /> PRIMARY_KEY_CONSTRAINT<br /><br /> RULE<br /><br /> REPLICATION_FILTER_PROCEDURE<br /><br /> SYSTEM_TABLE<br /><br /> SYNONYM<br /><br /> SERVICE_QUEUE<br /><br /> CLR_TRIGGER<br /><br /> SQL_TABLE_VALUED_FUNCTION<br /><br /> SQL_TRIGGER<br /><br /> TABLE_TYPE<br /><br /> USER_TABLE<br /><br /> UNIQUE_CONSTRAINT<br /><br /> VIEW<br /><br /> EXTENDED_STORED_PROCEDURE|  
|create_date|**datetime**|物件的建立日期。|  
|modify_date|**datetime**|上次利用 ALTER 陳述式來修改物件的日期。 如果物件是資料表或視圖，當建立或修改資料表或視圖上的索引時，modify_date 也會變更。|  
|is_ms_shipped|**bit**|內部 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 元件所建立的物件。|  
|is_published|**bit**|已發行物件。|  
|is_schema_published|**bit**|僅發行物件的結構描述。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)   
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)   
 [sys.system_objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-system-objects-transact-sql.md)  
  
  
