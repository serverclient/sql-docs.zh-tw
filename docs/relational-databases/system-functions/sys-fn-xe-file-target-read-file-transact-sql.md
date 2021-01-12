---
description: sys.fn_xe_file_target_read_file (Transact-SQL)
title: sys.fn_xe_file_target_read_file (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/22/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- fn_xe_file_target_read_file_TSQL
- fn_xe_file_target_read_file
- sys.fn_xe_file_target_read_file_TSQL
- sys.fn_xe_file_target_read_file
dev_langs:
- TSQL
helpviewer_keywords:
- extended events [SQL Server], functions
- fn_xe_file_target_read_file function
- sys.fn_xe_file_target_read_file function
ms.assetid: cc0351ae-4882-4b67-b0d8-bd235d20c901
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 8af7ee0bc6c899026e51110264f2b28c2f579dd7
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096338"
---
# <a name="sysfn_xe_file_target_read_file-transact-sql"></a>sys.fn_xe_file_target_read_file (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  讀取擴充事件非同步檔案目標所建立的檔案。 系統會以 XML 格式針對每個資料列傳回一個事件。  
  
> [!WARNING]  
>  [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 和 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 接受以 XEL 和 XEM 格式產生的追蹤結果。 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 擴充的事件只支援 XEL 格式的追蹤結果。 我們建議您使用 SQL Server Management Studio 來讀取 XEL 格式的追蹤結果。    
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sys.fn_xe_file_target_read_file ( path, mdpath, initial_file_name, initial_offset )  
```  
  
## <a name="arguments"></a>引數  
 *path*  
 要讀取之檔案的路徑。 *路徑* 可以包含萬用字元並包含檔案名。 *路徑* 為 **Nvarchar (260)**。 沒有預設值。 在 Azure SQL Database 的內容中，這個值是 Azure 儲存體中檔案的 HTTP URL。
  
 *mdpath*  
 中繼資料檔案的路徑，該檔案對應至 *path* 引數所指定的檔案。 *mdpath* 是 **Nvarchar (260)**。 沒有預設值。 從 SQL Server 2016 開始，可以將這個參數指定為 null。
  
> [!NOTE]  
>  [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 不需要 *mdpath* 參數。 但是，維護它是為了保留與舊版 SQL Server 產生之記錄檔之間的相容性。  
  
 *initial_file_name*  
 要從 *路徑* 讀取的第一個檔案。 *initial_file_name* 是 **Nvarchar (260)**。 沒有預設值。 如果指定 **null** 做為引數，就會讀取 *路徑* 中找到的所有檔案。  
  
> [!NOTE]  
>  *initial_file_name* 和 *initial_offset* 是成對的引數。 如果您指定任何一個引數的值，就必須指定另一個引數的值。  
  
 *initial_offset*  
 用來指定先前所讀取的上個位移，並略過所有事件直到位移為止 (含)。 從指定的位移之後，開始事件列舉。 *initial_offset* 為 **Bigint**。 如果指定 **null** 做為引數，就會讀取整個檔案。  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|module_guid|**uniqueidentifier**|事件模組 GUID。 不可為 Null。|  
|package_guid|**uniqueidentifier**|事件封裝 GUID。 不可為 Null。|  
|object_name|**nvarchar(256)**|事件的名稱。 不可為 Null。|  
|event_data|**nvarchar(max)**|事件內容 (XML 格式)。 不可為 Null。|  
|file_name|**nvarchar(260)**|包含此事件之檔案的名稱。 不可為 Null。|  
|file_offset|**bigint**|包含此事件之檔案中的區塊位移。 不可為 Null。|  
|timestamp_utc|**datetime2**|**適用於**：[!INCLUDE[ssSQLv14](../../includes/sssqlv14-md.md)] 及更新版本和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。<br /><br />事件的日期和時間 (UTC 時區) 。 不可為 Null。|  

  
## <a name="remarks"></a>備註  
 藉由在中執行 **sys.fn_xe_file_target_read_file** 來讀取大型結果集 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 可能會導致錯誤。 使用 [ **結果至** 檔案模式] (**Ctrl + Shift + F**) 將大型結果集匯出至檔案，並改為以其他工具讀取檔案。  
  
## <a name="permissions"></a>權限  
 需要伺服器的 VIEW SERVER STATE 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-retrieving-data-from-file-targets"></a>A. 從檔案目標中擷取資料  
 下列範例會從所有檔案中取得所有資料列。 在這個範例中，檔案目標和中繼檔都位於 C:\ 磁碟機的追蹤資料夾中。  
  
```  
SELECT * FROM sys.fn_xe_file_target_read_file('C:\traces\*.xel', 'C:\traces\metafile.xem', null, null);  
```  
  
## <a name="see-also"></a>另請參閱  
 [擴充的事件動態管理檢視](../../relational-databases/system-dynamic-management-views/extended-events-dynamic-management-views.md)   
 [&#40;Transact-sql&#41;的擴充事件目錄檢視 ](../../relational-databases/system-catalog-views/extended-events-catalog-views-transact-sql.md)   
 [擴充事件](../../relational-databases/extended-events/extended-events.md)  
  
  
