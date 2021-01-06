---
title: 建立網域獨立的可用性群組
description: 建立使用工作群組叢集之可用性群組的步驟。 這可讓 SQL Server 2016 (和更新版本) 在 WSFC 上部署 Always On 可用性群組，WSFC 不需要 Active Directory 網域服務，因此需要每部伺服器都屬於相同的網域。
ms.custom: seodec18
ms.date: 09/25/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- Availability Groups [SQL Server], domain independent
ms.assetid: ''
author: cawrites
ms.author: chadam
ms.openlocfilehash: 6c809abd5c333013b197dd48ca2fb4832bf6b615
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643604"
---
# <a name="create-a-domain-independent-availability-group"></a>建立網域獨立的可用性群組
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

Always On 可用性群組 (AG) 需要基礎 Windows Server 容錯移轉叢集 (WSFC)。 透過 Windows Server 2012 R2 部署 WSFC 時，一律需要參與 WSFC 的伺服器 (也稱為節點) 加入相同的網域。 如需 Active Directory 網域服務 (AD DS) 的詳細資訊，請參閱[這裡](/previous-versions/windows/it-pro/windows-server-2003/cc759073(v=ws.10))。

AD DS 和 WSFC 相依性比先前使用資料庫鏡像 (DBM) 組態所部署的相依性更為複雜，因為可以使用憑證跨多個資料中心部署 DBM，而不需要任何這類相依性。  跨多個資料中心的傳統可用性群組需要所有伺服器都必須加入相同的 Active Directory 網域；不同的網域即使是受信任的網域，也沒有作用。 所有伺服器都必須是相同 WSFC 的節點。 下圖顯示這個組態。 SQL Server 2016 也具有分散式可用性群組，同時以不同的方式達到此目標。


![跨兩個連接至相同網域之資料中心的 WSFC][1]

Windows Server 2012 R2 引進[已中斷連結 Active Directory 的叢集](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265970(v=ws.11))，這是可與可用性群組搭配使用的一種特殊形式的 Windows Server 容錯移轉叢集。 這種類型的 WSFC 仍然需要將節點加入相同的 Active Directory 網域，但在此情況下，WSFC 會使用 DNS，而不是使用網域。 因為仍然包含網域，所以已中斷連結 Active Directory 的叢集仍然未提供完全無網域體驗。

Windows Server 2016 引進以「已中斷連結 Active Directory 的叢集」為基礎之新類型的 Windows Server 容錯移轉叢集：Workgroup 叢集。 Workgroup 叢集可讓 SQL Server 2016 在不需要 AD DS 的 WSFC 上部署可用性群組。 SQL Server 需要使用憑證才能獲得端點安全性，就像資料庫鏡像案例需要憑證一樣。  這種類型的可用性群組稱為「網域獨立的可用性群組」。 部署具有基礎 Workgroup 叢集的可用性群組，支援將構成 WSFC 之節點的下列組合：
- 沒有節點加入網域。
- 所有節點都加入不同的網域。
- 混合使用節點，即合併使用加入網域和未加入網域的節點。

下圖顯示「網域獨立的可用性群組」範例，其中，資料中心 1 中的節點已加入網域，但資料中心 2 中的節點只會使用 DNS。 在此情況下，會在 WSFC 節點的所有伺服器上設定 DNS 尾碼。 每個存取可用性群組的應用程式和伺服器都必須看到相同的 DNS 資訊。


![具有兩個加入網域之節點的 Workgroup 叢集][2]

「網域獨立的可用性群組」不只是適用於多網站或災害復原案例。 它可以部署於單一資料中心，甚至可以與[基本可用性群組](basic-availability-groups-always-on-availability-groups.md) (也稱為 Standard Edition 可用性群組) 搭配使用，以提供用來搭配使用資料庫鏡像與憑證達成之作業的類似架構，如下所示。


![Standard Edition 中的 AG 高階檢視][3]

