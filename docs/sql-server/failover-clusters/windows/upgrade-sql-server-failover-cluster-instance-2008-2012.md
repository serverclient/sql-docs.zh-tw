---
title: 升級 SQL Server 執行個體：Windows Server 2012 和舊版叢集
description: 描述如何升級在 Windows Server 2008、Windows Server 2008 R2 和 Windows Server 2012 上執行的 SQL Server 容錯移轉叢集執行個體。
ms.date: 06/15/2020
ms.custom: seo-lt-2019
ms.prod: sql
ms.technology: failover-cluster-instance
ms.topic: how-to
helpviewer_keywords:
- upgrading failover clusters
- clusters [SQL Server], upgrading
- failover clustering [SQL Server], upgrading
author: cawrites
ms.author: chadam
ms.openlocfilehash: 90ab761cdc9a84008803ebb4bc5493eb87778f20
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642674"
---
# <a name="upgrade-sql-server-instances-running-on-windows-server-20082008-r22012-clusters"></a>升級在 Windows Server 2008/2008 R2/2012 叢集上執行的 SQL Server 執行個體

[!INCLUDE[nextref-longhorn-md](../../../includes/nextref-longhorn-md.md)]、[!INCLUDE[winserver2008r2-md](../../../includes/winserver2008r2-md.md)] 和 [!INCLUDE[win8srv-md](../../../includes/win8srv-md.md)] 防止 Windows Server 容錯移轉叢集執行就地作業系統升級，並具有叢集允許的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本。 叢集在升級為至少 [!INCLUDE[winblue-server-2-md](../../../includes/winblue-server-2-md.md)] 之後即可保持最新狀態。

## <a name="prerequisites"></a>Prerequisites

-   執行任何移轉策略之前，必須準備具有 Windows Server 2016/2012 R2 的平行 Windows Server 容錯移轉叢集。 所有包含 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 容錯移轉叢集執行個體 (FCI) 的節點都必須加入至已安裝平行 FCI 的 Windows 叢集。 在移轉之前，任何獨立電腦都 **不得** 加入至 Windows Server 容錯移轉叢集。 在移轉之前，應該同步處理新環境上的使用者資料庫。
-   所有目的地執行個體都必須使用相同的執行個體名稱和識別碼來執行與其在原始環境之平行執行個體的相同 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本，並安裝相同的功能。 在目的地電腦上，安裝路徑和目錄結構應該相同。 這不包含 FCI 虛擬網路名稱，這在移轉之前必須不同。 在目的地執行個體上，應該啟用原始執行個體所啟用的任何功能 (AlwaysOn、FILESTREAM 等等)。

-   在移轉之前，平行叢集應該未安裝 [!INCLUDE[sshadrc-md](../../../includes/sshadrc-md.md)]。

-   移轉使用嚴格可用性群組 (不論是否有 SQL FCI) 之叢集時的停機時間，大幅受限於使用分散式可用性群組，但這需要所有執行個體執行 [!INCLUDE[sssql15-md](../../../includes/sssql15-md.md)] RTM 版本 (或以上版本)。

-   所有移轉策略都需要 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] sysadmin 角色。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務所使用的所有 Windows 使用者 (即執行複寫代理程式的 Windows) 都必須有新環境中每部電腦的 OS 層級權限。

-   原始 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 叢集環境中所使用的任何網路檔案共用和網路對應磁碟機都仍然必須存在，而且目標叢集仍然可以使用與原始執行個體相同的權限進行存取。

-   原始 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體所接聽的任何 TCP/IP 連接埠都必須為未用，並允許目標電腦上的輸入流量。
-   所有 SQL 相關服務應該都是由相同的 Windows 使用者安裝及執行。

-   必須使用與原始執行個體相同的地區設定來安裝目的地執行個體。

## <a name="migration-scenarios"></a>移轉案例

