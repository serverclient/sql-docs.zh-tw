---
description: 管理系統建立版本之時態表中的歷程記錄資料保留
title: 管理由系統設定版本之時態表中的歷程記錄資料
ms.custom: seo-lt-2019
ms.date: 05/18/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
ms.assetid: 7925ebef-cdb1-4cfe-b660-a8604b9d2153
author: markingmyname
ms.author: maghan
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: c0dac2b5d344969f96016a86622ee4e07ab8c23d
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98167994"
---
# <a name="manage-retention-of-historical-data-in-system-versioned-temporal-tables"></a>管理系統建立版本之時態表中的歷程記錄資料保留


[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]


使用系統設定版本的時態表時，歷程記錄資料表增加資料庫大小的程度可能會比一般資料表大，特別是在下列情況下︰

- 您的歷程記錄資料保留一段很長的時間
- 您更新或刪除大量資料修改模式

對於不斷成長的大型歷程記錄資料表來說，單是儲存成本和加諸於時態查詢之上的效能負擔就可能會造成問題。 因此，開發資料保留原則來管理歷程記錄資料表中的資料是規劃及管理每個時態表生命週期的重要環節。

## <a name="data-retention-management-for-history-table"></a>歷程記錄資料表的資料保留管理

若要管理時態表的資料保留，第一步是決定每個時態表所需的保留期限。 在大部分情況下，您應將保留原則視為使用時態表之應用程式商務邏輯的一部分。 例如，資料稽核和時間移動案例中的應用程式，對於供應線上查詢之歷程記錄資料的保留期間有嚴格的規範。

一旦決定資料保留期限後，下一步擬定計劃來管理歷程記錄資料的儲存方式和位置，以及刪除超過保留需求之歷程記錄資料的方式。 您可以使用下列四種方法來管理時態歷程記錄資料表中的歷程記錄資料：

