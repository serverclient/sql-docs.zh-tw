---
title: 擴充性架構 API
titleSuffix: SQL Server Language Extensions
description: 您可使用擴充性架構來撰寫 SQL Server 的程式設計語言延伸模組。 Microsoft SQL Server 擴充性架構 API 是一種可供語言延伸模組用來與 SQL Server 互動及交換資料的 API。
author: dphansen
ms.author: davidph
ms.date: 10/09/2020
ms.topic: reference
ms.prod: sql
ms.technology: language-extensions
monikerRange: '>=sql-server-ver15||>=sql-server-linux-ver15'
ms.openlocfilehash: 244a8ce680704b0b25f9d0499e1a7802661d56cd
ms.sourcegitcommit: 1a544cf4dd2720b124c3697d1e62ae7741db757c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97471689"
---
# <a name="extensibility-framework-api-for-sql-server"></a>SQL Server 的擴充性架構 API
[!INCLUDE [SQL Server 2019 and later](../../includes/applies-to-version/sqlserver2019.md)]

您可使用擴充性架構來撰寫 SQL Server 的程式設計語言延伸模組。 Microsoft SQL Server 擴充性架構 API 是一種可供語言延伸模組用來與 SQL Server 互動及交換資料的 API。

身為語言延伸模組作者，您可使用此參考和開放原始碼語言延伸模組，以了解如何使用此 API 撰寫自己的語言延伸模組。 您可在 [aka.ms/mssql-lang-extensions](https://aka.ms/mssql-lang-extensions) 找到語言延伸模組的原始程式碼。

您可在下方找到所有 API 函數的相關語法和引數資訊。

## <a name="return-value"></a>傳回值

所有函數都會傳回 *SQLRETURN* 參數。 如果此值是 *SQL_SUCCESS* 以外的任何值，則會視為錯誤並停止執行指令碼。

## <a name="standard-output"></a>標準輸出

延伸模組對標準輸出或錯誤資料流的任何輸出都會追蹤到工作階段記錄檔，且最後會追蹤回 SQL Server (類似於 [SSMS 訊息] 索引標籤中顯示的任何訊息)。


## <a name="init"></a>Init

此函數只會呼叫一次，並用來將執行階段初始化以便執行。 

### <a name="syntax"></a>語法

```cpp
SQLRETURN Init(
    SQLCHAR *ExtensionParams,
    SQLULEN ExtensionParamsLength,
    SQLCHAR *ExtensionPath,
    SQLULEN ExtensionPathLength,
    SQLCHAR *PublicLibraryPath,
    SQLULEN PublicLibraryPathLength,
    SQLCHAR *PrivateLibraryPath,
    SQLULEN PrivateLibraryPathLength
);
```

### <a name="arguments"></a>引數

*ExtensionParams*  
\[輸入\] 以 Null 終止的字串，包含 [CREATE EXTERNAL LANGUAGE](../../t-sql/statements/create-external-language-transact-sql.md) 或 [ALTER EXTERNAL LANGUAGE](../../t-sql/statements/alter-external-language-transact-sql.md) 期間所提供的 `PARAMETERS` 值。

*ExtensionParamsLength*  
\[輸入\] *ExtensionParams* 的位元組長度 (不包括 Null 終止字元)。

*ExtensionPath*  
\[輸入\] 以 Null 終止的 UTF-8 字串，包含延伸模組安裝目錄的絕對路徑。

*ExtensionPathLength*  
\[輸入\] *ExtensionPath* 的位元組長度 (不包括 Null 終止字元)。

*PublicLibraryPath*  
\[輸入\] 以 Null 終止的 UTF-8 字串，包含此外部語言公用外部程式庫目錄的絕對路徑。

*PublicLibraryPathLength*  
\[輸入\] *PublicLibraryPath* 的位元組長度 (不包括 Null 終止字元)。

*PrivateLibraryPath*  
\[輸入\] 以 Null 終止的 UTF-8 字串，包含此使用者和此外部語言私人外部程式庫目錄的絕對路徑。

*PrivateLibraryPathLength*  
\[輸入\] *PrivateLibraryPath* 的位元組長度 (不包括 Null 終止字元)。

## <a name="initsession"></a>InitSession

此函數每個工作階段會呼叫一次，以初始化工作階段的特定設定。

### <a name="syntax"></a>語法

```cpp
SQLRETURN InitSession(
    SQLGUID         SessionId,
    SQLUSMALLINT    TaskId,
    SQLCHAR*        Script,
    SQLULEN         ScriptLength,
    SQLUSMALLINT    InputSchemaColumnsNumber,
    SQLUSMALLINT    ParametersNumber
    SQLCHAR*        InputDataName,
    SQLUSMALLINT    InputDataNameLength,
    SQLCHAR*        OutputDataName,
    SQLUSMALLINT    OutputDataNameLength
);
```

### <a name="arguments"></a>引數

*SessionId*  
\[輸入\] 唯一識別此指令碼工作階段的 GUID。

*TaskId*  
\[輸入\] 唯一識別此執行處理序的整數。

當 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@parallel = 1` 時，此值是在 0 到查詢平行處理原則程度的範圍內。

*指令碼*  
\[輸入\] 以 Null 終止的 UTF-8 字串，包含 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@script`。

*ScriptLength*  
\[輸入\] *ScriptScript* 的位元組長度 (不包括 Null 終止字元)。

*InputSchemaColumnsNumber*  
\[輸入\] 來自 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中 `@input_data_1` 結果集的資料行數目。

*ParametersNumber*  
\[輸入\] 來自 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中 `@params` 的輸入參數數目。

*InputDataName*  
\[輸入\] 以 Null 終止的 UTF-8 字串，包含 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@input_data_1_name`。

*InputDataNameLength*  
\[輸入\] *InputDataName* 的位元組長度 (不包括 Null 終止字元)。

*OutputDataName*  
\[輸入\] 以 Null 終止的 UTF-8 字串，包含 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@output_data_1_name`。

*OutputDataNameLength*  
\[輸入\] *OutputDataName* 的位元組長度 (不包括 Null 終止字元)。

## <a name="initcolumn"></a>InitColumn

將特定工作階段中指定資料行的資訊初始化。

您可從 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@input_data_1`，針對結果集的每個資料行呼叫此函數。

此結果集的資料行結構會稱為「輸入結構描述」  。

### <a name="syntax"></a>語法

```cpp
SQLRETURN InitColumn(
    SQLGUID       SessionId,
    SQLUSMALLINT  TaskId,
    SQLUSMALLINT  ColumnNumber,
    SQLCHAR*      ColumnName,
    SQLSMALLINT   ColumnNameLength,
    SQLSMALLINT   DataType,
    SQLULEN       ColumnSize,
    SQLSMALLINT   DecimalDigits,
    SQLSMALLINT   Nullable,
    SQLSMALLINT   PartitionByNumber,
    SQLSMALLINT   OrderByNumber
);
```

### <a name="arguments"></a>引數

*SessionId*  
\[輸入\] 唯一識別此指令碼工作階段的 GUID。

*TaskId*  
\[輸入\] 唯一識別此執行處理序的整數。

當 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@parallel = 1` 時，此值是在 0 到查詢平行處理原則程度的範圍內。

*ColumnNumber*  
\[輸入\] 在輸入結構描述中識別此資料行索引的整數。 資料行會依遞增順序循序編號，從 0 開始。

*ColumnName*  
\[輸入\] 以 Null 終止的 UTF-8 字串，包含資料行的名稱。

*ColumnNameLength*  
\[輸入\] *ColumnName* 的位元組長度 (不包括 Null 終止字元)。

*DataType*  
\[輸入\] 識別此資料行資料類型的 ODBC C 類型。

*ColumnSize*  
\[輸入\] 此資料行中基礎資料的大小上限 (以位元組為單位)。

針對 SQL_C_CHAR、SQL_C_WCHAR 和 SQL_C_BINARY 資料類型，大於 8000 的值表示此資料行代表 LOB 物件，且大小上限為 2 GB。

*DecimalDigits*  
\[輸入\] 此資料行中基礎資料的小數位數，如[小數位數](../../odbc/reference/appendixes/decimal-digits.md)所定義。

*可為 Null*  
\[輸入\] 值，指出此資料行是否可以包含 NULL 值。 可能的值：

- SQL_NO_NULLS：資料行不能包含 NULL 值。
- SQL_NULLABLE：資料行可以包含 NULL 值。

*PartitionByNumber*  
\[輸入\] 值，用於在 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 的 `@input_data_1_partition_by_columns` 序列中表示此資料行索引。 資料行會依遞增順序循序編號，從 0 開始。 如果此資料行不包含在序列中，則值為 -1。

*OrderByNumber*  
\[輸入\] 值，用以在 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 的 `@input_data_1_order_by_columns` 序列中表示此資料行索引。 資料行會依遞增順序循序編號，從 0 開始。 如果此資料行不包含在序列中，則值為 -1。

## <a name="initparam"></a>InitParam

將特定工作階段中指定輸入參數的相關資訊初始化。

您可從 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@params`，針對每個參數呼叫此函數。

### <a name="syntax"></a>語法

```cpp
SQLRETURN InitParam(
    SQLGUID      SessionId,
    SQLUSMALLINT TaskId,
    SQLUSMALLINT ParamNumber,
    SQLCHAR*     ParamName,
    SQLSMALLINT  ParamNameLength,
    SQLSMALLINT  DataType,
    SQLULEN      ParamSize,
    SQLSMALLINT  DecimalDigits,
    SQLPOINTER   ParamValue,
    SQLINTEGER   StrLen_or_Ind,
    SQLSMALLINT  InputOutputType
);
```

### <a name="arguments"></a>引數

*SessionId*  
\[輸入\] 唯一識別此指令碼工作階段的 GUID。

*TaskId*  
\[輸入\] 唯一識別此執行處理序的整數。

當 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@parallel = 1` 時，此值是在 0 到查詢平行處理原則程度的範圍內。

*ParamNumber*  
\[輸入\] 識別此參數索引的整數。 參數會依遞增順序循序編號，從 0 開始。

*ParamName*  
\[輸入\] 以 Null 終止的 UTF-8 字串，包含參數的名稱。

*ParamNameLength*  
\[輸入\] *ParamName* 的位元組長度 (不包括 Null 終止字元)。

*DataType*  
\[輸入\] 識別此參數資料類型的 ODBC C 類型。

*ParamSize*  
\[輸入\] 此參數中基礎資料的大小上限 (以位元組為單位)。

針對 SQL_C_CHAR、SQL_C_WCHAR 和 SQL_C_BINARY 資料類型，大於 8000 的值表示此參數代表 LOB 物件，且大小上限為 2 GB。

*DecimalDigits*  
\[輸入\] 此參數中基礎資料的小數位數，如[小數位數](../../odbc/reference/appendixes/decimal-digits.md)所定義。

*ParamValue*  
\[輸入\] 緩衝區的指標，包含參數的值。

*StrLen_or_Ind*  
\[輸入\] 整數值 (表示 *ParamValue* 的位元組長度) 或 SQL_NULL_DATA (表示資料為 NULL)。

如果資料行不可為 Null，且不代表下列其中一個資料類型，則可忽略 StrLen_or_Ind\[col\]：SQL_C_CHAR、SQL_C_WCHAR 和 SQL_C_BINARY、SQL_C_NUMERIC 或 SQL_C_TYPE_TIMESTAMP。 否則會指向具有 \[RowsNumber\] 個元素的有效陣列，其中每個元素都包含其長度或 Null 指標資料。

*InputOutputType*  
\[輸入\] 參數的類型。 *InputOutputType* 引數是下列其中一個值：

- SQL_PARAM_INPUT
- SQL_PARAM_INPUT_OUTPUT

## <a name="execute"></a>執行

執行 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@script`。

此函數可以呼叫多次。 從 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@input_data_1_partition_by_columns`，針對每個資料流區塊和每個分割區各一次。


### <a name="syntax"></a>語法

```cpp
SQLRETURN Execute(
    SQLGUID         SessionId,
    SQLUSMALLINT    TaskId,
    SQLULEN         RowsNumber,
    SQLPOINTER*     Data,
    SQLINTEGER**    StrLen_or_Ind,
    SQLUSMALLINT*   OutputSchemaColumnsNumber
);
```

### <a name="arguments"></a>引數

*SessionId*  
\[輸入\] 唯一識別此指令碼工作階段的 GUID。

*TaskId*  
\[輸入\] 唯一識別此執行處理序的整數。

當 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@parallel = 1` 時，此值是在 0 到查詢平行處理原則程度的範圍內。

*RowsNumber*  
\[輸入\] *Data* 中的資料列數目。

*Data*  
\[輸入\] 二維陣列，其包含`@input_data_1` n [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 的結果集。

資料行總數是在 [InitSession](#initsession) 呼叫中收到的 *InputSchemaColumnsNumber*。 每個資料行都包含 *RowsNumber* 個元素，這些元素應該根據 [InitColumn](#initcolumn) 中的資料行類型予以解譯。

*StrLen_or_Ind* 中以 NULL 表示的元素不保證有效，應該予以忽略。

*StrLen_or_Ind*  
\[輸入\] 二維陣列，其包含 *Data* 中每個值的長度/NULL 指標。 每個資料格的可能值：

- n，其中 n > 0。 表示資料長度 (以位元組為單位)
- SQL_Null_DATA，表示 NULL 值。

資料行總數是在 [InitSession](#initsession) 呼叫中收到的 *InputSchemaColumnsNumber*。 每個資料行都包含 *RowsNumber* 個元素，這些元素應該根據 [InitColumn](#initcolumn) 中的資料行類型予以解譯。

如果一個資料行不可為 Null，且不是下列其中一種資料類型，則可忽略 StrLen_or_Ind\[col\]：SQL_C_CHAR、SQL_C_WCHAR 和 SQL_C_BINARY、SQL_C_NUMERIC 或 SQL_C_TYPE_TIMESTAMP。 否則會指向具有 *RowsNumber* 個元素的有效陣列，每個元素都包含其長度或 Null 指標資料。

*OutputSchemaColumnsNumber*  
\[輸出\] 緩衝區的指標，其中會在 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 的 `@script` 預期結果集傳回資料行數目。

## <a name="getresultcolumn"></a>GetResultColumn

擷取特定工作階段中指定輸出資料行的相關資訊。

您可從 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@script`，針對結果集的每個資料行呼叫此函數。
此結果集的資料行結構會稱為「輸出結構描述」。

### <a name="syntax"></a>語法

```cpp
SQLRETURN GetResultColumn(
    SQLGUID         SessionId,
    SQLUSMALLINT    TaskId,
    SQLUSMALLINT    ColumnNumber,
    SQLSMALLINT*    DataType,
    SQLINTEGER*     ColumnSize,
    SQLSMALLINT*    DecimalDigits,
    SQLSMALLINT*    Nullable
);
```

### <a name="arguments"></a>引數

*SessionId*  
\[輸入\] 唯一識別此指令碼工作階段的 GUID。

*TaskId*  
\[輸入\] 唯一識別此執行處理序的整數。

當 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@parallel = 1` 時，此值是在 0 到查詢平行處理原則程度的範圍內。

*ColumnNumber*  
\[輸入\] 在輸出結構描述中識別此資料行索引的整數。 資料行會依遞增順序循序編號，從 0 開始。

*DataType*  
\[輸出\] 緩衝區的指標，包含識別此資料行資料類型的 ODBC C 類型。

*ColumnSize*  
\[輸出\] 緩衝區的指標，包含此資料行中基礎資料的大小上限 (以位元組為單位)。

*DecimalDigits*  
\[輸出\] 緩衝區的指標，包含此資料行中基礎資料的小數位數，如[小數位數](../../odbc/reference/appendixes/decimal-digits.md)所定義。 如果無法判斷小數位數或不適用，則會捨棄值。

*可為 Null*  
\[輸出\] 緩衝區的指標，包含值，指出此資料行是否可以包含 NULL 值。 可能的值：

- SQL_NO_NULLS：資料行不能包含 NULL 值。
- SQL_NULLABLE：資料行可以包含 NULL 值。

如果傳遞其他值，則會停止執行。

## <a name="getresults"></a>GetResults

透過執行 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@script` 來擷取結果集。

此函數可以呼叫多次。 從 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@input_data_1_partition_by_columns`，針對每個資料流區塊和每個分割區各一次。


### <a name="syntax"></a>語法

```cpp
SQLRETURN GetResults(
    SQLGUID         SessionId,
    SQLUSMALLINT    TaskId,
    SQLULEN*        RowsNumber,
    SQLPOINTER**    Data,
    SQLINTEGER**_   StrLen_or_Ind
);
```

### <a name="arguments"></a>引數

_SessionId*  
\[輸入\] 唯一識別此指令碼工作階段的 GUID。

*TaskId*  
\[輸入\] 唯一識別此執行處理序的整數。

當 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@parallel = 1` 時，此值是在 0 到查詢平行處理原則程度的範圍內。

*RowsNumber*  
\[輸出\]，緩衝區的指標，其包含 *Data* 中的資料列數目。

*Data*  
\[輸出\] 延伸模組配置的二維陣列指標，其包含 `@script` n [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 的結果集。

資料行總數應該是在 [Execute](#execute) 呼叫中擷取的 *OutputSchemaColumnsNumber*。 每個資料行都應該包含 *RowsNumber* 個元素，這些元素應該根據 [GetResultColumn](#getresultcolumn) 中的資料行類型予以解譯。

*StrLen_or_Ind*  
\[輸出\] 延伸模組配置的二維陣列指標，其包含 *Data* 中每個值的長度/NULL 指標。 每個資料格的可能值：

- n，其中 n > 0。 表示資料長度 (以位元組為單位)
- SQL_Null_DATA，表示 NULL 值。

資料行總數應該是在 [Execute](#execute) 呼叫中收到的 *OutputSchemaColumnsNumber*。 每個資料行都包含 *RowsNumber* 個元素，這些元素應該根據 [GetResultColumn](#getresultcolumn) 中的資料行類型予以解譯。

如果一個資料行不可為 Null，且不代表下列其中一個資料類型，則會忽略 StrLen_or_Ind\[col\]：SQL_C_CHAR、SQL_C_WCHAR 和 SQL_C_BINARY [新增日期]。 否則會指向具有 *RowsNumber* 個元素的有效陣列，每個元素都包含其長度或 Null 指標資料。

## <a name="getoutputparam"></a>GetOutputParam

擷取特定工作階段中指定輸出參數的相關資訊。

您可從 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@params` (標記為 OUTPUT)，針對每個參數呼叫此函數。

### <a name="syntax"></a>語法

```cpp
SQLRETURN GetOutputParam(
    SQLGUID        SessionId,
    SQLUSMALLINT   ParamNumber,
    SQLPOINTER*    ParamValue,
    SQLINTEGER*    StrLen_or_Ind
);
```

### <a name="arguments"></a>引數

*SessionId*  
\[輸入\] 唯一識別此指令碼工作階段的 GUID。

*ParamValue*  
\[輸出\] 緩衝區的指標，包含參數的值。

*StrLen_or_Ind* \[輸出\] 緩衝區的指標，其中包含整數值 (表示 *ParamValue* 的位元組長度) 或 SQL_NULL_DATA (表示資料為 NULL)。

## <a name="getinterfaceversion"></a>GetInterfaceVersion

擷取介面版本。
此函數會傳回整數，表示延伸模組的介面版本。 支援的值：
1. 版本 1 是初始 API 版本。 支援 SQL Server 2019 RTM。
1. 版本 2 已新增對 InstallExternalLibrary 和 UninstallExternalLibrary API 的支援，並支援 SQL Server 2019 CU3。                            

### <a name="syntax"></a>語法

```cpp
SQLUSMALLINT GetInterfaceVersion();
```

## <a name="cleanupsession"></a>CleanupSession

清除每一工作階段的資訊。

### <a name="syntax"></a>語法

```cpp
SQLRETURN CleanupSession(
    SQLGUID        SessionId,
    SQLUSMALLINT   TaskId
);
```

### <a name="arguments"></a>引數

*SessionId*  
\[輸入\] 唯一識別此指令碼工作階段的 GUID。

*TaskId*  
\[輸入\] 唯一識別此執行處理序的整數。

當 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@parallel = 1` 時，此值是在 0 到查詢平行處理原則程度的範圍內。

## <a name="cleanup"></a>清理

清除全域的共用資訊 (例如 JVM)。

### <a name="syntax"></a>語法

```cpp
SQLRETURN Cleanup();
```

## <a name="gettelemetryresults"></a>GetTelemetryResults

從延伸模組擷取遙測 (索引鍵/值組) 資料。 此函數為選擇性，不需要實作。 遙測是由 `dm_db_external_script_execution_stats` 動態管理檢視 (DMV) 所公開。

有一個名為 script_executions 的計數器，是由架構所傳送。 延伸模組不應該使用此名稱。

每個遙測項目都是索引鍵/值組。 索引鍵為字串，值為 64 位元整數 - 計數器。 因此，輸出是由兩個邏輯陣列所組成：名稱及其對應的計數器。 每個陣列都是輸出。

每個陣列的長度為 *RowsNumber*，這是一項輸出。 第一個邏輯輸出包含字串的指標，因此會以兩個陣列表示：*CounterNames* (實際字串資料) 和 *CounterNamesLength* (每個字串的長度)。 第二個邏輯輸出會儲存在 *CounterValues* 指標中。

### <a name="syntax"></a>語法

```cpp
SQLRETURN GetTelemetryResults(
    SQLGUID        SessionId,
    SQLUSMALLINT   TaskId,
    SQLUINTEGER    *RowsNumber,
    SQLCHAR        ***CounterNames,
    SQLINTEGER      **CounterNamesLength,
    SQLBIGINT       **CounterValues
);
```

### <a name="arguments"></a>引數

*SessionId*  
\[輸入\] 唯一識別此指令碼工作階段的 GUID。

*TaskId*  
\[輸入\] 唯一識別此執行處理序的整數。

當 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 中的 `@parallel = 1` 時，此值是在 0 到查詢平行處理原則程度的範圍內。

*RowsNumber*  
\[輸出\] 索引鍵/值組數目。

*CounterNames*  
\[輸出\] 包含索引鍵的字串資料。

*CounterNamesLength*  
\[輸出\] 每個索引鍵字串的長度。

*CounterValues*  
\[輸出\] 包含值的 64 位元整數資料。

## <a name="installexternallibrary"></a>InstallExternalLibrary

安裝程式庫。 此函數為選擇性，不需要實作。 預設實作是將程式庫內容複製到適當位置的檔案 (請參閱 [CREATE EXTERNAL LIBRARY](../../t-sql/statements/create-external-library-transact-sql.md))。 檔案名稱是程式庫名稱。

### <a name="syntax"></a>語法

```cpp
SQLRETURN InstallExternalLibrary(
    SQLGUID    SetupSessionId,
    SQLCHAR    *LibraryName,
    SQLINTEGER LibraryNameLength,
    SQLCHAR    *LibraryFile,
    SQLINTEGER LibraryFileLength,
    SQLCHAR    *LibraryInstallDirectory,
    SQLINTEGER LibraryInstallDirectoryLength,
    SQLCHAR    **LibraryError,
    SQLINTEGER *LibraryErrorLength
);
```

### <a name="arguments"></a>引數

*SetupSessionId*  
\[輸入\] 唯一識別此指令碼工作階段的 GUID。

*LibraryName*  
\[輸入\] 程式庫名稱。

*LibraryNameLength*  
\[輸入\] 程式庫名稱的長度。

*LibraryFile*  
\[輸入\] 程式庫檔案的路徑 (字串形式)，包含 [CREATE EXTERNAL LIBRARY](../../t-sql/statements/create-external-library-transact-sql.md) 所指定的二進位內容。

*LibraryFileLength*  
\[輸入\] LibraryFile 字串的長度。

*LibraryInstallDirectory：*  
\[輸入\] 用於安裝程式庫的根目錄。

*LibraryInstallDirectoryLength*  
\[輸入\] LibraryInstallDirectory 字串的長度。

*LibraryError*  
\[輸出\] 選擇性的輸出參數。 如果在程式庫安裝期間發生錯誤，則 LibraryError 會指向描述錯誤的字串。

*LibraryErrorLength*  
\[輸出\] LibraryError 字串的長度。

## <a name="uninstallexternallibrary"></a>UninstallExternalLibrary

解除安裝程式庫。 此函數為選擇性，不需要實作。 預設實作是復原 InstallExternalLibrary 預設實作所完成的工作。 預設實作會刪除 *LibraryInstallDirectory* 下 *LibraryName* 檔案的內容。

### <a name="syntax"></a>語法

```cpp
SQLRETURN UninstallExternalLibrary(
    SQLGUID    SetupSessionId,
    SQLCHAR    *LibraryName,
    SQLINTEGER LibraryNameLength,
    SQLCHAR    *LibraryInstallDirectory,
    SQLINTEGER LibraryInstallDirectoryLength,
    SQLCHAR    **LibraryError,
    SQLINTEGER *LibraryErrorLength
);
```

### <a name="arguments"></a>引數

*SetupSessionId*  
\[輸入\] 唯一識別此指令碼工作階段的 GUID。

*LibraryName*  
\[輸入\] 程式庫名稱

*LibraryNameLength*  
\[輸入\] 程式庫名稱的長度

*LibraryFile*  
\[輸入\] 程式庫檔案的路徑 (字串形式)，包含 CREATE EXTERNAL LIBRARY 所指定的二進位內容

*LibraryFileLength*  
\[輸入\] LibraryFile 字串的長度

*LibraryInstallDirectory*  
\[輸入\] 用於安裝程式庫的根目錄

*LibraryInstallDirectoryLength*  
\[輸入\] LibraryInstallDirectory 字串的長度。

*LibraryError*  
\[輸出\] 程式庫錯誤字串。

*LibraryErrorLength*  
\[輸出\] LibraryError 字串的長度。

## <a name="next-steps"></a>後續步驟

- [適用於 SQL Server 的 Microsoft Extensibility SDK for Java](../how-to/extensibility-sdk-java-sql-server.md)
- [Python 自訂執行階段](../../machine-learning/install/custom-runtime-python.md)
- [R 自訂執行階段](../../machine-learning/install/custom-runtime-r.md)。