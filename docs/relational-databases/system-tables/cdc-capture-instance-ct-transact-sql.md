---
description: 'cdc。 &lt;capture_instance &gt; _CT (transact-sql) '
title: cdc。 &lt;capture_instance &gt; _CT (transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 05/01/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- cdc
- cdc_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- cdc.<capture_instance>_CT
ms.assetid: 979c8110-3c54-4e76-953c-777194bc9751
author: cawrites
ms.author: chadam
ms.openlocfilehash: 7fa737644611f24d9d0858fd04066d3ba0571ee3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102722"
---
# <a name="cdcltcapture_instancegt_ct-transact-sql"></a>cdc。 &lt;capture_instance &gt; _CT (transact-sql) 
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  這是在來源資料表啟用異動資料擷取時所建立的變更資料表。 此資料表會針對在來源資料表上執行的每個插入和刪除作業傳回一個資料列，而且會針對在來源資料表上執行的每個更新作業傳回兩個資料列。 如果在啟用來源資料表時沒有指定變更資料表的名稱，就會衍生此名稱。 名稱的格式為 cdc。*capture_instance* _CT，其中 *capture_instance* 是來源資料表的架構名稱，以及格式 *schema_table* 的來源資料表名稱。 例如，如果 **AdventureWorks** 範例資料庫中的資料表 **Person** 已啟用變更資料捕捉，則會 **cdc.Person_Address_CT** 衍生的變更資料表名稱。  
  
 我們建議您不要 **直接查詢系統資料表**。 相反地，請執行 [cdc.fn_cdc_get_all_changes_<capture_instance>](../../relational-databases/system-functions/cdc-fn-cdc-get-all-changes-capture-instance-transact-sql.md) [和 cdc.fn_cdc_get_net_changes_<](../../relational-databases/system-functions/cdc-fn-cdc-get-net-changes-capture-instance-transact-sql.md) capture_instance 函數。  
  

  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**__$start_lsn**|**binary(10)**|與變更之認可交易相關聯的記錄序號 (LSN)。<br /><br /> 在相同交易中認可的所有變更都會共用相同的認可 LSN。 例如，如果來源資料表上的刪除作業移除兩個數據列，則變更資料表將會包含兩個數據列，每個資料列都有相同的 **__ $ start_lsn** 值。|  
|**__ $ end_lsn**|**binary(10)**|[!INCLUDE[ssInternalOnly](../../includes/ssinternalonly-md.md)]<br /><br /> 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 中，這個資料行一律是 NULL。|  
|**__$seqval**|**binary(10)**|用來排序交易內資料列變更的序列值。|  
|**__$operation**|**int**|識別與變更相關聯的資料操作語言 (DML) 作業。 可以是下列其中一項：<br /><br /> 1 = 刪除<br /><br /> 2 = 插入<br /><br /> 3 = 更新 (舊的值)<br /><br /> 執行更新陳述式之前，資料行資料具有資料列值。<br /><br /> 4 = 更新 (新的值)<br /><br /> 執行更新陳述式之後，資料行資料具有資料列值。|  
|**__$update_mask**|**varbinary(128)**|位元遮罩，可根據變更資料表的資料行序數識別這些變更的資料行。|  
|*\<captured source table columns>*|視情況而異|變更資料表中的其餘資料行都是建立擷取執行個體時，在來源資料表中識別成擷取資料行的資料行。 如果擷取的資料行清單中沒有指定任何資料行，這個資料表就會包含來源資料表中的所有資料行。|  
|**__ $ command_id** |**int** |追蹤交易內的作業順序。 |  
  
## <a name="remarks"></a>備註  

資料 `__$command_id` 行是在2012至2016版的累計更新中引進的資料行。 如需版本和下載資訊，請參閱知識庫文章 3030352 [：修正：為 Microsoft SQL Server 資料庫啟用變更資料捕捉之後，變更資料表的排序次序不正確](https://support.microsoft.com/help/3030352/fix-the-change-table-is-ordered-incorrectly-for-updated-rows-after-you)。  如需詳細資訊，請參閱 [升級至最新的 CU 之後，CDC 功能可能會中斷，以獲得 SQL Server 2012、2014和 2016](/archive/blogs/sql_server_team/cdc-functionality-may-break-after-upgrading-to-the-latest-cu-for-sql-server-2012-2014-and-2016)。

## <a name="captured-column-data-types"></a>擷取資料行資料類型  
 包含在這個資料表中的擷取資料行與對應的來源資料行具有相同的資料類型和值，但下列情況除外：  
  
-   **時間戳記** 資料行定義為 **二進位 (8)**。  
  
-   **標識** 列會定義為 **int** 或 **Bigint**。  
  
 不過，這些資料行中的值與來源資料行的值相同。  
  
### <a name="large-object-data-types"></a>大型物件資料類型  
 當 __ $ operation = 1 或   \_ \_ $operation = 3 時，資料類型為 image、Text 和 Ntext 的資料行一律會被指派 Null 值。 除非資料行在更新期間變更，否則在 (= 3 時，資料類型為 **Varbinary (max)**、 **Varchar (max)** 或 **Nvarchar) max $operation** 最大值的資料行會指派 Null 值 \_ \_ 。 當 \_ \_ $operation = 1 時，會在刪除時將這些資料行的值指派給這些資料行。 包含在 capture 實例中的計算資料行，一律會有 **Null** 值。  
  
 根據預設，在單一 INSERT、UPDATE、WRITETEXT 或 UPDATETEXT 陳述式中可加入至擷取資料行的大小上限為 65,536 個位元組或 64 KB。 若要增加此大小以支援較大的 LOB 資料，請使用 [ [設定最大文字複寫大小伺服器](../../database-engine/configure-windows/configure-the-max-text-repl-size-server-configuration-option.md) 設定] 選項來指定較大的大小上限。 如需詳細資訊，請參閱 [設定 max text repl size 伺服器組態選項](../../database-engine/configure-windows/configure-the-max-text-repl-size-server-configuration-option.md)。  
  
## <a name="data-definition-language-modifications"></a>資料定義語言修改  
 來源資料表的 DDL 修改（例如，加入或卸載資料行）會記錄在 [cdc.ddl_history](../../relational-databases/system-tables/cdc-ddl-history-transact-sql.md) 資料表中。 這些變更不會套用至變更資料表。 也就是說，變更資料表的定義會維持不變。 將資料列插入變更資料表時，擷取處理序會忽略沒有顯示在與來源資料表相關聯之擷取資料行清單中的這些資料行。 如果某個資料行顯示在擷取資料行清單中，但是該資料行已不存在來源資料表中，系統就會指派 Null 值給此資料行。  
  
 在來源資料表中變更資料行的資料類型，也會記錄在 [cdc.ddl_history](../../relational-databases/system-tables/cdc-ddl-history-transact-sql.md) 資料表中。 不過，這項變更不會更改變更資料表的定義。 當擷取處理序遇到對來源資料表所做 DDL 變更的記錄檔記錄時，變更資料表中擷取資料行的資料類型就會遭修改。  
  
 如果您必須以減少資料類型大小的方式來修改來源資料表中擷取資料行的資料類型，請使用下列程序來確保變更資料表中的對等資料行可成功進行修改。  
  
1.  在來源資料表中，更新即將修改之資料行的值，以便符合規劃的資料類型大小。 例如，如果您將資料類型從 **int** 變更為 **Smallint**，請將值更新為可容納在 **Smallint** 範圍中的大小，-32768 到32767。  
  
2.  在變更資料表中，在對等的資料行上執行相同的更新作業。  
  
3.  透過指定新的資料類型，更改來源資料表。 然後，資料類型變更就會成功地傳播至變更資料表。  

## <a name="data-manipulation-language-modifications"></a>資料操作語言修改  
 在啟用異動資料擷取的來源資料表上執行插入、更新和刪除作業時，這些 DML 作業的記錄就會顯示在資料庫交易記錄中。 變更資料捕獲進程會從交易記錄檔抓取這些變更的相關資訊，並將一或兩個數據列加入至變更資料表來記錄變更。 雖然變更資料表項目的認可通常必須針對一組變更而非單一項目執行，不過將項目加入至變更資料表的順序會與來源資料表認可這些項目的順序相同。  
  
 在變更資料表專案中， **__ $ start_lsn** 資料行用來記錄與來源資料表的變更相關聯的認可 lsn，而 **__ $ seqval 資料行** 則是用來排序其交易內的變更。 這些中繼資料資料行可一起用來確保來源變更的認可順序會保留下來。 由於擷取處理序會從交易記錄中取得其變更資訊，因此請務必注意，變更資料表項目不會以同步方式隨著其對應的來源資料表變更顯示。 不過，當擷取處理序已經處理來自交易記錄的相關變更項目之後，對應的變更會以非同步方式顯示。  
  
 若為插入和刪除作業，就會設定更新遮罩中的所有位元。 若為更新作業，就會同時修改更新舊值與更新新值資料列中的更新遮罩，以便反映在更新期間變更的資料行。  
  
## <a name="see-also"></a>另請參閱  
 [sys.sp_cdc_enable_table &#40;Transact-sql&#41;](../../relational-databases/system-stored-procedures/sys-sp-cdc-enable-table-transact-sql.md)   
 [sys.sp_cdc_get_ddl_history &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sys-sp-cdc-get-ddl-history-transact-sql.md)  
  
