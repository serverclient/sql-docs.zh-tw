---
title: Always Encrypted | Microsoft Docs
description: SQL Server 和 Azure SQL Database 中支援透明用戶端加密和機密計算的 Always Encrypted 概觀
ms.custom: ''
ms.date: 10/30/2019
ms.prod: sql
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- encryption [SQL Server], Always Encrypted
- Always Encrypted
- TCE Always Encrypted
- Always Encrypted, about
- SQL13.SWB.COLUMNMASTERKEY.CLEANUP.F1
ms.assetid: 54757c91-615b-468f-814b-87e5376a960f
author: jaszymas
ms.author: jaszymas
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 534d7238316fe2037ea0ce43e2b4aeeb11e6eea2
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171330"
---
# <a name="always-encrypted"></a>Always Encrypted
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]

  ![一律加密](../../../relational-databases/security/encryption/media/always-encrypted.png "Always Encrypted")  
  
 Always Encrypted 是一個設計來保護儲存於 [!INCLUDE[ssSDSFull](../../../includes/sssdsfull-md.md)] 或 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 資料庫中之敏感性資料的功能，像是信用卡號碼或全國性的身分證字號 (例如美國社會安全號碼)。 Always Encrypted 可讓用戶端將用戶端應用程式內的敏感性資料進行加密，且絕不會顯示 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] ([!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 或 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]) 的加密金鑰。 如此一來，Always Encrypted 在資料擁有者 (且可以檢視資料) 和資料管理者 (但應該不具備存取權) 之間做出區隔。 透過確定內部部署資料庫系統管理員，雲端資料庫操作員，或其他高權限未經授權的使用者則無法存取加密資料，Always Encrypted 可讓客戶有信心地將機密資料存放在他們無法直接控制的位置。 這讓組織能夠將其資料儲存在 Azure 中，並將內部部署資料庫管理委派給第三方，或是降低組織本身 DBA 人員的安全性許可需求。

 Always Encrypted 藉由啟用 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 來處理對加密資料的一些查詢，同時保留資料的機密性並提供上述安全性優點，從而提供機密計算功能。 在 [!INCLUDE[ssSQL15](../../../includes/sssql16-md.md)]、[!INCLUDE[sssSQLv14](../../../includes/sssqlv14-md.md)] 和 [!INCLUDE[ssSDSFull](../../../includes/sssdsfull-md.md)] 中，Always Encrypted 透過決定性加密支援相等比較。 請參閱[選擇決定性加密或隨機加密](#selecting--deterministic-or-randomized-encryption)。 

  > [!NOTE] 
  > 在 [!INCLUDE[sql-server-2019](../../../includes/sssqlv15-md.md)] 中，安全記憶體保護區會利用模式比對、其他比較運算子和就地加密，大幅擴充 Always Encrypted 的機密計算功能。 請參閱[具有安全記憶體保護區的 Always Encrypted](always-encrypted-enclaves.md)。

 永遠加密讓應用程式加密變得透明化。 安裝在用戶端電腦上且啟用永遠加密的驅動程式，透過自動將用戶端應用程式中的機密資料進行加密與解密，進而達成此目的。 驅動程式會先將敏感資料行中的資料進行加密，才會將資料傳遞至 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]，並自動重寫查詢以保留應用程式的語意。 同樣地，驅動程式會將儲存在加密資料庫資料行並包含在查詢結果中的資料明確解密。  
  
 Always Encrypted 在 [!INCLUDE[ssSDSFull](../../../includes/sssdsfull-md.md)] 所有的版本中都有提供，從 [!INCLUDE[ssSQL15](../../../includes/sssql16-md.md)] 和 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 的所有服務層級開始。 (在 [!INCLUDE[ssSQL15_md](../../../includes/sssql16-md.md)] SP1 之前，Always Encrypted 僅限於 Enterprise Edition 中使用)。如需包含「永遠加密」的 Channel 9 簡報，請參閱 [使用 [永遠加密] 保護機密資料安全性](https://channel9.msdn.com/events/DataDriven/SQLServer2016/AlwaysEncrypted)。  

  
## <a name="typical-scenarios"></a>典型案例  
  
### <a name="client-and-data-on-premises"></a>用戶端和內部部署資料  
 客戶的用戶端應用程式和 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 皆在其業務位置的內部部署執行。 而客戶希望聘雇外部廠商來管理 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]。 為了保護儲存於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]中的機密資料，客戶使用了 [永遠加密] 確保資料庫系統管理員和應用程式系統管理員之間的責任有所區隔。 客戶可將 Always Encrypted 金鑰的純文字值儲存在用戶端應用程式可以存取的信任的金鑰存放區中。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 系統管理員不具備金鑰的存取權，因此無法解密儲存於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]中的機密資料。  
  
