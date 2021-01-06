---
title: 命令和參數
description: 了解如何針對 Microsoft SqlClient Data Provider for SQL Server 使用 Command 物件來執行命令，並從資料來源傳回結果。
ms.date: 11/25/2020
ms.assetid: b623f810-d871-49a5-b0f5-078cc3c34db6
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 07f23aa6545636fd8cbbf1717f6c8ee81ebe9294
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771470"
---
# <a name="commands-and-parameters"></a>命令和參數

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

建立連至資料來源的連接後，您可以執行命令，並使用 <xref:System.Data.Common.DbCommand> 物件從資料來源傳回結果。 您可以使用適用於 Microsoft SqlClient Data Provider for SQL Server 的其中一個命令建構函式來建立命令。 建構函式可以接受選擇性引數，例如要在資料來源執行的 SQL 陳述式、<xref:System.Data.Common.DbConnection> 物件或 <xref:System.Data.Common.DbTransaction> 物件。

此外，您也可以將這些物件設定為命令的屬性。 您還可以使用 <xref:System.Data.Common.DbConnection.CreateCommand%2A> 物件的 `DbConnection` 方法，針對特定連接建立命令。 您可以使用 <xref:System.Data.Common.DbCommand.CommandText%2A> 屬性來設定正由命令執行的 SQL 陳述式。 Microsoft SqlClient Data Provider for SQL Server 具有 <xref:Microsoft.Data.SqlClient.SqlCommand> 物件。

## <a name="in-this-section"></a>本節內容

[執行命令](execute-command.md)  
說明 ADO.NET `Command` 物件，以及如何用它來針對資料來源執行查詢和命令。

[設定參數](configure-parameters.md)  
說明如何使用 `Command` 參數，包括方向、資料型別和參數語法。

[使用 CommandBuilder 產生命令](generate-commands-with-commandbuilders.md)  
說明如何使用命令產生器，針對具有單一資料表 SELECT 命令的 `DataAdapter` 自動產生 INSERT、UPDATE 和 DELETE 命令。

[從資料庫取得單一值](obtain-single-value-from-database.md)  
說明如何使用 `ExecuteScalar` 物件的 `Command` 方法，從資料庫查詢傳回單一值。

[使用命令來修改資料](use-commands-to-modify-data.md)  
描述如何使用 Microsoft SqlClient Data Provider for SQL Server 來執行預存程序或資料定義語言 (DDL) 陳述式。

## <a name="see-also"></a>請參閱

- [DataAdapter 和 DataReader](dataadapters-datareaders.md)
- [連線到資料來源](connecting-to-data-source.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
