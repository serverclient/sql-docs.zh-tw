---
title: 資料庫屬性 (選項頁面) | Microsoft 文件
description: 了解如何使用 [資料庫屬性] 對話方塊中 [選項] 索引標籤來檢視或修改資料庫的定序、復原模式及其他設定。
ms.custom: ''
ms.date: 08/28/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
f1_keywords:
- sql13.swb.databaseproperties.options.f1
ms.assetid: a3447987-5507-4630-ac35-58821b72354d
author: stevestein
ms.author: sstein
ms.openlocfilehash: 87c3b10b95a37c74ea1595dca15341aeffe5c261
ms.sourcegitcommit: 4a813a0741502c56c0cd5ecaafafad2e857a9d7f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98031097"
---
# <a name="database-properties-options-page"></a>資料庫屬性 (選項頁面)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  使用此頁面來檢視或修改選取之資料庫的選項。 如需此頁面可用之選項的詳細資訊，請參閱 [ALTER DATABASE SET Options &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md) 和 [ALTER DATABASE SCOPED CONFIGURATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)。  
  
## <a name="page-header"></a>頁首  
 **定序**  
 從清單中選取以指定資料庫的定序。 如需詳細資訊，請參閱 [設定或變更資料庫定序](../../relational-databases/collations/set-or-change-the-database-collation.md)。  
  
 **復原模式**  
 指定下列其中一個復原資料庫模式：[完整]、[大量記錄] 或 [簡單]。 如需復原模式的詳細資訊，請參閱[復原模式 &#40;SQL Server&#41;](../../relational-databases/backup-restore/recovery-models-sql-server.md)。  
  
 **相容性層級**  
 指定資料庫所支援的最新 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本。 如需可能的值，請參閱 [ALTER DATABASE (Transact-SQL) 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。 升級 SQL Server 資料庫之後，會盡可能保留該資料庫的相容性層級，或變更為新 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所支援的最低層級。 
  
 **內含項目類型**  
 指定無或部分以指定其是否為自主資料庫。 如需自主資料庫的詳細資訊，請參閱 [自主資料庫](../../relational-databases/databases/contained-databases.md)。 必須先將伺服器屬性 [啟用自主資料庫] 設為 [TRUE]，才能將資料庫設為自主。  
  
> [!IMPORTANT]  
>  若啟用部分自主資料庫，會將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的存取控制權委派給資料庫擁有者。 如需詳細資訊，請參閱 [Security Best Practices with Contained Databases](../../relational-databases/databases/security-best-practices-with-contained-databases.md)。  
  
## <a name="automatic"></a>自動  
 **自動關閉**  
 指定最後一個使用者結束後，資料庫是否正常關閉並釋出資源。 可能的值為 **True** 和 **False**。 若為 [True]，資料庫會正常關閉，並在最後一位使用者登出之後釋放其資源。  

 **自動建立累加統計資料**  
 指定是否在每個分割區的統計資料建立時使用累加選項。 如需累加統計資料的資訊，請參閱 [CREATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/create-statistics-transact-sql.md)。  
  
 **自動建立統計資料**  
 指定資料庫是否自動建立遺漏的最佳化統計資料。 可能的值為 **True** 和 **False**。 若為 [True]，針對最佳化查詢所需之任何遺漏的統計資料，都會在最佳化時自動建立。 如需詳細資訊，請參閱 [CREATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/create-statistics-transact-sql.md)。  
  
 **自動壓縮**  
 指定資料庫檔案是否可用於定期壓縮。 可能的值為 **True** 和 **False**。 如需詳細資訊，請參閱 [壓縮資料庫](../../relational-databases/databases/shrink-a-database.md)。  
  
 **自動更新統計資料**  
 指定資料庫是否自動更新過時的最佳化統計資料。 可能的值為 **True** 和 **False**。 若為 [True]，針對最佳化查詢所需之任何過時的統計資料，都會在最佳化時自動建立。 如需詳細資訊，請參閱 [CREATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/create-statistics-transact-sql.md)。  
  
 **自動非同步更新統計資料**  
 若為 [True]，起始自動更新過期統計資料的查詢，不會在編譯之前等待統計資料更新。 當有可用的更新統計資料時，後續的查詢會使用這些統計資料。  
  
 若為 [False]，初始化自動更新過期統計資料的查詢，則會等到可在查詢最佳化計畫中使用更新的統計資料。  
  
 除非 [自動更新統計資料] 也設定為 [True]，否則將這個選項設定為 [True] 時並不會有任何影響。  

## <a name="azure"></a>Azure
當連接到 Azure SQL Database 時，此區段會具有控制服務等級目標 (SLO) 的設定。 新資料庫的預設 SLO 為標準 S2。

  **目前的服務等級目標** 要使用的特定 SLO。 有效值受限於選取的版本。 如果您想要的 SLO 值不在清單中，您可以鍵入值。

  **版本** 要使用的 Azure SQL Database 版本，例如基本或進階。 如果您需要的版本值不在清單中，您可以鍵入值，它必須符合 Azure REST API 中使用的值。
  
  **大小上限** 資料庫的大小上限。 如果想要的大小值不在清單中，您可以鍵入值。 保留空白會使用指定版本和 SLO 的預設大小。
  
## <a name="containment"></a>Containment  
 在自主資料庫中，通常在伺服器層級設定的某些設定可在資料庫層級進行設定。  
  
 **預設全文檢索語言 LCID**  
 指定全文檢索索引資料行的預設語言。 全文檢索索引資料的語言分析相依於資料所用的語言。 這個選項的預設值是伺服器使用的語言。 如需所顯示設定的對應語言，請參閱 [sys.fulltext_languages &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-fulltext-languages-transact-sql.md)。  
  
 **預設語言**  
 所有新自主資料庫使用者的預設語言，除非另有指定。  
  
 **巢狀觸發程序已啟用**  
 允許觸發程序引發其他觸發程序。 觸發程序的巢狀結構，最多可達 32 層。 如需詳細資訊，請參閱 [CREATE TRIGGER &#40;Transact-SQL&#41;](../../t-sql/statements/create-trigger-transact-sql.md) 中的＜巢狀觸發程序＞一節。  
  
 **轉換非搜尋字**  
 如果非搜尋字 (即停用字詞) 導致全文檢索查詢的布林運算傳回零資料列，則會隱藏錯誤訊息。 如需詳細資訊，請參閱 [轉換非搜尋字伺服器組態選項](../../database-engine/configure-windows/transform-noise-words-server-configuration-option.md)。  
  
 **兩位數年份的截止**  
 指出可用兩位數年份格式輸入的最大年份數目。 列出的年份以及前 99 年均可用兩位數年份格式輸入。 所有其他的年份都必須用四位數年份輸入。  
  
 例如，2049 的預設設定指出用 '3/14/49' 格式輸入的日期會被解譯為 2049 年 3 月 14 日，而用 '3/14/50' 格式輸入的日期會被解譯為 1950 年 3 月 14 日。 如需詳細資訊，請參閱 [設定 two digit year cutoff 伺服器組態選項](../../database-engine/configure-windows/configure-the-two-digit-year-cutoff-server-configuration-option.md)。  
  
## <a name="cursor"></a>資料指標  
 **認可時關閉資料指標已啟用**  
 指定在開啟此資料指標的交易已經認可之後是否關閉資料指標。 可能的值為 **True** 和 **False**。 若為 [True]，在認可或回復交易時開啟的任何資料指標都會關閉。 若為 [False]，這類資料指標在認可交易時仍維持開啟。 若為 [False]，回復交易會關閉除了定義為 INSENSITIVE 或 STATIC 以外的任何資料指標。 如需詳細資訊，請參閱 [SET CURSOR_CLOSE_ON_COMMIT &#40;Transact-SQL&#41;](../../t-sql/statements/set-cursor-close-on-commit-transact-sql.md)。  
  
 **預設資料指標**  
 指定預設資料指標行為。 若為 [True]，資料指標宣告預設為 LOCAL。 若為 [False]，則 [!INCLUDE[tsql](../../includes/tsql-md.md)] 資料指標預設為 GLOBAL。  
  
## <a name="database-scoped-configurations"></a>資料庫範圍的組態  
 在 SQL Server 2016 和 Azure SQL Database 中，有許多可將範圍設為資料庫層級的組態屬性。 如需所有這些設定的詳細資訊，請參閱 [ALTER DATABASE SCOPED CONFIGURATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md)。  
  
 **舊版基數估計**  
 指定與資料庫相容性層級無關之主要的查詢最佳化工具基數估計模型。 這與 [追蹤旗標 9481](https://support.microsoft.com/kb/2801413)相同。  
  
 **次要的舊版基數估計**  
 指定與資料庫相容性層級無關之次要 (如果有的話) 的查詢最佳化工具基數估計模型。 這與 [追蹤旗標 9481](https://support.microsoft.com/kb/2801413)相同。  
  
 **最大 DOP**  
 指定應該用於陳述式之主要的預設 [MAXDOP](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md) 設定。  
  
 **次要的最大 DOP**  
 指定應該用於陳述式之次要 (如果有的話) 的預設 [MAXDOP](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md) 設定。  
  
 **參數探測**  
 啟用或停用主要的參數探測。 這與 [追蹤旗標 4136](https://support.microsoft.com/kb/980653)相同。  
  
 **次要的參數探測**  
 啟用或停用次要 (如果有的話) 的參數探測。 這與 [追蹤旗標 4136](https://support.microsoft.com/kb/980653)相同。  
  
 **查詢最佳化工具修正程式**  
 啟用或停用主要的查詢最佳化 Hotfix，而不管資料庫的相容性層級為何。 這與 [追蹤旗標 4199](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)相同。 如需詳細資料，請參閱 [QUERY_OPTIMIZER_HOTFIXES](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md#qo_hotfixes)。  
  
 **次要的查詢最佳化工具修正程式**  
 啟用或停用次要 (如果有的話) 的查詢最佳化 Hotfix，而不管資料庫的相容性層級為何。 這與 [追蹤旗標 4199](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)相同。 如需詳細資料，請參閱 [QUERY_OPTIMIZER_HOTFIXES](../../t-sql/statements/alter-database-scoped-configuration-transact-sql.md#qo_hotfixes)。  
  
## <a name="filestream"></a>FILESTREAM  
 **FILESTREAM 目錄名稱**  
 針對與選定資料庫相關的 FILESTREAM 資料指定目錄名稱。  
  
 **FILESTREAM 非交易存取**  
 指定下列其中一個選項，可進行透過檔案系統到 FileTable 中所儲存 FILESTREAM 資料的非交易存取：**OFF**、**READ_ONLY** 或 **FULL**。 如果伺服器上未啟用 FILESTREAM，這個值會設定為 OFF 而且會停用。 如需詳細資訊，請參閱 [FileTables &#40;SQL Server&#41;](../../relational-databases/blob/filetables-sql-server.md)。  
  
## <a name="miscellaneous"></a>其他  
**允許快照集隔離**  
啟用這項功能。  

 **ANSI NULL 預設值**  
 針對在 **CREATE TABLE** 或 **ALTER TABLE** 陳述式期間未明確定義為 **NOT NULL** 的所有使用者定義資料類型或資料行，允許 Null 值 (預設狀態)。 如需詳細資訊，請參閱 [SET ANSI_NULL_DFLT_ON &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-null-dflt-on-transact-sql.md) 和 [SET ANSI_NULL_DFLT_OFF &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-null-dflt-off-transact-sql.md)。  
  
 **ANSI NULLS 已啟用**  
 使用 Null 值時，指定等於 (`=`) 和不等於 (`<>`) 比較運算子的行為。 可能的值為 [True] \(開啟) 與 [False] \(關閉)。 若為 [True]，所有與 Null 值的比較都會評估為 UNKNOWN。 若為 [False]，非 UNICODE 值與 Null 值都為 NULL 時，其比較會評估為 [True]。 如需詳細資訊，請參閱 [SET ANSI_NULLS &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-nulls-transact-sql.md)。  
  
 **ANSI 填補已啟用**  
 指定開啟或關閉 ANSI 填補。 允許的值為 [True] \(開啟) 與 [False] \(關閉)。 如需詳細資訊，請參閱 [SET ANSI_PADDING &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-padding-transact-sql.md)。  
  
 **ANSI 警告已啟用**  
 針對數個錯誤狀況指定 ISO 標準行為。 若為 [True]，Null 值出現在彙總函數 (例如 SUM、AVG、MAX、MIN、STDEV、STDEVP、VAR、VARP 或 COUNT) 時，就會產生警告訊息。 若為 [False]，則不會發出警告。 如需詳細資訊，請參閱 [SET ANSI_WARNINGS &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-warnings-transact-sql.md)。  
  
 **算術中止已啟用**  
 指定是否啟用資料庫選項算術中止。 可能的值為 **True** 和 **False**。 若為 [True]，溢位或除以零的錯誤將導致查詢或批次結束。 如果交易發生這個錯誤，就會回復交易。 若為 [False]，將會顯示警告訊息，但查詢、批次或交易則會像未發生錯誤般繼續進行。 如需詳細資訊，請參閱 [SET ARITHABORT &#40;Transact-SQL&#41;](../../t-sql/statements/set-arithabort-transact-sql.md)。  
  
 **串連 Null 產生 Null**  
 指定串連 Null 值時的行為。 當屬性值為 [True] 時，**string** + NULL 會傳回 NULL。 若為 [False]，其結果為 **string**。 如需詳細資訊，請參閱 [SET CONCAT_NULL_YIELDS_NULL &#40;Transact-SQL&#41;](../../t-sql/statements/set-concat-null-yields-null-transact-sql.md)。  
  
 **已啟用跨資料庫擁有權鏈結**  
 這個唯讀值指出是否已啟用跨資料庫擁有權鏈結。 若為 [True]，資料庫可以是跨資料庫擁有權鏈結的來源或目標。 使用 ALTER DATABASE 陳述式設定這個屬性。  
  
 **已啟用日期相互關聯的最佳化**  
 若為 [True]，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會在資料庫中由 FOREIGN KEY 條件約束所連結且具有 **datetime** 資料行的任意兩個資料表之間，維護相互關聯統計資料。  
  
 若為 [False]，則不會維謢相互關聯統計資料。  
 
 **延遲持久性**  
 啟用這項功能。 如需詳細資訊，請參閱[控制交易持久性](../logs/control-transaction-durability.md)。
 
 **為啟用讀取認可快照**  
 啟用這項功能。  
 
 **數值捨入中止**  
 指定資料庫如何處理捨入錯誤。 可能的值為 **True** 和 **False**。 若為 [True]，如果運算式中發生遺失有效位數便會產生錯誤。 若為 [False]，遺失有效位數並不會產生錯誤訊息，而且結果將進位到儲存該結果之資料行或變數的有效位數。 如需詳細資訊，請參閱 [SET NUMERIC_ROUNDABORT &#40;Transact-SQL&#41;](../../t-sql/statements/set-numeric-roundabort-transact-sql.md)。  
  
 **參數化**  
 若為 [SIMPLE]，將會根據資料庫的預設行為將查詢參數化。 若為 [FORCED]，則 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將資料庫中的所有查詢都參數化。  
  
 **引號識別碼已啟用**  
 指定如果以引號括住，是否可以使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 關鍵字做為識別碼 (物件或變數名稱)。 可能的值為 **True** 和 **False**。 如需詳細資訊，請參閱 [SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../../t-sql/statements/set-quoted-identifier-transact-sql.md)。  
  
 **遞迴觸發程序已啟用**  
 指定其他觸發程序是否可以引發觸發程序。 可能的值為 **True** 和 **False**。 設定為 [True] 時，就會啟用觸發程序的遞迴引發。 設定為 [False] 時，只會避免直接遞迴。 若要停用間接遞迴，請使用 sp_configure 將巢狀觸發程序伺服器選項設定為 0。 如需相關資訊，請參閱 [建立巢狀觸發程序](../../relational-databases/triggers/create-nested-triggers.md)。  
  
 **可信任**  
 顯示 [True] 時，這個唯讀選項指出 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 允許存取資料庫外的資源，但前提是資料庫內已建立模擬內容。 在資料庫模組上使用 EXECUTE AS 使用者陳述式或 EXECUTE AS 子句，即可在資料庫內建立模擬內容。  
  
 若要擁有存取權，資料庫的擁有者也需要具有伺服器層級的 AUTHENTICATE SERVER 權限。  
  
 這個屬性也允許在資料庫內建立和執行不安全及外部存取組件。 除了將這個屬性設定為 [True] 之外，資料庫的擁有者也必須具有伺服器層級的 EXTERNAL ACCESS ASSEMBLY 或 UNSAFE ASSEMBLY 權限。  
  
 根據預設，所有使用者資料庫和所有系統資料庫 (但不包括 **MSDB**) 都會將這個屬性設定為 [False]。 **model** 和 **tempdb** 資料庫的這個值不可變更。  
  
 只要資料庫是附加至伺服器，就會將 TRUSTWORTHY 設定為 [False]。  
  
 建議使用憑證和簽章在模擬內容下存取資料庫外的資源，而不是使用 [Trustworthy] 選項。  
  
 若要設定此屬性，請使用 ALTER DATABASE 陳述式。  
  
 **VarDecimal 儲存格式已啟用**  
 這個選項從 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]開始是唯讀的。 若為 [True]，這個資料庫就會啟用 Vardecimal 儲存格式。 如果資料庫中的任何資料表正在使用 Vardecimal 儲存格式，就無法停用 Vardecimal 儲存格式。 在 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本中，所有資料庫都會啟用 vardecimal 儲存格式。 這個選項會使用 [sp_db_vardecimal_storage_format](../../relational-databases/system-stored-procedures/sp-db-vardecimal-storage-format-transact-sql.md)。  
  
## <a name="recovery"></a>復原  
 **頁面確認**  
 指定用來探索和報告磁碟 I/O 錯誤所引起之不完整 I/O 交易的選項。 可能的值為 [無]、[TornPageDetection] 及 [總和檢查碼]。 如需詳細資訊，請參閱 [管理 suspect_pages 資料表 &#40;SQL Server&#41;](../../relational-databases/backup-restore/manage-the-suspect-pages-table-sql-server.md)，在  中還原頁面。  
  
 **目標復原時間 (秒)**  
 指定發生損毀時，復原指定之資料庫的時間上限 (以秒鐘表示)。 如需詳細資訊，請參閱[資料庫檢查點 &#40;SQL Server&#41;](../../relational-databases/logs/database-checkpoints-sql-server.md)。  

## <a name="service-broker"></a>Service Broker  
**Broker 已啟用**  
啟用或停用 Service Broker。  

**接受 Broker 優先權**  
唯讀 Service Broker 屬性。  

**Service Broker 識別碼**  
唯讀識別碼。  

## <a name="state"></a>State  
 **資料庫唯讀**  
 指定資料庫是否為唯讀。 可能的值為 **True** 和 **False**。 若為 [True]，使用者只能讀取資料庫中的資料。 使用者無法修改資料或資料庫物件，但可以使用 `DROP DATABASE` 陳述式刪除資料庫本身。 在指定 [資料庫唯讀] 選項的新值時，資料庫不可在使用中。 master 資料庫為例外，只有系統管理員可以在設定此選項時使用 master。  
  
 **資料庫狀態**  
 檢視資料庫的目前狀態。 您無法編輯它。 如需 [資料庫狀態] 的詳細資訊，請參閱[資料庫狀態](../../relational-databases/databases/database-states.md)。  

 **加密已啟用**  
 若為 [True]，這個資料庫就會啟用資料庫加密。 「資料庫加密金鑰」都需要加密。 如需詳細資訊，請參閱[透明資料加密 &#40;TDE&#41;](../../relational-databases/security/encryption/transparent-data-encryption.md)。  
 
 **限制存取**  
 指定哪些使用者可以存取資料庫。 可能的值包括：  
  
-   **多重**  
  
     生產資料庫的一般狀態，允許多位使用者同時存取資料庫。  
  
-   **Single**  
  
     用於維護動作，每次只允許一位使用者存取資料庫。  
  
-   **Restricted**  
  
     只有 db_owner、dbcreator 或 sysadmin 角色的成員可以使用資料庫。  
  


## <a name="see-also"></a>另請參閱  
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [CREATE DATABASE &#40;SQL Server Transact-SQL&#41;](../../t-sql/statements/create-database-transact-sql.md)  