### <a name="client-on-premises-with-data-in-azure"></a>內部部署用戶端與 Azure 中的資料  
 客戶在其業務位置擁有內部部署的用戶端應用程式。 該應用程式會處理儲存於 Azure 託管之資料庫 ([!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 或在 Microsoft Azure 虛擬機器中執行的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] ) 中的敏感性資料。 客戶可使用 [永遠加密] 並將 Always Encrypted 金鑰儲存在內部部署託管之受信任的金鑰存放區中，以確保 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 雲端系統管理員無法存取敏感性資料。  
  
### <a name="client-and-data-in-azure"></a>用戶端和 Azure 中的資料  
 客戶的用戶端應用程式由 Microsoft Azure 託管 (例如，背景工作角色或 Web 角色)，該應用程式也會處理儲存於 Azure 託管之資料庫 (SQL Database 或在 Microsoft Azure 虛擬機器中執行的 SQL Server) 中的敏感性資料。 雖然 Always Encrypted 並未讓資料與雲端系統管理員完全隔離 (亦即託管用戶端層之平台的雲端系統管理員仍可看到資料和金鑰)，客戶仍具有降低安全性攻擊面 (資料庫中一律會加密資料) 的優勢。  
 
## <a name="how-it-works"></a>運作方式

您可以針對包含敏感性資料的個別資料庫資料行設定 [永遠加密]。 設定資料行加密時，可指定加密演算法和密碼編譯金鑰的相關資訊，以用來保護資料行中的資料。 [永遠加密] 會使用兩種類型的金鑰：資料行加密金鑰和資料行主要金鑰。 資料行加密金鑰用來加密已加密資料行中的資料。 資料行主要金鑰可為一或多個資料行加密金鑰進行加密，針對金鑰提供雙重保護。 

Database Engine 會將每個資料行的加密設定儲存在資料庫中繼資料中。 但請注意，Database Engine 絕不會以純文字的任何類型儲存或使用金鑰。 它只會將資料行加密金鑰的加密值以及資料行主要金鑰位置的資訊儲存在外部受信任的金鑰存放區，例如 Azure 金鑰保存庫、在用戶端電腦的 Windows 憑證存放區或硬體安全模組。

若要存取儲存在加密資料行的純文字資料，應用程式必須使用支援 [永遠加密] 的用戶端驅動程式。 當應用程式發出參數化查詢時，驅動程式會明確地與 Database Engine 共同作業並決定哪些參數是以加密的資料行為目標，因此應該受到加密。 驅動程式會針對需要加密的每一個參數，取得資料行的加密演算法以及資料行加密金鑰加密值的相關資訊、參數目標，以及其對應的資料行主要金鑰位置。

接下來，驅動程式會連絡內含資料行主要金鑰的金鑰存放區，以將加密的資料行加密金鑰值解密，再使用純文字資料行加密金鑰來加密參數。 系統會快取產生的純文字資料行加密金鑰，以減少後續使用相同資料行加密金鑰的金鑰存放區來回行程。 驅動程式會將以加密資料行為目標之參數的純文字值取代為其加密的值，再將查詢傳送至伺服器進行處理。

