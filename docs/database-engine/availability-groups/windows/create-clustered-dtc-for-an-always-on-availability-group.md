---
title: 建立可用性群組的叢集 DTC 資源
description: 本主題將逐步引導您完成 SQL Server AlwaysOn 可用性群組之叢集 DTC 資源的組態。
ms.custom: seodec18
ms.date: 08/30/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
ms.assetid: 0e332aa4-2c48-4bc4-a404-b65735a02cea
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: c976a81277f18f89486343af32c96509f8383fda
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643266"
---
# <a name="create-clustered-dtc-resource-for-an-always-on-availability-group"></a>建立 Always On 可用性群組的叢集 DTC 資源

[!INCLUDE[sql windows only](../../../includes/applies-to-version/sql-windows-only.md)]

本主題將逐步引導您完成 SQL Server AlwaysOn 可用性群組之叢集 DTC 資源的組態。 完整組態最多可能需要一小時才能完成。 

本逐步解說會配合[針對 SQL Server 可用性群組叢集化 DTC](../../../database-engine/availability-groups/windows/cluster-dtc-for-sql-server-2016-availability-groups.md) 中的需求，來建立叢集 DTC 資源和 SQL Server 可用性群組。

本逐步解說使用 PowerShell 和 Transact-SQL (T-SQL) 指令碼。  許多 T-SQL 指令碼必須啟用 **SQLCMD 模式** 。  如需 **SQLCMD 模式** 的詳細資訊，請參閱 [在查詢編輯器中啟用 SQLCMD 指令碼](../../../ssms/scripting/edit-sqlcmd-scripts-with-query-editor.md)。  您必須匯入 PowerShell 模組 **FailoverClusters** 。  如需匯入 PowerShell 模組的詳細資訊，請參閱[匯入 PowerShell 模組](/powershell/scripting/developer/module/importing-a-powershell-module)。  本逐步解說具有下列基本原則：
- 已符合 [AlwaysOn 可用性群組的必要條件、限制和建議 (SQL Server)](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md) 中的所有需求。  
- 網域為 `contoso.lab`。
- 使用者在即將建立 DTC 網路名稱資源的 OU 中具有建立電腦物件權限。
- 使用者是具有叢集中所有節點之系統管理員權限的網域使用者。
- 已為備份建立稱為 `sqlbackups` 的檔案共用。
- SQL Server 的預設執行個體名稱為 `SQLNODE1` 和 `SQLNODE2`。
- 所有 SQL Server 執行個體上都會使用相同的服務帳戶。
- 使用者是所有 SQL Server. 執行個體上之固定 SQL Server 系統管理員角色的成員。
- DTC 無法解析之交易的預設結果將設定為 `presume commit`。
- 鏡像端點將使用通訊埠 `5022`。
- 不存在其他任何可用性群組或叢集 DTC 資源。
- 叢集詳細資料 (現有)：
  - 名稱：`Cluster`
  - 網路名稱：`Cluster Network 1`
  - 多個節點：`SQLNODE1, SQLNODE2`
  - 共用儲存體：`Cluster Disk 3` (由 `SQLNODE1` 所擁有)
- 叢集詳細資料 (即將建立)：
  - 網路名稱資源：`DTCnet1`
  - DTC 網路名稱資源：`DTC1`
  - DTC 實體磁碟資源：`DTCDisk1`
  - DTC IP 和子網路資源︰`192.168.2.54`、`255.255.255.0`
  - DTC IP 資源：`DTCIP1`

