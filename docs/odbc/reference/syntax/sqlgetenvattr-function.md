---
description: SQLGetEnvAttr 函式
title: SQLGetEnvAttr 函式 |Microsoft Docs
ms.custom: ''
ms.date: 07/18/2019
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
apiname:
- SQLGetEnvAttr
apilocation:
- sqlsrv32.dll
- odbc32.dll
apitype: dllExport
f1_keywords:
- SQLGetEnvAttr
helpviewer_keywords:
- SQLGetEnvAttr function [ODBC]
ms.assetid: 01f4590f-427a-4280-a1c3-18de9f7d86c1
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 457896a5c845bc42e446f1a115ee47863f7fced3
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171850"
---
# <a name="sqlgetenvattr-function"></a>SQLGetEnvAttr 函式
**一致性**  
 引進的版本： ODBC 3.0 標準合規性： ISO 92  
  
 **總結**  
 **SQLGetEnvAttr** 會傳回環境屬性的目前設定。  
  
## <a name="syntax"></a>語法  
  
```cpp  
  
SQLRETURN SQLGetEnvAttr(  
     SQLHENV        EnvironmentHandle,  
     SQLINTEGER     Attribute,  
     SQLPOINTER     ValuePtr,  
     SQLINTEGER     BufferLength,  
     SQLINTEGER *   StringLengthPtr);  
```  
  
