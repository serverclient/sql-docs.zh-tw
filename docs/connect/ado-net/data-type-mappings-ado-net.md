---
title: 資料類型對應
description: 描述 Microsoft SqlClient Data Provider for SQL Server 會使用資料類型。
ms.date: 11/13/2020
ms.assetid: d4afab94-ada6-4c77-a73c-41f17bae6b5a
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 808acdf89350f539f03f6fdc75f2f7a5ed5b7707
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771610"
---
# <a name="data-type-mappings-in-adonet"></a>ADO.NET 中的資料類型對應

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

ADO.NET 會以一般型別系統為基礎，其會定義在執行階段宣告、使用及管理類型的方式。 它同時包含了都衍生自 <xref:System.Object> 基底類型的實值型別 (Value Type) 和參考型別 (Reference Type)。 使用資料來源時，如果沒有明確指定資料型別，就會從資料提供者 (Data Provider) 推斷資料型別。 例如，<xref:System.Data.DataSet> 物件與任何特定資料來源無關。 `DataSet` 內的資料是由資料來源擷取而來，且變更會藉由 `DataAdapter` 存回資料來源； 此程式流程表示當 `DataAdapter` 以資料來源的值填滿 `DataSet` 中的 <xref:System.Data.DataTable> 時，`DataTable` 中產生的資料行資料類型是 .NET Framework 類型，而不是用來連線到資料來源之 Microsoft SqlClient Data Provider for SQL Server 特有的類型。

同樣地，當 `DataReader` 從資料來源傳回值時，產生的值會儲存在具有 .NET Framework 類型的區域變數中。 針對 `DataAdapter` 的 `Fill` 作業與 `DataReader` 的 `Get` 方法，會從 Microsoft SqlClient Data Provider for SQL Server 所傳回的值推斷 .NET Framework 類型。

如果您知道傳回值的特定型別，就可以使用 `DataReader` 具型別的存取子方法，而不用仰賴推斷的資料型別。 具類型的存取子方法會透過以特定的 .NET Framework 類型來傳回值，為您提供更好的效能，而不需進行其他類型轉換。

> [!NOTE]
> 適用於 Microsoft SqlClient Data Provider for SQL Server 資料類型的 Null 值會以 `DBNull.Value` 來表示。

## <a name="in-this-section"></a>本節內容

[SQL Server 資料類型對應](sql-server-data-type-mappings.md) 針對 <xref:Microsoft.Data.SqlClient> 列出推斷的資料類型對應與資料存取子方法。

[浮點數](floating-point-numbers.md) 描述開發人員在使用浮點數時經常遇到的問題。

## <a name="see-also"></a>另請參閱

- [SQL Server 資料類型和 ADO.NET](./sql/sql-server-data-types.md)
- [設定參數](configure-parameters.md)
- [擷取資料庫結構描述資訊](retrieving-database-schema-information.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
