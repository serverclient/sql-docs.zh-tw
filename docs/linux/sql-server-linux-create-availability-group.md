---
title: 為 Linux 上的 SQL Server 建立和設定可用性群組
description: 此教學課程說明如何針對 Linux 上的 SQL Server 建立和設定可用性群組，以及建立可用性群組端點和憑證。
author: VanMSFT
ms.author: vanto
ms.reviewer: vanto
ms.date: 06/28/2018
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.openlocfilehash: ecb634a770b4990bfbbc094b15498b03bdea4a57
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172000"
---
# <a name="create-and-configure-an-availability-group-for-sql-server-on-linux"></a>為 Linux 上的 SQL Server 建立和設定可用性群組

[!INCLUDE [SQL Server - Linux](../includes/applies-to-version/sql-linux.md)]

本教學課程涵蓋如何為 Linux 上的 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 建立和設定可用性群組 (AG)。 不同於 Windows 上的 [!INCLUDE[sssql15-md](../includes/sssql16-md.md)] 和更早版本，不論是否先建立基礎 Pacemaker 叢集，您都可以啟用 AG。 如果需要整合叢集，則會在稍後才進行。

本教學課程包含下列工作：
 
> [!div class="checklist"]
> * 啟用可用性群組。
> * 建立可用性群組端點和憑證。
> * 使用 [!INCLUDE[ssmanstudiofull-md](../includes/ssmanstudiofull-md.md)] (SSMS) 或 Transact-SQL 來建立可用性群組。
> * 建立 Pacemaker 的 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 登入和權限。
> * 在 Pacemaker 叢集 (僅限外部類型) 中建立可用性群組。

## <a name="prerequisite"></a>必要條件
- 部署 Pacemaker 高可用性叢集，如[為 Linux 上的 SQL Server 部署 Pacemaker 叢集](sql-server-linux-deploy-pacemaker-cluster.md)中所述。


## <a name="enable-the-availability-groups-feature"></a>啟用可用性群組功能

不同於在 Windows 上，您無法使用 PowerShell 或 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] Configuration Manager 來啟用可用性群組 (AG) 功能。 在 Linux 底下，您必須使用 `mssql-conf` 來啟用此功能。 有兩種方式可啟用可用性群組功能：使用 `mssql-conf` 公用程式，或手動編輯 `mssql.conf` 檔案。

> [!IMPORTANT]
> 即使在 [!INCLUDE[ssexpress-md](../includes/ssexpress-md.md)] 上，也必須針對僅限設定複本啟用 AG 功能。

### <a name="use-the-mssql-conf-utility"></a>使用 mssql-conf 公用程式

在命令提示字元中，發出下列命令：

```bash
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
```

### <a name="edit-the-mssqlconf-file"></a>編輯 mssql.conf 檔案

您也可以修改位在 `/var/opt/mssql` 檔案夾下的 `mssql.conf` 檔案，新增下列幾行：

```
[hadr]

hadr.hadrenabled = 1
```

### <a name="restart-ssnoversion-md"></a>重新啟動 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)]
如同在 Windows 上，啟用可用性群組之後必須重新啟動 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)]。 這可以透過下列方式完成：

```bash
sudo systemctl restart mssql-server
```

## <a name="create-the-availability-group-endpoints-and-certificates"></a>建立可用性群組端點和憑證

可用性群組會使用 TCP 端點進行通訊。 在 Linux 底下，只有在使用憑證來進行驗證時，才支援 AG 的端點。 這表示來自某個執行個體的憑證，必須在相同 AG 中的所有其他將會參與複本的執行個體上還原。 即使是僅限設定複本，也需要憑證處理序。 

建立端點和還原憑證只能透過 Transact-SQL 完成。 您也可以使用非 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 產生的憑證。 您也需要一個處理序來管理和取代任何過期的憑證。

> [!IMPORTANT]
> 如果您打算使用 [!INCLUDE[ssmanstudiofull-md](../includes/ssmanstudiofull-md.md)] 精靈來建立 AG，您仍然需要在 Linux 上使用 Transact-SQL 來建立和還原憑證。

如需各種命令可用選項的完整語法 (例如，額外的安全性)，請參閱：

-   [BACKUP CERTIFICATE](../t-sql/statements/backup-certificate-transact-sql.md)
-   [CREATE CERTIFICATE](../t-sql/statements/create-certificate-transact-sql.md)
-   [CREATE ENDPOINT](../t-sql/statements/create-endpoint-transact-sql.md)

