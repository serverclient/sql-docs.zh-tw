---
title: 對 SQL Server 資料庫引擎的連線進行疑難排解
description: 了解如何針對連線問題進行疑難排解。 檢視當您無法使用 TCP/IP 連線到單一伺服器上的 SQL Server 資料庫引擎時要採取的步驟。
ms.prod: sql
ms.prod_service: high-availability
ms.technology: configuration
ms.topic: troubleshooting
helpviewer_keywords:
- troubleshooting, connecting to Database Engine
- connecting to Database Engine, troubleshooting
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.custom: sqlfreshmay19
ms.date: 11/25/2019
ms.openlocfilehash: 2ef49ed5d3d46e4f5c5602972ea87347b686d9bb
ms.sourcegitcommit: 8a8c89b0ff6d6dfb8554b92187aca1bf0f8bcc07
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617567"
---
# <a name="troubleshoot-connecting-to-the-sql-server-database-engine"></a>對 SQL Server 資料庫引擎的連線進行疑難排解

[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

此文章列出當您無法連線到單一伺服器上的 SQL Server 資料庫引擎執行個體時可使用的疑難排解技術。

>[!NOTE]
>針對其他案例，請參閱：
>- [可用性群組接聽程式](../availability-groups/windows/listeners-client-connectivity-application-failover.md)
>- [容錯移轉叢集執行個體](../../sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server.md)

這些步驟不是按照您可能已經嘗試過之最可能發生的問題順序來排列。 這些步驟是從最基本到最複雜的問題順序排列。 這些步驟假設您使用 TCP/IP 通訊協定從另一部電腦連線到 SQL Server 執行個體，這是最常見的狀況。

這些說明在針對「**連線到伺服器**」錯誤進行疑難排解時特別有用，該錯誤可能會是 `Error Number: 11001 (or 53), Severity: 20, State: 0`。 下列是錯誤訊息的範例：

> `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`
>
> `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`
>
> `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

此錯誤通常代表用戶端找不到 SQL Server 執行個體。 這通常會在至少存在下列其中一個問題時發生：

- 裝載 SQL Server 之電腦的名稱
- 執行個體無法解析正確 IP
- 未正確指定 TCP 連接埠號碼

> [!TIP]
> 在 [Solving Connectivity errors to SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) (解決 SQL Server 的連接錯誤)，從 [!INCLUDE[msCoName_md](../../includes/msconame-md.md)] Customer Support Services 取得互動疑難排解頁面。

## <a name="not-included"></a>不包括

- 此主題不包含 SSPI 錯誤的相關資訊。 針對 SSPI 錯誤，請參閱[如何疑難排解「無法產生 SSPI 內容」錯誤訊息](https://support.microsoft.com/kb/811889)。
- 此主題不包含 Kerberos 錯誤的相關資訊。 如需說明，請參閱 [Microsoft Kerberos Configuration Manager for SQL Server](https://www.microsoft.com/download/details.aspx?id=39046)。
- 此主題不包含 Azure SQL Database 連線能力的相關資訊。 如需協助，請參閱 [Troubleshooting connectivity issues with Microsoft Azure SQL Database](/azure/sql-database/troubleshoot-connectivity-issues-microsoft-azure-sql-database) (針對 Microsoft Azure SQL Database 連線問題進行疑難排解)。

## <a name="get-instance-name-from-configuration-manger"></a>從組態管理員取得執行個體名稱

在裝載 SQL Server 執行個體的伺服器上，確認執行個體名稱。 使用 [SQL Server 組態管理員](../../relational-databases/sql-server-configuration-manager.md)。

安裝 SQL Server 時，組態管理員會自動在電腦上安裝。 啟動組態管理員的指示因 SQL Server 和 Windows 版本不同而略有差異。 針對版本特定的詳細資料，請參閱 [SQL Server 組態管理員](../../relational-databases/sql-server-configuration-manager.md)。

1. 登入裝載 SQL Server 執行個體的電腦。
2. 啟動 SQL Server 組態管理員。
3. 在左側窗格中，選取 [SQL Server 服務]。
4. 在右側窗格中，驗證資料庫引擎的執行個體名稱。

   - `SQL SERVER (MSSQLSERVER)` 表示 SQL Server 的預設執行個體。 預設執行個體的名稱為 `<computer name>`。
   - `SQL SERVER (<instance name>)` 表示 SQL Server 的具名執行個體。 具名執行個體的名稱是 `<computer name>\<instance name>`

## <a name="verify---the-instance-is-running"></a>驗證 - 執行個體正在執行

若要驗證執行個體正在執行，請在組態管理員中查看 SQL Server 執行個體旁的符號。

- 綠色的箭頭表示執行個體正在執行。
- 紅色的方形表示執行個體已停止。

若執行個體已停止，請以滑鼠右鍵按一下執行個體，然後按一下 [啟動]。 伺服器執行個體隨即啟動，指示器也會變成綠色的箭頭。

## <a name="verify---sql-server-browser-service-is-running"></a><a name = "startbrowser"></a> 確認 - SQL Server Browser 服務正在執行中

若要連線到具名執行個體，SQL Server Browser 服務必須處於執行狀態。 在組態管理員中，找出 **SQL Server Browser** 服務，並驗證其正在執行。 若其並未執行，請加以啟動。 SQL Server Browser 服務不是預設執行個體的必要項目。

SQL Server 的預設執行個體不需要 SQL Server Browser 服務。

## <a name="testing-a-local-connection"></a>測試本機連線

在針對來自另一部電腦的連線問題進行疑難排解前，請先測試您是否能從在執行 SQL Server 電腦上進行本機安裝的用戶端應用程式進行連線。 在本機進行連線可以避免網路與防火牆問題。 

此程序使用 SQL Server Management Studio。 若未安裝 Management Studio，請參閱[下載 SQL Server Management Studio (SSMS)](../../ssms/download-sql-server-management-studio-ssms.md)。 若無法安裝 Management Studio，您可以使用 `sqlcmd.exe` 公用程式來測試連線。 `sqlcmd.exe` 會與資料庫引擎一同安裝。 如需 `sqlcmd.exe`的相關資訊，請參閱 [sqlcmd 公用程式](../../tools/sqlcmd-utility.md)。)

1. 使用具有 SQL Server 存取權限的登入，登入安裝 SQL Server 的電腦。 (在安裝期間，SQL Server 至少需要一個有 SQL Server 系統管理員身分的登入。 若您不認識系統管理員，請參閱[當系統管理員遭到鎖定時連線到 SQL Server](connect-to-sql-server-when-system-administrators-are-locked-out.md)。)
2. 在 [開始] 頁面中輸入 **SQL Server Management Studio**，或在舊版的 Windows [開始] 功能表上，依序指向 [所有程式] 和 [Microsoft SQL Server]，然後按一下 [SQL Server Management Studio]。
3. 在 [連接到伺服器] 對話方塊的 [伺服器] 類型方塊中，選取 [Database Engine]。 選取 [驗證] 方塊中的 [Windows 驗證]。 在 [伺服器名稱] 方塊中，鍵入下列其中一個連線類型：

   |連線對象|類型|範例|
   |:-----------------|:---------------|:-----------------|
   |預設執行個體|`<computer name>`|`ACCNT27`|
   |具名執行個體|`<computer name\instance name>`|`ACCNT27\PAYROLL`|

   > [!NOTE]
   > 當從同一部電腦上的用戶端應用程式連接到 SQL Server 時，會使用共用記憶體通訊協定。 共用記憶體是一種本機具名管道，因此有時會發生有關管道的錯誤。

   若此時遇到錯誤，您必須先解決問題才能繼續。 問題的發生有很多可能的原因。 登入可能沒有連接的授權。 可能遺漏了預設資料庫。

   > [!NOTE]
   > 某些刻意傳遞至用戶端的錯誤訊息，並未提供足夠資訊來針對問題進行疑難排解。 這是為免向攻擊者提供 SQL Server 相關資訊的安全性功能。 若要檢視有關錯誤的完整資訊，請查看 SQL Server 錯誤記錄檔。 裡面會提供詳細資料。 

4. 若您收到錯誤 `18456 Login failed for user`，線上叢書主題 [MSSQLSERVER_18456](../../relational-databases/errors-events/mssqlserver-18456-database-engine-error.md) 包含錯誤碼的額外資訊。 此外，Aaron Bertrand 的部落格提供了一份內容豐富的錯誤碼清單，請參閱[針對錯誤 18456 進行疑難排解](https://sqlblog.org/2011/01/14/troubleshooting-error-18456) \(英文\)。 如果可以連接，請使用 SSMS 在物件總管的 [管理] 區段中檢視錯誤記錄檔。 否則，請使用 Windows 記事本程式來檢視錯誤記錄檔。 預設位置會隨著您的版本而不同，並且可以在安裝期間變更。 [!INCLUDE[ssSQL15_md](../../includes/sssqlv15-md.md)] 的預設位置為 `C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\Log\ERRORLOG`。 

5. 如果可以使用共用記憶體連接，請使用 TCP 測試連接。 您可以透過在名稱前面指定 `tcp:`，來強制使用 TCP 連線。 例如：

   |連接到：|輸入：|範例：|
   |-----------------|---------------|-----------------|
   |預設執行個體|`tcp:<computer name>`|`tcp:ACCNT27`|
   |具名執行個體|`tcp:<computer name/instance name>`|`tcp:ACCNT27\PAYROLL`|

6. 如果可以使用共用記憶體連接，但不能使用 TCP 連接，則必須修正 TCP 問題。 最可能發生的問題是未啟用 TCP。 若要啟用 TCP，請參閱上述的[啟用通訊協定](#enableprotocols)步驟。

7. 如果您的目標是使用系統管理員帳戶以外的帳戶進行連線，則在您能以系統管理員身分連線之後，請使用用戶端應用程式所使用的 Windows 驗證登入或 SQL Server 驗證登入，再次嘗試連線。

## <a name="get-the-ip-address-of-the-server"></a>取得伺服器的 IP 位址

取得裝載 SQL Server 執行個體電腦的 IP 位址。

1. 在 [開始] 功能表上，按一下 [執行]。 在 [執行] 視窗中輸入 **cmd**，然後按一下 [確定]。
2. 在命令提示字元視窗中，輸入 `ipconfig`，然後按 ENTER。 記下 **IPv4** 位址和 **IPv6** 位址。

  >SQL Server 可使用 IP 版本 4 通訊協定或 IP 版本 6 通訊協定連線。 您的網路允許其中的一個，或兩個都允許。 大部分的人是從疑難排解 **IPv4** 位址開始。 它比較短也比較好輸入。

## <a name="get-the-sql-server-instance-tcp-port"></a><a name = "getTCP"></a>取得 SQL Server 執行個體 TCP 連接埠

在大部分的情況下，您會使用 TCP 通訊協定從另一部電腦連線到資料庫引擎。

1. 在執行 SQL Server 的電腦上，使用 SQL Server Management Studio 連接到 SQL Server 執行個體。 在物件總管中，依序展開 [管理] 和 [SQL Server 記錄檔]，然後按兩下目前的記錄檔。
2. 在記錄檢視器中，按一下工具列上的 [篩選] 按鈕。 在 [訊息包含文字] 方塊中鍵入 `server is listening on`，按一下 [套用篩選]，然後按一下 [確定]。
3. 隨即應會列出與 `Server is listening on [ 'any' <ipv4> 1433]` 相似的訊息。 

此訊息表示這個 SQL Server 執行個體正在接聽此電腦上的所有 IP 位址 (IP 第 4 版)，以及接聽 TCP 連接埠 1433。 (TCP 連接埠 1433 通常是 Database Engine 或 SQL Server 預設執行個體所使用的連接埠。 一個 SQL Server 執行個體只能使用一個連接埠，所以，如果安裝了多個 SQL Server 執行個體，有些執行個體就必須使用其他連接埠號碼)。記下您嘗試連線之目標 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] 執行個體所使用的連接埠號碼。

  > [!NOTE]
  > 其中可能會列出 `IP address 127.0.0.1`。 其稱為回送介面卡位址。 只有位於相同電腦上的處理序能使用它來進行連線。 它可用於疑難排解，但無法使用它從另一部電腦連線。

## <a name="enable-protocols"></a><a name = "enableprotocols"></a>啟用通訊協定

在某些 SQL Server 安裝中，除非系統管理員使用組態管理員來啟用從另一部電腦連線到資料庫引擎，否則不會啟用此功能。 啟用從其他電腦連接

1. 請依前文所述開啟 SQL Server 組態管理員。
2. 使用組態管理員，在左窗格中展開 [SQL Server 網路組態]，然後選取您想要連接的 SQL Server 執行個體。 右窗格會列出可用的連接通訊協定。 通常會啟用共用記憶體。 它只能從同一部電腦使用，因此大部分的安裝都會保持啟用共用記憶體。 若要從另一部電腦連線到 SQL Server，您一般會使用 TCP/IP。 若 TCP/IP 未啟用，請以滑鼠右鍵按一下 [TCP/IP]，然後按一下 [啟用]。
3. 若您變更任何通訊協定的啟用設定，請重新啟動資料庫引擎。 在左側窗格中，選取 [SQL Server 服務]。 在右窗格中，以滑鼠右鍵按一下 Database Engine 的執行個體，然後按一下 [重新啟動]。

## <a name="testing-tcpip-connectivity"></a><a name="testTCPIP"></a>測試 TCP/IP 連線能力

使用 TCP/IP 連接到 SQL Server，需要 Windows 能夠建立連線。 使用 `ping` 工具測試 TCP。

1. 在 [開始] 功能表上，按一下 [執行]。 在 [執行] 視窗中輸入 **cmd**，然後按一下 [確定]。 
2. 在命令提示字元視窗中，先輸入 `ping <ip address>`，然後輸入執行 SQL Server 的電腦 IP 位址。 例如：

    - IPv4：`ping 192.168.1.101`
    - IPv6：`ping fe80::d51d:5ab5:6f09:8f48%11`

3. 若您的網路已適當進行設定，`ping` 會傳回 `Reply from <IP address>` 與一些額外資訊。 若 `ping` 傳回 `Destination host unreachable` 或 `Request timed out`，則表示 TCP/IP 未正確設定。 此時的錯誤可能表示用戶端電腦、伺服器電腦，或網路方面 (例如路由器) 發生了問題。 若要針對網路問題進行疑難排解，請參閱[針對 TCP/IP 問題進行進階疑難排解](/windows/client-management/troubleshoot-tcpip)。
4. 接下來，如果使用 IP 位址順利完成 ping 測試，請再測試電腦名稱可否解析成 TCP/IP 位址。 在用戶端電腦的命令提示字元視窗中，先輸入 `ping` ，然後再輸入執行 SQL Server 的電腦名稱。 例如： `ping newofficepc` 。
5. 若向 IP 位址發出的 `ping` 成功，但向電腦發出的 `ping` 傳回 `Destination host unreachable` 或 `Request timed out`，表示您在用戶端電腦上可能擁有舊 (過時) 的快取名稱解析資訊。 輸入 `ipconfig /flushdns` 以清除 DNS (動態名稱解析) 快取。 再次用名稱 ping 電腦。 清空 DNS 快取後，用戶端電腦就會檢查有關伺服器電腦 IP 位址的最新資訊。
6. 若您的網路已適當進行設定，`ping` 會傳回 `Reply from <IP address>` 與一些額外資訊。 若您可以成功使用 IP 位址 Ping 伺服器電腦，但在使用電腦名稱 Ping 時收到 `Destination host unreachable.` 或 `Request timed out.` 之類的錯誤，則表示名稱解析並未設定正確。 (如需詳細資訊，請參閱先前參考的 2006 年文章 [Chapter 16 – Troubleshooting TCP/IP](https://support.microsoft.com/kb/169790) (第 16 章 – 疑難排解 TCP/IP)。)要連線到 SQL Server 不需要仰賴成功的名稱解析，但如果電腦名稱無法解析為 IP 位址，則必須指定 IP 位址才能連線。 名稱解析可在稍後進行修正。

## <a name="open-a-port-in-the-firewall"></a>在防火牆中開啟連接埠

預設會開啟 Windows 防火牆，並封鎖來自另一部電腦的連線。 若要從另一部電腦使用 TCP/IP 連接，您必須在 SQL Server 電腦上設定防火牆允許連接到 Database Engine 所使用的 TCP 連接埠。 根據預設，預設執行個體正在 TCP 連接埠 1433 上接聽。 若您擁有具名執行個體，或是您變更了預設執行個體連接埠，則 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] TCP 連接埠可能會在另一個連接埠上接聽。 請參閱[取得 SQL Server 執行個體 TCP 連接埠](#getTCP)。

如果您要連線到具名執行個體或 TCP 連接埠 1433 以外的連接埠，您也必須為 SQL Server Browser 服務開啟 UDP 連接埠 1434。 如需在 Windows 防火牆中開啟連接埠的逐步指示，請參閱[設定用於資料庫引擎存取的 Windows 防火牆](configure-a-windows-firewall-for-database-engine-access.md)。

## <a name="test-the-connection"></a>測試連線

一旦可在同一部電腦上使用 TCP 連線，就可以嘗試從用戶端電腦連線。 理論上，您可以使用任何用戶端應用程式，但為避免太過複雜，請在用戶端安裝 SQL Server 管理工具，嘗試使用 SQL Server Management Studio。

1. 在使用 SQL Server Management Studio 的用戶端電腦上，嘗試以 IP 位址加逗號加連接埠號碼的格式，使用 IP 位址和 TCP 連接埠號碼連線。 例如： `192.168.1.101,1433` 。 若此連線失敗，可能是下列其中一個問題：

   - IP 位址的 `ping` 並未正常運作，表示一般 TCP 設定問題。 請返回[測試 TCP/IP 連線能力](#testTCPIP)一節。
   - SQL Server 未接聽 TCP 通訊協定。 請返回[啟用通訊協定](#enableprotocols)一節。
   - SQL Server 正在接聽所指定連接埠以外的連接埠。 請返回[取得 TCP 連接埠號碼](#getTCP)一節。
   - 防火牆正在封鎖 SQL Server TCP 連接埠。 請返回[在防火牆中開啟連接埠](#open-a-port-in-the-firewall)一節。

2. 一旦可以使用 IP 位址和連接埠號碼連線，請嘗試使用不含連接埠號碼的 IP 位址來進行連線。 預設的執行個體只要使用 IP 位址即可。 針對具名執行個體，請使用 IP 位址和執行個體名稱，其格式為 IP 位址加反斜線加執行個體名稱，例如 `192.168.1.101\<instance name>`；如果仍然無法運作，則可能是下列其中一個問題︰

   - 如果您連線到預設執行個體，其可能會接聽 TCP 連接埠 1433 以外的連接埠，而用戶端不會嘗試連線到正確的連接埠號碼。
   - 如果您連線到具名執行個體，就不會將連接埠號碼傳回用戶端。

   這兩個問題都與 SQL Server Browser 服務有關，因為都是向用戶端提供連接埠號碼。 解決方案為︰

   - 啟動 SQL Server Browser 服務。 請參閱指示，以[在 SQL Server 組態管理員中啟動瀏覽器](#startbrowser)。
   - 防火牆正在封鎖 SQL Server Browser 服務。 在防火牆中開啟 UDP 連接埠 1434。 請返回[在防火牆中開啟連接埠](#open-a-port-in-the-firewall)一節。 請確定您開啟的是 UDP 連接埠，而非 TCP 連接埠。
   - 路由器正在封鎖 UDP 連接埠 1434 資訊。 UDP 通訊 (使用者資料包通訊協定) 不是針對穿過路由器所設計。 這會防止低優先順序流量填滿網路。 您可以設定路由器來轉送 UDP 流量，或者可決定連線時一律提供連接埠號碼。
   - 如果用戶端電腦使用的是 Windows 7 或 Windows Server 2008 (或較新的作業系統)，作業系統可能會捨棄UDP 流量，因為傳回伺服器回應的 IP 位址和被查詢的位址不同。 這是封鎖「鬆散的來源對應」的安全性功能。 如需詳細資訊，請參閱下列《線上叢書》主題的 **多個伺服器 IP 位址** 一節：[疑難排解：等候時間逾時](/previous-versions/sql/sql-server-2008-r2/ms190181(v=sql.105))。 這是 SQL Server 2008 R2 的文章，但是原則仍然適用。 您可以將用戶端設定為使用正確的 IP 位址，或者可決定連線時一律提供連接埠號碼。

3. 一旦可以使用 IP 位址 (或具名執行個體的 IP 位址和執行個體名稱) 連線，請嘗試使用電腦名稱 (或具名執行個體的電腦名稱和執行個體名稱) 連線。 將 `tcp:` 放在電腦名稱前面，以強制 TCP/IP 連線。 例如， `ACCNT27`電腦上的預設執行個體使用 `tcp:ACCNT27` 。該電腦上稱為 `PAYROLL`的具名執行個體使用 `tcp:ACCNT27\PAYROLL` 。如果可以使用 IP 位址連線，但不能使用電腦名稱連線，表示有名稱解析問題。 請返回 **測試 TCP/IP 連線能力** 一節 (第 4 節)。

4. 一旦可以使用電腦名稱強制 TCP 連線，請嘗試使用電腦名稱連線，但不強制執行 TCP。 例如，預設執行個體只使用電腦名稱，如 `CCNT27`。具名的執行個體使用電腦名稱和執行個體名稱，如 `ACCNT27\PAYROLL`。如果強制執行 TCP 時可以連線，但不強制執行 TCP 時無法連線，則用戶端可能使用的是另一個通訊協定 (如具名管道)。

    1. 在用戶端電腦上使用 SQL Server 組態管理員，在左窗格中展開 [SQL Native Client <版本> 組態] ，然後選取 [用戶端通訊協定]。
    2. 右窗格中，請確定已啟用 TCP/IP。 如果 TCP/IP 停用，請以滑鼠右鍵按一下 [TCP/IP]，然後按一下 [啟用]。
    3. 確定 TCP/IP 通訊協定的順序值小於具名管道 (或較舊版本上的 VIA) 通訊協定的順序。 通常共用記憶體的順序應該為 1，TCP/IP 的順序為 2。 只有當用戶端和 SQL Server 在同一部電腦上執行時，才使用共用記憶體。 會依序嘗試所有已啟用的通訊協定，直到有一個通訊協定成功為止，但若連線不是連到同一部電腦，則會跳過共用記憶體。