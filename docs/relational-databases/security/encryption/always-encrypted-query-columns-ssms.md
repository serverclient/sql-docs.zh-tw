---
title: 使用 Always Encrypted 與 SQL Server Management Studio 查詢資料行 | Microsoft Docs
description: 了解如何使用 SQL Server Management Studio 在 Always Encrypted 中查詢資料行。 擷取加密資料行中儲存的加密文字或文字值。
ms.custom: ''
ms.date: 01/15/2021
ms.prod: sql
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- Always Encrypted, configure with SSMS
ms.assetid: 29816a41-f105-4414-8be1-070675d62e84
author: jaszymas
ms.author: jaszymas
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 4bdf43c821bd278e6080e6f9244f3bb169388653
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596247"
---
# <a name="query-columns-using-always-encrypted-with-sql-server-management-studio"></a>使用 Always Encrypted 與 SQL Server Management Studio 查詢資料行
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]

本文描述如何使用 [SQL Server Management Studio (SSMS)](../../../ssms/download-sql-server-management-studio-ssms.md) 來查詢以 [Always Encrypted](../../../relational-databases/security/encryption/always-encrypted-database-engine.md) 加密的資料行。 您可以使用 SSMS：
- 擷取加密資料行中儲存的加密文字值。 
- 擷取加密資料行中儲存的純文字值。   
- 傳送目標為加密資料行的純文字值 (例如，在 `INSERT` 或 `UPDATE` 陳述式中，以及在 `SELECT` 陳述式中作為 `WHERE` 子句的查閱參數)。

## <a name="retrieving-ciphertext-values-stored-in-encrypted-columns"></a>擷取加密資料行中儲存的加密文字值    
執行 SELECT 查詢來擷取加密資料行中儲存的資料加密文字 (無須解密資料)，並不要求您必須能夠存取保護資料的資料行主要金鑰。 若要在 SSMS 中擷取加密資料行中的加密文字值：

