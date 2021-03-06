---
description: SQLGetTypeInfo 函數
title: SQLGetTypeInfo 函式 |Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
apiname:
- SQLGetTypeInfo
apilocation:
- sqlsrv32.dll
apitype: dllExport
f1_keywords:
- SQLGetTypeInfo
helpviewer_keywords:
- SQLGetTypeInfo function [ODBC]
ms.assetid: bdedb044-8924-4ca4-85f3-8b37578e0257
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 47ff65a1c7912aef196c79b9b26c8286fb05fef2
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88421192"
---
# <a name="sqlgettypeinfo-function"></a>SQLGetTypeInfo 函數
**一致性**  
 引進的版本： ODBC 1.0 標準合規性： ISO 92  
  
 **總結**  
 **SQLGetTypeInfo** 會傳回資料來源所支援資料類型的相關資訊。 驅動程式會以 SQL 結果集的形式傳回信息。 資料類型適用于資料定義語言 (DDL) 語句。  
  
> [!IMPORTANT]  
>  應用程式必須使用**ALTER TABLE**和**CREATE TABLE**語句中**SQLGetTypeInfo**結果集的 TYPE_NAME 資料行中所傳回的型別名稱。 **SQLGetTypeInfo** 可能會傳回多個資料列，在 DATA_TYPE 資料行中具有相同的值。  
  
## <a name="syntax"></a>語法  
  
```cpp  
  
SQLRETURN SQLGetTypeInfo(  
     SQLHSTMT      StatementHandle,  
     SQLSMALLINT   DataType);  
```  
  
## <a name="arguments"></a>引數  
 *StatementHandle*  
 輸出結果集的語句控制碼。  
  
 *DataType*  
 輸出SQL 資料類型。 這必須是附錄 D：資料類型的 [ [SQL 資料類型](../../../odbc/reference/appendixes/sql-data-types.md) ] 區段中的其中一個值，或是驅動程式特定的 sql 資料類型。 SQL_ALL_TYPES 指定應該傳回所有資料類型的相關資訊。  
  
## <a name="returns"></a>傳回  
 SQL_SUCCESS、SQL_SUCCESS_WITH_INFO、SQL_STILL_EXECUTING、SQL_ERROR 或 SQL_INVALID_HANDLE。  
  
## <a name="diagnostics"></a>診斷  
 當**SQLGetTypeInfo**傳回 SQL_ERROR 或 SQL_SUCCESS_WITH_INFO 時，可以藉由呼叫**SQLGetDiagRec**和*HandleType* （SQL_HANDLE_STMT）和*StatementHandle*的*控制碼*來取得相關聯的 SQLSTATE 值。 下表列出 **SQLGetTypeInfo** 常傳回的 SQLSTATE 值，並在此函式的內容中說明每一個值;「 (DM) 」標記法優先于驅動程式管理員所傳回的 SQLSTATEs 描述。 除非另有說明，否則會 SQL_ERROR 與每個 SQLSTATE 值相關聯的傳回碼。  
  
