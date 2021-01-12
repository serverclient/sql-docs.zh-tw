---
description: 'sys.fn_net_changes_ &lt; capture_instance &gt; (transact-sql) '
title: sys.fn_net_changes_ &lt; capture_instance &gt; (transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.fn_net_changes_TSQL
- fn_net_changes_TSQL
- fn_net_changes
- sys.fn_net_changes
dev_langs:
- TSQL
helpviewer_keywords:
- fn_net_changes_<capture_instance>
- sys.fn_net_changes_<capture_instance>
ms.assetid: 342fa030-9fd9-4b74-ae4d-49f6038a5073
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: eb0c58b3544afd5fa529db0c95af7c2f6ba3e6d3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096342"
---
# <a name="sysfn_net_changes_ltcapture_instancegt-transact-sql"></a>sys.fn_net_changes_ &lt; capture_instance &gt; (transact-sql) 
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **淨變更** 查詢函數的包裝函式。 sys.sp_cdc_generate_wrapper_function 系統預存程序會產生建立這些函數所需的指令碼。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
fn_net_changes_<capture_instance> ('start_time', 'end_time', '<row_filter_option>' )  
  
<capture_instance> ::= The name of the capture instance.  
<row_filter_option> ::=  
{ all  
  | all with mask  
  | all with merge  
}  
```  
  
## <a name="arguments"></a>引數  
 *start_time*  
 **Datetime** 值，代表要包含在結果集中之變更資料表專案範圍的低點點。  
  
 只有 cdc. <capture_instance>_CT 變更資料表中的資料列，其相關聯的認可時間嚴格大於 *start_time* 會包含在結果集中。  
  
 如果為這個引數提供 NULL 的值，查詢範圍的低端點將會對應到擷取執行個體之有效範圍的低端點。  
  
 *end_time*  
 **Datetime** 值，代表要包含在結果集中之變更資料表專案範圍的最高端點。  
  
 此參數可採用兩種意義的其中一種，取決於呼叫 sys.sp_cdc_generate_wrapper_function 時所選擇的值， @closed_high_end_point 以產生腳本來建立包裝函式：  
  
-   @closed_high_end_point = 1  
  
     只有 cdc. <capture_instance>_CT 變更資料表中的資料列，在 $start _lsn 中具有值 \_ \_ ，而對應的認可時間小於或等於 **start_time** ，則會包含在結果集中。  
  
-   @closed_high_end_point = 0  
  
     只有 cdc. <capture_instance>_CT 變更資料表中的資料列，在 $start _lsn 中具有值 \_ \_ ，而對應的認可時間則嚴格小於 **start_time** ，會包含在結果集中。  
  
 如果為這個引數提供 NULL 的值，查詢範圍的高端點將會對應到擷取執行個體之有效範圍的高端點。  
  
 *<row_filter_option>* ：： = {all | all with mask | all with merge}  
 管理結果集中傳回之中繼資料資料行以及資料列內容的選項。 可以是下列其中一個選項：  
  
 all  
 傳回內容資料行內已變更之資料列的最後內容，以及在 __CDC_OPERATION 中繼資料行內套用資料列所需的作業。  
  
 all with mask  
 傳回內容資料行內所有已變更之資料列的最後內容，以及在 __CDC_OPERATION 中繼資料行內套用每一個資料列所需的作業。 如果當您產生用來建立包裝函數的指令碼時，指定了更新旗標清單，則填入更新遮罩需要這個選項。  
  
 all with merge  
 傳回內容資料行內所有已變更之資料列的最後內容。  
  
 __CDC_OPERATION 資料行將會是以下兩個值的其中一個：  
  
-   D (如果必須刪除此資料列的話)。  
  
-   M (如果必須插入或更新此資料列的話)。  
  
 判斷需要插入或更新才能將變更套用至目標的邏輯會增加查詢的複雜性。 不需要區別插入和更新作業時，使用此選項可以改善效能。 在可直接使用合併作業的目標環境 (例如 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 環境) 中，這個方法的效果最好。  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料行類型|描述|  
|-----------------|-----------------|-----------------|  
|\<columns from @column_list>|**不同**|當呼叫來產生用來建立包裝函式的腳本時，在 sp_cdc_generate_wrapper_function 的 **column_list** 引數中所識別的資料行。 如果 *column_list* 為 Null，所有追蹤的來源資料行都會出現在結果集中。|  
|__CDC_OPERATION|**Nvarchar (2)**|指示將資料列套用到目標環境時，需要哪一個作業的作業碼。 作業會根據下列呼叫中提供的引數 *row_filter_option* 值而有所不同：<br /><br /> *row_filter_option* = ' all '、' all with mask '<br /><br /> 'D' - 刪除作業<br /><br /> 'I' - 插入作業<br /><br /> 'UN' - 更新作業<br /><br /> *row_filter_option* = ' all with merge '<br /><br /> 'D' - 刪除作業<br /><br /> 'M' - 插入作業或更新作業|  
|\<columns from @update_flag_list>|**bit**|藉由將 _uflag 附加到資料行名稱所命名的位元旗標。 只有當 *row_filter_option* **= ' all with mask '** 和 \_ _CDC_OPERATION **= ' UN '** 時，旗標才會採用非 null 值。 如果在查詢視窗內修改了對應的資料行，它會設定為 1。 否則為 0。|  
  
## <a name="remarks"></a>備註  
 Fn_net_changes_<capture_instance> 函數可作為 cdc.fn_cdc_get_net_changes_<capture_instance 查詢函數的包裝函式。 sys.sp_cdc_generate_wrapper 預存程序是用來建立此包裝函數的指令碼。  
  
 系統不會自動建立包裝函數。 您必須執行兩項作業，才能建立包裝函數：  
  
1.  執行預存程序來產生用來建立此包裝函數的指令碼。  
  
2.  執行指令碼來實際建立包裝函數。  
  
 包裝函數可讓使用者有系統地查詢在以 **datetime** 值（而非 LSN 值）所限定的間隔內發生的變更。 包裝函式會在提供的 **日期時間** 值與內部所需的 LSN 值之間執行所有必要的轉換，以做為查詢函數的引數。 當使用包裝函式來以序列方式處理變更資料的資料流程時，它們會確保不會遺失任何資料，也不會重複出現下列慣例： @end_time 與一個呼叫相關聯的間隔值會提供做為 @start_time 與後續呼叫相關聯的間隔值。  
  
 在建立指令碼時使用 @closed_high_end_point 參數，您便可以產生包裝函式來支援指定之查詢視窗上的封閉上限或開放上限。 也就是說，您可以決定具有認可時間的項目是否等於要包含在間隔內之擷取間隔的上限。 預設會包含上限。  
  
 **淨變更** 包裝函式所傳回的結果集，只會傳回產生包裝函式時在中的追蹤資料行 @column_list 。 如果 @column_list 為 NULL，就會傳回所有追蹤來源資料行。 來源資料行後面緊跟著 __CDC_OPERATION 作業資料行，這是可識別此作業的單字元或雙字元資料行。  
  
 然後，會將位旗標附加至參數中所識別之每個資料行的結果集 @update_flag_list 。 對於 **淨變更** 包裝函式，如果在包裝函式的 @row_filter_option 呼叫中使用的是 ' all ' 或 ' all with merge '，位旗標一定會是 Null。 如果 @row_filter_option 設定為 ' all with mask '，且 __CDC_OPERATION 為 ' d ' 或 ' I '，則旗標的值也會是 Null。 如果 \_ _CDC_OPERATION 為 ' UN '，此旗標將會設定為1或0，視 **net** update 作業是否造成資料行變更而定。  
  
 變更資料保存區設定範本「具現化架構的 CDC 包裝函式 Tvf」示範如何使用 sp_cdc_generate_wrapper_function 預存程式，針對架構定義的查詢函數的所有包裝函數，取得建立腳本。 然後，此範本會建立這些指令碼。 如需範本的詳細資訊，請參閱 [Template Explorer](../../ssms/template/template-explorer.md)。  
  
## <a name="see-also"></a>另請參閱  
 [sys.sp_cdc_generate_wrapper_function &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sys-sp-cdc-generate-wrapper-function-transact-sql.md)   
 [cdc.fn_cdc_get_net_changes_&#60;capture_instance&#62; &#40;Transact-SQL&#41;](../../relational-databases/system-functions/cdc-fn-cdc-get-net-changes-capture-instance-transact-sql.md)  
  
  
