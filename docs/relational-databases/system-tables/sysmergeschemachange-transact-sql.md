---
description: sysmergeschemachange (Transact-SQL)
title: sysmergeschemachange (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- sysmergeschemachange_TSQL
- sysmergeschemachange
dev_langs:
- TSQL
helpviewer_keywords:
- sysmergeschemachange system table
ms.assetid: ae9ce16e-6ee9-4c7c-8210-a9bf2c7efdf0
author: cawrites
ms.author: chadam
ms.openlocfilehash: 2a0315c49710ac324234710d30c223dfb9b27b12
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101999"
---
# <a name="sysmergeschemachange-transact-sql"></a>sysmergeschemachange (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  包含快照集代理程式所產生之已發行的發行項之相關資訊。 這份資料表儲存在發行集和訂閱資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**pubid**|**uniqueidentifier**|發行集的識別碼。|  
|**>artid**|**uniqueidentifier**|發行項的識別碼。|  
|**schemaversion**|**int**|前一個結構描述變更的編號。|  
|**schemaguid**|**uniqueidentifier**|前一個結構描述的唯一識別碼。|  
|**schematype**|**int**|結構描述變更的類型：<br /><br /> **-1** = 無效。<br /><br /> **1** = SQL 命令。<br /><br /> **2** = 架構腳本。<br /><br /> **3** = 原生大量複製程式公用程式 (BCP) 。<br /><br /> **4** = 字元 BCP。<br /><br /> **5** = 上次記錄的產生。<br /><br /> **6** = 上次傳送的世代。<br /><br /> **7** = 目錄。<br /><br /> **8** = 優先順序。<br /><br /> **9** = 保留期限。<br /><br /> **10** = 觸發程式腳本。<br /><br /> **11** = Alter table。<br /><br /> **12** = 全部重新初始化。<br /><br /> **13** = ALTER TABLE (非 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]) 。<br /><br /> **14** = 使用上傳重新初始化。<br /><br /> **15** = 條件約束和索引腳本。<br /><br /> **16** = 中繼資料清除。<br /><br /> **17** = 更新上次傳送的世代。<br /><br /> **18** = 回溯相容性層級。<br /><br /> **19** = 驗證訂閱者資訊。<br /><br /> **20** = 妥善分割。<br /><br /> **21** = 自訂解析程式。<br /><br /> **22** = 發行項處理順序。<br /><br /> **23** = 在交易式發行集中發行。<br /><br /> **24** = 補償錯誤。<br /><br /> **25** = 替代快照集資料夾。<br /><br /> **26** = 僅限下載。<br /><br /> **27** = 刪除追蹤。<br /><br /> **40** = 預先建立快照集腳本。<br /><br /> **45** = 建立後快照集腳本。<br /><br /> **46** = 隨選使用者腳本。<br /><br /> **50** = 快照集標頭開始。<br /><br /> **51** = 快照集標頭結尾。<br /><br /> **52** = 快照集結尾。<br /><br /> **53** = 檔案傳輸通訊協定 (FTP) 位址。<br /><br /> **54** = FTP 埠。<br /><br /> **55** = FTP 子目錄。<br /><br /> **56** = 快照集已壓縮。<br /><br /> **57** = FTP 登入。<br /><br /> **58** = FTP 密碼。<br /><br /> **60** = 系統預先建立腳本。<br /><br /> **61** = 預存程式架構。<br /><br /> **62** = View schema。<br /><br /> **63** = 使用者定義函數架構。<br /><br /> **64** = 視圖索引。<br /><br /> **65** = 擴充屬性。<br /><br /> **66** = 驗證。<br /><br /> **67** = 預先快照集 SQL 命令。<br /><br /> **71** = 動態快照集驗證。<br /><br /> **80** = 系統資料表原生 BCP 9.0。<br /><br /> **81** = 系統資料表字元 BCP 9.0。<br /><br /> **82** = 系統資料表原生 BCP 9.0 (僅限全域) 。<br /><br /> **83** = 系統資料表字元 BCP 9.0 (僅限全域) 。<br /><br /> **84** = 系統資料表原生 BCP 9.0 (輕量) 。<br /><br /> **85** = 系統資料表字元 BCP 9.0 (輕量) 。<br /><br /> **128** = 動態 BCP (位) 。<br /><br /> **131** = 動態原生 BCP。<br /><br /> **132** = 動態字元 BCP。<br /><br /> **208** = 動態系統資料表原生 BCP 9.0。<br /><br /> **209** = 動態系統資料表字元 BCP 9.0。<br /><br /> **210** = 動態系統資料表原生 BCP 9.0 (僅限全域) 。<br /><br /> **211** = 動態系統資料表字元 BCP 9.0 (僅限全域) 。<br /><br /> **212** = 動態系統資料表原生 BCP 9.0 (輕量) 。<br /><br /> **213** = 動態系統資料表字元 BCP 9.0 (輕量) 。<br /><br /> **300** = 資料定義語言 (DDL) 動作。<br /><br /> **1024** = 增量快照集控制。<br /><br /> **1049** = 增量快照集資料夾。<br /><br /> **1074** = 增量快照集開始標頭。<br /><br /> **1075** = 增量快照集結束標頭。<br /><br /> **1076** = 增量快照集結尾。<br /><br /> **1077** = 增量 FTP 位址。<br /><br /> **1078** = 增量 FTP 埠。<br /><br /> **1079** = 增量 FTP 子目錄。<br /><br /> **1080** = 增量壓縮快照集。<br /><br /> **1081** = 增量 FTP 登入。<br /><br /> **1082** = 增量 FTP 密碼。|  
|**schematext**|**Nvarchar (2000)**|指令碼檔案的名稱，或是包含檔案名稱的命令|  
|**schemastatus**|**tinyint**|指出發行項的結構描述變更是否暫止，它可以是下列值之一：<br /><br /> **0** = 非使用中。<br /><br /> **1** = 使用中。<br /><br /> 當架構變更暫止時，這個值會設定為 **1**。|  
|**schemasubtype**|**int**|結構描述變更的子類型：<br /><br /> **1** = ADDCOLUMN<br /><br /> **2** = DROPCOLUMN<br /><br /> **3** = ALTERCOLUMN<br /><br /> **4** = ADDPRIMARYKEY<br /><br /> **5** = ADDUNIQUE<br /><br /> **6** = ADDREFERENCE<br /><br /> **7** = DROPCONSTRAINT<br /><br /> **8** = ADDDEFAULT<br /><br /> **9** = ADDCHECK<br /><br /> **10** = DISABLETRIGGER<br /><br /> **11** = ENABLETRIGGER<br /><br /> **12** = DISABLETRIGGER<br /><br /> **13** = ENABLETRIGGER<br /><br /> **14** = ENABLECONSTRAINT<br /><br /> **15** = DISABLECONSTRAINT<br /><br /> **16** = ENABLECONSTRAINT<br /><br /> **17** = DISABLECONSTRAINT|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