部署「網域獨立的可用性群組」的一些已知警示如下：
- 可與仲裁搭配使用的唯一見證類型是磁碟和[雲端](/windows-server/failover-clustering/deploy-cloud-witness)，其為 Windows Server 2016 中的新功能。 因為可用性群組最有可能不需要使用共用磁碟，所以磁碟會有問題。
- WSFC 的基礎 Workgroup 叢集變異只能使用 PowerShell 建立，但接著可以使用容錯移轉叢集管理員進行管理。
- 如果需要 Kerberos，您必須部署連接至 Active Directory 網域的標準 WSFC，因此「網域獨立的可用性群組」可能不是其中一個選擇。
- 雖然可以設定接聽程式，但它必須在 DNS 中註冊才能使用。 如前所述，接聽程式沒有 Kerberos 支援。
- 因為網域可能不存在，或可能未設定成為搭配使用，所以連接至 SQL Server 的應用程式主要應該使用 SQL Server 驗證。 
- 憑證將用於可用性群組的組態中。

## <a name="set-and-verify-the-dns-suffix-on-all-replica-servers"></a>設定並確認所有複本伺服器上的 DNS 尾碼

「網域獨立的可用性群組」的 Workgroup 叢集需要一般 DNS 尾碼。 若要設定並確定將裝載可用性群組複本之每個 Windows Server 上的 DNS 尾碼，請遵循下列指示：

1. 使用 Windows 鍵 + X 快速鍵，選取 [系統]。
2. 如果電腦名稱和完整電腦名稱相同，則尚未設定 DNS 尾碼。 例如，如果電腦名稱為 ALLAN，則完整電腦名稱的值不應該只是 ALLAN。 它應該是 ALLAN.SQLHA.LAB 這類項目。 SQLHA.LAB 是 DNS 尾碼。 Workgroup 的值應該是 WORKGROUP。 如果您需要設定 DNS 尾碼，請選取 [變更設定]。
3. 在 [系統內容] 對話方塊中，按一下 [電腦名稱] 索引標籤上的 [變更]。
4. 在 [電腦名稱/網域變更] 對話方塊上，按一下 [更多]。
5. 在 [DNS 尾碼和 NetBIOS 電腦名稱] 對話方塊中，輸入一般 DNS 尾碼作為主要 DNS 尾碼。 
6. 按一下 [確定] 關閉 [DNS 尾碼和 NetBIOS 電腦名稱] 對話方塊。
7. 按一下 [確定] 關閉 [電腦名稱/網域變更] 對話方塊。
8. 系統會提示您重新啟動伺服器，以讓變更生效。 按一下 [確定] 關閉 [電腦名稱/網域變更] 對話方塊。
9. 按一下 [關閉] 關閉 [系統內容] 對話方塊。
10. 系統會提示您重新啟動。 如果您不想要立即重新啟動，請按一下 [稍後重新啟動]，否則按一下 [立即重新啟動]。
11. 重新啟動伺服器之後，請重新查看 [系統] 來確認已設定一般 DNS 尾碼。

![成功設定 DNS 尾碼][4]

  > [!NOTE]
  > 如果您使用多個子網路，且具有靜態 DNS，即必須採用程序才能更新與接聽程式相關聯的 DNS 記錄，再執行容錯移轉，否則網路名稱無法上線。

## <a name="create-a-domain-independent-availability-group"></a>建立網域獨立的可用性群組

目前使用 SQL Server Management Studio 無法完整建立「網域獨立的可用性群組」。 雖然建立「網域獨立的可用性群組」基本上與建立一般可用性群組相同，但是某些方面 (例如建立憑證) 只有使用 Transact-SQL 才能達成。 下列範例假設可用性群組組態包含兩個複本：一個主要複本，一個次要複本。 

