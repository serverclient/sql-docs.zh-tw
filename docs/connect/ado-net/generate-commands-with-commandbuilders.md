---
title: 使用 CommandBuilder 產生命令
description: 說明如何使用命令產生器，針對具有單一資料表 SELECT 命令的 `DataAdapter` 自動產生 INSERT、UPDATE 與 DELETE 命令。
ms.date: 11/25/2020
dev_langs:
- csharp
ms.assetid: 6e3fb8b5-373b-4f9e-ab03-a22693df8e91
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: d738650db9dbb7b0b2ed3f9411945173f73f748f
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771291"
---
# <a name="generating-commands-with-commandbuilders"></a>使用 CommandBuilder 產生命令

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

當 <xref:System.Data.Common.DbDataAdapter> 物件的 `SelectCommand` 是在執行階段動態指定 (例如透過接受來自使用者之文字命令的查詢工具) 時，您可能會無法在設計階段指定適當的 `InsertCommand`、`UpdateCommand` 或 `DeleteCommand`。 如果您的 <xref:System.Data.DataTable> 對應至或產生自單一資料庫資料表，則可以利用 <xref:System.Data.Common.DbCommandBuilder> 物件來自動產生 `DeleteCommand` 的 `InsertCommand`、`UpdateCommand` 和 <xref:System.Data.Common.DbDataAdapter>。

> [!NOTE]
> 在 Microsoft SqlClient Data Provider for SQL Server 中，<xref:Microsoft.Data.SqlClient.SqlDataAdapter> 類別是衍生自 <xref:System.Data.Common.DbDataAdapter> 類別，而 <xref:Microsoft.Data.SqlClient.SqlCommandBuilder> 類別則是衍生自 <xref:System.Data.Common.DbCommandBuilder> 類別。

根據最小需求，您必須設定 `SelectCommand` 屬性，才能使用自動命令產生功能。 `SelectCommand` 屬性擷取的資料表結構描述則決定自動產生的 INSERT、UPDATE 和 DELETE 陳述式語法。

<xref:System.Data.Common.DbCommandBuilder> 必須執行 `SelectCommand`，才能傳回必要的中繼資料來建構 INSERT、UPDATE 和 DELETE SQL 命令。 因此必須要另外連至資料來源，但這可能會降低效能。 若要達到最佳效能，請明確地指定命令，而不要使用 <xref:System.Data.Common.DbCommandBuilder>。

> [!NOTE]
> `SelectCommand` 還必須傳回至少一個主索引鍵或唯一的資料行。 如果不存在任何內容，便會產生 `InvalidOperation` 例外狀況，且不會產生命令。

與 `DataAdapter` 產生關聯時，<xref:System.Data.Common.DbCommandBuilder> 會自動產生 `InsertCommand` 的 `UpdateCommand`、`DeleteCommand` 和 `DataAdapter` 屬性 (如果它們是 Null 參考)。 如果屬性的 `Command` 已經存在，則會使用現有的 `Command`。

您不能將兩個或多個資料表合併所建立的資料庫檢視視為單一資料庫資料表。 在此情況下，您無法使用 <xref:System.Data.Common.DbCommandBuilder> 來自動產生命令，且必須明確指定您的命令。 如需有關明確設定命令以將 `DataSet` 的更新解析回資料來源的詳細資訊，請參閱[使用 DataAdapters 更新資料來源](update-data-sources-with-dataadapters.md)。

您可能想要把輸出參數對應回 `DataSet` 已更新的資料列。 一項通用工作便是從資料來源擷取自動產生的識別欄位值或時間戳記值。 <xref:System.Data.Common.DbCommandBuilder> 預設不會將輸出參數對應到已更新資料列中的資料行。 在此情況下，您必須明確指定您的命令。 如需範例以了解如何將自動產生的識別欄位對應回已插入資料列的資料行，請參閱[擷取識別或自動編號值](retrieve-identity-or-autonumber-values.md)。

## <a name="rules-for-automatically-generated-commands"></a>適用於自動產生之命令的規則

下列表格顯示產生自動產生命令的規則。