伺服器會計算結果集，並針對結果集中包含的任何加密資料行，附加資料行的加密中繼資料，包括加密演算法和對應金鑰的相關資訊。 驅動程式會先嘗試在本機快取中尋找純文字資料行加密金鑰；如果快取中找不到金鑰，它只會在資料行主要金鑰中來回一次。 接下來，驅動程式會解密結果，並將純文字值傳給應用程式。

 用戶端驅動程式會使用資料行主要金鑰存放區提供者，來與內含資料行主要金鑰的金鑰存放區互動；該提供者為一種用戶端軟體元件，可封裝含有資料行主要金鑰的金鑰存放區。 Microsoft 的用戶端驅動程式程式庫有提供常見的金鑰存放區提供者，或是作為獨立下載項目提供。 您也可以實作自己的提供者。 Always Encrypted 功能與內建的資料行主要金鑰存放區提供者，會因驅動程式程式庫和其版本而異。 

如需如何使用特定用戶端驅動程式與 Always Encrypted 來開發應用程式的詳細資訊，請參閱[使用 Always Encrypted 開發應用程式](always-encrypted-client-development.md)。

## <a name="remarks"></a>備註

加密和解密會透過用戶端驅動程式發生。 這表示使用 Always Encrypted 時，僅出現在伺服器端的某些動作將無法運作。 範例包括透過 UPDATE、BULK INSERT(T-SQL)、SELECT INTO、INSERT SELECT 將某個資料行的資料複製到另一個資料行。 

下列 UPDATE 範例會嘗試將資料從加密的資料行移至未加密的資料行，而不傳回結果集給用戶端： 

```sql
update dbo.Patients set testssn = SSN
```

如果 SSN 是使用 Always Encrypted 加密的資料行，則上述 update 陳述式會失敗並出現類似以下的錯誤：

```
Msg 206, Level 16, State 2, Line 89
Operand type clash: char(11) encrypted with (encryption_type = 'DETERMINISTIC', encryption_algorithm_name = 'AEAD_AES_256_CBC_HMAC_SHA_256', column_encryption_key_name = 'CEK_1', column_encryption_key_database_name = 'ssn') collation_name = 'Latin1_General_BIN2' is incompatible with char
```

若要成功更新資料行，請執行下列動作：

1. 從 SSN 資料行中選取資料，並將它儲存為應用程式中的結果集。 這可讓應用程式 (用戶端「驅動程式」  ) 將資料行解密。
2. 將結果集中的資料插入 SQL Server。 

 >[!IMPORTANT]
 > 在此案例中，資料會在傳回伺服器時予以解密，因為目的地資料行是不接受加密資料的一般 varchar。 
  
## <a name="selecting-deterministic-or-randomized-encryption"></a><a name="selecting--deterministic-or-randomized-encryption"></a> 選擇決定性加密或隨機加密  
 Database Engine 絕不會處理儲存於加密資料行中的純文字資料，但仍可根據資料行的加密類型，支援某些加密資料的查詢。 [永遠加密] 支援兩種類型的加密：隨機加密和決定性加密。  
  
- 確定性加密一律會針對特定的純文字值產生相同的加密值。 使用確定性加密時，您能根據加密資料行進行點查閱、相等聯結、分組和編製索引等作業。 但它也可能讓未獲授權使用者透過檢查加密資料行中的模式，來猜出加密值資訊，尤其當其中有一小組可能的加密值時 (例如 True/False 或北/南/東/西區域)。 確定性加密必須針對字元資料行使用 binary2 排序次序的資料行定序。

- 隨機化加密會使用更難預測的方式來加密資料。 隨機化加密雖較安全，但會讓您無法針對加密資料行進行搜尋、分組、編製索引和聯結等作業。

針對將做為搜尋或分組參數的資料行，請使用決定性加密。 例如，政府識別碼數字。 針對未利用其他記錄且未用來聯結資料表的資料 (例如機密調查註解)，請使用隨機加密。
如需 Always Encrypted 密碼編譯演算法的詳細資訊，請參閱 [Always Encrypted 密碼編譯](../../../relational-databases/security/encryption/always-encrypted-cryptography.md)。

