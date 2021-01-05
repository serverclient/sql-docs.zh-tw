---
title: ssbdiagnose 公用程式 [Service Broker]
description: ssbdiagnose 公用程式會報告 Service Broker 交談或 Service Broker 服務組態中的問題。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: tools-other
ms.topic: conceptual
helpviewer_keywords:
- Service Broker, runtime reports
- Service Broker, command prompt utilities
- troubleshooting [Service Broker], conversations
- troubleshooting [Service Broker], configurations
- command prompt utilities [Service Broker]
- Service Broker, troubleshooting
- Service Broker, configuration reports
- Service Broker, tools
- troubleshooting [Service Broker], runtime
- conversations [Service Broker], troubleshooting
- troubleshooting [Service Broker], ssbdiagnose utility
- tools [Service Broker], ssbdiagnose
- Service Broker, ssbdiagnose utility
- ssbdiagnose
ms.assetid: 0c1636e8-a3db-438e-be4c-1ea40d1f4877
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.openlocfilehash: 8590f3677d27c367782999877cb6892251785278
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641280"
---
# <a name="ssbdiagnose-utility-service-broker"></a>ssbdiagnose 公用程式 [Service Broker]

[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

**ssbdiagnose** 公用程式會報告 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 交談或 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 服務組態中的問題。 您可以針對兩個服務或單一服務進行組態檢查。 問題會在命令提示字元視窗中報告成人們可讀取的文字，或可重新導向至檔案或其他程式的格式化 XML。

## <a name="syntax"></a>語法  
  
```  
  
ssbdiagnose   
[ [ -XML ]  
    [ -LEVEL { ERROR | WARNING | INFO } ]  
  [-IGNORE error_id ] [ ...n]  
    [ <baseconnectionoptions> ]  
  { <configurationreport> | <runtimereport> }  
]  
| -?  
  
<configurationreport> ::=  
    CONFIGURATION  
  { [ FROM SERVICE service_name  
      [ <fromconnectionoptions> ]  
      [ MIRROR <mirrorconnectionoptions> ]  
    ]  
    [ TO SERVICE service_name[, broker_id ]  
      [ <toconnectionoptions> ]  
      [ MIRROR <mirrorconnectionoptions> ]  
    ]  
  }  
    ON CONTRACT contract_name  
  [ ENCRYPTION { ON | OFF | ANONYMOUS } ]  
  
<runtime_report> ::=  
    RUNTIME  
    [-SHOWEVENTS ]  
        [ -NEW  
         [ -ID { conversation_handle  
                | conversation_group_id  
                 | conversation_id  
                  }  
        ] [ ...n]  
        ]  
    [ -TIMEOUT timeout_interval ]  
    [ <runtimeconnectionoptions> ]  
  
<baseconnectionoptions> ::=  
  <connectionoptions>  
  
<fromconnectionoptions> ::=  
  <connectionoptions>  
  
<toconnectionoptions> ::=  
  <connectionoptions>  
  
<mirrorconnectionoptions> ::=  
  <connectionoptions>  
  
<runtimeconnectionoptions> ::=  
  [ CONNECT TO <connectionoptions> ] [ ...n]  
  
<connectionoptions> ::=  
    [ -E | { -U login_id [ -P password ] } ]  
  [ -S server_name[\instance_name] ]  
  [ -d database_name ]  
  [ -l login_timeout ]  
  
```  
  
## <a name="command-line-options"></a>命令列選項  
 **-XML**  
 指定 **ssbdiagnose** 輸出要產生為格式化的 XML。 這種輸出可重新導向至檔案或其他應用程式。 如未指定 **-XML** ，則 **ssbdiagnose** 輸出就會格式化成一般人看得懂的文字。  
  
 **-LEVEL** { **ERROR** | **WARNING** | **INFO**}  
 指定要報告的訊息層級。  
  
 **ERROR**：僅報告錯誤訊息。  
  
 **WARNING**：報告錯誤和警告訊息。  
  
 **INFO**：報告錯誤、警告和參考用訊息。  
  
 預設設定為 **WARNING**。  
  
 **-IGNORE** _error_id_  
 指定具有指定之 *error_id* 的錯誤或訊息，不要包含在報表中。 您可以多次指定 **-IGNORE** ，以隱藏多個訊息識別碼。  
  
 **\<baseconnectionoptions>**  
 指定當特定子句中未包含連線選項時， **ssbdiagnose** 所使用的基底連線資訊。 在特定子句中提供的連接資訊，會覆寫 **baseconnectionoption** 資訊。 這會針對每項參數個別執行。 例如， **baseconnetionoptions** 中同時指定了 **-S** 和 **-d**，而 **toconnetionoptions** 中只指定了 **-d**，則 **ssbdiagnose** 會使用 **baseconnetionoptions** 的 -S 和 **toconnetionoptions** 的 -d。  
  
 **CONFIGURATION**  
 要求一組 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 服務之間或單一服務的組態錯誤報表。  
  
 **FROM SERVICE** _service_name_  
 指定起始交談的服務。  
  
 **\<fromconnectionoptions>**  
 指定連接至保留起始端服務之資料庫所需的資訊。 如未指定 **fromconnectionoptions** ，則 **ssbdiagnose** 會使用 **baseconnectionoptions** 中的連接資訊連接至起始端資料庫。 如果指定了 **fromconnectionoptions** ，它就必須包括含有起始端服務的資料庫。 如未指定 **fromconnectionoptions** ，則 **baseconnectionoptions** 必須指定起始端資料庫。  
  
 **TO SERVICE** _service_name_[, *broker_id* ]  
 指定用於交談的服務。  
  
 *service_name*：指定目標服務的名稱。  
  
 *broker_id*：指定可識別目標資料庫的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼。 *broker_id* 是 GUID。 您可以在目標資料庫中執行下列查詢，以便尋找此識別碼：  
  
```  
SELECT service_broker_guid  
FROM sys.databases  
WHERE database_id = DB_ID();  
```  
  
 **\<toconnectionoptions>**  
 指定連接至保存目標服務之資料庫所需的資訊。 如未指定 **toconnectionoptions** ，則 **ssbdiagnose** 會使用 **baseconnectionoptions** 中的連接資訊連接至目標資料庫。  
  
 **MIRROR**  
 指定相關聯的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 服務由鏡像資料庫所主控。 **ssbdiagnose** 會確認服務的路由為鏡像路由，其中 MIRROR_ADDRESS 是在 CREATE ROUTE 上指定。  
  
 **\<mirrorconnectionoptions>**  
 指定連接至鏡像資料庫所需的資訊。 如未指定 **mirrorconnectionoptions** ，則 **ssbdiagnose** 會使用 **baseconnectionoptions** 中的連接資訊連接至鏡像資料庫。  
  
 **ON CONTRACT** _contract_name_  
 要求 **ssbdiagnose** 只檢查使用指定合約的組態。 如未指定 ON CONTRACT，則 **ssbdiagnose** 就會針對名為 DEFAULT 的合約進行報告。  
  
 **ENCRYPTION** { **ON** | **OFF** | **ANONYMOUS** }  
 要求確認是否已針對指定的加密層級正確設定對話：  
  
 **ON**：預設設定。 已設定完整的對話安全性。 已經在對話的兩端部署了憑證、存在遠端服務繫結，而且目標服務的 GRANT SEND 陳述式已指定了起始端使用者。  
  
 **OFF**：未設定任何對話安全性。 沒有部署任何憑證、沒有建立任何遠端服務繫結，而且起始端服務的 GRANT SEND 已指定了 **Public** 角色。  
  
 **ANONYMOUS**：已設定匿名對話安全性。 已經部署了一個憑證、遠端服務繫結已指定了匿名子句，而且目標服務的 GRANT SEND 已指定了 **Public** 角色。  
  
 **RUNTIME**  
 要求導致 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 交談發生執行階段錯誤之問題的報表。 如未指定 **-NEW** 或 **-ID** ， **ssbdiagnose** 就會監視連線選項中指定之所有資料庫中的所有交談。 如果指定了 **-NEW** 或 **-ID** ， **ssbdiagnose** 就會建立參數中指定的識別碼清單。  
  
 當 **ssbdiagnose** 在執行時，它會記錄所有指出執行階段錯誤的 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 事件。 它會記錄針對指定之識別碼發生的事件，以及系統層級事件。 如果發生執行階段錯誤， **ssbdiagnose** 就會針對相關聯的組態執行組態報表。  
  
 根據預設，輸出報表不會包含執行階段錯誤，只會包含組態分析的結果。 您可以使用 **-SHOWEVENTS** ，在報表中包含執行階段錯誤。  
  
 **-SHOWEVENTS**  
 指定 **ssbdiagnose** 在 RUNTIME 報表期間報告 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 事件。 僅報告被視為錯誤條件的事件。 **ssbdiagnose** 預設只監視錯誤事件，不會在輸出中報告這些事件。  
  
 **-NEW**  
 要求監視在 **ssbdiagnose** 開始執行之後開始的第一個交談執行階段。  
  
 **-ID**  
 要求指定的交談元素之執行階段監視。 您可以指定多次 **-ID** 。  
  
 如果您指定了交談控制代碼，系統就只會報告與相關聯之交談端點相關聯的事件。 如果您指定了交談識別碼，系統就會報告該交談及其起始端和目標端點的所有事件。 如果您指定了交談群組識別碼，系統就會報告該交談群組中所有交談和端點的所有事件。  
  
 *conversation_handle*  
 可識別應用程式中某個交談端點的唯一識別碼。 交談控制代碼對於交談的某個端點而言是唯一的，而起始端和目標端點具有不同的交談控制代碼。  
  
 **BEGIN DIALOG** 陳述式的 *\@dialog_handle* 參數和 **RECEIVE** 陳述式結果集中的 **conversation_handle** 資料行，會將交談控制代碼傳回應用程式。  
  
 **sys.transmission_queue** 和 **sys.conversation_endpoints** 目錄檢視的 **conversation_handle** 資料行中會報告交談控制代碼。  
  
 *conversation_group_id*  
 識別交談群組且不重複的識別碼。  
  
 **GET CONVERSATION GROUP** 陳述式的 *\@conversation_group_id* 參數和 **RECEIVE** 陳述式結果集中的 **conversation_group_id** 資料行會將交談群組識別碼傳回應用程式。  
  
 **sys.conversation_groups** 和 **sys.conversation_endpoints** 目錄檢視的 **conversation_group_id** 資料行中會報告交談群組識別碼。  
  
 *conversation_id*  
 識別交談且不重複的識別碼。 對於交談的起始端和目標端點而言，其交談識別碼都相同。  
  
 **sys.conversation_endpoints** 目錄檢視的 **conversation_id** 資料行中會報告交談識別碼。  
  
 **-TIMEOUT** _timeout_interval_  
 指定 **RUNTIME** 報表要執行的秒數。 如未指定 **-TIMEOUT** ，執行階段報表就會無限期地執行。 **-TIMEOUT** 僅用於 **RUNTIME** 報表，不用在 **CONFIGURATION** 報表。 如果沒有指定 **ssbdiagnose** if **-TIMEOUT** 間隔到期之前結束執行階段報表，您可以使用 CTRL + C 結束 **-** 。 *timeout_interval* 必須是介於 1 和 2,147,483,647 之間的數字。  
  
 **\<runtimeconnectionoptions>**  
 指定資料庫的連接資訊，而這些資料庫包含與受監視之交談元素相關聯的服務。 如果所有服務都位於相同的資料庫中，您就只需要指定一個 **CONNECT TO** 子句。 如果各項服務位於不同的資料庫中，您就必須針對每個資料庫提供一個 **CONNECT TO** 子句。 如未指定 **runtimeconnectionoptions** ，則 **ssbdiagnose** 會使用 **baseconnectionoptions** 中的連接資訊。  
  
 **-E**  
 使用您目前的 Windows 帳戶作為登入識別碼，藉以開啟 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體的 Windows 驗證連線。 登入必須是 **系統管理員** 固定伺服器角色的成員。  
  
 -E 選項會忽略 SQLCMDUSER 和 SQLCMDPASSWORD 環境變數的使用者與密碼設定。  
  
 如未指定 **-E** 也未指定 **-U** ， **ssbdiagnose** 就會使用 SQLCMDUSER 環境變數的值。 如果也沒有設定 SQLCMDUSER， **ssbdiagnose** 就會使用 Windows 驗證。  
  
 如果同時使用 **-E** 選項和 **-U** 或 **-P** 選項，就會產生錯誤訊息。  
  
 **-U** _login_id_  
 使用指定的登入識別碼，藉以開啟 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證連線。 登入必須是 **系統管理員** 固定伺服器角色的成員。  
  
 如未指定 **-E** 也未指定 **-U** ， **ssbdiagnose** 就會使用 SQLCMDUSER 環境變數的值。 如果也沒有設定 SQLCMDUSER， **ssbdiagnose** 就會根據正在執行 **ssbdiagnose** 的使用者 Windows 帳戶，使用 Windows 驗證模式嘗試連接。  
  
 如果同時使用 **-U** 選項和 **-E** 選項，就會產生錯誤訊息。 如果 **-U** 選項後面有多個引數，就會產生錯誤訊息並結束程式。  
  
 **-P** _password_  
 指定 **-U** 登入識別碼的密碼。 密碼會區分大小寫。 如果使用了 **-U** 選項，但沒有使用 **-P** 選項， **ssbdiagnose** 就會使用 SQLCMDPASSWORD 環境變數的值。 如果也沒有設定 SQLCMDPASSWORD， **ssbdiagnose** 就會提示使用者輸入密碼。  
  
> [!IMPORTANT]  
>  當您輸入 SET SQLCMDPASSWORD 命令時，任何能夠查看監視器的人都可以看到密碼。  
  
 如果指定了 **-P** 選項但無密碼， **ssbdiagnose** 就會使用預設密碼 (NULL)。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteStrongPass](../../includes/ssnotestrongpass-md.md)] 如需詳細資訊，請參閱[增強式密碼](../../relational-databases/security/strong-passwords.md)。  
  
 密碼提示的顯示方式，會以將密碼提示輸出到主控台的方式顯示，如： `Password:`  
  
 使用者輸入為隱藏狀態。 這表示畫面上不會顯示任何內容，而且游標也不會移動。  
  
 如果同時使用 **-P** 選項和 **-E** 選項，就會產生錯誤訊息。  
  
 如果 **-P** 選項後面有多個引數，就會產生錯誤訊息。  
  
 **-S** _server_name_[\\*instance_name*]  
 指定保存要分析之 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 服務的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 執行個體。  
  
 指定 *server_name* ，即可連接至該伺服器上之 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的預設執行個體。 指定 _server\_name_ **\\** _instance\_name_，即可連接到該伺服器上 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 的具名執行個體。 如未指定 **-S** ，則 **ssbdiagnose** 會使用 SQLCMDSERVER 環境變數的值。 如果也沒有設定 SQLCMDSERVER， **ssbdiagnose** 就會連接至本機電腦上的 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 預設執行個體。  
  
 **-d** _database_name_  
 指定保存要分析之 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 服務的資料庫。 如果此資料庫不存在，就會產生錯誤訊息。 如未指定 **-d** ，預設值就是登入之預設資料庫屬性中所指定的資料庫。  
  
 **-l** _login_timeout_  
 指定嘗試連接至伺服器會發生逾時之前，所經過的秒數。如未指定 **-l** ， **ssbdiagnose** 就會使用針對 SQLCMDLOGINTIMEOUT 環境變數所設定的值。 如果也沒有指定 SQLCMDLOGINTIMEOUT，預設的逾時就是三十秒。 此登入逾時必須是介於 0 和 65534 之間的數字。 如果所提供的值不是數值或不在該範圍內， **ssbdiagnose** 就會產生錯誤訊息。 0 值指定逾時值無限。  
  
 **-?**  
 顯示命令列說明。  
  