## <a name="1-check-operating-system"></a>1.檢查作業系統
針對支援的分散式交易，[!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 必須在 Windows Server 2016 或 Windows Server 2012 R2 上執行。  針對 Windows Server 2012 R2，您必須安裝 [https://support.microsoft.com/kb/3090973](https://support.microsoft.com/kb/3090973) 上所提供 KB3090973 中的更新。  此指令碼會檢查作業系統版本，以及是否必須安裝 Hotfix 3090973。  在 `SQLNODE1` 上執行下列 PowerShell 指令碼。

```powershell  
# A few OS checks

\<#
Script: 
    1) is re-runnable 
    2) will work on any node in the cluster, and 
    3) will execute against every node in the cluster
#>

$nodes = (Get-ClusterNode).Name;
foreach ($node in $nodes) {

    # At least 2012 R2
    $os = (Get-WmiObject -class Win32_OperatingSystem -ComputerName $node).caption;
    IF($os -like "*2012 R2*" -or $os -like "*2016*")
    {
        Write-Host "$os is supported on $node.";
    }
    ELSE
    {
        Write-Host "STOP!  $os is not supported on $node.";
    }
    
    # KB 3090973
    IF($os -like "*2012 R2*")
    {
        $kb = Get-Hotfix -ComputerName $node | Where {$_.HotFixID -eq 'KB3090973'};
        IF($kb)
        {
            Write-Host "KB3090973 is installed on $node."
        }
        ELSE
        {
            Write-Host "HotFixID KB3090973 must be applied on $node.  See https://support.microsoft.com/kb/3090973 for additional information and to download the hotfix.";
        }
    }
    ELSE
    {
        Write-Host "KB3090973 is not applicable to $os on $node."
    }
}
```  
## <a name="2---configure-firewall-rules"></a>2. 設定防火牆規則
此指令碼會將防火牆設定成允許裝載可用性群組複本之每部 SQL Server 上的 DTC 流量，以及參與分散式交易之任何其他伺服器上的 DTC 流量。  此指令碼也會將防火牆設定成允許資料庫鏡像端點的連接。  在 `SQLNODE1` 上執行下列 PowerShell 指令碼。

```powershell  
# Configure Firewall

\<#
Script: 
    1) is re-runnable 
    2) will work on any node in the cluster, and 
    3) will execute against every node in the cluster
#>

$nodes = (Get-ClusterNode).Name;
foreach ($node in $nodes) {
    Get-NetFirewallRule -CimSession $node -DisplayGroup 'Distributed Transaction Coordinator' -Enabled False -ErrorAction SilentlyContinue | Enable-NetFirewallRule;
    New-NetFirewallRule -CimSession $node -DisplayName 'SQL Server Mirroring' -Description 'Port 5022 for SQL Server Mirroring' -Action Allow -Direction Inbound -Protocol TCP -LocalPort 5022 -RemotePort Any -LocalAddress Any -RemoteAddress Any;
    };
```  
## <a name="3--configure-in-doubt-xact-resolution"></a>3.設定 [不能肯定的交易解析] 
此指令碼會將未決交易的 [不能肯定的交易解析]  伺服器設定選項設定為「假設為認可」。  在 SQL Server Management Studio (SSMS) 中，對 **SQLCMD 模式** 的 `SQLNODE1` 執行下列 T-SQL 指令碼。

```sql  
/*******************************************************************
    Execute script in its entirety on SQLNODE1 in SQLCMD mode
*******************************************************************/

-- Configure in-doubt xact resolution on all SQL Server instances to presume commit
IF (SELECT CAST(value_in_use as bit) FROM sys.configurations WITH (NOLOCK) WHERE [name] = N'show advanced options') = 0
BEGIN   
    EXEC sp_configure 'show advanced options', 1;
    RECONFIGURE;
END

-- Configure the server to presume commit for in-doubt transactions.
IF (SELECT CAST(value as bit) FROM sys.configurations WITH (NOLOCK) WHERE [name] = N'in-doubt xact resolution') <> 1
BEGIN   
    EXEC sp_configure 'in-doubt xact resolution', 1;
    RECONFIGURE;
END
GO
-----------------------------------------------------------------------------

:connect SQLNODE2
IF (SELECT CAST(value_in_use as bit) FROM sys.configurations WITH (NOLOCK) WHERE [name] = N'show advanced options') = 0
BEGIN   
    EXEC sp_configure 'show advanced options', 1;
    RECONFIGURE;
END

-- Configure the server to presume commit for in-doubt transactions.
IF (SELECT CAST(value as bit) FROM sys.configurations WITH (NOLOCK) WHERE [name] = N'in-doubt xact resolution') <> 1
BEGIN   
    EXEC sp_configure 'in-doubt xact resolution', 1;
    RECONFIGURE;
END
GO
-----------------------------------------------------------------------------
```

## <a name="4-create-test-databases"></a>4.建立測試資料庫
此指令碼會在 `SQLNODE1` 上建立名為 `AG1` 的資料庫，並在 `SQLNODE2` 上建立名為 `dtcDemoAG1` 的資料庫。  在 SSMS 中，對 `SQLNODE1` SQLCMD 模式 **的** 執行下列 T-SQL 指令碼。

```sql  
/*******************************************************************
    Execute script in its entirety on SQLNODE1 in SQLCMD mode
*******************************************************************/

-- On SQLNODE1 
USE master;
SET NOCOUNT ON;

IF  EXISTS (SELECT * FROM sys.databases WHERE name = N'AG1')
BEGIN
    DROP DATABASE AG1;
END
GO

CREATE DATABASE AG1;
ALTER DATABASE AG1 SET RECOVERY FULL WITH NO_WAIT;
ALTER AUTHORIZATION ON DATABASE::AG1 to sa;
GO

USE AG1;
CREATE TABLE [dbo].[Names] (
        [Name] [varchar](64) NULL,
        [EditDate] datetime
        );

INSERT Names
VALUES ('AG1', GETDATE());
GO


-- Against SQNODE2
:connect SQLNODE2
USE master;
SET NOCOUNT ON;

IF  EXISTS (SELECT * FROM sys.databases WHERE name = N'dtcDemoAG1')
BEGIN
    DROP DATABASE dtcDemoAG1;
END
GO

CREATE DATABASE dtcDemoAG1;
ALTER DATABASE dtcDemoAG1 SET RECOVERY SIMPLE WITH NO_WAIT;
ALTER AUTHORIZATION ON DATABASE::dtcDemoAG1 to sa;
GO

USE dtcDemoAG1;
CREATE TABLE [dbo].[Names] (
        [Name] [varchar](64) NULL,
        [EditDate] datetime
        );
GO    
----------------------------------------------------------------
```
## <a name="5---create-endpoints"></a>5. 建立端點
此指令碼會建立在 TCP 通訊埠 `5022` 上接聽的端點 `AG1_endpoint`。  在 SSMS 中，對 `SQLNODE1` SQLCMD 模式 **的** 執行下列 T-SQL 指令碼。

```sql  
/**********************************************
Execute on SQLNODE1 in SQLCMD mode
**********************************************/

-- Create endpoint on server instance that hosts the primary replica:
IF NOT EXISTS (SELECT * FROM sys.database_mirroring_endpoints)
BEGIN
    CREATE ENDPOINT AG1_endpoint
    AUTHORIZATION [sa]
        STATE=STARTED 
        AS TCP (LISTENER_PORT=5022) 
        FOR DATABASE_MIRRORING (ROLE=ALL);
END
GO
-----------------------------------------------------------------------------

:connect SQLNODE2
IF NOT EXISTS (SELECT * FROM sys.database_mirroring_endpoints)
BEGIN
    CREATE ENDPOINT AG1_endpoint
    AUTHORIZATION [sa]
        STATE=STARTED 
        AS TCP (LISTENER_PORT=5022) 
        FOR DATABASE_MIRRORING (ROLE=ALL);
END
GO
-----------------------------------------------------------------------------
```

## <a name="6---prepare-databases-for-availability-group"></a>6. 準備可用性群組的資料庫
此指令碼會在 `SQLNODE1` 上備份 `AG1`，並將它還原至 `SQLNODE2`。  在 SSMS 中，對 `SQLNODE1` SQLCMD 模式 **的** 執行下列 T-SQL 指令碼。

```sql  
/*******************************************************************
    Execute script in its entirety on SQLNODE1 in SQLCMD mode
*******************************************************************/

-- Backup database
BACKUP DATABASE AG1 
TO DISK = N'\\sqlnode1\sqlbackups\AG1.bak' 
WITH FORMAT, STATS = 10;

-- Backup transaction log
BACKUP LOG AG1
TO DISK = N'\\sqlnode1\sqlbackups\AG1_Log.bak'
WITH FORMAT, STATS = 10;
GO


-- Restore database and logs on secondary WITH NORECOVERY
:connect SQLNODE2
USE [master]
RESTORE DATABASE AG1 
FROM DISK = N'\\sqlnode1\sqlbackups\AG1.bak' 
WITH NORECOVERY, STATS = 10;

RESTORE LOG AG1 
FROM DISK = N'\\sqlnode1\sqlbackups\AG1_Log.bak'
WITH NORECOVERY, STATS = 10;
GO
```

## <a name="7---create-availability-group"></a>7. 建立可用性群組
[!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 必須使用 **CREATE AVAILABILITY GROUP** 命令和 **WITH DTC_SUPPORT = PER_DB** 子句建立。  您目前無法改變現有可用性群組。  [新增可用性群組精靈] 不允許您啟用新可用性群組的 DTC 支援。  下列指令碼會建立新的可用性群組，並加入次要。  在 SSMS 中，對 `SQLNODE1` SQLCMD 模式 **的** 執行下列 T-SQL 指令碼。

```sql  
/*******************************************************************
    Execute script in its entirety on SQLNODE1 in SQLCMD mode
*******************************************************************/

--  Create Availability Group on SQLNODE1 
USE master;
CREATE AVAILABILITY GROUP DTCAG1
WITH (DTC_SUPPORT = PER_DB) 
FOR DATABASE AG1
REPLICA ON 
  'SQLNODE1' WITH
     (
     ENDPOINT_URL = 'TCP://SQLNODE1.contoso.lab:5022', 
     AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
     FAILOVER_MODE = MANUAL
     ),
  'SQLNODE2' WITH 
     (
     ENDPOINT_URL = 'TCP://SQLNODE2.contoso.lab:5022',
     AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
     FAILOVER_MODE = MANUAL
     ); 
GO


-- SQLNODE2
-- Join secondary replica to the Availability Group 
:connect SQLNODE2
ALTER AVAILABILITY GROUP DTCag1 JOIN;

-- Join database to the Availability Group
ALTER DATABASE AG1 SET HADR AVAILABILITY GROUP = DTCAG1;
GO
```

> [!IMPORTANT]
> 您無法在現有的 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)] 上啟用 DTC。  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 會接受現有可用性群組的下列語法：  
> 
> USE master;    
> ALTER AVAILABILITY GROUP \<availability_group\>  
> SET (DTC_Support = Per_DB)  
> 
> 不過，實際上不會變更任何組態。  您可以使用下列 T-SQL 查詢確認 **dtc_support** 組態：  
> 
> SELECT name, dtc_support FROM sys.availability_groups  
> 
> 在可用性群組上啟用 DTC 支援的唯一方法是使用 Transact-SQL 來建立可用性群組。
 
## <a name="8--prepare-cluster-resources"></a><a name="ClusterDTC"></a>8.準備叢集資源

此指令碼會準備 DTC 相依資源：磁碟和 IP。  共用儲存體會加入 Windows 叢集。  這會建立網路資源，然後建立 DTC 並將其設為可用性群組的資源。  在 `SQLNODE1` 上執行下列 PowerShell 指令碼。 感謝 [Allan Hirt](https://sqlha.com/2013/03/12/how-to-properly-configure-dtc-for-clustered-instances-of-sql-server-with-windows-server-2008-r2/) 提供此指令碼！

```powershell  
# Create a clustered Microsoft Distributed Transaction Coordinator properly in the resource group with SQL Server

\<#----------------------------------- Begin User Input -----------------------------------#>
$AGgrp = "DTCag1";                          # Name of the WSFC resource group that will contain the DTC resource

$WSFC = (Get-Cluster).Name;                 # Windows Failover Cluster name
$DTCnetwk = "Cluster Network 1"             # WSFC Network to use for the DTC IP address

$ClusterAvailableDisk = "Cluster Disk 3";   # Designated disk that can support failover clustering and is visible to all nodes, but not yet part of the set of clustered disks
$DTCdisk = "DTCDisk1";                      # Name of the disk to be used with DTC

$DTCipresnm = "DTCIP1";                     # WSFC Friendly Name of the DTC's IP resource 
$DTCipaddr = "192.168.2.54";                # IP address of the DTC resource 
$DTCsubnet = "255.255.255.0";               # Subnet for the DTC IP address 
$DTCnetnm = "DTCNet1";                      # WSFC Friendly Name of the Network Name resource
$DTCresnm = "DTC1";                         # Name of the WSFC DTC Network Name resource; Name must be unique in AD
\<#------------------------------------ End User Input ------------------------------------#>


# Make a new disk available for use in a failover cluster.
Get-ClusterAvailableDisk | Where {$_.Name -eq $ClusterAvailableDisk} | Add-ClusterDisk;

# Rename disk
$resource = Get-ClusterResource $ClusterAvailableDisk; $resource.Name = $DTCdisk;

# Create the IP resource
Add-ClusterResource -Name $DTCipresnm -ResourceType "IP Address" -Group $AGgrp;

# Set the network to use, IP address, and subnet
# All three have to be configured at the same time
$DTCIPres = Get-ClusterResource $DTCipresnm;
$ntwk = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $DTCipres,Network,$DTCnetwk;
$ipaddr = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $DTCipres,Address,$DTCipaddr;
$subnet = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $DTCipres,SubnetMask,$dtcsubnet;

$setdtcipparams = $ntwk,$ipaddr,$subnet;
$setdtcipparams | Set-ClusterParameter;

# Create the Network Name resource
Add-ClusterResource $DTCnetnm -ResourceType "Network Name" -Group $AGgrp;

# Set the value for the Network Name resource
Get-ClusterResource $DTCnetnm | Set-ClusterParameter DnsName $DTCresnm;

# Add the IP address as a depenency of the Network Name resource
Add-ClusterResourceDependency $DTCnetnm $DTCipresnm;

# Create the Distributed Transaction Coordinator resource
Add-ClusterResource $DTCresnm -ResourceType "Distributed Transaction Coordinator" -Group $AGgrp;

# Add the Network Name as a depenency of the DTC resource
Add-ClusterResourceDependency $DTCresnm $DTCnetnm;

# Move the disk into the resource group with SQL Server
Move-ClusterResource -Name $DTCdisk -Group $AGgrp;

# Add the disk as a depenency of the DTC resource
Add-ClusterResourceDependency $DTCresnm $DTCdisk;

# Bring the IP resource online
Start-ClusterResource $DTCipresnm;

# Bring the Network Name resource online
Start-ClusterResource $DTCnetnm;

# Bring the DTC resource online
Start-ClusterResource $DTCresnm;
```  

## <a name="9---enable-network-dtc"></a>9. 啟用網路 DTC 

下列指令碼會啟用叢集 DTC 服務的網路 DTC 存取，以允許透過網路將遠端電腦登錄在分散式交易中。  在 `SQLNODE1` 上執行下列 PowerShell 指令碼。

```powershell  
# Enable Network DTC access for the clustered DTC service

\<#
Script: 
    1) is re-runnable 
    2) will work on any node in the cluster, and 
    3) will execute against every node in the cluster
#>

# Enter Name of DTC resource

$DtcName = "DTC1";
\<# ------- End of User Input ------- #>

[bool]$restart = 0;
$node = (Get-ClusterResource -Name $DtcName).OwnerNode.Name;
$DtcSettings = Get-DtcNetworkSetting -DtcName $DtcName;

IF ($DtcSettings.InboundTransactionsEnabled -eq $false)   
{
    Set-DtcNetworkSetting -CimSession $node -DtcName $DtcName -AuthenticationLevel "Mutual" -InboundTransactionsEnabled $true -Confirm:$false;
    $restart = 1;
}

IF ($DtcSettings.OutboundTransactionsEnabled -eq $false)   
{
    Set-DtcNetworkSetting -CimSession $node -DtcName $DtcName -AuthenticationLevel "Mutual" -OutboundTransactionsEnabled $true -Confirm:$false;
    $restart = 1;
}

IF ($restart -eq 1)
{
    Stop-Dtc -CimSession $node -DtcName $DTCname -Confirm:$false;
    Start-Dtc -CimSession $node -DtcName $DTCname;
}
```  

## <a name="10--disable-and-stop-the-local-dtc-service-on-each-node"></a>10.停用並停止每個節點上的本機 DTC 服務

為了確保分散式交易使用叢集 DTC 資源，請停用這兩個節點上的本機 DTC。  下列指令碼將停用並停止每個節點上的本機 DTC 服務。  在 `SQLNODE1` 上執行下列 PowerShell 指令碼。
```powershell  
# Disable local DTC service

\<#
Script: 
    1) is re-runnable 
    2) will work on any node in the cluster, and 
    3) will execute against every node in the cluster
#>

$DTCname = 'Local';
$nodes = (Get-ClusterNode).Name;

 foreach ($node in $nodes) {

    $service = Get-WmiObject -class Win32_Service -computername $node -Filter "Name='MSDTC'";
    IF ($service.StartMode -ne 'Disabled')
    {
        $service.ChangeStartMode('Disabled');
    }
    
    IF ($service.State -ne 'Stopped')
    {
        $service.StopService();
    }
}
```  

## <a name="11--cycle-the-ssnoversion-service-for-each-instance"></a>11.針對每個執行個體輪流使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務

完全設定叢集 DTC 服務之後，您必須停止並重新啟動可用性群組中的每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體，以確定 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 已註冊使用這項 DTC 服務。

[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務第一次需要分散式交易時，它會向 DTC 服務註冊。 SQL Server 服務將繼續使用該 DTC 服務，直到重新啟動為止。 如果有叢集 DTC 服務可供使用，[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 會向叢集 DTC 服務註冊。 如果沒有叢集 DTC 服務可供使用，[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 會向本機 DTC 服務註冊。 若要確認 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 向叢集 DTC 服務註冊，請停止並重新啟動每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。 

請依照下列 T-SQL 指令碼中包含的步驟進行：
```sql  
/*
Gracefully cycle the SQL Server service and failover the Availability Group
    a.  On SQLNODE2, cycle the SQL Server service from SQL Server Configuration Manger

    b.  On SQLNODE2 failover the Availability Group to SQLNODE2
        Execute T-SQL script, below, on SQLNODE2 (Use Results to Text)

    c.  On SQLNODE1, cycle the SQL Server service from SQL Server Configuration Manger

    d.  On SQLNODE1 failover the Availability Group to SQLNODE1 once the databases are back in sync.
        Execute T-SQL script, below, on SQLNODE1 (Use Results to Text)
*/

SET NOCOUNT ON;

-- Ensure replica is secondary
IF (
SELECT rs.is_primary_replica 
    FROM sys.availability_groups ag
    JOIN sys.dm_hadr_database_replica_states rs
    ON  ag.group_id = rs.group_id
    WHERE ag.name = N'DTCag1'
    AND rs.is_local = 1) = 0
BEGIN
    -- Wait for SYNCHRONIZED state
    DECLARE @ctr tinyint = 0;
    declare @msg varchar(128);
    WHILE (SELECT synchronization_state 
        FROM sys.availability_groups ag
        JOIN sys.dm_hadr_database_replica_states rs
        ON  ag.group_id = rs.group_id
        WHERE ag.name = N'DTCag1'
        AND rs.is_primary_replica = 0
        AND rs.is_local = 1) <> 2
    BEGIN
        WAITFOR DELAY '00:00:01'
        SET @ctr += 1
        SET @msg = 'Waiting for databases to become synchronized. Duration in seconds: ' + cast(@ctr AS varchar(3))
        RAISERROR (@msg, 0, 1) WITH NOWAIT
    END

    ALTER AVAILABILITY GROUP DTCAG1 FAILOVER;
    SELECT 'Failover complete' AS [Sucess]
END
ELSE BEGIN
    SELECT 'This instance is the primary replica.  Connect to the secondary replica and try again.' AS [Error]
END

```

## <a name="12--test-configuration"></a>12.測試組態

這項測試使用從 `SQLNODE1` 連至 `SQLNODE2` 的已連結伺服器，來建立分散式交易。  確定可用性群組的主要複本位於 `SQLNODE1`。 若要測試組態，您將會：

- 建立連結的伺服器
- 執行分散式交易

### <a name="create-linked-servers"></a>建立連結的伺服器  
下列指令碼會在 `SQLNODE1`上建立兩個連結的伺服器。  在 SSMS 中，對 `SQLNODE1`執行下列 T-SQL 指令碼。

```sql  
-- SQLNODE1
IF NOT EXISTS (SELECT * FROM sys.servers where name = N'SQLNODE1')
BEGIN
    EXEC master.dbo.sp_addlinkedserver @server = N'SQLNODE1';   
END

IF NOT EXISTS (SELECT * FROM sys.servers where name = N'SQLNODE2')
BEGIN
    EXEC master.dbo.sp_addlinkedserver @server = N'SQLNODE2';   
END
 ```

### <a name="execute-a-distributed-transaction"></a>執行分散式交易
此指令碼會先傳回目前的 DTC 交易統計資料，  再使用 `SQLNODE1` 和 `SQLNODE2`上的資料庫來執行分散式交易。  然後，指令碼會再次傳回 DTC 交易統計資料，其計數現在應該會增加。  實際連接到 `SQLNODE1` ，並在 SSMS 中，對 `SQLNODE1` SQLCMD 模式 **的** 執行下列 T-SQL 指令碼。

```sql  
/*******************************************************************
    Execute script in its entirety on SQLNODE1 in SQLCMD mode
    Must be physically connected to SQLNODE1
*******************************************************************/

USE AG1;
SET NOCOUNT ON;

-- Get Baseline
!! Powershell; $DtcNameC = Get-DtcClusterDefault; Get-DtcTransactionsStatistics -DtcName $DtcNameC;

SET XACT_ABORT ON
BEGIN DISTRIBUTED TRANSACTION
    INSERT INTO SQLNODE1.[AG1].[dbo].[Names] VALUES ('TestValue1', GETDATE());
    INSERT INTO SQLNODE2.[dtcDemoAG1].[dbo].[Names] VALUES ('TestValue2', GETDATE());
COMMIT TRAN
GO

-- Review DTC Transaction Statistics
!! Powershell; $DtcNameC = Get-DtcClusterDefault; Get-DtcTransactionsStatistics -DtcName $DtcNameC;
```

> [!IMPORTANT]
> 您必須執行 `USE AG1` 陳述式，確保資料庫內容已設定為 `AG1`。  否則，您會收到下列錯誤訊息：「交易內容正由另一個工作階段使用中」。