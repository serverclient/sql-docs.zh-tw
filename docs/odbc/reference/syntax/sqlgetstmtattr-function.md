---
description: SQLGetStmtAttr 函數
title: SQLGetStmtAttr 函式 |Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
apiname:
- SQLGetStmtAttr
apilocation:
- sqlsrv32.dll
apitype: dllExport
f1_keywords:
- SQLGetStmtAttr
helpviewer_keywords:
- SQLGetStmtAttr function [ODBC]
ms.assetid: e321d460-e997-4527-aee6-207cf5a498e9
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: f6af1813068b51780c9181f3e3e98559769495fe
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172370"
---
# <a name="sqlgetstmtattr-function"></a>SQLGetStmtAttr 函數
**一致性**  
 引進的版本： ODBC 3.0 標準合規性： ISO 92  
  
 **總結**  
 **SQLGetStmtAttr** 會傳回語句屬性的目前設定。  
  
> [!NOTE]  
>  如需有關驅動程式管理員如何將此函式對應至 ODBC 3 的詳細資訊。*x* 應用程式正在使用 ODBC 2。*x* 驅動程式，請參閱對應取代函式 [以提供應用程式的回溯相容性](../../../odbc/reference/develop-app/mapping-replacement-functions-for-backward-compatibility-of-applications.md)。  
  
## <a name="syntax"></a>語法  
  
```cpp  
  
SQLRETURN SQLGetStmtAttr(  
     SQLHSTMT        StatementHandle,  
     SQLINTEGER      Attribute,  
     SQLPOINTER      ValuePtr,  
     SQLINTEGER      BufferLength,  
     SQLINTEGER *    StringLengthPtr);  
```  
  