## <a name="remarks"></a>備註  
 您可以使用 **ssbdiagnose** 執行下列作業：  
  
-   在新設定的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 應用程式中，確認沒有任何組態錯誤。  
  
-   在您變更現有 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 應用程式的組態之後，確認沒有任何組態錯誤。  
  
-   在 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 資料庫卸離，然後重新附加至新的 [!INCLUDE[ssDE](../../includes/ssde-md.md)]執行個體之後，確認沒有任何組態錯誤。  
  
-   當訊息無法順利在服務之間傳輸時，研究是否存在組態錯誤。  
  
-   取得在一組 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 交談元素中發生之任何錯誤的報表。  
  
## <a name="configuration-reporting"></a>組態報表  
 若要正確分析交談所使用的組態，請執行與交談使用相同選項的 **ssbdiagnose** 組態報表。 如果您為 **ssbdiagnose** 指定的選項層級低於交談所使用的選項層級，則 **ssbdiagnose** 可能不會報告交談所需的條件。 如果您為 **ssbdiagnose** 指定較高的選項層級，它可能會報告交談所不需要的報表項目。 例如，在相同資料庫中兩個服務之間的交談，可能會在 ENCPRYPTION OFF 的情況下執行。 如果您執行 **ssbdiagnose** 以驗證這兩個服務之間的組態，但卻使用預設的 ENCRYPTION ON 設定， **ssbdiagnose** 就會報告資料庫缺少主要金鑰。 這種交談不需要使用主要金鑰。  
  
 每次執行 **ssbdiagnose** 組態報表時，它只會分析一個 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 服務或一對服務。 若要報告多對 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 服務，請建立多次呼叫 **ssbdiagnose** 的 .cmd 命令檔。  
  