|SQLSTATE|錯誤|描述|  
|--------------|-----------|-----------------|  
|01000|一般警告|驅動程式特定的告知性訊息。  (函數會傳回 SQL_SUCCESS_WITH_INFO。 ) |  
|01S02|選項值已變更|指定的語句屬性無效，因為執行中的工作狀況，所以暫時取代了類似的值。  (呼叫 **SQLGetStmtAttr** 來判斷暫時替代值。 ) 替代值對 *StatementHandle* 有效，直到資料指標關閉為止。 可以變更的語句屬性包括： SQL_ATTR_CONCURRENCY、SQL_ATTR_CURSOR_TYPE、SQL_ATTR_KEYSET_SIZE、SQL_ATTR_MAX_LENGTH、SQL_ATTR_MAX_ROWS、SQL_ATTR_QUERY_TIMEOUT 和 SQL_ATTR_SIMULATE_CURSOR。  (函數會傳回 SQL_SUCCESS_WITH_INFO。 ) |  
|08S01|通訊連結失敗|在函式完成處理之前，驅動程式與連接驅動程式的資料來源之間的通訊連結失敗。|  
|24000|指標狀態無效|在 StatementHandle 上開啟了資料指標 *，* 且已呼叫 **SQLFetch** 或 **SQLFetchScroll** 。 如果 **SQLFetch** 或 **SQLFetchScroll** 尚未傳回 SQL_NO_DATA，驅動程式管理員會傳回此錯誤，如果 **SQLFetch** 或 **SQLFetchScroll** 已傳回 SQL_NO_DATA，驅動程式會傳回此錯誤。<br /><br /> 在 *StatementHandle*上開啟了結果集，但尚未呼叫 **SQLFetch** 或 **SQLFetchScroll** 。|  
|40001|序列化失敗|因為另一個交易發生資源鎖死，所以已回復交易。|  
|40003|語句完成不明|在此函數執行期間，相關聯的連接失敗，而且無法判斷交易的狀態。|  
|HY000|一般錯誤|發生未定義特定 SQLSTATE 的錯誤，且未定義任何執行特定的 SQLSTATE。 **SQLGetDiagRec**在* \* MessageText*緩衝區中傳回的錯誤訊息會描述錯誤及其原因。|  
|HY001|記憶體配置錯誤|驅動程式無法配置支援執行或完成函式所需的記憶體。|  
|HY004|不正確 SQL 資料類型|為 *引數資料類型* 指定的值不是有效的 ODBC SQL 資料類型識別碼，也不是驅動程式所支援的驅動程式特定資料類型識別碼。|  
|HY008|已取消作業|已針對*StatementHandle*啟用非同步處理，然後在完成執行之前呼叫函式，並在*StatementHandle*上呼叫**SQLCancel**或**SQLCancelHandle** 。 然後在 *StatementHandle*上再次呼叫該函式。<br /><br /> 已呼叫函式，並在它完成執行之前，從多執行緒應用程式中的不同執行緒呼叫*StatementHandle*上的**SQLCancel**或**SQLCancelHandle** 。|  
|HY010|函數順序錯誤| (DM) 以非同步方式執行的函式，是與 *StatementHandle*相關聯的連接控制碼所呼叫。 呼叫 **SQLGetTypeInfo** 函式時，仍在執行此非同步函式。<br /><br /> 針對*StatementHandle*呼叫**SQLExecute**、 **SQLEXECDIRECT**或**SQLMoreResults** () DM，並傳回 SQL_PARAM_DATA_AVAILABLE。 在抓取所有資料流程參數的資料之前，會呼叫此函數。<br /><br />  (DM) 以非同步方式執行的函式 (不是針對 *StatementHandle* 呼叫這個) ，而且在呼叫此函式時仍在執行。<br /><br /> 針對*StatementHandle*呼叫 (DM) **SQLExecute**、 **SQLExecDirect**、 **SQLBulkOperations**或**SQLSetPos** ，並傳回 SQL_NEED_DATA。 在傳送所有資料執行中參數或資料行的資料之前，會呼叫此函數。|  
|HY013|記憶體管理錯誤|無法處理函式呼叫，因為無法存取基礎記憶體物件，可能是因為記憶體不足的情況。|  
|HY117|由於未知的交易狀態，連接已暫止。 只允許中斷連接和唯讀功能。| (DM) 如需暫停狀態的詳細資訊，請參閱 [SQLEndTran 函數](../../../odbc/reference/syntax/sqlendtran-function.md)。|  
|HYC00|未執行選用功能|驅動程式或資料來源不支援 SQL_ATTR_CONCURRENCY 和 SQL_ATTR_CURSOR_TYPE 語句屬性的目前設定組合。<br /><br /> SQL_ATTR_USE_BOOKMARKS 語句屬性已設定為 SQL_UB_VARIABLE，而且 SQL_ATTR_CURSOR_TYPE 語句屬性已設定為驅動程式不支援書簽的資料指標類型。|  
|HYT00|已超過逾時的設定|查詢超時時間已在資料來源傳回結果集之前過期。 Timeout 期限是透過 **SQLSetStmtAttr**、SQL_ATTR_QUERY_TIMEOUT 設定。|  
|HYT01|連接逾時已過期|連接逾時期間已在資料來源回應要求之前過期。 連接逾時期間是透過 **SQLSetConnectAttr**、SQL_ATTR_CONNECTION_TIMEOUT 設定。|  
|IM001|驅動程式不支援此功能| (DM) 對應至 *StatementHandle* 的驅動程式不支援此功能。|  
|IM017|非同步通知模式中的輪詢已停用|每當使用通知模型時，就會停用輪詢。|  
|IM018|尚未呼叫**SQLCompleteAsync**來完成這個控制碼上先前的非同步作業。|如果控制碼上先前的函式呼叫傳回 SQL_STILL_EXECUTING，而且如果啟用了通知模式，則必須在控制碼上呼叫 **SQLCompleteAsync** ，以進行後置處理，並完成作業。|  
  
