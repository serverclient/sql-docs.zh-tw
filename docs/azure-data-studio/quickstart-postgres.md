---
title: 快速入門：連線及查詢 PostgreSQL
description: 進行快速入門，使用 Azure Data Studio 連線到 PostgreSQL，然後使用 SQL 陳述式建立和查詢資料庫。
ms.custom: seodec18
ms.date: 09/18/2019
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.reviewer: alayu, maghan, sstein
ms.topic: quickstart
author: sunilagarwal
ms.author: sunila
ms.openlocfilehash: 389c8b745a3768f63e2f702a7f48e8c110262fb7
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97637705"
---
# <a name="quickstart-use-azure-data-studio-to-connect-and-query-postgresql"></a>快速入門：使用 Azure Data Studio 連線及查詢 PostgreSQL

本快速入門說明如何使用 Azure Data Studio 連線至 PostgreSQL，然後使用 SQL 陳述式建立並查詢資料庫 *tutorialdb*。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，則需要 Azure Data Studio、適用於 Azure Data Studio 的 PostgreSQL 延伸模組，以及 PostgreSQL 伺服器的存取權。

- [安裝 Azure Data Studio](./download-azure-data-studio.md)。
- [安裝適用於 Azure Data Studio 的 PostgreSQL 延伸模組](./extensions/postgres-extension.md)。
- [安裝 PostgreSQL](https://www.postgresql.org/download/) (或者，您可以使用 [az postgres up](/azure/postgresql/quickstart-create-server-up-azure-cli)，在雲端建立 Postgres 資料庫)。 

## <a name="connect-to-postgresql"></a>連線到 PostgreSQL

1. 啟動 **Azure Data Studio**。

2. 第一次啟動 Azure Data Studio 時，會隨即開啟 [連線] 對話方塊。 如果 [連線] 對話方塊未開啟，請按一下 [伺服器] 頁面中的 **新增連線** 圖示：

   ![新增連線圖示](media/quickstart-postgresql/new-connection-icon.png)

3. 在快顯的表單中，移至 [連線類型]，然後從下拉式選單中選取 [PostgreSQL]。


4. 使用 PostgreSQL 伺服器的伺服器名稱、使用者名稱和密碼，填入其餘欄位。 

   ![新增連線畫面](media/quickstart-postgresql/new-connection-screen.png)  

   | 設定       | 範例值 | 描述 |
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **伺服器名稱** | localhost | 完整伺服器名稱 |
   | **使用者名稱** | postgres | 您要用來登入的使用者名稱。 |
   | **密碼 (SQL 登入)** | *password* | 您要用來登入的帳戶密碼。 |
   | **密碼** | *檢查* | 如果您不想要每次連線都輸入密碼，請核取此方塊。 |
   | **資料庫名稱** | \<Default\> | 如果您希望連線能夠指定資料庫，請填入這個選項。 |
   | **伺服器群組** | \<Default\> | 這個選項可讓您將此連線指派給您所建立的特定伺服器群組。 | 
   | **名稱 (選擇性)** | 保留空白 | 這個選項可讓您指定伺服器的易記名稱。 | 

5. 選取 [連接]。 

成功連線之後，您的伺服器就會在 [伺服器] 提要欄位中開啟。


## <a name="create-a-database"></a>建立資料庫

下列步驟會建立名為 **tutorialdb** 的資料庫：

1. 在 [伺服器] 提要欄位中，以滑鼠右鍵按一下您的 PostgreSQL 伺服器，然後選取 [新增查詢]。

2. 將此 SQL 陳述式貼到開啟的查詢編輯器。

   ```sql
   CREATE DATABASE tutorialdb;
   ```

3. 從工具列，選取 [執行] 執行查詢。 通知會出現在 [訊息] 窗格中，顯示查詢進度。

>[!TIP]
> 您可以使用鍵盤上的 **F5** 執行陳述式，而不是使用 [執行]。

查詢完成後，以滑鼠右鍵按一下 [資料庫]，然後選取 [重新整理]，即可在 [資料庫] 節點下的清單中看到 **tutorialdb**。


## <a name="create-a-table"></a>建立資料表

 下列步驟會在 **tutorialdb** 中建立資料表：

1. 使用查詢編輯器中的下拉式清單，將連線內容變更為 **tutorialdb**。 

   ![變更內容](media/quickstart-postgresql/change-context.png)

2. 將下列 SQL 陳述式貼到查詢編輯器，然後按一下 [執行]。 

   > [!NOTE]
   > 您可以在編輯器中附加此項目至查詢，或覆寫現有的查詢。 按一下 [執行] 只會執行醒目提示的查詢。 如果沒有醒目提示任何項目，按一下 [執行] 會執行編輯器中的所有查詢。

   ```sql
   -- Drop the table if it already exists
   DROP TABLE IF EXISTS customers;
   -- Create a new table called 'customers'
   CREATE TABLE customers(
       customer_id SERIAL PRIMARY KEY,
       name VARCHAR (50) NOT NULL,
       location VARCHAR (50) NOT NULL,
       email VARCHAR (50) NOT NULL
   );
   ```

## <a name="insert-rows"></a>插入資料列

將下列程式碼片段貼到查詢視窗，然後按一下 [執行]：

   ```sql
   -- Insert rows into table 'customers'
   INSERT INTO customers
       (customer_id, name, location, email)
    VALUES
      ( 1, 'Orlando', 'Australia', ''),
      ( 2, 'Keith', 'India', 'keith0@adventure-works.com'),
      ( 3, 'Donna', 'Germany', 'donna0@adventure-works.com'),
      ( 4, 'Janet', 'United States','janet1@adventure-works.com');
   ```

## <a name="query-the-data"></a>查詢資料

1. 將下列程式碼片段貼到查詢編輯器，然後按一下 [執行]：
   
   ```sql
   -- Select rows from table 'customers'
   SELECT * FROM customers; 
   ```

2. 查詢的結果隨即顯示：

   ![檢視結果](media/quickstart-postgresql/view-results.png)

## <a name="next-steps"></a>後續步驟

了解[適用於 Azure Data Studio 中 Postgres 的案例](./extensions/postgres-extension.md)。