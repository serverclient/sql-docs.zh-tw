---
title: 可用性群組的資源 DLL 健康情況診斷記錄
description: 描述 SQL Server 資源 DLL 如何監視 Always On 可用性群組的健全狀況。
ms.custom: seo-lt-2019
ms.date: 06/13/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
ms.assetid: c1862d8a-5f82-4647-a280-3e588b82a6dc
author: rothja
ms.author: jroth
ms.openlocfilehash: 72a023d4627d348421b392e8f787914edc986505
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643707"
---
# <a name="sql-server-resource-dll-health-diagnostic-logs-for-availability-groups"></a>可用性群組的 SQL Server 資源 DLL 健全狀況診斷記錄
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  由 Windows Server 容錯移轉叢集 (WSFC) 叢集執行的 SQL Server 資源 DLL 會使用 SQL Server 執行個體中稱為 [sp_server_diagnostics](~/relational-databases/system-stored-procedures/sp-server-diagnostics-transact-sql.md) 的預存程序，來監視主要可用性複本的健康情況。  
  
 SQL Server 資源 DLL 會維持與 SQL Server 執行個體的專用開啟連線，SQL Server 執行個體可透過該連線定期將詳細健康情況診斷傳送到 SQL Server 資源 DLL。 結合健康情況診斷和叢集中在可用性群組資源內設定的容錯移轉原則 (FailoverConditionLevel 屬性)，可供叢集用來判斷要重新啟動或是要對可用性群組資源進行容錯移轉。 SQL Server 2012 和更新版本執行個體使用此預存程序傳送活動訊號到 WSFC 叢集，它比 SQL Server 2008 R2 或舊版中的更為繫為細微且可靠，因為它使用查詢 `SELECT @@SERVERNAME` 建立與執行個體的定期連線。 然後您可以設定可用性群組 FailureConditonLevel 屬性，以控制觸發容錯移轉的條件。  
  
 **使用 SQL Server 容錯移轉叢集診斷記錄**
 
 SQL Server 資源 DLL 從 sp_server_diagnostics 接收到的所有健康情況診斷，都自動儲存到 SQL Server 執行個體的預設 [Log] 目錄中 (%PROGRAMFILES%\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Log)。 這些記錄檔稱為 SQLDIAG 記錄檔，且以 XEL (擴充事件) 檔案格式儲存。 這些檔案在 SQL Server 的 [Log] 目錄中具有下列格式：\<HOSTNAME>_\<INSTANCENAME>_SQLDIAG_X_XXXXXXXXX.xel。 藉由查看 SQLDIAG 記錄檔，您或許可以判斷可用性群組資源失敗或容錯移轉事件的根本原因。  
  
 若要檢視 SQLDIAG 記錄檔，請將 .xel 檔案拖曳至 SQL Server Management Studio 中。  
  
  