## <a name="comments"></a>註解  
 **SQLGetTypeInfo** 會以標準結果集的形式傳回結果，然後依 DATA_TYPE 排序，然後將資料類型對應至對應的 ODBC SQL 資料類型。 資料來源所定義的資料類型優先于使用者定義的資料類型。 因此，排序次序不一定是一致的，但可以一般化為 DATA_TYPE 先，然後以遞增的方式 TYPE_NAME。 例如，假設資料來源定義了整數和計數器資料類型，其中計數器是自動遞增的，而且也已定義使用者定義的資料類型 WHOLENUM。 這些會以 order 整數、WHOLENUM 和 COUNTER 傳回，因為 WHOLENUM 會與 ODBC SQL 資料類型緊密對應 SQL_INTEGER，而自動遞增資料類型（即使資料來源支援）不會與 ODBC SQL 資料類型緊密對應。 如需有關如何使用此資訊的詳細資訊，請參閱 [DDL 語句](../../../odbc/reference/develop-app/ddl-statements.md)。  
  
 如果 *DataType* 引數指定的資料類型對於驅動程式所支援的 ODBC 版本而言是有效的，但驅動程式不支援此資料類型，則會傳回空的結果集。  
  
> [!NOTE]  
>  如需 ODBC 目錄函數的一般使用、引數和傳回資料的詳細資訊，請參閱 [目錄函數](../../../odbc/reference/develop-app/catalog-functions.md)。  
  
 下列資料行已經重新命名為 ODBC 3。*x*。 資料行名稱的變更不會影響回溯相容性，因為應用程式會依資料行編號進行系結。  
  
|ODBC 2.0 資料行|ODBC 3。*x* 資料行|  
|---------------------|-----------------------|  
|PRECISION|COLUMN_SIZE|  
|MONEY|FIXED_PREC_SCALE|  
|AUTO_INCREMENT|AUTO_UNIQUE_VALUE|  
  
 下列資料行已加入至 **SQLGetTypeInfo** for ODBC 3 所傳回的結果集。*x*：  
  
-   SQL_DATA_TYPE  
  
-   INTERVAL_PRECISION  
  
-   SQL_DATETIME_SUB  
  
-   NUM_PREC_RADIX  
  
 下表列出結果集中的資料行。 除了資料行19以外的其他資料行 (INTERVAL_PRECISION) 可以由驅動程式定義。 應用程式應該從結果集的結尾算下，而不是指定明確的序數位置，藉以取得驅動程式特定資料行的存取權。 如需詳細資訊，請參閱 [目錄函數所傳回的資料](../../../odbc/reference/develop-app/data-returned-by-catalog-functions.md)。  
  
> [!NOTE]  
>  **SQLGetTypeInfo** 可能不會傳回所有資料類型。 例如，驅動程式可能不會傳回使用者定義的資料類型。 應用程式可以使用任何有效的資料類型，不論 **SQLGetTypeInfo**是否傳回。 **SQLGetTypeInfo**所傳回的資料類型是資料來源所支援的資料類型。 它們是用來在資料定義語言 (DDL) 語句中使用。 驅動程式可以使用 **SQLGetTypeInfo**所傳回之類型以外的資料類型，傳回結果集資料。 在建立目錄函數的結果集時，驅動程式可能會使用資料來源不支援的資料類型。  
  