> [!NOTE]
> 雖然您將建立可用性群組，但端點類型是使用 *FOR DATABASE_MIRRORING* 的，因為某些基礎層面曾經與現在已淘汰的功能共用。

此範例會建立三節點設定的憑證。 執行個體名稱為 LinAGN1、LinAGN2 和 LinAGN3。

1.  在 LinAGN1 上執行下列命令，以建立主要金鑰、憑證和端點，以及備份憑證。 在此範例中，會針對端點使用一般的 TCP 通訊埠 5022。
    
    ```SQL
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<StrongPassword>';
    
    GO
    
    CREATE CERTIFICATE LinAGN1_Cert
    WITH SUBJECT = 'LinAGN1 AG Certificate';
    
    GO
    
    BACKUP CERTIFICATE LinAGN1_Cert
    TO FILE = '/var/opt/mssql/data/LinAGN1_Cert.cer';
    
    GO
    
    CREATE ENDPOINT AGEP
    STATE = STARTED
    AS TCP (
        LISTENER_PORT = 5022,
        LISTENER_IP = ALL)
    FOR DATABASE_MIRRORING (
        AUTHENTICATION = CERTIFICATE LinAGN1_Cert,
        ROLE = ALL);
    
    GO
    ```
    
2.  在 LinAGN2 上執行相同命令：
    
    ```SQL
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<StrongPassword>';
    
    GO
    
    CREATE CERTIFICATE LinAGN2_Cert
    WITH SUBJECT = 'LinAGN2 AG Certificate';
    
    GO
    
    BACKUP CERTIFICATE LinAGN2_Cert
    TO FILE = '/var/opt/mssql/data/LinAGN2_Cert.cer';
    
    GO
    
    CREATE ENDPOINT AGEP
    STATE = STARTED
    AS TCP (
        LISTENER_PORT = 5022,
        LISTENER_IP = ALL)
    FOR DATABASE_MIRRORING (
        AUTHENTICATION = CERTIFICATE LinAGN2_Cert,
        ROLE = ALL);
    
    GO
    ```
    
3.  最後，在 LinAGN3 上執行相同的順序：
    
    ```SQL
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<StrongPassword>';
    
    GO
    
    CREATE CERTIFICATE LinAGN3_Cert
    WITH SUBJECT = 'LinAGN3 AG Certificate';
    
    GO
    
    BACKUP CERTIFICATE LinAGN3_Cert
    TO FILE = '/var/opt/mssql/data/LinAGN3_Cert.cer';
    
    GO
    
    CREATE ENDPOINT AGEP
    STATE = STARTED
    AS TCP (
        LISTENER_PORT = 5022,
        LISTENER_IP = ALL)
    FOR DATABASE_MIRRORING (
        AUTHENTICATION = CERTIFICATE LinAGN3_Cert,
        ROLE = ALL);
    
    GO
    ```
    
4.  使用 `scp` 或其他公用程式，將憑證的備份複製到將成為 AG 一部分的每個節點。
    
    針對此範例：
    
    - 將 LinAGN1_Cert.cer 複製到 LinAGN2 和 LinAGN3
    - 將 LinAGN2_Cert.cer 複製到 LinAGN1 和 LinAGN3。
    - 將 LinAGN3_Cert.cer 複製到 LinAGN1 和 LinAGN2。
    
5.  將擁有權以及與所複製憑證檔案相關聯的群組變更為 `mssql`。
    
    ```bash
    sudo chown mssql:mssql <CertFileName>
    ```
    
6.  在 LinAGN1 上建立與 LinAGN2 和 LinAGN3 相關聯的執行個體層級登入和使用者。
    
    ```SQL
    CREATE LOGIN LinAGN2_Login WITH PASSWORD = '<StrongPassword>';
    CREATE USER LinAGN2_User FOR LOGIN LinAGN2_Login;
    
    GO
    
    CREATE LOGIN LinAGN3_Login WITH PASSWORD = '<StrongPassword>';
    CREATE USER LinAGN3_User FOR LOGIN LinAGN3_Login;
    
    GO
    ```
    
