---
title: XTP (記憶體中 OLTP) 效能計數器
description: SQL Server 提供物件和計數器，其可供效能監視器用來監視記憶體內部 OLTP 活動。
ms.custom: seo-dt-2019
ms.date: 04/06/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: performance
ms.topic: conceptual
ms.assetid: fe3cbaf4-65f4-44c5-acc6-7b735cda0c5d
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 38d44cc0c916c7e40e26b89196b21012f7b72499
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170710"
---
# <a name="sql-server-xtp-in-memory-oltp-performance-counters"></a>SQL Server XTP (記憶體中 OLTP) 效能計數器
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 提供物件和計數器，可供效能監視器用來監視記憶體中 OLTP 活動。 從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 開始，電腦上所有指定版本的 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]執行個體之間都可共用物件和計數器。  
  
 物件和計數器名稱以前都是以 *XTP* 開頭，如 **XTP 資料指標**。 現在都以 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]開頭，名稱格式如下︰  
  
-   **SQL Server** *\<version>* **XTP 資料指標**  
  
 *\<version>* 的值會是如 2016 之類。  
  
##  <a name="sql-server-xtp-performance-objects"></a><a name="SQLServerPOs"></a> SQL Server XTP 效能物件  
 下表說明 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 效能物件。  
  
|效能物件|描述|  
|------------------------|-----------------|  
|[SQL Server XTP 資料指標](../../relational-databases/performance-monitor/sql-server-xtp-cursors.md)|SQL Server XTP 資料指標效能物件包含與記憶體內 OLTP 引擎資料指標相關的計數器。 資料指標是記憶體內 OLTP 引擎用來處理 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢的低階建置組塊。 因此，您通常不會有這些指標的直接控制權。|  
|[SQL Server XTP 資料庫](../../relational-databases/performance-monitor/sql-server-xtp-databases.md)|SQL Server XTP 資料庫效能物件提供記憶體內部 OLTP 資料庫特定的計數器。|  
|[SQL Server XTP 記憶體回收](../../relational-databases/performance-monitor/sql-server-xtp-garbage-collection.md)|SQL Server XTP 記憶體回收效能物件包含與記憶體內部 OLTP 引擎記憶體回收行程相關的計數器。|  
|[SQL Server 2016 XTP IO 管理員](../../relational-databases/performance-monitor/sql-server-xtp-io-governor.md)|SQL Server XTP IO 管理員效能物件包含與記憶體內部 OLTP IO 速率管理員相關的計數器。|
|[SQL Server XTP 虛設項目處理器](../../relational-databases/performance-monitor/sql-server-xtp-phantom-processor.md)|SQL Server XTP 虛設項目處理器效能物件包含與記憶體內部 OLTP 引擎的虛設項目處理子系統相關的計數器。 此元件負責偵測在 SERIALIZABLE 隔離等級執行之交易中的虛設項目列。|  
|[SQL Server XTP 儲存體](../../relational-databases/performance-monitor/sql-server-xtp-storage.md)|SQL Server XTP 儲存效能物件包含與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]中記憶體內部 OLTP 儲存體相關的計數器。|  
|[SQL Server XTP 交易記錄](../../relational-databases/performance-monitor/sql-server-xtp-transaction-log.md)|SQL Server XTP Transactions Log 效能物件包含與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]中記憶體內部 OLTP 交易記錄相關的計數器。|  
|[SQL Server XTP 交易](../../relational-databases/performance-monitor/sql-server-xtp-transactions.md)|SQL Server XTP Transactions 效能物件包含與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]中記憶體內部 OLTP 引擎交易相關的計數器。|  
  
  