適當的移轉策略取決於原始 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 叢集拓撲的特定參數，也就是使用 [!INCLUDE[sshadrc-md](../../../includes/sshadrc-md.md)] 和 SQL 容錯移轉叢集執行個體。 所選擇的策略也取決於目的地環境的需求。 如果新的環境需要每部電腦或 SQL FCI 都維護原始虛擬網路名稱 (VNN)，或者 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 拓撲取決於繼承原始執行個體的所有系統物件的新執行個體，則我們必須選擇可移轉這些項目的策略。

|                                   | 需要所有伺服器物件和 VNNS | 需要所有伺服器物件和 VNNS | 不需要伺服器物件/VNNS\* | 不需要伺服器物件/VNNS\* |
|-----------------------------------|--------------------------------------|--------------------------------------------------------------------|------------|------------|
| **_可用性群組？(是/否_)**                  | **_是_**                              | **_否_**                                                            | **_是_**    | **_否_**    |
| **叢集只會使用 SQL FCI**         | [案例 3](#scenario-3-windows-cluster-has-both-sql-fcis-and-sql-server-availability-groups)                           | [案例 2](#scenario-2-windows-clusters-with-sql-server-failover-cluster-instances-fcis)                                                        | [案例 1](#scenario-1-windows-cluster-with-sql-server-availability-groups-and-no-failover-cluster-instances-fcis) | [案例 2](#scenario-2-windows-clusters-with-sql-server-failover-cluster-instances-fcis) |
| **叢集使用獨立執行個體** | [案例 5](#scenario-5-windows-cluster-with-standalone-sql-server-instances-and-availability-groups)                           | [案例 4](#scenario-4-windows-cluster-with-standalone-sql-server-instances-and-no-availability-groups)                                                         | [案例 1](#scenario-1-windows-cluster-with-sql-server-availability-groups-and-no-failover-cluster-instances-fcis) | [案例 4](#scenario-4-windows-cluster-with-standalone-sql-server-instances-and-no-availability-groups) |

\* 排除可用性群組接聽程式名稱

## <a name="scenario-1-windows-cluster-with-sql-server-availability-groups-and-no-failover-cluster-instances-fcis"></a>案例 1：有 SQL Server 可用性群組、沒有容錯移轉叢集執行個體 (FCI) 的 Windows 叢集
如果您的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式使用可用性群組 (AG) 且沒有容錯移轉叢集執行個體，則可以在具有 Windows Server 2016/2012 R2 的不同 Windows 叢集上建立平行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 部署，來移轉至新叢集。 在此之後，您可以建立目標叢集為目前生產叢集之次要叢集的分散式 AG。 這需要使用者升級為 [!INCLUDE[sssql15-md](../../../includes/sssql15-md.md)] 或更新版本。

###  <a name="to-perform-the-upgrade"></a>執行升級

1.  如有需要，請將所有執行個體升級為 [!INCLUDE[sssql15-md](../../../includes/sssql15-md.md)] 或更新版本。 平行執行個體應該執行相同版本的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]。

2.  建立目標叢集的可用性群組。 如果目標叢集的主要節點不是 FCI，請建立接聽程式。

3.  形成目標叢集是次要可用性群組的分散式可用性群組。

    >[!NOTE]
    >建立分散式 AG T-SQL 的 LISTENER\_URL 參數，其行為對於以 SQL FCI 作為主要執行個體的 AG 會有所不同。 如果主要或次要 AG 遇到這種情況，請使用主要 SQL FCI 的 VNN 作為接聽程式 URL 來取代接聽程式的網路名稱，以及資料庫鏡像端點的連接埠。

4.  將次要可用性群組加入至分散式 AG 中。

5.  加入次要 AG 上的次要資料庫。

    >[!NOTE]
    >如果目標可用性群組使用自動植入，則這會自動完成；這只有在所有複本的資料和記錄路徑都相同時才可能。

6.  截斷所有流向主要 AG 的流量，並允許次要 AG 同步。

7.  將這兩個可用性群組的認可原則變更為 SYNCHRONOUS_COMMIT，並在狀態為 SYNCHRONIZED 之後容錯移轉至目標叢集。

8.  刪除分散式 AG。

9.  刪除或重新命名原始 AG 上的接聽程式。

10. 使用原始 AG 接聽程式名稱來重新命名或建立新 AG 接聽程式。

    >[!NOTE]
    >雖然原始 AG 接聽程式的 DNS 記錄存在，但是嘗試使用此名稱來建立接聽程式將會失敗。

11. 繼續流向接聽程式的流量。

## <a name="scenario-2-windows-clusters-with-sql-server-failover-cluster-instances-fcis"></a>案例 2：有 SQL Server 容錯移轉叢集執行個體 (FCI) 的 Windows 叢集

如果您的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 環境僅有 SQL FCI 執行個體，則可以在具有 Windows Server 2016/2012 R2 的不同 Windows 叢集上建立平行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 環境，來移轉至新叢集。 您將「竊取」舊 SQL FCI 的 VNN 並在新叢集上取得它們，以移轉至目標叢集。 這將會根據 DNS 傳播時間來建立額外停機時間。

###  <a name="to-perform-the-upgrade"></a>執行升級

1.  進行完整備份，並停止流向原始 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 叢集的流量。

2.  進行使用者資料庫的結尾記錄備份，並在新環境上使用復原進行還原。

3.  在目標叢集的容錯移轉叢集管理員中，關閉每個 SQL FCI 叢集角色。

4.  仍然在目標叢集的容錯移轉叢集管理員中，重新顯示每個 SQL FCI 所使用的叢集磁碟。

5.  在原始叢集的容錯移轉叢集管理員中，關閉每個 SQL FCI 叢集角色。

6.  仍然在原始叢集的容錯移轉叢集管理員中，重新顯示每個 SQL FCI 所使用的叢集磁碟。

7.  將系統資料庫從原始電腦複製至其平行目標電腦。

8.  在原始環境的容錯移轉叢集管理員中，變更每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 角色的「伺服器名稱」資源名稱。

9.  立即只讓每個 SQL FCI 角色的已重新命名「伺服器名稱」資源重新上線。

10. 現在，在目標叢集的容錯移轉叢集管理員中，將每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 角色的「伺服器名稱」資源重新命名為原始叢集先前所保留名稱。

    >[!NOTE]
    >刪除名稱的 DNS 記錄之後，另一部電腦已持有之名稱所引發的錯誤將會停止。

11. 重新命名所有 FCI 之後，請重新啟動新叢集中的每部電腦。

12. 電腦在重新啟動後重新上線時，請在容錯移轉叢集管理員中啟動每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 角色。

## <a name="scenario-3-windows-cluster-has-both-sql-fcis-and-sql-server-availability-groups"></a>案例 3：有 SQL FCI 和 SQL Server 可用性群組的 Windows 叢集

如果 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安裝程式未使用任何獨立 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體 (只有包含在至少一個可用性群組中的 SQL FCI)，則可以使用與「無可用性群組、無獨立執行個體」案例類似的方法將此項目移轉至新叢集。 將系統資料表複製至目標 FCI 共用磁碟之前，您必須卸除原始環境中的所有可用性群組。 所有資料庫都移轉至目標電腦之後，您將會重新建立具有相同結構描述和接聽程式名稱的可用性群組。 如此一來，Windows Server 容錯移轉叢集資源的格式會正確，並在目標叢集上進行管理。 **移轉之前，必須在目標環境之每部電腦的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Configuration Manager 中啟用 AlwaysOn。**

### <a name="to-perform-the-upgrade"></a>執行升級

1.  停止流向 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的流量。

2.  進行使用者資料庫的結尾記錄備份、在新環境的預定主要上使用復原進行還原，並在所有預定次要上使用 NORECOVERY 進行還原。

3.  在目標叢集的容錯移轉叢集管理員中，關閉每個 SQL FCI 叢集角色。

4.  仍然在目標叢集的容錯移轉叢集管理員中，重新顯示每個 SQL FCI 所使用的叢集磁碟。

5.  在原始叢集上，刪除可用性群組。

6.  在原始叢集的容錯移轉叢集管理員中，關閉每個 SQL FCI 叢集角色。

7.  仍然在原始叢集的容錯移轉叢集管理員中，重新顯示每個 SQL FCI 所使用的叢集磁碟。

8.  將系統資料庫從原始電腦複製至其平行目標電腦。

9.  在原始環境的容錯移轉叢集管理員中，變更每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 角色的「伺服器名稱」資源名稱。

10. 立即只讓每個 SQL FCI 角色的已重新命名「伺服器名稱」資源重新上線。

11. 現在，在目標叢集的容錯移轉叢集管理員中，將每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 角色的「伺服器名稱」資源重新命名為原始叢集先前所保留名稱。

12. 重新命名所有 FCI 之後，請重新啟動新叢集中的每部電腦。

13. 電腦在重新啟動後重新上線時，請在容錯移轉叢集管理員中啟動每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 角色。

14. 所有執行個體都上線之後，會在使用復原還原資料庫的複本上形成可用性群組。

15. 將所有次要複本加入至 AG，將所有次要資料庫加入至 AG。

16. 在具有原始可用性群組接聽程式之接聽程式名稱的新 AG 中，建立接聽程式。

## <a name="scenario-4-windows-cluster-with-standalone-sql-server-instances-and-no-availability-groups"></a>案例 4：有獨立 SQL Server 執行個體、沒有可用性群組的 Windows 叢集

移轉具有獨立執行個體的叢集，與移轉只有 FCI 的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 叢集的程序類似，但您需要變更原始獨立電腦的電腦名稱，並「竊取」目標電腦上的舊電腦名稱，而不需要變更 FCI 網路名稱叢集資源的 VNN。 這會導致相對於無獨立案例的額外停機時間，因為除非您取得舊電腦的網路名稱，否則無法將目標獨立電腦新增至 WSFC。

###  <a name="to-perform-the-upgrade"></a>執行升級

1.  停止流向 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的流量。

2.  進行使用者資料庫的結尾記錄備份，並在每部電腦的新環境上使用復原進行還原。

3.  在目標叢集的容錯移轉叢集管理員中，關閉每個 SQL FCI 叢集角色。

4.  仍然在目標叢集的容錯移轉叢集管理員中，重新顯示每個 SQL FCI 所使用的叢集磁碟。

5.  在原始叢集的容錯移轉叢集管理員中，關閉每個 SQL FCI 叢集角色，並停止 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Configuration Manager 中的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 獨立執行個體。

6.  針對原始叢集中的每部獨立電腦，將每部電腦重新命名為新的唯一電腦名稱。 依指示，重新啟動所有這些電腦。

7.  在原始叢集的容錯移轉叢集管理員中，重新顯示每個 SQL FCI 所使用的叢集磁碟。

8.  將系統資料庫複製至目標電腦。

9.  在原始環境的容錯移轉叢集管理員中，將每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 角色的「伺服器名稱」資源變更為新唯一名稱。

10. 立即只讓每個 SQL FCI 角色的已重新命名「伺服器名稱」資源重新上線。

11. 現在，在平行獨立執行個體上，將電腦重新命名為原始獨立電腦名稱 (卸除舊的伺服器名稱，使用本機 param 新增新的伺服器名稱)。依指示，重新啟動電腦。

12. 重新啟動之後，請將每部獨立電腦加入至目標 Windows Server 容錯移轉叢集。

13. 現在，在目標叢集的容錯移轉叢集管理員中，將每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 角色的「伺服器名稱」資源重新命名為原始叢集先前所保留名稱。

14. 重新命名所有 FCI 之後，請重新啟動新叢集中的每部電腦。

15. 電腦在重新啟動後重新上線時，請在容錯移轉叢集管理員中啟動每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 角色。

## <a name="scenario-5-windows-cluster-with-standalone-sql-server-instances-and-availability-groups"></a>案例 5：有獨立 SQL Server 執行個體和可用性群組的 Windows 叢集

移轉使用具有獨立複本之可用性群組的叢集，與使用可用性群組移轉具有 FCI 之叢集的程序類似。 您仍然必須刪除原始可用性群組，並在目標叢集上重新予以建構；不過，會因移轉獨立執行個體的額外成本而造成額外的停機時間。 **移轉之前，必須在目標環境的每個 FCI 上啟用 AlwaysOn。**

###  <a name="to-perform-the-upgrade"></a>執行升級

1.  停止流向 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的流量。

2.  進行使用者資料庫的結尾記錄備份、在新環境的預定主要上使用復原進行還原，並在每個預定次要上使用 NORECOVERY 進行還原。

3.  刪除原始叢集上的可用性群組。

4.  在目標叢集的容錯移轉叢集管理員中，關閉每個 SQL FCI 叢集角色。

5.  仍然在目標叢集的容錯移轉叢集管理員中，重新顯示每個 SQL FCI 所使用的叢集磁碟。

6.  在原始叢集的容錯移轉叢集管理員中，關閉每個 SQL FCI 叢集角色，並停止 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Configuration Manager 中的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 獨立執行個體。

7.  針對原始叢集中的每部獨立電腦，將每部電腦重新命名為新的唯一電腦名稱。 依指示，重新啟動所有這些電腦。

8.  在原始叢集的容錯移轉叢集管理員中，重新顯示每個 SQL FCI 所使用的叢集磁碟。

9.  將系統資料庫複製至目標電腦。

10. 在原始環境的容錯移轉叢集管理員中，將每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 角色的「伺服器名稱」資源變更為新唯一名稱。

11. 立即只讓每個 SQL FCI 角色的已重新命名「伺服器名稱」資源重新上線。

12. 現在，在平行獨立執行個體上，將電腦重新命名為原始獨立電腦名稱 (在 SQL 中，卸除並新增伺服器名稱)。依指示，重新啟動電腦。

13. 重新啟動之後，請將每部獨立電腦加入至目標 Windows Server 容錯移轉叢集。

14. 現在，在目標叢集的容錯移轉叢集管理員中，將每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 角色的「伺服器名稱」資源重新命名為原始叢集先前所保留名稱。

15. 重新命名所有 FCI 之後，請重新啟動新叢集中的每部電腦。

16. 電腦在重新啟動後重新上線時，請在容錯移轉叢集管理員中啟動每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] FCI 角色。

