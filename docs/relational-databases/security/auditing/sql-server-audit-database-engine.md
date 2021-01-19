---
title: SQL Server Audit (Database Engine) | Microsoft Docs
description: 了解 SQL Server 資料庫引擎或個別資料庫的伺服器稽核。 伺服器稽核包含伺服器和資料庫稽核規格。
ms.custom: ''
ms.date: 01/01/2020
ms.prod: sql
ms.prod_service: security
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
f1_keywords:
- audit
helpviewer_keywords:
- SQL Server Audit
- audits [SQL Server], SQL Server Audit
ms.assetid: 0c1fca2e-f22b-4fe8-806f-c87806664f00
author: davidtrigano
ms.author: datrigan
monikerRange: =azuresqldb-mi-current||>=sql-server-2016||>=sql-server-linux-2017
ms.openlocfilehash: 45b22073dd0ac7e4c87d8fb838e9e55e346b5b5d
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171460"
---
# <a name="sql-server-audit-database-engine"></a>SQL Server Audit (Database Engine)
[!INCLUDE[sql-asdbmi](../../../includes/applies-to-version/sql-asdbmi.md)]

  「稽核」[!INCLUDE[ssDEnoversion](../../../includes/ssdenoversion-md.md)] 執行個體或個別資料庫會在 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 上發生追蹤及記錄事件。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 稽核可讓您建立伺服器稽核，其中可包含伺服器層級事件的伺服器稽核規格，以及資料庫層級事件的資料庫稽核規格。 稽核的事件可以寫入事件記錄或稽核檔案。  
  
[!INCLUDE[ssMIlimitation](../../../includes/sql-db-mi-limitation.md)]
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]的稽核有幾個層級，需視安裝的管制或標準需求而定。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Audit 提供了一些工具和程序，您必須擁有這些工具和程序，才能啟用、儲存及檢視各種伺服器和資料庫物件的稽核。  
  
 您可以依各個執行個體記錄伺服器稽核動作群組，然後依各個資料庫記錄資料庫稽核動作群組或資料庫稽核動作。 每次遇到可稽核的動作時，就會發生稽核事件。  
  
 所有 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本都支援伺服器層級稽核。 所有版本都支援以 [!INCLUDE[ssSQL15_md](../../../includes/sssql16-md.md)] SP1 開頭的資料庫層級稽核。 在這之前，資料庫層級稽核則限於 Enterprise、Developer 和 Evaluation Edition。 如需詳細資訊，請參閱 [SQL Server 2016 版本支援的功能](~/sql-server/editions-and-supported-features-for-sql-server-2016.md)。  
  
> [!NOTE]  
>  本主題適用於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]。  若是 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]，請參閱 [開始使用 SQL 資料庫稽核](/azure/azure-sql/database/auditing-overview)。  
  
## <a name="sql-server-audit-components"></a>SQL Server Audit 元件  
 「稽核」  是針對特定伺服器動作或資料庫動作群組將幾個元素組合成單一封裝的動作。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Audit 的元件結合起來可產生稱為稽核的輸出，就像是與圖形和資料元素結合的報表定義會產生報表一樣。  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Audit 會使用「擴充事件」  來協助建立稽核。 如需擴充事件的詳細資訊，請參閱 [擴充事件](../../../relational-databases/extended-events/extended-events.md)。  
  
### <a name="sql-server-audit"></a>SQL Server Audit  
 *SQL Server Audit* 物件會收集要監視之伺服器或資料庫層級動作和動作群組的單一執行個體。 此稽核位於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體層級。 您可以針對每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體設有多個稽核。  
  
 當您定義稽核時，會針對結果的輸出指定位置， 這就是稽核目的地。 此稽核會在「停用」  狀態下建立，而且不會自動稽核任何動作。 在啟用稽核之後，稽核目的地會從此稽核接收資料。  
  
