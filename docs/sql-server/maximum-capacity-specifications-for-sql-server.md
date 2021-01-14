---
title: SQL Server 的最大容量規格
description: 本文說明 SQL Server 元件中已定義的各種物件大小和數量上限及其他資訊。
ms.date: 03/05/2020
ms.prod: sql
ms.reviewer: ''
ms.custom: ''
ms.technology: release-landing
ms.topic: conceptual
helpviewer_keywords:
- objects [SQL Server]
- number capacity specifications [SQL Server]
- size [SQL Server], capacity specifications
- number of objects
- capacity specifications [SQL Server]
- maximum capacity specifications [SQL Server]
- size [SQL Server]
- replication capacity specifications [SQL Server]
- objects [SQL Server], capacity specifications
- Database Engine [SQL Server], capacity specifications
ms.assetid: 13e95046-0e76-4604-b561-d1a74dd824d7
ms.author: mikeray
author: MikeRayMSFT
ms.openlocfilehash: 126ac3c36d7e72205c4e0268fcd4b1cedf993169
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172380"
---
# <a name="maximum-capacity-specifications-for-sql-server"></a>SQL Server 的最大容量規格

[!INCLUDE[sqlserver](../includes/applies-to-version/sqlserver.md)]

本文會顯示 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 元件中已定義各種物件的大小和數量上限。

>[!NOTE]
>除了本文中的資訊外，您可能也發現下列連結有所幫助：
>
>* [下載 SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads)
>* [安裝 SQL Server 的硬體與軟體需求](../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)
>* [檢查 System Configuration Checker 的參數](../database-engine/install-windows/check-parameters-for-the-system-configuration-checker.md)
>

## <a name="ssde-objects"></a>[!INCLUDE[ssDE](../includes/ssde-md.md)] 物件

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 資料庫中已定義或 [!INCLUDE[tsql](../includes/tsql-md.md)] 陳述式中所參考之各種物件的大小和數目上限。