## <a name="runtime-reporting"></a>執行階段報表  
 指定 -RUNTIME 時， **ssbdiagnose** 會搜尋 **runtimeconnectionoptions** 和 **baseconnectionoptions** 中指定的所有資料庫，建立一份 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼清單。 所建立的完整識別碼清單會因針對 -NEW 和 -ID 指定的內容而不同：  
  
-   如未指定 **-NEW** 或 **-ID** ，此清單就會包含連線選項中指定之所有資料庫的所有交談。  
  
-   如果指定了 **-NEW** ， **ssbdiagnose** 就會包含 **ssbdiagnose** 執行之後開始的第一個交談的元素。 這同時包括目標和起始端交談端點的交談識別碼與交談控制代碼。  
  
-   如果指定了 **-ID** 與交談控制代碼，只有該控制代碼會包含在清單中。  
  
-   如果指定了 **-ID** 與交談識別碼，則交談識別碼及其兩個交談端點的控制代碼都會加入清單中。  
  
-   如果指定了 **-ID** 與交談群組識別碼，則該群組中的所有交談識別碼和交談控制代碼都會加入清單中。  
  
 此清單不會包含連接選項未涵蓋之資料庫的元素。 例如，假設您使用 **-ID** 指定交談識別碼，但是僅針對起始端資料庫 (而非目標資料庫) 提供 **runtimeconnectionoptions** 子句。 **ssbdiagnose** 不會在其識別碼清單中包含目標交談控制代碼，而只會包含交談識別碼和起始端交談控制代碼。  
  
 **ssbdiagnose** 監視 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] runtimeconnectionoptions **和** baseconnectionoptions **涵蓋的資料庫** 事件。 它會搜尋 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 事件，指出執行階段清單中一或多個 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼發生的錯誤。 **ssbdiagnose** 也會搜尋未特別與任何交談群組相關聯的系統層級 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 錯誤事件。  
  
 如果 **ssbdiagnose** 找到交談錯誤，此公用程式就會透過同時執行組態報表，嘗試報告事件的根本原因。 **ssbdiagnose** 會使用資料庫內的中繼資料，嘗試判斷交談所使用的執行個體、 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 識別碼、資料庫、服務與合約。 然後，它會使用所有可用的資訊來執行組態報表。  
  
 **ssbdiagnose** 預設不會報告錯誤事件。 它只會報告在組態檢查期間所找到的基礎問題。 如此會將報告的資訊量減到最少，協助您將焦點集中在基礎組態問題。 您可以指定 **-SHOWEVENTS** ，以便查看 **ssbdiagnose** 所遇到的錯誤事件。  
  