- [Stretch Database](#using-stretch-database-approach)
- [資料表資料分割](#using-table-partitioning-approach)
- [自訂清除指令碼](#using-custom-cleanup-script-approach)
- [保留原則](#using-temporal-history-retention-policy-approach)

 對於以上任一種方式，移轉或清除歷程記錄資料的邏輯乃基於與目前資料表之期間結束相對應的資料行。 每個資料列的期間結束值決定資料列版本「關閉」時間，也就是落在歷程記錄資料表的時間。 例如， `SysEndTime < DATEADD (DAYS, -30, SYSUTCDATETIME ())` 條件指定超過一個月的歷程記錄資料需要移除或移出歷程記錄資料表。

> [!NOTE]
> 本主題中的範例使用此[時態表範例](creating-a-system-versioned-temporal-table.md)。

## <a name="using-stretch-database-approach"></a>使用 Stretch Database 方法

> [!NOTE]
> Stretch Database 方法僅適用於 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]，不適用於 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。

[Stretch Database](../../sql-server/stretch-database/stretch-database.md) 中的 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 能以透明的方式將歷程記錄資料移轉到 Azure。 若要提升安全性，您可以使用 SQL Server 的 [一律加密](../security/encryption/always-encrypted-database-engine.md) 功能為移動中的資料加密。 此外，您還可以使用 [資料列層級安全性](../../relational-databases/security/row-level-security.md) 和其他先進的 SQL Server 安全性功能來搭配 Temporal 和 Stretch Database，藉此保護資料。

透過 Stretch Database 方法，您可以將部分或所有時態歷程記錄資料表延展到 Azure，而 SQL Server 將會以無訊息的方式將歷程記錄資料移動到 Azure。 讓歷程記錄資料表具備延展功能，不會變更您與時態表在資料修改和時態查詢等方面的互動方式。

- **延展整個歷程記錄資料表：** 在您的主要案例是在資料頻繁變更和歷程記錄資料查詢相對稀少的環境中進行資料稽核時，為整個記錄資料表設定 Stretch Database。 換句話說，如果時態查詢的效能不重要，您可以使用這個方法。 在此情況下，Azure 提供的成本效益極具吸引力。 在延展整個歷程記錄資料表時，您可以使用延展精靈或 Transact-SQL。 如需這兩者的範例，請參閱下文。

- **延展記錄資料表的一部分：** 在您的主要案例主要涉及查詢最近的歷程記錄資料，但您想要保留在需要時查詢舊有歷程記錄資料的選項，同時以較低成本將這些資料儲存在遠端時，針對一部分的歷程記錄資料表設定 Stretch Database 來改善效能。 透過 Transact-SQL，您可以指定述詞函式來選取要從記錄資料表移轉的資料列 (而不是移轉所有資料列)，進而達成前述目的。 在使用時態表時，根據時間條件移動資料 (也就是根據歷程記錄資料表中資料列版本的存在時間) 通常是合理的做法。

  藉由確定性述詞函數，您可以將歷程記錄的一部分保存在與目前資料相同的資料庫內，再將其他部分移轉到 Azure。 如需範例與限制，請參閱 [使用篩選函數來選取要移轉的資料列 (Stretch Database)](../../sql-server/stretch-database/select-rows-to-migrate-by-using-a-filter-function-stretch-database.md)。 由於非確定性的函數不合法，所以如果您想要以滑動視窗的方式傳輸歷程記錄資料，必須定期變更內嵌述詞函數的定義，讓保存在本機之資料列視窗的存在時間保持不變。 滑動視窗可讓您不斷將超過一個月的歷程記錄資料移動到 Azure。 如需這個方法的範例，請參閱下文。

> [!NOTE]
> Stretch Database 能將資料移轉到 Azure。 因此，您必須擁有 Azure 帳戶和計費用的訂閱。 若要取得免費試用的 Azure 帳戶，請按一下[免費試用一個月](https://azure.microsoft.com/pricing/free-trial/)。

您可以使用延展精靈或 Transact-SQL 來設定 Stretch 的時態歷程記錄資料表，而且可以在系統版本設定功能設定為 [ON]  時啟用時態歷程記錄資料表的延展功能。 您無法延展目前的資料表，因為延展目前的資料表不具意義。

### <a name="using-the-stretch-wizard-to-stretch-the-entire-history-table"></a>使用延展精靈來延展整個歷程記錄資料表

對初學者來說，最簡單的方法是使用延展精靈來啟用整個資料庫的延展功能，接著在延展精靈中選取時態歷程記錄資料表 (以下範例假設您已將 Department 資料表設定為空資料庫中的系統設定版本時態資料表)。 在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 中，您無法以滑鼠右鍵按一下時態歷程記錄資料表本身，然後再按一下 [延展]。

1. 以滑鼠右鍵按一下資料庫，並指向 [工作]  和 [延展]  ，然後按一下 [啟用]  來啟動精靈。
2. 在 [選取資料表]  視窗中，選取時態歷程記錄資料表的核取方塊，然後按 [下一步]。

    ![在 [選取資料表] 頁面上選取記錄資料表](../../relational-databases/tables/media/stretch-wizard-2-for-temporal.png "在 [選取資料表] 頁面上選取記錄資料表")
3. 在 [設定 Azure]  視窗中，提供您的登入認證。 登入 Microsoft Azure 或註冊帳戶。 選取要使用的訂閱，並選取 Azure 區域。 接下來，建立新伺服器或選取現有伺服器。 按 [下一步]  。

    ![建立新的 Azure 伺服器 - Stretch Database 精靈](../../relational-databases/tables/media/stretch-wizard-4.png "建立新的 Azure 伺服器 - Stretch Database 精靈")
4. 在 [安全認證]  視窗中，提供資料庫主要金鑰的密碼來保護來源 SQL Server 資料庫認證，然後按 [下一步]。

    ![Stretch Database 精靈的 [安全認證] 頁面](../../relational-databases/tables/media/stretch-wizard-6.png "Stretch Database 精靈的 [安全認證] 頁面")
5. 在 [選取 IP 位址]  視窗中提供 SQL Server 的 IP 位址範圍，讓 Azure 伺服器得以與您的 SQL Server 通訊 (如果您選取已有防火牆規則的現有伺服器，在這裡只要按 [下一步] 就能使用現有的防火牆規則)。 依序按 [下一步]  和 [完成]  以啟用 Stretch Database 並延展時態性記錄資料表。

    ![ 精靈的 [選取 IP 位址] 頁面](../../relational-databases/tables/media/stretch-wizard-7.png "Stretch Database 精靈的 [選取 IP 位址] 頁面")
6. 當精靈完成時，請確認資料庫是否成功啟用延展。 請注意，物件總管中的圖示指出資料庫已延展。

> [!NOTE]
> 如果 [啟用資料庫的延展] 失敗，請檢閱錯誤記錄檔。 常見錯誤是防火牆規則的設定不正確。

另請參閱：

- [為資料庫啟用 Stretch Database](../../sql-server/stretch-database/enable-stretch-database-for-a-database.md)
- [開始執行啟用資料庫的延展功能精靈](../../sql-server/stretch-database/get-started-by-running-the-enable-database-for-stretch-wizard.md)
- [為資料表啟用 Stretch Database](../../sql-server/stretch-database/enable-stretch-database-for-a-table.md)

### <a name="using-transact-sql-to-stretch-the-entire-history-table"></a>使用 Transact-SQL 來延展整個歷程記錄資料表

您也可以使用 Transact-SQL 在本機伺服器上啟用 Stretch 以及 [為資料庫啟用 Stretch Database](../../sql-server/stretch-database/enable-stretch-database-for-a-database.md)。 接著，您可以[使用 Transact-SQL 在資料表上啟用 Stretch Database](../../sql-server/stretch-database/enable-stretch-database-for-a-table.md)。 對於先前已啟用 Stretch Database 的資料庫，執行下列 Transact-SQL 指令碼可延伸現有的系統設定版本時態歷程記錄資料表︰

```sql
ALTER TABLE <history table name>
SET (REMOTE_DATA_ARCHIVE = ON (MIGRATION_STATE = OUTBOUND));
```

### <a name="using-transact-sql-to-stretch-a-portion-of-the-history-table"></a>使用 Transact-SQL 來延展一部分的歷程記錄資料表

若只要延展一部分的歷程記錄資料表，您可以先從建立 [內嵌述詞函數](../../sql-server/stretch-database/select-rows-to-migrate-by-using-a-filter-function-stretch-database.md)開始。 此範例中，我們假設您在 2015 年 12 月 1 日首次設定內嵌述詞函式，並想要將歷程記錄日期比 2015 年 11 月 1 日早的所有內容延伸到 Azure。 若要達成此目的，請從建立下列函數開始︰

```sql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20151101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2015-11-01T00:00:00', 101) ;
```

接下來，使用下列指令碼將篩選器述詞新增至歷程記錄資料表，並將移轉狀態設定為 OUTBOUND，以便為歷程記錄資料表啟用述詞型的資料移轉。

```sql
ALTER TABLE <history table name>
SET (
      REMOTE_DATA_ARCHIVE = ON
        (
          FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20151101 (SysEndTime)
            , MIGRATION_STATE = OUTBOUND
        )
    )
;
```

若要維護滑動視窗，您每天都需要讓述詞函式精確 (也就是每天將篩選資料列條件變更一天)。 下列指令碼是您需要在 2015 年 12 月 2 日 執行的指令碼︰

```sql
BEGIN TRAN
/*(1) Create new predicate function definition */
  CREATE FUNCTION dbo.fn_StretchBySystemEndTime20151102(@systemEndTime datetime2)
   RETURNS TABLE
    WITH SCHEMABINDING
      AS
        RETURN SELECT 1 AS is_eligible
          WHERE @systemEndTime < CONVERT(datetime2,'2015-11-02T00:00:00', 101)
  GO
 
/*(2) Set the new function as filter predicate */
  ALTER TABLE <history table name>
    SET
      (
        REMOTE_DATA_ARCHIVE = ON
          (
            FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20151102(SysEndTime),
              MIGRATION_STATE = OUTBOUND
          )
      )
COMMIT ;
```

使用 SQL Server Agent 或其他某些排程機制來確保述詞函數定義總是有效。

## <a name="using-table-partitioning-approach"></a>使用資料表資料分割方法

[資料表資料分割](../partitions/create-partitioned-tables-and-indexes.md) 可讓大型資料表更容易管理及擴充。 透過資料表資料分割方法，您可以使用記錄資料表資料分割來實作以時間為基礎的自訂資料清除或離線封存。 在使用資料分割刪除查詢時態表中的資料歷程記錄子集時，資料表資料分割也能帶來效能上的效益。

資料表資料分割可讓您實作滑動視窗方法，將歷程記錄資料最舊的部分移出歷程記錄資料表，並依照存在時間讓保留部分的大小維持不變 - 讓歷程記錄資料表中的資料等於需要的保留期限。 當 SYSTEM_VERSIONING 為 ON 時，系統支援將資料切換移出歷程記錄資料表的作業，這表示您可以在不採用維護期間或不阻礙正常工作負載的情況下清除一部分的歷程記錄資料。

> [!NOTE]
> 若要執行資料分割切換，歷程記錄資料表上的叢集索引必須與資料分割結構描述對齊 (它必須含有 SysEndTime)。 由系統建立的預設歷程記錄資料表含有叢集索引，其中包含 SysEndTime 和 SysStartTime 資料行，最適合進行資料分割、插入新歷程記錄資料及一般時態查詢。 如需相關資訊，請參閱 [Temporal Tables](../../relational-databases/tables/temporal-tables.md)。

採用滑動視窗方法時，您需要執行兩組工作︰

- 資料分割組態工作
- 週期性資料分割維護工作

為了方便解說，假設我們想要保留歷程記錄資料 6 個月，且我們想要將每個月的資料保存在不同分割區。 此外，假設我們在 2015 年 9 月啟動了系統版本設定功能。

資料分割組態工作會建立歷程記錄資料表的初始資料分割組態。 此範例中，我們可以建立與滑動視窗大小相同數目的資料分割 (以月份為單位)，再加上一個預先準備的額外空資料分割 (如下所述)。 這個設定可確保系統能在我們首次啟動週期性資料分割維護工作時正確地儲存新資料，而且能確保我們永遠不會分割資料分割，以避免昂貴的資料移動。 您應該使用 Transact-SQL 依照以下範例指令碼來執行這項工作。

下圖顯示保存 6 個月資料的初始資料分割組態。

![顯示保存六個月資料的初始資料分割組態圖表。](../../relational-databases/tables/media/partitioning.png "資料分割")

> [!NOTE]
> 如需在設定資料分割時使用 RANGE LEFT 與 RANGE RIGHT 的效能含意，請參閱下文中的資料表資料分割效能考量。

第一個和最後一個資料分割的上限和下限分別保持「開啟」，以確保不論分割資料行中的值為何，每個新資料列都有目的地資料分割。 隨著時間流逝，歷程記錄資料表中的新資料列將落在較高的資料分割。 當第 6 個資料分割填滿時，我們將達到預計的保留期限。 這是首次啟動週期性資料分割維護工作的時候 (您需要將它排程為定期執行，在本範例中為每月一次)。

下圖說明週期性資料分割維護工作 (請參閱下文中的詳細步驟)。

![顯示週期性資料分割維護工作的圖表。](../../relational-databases/tables/media/partitioning2.png "資料分割 2")

週期性資料分割維護工作的詳細步驟如下︰

1. 切換移出：建立暫存表格，然後使用 [ALTER TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-table-transact-sql.md) 陳述式並搭配 SWITCH PARTITION 引數，在記錄資料表和暫存資料表之間切換分割區 (請參閱範例 C 在資料表之間切換分割區)。

    ```sql
    ALTER TABLE <history table> SWITCH PARTITION 1 TO <staging table>
    ```

    切換資料分割之後，您可以選擇性地封存暫存資料表中的資料，然後丟棄或截斷暫存資料表以做好下次執行此週期性資料分割維護工作的準備。
2. MERGE RANGE：使用 [ALTER PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-partition-function-transact-sql.md) 並搭配 MERGE RANGE 來合併空的分割區 1 和分割區 2 (請參閱範例 B)。 藉由使用這個函數移除最低界限，您可以有效地合併空的資料分割 1 和先前的資料分割 2，組成新資料分割 1。 其他資料分割也可以有效地變更其序數。
3. SPLIT RANGE：使用 [ALTER PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-partition-function-transact-sql.md) 並搭配 SPLIT RANGE 來建立新的空白分割區 7 (請參閱範例 A)。 藉由使用這個函數加入新的上限，您可以有效地為下個月份建立個別資料分割。

### <a name="use-transact-sql-to-create-partitions-on-history-table"></a>使用 Transact-SQL 在歷程記錄資料表上建立資料分割

使用下列程式碼視窗中的 Transact-SQL 指令碼來建立資料分割函數、資料分割結構描述，以及重新建立與資料分割結構描述和資料分割對齊的叢集索引。 在此範例中，我們將利用從 2015 年 9 月開始的每月資料分割建立為期六個月的滑動視窗方法。

```sql
BEGIN TRANSACTION
/*Create partition function*/
    CREATE PARTITION FUNCTION [fn_Partition_DepartmentHistory_By_SysEndTime] (datetime2(7))
        AS RANGE LEFT FOR VALUES
          (
            N'2015-09-30T23:59:59.999'
          , N'2015-10-31T23:59:59.999'
          , N'2015-11-30T23:59:59.999'
          , N'2015-12-31T23:59:59.999'
          , N'2016-01-31T23:59:59.999'
          , N'2016-02-29T23:59:59.999'
          )
/*Create partition scheme*/
    CREATE PARTITION SCHEME [sch_Partition_DepartmentHistory_By_SysEndTime]
        AS PARTITION [fn_Partition_DepartmentHistory_By_SysEndTime]
            TO ([PRIMARY], [PRIMARY], [PRIMARY], [PRIMARY], [PRIMARY], [PRIMARY], [PRIMARY])
/*Re-create index to be partition-aligned with the partitioning schema*/
    CREATE CLUSTERED INDEX [ix_DepartmentHistory] ON [dbo].[DepartmentHistory]
        (
            [SysEndTime] ASC
          , [SysStartTime] ASC
        )  
    WITH
        (
            PAD_INDEX = OFF
          , STATISTICS_NORECOMPUTE = OFF
          , SORT_IN_TEMPDB = OFF
          , DROP_EXISTING = ON
          , ONLINE = OFF
          , ALLOW_ROW_LOCKS = ON
          , ALLOW_PAGE_LOCKS = ON
          , DATA_COMPRESSION = PAGE
        )
    ON [sch_Partition_DepartmentHistory_By_SysEndTime] ([SysEndTime])

COMMIT TRANSACTION;
```

### <a name="using-transact-sql-to-maintain-partitions-in-sliding-window-scenario"></a>使用 Transact-SQL 來維護滑動視窗案例中的資料分割

使用下列程式碼視窗中的 Transact-SQL 指令碼來維護滑動視窗案例中的資料分割。 在此案例中，我們將使用 MERGE RANGE 切換移出 2015 年 9 月的資料分割，再使用 SPLIT RANGE 加入 2016 年 3 月的新資料分割。

```sql
BEGIN TRANSACTION
/*(1) Create staging table */
    CREATE TABLE [dbo].[staging_DepartmentHistory_September_2015]
        (
            [DeptID] [int] NOT NULL
          , [DeptName] [varchar](50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
          , [ManagerID] [int] NULL
          , [ParentDeptID] [int] NULL
          , [SysStartTime] [datetime2](7) NOT NULL
          , [SysEndTime] [datetime2](7) NOT NULL
        ) ON [PRIMARY]
    WITH
        (
            DATA_COMPRESSION = PAGE
        )
/*(2) Create index on the same filegroups as the partition that will be switched out*/
    CREATE CLUSTERED INDEX [ix_staging_DepartmentHistory_September_2015]
        ON [dbo].[staging_DepartmentHistory_September_2015]
        (
            [SysEndTime] ASC
          , [SysStartTime] ASC
        )
    WITH
        (
            PAD_INDEX = OFF
          , SORT_IN_TEMPDB = OFF
          , DROP_EXISTING = OFF
          , ONLINE = OFF
          , ALLOW_ROW_LOCKS = ON
          , ALLOW_PAGE_LOCKS = ON
        )
    ON [PRIMARY]
/*(3) Create constraints matching the partition that will be switched out*/
    ALTER TABLE [dbo].[staging_DepartmentHistory_September_2015] WITH CHECK
        ADD CONSTRAINT [chk_staging_DepartmentHistory_September_2015_partition_1]
            CHECK ([SysEndTime]<=N'2015-09-30T23:59:59.999')
    ALTER TABLE [dbo].[staging_DepartmentHistory_September_2015]
        CHECK CONSTRAINT [chk_staging_DepartmentHistory_September_2015_partition_1]
/*(4) Switch partition to staging table*/
    ALTER TABLE [dbo].[DepartmentHistory]
        SWITCH PARTITION 1 TO [dbo].[staging_DepartmentHistory_September_2015]
        WITH (WAIT_AT_LOW_PRIORITY (MAX_DURATION = 0 MINUTES, ABORT_AFTER_WAIT = NONE))
/*(5) [Commented out] Optionally archive the data and drop staging table
      INSERT INTO [ArchiveDB].[dbo].[DepartmentHistory]
      SELECT * FROM [dbo].[staging_DepartmentHistory_September_2015];
      DROP TABLE [dbo].[staging_DepartmentHIstory_September_2015];
*/
/*(6) merge range to move lower boundary one month ahead*/
    ALTER PARTITION FUNCTION [fn_Partition_DepartmentHistory_By_SysEndTime]()
        MERGE RANGE(N'2015-09-30T23:59:59.999')
/*(7) Create new empty partition for "April and after" by creating new boundary point and specifying NEXT USED file group*/
    ALTER PARTITION SCHEME [sch_Partition_DepartmentHistory_By_SysEndTime] NEXT USED [PRIMARY]
        ALTER PARTITION FUNCTION [fn_Partition_DepartmentHistory_By_SysEndTime]() SPLIT RANGE(N'2016-03-31T23:59:59.999')
COMMIT TRANSACTION
```

您可以稍微修改上述指令碼，並運用在一般的每月維護程序中︰

1. 在步驟 (1) 中，針對要移除的月份建立新的暫存資料表 (範例中的下一個月份是 10 月)。
2. 在步驟 (3) 中，建立及檢查符合要移除之資料月份的條件約束︰`[SysEndTime]<=N'2015-10-31T23:59:59.999'` (適用於 10 月資料分割)。
3. 在步驟 (4) 中，將資料分割 1 切換為新建立的暫存資料表。
4. 在步驟 (6) 中，藉由合併下限來改變資料分割函式︰`MERGE RANGE(N'2015-10-31T23:59:59.999'` (在移出 10 月的資料之後)。
5. 在步驟 (7) 中，藉由建立新的上限來分割資料分割函數︰移出 10 月的資料之後， `SPLIT RANGE (N'2016-04-30T23:59:59.999'` 。

然而，最好的方案是定期執行不需要修改指令碼即可在每個月執行適當動作的一般性 Transact-SQL 指令碼。 您可以將上述指令碼一般化，以根據提供的參數採取行動 (需要合併的下限和使用資料分割分割建立的新界限)。 為了避免在每個月建立暫存資料表，您可以將檢查條件約束變更為比對要切換移出的資料分割，藉此預先建立暫存資料表並重複使用。請參閱下列頁面以了解如何使用 Transact-SQL 指令碼將 [滑動視窗全面自動化](/previous-versions/sql/sql-server-2005/administrator/aa964122(v=sql.90)) 的概念。

### <a name="performance-considerations-with-table-partitioning"></a>資料表資料分割效能考量

執行 MERGE RANGE 和 SPLIT RANGE 作業來避免任何資料移動是必要的，因為資料移動可能會造成繁重的效能負荷。 如需詳細資訊，請參閱[修改資料分割函數](../../relational-databases/partitions/modify-a-partition-function.md)。在 [CREATE PARTITION FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-partition-function-transact-sql.md) 時，您可以使用 RANGE LEFT (而非 RANGE RIGHT) 來達成目的。

讓我們先以視覺化方式說明 RANGE LEFT 和 RANGE RIGHT 選項的意義：

![顯示 RANGE LEFT 與 RANGE RIGHT 選項的圖表。](../../relational-databases/tables/media/partitioning3.png "資料分割 3")

當您將資料分割函數定義為 RANGE LEFT 時，指定的值是資料分割的上限。 當您使用 RANGE RIGHT 時，指定的值是資料分割的下限。 當您使用 MERGE RANGE 作業來移除資料分割函數定義中的界限時，基礎實作也會移除包含界限的資料分割。 如果該資料分割不是空的，系統會將資料移動到 MERGE RANGE 作業產生的資料分割。

在滑動視窗案例中，我們一律會移除最低的資料分割界限。

- RANGE LEFT 案例：在 RANGE LEFT 案例中，最低的分割區界限屬於分割區 1，由於該分割區是空的 (在分割區切換移出後)，所以 MERGE RANGE 不會引發任何資料移動。
- RANGE RIGHT 案例：在 RANGE RIGHT 案例中，最低的分割區界限屬於分割區 2，由於我們假設切換移出已將分割區 1 清空，因此分割區 2 不是空的。在該案例中，MERGE RANGE 將會引發資料移動 (資料分割 2 的資料將移動到資料分割 1)。 若要避免資料移動，滑動視窗案例中的 RANGE RIGHT 需要隨時保持清空的資料分割 1。 相較於 RANGE LEFT 案例，這表示如果我們使用 RANGE RIGHT，應該要建立及維護一個額外的資料分割，。

**結論**：在滑動資料分割中使用 RANGE LEFT 的管理作業較為簡單，也能避免資料移動。 不過，定義 RANGE RIGHT 的資料分割界限則稍微簡單一些，因為您不需要應付日期時間的對時問題。

## <a name="using-custom-cleanup-script-approach"></a>使用自訂清除指令碼方法

當 Stretch Database 和資料表資料分割方法均不可行時，第三種方法是使用自訂清除指令碼刪除記錄資料表中的資料。 唯有當 **SYSTEM_VERSIONING = OFF** 時，您才可以刪除歷程記錄資料表中的資料。 為了避免資料不一致，請在維護期間 (當修改資料的工作負載未作用時) 或交易內 (有效地封鎖其他工作負載) 執行清除作業。 這項作業需要目前和歷程記錄資料表的 **CONTROL** 權限。

為了盡可能避免阻礙一般應用程式和使用者查詢，於交易內執行清除指令碼時，請在設定延遲的情況下以較小的區塊刪除資料。 對於每個要刪除的資料區塊來說，雖然所有案例都沒有最合適的大小，不過在單一交易中刪除 10,000 個以上的資料列就可能會造成顯著影響。

所有時態表的清除邏輯都一樣，因此只要透過為每個要限制資料歷程記錄之時態表排程為定期執行的一般預存程序，您就可以利用相對簡單的方式將其自動化。

下圖說明如何安排單一資料表的清除邏輯，以減少對執行中工作負載的影響。

![顯示如何安排單一資料表的清除邏輯，以減少對執行中工作負載影響的圖表。](../../relational-databases/tables/media/customcleanupscriptdiagram.png "自訂清除指令碼圖表")

以下是一些實作程序的高階指導方針。 將清除邏輯排程為每天執行，並且逐一處理所有需要清除資料的時態表。 使用 SQL Server Agent 或其他工具來排程這個程序：

- 如上圖所示，以小區塊為單位，按照最舊資料列到最新資料列的順序，利用多次反覆運算的方式刪除每個時態表中的歷程記錄資料，避免在單一交易中刪除所有資料列。
- 將每個反覆運算視為叫用移除歷程記錄資料表部分資料的一般預存程序實作 (如需此程序，請參閱下文中的程式碼範例)。
- 在每次叫用程序時，計算需要刪除單一時態表中多少資料列。 根據前述結果和需要的反覆運算數目，判斷每次程序叫用的動態分割點。
- 在單一資料表的反覆運算之間規劃一段延遲期間，以減少對存取時態表之應用程式的影響。

對於刪除單一時態表之資料的預存程序，其內容可能與以下程式碼片段相似 (在環境中套用之前，請仔細檢閱以下程式碼並加以調整)：

```sql
DROP PROCEDURE IF EXISTS sp_CleanupHistoryData;
GO

CREATE PROCEDURE sp_CleanupHistoryData
        @temporalTableSchema sysname
      , @temporalTableName sysname
      , @cleanupOlderThanDate datetime2
AS
    DECLARE @disableVersioningScript nvarchar(max) = '';
    DECLARE @deleteHistoryDataScript nvarchar(max) = '';
    DECLARE @enableVersioningScript nvarchar(max) = '';

DECLARE @historyTableName sysname
DECLARE @historyTableSchema sysname
DECLARE @periodColumnName sysname

/*Generate script to discover history table name and end of period column for given temporal table name*/
EXECUTE sp_executesql
    N'SELECT @hst_tbl_nm = t2.name, @hst_sch_nm = s.name, @period_col_nm = c.name
        FROM sys.tables t1
            JOIN sys.tables t2 on t1.history_table_id = t2.object_id
        JOIN sys.schemas s on t2.schema_id = s.schema_id
            JOIN sys.periods p on p.object_id = t1.object_id
           JOIN sys.columns c on p.end_column_id = c.column_id and c.object_id = t1.object_id
                  WHERE
                 t1.name = @tblName and s.name = @schName'
                , N'@tblName sysname
                , @schName sysname
                , @hst_tbl_nm sysname OUTPUT
                , @hst_sch_nm sysname OUTPUT
                , @period_col_nm sysname OUTPUT'
                , @tblName = @temporalTableName
                , @schName = @temporalTableSchema
                , @hst_tbl_nm = @historyTableName OUTPUT
                , @hst_sch_nm = @historyTableSchema OUTPUT
                , @period_col_nm = @periodColumnName OUTPUT
  
IF @historyTableName IS NULL OR @historyTableSchema IS NULL OR @periodColumnName IS NULL
    THROW 50010, 'History table cannot be found. Either specified table is not system-versioned temporal or you have provided incorrect argument values.', 1

/*Generate 3 statements that will run inside a transaction:
  (1) SET SYSTEM_VERSIONING = OFF,
  (2) DELETE FROM history_table,
  (3) SET SYSTEM_VERSIONING = ON
  On SQL Server 2016, it is critical that (1) and (2) run in separate EXEC statements, or SQL Server will generate the following error:
  Msg 13560, Level 16, State 1, Line XXX
  Cannot delete rows from a temporal history table '<database_name>.<history_table_schema_name>.<history_table_name>'.
*/
SET @disableVersioningScript = @disableVersioningScript + 'ALTER TABLE [' + @temporalTableSchema + '].[' + @temporalTableName + '] SET (SYSTEM_VERSIONING = OFF)'
SET @deleteHistoryDataScript = @deleteHistoryDataScript + ' DELETE FROM [' + @historyTableSchema + '].[' + @historyTableName + ']
    WHERE ['+ @periodColumnName + '] < ' + '''' + convert(varchar(128), @cleanupOlderThanDate, 126) + ''''
SET @enableVersioningScript = @enableVersioningScript + ' ALTER TABLE [' + @temporalTableSchema + '].[' + @temporalTableName + ']
    SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = [' + @historyTableSchema + '].[' + @historyTableName + '], DATA_CONSISTENCY_CHECK = OFF )); '

BEGIN TRAN
    EXEC (@disableVersioningScript);
    EXEC (@deleteHistoryDataScript);
    EXEC (@enableVersioningScript);
COMMIT;
```

## <a name="using-temporal-history-retention-policy-approach"></a>使用時態歷程記錄保留原則方法

> [!NOTE]
> 使用時態歷程記錄保留原則適用於 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] 和 SQL Server 2017 (從 CTP 1.3 開始)。

時態歷程記錄保留可在不同的資料表層級上設定，讓使用者建立彈性的過時原則。 套用暫時保留很簡單：只需要在資料表建立或結構描述變更期間設定一個參數。

定義保留原則之後，Azure SQL Database 會開始定期檢查是否有可自動清除資料的合格歷程記錄資料列。 識別相符資料列及從歷程記錄資料表中移除它們的作業，會以明確的方式在系統排程和執行的背景工作中進行。 會根據代表 SYSTEM_TIME 期間結束的資料行，檢查歷程記錄資料表資料列的存留期條件。 如果保留期限 (例如，設定為六個月) 資料表資料列符合清除資格，請滿足下列條件：

```sql
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```
在上述範例中，我們假設 ValidTo 資料行對應於 SYSTEM_TIME 期間的結束時間。

### <a name="how-to-configure-retention-policy"></a>如何設定保留原則？

設定時態資料表的保留原則之前，請先檢查是否已在資料庫層級啟用時態歷程記錄保留：

```sql
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

根據預設，資料庫旗標 **is_temporal_history_retention_enabled** 設為 ON，但使用者可利用 ALTER DATABASE 陳述式來變更它。 它也會在時間點還原作業之後自動設為 OFF。 若要為您的資料庫啟用時態歷程記錄保留清除功能，請執行下列陳述式：

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION ON
```

藉由指定 HISTORY_RETENTION_PERIOD 參數值，在資料表建立期間設定保留原則：

```sql
CREATE TABLE dbo.WebsiteUserInfo
(
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )
 WITH
 (
    SYSTEM_VERSIONING = ON
    (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
    )
 );
```

您可以使用不同的時間單位來指定保留期間：DAYS、WEEKS、MONTHS 和 YEARS。 如果省略了 HISTORY_RETENTION_PERIOD，則會假設 INFINITE 保留。 您也可以明確地使用 INFINITE 關鍵字。
在某些狀況中，您可能想要在資料表建立後設定保留，或變更先前設定的值。 在此情況下，請使用 ALTER TABLE 陳述式：

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

若要檢閱目前的保留原則狀態，請使用下列查詢，以聯結資料庫層級的暫時保留啟用旗標與個別資料表的保留期間：

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName, SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```

### <a name="how-sql-database-deletes-aged-rows"></a>SQL Database 如何刪除過時資料列？

清除處理序取決於歷程記錄資料表的索引配置。 請務必注意 *只有包含叢集索引 (B 型樹狀目錄或資料行存放區）的歷程記錄資料表可以設定有限的保留原則*。 建立的背景工作可利用有限的保留期間為所有時態表執行過時資料清除。 資料列存放區 (B 型樹狀目錄) 叢集索引的清除邏輯會刪除較小區塊 (最多 10 K) 中的過時資料列，最大限度地減輕資料庫記錄和 I/O 子系統的壓力。 雖然清除邏輯會利用必要的 B 型樹狀目錄索引，但無法絶對保證早於保留期間之資料列的刪除順序。 因此，*請勿在應用程式中對清除順序採用任何相依性*。

叢集資料行存放區清除工作可一次移除整個資料列群組 (每個通常包含 1 百萬個資料列)，這是非常有效率的方法，特別是在高速產生歷程記錄資料時。

![叢集資料行存放區保留](../../relational-databases/tables/media/cciretention.png "叢集資料行存放區保留")

卓越的資料壓縮和有效率的保留清除，可讓叢集資料行存放區索引成為您的工作負載快速產生大量歷程記錄資料時的完美選擇。 此模式一般適用於使用時態表進行變更追蹤和稽核、趨勢分析或 IoT 資料擷取的大量交易處理工作負載。

如需更多詳細資料，請參閱[使用保留原則管理時態資料表中的歷程記錄資料](/azure/sql-database/sql-database-temporal-tables-retention-policy)。

## <a name="next-steps"></a>後續步驟

- [時態表](../../relational-databases/tables/temporal-tables.md)
- [開始使用系統建立版本的時態表](../../relational-databases/tables/getting-started-with-system-versioned-temporal-tables.md)
- [時態表系統一致性檢查](../../relational-databases/tables/temporal-table-system-consistency-checks.md)
- [對時態表進行資料分割](../../relational-databases/tables/partitioning-with-temporal-tables.md)
- [時態表考量與限制](../../relational-databases/tables/temporal-table-considerations-and-limitations.md)
- [時態表安全性](../../relational-databases/tables/temporal-table-security.md)
- [系統版本設定時態表與記憶體最佳化資料表](../../relational-databases/tables/system-versioned-temporal-tables-with-memory-optimized-tables.md)
- [暫存資料表中繼資料檢視和函數](../../relational-databases/tables/temporal-table-metadata-views-and-functions.md)