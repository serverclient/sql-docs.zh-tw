---
title: 執行目錄作業
description: 說明如何執行修改資料庫結構描述的命令。
ms.date: 11/25/2020
dev_langs:
- csharp
ms.assetid: e60f542f-6271-495b-a9e4-48553481c2a3
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 79eef694b3c6294eb12630f27c4b3688823581c7
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771536"
---
# <a name="performing-catalog-operations"></a>執行目錄作業

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

若要執行 CREATE TABLE 或 CREATE PROCEDURE 陳述式等命令來修改資料庫或目錄，請使用適當的 SQL 陳述式與 **Connection** 物件來建立 **Command** 物件。 使用 <xref:Microsoft.Data.SqlClient.SqlCommand> 物件的 <xref:Microsoft.Data.SqlClient.SqlCommand.ExecuteNonQuery%2A> 方法來執行命令。

## <a name="example"></a>範例

下列程式碼範例會在 Microsoft SQL Server 資料庫中建立預存程序。

[!code-csharp[DataWorks SqlCommand.ExecuteNonQuery#3](~/../sqlclient/doc/samples/SqlCommand_ExecuteNonQuery_SP_DML.cs#3)]

## <a name="see-also"></a>請參閱

- [使用命令來修改資料](use-commands-to-modify-data.md)
- [命令與參數](commands-parameters.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
