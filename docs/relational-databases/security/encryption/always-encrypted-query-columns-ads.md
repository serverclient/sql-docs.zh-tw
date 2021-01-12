---
description: 搭配 Azure Data Studio 使用 Always Encrypted 查詢資料行
title: 搭配 Azure Data Studio 使用 Always Encrypted 查詢資料行 | Microsoft Docs
ms.custom: ''
ms.date: 5/19/2020
ms.prod: sql
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 0a61514ff76bbae9e403683529d017f6fcb1c079
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101846"
---
# <a name="query-columns-using-always-encrypted-with-azure-data-studio"></a>搭配 Azure Data Studio 使用 Always Encrypted 查詢資料行
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]

本文描述如何使用 [Azure Data Studio](../../../azure-data-studio/what-is-azure-data-studio.md) 來查詢以 [Always Encrypted](../../../relational-databases/security/encryption/always-encrypted-database-engine.md) 加密的資料行。 您可以使用 Azure Data Studio 進行：
- 擷取加密資料行中儲存的加密文字值。 
- 擷取加密資料行中儲存的純文字值。  
- 傳送目標為加密資料行的純文字值 (例如，在 `INSERT` 或 `UPDATE` 陳述式中，以及在 `SELECT` 陳述式中作為 `WHERE` 子句的查閱參數)。 

## <a name="retrieving-ciphertext-values-stored-in-encrypted-columns"></a>擷取加密資料行中儲存的加密文字值    
此節描述如何將加密資料行中所儲存的資料，當做加密文字來擷取。

