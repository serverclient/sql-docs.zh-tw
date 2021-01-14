---
description: 建立分割區資料表及索引
title: 建立資料分割資料表及索引 | Microsoft Docs
ms.custom: ''
ms.date: 1/5/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
f1_keywords:
- sql13.swb.createpartition.progress.f1
- sql13.swb.createpartition.partitioncolumn.f1
- sql13.swb.createpartition.createjob.f1
- sql13.swb.createpartition.finish.f1
- sql13.swb.createpartition.selectoutput.f1
- sql13.swb.createpartition.partitionfunction.f1
- sql13.swb.createpartition.partitionscheme.f1
- sql13.swb.createpartition.getstart.f1
- sql13.swb.createpartition.mappartition.f1
- sql13.swb.createpartition.summary.f1
helpviewer_keywords:
- partitioned indexes [SQL Server], creating
- partition schemes [SQL Server], creating
- partition functions [SQL Server], creating
- partitioned tables [SQL Server], creating
- partition functions [SQL Server]
- partition schemes [SQL Server]
ms.assetid: 7641df10-1921-42a7-ba6e-4cb03b3ba9c8
author: julieMSFT
ms.author: jrasnick
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 387a2f88afd22004f7146384ce29ddc2ba2f2da7
ms.sourcegitcommit: 629229a7c33a3ed99db63b89127bb016449f7d3d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97952047"
---
# <a name="create-partitioned-tables-and-indexes"></a>建立分割區資料表及索引
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  您可以使用 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 或 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] ，在 [!INCLUDE[tsql](../../includes/tsql-md.md)]中建立分割區資料表或索引。 分割區資料表及索引中的資料會被水平分割成單元，可散佈在資料庫中的多個檔案群組中。 分割作業可讓大型資料表和索引更容易管理及擴充。  
  
 分割區資料表或索引的建立過程通常分為四個部分：  
  
1.  建立一個或多個檔案群組和對應的檔案，它們將存放分割區配置所指定的分割區。  
  
2.  建立分割區函數，此函數根據指定之資料行的各個值，將資料表或索引的資料列對應到分割區中。  
  
3.  建立分割區配置，將分割區資料表或索引的分割區，對應至新的檔案群組。  
  
4.  建立或修改資料表或索引，以及指定分割區配置做為儲存位置。  
 
> [!NOTE]
> Azure [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] 只支援主要檔案群組。  
  
 **本主題內容**  
  
