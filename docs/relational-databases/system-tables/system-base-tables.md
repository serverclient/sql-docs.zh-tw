---
description: 系統基底資料表
title: 系統基表 |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- system base tables [SQL Server]
- hobt [SQL Server]
- base tables
ms.assetid: 31f2df90-651f-4699-8067-19f59b60904f
author: cawrites
ms.author: chadam
ms.openlocfilehash: c5805fd84eb9bce1092cdfae59a9711c2d63edb8
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102056"
---
# <a name="system-base-tables"></a>系統基底資料表
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  系統基底資料表其實是儲存特定資料庫之中繼資料的基礎資料表。 **Master** 資料庫在這個方面是特殊的，因為它包含其他任何資料庫中找不到的其他資料表。 這些資料表包含整個伺服器範圍所保存的中繼資料。  
  
> [!IMPORTANT]  
>  系統基底資料表僅用於 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 內部，不供一般客戶使用。 它們隨時可以變更，而且不保證其相容性。  
  
## <a name="system-base-table-metadata"></a>系統基底資料表中繼資料  
 具有資料庫之 CONTROL、ALTER 或 VIEW DEFINITION 許可權的被授與者可以在 **sys. objects** 目錄檢視中查看系統基表中繼資料。 被授與者也可以使用內建函數（例如 [OBJECT_NAME](../../t-sql/functions/object-name-transact-sql.md) 和 [OBJECT_ID](../../t-sql/functions/object-id-transact-sql.md)）來解析系統基表的名稱和物件識別碼。  
  
 若要繫結至系統基底資料表，使用者必須使用專用管理員連接 (DAC)，連接到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體。 嘗試在不使用 DAC 連接的情況下，從系統基底資料表執行 SELECT 查詢時，會產生錯誤。  
  
> [!IMPORTANT]  
>  使用 DAC 存取系統基底資料表的設計僅適用於 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 人員，因此這不是受支援的客戶案例。  
  
## <a name="system-base-tables"></a>系統基底資料表  
 下表列出並描述 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的每個系統基底資料表。  
  