## <a name="issues-reported-by-ssbdiagnose"></a>ssbdiagnose 所報告的問題  
 **ssbdiagnose** 會報告三種問題類別。 在 XML 輸出檔中，每種問題類別都會報告成個別的 Issue 元素類型。 **ssbdiagnose** 所報告的三種問題類型如下：  
  
 **診斷**  
 報告組態問題。 這包括在 **CONFIGURATION** 報表執行時或 **RUNTIME** 報表的組態階段期間所發現的問題。 **ssbdiagnose** 每個組態問題只報告一次。  
  
 **事件**  
 報告 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)] 事件，指出在 **RUNTIME** 報表期間監視的交談所遇到的問題。 **ssbdiagnose** 會在每次事件產生時報告這些事件。 如果許多交談都遇到該問題，可能會報告多次該事件。  
  
 **問題**  
 報告會讓 **ssbdiagnose** 無法完成組態分析或無法監視交談的問題。  
  
## <a name="sqlcmd-environment-variables"></a>sqlcmd 環境變數  
 **ssbdiagnose** 公用程式支援 **sqlcmd** 公用程式也使用的 SQLCMDSERVER、SQLCMDUSER、SQLCMDPASSWORD 和 SQLCMDLOGINTIMOUT 環境變數。 您可以使用命令提示字元 SET 命令，或在用 **sqlcmd** 執行的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 指令碼中使用 **setvar** 命令，來設定這些環境變數。 如需如何在 **sqlcmd** 中使用 **setvar** 的詳細資訊，請參閱 [以指令碼變數使用 sqlcmd](../../ssms/scripting/sqlcmd-use-with-scripting-variables.md)。  
  
