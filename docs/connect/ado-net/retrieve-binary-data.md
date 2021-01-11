---
title: 擷取二進位資料
description: 說明如何使用 `CommandBehavior`.`SequentialAccess` 來擷取二進位資料或大型資料結構 以修改 `DataReader` 的預設行為。
ms.date: 12/04/2020
dev_langs:
- csharp
ms.assetid: 56c5a9e3-31f1-482f-bce0-ff1c41a658d0
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 1033a6b5394d92a45cd19d70f4dd6c250ec37b62
ms.sourcegitcommit: 4419e99d77ee2c73f9da1559c7944f7702f2de30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744381"
---
# <a name="retrieve-binary-data"></a>擷取二進位資料

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

根據預設，一旦有整列資料可用時，**DataReader** 便會將內送資料載入為資料列。 但是二進位大型物件 (BLOB) 需要不同的處理方式，因為它們所包含的資料可能高達數 GB，無法包含在單一資料列內。 **Command.ExecuteReader** 方法具有多載，其會接受 <xref:System.Data.CommandBehavior> 引數以修改 **DataReader** 的預設行為。 您可以將 <xref:System.Data.CommandBehavior.SequentialAccess> 傳遞給 **ExecuteReader** 方法以修改 **DataReader** 的預設行為，這樣可在接收到資料時依序載入資料，而不必載入資料列。 這種方式相當適於載入 BLOB 或其他大型資料結構。

> [!NOTE]
> 設定 **DataReader** 來使用 **SequentialAccess** 時，您必須注意存取傳回欄位的順序。 **DataReader** 的預設行為 (一旦有整列資料可用時立即將其載入) 能讓您在讀取下一個資料列前以任何順序存取傳回欄位。 但是使用 **SequentialAccess** 時，您就必須依序存取 **DataReader** 傳回的欄位。 例如，如果您的查詢傳回三個資料行，而第三個是 BLOB，則您必須在存取第三個欄位內的 BLOB 資料前，先把第一和第二個欄位的值傳回。 如果您在尚未存取第一和第二個欄位前先存取第三個欄位，便無法再使用第一和第二個欄位的值。 這是因為 **SequentialAccess** 已修改 **DataReader** 來循序傳回資料，而 **DataReader** 讀過資料後，就無法取得資料。

存取 BLOB 欄位中的資料時，請使用 **DataReader** 的 **GetBytes** 或 **GetChars** 具型別存取子，這會使用資料填入陣列。 您也可以為字元資料使用 **GetString**，然而為了節省系統資源，建議您不要將整個 BLOB 值載入單一字串變數。 您可以指定傳回資料的特定緩衝區大小，以及從傳回資料讀取的第一個位元組或字元的開始位置。 **GetBytes** 與 **GetChars** 將會傳回 `long` 值，其代表傳回的位元組數或字元數。 如果您將 Null 陣列傳遞給 **GetBytes** 或 **GetChars**，則傳回的長整數值將會是 BLOB 中的總位元組數或總字元數。 您可以選擇將陣列內的索引指定為要讀取資料的開始位置。

## <a name="example"></a>範例

下列範例會從 [**pubs** 範例資料庫](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/northwind-pubs)傳回發行者識別碼與商標。 發行者識別碼 (`pub_id`) 是一個字元欄位，而商標則是 BLOB 影像。 由於 **logo** 欄位是點陣圖，因此範例會使用 **GetBytes** 傳回二進位資料。 請注意，由於欄位必須被循序存取，所以您要先存取目前資料列的發行者 ID，再存取標誌。

[!code-csharp[SqlCommand_ExecuteReader_SequentialAccess#1](~/../sqlclient/doc/samples/SqlCommand_ExecuteReader_SequentialAccess.cs#1)]

## <a name="see-also"></a>請參閱

- [SQL Server 二進位和大型數值資料](./sql/sql-server-binary-large-value-data.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
