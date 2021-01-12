---
description: sysmergeschemaarticles (Transact-SQL)
title: sysmergeschemaarticles (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- sysmergeschemaarticles_TSQL
- sysmergeschemaarticles
dev_langs:
- TSQL
helpviewer_keywords:
- sysmergeschemaarticles system table
ms.assetid: b5085979-2f76-48e1-bf3b-765a84003dd9
author: cawrites
ms.author: chadam
ms.openlocfilehash: 98dc783b78d26d040789cc3dc46f2f53aaa4dc7c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097376"
---
# <a name="sysmergeschemaarticles-transact-sql"></a>sysmergeschemaarticles (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  追蹤合併式複寫之僅限結構描述的發行項。 這份資料表儲存在發行集和訂閱資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|合併式發行集中僅限結構描述的發行項之名稱。|  
|**type**|**tinyint**|指出僅限結構描述的發行項之類型，它可以是下列項目之一：<br /><br /> **0x20** = 預存程式僅限架構的發行項。<br /><br /> **0x40** = View 僅限架構的發行項或索引視圖僅限架構的發行項。|  
|**objid**|**int**|發行項基底物件的物件識別碼。 它可以是程序、檢視、索引檢視或使用者定義函數的物件識別碼。|  
|**>artid**|**uniqueidentifier**|發行項識別碼。|  
|**description**|**nvarchar(255)**|發行項的描述。|  
|**pre_creation_command**|**tinyint**|當在訂閱資料庫中建立發行項時，所採取的預設動作。<br /><br /> **0 =** 無-如果資料表已存在於訂閱者上，則不會採取任何動作。<br /><br /> **1** = 卸載-在重新建立資料表之前，先將它刪除。<br /><br /> **2** = 刪除-根據子集篩選中的 WHERE 子句發出刪除。<br /><br /> **3** = 截斷-與 **2** 相同，但刪除頁面而不是資料列。 不過，它不用 WHERE 子句。|  
|**pubid**|**uniqueidentifier**|發行集的唯一識別碼。|  
|**status**|**tinyint**|指出僅限結構描述的發行項之狀態，它可以是下列項目之一：<br /><br /> **1** = 未同步-下一次執行快照集代理程式時，發行資料表的初始處理腳本會執行。<br /><br /> **2** = 主動-已執行發行資料表的初始處理腳本。<br /><br /> **5** = 要加入 New_inactive。<br /><br /> **6** = 要加入 New_active。|  
|**creation_script**|**nvarchar(255)**|用來建立目標資料表的選擇性發行項結構描述預先建立指令碼的路徑和名稱。|  
|**schema_option**|**二元 (8)**|給定僅限結構描述的發行項之結構描述產生選項的點陣圖，它可能是一或多個這些值的位元邏輯 OR 結果：<br /><br /> **0x00** = 停用快照集代理程式的腳本，並使用提供的 CreationScript。<br /><br /> **0x01** = 產生物件建立 (CREATE TABLE、建立程式等) 。<br /><br /> **0x10** = 產生對應的叢集索引。<br /><br /> **0x20** = 將使用者自訂資料類型轉換成基底資料類型。<br /><br /> **0x40** = 產生對應的非叢集索引或索引。<br /><br /> **0x80** = 包括主鍵的宣告參考完整性。<br /><br /> **0x100** = 複寫資料表發行項的使用者觸發程式（如果已定義）。<br /><br /> **0x200** = 複製外鍵條件約束。 如果參考的資料表不是發行集的一部份，便不會複寫發行資料表的所有外部索引鍵條件約束。<br /><br /> **0x400** = 複製檢查條件約束。<br /><br /> **0x800** = 複寫預設值。<br /><br /> **0x1000** = 複寫資料行層級定序。<br /><br /> **0x2000** = 複寫與已發行之發行項來源物件相關聯的擴充屬性。<br /><br /> 如果資料表發行項上定義了唯一索引鍵， **0x4000** = 會進行複寫。<br /><br /> **0x8000** = 複寫資料表發行項的主鍵和唯一索引鍵，做為使用 ALTER table 語句的條件約束。<br /><br /> 如需 **schema_option** 可能值的詳細資訊，請參閱 [sp_addmergearticle](../../relational-databases/system-stored-procedures/sp-addmergearticle-transact-sql.md)。|  
|**destination_object**|**sysname**|訂閱資料庫中的目的地物件名稱。 這個值只適用於僅限結構描述的發行項，如預存程序、檢視和 UDF。|  
|**destination_owner**|**sysname**|訂用帳戶資料庫中物件的擁有者（如果不是 **dbo**）。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