17. 所有執行個體都上線之後，請重新建立預期主要複本上的可用性群組。

18. 加入每個次要複本和次要資料庫。

19. 重新建立與原始同名的可用性群組接聽程式。

## <a name="specific-concerns-for-individual-features"></a>個別功能的特定考量

### [!INCLUDE[sshadrc-md](../../../includes/sshadrc-md.md)]

-   **資料庫鏡像端點**

    從 SQL 的觀點，資料庫鏡像端點將會與系統資料表一起移轉至新的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體。 在移轉之前，請確定在防火牆中套用適當的規則，而且在相同的連接埠上未接聽任何其他處理序。

-   **可用性群組**

    可用性群組和其接聽程式無法在執行個體之間移轉。 在目標環境上，無法輕鬆地重新建立可用性群組所建立的 Windows Server 容錯移轉叢集資源。 我們會尋求卸除並重新建立目標叢集上的可用性群組，而不是嘗試移轉可用性群組。

-   **可用性群組接聽程式**

    就像可用性群組本身，我們會卸除並重新建立接聽程式，以直接移轉它們。

### <a name="replication"></a>複寫

-   **遠端散發者、發行者、訂閱者**

    散發者與發行者之間的關聯性只是依賴裝載這兩者的電腦 VNN，而這兩者會正確地解析為新電腦。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Agent 作業也會使用系統資料表正確地移轉，因此各種複寫代理程式可以如常地繼續執行。 在移轉之前，我們需要執行 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Agent 本身或任何 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Agent 作業的任何 Windows 帳戶都有目標環境中的相同權限。 發行者與訂閱者之間的通訊將會如常執行。

