---
title: sqlcmd 公用程式
description: Sqlcmd 公用程式可讓您使用不同模式輸入 Transact-SQL 陳述式、系統程序與指令檔，以及使用 ODBC 來執行 Transact-SQL 批次。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: tools-other
ms.topic: conceptual
helpviewer_keywords:
- statements [SQL Server], command prompt
- QUIT command
- Transact-SQL statements, command prompt
- EXIT command
- sqlcmd commands
- ED command
- sqlcmd utility
- command prompt utilities [SQL Server], sqlcmd
- '!! command'
- stored procedures [SQL Server], command prompt
- system stored procedures [SQL Server], command prompt
- sqlcmd utility, about sqlcmd utility
- scripts [SQL Server], command prompt
- RESET command
- GO command
ms.assetid: e1728707-5215-4c04-8320-e36f161b834a
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.custom: seo-lt-2019
ms.date: 09/11/2020
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017'
ms.openlocfilehash: 0c822321323eb5f74fda34df2d540b7c5c79c382
ms.sourcegitcommit: e40e75055c1435c5e3f9b6e3246be55526807b4c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98151299"
---
# <a name="sqlcmd-utility"></a>sqlcmd 公用程式

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

> 針對 SQL Server 2014 與更舊版本，請參閱 [sqlcmd 公用程式](/previous-versions/sql/2014/tools/sqlcmd-utility?view=sql-server-2014&preserve-view=true)。
>
> 如需在 Linux 上使用 sqlcmd 的詳細資訊，請參閱[在 Linux上安裝 sqlcmd 與 bcp](../linux/sql-server-linux-setup-tools.md)。

 **sqlcmd** 公用程式可讓您透過下列各種可用的模式，在命令列提示字元中輸入 Transact-SQL 陳述式、系統程序和指令碼檔案：

- 在命令提示字元中。
- 在 [查詢編輯器] 中的 SQLCMD 模式中。
- 在 Windows 指令碼檔案中。
- 在 SQL Server Agent 作業的作業系統(Cmd.exe) 作業步驟中。

此公用程式會使用 ODBC 來執行 Transact-SQL 批次。

## <a name="download-the-latest-version-of-sqlcmd-utility"></a>下載最新版的 sqlcmd 公用程式