-   **開始之前：**  
  
     [限制事項](#Restrictions)  
  
     [安全性](#Security)  
  
-   **使用下列方法建立分割區資料表或索引：**  
  
     [Transact-SQL](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 限制事項  
  
-   分割區函數和配置的範圍只限於它們建立所在的資料庫。 在這個資料庫內，分割區函數是在不同於其他函數的個別命名空間中。  
  
-   如果分割區函數內的任何資料列含有 Null 值的分割資料行，這些資料列都會配置到最左邊的分割區。 不過，如果將 NULL 指定為界限值且指示 RIGHT，最左邊的分割區會保持空白，而且 NULL 值會放在第二個分割區。  
  
###  <a name="security"></a><a name="Security"></a> Security  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 建立分割區資料表，需要資料庫中的 CREATE TABLE 權限及建立資料表的結構描述之 ALTER 權限。 建立分割區索引，需要建立索引的資料表或檢視的 ALTER 權限。 建立分割區資料表或索引，還需要下列任何一個附加權限：  
  
-   ALTER ANY DATASPACE 權限。 這個權限預設會授與 **sysadmin** 固定伺服器角色以及 **db_owner** 和 **db_ddladmin** 固定資料庫角色的成員。  
  
-   建立分割區函數和分割區配置之資料庫的 CONTROL 或 ALTER 權限。  
  
-   建立分割區函數和分割區配置之資料庫伺服器的 CONTROL SERVER 或 ALTER ANY DATABASE 權限。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
 請遵循此程序中的步驟，建立一個或多個檔案群組、對應檔案和資料表。 在下一個程序中建立分割區資料表時，您將會參考這些物件。  
  
#### <a name="to-create-new-filegroups-for-a-partitioned-table"></a>為分割區資料表建立新的檔案群組  
  
1.  在物件總管中，以滑鼠右鍵按一下要建立分割區資料表的資料庫，然後選取 [屬性]。  
  
2.  在 [資料庫屬性 - *database_name*] 對話方塊的 [選取頁面] 底下，選取 [檔案群組]。  
  
3.  按一下 **[資料列]** 底下的 **[加入]**。 在新資料列中，輸入檔案群組名稱。  
  
    > [!WARNING]  
    >  建立分割區時，如果要指定多個檔案群組，除了針對界限值指定的檔案群組數目以外，一律要一個額外的檔案群組。  
  
4.  繼續加入資料列，直到為分割區資料表建立所有的檔案群組。  
  
5.  按一下 [確定]。  
  
6.  在 **[選取頁面]** 底下，選取 **[檔案]**。  
  
7.  按一下 **[資料列]** 底下的 **[加入]**。 在新資料列中，輸入檔案名稱並選取檔案群組。  
  
8.  繼續加入資料列，直到為每個檔案群組建立至少一個檔案。  
  
9. 展開 **[資料表]** 資料夾，像平常一樣地建立資料表。 如需詳細資訊，請參閱[建立資料表 &#40;Database Engine&#41;](../../relational-databases/tables/create-tables-database-engine.md)。 或者，您可以在下個程序中指定現有的資料表。  
  
#### <a name="to-create-a-partitioned-table"></a>建立分割區資料表  
  
1.  以滑鼠右鍵按一下要分割的資料表，指向 [儲存體]，然後按一下 [建立分割區...]。  
  
2.  在 **[建立分割區精靈]** 的 **[歡迎使用建立分割區精靈]** 頁面上，按 **[下一步]**。  
  
3.  在 **[選取分割資料行]** 頁面的 **[可用的分割資料行]** 方格中，選取要用來分割資料表的資料行。 只有包含可用來分割資料之資料類型的資料行才會顯示在 **[可用的分割資料行]** 方格中。 如果您選取了某個計算資料行當做分割資料行，就必須將此資料行指定為保存的資料行。  
  
     您對分割資料行和值範圍擁有的選擇主要是由資料可以按邏輯方式分組到什麼程度所決定。 例如，您可能會選擇依據每年的月份或季度，將資料分成邏輯群組。 您打算針對資料進行的查詢將會決定這個邏輯群組是否足夠用於管理資料表分割區。 除了 **text**、 **ntext**、 **image**、 **xml**、 **timestamp**、 **varchar(max)**、 **nvarchar(max)**、 **varbinary(max)**、別名資料類型或 CLR 使用者自訂資料類型，所有資料類型都能有效用在分割資料行上。  
  
     在此頁面上可以使用下列其他選項：  
  
     **共置此資料表至選取的分割區資料表**  
     可讓您選取分割區資料表，其中包含要在此分割資料行上與這個資料表聯結的相關資料。 在分割資料行上聯結分割區的資料表通常會讓查詢更有效率。  
  
     **使用索引的分割區資料行讓非唯一索引和唯一索引進行儲存體對齊**  
     讓使用相同分割區配置進行分割區的所有資料表索引對齊。 當資料表及其索引對齊時，您就可以更有效率地將分割區移入和移出分割區資料表，因為資料是使用相同的演算法進行分割區。  
  
     選取分割資料行和任何其他選項之後，請按 **[下一步]**。  
  
4.  在 **[選取分割區函數]** 頁面，按一下 **[選取分割區函數]** 底下的 **[新的分割區函數]** 或 **[現有的分割區函數]**。 如果您選擇 **[新的分割區函數]**，請輸入函數的名稱。 如果您選擇 [現有的分割區函數]，請從清單中選取要使用的函數名稱。 如果資料庫上沒有其他分割區函數， **[現有的分割區函數]** 選項就無法使用。  
  
     完成這個頁面之後，請按 **[下一步]** 。  
  
5.  在 **[選取分割區配置]** 頁面，按一下 **[選取分割區配置]** 底下的 **[新的分割區配置]** 或 **[現有的分割區配置]**。 如果您選擇 **[新的分割區配置]**，請輸入配置的名稱。 如果您選擇 [現有的分割區配置]，請從清單中選取要使用的配置名稱。 如果資料庫上沒有其他分割區配置， **[現有的分割區配置]** 選項就無法使用。  
  
     完成這個頁面之後，請按 **[下一步]** 。  
  
6.  在 **[對應分割區]** 頁面上，選取 **[範圍]** 底下的 **[左界限]** 或 **[右界限]** ，指定要在建立的每個檔案群組中包含最高或最低的界限值。 建立分割區時，如果要指定多個檔案群組，除了針對界限值指定的檔案群組數目以外，一律要輸入一個額外的檔案群組。  
  
     在 **[選取檔案群組並指定界限值]** 方格中的 **[檔案群組]** 底下，選取要用於分割資料的檔案群組。 在 **[界限]** 下方，輸入每個檔案群組的界限值。 如果界限值保持空白，分割區函數會使用分割區函數名稱，將整份資料表或索引對應到單一分割區。  
  
     在此頁面上可以使用下列其他選項：  
  
     **設定界限…**  
     開啟 **[設定界限值]** 對話方塊，即可選取您想要用於分割區的界限值和日期範圍。 只有當您選取了包含下列其中一種資料類型的分割資料行時，才能使用這個選項： **date**、 **datetime**、 **smalldatetime**、 **datetime2** 或 **datetimeoffset**。  
  
     **估計儲存體**  
     針對指定給分割區的每個檔案群組估計儲存體的列數、需要空間和可用空間。 這些值都會在方格中顯示成唯讀值。  
  
     **[設定界限值]** 對話方塊允許下列其他選項：  
  
     **開始日期**  
     針對分割區的範圍值選取開始日期。  
  
     **結束日期**  
     針對分割區的範圍值選取結束日期。 如果您在 [對應分割區] 頁面上選取 [左界限]，這個日期就會成為每個檔案群組/分割區的最後一個值。 如果您在 [對應分割區] 頁面上選取 [右界限]，這個日期就會成為倒數第二個檔案群組的第一個值。  
  
     **日期範圍**  
     針對每個分割區選取您想要的日期資料粒度或範圍值遞增。  
  
     完成這個頁面之後，請按 **[下一步]** 。  
  
7.  在 **[選取輸出選項]** 頁面上，指定要如何完成分割區資料表。 選取 **[建立指令碼]** ，根據精靈中先前的步驟建立 SQL 指令碼。 選取 **[立即執行]** ，在完成精靈中的其餘所有頁面後建立新的分割區資料表。 選取 **[排程]** ，在預先定義的未來日期建立新的分割區資料表。  
  
     如果您選取 **[建立指令碼]** ，在 **[指令碼選項]** 底下可以使用下列選項：  
  
     **編寫指令碼至檔案**  
     產生指令碼做為 .sql 檔案。 在 **[檔案名稱]** 方塊中輸入檔案名稱和位置，或按一下 **[瀏覽]** 開啟 **[指令碼檔案位置]** 對話方塊。 從 **[另存新檔]** ，選取 **[Unicode 文字]** 或 **[ANSI 文字]** 。  
  
     **編寫指令碼至剪貼簿**  
     將指令碼儲存至剪貼簿。  
  
     **編寫指令碼至新增查詢視窗**  
     產生指令碼至新的 [查詢編輯器] 視窗。 這是預設選項。  
  
     如果您選取 **[排程]** ，請按一下 **[變更排程]** 。  
  
    1.  在 [新增作業排程] 對話方塊的 [名稱] 方塊中，輸入作業排程的名稱。  
  
    2.  在 **[排程類型]** 清單，選取排程類型：  
  
        -   **當 SQL Server Agent 啟動時自動啟動**  
  
        -   **只要 CPU 閒置就啟動**  
  
        -   **重複執行**： 如果您的新分割區資料表會使用新資訊定期更新，請選取此選項。  
  
        -   **執行一次**： 這是預設選項。  
  
    3.  選取或清除 **[已停用]** 核取方塊，以啟用或停用排程。  
  
    4.  如果您選取 **[重複執行]** ：  
  
        1.  在 **[頻率]** 底下的 **[發生於]** 清單中，指定發生頻率：  
  
            -   如果您選取 **[每天]** ，在 **[重複頻率]** 方塊中，輸入幾天重複一次作業排程的頻率。  
  
            -   如果您選取 **[每週]** ，在 **[重複頻率]** 方塊中，輸入幾週重複一次作業排程的頻率。 選取一週中執行作業排程是在星期幾。  
  
            -   如果您選取 **[每月]** ，可以選取 **[天]** 或 **[於]** 。  
  
                -   如果您選取 **[天]** ，請輸入執行作業排程的當月日期以及幾個月重複一次作業排程的頻率。 例如，若要在每兩個月的 15 日執行一次作業排程，請選取 [日]，然後在第一個方塊中輸入 "15"，並在第二個方塊中輸入 "2"。 請注意，在第二個方塊中允許的最大數目是 "99"。  
  
                -   如果您選取 **[於]** ，請選取執行作業排程的當月一週中特定的星期幾，以及幾個月重複一次作業排程的頻率。 例如，若要在每兩個月的最後一個工作日執行一次作業排程，請選取 [日]，然後從第一個清單中選取 [最後一個]，並從第二個清單中選取 [工作日]，然後在最後一個方塊中輸入 "2"。 您也可以在前兩個清單中選取 [第一個]、[第二個]、[第三個] 或 [第四個]，以及特定工作日 (例如：星期日或星期三)。 請注意，在最後一個方塊中允許的最大數目是 "99"。  
  
        2.  在 **[每日頻率]** 底下，指定在執行作業排程當天重複作業排程的頻率：  
  
            -   如果您選取 **[執行一次於]** ，請在 **[執行一次於]** 方塊中輸入執行作業排程的當天特定時間。 輸入時、分鐘和秒的時間，以及上午或下午。  
  
            -   如果您選取 **[重複執行於每]** ，請在 **[頻率]** 底下指定在所選當天執行作業排程的頻率。 例如，若要在執行作業排程的當天每 2 個小時重複一次作業排程，請選取 [發生間隔]，在第一個方塊中輸入 "2"，然後從清單中選取 [小時]。 您也可以從這個清單中選取 [分鐘] 和 [秒]。 請注意，在第一個方塊中允許的最大數目是 "100"。  
  
                 在 **[開始時間]** 方塊中，輸入作業排程應該開始執行的時間。 在 **[結束時間]** 方塊中，輸入作業排程應該停止重複的時間。 輸入時、分鐘和秒的時間，以及上午或下午。  
  
        3.  在 **[持續時間]** 底下的 **[開始日期]** ，輸入您希望作業排程開始執行的日期。 選取 **[結束日期]** 或 **[沒有結束日期]** ，以指示作業排程應該停止執行的日期。 如果您選取 **[結束日期]** ，請輸入您希望作業排程停止執行的日期。  
  
    5.  如果您選取 [執行一次]，請在 [僅執行一次於] 底下的 [日期] 方塊中，輸入將要執行作業排程的日期。 在 **[時間]** 方塊中，輸入將要執行作業排程的時間。 輸入時、分鐘和秒的時間，以及上午或下午。  
  
    6.  在 **[摘要]** 底下的 **[描述]** ，確認所有作業排程設定是否都正確。  
  
    7.  按一下 [確定]。  
  
     完成這個頁面之後，請按 **[下一步]** 。  
  
8.  在 **[檢閱摘要]** 頁面上，展開 **[檢閱您的選擇]** 底下的所有可用選項，確認所有分割區設定是否都正確。 如果一切如預期，請按一下 **[完成]** 。  
  
9. 在 **[建立分割區精靈進度]** 頁面上，監視 [建立分割區精靈] 動作的狀態資訊。 根據您在精靈中選取的選項，[進度] 頁面可能會包含一個或多個動作。 頂端的方塊會顯示精靈的整體狀態以及精靈已接收的狀態、錯誤和警告訊息數。  
  
     在 **[建立分割區精靈進度]** 頁面上可以使用下列選項：  
  
     **詳細資料**  
     提供從精靈所採取的動作傳回的動作、狀態和任何訊息。  
  
     **動作**  
     指定每個動作的類型和名稱。  
  
     **狀態**  
     指出整個精靈動作傳回 [成功] 或 [失敗] 的值。  
  
     **訊息**  
     提供從程序所傳回的任何錯誤或警告訊息。  
  
     **Report**  
     建立包含 [建立分割區精靈] 結果的報表。 選項為 **[檢視報表]** 、 **[將報表儲存到檔案]** 、 **[複製報表到剪貼簿]** 和 **[以電子郵件傳送報表]** 。  
  
     **檢視報表**  
     開啟 [檢視報表] 對話方塊，其中包含 [建立分割區精靈] 進度的文字報表。  
  
     **將報表儲存到檔案**  
     開啟 [另存報表] 對話方塊。  
  
     **複製報表到剪貼簿**  
     將精靈進度報表的結果複製到剪貼簿。  
  
     **[以電子郵件傳送報表]**  
     將精靈進度報表的結果複製到電子郵件。  
  
     完成後，請按一下 **[關閉]** 。  
  
 建立分割區精靈會建立分割區函數和配置，然後將此分割區套用至指定資料表。 若要驗證資料表分割區，在物件總管中以滑鼠右鍵按一下資料表，並選取 [屬性]。 按一下 **[儲存體]** 頁面。 此頁面會顯示諸如分割區函數和配置名稱以及分割區數目等資訊。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
  
#### <a name="to-create-a-partitioned-table"></a>建立分割區資料表  
  
1.  在 **[物件總管]** 中，連接到 [!INCLUDE[ssDE](../../includes/ssde-md.md)]的執行個體。  
  
2.  在標準列上，按一下 **[新增查詢]** 。  
  
3.  複製下列範例並將其貼到查詢視窗中，然後按一下 **[執行]** 。 範例會建立新的檔案群組、分割區函數和分割區配置。 新資料表是以指定為儲存位置的分割區配置建立的。  
  
    ```  
    USE AdventureWorks2012;  
    GO  
    -- Adds four new filegroups to the AdventureWorks2012 database  
    ALTER DATABASE AdventureWorks2012  
    ADD FILEGROUP test1fg;  
    GO  
    ALTER DATABASE AdventureWorks2012  
    ADD FILEGROUP test2fg;  
    GO  
    ALTER DATABASE AdventureWorks2012  
    ADD FILEGROUP test3fg;  
    GO  
    ALTER DATABASE AdventureWorks2012  
    ADD FILEGROUP test4fg;   
  
    -- Adds one file for each filegroup.  
    ALTER DATABASE AdventureWorks2012   
    ADD FILE   
    (  
        NAME = test1dat1,  
        FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\t1dat1.ndf',  
        SIZE = 5MB,  
        MAXSIZE = 100MB,  
        FILEGROWTH = 5MB  
    )  
    TO FILEGROUP test1fg;  
    ALTER DATABASE AdventureWorks2012   
    ADD FILE   
    (  
        NAME = test2dat2,  
        FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\t2dat2.ndf',  
        SIZE = 5MB,  
        MAXSIZE = 100MB,  
        FILEGROWTH = 5MB  
    )  
    TO FILEGROUP test2fg;  
    GO  
    ALTER DATABASE AdventureWorks2012   
    ADD FILE   
    (  
        NAME = test3dat3,  
        FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\t3dat3.ndf',  
        SIZE = 5MB,  
        MAXSIZE = 100MB,  
        FILEGROWTH = 5MB  
    )  
    TO FILEGROUP test3fg;  
    GO  
    ALTER DATABASE AdventureWorks2012   
    ADD FILE   
    (  
        NAME = test4dat4,  
        FILENAME = 'C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\MSSQL\DATA\t4dat4.ndf',  
        SIZE = 5MB,  
        MAXSIZE = 100MB,  
        FILEGROWTH = 5MB  
    )  
    TO FILEGROUP test4fg;  
    GO  
    -- Creates a partition function called myRangePF1 that will partition a table into four partitions  
    CREATE PARTITION FUNCTION myRangePF1 (int)  
        AS RANGE LEFT FOR VALUES (1, 100, 1000) ;  
    GO  
    -- Creates a partition scheme called myRangePS1 that applies myRangePF1 to the four filegroups created above  
    CREATE PARTITION SCHEME myRangePS1  
        AS PARTITION myRangePF1  
        TO (test1fg, test2fg, test3fg, test4fg) ;  
    GO  
    -- Creates a partitioned table called PartitionTable that uses myRangePS1 to partition col1  
    CREATE TABLE PartitionTable (col1 int PRIMARY KEY, col2 char(10))  
        ON myRangePS1 (col1) ;  
    GO  
    ```  


#### <a name="to-create-a-partitioned-table-in-azure-sqldbesa"></a>在 Azure [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] 中建立資料分割資料表

Azure [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] 不支援新增檔案和檔案群組，但只有跨 PRIMARY 檔案群組進行資料分割時支援資料表分割區。
  
1.  在 **[物件總管]** 中，連接到 [!INCLUDE[ssDE](../../includes/ssde-md.md)]的執行個體。  
  
1.  在標準列上，按一下 **[新增查詢]** 。  
  
1.  複製下列範例並將其貼到查詢視窗中，然後按一下 **[執行]** 。 此範例會建立資料分割函數和資料分割配置。 新資料表是以指定為儲存位置的分割區配置建立的。 

    ```
    -- Creates a partition function called myRangePF1 that will partition a table into four partitions  
    CREATE PARTITION FUNCTION myRangePF1 (int)  
        AS RANGE LEFT FOR VALUES (1, 100, 1000) ;  
    GO  
    -- Creates a partition scheme called myRangePS1 that applies myRangePF1 to the PRIMARY filegroup 
    CREATE PARTITION SCHEME myRangePS1  
        AS PARTITION myRangePF1  
        ALL TO ('PRIMARY') ;  
    GO  
    -- Creates a partitioned table called PartitionTable that uses myRangePS1 to partition col1  
    CREATE TABLE PartitionTable (col1 int PRIMARY KEY, col2 char(10))  
        ON myRangePS1 (col1) ;  
    GO
    ```  

  
#### <a name="to-determine-if-a-table-is-partitioned"></a>若要判斷資料表是否已分割  
  
1.  下列查詢會在資料表 `PartitionTable` 已分割時，傳回一個或多個資料列。 如果資料表未分割，則不會傳回任何資料列。  
  
    ```  
    SELECT *   
    FROM sys.tables AS t   
    JOIN sys.indexes AS i   
        ON t.[object_id] = i.[object_id]   
        AND i.[type] IN (0,1)   
    JOIN sys.partition_schemes ps   
        ON i.data_space_id = ps.data_space_id   
    WHERE t.name = 'PartitionTable';   
    GO  
    ```  
  
#### <a name="to-determine-the-boundary-values-for-a-partitioned-table"></a>若要判斷分割資料表的界限值  
  
1.  下列查詢會針對 `PartitionTable` 資料表中的每一個分割區傳回界限值。  
  
    ```  
    SELECT t.name AS TableName, i.name AS IndexName, p.partition_number, p.partition_id, i.data_space_id, f.function_id, f.type_desc, r.boundary_id, r.value AS BoundaryValue   
    FROM sys.tables AS t  
    JOIN sys.indexes AS i  
        ON t.object_id = i.object_id  
    JOIN sys.partitions AS p  
        ON i.object_id = p.object_id AND i.index_id = p.index_id   
    JOIN  sys.partition_schemes AS s   
        ON i.data_space_id = s.data_space_id  
    JOIN sys.partition_functions AS f   
        ON s.function_id = f.function_id  
    LEFT JOIN sys.partition_range_values AS r   
        ON f.function_id = r.function_id and r.boundary_id = p.partition_number  
    WHERE t.name = 'PartitionTable' AND i.type <= 1  
    ORDER BY p.partition_number;  
    ```  
  
#### <a name="to-determine-the-partition-column-for-a-partitioned-table"></a>若要判斷分割資料表的分割區資料行  
  
1.  下列查詢會傳回資料表之分割區資料行的名稱。 `PartitionTable`.  
  
    ```  
    SELECT   
        t.[object_id] AS ObjectID   
        , t.name AS TableName   
        , ic.column_id AS PartitioningColumnID   
        , c.name AS PartitioningColumnName   
    FROM sys.tables AS t   
    JOIN sys.indexes AS i   
        ON t.[object_id] = i.[object_id]   
        AND i.[type] <= 1 -- clustered index or a heap   
    JOIN sys.partition_schemes AS ps   
        ON ps.data_space_id = i.data_space_id   
    JOIN sys.index_columns AS ic   
        ON ic.[object_id] = i.[object_id]   
        AND ic.index_id = i.index_id   
        AND ic.partition_ordinal >= 1 -- because 0 = non-partitioning column   
    JOIN sys.columns AS c   
        ON t.[object_id] = c.[object_id]   
        AND ic.column_id = c.column_id   
    WHERE t.name = 'PartitionTable' ;   
    GO  
    ```  
  
 如需詳細資訊，請參閱  
  
-   [ALTER DATABASE 檔案及檔案群組選項 &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-file-and-filegroup-options.md)  
  
-   [CREATE PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-function-transact-sql.md)  
  
-   [CREATE PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-scheme-transact-sql.md)  
  
-   [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)  
  
  
