---
description: 陳述式轉換
title: 語句轉換 |Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
helpviewer_keywords:
- transitioning states [ODBC], statement
- state transitions [ODBC], statement
- statement transitions [ODBC]
ms.assetid: 3d70e0e3-fe83-4b4d-beac-42c82495a05b
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: 3515b1d6aea4cab66bc01ee3d071727e6cb8f447
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88386514"
---
# <a name="statement-transitions"></a>陳述式轉換
ODBC 語句具有下列狀態。  
  
|State|描述|  
|-----------|-----------------|  
|S0|未配置的語句。  (連接狀態必須是 C4、C5 或 C6。 如需詳細資訊，請參閱 [連接轉換](../../../odbc/reference/appendixes/connection-transitions.md)。 ) |  
|S1|已配置語句。|  
|S2|備妥的語句。 將不會建立任何結果集。|  
|S3|備妥的語句。 將會建立 (可能是空的) 結果集。|  
|S4|語句已執行，但未建立任何結果集。|  
|S5|執行的語句和 (可能是空的) 結果集已建立。 資料指標是開啟的，而且位於結果集的第一個資料列之前。|  
|S6|資料指標位於 **SQLFetch** 或 **SQLFetchScroll**。|  
|S7|以 **SQLExtendedFetch**定位的資料指標。|  
|S8|函數需要資料。 尚未呼叫**SQLParamData** 。|  
|S9|函數需要資料。 尚未呼叫**SQLPutData** 。|  
|S10|函數需要資料。 已呼叫**SQLPutData** 。|  
|S11|仍在執行中。 在以非同步方式執行的函式傳回 SQL_STILL_EXECUTING 之後，語句會維持在此狀態。 當任何接受語句控制碼的函式正在執行時，語句會暫時處於此狀態。 狀態 S11 中的暫時居住不會顯示在 **SQLCancel**的狀態資料表以外的任何狀態資料表中。 當語句在狀態 S11 中暫時處於狀態時，您可以從另一個執行緒呼叫 **SQLCancel** 來取消函式。|  
|S12|非同步執行已取消。 在 S12 中，應用程式必須呼叫已取消的函式，直到它傳回 SQL_STILL_EXECUTING 以外的值為止。 只有當函式傳回 SQL_ERROR，且已取消) 的 SQLSTATE HY008 (作業時，才會成功取消函數。 如果它傳回任何其他值（例如 SQL_SUCCESS），取消作業就會失敗，且函式會正常執行。|  
  
 狀態 S2 和 S3 稱為「準備的」狀態，會以資料指標狀態的方式，將 S5 視為 S7，狀態是透過 S10 來 S8，並將 S11 和 S12 視為非同步狀態。 在每個群組中，只有當群組中的每個狀態都不同時，才會分別顯示轉換;在大部分的情況下，每個群組中每個狀態的轉換都相同。  
  
 下表顯示每個 ODBC 函數對語句狀態的影響。  
  
## <a name="sqlallochandle"></a>SQLAllocHandle  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|--[1]，[5]，[6]|--[5]|--[5]|--[5]|--[5]|--[5]|--[5]|  
|--[2]，[5]|--[5]|--[5]|--[5]|--[5]|--[5]|--[5]|  
|S1 [3]|--[5]|--[5]|--[5]|--[5]|--[5]|--[5]|  
|--[4]，[5]|--[5]|--[5]|--[5]|--[5]|--[5]|--[5]|  
  
 [1] 當 SQL_HANDLE_ENV *HandleType* 時，這個資料列會顯示轉換。  
  
 [2] 當 SQL_HANDLE_DBC *HandleType* 時，這個資料列會顯示轉換。  
  
 [3] 當 SQL_HANDLE_STMT *HandleType* 時，這個資料列會顯示轉換。  
  
 [4] 當 SQL_HANDLE_DESC *HandleType* 時，這個資料列會顯示轉換。  
  
 [5] 呼叫 **SQLAllocHandle** 時， *OutputHandlePtr* 指向有效的控制碼會覆寫該控制碼，而不考慮該控制碼先前的內容，而且可能會導致 ODBC 驅動程式發生問題。 這是不正確的 ODBC 應用程式設計，可使用針對* \* OutputHandlePtr*定義的相同應用程式變數來呼叫**SQLAllocHandle**兩次，而不需要呼叫**SQLFreeHandle**來釋放控制碼，然後再重新配置。 以這種方式覆寫 ODBC 控制碼可能會導致 ODBC 驅動程式部分的行為不一致或發生錯誤。  
  
## <a name="sqlbindcol"></a>SQLBindCol  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|--|--|--|--|HY010|HY010|  
  
## <a name="sqlbindparameter"></a>SQLBindParameter  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|--|--|--|--|HY010|HY010|  
  
## <a name="sqlbrowseconnect-sqlconnect-and-sqldriverconnect"></a>SQLBrowseConnect、SQLConnect 和 SQLDriverConnect  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|08002|08002|08002|08002|08002|08002|08002|  
  
## <a name="sqlbulkoperations"></a>SQLBulkOperations  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|HY010|HY010|24000|請參閱下一個資料表|HY010|NS [c] HY010 o|  
  
## <a name="sqlbulkoperations-cursor-states"></a>SQLBulkOperations (資料指標狀態)   
  
|S5<br /><br /> 已開啟|S6<br /><br /> SQLFetch 或 SQLFetchScroll|S7<br /><br /> SQLExtendedFetch|  
|-------------------|---------------------------------------|-----------------------------|  
|--[s] S8 [d] S11 [x]|--[s] S8 [d] S11 [x]|HY010|  
  
## <a name="sqlcancel"></a>SQLCancel  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|--|--|--|--|S1 [1] S2 [nr] 和 [2] S3 [r] 和 [2] S5 [3] 和 [5] S6 ( [3] 或 [4] ) 和 [6] S7 [4] 和 [7]|請參閱下一個資料表|  
  
 [1]   **SQLExecDirect** 傳回 SQL_NEED_DATA。  
  
 [2]   **SQLExecute** 傳回 SQL_NEED_DATA。  
  
 [3]   **SQLBulkOperations** 傳回 SQL_NEED_DATA。  
  
 [4]   **SQLSetPos** 傳回 SQL_NEED_DATA。  
  
 尚未呼叫 [5]   **SQLFetch**、 **SQLFetchScroll**或 **SQLExtendedFetch** 。  
  
 已呼叫 [6]   **SQLFetch** 或 **SQLFetchScroll** 。  
  
 [7] 已呼叫   **SQLExtendedFetch** 。  
  
## <a name="sqlcancel-asynchronous-states"></a>SQLCancel (非同步狀態)   
  
|S11<br /><br /> 仍在執行|S12<br /><br /> 已取消非同步|  
|-----------------------------|-----------------------------|  
|NS [1] S12 [2]|S12|  
  
 [1] 在函式執行時，語句已暫時在狀態 S11 中。 從不同的執行緒呼叫**SQLCancel** 。  
  
 [2] 語句的狀態為 S11，因為呼叫以非同步方式傳回的函式 SQL_STILL_EXECUTING。  
  
## <a name="sqlclosecursor"></a>SQLCloseCursor  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|24000|24000|24000|S1 [np] S3 [p]|HY010|HY010|  
  
## <a name="sqlcolattribute"></a>SQLColAttribute  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|HY010|請參閱下一個資料表|24000|--[s] S11 [x]|HY010|NS [c] HY010 o|  
  
## <a name="sqlcolattribute-prepared-states"></a>SQLColAttribute (備妥的狀態)   
  
|S2<br /><br /> 沒有結果|S3<br /><br /> 結果|  
|-----------------------|--------------------|  
|--[1] 07005 [2]|--[s] S11 x|  
  
 [1]   *FieldIdentifier* 已 SQL_DESC_COUNT。  
  
 [2] 未 SQL_DESC_COUNT   *FieldIdentifier* 。  
  
## <a name="sqlcolumnprivileges-sqlcolumns-sqlforeignkeys-sqlgettypeinfo-sqlprimarykeys-sqlprocedurecolumns-sqlprocedures-sqlspecialcolumns-sqlstatistics-sqltableprivileges-and-sqltables"></a>SQLColumnPrivileges、SQLColumns、SQLForeignKeys、SQLGetTypeInfo、SQLPrimaryKeys、SQLProcedureColumns、SQLProcedures、SQLSpecialColumns、SQLStatistics、SQLTablePrivileges 和 SQLTables  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
| (IH) |S5 [s] S11 [x]|S1 [e] S5 [s] S11 [x]|S1 [e] 和 [1] S5 [s] 和 [1] S11 [x] 和 [1] 24000 [2]|請參閱下一個資料表|HY010|NS [c] HY010 o|  
  
 [1] 目前的結果是最後一個或唯一的結果，或目前沒有結果。 如需多個結果的詳細資訊，請參閱 [多個結果](../../../odbc/reference/develop-app/multiple-results.md)。  
  
 [2] 目前的結果不是最後的結果。  
  
## <a name="sqlcolumnprivileges-sqlcolumns-sqlforeignkeys-sqlgettypeinfo-sqlprimarykeys-sqlprocedurecolumns-sqlprocedures-sqlspecialcolumns-sqlstatistics-sqltableprivileges-and-sqltables-cursor-states"></a>SQLColumnPrivileges、SQLColumns、SQLForeignKeys、SQLGetTypeInfo、SQLPrimaryKeys、SQLProcedureColumns、SQLProcedures、SQLSpecialColumns、SQLStatistics、SQLTablePrivileges 和 SQLTables (資料指標狀態)   
  
|S5<br /><br /> 已開啟|S6<br /><br /> SQLFetch 或 SQLFetchScroll|S7<br /><br /> SQLExtendedFetch|  
|-------------------|---------------------------------------|-----------------------------|  
|24000|24000 [1]|24000|  
  
 [1] 如果 **SQLFetch** 或 **SQLFetchScroll** 尚未傳回 SQL_NO_DATA，驅動程式管理員會傳回此錯誤，如果 **SQLFetch** 或 **SQLFetchScroll** 已傳回 SQL_NO_DATA，驅動程式會傳回此錯誤。  
  
## <a name="sqlcopydesc"></a>SQLCopyDesc  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|IH [1]|--|--|--|--|HY010|NS [c] 和 [3] HY010 [o] 或 [4]|  
|IH [2]|HY010|請參閱下一個資料表|24000|--[s] S11 x|HY010|NS [c] 和 [3] HY010 [o] 或 [4]|  
  
 [1] 當 *SourceDescHandle* 引數為 ARD、APD 或 IPD 時，此資料列會顯示轉換。  
  
 [2] 當 *SourceDescHandle* 引數為 IRD 時，此資料列會顯示轉換。  
  
 [3] *SourceDescHandle* 和 *TargetDescHandle* 引數與以非同步方式執行的 **SQLCopyDesc** 函數相同。  
  
 [4] *SourceDescHandle* 引數或 *TargetDescHandle* 引數 (或兩個) 與以非同步方式執行的 **SQLCopyDesc** 函數不同。  
  
## <a name="sqlcopydesc-prepared-states"></a>SQLCopyDesc (備妥的狀態)   
  
|S2<br /><br /> 沒有結果|S3<br /><br /> 結果|  
|-----------------------|--------------------|  
|24000 [1]|--[s] S11 [x]|  
  
 單當 *SourceDescHandle* 引數為 IRD 時，此資料列會顯示轉換。  
  
## <a name="sqldatasources-and-sqldrivers"></a>SQLDataSources 和 SQLDrivers  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|--|--|--|--|--|--|--|  
  
## <a name="sqldescribecol"></a>SQLDescribeCol  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|HY010|請參閱下一個資料表|24000|--[s] S11 [x]|HY010|NS [c] HY010 o|  
  
## <a name="sqldescribecol-prepared-states"></a>SQLDescribeCol (備妥的狀態)   
  
|S2<br /><br /> 沒有結果|S3<br /><br /> 結果|  
|-----------------------|--------------------|  
|07005|--[s] S11 [x]|  
  
## <a name="sqldescribeparam"></a>SQLDescribeParam  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|HY010|--[s] S11 [x]|HY010|HY010|HY010|NS [c] HY010 [o]|  
  
## <a name="sqldisconnect"></a>SQLDisconnect  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|--[1]|S0 [1]|S0 [1]|S0 [1]|S0 [1]| (HY010) | (HY010) |  
  
 [1] 呼叫 **SQLDisconnect** 會釋放與連接相關聯的所有語句。 此外，這會將連接狀態傳回至 C2;在語句狀態為 S0 之前，連接狀態必須為 C4。  
  
## <a name="sqlendtran"></a>SQLEndTran  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|--|--|--[2] 或 [3] S1 [1]|--[3] S1 [np] 和 ( [1] 或 [2] ) S1 [p] 和 [1] S2 [p] 和 [2]|--[3] S1 [np] 和 ( [1] 或 [2] ) S1 [p] 和 [1] S3 [p] 和 [2]| (HY010) | (HY010) |  
  
 [1] *CompletionType* 引數是 SQL_COMMIT，而 **SQLGetInfo** 會傳回 SQL_CURSOR_COMMIT_BEHAVIOR 資訊類型的 SQL_CB_DELETE，或 *CompletionType* 引數是 SQL_ROLLBACK，而 **SQLGetInfo** 會傳回 SQL_CB_DELETE 資訊類型的 SQL_CURSOR_ROLLBACK_BEHAVIOR。  
  
 [2] *CompletionType* 引數是 SQL_COMMIT，而 **SQLGetInfo** 會傳回 SQL_CURSOR_COMMIT_BEHAVIOR 資訊類型的 SQL_CB_CLOSE，或 *CompletionType* 引數是 SQL_ROLLBACK，而 **SQLGetInfo** 會傳回 SQL_CB_CLOSE 資訊類型的 SQL_CURSOR_ROLLBACK_BEHAVIOR。  
  
 [3] *CompletionType* 引數是 SQL_COMMIT，而 **SQLGetInfo** 會傳回 SQL_CURSOR_COMMIT_BEHAVIOR 資訊類型的 SQL_CB_PRESERVE，或 *CompletionType* 引數是 SQL_ROLLBACK，而 **SQLGetInfo** 會傳回 SQL_CB_PRESERVE 資訊類型的 SQL_CURSOR_ROLLBACK_BEHAVIOR。  
  
## <a name="sqlexecdirect"></a>SQLExecDirect  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
| (IH) |S4 [s] 和 [nr] S5 [s] 和 [r] S8 [d] S11 [x]|--[e] 和 [1] S1 [e] 和 [2] S4 [s] and [nr] S5 [s] 和 [r] S8 [d] S11 [x]|--[e]、[1]、[3] S1 [e]、[2] 和 [3] S4 [s]、[nr] 和 [3] S5 [s]、[r]、[3] S8 [d] 和 [3] S11 [x] 和 [3] 24000 [4]|請參閱下一個資料表|HY010|NS [c] HY010 [o]|  
  
 [1] 驅動程式管理員傳回錯誤。  
  
 [2] 驅動程式管理員不會傳回錯誤。  
  
 [3] 目前的結果是最後一個或唯一的結果，或目前沒有結果。 如需多個結果的詳細資訊，請參閱 [多個結果](../../../odbc/reference/develop-app/multiple-results.md)。  
  
 [4] 目前的結果不是最後的結果。  
  
## <a name="sqlexecdirect-cursor-states"></a>SQLExecDirect (資料指標狀態)   
  
|S5<br /><br /> 已開啟|S6<br /><br /> SQLFetch 或 SQLFetchScroll|S7<br /><br /> SQLExtendedFetch|  
|-------------------|---------------------------------------|-----------------------------|  
|24000|24000 [1]|24000|  
  
 [1] 如果 **SQLFetch** 或 **SQLFetchScroll** 尚未傳回 SQL_NO_DATA，驅動程式管理員會傳回此錯誤，如果 **SQLFetch** 或 **SQLFetchScroll** 已傳回 SQL_NO_DATA，驅動程式會傳回此錯誤。  
  
## <a name="sqlexecute"></a>SQLExecute  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
| (IH) | (HY010) |請參閱下一個資料表|S2 [e]、p、and [1] S4 [s]、[p]、[nr] 和 [1] S5 [s]、[p]、[r] 和 [1] S8 [d]、[p] 和 [1] S11 [x]、[p] 和 [1] 24000 [p] 和 [2] HY010 [np]|查看資料指標狀態資料表|HY010|NS [c] HY010 [o]|  
  
 [1] 目前的結果是最後一個或唯一的結果，或目前沒有結果。 如需多個結果的詳細資訊，請參閱 [多個結果](../../../odbc/reference/develop-app/multiple-results.md)。  
  
 [2] 目前的結果不是最後的結果。  
  
## <a name="sqlexecute-prepared-states"></a>SQLExecute (備妥的狀態)   
  
|S2<br /><br /> 沒有結果|S3<br /><br /> 結果|  
|-----------------------|--------------------|  
|S4 [s] S8 [d] S11 [x]|S5 [s] S8 [d] S11 [x]|  
  
## <a name="sqlexecute-cursor-states"></a>SQLExecute (資料指標狀態)   
  
|S5<br /><br /> 已開啟|S6<br /><br /> SQLFetch 或 SQLFetchScroll|S7<br /><br /> SQLExtendedFetch|  
|-------------------|---------------------------------------|-----------------------------|  
|24000 [p] HY010 [np]|24000 [p]，[1] HY010 [np]|24000 [p] HY010 [np]|  
  
 [1] 如果 **SQLFetch** 或 **SQLFetchScroll** 尚未傳回 SQL_NO_DATA，驅動程式管理員會傳回此錯誤，如果 **SQLFetch** 或 **SQLFetchScroll** 已傳回 SQL_NO_DATA，驅動程式會傳回此錯誤。  
  
## <a name="sqlextendedfetch"></a>SQLExtendedFetch  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|S1010|S1010|24000|請參閱下一個資料表|S1010|NS [c] S1010 [o]|  
  
## <a name="sqlextendedfetch-cursor-states"></a>SQLExtendedFetch (資料指標狀態)   
  
|S5<br /><br /> 已開啟|S6<br /><br /> SQLFetch 或 SQLFetchScroll|S7<br /><br /> SQLExtendedFetch|  
|-------------------|---------------------------------------|-----------------------------|  
|S7 [s] 或 [nf-e] S11 [x]|S1010|--[s] 或 [nf-e] S11 [x]|  
  
## <a name="sqlfetch-and-sqlfetchscroll"></a>SQLFetch 和 SQLFetchScroll  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|HY010|HY010|24000|請參閱下一個資料表|HY010|NS [c] HY010 [o]|  
  
## <a name="sqlfetch-and-sqlfetchscroll-cursor-states"></a>SQLFetch 和 SQLFetchScroll (資料指標狀態)   
  
|S5<br /><br /> 已開啟|S6<br /><br /> SQLFetch 或 SQLFetchScroll|S7<br /><br /> SQLExtendedFetch|  
|-------------------|---------------------------------------|-----------------------------|  
|S6 [s] 或 [nf-e] S11 [x]|--[s] 或 [nf-e] S11 [x]|HY010|  
  
## <a name="sqlfreehandle"></a>SQLFreeHandle  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|--[1]|HY010|HY010|HY010|HY010|HY010|HY010|  
|IH [2]|S0|S0|S0|S0|HY010|HY010|  
|--[3]|--|--|--|--|--|--|  
  
 [1] 當 SQL_HANDLE_ENV 或 SQL_HANDLE_DBC *HandleType* 時，這個資料列會顯示轉換。  
  
 [2] 當 SQL_HANDLE_STMT *HandleType* 時，這個資料列會顯示轉換。  
  
 [3] 此資料列會在 *HandleType* SQL_HANDLE_DESC 且明確配置描述項時顯示轉換。  
  
## <a name="sqlfreestmt"></a>SQLFreeStmt  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|IH [1]|--|--|S1 [np] S2 [p]|S1 [np] S3 [p]|HY010|HY010|  
|IH [2]|--|--|--|--|HY010|HY010|  
  
 [1] 此資料列會顯示 SQL_CLOSE *選項* 時的轉換。  
  
 [2] 當 *選項* 為 SQL_UNBIND 或 SQL_RESET_PARAMS 時，此資料列會顯示轉換。 如果*選項*引數是 SQL_DROP，且基礎*驅動程式是 ODBC 3.x 驅動程式*，驅動程式管理員會將此對應到*HandleType*設定為 SQL_HANDLE_STMT 的**SQLFreeHandle**呼叫。 如需詳細資訊，請參閱 [SQLFreeHandle](../../../odbc/reference/syntax/sqlfreehandle-function.md)的轉換表。  
  
## <a name="sqlgetconnectattr"></a>SQLGetConnectAttr  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|--|--|--|--|--|--|--|  
  
## <a name="sqlgetcursorname"></a>SQLGetCursorName  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|--|--|--|--|HY010|HY010|  
  
## <a name="sqlgetdata"></a>SQLGetData  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|HY010|HY010|24000|請參閱下一個資料表|HY010|NS [c] HY010 [o]|  
  
## <a name="sqlgetdata-cursor-states"></a>SQLGetData (資料指標狀態)   
  
|S5<br /><br /> 已開啟|S6<br /><br /> SQLFetch 或 SQLFetchScroll|S7<br /><br /> SQLExtendedFetch|  
|-------------------|---------------------------------------|-----------------------------|  
|24000|--[s] 或 [nf-e] S11 [x] 24000 [b] HY109 [i]|--[s] 或 [nf-e] S11 [x] 24000 [b] HY109 [i]|  
  
## <a name="sqlgetdescfield-and-sqlgetdescrec"></a>SQLGetDescField 和 SQLGetDescRec  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|--[1] 或 [2] HY010 [3]|請參閱下一個資料表|--[1] 或 [2] 24000 [3]|--[1]、[2] 或 [3] S11 [3] 和 [x]|HY010|NS [c] 或 [4] HY010 [o] 和 [5]|  
  
 [1] *DescriptorHandle* 引數是 APD 或 ARD。  
  
 [2] *DescriptorHandle* 引數是 IPD。  
  
 [3] *DescriptorHandle* 引數為 IRD。  
  
 [4] *DescriptorHandle*引數與以非同步方式執行的**SQLGetDescField**或**SQLGetDescRec**函數中的*DescriptorHandle*引數相同。  
  
 [5] *DescriptorHandle*引數與以非同步方式執行的**SQLGetDescField**或**SQLGetDescRec**函數中的*DescriptorHandle*引數不同。  
  
## <a name="sqlgetdescfield-and-sqlgetdescrec-prepared-states"></a>SQLGetDescField 和 SQLGetDescRec (備妥的狀態)   
  
|S2<br /><br /> 沒有結果|S3<br /><br /> 結果|  
|-----------------------|--------------------|  
|--[1]、[2] 或 [3] S11 [2] 和 [x]|--[1]、[2] 或 [3] S11 [x]|  
  
 [1] *DescriptorHandle* 引數是 APD 或 ARD。  
  
 [2] *DescriptorHandle* 引數是 IPD。  
  
 [3] *DescriptorHandle* 引數為 IRD。 請注意，當 *DescriptorHandle* 是 IRD 時，這些函數一律會傳回狀態 S2 SQL_NO_DATA。  
  
## <a name="sqlgetdiagfield-and-sqlgetdiagrec"></a>SQLGetDiagField 和 SQLGetDiagRec  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|--[1]|--|--|--|--|--|--|  
|IH [2]|--[3]|--[3]|--|--|--[3]|--[3]|  
  
 [1] 此資料列會顯示 *HandleType* SQL_HANDLE_ENV、SQL_HANDLE_DBC 或 SQL_HANDLE_DESC 時的轉換。  
  
 [2] 當 SQL_HANDLE_STMT *HandleType* 時，這個資料列會顯示轉換。  
  
 [3] 在 SQL_DIAG_ROW_COUNT*以*時， **SQLGetDiagField**一律會傳回此狀態的錯誤。  
  
## <a name="sqlgetenvattr"></a>SQLGetEnvAttr  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|--|--|--|--|--|--|--|  
  
## <a name="sqlgetfunctions"></a>SQLGetFunctions  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|--|--|--|--|--|--|--|  
  
## <a name="sqlgetinfo"></a>SQLGetInfo  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|--|--|--|--|--|--|--|  
  
## <a name="sqlgetstmtattr"></a>SQLGetStmtAttr  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|--[1] 24000 [2]|--[1] 24000 [2]|--[1] 24000 [2]|請參閱下一個資料表|HY010|HY010|  
  
 [1] 未 SQL_ATTR_ROW_NUMBER 語句屬性。  
  
 [2] 語句屬性已 SQL_ATTR_ROW_NUMBER。  
  
## <a name="sqlgetstmtattr-cursor-states"></a>SQLGetStmtAttr (資料指標狀態)   
  
|S5<br /><br /> 已開啟|S6<br /><br /> SQLFetch 或 SQLFetchScroll|S7<br /><br /> SQLExtendedFetch|  
|-------------------|---------------------------------------|-----------------------------|  
|--[1] 24000 [2]|--[1] 或 ( [v] 和 [2] ) 24000 [b] 和 [2] HY109 [i] 和 [2]|--[i] 或 ( [v] 和 [2] ) 24000 [b] 和 [2] HY109 [1] 和 [2]|  
  
 [1] 未 SQL_ATTR_ROW_NUMBER *屬性* 引數。  
  
 [2] *屬性* 引數已 SQL_ATTR_ROW_NUMBER。  
  
## <a name="sqlmoreresults"></a>SQLMoreResults  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
| (IH) |--[1]|--[1]|--[s] 和 [2] S1 [nf-e]、[np]、[4] S2 [nf-e]、[p] 和 [4] S5 [s] 和 [3] S11 [x]|S1 [nf-e]、[np]、[4] S3 [nf-e]、[p] 和 [4] S4 [s] 和 [2] S5 [s] 和 [3] S11 [x]|HY010|NS [c] HY010 [o]|  
  
 [1] 函數一律會傳回此狀態 SQL_NO_DATA。  
  
 [2] 下一個結果是資料列計數。  
  
 [3] 下一個結果是結果集。  
  
 [4] 目前的結果是最後一個結果。  
  
## <a name="sqlnativesql"></a>SQLNativeSql  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|--|--|--|--|--|--|--|  
  
## <a name="sqlnumparams"></a>SQLNumParams  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|HY010|--[s] S11 [x]|--[s] S11 [x]|--[s] S11 [x]|HY010|NS [c] HY010 [o]|  
  
## <a name="sqlnumresultcols"></a>SQLNumResultCols  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|HY010|--[s] S11 [x]|--[s] S11 [x]|--[s] S11 [x]|HY010|NS [c] HY010 [o]|  
  
## <a name="sqlparamdata"></a>SQLParamData  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|HY010|HY010|HY010|HY010|請參閱下一個資料表|NS [c] HY010 [o]|  
  
## <a name="sqlparamdata-need-data-states"></a>SQLParamData (需要資料狀態)   
  
|S8<br /><br /> 需要資料|S9<br /><br /> 必須 Put|S10<br /><br /> 可以 Put|  
|----------------------|---------------------|---------------------|  
|S1 [e] 和 [1] S2 [e]、[nr] 和 [2] S3 [e]、[r] 和 [2] S5 [e] 和 [4] S6 [e] 和 [5] S7 [e] 和 [3] S9 [d] S11 [x]|HY010|S1 [e] 和 [1] S2 [e]、[nr] 和 [2] S3 [e]，[r]，and [2] S4 [s]，[nr]， ( [1] 或 [2] ) S5 [s]，[r] 和 ( [1] 或 [2] ) S5 ( [s] 或 [e] ) 和 [4] S6 ( [s] 或 [e] ) 和 [5] S7 ( [s] 或 [e] ) 和 [3] S9 [d] S11 [x]|  
  
 [1]   **SQLExecDirect** 傳回 SQL_NEED_DATA。  
  
 [2]   **SQLExecute** 傳回 SQL_NEED_DATA。  
  
 [3] 已從狀態 S7 呼叫   **SQLSetPos** ，並傳回 SQL_NEED_DATA。  
  
 [4] 已從狀態 S5 呼叫   **SQLBulkOperations** ，並傳回 SQL_NEED_DATA。  
  
 [5] 已從狀態 S6 呼叫   **SQLSetPos** 或 **SQLBulkOperations** ，並傳回 SQL_NEED_DATA。  
  
## <a name="sqlprepare"></a>SQLPrepare  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
| (IH) |S2 [s] 和 [nr] S3 [s] 和 [r] S11 [x]|--[s] 或 ( [e] 和 [1] ) S1 [e] 和 [2] S11 [x]|S1 [e] 和 [3] S2 [s]、[nr] 和 [3] S3 [s]、[r] 和 [3] S11 [x] 和 [3] 24000 [4]|請參閱下一個資料表|HY010|NS [c] HY010 [o]|  
  
 [1] 因為 SQLSTATE was HY009 [不正確引數值] 或 HY090 [不正確字串或緩衝區長度] ) ，所以準備失敗，原因是無法驗證 (語句。  
  
 [2] 驗證語句時發生的準備失敗 (SQLSTATE 未 HY009 [不正確引數值] 或 HY090 [不正確字串或緩衝區長度] ) 。  
  
 [3] 目前的結果是最後一個或唯一的結果，或目前沒有結果。 如需多個結果的詳細資訊，請參閱 [多個結果](../../../odbc/reference/develop-app/multiple-results.md)。  
  
 [4] 目前的結果不是最後的結果。  
  
## <a name="sqlprepare-cursor-states"></a>SQLPrepare (資料指標狀態)   
  
|S5<br /><br /> 已開啟|S6<br /><br /> SQLFetch 或 SQLFetchScroll|S7<br /><br /> SQLExtendedFetch|  
|-------------------|---------------------------------------|-----------------------------|  
|24000|24000|24000|  
  
## <a name="sqlputdata"></a>SQLPutData  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|HY010|HY010|HY010|HY010|請參閱下一個資料表|NS [c] HY010 [o]|  
  
## <a name="sqlputdata-need-data-states"></a>SQLPutData (需要資料狀態)   
  
|S8<br /><br /> 需要資料|S9<br /><br /> 必須 Put|S10<br /><br /> 可以 Put|  
|----------------------|---------------------|---------------------|  
|HY010|S1 [e] 和 [1] S2 [e]、[nr] 和 [2] S3 [e]、[r] 和 [2] S5 [e] 和 [4] S6 [e] 和 [5] S7 [e] 和 [3] S10 [s] S11 [x]|--[s] S1 [e] 和 [1] S2 [e]、[nr] 和 [2] S3 [e]、[r] 和 [2] 個 S5 [e]、[4] S6 [e] 和 [5] S7 [e] 和 [3] S11 [x] HY011 [6]|  
  
 [1]   **SQLExecDirect** 傳回 SQL_NEED_DATA。  
  
 [2]   **SQLExecute** 傳回 SQL_NEED_DATA。  
  
 [3] 已從狀態 S7 呼叫   **SQLSetPos** ，並傳回 SQL_NEED_DATA。  
  
 [4] 已從狀態 S5 呼叫   **SQLBulkOperations** ，並傳回 SQL_NEED_DATA。  
  
 [5] 已從狀態 S6 呼叫   **SQLSetPos** 或 **SQLBulkOperations** ，並傳回 SQL_NEED_DATA。  
  
 [6] 對 **SQLPutData** 的一或多個呼叫會傳回 SQL_SUCCESS 的單一參數，然後針對相同的參數建立 **SQLPutData** 的呼叫，並將 *StrLen_or_Ind* 設定為 SQL_Null_DATA。  
  
## <a name="sqlrowcount"></a>SQLRowCount  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
| (IH) | (HY010) | (HY010) |--|--| (HY010) | (HY010) |  
  
## <a name="sqlsetconnectattr"></a>SQLSetConnectAttr  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|--[1]|--|--|--|--[2] 24000 [3]|HY010|HY010|  
  
 [1] 當 *屬性* 是連接屬性時，此資料列會顯示轉換。 若為 *屬性* 為語句屬性的轉換，請參閱 **SQLSetStmtAttr**的語句轉換表。  
  
 [2] 未 SQL_ATTR_CURRENT_CATALOG *屬性* 引數。  
  
 [3] *屬性* 引數已 SQL_ATTR_CURRENT_CATALOG。  
  
## <a name="sqlsetcursorname"></a>SQLSetCursorName  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|--|--|24000|24000|HY010|HY010|  
  
## <a name="sqlsetdescfield-and-sqlsetdescrec"></a>SQLSetDescField 和 SQLSetDescRec  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|IH [1]|--|--|--|--|HY010|HY010|  
  
 [1] 此資料列會顯示轉換，其中*DescriptorHandle*引數為 ARD、APD、IPD 或 (（當*IRD*引數 SQL_DESC_ARRAY_STATUS_PTR 或 SQL_DESC_ROWS_PROCESSED_PTR 時， **SQLSetDescField**) FieldIdentifier。 當*FieldIdentifier*為任何其他值時，呼叫 IRD 的**SQLSetDescField**是錯誤。  
  
## <a name="sqlsetenvattr"></a>SQLSetEnvAttr  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|HY011|HY011|HY011|HY011|Y011|HY01|HY011|  
  
## <a name="sqlsetpos"></a>SQLSetPos  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|HY010|HY010|24000|請參閱下一個資料表|HY010|NS [c] HY010 [o]|  
  
## <a name="sqlsetpos-cursor-states"></a>SQLSetPos (資料指標狀態)   
  
|S5<br /><br /> 已開啟|S6<br /><br /> SQLFetch 或 SQLFetchScroll|S7<br /><br /> SQLExtendedFetch|  
|-------------------|---------------------------------------|-----------------------------|  
|24000|--[s] S8 [d] S11 [x] 24000 [b] HY109 [i]|--[s] S8 [d] S11 [x] 24000 [b] HY109 [i]|  
  
## <a name="sqlsetstmtattr"></a>SQLSetStmtAttr  
  
|S0<br /><br /> 未配置|S1<br /><br /> 已配置|S2-S3<br /><br /> Prepared|S4<br /><br /> 執行|S5-S7<br /><br /> 資料指標|S8-S10<br /><br /> 需要資料|S11-S12<br /><br /> Async|  
|------------------------|----------------------|------------------------|---------------------|----------------------|--------------------------|-----------------------|  
|Ih|--|--[1] HY011 [2]|--[1] 24000 [2]|--[1] 24000 [2]|HY010 [np] 或 [1] HY011 [p] 和 [2]|HY010 [np] 或 [1] HY011 [p] 和 [2]|  
  
 [1] *屬性* 引數未 SQL_ATTR_CONCURRENCY、SQL_ATTR_CURSOR_TYPE、SQL_ATTR_SIMULATE_CURSOR、SQL_ATTR_USE_BOOKMARKS、SQL_ATTR_CURSOR_SCROLLABLE 或 SQL_ATTR_CURSOR_SENSITIVITY。  
  
 [2] *屬性* 引數已 SQL_ATTR_CONCURRENCY、SQL_ATTR_CURSOR_TYPE、SQL_ATTR_SIMULATE_CURSOR、SQL_ATTR_USE_BOOKMARKS、SQL_ATTR_CURSOR_SCROLLABLE 或 SQL_ATTR_CURSOR_SENSITIVITY。
