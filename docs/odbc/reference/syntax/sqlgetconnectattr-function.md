---
description: SQLGetConnectAttr 函數
title: SQLGetConnectAttr 函式 |Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
apiname:
- SQLGetConnectOption
apilocation:
- sqlsrv32.dll
apitype: dllExport
f1_keywords:
- SQLGetConnectAttr
helpviewer_keywords:
- SQLGetConnectAttr function [ODBC]
ms.assetid: 2cb4ffa8-19d3-4664-8c2f-6682cdcc3f33
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 5da83146d0c6167a08f36a648749470830795703
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171860"
---
# <a name="sqlgetconnectattr-function"></a>SQLGetConnectAttr 函數
**一致性**  
 引進的版本： ODBC 3.0 標準合規性： ISO 92  
  
 **總結**  
 **SQLGetConnectAttr** 會傳回連接屬性的目前設定。  
  
> [!NOTE]
>  如需當 ODBC 3.x 應用程式與 ODBC 2.x *驅動程式搭配* 使用時，驅動程式管理員會將此函式對應至的詳細資訊，請參閱對應取代函式 [以取得應用程式的回溯相容性](../../../odbc/reference/develop-app/mapping-replacement-functions-for-backward-compatibility-of-applications.md)。  
  
## <a name="syntax"></a>語法  
  
```cpp  
  
SQLRETURN SQLGetConnectAttr(  
     SQLHDBC        ConnectionHandle,  
     SQLINTEGER     Attribute,  
     SQLPOINTER     ValuePtr,  
     SQLINTEGER     BufferLength,  
     SQLINTEGER *   StringLengthPtr);  
```  
  
