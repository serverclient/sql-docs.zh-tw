---
title: sp_data_source_table_columns
description: 'sp_data_source_table_columns (Transact-sql) '
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: conceptual
f1_keywords:
- sp_data_source_table_columns_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- PolyBase
author: MikeRayMSFT
ms.author: mikeray
ms.custom: ''
ms.date: 11/10/2020
ms.openlocfilehash: 30205befcdd9da78f4f9a3f002ddd398f49abc55
ms.sourcegitcommit: d681796e8c012eca2d9629d3b816749e9f50f868
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98005383"
---
# <a name="sp_data_source_table_columns-transact-sql"></a>sp_data_source_table_columns (Transact-sql) 

[!INCLUDE [sqlserver2019](../../includes/applies-to-version/sqlserver2019.md)]

傳回外部資料來源資料表中的資料行清單。
  
> [!NOTE]
> 此程式是在 [SQL 2019 CU5](../../big-data-cluster/release-notes-big-data-cluster.md#cu5)中引進。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```sqlsyntax
sp_data_source_table_columns
         [ @data_source = ] 'data_source'
       , [ @table_location = ] 'table_location'
[ ; ]
```  

## <a name="arguments"></a>引數

`[ @data_source = ] 'data_source'`   
要從中取得中繼資料的外部資料源名稱。 類型為 `sysname` 。

`[ @table_location = ] 'table_location'`   
識別資料表的資料表位置字串。 `table_location` 類型為 `nvarchar(max)` 。

## <a name="returns"></a>傳回

預存程式會傳回下列資訊：

|資料行名稱 |資料類型 |描述|
|---|---|---|
|名稱|nvarchar(max)|資料行名稱。
|TYPE|nvarchar(200)|SQL Server 類型名稱
|LENGTH|int|資料行的長度
|PRECISION|int|資料行的有效位數
|SCALE|int|資料行的小數位數
|COLLATION|nvarchar(200)|SQL Server 資料行的定序
|IS_NULLABLE|bit|此資料行是否可為 null。
|SOURCE_TYPE_NAME|nvarchar(max)|後端特定的類型名稱。 大部分用於偵錯工具。 針對 ODBC 來源，這會對應到 SQLColumns ( # A1 的 TYPE_NAME 結果資料行。
|REMARKS|nvarchar(max)|資料行的一般批註或描述。 目前一律為 Null。|

## <a name="permissions"></a>權限  

要求 ALTER ANY EXTERNAL DATA SOURCE 權限。
  
## <a name="remarks"></a>備註  

SQL Server 實例必須安裝  [PolyBase](../../relational-databases/polybase/polybase-guide.md) 功能。

這個預存程式支援的連接器：

- SQL Server
- Oracle
- Teradata
- MongoDB
- CosmosDB

預存程式不支援泛型 ODBC dta 來源連接器。

空白與非空白的概念與 ODBC 驅動程式和函式的行為[ `SQLTables` 有關。](../native-client-odbc-api/sqltables.md) 非空白表示物件包含資料表，而不是資料列。 例如，空的架構在 SQL Server 中不包含任何資料表。 空的資料庫包含 Teradata 內沒有任何資料表。結果會是後端架構的 SQL Server 標記法，如後端的 PolyBase 連接器所解讀。 此處的差別是，您不只是將 ODBC 呼叫的結果傳遞給後端，而是以 PolyBase 型別對應程式碼的結果為基礎。

使用 [`sp_data_source_objects`](sp-data-source-objects.md) 和 `sp_data_source_table_columns` 來探索外部物件。 這些系統預存程式會傳回可虛擬化之資料表的架構。 Azure Data Studio 使用這兩個預存程式來支援 [資料虛擬化](../../azure-data-studio/extensions/data-virtualization-extension.md)。 用 `sp_data_source_table_columns` 來探索 SQL Server 資料類型中所表示的外部資料表架構。

由於 Hadoop 來源資料中的定序與 SQL Server 2019 中支援的定序不同，因此外部資料表中 Varchar 資料類型資料行的建議資料類型長度可能會比預期更大。 這是原廠設定。

## <a name="example"></a>範例  

下列範例會傳回名為的 SQL Server 中的外部資料表的資料表資料行，該資料表 `server` 屬於名為的架構 `schema` 。
  
```sql
DECLARE @data_source SYSNAME = N'ExternalDataSourceName';
DECLARE @table_location NVARCHAR(400) = N'[database].[schema].[table]';
EXEC sp_data_source_table_columns @data_source, @table_location
```  
  
## <a name="see-also"></a>請參閱

- [開始使用 PolyBase](../polybase/polybase-guide.md)
- [系統預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)
- [CREATE EXTERNAL TABLE AS SELECT (Transact-SQL)](../../t-sql/statements/create-external-table-as-select-transact-sql.md)
- [CREATE EXTERNAL TABLE (Transact-SQL)](../../t-sql/statements/create-external-table-transact-sql.md)