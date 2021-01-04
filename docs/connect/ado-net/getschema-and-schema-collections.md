---
title: 取得結構描述與結構描述集合
description: 描述如何使用 GetSchema 方法，從資料庫中擷取及限制結構描述資訊。
ms.date: 11/26/2020
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 113ff460017cb5fabddd4763b28294ef6f19954c
ms.sourcegitcommit: 2add15a99df7b85d271adb261523689984dfd134
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051280"
---
# <a name="get-schema-and-schema-collections"></a>取得結構描述與結構描述集合

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

Microsoft SqlClient Data Provider for SQL Server 中的 **SqlConnection** 類別會實作 **GetSchema** 方法，此方法用於擷取目前所連接資料庫的結構描述資訊，而從 **GetSchema** 方法傳回的結構描述資訊則以 <xref:System.Data.DataTable> 形式表示。 **GetSchema** 方法是一種多載方法，可提供選擇性參數，以指定要傳回的結構描述集合及限制傳回的資訊量。

## <a name="specifying-the-schema-collections"></a>指定結構描述集合

**GetSchema** 方法的第一個選擇性參數是指定為字串的集合名稱。 結構描述集合有兩種型別：通用於所有提供者的通用結構描述集合與每個提供者特有的特定結構描述集合。  

您可以藉由呼叫 **GetSchema** 方法 (不使用引數或使用結構描述集合名稱 "MetaDataCollections")，以查詢 Microsoft SqlClient Data Provider for SQL Server，以決定支援的結構描述集合清單。 這會傳回 <xref:System.Data.DataTable>，包括支援的結構描述集合清單、每個集合所支援的限制數目，以及集合所使用之識別項部分的數目。  

### <a name="retrieving-schema-collections-example"></a>擷取結構描述集合範例

下列範例示範如何使用 Microsoft SqlClient Data Provider for SQL Server <xref:Microsoft.Data.SqlClient.SqlConnection> 類別的 <xref:Microsoft.Data.SqlClient.SqlConnection.GetSchema%2A> 方法，擷取 **AdventureWorks** 範例資料庫中包含之所有資料表的結構描述資訊：  

[!code-csharp[SqlClient GetSchema#1](~/../sqlclient/doc/samples/SqlConnection_GetSchema_Tables.cs#1)]  

## <a name="see-also"></a>請參閱

- [擷取資料庫結構描述資訊](retrieving-database-schema-information.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