|基底資料表|描述|  
|----------------|-----------------|  
|**sys.sysschobjs**|存在於每個資料庫中。 每一個資料列都代表資料庫中的一個物件。|  
|**sys.sysbinobjs**|存在於每個資料庫中。 資料庫中的每個 Service Broker 實體都包含一個資料列。 Service Broker 實體包括下列各項：<br /><br /> 訊息類型<br /><br /> 服務合約<br /><br /> 服務<br /><br /> 名稱和類型會使用修復過的二進位定序。|  
|**sys.sysclsobjs**|存在於每個資料庫中。 每個分類的實體都包含一個資料列，該實體會共用相同的通用屬性，包括：<br /><br /> 組件<br /><br /> 備份裝置<br /><br /> 全文檢索目錄<br /><br /> 分割區函數<br /><br /> 分割區配置<br /><br /> 檔案群組<br /><br /> 模糊化索引鍵|  
|**sys.sysnsobjs**|存在於每個資料庫中。 每個命名空間範圍的實體都包含一個資料列。 此資料表用於儲存 XML 集合實體。|  
|**sys.syscolpars**|存在於每個資料庫中。 資料表、檢視或資料表值函數中的每個資料行都包含一個資料列。 對於程序或函數的每個參數，它也包含資料列。|  
|**sys.systypedsubobjs**|存在於每個資料庫中。 每個輸入的子實體都包含一個資料列。 只有資料分割函數的參數屬於這個類別目錄。|  
|**sys.sysidxstats**|存在於每個資料庫中。 針對資料表和索引檢視的每個索引或統計資料，各包含一個資料列。<br /><br /> 注意：除了堆積) 之外，每個索引 (都會與與索引同名的統計資料相關聯。|  
|**sys.sysiscols**|存在於每個資料庫中。 每個保存的索引和統計資料資料行都包含一個資料列。|  
|**sys.sysscalartypes**|存在於每個資料庫中。 每個使用者自訂或系統類型都包含一個資料列。|  
|**sys.sysdbreg**|只存在於 **master** 資料庫中。 每個註冊的資料庫都包含一個資料列。|  
|**sys.sysxsrvs**|只存在於 **master** 資料庫中。 每個本機、連結或遠端伺服器都包含一個資料列。|  
|**sys.sysrmtlgns**|這個系統基表只存在於 **master** 資料庫中。 每個遠端登入對應都包含一個資料列。 這用來將宣告為來自對應伺服器的內送登入對應至實際的本機登入。|  
|**sys.syslnklgns**|只存在於 **master** 資料庫中。 每個連結登入對應都包含一個資料列。 連結登入對應是由從遠端伺服器發出到對應連結伺服器的遠端程序呼叫和分散式查詢所使用。|  
|**sys.sysxlgns**|只存在於 **master** 資料庫中。 每個伺服器主體都包含一個資料列。|  
|**sys.sysdbfiles**|存在於每個資料庫中。 如果資料行 **dbid** 為零，則資料列代表屬於這個資料庫的檔案。 在 master 資料庫中， **資料** 行 **dbid** 可以是非零。 如果是這個情況，資料列代表主檔案。|  
|**sys.sysusermsg**|只存在於 **master** 資料庫中。 每個資料列都代表一個使用者自訂的錯誤訊息。|  
|**sys.sysprivs**|存在於每個資料庫中。 每個資料庫或伺服器層級的權限都包含一個資料列。<br /><br /> 注意：伺服器層級許可權會儲存在 **master** 資料庫中。|  
|**sys.sysowners**|存在於每個資料庫中。 每個資料列都代表一個資料庫主體。|  
|**sys.sysobjkeycrypts**|存在於每個資料庫中。 與物間相關聯的每個對稱金鑰、加密或密碼編譯屬性都包含一個資料列。|  
|**sys.syscerts**|存在於每個資料庫中。 資料庫中的每個憑證都包含一個資料列。|  
|**sys.sysasymkeys**|存在於每個資料庫中。 每個資料列都代表一個非對稱金鑰。|  
|**sys.ftinds**|存在於每個資料庫中。 資料庫中的每個全文檢索索引都包含一個資料列。|  
|**sys.sysxprops**|存在於每個資料庫中。 每個擴充屬性都包含一個資料列。|  
|**sys.sysallocunits**|存在於每個資料庫中。 每個儲存配置單位都包含一個資料列。|  
|**sys.sysrowsets**|存在於每個資料庫中。 索引或堆積的每個資料分割資料列集都包含一個資料列。|  
|**sys.sysrowsetrefs**|存在於每個資料庫中。 資料列集參考的每個索引都包含一個資料列。|  
|**sys.syslogshippers**|只存在於 **master** 資料庫中。 針對每個資料庫鏡像見證都包含一個資料列。|  
|**sys.sysremsvcbinds**|存在於每個資料庫中。 每個遠端服務繫結都包含一個資料列。|  
|**sys.sysconvgroup**|存在於每個資料庫中。 Service Broker 中的每個服務執行個體都包含一個資料列。|  
|**sys.sysxmitqueue**|存在於每個資料庫中。 每個 Service Broker 傳輸佇列都包含一個資料列。|  
|**sys.sysdesend**|存在於每個資料庫中。 Service Broker 轉換的每個傳送端點都包含一個資料列。|  
|**sys.sysdercv**|存在於每個資料庫中。 Service Broker 轉換的每個接收端點都包含一個資料列。|  
|**sys.sysendpts**|只存在於 **master** 資料庫中。 在伺服器中建立的每個端點都包含一個資料列。|  
|**sys.syswebmethods**|只存在於 **master** 資料庫中。 在 SOAP 之 HTTP 端點 (在伺服器中建立的) 上定義的每個 SOAP 方法都包含一個資料列。|  
|**sys.sysqnames**|存在於每個資料庫中。 每個命名空間或符合 4 位元組識別碼語彙基元的名稱都包含一個資料列。|  
|**sys.sysxmlcomponent**|存在於每個資料庫中。 每個資料列都代表一個 XML 結構描述元件。|  
|**sys.sysxmlfacet**|存在於每個資料庫中。 XML 類型定義的每個 XML Facet (限制) 都包含一個資料列。|  
|**sys.sysxmlplacement**|存在於每個資料庫中。 XML 元件的每個 XML 位置都包含一個資料列。|  
|**sys.syssingleobjrefs**|存在於每個資料庫中。 每個一般 N 對 1 參考都包含一個資料列。|  
|**sys.sysmultiobjrefs**|存在於每個資料庫中。 每個一般 N 對 N 參考都包含一個資料列。|  
|**sys.sysobjvalues**|存在於每個資料庫中。 實體的每個一般值屬性都包含一個資料列。|  
|**sys.sysguidrefs**|存在於每個資料庫中。 每個 GUID 分類的識別碼參考都包含一個資料列。|  
  
## <a name="updating-system-base-tables"></a>更新系統基表    
您可透過系統目錄檢視來查看系統資料表中的資料。 若要更新系統基表中的中繼資料，請使用適當的 TSQL 介面 (例如) 的 DDL 語句。 您無法手動更新系統資料表。 當您執行系統資料表的直接更新時，SQL Server 會報告下列訊息。

### <a name="a-system-table-is-manually-updated"></a>系統資料表會以手動方式更新
訊息 17659：警告：已直接在資料庫識別碼 <id> 中更新系統資料表識別碼 <id>，可能未能保持快取的連貫性。 應該重新啟動 SQL Server。

### <a name="starting-a-database-with-a-system-table-that-was-manually-updated"></a>使用手動更新的系統資料表啟動資料庫
訊息3859：警告：已直接在資料庫識別碼17中更新系統目錄，最近在 date_time。

### <a name="executing-the-dbcc_checkdb-command-after-a-system-table-is-manually-updated"></a>手動更新系統資料表後執行 DBCC_CHECKDB 命令
訊息3859：警告：已直接在資料庫識別碼17中更新系統目錄，最近在 date_time。

如果您對系統資料表執行手動更新，並遇到問題，系統可能會要求您從備份還原，或將資料從受影響的資料庫複製到新的資料庫。 深入瞭解 [使用者動作錯誤訊息](../errors-events/mssqlserver-8992-database-engine-error.md#user-action)。