1. 確定您可以存取保護要執行查詢之資料行的金鑰相關中繼資料。 雖然您不需要能夠存取實際資料行主要金鑰，但您需要資料庫層級權限，才能檢視資料庫中的資料行主要金鑰和資料行加密金鑰中繼資料物件。 如需詳細資料，請參閱以下[查詢加密資料行的權限](#permissions-for-querying-encrypted-columns)。
1. 確定您已針對 [查詢編輯器] 視窗的資料庫連接停用 Always Encrypted，您將從該視窗執行 `SELECT` 查詢來擷取加密文字值。 請參閱以下的[針對資料庫連接啟用和停用 Always Encrypted](#en-dis)。     
1. 執行您的 `SELECT` 查詢。 從加密資料行擷取的任何資料都將當做二進位 (加密) 值來傳回。   

### <a name="example"></a>範例
假設 `SSN` 是 `Patients` 資料表中的加密資料行，如果已針對資料庫連接停用 Always Encrypted，以下所示的查詢將擷取二進位的加密文字值。   

![SELECT [SSN] FROM [dbo].[Patients] 查詢，以及該查詢結果顯示為二進位加密文字值的螢幕擷取畫面。](../../../relational-databases/security/encryption/media/always-encrypted-ciphertext.png)
 
## <a name="retrieving-plaintext-values-stored-in-encrypted-columns"></a>擷取加密資料行中儲存的純文字值    
擷取加密資料行的值做為純文字 (以便將值解密)：   
1. 確定您可以存取保護要執行查詢之資料行的資料行主要金鑰和金鑰相關中繼資料。 如需詳細資料，請參閱以下[查詢加密資料行的權限](#permissions-for-querying-encrypted-columns)。
1.  確定您已針對 [查詢編輯器] 視窗的資料庫連接啟用 Always Encrypted，您將從該視窗執行 `SELECT` 查詢來擷取資料並進行解密。 這會指示 .NET Framework Data Provider for SQL Server (由 SSMS 所使用)，將查詢結果集中的加密資料行解密。 請參閱以下的[針對資料庫連接啟用和停用 Always Encrypted](#en-dis)。
1.  執行您的 `SELECT` 查詢。 從加密資料行所擷取任何資料將會以原始資料類型的純文字值傳回。
 
### <a name="example"></a>範例
假設 SSN 是 `char(11)` 資料表中加密的 `Patients` 資料行，如果已針對資料庫連接啟用 Always Encrypted，而且您有權存取針對 `SSN` 資料行設定的資料行主要金鑰，則以下所示的查詢將傳回純文字值。   

![SELECT [SSN] FROM [Clinic].[dbo].[Patients] 查詢，以及該查詢結果顯示為純文字值的螢幕擷取畫面。](../../../relational-databases/security/encryption/media/always-encrypted-plaintext.png)
 
## <a name="sending-plaintext-values-targeting-encrypted-columns"></a>傳送目標為加密資料行的純文字值       
執行查詢以傳送目標為加密資料行的值，例如，利用儲存於加密資料行中的值來插入、更新或據以篩選的查詢：

1. 確定您可以存取保護要執行查詢之資料行的資料行主要金鑰和金鑰中繼資料。 如需詳細資訊，請參閱以下的[查詢加密資料行的權限](#permissions-for-querying-encrypted-columns)。
1.  確定您已針對 [查詢編輯器] 視窗的資料庫連接啟用 Always Encrypted，您將從該視窗執行 `SELECT` 查詢來擷取資料並進行解密。 這會指示 .NET Framework Data Provider for SQL Server (由 SSMS 所使用)，將查詢結果集中的加密資料行解密。 請參閱以下的[針對資料庫連接啟用和停用 Always Encrypted](#en-dis)。
1. 確定已針對 [查詢編輯器] 視窗啟用 [Always Encrypted 的參數化]。 (至少需要 SSMS 17.0 版。)宣告 Transact-SQL 變數，並使用您想要傳送 (插入、更新或據以篩選) 至資料庫的值來將它初始化。 如需詳細資訊，請參閱下方的 [Always Encrypted 的參數化](#param) 。

1. 執行查詢以將 Transact-SQL 變數的值傳送至資料庫。 SSMS 會將變數轉換為查詢參數，而它會在將值傳送至資料庫之前，先進行加密。   

### <a name="example"></a>範例
假設 `SSN` 是 `char(11)` 資料表中加密的 `Patients` 資料行，以下指令碼將嘗試在 SSN 資料行中尋找包含 `'795-73-9838'` 的資料列，並傳回 `LastName` 資料行的值，但前提是已針對資料庫連線啟用 Always Encrypted、已針對 [查詢編輯器] 視窗啟用 [Always Encrypted 的參數化]，而且您有權存取針對 `SSN` 資料行設定的資料行主要金鑰。   

![DECLARE @SSN CHAR(11) = '795-73-9838' SELECT [LastName] FROM [dbo].[Patients] WHERE [SSN] = @SSN 查詢，以及該查詢結果的螢幕擷取畫面。](../../../relational-databases/security/encryption/media/always-encrypted-patients.png)

## <a name="permissions-for-querying-encrypted-columns"></a>查詢加密資料行的權限

若要針對加密資料行 (包括以加密文字擷取資料的查詢) 執行任何查詢，您需要資料庫中的 `VIEW ANY COLUMN MASTER KEY DEFINITION` 和 `VIEW ANY COLUMN ENCRYPTION KEY DEFINITION` 權限。

除了上述權限，若要將任何查詢結果解密或加密任何查詢參數 (透過參數化 Transact-SQL 變數來產生)，您還需要權限來存取保護目標資料行的資料行主要金鑰：

- **憑證存放區 - 本機電腦** 您必須具有當成資料行主要金鑰使用之憑證的 `Read` 權限，或為電腦上的系統管理員。   
- **Azure Key Vault**：您需要包含資料行主要金鑰的保存庫 `get`、`unwrapKey` 和 `verify` 權限。
- **金鑰存放區提供者 (KSP)** ：必要權限和認證 (您在使用金鑰存放區或金鑰時可能收到提示) 取決於存放區和 KSP 設定。   
- **密碼編譯服務提供者 (CSP)** ：必要權限和認證 (您在使用金鑰存放區或金鑰時可能會收到提示) 取決於存放區和 CSP 設定。

如需詳細資訊，請參閱 [建立及儲存資料行主要金鑰 (永遠加密)](../../../relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted.md)。

## <a name="enabling-and-disabling-always-encrypted-for-a-database-connection"></a><a name="en-dis"></a> 針對資料庫連線啟用和停用 Always Encrypted   
當您在 SSMS 中連接到資料庫時，可以針對資料庫連接啟用或停用 Always Encrypted。 預設會停用 Always Encrypted。 

針對資料庫連線啟用和停用 Always Encrypted，會指示 .NET Framework Data Provider for SQL Server (SQL Server Management Studio 所使用) 明確地嘗試進行下列動作：   
-   將從加密資料行擷取以及查詢結果中傳回的任何值解密。   
-   將目標為加密資料庫資料行且已參數化的 Transact-SQL 變數的值加密。   

如果您未針對連接啟用 Always Encrypted，則 SSMS 所使用的 .NET Framework Data Provider for SQL Server 不會嘗試加密查詢參數或將結果解密。

您可以在建立新連接或變更現有的連接時，使用 [連接到伺服器] 對話方塊來啟用或停用 Always Encrypted。 

若要啟用 (停用) Always Encrypted：
1. 開啟 [連接到伺服器] 對話方塊 (如需詳細資料，請參閱[連接到 SQL Server 執行個體](../../../ssms/quickstarts/ssms-connect-query-sql-server.md#connect-to-a-sql-server-instance))。
1. 按一下 [選項 >>]。
1. 如果您使用 SSMS 18 或更新版本：
    1. 選取 [Always Encrypted] 索引標籤。
    1. 若要啟用 Always Encrypted，請選取 [啟用 Always Encrypted (資料行加密)]。 若要停用 Always Encrypted，請確定未選取 [啟用 Always Encrypted (資料行加密)]。
1. 如果您使用 SSMS 17 或更舊版本：
    1. 選取 [其他屬性] 索引標籤。
    1. 若要啟用 Always Encrypted，請鍵入 `Column Encryption Setting = Enabled`。 若要停用 Always Encrypted，請指定 `Column Encryption Setting = Disabled`，或從 [其他屬性] 索引標籤中移除 [資料行加密設定] 的設定 (預設值為 [停用])。   
 1. 按一下 [ **連接**]。

若要在搭配 [安全記憶體保護區使用 Always Encrypted](always-encrypted-enclaves.md)時，執行利用伺服器端安全記憶體保護區的語句，除了啟用連接的 Always Encrypted 之外，還需要指定記憶體保護區證明 URL。 如需詳細資訊，請參閱  [在 SSMS 中使用記憶體保護區執行 t-sql 語句的必要條件](always-encrypted-enclaves-query-columns.md#prerequisites-for-running-t-sql-statements-using-enclaves-in-ssms)。

> [!TIP]
> 在已針對現有的 [查詢編輯器] 視窗啟用和停用的 Always Encrypted 之間進行切換：   
> 1.    以滑鼠右鍵按一下 [查詢編輯器] 視窗中的任何位置。
> 2.    選取 [連接] > [變更連接...]。這會針對 [查詢編輯器] 視窗的目前連接開啟 [連接到伺服器] 對話方塊。 
> 2.    啟用或停用 Always Encrypted，然後遵循上述步驟並按一下 [連接]。  
   
## <a name="parameterization-for-always-encrypted"></a><a name="param"></a>Always Encrypted 的參數化   
 
[Always Encrypted 的參數化] 是 SQL Server Management Studio 中的一項功能，可將 Transact-SQL 變數自動轉換為查詢參數 ( [SqlParameter 類別](/dotnet/api/system.data.sqlclient.sqlparameter)的執行個體)。 (至少需要 SSMS 17.0 版。)這讓基礎的 .NET Framework Data Provider for SQL Server 能夠偵測目標為加密資料行的資料，並且在將這類資料傳送至資料庫之前，先進行加密。 
  
未啟用參數化時，.NET Framework Data Provider 會以非參數化的查詢傳遞您在 [查詢編輯器] 中撰寫的每一個陳述式。 如果查詢包含目標為加密資料行的常值或 Transact-SQL 變數，.NET Framework Data Provider for SQL Server 就無法在將查詢傳送至資料庫之前先偵測並加密它們。 如此一來，查詢將因 (純文字的常值 Transact-SQL 變數與加密資料行之間) 類型不符而導致失敗。 例如，假設 `SSN` 資料行已加密，下列查詢將因未啟用參數化而失敗。   

```sql
DECLARE @SSN NCHAR(11) = '795-73-9838'
SELECT * FROM [dbo].[Patients]
WHERE [SSN] = @SSN
```

### <a name="enabling-and-disabling-parameterization-for-always-encrypted"></a>啟用和停用 Always Encrypted 的參數化

預設為停用 [Always Encrypted 的參數化]。

針對目前的 [查詢編輯器] 視窗啟用/停用 [Always Encrypted 的參數化]：

1. 從主功能表選取 [查詢]  。
2. 選取 [查詢選項]。
3. 瀏覽至 [執行] > [進階]。
4. 選取或取消選取 [啟用 Always Encrypted 的參數化] 。
5. 按一下 [確定]。

針對未來的 [查詢編輯器] 視窗啟用/停用 [Always Encrypted 的參數化]：

1. 從主功能表選取 [工具]  。
2. 選取 [選項]。
3. 瀏覽至 [查詢執行] > [SQL Server] > [進階]。
4. 選取或取消選取 [啟用 Always Encrypted 的參數化] 。
5. 按一下 [確定]。

如果您執行查詢的 [查詢編輯器] 視窗使用已啟用 Always Encrypted 的資料庫連接，但尚未針對 [查詢編輯器] 視窗啟用參數化，則系統會提示您啟用它。

> [!NOTE]
> [Always Encrypted 的參數化] 僅適用於使用已啟用 Always Encrypted 的資料庫連接 [查詢編輯器] 視窗 (請參閱[啟用和停用 Always Encrypted 的參數化](#enabling-and-disabling-parameterization-for-always-encrypted))。 如果 [查詢編輯器] 視窗使用尚未啟用 Always Encrypted 的資料庫連接，則不會將任何 Transact-SQL 變數參數化。

### <a name="how-parameterization-for-always-encrypted-works"></a>Always Encrypted 的參數化的運作方式

如果已針對 [查詢編輯器] 視窗啟用 [Always Encrypted 的參數化] 和資料庫連接中的 Always Encrypted 行為，則 SQL Server Management Studio 會嘗試將 Transact-SQL 變數參數化，以符合下列必要條件的情況：

- 在相同陳述式中宣告及初始化 (內嵌初始化)。 使用個別 `SET` 陳述式宣告的變數將不會參數化。
- 使用單一常值初始化。 使用運算式 (包含任何運算子或函數) 初始化的變數將不會參數化。

以下是 SQL Server Management Studio 將參數化的變數範例。

```sql
DECLARE @SSN char(11) = '795-73-9838';
   
DECLARE @BirthDate date = '19990104';
DECLARE @Salary money = $30000;
```

此外，以下是 SQL Server Management Studio 不會嘗試參數化的一些變數範例：

```sql
DECLARE @Name nvarchar(50); --Initialization separate from declaration
SET @Name = 'Abel';

DECLARE @StartDate date = GETDATE(); -- a function used instead of a literal

DECLARE @NewSalary money = @Salary * 1.1; -- an expression used instead of a literal
```
 
若要成功嘗試進行參數化：   
- 針對要參數化之變數初始化所使用的常值類型必須符合變數宣告中的類型。   
- 如果變數的宣告類型是日期類型或時間類型，就必須使用字串，以下列其中一個遵循 [ISO 8601 規範的格式](../../../t-sql/functions/cast-and-convert-transact-sql.md#date-and-time-styles)來初始化變數。    

以下是將產生參數化錯誤的 Transact-SQL 變數宣告範例：   
```sql
DECLARE @BirthDate date = '01/04/1999' -- unsupported date format   
   
DECLARE @Number int = 1.1 -- the type of the literal does not match the type of the variable   
```
SQL Server Management Studio 會使用 Intellisense，來告知您哪些變數可以成功參數化，以及哪些參數化嘗試失敗 (與原因)。   

在 [查詢編輯器] 中，會以警告底線標示可成功參數化的變數宣告。 如果您將滑鼠停留在以警告底線標示的宣告陳述式上，您會看到參數化程序的結果，包括所產生 [SqlParameter](/dotnet/api/system.data.sqlclient.sqlparameter) 物件 (變數要與其對應) 的主要屬性值：[SqlDbType](/dotnet/api/system.data.sqlclient.sqlparameter.sqldbtype)、[Size](/dotnet/api/system.data.sqlclient.sqlparameter.size)、[Precision](/dotnet/api/system.data.sqlclient.sqlparameter.precision)、[Scale](/dotnet/api/system.data.sqlclient.sqlparameter.scale)、[SqlValue](/dotnet/api/system.data.sqlclient.sqlparameter.sqlvalue)。 您也可以在 [錯誤清單]  檢視的 [警告]  索引標籤中，查看已成功地參數化的所有變數的完整清單。 若要開啟 [錯誤清單]  檢視，從主功能表選取 [檢視]  ，然後選取 [錯誤清單] 。    

如果 SQL Server Management Studio 已嘗試將變數參數化，但參數化失敗，則將會以錯誤底線標示變數的宣告。 如果您將滑鼠停留在已使用錯誤底線標示的宣告陳述式上，您會取得有關錯誤的結果。 您也可以在 [錯誤清單]  檢視的 [錯誤]  索引標籤中，查看所有變數的參數化錯誤的完整清單。 若要開啟 [錯誤清單]  檢視，從主功能表選取 [檢視]  ，然後選取 [錯誤清單] 。   

以下螢幕擷取畫面顯示六個變數宣告的範例。 SQL Server Management Studio 已成功將前三個變數參數化。 最後三個變數不符合參數化的必要條件情況，因此，SQL Server Management Studio 未嘗試將它們參數化 (未以任何方式標示它們的宣告)。

![顯示六個變數宣告範例的螢幕擷取畫面，包含三個成功參數化的宣告、三個失敗的宣告，以及相關聯的警告訊息。](../../../relational-databases/security/encryption/media/always-encrypted-parameter-warnings.png)
 
以下另一個示範顯示了兩個參數，其符合參數化的必要條件情況，但參數化嘗試因為變數未正確初始化而失敗。    
 
![顯示兩個變數宣告範例的螢幕擷取畫面，宣告最終失敗，並包含相關聯的錯誤訊息。](../../../relational-databases/security/encryption/media/always-encrypted-error.png)
 
> [!NOTE]
> 由於 Always Encrypted 支援一組有限的類型轉換子集，因此，在許多情況下，會要求 Transact-SQL 變數的資料類型與其設定為目標的目標資料庫資料行的類型相同。 例如，假設 `SSN` 資料表中 `Patients` 資料行的類型是 `char(11)`，以下查詢將會失敗，因為 `@SSN` 變數的類型為 `nchar(11)`，不符合資料行的類型。   

```sql
DECLARE @SSN nchar(11) = '795-73-9838'
SELECT * FROM [dbo].[Patients]
WHERE [SSN] = @SSN;
```

```output
Msg 402, Level 16, State 2, Line 5   
The data types char(11) encrypted with (encryption_type = 'DETERMINISTIC', 
encryption_algorithm_name = 'AEAD_AES_256_CBC_HMAC_SHA_256', column_encryption_key_name = 'CEK_Auto1', 
column_encryption_key_database_name = 'Clinic') collation_name = 'Latin1_General_BIN2' 
and nchar(11) encrypted with (encryption_type = 'DETERMINISTIC', 
encryption_algorithm_name = 'AEAD_AES_256_CBC_HMAC_SHA_256', column_encryption_key_name = 'CEK_Auto1', 
column_encryption_key_database_name = 'Clinic') are incompatible in the equal to operator.
```

> [!NOTE]
> 未啟用參數化時，整個查詢 (包括類型轉換) 會在 SQL Server/Azure SQL Database 內部進行處理。 啟用參數化時，.NET Framework 會在 SQL Server Management Studio 內部執行一些類型轉換。 由於 .NET Framework 類型系統與 SQL Server 類型系統之間的差異 (例如某些類型 (例如浮點數) 的有效位數不同)，因此，已啟用參數化執行的查詢可以產生不同於未啟用參數化執行的查詢的結果。 

## <a name="next-steps"></a>後續步驟
- [使用 Always Encrypted 開發應用程式](always-encrypted-client-development.md)


## <a name="see-also"></a>另請參閱
- [一律加密](../../../relational-databases/security/encryption/always-encrypted-database-engine.md)