## <a name="permissions"></a>權限  
 在每個 **connectionoptions** 子句中，使用 **-E** 或 **-U** 指定的登入，必須是以 **-S** 所指定之執行個體內 **系統管理員** 固定伺服器角色的成員。  
  
## <a name="examples"></a>範例  
 本節包含在命令提示字元處使用 **ssbdiagnose** 的範例。  
  
### <a name="a-checking-the-configuration-of-two-services-in-the-same-database"></a>A. 檢查相同的資料庫中兩個服務的組態  
 下列範例將示範如何在下列條件成立時要求組態報表：  
  
-   起始端和目標服務位於相同的資料庫中。  
  
-   資料庫位於 [!INCLUDE[ssDE](../../includes/ssde-md.md)]的預設執行個體中。  
  
-   執行個體位於執行 **ssbdiagnose** 的同一部電腦上。  
  
 **ssbdiagnose** 公用程式會報告使用 DEFAULT 合約的組態，因為沒有指定 ON CONTRACT。  
  
```  
ssbdiagnose -E -d MyDatabase CONFIGURATION FROM SERVICE /test/initiator TO SERVICE /test/target  
```  
  
### <a name="b-checking-the-configuration-of-two-services-on-separate-computers-that-use-one-login"></a>B. 檢查使用單一登入之不同電腦上的兩個服務之組態  
 下列範例將示範如何在起始端和目標服務位於不同的電腦，但是可以使用相同的 Windows 驗證登入存取時，要求組態報表。  
  
