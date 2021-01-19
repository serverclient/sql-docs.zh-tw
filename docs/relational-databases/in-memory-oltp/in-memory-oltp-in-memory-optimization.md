---
title: 記憶體內部 OLTP (記憶體內部最佳化) | Microsoft Docs
description: 使用這些適用於記憶體內部 OLTP 的範例及資源，此功能可大幅提升 SQL Server 中的效能。
ms.custom: ''
ms.date: 11/21/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: in-memory-oltp
ms.topic: conceptual
helpviewer_keywords:
- In-Memory OLTP
- memory-optimized tables
ms.assetid: e1d03d74-2572-4a55-afd6-7edf0bc28bdb
author: MightyPen
ms.author: genemi
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 7af10ecc4753034948cc616cc85301afd7a7046d
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172070"
---
# <a name="in-memory-oltp-and-memory-optimization"></a>記憶體內部 OLTP 與記憶體最佳化

[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

 [!INCLUDE[hek_2](../../includes/hek-2-md.md)] 可大幅提升交易處理、資料擷取和資料載入，以及暫時性資料案例的效能。  若要跳至快速測試您自己的記憶體最佳化資料表和原生編譯預存程序所需的基本程式碼和知識，請參閱
 -  [快速入門 1：可讓 Transact-SQL 擁有更快效能的記憶體內部 OLTP 技術](../../relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp.md)。  
 
我們已將一段 [**17 分鐘的影片**](#anchorname-17minute-video)上傳到 YouTube，說明 SQL Server 上的記憶體內部 OLTP，並示範其效能優勢。

如需記憶體內部 OLTP 的更詳細概觀，以及檢閱案例以查看來自此技術的效能效益：

- [概觀和使用案例](../../relational-databases/in-memory-oltp/overview-and-usage-scenarios.md)
 
 請注意， [!INCLUDE[hek_2](../../includes/hek-2-md.md)] 是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 技術，可改善交易處理的效能。 如需改善報告和分析查詢效能的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 技術，請參閱 [資料行存放區索引指南](../../relational-databases/indexes/columnstore-indexes-overview.md)。
  
 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]、[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 及 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中的記憶體內部 OLTP 進行了幾項改進。 已增加 Transact-SQL 介面區，讓移轉資料庫應用程式更容易。 已新增對記憶體最佳化資料表和原生編譯預存程序的執行 ALTER 作業支援，讓維護應用程式更容易。
  
> [!NOTE]  
>  **現在就試試看**  
>   
>  記憶體內部 OLTP 在「進階」和「商務關鍵」層 Azure SQL 資料庫和彈性集區中提供。 若要開始使用記憶體內部 OLTP 以及 Azure SQL Database 中的資料行存放區，請參閱 [在 SQL Database 中使用記憶體內部技術最佳化效能](/azure/azure-sql/in-memory-oltp-overview)。  
  

## <a name="in-this-section"></a>本節內容  
 本節包含下列主題：  
  
|主題|描述|  
|-----------|-----------------|  
|[快速入門 1：可讓 Transact-SQL 擁有更快效能的記憶體內部 OLTP 技術](../../relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp.md)|適當探討記憶體內部 OLTP|
|[概觀和使用案例](../../relational-databases/in-memory-oltp/overview-and-usage-scenarios.md)|記憶體內部 OLTP 是什麼，以及有哪些效能案例可看到效能效益的概觀。|
|[使用記憶體最佳化資料表的需求](../../relational-databases/in-memory-oltp/requirements-for-using-memory-optimized-tables.md)|討論有關使用記憶體最佳化資料表的硬體和軟體需求以及方針。|  
|[記憶體內部 OLTP 程式碼範例](./sample-database-for-in-memory-oltp.md)|包含程式碼範例，示範如何建立及使用記憶體最佳化資料表。|  
|[記憶體最佳化資料表](./sample-database-for-in-memory-oltp.md)|介紹記憶體最佳化的資料表。|  
|[記憶體最佳化資料表變數](./faster-temp-table-and-table-variable-by-using-memory-optimization.md)|程式碼範例，示範如何使用記憶體最佳化的資料表變數取代傳統資料表變數，以減少 tempdb 的使用量。|  
|[記憶體最佳化資料表上的索引](./indexes-for-memory-optimized-tables.md)|介紹記憶體最佳化索引。|  
|[原生編譯的預存程序](./a-guide-to-query-processing-for-memory-optimized-tables.md)|介紹原生編譯的預存程序。|  
|[為記憶體內部 OLTP 管理記憶體](/previous-versions/sql/sql-server-2016/dn465872(v=sql.130))|了解及管理系統上的記憶體使用量。|  
|[建立及管理記憶體最佳化物件的儲存體](../../relational-databases/in-memory-oltp/creating-and-managing-storage-for-memory-optimized-objects.md)|討論資料與差異檔案 (儲存記憶體最佳化之資料表中的交易資訊)。|  
|[備份、還原及復原記憶體最佳化資料表](/previous-versions/sql/sql-server-2016/dn624160(v=sql.130))|討論記憶體最佳化資料表的備份、還原及復原。|  
|[記憶體內部 OLTP 的 Transact-SQL 支援](../../relational-databases/in-memory-oltp/transact-sql-support-for-in-memory-oltp.md)|討論適用於 [!INCLUDE[tsql](../../includes/tsql-md.md)] 的 [!INCLUDE[hek_2](../../includes/hek-2-md.md)]支援。|  
|[記憶體內部 OLTP 資料庫的高可用性支援](../../relational-databases/in-memory-oltp/high-availability-support-for-in-memory-oltp-databases.md)|討論可用性群組與 [!INCLUDE[hek_2](../../includes/hek-2-md.md)]中的容錯移轉叢集。|  
|[記憶體中 OLTP 的 SQL Server 支援](./transact-sql-support-for-in-memory-oltp.md)|列出為支援記憶體最佳化資料表新增和更新的語法和功能。|  
|[移轉至 In-Memory OLTP](./plan-your-adoption-of-in-memory-oltp-features-in-sql-server.md)|討論如何將以磁碟為基礎的資料表移轉到記憶體最佳化的資料表。|  
| &nbsp; | &nbsp; |

## <a name="links-to-other-websites"></a>其他網站的連結

本節提供其他包含 SQL Server 上記憶體內部 OLTP 相關資訊的網站連結。

- [說明記憶體內部 OLTP 並示範其效能優勢的 **影片**](#anchorname-17minute-video)

- [記憶體內部 OLTP 效能示範 v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)

-   [SQL Server 記憶體內部 OLTP 內部技術白皮書](./sql-server-in-memory-oltp-internals-for-sql-server-2016.md)  

-   [SQL Server 記憶體內部 OLTP 和資料行存放區功能的比較](https://download.microsoft.com/download/D/0/0/D0075580-6D72-403D-8B4D-C3BD88D58CE4/SQL_Server_2016_In_Memory_OLTP_and_Columnstore_Comparison_White_Paper.pdf)

-   SQL Server 2016 中記憶體內部 OLTP 的新功能，[第 1 部分](/archive/blogs/sqlserverstorageengine/in-memory-oltp-whats-new-in-sql2016-ctp3) \(英文\) 和[第 2 部分](/archive/blogs/sqlserverstorageengine/whats-new-for-in-memory-oltp-in-sql-server-2016-since-ctp3) \(英文\)
  
-   [記憶體內部 OLTP - 一般工作負載模式和移轉考量](/previous-versions/dn673538(v=msdn.10))  
  
-   [記憶體內部 OLTP 部落格](https://cloudblogs.microsoft.com/sqlserver/2013/06/26/sql-server-2014-in-memory-technologies-blog-series-introduction/)  

## <a name="17-minute-video-indexed"></a><a name="anchorname-17minute-video"></a>17 分鐘的影片，已建立索引

- _影片標題：_ &nbsp; **SQL Server 2016 中的記憶體內部 OLTP**
- _發佈日期：_ &nbsp; 2019-03-10，`YouTube.com` 上。
- _影片長度：_ &nbsp; 17:32 &nbsp; &nbsp; (請參閱下列 [**索引**](#anchorname-index-17minute-video)以取得影片的連結)。
- _主持人：_ &nbsp; SQL Server 資深方案經理 Jos de Bruijn

### <a name="demo-can-be-downloaded"></a>您可以下載示範內容

在時間標記 08:09 處，影片會執行示範兩次。 您可以在下列連結下載影片中所使用的可執行效能示範的原始程式碼：

- [記憶體內部 OLTP 效能示範 v1.0，原始程式碼](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)

影片中所見到的一般步驟如下：

1. 首先會使用一般資料表來執行示範。
2. 接下來我們會看到正在建立資料表記憶體最佳化版本，並在 SQL Server Management Studio (SSMS.exe) 中按幾下以進行填入的過程。
3. 接著，會使用經記憶體最佳化的資料表再次執行示範。 我們可以測量到速度上的大幅改善。

### <a name="index-to-each-section-in-the-video"></a><a name="anchorname-index-17minute-video"></a>影片中各區段的索引

| 時間標記連結 | 區段標題 |
| :------------- | :------------ |
| A.&nbsp;[00:00](https://www.youtube.com/watch?v=l5l5eophmK4&t=0) | 開頭。 |
| <br/>B.&nbsp;[00:56](https://www.youtube.com/watch?v=l5l5eophmK4&t=56) | <br/>為什麼客戶應在意記憶體內部 OLTP。 |
| &nbsp; &nbsp; [01:03](https://www.youtube.com/watch?v=l5l5eophmK4&t=63) | 現代硬體需要現代的資料庫系統架構。 |
| &nbsp; &nbsp; [02:10](https://www.youtube.com/watch?v=l5l5eophmK4&t=130) | 資料正在產生爆炸，作業必須即時 (低延遲)。 |
| &nbsp; &nbsp; [03:19](https://www.youtube.com/watch?v=l5l5eophmK4&t=199) | 減少 TCO - 以您擁有的資源進行更多作業。 |
| <br/>C.&nbsp;[03:33](https://www.youtube.com/watch?v=l5l5eophmK4&t=213) | <br/>什麼是記憶體內部 OLTP。<br/>使用記憶體最佳化技術最佳化後的效能。 |
| &nbsp; &nbsp; [05:03](https://www.youtube.com/watch?v=l5l5eophmK4&t=303) | 交易處理最高可以快上 30 倍。 |
| &nbsp; &nbsp; [05:22](https://www.youtube.com/watch?v=l5l5eophmK4&t=322) | 完全耐久 - 資料不會因伺服器失敗而遺失。 |
| &nbsp; &nbsp; [06:15](https://www.youtube.com/watch?v=l5l5eophmK4&t=375) | 完全整合在 SQL Server 中。 因此您不需要學習新的語言或工具。 |
| &nbsp; &nbsp; [07:22](https://www.youtube.com/watch?v=l5l5eophmK4&t=442) | 在 SQL Server 2014 中首次發行，但在 2016 中有了大幅度的改善。 |
| &nbsp; &nbsp; [07:58](https://www.youtube.com/watch?v=l5l5eophmK4&t=558) | 也在 Azure SQL Database 中提供使用 (雲端)。 |
| <br/>D.&nbsp;[08:09](https://www.youtube.com/watch?v=l5l5eophmK4&t=489) | <br/>效能示範。<br/> 使用一般資料表執行示範。 |
| &nbsp; &nbsp; [09:11](https://www.youtube.com/watch?v=l5l5eophmK4&t=551) | SSMS 操作功能表：**報表** &gt; **交易效能分析** |
| &nbsp; &nbsp; [10:38](https://www.youtube.com/watch?v=l5l5eophmK4&t=638) | SSMS 操作功能表：**記憶體最佳化 Advisor**<br/> &nbsp; &nbsp; 從一般資料表實際建立經記憶體最佳化的資料表並移轉資料。 |
| &nbsp; &nbsp; [11:28](https://www.youtube.com/watch?v=l5l5eophmK4&t=688) | 重新執行示範，看到 45 倍的速度改善。 |
| <br/>E.&nbsp;[12:17](https://www.youtube.com/watch?v=l5l5eophmK4&t=737) | <br/>在 SQL Server 2016 中更易於使用記憶體內部 OLTP (相較於 2014)。 |
| &nbsp; &nbsp; [12:43](https://www.youtube.com/watch?v=l5l5eophmK4&t=763) | 簡化分析以協助應用程式移轉。 |
| &nbsp; &nbsp; [13:03](https://www.youtube.com/watch?v=l5l5eophmK4&t=783) | 透過增加 Transact-SQL 語言支援來減少應用程式移轉的複雜度 (例如包含外部索引鍵及觸發程序)。 |
| &nbsp; &nbsp; [13:56](https://www.youtube.com/watch?v=l5l5eophmK4&t=836) | 改善管理性。<br/> &nbsp; &nbsp; 例如變更結構描述及索引、自動更新統計資料。 |
| <br/>F.&nbsp;[14:46](https://www.youtube.com/watch?v=l5l5eophmK4&t=886) | <br/>改善延展性。 |
| &nbsp; &nbsp; [15:12](https://www.youtube.com/watch?v=l5l5eophmK4&t=912) | 大型記憶體最佳化資料表 (每個資料庫最高可達 2 TB)。 |
| &nbsp; &nbsp; [15:34](https://www.youtube.com/watch?v=l5l5eophmK4&t=934) | 更佳的縮放。 |
| &nbsp; &nbsp; [16:41](https://www.youtube.com/watch?v=l5l5eophmK4&t=1001) | 利用您已經擁有的資源進行更多作業！ |
| <br/>G.&nbsp;[16:53](https://www.youtube.com/watch?v=l5l5eophmK4&t=1013) | <br/>最後評論。 (結束於 17:32。) |
| &nbsp; | &nbsp; |

## <a name="see-also"></a>另請參閱  
 [資料庫功能](../databases/databases.md)  
