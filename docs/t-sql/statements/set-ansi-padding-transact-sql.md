---
description: SET ANSI_PADDING (Transact-SQL)
title: SET ANSI_PADDING (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 12/04/2017
ms.prod: sql
ms.prod_service: sql-data-warehouse, pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ANSI_PADDING_TSQL
- ANSI_PADDING
- SET_ANSI_PADDING_TSQL
- SET ANSI_PADDING
dev_langs:
- TSQL
helpviewer_keywords:
- data types [SQL Server], short values
- ANSI_PADDING option
- short values [SQL Server]
- SET ANSI_PADDING statement
- trailing blanks
ms.assetid: 92bd29a3-9beb-410e-b7e0-7bc1dc1ae6d0
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 95aef665b10cebc457f586837b9c59f81f03443f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102291"
---
# <a name="set-ansi_padding-transact-sql"></a>SET ANSI_PADDING (Transact-SQL)
[!INCLUDE [sql-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdbmi-asa-pdw.md)]

  控制資料行針對數值長度短於資料行所定義大小的儲存方式，以及資料行針對 **char**、 **varchar**、 **binary** 和 **varbinary** 資料尾端具有空白之數值的儲存方式。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法
 
### <a name="syntax-for-ssnoversion-mdmd-and-sssodfull-mdmd"></a>[!INCLUDE[ssnoversion-md.md](../../includes/ssnoversion-md.md)] 和 [!INCLUDE[sssodfull-md.md](../../includes/sssodfull-md.md)] 的語法 
```syntaxsql
SET ANSI_PADDING { ON | OFF }
```

### <a name="syntax-for-sssdw-mdmd-and-sspdw-mdmd"></a>[!INCLUDE[sssdw-md.md](../../includes/sssdw-md.md)] 和 [!INCLUDE[sspdw-md.md](../../includes/sspdw-md.md)] 的語法
```syntaxsql
SET ANSI_PADDING ON
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註
 以 **char**、**varchar**、**binary** 和 **varbinary** 資料類型定義的資料行具有定義的大小。  
  
 這項設定只會影響新資料行的定義。 建立好資料行之後， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會根據建立資料行之時的設定來儲存值。 這項設定後來的變更並不會影響現有的資料行。  
  
> [!NOTE]  
> ANSI_PADDING 應該一律設定為 ON。  
  
 下表顯示當值插入含 **char**、**varchar**、**binary** 和 **varbinary** 等資料類型的資料行時，SET ANSI_PADDING 設定所造成的影響。  
  
|設定|char(*n*) NOT NULL 或 binary(*n*) NOT NULL|char(*n*) NULL 或 binary(*n*) NULL|varchar(*n*) 或 varbinary(*n*)|  
|-------------|----------------------------------------------------|--------------------------------------------|----------------------------------------|  
|開啟|將原始值填補到資料行的長度 (**char** 資料行使用尾端空格，**binary** 資料行使用尾端零)。|當 SET ANSI_PADDING 為 ON 時，請遵循 **char(** _n_ **)** 或 **binary(** _n_ **)** NOT NULL 的相同規則。|不修剪插入 **varchar** 資料行之字元值的尾端空格。 不修剪插入 **varbinary** 資料行之二進位值的尾端零。 值不會填補到資料行的長度。|  
|OFF|將原始值填補到資料行的長度 (**char** 資料行使用尾端空格，**binary** 資料行使用尾端零)。|當 SET ANSI_PADDING 是 OFF 時，請遵照 **varchar** 或 **varbinary** 的相同規則。|修剪插入 **varchar** 資料行之字元值的尾端空格。 修剪插入 **varbinary** 資料行之二進位值的尾端零。|  
  
> [!NOTE]  
> 當填補時，**char** 資料行會填補空格，**binary** 資料行會填補零。 當修剪時，**char** 資料行會修剪尾端空格，**binary** 資料行會修剪尾端零。  
  
當您要建立或變更計算資料行索引或索引檢視表時，ANSI_PADDING 必須是 ON。 如需使用索引檢視表和計算資料行索引時所需之 SET 選項設定的詳細資訊，請參閱 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md) 中的＜使用 SET 陳述式時的考量＞一節。  
  
SET ANSI_PADDING 的預設值是 ON。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC 驅動程式和 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client OLE DB 提供者在連接時，都會自動將 ANSI_PADDING 設為 ON。 在連接之前，您可以在應用程式的 ODBC 資料來源、ODBC 連接屬性或 OLE DB 連接屬性集中設定這個項目。 起始於 DB-Library 應用程式的連接之 SET ANSI_PADDING 預設值為 OFF。  
  
 SET ANSI_PADDING 設定不會影響 **nchar**、**nvarchar**、**ntext**、**text**、**image**、**varbinary(max)**、**varchar(max)** 和 **nvarchar(max)** 資料型別。 它們一律會顯示 SET ANSI_PADDING ON 行為。 這表示不會修剪尾端空格和零。  
  
當 ANSI_DEFAULTS 是 ON 時，會啟用 ANSI_PADDING。  
  
ANSI_PADDING 的設定是在執行時或執行階段進行定義，而不是在剖析階段進行定義。  
  
若要檢視此設定的目前設定，請執行下列查詢。  
  
```sql  
DECLARE @ANSI_PADDING VARCHAR(3) = 'OFF';  
IF ( (16 & @@OPTIONS) = 16 ) SET @ANSI_PADDING = 'ON';  
SELECT @ANSI_PADDING AS ANSI_PADDING;  
```  
  
## <a name="permissions"></a>權限  
需要 **public** 角色的成員資格。  
  
## <a name="examples"></a>範例  
下列範例顯示設定如何影響這些資料類型。  

將 ANSI_PADDING 設定為 ON 並進行測試。

```sql  
PRINT 'Testing with ANSI_PADDING ON'  
SET ANSI_PADDING ON;  
GO  
  
CREATE TABLE t1 (  
   charcol CHAR(16) NULL,   
   varcharcol VARCHAR(16) NULL,   
   varbinarycol VARBINARY(8)  
);  
GO  
INSERT INTO t1 VALUES ('No blanks', 'No blanks', 0x00ee);  
INSERT INTO t1 VALUES ('Trailing blank ', 'Trailing blank ', 0x00ee00);  
  
SELECT 'CHAR' = '>' + charcol + '\<', 'VARCHAR'='>' + varcharcol + '\<',  
   varbinarycol  
FROM t1;  
GO  
```

現在，將 ANSI_PADDING 設定為 OFF 並進行測試。

```sql
PRINT 'Testing with ANSI_PADDING OFF';  
SET ANSI_PADDING OFF;  
GO  
  
CREATE TABLE t2 (  
   charcol CHAR(16) NULL,   
   varcharcol VARCHAR(16) NULL,   
   varbinarycol VARBINARY(8)  
);  
GO  
INSERT INTO t2 VALUES ('No blanks', 'No blanks', 0x00ee);  
INSERT INTO t2 VALUES ('Trailing blank ', 'Trailing blank ', 0x00ee00);  
  
SELECT 'CHAR' = '>' + charcol + '\<', 'VARCHAR'='>' + varcharcol + '<',  
   varbinarycol  
FROM t2;  
GO  
  
DROP TABLE t1;  
DROP TABLE t2;  
```  
  
## <a name="see-also"></a>另請參閱  
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SESSIONPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/sessionproperty-transact-sql.md)   
 [CREATE TABLE &#40;Transact-SQL&#41;](../../t-sql/statements/create-table-transact-sql.md)   
 [INSERT &#40;Transact-SQL&#41;](../../t-sql/statements/insert-transact-sql.md)   
 [SET ANSI_DEFAULTS &#40;Transact-SQL&#41;](../../t-sql/statements/set-ansi-defaults-transact-sql.md)  
  
  