## <a name="configuring-always-encrypted"></a>設定永遠加密

 資料庫中的 [永遠加密] 初始設定包括：產生 [永遠加密] 金鑰、建立金鑰中繼資料、設定所選資料庫資料行的加密屬性，及/或針對需要加密之資料行中可能已存在的資料進行加密。 請注意，其中有些工作並不支援 Transact-SQL，而需要使用用戶端工具。 由於 Always Encrypted 金鑰和受保護的敏感性資料絕不會以純文字形式顯示給伺服器，因此 Database Engine 無法佈建金鑰和執行資料加密或解密作業。 您可以使用 SQL Server Management Studio 或 PowerShell 來完成這類工作。 

|Task|SSMS|PowerShell|T-SQL|
|:---|:---|:---|:---
|搭配相對應的資料行主要金鑰，佈建資料行主要金鑰、資料行加密金鑰和加密的資料行加密金鑰。|是|是|否|
|在資料庫中建立金鑰中繼資料。|是|是|是|
|建立含加密資料行的新資料表|是|是|是|
|加密所選資料庫資料行中的現有資料|是|是|否|

> [!NOTE]
> 在 [!INCLUDE[sql-server-2019](../../../includes/sssqlv15-md.md)] 中所引進[具有安全記憶體保護區的 Always Encrypted](always-encrypted-enclaves.md) 支援使用 Transact-SQL 來加密現有資料。 此外，也不需要將資料移到資料之外，就能進行密碼編譯作業。

> [!NOTE]
> 執行金鑰佈建或資料加密工具時，請務必在裝載資料庫之電腦以外的電腦且安全的環境中進行。 否則，敏感性資料或金鑰可能會洩漏到伺服器環境中，而縮減使用 [永遠加密] 的優點。  

如需設定 [永遠加密] 的詳細資訊，請參閱︰
- [使用 SSMS 設定 Always Encrypted](../../../relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio.md)
- [使用 PowerShell 設定 Always Encrypted](../../../relational-databases/security/encryption/configure-always-encrypted-using-powershell.md)

## <a name="getting-started-with-always-encrypted"></a>開始使用 [永遠加密]

使用 [永遠加密的精靈](../../../relational-databases/security/encryption/always-encrypted-wizard.md) 來快速開始使用 Always Encrypted。 精靈將會佈建必要的金鑰，並針對所選的資料行設定加密。 如果您要設定加密的資料行已經包含一些資料，則精靈會加密這些資料。 下列範例會示範加密資料行的程序。