## <a name="arguments"></a>引數  
 *EnvironmentHandle*  
 輸出環境控制碼。  
  
 *Attribute*  
 輸出要取得的屬性。  
  
 *ValuePtr*  
 出緩衝區的指標，此緩衝區會傳回 *屬性* 所指定之屬性的目前值。  
  
 如果 *ValuePtr* 為 Null， *StringLengthPtr* 仍會傳回位元組總數 (排除字元資料的 Null 終止字元) 可在 *ValuePtr* 所指向的緩衝區中傳回。  
  
 *BufferLength*  
 輸出如果 *ValuePtr* 指向字元字串，此引數應該是 \* *ValuePtr* 的長度。 如果 \* *ValuePtr* 是整數，則會忽略 *BufferLength* 。 如果 *\* ValuePtr* 是在呼叫 **SQLGetEnvAttrW**) 時 (的 Unicode 字串，則 *BufferLength* 引數必須是偶數。 如果屬性值不是字元字串，就不會使用 *BufferLength* 。  
  
 *StringLengthPtr*  
 出緩衝區的指標，此緩衝區會傳回位元組總數 (不包括 null 終止字元) 可在 *\* ValuePtr* 中傳回。 如果屬性值是字元字串，且可傳回的位元組數目大於或等於 *BufferLength*，則 ValuePtr 中的資料 \* 會截斷為 *BufferLength* 減去 null 終止字元的長度，而且由驅動程式以 null 終止。  
  
## <a name="returns"></a>傳回  
 SQL_SUCCESS、SQL_SUCCESS_WITH_INFO、SQL_NO_DATA、SQL_ERROR 或 SQL_INVALID_HANDLE。  
  
## <a name="diagnostics"></a>診斷  
 當 **SQLGetEnvAttr** 傳回 SQL_ERROR 或 SQL_SUCCESS_WITH_INFO 時，可以藉由呼叫 **SQLGetDiagRec** 和 *HandleType* （SQL_HANDLE_ENV）和 *EnvironmentHandle* 的 *控制碼* 來取得相關聯的 SQLSTATE 值。 下表列出 **SQLGetEnvAttr** 常傳回的 SQLSTATE 值，並在此函式的內容中說明每一個值;「 (DM) 」標記法優先于驅動程式管理員所傳回的 SQLSTATEs 描述。 除非另有說明，否則會 SQL_ERROR 與每個 SQLSTATE 值相關聯的傳回碼。  
  
|SQLSTATE|錯誤|描述|  
|--------------|-----------|-----------------|  
|01000|一般警告|驅動程式特定的告知性訊息。  (函數會傳回 SQL_SUCCESS_WITH_INFO。 ) |  
|01004|字串資料，右邊已截斷|ValuePtr 中傳回的資料 \* 已截斷為 *BufferLength* 減去 null 終止字元。 Untruncated 字串值的長度會在 **StringLengthPtr* 中傳回。  (函數會傳回 SQL_SUCCESS_WITH_INFO。 ) |  
|HY000|一般錯誤|發生未定義特定 SQLSTATE 的錯誤，且未定義任何執行特定的 SQLSTATE。 **SQLGetDiagRec** 在 *\* MessageText* 緩衝區中傳回的錯誤訊息會描述錯誤及其原因。|  
|HY001|記憶體配置錯誤|驅動程式無法配置支援執行或完成函式所需的記憶體。|  
|HY010|函數順序錯誤|尚未透過 **SQLSetEnvAttr** 設定 (DM) **SQL_ATTR_ODBC_VERSION** 。 如果您使用 **SQLAllocHandleStd**，就不需要明確設定 **SQL_ATTR_ODBC_VERSION** 。|  
|HY013|記憶體管理錯誤|無法處理函式呼叫，因為無法存取基礎記憶體物件，可能是因為記憶體不足的情況。|  
|HY092|不正確屬性/選項識別碼|針對引數 *屬性* 指定的值對於驅動程式所支援的 ODBC 版本而言是不正確。|  
|HY117|由於未知的交易狀態，連接已暫止。 只允許中斷連接和唯讀功能。| (DM) 如需暫停狀態的詳細資訊，請參閱 [SQLEndTran 函數](../../../odbc/reference/syntax/sqlendtran-function.md)。|  
|HYC00|未執行選用功能|針對引數 *屬性* 指定的值是驅動程式所支援之 odbc 版本的有效 odbc 環境屬性，但驅動程式不支援此屬性。|  
|IM001|驅動程式不支援此功能| (DM) 對應至 *EnvironmentHandle* 的驅動程式不支援此功能。|  
  
## <a name="comments"></a>註解  
 如需屬性的清單，請參閱 [SQLSetEnvAttr](../../../odbc/reference/syntax/sqlsetenvattr-function.md)。 沒有任何驅動程式特定的環境屬性。 如果 *屬性* 指定會傳回字串的屬性，則 *ValuePtr* 必須是要傳回字串之緩衝區的指標。 字串的最大長度（包括 null 終止位元組）將會是 *BufferLength* 位元組。  
  
 在配置和釋放環境控制碼之間的任何時間都可以呼叫 **SQLGetEnvAttr** 。 針對環境，應用程式所設定的所有環境屬性都會持續存在，直到在 *EnvironmentHandle* 上呼叫 **SQLFreeHandle** 時， *HandleType* 為 SQL_HANDLE_ENV。 您可以在 ODBC 3.x 中同時配置一個 *以上的環境控制碼。* 當另一個環境已配置時，一個環境的環境屬性不會受到影響。  
  
> [!NOTE]
>  符合標準的應用程式支援 SQL_ATTR_OUTPUT_NTS 環境屬性。 當呼叫 **SQLGetEnvAttr** 時，ODBC *3.X 驅動程式* 管理員一律會傳回這個屬性的 SQL_TRUE。 SQL_ATTR_OUTPUT_NTS 只能透過呼叫 **SQLSetEnvAttr** SQL_TRUE 設定為。  
  
## <a name="related-functions"></a>相關函數  
  
|如需下列資訊|請參閱|  
|---------------------------|---------|  
|傳回連接屬性的設定|[SQLGetConnectAttr 函數](../../../odbc/reference/syntax/sqlgetconnectattr-function.md)|  
|傳回語句屬性的設定|[SQLGetStmtAttr 函數](../../../odbc/reference/syntax/sqlgetstmtattr-function.md)|  
|設定連接屬性|[SQLSetConnectAttr 函數](../../../odbc/reference/syntax/sqlsetconnectattr-function.md)|  
|設定環境屬性|[SQLSetEnvAttr 函數](../../../odbc/reference/syntax/sqlsetenvattr-function.md)|  
|設定語句屬性|[SQLSetStmtAttr 函數](../../../odbc/reference/syntax/sqlsetstmtattr-function.md)|  
  
## <a name="see-also"></a>另請參閱  
 [ODBC API 參考](../../../odbc/reference/syntax/odbc-api-reference.md)   
 [ODBC 標頭檔](../../../odbc/reference/install/odbc-header-files.md)
