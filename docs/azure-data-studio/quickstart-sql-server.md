---
title: 快速入門：連線及查詢 SQL Server
description: 進行快速入門，使用 Azure Data Studio 連線到 SQL Server，然後使用 Transact-SQL (T-SQL) 陳述式建立資料庫。
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.topic: quickstart
author: yualan
ms.author: alayu
ms.reviewer: alayu, maghan, sstein
ms.custom: seodec18, sqlfreshmay19
ms.date: 08/02/2019
ms.openlocfilehash: 6a637791eac7858e905f766126ca93d9277b65c1
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97637624"
---
# <a name="quickstart-use-azure-data-studio-to-connect-and-query-sql-server"></a>快速入門：使用 Azure Data Studio 連線及查詢 SQL Server

此快速入門示範如何使用 Azure Data Studio 連線到 SQL Server，然後使用 Transact-SQL (T-SQL) 陳述式來建立 Azure Data Studio 教學課程中所使用的 *TutorialDB*。

## <a name="prerequisites"></a>必要條件

若要完成此快速入門，您需要 Azure Data Studio 以及 SQL Server 的存取權。

- [安裝 Azure Data Studio](./download-azure-data-studio.md)。

如果您沒有 SQL Server 存取權，請從下列連結中選取平台 (請務必記住您的 SQL 登入和密碼)：

- [Windows - 下載 SQL Server 2017 Developer Edition](https://www.microsoft.com/sql-server/sql-server-downloads)
- [macOS - 下載 Docker 上的 SQL Server 2017](../linux/quickstart-install-connect-docker.md)
- [Linux - 下載 SQL Server 2017 Developer 版](../linux/sql-server-linux-overview.md#install) - 您只需要執行步驟到「建立及查詢資料」。

## <a name="connect-to-a-sql-server"></a>連線到 SQL Server

1. 啟動 **Azure Data Studio**。

2. 第一次執行 Azure Data Studio 時，應該會開啟 [歡迎使用] 頁面。 如果您沒有看到 [歡迎使用] 頁面，請選取 [說明] > [歡迎使用]。 選取 [新增連線]，開啟 [連線] 窗格：

   ![新增連線圖示](media/quickstart-sql-server/new-connection-icon.png)

3. 本文使用「SQL 登入」，但「Windows 驗證」亦受支援。 填入欄位如下：

   - **伺服器名稱：** 在這裡輸入伺服器名稱。 例如 localhost。
   - **驗證類型：** SQL 登入
   - **使用者名稱：** SQL Server 的使用者名稱
   - **密碼：** SQL Server 的密碼
   - **資料庫名稱：** \<Default\>
   - **伺服器群組：** \<Default\>

   ![新增連線畫面](media/quickstart-sql-server/new-connection-screen.png)

## <a name="create-a-database"></a>建立資料庫

下列步驟會建立名為 **TutorialDB** 的資料庫：

1. 以滑鼠右鍵按一下您的伺服器 **localhost**，然後選取 [新增查詢]。

2. 將下列程式碼片段貼到查詢視窗，然後選取 [執行]。

    ```sql
    USE master
    GO
    IF NOT EXISTS (
     SELECT name
     FROM sys.databases
     WHERE name = N'TutorialDB'
    )
     CREATE DATABASE [TutorialDB];
    GO
    IF SERVERPROPERTY('ProductVersion') > '12'
     ALTER DATABASE [TutorialDB] SET QUERY_STORE=ON;
    GO
    ```

   查詢完成後，新的 **TutorialDB** 會出現在資料庫清單中。 如果看不到，請以滑鼠右鍵按一下 [資料庫] 節點，然後選取 [重新整理]。

   ![建立資料庫](media/quickstart-sql-server/create-database.png)

## <a name="create-a-table"></a>建立資料表

查詢編輯器仍會連線到 *master* 資料庫，但我們想要在 *TutorialDB* 資料庫中建立資料表。

1. 將連線內容變更為 **TutorialDB**：

   ![變更內容](media/quickstart-sql-server/change-context.png)

2. 將下列程式碼片段貼到查詢視窗，然後按一下 [執行]：

   > [!NOTE]
   > 您可以在編輯器中將此程式碼片段附加至查詢，也可以覆寫先前的查詢。 請注意，按一下 [執行] 只會執行選取的查詢。 如果沒有選取任何項目，按一下 [執行] 會執行編輯器中的所有查詢。

    ```sql
    -- Create a new table called 'Customers' in schema 'dbo'
    -- Drop the table if it already exists
    IF OBJECT_ID('dbo.Customers', 'U') IS NOT NULL
     DROP TABLE dbo.Customers;
    GO
    -- Create the table in the specified schema
    CREATE TABLE dbo.Customers
    (
     CustomerId int NOT NULL PRIMARY KEY, -- primary key column
     Name nvarchar(50) NOT NULL,
     Location nvarchar(50) NOT NULL,
     Email nvarchar(50) NOT NULL
    );
    GO
    ```

查詢完成後，新的 [客戶] 資料表會出現在資料表清單中。 您可能必須以滑鼠右鍵按一下 [TutorialDB] > [資料表] 節點，然後選取 [重新整理]。

## <a name="insert-rows"></a>插入資料列

- 將下列程式碼片段貼到查詢視窗，然後按一下 [執行]：

    ```sql
    -- Insert rows into table 'Customers'
    INSERT INTO dbo.Customers
     ([CustomerId], [Name], [Location], [Email])
    VALUES
     ( 1, N'Orlando', N'Australia', N''),
     ( 2, N'Keith', N'India', N'keith0@adventure-works.com'),
     ( 3, N'Donna', N'Germany', N'donna0@adventure-works.com'),
     ( 4, N'Janet', N'United States', N'janet1@adventure-works.com')
    GO
    ```

## <a name="view-the-data-returned-by-a-query"></a>檢視查詢所傳回的資料

 - 將下列程式碼片段貼到查詢視窗，然後按一下 [執行]：

   ```sql
   -- Select rows from table 'Customers'
   SELECT * FROM dbo.Customers;
   ```

   ![選取結果](media/quickstart-sql-server/select-results.png)

## <a name="next-steps"></a>後續步驟

現在您已成功連線到 SQL Server 並執行查詢，請嘗試[程式碼編輯器教學課程](tutorial-sql-editor.md)。