|資料行名稱|資料行<br /><br /> number|資料類型|註解|  
|-----------------|-----------------------|---------------|--------------|  
|TYPE_NAME (ODBC 2.0) |1|Varchar not Null|與資料來源相關的資料類型名稱;例如，「CHAR ( # A1」、「VARCHAR ( # A3」、「MONEY」、「LONG VARBINARY」或「CHAR ( ) FOR BIT DATA」。 應用程式必須在 **CREATE TABLE** 和 **ALTER TABLE** 語句中使用這個名稱。|  
|DATA_TYPE (ODBC 2.0) |2|Smallint 非 NULL|SQL 資料類型。 這可以是 ODBC SQL 資料類型或驅動程式特定的 SQL 資料類型。 若為 datetime 或 interval 資料類型，這個資料行會傳回精確的資料類型 (例如 SQL_TYPE_TIME 或 SQL_INTERVAL_YEAR_TO_MONTH) 。 如需有效 ODBC SQL 資料類型的清單，請參閱附錄 D：資料類型中的 [SQL 資料類型](../../../odbc/reference/appendixes/sql-data-types.md) 。 如需有關驅動程式特定的 SQL 資料類型的詳細資訊，請參閱驅動程式的檔。|  
|COLUMN_SIZE (ODBC 2.0) |3|整數|伺服器針對此資料類型所支援的最大資料行大小。 若為數值資料，則為最大有效位數。 若為字串資料，這是以字元為單位的長度。 針對 datetime 資料類型，這是字串表示的字元長度（以字元為單位）， (假設小數秒陣列件的最大允許精確度) 。 資料行大小不適用的資料類型會傳回 Null。 針對 interval 資料類型，這是間隔常值的字元標記法中的字元數 (如間隔的有效位數所定義;請參閱附錄 D：資料類型) 的 [間隔資料類型長度](../../../odbc/reference/appendixes/interval-data-type-length.md) 。<br /><br /> 如需資料行大小的詳細資訊，請參閱附錄 D：資料類型中的資料 [行大小、小數位數、傳輸八位長度和顯示大小](../../../odbc/reference/appendixes/column-size-decimal-digits-transfer-octet-length-and-display-size.md) 。|  
|LITERAL_PREFIX (ODBC 2.0) |4|Varchar|用來做為常值前置詞的字元或字元;例如，針對字元資料類型使用單引號 ( ' ) ，針對二進位資料類型則為 0x;如果資料類型不適用常值前置詞，則會傳回 Null。|  
|LITERAL_SUFFIX (ODBC 2.0) |5|Varchar|用來終止常值的字元或字元;例如，字元資料類型的單引號 ( ' ) ;如果不適用常值尾碼的資料類型，則會傳回 Null。|  
|CREATE_PARAMS (ODBC 2.0) |6|Varchar|關鍵字清單（以逗號分隔），對應到應用程式在使用 TYPE_NAME 欄位中傳回的名稱時，可在括弧中指定的每個參數。 清單中的關鍵字可以是下列任何一項：長度、有效位數或小數位數。 它們會依照語法要求使用的順序顯示。 例如，DECIMAL 的 CREATE_PARAMS 會是 "precision，scale";VARCHAR 的 CREATE_PARAMS 會等於「長度」。 如果資料類型定義沒有任何參數，則會傳回 Null;例如，整數。<br /><br /> 驅動程式會以使用它的國家/地區語言來提供 CREATE_PARAMS 文字。|  
|可為 Null (ODBC 2.0) |7|Smallint 非 NULL|資料類型是否接受 Null 值：<br /><br /> 如果資料類型不接受 Null 值，則為 SQL_NO_NullS。<br /><br /> 如果資料類型接受 Null 值，則為 SQL_NullABLE。<br /><br /> SQL_NullABLE_UNKNOWN 如果不知道資料行是否接受 Null 值，則為。|  
|CASE_SENSITIVE (ODBC 2.0) |8|Smallint 非 NULL|在定序和比較中，字元資料類型是否區分大小寫：<br /><br /> 如果資料類型是字元資料類型，則 SQL_TRUE，且區分大小寫。<br /><br /> 如果資料類型不是字元資料類型，或不區分大小寫，則為 SQL_FALSE。|  
|可搜尋 (ODBC 2.0) |9|Smallint 非 NULL|在 **WHERE** 子句中使用資料類型的方式：<br /><br /> 如果資料行不能用在 **WHERE** 子句中，則為 SQL_PRED_NONE。  (這與 ODBC 2 中的 SQL_UNSEARCHABLE 值相同。*x*. ) <br /><br /> 如果資料行可以在 **WHERE** 子句中使用，則為 SQL_PRED_CHAR，但僅適用 **于 LIKE** 述詞。  (這與 ODBC 2 中的 SQL_LIKE_ONLY 值相同。*x*. ) <br /><br /> SQL_PRED_BASIC，如果資料行可以在 **WHERE** 子句中搭配所有的比較運算子（ **如** (比較、量化比較、 **BETWEEN**、 **DISTINCT**、 **in**、 **MATCH**和 **UNIQUE**) ）使用。  (這與 ODBC 2 中的 SQL_ALL_EXCEPT_LIKE 值相同。*x*. ) <br /><br /> 如果資料行可以在 **WHERE** 子句中搭配任何比較運算子使用，則為 SQL_SEARCHABLE。|  
|UNSIGNED_ATTRIBUTE (ODBC 2.0) |10|Smallint|資料類型是否未簽署：<br /><br /> 如果資料類型不帶正負號，則為 SQL_TRUE。<br /><br /> 如果資料類型已簽署，則為 SQL_FALSE。<br /><br /> 如果屬性不適用於資料類型，或資料類型不是數值，則會傳回 Null。|  
|FIXED_PREC_SCALE (ODBC 2.0) |11|Smallint 非 NULL|資料類型是否具有預先定義的固定有效位數和小數位數 (是資料來源特定的) ，例如 money 資料類型：<br /><br /> SQL_TRUE 是否具有預先定義的固定有效位數和小數位數。<br /><br /> SQL_FALSE （如果沒有預先定義的固定有效位數和小數位數）。|  
|AUTO_UNIQUE_VALUE (ODBC 2.0) |12|Smallint|資料類型是否為自動遞增：<br /><br /> 如果資料類型為自動遞增，則為 SQL_TRUE。<br /><br /> 如果資料類型不是自動遞增，則為 SQL_FALSE。<br /><br /> 如果屬性不適用於資料類型，或資料類型不是數值，則會傳回 Null。<br /><br /> 應用程式可以將值插入具有這個屬性的資料行中，但通常無法更新資料行中的值。<br /><br /> 當插入自動遞增資料行時，在插入時，會在資料行中插入唯一值。 遞增未定義，但是資料來源特定的。 應用程式不應假設自動遞增資料行是從任何特定的點開始，或是以任何特定的值遞增。|  
|LOCAL_TYPE_NAME (ODBC 2.0) |13|Varchar|資料類型之資料來源相依名稱的當地語系化版本。 如果資料來源不支援當地語系化名稱，便傳回 NULL。 此名稱僅供顯示，例如對話方塊中。|  
|MINIMUM_SCALE (ODBC 2.0) |14|Smallint|資料來源上資料類型的最小小數位數。 如果資料類型有固定的小數位數，MINIMUM_SCALE 和 MAXIMUM_SCALE 資料行都包含這個值。 例如，SQL_TYPE_TIMESTAMP 資料行的小數秒數可能會有固定的小數位數。 當小數位數不適用時，會傳回 NULL。 如需詳細資訊，請參閱附錄 D：資料類型中的資料 [行大小、小數位數、傳輸八位長度和顯示大小](../../../odbc/reference/appendixes/column-size-decimal-digits-transfer-octet-length-and-display-size.md) 。|  
|MAXIMUM_SCALE (ODBC 2.0) |15|Smallint|資料來源上資料類型的最大刻度。 當小數位數不適用時，會傳回 NULL。 如果未在資料來源上個別定義最大刻度，但是定義為與最大有效位數相同，則此資料行包含與 COLUMN_SIZE 資料行相同的值。 如需詳細資訊，請參閱附錄 D：資料類型中的資料 [行大小、小數位數、傳輸八位長度和顯示大小](../../../odbc/reference/appendixes/column-size-decimal-digits-transfer-octet-length-and-display-size.md) 。|  
|SQL_DATA_TYPE (ODBC 3.0) |16|Smallint 非 Null|出現在描述項的 SQL_DESC_TYPE 欄位中的 SQL 資料類型值。 除了 interval 和 datetime 資料類型以外，這個資料行與 DATA_TYPE 資料行相同。<br /><br /> 針對 interval 和 datetime 資料類型，結果集中的 SQL_DATA_TYPE 欄位會傳回 SQL_INTERVAL 或 SQL_DATETIME，而 SQL_DATETIME_SUB 欄位則會傳回特定間隔或日期時間資料類型的子代碼。  (參閱 [附錄 D：資料類型](../../../odbc/reference/appendixes/appendix-d-data-types.md)。 ) |  
|SQL_DATETIME_SUB (ODBC 3.0) |17|Smallint|當 SQL_DATA_TYPE 的值是 SQL_DATETIME 或 SQL_INTERVAL 時，此資料行就會包含日期時間/間隔子代碼。 若為 datetime 和 interval 以外的資料類型，此欄位為 Null。<br /><br /> 針對 interval 或 datetime 資料類型，結果集中的 SQL_DATA_TYPE 欄位會傳回 SQL_INTERVAL 或 SQL_DATETIME，而 SQL_DATETIME_SUB 欄位將會傳回特定間隔或日期時間資料類型的子代碼。  (參閱 [附錄 D：資料類型](../../../odbc/reference/appendixes/appendix-d-data-types.md)。 ) |  
|NUM_PREC_RADIX (ODBC 3.0) |18|整數|如果資料類型是近似的數數值型別，這個資料行就會包含值2，指出 COLUMN_SIZE 指定位數。 如果是精確數數值型別，這個資料行會包含值10，表示 COLUMN_SIZE 指定十進位數的數位。 否則，這個資料行就是 NULL。|  
|INTERVAL_PRECISION (ODBC 3.0) |19|Smallint|如果資料類型是 interval 資料類型，則此資料行包含間隔前置精確度的值。  (請參閱附錄 D：資料類型中的 [間隔資料類型有效位數](../../../odbc/reference/appendixes/interval-data-type-precision.md) ) 。否則，這個資料行是 Null。|  
  
 屬性資訊可以套用至資料類型或結果集內的特定資料行。 **SQLGetTypeInfo** 會傳回與資料類型相關聯之屬性的相關資訊; **SQLColAttribute** 會傳回與結果集中的資料行相關聯之屬性的資訊。  
  
## <a name="related-functions"></a>相關函數  
  
|如需下列資訊|請參閱|  
|---------------------------|---------|  
|將緩衝區系結至結果集內的資料行|[SQLBindCol 函數](../../../odbc/reference/syntax/sqlbindcol-function.md)|  
|取消語句處理|[SQLCancel 函式](../../../odbc/reference/syntax/sqlcancel-function.md)|  
|傳回結果集中資料行的相關資訊|[SQLColAttribute 函式](../../../odbc/reference/syntax/sqlcolattribute-function.md)|  
|提取資料區塊或滾動整個結果集|[SQLFetchScroll 函式](../../../odbc/reference/syntax/sqlfetchscroll-function.md)|  
|以順向方向提取單一資料列或資料區塊|[SQLFetch 函式](../../../odbc/reference/syntax/sqlfetch-function.md)|  
|傳回驅動程式或資料來源的相關資訊|[SQLGetInfo 函數](../../../odbc/reference/syntax/sqlgetinfo-function.md)|  
  
## <a name="see-also"></a>另請參閱  
 [ODBC API 參考](../../../odbc/reference/syntax/odbc-api-reference.md)   
 [ODBC 標頭檔](../../../odbc/reference/install/odbc-header-files.md)
