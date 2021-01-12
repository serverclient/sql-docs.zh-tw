---
title: 使用 Azure Synapse Analytics 進行連線及查詢
description: 本快速入門示範如何使用 Azure Data Studio 連線至 Azure Synapse Analytics 中的專用 SQL 集區。
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.topic: quickstart
author: yualan
ms.author: alayu
ms.reviewer: maghan, jrasnick
ms.custom: seodec18; seo-lt-2019
ms.date: 10/15/2020
ms.openlocfilehash: 8215e84c4c61935b3f5831ca771e2868e27be428
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091721"
---
# <a name="quickstart-use-azure-data-studio-to-connect-and-query-data-using-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>快速入門：使用 Azure Data Studio 以透過 Azure Synapse Analytics 中的專用 SQL 集區來連線及查詢資料

本快速入門示範如何使用 Azure Data Studio 連線至 Azure Synapse Analytics 中的專用 SQL 集區。

## <a name="prerequisites"></a>必要條件
您需要 Azure Data Studio 與 Azure Synapse Analytics 的專用 SQL 集區，才能完成本快速入門。

- [安裝 Azure Data Studio](./download-azure-data-studio.md)。

如果還沒有專用的 SQL 集區，請參閱[建立專用的 SQL 集區](/azure/sql-data-warehouse/sql-data-warehouse-get-started-provision)。

請記住伺服器名稱和登入認證！


## <a name="connect-to-your-dedicated-sql-pool"></a>連線到專用 SQL 集區

使用 Azure Data Studio 建立對 Azure Synapse Analytics 伺服器的連線。

1. 第一次執行 Azure Data Studio 時，應該會開啟 [連線] 頁面。 如果沒有看到 [連線] 頁面，請選取 [伺服器] 提要欄位中的 [新增連線] 或 **新增連線** 圖示：
   
   ![[連線] 頁面的螢幕擷取畫面，其中已呼叫新增連線圖示。](media/quickstart-sql-dw/new-connection-icon.png)

2. 本文使用「SQL 登入」，但「Windows 驗證」亦受支援。 使用「您的」Azure SQL 伺服器的伺服器名稱、使用者名稱和密碼填入欄位如下：

   |   設定    | 建議的值 | 描述 |
   |--------------|-----------------|-------------| 
   | **伺服器名稱** | 完整伺服器名稱 | 例如，名稱看起來應該像這樣：**sqlpoolservername.database.windows.net**。 |
   | **驗證** | SQL 登入| 本教學課程使用 SQL 驗證。 |
   | **使用者名稱** | 伺服器系統管理員帳戶 | 這是您在建立伺服器時指定的帳戶。 |
   | **密碼 (SQL 登入)** | 伺服器系統管理員帳戶的密碼 | 這是您在建立伺服器時指定的密碼。 |
   | **儲存密碼嗎？** | [是] 或 [否] | 如果您不想要每次都輸入密碼，請選取 [是]。 |
   | **資料庫名稱** | 保留空白 | 要連線之資料庫的名稱。 |
   | **伺服器群組** | 選取 <Default> | 如果您已建立伺服器群組，您可以設定為特定伺服器群組。 | 

3. 如果您伺服器的防火牆規則沒有允許 Azure Data Studio 進行連線，就會開啟 [建立新的防火牆規則] 表單。 完成表單，以便建立新的防火牆規則。 如需詳細資訊，請參閱[防火牆規則](/azure/sql-database/sql-database-firewall-configure)。

4. 成功連線之後，您的伺服器就會在 [伺服器] 提要欄位中開啟。

## <a name="create-a-database-in-your-dedicated-sql-pool"></a>在專用的 SQL 集區中建立資料庫

1. 以滑鼠右鍵按一下伺服器，然後在物件總管中選取 [新增查詢]。

2. 將下列程式碼片段貼至查詢編輯器，然後選取 [執行]：

   ```sql
    IF NOT EXISTS (
       SELECT name
       FROM sys.databases
       WHERE name = N'TutorialDB'
    )
    CREATE DATABASE [TutorialDB] (EDITION = 'datawarehouse', SERVICE_OBJECTIVE='DW100');
    GO  
    
    ALTER DATABASE [TutorialDB] SET QUERY_STORE=ON
    GO
   ```

## <a name="create-a-table"></a>建立資料表

查詢編輯器仍會連線到 *master* 資料庫，但我們想要在 *TutorialDB* 資料庫中建立資料表。 

1. 將連線內容變更為 **TutorialDB**：

2. 將下列程式碼片段貼至查詢編輯器，然後選取 [執行]：

   > [!NOTE]
   > 您可以在編輯器中將此項目附加至查詢，或覆寫先前的查詢。 請注意，選取 [執行] 只會執行選取的查詢。 如果沒有選取任何項目，則選取 [執行] 會執行編輯器中的所有查詢。

   ```sql
   -- Create a new table called 'Customers' in schema 'dbo'
   -- Drop the table if it already exists
   IF OBJECT_ID('dbo.Customers', 'U') IS NOT NULL
   DROP TABLE dbo.Customers
   GO
   -- Create the table in the specified schema
   CREATE TABLE dbo.Customers
   (
      CustomerId        INT     NOT NULL,
      Name      [NVARCHAR](50)  NOT NULL,
      Location  [NVARCHAR](50)  NOT NULL,
      Email     [NVARCHAR](50)  NOT NULL
   );
   GO
   ```

    :::image type="content" source="media/quickstart-sql-dw/create-table.png" alt-text="在 TutorialDB 資料庫中建立資料表":::


## <a name="insert-rows"></a>插入資料列

1. 將下列程式碼片段貼至查詢編輯器，然後選取 [執行]：

   ```sql
   -- Insert rows into table 'Customers'
   INSERT INTO dbo.Customers
      ([CustomerId],[Name],[Location],[Email])
      SELECT 1, N'Orlando',N'Australia', N'' UNION ALL
      SELECT 2, N'Keith', N'India', N'keith0@adventure-works.com' UNION ALL
      SELECT 3, N'Donna', N'Germany', N'donna0@adventure-works.com' UNION ALL
      SELECT 4, N'Janet', N'United States', N'janet1@adventure-works.com'
   ```

    :::image type="content" source="media/quickstart-sql-dw/create-rows.png" alt-text="在資料表中建立資料列":::

## <a name="view-the-result"></a>檢視結果

1. 將下列程式碼片段貼至查詢編輯器，然後選取 [執行]：

   ```sql
   -- Select rows from table 'Customers'
   SELECT * FROM dbo.Customers;
   ```

2. 查詢的結果隨即顯示：

    :::image type="content" source="media/quickstart-sql-dw/view-results.png" alt-text="View the results":::


## <a name="clean-up-resources"></a>清除資源

如果不打算繼續使用在本文中建立的範例資料庫，請[刪除資源群組](/azure/synapse-analytics/sql-data-warehouse/create-data-warehouse-portal#clean-up-resources)。

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請造訪[使用 Azure Data Studio 連線到 Synapse SQL](/azure/synapse-analytics/sql/get-started-azure-data-studio)。

成功連線到 Azure Synapse Analytics 並執行查詢後，請嘗試[程式碼編輯器教學課程](tutorial-sql-editor.md)。