1. [使用本連結的指示](https://techcommunity.microsoft.com/t5/Failover-Clustering/Workgroup-and-Multi-domain-clusters-in-Windows-Server-2016/ba-p/372059)，部署 Workgroup 叢集以包含所有要參與可用性群組的伺服器。 設定 Workgroup 叢集之前，請確定已設定一般 DNS 尾碼。
2. 在要參與可用性群組的每個執行個體上[啟用 AlwaysOn 可用性群組](./enable-and-disable-always-on-availability-groups-sql-server.md)功能。 這需要重新啟動每個 SQL Server 執行個體。
3. 將裝載主要複本的每個執行個體都需要資料庫主要金鑰。 如果還沒有主索引鍵，請執行下列命令：

   ```sql
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Strong Password';
   GO
   ```

4. 在將為主要複本的執行個體上，建立憑證，以用於次要複本上的傳入連接，以及用於保護主要複本上的端點。

   ```sql
   CREATE CERTIFICATE InstanceA_Cert 
   WITH SUBJECT = 'InstanceA Certificate';
   GO
   ``` 

5. 備份憑證。 如有需要，您也可以使用私密金鑰進一步保護它。 此範例未使用私密金鑰。

   ```sql
   BACKUP CERTIFICATE InstanceA_Cert 
   TO FILE = 'Backup_path\InstanceA_Cert.cer';
   GO
   ```

6. 重複步驟 4 和 5，以使用適當的憑證名稱來建立和備份每個次要複本的憑證，例如 InstanceB_Cert。
7. 在主要複本上，您必須建立可用性群組之每個次要複本的登入。 這個登入將會獲授與連接至「網域獨立的可用性群組」所使用端點的權限。 例如，針對名為 InstanceB 的複本：

   ```sql
   CREATE LOGIN InstanceB_Login WITH PASSWORD = 'Strong Password';
   GO
   ```

8. 在每個次要複本上，建立主要複本的登入。 此登入會獲授與連接至端點的權限。 例如，在名為 InstanceB 的複本上：

   ```sql
   CREATE LOGIN InstanceA_Login WITH PASSWORD = 'Strong Password';
   GO
   ```

9. 在所有執行個體上，為所建立的每個登入建立使用者。 這會在還原憑證時使用。 例如，建立主要複本的使用者：

   ```sql
   CREATE USER InstanceA_User FOR LOGIN InstanceA_Login;
   GO
   ```

10. 針對可能是主要的任何複本，在所有相關的次要複本上建立登入和使用者。
11. 在每個執行個體上，還原已建立登入和使用者之其他執行個體的憑證。 在主要複本上，還原所有次要複本憑證。 在每個次要複本上，以及可能是主要複本的任何其他複本上，還原主要複本的憑證。 例如：

   ```sql
   CREATE CERTIFICATE [InstanceB_Cert]
   AUTHORIZATION InstanceB_User
   FROM FILE = 'Restore_path\InstanceB_Cert.cer'
   ```

12. 建立要由每個執行個體上將為複本之可用性群組使用的端點。 針對可用性群組，端點必須有一種類型的 DATABASE_MIRRORING。 端點會使用步驟 4 中針對該執行個體所建立的憑證來進行驗證。 如下顯示的範例語法會使用憑證來建立端點。 使用適當的加密方法以及您環境的其他相關選項。 如需可用選項的詳細資訊，請參閱 [CREATE ENDPOINT (Transact-SQL)](../../../t-sql/statements/create-endpoint-transact-sql.md)。

   ```sql
   CREATE ENDPOINT DIAG_EP
   STATE = STARTED
   AS TCP (   
    LISTENER_PORT = 5022,
    LISTENER_IP = ALL
         )
   FOR DATABASE_MIRRORING (
    AUTHENTICATION = CERTIFICATE InstanceX_Cert,
    ROLE = ALL
         )
   ```

13. 將可以連接至端點的權限指派給步驟 9 中於該執行個體上建立的每位使用者。 

   ```sql
   GRANT CONNECT ON ENDPOINT::DIAG_EP TO [InstanceX_User];
   GO
   ```

14. 設定基礎憑證和端點安全性之後，請使用慣用方法來建立可用性群組。 建議您手動備份、複製和還原用來初始化次要的備份，或使用[自動植入](automatically-initialize-always-on-availability-group.md)。 使用精靈來初始化次要複本，涉及使用伺服器訊息區塊 (SMB) 檔案，這在使用未加入網域的 Workgroup 叢集時可能未運作。
15. 如果建立接聽程式，請確定已在 DNS 中註冊其名稱和其 IP 位址。

### <a name="next-steps"></a>後續步驟 

- [使用可用性群組精靈 (SQL Server Management Studio)](use-the-availability-group-wizard-sql-server-management-studio.md)

- [使用新增可用性群組對話方塊 (SQL Server Management Studio)](use-the-new-availability-group-dialog-box-sql-server-management-studio.md)
 
- [使用 Transact-SQL 建立可用性群組](create-an-availability-group-transact-sql.md)

<!--Image references-->
[1]: ./media/diag-wsfc-two-data-centers-same-domain.png
[2]: ./media/diag-workgroup-cluster-two-nodes-joined.png
[3]: ./media/diag-high-level-view-ag-standard-edition.png
[4]: ./media/diag-successful-dns-suffix.png