-   **快照集資料夾**

    在移轉之前，需要目標環境中的電腦使用與原始環境相同的權限來存取任何 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 功能所使用的任何網路共用。 在移轉之前，您必須確定是這種情況。

### <a name="service-broker"></a>Service Broker

-   **Service Broker 端點**

    從 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 觀點，沒有任何端點考量。 在移轉之前，您需要確保相同的連接埠上尚未接聽任何處理序，而且沒有防火牆規則會封鎖該連接埠，或有專門允許連接埠的防火牆規則。

-   **憑證**

    如果憑證需要還原到新電腦，則也應該將憑證備份和還原到目標電腦。

-   **路由**

    路由取決於目標的虛擬網路名稱，而其電腦名稱和 SQL FCI 網路名稱將正確地解析為新環境中的正確電腦。 任何其他參考的 VNN 也必須重新導向至新的電腦。

-   **遠端服務繫結**

    在移轉之後，遠端服務繫結將如預期般運作，因為任何使用遠端服務繫結的使用者都會適當地移轉。

### <a name="ssnoversion-agent"></a>[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Agent

-   **作業**

    將適當地移轉作業與系統資料庫。 任何執行 SQL Agent 作業或 SQL Agent 本身的使用者都會有必要條件中所指定目標電腦的相同權限。