|命令|規則|  
|-------------|----------|  
|`InsertCommand`|針對含 <xref:System.Data.DataRow.RowState%2A> 的 <xref:System.Data.DataRowState.Added> 之資料表的所有資料列，插入資料來源上的資料列。 插入所有可更新的資料行值 (但非識別、運算式或時間籤記等資料行)。|  
|`UpdateCommand`|針對含 `RowState` 的 <xref:System.Data.DataRowState.Modified> 之資料表的所有資料列，更新資料來源上的資料列。 除了無法更新的資料行 (例如識別或運算式) 之外，更新所有可更新的資料行值。 並且在資料來源中，找出資料行值與資料列主索引鍵資料行值相符的資料列，以及剩餘資料行與資料列原始資料相符的資料列，將這些資料列全都更新。 如需詳細資訊，請參閱這個主題稍後的＜更新和刪除的開放式同步存取模式＞。|  
|`DeleteCommand`|針對含 `RowState` 的 <xref:System.Data.DataRowState.Deleted> 之資料表的所有資料列，刪除資料來源上的資料列。 在資料來源中，找出其資料行值與資料列的主索引鍵資料行值相符的所有資料列，以及剩餘資料行與原始資料列值相符的所有資料列，並將這些資料列全都刪除。 如需詳細資訊，請參閱此主題稍後的[適用於更新和刪除的開放式同步存取模型](#optimistic-concurrency-model-for-updates-and-deletes)。|

## <a name="optimistic-concurrency-model-for-updates-and-deletes"></a>適用於更新與刪除的開放式同步存取模型

自動替 UPDATE 與 DELETE 陳述式產生命令的邏輯是以「開放式同步存取」為基礎，也就是說，系統不會鎖定對記錄進行編輯的能力，其他使用者或處理序也可以隨時加以修改。 記錄從 SELECT 陳述式傳回後可能已經修改，但是發出 UPDATE 或 DELETE 陳述式之前，自動產生的 UPDATE 或 DELETE 陳述式會包含 WHERE 子句，指定只有資料列包含所有原始值且尚未從資料來源刪除時，才會更新該資料列。 這樣是為了避免覆寫新資料。
 
> [!NOTE]
> 自動產生的更新嘗試更新已刪除的資料列或未包含 <xref:System.Data.DataSet> 中所找到之原始值的資料列時，該命令不會影響任何記錄，且會擲回 <xref:System.Data.DBConcurrencyException>。

如果您想完成 UPDATE 或 DELETE (不管是否為原始值)，則您必須明確設定 `UpdateCommand` 的 `DataAdapter`，而不是依賴自動命令產生功能。

## <a name="limitations-of-automatic-command-generation-logic"></a>自動命令產生邏輯的限制

下列限制適用於自動命令產生。

### <a name="unrelated-tables-only"></a>僅限沒有關聯的資料表

自動命令產生邏輯為獨立資料表產生 INSERT、UPDATE 或 DELETE 陳述式，而不顧及任何與資料來源中其他資料表的關聯性。 所以，如果呼叫 `Update` 將變更送出至資料行，而這個資料行又擔任資料庫的外部索引鍵條件約束，則可能會發生錯誤。 為了避免發生這種例外狀況，請勿使用 <xref:System.Data.Common.DbCommandBuilder> 來更新外部索引鍵條件約束所牽涉的資料行，而應該明確地指定用於執行作業的陳述式。

### <a name="table-and-column-names"></a>資料表與資料行名稱

如果資料行名稱或資料表名稱包含任何特殊字元，例如空格、句號、引號或其他非英數的字元，則即使以括弧分隔，自動命令產生邏輯仍可能會失敗。 根據提供者而定，設定 QuotePrefix 和 QuoteSuffix 參數可能會允許產生邏輯以處理空格，不過，卻不能逸出特殊字元。 支援格式為 *catalog.schema.table* 的完整資料表名稱。

## <a name="use-the-commandbuilder-to-automatically-generate-an-sql-statement"></a>使用 CommandBuilder 來自動產生 SQL 陳述式

若要自動產生 `DataAdapter` 的 SQL 陳述式，請首先設定 `SelectCommand` 的 `DataAdapter` 屬性，然後建立 `CommandBuilder` 物件，再指定為引數，該引數則是 `DataAdapter` 將自動為其產生 SQL 陳述式的 `CommandBuilder`。

[!code-csharp[SqlCommandBuilder_Create#1](~/../sqlclient/doc/samples/SqlCommandBuilder_Create.cs#1)]

## <a name="modify-the-selectcommand"></a>修改 SelectCommand

如果您在 INSERT、UPDATE 或 DELETE 命令自動產生後，修改 `CommandText` 的 `SelectCommand`，便可能發生例外狀況。 如果已修改的 `SelectCommand.CommandText` 包含的結構描述資訊與插入、更新或刪除命令自動產生時使用的 `SelectCommand.CommandText` 不一致，日後呼叫的 `DataAdapter.Update` 方法便可能會到 `SelectCommand` 參考的目前資料表中，嘗試存取已不存在的資料行，這樣就會擲回例外狀況。

您可以藉由重新整理 `CommandBuilder` 使用的結構描述資訊，呼叫 `RefreshSchema` 的 `CommandBuilder` 方法以自動產生命令。

若您想要知道自動產生的命令為何，可以使用 `GetInsertCommand` 物件的 `GetUpdateCommand`、`GetDeleteCommand` 和 `CommandBuilder` 方法，取得自動產生命令的參考，然後檢查相關命令的 `CommandText` 屬性。

下列程式碼範例將自動產生的更新命令寫入主控台。

[!code-csharp[SqlCommandBuilder_Create#2](~/../sqlclient/doc/samples/SqlCommandBuilder_Create.cs#2)]

下列範例會在資料集中重新建立資料表。 系統會呼叫 **RefreshSchema** 方法，以使用這個新資料行資訊重新整理自動產生的命令。

[!code-csharp[SqlCommandBuilder_Create#3](~/../sqlclient/doc/samples/SqlCommandBuilder_Create.cs#3)]

## <a name="see-also"></a>請參閱

- [命令與參數](commands-parameters.md)
- [執行命令](execute-command.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
