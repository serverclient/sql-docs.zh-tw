---
title: 使用 SQL Server Management Studio (SSMS) 以連線及查詢 SQL Server 執行個體
description: 在 SSMS 中連線到 SQL Server 執行個體。 在執行基本 T-SQL 查詢的 SSMS 中，建立並查詢 SQL Server 資料庫。
ms.prod: sql
ms.technology: ssms
ms.topic: quickstart
author: markingmyname
ms.author: maghan
ms.reviewer: sstein, mikeray
ms.custom: contperf-fy21q2
ms.date: 12/15/2020
ms.openlocfilehash: 519b60f63da38192e2196014e0ea7820dafd5491
ms.sourcegitcommit: 8a8c89b0ff6d6dfb8554b92187aca1bf0f8bcc07
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97619032"
---
# <a name="quickstart-connect-and-query-a-sql-server-instance-using-sql-server-management-studio-ssms"></a>快速入門：使用 SQL Server Management Studio (SSMS) 以連線及查詢 SQL Server 執行個體

[!INCLUDE [sqlserver](../../includes/applies-to-version/sqlserver.md)]

開始使用 SQL Server Management Studio (SSMS) 連線到您的 SQL Server 執行個體，並執行一些 Transact-SQL (T-SQL) 命令。

此文章示範如何遵循下列步驟：

> [!div class="checklist"]
> - 連接到 SQL Server 執行個體
> - 建立資料庫
> - 在新的資料庫中建立資料表
> - 在新的資料表內插入資料列
> - 查詢新的資料表並檢視結果
> - 使用查詢視窗資料表來驗證您的連線屬性

## <a name="prerequisites"></a>必要條件

