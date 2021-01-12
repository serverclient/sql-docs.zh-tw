---
description: sys.syscacheobjects (Transact-SQL)
title: sys.syscacheobjects (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.syscacheobjects_TSQL
- sys.syscacheobjects
- syscacheobjects
- syscacheobjects_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- syscacheobjects system table
- sys.syscacheobjects compatibility view
ms.assetid: 9b14f37c-b7f5-4f71-b070-cce89a83f69e
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 4b33b97a5b3753d63e1df4759d26970cf6359ee2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097800"
---
# <a name="syssyscacheobjects-transact-sql"></a>sys.syscacheobjects (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含如何使用快取的相關資訊。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**bucketid**|**int**|值區識別碼。 值表示 0 至 (目錄大小 - 1) 的範圍。 目錄大小是雜湊表的大小。|  
|**cacheobjtype**|**nvarchar(17)**|快取中的物件類型：<br /><br /> 編譯的計畫<br /><br /> 可執行的計畫<br /><br /> 剖析樹狀結構<br /><br /> 資料指標<br /><br /> 擴充預存程序|  
|**objtype**|**nvarchar(8)**|物件的類型：<br /><br /> 預存程序<br /><br /> 準備陳述式<br /><br /> 臨機操作查詢 ([!INCLUDE[tsql](../../includes/tsql-md.md)] 以 **sqlcmd** 或 **osql** 公用程式的語言事件形式提交，而不是遠端程序呼叫) <br /><br /> ReplProc (複寫程序)<br /><br /> 觸發程序<br /><br /> 檢視<br /><br /> 預設值<br /><br /> 使用者資料表<br /><br /> 系統資料表<br /><br /> 勾選<br /><br /> 規則|  
|**objid**|**int**|用來查閱快取中物件的主要索引鍵之一。 這是儲存在資料庫物件 **sysobjects** 中的物件識別碼， (程式、視圖、觸發程式等) 。 針對臨機操作或備妥的 SQL 等快取物件， **objid** 是內部產生的值。|  
|**dbid**|**smallint**|快取物件編譯所在的資料庫識別碼。|  
|**dbidexec**|**smallint**|執行查詢時所在的資料庫識別碼。<br /><br /> 對於大部分的物件而言， **dbidexec** 的值與 **dbid** 相同。<br /><br /> 針對系統檢視， **dbidexec** 是執行查詢的資料庫識別碼。<br /><br /> 若為特定查詢， **dbidexec** 為0。 這表示 **dbidexec** 的值與 **dbid** 相同。|  
|**Uid**|**smallint**|指出隨選查詢計畫和準備計畫的計畫建立者。<br /><br /> -2 = 提交的批次不會隨著隱含的名稱解析而不同，不同的使用者可以共用它們。 這是慣用的方法。 任何其他值都代表在資料庫中提交查詢之使用者的使用者識別碼。<br /><br /> 如果使用者和角色數目超過 32,767 個，則會造成溢位或傳回 NULL。|  
|**refcounts**|**int**|參考這個快取物件的其他快取物件數目。 計數 1 是基底。|  
|**usecounts**|**int**|自開始之後使用這個快取物件的次數。|  
|**pagesused**|**int**|快取物件所耗用的分頁數目。|  
|**setopts**|**int**|影響編譯計畫的 SET 選項設定。 這些設定是快取索引鍵的一部分。 變更這個資料行的值指出使用者已修改 SET 選項。 這些選項包括：<br /><br /> **ANSI_PADDING**<br /><br /> **FORCEPLAN**<br /><br /> **CONCAT_NULL_YIELDS_NULL**<br /><br /> **ANSI_WARNINGS**<br /><br /> **ANSI_NULLS**<br /><br /> **QUOTED_IDENTIFIER**<br /><br /> **ANSI_NULL_DFLT_ON**<br /><br /> **ANSI_NULL_DFLT_OFF**|  
|**langid**|**smallint**|語言識別碼。 建立快取物件之連接的語言識別碼。|  
|**dateformat**|**smallint**|建立快取物件之連接的日期格式。|  
|**status**|**int**|指出快取物件是否為資料指標計畫。 目前只會使用最不重要的位元。|  
|**lasttime**|**bigint**|只是為了與舊版相容。 永遠傳回 0。|  
|**maxexectime**|**bigint**|只是為了與舊版相容。 永遠傳回 0。|  
|**avgexectime**|**bigint**|只是為了與舊版相容。 永遠傳回 0。|  
|**lastreads**|**bigint**|只是為了與舊版相容。 永遠傳回 0。|  
|**lastwrites**|**bigint**|只是為了與舊版相容。 永遠傳回 0。|  
|**sqlbytes**|**int**|提交的程序定義或批次的長度 (以位元組為單位)。|  
|**sql**|**nvarchar(3900)**|模組定義或提交之批次的前 3900 個字元。|  
  
## <a name="see-also"></a>另請參閱  
 [相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)  
  
  

