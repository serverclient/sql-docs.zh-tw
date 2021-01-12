---
description: SET ANSI_WARNINGS (Transact-SQL)
title: SET ANSI_WARNINGS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 04/15/2020
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SET ANSI_WARNINGS
- ANSI_WARNINGS_TSQL
- ANSI_WARNINGS
- SET_ANSI_WARNINGS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- errors [SQL Server], ISO standard behavior
- warnings [SQL Server]
- SET ANSI_WARNINGS statement
- ANSI_WARNINGS option
ms.assetid: f82aaab0-334f-427b-89b0-de4af596b4fa
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 796f94538e2ec8ba3a89db014824b135e5be9e6d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096888"
---
# <a name="set-ansi_warnings-transact-sql"></a>SET ANSI_WARNINGS (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  指定數個錯誤狀況的 ISO 標準行為。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法

### <a name="syntax-for-ssnoversion-mdmd-and-sssodfull-mdmd"></a>[!INCLUDE[ssnoversion-md.md](../../includes/ssnoversion-md.md)] 和 [!INCLUDE[sssodfull-md.md](../../includes/sssodfull-md.md)] 的語法
```syntaxsql
SET ANSI_WARNINGS { ON | OFF }
```

### <a name="syntax-for-sssdw-mdmd-and-sspdw-mdmd"></a>[!INCLUDE[sssdw-md.md](../../includes/sssdw-md.md)] 和 [!INCLUDE[sspdw-md.md](../../includes/sspdw-md.md)] 的語法
```syntaxsql
SET ANSI_WARNINGS ON
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註
 SET ANSI_WARNINGS 會影響下列狀況：  
  
-   當設為 ON 時，如果彙總函式 (如 SUM、AVG、MAX、MIN、STDEV、STDEVP、VAR、VARP 或 COUNT) 中出現 Null 值，就會產生警告訊息。 當設為 OFF 時，不會產生警告訊息。  
  
-   當設為 ON 時，除以零和算術溢位錯誤會造成陳述式的回復，且會產生錯誤訊息。 當設為 OFF 時，除以零和算術溢位錯誤會造成傳回 Null 值。 如果在 **character**、Unicode 或 **binary** 資料行中嘗試 INSERT 或 UPDATE，且新值長度超過資料行的大小上限時，除以零或算術溢位錯誤會造成傳回 Null 值。 如果 SET ANSI_WARNINGS 為 ON，INSERT 或 UPDATE 就會依 ISO 標準的指定加以取消。 字元資料行尾端的空格會被忽略，二進位資料行尾端的 Null 也會被忽略。 當它是 OFF 時，便會將資料截斷成為資料行大小，陳述式會繼續作業。  
  
> [!NOTE]  
> 在來源或目標是 **binary** 或 **varbinary** 資料的任何轉換作業中，當發生截斷時，不論 SET 選項為何，都不會發出任何警告或錯誤。  
  
> [!NOTE]  
> 當在預存程序或使用者自訂函數中傳遞參數時，或在批次陳述式中宣告和設定變數時，會忽略 ANSI_WARNINGS。 例如，如果將變數定義為 **char(3)** ，然後設定為大於三個字元的值，資料就會被截斷成定義的大小，而 INSERT 或 UPDATE 陳述式會執行成功。  
  
您可以利用 sp_configure 的 user options 選項來設定所有伺服器連接的 ANSI_WARNINGS 預設值。 如需詳細資訊，請參閱本主題稍後的 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)的使用者閱讀。  
  
當您要建立或操作計算資料行索引或索引檢視表時，ANSI_WARNINGS 必須是 ON。 如果 SET ANSI_WARNINGS 是 OFF，含計算資料行索引的資料表或索引檢視之 CREATE、UPDATE、INSERT 和 DELETE 陳述式會失敗。 如需使用索引檢視表和計算資料行索引時所需之 SET 選項設定的詳細資訊，請參閱 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md) 中的＜使用 SET 陳述式時的考量＞一節。  
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 包括 ANSI_WARNINGS 資料庫選項。 這相當於 SET ANSI_WARNINGS。 當 SET ANSI_WARNINGS 是 ON 時，會在除以零、資料庫資料行的字串太大及其他類似的錯誤中，產生錯誤或警告。 當 SET ANSI_WARNINGS 是 OFF 時，不會產生這些錯誤和警告。 model 資料庫中的 SET ANSI_WARNINGS 預設值是 OFF。 若未指定，就會套用 ANSI_WARNINGS 的設定。 如果 SET ANSI_WARNINGS 是 OFF，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會使用 [sys.databases](../../relational-databases/system-catalog-views/sys-databases-transact-sql.md) 目錄檢視中的 is_ansi_warnings_on 資料行值。  
  
> [!IMPORTANT]
> ANSI_WARNINGS 應該設為 ON，以便執行分散式查詢。  
  
用戶端，例如 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC 驅動程式、適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB 提供者與適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的 Microsoft JDBC 驅動程式會自動使用連線旗標將 ANSI_WARNINGS 設定為開啟。 在連接之前，您可以在應用程式的 ODBC 資料來源或 ODBC 連接屬性中設定這個項目。 起始於 DB-Library 應用程式的連接之 SET ANSI_WARNINGS 預設值是 OFF。 如需詳細資訊，請參閱表格式資料流 (TDS) 通訊協定規格中的 [LOGIN7](/openspecs/windows_protocols/ms-tds/773a62b6-ee89-4c02-9e5e-344882630aac) \(英文\)。 

當 ANSI_DEFAULTS 是 ON 時，會啟用 ANSI_WARNINGS。  
  
ANSI_WARNINGS 的設定是在執行時或執行階段進行定義，而不是在剖析階段進行定義。  
  
如果 SET ARITHABORT 或 SET ARITHIGNORE 是 OFF，而 SET ANSI_WARNINGS 是 ON，當發現除以零或溢位的錯誤時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回錯誤訊息。  
  
若要檢視此設定的目前設定，請執行下列查詢。  
  
```sql  
DECLARE @ANSI_WARN VARCHAR(3) = 'OFF';  
IF ( (8 & @@OPTIONS) = 8 ) SET @ANSI_WARN = 'ON';  
SELECT @ANSI_WARN AS ANSI_WARNINGS;  
```  
  
## <a name="permissions"></a>權限  
需要 **public** 角色的成員資格。  
  
## <a name="examples"></a>範例  
下列範例會示範將 SET ANSI_WARNINGS 設為 ON 和 OFF 的上述三種狀況。  
  
```sql  
CREATE TABLE T1   
(  
   a int,   
   b int NULL,   
   c varchar(20)  
);  
GO  
  
