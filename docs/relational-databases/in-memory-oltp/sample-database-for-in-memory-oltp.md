---
title: 記憶體內部 OLTP 的範例資料庫 | Microsoft 文件
description: 了解記憶體內部 OLTP 功能及其效能優勢。 此範例會示範經記憶體最佳化的資料表和原生編譯預存程序。
ms.custom: ''
ms.date: 12/14/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: in-memory-oltp
ms.topic: conceptual
ms.assetid: df347f9b-b950-4e3a-85f4-b9f21735eae3
author: MightyPen
ms.author: genemi
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: b4cf02b18cdaef035cd27b568d5f8d84f0a25619
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094595"
---
# <a name="sample-database-for-in-memory-oltp"></a>記憶體內部 OLTP 的範例資料庫
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
    
## <a name="overview"></a>概觀  
 此範例示範了記憶體內部 OLTP 功能。 其顯示經記憶體最佳化的資料表和原生編譯的預存程序，並可用來示範記憶體內部 OLTP 的效能優勢。  
  
> [!NOTE]  
>  若要檢視 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]的這項主題，請參閱 [Extensions to AdventureWorks to Demonstrate In-Memory OLTP](./overview-and-usage-scenarios.md#in-memory-oltp-overview)(示範記憶體內部 OLTP 的 AdventureWorks 延伸模組)。  
  
 此範例會將 AdventureWorks 資料庫中的五個資料表移轉至經記憶體最佳化的資料表，並包含銷售訂單處理的工作負載示範。 您可以利用此工作負載示範，查看在伺服器上使用記憶體內部 OLTP 的效能優勢。  
  
 在此範例的描述中，我們會討論將資料表移轉至記憶體內部 OLTP 的利弊，以說明經記憶體最佳化的資料表尚未支援的功能。  
  
 此範例的文件集結構如下：  
  
-   安裝範例及執行工作負載示範的[必要條件](#Prerequisites)  
  
-   [安裝以 AdventureWorks 為基礎的 In-Memory OLTP 範例](#InstallingtheIn-MemoryOLTPsamplebasedonAdventureWorks)的指示  
  
-   [範例資料表和程序描述](#Descriptionofthesampletablesandprocedures) - 包含 In-Memory OLTP 範例加入 AdventureWorks 中的資料表和程序的描述，以及將部分原始 AdventureWorks 資料表移轉至經記憶體最佳化的資料表的考量  
  
-   執行[使用工作負載示範的效能度量](#PerformanceMeasurementsusingtheDemoWorkload)之指示 - 包括安裝及執行 ostress (用於驅動工作負載的工具) 的指示，以及執行工作負載示範本身的指示  
  
-   [範例中的記憶體和磁碟空間使用量](#MemoryandDiskSpaceUtilizationintheSample)  
  
##  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
  
-   [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]  
  
-   基於效能測試考量，伺服器的規格必須與您的實際執行環境類似。 針對此特定範例，您應該準備至少 16 GB 的記憶體供 SQL Server 使用。 如需有關「記憶體內部 OLTP」硬體的一般指導方針，請參閱下列部落格文章：[SQL Server 2014 中記憶體內部 OLTP 的硬體考量](blog-hardware-in-memory-oltp.md)

##  <a name="installing-the-in-memory-oltp-sample-based-on-adventureworks"></a><a name="InstallingtheIn-MemoryOLTPsamplebasedonAdventureWorks"></a>安裝以 AdventureWorks 為基礎的 In-Memory OLTP 範例  
 請遵循下列步驟來安裝範例：  
  
1.  從 [https://github.com/microsoft/sql-server-samples/releases/tag/adventureworks](https://github.com/microsoft/sql-server-samples/releases/tag/adventureworks) 將 AdventureWorks2016CTP3.bak 和 SQLServer2016CTP3Samples.zip 下載至本機資料夾，例如 'c:\temp'。  
  
2.  使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 或 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]還原資料庫備份：  
  
    1.  識別目標資料夾和資料檔案的檔案名稱，例如  
  
         'h:\DATA\AdventureWorks2016CTP3_Data.mdf'  
  
    2.  識別目標資料夾和記錄檔的檔案名稱，例如  
  
         'i:\DATA\AdventureWorks2016CTP3_log.ldf'  
  
        1.  放置記錄檔的磁碟機應該與放置資料檔案的磁碟機不同，最好使用低度延遲的磁碟機以取得最高效能，例如 SSD 或 PCIe 儲存體。  
  
     範例 T-SQL 指令碼：  
  
    ```sql
    RESTORE DATABASE [AdventureWorks2016CTP3]   
      FROM DISK = N'C:\temp\AdventureWorks2016CTP3.bak'   
        WITH FILE = 1,    
      MOVE N'AdventureWorks2016_Data' TO N'h:\DATA\AdventureWorks2016CTP3_Data.mdf',    
      MOVE N'AdventureWorks2016_Log' TO N'i:\DATA\AdventureWorks2016CTP3_log.ldf',  
      MOVE N'AdventureWorks2016CTP3_mod' TO N'h:\data\AdventureWorks2016CTP3_mod'  
     GO  
    ```  
  
3.  若要檢視範例指令碼和工作負載，請將檔案 SQLServer2016CTP3Samples.zip 解壓縮至本機資料夾。 如需執行工作負載的指示，請參閱記憶體內部 OLTP\readme.txt 中的檔案。  
  
##  <a name="description-of-the-sample-tables-and-procedures"></a><a name="Descriptionofthesampletablesandprocedures"></a> 範例資料表和程序描述  
 此範例以 AdventureWorks 中的現有資料表為基礎，為產品和銷售訂單建立新資料表。 新資料表的結構描述類似現有的資料表，但有一些差異 (如下所述)。  
  
 新的記憶體最佳化資料表具有後置詞 '_inmem'。 此範例也會包含具有後置詞 '_ondisk' 的對應資料表，這些資料表可用來在系統上的記憶體最佳化資料表與磁碟資料表之間，進行一對一的效能比較。  
  
 工作負載中用於比較效能的經記憶體最佳化的資料表是完全持久且完整記錄的。 這些資料表不會為了提升效能而犧牲持久性或可靠性。  
  
 此範例的目標工作負載是銷售訂單處理，同時也會考量產品和折扣的相關資訊。 因此需要 SalesOrderHeader、SalesOrderDetail、Product、SpecialOffer 和 SpecialOfferProduct 資料表。  
  
 兩個新的預存程序 Sales.usp_InsertSalesOrder_inmem 和 Sales.usp_UpdateSalesOrderShipInfo_inmem，可用來插入銷售訂單及更新給定銷售訂單的出貨資訊。  
  
 新的結構描述「示範」包含協助程式資料表和預存程序，可執行工作負載示範。  
  
 具體而言，記憶體內部 OLTP 範例會將下列物件加入 AdventureWorks：  
  
### <a name="tables-added-by-the-sample"></a>此範例加入的資料表  
  
#### <a name="the-new-tables"></a>新資料表  
 Sales.SalesOrderHeader_inmem  
  
-   銷售訂單的相關標頭資訊。 每個銷售訂單在此資料表中有一個資料列。  
  
 Sales.SalesOrderDetail_inmem  
  
-   銷售訂單的詳細資料。 銷售訂單的每個明細項目在此資料表中有一個資料列。  
  
 Sales.SpecialOffer_inmem  
  
-   特殊供應項目的相關資訊，包括與每個特殊供應項目相關聯的折扣百分比。  
  
 Sales.SpecialOfferProduct_inmem  
  
-   特殊供應項目與產品之間的參考資料表。 每個特殊供應項目可能適用於零個或多個產品，而每個產品也可適用於零個或多個特殊供應項目。  
  
 Production.Product_inmem  
  
-   產品的相關資訊，包括產品的標價。  
  
 Demo.DemoSalesOrderDetailSeed  
  
-   在工作負載示範中用於建構範例銷售訂單。  
  
 以磁碟為基礎的資料表變化：  
  
-   Sales.SalesOrderHeader_ondisk  
  
-   Sales.SalesOrderDetail_ondisk  
  
-   Sales.SpecialOffer_ondisk  
  
-   Sales.SpecialOfferProduct_ondisk  
  
-   Production.Product_ondisk  
  
#### <a name="differences-between-original-disk-based-and-the-and-new-memory-optimized-tables"></a>原始磁碟資料表與新的記憶體最佳化資料表之間的差異  
 大體而言，此範例所導入的新資料表使用與原始資料表相同的資料行和資料類型。 但是仍有一些差異。 以下列出這些差異及變更的基本原理。  
  
 Sales.SalesOrderHeader_inmem  
  
-   記憶體最佳化資料表支援「預設條件約束」 ，且大部分的預設條件約束在移轉後會保持原狀。 不過，原始資料表 Sales.SalesOrderHeader 包含兩個預設條件約束，會擷取資料行 OrderDate 和 ModifiedDate 的目前日期。 在具有大量並行的高輸送量訂單處理工作負載中，任何全域資源都可能會成為競爭重點。 系統時間即為這類全域資源。根據觀察，執行插入銷售訂單的記憶體內部 OLTP 工作負載時，系統時間可能會成為瓶頸，特別是在需要針對銷售訂單標頭的多個資料行擷取系統時間，並同時擷取銷售訂單詳細資料時。 這個問題已在此範例中獲得解決，只對每個插入的銷售訂單擷取一次系統時間，然後在預存程序 Sales.usp_InsertSalesOrder_inmem 中，將此值做為 SalesOrderHeader_inmem 和 SalesOrderDetail_inmem 的日期時間資料行使用。  
  
-   別名使用者定義資料類型 (UDT) - 原始資料表針對資料行 PurchaseOrderNumber 和 AccountNumber，分別使用兩個別名使用者定義資料類型 (UDT) dbo.OrderNumber 和 dbo.AccountNumber。 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 不支援在記憶體最佳化資料表中使用別名 UDT，因此新資料表會分別使用系統資料類型 Nvarchar(25) 和 Nvarchar(15)。  
  
-   「索引鍵中的資料行可為 Null」 - 在原始資料表中，資料行 SalesPersonID 可為 Null，但是在新資料表中，資料行不可為 Null，且預設條件約束必須帶有值 (-1)。 這種情況是因為經記憶體最佳化的資料表上的索引不可以在索引鍵中有可為 Null 的資料行，在此情況下，-1 會代替 NULL 值。  
  
-   「計算資料行」 - 由於 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 不支援在記憶體最佳化資料表中使用計算資料行，因此會省略計算資料行 SalesOrderNumber 和 TotalDue。 新檢視 Sales.vSalesOrderHeader_extended_inmem 會反映資料行 SalesOrderNumber 和 TotalDue。 因此，如果需要這些資料行，您可以使用此檢視。  

    - **適用範圍：** [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1。  
從 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] CTP 1.1 開始，記憶體最佳化的資料表和索引支援計算資料行。

  
-   *中的記憶體最佳化資料表支援* 外部索引鍵條件約束 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]，但僅限於參考的資料表也處於記憶體最佳化的情況。 參考移轉至記憶體最佳化之資料表的外部索引鍵會保留在移轉的資料表中，而其他外部索引鍵會被省略。  此外，SalesOrderHeader_inmem 是範例工作負載中的作用資料表，而外部索引鍵條件約束需要對所有 DML 作業進行額外的處理，因為它需要查閱這些條件約束中參考的其他所有資料表。 因此，此處的假設是應用程式會確保 Sales.SalesOrderHeader_inmem 資料表的參考完整性，但插入資料列時不會驗證參考完整性。  
  
-   *Rowguid* - Rowguid 資料行會遭到省略。 記憶體最佳化資料表支援 uniqueidentifier，但是 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)]不支援 ROWGUIDCOL 選項。 這類資料行通常會用於合併式複寫或具有 filestream 資料行的資料表。 此範例不包含這兩者。  
  
 Sales.SalesOrderDetail  
  
-   *預設條件約束* - 類似 SalesOrderHeader，預設條件約束要求不得移轉系統日期/時間，而是由插入銷售訂單的預存程序在第一次插入時，負責插入目前的系統日期/時間。  
  
-   *計算資料行* - 由於 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 中的記憶體最佳化資料表不支援計算資料行，因此不會移轉計算資料行 LineTotal。 若要存取此資料行，請使用 Sales.vSalesOrderDetail_extended_inmem 檢視。  
  
-   *Rowguid* - Rowguid 資料行會遭到省略。 如需詳細資訊，請參閱 SalesOrderHeader 資料表的描述。  
  
 Production.Product  
  
-   *別名 UDT* - 原始資料表使用使用者定義資料類型 dbo.Flag，相當於系統資料類型 bit。 移轉的資料表會改用 bit 資料類型。  
  
-   *Rowguid* - Rowguid 資料行會遭到省略。 如需詳細資訊，請參閱 SalesOrderHeader 資料表的描述。  
  
 Sales.SpecialOffer  
  
-   *Rowguid* - Rowguid 資料行會遭到省略。 如需詳細資訊，請參閱 SalesOrderHeader 資料表的描述。  
  
 Sales.SpecialOfferProduct  
  
-   *Rowguid* - Rowguid 資料行會遭到省略。 如需詳細資訊，請參閱 SalesOrderHeader 資料表的描述。  
  
#### <a name="considerations-for-indexes-on-memory-optimized-tables"></a>記憶體最佳化資料表上索引的考量  
 記憶體最佳化資料表的基準索引為 NONCLUSTERED 索引，支援點查閱 (等號比較述詞的索引搜尋)、範圍掃描 (不等號比較述詞的索引搜尋)、完整索引掃描及依序掃描。 此外，NONCLUSTERED 索引支援搜尋索引鍵的前置資料行。 事實上，記憶體最佳化的 NONCLUSTERED 索引支援以磁碟為基礎之 NONCLUSTERED 索引支援的所有作業，唯一的例外狀況是回溯掃描。 因此，使用 NONCLUSTERED 索引對您的索引而言是安全的選擇。  
  
 HASH 索引可用來進一步最佳化工作負載。 特別是最佳化點查閱和資料列插入。 不過請注意，這些索引不支援範圍掃描、依序掃描，或搜尋前置索引鍵資料行。 因此，您需要謹慎地使用這些索引。 此外，您必須在建立時指定 bucket_count。 此計數通常應該設定為索引鍵數值到兩倍索引鍵數值之間的值，不過高估通常不會造成問題。  
  
如需詳細資訊，請參閱《線上叢書》以取得有關[索引指導方針](../indexes/guidelines-for-online-index-operations.md)與[選擇正確的 bucket_count](https://sqlserver-help.com/tag/bucket_count/) 之指導方針的詳細資料。  

《線上叢書》提供有關下列主題的詳細資訊：
- [索引指導方針](./indexes-for-memory-optimized-tables.md) <!-- On MSDN-TechNet was version sql.120 (2014), library/dn133166 -->

 移轉之資料表上的索引已調整為適用於銷售訂單處理工作負載示範。 此工作負載需要在 Sales.SalesOrderHeader_inmem 和 Sales.SalesOrderDetail_inmem 資料表中進行插入和點查閱，也需要對 Production.Product_inmem 和 Sales.SpecialOffer_inmem 資料表中的主索引鍵資料行進行點查閱。  
  
 Sales.SalesOrderHeader_inmem 具有三個索引，基於效能的考量，以及由於此工作負載不需要依序或範圍掃描，這三個索引都是 HASH 索引。  
  
-   (SalesOrderID) 上的 HASH 索引：bucket_count 的大小調整為 1,000 萬 (無條件進位到 1,600 萬)，因為預期的銷售訂單數目為 1,000 萬  
  
-   (SalesPersonID) 上的 HASH 索引：bucket_count 為 100 萬。 提供的資料集沒有很多銷售人員。 但此大型 bucket_count 允許未來的成長。 此外，如果 bucket_count 過大，也不會對點查閱的效能造成負面影響。  
  
-   (CustomerID) 上的 HASH 索引：bucket_count 為 100 萬。 提供的資料集沒有很多客戶，但容許未來成長使用。  
  
 Sales.SalesOrderDetail_inmem 具有三個索引，基於效能的考量，以及由於此工作負載不需要依序或範圍掃描，這三個索引都是 HASH 索引。  
  
-   (SalesOrderID, SalesOrderDetailID) 上的 HASH 索引：這是主索引鍵索引，即使 (SalesOrderID, SalesOrderDetailID) 上的查閱不頻繁，針對索引鍵使用雜湊索引仍可加速資料列插入。 bucket_count 的大小調整為 5,000 萬 (無條件進位到 6,700 萬)：預期的銷售訂單數目為 1,000 萬，放大的用意是為了使每個訂單平均有五個項目  
  
-   (SalesOrderID) 上的 HASH 索引：銷售訂單的查閱很頻繁，您會想要尋找對應至單一訂單的所有明細項目。  bucket_count 的大小調整為 1,000 萬 (無條件進位到 1,600 萬)，因為預期的銷售訂單數目為 1,000 萬  
  
-   (ProductID) 上的 HASH 索引：bucket_count 為 100 萬。 提供的資料集沒有很多產品，但容許未來成長使用。  
  
 Production.Product_inmem 具有三個索引  
  
-   (ProductID) 上的 HASH 索引：ProductID 上的查閱位於工作負載示範的關鍵路徑中，因此這是雜湊索引  
  
-   (Name) 上的 NONCLUSTERED 索引：允許對產品名稱進行依序掃描  
  
-   (ProductNumber) 上的 NONCLUSTERED 索引：允許對產品號碼進行依序掃描  
  
 Sales.SpecialOffer_inmem 在 (SpecialOfferID) 上具有一個 HASH 索引：特殊供應項目的點查閱在工作負載示範的關鍵過程中。 bucket_count 的大小調整為 100 萬，以允許未來成長使用。  
  
 工作負載示範中不會參考 Sales.SpecialOfferProduct_inmem，因此沒有在此資料表上使用雜湊索引來最佳化工作負載的明顯需求 - (SpecialOfferID, ProductID) 和 (ProductID) 上的索引為 NONCLUSTERED。  
  
 請注意，上述的部分 bucket_counts 大小過大，但不包括 SalesOrderHeader_inmem 和 SalesOrderDetail_inmem 上索引的 bucket_counts：其大小剛好是 1,000 萬個銷售訂單。 這樣做是為了在記憶體可用量很低的系統上安裝範例，在此情況下，工作負載示範會因為記憶體不足而失敗。 如果您想要擴充到超過 1,000 萬個銷售訂單，請視需求隨意增加值區計數。  
  
#### <a name="considerations-for-memory-utilization"></a>記憶體使用量的考量  
 範例資料庫中的記憶體使用情形在工作負載示範前後的變化，都會在 [記憶體最佳化資料表的記憶體使用量](#Memoryutilizationforthememory-optimizedtables)一節中說明。  
  
### <a name="stored-procedures-added-by-the-sample"></a>此範例加入的預存程序  
 以下是用於插入銷售訂單及更新出貨詳細資料的兩個主要預存程序：  
  
-   Sales.usp_InsertSalesOrder_inmem  
  
    -   在資料庫中插入新的銷售訂單，並輸出該銷售訂單的 SalesOrderID。 此預存程序會擷取銷售訂單標頭的詳細資料及訂單中的明細項目，以做為輸入參數。  
  
    -   輸出參數：  
  
        -   @SalesOrderID int - 剛插入之銷售訂單的 SalesOrderID  
  
    -   輸入參數 (必要)：  
  
        -   @DueDate datetime2  
  
        -   @CustomerID int  
  
        -   @BillToAddressID [int]  
  
        -   @ShipToAddressID [int]  
  
        -   @ShipMethodID [int]  
  
        -   @SalesOrderDetails Sales.SalesOrderDetailType_inmem - 包含訂單明細項目的資料表值參數 (TVP)  
  
    -   輸入參數 (選擇性)：  
  
        -   @Status [tinyint]  
  
        -   @OnlineOrderFlag [bit]  
  
        -   @PurchaseOrderNumber [nvarchar](25\)  
  
        -   @AccountNumber [nvarchar](15\)  
  
        -   @SalesPersonID [int]  
  
        -   @TerritoryID [int]  
  
        -   @CreditCardID [int]  
  
        -   @CreditCardApprovalCode [varchar](15\)  
  
        -   @CurrencyRateID [int]  
  
        -   @Comment nvarchar(128)  
  
-   Sales.usp_UpdateSalesOrderShipInfo_inmem  
  
    -   更新給定銷售訂單的出貨資訊。 這也會更新銷售訂單之所有明細項目的出貨資訊。  
  
    -   這是原生編譯預存程序 Sales.usp_UpdateSalesOrderShipInfo_native 的包裝函式程序，其重試邏輯可處理更新相同訂單之並行交易所時產生的 (非預期) 潛在衝突。 如需有關重試邏輯的詳細資訊，請參閱 [這裡](./transactions-with-memory-optimized-tables.md)的《線上叢書》主題。  
  
-   Sales.usp_UpdateSalesOrderShipInfo_native  
  
    -   這是實際處理出貨資訊更新的原生編譯預存程序。 此程序是包裝函式預存程序 Sales.usp_UpdateSalesOrderShipInfo_inmem 呼叫的方法。 如果客戶可處理失敗並實作重試邏輯，您便可以直接呼叫此程序，而不需使用包裝函式預存程序。  
  
 下列預存程序可用於工作負載示範。  
  
-   Demo.usp_DemoReset  
  
    -   透過清空及重新植入 SalesOrderHeader 和 SalesOrderDetail 資料表來重設示範。  
  
 下列預存程序可用於插入資料至記憶體最佳化資料表及從中刪除資料，同時確保網域和參考完整性。  
  
-   Production.usp_InsertProduct_inmem  
  
-   Production.usp_DeleteProduct_inmem  
  
-   Sales.usp_InsertSpecialOffer_inmem  
  
-   Sales.usp_DeleteSpecialOffer_inmem  
  
-   Sales.usp_InsertSpecialOfferProduct_inmem  
  
 最後，下列預存程序可用來驗證網域和參考完整性。  
  
1.  dbo.usp_ValidateIntegrity  
  
    -   選擇性參數：@object_id - 要驗證完整性的物件識別碼  
  
    -   此程序依賴資料表 dbo.DomainIntegrity、dbo.ReferentialIntegrity 和 dbo.UniqueIntegrity 取得需要驗證的完整性規則 - 此範例會根據 AdventureWorks 資料庫中原始資料表的檢查、外部索引鍵及唯一條件約束，以填入這些資料表。  
  
    -   此程序依賴協助程式程序 dbo.usp_GenerateCKCheck、dbo.usp_GenerateFKCheck 和 dbo.GenerateUQCheck 來產生執行完整性檢查所需的 T-SQL。  
  
##  <a name="performance-measurements-using-the-demo-workload"></a><a name="PerformanceMeasurementsusingtheDemoWorkload"></a> 使用工作負載示範的效能度量  
 Ostress 是由 Microsoft CSS SQL Server 支援小組所開發的命令列工具。 此工具可用來平行執行查詢或執行預存程序。 您可以設定平行執行給定 T-SQL 陳述式的執行緒數目，以及指定此執行緒上應該執行陳述式的次數，ostress 會加快執行緒的速度，並平行執行所有執行緒上的陳述式。 所有執行緒完成執行之後，ostress 會報告所有執行緒完成執行所花費的時間。  
  
### <a name="installing-ostress"></a>安裝 ostress  
 Ostress 會當做報表標記語言 (RML) 公用程式的一部分來安裝，您無法獨立安裝 Ostress。  
  
 安裝步驟：  
  
1.  從下列頁面下載並執行 RML 公用程式的 x64 安裝套件：[下載適用於 SQL Server 的 RML](https://www.microsoft.com/download/details.aspx?id=4511)

2.  如果出現對話方塊，指出特定檔案正在使用，請按一下 [繼續]  
  
### <a name="running-ostress"></a>執行 ostress  
 Ostress 會從命令提示字元執行。 最便利的方式是從「RML CMD 命令提示字元」執行此工具，該工具會當做 RML 公用程式的一部分進行安裝。  
  
 若要開啟 RML CMD 命令提示字元，請遵循下列指示：  
  
 在 Windows Server 2012 [R2] 以及 Windows 8 和 8.1 中，按一下 Windows 鍵開啟 [開始] 功能表，然後輸入 'rml'。 按一下搜尋結果清單中的 "RML Cmd Prompt"。  
  
 確定命令提示字元位於 RML 公用程式安裝資料夾中。  
  
 只需執行 ostress.exe 即可顯示 ostress 的命令列選項，而不需要使用任何命令列選項。 在此範例中執行 ostress 所要考量的主要選項包括：  
  
-   -S：用來連接到 Microsoft SQL Server 執行個體的名稱  
  
-   -E：使用 Windows 驗證進行連接 (預設值)，若使用 SQL Server 驗證，請分別使用 -U 和 -P 選項來指定使用者名稱和密碼  
  
-   -d：資料庫的名稱，在此範例中為 AdventureWorks2014  
  
-   -Q：要執行的 T-SQL 陳述式  
  
-   -n：處理每個輸入檔案/查詢的連接數目  
  
-   -r：每個連接執行每個輸入檔案/查詢的反覆運算次數  
  
### <a name="demo-workload"></a>工作負載示範  
 工作負載示範中所使用的主要預存程序為 Sales.usp_InsertSalesOrder_inmem/ondisk。 下列指令碼使用範例資料建構資料表值參數 (TVP)，並呼叫程序插入具有五個明細項目的銷售訂單。  
  
 ostress 工具可用來平行執行預存程序呼叫，以模擬同時插入銷售訂單的客戶。  
  
 每次壓力執行 Demo.usp_DemoReset 之後，請重設示範。 此程序會刪除記憶體最佳化資料表中的資料列、截斷磁碟資料表，並執行資料庫檢查點。  
  
 下列指令碼會同時執行，以模擬銷售訂單處理工作負載：  
  
```sql
DECLARE   
      @i int = 0,   
      @od Sales.SalesOrderDetailType_inmem,   
      @SalesOrderID int,   
      @DueDate datetime2 = sysdatetime(),   
      @CustomerID int = rand() * 8000,   
      @BillToAddressID int = rand() * 10000,   
      @ShipToAddressID int = rand() * 10000,   
      @ShipMethodID int = (rand() * 5) + 1;   
  
INSERT INTO @od   
SELECT OrderQty, ProductID, SpecialOfferID   
FROM Demo.DemoSalesOrderDetailSeed   
WHERE OrderID= cast((rand()*106) + 1 as int);   
  
WHILE (@i < 20)   
BEGIN;   
      EXEC Sales.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @ShipMethodID, @od;   
      SET @i += 1   
END
```  
  
 利用此指令碼，每個建構的範例訂單會透過以 WHILE 迴圈執行的 20 個預存程序被插入 20 次。 此迴圈可用來說明使用資料庫建構範例訂單的情況。 在一般實際執行環境中，中間層應用程式會建構要插入的銷售訂單。  
  
 上述指令碼會將銷售訂單插入記憶體最佳化資料表。 以 '_ondisk' 取代出現兩次的 '_inmem'，即可衍生將銷售訂單插入磁碟資料表的指令碼。  
  
 我們將在數個並行連接下，使用 ostress 工具執行這些指令碼。 我們將使用參數 '-n' 來控制連接數目，並使用參數 'r' 來控制每個連接上執行指令碼的次數。  
  
#### <a name="running-the-workload"></a>執行工作負載  
 為了進行規模測試，我們使用 100 個連接插入 1,000 萬個銷售訂單。 此測試會在適合的伺服器 (例如 8 個實體、16 個邏輯核心) 上適當地執行，並使用基本 SSD 儲存體儲存記錄檔。 如果此測試無法在您的硬體上正常執行，請檢閱 [為執行緩慢的測試疑難排解](#Troubleshootingslow-runningtests)一節。如果您想要降低此測試的壓力程度，請變更參數 '-n' 以減少連線數目。 例如，若要將連接計數減少到 40，請將參數 '-n100' 變更為 '-n40'。  
  
 我們使用執行工作負載之後由 ostress.exe 報告的經過時間，做為工作負載的效能度量。  
  
 下面的指示和度量使用插入 1 千萬個銷售訂單的工作負載。 如需執行插入 1 百萬個銷售訂單之縮小工作負載的相關指示，請參閱 'In-Memory OLTP\readme.txt' 中的指示，該檔案為 SQLServer2016CTP3Samples.zip 封存的一部分。  
  
##### <a name="memory-optimized-tables"></a>記憶體最佳化的資料表  
 首先執行記憶體最佳化資料表上的工作負載。 下列命令會開啟 100 個執行緒，每個執行緒執行 5,000 次反覆運算。  每次反覆運算會將 20 個銷售訂單插入個別交易。 每次反覆運算會插入 20 個訂單，以彌補使用資料庫產生要插入之資料的情況。 這樣會產生總計 20 \* 5,000 \* 100 = 10,000,000 個銷售訂單的插入。  
  
 開啟 RML CMD 命令提示字元，然後執行下列命令：  
  
 按一下 [複製] 按鈕複製命令，然後將其貼入 RML 公用程式命令提示字元。  
  
```console
ostress.exe -n100 -r5000 -S. -E -dAdventureWorks2016CTP3 -q -Q"DECLARE @i int = 0, @od Sales.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand() * 10000, @ShipMethodID int = (rand() * 5) + 1; INSERT INTO @od SELECT OrderQty, ProductID, SpecialOfferID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*106) + 1 as int); while (@i < 20) begin; EXEC Sales.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @ShipMethodID, @od; set @i += 1 end"  
```  
  
 在具有 8 顆實體 (16 顆邏輯) 核心的測試伺服器上，此作業約需 2 分 5 秒。 在具有 24 個實體 (48 個邏輯) 核心的第二部測試伺服器上，此作業需要 1 分 0 秒。  
  
 觀察執行工作負載時的 CPU 使用率，例如使用工作管理員。 您會看到 CPU 使用率接近 100%。 如果不是這種情況，則會發生記錄 IO 瓶頸 (也請參閱 [疑難排解執行緩慢的測試](#Troubleshootingslow-runningtests))。  
  
##### <a name="disk-based-tables"></a>磁碟型資料表  
 下列命令會執行磁碟資料表上的工作負載。 執行此工作負載可能需要一些時間，主要是因為系統中發生閂鎖爭用。 記憶體最佳化資料表不需閂鎖，因此不會發生這個問題。  
  
 開啟 RML CMD 命令提示字元，然後執行下列命令：  
  
 按一下 [複製] 按鈕複製命令，然後將其貼入 RML 公用程式命令提示字元。  
  
```console
ostress.exe -n100 -r5000 -S. -E -dAdventureWorks2016CTP3 -q -Q"DECLARE @i int = 0, @od Sales.SalesOrderDetailType_ondisk, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand() * 10000, @ShipMethodID int = (rand() * 5) + 1; INSERT INTO @od SELECT OrderQty, ProductID, SpecialOfferID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*106) + 1 as int); while (@i < 20) begin; EXEC Sales.usp_InsertSalesOrder_ondisk @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @ShipMethodID, @od; set @i += 1 end"  
```  
  
 在具有總數 8 個實體 (16 個邏輯) 核心的測試伺服器上，此作業需要 41 分 25 秒。 在具有 24 個實體 (48 個邏輯) 核心的第二部測試伺服器上，此作業需要 52 分 16 秒。  
  
 在此測試中，記憶體最佳化資料表與磁碟資料表之間出現效能差異的主要因素，是使用磁碟資料表時，SQL Server 無法充分運用 CPU。 原因在於閂鎖競爭：並行交易嘗試寫入相同的資料頁面，使用閂鎖可確保一次只有一筆交易可以寫入頁面。 記憶體內部 OLTP 引擎不需閂鎖，且資料列不是以頁面方式組織。 因此，並行交易不會封鎖彼此的插入，而能讓 SQL Server 充分運用 CPU。  
  
 您可以觀察執行工作負載時的 CPU 使用率，例如使用工作管理員。 您會看到磁碟資料表的 CPU 使用率遠低於 100%。 在具有 16 個邏輯處理器的測試組態中，使用率保持在 24% 左右。  
  
 或者，您可以使用效能監視器搭配效能計數器 '\SQL Server:Latches\Latch Waits/sec'，檢視每秒的閂鎖等候次數。  
  
#### <a name="resetting-the-demo"></a>重設示範  
 若要重設示範，請開啟 RML CMD 命令提示字元，然後執行下列命令：  
  
```console
ostress.exe -S. -E -dAdventureWorks2016CTP3 -Q"EXEC Demo.usp_DemoReset"  
```  
  
 視硬體而定，此作業可能需要幾分鐘才能執行。  
  
 建議每次執行示範之後將其重設。 由於此工作負載只能插入，每次執行會耗用更多記憶體，因此需要重設以防止記憶體不足。 [執行工作負載之後的記憶體使用量](#Memoryutilizationafterrunningtheworkload)章節中會討論執行後的記憶體耗用量。  
  
###  <a name="troubleshooting-slow-running-tests"></a><a name="Troubleshootingslow-runningtests"></a> 為執行緩慢的測試疑難排解  
 測試結果通常會隨硬體而有所不同，也會隨測試執行中使用並行的程度而有所不同。 如果結果不如預期，可注意下列幾點：  
  
-   並行交易數目：在單一執行緒上執行工作負載時，透過「記憶體內部 OLTP」提升的效能可能不到兩倍。 只有在高度並行的情況下，閂鎖競爭才會成為嚴重的問題。  
  
-   可供 SQL Server 使用的核心數目很低：這表示系統中的並行程度不高，因為同時執行的交易數目必須與 SQL 可用核心數目相同。  
  
    -   徵兆：執行磁碟資料表上的工作負載時，如果 CPU 使用率很高，並不是指競爭很多，而是指缺少並行。  
  
-   記錄磁碟機的速度：如果記錄磁碟機跟不上系統中的交易輸送量層級，則工作負載會在記錄 IO 上成為瓶頸。 雖然記憶體內部 OLTP 的記錄效率較高，但一旦記錄 IO 成為瓶頸，將會限制可能提升的效能。  
  
    -   徵兆：執行記憶體最佳化資料表上的工作負載時，如果 CPU 使用率離 100% 有段距離或急起急落，可能會發生記錄 IO 瓶頸。 您可以開啟資源監視器並查看記錄磁碟機的佇列長度，以確認是否發生此瓶頸。  
  
##  <a name="memory-and-disk-space-utilization-in-the-sample"></a><a name="MemoryandDiskSpaceUtilizationintheSample"></a> 範例中的記憶體和磁碟空間使用量  
 以下將描述範例資料庫之記憶體和磁碟空間使用量的預期結果。 我們也將顯示在具有 16 個邏輯核心的測試伺服器上看到的結果。  
  
###  <a name="memory-utilization-for-the-memory-optimized-tables"></a><a name="Memoryutilizationforthememory-optimizedtables"></a> 記憶體最佳化資料表的記憶體使用量  
  
#### <a name="overall-utilization-of-the-database"></a>資料庫的整體使用情況  
 下列查詢可用來取得系統中記憶體內部 OLTP 的總記憶體使用量。  
  
```sql
SELECT type  
   , name  
, pages_kb/1024 AS pages_MB   
FROM sys.dm_os_memory_clerks WHERE type LIKE '%xtp%'  
```  
  
 以下是剛建立資料庫之後的快照集：  
  
| type | NAME | pages_MB |
| ---- | ---- | -------- |
|MEMORYCLERK_XTP|預設|94|  
|MEMORYCLERK_XTP|DB_ID_5|877|  
|MEMORYCLERK_XTP|預設|0|  
|MEMORYCLERK_XTP|預設|0|  
  
 預設記憶體 Clerk 包含全系統記憶體結構，且規模相對較小。 使用者資料庫 (在此案例中為識別碼 5 的資料庫) 的記憶體 Clerk 約為 900 MB。  
  
#### <a name="memory-utilization-per-table"></a>每個資料表的記憶體使用量  
 下列查詢可用來向下鑽研至個別資料表及其索引的記憶體使用量：  
  
```sql
SELECT object_name(t.object_id) AS [Table Name]  
     , memory_allocated_for_table_kb  
 , memory_allocated_for_indexes_kb  
FROM sys.dm_db_xtp_table_memory_stats dms JOIN sys.tables t   
ON dms.object_id=t.object_id  
WHERE t.type='U'  
```  
  
 下表顯示範例全新安裝的查詢結果：  
  
| 資料表名稱 | memory_allocated_for_table_kb | memory_allocated_for_indexes_kb |
| ---------- | ----------------------------- | ------------------------------- |
|SpecialOfferProduct_inmem|64|3840|  
|DemoSalesOrderHeaderSeed|1984|5504|  
|SalesOrderDetail_inmem|15316|663552|  
|DemoSalesOrderDetailSeed|64|10432|  
|SpecialOffer_inmem|3|8192|  
|SalesOrderHeader_inmem|7168|147456|  
|Product_inmem|124|12352|  

 如您所見，這些資料表相當小：SalesOrderHeader_inmem 的大小約為 7 MB，而 SalesOrderDetail_inmem 的大小約為 15 MB。  
  
 此處值得注意的是，與資料表資料大小相較下，配置給索引的記憶體大小。 這是因為範例中的雜湊索引會預留大小以容納較大的資料。 請注意，雜湊索引有固定的大小，因此其大小不會隨資料表中的資料大小增加。  
  
####  <a name="memory-utilization-after-running-the-workload"></a><a name="Memoryutilizationafterrunningtheworkload"></a> 執行工作負載之後的記憶體使用量  
 插入 1,000 萬個銷售訂單之後，總記憶體使用量會類似如下：  
  
```sql
SELECT type  
, name  
, pages_kb/1024 AS pages_MB   
FROM sys.dm_os_memory_clerks WHERE type LIKE '%xtp%'  
```  
  
| type | NAME | pages_MB |
| ---- | ---- | -------- |
|MEMORYCLERK_XTP|預設|146|  
|MEMORYCLERK_XTP|DB_ID_5|7374|  
|MEMORYCLERK_XTP|預設|0|  
|MEMORYCLERK_XTP|預設|0|  

 如您所見，SQL Server 針對範例資料庫中的經記憶體最佳化的資料表和索引使用小於 8 GB 的記憶體。  
  
 請在執行一個範例之後，查看每個資料表的詳細記憶體使用量：  
  
```sql
SELECT object_name(t.object_id) AS [Table Name]  
     , memory_allocated_for_table_kb  
 , memory_allocated_for_indexes_kb  
FROM sys.dm_db_xtp_table_memory_stats dms JOIN sys.tables t   
ON dms.object_id=t.object_id  
WHERE t.type='U'  
```  
  
| 資料表名稱 | memory_allocated_for_table_kb | memory_allocated_for_indexes_kb |
| ---------- | ----------------------------- | ------------------------------- |
|SalesOrderDetail_inmem|5113761|663552|  
|DemoSalesOrderDetailSeed|64|10368|  
|SpecialOffer_inmem|2|8192|  
|SalesOrderHeader_inmem|1575679|147456|  
|Product_inmem|111|12032|  
|SpecialOfferProduct_inmem|64|3712|  
|DemoSalesOrderHeaderSeed|1984|5504|  

 我們會看到總計約 6.5 GB 的資料。 請注意，資料表 SalesOrderHeader_inmem 和 SalesOrderDetail_inmem 上的索引大小，與插入銷售訂單之前的索引大小相同。 由於這兩個資料表使用雜湊索引，而雜湊索引是靜態的，因此索引大小不會改變。  
  
#### <a name="after-demo-reset"></a>重設示範之後  
 您可以使用預存程序 Demo.usp_DemoReset 重設示範。 此預存程序會刪除資料表 SalesOrderHeader_inmem 和 SalesOrderDetail_inmem 中的資料，然後重新植入原始資料表 SalesOrderHeader 和 SalesOrderDetail 中的資料。  
  
 現在，即使已刪除資料表中的資料列，也不表示會立即回收記憶體。 SQL Server 會視需要在背景回收記憶體最佳化資料表中已刪除資料列的記憶體。 由於系統上沒有交易式工作負載，因此重設示範之後，您會立即看到系統尚未對已刪除的資料列進行記憶體回收：  
  
```sql
SELECT type  
, name  
, pages_kb/1024 AS pages_MB   
FROM sys.dm_os_memory_clerks WHERE type LIKE '%xtp%'  
```  
  
| type | NAME | pages_MB |
| ---- | ---- | -------- |
|MEMORYCLERK_XTP|預設|2261|  
|MEMORYCLERK_XTP|DB_ID_5|7396|  
|MEMORYCLERK_XTP|預設|0|  
|MEMORYCLERK_XTP|預設|0|  

 這是預期的結果：當交易式工作負載執行時，才會回收記憶體。  
  
 如果您開始執行第二次工作負載示範，一開始會看到記憶體使用量減少，這是由於之前刪除的資料列遭到清除所致。 在工作負載完成之前，記憶體大小會在某些時候再次增加。 重設示範並插入 1,000 萬個資料列之後，記憶體使用量與第一次執行之後的使用量非常相似。 例如：  
  
```sql
SELECT type  
, name  
, pages_kb/1024 AS pages_MB   
FROM sys.dm_os_memory_clerks WHERE type LIKE '%xtp%'  
```  
  
| type | NAME | pages_MB |
| ---- | ---- | -------- |
|MEMORYCLERK_XTP|預設|1863|  
|MEMORYCLERK_XTP|DB_ID_5|7390|  
|MEMORYCLERK_XTP|預設|0|  
|MEMORYCLERK_XTP|預設|0|  

### <a name="disk-utilization-for-memory-optimized-tables"></a>記憶體最佳化資料表的磁碟使用狀況  
 您可以使用下列查詢，找到給定時間下，資料庫的檢查點檔案在磁碟上的整體大小：  
  
```sql
SELECT SUM(df.size) * 8 / 1024 AS [On-disk size in MB]  
FROM sys.filegroups f JOIN sys.database_files df   
   ON f.data_space_id=df.data_space_id  
WHERE f.type=N'FX'  
  
```  
  
#### <a name="initial-state"></a>初始狀態  
 一開始建立範例檔案群組和範例記憶體最佳化資料表時，會預先建立許多檢查點檔案，然後系統會開始填入檔案 (預先建立的檢查點檔案數目取決於系統中的邏輯處理器數目)。 由於範例一開始非常小，因此預先建立的檔案在初始建立之後，大部分都是空的。  
  
 下列程式碼顯示具有 16 個邏輯處理器的機器上之範例在磁碟上的初始大小：  
  
```sql
SELECT SUM(df.size) * 8 / 1024 AS [On-disk size in MB]  
FROM sys.filegroups f JOIN sys.database_files df   
   ON f.data_space_id=df.data_space_id  
WHERE f.type=N'FX'  
```  
  
| 在磁碟上的大小 (MB) |
| ------------------ |
|2312|  

 如您所見，檢查點檔案在磁碟上的大小與實際資料大小差距懸殊，磁碟上的大小是 2.3 GB，而實際資料大小則接近 30 MB。  
  
 您可以使用下列查詢，更仔細地查看磁碟空間的使用來源。 這個查詢傳回的磁碟大小近似於狀態 5 (REQUIRED FOR BACKUP/HA)、6 (IN TRANSITION TO TOMBSTONE) 或 7 (TOMBSTONE) 的檔案。  
  
```sql
SELECT state_desc  
 , file_type_desc  
 , COUNT(*) AS [count]  
 , SUM(CASE  
   WHEN state = 5 AND file_type=0 THEN 128*1024*1024  
   WHEN state = 5 AND file_type=1 THEN 8*1024*1024  
   WHEN state IN (6,7) THEN 68*1024*1024  
   ELSE file_size_in_bytes  
    END) / 1024 / 1024 AS [on-disk size MB]   
FROM sys.dm_db_xtp_checkpoint_files  
GROUP BY state, state_desc, file_type, file_type_desc  
ORDER BY state, file_type  
```  
  
 初始狀態的範例結果，與具有 16 個邏輯處理器的伺服器相似：  
  
| state_desc | file_type_desc | count | 在磁碟上的大小 (MB) |
| ---------- | -------------- | ----- | --------------- |
|已預先建立|DATA|16|2048|  
|已預先建立|DELTA|16|128|  
|建構中|DATA|1|128|  
|建構中|DELTA|1|8|  

 如您所見，預先建立的資料和差異檔案使用了大部分空間。 SQL Server 會針對每個邏輯處理器預先建立一組 (資料和差異) 檔案。 此外，系統會為資料檔案預留 128 MB 的大小，並為差異檔案預留 8 MB 的大小，以便更有效率地將資料插入這些檔案。  
  
 記憶體最佳化資料表中的實際資料會包含在單一資料檔案中。  
  
#### <a name="after-running-the-workload"></a>執行工作負載之後  
 完成插入 1,000 萬個銷售訂單的單一測試執行之後，磁碟上的整體大小會類似如下 (16 個核心測試伺服器)：  
  
```sql
SELECT SUM(df.size) * 8 / 1024 AS [On-disk size in MB]  
FROM sys.filegroups f JOIN sys.database_files df   
   ON f.data_space_id=df.data_space_id  
WHERE f.type=N'FX'  
```  
  
| 在磁碟上的大小 (MB) |
| ------------------ |
|8828|
  
 在磁碟上的大小接近 9 GB，與記憶體中的資料大小更接近。  
  
 請更仔細地查看不同狀態的檢查點檔案大小：  
  
```sql
SELECT state_desc  
 , file_type_desc  
 , COUNT(*) AS [count]  
 , SUM(CASE  
   WHEN state = 5 AND file_type=0 THEN 128*1024*1024  
   WHEN state = 5 AND file_type=1 THEN 8*1024*1024  
   WHEN state IN (6,7) THEN 68*1024*1024  
   ELSE file_size_in_bytes  
    END) / 1024 / 1024 AS [on-disk size MB]   
FROM sys.dm_db_xtp_checkpoint_files  
GROUP BY state, state_desc, file_type, file_type_desc  
ORDER BY state, file_type  
```  
  
| state_desc | file_type_desc | count | 在磁碟上的大小 (MB) |
| ---------- | -------------- | ----- | --------------- |
|已預先建立|DATA|16|2048|  
|已預先建立|DELTA|16|128|  
|建構中|DATA|1|128|  
|建構中|DELTA|1|8|  

 我們還有 16 組預先建立的檔案，準備在關閉檢查點之後填入。  
  
 以及一組建構中的檔案，會在關閉目前的檢查點之前使用。 連同使用中的檢查點檔案，記憶體中 6.5 GB 資料的磁碟空間使用量約為 6.5 GB。 請注意，索引不會保存在磁碟上，因此在本例中，磁碟上的整體大小會小於記憶體中的大小。  
  
#### <a name="after-demo-reset"></a>重設示範之後  
 重設示範之後，如果系統上沒有交易式工作負載，且沒有資料庫檢查點，則不會立即回收磁碟空間。 對於要在各階段移動並在最後捨棄的檢查點檔案而言，需要發生一些檢查點和記錄截斷事件，才會開始合併檢查點檔案，並開始進行記憶體回收。 如果系統中有交易式工作負載，則會自動發生這些事件 (如果使用完整復原模式，則需要定期備份記錄)，但是當系統閒置時，不會發生這些事件 (如示範情況所示)。  
  
 在此範例中，您會在重設示範之後看到類似如下的情況：  
  
```sql
SELECT SUM(df.size) * 8 / 1024 AS [On-disk size in MB]  
FROM sys.filegroups f JOIN sys.database_files df   
   ON f.data_space_id=df.data_space_id  
WHERE f.type=N'FX'  
```  
  
| 在磁碟上的大小 (MB) |
| ------------------ |
|11839|
  
 接近 12 GB，明顯大於重設示範之前的 9 GB。 這是因為已開始合併某些檢查點檔案，但尚未安裝部分合併目標，且尚未清除部分合併來源檔案 (如下所示)：  
  
```sql
SELECT state_desc  
 , file_type_desc  
 , COUNT(*) AS [count]  
 , SUM(CASE  
   WHEN state = 5 AND file_type=0 THEN 128*1024*1024  
   WHEN state = 5 AND file_type=1 THEN 8*1024*1024  
   WHEN state IN (6,7) THEN 68*1024*1024  
   ELSE file_size_in_bytes  
    END) / 1024 / 1024 AS [on-disk size MB]   
FROM sys.dm_db_xtp_checkpoint_files  
GROUP BY state, state_desc, file_type, file_type_desc  
ORDER BY state, file_type  
```  
  
| state_desc | file_type_desc | count | 在磁碟上的大小 (MB) |
| ---------- | -------------- | ----- | --------------- |
|已預先建立|DATA|16|2048|  
|已預先建立|DELTA|16|128|  
|使用中|DATA|38|5152|  
|使用中|DELTA|38|1331|  
|合併目標|DATA|7|896|  
|合併目標|DELTA|7|56|  
|已合併來源|DATA|13|1772|  
|已合併來源|DELTA|13|455|  

 由於系統中發生交易活動，因此已安裝合併目標並清除已合併來源。  
  
 重設示範並在第二次執行工作負載示範時插入 1,000 萬個銷售訂單之後，您會看到第一次執行工作負載期間所建構的檔案已遭到清除。 如果您在工作負載執行期間執行數次上述查詢，便會看到檢查點檔案度過各個階段。  
  
 第二次執行工作負載並插入 1,000 萬個銷售訂單之後，您會看到磁碟使用狀況與第一次執行之後的磁碟使用狀況很相似，但不一定相同 (因為系統在本質上是動態的)。 例如：  
  
```sql
SELECT state_desc  
 , file_type_desc  
 , COUNT(*) AS [count]  
 , SUM(CASE  
   WHEN state = 5 AND file_type=0 THEN 128*1024*1024  
   WHEN state = 5 AND file_type=1 THEN 8*1024*1024  
   WHEN state IN (6,7) THEN 68*1024*1024  
   ELSE file_size_in_bytes  
    END) / 1024 / 1024 AS [on-disk size MB]   
FROM sys.dm_db_xtp_checkpoint_files  
GROUP BY state, state_desc, file_type, file_type_desc  
ORDER BY state, file_type  
```  
  
| state_desc | file_type_desc | count | 在磁碟上的大小 (MB) |
| ---------- | -------------- | ----- | --------------- |
|已預先建立|DATA|16|2048|  
|已預先建立|DELTA|16|128|  
|建構中|DATA|2|268|  
|建構中|DELTA|2|16|  
|使用中|DATA|41|5608|  
|使用中|DELTA|41|328|  

 在本例中，有兩個檢查點檔案組在「建構中」狀態，這表示多組檔案已移至「建構中」狀態，這可能是由於工作負載中有高度並行所致。 多個並行執行緒會同時要求一個新的檔案組，因此會將一組檔案從「已預先建立」移至「建構中」。  
  
## <a name="see-also"></a>另請參閱

[記憶體內部 OLTP &#40;記憶體內部最佳化&#41;](in-memory-oltp-in-memory-optimization.md)