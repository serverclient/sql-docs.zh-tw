---
title: 建立可重複使用的程式碼片段
description: 了解如何建立及使用 Azure Data Studio SQL 程式碼片段，讓您輕鬆建立資料庫和資料庫物件。
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.topic: how-to
author: markingmyname
ms.author: maghan
ms.reviewer: alayu, sstein
ms.custom: seodec18
ms.date: 09/24/2018
ms.openlocfilehash: bd86afbe288be3b92b15b6702ec46903f56a2ad5
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97637801"
---
# <a name="create-and-use-code-snippets-to-quickly-create-transact-sql-t-sql-scripts-in-azure-data-studio"></a>建立和使用程式碼片段，以在 Azure Data Studio 中快速建立 Transact-SQL (T-SQL) 指令碼

Azure Data Studio 中的程式碼片段是範本，其可供輕鬆建立資料庫和資料庫物件。 

Azure Data Studio 提供數個 T-SQL 程式碼片段，以協助快速產生適當的語法。 

您也可以建立使用者定義的程式碼片段。

## <a name="using-built-in-t-sql-code-snippets"></a>使用內建 T-SQL 程式碼片段

1. 若要存取可用的程式碼片段，請在查詢編輯器中鍵入 *sql* 開啟清單：

   ![程式碼片段](media/code-snippets/sql-snippets.png)

2. 選取您要使用的程式碼片段，它會產生 T-SQL 指令碼。 例如，選取 *sqlCreateTable*：

   ![CREATE TABLE 程式碼片段](media/code-snippets/create-table.png)

3. 將醒目提示的欄位更新為您的特定值。 例如，將 *TableName* 和 *Schema* 取代為您的資料庫值：

   ![程式碼片段中的資料表](media/code-snippets/table-from-snippet.png)

   如果您要變更的欄位不再醒目提示 (當您在編輯器中四處移動游標時，就會發生這種情況)，請以滑鼠右鍵按一下您要變更的文字，然後選取 [變更所有發生次數]：

   ![全部變更](media/code-snippets/change-all.png)

4. 更新或新增所選程式碼片段所需的其他 T-SQL。 例如，更新 *Column1*、*Column2*，並新增更多資料行。

## <a name="creating-sql-code-snippets"></a>建立 SQL 程式碼片段

您可以定義自己的程式碼片段。 若要開啟 SQL 程式碼片段檔案進行編輯：

1. 開啟 *命令選擇區* (**Shift+Ctrl+P**)，並鍵入 *snip*，然後選取 [喜好設定:開啟使用者程式碼片段]：

   ![使用者程式碼片段](media/code-snippets/user-snippets.png)

2. 選取 [SQL]：

   > [!NOTE]
   > Azure Data Studio 會從 Visual Studio Code 繼承程式碼片段功能，因此本文專門討論如何使用 SQL 程式碼片段。 如需詳細資訊，請參閱 Visual Studio Code 文件中的 [Creating your own snippets](https://code.visualstudio.com/docs/editor/userdefinedsnippets) (建立您自己的程式碼片段)。 

   ![選取 SQL](media/code-snippets/select-sql.png)

3. 將下列程式碼貼到 *sql.json*：

    ```sql
    {
     "Select top 5": {
    "prefix": "sqlSelectTop5",
    "body": "SELECT TOP 5 * FROM ${1:TableName}",
    "description": "User-defined snippet example 1"
    },
    "Create Table snippet":{
    "prefix": "sqlCreateTable2",
    "body": [
    "-- Create a new table called '${1:TableName}' in schema '${2:SchemaName}'",
    "-- Drop the table if it already exists",
    "IF OBJECT_ID('$2.$1', 'U') IS NOT NULL",
    "DROP TABLE $2.$1",
    "GO",
    "-- Create the table in the specified schema",
    "CREATE TABLE $2.$1",
    "(",
    "$1Id INT NOT NULL PRIMARY KEY, -- primary key column",
    "Column1 [NVARCHAR](50) NOT NULL,",
    "Column2 [NVARCHAR](50) NOT NULL",
    "-- specify more columns here",
    ");",
    "GO"
    ],
       "description": "User-defined snippet example 2"
       }
       }
    ```

4. 儲存 sql.json 檔案。

5. 按一下 **Ctrl+N** 開啟新的查詢編輯器視窗。

6. 鍵入 **sql**，您會看到剛才新增的兩個使用者程式碼片段：*sqlCreateTable2* 和 *sqlSelectTop5*。

選取其中一個新的程式碼片段，並進行測試回合！

## <a name="next-steps"></a>後續步驟

如需 SQL 編輯器的資訊，請參閱[程式碼編輯器教學課程](tutorial-sql-editor.md)。