- 已安裝 [SQL Server Management Studio](../download-sql-server-management-studio-ssms.md)。
- 已安裝及設定 [SQL Server 執行個體](https://www.microsoft.com/sql-server/sql-server-downloads)。

## <a name="connect-to-a-sql-server-instance"></a>連接到 SQL Server 執行個體

1. 啟動 SQL Server Management Studio。 首次執行 SSMS 時，會開啟 [連線至伺服器] 視窗。 若該視窗未開啟，您可透過選取 [物件總管] > [連線] > [資料庫引擎] 手動加以開啟。

    :::image type="content" source="media/ssms-connect-query-sql-server/connect-object-explorer.png" alt-text="[物件總管] 中的 [連線] 連結":::

2. [連線到伺服器] 對話方塊隨即出現。 輸入以下資訊：

    |   設定   |   建議的值   |   描述   |
    |--------------|-----------------------|-----------------|
    | **伺服器類型** | 資料庫引擎 | 針對 **伺服器類型**，選取 [資料庫引擎] \(通常為預設選項)。 |
    | **伺服器名稱** | 完整伺服器名稱 | 針對 [伺服器名稱]，輸入 SQL Server 的名稱 (如果您是在本機連線，也可以使用 *localhost* 作為伺服器名稱)。 如果您不是使用預設執行個體 (***MSSQLSERVER** _)，則必須輸入伺服器名稱和執行個體名稱。 </br> </br> 若您不確定如何判斷 SQL Server 執行個體名稱，請參閱[使用 SSMS 的其他提示與祕訣](../tutorials/ssms-tricks.md#find-sql-server-instance-name)。 |
    | _ *驗證** | Windows 驗證 </br> </br> SQL Server 驗證 | Windows 驗證會設為預設值。 </br> </br> 您也可以使用 [SQL Server 驗證] 進行連線。 不過，如果您選取 [SQL Server 驗證]，則需要使用者名稱和密碼。 </br> </br> 如需驗證類型的詳細資訊，請參閱[連線至伺服器 (資料庫引擎)](../f1-help/connect-to-server-database-engine.md)。 |
    | **登入** | 伺服器帳戶使用者識別碼 | 登入伺服器時所使用伺服器帳戶的使用者識別碼。 使用 [SQL Server 驗證] 時，需要登入。 |
    | **密碼** | 伺服器帳戶密碼 | 登入伺服器時所使用伺服器帳戶的密碼。 使用 [SQL Server 驗證] 時，需要密碼。 |

    :::image type="content" source="media/ssms-connect-query-sql-server/connect-to-sql-server-object-explorer.png" alt-text="SQL Server 的 [伺服器名稱] 欄位":::

3. 當您填完所有欄位後，請選取 [連線]。

    您也可透過選取 [選項] 修改其他連線設定。 連線選項的範例為您所連線的資料庫、連線逾時值以及網路通訊協定。 本文會為所有欄位使用預設值。

4. 若要確認您的 SQL Server 連線成功，請展開並瀏覽 [物件總管] 中的物件，其中會顯示伺服器名稱、SQL Server 版本及使用者名稱。 這些物件會根據伺服器類型而有所不同。

    :::image type="content" source="media/ssms-connect-query-sql-server/connect-on-prem.png" alt-text="連線到內部部署伺服器":::

## <a name="troubleshoot-connectivity-issues"></a>疑難排解連線問題

若要檢閱當您無法連線到單一伺服器的 SQL Server 資料庫引擎執行個體時所使用的疑難排解技術，請前往[針對 SQL Server 資料庫引擎的連線進行疑難排解](../../database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine.md)。

## <a name="create-a-database"></a>建立資料庫

現在，讓我們遵循下列步驟，建立名為 TutorialDB 的資料庫：

1. 在物件總管中以滑鼠右鍵按一下您的伺服器執行個體，然後選取 [新增查詢]：

   :::image type="content" source="media/ssms-connect-query-sql-server/new-query.png" alt-text="新增查詢連結":::

2. 將下列 T-SQL 程式碼貼入查詢視窗中：

    ```sql
    USE master
    GO
    IF NOT EXISTS (
       SELECT name
       FROM sys.databases
       WHERE name = N'TutorialDB'
    )
    CREATE DATABASE [TutorialDB]
    GO
   ```

3. 選取 [執行] 或在鍵盤上選取 F5 執行查詢。

   :::image type="content" source="media/ssms-connect-query-sql-server/execute.png" alt-text="執行命令":::
  
    查詢完成後，新的 TutorialDB 資料庫會顯示在物件總管的資料庫清單中。 若其未顯示，請以滑鼠右鍵按一下 [資料庫] 節點，然後選取 [重新整理]。

## <a name="create-a-table-in-the-new-database"></a>在新的資料庫中建立資料表

在本節中，您會在新建立的 TutorialDB 資料庫中建立資料表。 因為查詢編輯器仍在 *master* 資料庫的內容中，請執行下列步驟將連線內容切換至 *TutorialDB* 資料庫：

1. 在資料庫下拉式清單中，選取您想要的資料庫，如此處所示：

   :::image type="content" source="media/ssms-connect-query-sql-server/change-db.png" alt-text="變更資料庫":::

2. 將下列 T-SQL 程式碼貼入查詢視窗中：

    ```sql
    USE [TutorialDB]
    -- Create a new table called 'Customers' in schema 'dbo'
    -- Drop the table if it already exists
    IF OBJECT_ID('dbo.Customers', 'U') IS NOT NULL
    DROP TABLE dbo.Customers
    GO
    -- Create the table in the specified schema
    CREATE TABLE dbo.Customers
    (
       CustomerId        INT    NOT NULL   PRIMARY KEY, -- primary key column
       Name      [NVARCHAR](50)  NOT NULL,
       Location  [NVARCHAR](50)  NOT NULL,
       Email     [NVARCHAR](50)  NOT NULL
    );
    GO
    ```

3. 選取 [執行] 或在鍵盤上選取 F5 執行查詢。

查詢完成後，新的 [客戶] 資料表會顯示在物件總管的資料表清單中。 若資料表未顯示，請在物件總管中以滑鼠右鍵按一下 **TutorialDB** > **Tables** 節點，然後選取 [重新整理]。

   :::image type="content" source="media/ssms-connect-query-sql-server/new-table.png" alt-text="新增資料表":::

## <a name="insert-rows-into-the-new-table"></a>在新的資料表插入資料列

現在，讓我們在您建立的 Customers 資料表插入一些資料列。 在查詢視窗中貼上以下 T-SQL 程式碼片段，然後選取 [執行]：

   ```sql
   -- Insert rows into table 'Customers'
   INSERT INTO dbo.Customers
      ([CustomerId],[Name],[Location],[Email])
   VALUES
      ( 1, N'Orlando', N'Australia', N''),
      ( 2, N'Keith', N'India', N'keith0@adventure-works.com'),
      ( 3, N'Donna', N'Germany', N'donna0@adventure-works.com'),
      ( 4, N'Janet', N'United States', N'janet1@adventure-works.com')
   GO
   ```

## <a name="query-the-table-and-view-the-results"></a>查詢資料表並檢視結果

查詢結果會顯示在查詢文字視窗下方。 若要查詢 Customers 資料表並檢視已插入的資料列，請遵循下列步驟：

1. 在查詢視窗中貼上以下 T-SQL 程式碼片段，然後選取 [執行]：

   ```sql
   -- Select rows from table 'Customers'
   SELECT * FROM dbo.Customers;
   ```

    查詢結果會顯示在輸入文字的區域下。

   :::image type="content" source="media/ssms-connect-query-sql-server/query-results.png" alt-text="結果清單":::

    您也可以透過選取下列其中一個選項修改結果的呈現方式：

   ![三個顯示查詢結果的選項](media/ssms-connect-query-sql-server/results.png)

   - 第一個按鈕會在 [文字檢視] 中顯示結果，如下一節中的影像所示。
   - 中間的按鈕會在 [格線檢視] 中顯示結果，這是預設選項。
       - 這會設定為預設值
   - 第三個按鈕可讓您將結果儲存至檔案，其副檔名預設為 .rpt。

## <a name="verify-your-connection-properties-by-using-the-query-window-table"></a>您可透過使用查詢視窗資料表來驗證連線屬性

您可以在查詢結果下找到連線屬性的相關資訊。 當您執行在上一個步驟提到的查詢後，請檢閱查詢視窗底部的連線屬性。

- 您可以判斷您連線至哪一部伺服器與哪個資料庫，以及您使用的使用者名稱。
- 您也可以檢視上一個執行之查詢所傳回的查詢持續時間與資料列數。

   :::image type="content" source="media/ssms-connect-query-sql-server/connection-properties.png" alt-text="連線內容":::

## <a name="additional-tools"></a>其他工具

您也可以使用 [Azure Data Studio](../../azure-data-studio/download-azure-data-studio.md) 以連線及查詢 [SQL Server](../../azure-data-studio/quickstart-sql-server.md) ([Azure SQL Database](../../azure-data-studio/quickstart-sql-database.md)) 和 [Azure Synapse Analytics](../../azure-data-studio/quickstart-sql-dw.md)。

## <a name="next-steps"></a>後續步驟

熟悉 SSMS 的最佳方式是實際練習。 這些文章可協助您使用 SSMS 內所提供的各種功能。

- [SQL Server Management Studio (SSMS) 查詢編輯器](../f1-help/database-engine-query-editor-sql-server-management-studio.md)
- [指令碼](../tutorials/scripting-ssms.md)
- [在 SSMS 中使用範本](../template/templates-ssms.md)
- [SSMS 組態](../tutorials/ssms-configuration.md)
- [使用 SSMS 的其他提示與訣竅](../tutorials/ssms-tricks.md)