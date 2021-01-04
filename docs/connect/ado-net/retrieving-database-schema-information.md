---
title: 擷取資料庫結構描述資訊
description: 了解如何使用 Microsoft SqlClient Data Provider for SQL Server 擷取資料庫結構描述資訊。
ms.date: 11/26/2020
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 932f4ff2109e3754fb97c79274a5ffb93b9494d3
ms.sourcegitcommit: 2add15a99df7b85d271adb261523689984dfd134
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051278"
---
# <a name="retrieving-database-schema-information"></a>擷取資料庫結構描述資訊

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

從資料庫取得結構描述資訊是透過結構描述探索處理序來完成。 結構描述探索允許應用程式要求受控提供者尋找並傳回給定資料庫的資料庫結構描述 (亦稱為「中繼資料」) 相關資訊。 不同的資料庫結構描述項目 (如資料表、資料行及預存程序) 都透過結構描述集合公開。 每個結構描述集合都包含正在使用的提供者之各種特定的結構描述資訊。

Microsoft SqlClient Data Provider for SQL Server 會在 **SqlConnection** 類別中實作 **GetSchema** 方法，而從 **GetSchema** 方法傳回的結構描述資訊則以 <xref:System.Data.DataTable> 形式表示。 **GetSchema** 方法是一種多載方法，可提供選擇性參數，以指定要傳回的結構描述集合及限制傳回的資訊量。 SqlClient 資料提供者也提供 **GetSchemaTable** 方法，可傳回描述 **SqlDataReader** 資料行中繼資料的 DataTable。

## <a name="in-this-section"></a>本節內容

[GetSchema 與結構描述集合](getschema-and-schema-collections.md)  
說明 **GetSchema** 方法及其在擷取及限制資料庫結構描述資訊時的使用方式。

[結構描述限制](schema-restrictions.md)  
說明可搭配 **GetSchema** 使用的結構描述限制。 

[一般結構描述集合](common-schema-collections.md)  
描述所有 .NET 受控提供者都支援的所有一般結構描述集合。  
  
[SQL Server 結構描述集合](sql-server-schema-collections.md)  
說明 Microsoft SqlClient Data Provider for SQL Server 所支援的其他結構描述集合。 

## <a name="reference"></a>參考

<xref:System.Data.Common.DbConnection.GetSchema%2A>  
說明 <xref:System.Data.Common.DbConnection> 類別的 **GetSchema** 方法。

<xref:Microsoft.Data.SqlClient.SqlConnection.GetSchema%2A>  
說明 <xref:Microsoft.Data.SqlClient.SqlConnection> 類別的 **GetSchema** 方法。

<xref:System.Data.Common.DbDataReader.GetSchemaTable%2A>  
說明 <xref:System.Data.Common.DbDataReader> 類別的 **GetSchemaTable** 方法。 

<xref:Microsoft.Data.SqlClient.SqlDataReader.GetSchemaTable%2A>  
說明 <xref:Microsoft.Data.SqlClient.SqlDataReader> 類別的 **GetSchemaTable** 方法。

## <a name="see-also"></a>請參閱

- [在 ADO.NET 中擷取及修改資料](retrieving-modifying-data.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