7.  在 LinAGN1 上還原 LinAGN2_Cert 和 LinAGN3_Cert。 擁有其他複本的憑證是 AG 通訊和安全性的重要層面。
    
    ```SQL
    CREATE CERTIFICATE LinAGN2_Cert
    AUTHORIZATION LinAGN2_User
    FROM FILE = '/var/opt/mssql/data/LinAGN2_Cert.cer';
    
    GO
    
    CREATE CERTIFICATE LinAGN3_Cert
    AUTHORIZATION LinAGN3_User
    FROM FILE = '/var/opt/mssql/data/LinAGN3_Cert.cer';
    
    GO
    
8.  Grant the logins associated with LinAG2 and LinAGN3 permission to connect to the endpoint on LinAGN1.
    
    ```SQL
    GRANT CONNECT ON ENDPOINT::AGEP TO LinAGN2_Login;
    
    GO
    
    GRANT CONNECT ON ENDPOINT::AGEP TO LinAGN3_Login;
    
    GO
    ```
    
9.  在 LinAGN2 上建立與 LinAGN1 和 LinAGN3 相關聯的執行個體層級登入和使用者。
    
    ```SQL
    CREATE LOGIN LinAGN1_Login WITH PASSWORD = '<StrongPassword>';
    CREATE USER LinAGN1_User FOR LOGIN LinAGN1_Login;
    
    GO
    
    CREATE LOGIN LinAGN3_Login WITH PASSWORD = '<StrongPassword>';
    CREATE USER LinAGN3_User FOR LOGIN LinAGN3_Login;
    
    GO
    ```
    
10. 在 LinAGN2 上還原 LinAGN1_Cert 和 LinAGN3_Cert。
    
    ```SQL
    CREATE CERTIFICATE LinAGN1_Cert
    AUTHORIZATION LinAGN1_User
    FROM FILE = '/var/opt/mssql/data/LinAGN1_Cert.cer';
    
    GO
    
    CREATE CERTIFICATE LinAGN3_Cert
    AUTHORIZATION LinAGN3_User
    FROM FILE = '/var/opt/mssql/data/LinAGN3_Cert.cer';
    
    GO
    ```
    
11. 授與 LinAG1 和 LinAGN3 相關聯的登入連線到 LinAGN2 上端點的權限。
    
    ```SQL
    GRANT CONNECT ON ENDPOINT::AGEP TO LinAGN1_Login;
    
    GO
    
    GRANT CONNECT ON ENDPOINT::AGEP TO LinAGN3_Login;
    
    GO
    ```
    
12. 在 LinAGN3 上建立與 LinAGN1 和 LinAGN2 相關聯的執行個體層級登入和使用者。
    
    ```SQL
    CREATE LOGIN LinAGN1_Login WITH PASSWORD = '<StrongPassword>';
    CREATE USER LinAGN1_User FOR LOGIN LinAGN1_Login;
    
    GO
    
    CREATE LOGIN LinAGN2_Login WITH PASSWORD = '<StrongPassword>';
    CREATE USER LinAGN2_User FOR LOGIN LinAGN2_Login;
    
    GO
    ```
    
13. 在 LinAGN3 上還原 LinAGN1_Cert 和 LinAGN2_Cert。 
    
    ```SQL
    CREATE CERTIFICATE LinAGN1_Cert
    AUTHORIZATION LinAGN1_User
    FROM FILE = '/var/opt/mssql/data/LinAGN1_Cert.cer';
    
    GO
    
    CREATE CERTIFICATE LinAGN2_Cert
    AUTHORIZATION LinAGN2_User
    FROM FILE = '/var/opt/mssql/data/LinAGN2_Cert.cer';
    
    GO
    ```
    
14. 授與 LinAG1 和 LinAGN2 相關聯的登入連線到 LinAGN3 上端點的權限。
    
    ```SQL
    GRANT CONNECT ON ENDPOINT::AGEP TO LinAGN1_Login;
    
    GO
    
    GRANT CONNECT ON ENDPOINT::AGEP TO LinAGN2_Login;
    
    GO
    ```

## <a name="create-the-availability-group"></a>建立可用性群組

本節涵蓋如何使用 [!INCLUDE[ssmanstudiofull-md](../includes/ssmanstudiofull-md.md)] (SSMS) 或 Transact-SQL 來建立 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 的可用性群組。

### <a name="use-ssmanstudiofull-md"></a>使用[!INCLUDE[ssmanstudiofull-md](../includes/ssmanstudiofull-md.md)]

本節顯示如何使用 SSMS 搭配 [新增可用性群組精靈]，建立叢集類型為「外部」的 AG。

1.  在 SSMS 中，展開 [Always On 高可用性]，以滑鼠右鍵按一下[可用性群組]，然後選取 [新增可用性組精靈]。

2.  在 [簡介] 對話方塊上，按 [下一步]。

3.  在 [指定可用性群組選項] 對話方塊中，輸入可用性群組的名稱，然後在下拉式清單中選取 [外部] 或 [無] 的叢集類型。 如果要部署 Pacemaker，應該使用 [外部]。 [無] 適用於特定案例，例如，讀取擴增。選取 [資料庫層級健康情況偵測] 選項是選擇性的。 如需此選項的詳細資訊，請參閱[可用性群組資料庫層級健康情況偵測容錯移轉選項](../database-engine/availability-groups/windows/sql-server-always-on-database-health-detection-failover-option.md)。 按 [下一步] 。

    ![建立可用性群組 03](./media/sql-server-linux-create-availability-group/image3.png)

4.  在 [選取資料庫] 對話方塊中，選取將參與 AG 的資料庫。 每個資料庫都必須有完整備份，才能將它加入 AG。 按 [下一步] 。

5.  在 [指定複本] 對話方塊中，按一下 [新增複本]。

6.  在 [連線至伺服器] 對話方塊中，輸入將作為次要複本 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] Linux 執行個體的名稱，以及要連線的認證。 按一下 [ **連接**]。

7.  針對將包含僅限設定複本或另一個次要複本的執行個體，重複前兩個步驟。

8.  三個執行個體現在應該都會列在 [指定複本] 對話方塊中。 如果針對將成為真次要的次要複本使用 [外部] 叢集類型，請確定 [可用性模式] 與主要複本的相符，而且容錯移轉模式設定為 [外部]。 針對僅限設定複本，選取 [僅設定] 可用性模式。

    下列範例顯示的 AG 包含兩個複本、一個「外部」類型叢集，以及一個僅限設定複本。

    ![建立可用性群組 04](./media/sql-server-linux-create-availability-group/image4.png)

    下列範例顯示的 AG 包含兩個複本、一個「無」類型叢集，以及一個僅限設定複本。

    ![建立可用性群組 05](./media/sql-server-linux-create-availability-group/image5.png)

9.  如果您想要變更備份喜好設定，請按一下 [備份喜好設定] 索引標籤。如需 AG 的備份喜好設定詳細資訊，請參閱[設定可用性複本的備份](../database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server.md)。

10. 如果針對讀取縮放使用可讀取次要複本，或建立叢集類型為「無」的 AG，您可以選取 [接聽程式] 索引標籤來建立接聽程式。您也可以稍後再新增接聽程式。 若要建立接聽程式，請選擇 [建立可用性群組接聽程式] 選項，然後輸入名稱、TCP/IP 連接埠，以及要使用靜態或自動指派的 DHCP IP 位址。 請記住，對於叢集類型為「無」的 AG，IP 應為靜態，並設定為主要的 IP 位址。

    ![建立可用性群組 06](./media/sql-server-linux-create-availability-group/image6.png)

11. 如果接聽程式是為可讀取的案例建立，SSMS 17.3 或更新版本允許在精靈中建立唯讀路由。 您也可以在稍後透過 SSMS 或 Transact-SQL 新增它。 現在新增唯讀路由：

    a.  選取 [唯讀路由] 索引標籤。

    b.  輸入唯讀複本的 URL。 這些 URL 與端點相似，不同之處在於它們使用執行個體的連接埠，而不是端點。

    c.  選取每個 URL，然後從底部選取可讀取複本。 若要複選，請按住 SHIFT 或按一下並拖曳。

12. 按 [下一步] 。

13. 選擇次要複本將如何初始化。 預設值是使用[自動植入](../database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group.md)，這需要參與 AG 的所有伺服器上都有相同路徑。 您也可以讓精靈執行備份、複製和還原 (第二個選項)；如果您已在複本上手動備份、複製及還原資料庫，請將它加入 (第三個選項)；或者，稍後再新增資料庫 (最後一個選項)。 如同憑證，如果您要手動建立備份並複製它們，則必須在其他複本上設定備份檔案上的權限。 按 [下一步] 。

14. 在 [驗證] 對話方塊中，如果所有項目都未傳回為 [成功]，請進行調查。 有些警告是可接受且不嚴重的，例如，如果您未建立接聽程式。 按 [下一步] 。

15. 在 [摘要] 頁面上，按一下 [完成]。 現在會開始建立 AG 的程序。

16. 當 AG 建立完成時，請按一下 [結果] 上的 [關閉]。 您現在可以在複本上的動態管理檢視，以及 SSMS 中的 [Always On 高可用性] 資料夾底下看到 AG。

### <a name="use-transact-sql"></a>使用 Transact-SQL

本節顯示使用 Transact-SQL 建立 AG 的範例。 建立 AG 之後，可以設定接聽程式和唯讀路由。 您可以使用 `ALTER AVAILABILITY GROUP` 來修改 AG 本身，但無法在 [!INCLUDE[sssql17-md](../includes/sssql17-md.md)] 中變更叢集類型。 如果您不是要建立叢集類型為「外部」的 AG，則必須將它刪除並重新建立為「無」的叢集類型。 如需詳細資訊和其他選項，請參閱下列連結：

-   [CREATE AVAILABILITY GROUP (Transact-SQL)](../t-sql/statements/create-availability-group-transact-sql.md)
-   [ALTER AVAILABILITY GROUP (Transact-SQL)](../t-sql/statements/alter-availability-group-transact-sql.md)
-   [設定可用性群組的唯讀路由 (SQL Server)](../database-engine/availability-groups/windows/configure-read-only-routing-for-an-availability-group-sql-server.md)
-   [建立或設定可用性群組接聽程式 (SQL Server)](../database-engine/availability-groups/windows/create-or-configure-an-availability-group-listener-sql-server.md)

#### <a name="example-one---two-replicas-with-a-configuration-only-replica-external-cluster-type"></a>範例一 - 使用僅限設定複本的兩個複本 (外部叢集類型)

此範例示範如何建立使用僅限設定複本的兩個複本 AG。

1.  在將成為主要複本 (包含資料庫的完整讀取/寫入複本) 的節點上執行。 此範例使用自動植入。

    ```SQL
    CREATE AVAILABILITY GROUP [<AGName>]
    WITH (CLUSTER_TYPE = EXTERNAL)
    FOR DATABASE <DBName>
    REPLICA ON N'LinAGN1' WITH (
       ENDPOINT_URL = N' TCP://LinAGN1.FullyQualified.Name:5022',
       FAILOVER_MODE = EXTERNAL,
       AVAILABILITY_MODE = SYNCHRONOUS_COMMIT),
    N'LinAGN2' WITH (
       ENDPOINT_URL = N'TCP://LinAGN2.FullyQualified.Name:5022',
       FAILOVER_MODE = EXTERNAL,
       AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
       SEEDING_MODE = AUTOMATIC),
    N'LinAGN3' WITH (
       ENDPOINT_URL = N'TCP://LinAGN3.FullyQualified.Name:5022',
       AVAILABILITY_MODE = CONFIGURATION_ONLY);
       
    GO
    ```
    
2.  在連線到另一個複本的查詢視窗中，執行下列命令，將複本加入至 AG，並起始從主要複本到次要複本的植入程序。
    
    ```SQL
    ALTER AVAILABILITY GROUP [<AGName>] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    
    GO
    
    ALTER AVAILABILITY GROUP [<AGName>] GRANT CREATE ANY DATABASE;
    
    GO
    ```
    
3. 在連接到僅限設定複本的查詢視窗中，將它加入 AG。
    
   ```SQL
    ALTER AVAILABILITY GROUP [<AGName>] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    
    GO
   ```

#### <a name="example-two---three-replicas-with-read-only-routing-external-cluster-type"></a>範例二 - 使用唯讀路由的三個複本 (外部叢集類型)

此範例顯示三個完整複本，以及如何將唯讀路由設定為初始 AG 建立的一部分。

1.  在將成為主要複本 (包含資料庫的完整讀取/寫入複本) 的節點上執行。 此範例使用自動植入。

    ```SQL
    CREATE AVAILABILITY GROUP [<AGName>]
    WITH (CLUSTER_TYPE = EXTERNAL)
    FOR DATABASE <DBName>
    REPLICA ON N'LinAGN1'
    WITH (
       ENDPOINT_URL = N'TCP://LinAGN1.FullyQualified.Name:5022',
       FAILOVER_MODE = EXTERNAL,
       AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
       PRIMARY_ROLE (ALLOW_CONNECTIONS = READ_WRITE, READ_ONLY_ROUTING_LIST = (('LinAGN2.FullyQualified.Name', 'LinAGN3.FullyQualified.Name'))),
       SECONDARY_ROLE (ALLOW_CONNECTIONS = ALL, READ_ONLY_ROUTING_URL = N'TCP://LinAGN1.FullyQualified.Name:1433')),
    N'LinAGN2' WITH (
       ENDPOINT_URL = N'TCP://LinAGN2.FullyQualified.Name:5022',
       FAILOVER_MODE = EXTERNAL,
       SEEDING_MODE = AUTOMATIC,
       AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
       PRIMARY_ROLE (ALLOW_CONNECTIONS = READ_WRITE, READ_ONLY_ROUTING_LIST = (('LinAGN1.FullyQualified.Name', 'LinAGN3.FullyQualified.Name'))),
       SECONDARY_ROLE (ALLOW_CONNECTIONS = ALL, READ_ONLY_ROUTING_URL = N'TCP://LinAGN2.FullyQualified.Name:1433')),
    N'LinAGN3' WITH (
       ENDPOINT_URL = N'TCP://LinAGN3.FullyQualified.Name:5022',
       FAILOVER_MODE = EXTERNAL,
       SEEDING_MODE = AUTOMATIC,
       AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
       PRIMARY_ROLE (ALLOW_CONNECTIONS = READ_WRITE, READ_ONLY_ROUTING_LIST = (('LinAGN1.FullyQualified.Name', 'LinAGN2.FullyQualified.Name'))),
       SECONDARY_ROLE (ALLOW_CONNECTIONS = ALL, READ_ONLY_ROUTING_URL = N'TCP://LinAGN3.FullyQualified.Name:1433'))
    LISTENER '<ListenerName>' (WITH IP = ('<IPAddress>', '<SubnetMask>'), Port = 1433);
    
    GO
    ```
    
    關於此設定的一些注意事項：
    
    - *AGName* 是可用性群組的名稱。
    - *DBName* 是將會與可用性群組搭配使用之資料庫的名稱。 它也可以是以逗號分隔的名稱清單。
    - *ListenerName* 是不同於任何基礎伺服器/節點的名稱。 它將會和 *IPAddress* 一起註冊在 DNS 中。
    - *IPAddress* 是與 *ListenerName* 相關聯的 IP 位址。 它也是唯一的，且不與任何伺服器/節點相同。 應用程式和使用者會使用 *ListenerName* 或 *IPAddress* 來連線到 AG。
    - *SubnetMask* 是 *IPAddress* 的子網路遮罩，例如，255.255.255.0。

2.  在連線到另一個複本的查詢視窗中，執行下列命令，將複本加入至 AG，並起始從主要複本到次要複本的植入程序。
    
    ```SQL
    ALTER AVAILABILITY GROUP [<AGName>] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    
    GO
    
    ALTER AVAILABILITY GROUP [<AGName>] GRANT CREATE ANY DATABASE;
    
    GO
    ```
    
3.  針對第三個複本重複步驟 2。

#### <a name="example-three---two-replicas-with-read-only-routing-none-cluster-type"></a>範例三 - 使用唯讀路由的兩個複本 (無叢集類型)

此範例示範如何使用「無」叢集類型建立兩個複本設定。 它用於不需要容錯移轉的讀取縮放案例。 這會使用循環配置資源功能，建立實際上是主要複本的接聽程式，以及唯讀路由。

1.  在將成為主要複本 (包含資料庫的完整讀取/寫入複本) 的節點上執行。 此範例使用自動植入。

    ```SQL
    CREATE AVAILABILITY GROUP [<AGName>]
    WITH (CLUSTER_TYPE = NONE)
    FOR DATABASE <DBName>
    REPLICA ON N'LinAGN1'
    WITH (
       ENDPOINT_URL = N'TCP://LinAGN1.FullyQualified.Name: <PortOfEndpoint>',
       FAILOVER_MODE = MANUAL,
       AVAILABILITY_MODE = ASYNCHRONOUS_COMMIT,
       PRIMARY_ROLE (ALLOW_CONNECTIONS = READ_WRITE, READ_ONLY_ROUTING_LIST = (('LinAGN1.FullyQualified.Name'.'LinAGN2.FullyQualified.Name'))),
       SECONDARY_ROLE (ALLOW_CONNECTIONS = ALL, READ_ONLY_ROUTING_URL = N'TCP://LinAGN1.FullyQualified.Name:<PortOfInstance>'));
    N'LinAGN2' WITH (
       ENDPOINT_URL = N'TCP://LinAGN2.FullyQualified.Name:<PortOfEndpoint>',
       FAILOVER_MODE = MANUAL,
       SEEDING_MODE = AUTOMATIC,
       AVAILABILITY_MODE = ASYNCHRONOUS_COMMIT,
       PRIMARY_ROLE (ALLOW_CONNECTIONS = READ_WRITE, READ_ONLY_ROUTING_LIST = (('LinAGN1.FullyQualified.Name', 'LinAGN2.FullyQualified.Name'))),
       SECONDARY_ROLE (ALLOW_CONNECTIONS = ALL, READ_ONLY_ROUTING_URL =N'TCP://LinAGN2.FullyQualified.Name:<PortOfInstance>'));
    LISTENER '<ListenerName>' (WITH IP = ('<PrimaryReplicaIPAddress>', '<SubnetMask>'), Port = <PortOfListener>);
    
    GO
    ```
    
    Where
    - *AGName* 是可用性群組的名稱。
    - *DBName* 是將會與可用性群組搭配使用之資料庫的名稱。 它也可以是以逗號分隔的名稱清單。
    - *PortOfEndpoint* 是所建立端點使用的連接埠號碼。
    - *PortOfInstance* 是 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 執行個體使用的連接埠號碼。
    - *ListenerName* 是與任何基礎複本都不同的名稱，但實際上不會使用。
    - *PrimaryReplicaIPAddress* 是主要複本的 IP 位址。
    - *SubnetMask* 是 *IPAddress* 的子網路遮罩。 例如，255.255.255.0。
    
2.  將次要複本加入 AG，並起始自動植入。
    
    ```SQL
    ALTER AVAILABILITY GROUP [<AGName>] JOIN WITH (CLUSTER_TYPE = NONE);
    
    GO
    
    ALTER AVAILABILITY GROUP [<AGName>] GRANT CREATE ANY DATABASE;
    
    GO
    ```

## <a name="create-the-ssnoversion-md-login-and-permissions-for-pacemaker"></a>建立 Pacemaker 的 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 登入和權限

Linux 上的基礎 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] Pacemaker 高可用性叢集，需要存取 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 執行個體存，以及可用性群組本身的權限。 這些步驟會建立登入和相關聯的權限，以及告訴 Pacemaker 如何登入 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 的檔案。

1.  在連線到第一個複本的查詢視窗中，執行下列命令：

    ```SQL
    CREATE LOGIN PMLogin WITH PASSWORD ='<StrongPassword>';
    
    GO
    
    GRANT VIEW SERVER STATE TO PMLogin;
    
    GO
    
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::<AGThatWasCreated> TO PMLogin;
    
    GO
    ```
    
2.  在節點 1 上，輸入命令 
    ```bash
    sudo emacs /var/opt/mssql/secrets/passwd
    ```
    
    這會開啟 Emacs 編輯器。
    
3.  在編輯器中輸入下列兩行：

    ```
    PMLogin
    <StrongPassword>
    ```
    
4.  按住 CTRL 鍵，然後按 X，再按 C，以結束並儲存檔案。

5.  執行 
    ```bash
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```
    
    以鎖定檔案。

6.  在將會作為複本的其他伺服器上，重複步驟 1-5。

## <a name="create-the-availability-group-resources-in-the-pacemaker-cluster-external-only"></a>在 Pacemaker 叢集 (僅限外部) 中建立可用性群組

在 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 中建立可用性群組之後，如果指定「外部」叢集類型，就必須在 Pacemaker 中建立對應的資源。 有兩個與 AG 相關聯的資源：AG 本身和 IP 位址。 如果您未使用接聽程式功能，則設定 IP 位址資源是選擇性的，但建議您這麼做。

所建立的 AG 資源是稱為「複製品」的特殊資源。 AG 資源基本上在每個節點上都有複本，而且有一個稱為主資源 (Master) 的控制資源。 主資源會與裝載主要複本的伺服器相關聯。 其他資源會裝載次要複本 (一般或僅限設定)，而且可以在容錯移轉時升階為主資源。

[!INCLUDE [bias-sensitive-term-t](../includes/bias-sensitive-term-t.md)]

1.  使用下列語法建立 AG 資源：

    **Red Hat Enterprise Linux (RHEL) 和 Ubuntu**
    
    ```bash
    sudo pcs resource create <NameForAGResource> ocf:mssql:ag ag_name=<AGName> meta failure-timeout=30s --master meta notify=true
    ```

    >[!NOTE]
    >在 RHEL 7.4 上，您可能會遇到使用 --master 的警告。 若要避免此狀況，請使用 `sudo pcs resource create <NameForAGResource> ocf:mssql:ag ag_name=<AGName> meta failover-timeout=30s master notify=true`
   
    **SUSE Linux Enterprise Server (SLES)**
    
    ```bash
    primitive <NameForAGResource> \
    ocf:mssql:ag \
    params ag_name="<AGName>" \
    meta failure-timeout=60s \
    op start timeout=60s \
    op stop timeout=60s \
    op promote timeout=60s \
    op demote timeout=10s \
    op monitor timeout=60s interval=10s \
    op monitor timeout=60s interval=11s role="Master" \
    op monitor timeout=60s interval=12s role="Slave" \
    op notify timeout=60s
    ms ms-ag_cluster <NameForAGResource> \
    meta master-max="1" master-node-max="1" clone-max="3" \
    clone-node-max="1" notify="true" \
    commit
    ```
    
    其中 *NameForAGResource* 是指定給 AG 之叢集資源的唯一名稱，而 *AGName* 是所建立 AG 的名稱。
 
2.  建立將與接聽程式功能相關聯之 AG 的 IP 位址資源。

    **RHEL 和 Ubuntu**
    
    ```bash
    sudo pcs resource create <NameForIPResource> ocf:heartbeat:IPaddr2 ip=<IPAddress> cidr_netmask=<Netmask>
    ```

    **SLES**
    
    ```bash
    crm configure \
    primitive <NameForIPResource> \
       ocf:heartbeat:IPaddr2 \
       params ip=<IPAddress> \
          cidr_netmask=<Netmask>
    ```
    
    其中 *NameForIPResource* 是 IP 資源的唯一名稱，而 *IPAddress* 是指派給資源的靜態 IP 位址。 在 SLES 上，您也必須提供網路遮罩。 例如，255.255.255.0 的 *Netmask* 值為 24。
    
3.  為了確保 IP 位址和 AG 資源在相同的節點上執行，必須設定共置限制式。

    **RHEL 和 Ubuntu**
    
    ```bash
    sudo pcs constraint colocation add <NameForIPResource> <NameForAGResource>-master INFINITY with-rsc-role=Master
    ```

    **SLES**
    
    ```bash
    crm configure <NameForConstraint> inf: \
    <NameForIPResource> <NameForAGResource>:Master 
    commit
    ```
    
    其中 *NameForIPResource* 是 IP 資源的名稱，*NameForAGResource* 是 AG 資源的名稱，而在 SLES 上，*NameForConstraint* 是限制式的名稱。

4.  建立排序限制式，以確保 AG 資源會在 IP 位址之前啟動並執行。 雖然共置限制式隱含排序限制式，但此步驟會強制執行它。

    **RHEL 和 Ubuntu**
    
    ```bash
    sudo pcs constraint order promote <NameForAGResource>-master then start <NameForIPResource>
    ```
    
    **SLES**
    
    ```bash
    crm configure \
    order <NameForConstraint> inf: <NameForAGResource>:promote <NameForIPResource>:start
    commit
    ```
    
    其中 *NameForIPResource* 是 IP 資源的名稱，*NameForAGResource* 是 AG 資源的名稱，而在 SLES 上，*NameForConstraint* 是限制式的名稱。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您將了解如何為 Linux 上的 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 建立和設定可用性群組。 您已了解如何︰
> [!div class="checklist"]
> * 啟用可用性群組。
> * 建立 AG 端點和憑證。
> * 使用 [!INCLUDE[ssmanstudiofull-md](../includes/ssmanstudiofull-md.md)] (SSMS) 或 Transact-SQL 來建立 AG。
> * 建立 Pacemaker 的 [!INCLUDE[ssnoversion-md](../includes/ssnoversion-md.md)] 登入和權限。
> * 在 Pacemaker 叢集中建立 AG 資源。

對於大部分的 AG 管理工作 (包括升級和容錯移轉)，請參閱：

> [!div class="nextstepaction"]
> [在 Linux 上操作 SQL Server 的 HA 可用性群組](sql-server-linux-availability-group-failover-ha.md)