**[![下載 sqlcmd for x64](../ssdt/media/download.png) 下載適用於 SQL Server (x64) 的 Microsoft 命令列公用程式 15 (2.6 MB)](https://go.microsoft.com/fwlink/?linkid=2142258)**
<br>**[![下載 sqlcmd for x86](../ssdt/media/download.png) 下載適用於 SQL Server (x86) 的 Microsoft 命令列公用程式 15 (2.3 MB)](https://go.microsoft.com/fwlink/?linkid=2142257)**

命令列工具已正式推出，不過，其將透過 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 的安裝程式套件發行。

**版本資訊**

版本號碼：15.0.2<br>
組建編號：15.0.2000.5<br>
發行日期：2020 年 9 月 11 日

新版本的 SQLCMD 支援 Azure AD 驗證，包括 SQL Database、Azure Synapse Analytics 和 Always Encrypted 功能的 Multi-Factor Authentication (MFA) 支援。
新的 BCP 支援 Azure AD 驗證，包括 SQL Database 與 Azure Synapse Analytics 的 Multi-Factor Authentication (MFA) 支援。

**系統需求**：Windows 10、Windows 7、Windows 8、Windows 8.1、Windows Server 2008 - 2019。

此元件同時需要 [Windows Installer 4.5](https://www.microsoft.com/download/details.aspx?id=8483) 及 [Microsoft ODBC Driver 17 for SQL Server](https://aka.ms/downloadmsodbcsql)。
 
若要檢查 SQLCMD 版本執行 `sqlcmd -?` 命令，並確認正在使用 15.0.2000.5 版或更新版本。

> [!NOTE]
> 您需要 13.1 版或更新版本才能支援 Always Encrypted (`-g`) 與 Azure Active Directory 驗證 (`-G`)。 (您可能已在電腦上安裝多個 sqlcmd.exe 版本。 請務必使用正確的版本。 若要判斷版本，請執行 `sqlcmd -?`。)

根據預設，您可以從 Azure Cloud Shell 試用已預先安裝的 sqlcmd 公用程式：[![啟動 Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "啟動 Cloud Shell")](https://shell.azure.com)

若要在 SSMS 中執行 sqlcmd 陳述式，請從上方導覽 [查詢] 功能表的下拉式清單中選取 [SQLCMD 模式]。  

> [!IMPORTANT]
> [!INCLUDE[ssManStudioFull_md](../includes/ssmanstudiofull-md.md)] (SSMS) 使用 Microsoft [!INCLUDE[dnprdnshort_md](../includes/dnprdnshort-md.md)] SqlClient，在 [查詢編輯器] 中執行正規和 SQLCMD 模式。 從命令列執行 **sqlcmd** 時，**sqlcmd** 會使用 ODBC 驅動程式。 因為可能會套用不同的預設選項，因此，以 SQLCMD 模式在 [!INCLUDE[ssManStudioFull_md](../includes/ssmanstudiofull-md.md)] 中以及在 **sqlcmd** 公用程式中執行相同的查詢時，可能會看到不同的行為。  
>

目前， **sqlcmd** 不需要在命令列選項與值之間保留一個空格。 但是在未來的版本中，可能會需要在命令列選項與值之間空一個空格。  

 其他主題： 

- [啟動 sqlcmd 公用程式](../ssms/scripting/sqlcmd-start-the-utility.md)
- [使用 sqlcmd 公用程式](../ssms/scripting/sqlcmd-use-the-utility.md)
  
## <a name="syntax"></a>語法

```cmd
sqlcmd
   -a packet_size
   -A (dedicated administrator connection)
   -b (terminate batch job if there is an error)
   -c batch_terminator
   -C (trust the server certificate)
   -d db_name
   -D
   -e (echo input)
   -E (use trusted connection)
   -f codepage | i:codepage[,o:codepage] | o:codepage[,i:codepage]
   -g (enable column encryption)
   -G (use Azure Active Directory for authentication)
   -h rows_per_header
   -H workstation_name
   -i input_file
   -I (enable quoted identifiers)
   -j (Print raw error messages)
   -k[1 | 2] (remove or replace control characters)  
   -K application_intent  
   -l login_timeout  
   -L[c] (list servers, optional clean output)  
   -m error_level  
   -M multisubnet_failover  
   -N (encrypt connection)  
   -o output_file  
   -p[1] (print statistics, optional colon format)  
   -P password  
   -q "cmdline query"  
   -Q "cmdline query" (and exit)  
   -r[0 | 1] (msgs to stderr)  
   -R (use client regional settings)  
   -s col_separator  
   -S [protocol:]server[instance_name][,port]  
   -t query_timeout  
   -u (unicode output file)  
   -U login_id  
   -v var = "value"
   -V error_severity_level
   -w column_width
   -W (remove trailing spaces)
   -x (disable variable substitution)
   -X[1] (disable commands, startup script, environment variables, optional exit)
   -y variable_length_type_display_width
   -Y fixed_length_type_display_width
   -z new_password
   -Z new_password (and exit)
   -? (usage)
```
  
## <a name="command-line-options"></a>命令列選項

**登入相關選項**

**-A**  
使用專用管理員連接 (DAC) 來登入 SQL Server。 這種連接可用以進行伺服器的疑難排解。 此連接只適用於支援 DAC 的伺服器電腦。 如果無法使用 DAC， **sqlcmd** 會產生一則錯誤訊息，並結束作業。 如需有關 DAC 的詳細資訊，請參閱 [資料庫管理員的診斷連線](../database-engine/configure-windows/diagnostic-connection-for-database-administrators.md)。 -G 選項不支援 -A 選項。 當使用 -A 連線到 SQL Database 時，您必須為 SQL Server 系統管理員。 DAC 無法供 Azure Active Directory 系統管理員使用。

**-C**  
這個參數由用戶端所設定，以隱含方式信任伺服器憑證而且不進行驗證。 這個選項相當於 ADO.NET 選項 `TRUSTSERVERCERTIFICATE = true`。  

**-d** _db_name_  
當您啟動 **sqlcmd** 時會發出 `USE`*db_name* 陳述式。 這個選項會設定 **sqlcmd** 指令碼變數 SQLCMDDBNAME。 這項參數會指定初始資料庫。 預設值為您登入的預設資料庫屬性。 如果資料庫不存在，系統會產生一則錯誤訊息，且會結束 **sqlcmd** 。  

**-D**  
將提供給 -S 的伺服器名稱解譯為 DSN，而不是主機名稱。 如需詳細資訊，請參閱[使用 sqlcmd 進行連線](../connect/odbc/linux-mac/connecting-with-sqlcmd.md)中的 「sqlcmd 和 bcp 中的 DSN 支援」。

> [!NOTE]
> 只有 Linux 和 MacOS 用戶端可使用 -D 選項。 在 Windows 用戶端上，已移除且忽略以前稱為現已過時的選項。

**-l** _login_timeout_  
指定在您嘗試連接到伺服器時， **sqlcmd** 登入 ODBC 驅動程式逾時之前的秒數。 這個選項會設定 **sqlcmd** 指令碼變數 SQLCMDLOGINTIMEOUT。 **sqlcmd** 的預設登入逾時值是 8 秒。 當使用 **-G** 選項連線到 SQL Database 或 Azure Synapse Analytics，並使用 Azure Active Directory 進行驗證時，建議使用至少 30 秒的逾時值。 此登入逾時必須是介於 0 和 65534 之間的數字。 如果所提供的值不是數值或不在該範圍內， **sqlcmd** 就會產生錯誤訊息。 0 值指定逾時值無限。

**-E**  
使用信任連線登入 SQL Server，而不用使用者名稱和密碼。 根據預設，如果沒有指定 **-E** ， **sqlcmd** 會使用信任連接選項。  

**-E** 選項會忽略可能出現的使用者名稱與密碼環境變數設定，例如 SQLCMDPASSWORD。 如果同時使用 **-E** 選項和 **-U** 或 **-P** 選項，就會產生錯誤訊息。  

**-g**  
將 [資料行加密設定] 設定為 `Enabled`。 如需詳細資訊，請參閱 [Always Encrypted](../relational-databases/security/encryption/always-encrypted-database-engine.md)。 僅支援儲存在 Windows 憑證存放區中的主要金鑰。 -g 參數至少需要 **sqlcmd**[13.1](https://go.microsoft.com/fwlink/?LinkID=825643)版。 若要判斷您的版本，請執行 `sqlcmd -?`。

**-G**  
這個參數在連線到 SQL Database 或 Azure Synapse Analytics 時由用戶端使用，以指定使用 Azure Active Directory 驗證來驗證使用者。 這個選項會設定 **sqlcmd** 指令碼變數 SQLCMDUSEAAD = true。 -G 參數至少需要 **sqlcmd**[13.1](https://go.microsoft.com/fwlink/?LinkID=825643)版。 若要判斷您的版本，請執行 `sqlcmd -?`。 如需詳細資訊，請參閱[使用 Azure Active Directory 驗證連線到 SQL Database 或 Azure Synapse Analytics](/azure/azure-sql/database/authentication-aad-overview)。 -G 選項不支援 -A 選項。

> [!IMPORTANT]
> `-G` 選項只適用於 Azure SQL Database 與 Azure 資料倉儲。
> Linux 或 macOS 目前不支援 AAD 互動式驗證。 AAD 整合式驗證需要 [Microsoft ODBC Driver 17 for SQL Server](https://aka.ms/downloadmsodbcsql) 17.6.1 版或更高版本，以及正確設定的 Kerberos 環境。

- **Azure Active Directory 使用者名稱和密碼：** 

    當您想要使用 Azure Active Directory 使用者名稱和密碼時，您可以提供 **-G** 選項，並同時提供 **-U** 和 **-P** 選項來使用使用者名稱和密碼。

    ```cmd
    Sqlcmd -S testsrv.database.windows.net -d Target_DB_or_DW -U bob@contoso.com -P MyAADPassword -G
    ```

    -G 參數會在後端產生下列連接字串︰ 

    ```cmd
     SERVER = Target_DB_or_DW.testsrv.database.windows.net;UID= bob@contoso.com;PWD=MyAADPassword;AUTHENTICATION = ActiveDirectoryPassword 
    ```

- **Azure Active Directory 整合式**

   若是 Azure Active Directory 整合式驗證，請提供 **-G** 選項，但不提供使用者名稱或密碼。
   *Linux 或 macOS 目前不支援 AAD 整合式驗證*。

    ```cmd
    Sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G
    ```

    這會在後端產生下列連線字串︰ 

    ```cmd
    SERVER = Target_DB_or_DW.testsrv.database.windows.net Authentication = ActiveDirectoryIntegrated; Trusted_Connection=NO
    ```

    > [!NOTE]
    > `-E` 選項 (Trusted_Connection) 不能搭配 `-G` 選項一起使用。

- **Azure Active Directory 互動式**

    Azure SQL Database 與 Azure Synapse Analytics 的 Azure AD 互動式驗證，其允許使用支援 Multi-Factor Authentication 的互動式方法。 如需詳細資訊，請參閱 [Active Directory 互動式驗證](../ssdt/azure-active-directory.md#active-directory-interactive-authentication)。 

   Azure AD 互動需要 **sqlcmd** [15.0.1000.34 版](#download-the-latest-version-of-sqlcmd-utility) 或更新版本，以及 [ODBC 17.2 版或更新版本](https://aka.ms/downloadmsodbcsql)。  

   若要啟用互動式驗證，請在不使用密碼的情況下，僅以使用者名稱 (-U) 提供 -G 選項。

   下列範例會使用 Azure AD 互動模式來匯出資料，其中指出使用者代表 AAD 帳戶的使用者名稱。 此為與在上一區段中所使用的相同範例：*Azure Active Directory 使用者名稱與密碼*。  

   互動模式需要手動輸入密碼，若為已啟用多重要素驗證的帳戶，請完成您已設定的 MFA 驗證方法。

   ```cmd
   sqlcmd -S testsrv.database.windows.net -d Target_DB_or_DW -G -U alice@aadtest.onmicrosoft.com
   ```

   先前的命令會在後端產生下列連接字串︰  

   ```cmd
   SERVER = Target_DB_or_DW.testsrv.database.windows.net;UID=alice@aadtest.onmicrosoft.com; AUTHENTICATION = ActiveDirectoryInteractive   
   ```

   如果 Azure AD 使用者是使用 Windows 帳戶的網域同盟使用者，則命令列中所需的使用者名稱會包含其網域帳戶 (例如，joe@contoso.com 如下所示)：

   ```cmd
   sqlcmd -S testsrv.database.windows.net -d Target_DB_or_DW -G -U joe@contoso.com  
   ```

   如果來賓使用者存在於特定 Azure AD 中，且屬於 SQL Database 中具有執行 sqlcmd 命令的資料庫權限群組，則會使用其來賓使用者別名 (例如， *keith0@adventureworks.com* )。

  >[!IMPORTANT]
  >使用 `-G` 與 `-U` 選項搭配 SQLCMD 時，存在一個已知問題：若在 `-G` 選項之前放置 `-U` 選項可能會導致驗證失敗。 請一律先以 `-G` 選項開始，再放置 `-U` 選項。

**-H** _workstation_name_  
 這是工作站名稱。 這個選項會設定 **sqlcmd** 指令碼變數 SQLCMDWORKSTATION。 工作站名稱列在 **sys.sysprocesses** 目錄檢視的 **hostname** 資料行中，而且可以使用預存程序 **sp_who** 傳回名稱。 如果未指定這個選項，預設值為目前的電腦名稱。 這個名稱可用來識別不同的 

**sqlcmd** 工作階段。  

**-j** ：將原始錯誤訊息列印至畫面。
  
 **-K** _application_intent_  
 宣告連接到伺服器時的應用程式工作負載類型。 目前唯一支援的值是 **ReadOnly**。 若未指定 **-K** ，sqlcmd 公用程式將無法支援在 AlwaysOn 可用性群組中連接至次要複本。 如需詳細資訊，請參閱[使用中次要：可讀取的次要複本 (AlwaysOn 可用性群組)](../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)  
  
**-M** _multisubnet_failover_  
 在連線到 SQL Server 可用性群組的可用性群組接聽程式或 SQL Server 容錯移轉叢集執行個體時，一律指定 **-M**。 **-M** 可提供對 (目前) 作用中伺服器更快速的偵測與連接。 如果未指定 **-M**，則會關閉 **-M**。 如需詳細資訊，請參閱[接聽程式、用戶端連線能力、應用程式容錯移轉](../database-engine/availability-groups/windows/listeners-client-connectivity-application-failover.md)、[建立及設定可用性群組 &#40;SQL Server&#41;](../database-engine/availability-groups/windows/creation-and-configuration-of-availability-groups-sql-server.md)、[容錯移轉叢集和 AlwaysOn 可用性群組 (SQL Server)](../database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server.md) 及[使用中次要：可讀取的次要複本 (AlwaysOn 可用性群組)](../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)。
  
 **-N**  
 用戶端會用這個參數要求加密的連接。  
  
 **-P** _password_  
 這是一個使用者指定的密碼。 密碼會區分大小寫。 如果使用 -U 選項但未使用 **-P** 選項，且未設定 SQLCMDPASSWORD 環境變數， **sqlcmd** 會提示使用者輸入密碼。 我們不建議使用 Null 密碼，但您可以針對參數值使用一組連續雙引號來指定 Null 密碼：

- **-P ""**

我們建議您使用強式密碼。

#### <a name="use-a-strong-password"></a>[**使用強式密碼！**](../relational-databases/security/strong-passwords.md)

 密碼提示的顯示方式，會以將密碼提示輸出到主控台的方式顯示，如： `Password:`  
  
 使用者輸入為隱藏狀態。 這表示畫面上不會顯示任何內容，而且游標也不會移動。  
  
 SQLCMDPASSWORD 環境變數可讓您設定目前工作階段的預設密碼。 因此，您不需要將密碼寫在批次檔中。  
  
 下列範例會先在命令提示字元處設定 SQLCMDPASSWORD 變數，再存取 **sqlcmd** 公用程式。 在命令提示字元中，輸入：  
  
 `SET SQLCMDPASSWORD= p@a$$w0rd`  
 請在下列命令提示字元之下，輸入：  
  
 `sqlcmd`  
  
 如果使用者名稱和密碼的組合不正確，會產生錯誤訊息。  
  
**注意！**  保留 OSQLPASSWORD 環境變數的目的是為了與舊版相容。 SQLCMDPASSWORD 環境變數會優先於 OSQLPASSWORD 環境變數。 OSQLPASSWORD 現在已不再共用，**sqlcmd** 與 **osql** 公用程式可以在彼此之間使用，而不會受到干擾。 舊版指令碼仍可繼續使用。  
  
 如果同時使用 **-P** 選項和 **-E** 選項，就會產生錯誤訊息。  
  
 如果 **-P** 選項後面有多個引數，便會產生錯誤訊息，而且程式將會結束。  
  
 **-S** [*protocol*:]*server*[ **\\** _instance\_name_][ **,** _port_]  
 指定要連線的 SQL Server 執行個體。 它會設定 **sqlcmd** 指令碼變數 SQLCMDSERVER。  
  
 指定 *server_name*，即可連線至該伺服器電腦上之 SQL Server 的預設執行個體。 指定 *server_name* [ **\\** _instance\_name_ ]，即可連線到該伺服器電腦上 SQL Server 的具名執行個體。 如果未指定伺服器電腦，**sqlcmd** 會連線到本機電腦上 SQL Server 的預設執行個體。 當您從網路的遠端電腦執行 **sqlcmd** 時，需要這個選項。  
  
 *protocol* 可以是 **tcp** (TCP/IP)、 **lpc** (共用記憶體) 或 **np** (具名管道)。  
  
 如果您啟動 **sqlcmd** 時，並未指定 *server_name* [ **\\** _instance\_name_ ]，SQL Server 會檢查並使用 SQLCMDSERVER 環境變數。  
  
> [!NOTE]  
>  保留 OSQLSERVER 環境變數的目的是為了與舊版相容。 SQLCMDSERVER 環境變數優先於 OSQLSERVER 環境變數；這表示您可以先後使用 **sqlcmd** 和 **osql** ，它們不會互相干擾，舊的指令碼仍能運作。  
  
 **-U** _login_id_  
 是登入名稱或自主資料庫使用者名稱。 針對自主資料庫使用者，您必須提供資料庫名稱選項 (-d)。  
  
> [!NOTE]  
>  可利用 OSQLUSER 環境變數達到回溯相容性。 SQLCMDUSER 環境變數優先於 OSQLUSER 環境變數。 這表示您可以先後使用 **sqlcmd** 和 **osql** ，而不會發生互相干擾的狀況。 這也表示現有的 **osql** 指令碼仍然有效。  
  
 如果沒有指定 **-U** 選項或 **-P** 選項，**sqlcmd** 便會嘗試使用 Microsoft Windows 驗證模式進行連線。 這項驗證以執行 **sqlcmd** 之使用者的 Windows 帳戶為基礎。  
  
 如果同時使用 **-U** 選項和 **-E** 選項 (稍後於本文中描述)，即會產生錯誤訊息。 如果 **-U** 選項後面有多個引數，就會產生錯誤訊息並結束程式。  
  
 **-z** _new_password_  
 變更密碼：  
  
 `sqlcmd -U someuser -P s0mep@ssword -z a_new_p@a$$w0rd`  
  
 **-Z** _new_password_  
 變更密碼並結束：  
  
 `sqlcmd -U someuser -P s0mep@ssword -Z a_new_p@a$$w0rd`  
  
 **輸入/輸出選項**  
  **-f** _codepage_ | **i:** _codepage_[ **,o:** _codepage_] | **o:** _codepage_[ **,i:** _codepage_]  
 指定輸入和輸出字碼頁。 字碼頁碼是一個數值，指定已安裝的 Windows 字碼頁。  
  
 字碼頁轉換規則：  
  
- 如果未指定字碼頁，則 **sqlcmd** 會在輸入檔和輸出檔中使用目前的字碼頁，除非輸入檔是 Unicode 檔，否則就不需要轉換。  
  
- **sqlcmd** 會自動識別位元組由大到小 (Big-Endian) 和位元組由小到大 (Little-Endian) 的 Unicode 輸入檔。 如果已指定 **-u** 選項，則輸出一律為位元組由小到大的 Unicode。  
  
- 如果未指定輸出檔，則輸出字碼頁會是主控台字碼頁。 此方法可讓輸出正確地顯示在主控台上。  
  
- 假設多個輸入檔都是相同的字碼頁。 Unicode 與非 Unicode 的輸入檔可以混合使用。  
  
 在命令提示字元下輸入 **chcp** ，以驗證 Cmd.exe 的字碼頁。  
  
 **-i** _input_file_[ **,** _input\_file2_...]  
 識別包含 SQL 陳述式或預存程序的批次之檔案。 您可以指定多個檔案，它們會依照順序加以讀取和處理。 檔案名稱之間不能有空格。 **sqlcmd** 會先檢查指定的檔案是否全部存在。 如果有一個或多個檔案不存在， **sqlcmd** 會結束作業。 -i 和 -Q/-q 為互斥選項。  
  
 路徑範例：  

```cmd
-i C:\<filename>  
-i \\<Server>\<Share$>\<filename>  
-i "C:\Some Folder\<file name>"  
```
  
 包含空格的檔案路徑必須用引號括住。  
  
 這個選項可以使用一次以上： **-i**_input\_file_ **-I**_I input_file_。  
  
 **-o** _output_file_  
 識別用來接收 **sqlcmd** 輸出的檔案。  
  
 如果指定 **-u** ， *output_file* 會以 Unicode 格式儲存。 如果檔案名稱無效，系統會產生一則錯誤訊息，且會結束 **sqlcmd** 。 **sqlcmd** 不支援同時將多個 **sqlcmd** 處理序寫入相同的檔案。 檔案輸出會損毀或不正確。 請注意 **-f** 參數也與檔案格式相關。 如果不存在，則會建立這個檔案。 並覆寫先前 **sqlcmd** 工作階段所產生的同名檔案。 此處所指定的檔案並不是 **stdout** 檔案。 如果指定 **stdout** 檔案，則不會使用這個檔案。  
  
 路徑範例：  

```cmd
-o C:< filename>  
-o \\<Server>\<Share$>\<filename>  
-o "C:\Some Folder\<file name>"  
 ```
 包含空格的檔案路徑必須用引號括住。  
  
 **-r**[**0** | **1**]  
 將錯誤訊息輸出重新導向至畫面 (**stderr**)。 如果您沒有指定參數，或指定 **0**，只會重新導向嚴重性層級 11 (含) 以上的錯誤訊息。 如果您指定 **1**，便會重新導向包括 PRINT 在內的所有錯誤訊息輸出。 如果您使用 -o，不會有任何作用。 根據預設，訊息會傳送到 **stdout**。  
  
 **-R**  
 讓 **sqlcmd** 根據用戶端的地區設定，將從 SQL Server 擷取的數值、貨幣、日期和時間資料行當地語系化。 根據預設，這些資料行會使用伺服器的地區設定來顯示。  
  
 **-u**  
 指定無論 *input_file* 的格式為何， *output_file* 均以 Unicode 格式儲存。  
  
 **查詢執行選項**  
  **-e**  
 將輸入指令碼寫入標準的輸出裝置 (**stdout**) 中。  
  
 **-I**  
 將 SET QUOTED_IDENTIFIER 連接選項設為 ON。 依預設，此選項設定為 OFF。 如需詳細資訊，請參閱 [SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](~/t-sql/statements/set-quoted-identifier-transact-sql.md)。  
  
 **-q "** _cmdline query_ **"**  
 當 **sqlcmd** 啟動時執行查詢，但查詢執行完成時不要結束 **sqlcmd** 。 可以執行多項以分號分隔的查詢。 請依照下列範例所示，利用引號來括住查詢。  
  
 在命令提示字元中，輸入：  
  
 `sqlcmd -d AdventureWorks2012 -q "SELECT FirstName, LastName FROM Person.Person WHERE LastName LIKE 'Whi%';"`  
  
 `sqlcmd -d AdventureWorks2012 -q "SELECT TOP 5 FirstName FROM Person.Person;SELECT TOP 5 LastName FROM Person.Person;"`  
  
> [!IMPORTANT]  
>  請勿在查詢中使用 GO 結束字元。  
  
 如果使用此選項時指定 **-b** ， **sqlcmd** 會發生錯誤，並結束作業。 本文稍後將描述 **-b**。  
  
 **-Q "** _cmdline query_ **"**  
 啟動 **sqlcmd** 時執行查詢，然後立即結束 **sqlcmd**。 可以執行多項以分號分隔的查詢。  
  
 請依照下列範例所示，利用引號來括住查詢。  
  
 在命令提示字元中，輸入：  
  
 `sqlcmd -d AdventureWorks2012 -Q "SELECT FirstName, LastName FROM Person.Person WHERE LastName LIKE 'Whi%';"`  
  
 `sqlcmd -d AdventureWorks2012 -Q "SELECT TOP 5 FirstName FROM Person.Person;SELECT TOP 5 LastName FROM Person.Person;"`  
  
> [!IMPORTANT]  
>  請勿在查詢中使用 GO 結束字元。  
  
 如果使用此選項時指定 **-b** ， **sqlcmd** 會發生錯誤，並結束作業。 本文稍後將描述 **-b**。  
  
 **-t** _query_timeout_  
 指定命令 (或 SQL 陳述式) 逾時之前的秒數。這個選項會設定 **sqlcmd** 指令碼變數 SQLCMDSTATTIMEOUT。 如果未指定 *time_out* 值，命令不會逾時。*query**time_out* 必須是介於 1 與 65534 之間的數字。 如果所提供的值不是數值或不在該範圍內， **sqlcmd** 就會產生錯誤訊息。  
  
> [!NOTE]  
>  實際逾時值可能與指定的 *time_out* 值之間有幾秒的差異。  
  
 **-vvar =**  _value_[ **var =** _value_...]  
 建立 **sqlcmd** 指令碼中所能使用的 **sqlcmd** 指令碼變數。 如果值包含空格，請用引號括住該值。 您可以指定多個 _**var**_= **"** _values_ **"** 值。 如果指定的任何值發生錯誤， **sqlcmd** 會產生一則錯誤訊息，並結束作業。  
  
 `sqlcmd -v MyVar1=something MyVar2="some thing"`  
  
 `sqlcmd -v MyVar1=something -v MyVar2="some thing"`  
  
 **-x**  
 讓 **sqlcmd** 忽略指令碼變數。 當指令碼所包含的許多 INSERT 陳述式可能含有與一般變數 (例如 $(*variable_name*)) 相同格式的字串時，此參數就很有用。  
  
 **格式化選項**  
  **-h** _headers_  
 指定資料行標頭之間所要列印的資料列數。 預設值是每一組查詢結果各列印一次標頭。 這個選項會設定 **sqlcmd** 指令碼變數 SQLCMDHEADERS。 使用 **-1** 來指定不列印標頭。 任何無效的值都會讓 **sqlcmd** 產生錯誤訊息，並結束作業。  
  
 **-k** [**1** | **2**]  
 從輸出中移除所有控制字元，如定位字元和新行字元。 此參數會在資料傳回時，保留資料行的格式。 如果指定 1，會用單一空格來取代控制字元。 如果指定 2，會用單一空格取代連續控制字元。 **-k** 與 **-k1** 相同。  
  
 **-s** _col_separator_  
 指定資料行分隔字元。 預設值是空格。 這個選項會設定 **sqlcmd** 指令碼變數 SQLCMDCOLSEP。 若要使用對作業系統有特殊意義的字元，如連字號 (&) 或分號 (;)，請用引號 (") 括住該字元。 資料行分隔字元可以是任何 8 位元的字元。  
  
 **-w** _column_width_  
 指定輸出的螢幕寬度。 這個選項會設定 **sqlcmd** 指令碼變數 SQLCMDCOLWIDTH。 資料行寬度必須是大於 8 且小於 65536 的數字。 如果指定的資料行寬度不在該範圍內，**sqlcmd** 會產生錯誤訊息。 預設寬度是 80 個字元。 當輸出行超出指定的資料行寬度時，它會折行。  
  
 **-W**  
 這個選項會從資料行中移除尾端的空格。 在準備要匯出至另一個應用程式的資料時，請使用 **-s** 選項與這個選項。 這個選項不能搭配 **-y** 或 **-Y** 選項一起使用。  
  
 **-y** _variable_length_type_display_width_  
 設定 **sqlcmd** 指令碼變數 `SQLCMDMAXVARTYPEWIDTH`。 預設值是 256。 這會限制大型可變長度資料類型的傳回字元數：  
  
- **varchar(max)**  
  
- **nvarchar(max)**  
  
- **varbinary(max)**  
  
- **xml**  
  
- **UDT (使用者定義資料類型)**  
  
- **text**  
  
- **ntext**  
  
- **image**  
  
> [!NOTE]  
>  UDT 可以是固定長度，這會隨著實作情況而不同。 如果固定長度 UDT 的長度小於 *display_width*，傳回的 UDT 值不會受到影響。 不過，如果長度超出 *display_width*，便會截斷輸出。  
   
  
> [!IMPORTANT]  
>  使用 **-y 0** 選項時，要非常小心，因為它可能會讓伺服器和網路出現嚴重的效能問題，這會隨著傳回的資料大小而不同。  
  
 **-Y** _fixed_length_type_display_width_  
 設定 **sqlcmd** 指令碼變數 `SQLCMDMAXFIXEDTYPEWIDTH`。 預設值是 0 (無限制)。 限制針對下列資料類型傳回的字元數：  
  
- **char(** _n_ **)** ，其中 1<=n<=8000  
  
- **nchar(n** _n_ **)** ，其中 1<=n<=4000  
  
- **varchar(n** _n_ **)** ，其中 1<=n<=8000  
  
- **nvarchar(n** _n_ **)** ，其中 1<=n<=4000  
  
- **varbinary(n** _n_ **)** ，其中 1<=n\<=4000  
  
- **variant**  
  
 **錯誤報告選項**  
  **-b**  
 指定在發生錯誤時， **sqlcmd** 會結束作業並傳回 DOS ERRORLEVEL 值。 當 SQL Server 錯誤訊息的嚴重性層級大於 10 時，傳回 DOS ERRORLEVEL 變數的值是 **1**；否則，傳回的值是 **0**。 如果除了設定 **-b** 以外，還設定了 **-V** 選項，且嚴重性層級低於使用 **-V** 所設定的值，則 **sqlcmd** 不會報告發生錯誤。 命令提示字元批次檔案可以測試 ERRORLEVEL 的值，而且能夠適當地處理錯誤。 **sqlcmd** 不會報告嚴重性層級 10 (資訊訊息) 的錯誤。  
  
 如果 **sqlcmd** 指令碼包含不正確的命令、語法錯誤，或遺漏指令碼變數，則傳回的 ERRORLEVEL 便是 1。  
  
 **-m** _error_level_  
 控制哪些錯誤訊息會傳送至 **stdout**。 系統會傳送嚴重性層級大於或等於這個層級的訊息。 當這個值設定為 **-1** 時，系統就會傳送包括資訊訊息的所有訊息。 **-m** 與 **-1** 之間不允許有空格。 例如， **-m-1** 為有效，而 **-m -1** 為無效。  
  
 這個選項也會設定 **sqlcmd** 指令碼變數 SQLCMDERRORLEVEL。 這個變數具有預設值 0。  
  
 **-V** _error_severity_level_  
 控制用來設定 ERRORLEVEL 變數的嚴重性層級。 嚴重性層級大於或等於這個值的錯誤訊息會設定 ERRORLEVEL。 小於 0 的值會回報成 0。 批次和 CMD 檔案可用來測試 ERRORLEVEL 變數的值。  
  
 **其他選項**  
  **-a** _packet_size_  
 要求不同大小的封包。 這個選項會設定 **sqlcmd** 指令碼變數 SQLCMDPACKETSIZE。 *packet_size* 必須是介於 512 與 32767 之間的值。 預設 = 4096。 較大的封包大小可以讓 GO 命令之間具有許多 SQL 陳述式的指令碼執行得以提高效能。 您可以要求較大的封包。 但是，若要求遭到拒絕， **sqlcmd** 便會使用伺服器預設的封包大小。  
  
 **-c** _batch_terminator_  
 指定批次結束字元。 根據預設，在一行中單獨鍵入 "GO" 這個字，便會終止命令，並將命令傳給 SQL Server。 當您重設批次結束字元時，請勿使用對作業系統有特殊意義的 Transact-SQL 保留關鍵字或字元，即使它們前面附加了反斜線也一樣。  
  
 **-L**[**c**]  
 列出設定在本機的伺服器電腦，以及在網路中進行廣播的伺服器電腦名稱。 這個參數不能結合其他參數來使用。 可以列出的最大伺服器電腦數目為 3000。 如果伺服器清單因為緩衝區的大小而遭到截斷，將會顯示一則警告訊息。  
  
> [!NOTE]  
>  由於網路廣播的本質之故， **sqlcmd** 可能不會收到所有伺服器及時的回應。 因此，這個選項每次的引動過程，所傳回的伺服器清單可能各不相同。  
  
 如果指定了選擇性的參數 **c** ，顯示的輸出不會有 **Servers:** 標頭行，列出的每個伺服器行也都不會有開頭空白。 這種呈現方式稱為清除輸出。 清除輸出可以增進指令碼語言的處理效能。  
  
 **-p**[**1**]  
 列印每個結果集的效能統計資料。 以下顯示的是效能統計資料的格式範例：  
  
 `Network packet size (bytes): n`  
  
 `x xact[s]:`  
  
 `Clock Time (ms.): total       t1  avg       t2 (t3 xacts per sec.)`  
  
 其中：  
  
 `x` = SQL Server 所處理的交易數目。  
  
 `t1` = 所有交易的總時間。  
  
 `t2` = 單一交易的平均時間。  
  
 `t3` = 每秒的平均交易數。  
  
 所有時間都以毫秒表示。  
  
 如果指定了選擇性參數 **1** ，統計資料的輸出格式是用冒號分隔的格式，很容易匯入試算表中，指令碼也很容易處理它。  
  
 如果選擇性的參數是 **1** 以外的任何值，就會產生錯誤，且會結束 **sqlcmd** 。  
  
 **-X**[**1**]  
 停用從批次檔執行 **sqlcmd** 時，可能會危及系統安全性的命令。 仍會辨識停用的命令； **sqlcmd** 會發出一則警告訊息，並繼續作業。 如果指定了選擇性參數 **1** ， **sqlcmd** 會產生一則錯誤訊息，並結束作業。 使用 **-X** 選項時，會停用下列命令：  
  
- **ED**  
  
- **!!** _command_  
  
 如果指定 **-X** 選項，您就無法將環境變數傳給 **sqlcmd**。 也無法執行利用 SQLCMDINI 指令碼變數所指定的啟動指令碼。 如需 **sqlcmd** 指令碼變數的詳細資訊，請參閱 [以指令碼變數使用 sqlcmd](~/relational-databases/scripting/sqlcmd-use-with-scripting-variables.md)。  
  
 **-?**  
 顯示 **sqlcmd** 的版本和 **sqlcmd** 選項的語法摘要。  
  
## <a name="remarks"></a>備註

您不需要按照語法區段中顯示的順序使用選項。

傳回多項結果時， **sqlcmd** 會在批次的各結果集之間，列印一行空白行。 另外，當 `<x> rows affected` 的訊息不適合執行的陳述式時，便不會出現這則訊息。

若要以互動方式使用 **sqlcmd**，請在命令提示字元處鍵入 **sqlcmd**，並指定本文稍早所述的一或多個選項。 如需詳細資訊，請參閱 [使用 sqlcmd 公用程式](~/relational-databases/scripting/sqlcmd-use-the-utility.md)

> [!NOTE]  
>  **-L**、 **-Q**、 **-Z** 或 **-i** 選項造成 **sqlcmd** 在執行之後結束。
  
 命令環境 (Cmd.exe) 中 **sqlcmd** 命令列的總長度 (包括所有引數和擴充的變數)，皆由 Cmd.exe 的作業系統決定。
  
## <a name="variable-precedence-low-to-high"></a>變數優先順序 (由低至高)
  
1.  系統層級環境變數
  
2.  使用者層級環境變數  
  
3.  執行 **sqlcmd** 之前，在命令提示字元處設定的命令殼層 ( **SET** X=Y)。  
  
4.  **sqlcmd-v** X=Y  
  
5.  **:Setvar** X Y  
  
> [!NOTE]  
>  若要檢視環境變數，請在 [控制台] 中開啟 [系統] ，然後按一下 [進階]  索引標籤。  
  
## <a name="sqlcmd-scripting-variables"></a>sqlcmd 指令碼變數  
  
|變數|相關參數|R/W|預設|  
|--------------|--------------------|----------|-------------|  
|SQLCMDUSER|-U|R|""|  
|SQLCMDPASSWORD|-P|--|""|  
|SQLCMDSERVER|-S|R|"DefaultLocalInstance"|  
|SQLCMDWORKSTATION|-H|R|"ComputerName"|  
|SQLCMDDBNAME|-d|R|""|  
|SQLCMDLOGINTIMEOUT|-l|R/W|"8" (秒)|  
|SQLCMDSTATTIMEOUT|-t|R/W|"0" = 永遠等候|  
|SQLCMDHEADERS|-H|R/W|"0"|  
|SQLCMDCOLSEP|-S|R/W|" "|  
|SQLCMDCOLWIDTH|-w|R/W|"0"|  
|SQLCMDPACKETSIZE|-a|R|"4096"|  
|SQLCMDERRORLEVEL|-M|R/W|0|  
|SQLCMDMAXVARTYPEWIDTH|-y|R/W|"256"|  
|SQLCMDMAXFIXEDTYPEWIDTH|-y|R/W|"0" = 無限制|  
|SQLCMDEDITOR||R/W|"edit.com"|  
|SQLCMDINI||R|""|
|SQLCMDUSEAAD  | -G | R/W | "" |  
  
 SQLCMDUSER、SQLCMDPASSWORD 和 SQLCMDSERVER 會在使用 **:Connect** 時設定。  
  
 R 表示在程式初始化期間只能設定該值一次。  
  
 R/W 表示可以使用 **setvar** 命令修改該值，且後續的命令會受到新值的影響。  
  
## <a name="sqlcmd-commands"></a>sqlcmd 命令  
 除了 **sqlcmd** 內的 Transact-SQL 陳述式之外，您也可以使用下列命令：  
  
|||  
|-|-|  
|**GO** [*count*]|**:List**|  
|[ **:** ] **RESET**|**:Error**|  
|[ **:** ] **ED**|**:Out**|  
|[**:**] **!!**|**:Perftrace**|  
|[ **:** ] **QUIT**|**:Connect**|  
|[ **:** ] **EXIT**|**:On Error**|  
|**:r**|**:Help**|  
|**:ServerList**|**:XML** [**ON** &#124; **OFF**]|  
|**:Setvar**|**:Listvar**|  
  
 使用 **sqlcmd** 命令時請注意下列事項：  
  
- 除了 GO 之外的所有 **sqlcmd** 命令開頭都必須加上冒號 (:)。  
  
    > [!IMPORTANT]  
    >  為了顧及與現有 **osql** 指令碼的回溯相容性，也會辨識部分沒有冒號的命令，其以 [ **:** ] 表示。
  
- **sqlcmd** 命令必須在行首，才能夠辨識。  
  
- 所有 **sqlcmd** 命令都不區分大小寫。  
  
- 每個命令都必須在不同行中。 命令後面不能有 Transact-SQL 陳述式或另一個命令。  
  
- 命令會立即執行， 不會像 Transact-SQL 陳述式一樣放在執行緩衝區中。  
  
 **編輯命令**  
  [ **:** ] **ED**  
 啟動文字編輯器。 您可以使用這個編輯器編輯目前的 Transact-SQL 批次，或上次執行的批次。 若要編輯上次執行的批次，在上一個批次執行完成之後，必須立即輸入 **ED** 命令。  
  
 文字編輯器由 SQLCMDEDITOR 環境變數來定義。 預設編輯器是 'Edit'。 若要變更編輯器，請設定 SQLCMDEDITOR 環境變數。 例如，若要將編輯器設為 Microsoft Notepad，請在命令提示字元之下，鍵入：  
  
 `SET SQLCMDEDITOR=notepad`  
  
 [ **:** ] **RESET**  
 清除陳述式快取。  
  
 **:List**  
 列印陳述式快取內容。  
  
 **變數**  
  **:Setvar** \<**var**> [ **"** _value_ **"** ]  
 定義 **sqlcmd** 指令碼變數。 指令碼變數的格式如下： `$(VARNAME)`。  
  
 變數名稱不區分大小寫。  
  
 指令碼變數可以透過下列幾種方式設定：  
  
- 隱含地使用命令列選項。 例如， **-l** 選項會設定 SQLCMDLOGINTIMEOUT **sqlcmd** 變數。  
  
- 明確地利用 **:Setvar** 命令。  
  
- 在您執行 **sqlcmd** 之前，定義環境變數。  
  
> [!NOTE]  
>  **-X** 選項會讓您無法將環境變數傳給 **sqlcmd**。  
  
 如果使用 **:Setvar** 所定義的變數和環境變數同名，則以使用 **:Setvar** 所定義的變數優先。  
  
 變數名稱不能包含空格字元。  
  
 變數名稱的格式不能與變數運算式 (例如 $(var)) 相同。  
  
 如果指令碼變數的字串值包含空格，請用引號括住這個值。 如果未指定指令碼變數值，就會卸除指令碼變數。  
  
 **:Listvar**  
 顯示目前所設定之指令碼變數的清單。  
  
> [!NOTE]  
>  只顯示 **sqlcmd** 所設定的指令碼變數，以及使用 **:Setvar** 命令所設定的指令碼變數。  
  
 **輸出命令**  
  **:Error**   
 _**\<**_  _filename_  **_>|_ STDERR|STDOUT**  
 將所有錯誤輸出重新導向至 *filename* 所指定的檔案、 **stderr** 或 **stdout**。 在指令碼中， **Error** 命令可以重複出現。 根據預設，錯誤輸出會傳送到 **stderr**。  
  
 *file name*  
 建立和開啟用來接收輸出的檔案。 如果檔案已經存在，它會截斷成零位元組。 如果因為權限或其他原因無法使用，就不會切換輸出，輸出會送往最後指定的目的地或預設目的地。  
  
 **STDERR**  
 將錯誤輸出切換到 **stderr** 資料流。 如果它已重新導向，資料流所重新導向的目標會接收這個錯誤輸出。  
  
 **STDOUT**  
 將錯誤輸出切換到 **stdout** 資料流。 如果它已重新導向，資料流所重新導向的目標會接收這個錯誤輸出。  
  
 **:Out \<** _filename_ **>** | **STDERR**| **STDOUT**  
 建立並將所有查詢結果重新導向至 *filename* 所指定的檔案、 **stderr** 或 **stdout**。 根據預設，輸出會傳送到 **stdout**。 如果檔案已經存在，它會截斷成零位元組。 在指令碼中， **Out** 命令可以重複出現。  
  
 **:Perftrace \<** _filename_ **>** | **STDERR**| **STDOUT**  
 建立並將所有效能追蹤資訊重新導向至 *filename* 所指定的檔案、 **stderr** 或 **stdout**。 根據預設，效能追蹤輸出會傳送到 **stdout**。 如果檔案已經存在，它會截斷成零位元組。 在指令碼中， **Perftrace** 命令可以重複出現。  
  
 **執行控制命令**  
  **:On Error**[ **exit** | **ignore**]  
 設定執行指令碼或批次發生錯誤時所要執行的動作。  
  
 使用 **exit** 選項時， **sqlcmd** 會結束作業，並會出現適當的錯誤值。  
  
 使用 **ignore** 選項時， **sqlcmd** 會忽略錯誤，並繼續執行批次或指令碼。 根據預設，會列印錯誤訊息。  
  
 [ **:** ] **QUIT**  
 導致 **sqlcmd** 結束。  
  
 [ **:** ] **EXIT**[ **(** _statement_ **)** ]  
 可讓您使用 SELECT 陳述式的結果作為 **sqlcmd** 的傳回值。 如果為數值，最後一個結果資料列的第一個資料行會轉換成 4 位元組的整數 (long)。 MS-DOS、Linux 和 Mac 會將低位元組傳遞給父處理序或作業系統錯誤層級。 Windows 200x 會傳遞完整的 4 位元組整數。 語法為：  
  
 `:EXIT(query)`  
  
 例如：  
  
 `:EXIT(SELECT @@ROWCOUNT)`  
  
 您也可以將 **EXIT** 參數併入批次檔中。 例如，在命令提示字元之下，輸入：  
  
 `sqlcmd -Q "EXIT(SELECT COUNT(*) FROM '%1')"`  
  
 **sqlcmd** 公用程式會將 **()** 括號之間的所有內容傳送給伺服器。 如果系統預存程序選取某一組，傳回某個值，此時只會傳回選取的項目。 括號中沒有任何內容的 EXIT **()** 陳述式，會執行批次中在它前面的任何內容，然後結束作業，不傳回任何值。  
  
 指定不正確的查詢時， **sqlcmd** 會結束作業，不傳回任何值。  
  
 以下是 EXIT 格式清單：  
  
- :EXIT  
  
 不執行批次，然後立即結束，不傳回任何值。  
  
- :EXIT( )  
  
 執行批次之後，便結束作業，不傳回任何值。  
  
- :EXIT(query)  
  
 執行包含查詢的批次，傳回查詢結果之後再結束。  
  
 如果在 **sqlcmd** 指令碼內使用 RAISERROR，且產生 127 狀態， **sqlcmd** 會結束作業，且會將訊息識別碼傳回用戶端。 例如：  
  
 `RAISERROR(50001, 10, 127)`  
  
 這個錯誤會使得 **sqlcmd** 指令碼結束作業，並將訊息識別碼 50001 傳回用戶端。  
  
 傳回值 -1 到 -99 會由 SQL Server 保留，而 **sqlcmd** 定義下列額外傳回值：  
  
|傳回值|描述|  
|-------------------|-----------------|  
|-100|在選取傳回值之前發生錯誤。|  
|-101|在選取傳回值時，找不到任何資料列。|  
|-102|在選取傳回值時，發生轉換錯誤。|  
  
 **GO** [*count*]  
 GO 會發出批次結束及執行任何快取的 Transact-SQL 陳述式的信號。 該批次會作為個別批次執行數次。 您無法在單一批次中宣告變數超過一次。
  
 **其他命令**  
  **:r \<** _filename_ **>**  
 將 **\<**_filename_**>** 所指定檔案中的其他 Transact-SQL 陳述式與 **sqlcmd** 命令，剖析至陳述式快取中。  
  
 如果檔案包含的 Transact-SQL 陳述式後面沒有緊接著 **GO**，您必須在 **:r** 之後的一行輸入 **GO**。  
  
> [!NOTE]  
>  從執行 **sqlcmd** 所在啟動目錄的相對目錄中讀取 **\<** _filename_ **>** 。  
  
 在發現批次結束字元之後，便會讀取和執行這個檔案。 您可以發出多個 **:r** 命令。 這個檔案可包含任何 **sqlcmd** 命令。 其中包括批次結束字元 **GO**。  
  
> [!NOTE]  
>  每次發現 `:r` 命令時，以互動模式顯示的行數便會加 1。 `:r` 命令會出現在 list 命令的輸出中。  
  
 **:Serverlist**  
 列出設定在本機的伺服器，以及在網路中進行廣播的伺服器名稱。  
  
 **:Connect**  _server_name_[ **\\** _instance\_name_] [-l *timeout*] [-U *user_name* [-P *password*]]  
 連線到 SQL Server 的執行個體。 此外也會關閉目前的連接。  
  
 逾時選項：  
  
|值|行為|  
|-|-|  
|0|永久等候|  
|n>0|等候 n 秒|  
  
 SQLCMDSERVER 指令碼變數會反映目前作用中的連接。  
  
 如果沒有指定 *timeout* ，預設值就是 SQLCMDLOGINTIMEOUT 變數的值。  
  
 如果只指定 *user_name* (作為選項或作為環境變數)，則會提示使用者輸入密碼。 如果已設定 SQLCMDUSER 或 SQLCMDPASSWORD 環境變數，就不會提示使用者。 如果既沒有提供選項，也沒有提供環境變數，就會利用 Windows 驗證模式來登入。 例如，若要使用整合式安全性連線到 SQL Server `myserver` 的執行個體 `instance1`，您將使用下列命令：  
  
 `:connect myserver\instance1`  
  
 若要利用指令碼變數來連接 `myserver` 的預設執行個體，您將使用下列命令：  
  
 `:setvar myusername test`  
  
 `:setvar myservername myserver`  
  
 `:connect $(myservername) $(myusername)`  
  
 [ **:** ] **!!** < *command*>  
 執行作業系統命令。 若要執行作業系統命令，請在行首輸入兩個驚歎號 ( **!!** )，後面再接著作業系統命令。 例如：  
  
 `:!! Dir`  
  
> [!NOTE]  
>  這個命令會在執行 **sqlcmd** 的電腦上執行。  
  
 **:XML** [**ON** | **OFF**]  
 如需詳細資訊，請參閱本文中的 [XML 輸出格式](#OutputXML)和 [JSON 輸出格式](#OutputJSON)  
  
 **:Help**  
 列出 **sqlcmd** 命令及各命令的簡短描述。  
  
### <a name="sqlcmd-file-names"></a>sqlcmd 檔案名稱  
 您可以使用 **sqlcmd** 選項或 **sqlcmd** 命令指定 **sqlcmd** 輸入檔。 輸出檔則可以使用 **-o** 選項或 **:Error**、 **:Out** 和 **:Perftrace** 命令予以指定。 以下列出使用這些檔案的幾項指導方針：  
  
- **:Error**、 **:Out** 及 **:Perftrace** 應使用不同的 **\<**_filename_**>** 。 如果使用相同的 **\<**_filename_**>** ，命令，命令的輸入可能會混合在一起。  
  
- 如果從本機電腦的 **sqlcmd** 呼叫位於遠端伺服器上的輸入檔，且檔案中包含磁碟機檔案路徑 (例如 :Out c:\OutputFile.txt)， 即會在本機電腦建立輸出檔案，而不是在遠端伺服器建立。  
  
- 有效的檔案路徑包括︰`C:\<filename>`、`\\<Server>\<Share$>\<filename>` 與 `"C:\Some Folder\<file name>"`。 如果路徑中有空格，請使用引號。  
  
- 每個新的 **sqlcmd** 工作階段都會覆寫現有的同名檔案。  
  
### <a name="informational-messages"></a>參考用訊息

**sqlcmd** 會列印伺服器所傳送的所有參考用訊息。 在下列範例中，執行 Transact-SQL 陳述式之後，會列印一則參考用訊息。
  
於命令提示字元中，輸入命令：

`sqlcmd`
  
在 sqlcmd 提示字元中鍵入：

`USE AdventureWorks2012;`

`GO`

當您按 ENTER 時，會列印下列參考訊息：「已將資料庫內容變更為 'AdventureWorks2012'。」  
  
### <a name="output-format-from-transact-sql-queries"></a>Transact-SQL 查詢的輸出格式  
 **sqlcmd** 會先列印包含選取清單中所指定之資料行名稱的資料行標頭。 資料行名稱是以 SQLCMDCOLSEP 字元分隔。 根據預設，這是一個空格。 如果資料行名稱長度小於資料行寬度，便會在輸出中填補空格直到下一個資料行。  
  
 這一行後面會接著一條由虛線字元組成的分隔線。 下列輸出顯示一個範例。  
  
 啟動 **sqlcmd**。 在 **sqlcmd** 命令提示字元中，鍵入下列查詢：  
  
 `USE AdventureWorks2012;`  
  
 `SELECT TOP (2) BusinessEntityID, FirstName, LastName`  
  
 `FROM Person.Person;`  
  
 `GO`  
  
 當您按下 ENTER，就會傳回下列結果集。  
  
 `BusinessEntityID FirstName    LastName`  
  
 `---------------- ------------ ----------`  
  
 `285              Syed         Abbas`  
  
 `293              Catherine    Abel`  
  
 `(2 row(s) affected)`  
  
 雖然 `BusinessEntityID` 資料行的寬度只有四個字元，但它已擴充，能夠容納較長的資料行名稱。 依預設，輸出的長度最多為 80 個字元。 您可以利用 **-w** 選項或設定 SQLCMDCOLWIDTH 指令碼變數，變更此設定。  
  
###  <a name="xml-output-format"></a><a name="OutputXML"></a> XML 輸出格式  
 FOR XML 子句所產生的 XML 輸出，會在連續資料流中以未格式化的形式輸出。  
  
 當您希望產生 XML 輸出時，請使用下列命令： `:XML ON`。  
  
> [!NOTE]  
>  **sqlcmd** 會以一般格式傳回錯誤訊息。 請注意，錯誤訊息也會以 XML 格式輸出至 XML 文字資料流。 如果使用 `:XML ON`， **sqlcmd** 就不會顯示參考用訊息。  
  
 若要將 XML 模式設定為關閉，請使用下列命令：`:XML OFF`。  
  
 在發出 XML OFF 命令之前，不應出現 GO 命令，因為 XML OFF 命令會將 **sqlcmd** 切換回資料列導向的輸出。  
  
 XML (資料流) 資料和資料列集資料不能混合。 如果在執行輸出 XML 資料流的 Transact-SQL 陳述式之前發出 XML ON 命令，則輸出會混亂。 已發出 XML ON 命令之後，您就不能執行輸出正規資料列集的 Transact-SQL 陳述式。  
  
> [!NOTE]  
>  `:XML` 命令不支援 SET STATISTICS XML 陳述式。  
  
###  <a name="json-output-format"></a><a name="OutputJSON"></a> JSON 輸出格式  
 當您希望產生 JSON 輸出時，請使用下列命令： `:XML ON`。 否則輸出會包含資料行名稱和 JSON 文字。 此輸出不是有效的 JSON。  
  
 若要將 XML 模式設定為關閉，請使用下列命令：`:XML OFF`。  
  
 如需詳細資訊，請參閱本文中的 [XML 輸出格式](#OutputXML)。  

### <a name="using-azure-active-directory-authentication"></a>使用 Azure Active Directory 驗證

使用 Azure Active Directory 驗證的範例如下：

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  -l 30
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G -U bob@contoso.com -P MyAADPassword -l 30
```
  
## <a name="sqlcmd-best-practices"></a>sqlcmd 最佳作法

請採用下列作法以提高安全性與效率。  

- 使用整合式安全性。  

- 在自動化環境中使用 **-X[1]** 。

- 利用 NTFS 檔案系統權限保護輸入檔和輸出檔。

- 為提高效能，請盡可能在單一 **sqlcmd** 工作階段中執行所有工作，而不要使用一連串的工作階段。

- 將批次或查詢執行的逾時值，設定成高於您預期執行批次或查詢所需的時間值。

使用下列作法以協助將正確性最大化：

- 使用 **-V16** 來記錄所有[嚴重性 16 層級訊息](../relational-databases/errors-events/database-engine-error-severities.md#levels-of-severity)。  嚴重性 16 訊息會指出使用者能夠更正的一般錯誤。

- 在處理序結束之後檢查結束代碼與 DOS ERRORLEVEL 變數。  **sqlcmd** 在正常情況下會傳回 0，否則其將會設定由 **-V** 所設定的 ERRORLEVEL。  換句話說，不應該預期 ERRORLEVEL 會與 SQL Server 回報的「錯誤號碼」相同。 「錯誤號碼「是 SQL Server 特有值，其會與系統函式 [ **@@ERROR** ](../t-sql/functions/error-transact-sql.md) 相對應。  ERRORLEVEL 是 SQLCMD 特有值，是用來指出其 (即 SQLCMD) 終止的原因，且其值可透過指定 **-b** 命令列引數來加以影響。

使用 **-V16** 搭配檢查結束代碼與 DOS ERRORLEVEL，有助於攔截自動化環境中的錯誤，其特別適用於生產發行之前的品質閘門。

## <a name="next-steps"></a>後續步驟

- [啟動 sqlcmd 公用程式](~/relational-databases/scripting/sqlcmd-start-the-utility.md)
- [使用 sqlcmd 執行 Transact-SQL 指令碼檔案](~/relational-databases/scripting/sqlcmd-run-transact-sql-script-files.md)
- [使用 sqlcmd 公用程式](~/relational-databases/scripting/sqlcmd-use-the-utility.md)
- [以指令碼變數使用 sqlcmd](~/relational-databases/scripting/sqlcmd-use-with-scripting-variables.md)
- [使用 sqlcmd 連接至 Database Engine](~/relational-databases/scripting/sqlcmd-connect-to-the-database-engine.md)
- [使用查詢編輯器編輯 SQLCMD 指令碼](~/relational-databases/scripting/edit-sqlcmd-scripts-with-query-editor.md)
- [管理作業步驟](~/ssms/agent/manage-job-steps.md)   
- [建立 CmdExec 作業步驟](~/ssms/agent/create-a-cmdexec-job-step.md)  

[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]