```  
ssbdiagnose -E CONFIGURATION FROM SERVICE /text/initiator -S InitiatorComputer -d InitiatorDatabase TO SERVICE /test/target -S TargetComputer -d TargetDatabase ON CONTRACT TestContract  
```  
  
### <a name="c-checking-the-configuration-of-two-services-on-separate-computers-that-use-separate-logins"></a>C. 檢查使用不同登入之不同電腦上兩個服務的組態  
 下列範例將示範如何在起始端和目標服務位於不同的電腦，而且每個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體都需要不同的 [!INCLUDE[ssDE](../../includes/ssde-md.md)]驗證登入時，要求組態報表。  
  
```  
ssbdiagnose CONFIGURATION FROM SERVICE /text/initiator   
-S InitiatorComputer -U InitiatorLogin -p !wEx23Dvb   
-d InitiatorDatabase TO SERVICE /test/target -S TargetComputer   
-U TargetLogin -p ER!49jiy -d TargetDatabase ON CONTRACT TestContract  
```  
  
### <a name="d-checking-mirrored-service-configurations-on-separate-computers-with-anonymous-encryption"></a>D. 檢查具有匿名加密的不同電腦上之鏡像服務組態  
 下列範例將示範如何在起始端和目標服務位於不同的電腦，而且起始端鏡像至具名執行個體時，要求組態報表。 此報表也會確認這些服務都設定為使用匿名加密。  
  
