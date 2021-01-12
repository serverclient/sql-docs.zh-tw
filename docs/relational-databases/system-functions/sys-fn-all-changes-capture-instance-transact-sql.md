---
description: 'sys.fn_all_changes_ &lt; capture_instance &gt; (transact-sql) '
title: sys.fn_all_changes_ &lt; capture_instance &gt; (transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/02/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- fn_all_changes
- sys.fn_all_changes
- fn_all_changes_TSQL
- sys.fn_all_changes_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- fn_all_changes_<capture_instance>
- sys.fn_all_changes_<capture_instance>
ms.assetid: 564fae96-b88c-4f22-9338-26ec168ba6f5
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: c1b411daa6ccdfdb34da1a9560520416e26e1191
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096469"
---
# <a name="sysfn_all_changes_ltcapture_instancegt-transact-sql"></a>sys.fn_all_changes_ &lt; capture_instance &gt; (transact-sql) 
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **所有變更** 查詢函數的包裝函式。 sys.sp_cdc_generate_wrapper_function 系統預存程序會產生建立這些函數所需的指令碼。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
fn_all_changes_<capture_instance> ('start_time' ,'end_time', '<row_filter_option>' )  
  
<capture_instance> ::= The name of the capture instance.  
<row_filter_option> ::=  
{ all  
  | all update old  
}  
```  
  
## <a name="arguments"></a>引數  
 *start_time*  
 **Datetime** 值，代表要包含在結果集中之變更資料表專案範圍的低點點。  
  
 結果集內只會包含 cdc. <capture_instance>_CT 變更資料表中，資料列的相關聯認可時間大於 *start_time* 。  
  
 如果為這個引數提供 NULL 的值，查詢範圍的低端點將會對應到擷取執行個體之有效範圍的低端點。  
  
 *end_time*  
 **Datetime** 值，代表要包含在結果集中之變更資料表專案範圍的最高端點。  
  
 這個參數可以根據在呼叫 sys.sp_cdc_generate_wrapper_function 時所選擇的值 @closed_high_end_point ，以產生包裝函式的建立腳本，來採用兩種可能的意義：  
  
-   @closed_high_end_point = 1  
  
     結果集內只會包含 cdc. <capture_instance>_CT 變更資料表的關聯認可時間小於或等於 end_time 的資料列。  
  
-   @closed_high_end_point = 0  
  
     結果集內只會包含 cdc.capture_instance_CT 變更資料表中的資料列，而這些資料列的相關聯認可時間嚴格小於 end_time。  
  
 如果為這個引數提供 NULL 的值，查詢範圍的高端點將會對應到擷取執行個體之有效範圍的高端點。  
  
 <row_filter_option>：： = {all | all update old}  
 管理結果集中傳回之中繼資料資料行及資料列內容的選項。  
  
 可以是下列其中一個選項：  
  
 all  
 傳回指定之 LSN 範圍內的所有變更。 如果是因為更新作業所發生的變更，這個選項就只會傳回包含套用更新之後之新值的資料列。  
  
 all update old  
 傳回指定之 LSN 範圍內的所有變更。 如果是因為更新作業而發生的變更，這個選項就會同時傳回包含更新之前之資料行值的資料列以及包含更新之後之資料行值的資料列。  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料行類型|描述|  
|-----------------|-----------------|-----------------|  
|__CDC_STARTLSN|**binary(10)**|與變更相關聯之交易的認可 LSN。 在相同交易中認可的所有變更都會共用相同的認可 LSN。|  
|__CDC_SEQVAL|**binary(10)**|用來排序交易內資料列變更的序列值。|  
|\<columns from @column_list>|**不同**|當呼叫它來產生建立包裝函式的腳本時，所要 sp_cdc_generate_wrapper_function 的 *column_list* 引數中所識別的資料行。|  
|__CDC_OPERATION|**Nvarchar (2)**|表示將資料列套用到目標環境所需之作業的作業碼。 它會根據在呼叫中提供的引數 *row_filter_option* 值而有所不同：<br /><br /> *row_filter_option* = ' all '<br /><br /> 'D' - 刪除作業<br /><br /> 'I' - 插入作業<br /><br /> 'UN' - 更新作業新值<br /><br /> *row_filter_option* = ' all update old '<br /><br /> 'D' - 刪除作業<br /><br /> 'I' - 插入作業<br /><br /> 'UN' - 更新作業新值<br /><br /> 'UO' - 更新作業舊值|  
|\<columns from @update_flag_list>|**bit**|藉由將 _uflag 附加到資料行名稱所命名的位元旗標。 當 _CDC_OPERATION 為 ' UO ' 時，旗標一律設定為 Null \_ 。 當 \_ _CDC_OPERATION 為 ' UN ' 時，如果更新產生對應資料行的變更，它就會設定為1。 否則為 0。|  
  
## <a name="remarks"></a>備註  
 Fn_all_changes_<capture_instance> 函數可作為 cdc.fn_cdc_get_all_changes_<capture_instance 查詢函數的包裝函式。 sys.sp_cdc_generate_wrapper 預存程序是用來產生建立此包裝函數的指令碼。  
  
 系統不會自動建立包裝函數。 您必須執行兩項作業，才能建立包裝函數：  
  
1.  執行預存程序來產生用來建立此包裝函數的指令碼。  
  
2.  執行指令碼來實際建立包裝函數。  

 包裝函數可讓使用者有系統地查詢在以 **datetime** 值（而非 LSN 值）所限定的間隔內發生的變更。 包裝函式會在提供的 **日期時間** 值與內部所需的 LSN 值之間執行所有必要的轉換，以做為查詢函數的引數。 當使用包裝函式來以序列方式處理變更資料的資料流程時，它們會確保不會遺失任何資料，也不會重複出現下列慣例： @end_time 與一個呼叫相關聯的間隔值會提供做為 @start_time 與後續呼叫相關聯的間隔值。  
  
 在建立指令碼時使用 @closed_high_end_point 參數，您便可以產生包裝函式來支援指定之查詢視窗上的封閉上限或開放上限。 也就是說，您可以決定具有認可時間的項目是否等於要包含在間隔內之擷取間隔的上限。 預設會包含上限。  
  
 **所有變更** 包裝函式所傳回的結果集會分別傳回變更資料表的 __ $ start_lsn 和 $seqval 資料行 \_ \_ 做為資料行 \_ _CDC_STARTLSN 和 \_ _CDC_SEQVAL。 它只會在產生包裝函式時，只使用出現在 *\@ column_list* 參數中的追蹤資料行。 如果 *\@ COLUMN_LIST* 為 Null，則會傳回所有追蹤的來源資料行。 來源資料行後面接著 _CDC_OPERATION 的作業資料行， \_ 這是可識別作業的一或兩個字元的資料行。  
  
 然後，系統會針對 @update_flag_list 參數內識別的每個資料行，將位元旗標附加到結果集。 針對 **所有變更** 包裝函式，如果 __CDC_OPERATION 是 '、' I ' 或 ' UO '，位旗標一定會是 Null。 如果 \_ _CDC_OPERATION 為 ' UN '，此旗標將會設定為1或0，這取決於更新作業是否會導致資料行變更。  
  
 變更資料保存區設定範本「具現化架構的 CDC 包裝函式 Tvf」示範如何使用 sp_cdc_generate_wrapper_function 預存程式，針對架構定義的查詢函數的所有包裝函數，取得建立腳本。 然後，此範本會建立這些指令碼。 如需範本的詳細資訊，請參閱 [Template Explorer](../../ssms/template/template-explorer.md)。  
  
## <a name="see-also"></a>另請參閱  
 [sys.sp_cdc_generate_wrapper_function &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sys-sp-cdc-generate-wrapper-function-transact-sql.md)   
 [cdc.fn_cdc_get_all_changes_&#60;capture_instance&#62;  &#40;Transact-SQL&#41;](../../relational-databases/system-functions/cdc-fn-cdc-get-all-changes-capture-instance-transact-sql.md)  
  
  
