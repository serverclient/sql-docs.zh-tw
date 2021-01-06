---
title: 搭配可用性群組使用 FILESTREAM 和 FileTable
description: 搭配參與 Always On 可用性群組的資料庫使用 FILESTREAM 或 FileTable 的步驟。
ms.custom: seodec18
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- FileTables [SQL Server], Availability Groups
- FILESTREAM [SQL Server], Availability Groups
- Availability Groups [SQL Server], interoperability
ms.assetid: fdceda9a-a9db-4d1d-8745-345992164a98
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 566c50867511e9e9d02996247c3efecf634b93aa
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97644118"
---
# <a name="use-filestream-and-filetable-with-always-on-availability-groups"></a>搭配 Always On 可用性群組使用 FILESTREAM 和 FileTable

[!INCLUDE[sql windows only](../../../includes/applies-to-version/sql-windows-only.md)]

  本主題包含在 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 中使用 FILESTREAM 和 FileTable 功能搭配 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]的相關資訊。  
  
 支援所有 FILESTREAM 功能。 在容錯移轉之後，可讀取的次要複本以及新的主要複本上的 FILESTREAM 資料皆可供存取。  
  
 僅部分支援 FileTable 功能。 在容錯移轉之後，主要複本上的 FileTable 資料可供存取，但卻無法存取位在可讀取之次要複本上的 FileTable 資料。  
  
##  <a name="prerequisites"></a><a name="Prerequisites"></a> 必要條件  
  
-   將使用 FILESTREAM (包含或不含 FileTable) 的資料庫加入至可用性群組之前，請確定裝載可用性群組之可用性複本的每個伺服器執行個體都啟用了 FILESTREAM。 如需詳細資訊，請參閱 [Enable and Configure FILESTREAM](../../../relational-databases/blob/enable-and-configure-filestream.md)。  
  
##  <a name="using-virtual-network-names-vnns-for-filestream-and-filetable-access"></a><a name="vnn"></a> 使用虛擬網路名稱 (VNN) 進行 FILESTREAM 和 FileTable 存取  
 當您在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體上啟用 FILESTREAM 時，系統就會建立執行個體層級共用，讓您存取 FILESTREAM 資料。 您可以依照下列格式使用電腦名稱來存取這個共用：  
  
 `\\<computer_name>\<filestream_share_name>`  
  
 不過，在 AlwaysOn 可用性群組中，電腦名稱是使用虛擬網路名稱 (VNN) 來虛擬化。 當電腦是可用性群組中的主要複本，而且可用性群組中的資料庫包含 FILESTREAM 資料時，系統也會建立 VNN 範圍的共用，讓您存取 FILESTREAM 資料。 這並不會影響 FILESTREAM 資料的 Transact-SQL 存取。 不過，使用檔案系統 API 的應用程式必須使用 VNN 範圍的共用，其路徑採用下列格式：  
  
 `\\<VNN>\<filestream_share_name>`  
  
 發生下列其中一個事件時，系統就會建立這個 VNN 範圍的共用。  
  
-   您將包含 FILESTREAM 資料的資料庫加入至主要複本上的 AlwaysOn 可用性群組。 在此情況中， `\\<computer_name>\<filestream_share_name>` 共用已經存在。 系統會建立 `\\<VNN>\<filestream_share_name>` 共用。  
  
-   您在具有可用性群組的主要複本上啟用 FILESTREAM 進行檔案 i/o 資料流存取。 系統會建立下列共用：  
  
    1.  `\\<computer_name>\<filestream_share_name>`  
  
    2.  `\\<VNN1>\<filestream_share_name>` (可用性群組 1)。  
  
    3.  `\\<VNN2>\<filestream_share_name>` (可用性群組 2)。  
  
 這些 VNN 範圍的共用也會傳播至所有次要複本。  
  
 當包含 FILESTREAM 或 FileTable 資料的資料庫屬於 AlwaysOn 可用性群組時：  
  
-   FILESTREAM 和 FileTable 函數會接受或傳回虛擬網路名稱 (VNN) 而非電腦名稱。 如需有關這些函數的詳細資訊，請參閱 [Filestream and FileTable Functions &#40;Transact-SQL&#41;](../../../relational-databases/system-functions/filestream-and-filetable-functions-transact-sql.md) (Filestream 和 FileTable 函數 (Transact-SQL))。  
  
-   透過檔案系統 API 對 FILESTREAM 或 FileTable 資料進行的所有存取都應該使用 VNN 而非電腦名稱。  
  
 當資料庫屬於可用性群組的一部分時，如果您的應用程式嘗試依照 `\\<computer_name>\<filestream_share_name>` 格式使用電腦名稱來存取共用，就會引發錯誤。  
  
 當資料庫不屬於可用性群組的一部分時，如果您的應用程式嘗試使用 VNN 範圍的路徑來存取共用，則要求可能會成功。 在此情況中，虛擬網路名稱會解析成電腦名稱。 不過，強烈建議您不要使用這種方式，因為如果可用性群組已卸除，VNN 範圍的路徑將會停止運作。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [啟用及設定 FILESTREAM](../../../relational-databases/blob/enable-and-configure-filestream.md)  
  
-   [啟用 FileTable 的必要條件](../../../relational-databases/blob/enable-the-prerequisites-for-filetable.md)  
  
##  <a name="related-content"></a><a name="RelatedContent"></a> 相關內容  
 無。  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)  
  
  
