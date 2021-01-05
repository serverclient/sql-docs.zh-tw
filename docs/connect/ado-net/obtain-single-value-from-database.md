---
title: 從資料庫取得單一值
description: 了解如何在 ADO.NET 中傳回單一值。 此範例程式碼會傳回已插入之記錄的識別欄位值。
ms.date: 11/25/2020
dev_langs:
- csharp
ms.assetid: b38526cd-a62a-48cb-822a-e91dfa68e02d
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 3937c1d4617f3cecb9012d82d590f75634ab1043
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771490"
---
# <a name="obtaining-a-single-value-from-a-database"></a>從資料庫取得單一值

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

或許您需要以單一數值傳回資料庫資訊，而非以資料表或資料流的形式。 例如，假設您想要傳回彙總函式 (例如 COUNT(\*)、SUM(Price) 或 AVG(Quantity)) 的結果。 **Command** 物件可讓您以 **ExecuteScalar** 方法傳回單一數值。 **ExecuteScalar** 方法會將結果集第一個資料列之第一個資料行的值當做純量值傳回。

## <a name="example"></a>範例

下列程式碼範例會使用 <xref:Microsoft.Data.SqlClient.SqlCommand>，在資料庫中插入新的值。 <xref:Microsoft.Data.SqlClient.SqlCommand.ExecuteScalar%2A> 方法是用來傳回所插入資料錄的識別資料行值。

[!code-csharp[DataWorks SqlCommand.ExecuteScalar#1](~/../sqlclient/doc/samples/SqlCommand_ExecuteScalar_Return_Id.cs#1)]

## <a name="see-also"></a>請參閱

- [命令與參數](commands-parameters.md)
- [執行命令](execute-command.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
