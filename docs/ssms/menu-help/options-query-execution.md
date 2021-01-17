---
title: SQL Server [選項] 頁面 - 查詢執行
description: 選項 (查詢執行)
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: ssms
ms.topic: conceptual
f1_keywords:
- VS.ToolsOptionsPages.Query_Execution.Sql_Server.General
dev_langs:
- TSQL
author: markingmyname
ms.author: maghan
ms.date: 01/13/2021
ms.openlocfilehash: 25ac37cdb9095151c90fdf81314d4c32044e6159
ms.sourcegitcommit: af64e2b8d498af26b973e86db5c00f8d72991295
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98193126"
---
# <a name="query-options-execution-general-page"></a>查詢選項執行 (一般頁面)

使用此頁面來指定執行 Microsoft SQL Server 查詢的選項。 若要存取此對話方塊，請以滑鼠右鍵按一下查詢編輯器視窗的主體，然後按一下 [查詢選項]。

- **SET ROWCOUNT** 預設值為 0，表示 SQL Server 將等候結果，直到所有結果都收到為止。 如果您要 SQL Server 在取得指定的資料列數後停止查詢，請提供大於 0 的值。 若要關閉此選項 (以便傳回所有資料列)，請指定 SET ROWCOUNT 0。

- **SET TEXTSIZE** 預設值為 2,147,483,647 個位元組，表示 SQL Server 將提供完整的資料欄位，直到 text、ntext、nvarchar(max) 與 varchar(max) 資料欄位的上限。 這不會影響 XML 資料類型。 提供較小的數值，即可在有大量數值時，限制傳回的結果數量。 資料行若大於提供的數值，就會被截斷。

- **Execution time-out** 指出取消查詢之前要等候的秒數。 值為 0 表示無盡的等候，或沒有逾時。

- **Batch separator** 輸入您用來將 Transact-SQL 陳述式分隔成批次的文字。 預設為 GO。

- **預設會以 SQLCMD 模式開啟新查詢** 選取此核取方塊來以 SQLCMD 模式開啟新查詢。 只有在透過 [工具] 功能表開啟此對話方塊時，才可以看見此核取方塊。

    當您選取此選項時，請注意以下限制：

    - Database Engine 查詢編輯器中的 IntelliSense 會關閉。

    - 由於查詢編輯器無法從命令列執行，所以您無法傳入命令列參數 (例如變數)。

    - 由於查詢編輯器無法回應作業系統提示，所以您必須非常小心，不要執行互動式陳述式。

- **重設為預設值** 將此頁面上的所有值重設為原始預設值。