|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] [!INCLUDE[ssDE](../includes/ssde-md.md)] 物件|大小/數目上限 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] (64 位元)|其他資訊|
|---------------------------------------------------------|------------------------------------------------------------------|----------------------------|
|批次大小|65,536 <sup>*</sup> (網路封包大小)|網路封包大小是表格式資料流 (TDS) 封包的大小，這些封包會用於應用程式與關聯式 [!INCLUDE[ssDE](../includes/ssde-md.md)] 之間的通訊。 預設封包大小是 4 KB，由 network packet size 組態選項所控制。|
|每個短字串資料行的位元組數|8,000||
|每個 `GROUP BY`、`ORDER BY` 的位元組數|8,060||
|每個索引鍵的位元組數|叢集索引為 900 個位元組數。 非叢集索引為 1,700。|在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]中，叢集索引鍵的最大位元組數不得超過 900。 非叢集索引鍵的最大位元組數為 1700。<br /><br /> 您可以使用大小上限增加超過限制的可變長度資料行，來定義索引鍵。 不過，這些資料行中的資料大小總和不得超過限制。<br /><br /> 在非叢集索引中，您可以包含額外的非索引鍵資料行，這些資料行不會計入索引鍵的大小限制。 非索引鍵資料行可能有助於提升某些查詢的執行效能。|
|記憶體最佳化資料表之每個索引鍵的位元組數|非叢集索引為 2500 個位元組。 只要所有索引鍵都能納入資料列，雜湊索引便沒有限制。|在記憶體最佳化資料表中，非叢集索引不能有宣告大小上限超過 2500 個位元組的索引鍵資料行。 索引鍵資料行中的實際資料是否小於宣告大小上限則與此無關。<br /><br /> 雜湊索引鍵的大小沒有固定限制。<br /><br /> 記憶體最佳化資料表上的索引沒有內含資料行的概念，因為所有索引本來就涵蓋所有資料行。<br /><br /> 至於記憶體最佳化資料表，即使資料列大小為 8060 個位元組，某些可變長度資料行實際可儲存的大小超過 8060 個位元組。 不過，資料表上所有索引之所有索引鍵資料行的宣告大小上限，加上資料表中任何其他的固定長度資料行，必須符合 8060 個位元組。|
|每個外部索引鍵的位元組數|900||
|每個主索引鍵的位元組|900||
|每個資料列的位元組數|8,060|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 支援資料列溢位儲存，讓可變長度資料行可以非資料列形式推送。 只有 24 位元組的根會儲存在以非資料列形式所推送可變長度資料行其主要記錄中。 這項功能可讓限制比舊版 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 更有效率。 如需詳細資訊，請參閱[大型資料列支援](../relational-databases/pages-and-extents-architecture-guide.md#large-row-support)。|
|記憶體最佳化資料表中每個資料列的位元組數|8,060|從 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)] 開始，記憶體最佳化資料表支援非資料列儲存。 如果資料表中所有資料行的大小上限超過 8060 個位元組，則會以非資料列形式推送可變長度資料行；這是編譯時間的決策。 針對以非資料列形式儲存的資料行，只會以非資料列形式儲存 8 位元組參考。 如需詳細資訊，請參閱 [記憶體最佳化資料表中的資料表和資料列大小](../relational-databases/in-memory-oltp/table-and-row-size-in-memory-optimized-tables.md)。|
|預存程序之來源文字的位元組數|批次大小或 250 MB 當中較小者||
|每個 `varchar(max) `、`varbinary(max)`、`xml`、`text` 或 `image` 資料行的位元組數|2^31-1||
|每個 `ntext` 或 `nvarchar(max)` 資料行的字元數|2^30-1||
|每份資料表的叢集索引數|1||
|`GROUP BY`、`ORDER BY` 中的資料行|僅受限於位元組數||
|`GROUP BY WITH CUBE` 或 `WITH ROLLUP` 陳述式中的資料行或運算式|10||
|每個索引鍵的資料行數|32|如果資料表包含一或多個 XML 索引，則使用者資料表的叢集索引鍵限制為 31 個資料行，因為 XML 資料行會加入主要 XML 索引的叢集索引鍵中。 在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 中，您可在非叢集索引中包含非索引鍵資料行，以避免達到最多 32 個索引鍵資料行的限制。 如需詳細資訊，請參閱 [建立內含資料行的索引](../relational-databases/indexes/create-indexes-with-included-columns.md)。|
|每個外部索引鍵或主索引鍵的資料行數|32||
|每個 `INSERT` 陳述式的資料行數|4,096||
|每個 `SELECT` 陳述式的資料行數|4,096||
|資料表的資料行數|1,024|包含疏鬆資料行集的資料表最多含有 30,000 個資料行。 請參閱[疏鬆資料行集合](../relational-databases/tables/use-column-sets.md)。|
|每個 `UPDATE` 陳述式的資料行數|4,096|適用於[疏鬆資料行集合](../relational-databases/tables/use-column-sets.md)的不同限制。|
|每個檢視的資料行數|1,024||
|每個用戶端的連接數目|已設定之連接的最大值||
|資料庫大小|524,272 TB||
|每個 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]|32,767||
|每個資料庫的檔案群組數|32,767||
|記憶體最佳化資料的每個資料庫檔案群組|1||
|每個資料庫的檔案數|32,767||
|檔案大小 (資料)|16 TB||
|檔案大小 (記錄檔)|2 TB||
|每個資料庫之記憶體最佳化資料的資料檔案|在 [!INCLUDE[ssSQL14](../includes/ssSQL14-md.md)] 中為 4,096。 更新版的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 不會有這類嚴格的限制。||
|記憶體最佳化資料之每個資料檔案的差異檔案|1||
|每個資料表的外部索引鍵資料表參考數|外寄 = 253。 內送 = 10,000。|相關限制，請參閱 [Create Foreign Key Relationships](../relational-databases/tables/create-foreign-key-relationships.md)。|
|識別碼長度 (字元數)|128||
|每部電腦的執行個體數|單機伺服器為 50 個執行個體。<br /><br />使用共用叢集磁碟作為儲存體時為 25 個容錯移轉叢集執行個體。<br/><br/>使用 SMB 檔案共用作為儲存選項時為 50 個容錯移轉叢集執行個體。||
|每個記憶體最佳化資料表的索引|從 [!INCLUDE[ssSQL17](../includes/ssSQL17-md.md)] 開始及在 [!INCLUDE[ssSDSFull](../includes/ssSDSFull-md.md)]<br/>8 ([!INCLUDE[ssSQL14](../includes/ssSQL14-md.md)] 和 [!INCLUDE[ssSQL15](../includes/sssql16-md.md)]) 中為 999||
|包含 SQL 陳述式的字串長度 (批次大小)|65,536 (網路封包大小)|網路封包大小是表格式資料流 (TDS) 封包的大小，這些封包會用於應用程式與關聯式 [!INCLUDE[ssDE](../includes/ssde-md.md)] 之間的通訊。 預設封包大小是 4 KB，由 network packet size 組態選項所控制。|
|每個連接的鎖定數|每部伺服器的最大鎖定數||
|每個 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]|僅受限於記憶體|這個值是針對靜態鎖定配置。 動態鎖定僅受限於記憶體。|
|巢狀預存程序層級|32|如果預存程序存取超過 64 個資料庫或以交錯方式超過兩個資料庫，則會收到錯誤。|
|巢狀子查詢|32||
|巢狀交易|4,294,967,296|| 
|巢狀觸發程序層級|32||
|每份資料表的非叢集索引數|999||
|當下列任何一個項目存在時，`GROUP BY` 子句中相異運算式的數目：`CUBE`、`ROLLUP`、`GROUPING SETS`、`WITH CUBE`、`WITH ROLLUP`|32||
|`GROUP BY` 子句中由運算子產生的群組集合數目|4,096||
|每個預存程序的參數數目|2,100||
|每個使用者定義函數的參數數目|2,100||
|每份資料表的 REFERENCES|253||
|每份資料表的資料列數|受限於可用的儲存體||
|每個資料庫的資料表數|受限於資料庫中的總物件數|物件包含資料表、檢視、預存程序、使用者定義函式、觸發程序、規則、預設和條件約束等物件。 資料庫中所有物件數的總和不得超過 2,147,483,647。|
|每份分割區資料表或索引的分割區數|15,000||
|非索引資料行的統計資料|30,000|| 
|每個 `SELECT` 陳述式的資料表數|僅受限於可用的資源||
|每份資料表的觸發程序數|受限於資料庫的物件數|物件包含資料表、檢視、預存程序、使用者定義函式、觸發程序、規則、預設和條件約束等物件。 資料庫中所有物件數的總和不得超過 2,147,483,647。|
|使用者連線|32,767||
|XML 索引|249||