### <a name="server-audit-specification"></a>伺服器稽核規格  
 *「伺服器稽核規格」* (Server Audit Specification) 物件屬於稽核。 您可以針對每個稽核建立一個伺服器稽核規格，因為這兩者都是在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 執行個體範圍所建立。  
  
 伺服器稽核規格會收集由擴充的事件功能所引發的許多伺服器層級動作群組。 您可以將「稽核動作群組」  併入伺服器稽核規格中。 稽核動作群組是預先定義的動作群組，這些動作是 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]中發生之不可部分完成的事件。 這些動作會傳送給稽核，然後它會在目標中記錄這些動作。  
  
 伺服器層級的稽核動作群組在 [SQL Server Audit 動作群組和動作](../../../relational-databases/security/auditing/sql-server-audit-action-groups-and-actions.md)主題中有描述。  
  
### <a name="database-audit-specification"></a>資料庫稽核規格  
 「資料庫稽核規格」  物件屬於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Audit。 您可以針對每個稽核的每個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資料庫建立一個資料庫稽核規格。  
  
 資料庫稽核規格會收集由擴充的事件功能所引發的資料庫層級稽核動作。 您可以將稽核動作群組或稽核事件加入資料庫稽核規格。 「稽核事件」 是可由 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 引擎稽核之不可部分完成的動作。 「稽核動作群組」 是預先定義的動作群組。 這兩者都在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資料庫範圍內。 這些動作會傳送給稽核，然後它會在目標中記錄這些動作。 請勿在使用者資料庫稽核規格中包含伺服器範圍的物件，例如系統檢視表。  
  
 資料庫層級的稽核動作群組和稽核動作在 [SQL Server Audit 動作群組和動作](../../../relational-databases/security/auditing/sql-server-audit-action-groups-and-actions.md)主題中有描述。  
  
### <a name="target"></a>目標  
 稽核的結果會傳送到目標，這可以是檔案、Windows 安全性事件記錄檔或 Windows 應用程式事件記錄檔 記錄檔必須定期檢閱及封存，以確保目標有足夠的空間來寫入其他的記錄。  
  
> [!IMPORTANT]  
>  任何經過驗證的使用者都可以讀寫 Windows 應用程式事件記錄檔。 應用程式事件記錄檔所需的權限低於 Windows 安全性事件記錄檔，所以比起 Windows 安全性事件記錄檔是較不安全的。  
  
 寫入到 Windows 安全性記錄檔需要將 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶加入 [產生安全性稽核]  原則。 根據預設，本機系統、本機服務和網路服務都是此原則的一部分。 您可以使用安全性原則嵌入式管理單元 (secpol.msc) 來設定這項設定。 此外，[稽核物件存取]  安全性原則必須已啟用 [成功]  和 [失敗] 。 您可以使用安全性原則嵌入式管理單元 (secpol.msc) 來設定這項設定。 在 [!INCLUDE[wiprlhext](../../../includes/wiprlhext-md.md)] 或 Windows Server 2008 中，您可以使用稽核原則程式 ( **AuditPol.exe)** 從命令列設定更細微的 [產生的應用程式]原則。 如需啟用寫入 Windows 安全性記錄檔之步驟的詳細資訊，請參閱 [將 SQL Server Audit 事件寫入安全性記錄檔](../../../relational-databases/security/auditing/write-sql-server-audit-events-to-the-security-log.md)。 如需 Auditpol.exe 程式的詳細資訊，請參閱知識庫文章 921469： [如何使用「群組原則」進行詳細的安全性稽核設定](https://support.microsoft.com/kb/921469/)。 Windows 事件記錄檔在 Windows 作業系統中為全域的範圍。 如需 Windows 事件記錄檔的詳細資訊，請參閱 [事件檢視器概觀](/previous-versions/windows/it-pro/windows-server-2003/cc737015(v=ws.10))。 如果您需要更精確的稽核權限，請使用二進位檔案目標。  
  
 當您將稽核資訊儲存到檔案時，為了避免遭到篡改，您可以使用以下方式來限制對檔案位置的存取：  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 服務帳戶必須具有讀取和寫入權限。  
  
-   稽核管理員通常需要讀取和寫入權限。 這會假設稽核管理員為管理稽核檔案的 Windows 帳戶，例如將稽核檔案複製到不同的共用位置、備份稽核檔案等等。  
  
-   被授權可讀取稽核檔案的稽核讀取者必須擁有讀取權限。  
  
 即使當 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 寫入檔案時，其他擁有權限的 Windows 使用者還是可以讀取稽核檔案。 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 不會進行獨佔鎖定來防止讀取作業。  
  
 因為 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 可以存取檔案，所以具有 CONTROL SERVER 權限的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 登入可以使用 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 來存取稽核檔案。 若要記錄正在讀取稽核檔案的任何使用者，請在 master.sys.fn_get_audit_file 上定義稽核。 這樣會記錄具有 CONTROL SERVER 權限的登入，該登入已經透過 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]來存取稽核檔案。  
  
 如果稽核管理員將檔案複製到不同的位置 (基於類似封存的理由)，新位置的 ACL 應該降低為以下權限：  
  