## <a name="arguments"></a>引數  
 *ConnectionHandle*  
 [輸入] 連線控制代碼。  
  
 *Attribute*  
 輸出要取得的屬性。  
  
 *ValuePtr*  
 出記憶體的指標，要在其中傳回 *屬性* 所指定之屬性的目前值。 針對整數型別屬性，某些驅動程式可能只會寫入較低的32位或16位的緩衝區，並讓較高的順序位保持不變。 因此，在呼叫此函式之前，應用程式應該使用 SQLULEN 的緩衝區，並將值初始化為0。  
  
 如果 *ValuePtr* 為 Null， *StringLengthPtr* 仍會傳回位元組總數 (排除字元資料的 Null 終止字元) 可在 *ValuePtr* 所指向的緩衝區中傳回。  
  
 *BufferLength*  
 輸出如果 *attribute* 是 ODBC 定義的屬性，而且 *ValuePtr* 指向字元字串或二進位緩衝區，則這個引數應該是 \* *ValuePtr* 的長度。 如果 *attribute* 是 ODBC 定義的屬性，而且 *\* ValuePtr* 是整數，則會忽略 *BufferLength* 。 如果 *\* ValuePtr* 中的值是在呼叫 **SQLGetConnectAttrW**) 時 (的 Unicode 字串，則 *BufferLength* 引數必須是偶數。  
  
 如果 *屬性* 是驅動程式定義的屬性，則應用程式會藉由設定 *BufferLength* 引數來指出驅動程式管理員的屬性性質。 *BufferLength* 可以有下列值：  
  
-   如果 *\* ValuePtr* 是字元字串的指標， *BufferLength* 就是字串的長度。  
  
-   如果 *\* ValuePtr* 是二進位緩衝區的指標，則應用程式會將 SQL_LEN_BINARY_ATTR (*長度*) 宏的結果放在 *BufferLength* 中。 這會在 *BufferLength* 中放置負數值。  
  
-   如果 *\* ValuePtr* 是字元字串或二進位字串以外的值指標， *BufferLength* 應該會有 SQL_IS_POINTER 的值。  
  
-   如果 *\* ValuePtr* 包含固定長度的資料類型，則會適當地 SQL_IS_INTEGER 或 SQL_IS_UINTEGER *BufferLength* 。  
  
 *StringLengthPtr*  
 出緩衝區的指標，此緩衝區會傳回位元組總數 (不包括 null 終止字元) 可在 \* *ValuePtr* 中傳回。 如果屬性值是字元字串，且可傳回的位元組數目大於 *BufferLength* 減去 null 終止字元的長度，則 *\* ValuePtr* 中的資料會截斷為 *BufferLength* 減去 null 終止字元的長度，而且由驅動程式以 null 終止。  
  
## <a name="returns"></a>傳回  
 SQL_SUCCESS、SQL_SUCCESS_WITH_INFO、SQL_NO_DATA、SQL_ERROR 或 SQL_INVALID_HANDLE。  
  
## <a name="diagnostics"></a>診斷  
 當 **SQLGetConnectAttr** 傳回 SQL_ERROR 或 SQL_SUCCESS_WITH_INFO 時，您可以藉由呼叫 **SQLGetDiagRec** 和 *HandleType* （SQL_HANDLE_DBC）和 *ConnectionHandle* 的 *控制碼*，從診斷資料結構取得相關聯的 SQLSTATE 值。 下表列出 **SQLGetConnectAttr** 通常會傳回的 SQLSTATE 值，並在此函式的內容中說明每一個值;「 (DM) 」標記法優先于驅動程式管理員所傳回的 SQLSTATEs 描述。 除非另有說明，否則會 SQL_ERROR 與每個 SQLSTATE 值相關聯的傳回碼。  
  
|SQLSTATE|錯誤|描述|  
|--------------|-----------|-----------------|  
|01000|一般警告|驅動程式特定的告知性訊息。  (函數會傳回 SQL_SUCCESS_WITH_INFO。 ) |  
|01004|字串資料，右邊已截斷|ValuePtr 中傳回的資料 \* 已截斷為 *BufferLength* 減去 null 終止字元的長度。 Untruncated 字串值的長度會在 *\* StringLengthPtr* 中傳回。  (函數會傳回 SQL_SUCCESS_WITH_INFO。 ) |  
|08003|連接未開啟| (DM) 指定了需要開啟連接的 *屬性* 值。|  
|08S01|通訊連結失敗|在函式完成處理之前，驅動程式與連接驅動程式的資料來源之間的通訊連結失敗。|  
|HY000|一般錯誤|發生未定義特定 SQLSTATE 的錯誤，且未定義任何執行特定的 SQLSTATE。 由 **SQLGetDiagField** 中的引數 *MessageText* 所傳回的診斷資料結構所傳回的錯誤訊息會描述錯誤及其原因。|  
|HY001|記憶體配置錯誤|驅動程式無法配置支援執行或完成函式所需的記憶體。|  
|HY010|函數順序錯誤|針對 *ConnectionHandle* 呼叫 (DM) **SQLBrowseConnect** ，並傳回 SQL_NEED_DATA。 在 **SQLBrowseConnect** 傳回 SQL_SUCCESS_WITH_INFO 或 SQL_SUCCESS 之前，會呼叫此函數。<br /><br /> 針對 *ConnectionHandle* 呼叫 **SQLExecute**、 **SQLEXECDIRECT** 或 **SQLMoreResults** () DM，並傳回 SQL_PARAM_DATA_AVAILABLE。 在抓取所有資料流程參數的資料之前，會呼叫此函數。|  
|HY013|記憶體管理錯誤|無法處理函式呼叫，因為無法存取基礎記憶體物件，可能是因為記憶體不足的情況。|  
|HY090|不正確字串或緩衝區長度| (DM) *\* ValuePtr* 是一個字元字串，而 BufferLength 小於零但不等於 SQL_NTS。|  
|HY092|不正確屬性/選項識別碼|針對引數 *屬性* 指定的值對於驅動程式所支援的 ODBC 版本而言是不正確。|  
|HY114|驅動程式不支援連接層級的非同步函式執行| (DM) 應用程式嘗試針對不支援非同步連接作業的驅動程式，使用 SQL_ATTR_ASYNC_DBC_FUNCTIONS_ENABLE 來啟用非同步函式執行。|  
|HY117|由於未知的交易狀態，連接已暫止。 只允許中斷連接和唯讀功能。| (DM) 如需暫停狀態的詳細資訊，請參閱 [SQLEndTran 函數](../../../odbc/reference/syntax/sqlendtran-function.md)。|  
|HYC00|未執行選用功能|針對引數 *屬性* 指定的值是驅動程式所支援之 odbc 版本的有效 odbc 連接屬性，但驅動程式並不支援。|  
|HYT01|連接逾時已過期|連接逾時期間已在資料來源回應要求之前過期。 連接逾時期間是透過 **SQLSetConnectAttr**、SQL_ATTR_CONNECTION_TIMEOUT 設定。|  
|IM001|驅動程式不支援此功能| (DM) 對應至 *ConnectionHandle* 的驅動程式不支援此功能。|  
  
## <a name="comments"></a>註解  
 如需連接屬性的一般資訊，請參閱 [連接屬性](../../../odbc/reference/develop-app/connection-attributes.md)。  
  
 如需可設定的屬性清單，請參閱 [SQLSetConnectAttr](../../../odbc/reference/syntax/sqlsetconnectattr-function.md)。 請注意，如果 *屬性* 指定了會傳回字串的屬性，則 *ValuePtr* 必須是字串的緩衝區指標。 傳回字串的最大長度（包括 null 終止字元）將會是 *BufferLength* 位元組。  
  
 視屬性而定，應用程式不需要在呼叫 **SQLGetConnectAttr** 之前建立連接。 但是，如果呼叫 **SQLGetConnectAttr** ，且指定的屬性沒有預設值，而且尚未由先前的 **SQLSetConnectAttr** 呼叫設定，則 **SQLGetConnectAttr** 會傳回 SQL_NO_DATA。  
  
 如果 *屬性* SQL_ATTR_ TRACE 或 SQL_ATTR_ TRACEFILE， *ConnectionHandle* 不一定要是有效的，而且如果 *ConnectionHandle* 無效， **SQLGetConnectAttr** 將不會傳回 SQL_ERROR 或 SQL_INVALID_HANDLE。 這些屬性適用于所有連接。 如果另一個引數無效， **SQLGetConnectAttr** 會傳回 SQL_ERROR 或 SQL_INVALID_HANDLE。  
  
 雖然應用程式可以使用 **SQLSetConnectAttr** 來設定語句屬性，但應用程式無法使用 **SQLGetConnectAttr** 來取得語句屬性值;它必須呼叫 **SQLGetStmtAttr** 來取得語句屬性的設定。  
  
 SQL_ATTR_AUTO_IPD 和 SQL_ATTR_CONNECTION_DEAD 連接屬性都可以透過呼叫 **SQLGetConnectAttr** 來傳回，但是無法藉由呼叫 **SQLSetConnectAttr** 來設定。  
  
> [!NOTE]  
>  沒有對 **SQLGetConnectAttr** 的非同步支援。 在執行 **SQLGetConnectAttr** 時，驅動程式可以將資訊從伺服器傳送或要求的次數降到最低，藉以改善效能。  
  
## <a name="related-functions"></a>相關函數  
  
|如需下列資訊|請參閱|  
|---------------------------|---------|  
|傳回語句屬性的設定|[SQLGetStmtAttr 函數](../../../odbc/reference/syntax/sqlgetstmtattr-function.md)|  
|設定連接屬性|[SQLSetConnectAttr 函數](../../../odbc/reference/syntax/sqlsetconnectattr-function.md)|  
|設定環境屬性|[SQLSetEnvAttr 函數](../../../odbc/reference/syntax/sqlsetenvattr-function.md)|  
|設定語句屬性|[SQLSetStmtAttr 函數](../../../odbc/reference/syntax/sqlsetstmtattr-function.md)|  
  
## <a name="see-also"></a>另請參閱  
 [ODBC API 參考](../../../odbc/reference/syntax/odbc-api-reference.md)   
 [ODBC 標頭檔](../../../odbc/reference/install/odbc-header-files.md)