### <a name="steps"></a>步驟
1. 確定您已針對查詢視窗的資料庫連接停用 Always Encrypted，您將從該視窗執行 `SELECT` 查詢來擷取加密文字值。 請參閱以下的[針對資料庫連接啟用和停用 Always Encrypted](#enabling-and-disabling-always-encrypted-for-a-database-connection)。     
1. 執行您的 `SELECT` 查詢。 從加密資料行擷取的任何資料都將當做二進位 (加密) 值來傳回。   

### <a name="example"></a>範例
假設 `SSN` 是 `Patients` 資料表中的加密資料行，如果已針對資料庫連接停用 Always Encrypted，以下所示的查詢將擷取二進位的加密文字值。   

![SELECT * FROM [dbo].[Patients] 查詢，以及該查詢結果顯示為二進位加密文字值的螢幕擷取畫面。](../../../relational-databases/security/encryption/media/always-encrypted-ads-query-ciphertext.png)
 
## <a name="retrieving-plaintext-values-stored-in-encrypted-columns"></a>擷取加密資料行中儲存的純文字值    
此節描述如何將加密資料行中所儲存的資料，當做加密文字來擷取。

### <a name="prerequisites"></a>必要條件
- Azure Data Studio 17.1 版或更新版本。
- 您必須要有保護所要查詢資料行的資料行主要金鑰和金鑰相關中繼資料的存取權。 如需詳細資料，請參閱以下[查詢加密資料行的權限](#permissions-for-querying-encrypted-columns)。
- 您的資料行主要金鑰必須儲存在 Azure Key Vault 或 Windows 憑證存放區中。 Azure Data Studio 不支援其他金鑰存放區。

### <a name="steps"></a>步驟
1.  為查詢視窗的資料庫連接啟用 Always Encrypted，您將會從該視窗執行 `SELECT` 查詢來擷取資料並進行解密。 這會指示 [Microsoft .NET Data Provider for SQL Server](../../../connect/ado-net/sql/sqlclient-support-always-encrypted.md) (由 Azure Data Studio 所使用)，將查詢結果集中的加密資料行解密。 請參閱以下的[針對資料庫連接啟用和停用 Always Encrypted](#enabling-and-disabling-always-encrypted-for-a-database-connection)。
1.  執行您的 `SELECT` 查詢。 從加密資料行所擷取任何資料將會以原始資料類型的純文字值傳回。
 
### <a name="example"></a>範例
假設 SSN 是 `Patients` 資料表中的加密資料行，如果已針對資料庫連接啟用 Always Encrypted，而且您有權存取針對 `SSN` 資料行設定的資料行主要金鑰，則以下所示的查詢將傳回純文字值。   

![SELECT * FROM [dbo].[Patients] 查詢，以及該查詢結果顯示為純文字值的螢幕擷取畫面。](../../../relational-databases/security/encryption/media/always-encrypted-ads-query-plaintext.png)
 
## <a name="sending-plaintext-values-targeting-encrypted-columns"></a>傳送目標為加密資料行的純文字值       
此節描述如何執行查詢，其會傳送以加密資料行為目標的值。 例如，依儲存於加密資料行中的值來插入、更新或篩選的查詢：

### <a name="prerequisites"></a>必要條件
- Azure Data Studio 18.1 版或更新版本。
- 您必須要有保護所要查詢資料行的資料行主要金鑰和金鑰相關中繼資料的存取權。 如需詳細資料，請參閱以下[查詢加密資料行的權限](#permissions-for-querying-encrypted-columns)。
- 您的資料行主要金鑰必須儲存在 Azure Key Vault 或 Windows 憑證存放區中。 Azure Data Studio 不支援其他金鑰存放區。

### <a name="steps"></a>步驟
1. 為查詢視窗的資料庫連接啟用 Always Encrypted，您將會從該視窗執行 `SELECT` 查詢來擷取資料並進行解密。 這會指示 [Microsoft .NET Data Provider for SQL Server](../../../connect/ado-net/sql/sqlclient-support-always-encrypted.md)(由 Azure Data Studio 使用) 將以加密資料行為目標的查詢參數加密，並將從加密資料行中擷取的結果解密。 請參閱以下的[針對資料庫連接啟用和停用 Always Encrypted](#enabling-and-disabling-always-encrypted-for-a-database-connection)。 
1. 為查詢視窗啟用 Always Encrypted 的參數化。 如需詳細資訊，請參閱下方的 [Always Encrypted 的參數化](#parameterization-for-always-encrypted) 。
1. 宣告 Transact-SQL 變數，並使用您想要傳送 (據以插入、更新或篩選) 至資料庫的值加以初始化。 
1. 執行查詢以將 Transact-SQL 變數的值傳送至資料庫。 Azure Data Studio 會將變數轉換為查詢參數，而其會在將值傳送至資料庫之前，先進行加密。   

### <a name="example"></a>範例
假設 `SSN` 是 `Patients` 資料表中的加密 `char(11)` 資料行，下列指令碼會嘗試在 SSN 資料行中尋找包含 `'795-73-9838'` 的資料列。 如果已針對資料庫連接啟用 Always Encrypted、已針對查詢視窗啟用 Always Encrypted 的參數化，而且您可以存取為 `SSN` 資料行設定的資料行主要金鑰，則會傳回結果。   

![DECLARE @SSN char(11) = '795-73-9838' SELECT * FROM [dbo].[Patients] WHERE [SSN] = @SSN 查詢，以及該查詢結果的螢幕擷取畫面。](../../../relational-databases/security/encryption/media/always-encrypted-ads-query-parameters.png)

## <a name="permissions-for-querying-encrypted-columns"></a>查詢加密資料行的權限

若要對加密資料行執行任何查詢 (包括以加密文字擷取資料的查詢)，您需要資料庫中的 **檢視任何資料行的主要金鑰定義** 和 **檢視任何資料行的加密金鑰定義** 權限。

除了上述權限，若要將任何查詢結果解密或加密任何查詢參數 (透過參數化 Transact-SQL 變數來產生)，您還需要權限來存取保護目標資料行的資料行主要金鑰：

- **憑證存放區 - 本機電腦：** 您必須有憑證 (當成資料行主要金鑰使用) 的 **讀取** 權，或為電腦上的系統管理員。   
- **Azure Key Vault：** 您需要有包含資料行主要金鑰的金鑰保存庫 **get**、**unwrapKey** 和 **verify** 權限。

如需詳細資訊，請參閱 [建立及儲存資料行主要金鑰 (永遠加密)](../../../relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted.md)。

## <a name="enabling-and-disabling-always-encrypted-for-a-database-connection"></a>針對資料庫連接啟用和停用 Always Encrypted   
當您在 Azure Data Studio 中連線到資料庫時，可以針對資料庫連接啟用或停用 Always Encrypted。 預設會停用 Always Encrypted。 

針對資料庫連接啟用和停用 Always Encrypted，會指示 [Microsoft .NET Data Provider for SQL Server](../../../connect/ado-net/sql/sqlclient-support-always-encrypted.md) (由 Azure Data Studio 所使用) 在背景嘗試進行下列動作：   
-   將從加密資料行擷取以及查詢結果中傳回的任何值解密。   
-   將目標為加密資料庫資料行且已參數化的 Transact-SQL 變數的值加密。   

如果您未針對連接啟用 Always Encrypted，則 Microsoft .NET Data Provider for SQL Server 不會嘗試加密查詢參數或將結果解密。

當您連線到資料庫時，可以啟用或停用 Always Encrypted。 如需如何連線至資料庫伺服器的一般資訊，請參閱：
- [快速入門：使用 Azure Data Studio 連線及查詢 SQL Server](../../../azure-data-studio/quickstart-sql-server.md)
- [快速入門：使用 Azure Data Studio 連線及查詢 Azure SQL 資料庫](../../../azure-data-studio/quickstart-sql-database.md)

若要啟用 (停用) Always Encrypted：
1. 在 [連線] 對話方塊中，按一下 [進階]。
2. 若要針對連線啟用 Always Encrypted，請將 [Always Encrypted] 欄位設定為 [啟用]。 若要停用 Always Encrypted，請將 [Always Encrypted] 欄位的值保留空白，或將其設定為 [停用]。
3. 如果您使用 [!INCLUDE [sssqlv15-md](../../../includes/sssqlv15-md.md)]，且您的 SQL Server 執行個體已設定安全記憶體保護區，您可以指定記憶體保護區通訊協定和記憶體保護區證明 URL。 如果您的 SQL Server 執行個體未使用安全記憶體保護區，請確定將 [證明通訊協定] 和 [記憶體保護區證明 URL] 欄位留白。 如需詳細資訊，請參閱[具有安全記憶體保護區的 Always Encrypted](always-encrypted-enclaves.md)。
4. 按一下 [確定]，以關閉 [進階屬性]。

![說明如何針對連線啟用 Always Encrypted 步驟的短片。](../../../relational-databases/security/encryption/media/always-encrypted-ads-connect.gif)

> [!TIP]
> 若要在現有查詢視窗中，於啟用和停用 Always Encrypted 之間切換，請按一下 [中斷連線]，然後按一下 [連線] 並完成上述步驟，以使用 [Always Encrypted] 欄位的所需值重新連線到您的資料庫。 

> [!NOTE] 
> 查詢視窗中的 [變更連線] 按鈕目前不支援在啟用和停用 Always Encrypted 之間切換。

## <a name="parameterization-for-always-encrypted"></a>Always Encrypted 的參數化

[Always Encrypted 的參數化] 是 Azure Data Studio 18.1 和更新版本中的一個功能，可將 Transact-SQL 變數自動轉換為查詢參數 ([SqlParameter 類別](/dotnet/api/microsoft.data.sqlclient.sqlparameter)的執行個體)。 這讓底層的 [Microsoft .NET Data Provider for SQL Server](../../../connect/ado-net/sql/sqlclient-support-always-encrypted.md) 能夠偵測目標為加密資料行的資料，並且在將此類資料傳送至資料庫之前，先進行加密。
  
未啟用參數化時，Microsoft .NET Data Provider for SQL Server 會以非參數化的查詢傳遞您在查詢視窗中撰寫的每一個陳述式。 如果查詢包含目標為加密資料行的常值或 Transact-SQL 變數，.NET Framework Data Provider for SQL Server 就無法在將查詢傳送至資料庫之前先加以偵測並加密。 如此一來，查詢將因 (純文字的常值 Transact-SQL 變數與加密資料行之間) 類型不符而導致失敗。 例如，假設 `SSN` 資料行已加密，下列查詢將因未啟用參數化而失敗。   

```sql
DECLARE @SSN CHAR(11) = '795-73-9838'
SELECT * FROM [dbo].[Patients]
WHERE [SSN] = @SSN
```

### <a name="enabling-and-disabling-parameterization-for-always-encrypted"></a>啟用和停用 Always Encrypted 的參數化

預設為停用 [Always Encrypted 的參數化]。

啟用/停用 Always Encrypted 的參數化：

1. 選取 [檔案] > [喜好設定] > [設定] (在 Mac 上為 [Code] > [偏好設定] > [設定])。
2. 瀏覽到 [資料] > [Microsoft SQL Server]。
3. 選取或取消選取 [啟用 Always Encrypted 的參數化] 。
4. 關閉 [設定] 視窗。

![說明如何啟用/停用 Always Encrypted 參數化的短片。](../../../relational-databases/security/encryption/media/always-encrypted-ads-parameterization.gif)

> [!NOTE]
> [Always Encrypted 的參數化] 僅適用於使用已啟用 Always Encrypted 資料庫連接的查詢視窗 (請參閱[針對資料庫連接啟用和停用 Always Encrypted](#enabling-and-disabling-always-encrypted-for-a-database-connection))。 如果查詢視窗使用尚未啟用 Always Encrypted 的資料庫連接，則不會將任何 Transact-SQL 變數參數化。

### <a name="how-parameterization-for-always-encrypted-works"></a>Always Encrypted 的參數化的運作方式

如果已針對查詢視窗啟用 [Always Encrypted 的參數化] 和 [Always Encrypted]，則 Azure Data Studio 會嘗試將 Transact-SQL 變數參數化，以符合下列先決條件的情況：

- 在相同陳述式中宣告及初始化 (內嵌初始化)。 使用個別 `SET` 陳述式宣告的變數將不會參數化。
- 使用單一常值初始化。 使用運算式 (包含任何運算子或函數) 初始化的變數將不會參數化。

以下是 Azure Data Studio 將參數化的變數範例。

```sql
DECLARE @SSN char(11) = '795-73-9838';
   
DECLARE @BirthDate date = '19990104';
DECLARE @Salary money = $30000;
```

以下是 Azure Data Studio 不會嘗試參數化的一些變數範例：

```sql
DECLARE @Name nvarchar(50); --Initialization separate from declaration
SET @Name = 'Abel';

DECLARE @StartDate date = GETDATE(); -- a function used instead of a literal

DECLARE @NewSalary money = @Salary * 1.1; -- an expression used instead of a literal
```
 
若要成功嘗試進行參數化：   
- 針對要參數化之變數初始化所使用的常值類型必須符合變數宣告中的類型。   
- 如果變數的宣告類型是日期類型或時間類型，就必須使用字串，以其中一個遵循 ISO 8601 規範的格式來初始化變數。   

以下是將產生參數化錯誤的 Transact-SQL 變數宣告範例：

```sql
DECLARE @BirthDate date = '01/04/1999' -- unsupported date format   
   
DECLARE @Number int = 1.1 -- the type of the literal does not match the type of the variable   
```

Azure Data Studio 會使用 Intellisense，來告知您哪些變數可以成功參數化，以及哪些參數化嘗試失敗 (與原因)。   

在查詢視窗中，會以資訊訊息底線標示可成功參數化的變數宣告。 如果您將滑鼠暫留在以資訊訊息底線標示的宣告陳述式上，您會看到包含參數化程序結果的訊息，包括所產生 [SqlParameter 類別](/dotnet/api/microsoft.data.sqlclient.sqlparameter) \(英文\) 物件的主要屬性 (變數對應至：[SqlDbType](/dotnet/api/microsoft.data.sqlclient.sqlparameter.dbtype)、[Size](/dotnet/api/microsoft.data.sqlclient.sqlparameter.size)、[Precision](/dotnet/api/microsoft.data.sqlclient.sqlparameter.precision)、[Scale](/dotnet/api/microsoft.data.sqlclient.sqlparameter.scale)、[SqlValue](/dotnet/api/microsoft.data.sqlclient.sqlparameter.sqlvalue))。 您也可以在 [問題] 檢視中，查看已成功地參數化的所有變數的完整清單。 若要開啟 [問題] 檢視，請選取 [檢視] > [問題]。    



如果 Azure Data Studio 已嘗試將變數參數化，但參數化失敗，則將會以錯誤底線標示變數的宣告。 如果您將滑鼠停留在已使用錯誤底線標示的宣告陳述式上，您會取得有關錯誤的結果。 您也可以在 [問題] 檢視中，查看所有變數的參數化錯誤的完整清單。

 
> [!NOTE]
> 由於 Always Encrypted 支援一組有限的類型轉換子集，因此，在許多情況下，會要求 Transact-SQL 變數的資料類型與其設定為目標的目標資料庫資料行的類型相同。 例如，假設 `Patients` 資料表中 `SSN` 資料行的類型是 `char(11)`，以下查詢將會失敗，因為 `@SSN` 變數的類型為 `nchar(11)`，不符合資料行的類型。   

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
> 未啟用參數化時，整個查詢 (包括類型轉換) 會在 SQL Server/Azure SQL Database 內部處理。 啟用參數化時，Microsoft .NET Data Provider for SQL Server 會在 Azure Data Studio 內部執行一些類型轉換。 由於 Microsoft .NET 類型系統與 SQL Server 類型系統之間的差異 (例如某些類型 (例如浮點數) 的有效位數不同)，因此，已啟用參數化執行的查詢可以產生不同於未啟用參數化執行的查詢的結果。 

## <a name="next-steps"></a>後續步驟
- [使用 Always Encrypted 開發應用程式](always-encrypted-client-development.md)


## <a name="see-also"></a>另請參閱
- [一律加密](../../../relational-databases/security/encryption/always-encrypted-database-engine.md)