-   稽核管理員 - 讀取/寫入  
  
-   稽核讀取者 - 讀取  
  
 建議您從只有稽核管理員或稽核讀取者可存取的不同 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體 (例如 [!INCLUDE[ssExpress](../../../includes/ssexpress-md.md)]執行個體) 產生稽核報表。 您可以藉由使用不同的 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 執行個體進行報告，以防止未經授權的使用者取得稽核記錄的存取權。  
  
 您可以提供額外的防護措施來避免未經授權的人存取，其方式是使用 Windows BitLocker 磁碟機加密或 Windows 加密檔案系統，將用來存放稽核檔案的資料夾加密。  
  
 如需寫入目標之稽核記錄的詳細資訊，請參閱 [SQL Server Audit 記錄](../../../relational-databases/security/auditing/sql-server-audit-records.md)。  
  
## <a name="overview-of-using-sql-server-audit"></a>使用 SQL Server Audit 的概觀  
 您可以使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 或 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 來定義稽核。 在建立及啟用稽核之後，目標將會收到項目。  
  
 您可以使用 Windows 中的 [事件檢視器]  公用程式來閱讀 Windows 事件記錄檔。 如果是檔案目標，您可以使用 **中的 [記錄檔檢視器]** [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 或是 [fn_get_audit_file](../../../relational-databases/system-functions/sys-fn-get-audit-file-transact-sql.md) 函數來閱讀目標檔案。  
  
 建立及使用稽核的一般程序如下所示。  
  
1.  建立稽核，並定義目標。  
  
2.  建立對應至稽核的伺服器稽核規格或資料庫稽核規格。 啟用該稽核規格。  
  
3.  啟用稽核。  
  
4.  使用 Windows [事件檢視器] 、[記錄檔檢視器] 或是 fn_get_audit_file 函數來閱讀稽核事件。  

 如需詳細資訊，請參閱 [建立伺服器稽核與伺服器稽核規格](../../../relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification.md) 和 [建立伺服器稽核和資料庫稽核規格](../../../relational-databases/security/auditing/create-a-server-audit-and-database-audit-specification.md)。  
  
## <a name="considerations"></a>考量  
 如果在稽核起始期間發生失敗，伺服器將不會啟動。 在此情況下，可以在命令列上使用 **-f** 選項來啟動伺服器。  
  
 當稽核失敗造成伺服器關閉，或是因為已針對稽核指定 ON_FAILURE=SHUTDOWN 而造成伺服器無法啟動時，MSG_AUDIT_FORCED_SHUTDOWN 事件將會寫入記錄檔中。 由於關閉將發生在初次遇到此設定時，所以此事件將會寫入一次。 當稽核的失敗訊息造成伺服器關閉之後，將會寫入此事件。 管理員可藉由使用 **-m** 旗標在單一使用者模式下啟動[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]，以略過稽核所導致的關閉。 如果您在單一使用者模式下啟動，您會將指定 ON_FAILURE=SHUTDOWN 於該工作階段執行的任何稽核降級為 ON_FAILURE=CONTINUE。 當使用 **-m** 旗標啟動 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 時，MSG_AUDIT_SHUTDOWN_BYPASSED 訊息將會寫入到錯誤記錄檔。  
  
 如需服務啟動選項的詳細資訊，請參閱 [Database Engine 服務啟動選項](../../../database-engine/configure-windows/database-engine-service-startup-options.md)。  
  
### <a name="attaching-a-database-with-an-audit-defined"></a>附加已定義稽核的資料庫  
 如果附加的資料庫有稽核規格，而且指定了未存在於伺服器上的 GUID 時，將會造成「孤立的」  稽核規格。 因為具有相符 GUID 的稽核不存在於伺服器執行個體上，所以不會記錄任何稽核事件。 若要更正這個狀況，請使用 ALTER DATABASE AUDIT SPECIFICATION 命令，將被遺棄的稽核規格連接到現有的伺服器稽核。 或者，使用 CREATE SERVER AUDIT 命令，建立具有指定之 GUID 的新伺服器稽核。  
  
 您可以將有定義稽核規格的資料庫附加到不支援 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Audit 的另一個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 版本 (例如 [!INCLUDE[ssExpress](../../../includes/ssexpress-md.md)] )，但是它不會記錄稽核事件。  
  
### <a name="database-mirroring-and-sql-server-audit"></a>資料庫鏡像和 SQL Server Audit  
 已定義資料庫稽核規格而且使用資料庫鏡像的資料庫將會包含資料庫稽核規格。 若要在鏡像的 SQL 執行個體上正確運作，必須設定下列項目：  
  
-   鏡像伺服器必須具有相同 GUID 的稽核，才能讓資料庫稽核規格寫入稽核記錄。 您可使用 CREATE AUDIT WITH GUID **=** _\<GUID from source Server Audit_> 命令來設定此項目。  
  
-   如果是二進位檔案目標，鏡像伺服器服務帳戶必須具有寫入稽核記錄之位置的適當權限。  
  
-   如果是 Windows 事件記錄檔目標，鏡像伺服器所在之電腦的安全性原則必須允許服務帳戶存取安全性或應用程式事件記錄檔。  
  
### <a name="auditing-administrators"></a>稽核系統管理員  
 **系統管理員** 固定伺服器角色的成員會識別為每個資料庫中的 **dbo** 使用者。 若要稽核系統管理員的動作，請稽核 **dbo** 使用者的動作。  
  
## <a name="creating-and-managing-audits-with-transact-sql"></a>使用 Transact-SQL 建立及管理稽核  
 您可以使用 DDL 陳述式、動態管理檢視和函數及目錄檢視來實作 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Audit 的所有層面。  
  
### <a name="data-definition-language-statements"></a>資料定義語言陳述式  
 您可以使用下列 DDL 陳述式來建立、改變和卸除稽核規格：  
  
|DDL 陳述式|描述| 
|-|-|  
|[ALTER AUTHORIZATION](../../../t-sql/statements/alter-authorization-transact-sql.md)|變更安全性實體的擁有權。|  
|[ALTER DATABASE AUDIT SPECIFICATION](../../../t-sql/statements/alter-database-audit-specification-transact-sql.md)|使用 SQL Server Audit 功能改變資料庫稽核規格物件。|  
|[ALTER SERVER AUDIT](../../../t-sql/statements/alter-server-audit-transact-sql.md)|使用 SQL Server Audit 功能改變伺服器稽核物件。|  
|[ALTER SERVER AUDIT SPECIFICATION](../../../t-sql/statements/alter-server-audit-specification-transact-sql.md)|使用 SQL Server Audit 功能改變伺服器稽核規格物件。|  
|[CREATE DATABASE AUDIT SPECIFICATION](../../../t-sql/statements/create-database-audit-specification-transact-sql.md)|使用 SQL Server Audit 功能建立資料庫稽核規格物件。|  
|[CREATE SERVER AUDIT](../../../t-sql/statements/create-server-audit-transact-sql.md)|使用 SQL Server Audit 來建立伺服器稽核物件。|  
|[CREATE SERVER AUDIT SPECIFICATION](../../../t-sql/statements/create-server-audit-specification-transact-sql.md)|使用 SQL Server Audit 功能建立伺服器稽核規格物件。|  
|[DROP DATABASE AUDIT SPECIFICATION](../../../t-sql/statements/drop-database-audit-specification-transact-sql.md)|使用 SQL Server Audit 功能卸除資料庫稽核規格物件。|  
|[DROP SERVER AUDIT](../../../t-sql/statements/drop-server-audit-transact-sql.md)|使用 SQL Server Audit 功能卸除伺服器稽核物件。|  
|[DROP SERVER AUDIT SPECIFICATION](../../../t-sql/statements/drop-server-audit-specification-transact-sql.md)|使用 SQL Server Audit 功能卸除伺服器稽核規格物件。|  
  
### <a name="dynamic-views-and-functions"></a>動態檢視和函數  
 下表列出可用於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 稽核的動態檢視和函數。  
  
|動態檢視和函數|描述|  
|---------------------------------|-----------------|  
|[sys.dm_audit_actions](../../../relational-databases/system-dynamic-management-views/sys-dm-audit-actions-transact-sql.md)|傳回稽核記錄檔中可報告的每一個稽核動作及 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Audit 中可設定之每一個稽核動作群組的資料列。|  
|[sys.dm_server_audit_status](../../../relational-databases/system-dynamic-management-views/sys-dm-server-audit-status-transact-sql.md)|提供有關稽核之目前狀態的資訊。|  
|[sys.dm_audit_class_type_map](../../../relational-databases/system-dynamic-management-views/sys-dm-audit-class-type-map-transact-sql.md)|傳回一個資料表，它會將稽核記錄檔中的 class_type 欄位對應到 sys.dm_audit_actions 中的 class_desc 欄位。|  
|[fn_get_audit_file](../../../relational-databases/system-functions/sys-fn-get-audit-file-transact-sql.md)|從伺服器稽核建立的稽核檔案中傳回資訊。|  
  
### <a name="catalog-views"></a>目錄檢視  
 下表列出可用於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 稽核的目錄檢視。  
  
|目錄檢視|描述|  
|-------------------|-----------------|  
|[sys.database_audit_specifications](../../../relational-databases/system-catalog-views/sys-database-audit-specifications-transact-sql.md)|包含有關伺服器執行個體上 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 稽核內之資料庫稽核規格的資訊。|  
|[sys.database_audit_specification_details](../../../relational-databases/system-catalog-views/sys-database-audit-specification-details-transact-sql.md)|包含所有資料庫伺服器執行個體上 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 稽核內之資料庫稽核規格的資訊。|  
|[sys.server_audits](../../../relational-databases/system-catalog-views/sys-server-audits-transact-sql.md)|針對伺服器執行個體中的每一個 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 稽核各包含一個資料列。|  
|[sys.server_audit_specifications](../../../relational-databases/system-catalog-views/sys-server-audit-specifications-transact-sql.md)|包含有關伺服器執行個體上 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 稽核內之伺服器稽核規格的資訊。|  
|[sys.server_audit_specifications_details](../../../relational-databases/system-catalog-views/sys-server-audit-specification-details-transact-sql.md)|包含伺服器執行個體上 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 稽核內之伺服器稽核規格詳細資料 (動作) 的資訊。|  
|[sys.server_file_audits](../../../relational-databases/system-catalog-views/sys-server-file-audits-transact-sql.md)|包含有關伺服器執行個體上 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 稽核內之檔案稽核類型的存放區擴充資訊。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Audit 的每一個功能和命令都有個別的權限需求。  
  
 若要建立、改變或卸除伺服器稽核或伺服器稽核規格，伺服器主體需要 ALTER ANY SERVER AUDIT 或 CONTROL SERVER 權限。 若要建立、改變或卸除資料庫稽核規格，資料庫主體需要資料庫的 ALTER ANY DATABASE AUDIT 權限或是 ALTER 或 CONTROL 權限。 此外，主體還必須擁有連接資料庫的權限，或 ALTER ANY SERVER AUDIT 或 CONTROL SERVER 權限。  
  
 VIEW ANY DEFINITION 權限提供的存取權可以檢視伺服器層級稽核檢視，而 VIEW DEFINITION 提供的存取權可以檢視資料庫層級稽核檢視。 拒絕這些權限時，會覆寫目錄檢視的檢視能力，即使主體具有 ALTER ANY SERVER AUDIT 或 ALTER ANY DATABASE AUDIT 權限亦同。  
  
 如需如何授與權限的詳細資訊，請參閱 [GRANT &#40;Transact-SQL&#41;](../../../t-sql/statements/grant-transact-sql.md)。  
  
> [!CAUTION]  
>  系統管理員 (sysadmin) 角色中的主體可以損害任何稽核元件，而 db_owner 角色中的主體可以損害資料庫中的稽核規格。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Audit 將會驗證建立或改變稽核規格的登入至少要有 ALTER ANY DATABASE AUDIT 權限。 但是，當您附加資料庫時，它不會進行任何驗證。 您應該假設所有資料庫稽核規格都與系統管理員 (sysadmin) 或 db_owner 角色中的主體一樣值得信任。  
  
## <a name="related-tasks"></a>相關工作  
 [建立伺服器稽核與伺服器稽核規格](../../../relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification.md)  
  
 [建立伺服器稽核和資料庫稽核規格](../../../relational-databases/security/auditing/create-a-server-audit-and-database-audit-specification.md)  
  
 [檢視 SQL Server Audit 記錄](../../../relational-databases/security/auditing/view-a-sql-server-audit-log.md)  
  
 [將 SQL Server Audit 事件寫入安全性記錄檔](../../../relational-databases/security/auditing/write-sql-server-audit-events-to-the-security-log.md)  
  
## <a name="topics-closely-related-to-auditing"></a>與稽核密切相關的主題  
 [伺服器屬性 &#40;安全性頁面&#41;](../../../database-engine/configure-windows/server-properties-security-page.md)  
 說明如何開啟 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]的登入稽核。 這些稽核記錄會儲存在 Windows 應用程式記錄檔中。  
  
 [C2 稽核模式伺服器組態選項](../../../database-engine/configure-windows/c2-audit-mode-server-configuration-option.md)  
 說明 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]中的 C2 安全性規範稽核模式。  
  
 [安全性稽核事件類別目錄 &#40;SQL Server Profiler&#41;](../../../relational-databases/event-classes/security-audit-event-category-sql-server-profiler.md)  
 說明 [!INCLUDE[ssSqlProfiler](../../../includes/sssqlprofiler-md.md)]中可用的稽核事件。 如需詳細資訊，請參閱 [SQL Server Profiler](../../../tools/sql-server-profiler/sql-server-profiler.md)。  
  
 [SQL 追蹤](../../../relational-databases/sql-trace/sql-trace.md)  
 說明如何從自己的應用程式中使用 SQL 追蹤來手動建立追蹤，而不是使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Profiler 來建立追蹤。  
  
 [DDL 觸發程序](../../../relational-databases/triggers/ddl-triggers.md)  
 說明如何使用資料定義語言 (DDL) 觸發程序來追蹤資料庫的變更。  
  
 [Microsoft TechNet：SQL Server TechCenter：SQL Server 2005 安全性與保護](../../../sql-server/index.yml)  
 提供有關 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 安全性的最新資訊。  
  
## <a name="see-also"></a>另請參閱  
 [SQL Server Audit 動作群組和動作](../../../relational-databases/security/auditing/sql-server-audit-action-groups-and-actions.md)   
 [SQL Server Audit 記錄](../../../relational-databases/security/auditing/sql-server-audit-records.md)  
  
