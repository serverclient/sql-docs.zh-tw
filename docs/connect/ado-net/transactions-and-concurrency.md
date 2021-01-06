---
title: 異動和並行存取
description: 描述如何將 Microsoft SqlClient Data Provider for SQL Server 用於交易和並行存取。
ms.date: 11/24/2020
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 2a00ef1ec1f2f5d8ee892289021f42cb139b5a12
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771304"
---
# <a name="transactions-and-concurrency"></a>異動和並行存取

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

異動是由單一命令或當做封裝 (Package) 執行的命令群組所組成。 交易可讓您將多項作業結合成單一工作單位。 如果異動的某一處失敗，則所有更新都會復原到異動之前的狀態。

異動必須符合 ACID 屬性 (單元性 (Atomicity)、一致性 (Consistency)、隔離性 (Isolation) 和持續性 (Durability)，才能保證資料一致性。 大多數關聯式資料庫系統 (例如 Microsoft SQL Server) 都可以支援異動，其方法是在每次用戶端應用程式執行更新、插入或刪除作業時，提供鎖定、記錄和異動管理功能。

> [!NOTE]
> 如果鎖定保留時間太久，包含多個資源的交易可能會降低並行。 因此，請盡可能縮短異動的時間長度。  

如果某筆異動包含相同資料庫或伺服器中的多個資料表，則預存程序 (Stored Procedure) 中的明確異動通常會有較佳的效能。 您可以使用 Transact-SQL `BEGIN TRANSACTION`、`COMMIT TRANSACTION` 和 `ROLLBACK TRANSACTION` 陳述式，在 SQL Server 預存程序中建立交易。 如需詳細資訊，請參閱《SQL Server 線上叢書》。

包含不同資源管理員的交易 (例如 SQL Server 與 Oracle 之間的交易) 需要分散式交易。

## <a name="in-this-section"></a>本節內容

[本機交易](local-transactions.md)  
示範如何針對資料庫執行交易。  
  
[分散式交易](distributed-transactions.md)  
說明如何在 ADO.NET 中執行分散式異動。  
  
[System.Transactions 與 SQL Server 的整合](system-transactions-integration-with-sql-server.md)  
說明 <xref:System.Transactions> 如何與 SQL Server 整合以使用分散式交易。  
  
[開放式並行存取](optimistic-concurrency.md)描述開放式和封閉式並行存取，以及您如何測試並行存取違規。  

## <a name="see-also"></a>請參閱

- [交易基本概念](/dotnet/framework/data/transactions/transaction-fundamentals)
- [連線到資料來源](connecting-to-data-source.md)
- [命令與參數](commands-parameters.md)
- [DataAdapter 和 DataReader](dataadapters-datareaders.md)
- [DbProviderFactory](dbproviderfactories.md)
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
