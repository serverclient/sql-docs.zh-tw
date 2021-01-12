---
description: sys.sysindexes (Transact-SQL)
title: sys.sys(Transact-sql) 的索引 |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sysindexes
- sysindexes_TSQL
- sys.sysindexes
- sys.sysindexes_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysindexes system table
- sys.sysindexes compatibility view
ms.assetid: f483d89c-35c4-4a08-8f8b-737fd80d13f5
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: cf8982115b1a4399c327aefc66a5e99a1d46c575
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094189"
---
# <a name="syssysindexes-transact-sql"></a>sys.sysindexes (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對目前資料庫中每個索引和資料表，各包含一個資料列。 這份檢視不支援 XML 索引。 此視圖不完整支援資料分割資料表和索引;請改用 [sys. 索引](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md) 目錄查看。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssnoteCompView](../../includes/ssnotecompview-md.md)]  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**id**|**int**|索引所屬的資料表識別碼。|  
|**status**|**int**|系統狀態資訊。<br /><br /> [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**first**|**binary(6)**|指向第一個頁面或根頁面的指標。<br /><br /> **Indid** = 0 時未使用。<br /><br /> Null = **indid** > 1 時，索引會進行分割。<br /><br /> Null = 當 **indid** 為0或1時，資料表會進行分割。|  
|**indid**|**smallint**|索引的識別碼：<br /><br /> 0 = 堆積<br /><br /> 1 = 叢集索引<br /><br /> >1 = 非叢集索引|  
|**root**|**binary(6)**|針對 **indid** >= 1， **root** 是根頁面的指標。<br /><br /> **Indid** = 0 時未使用。<br /><br /> Null = **indid** > 1 時，索引會進行分割。<br /><br /> Null = 當 **indid** 為0或1時，資料表會進行分割。|  
|**minlen**|**smallint**|資料列的大小下限。|  
|**keycnt**|**smallint**|索引鍵數目。|  
|**groupid**|**smallint**|建立物件的檔案群組識別碼。<br /><br /> Null = **indid** > 1 時，索引會進行分割。<br /><br /> Null = 當 **indid** 為0或1時，資料表會進行分割。|  
|**dpages**|**int**|如果是 **indid** = 0 或 **indid** = 1， **dpages** 就是所使用的資料頁計數。<br /><br /> 針對 **indid** > 1， **dpages** 是使用的索引頁計數。<br /><br /> 0 = **indid** > 1 時，索引會進行分割。<br /><br /> 0 = 當 **indid** 為0或1時，資料表會進行分割。<br /><br /> 如果發生資料列溢位，便不產生精確的結果。|  
|**保留**|**int**|如果是 **indid** = 0 或 **indid** = 1， **reserved** 就是配置給所有索引和資料表資料的頁面計數。<br /><br /> 針對 **indid** > 1， **reserved** 是針對索引所配置的頁面計數。<br /><br /> 0 = **indid** > 1 時，索引會進行分割。<br /><br /> 0 = 當 **indid** 為0或1時，資料表會進行分割。<br /><br /> 如果發生資料列溢位，便不產生精確的結果。|  
|**使用**|**int**|如果是 **indid** = 0 或 **indid** = 1，則 **使用** 的是所有索引和資料表資料所用總頁數的計數。<br /><br /> 針對 **indid** > 1， **使用** 的是用於索引的頁面計數。<br /><br /> 0 = **indid** > 1 時，索引會進行分割。<br /><br /> 0 = 當 **indid** 為0或1時，資料表會進行分割。<br /><br /> 如果發生資料列溢位，便不產生精確的結果。|  
|**rowcnt**|**bigint**|以 **indid** = 0 和 **indid** = 1 為基礎的資料層級資料列計數。<br /><br /> 0 = **indid** > 1 時，索引會進行分割。<br /><br /> 0 = 當 **indid** 為0或1時，資料表會進行分割。|  
|**rowmodctr**|**int**|計算前次更新資料表的統計資料之後，插入、刪除或更新資料列的總數。<br /><br /> 0 = **indid** > 1 時，索引會進行分割。<br /><br /> 0 = 當 **indid** 為0或1時，資料表會進行分割。<br /><br /> 在 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 和更新版本中， **rowmodctr** 與舊版不完全相容。 如需詳細資訊，請參閱＜備註＞。|  
|**reserved3**|**int**|傳回 0。<br /><br /> [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**reserved4**|**int**|傳回 0。<br /><br /> [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**xmaxlen**|**smallint**|資料列的大小上限|  
|**maxirow**|**smallint**|非分葉索引資料列的大小上限。<br /><br /> 在 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 和更新版本中， **maxirow** 與舊版不完全相容。|  
|**OrigFillFactor**|**tinyint**|當建立索引時，所用的原始填滿因數值。 這個值並沒有維護；不過，如果您必須重建索引，且忘了所用的填滿因數值，它可能很有用。|  
|**StatVersion**|**tinyint**|傳回 0。<br /><br /> [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**reserved2**|**int**|傳回 0。<br /><br /> [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**FirstIAM**|**binary(6)**|NULL = 索引進行分割。<br /><br /> [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**impid**|**smallint**|索引實作旗標。<br /><br /> 傳回 0。<br /><br /> [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**lockflags**|**smallint**|用來約束所考量的索引鎖定資料粒度。 例如，若要將鎖定成本降到最低，您可以將基本上是唯讀的參考表設為只執行資料表層級的鎖定。|  
|**pgmodctr**|**int**|傳回 0。<br /><br /> [!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**鑰匙**|**varbinary(816)**|組成索引鍵的各個資料行之資料行識別碼清單。<br /><br /> 傳回 NULL。<br /><br /> 若要顯示索引鍵資料行，請使用 [sys.sysindexkeys](../../relational-databases/system-compatibility-views/sys-sysindexkeys-transact-sql.md)。|  
|**name**|**sysname**|索引或統計資料的名稱。 當 **indid** = 0 時，會傳回 Null。 請修改您的應用程式來查閱 NULL 堆積名稱。|  
|**statblob**|**image**|統計資料二進位大型物件 (BLOB)。<br /><br /> 傳回 NULL。|  
|**maxlen**|**int**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]|  
|**rows**|**int**|以 **indid** = 0 和 **indid** = 1 為基礎的資料層級資料列計數，並針對 **indid** >1 重複此值。|  
  
## <a name="remarks"></a>備註  
 不應使用定義為已保留的資料行。  
  
 如果資料表或索引包含 ROW_OVERFLOW 配置單位中的資料， **dpages**、 **reserved** 和 **used** 資料行就不會傳回精確的結果。 另外，每個索引的頁面計數也都會個別追蹤，不會彙總基底資料表這些計數。 若要查看頁面計數，請使用 [sys.allocation_units](../../relational-databases/system-catalog-views/sys-allocation-units-transact-sql.md) 或 [sys. 磁碟分割](../../relational-databases/system-catalog-views/sys-partitions-transact-sql.md) 目錄檢視，或 [sys.dm_db_partition_stats](../../relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql.md) 動態管理檢視。  
  
 在 SQL Server 2000 和舊版中，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會維護資料列層級的修改計數器。 現在，系統則會在資料行層級維護這些計數器。 因此，會計算 **rowmodctr** 資料行，並產生類似于舊版中結果的結果，但不是精確的結果。  
  
 如果您使用 **rowmodctr** 中的值來判斷更新統計資料的時機，請考慮下列解決方案：  
  
-   不執行任何動作。 新的 **rowmodctr** 值會經常協助您決定何時更新統計資料，因為行為相當接近較早版本的結果。  
  
-   不使用 AUTO_UPDATE_STATISTICS。 如需詳細資訊，請參閱 [統計資料](../../relational-databases/statistics/statistics.md)。  
  
-   利用時間限制來判斷更新統計資料的時間。 例如，每小時、每日或每週。  
  
-   利用應用程式層級的資訊來判斷更新統計資料的時間。 例如，每次 **標識** 列的最大值變更為10000以上，或每次執行大量插入作業時。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [&#40;Transact-sql&#41;將系統資料表對應至系統檢視 ](../../relational-databases/system-tables/mapping-system-tables-to-system-views-transact-sql.md)   
 [sys.indexes &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-indexes-transact-sql.md)  
  
  