```  
ssbdiagnose -E CONFIGURATION FROM SERVICE /text/initiator   
-S InitiatorComputer -d InitiatorDatabase MIRROR   
-S MirrorComputer/MirrorInstance TO SERVICE /test/target   
-S TargetComputer -d TargetDatabase ON CONTRACT TestContract ENCRYPTION ANONYMOUS  
```  
  
### <a name="e-checking-the-configuration-of-two-contracts"></a>E. 檢查兩個合約的組態  
 下列範例將示範如何建立命令檔，以便在下列條件成立時要求組態報表：  
  
-   起始端和目標服務位於相同的資料庫中。  
  
-   資料庫位於 [!INCLUDE[ssDE](../../includes/ssde-md.md)]的預設執行個體中。  
  
-   執行個體位於執行 **ssbdiagnose** 的同一部電腦上。  
  
 每次執行 **ssbdiagnose** 時，它就會針對相同服務之間的不同合約報告組態。  
  
```  
ssbdiagnose -E -d MyDatabase CONFIGURATION FROM SERVICE   
/test/initiator TO SERVICE /test/target ON CONTRACT PayRaiseContract  
ssbdiagnose -E -d MyDatabase CONFIGURATION FROM SERVICE /test/initiator   
TO SERVICE /test/target ON CONTRACT PromotionContract  
```  
  
### <a name="f-monitor-the-status-of-a-specific-conversation-on-the-local-computer-with-a-time-out"></a>F. 在具有逾時之本機電腦上監視特定交談的狀態  
 下列範例將示範如何監視特定交談，其中起始端和目標服務位於執行 **ssbdiagnose** 之相同電腦的預設執行個體內的相同資料庫中。 其逾時間隔設定為 20 秒。  
  
```  
ssbdiagnose -E -d TestDatabase RUNTIME -ID D68D77A9-B1CF-41BF-A5CE-279ABCAB140D -TIMEOUT 20  
```  
  
### <a name="g-monitor-the-status-of-a-conversation-that-spans-two-computers"></a>G. 監視跨越兩部電腦之交談的狀態  
 下列範例將示範如何監視特定交談，其中起始端和目標服務位於不同的電腦上。  
  
```  
ssbdiagnose RUNTIME -ID D68D77A9-B1CF-41BF-A5CE-279ABCAB140D   
-TIMEOUT 10 CONNECT TO -E -S InitiatorComputer/InitiatorInstance   
-d InitiatorDatabase CONNECT TO -E -S TargetComputer/TargetInstance   
-d TargetDatabase  
```  
  
### <a name="h-monitor-the-status-of-a-conversation-in-two-databases-in-the-same-instance"></a>H. 監視相同執行個體內兩個資料庫中的交談狀態  
 下列範例將示範如何監視特定交談，其中起始端和目標服務位於相同 [!INCLUDE[ssDE](../../includes/ssde-md.md)]執行個體的不同資料庫中。 此範例會使用 **baseconnectionoptions** 指定執行個體和登入資訊，並且使用兩個 CONNECT TO 子句來指定資料庫。 此外，也指定了 -SHOWEVENTS，如此所有執行階段事件都會包含在報表輸出中。  
  
```  
ssbdiagnose -E -S TestComputer/DevTestInstance RUNTIME -SHOWEVENTS   
-ID 5094d4a7-e38c-4c37-da37-1d58b1cb8455 -TIMEOUT 10 CONNECT TO   
-d InitiatorDatabase CONNECT TO -d TargetDatabase  
```  
  
### <a name="i-monitor-the-status-of-two-conversations-between-two-databases"></a>I. 監視兩個資料庫之間兩個交談的狀態  
 下列範例將示範如何監視兩個交談，其中起始端和目標服務位於相同 [!INCLUDE[ssDE](../../includes/ssde-md.md)]執行個體的不同資料庫中。 此範例會使用 **baseconnectionoptions** 指定執行個體和登入資訊，並且使用兩個 CONNECT TO 子句來指定資料庫。  
  
```  
ssbdiagnose -E -S TestComputer/DevTestInstance RUNTIME   
-ID 5094d4a7-e38c-4c37-da37-1d58b1cb8455   
-ID 9b293be9-226b-4e22-e169-1d2c2c15be86 -TIMEOUT 10 CONNECT TO   
-d InitiatorDatabase CONNECT TO -d TargetDatabase  
```  
  