## <a name="ssnoversion-utility-objects"></a>[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 公用程式物件

在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 公用程式中測試之各種物件的大小和數目上限。

|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 公用程式物件|大小/數目上限 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] (64 位元)|
|----------------------------------------------|------------------------------------------------------------------|
|每個 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 公用程式的電腦數 (實體電腦或虛擬機器)|100|
|每部電腦的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體數|5|
|每個 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 公用程式的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體總數|200<sup>*</sup>|
|每個 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]執行個體的使用者資料庫數，包括資料層應用程式|50|
|每個 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 公用程式的使用者資料庫總數|1,000|
|每個資料庫的檔案群組數|1|
|每個檔案群組的資料檔案數|1|
|每個資料庫的記錄檔案數|1|
|每部電腦的磁碟區數|3|

<sup>*</sup> [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]公用程式所支援 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 受控執行個體數量上限可能會根據伺服器的硬體組態而有所不同。 如需入門資訊，請參閱 [SQL Server 公用程式的功能與工作](../relational-databases/manage/sql-server-utility-features-and-tasks.md)。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 版本都提供 [!INCLUDE[ssCurrent](../includes/sscurrent-md.md)]。 如需 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]版本支援的功能清單，請參閱 [SQL Server 2016 版本支援的功能](./editions-and-components-of-sql-server-2016.md)。

## <a name="ssnoversion-data-tier-application-objects"></a>[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 資料層應用程式物件

在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 資料層應用程式 (DAC) 中測試之各種物件的大小和數目上限。

|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] DAC 物件|大小/數目上限 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] (64 位元)|
|------------------------------------------|------------------------------------------------------------------|
|每個 DAC 的資料庫數|1|
|每個 DAC 的物件數 <sup>*</sup>|受限於資料庫的物件數或可用的記憶體。|

<sup>*</sup> 此限制所包括的物件類型為使用者、資料表、檢視、預存程序、使用者定義函式、使用者定義資料類型、資料庫角色、結構描述和使用者定義資料表類型。

## <a name="replication-objects"></a>複寫物件

[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 複寫中已定義之各種物件的大小和數目上限。

|[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 複寫物件|SQL Server 大小/數目上限 (64 位元)|
|--------------------------------------------------|---------------------------------------------------|
|發行項 (合併式發行集)|2048|
|發行項 (快照式或交易式發行集)|32,767|
|資料表中的資料行<sup>*</sup> (合併式發行集)|246|
|資料表中的資料行<sup>**</sup> ([!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 快照式或交易式發行集)|1,000|
|資料表中的資料行<sup>**</sup> (Oracle 快照式或交易式發行集)|995|
|用於資料列篩選之資料行的位元組數 (合併式發行集)|1,024|
|用於資料列篩選之資料行的位元組數 (快照式或交易式發行集)|8,000|

<sup>*</sup> 如果使用資料列追蹤執行衝突偵測 (預設)，基底資料表最多可以包含 1,024 個資料行，但必須從發行項篩選資料行，以便發佈最多 246 個資料行。 如果使用資料行追蹤，則基底資料表可包括的資料行數上限為 246。

<sup>**</sup> 基底資料表可包含發行集資料庫中允許的最大資料行數 (如果是 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]，則為 1,024 個)，但如果資料行超出對發行集類型指定的最大值，則必須篩選發行項的資料行。

## <a name="see-also"></a>另請參閱

[SQL Server 公用程式功能與工作](../relational-databases/manage/sql-server-utility-features-and-tasks.md)