SET NOCOUNT ON;  
  
INSERT INTO T1   
VALUES (1, NULL, '')   
      ,(1, 0, '')  
      ,(2, 1, '')  
      ,(2, 2, '');  
  
SET NOCOUNT OFF;  
GO  
```

現在，將 ANSI_WARNINGS 設定為 ON 並進行測試。

```sql
PRINT '**** Setting ANSI_WARNINGS ON';  
GO  
  
SET ANSI_WARNINGS ON;  
GO  
  
PRINT 'Testing NULL in aggregate';  
GO  
SELECT a, SUM(b)   
FROM T1   
GROUP BY a;  
GO  
  
PRINT 'Testing String Overflow in INSERT';  
GO  
INSERT INTO T1   
VALUES (3, 3, 'Text string longer than 20 characters');  
GO  
  
PRINT 'Testing Divide by zero';  
GO  
SELECT a / b AS ab   
FROM T1;  
GO  
```

現在，將 ANSI_WARNINGS 設定為 OFF 並進行測試。

```sql
PRINT '**** Setting ANSI_WARNINGS OFF';  
GO  
SET ANSI_WARNINGS OFF;  
GO  
  
PRINT 'Testing NULL in aggregate';  
GO  
SELECT a, SUM(b)   
FROM T1   
GROUP BY a;  
GO  
  
PRINT 'Testing String Overflow in INSERT';  
GO  
INSERT INTO T1   
VALUES (4, 4, 'Text string longer than 20 characters');  
GO  
SELECT a, b, c   
FROM T1  
WHERE a = 4;  
GO  
  
PRINT 'Testing Divide by zero';  
GO  
SELECT a / b AS ab   
FROM T1;  
GO  
  
DROP TABLE T1;  
```  
  
## <a name="see-also"></a>另請參閱  
 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SET ANSI_DEFAULTS &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-defaults-transact-sql.md)   
 [SESSIONPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/sessionproperty-transact-sql.md)  
  
