---
title: DbProviderFactory
description: 說明提供者 Factory 模型並示範如何使用 `System.Data.Common` 命名空間 (Namespace) 中的基底類別 (Base Class)。
ms.date: 12/22/2020
ms.assetid: 2a8e2640-3a49-42a1-a3a9-b43026907ae1
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 1a373e7bde1e93d8dc92294f983a96de64e28ae1
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771758"
---
# <a name="dbproviderfactories"></a>DbProviderFactory

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

<xref:System.Data.Common> 命名空間 (Namespace) 提供類別 (Class)，可用於建立 <xref:System.Data.Common.DbProviderFactory> 執行個體 (Instance) 以使用特定的資料來源。 當您建立 <xref:System.Data.Common.DbProviderFactory> 執行個體並將資料提供者相關資訊傳遞給它時，`DbProviderFactory` 可以根據所提供的資訊來決定要傳回的正確強型別 (Strongly Typed) 物件。  

資料提供者 <xref:Microsoft.Data.SqlClient> 不再列於 machine.config 檔案中，但自訂提供者將會繼續列在該檔案中。  

## <a name="in-this-section"></a>本節內容  

[取得 SqlClientFactory](obtain-sqlclientfactory.md)  
示範如何從 `DbProviderFactories` 類別取得 `SqlClientFactory`，以便在 .NET 中使用特定的資料來源。  

## <a name="see-also"></a>請參閱

- [在 ADO.NET 中擷取及修改資料](retrieving-modifying-data.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