-   **警示和操作員**

    將適當地移轉警示和運算子與系統資料庫。

### <a name="filestream"></a>FILESTREAM

-   **Windows 檔案共用連接埠**

    Windows 檔案共用連接埠 139 和 445 都必須具有允許輸入流量以使用 FILESTREAM 的規則

-   **Windows 共用**

    Windows 共用路徑取決於 SQL FCI 名稱資源，因為它是透過 `\\ServerName\ShareName` 進行存取。 移轉 FILESTREAM 需要目標 FCI 中的所有節點都啟用 FILESTREAM；而且，如果使用 Windows 共用，則會將它們設定成使用與原始電腦相同的 Windows 共用名稱。 目標 FCI 取得正確的伺服器名稱之後，就會裝載使用所需路徑的 Windows 共用。

-   **FILESTREAM 資料**

    FILESTREAM 資料會包含在備份中。

### <a name="integration-services"></a>Integration Services

-   **SSIS 專案**

    SSIS 專案會與 SSIS 資料庫一起移轉。 移動 SSIS 資料庫之後，會在移動任何系統資料表之前立即執行套件。

-   **檔案資料來源**

    必須可以在 SSIS 套件所指定的相同位置存取一般檔案、Excel 檔案、XML 來源和其他項目。

## <a name="next-steps"></a>後續步驟
- [完成資料庫引擎升級](../../../database-engine/install-windows/complete-the-database-engine-upgrade.md)
- [變更資料庫相容性模式並使用查詢存放區](../../../database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store.md)
- [善用新的 SQL Server 2016 功能](../../what-s-new-in-sql-server-2017.md)
- [升級 SQL Server 容錯移轉叢集執行個體](upgrade-a-sql-server-failover-cluster-instance.md)
- [檢視與讀取 SQL Server 安裝程式記錄檔](../../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)
- [將功能新增至 SQL Server 2016 的執行個體 (安裝程式)](../../../database-engine/install-windows/add-features-to-an-instance-of-sql-server-setup.md)