> [!NOTE]  
>  如需使用精靈的影片，請參閱 [Getting Started with Always Encrypted with SSMS](https://channel9.msdn.com/Shows/Data-Exposed/Getting-Started-with-Always-Encrypted-with-SSMS)(搭配 SSMS 開始使用永遠加密)。

1.  您可連接到現有的資料庫，其中包含您想要使用 Management Studio 物件總管  加密之資料行的資料表；或者，建立新的資料庫，再以要加密的資料行建立一或多個資料表，然後連接到該資料庫。
2.  以滑鼠右鍵按一下您的資料庫，指向 [工作]  ，然後按一下 [加密資料行]  以開啟 [Always Encrypted 精靈]  。
3.  檢閱[簡介]  頁面，然後按一下 [下一步]  。
4.  在 [資料行選取]  頁面上，展開資料表，並選取您想要加密的資料行。
5.  針對每個已選取要進行加密的資料行，將 [加密類型]  設定為 [決定性]  或 [隨機化]  。
6.  針對每個已選取要進行加密的資料行，選取 [加密金鑰]  。 如果您之前沒有針對此資料庫建立任何加密金鑰，請選取新的自動產生金鑰的預設選項，然後按一下 [下一步]  。
7.  在 [主要金鑰組態]  頁面上，選取要儲存新金鑰的位置，並選取主要金鑰來源，然後按一下 [下一步]  。
8.  在 [驗證]  頁面上，選擇是否要立即執行指令碼或建立 PowerShell 指令碼，然後按一下 [下一步]  。
9.  在 [摘要]  頁面上，檢閱您已選取的選項，然後按一下 [完成]  。 完成時請關閉精靈。

  
## <a name="feature-details"></a>功能詳細資料  
  
-   查詢可以在使用決定性加密進行加密的資料行上執行相等比較，但無法在其他的作業上執行 (例如大於/小於、使用 LIKE 運算子的模式比對或算術運算)。  
  
-   在使用隨機加密進行加密的資料行上的查詢，無法在任何這些資料行上執行作業。 不支援建立使用隨機加密進行加密之資料行的索引。  
 > [!NOTE] 
 > 在 [!INCLUDE[sql-server-2019](../../../includes/sssqlv15-md.md)] 中引進的[具有安全記憶體保護區的 Always Encrypted](always-encrypted-enclaves.md)，可以藉由啟用模式比對、比較運算子，以及使用隨機加密來對資料行編製索引，來解決上述限制。

-   資料行加密金鑰最多可以有兩個不同的加密值，每個都使用不同的資料行主要金鑰進行加密。 這有助於資料行主要金鑰輪替。  
  
-   決定性加密要求資料行具備其中一個 [*binary2* 定序](../../../relational-databases/collations/collation-and-unicode-support.md)。  

-   變更加密物件的定義之後，執行 [sp_refresh_parameter_encryption](../../../relational-databases/system-stored-procedures/sp-refresh-parameter-encryption-transact-sql.md) 更新物件的 Always Encrypted 中繼資料。
  
具有下列特性的資料行不支援 Always Encrypted。 例如，如果下列任何條件適用於資料行，則 `ENCRYPTED WITH` 子句不能用於資料行的 `CREATE TABLE/ALTER TABLE` 中：  
  
-   使用下列其中一種資料類型的資料行︰`xml`、`timestamp`/`rowversion`、`image`、`ntext`、`text`、`sql_variant`、`hierarchyid`、`geography`、`geometry`、別名、使用者定義類型。  
- `FILESTREAM` 資料行  
- 屬性為 `IDENTITY` 的資料行。  
- 屬性為 `ROWGUIDCOL` 的資料行。  
- 包含非 bin2 定序的字串 (`varchar`、`char` 等) 資料行。  
- 使用隨機化加密時，叢集和非叢集索引索引鍵的資料行 (支援確定性加密)。
- 包含在全文檢索索引中的資料行 (Always Encrypted 不支援[全文檢索搜尋](../../../relational-databases/search/full-text-search.md))。  
- 計算資料行。
- 計算資料行所參考之資料行 (當運算式執行 Always Encrypted 不支援的作業)。  
- 疏鬆資料行集合。  
- 使用隨機化加密時，統計資料所參考的資料行 (支援決定性加密)。  
- 使用別名資料型別的資料行。  
- 資料分割資料行。  
- 包含預設條件約束的資料行。  
- 使用隨機加密時，唯一條件約束所參考的資料行 (支援決定性加密)。  
- 使用隨機加密時的主索引鍵資料行 (支援決定性加密)。  
- 當使用隨機加密或使用決定性加密時 (如果已參考和參考資料行使用不同的索引鍵或演算法)，外部索引鍵條件約束中的參考資料行。  
- 檢查條件約束所參考之資料行。  
- 使用異動資料擷取來擷取/追蹤的資料行。  
- 具有變更追蹤之資料表上的主索引鍵資料行。  
- 已遮罩的資料行 (使用動態資料遮罩)。  
- Stretch Database 資料表中的資料行。 (包含使用 [永遠加密] 進行加密之資料行的資料表可針對 Stretch 啟用。)  
- 外部 (PolyBase) 資料表中的資料行 (附註：支援使用外部資料表和包含加密資料行的資料表)。  
- 不支援以加密的資料行為目標的資料表值參數。  

下列子句不能用於加密的資料行：

- `FOR XML`
- `FOR JSON PATH`

下列功能無法在加密的資料行運作：

- 交易式或合併式複寫
- 分散式查詢 (連結的伺服器，`OPENROWSET`(T-SQL)、`OPENDATASOURCE`(T-SQL))

工具需求

- 建議使用 SQL Server Management Studio 18 版或更高版本來執行查詢，以解密從加密資料行取得的結果，或插入、更新或篩選加密的資料行。 
- 需要 `sqlcmd` 13.1 版或更高版本，可從[下載中心](https://go.microsoft.com/fwlink/?LinkID=825643)取得。

  
## <a name="database-permissions"></a>資料庫權限  
 永遠加密有下列四個權限：  
  
*  `ALTER ANY COLUMN MASTER KEY` (需具備才能建立和刪除資料行主要金鑰)。  
  
*  `ALTER ANY COLUMN ENCRYPTION KEY` (需具備才能建立和刪除資料行加密金鑰)。 
  
*  `VIEW ANY COLUMN MASTER KEY DEFINITION` (需具備才能存取和讀取資料行主要金鑰的中繼資料，以管理金鑰或查詢加密資料行)。  
  
*  `VIEW ANY COLUMN ENCRYPTION KEY DEFINITION` (需具備才能存取和讀取資料行加密金鑰的中繼資料，以管理金鑰或查詢加密資料行)。  
  
 下表摘要說明一般動作所需的權限。  
  
|狀況|<code>ALTER ANY COLUMN MASTER KEY</code>|<code>ALTER ANY COLUMN ENCRYPTION KEY</code>|<code>VIEW ANY COLUMN MASTER KEY DEFINITION</code>|<code>VIEW ANY COLUMN ENCRYPTION KEY DEFINITION</code>|  
|--------------|-----------------------------------|---------------------------------------|---------------------------------------------|-------------------------------------------------|  
|金鑰管理 (在資料庫中建立/變更/檢閱金鑰中繼資料)|X|X|X|X|  
|查詢加密資料行|||X|X|  
  
 **重要事項：**  
  
-   您可使用 [!INCLUDE[tsql](../../../includes/tsql-md.md)]、 [!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)] (對話方塊和精靈) 或 PowerShell 將權限套用至動作。  
  
-   選取加密的資料行時需具備這兩種「檢視」  權限 (即使使用者沒有解密資料行的權限亦同)。  
  
-   [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]預設會將這兩個「檢視」  權限授與 `public` 固定資料庫角色。 資料庫管理員可以選擇撤銷 (或拒絕) 授與 *角色的「檢視」* `public` 權限，而將其授與特定角色或使用者，以實作更嚴格的控制。  
  
-   在 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 中，預設不會將「檢視」  權限授與 `public` 固定資料庫角色。 這可讓某些現有的舊版工具 (使用舊版 DacFx) 正常運作。 因此，若要使用加密的資料行 (即使不要加以解密)，資料庫管理員必須明確授與這兩個「檢視」  權限。  

  
## <a name="example"></a>範例  
 下列 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 會建立資料行主要金鑰中繼資料、資料行加密金鑰中繼資料以及含加密資料行的資料表。 如需如何建立中繼資料參考金鑰的資訊，請參閱︰
- [使用 SSMS 設定 Always Encrypted](../../../relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio.md)
- [使用 PowerShell 設定 Always Encrypted](../../../relational-databases/security/encryption/configure-always-encrypted-using-powershell.md)

  
```sql  
CREATE COLUMN MASTER KEY MyCMK  
WITH (  
     KEY_STORE_PROVIDER_NAME = 'MSSQL_CERTIFICATE_STORE',   
     KEY_PATH = 'Current User/Personal/f2260f28d909d21c642a3d8e0b45a830e79a1420'  
   );  
---------------------------------------------  
CREATE COLUMN ENCRYPTION KEY MyCEK   
WITH VALUES  
(  
    COLUMN_MASTER_KEY = MyCMK,   
    ALGORITHM = 'RSA_OAEP',   
    ENCRYPTED_VALUE = 0x01700000016C006F00630061006C006D0061006300680069006E0065002F006D0079002F003200660061006600640038003100320031003400340034006500620031006100320065003000360039003300340038006100350064003400300032003300380065006600620063006300610031006300284FC4316518CF3328A6D9304F65DD2CE387B79D95D077B4156E9ED8683FC0E09FA848275C685373228762B02DF2522AFF6D661782607B4A2275F2F922A5324B392C9D498E4ECFC61B79F0553EE8FB2E5A8635C4DBC0224D5A7F1B136C182DCDE32A00451F1A7AC6B4492067FD0FAC7D3D6F4AB7FC0E86614455DBB2AB37013E0A5B8B5089B180CA36D8B06CDB15E95A7D06E25AACB645D42C85B0B7EA2962BD3080B9A7CDB805C6279FE7DD6941E7EA4C2139E0D4101D8D7891076E70D433A214E82D9030CF1F40C503103075DEEB3D64537D15D244F503C2750CF940B71967F51095BFA51A85D2F764C78704CAB6F015EA87753355367C5C9F66E465C0C66BADEDFDF76FB7E5C21A0D89A2FCCA8595471F8918B1387E055FA0B816E74201CD5C50129D29C015895CD073925B6EA87CAF4A4FAF018C06A3856F5DFB724F42807543F777D82B809232B465D983E6F19DFB572BEA7B61C50154605452A891190FB5A0C4E464862CF5EFAD5E7D91F7D65AA1A78F688E69A1EB098AB42E95C674E234173CD7E0925541AD5AE7CED9A3D12FDFE6EB8EA4F8AAD2629D4F5A18BA3DDCC9CF7F352A892D4BEBDC4A1303F9C683DACD51A237E34B045EBE579A381E26B40DCFBF49EFFA6F65D17F37C6DBA54AA99A65D5573D4EB5BA038E024910A4D36B79A1D4E3C70349DADFF08FD8B4DEE77FDB57F01CB276ED5E676F1EC973154F86  
);  
---------------------------------------------  
CREATE TABLE Customers (  
    CustName nvarchar(60)   
        COLLATE  Latin1_General_BIN2 ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = MyCEK,  
        ENCRYPTION_TYPE = RANDOMIZED,  
        ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256'),   
    SSN varchar(11)   
        COLLATE  Latin1_General_BIN2 ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = MyCEK,  
        ENCRYPTION_TYPE = DETERMINISTIC ,  
        ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256'),   
    Age int NULL  
);  
GO  
  
```  
## <a name="see-also"></a>另請參閱  
- [使用 SSMS 設定 Always Encrypted](../../../relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio.md)   
- [使用 PowerShell 設定 Always Encrypted](../../../relational-databases/security/encryption/configure-always-encrypted-using-powershell.md)   
- [使用 Always Encrypted 開發應用程式](always-encrypted-client-development.md) 
- [使用 [Always Encrypted 精靈] 設定資料行加密](always-encrypted-wizard.md)
- [Always Encrypted 密碼編譯](../../../relational-databases/security/encryption/always-encrypted-cryptography.md)   
- [CREATE COLUMN MASTER KEY &#40;Transact-SQL&#41;](../../../t-sql/statements/create-column-master-key-transact-sql.md)   
- [CREATE COLUMN ENCRYPTION KEY &#40;Transact-SQL&#41;](../../../t-sql/statements/create-column-encryption-key-transact-sql.md)   
- [CREATE TABLE &#40;Transact-SQL&#41;](../../../t-sql/statements/create-table-transact-sql.md)   
- [column_definition &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-table-column-definition-transact-sql.md)   
- [sys.column_encryption_keys  &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-column-encryption-keys-transact-sql.md)  
- [sys.column_encryption_key_values &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-column-encryption-key-values-transact-sql.md)   
- [sys.column_master_keys &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-column-master-keys-transact-sql.md)   
- [sys.columns &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/sys-columns-transact-sql.md)   
- [sp_refresh_parameter_encryption &#40;Transact-SQL&#41;](../../../relational-databases/system-stored-procedures/sp-refresh-parameter-encryption-transact-sql.md)   
  
  