### <a name="j-monitor-the-status-of-all-conversations-between-two-databases"></a>J. 監視兩個資料庫之間的所有交談狀態  
 下列範例將示範如何監視相同 [!INCLUDE[ssDE](../../includes/ssde-md.md)]執行個體中兩個資料庫之間的所有交談。 此範例會使用 **baseconnectionoptions** 指定執行個體和登入資訊，並且使用兩個 CONNECT TO 子句來指定資料庫。  
  
```  
ssbdiagnose -E -S TestComputer/DevTestInstance RUNTIME   
-TIMEOUT 10 CONNECT TO -d InitiatorDatabase CONNECT TO   
-d TargetDatabase  
```  
  
### <a name="k-ignore-specific-errors"></a>K. 忽略特定錯誤  
 下列範例將示範如何在目前已設定啟用方式的測試系統中忽略已知的錯誤 (303 和 304)。  
  
```  
ssbdiagnose -IGNORE 303 -IGNORE 304 -E -d TestDatabase   
CONFIGURATION FROM SERVICE /test/initiator TO SERVICE /test/target   
ON CONTRACT TextContract  
```  
  
### <a name="l-redirecting-ssbdiagnose-xml-output"></a>L. 重新導向 ssbdiagnose XML 輸出  
 下列範例將示範如何要求 **ssbdiagnose** 將其輸出產生為 XML 檔，以便重新導向至檔案。 然後，應用程式可以開啟 TestDiag.xml 檔，以便分析或報告 **ssbdiagnose** XML 檔。 或者，您可以從一般 XML 編輯器 (例如 XML Notepad) 中檢視此檔案。  
  
```  
ssbdiagnose -XML -E -d MyDatabase CONFIGURATION FROM SERVICE   
/test/initiator TO SERVICE /test/target > c:\MyDiagnostics\TestDiag.xml  
```  
  
### <a name="m-using-an-environment-variable"></a>M. 使用環境變數  
 下列範例會先設定 SQLCMDSERVER 環境變數，以便保存伺服器名稱，然後在不指定 **-S** 的情況下執行 **ssbdiagnose**。  
  
```  
SET SQLCMDSERVER=MyComputer  
ssbdiagnose -XML -E -d MyDatabase CONFIGURATION FROM SERVICE   
/test/initiator TO SERVICE /test/target  
```  
  
## <a name="see-also"></a>另請參閱  
 [SQL Server Service Broker](../../database-engine/configure-windows/sql-server-service-broker.md)   
 [BEGIN DIALOG CONVERSATION &#40;Transact-SQL&#41;](../../t-sql/statements/begin-dialog-conversation-transact-sql.md)   
 [CREATE BROKER PRIORITY &#40;Transact-SQL&#41;](../../t-sql/statements/create-broker-priority-transact-sql.md)   
 [CREATE CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/create-certificate-transact-sql.md)   
 [CREATE CONTRACT &#40;Transact-SQL&#41;](../../t-sql/statements/create-contract-transact-sql.md)   
 [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/create-endpoint-transact-sql.md)   
 [CREATE MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/create-master-key-transact-sql.md)   
 [CREATE MESSAGE TYPE &#40;Transact-SQL&#41;](../../t-sql/statements/create-message-type-transact-sql.md)   
 [CREATE QUEUE &#40;Transact-SQL&#41;](../../t-sql/statements/create-queue-transact-sql.md)   
 [CREATE REMOTE SERVICE BINDING &#40;Transact-SQL&#41;](../../t-sql/statements/create-remote-service-binding-transact-sql.md)   
 [CREATE ROUTE &#40;Transact-SQL&#41;](../../t-sql/statements/create-route-transact-sql.md)   
 [CREATE SERVICE &#40;Transact-SQL&#41;](../../t-sql/statements/create-service-transact-sql.md)   
 [RECEIVE &#40;Transact-SQL&#41;](../../t-sql/statements/receive-transact-sql.md)   
 [sys.transmission_queue &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-transmission-queue-transact-sql.md)   
 [sys.conversation_endpoints &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-conversation-endpoints-transact-sql.md)   
 [sys.conversation_groups &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-conversation-groups-transact-sql.md)  
  
