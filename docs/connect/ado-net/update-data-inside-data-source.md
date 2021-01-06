---
title: 更新資料來源中的資料
description: 說明如何執行修改資料庫資料的命令或預存程序。
ms.date: 11/25/2020
dev_langs:
- csharp
ms.assetid: 55c545e5-dcd5-4323-a5b9-3825c2157462
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: aca7b1adb8ce91a12832bccfd6cbd27b07229d22
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771480"
---
# <a name="updating-data-in-a-data-source"></a>更新資料來源中的資料

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

會修改資料的 SQL 陳述式 (例如 INSERT、UPDATE 或 DELETE) 不會傳回資料列。 同樣地，許多預存程序會執行動作但不傳回資料列。 若要執行不會傳回資料列的命令，請使用適當的 SQL 命令與 **連線** (包含任何所需的 **參數**) 來建立 **Command** 物件。 使用 <xref:Microsoft.Data.SqlClient.SqlCommand> 物件的 <xref:Microsoft.Data.SqlClient.SqlCommand.ExecuteNonQuery%2A> 方法來執行命令。

> [!NOTE]
> **ExecuteNonQuery** 方法會傳回整數，其代表執行的陳述式或預存程序所影響的資料列數目。 如果執行多個陳述式，傳回的值就是受到所有執行的陳述式影響的記錄數總和。

## <a name="example"></a>範例

下列程式碼範例會使用 **ExecuteNonQuery** 來執行 INSERT 陳述式，以將記錄插入資料庫。
  
[!code-csharp[DataWorks SqlCommand.ExecuteNonQuery#1](~/../sqlclient/doc/samples/SqlCommand_ExecuteNonQuery_SP_DML.cs#1)]

下列程式碼範例會執行由[執行目錄作業](perform-catalog-operations.md)中的範例程式碼所建立的預存程序。 預存程序不會傳回任何資料列，因此會使用 **ExecuteNonQuery** 方法，但是預存程序確實會收到輸入參數，而且會傳回輸出參數與傳回值。

[!code-csharp[DataWorks SqlCommand.ExecuteNonQuery#2](~/../sqlclient/doc/samples/SqlCommand_ExecuteNonQuery_SP_DML.cs#2)]

## <a name="see-also"></a>請參閱

- [使用命令來修改資料](use-commands-to-modify-data.md)
- [使用 DataAdapter 更新資料來源](update-data-sources-with-dataadapters.md)
- [命令與參數](commands-parameters.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
