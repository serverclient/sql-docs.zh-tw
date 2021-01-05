---
description: MSSQLSERVER_5120
title: MSSQLSERVER_5120
ms.custom: ''
ms.date: 07/25/2020
ms.prod: sql
ms.reviewer: ''
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 5120 (Database Engine error)
ms.assetid: ''
author: PijoCoder
ms.author: mathoma
ms.openlocfilehash: 331c35a269d63f9f71c3d0fce59485bd08b88df4
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97638757"
---
# <a name="mssqlserver_5120"></a>MSSQLSERVER_5120
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  
## <a name="details"></a>詳細資料  
  
| 屬性 | 值 |  
| :-------- | :---- |  
|產品名稱|SQL Server|  
|事件識別碼|5120|  
|事件來源|MSSQLSERVER|  
|元件|SQLEngine|  
|符號名稱|DSK_FCB_FAILURE|  
|訊息文字|資料表錯誤：無法開啟實體檔案 "%.*ls"。 作業系統錯誤 %d: "%ls"。|  
  
## <a name="explanation"></a>說明  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 無法開啟資料庫檔案。  訊息中所提供的作業系統錯誤能更明確地指出造成失敗的根本原因。 您通常會和其他錯誤 (例如 [17204](mssqlserver-17204-database-engine-error.md) 或 [17207](mssqlserver-17207-database-engine-error.md)) 一起看見此錯誤。
  
## <a name="user-action"></a>使用者動作  
  
  診斷並更正作業系統錯誤，然後重試此作業。 有多個狀態可協助 Microsoft 縮小產品中正在發生錯誤的區域。 
  
### <a name="access-is-denied"></a>存取遭拒 
如果您接收到 `Access is Denied` 作業系統錯誤 = 5，請考慮這些方法：
   -  透過在 Windows 檔案總管中查看檔案的內容，來檢查針對檔案所設定的權限。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會使用 Windows 群組來佈建各種檔案資源上的存取控制。 確定適當的群組 [具有如 SQLServerMSSQLUser$ComputerName$MSSQLSERVER 或 SQLServerMSSQLUser$ComputerName$InstanceName 的名稱] 針對錯誤訊息中所述的資料庫檔案具有必要的權限。 如需詳細資料，請檢閱[設定資料庫引擎對檔案系統的存取權限](/previous-versions/sql/2014/database-engine/configure-windows/configure-file-system-permissions-for-database-engine-access?view=sql-server-2014&preserve-view=true)。 確定 Windows 群組確實包含 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務啟動帳戶或服務 SID。
   -  檢閱正在執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務的使用者帳戶。 您可以使用 Windows 工作管理員來取得此資訊。 尋找可執行檔 "sqlservr.exe" 的 [使用者名稱]。 此外，如果您最近已變更 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶，請記住執行此作業的支援方式是使用 [SQL Server 組態管理員](../sql-server-configuration-manager.md)公用程式。 
   -  取決於作業的類型 (在伺服器啟動時開啟資料庫、附加資料庫、資料庫還原等)，用來進行模擬和存取資料庫檔案的帳戶可能會有所不同。 請檢閱[保護資料和記錄檔](/previous-versions/sql/sql-server-2008-r2/ms189128(v=sql.105))主題，以了解哪個作業會設定哪一種權限，以及會針對哪些帳戶設定。 使用如 Windows SysInternals [Process Monitor](/sysinternals/downloads/procmon) \(英文\) 之類的工具，來了解檔案存取是否正在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體服務啟動帳戶 [或服務 SID] 或模擬帳戶的資訊安全內容底下發生。

      如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 正在模擬執行 ALTER DATABASE 或 CREATE DATABASE 作業其登入的使用者認證，則會在 Process Monitor 工具 (作為範例) 中注意到下列資訊。
      
        ```
        Date & Time:      3/27/2010 8:26:08 PM
        Event Class:        File System
        Operation:          CreateFile
        Result:                ACCESS DENIED
        Path:                  C:\Program Files\Microsoft SQL Server\MSSQL13.SQL2016\MSSQL\DATA\attach_test.mdf
        TID:                   4288
        Duration:             0.0000366
        Desired Access:Generic Read/Write
        Disposition:        Open
        Options:            Synchronous IO Non-Alert, Non-Directory File, Open No Recall
        Attributes:          N
        ShareMode:       Read
        AllocationSize:   n/a
        Impersonating: DomainName\UserName
        ```
  
  
### <a name="attaching-files-that-reside-on-a-network-attached-storage"></a>附加位於網路連接儲存裝置上的檔案  
如果無法重新附加位於網路連接儲存裝置上的資料庫，則這類訊息可能會記錄於應用程式記錄檔中。

`Msg 5120, Level 16, State 101, Line 1 Unable to open the physical file "\\servername\sharename\filename.mdf". Operating system error 5: (Access is denied.).`

發生此問題是因為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在中斷連結資料庫時會重設檔案權限。 在嘗試重新附加資料庫時，因為共用權限受到限制，所以會發生失敗。

若要解決此問題，請遵循下列步驟：
1. 使用 -T 啟動選項來啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 使用此啟動選項來開啟 [SQL Server 組態管理員](../sql-server-configuration-manager.md)中的追蹤旗標 1802 (如需 1802 的詳細資訊，請參閱[追蹤旗標](../../t-sql/database-console-commands/dbcc-traceon-transact-sql.md))。 如需如何變更啟動參數的詳細資訊，請參閱[資料庫引擎服務啟動選項](../../database-engine/configure-windows/database-engine-service-startup-options.md)。

2. 使用下列命令來中斷連結資料庫。
   ```sql
    exec sp_detach_db DatabaseName
    go 
   ```

3. 使用下列命令來重新附加資料庫。
   ```sql
   exec sp_attach_db DatabaseName, '\\Network-attached storage_Path\DatabaseMDFFile.mdf', '\\Network-attached storage_Path\DatabaseLDFFile.ldf'
   go
   ```