## <a name="arguments"></a>引數  
 *StatementHandle*  
 輸出語句控制碼。  
  
 *Attribute*  
 輸出要取得的屬性。  
  
 *ValuePtr*  
 出緩衝區的指標，此緩衝區會傳回 *屬性* 中指定之屬性的值。  
  
 如果 *ValuePtr* 為 Null， *StringLengthPtr* 仍會傳回位元組總數 (排除字元資料的 Null 終止字元) 可在 *ValuePtr* 所指向的緩衝區中傳回。  
  
 *BufferLength*  
 輸出如果 *attribute* 是 ODBC 定義的屬性，而且 *ValuePtr* 指向字元字串或二進位緩衝區，則這個引數應該是 \* *ValuePtr* 的長度。 如果 *attribute* 是 ODBC 定義的屬性，而且 \* *ValuePtr* 是整數，則會忽略 *BufferLength* 。 如果在 *\* ValuePtr* 中傳回的值是在呼叫 **SQLGetStmtAttrW**) 時 (的 Unicode 字串，則 *BufferLength* 引數必須是偶數。  
  
 如果 *屬性* 是驅動程式定義的屬性，則應用程式會藉由設定 *BufferLength* 引數來指出驅動程式管理員的屬性性質。 *BufferLength* 可以有下列值：  
  
-   如果 *\* ValuePtr* 是字元字串的指標，則 *BufferLength* 是字串或 SQL_NTS 的長度。  
  
-   如果 *\* ValuePtr* 是二進位緩衝區的指標，則應用程式會將 SQL_LEN_BINARY_ATTR (*長度*) 宏的結果放在 *BufferLength* 中。 這會在 *BufferLength* 中放置負數值。  
  
-   如果 *\* ValuePtr* 是字元字串或二進位字串以外的值指標，則 *BufferLength* 應該具有 SQL_IS_POINTER 的值。  
  
-   如果 *\* ValuePtr* 包含固定長度的資料類型，則 *BufferLength* 會在適當的情況下 SQL_IS_INTEGER 或 SQL_IS_UINTEGER。  
  
 *StringLengthPtr*  
 出緩衝區的指標，此緩衝區會傳回位元組總數 (不包括 null 終止字元) 可在 *\* ValuePtr* 中傳回。 如果屬性值是字元字串，而且可傳回的位元組數目大於或等於 *BufferLength*，則 *\* ValuePtr* 中的資料會截斷為 *BufferLength* 減去 null 終止字元的長度，而且由驅動程式以 null 終止。  
  
## <a name="returns"></a>傳回  
 SQL_SUCCESS、SQL_SUCCESS_WITH_INFO、SQL_ERROR 或 SQL_INVALID_HANDLE。  
  
## <a name="diagnostics"></a>診斷  
 當 **SQLGetStmtAttr** 傳回 SQL_ERROR 或 SQL_SUCCESS_WITH_INFO 時，可以藉由呼叫 **SQLGetDiagRec** 和 *HandleType* （SQL_HANDLE_STMT）和 *StatementHandle* 的 *控制碼* 來取得相關聯的 SQLSTATE 值。 下表列出 **SQLGetStmtAttr** 常傳回的 SQLSTATE 值，並在此函式的內容中說明每一個值;「 (DM) 」標記法優先于驅動程式管理員所傳回的 SQLSTATEs 描述。 除非另有說明，否則會 SQL_ERROR 與每個 SQLSTATE 值相關聯的傳回碼。  
  
|SQLSTATE|錯誤|描述|  
|--------------|-----------|-----------------|  
|01000|一般警告|驅動程式特定的告知性訊息。  (函數會傳回 SQL_SUCCESS_WITH_INFO。 ) |  
|01004|字串資料，右邊已截斷|*\* ValuePtr* 中傳回的資料已截斷為 *BufferLength* 減去 null 終止字元的長度。 Untruncated 字串值的長度會在 **StringLengthPtr* 中傳回。  (函數會傳回 SQL_SUCCESS_WITH_INFO。 ) |  
|24000|指標狀態無效|引數 *屬性* 為 SQL_ATTR_ROW_NUMBER，且資料指標未開啟，或是資料指標位於結果集的開頭或結果集的結尾之後。|  
|HY000|一般錯誤|發生未定義特定 SQLSTATE 的錯誤，且未定義任何執行特定的 SQLSTATE。 **SQLGetDiagRec** 在引數 *MessageText* 中傳回的錯誤訊息會描述錯誤及其原因。|  
|HY001|記憶體配置錯誤|驅動程式無法配置支援執行或完成函式所需的記憶體。|  
|HY010|函數順序錯誤| (DM) 以非同步方式執行的函式，是與 *StatementHandle* 相關聯的連接控制碼所呼叫。 呼叫 **SQLGetStmtAttr** 函式時，仍在執行此非同步函式。<br /><br />  (DM) 針對 *StatementHandle* 呼叫非同步執行的函式，但在呼叫此函式時仍在執行。<br /><br /> 針對 *StatementHandle* 呼叫 (DM) **SQLExecute**、 **SQLExecDirect**、 **SQLBulkOperations** 或 **SQLSetPos** ，並傳回 SQL_NEED_DATA。 在傳送所有資料執行中參數或資料行的資料之前，會呼叫此函數。|  
|HY013|記憶體管理錯誤|無法處理函式呼叫，因為無法存取基礎記憶體物件，可能是因為記憶體不足的情況。|  
|HY090|不正確字串或緩衝區長度|*(DM) \*ValuePtr* 是一個字元字串，而 BufferLength 小於零，但不等於 SQL_NTS。|  
|HY092|不正確屬性/選項識別碼|針對引數 *屬性* 指定的值對於驅動程式所支援的 ODBC 版本而言是不正確。|  
|HY109|不正確資料指標位置|*屬性* 引數是 SQL_ATTR_ROW_NUMBER，且資料列已刪除或無法提取。|  
|HY117|由於未知的交易狀態，連接已暫止。 只允許中斷連接和唯讀功能。| (DM) 如需暫停狀態的詳細資訊，請參閱 [SQLEndTran 函數](../../../odbc/reference/syntax/sqlendtran-function.md)。|  
|HYC00|未執行選用功能|針對引數 *屬性* 指定的值是驅動程式所支援之 odbc 版本的有效 odbc 語句屬性，但驅動程式不支援此屬性。|  
|HYT01|連接逾時已過期|連接逾時期間已在資料來源回應要求之前過期。 連接逾時期間是透過 **SQLSetConnectAttr**、SQL_ATTR_CONNECTION_TIMEOUT 設定。|  
|IM001|驅動程式不支援此功能| (DM) 對應至 *StatementHandle* 的驅動程式不支援此功能。|  
  
## <a name="comments"></a>註解  
 如需語句屬性的一般資訊，請參閱 [語句屬性](../../../odbc/reference/develop-app/statement-attributes.md)。  
  
 對 **SQLGetStmtAttr** 的呼叫會在 *\* ValuePtr* 中傳回 *屬性* 中指定的語句屬性值。 該值可以是 SQLULEN 值，也可以是以 null 結束的字元字串。 如果值是 SQLULEN 值，某些驅動程式可能只會寫入較低的32位或16位的緩衝區，並讓較高的順序位保持不變。 因此，在呼叫此函式之前，應用程式應該使用 SQLULEN 的緩衝區，並將值初始化為0。 此外，也不會使用 *BufferLength* 和 *StringLengthPtr* 引數。 如果值是以 null 終止的字串，則應用程式會在 *BufferLength* 引數中指定該字串的最大長度，而驅動程式會在 *\* StringLengthPtr* 緩衝區中傳回該字串的長度。  
  
 允許呼叫 **SQLGetStmtAttr** 的應用程式使用 ODBC 2。*x* 驅動程式，驅動程式管理員會將 **SQLGetStmtAttr** 的呼叫對應至 **SQLGetStmtOption**。  
  
 下列語句屬性是唯讀的，因此可由 **SQLGetStmtAttr** 抓取，但不能由 **SQLSetStmtAttr** 設定：  
  
-   SQL_ATTR_IMP_PARAM_DESC  
  
-   SQL_ATTR_IMP_ROW_DESC  
  
-   SQL_ATTR_ROW_NUMBER  
  
 如需可設定和取出的屬性清單，請參閱 [SQLSetStmtAttr](../../../odbc/reference/syntax/sqlsetstmtattr-function.md)。  
  
## <a name="related-functions"></a>相關函數  
  
|如需下列資訊|請參閱|  
|---------------------------|---------|  
|傳回連接屬性的設定|[SQLGetConnectAttr 函數](../../../odbc/reference/syntax/sqlgetconnectattr-function.md)|  
|設定連接屬性|[SQLSetConnectAttr 函數](../../../odbc/reference/syntax/sqlsetconnectattr-function.md)|  
|設定語句屬性|[SQLSetStmtAttr 函數](../../../odbc/reference/syntax/sqlsetstmtattr-function.md)|  
  
## <a name="see-also"></a>另請參閱  
 [ODBC API 參考](../../../odbc/reference/syntax/odbc-api-reference.md)   
 [ODBC 標頭檔](../../../odbc/reference/install/odbc-header-files.md)
