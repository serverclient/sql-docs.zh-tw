---
description: OBJECTPROPERTY (Transact-SQL)
title: OBJECTPROPERTY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/15/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- OBJECTPROPERTY
- OBJECTPROPERTY_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- displaying schema-scoped object information
- viewing schema-scoped object information
- OBJECTPROPERTY function
- schema-scoped objects [SQL Server]
- objects [SQL Server], schema-scoped
ms.assetid: 27569888-f8b5-4cec-a79f-6ea6d692b4ae
author: julieMSFT
ms.author: jrasnick
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 652d705c92aede4592a079733c8dd281e9dd8e91
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170530"
---
# <a name="objectproperty-transact-sql"></a>OBJECTPROPERTY (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回目前資料庫中結構描述範圍物件的相關資訊。 如需結構描述範圍物件的清單，請參閱 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)。 不是以結構描述為範圍的物件，如資料定義語言 (DDL) 觸發程序和事件通知，無法使用這項功能。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql  
OBJECTPROPERTY ( id , property )   
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *id*  
 這是代表目前資料庫中之物件識別碼的運算式。 *id* 是 **int**，假設為目前資料庫內容中的結構描述範圍物件。  
  
 *property*  
 這個運算式代表 *id* 指定的物件要傳回的資訊。*property* 可以是下列值之一。  
  
> [!NOTE]  
>  除非另有說明，否則，當 *property* 不是有效屬性的名稱、*id* 不是有效的物件識別碼、*id* 對於指定的 *property* 是不支援的物件類型，或呼叫者無權檢視物件中繼資料時，便會傳回 NULL。  
  
|屬性名稱|物件類型|描述和傳回的值|  
|-------------------|-----------------|-------------------------------------|  
|CnstIsClustKey|條件約束|含叢集索引的 PRIMARY KEY 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsColumn|條件約束|單一資料行的 CHECK、DEFAULT 或 FOREIGN KEY 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsDeleteCascade|條件約束|含 ON DELETE CASCADE 選項的 FOREIGN KEY 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsDisabled|條件約束|停用的條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsNonclustKey|條件約束|含非叢集索引的 PRIMARY KEY 或 UNIQUE 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsNotRepl|條件約束|條件約束是利用 NOT FOR REPLICATION 關鍵字來定義的。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsNotTrusted|條件約束|在不檢查現有資料列的情況下啟用條件約束；因此，條件約束並不適用於所有資料列。<br /><br /> 1 = True<br /><br /> 0 = False|  
|CnstIsUpdateCascade|條件約束|含 ON UPDATE CASCADE 選項的 FOREIGN KEY 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsAfterTrigger|觸發程序|AFTER 觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsAnsiNullsOn|[!INCLUDE[tsql](../../includes/tsql-md.md)] 函數、[!INCLUDE[tsql](../../includes/tsql-md.md)] 程序、[!INCLUDE[tsql](../../includes/tsql-md.md)] 觸發程序、檢視表|建立期間的 ANSI_NULLS 設定。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsDeleteTrigger|觸發程序|DELETE 觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsFirstDeleteTrigger|觸發程序|當執行資料表的 DELETE 作業時，所引發的第一個觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsFirstInsertTrigger|觸發程序|當執行資料表的 INSERT 作業時，所引發的第一個觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsFirstUpdateTrigger|觸發程序|當執行資料表的 UPDATE 作業時，所引發的第一個觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsInsertTrigger|觸發程序|INSERT 觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsInsteadOfTrigger|觸發程序|INSTEAD OF 觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsLastDeleteTrigger|觸發程序|當執行資料表的 DELETE 作業時，所引發的最後一個觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsLastInsertTrigger|觸發程序|當執行資料表的 INSERT 作業時，所引發的最後一個觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsLastUpdateTrigger|觸發程序|當執行資料表的 UPDATE 作業時，所引發的最後一個觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsQuotedIdentOn|[!INCLUDE[tsql](../../includes/tsql-md.md)] 函數、[!INCLUDE[tsql](../../includes/tsql-md.md)] 程序、[!INCLUDE[tsql](../../includes/tsql-md.md)] 觸發程序、檢視表|建立期間的 QUOTED_IDENTIFIER 設定。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsStartup|程序|啟動程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsTriggerDisabled|觸發程序|停用的觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsTriggerNotForRepl|觸發程序|定義為 NOT FOR REPLICATION 的觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsUpdateTrigger|觸發程序|UPDATE 觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|ExecIsWithNativeCompilation|[!INCLUDE[tsql](../../includes/tsql-md.md)] 程序|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 此為原生編譯的程序。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基底資料類型：**int**|  
|HasAfterTrigger|資料表、檢視表|資料表或檢視表有 AFTER 觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|HasDeleteTrigger|資料表、檢視表|資料表或檢視表有 DELETE 觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|HasInsertTrigger|資料表、檢視表|資料表或檢視表有 INSERT 觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|HasInsteadOfTrigger|資料表、檢視表|資料表或檢視表有 INSTEAD OF 觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|HasUpdateTrigger|資料表、檢視表|資料表或檢視表有 UPDATE 觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsAnsiNullsOn|[!INCLUDE[tsql](../../includes/tsql-md.md)] 函數、[!INCLUDE[tsql](../../includes/tsql-md.md)] 程序、資料表、[!INCLUDE[tsql](../../includes/tsql-md.md)] 觸發程序、檢視表|指定資料表的 ANSI NULLS 選項設定是 ON。 這表示所有對於 Null 值的比較都會得出 UNKNOWN。 只要資料表存在，這項設定便適用於資料表定義中的所有運算式，其中包括計算資料行和條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsCheckCnst|任何結構描述範圍物件|CHECK 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsConstraint|任何結構描述範圍物件|這是資料行或資料表的單一資料行 CHECK、DEFAULT 或 FOREIGN KEY 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsDefault|任何結構描述範圍物件|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 繫結預設值。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsDefaultCnst|任何結構描述範圍物件|DEFAULT 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsDeterministic|函數、檢視表|函數或檢視表的決定性屬性。<br /><br /> 1 = 具有決定性<br /><br /> 0 = 不具決定性|  
|IsEncrypted|[!INCLUDE[tsql](../../includes/tsql-md.md)] 函數、[!INCLUDE[tsql](../../includes/tsql-md.md)] 程序、資料表、[!INCLUDE[tsql](../../includes/tsql-md.md)] 觸發程序、檢視表|指出此模組陳述式的原始文字已轉換為混亂格式。 在 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 中，無法直接從任何目錄檢視中看見混亂格式的輸出。 對系統資料表或資料庫檔案沒有存取權的使用者，將無法擷取模糊化的文字。 不過，可以透過 [DAC 通訊埠](../../database-engine/configure-windows/diagnostic-connection-for-database-administrators.md)存取系統資料表，或直接存取資料庫檔案的使用者，則可使用該文字。 另外，可將偵錯工具附加至伺服器處理序的使用者，可以在執行階段從記憶體擷取原始程序。<br /><br /> 1 = 已加密<br /><br /> 0 = 未加密<br /><br /> 基底資料類型：**int**|  
|IsExecuted|任何結構描述範圍物件|可以執行物件 (檢視、程序、函數或觸發程序)。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsExtendedProc|任何結構描述範圍物件|擴充程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsForeignKey|任何結構描述範圍物件|FOREIGN KEY 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsIndexed|資料表、檢視表|有索引的資料表或檢視。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsIndexable|資料表、檢視表|可以建立索引的資料表或檢視。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsInlineFunction|函式|內嵌函數。<br /><br /> 1 = 內嵌函數<br /><br /> 0 = 非內嵌函數|  
|IsMSShipped|任何結構描述範圍物件|在安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 期間所建立的物件。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsPrimaryKey|任何結構描述範圍物件|PRIMARY KEY 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> NULL = 不是函數，或物件識別碼無效。|  
|IsProcedure|任何結構描述範圍物件|程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsQuotedIdentOn|[!INCLUDE[tsql](../../includes/tsql-md.md)] 函數、[!INCLUDE[tsql](../../includes/tsql-md.md)] 程序、資料表、[!INCLUDE[tsql](../../includes/tsql-md.md)] 觸發程序、檢視、CHECK 條件約束、DEFAULT 定義|指定物件含引號的識別碼設定是 ON。 這表示用雙引號來分隔物件定義所涉及的所有運算式中之識別碼。<br /><br /> 1 = ON<br /><br /> 0 = OFF|  
|IsQueue|任何結構描述範圍物件|Service Broker 佇列<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsReplProc|任何結構描述範圍物件|複寫程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsRule|任何結構描述範圍物件|繫結規則。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsScalarFunction|函式|純量值函式。<br /><br /> 1 = 純量值函式<br /><br /> 0 = 非純量值函式|  
|IsSchemaBound|函數、檢視表|利用 SCHEMABINDING 來建立的結構描述繫結函數或檢視表。<br /><br /> 1 = 結構描述繫結<br /><br /> 0 = 非結構描述繫結。|  
|IsSystemTable|Table|系統資料表。<br /><br /> 1 = True<br /><br /> 0 = False| 
|IsSystemVerified|Object|SQL Server 可以驗證物件的決定性和有效位數屬性。<br /><br /> 1 = True<br /><br /> 0 = False| 
|IsTable|Table|資料表。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsTableFunction|函式|資料表值函式。<br /><br /> 1 = 資料表值函式<br /><br /> 0 = 非資料表值函式|  
|IsTrigger|任何結構描述範圍物件|觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsUniqueCnst|任何結構描述範圍物件|UNIQUE 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsUserTable|Table|使用者自訂資料表。<br /><br /> 1 = True<br /><br /> 0 = False|  
|IsView|檢視|檢視表。<br /><br /> 1 = True<br /><br /> 0 = False|  
|OwnerId|任何結構描述範圍物件|物件的擁有者。<br /><br /> **注意：** 結構描述擁有者不一定是物件擁有者。 例如，子物件 (其 *parent_object_id* 非 Null) 一律會傳回與父系相同的擁有者識別碼。<br /><br /> 非 Null = 物件擁有者的資料庫使用者識別碼。|  
|SchemaId|任何結構描述範圍物件| 這是物件所屬結構描述的結構描述識別碼。| 
|TableDeleteTrigger|Table|資料表有 DELETE 觸發程序。<br /><br /> >1 = 屬於指定類型的第一個觸發程序識別碼。|  
|TableDeleteTriggerCount|Table|資料表有指定數目的 DELETE 觸發程序。<br /><br /> >0 = DELETE 觸發程序的數目。|  
|TableFullTextMergeStatus|Table|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 資料表是否具有目前正在合併的全文檢索索引。<br /><br /> 0 = 資料表沒有全文檢索索引，或是全文檢索索引並未合併。<br /><br /> 1 = 全文檢索索引正在合併。|  
|TableFullTextBackgroundUpdateIndexOn|Table|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 資料表啟用全文檢索背景更新索引 (自動變更追蹤)。<br /><br /> 1 = TRUE<br /><br /> 0 = FALSE|  
|TableFulltextCatalogId|Table|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 資料表的全文檢索索引資料所在的全文檢索目錄識別碼。<br /><br /> 非零 = 全文檢索目錄識別碼，關聯於用來識別全文檢索索引資料表中的資料列之唯一索引。<br /><br /> 0 = 資料表沒有全文檢索索引。|  
|TableFulltextChangeTrackingOn|Table|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 資料表啟用全文檢索變更追蹤。<br /><br /> 1 = TRUE<br /><br /> 0 = FALSE|  
|TableFulltextDocsProcessed|Table|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 全文檢索索引啟動之後所處理的資料列數。 在建立全文檢索搜尋索引的資料表中，單一資料列的所有資料行都會被視為單一文件的一部分來建立索引。<br /><br /> 0 = 沒有使用中的搜耙，或全文檢索索引已完成。<br /><br /> > 0 = 下列其中之一 (A 或 B)：A) 在啟動完整、累加或手動變更追蹤母體擴展之後，插入或更新作業所處理的文件數目。 B) 啟用含背景更新索引母體擴展的變更追蹤、變更全文檢索索引結構描述、重建全文檢索目錄，或重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體等作業之後，插入或更新作業所處理的資料列數。<br /><br /> NULL = 資料表沒有全文檢索索引。<br /><br /> 這個屬性不會監視或計算已刪除的資料列。|  
|TableFulltextFailCount|Table|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 全文檢索搜尋未建立索引的資料列數。<br /><br /> 0 = 母體擴展已完成。<br /><br /> > 0 = 下列其中之一 (A 或 B)：A) 啟動完整、累加或手動更新變更追蹤母體擴展之後，尚未建立索引的文件數目。 B) 對於背景更新索引的變更追蹤，在開始母體擴展或重新開始母體擴展之後，尚未建立索引的資料列數。 這可能是結構描述變更、重建目錄、伺服器重新啟動等所造成的。<br /><br /> NULL = 資料表沒有全文檢索索引。|  
|TableFulltextItemCount|Table|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 已順利建立全文檢索索引的資料列數。|  
|TableFulltextKeyColumn|Table|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 參與全文檢索索引定義之單一資料行唯一索引的相關資料行識別碼。<br /><br /> 0 = 資料表沒有全文檢索索引。|  
|TableFulltextPendingChanges|Table|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 要處理的暫止變更追蹤項目數。<br /><br /> 0 = 未啟用變更追蹤。<br /><br /> NULL = 資料表沒有全文檢索索引。|  
|TableFulltextPopulateStatus|Table|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 0 = 閒置。<br /><br /> 1 = 完整母體擴展在進行中。<br /><br /> 2 = 累加母體擴展在進行中。<br /><br /> 3 = 追蹤變更的傳播在進行中。<br /><br /> 4 = 背景更新索引在進行中，如自動變更追蹤。<br /><br /> 5 = 全文檢索索引在調整執行速度或暫停。|  
|TableHasActiveFulltextIndex|Table|**適用對象**：[!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本。<br /><br /> 資料表有使用中的全文檢索索引。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasCheckCnst|Table|資料表有 CHECK 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasClustIndex|Table|資料表有叢集索引。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasDefaultCnst|Table|資料表有 DEFAULT 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasDeleteTrigger|Table|資料表有 DELETE 觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasForeignKey|Table|資料表有 FOREIGN KEY 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasForeignRef|Table|資料表由 FOREIGN KEY 條件約束參考。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasIdentity|Table|資料表有識別欄位。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasIndex|Table|資料表有任何類型的索引。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasInsertTrigger|Table|物件有 INSERT 觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasNonclustIndex|Table|資料表有非叢集索引。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasPrimaryKey|Table|資料表有主索引鍵。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasRowGuidCol|Table|資料表有 **uniqueidentifier** 資料行的 ROWGUIDCOL。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasTextImage|Table|資料表有 **text**、**ntext** 或 **image** 資料行。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasTimestamp|Table|資料表有 **timestamp** 資料行。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasUniqueCnst|Table|資料表有 UNIQUE 條件約束。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasUpdateTrigger|Table|物件有 UPDATE 觸發程序。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableHasVarDecimalStorageFormat|Table|資料表可使用 **vardecimal** 儲存格式。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableInsertTrigger|Table|資料表有 INSERT 觸發程序。<br /><br /> >1 = 屬於指定類型的第一個觸發程序識別碼。|  
|TableInsertTriggerCount|Table|資料表有指定數目的 INSERT 觸發程序。<br /><br /> >0 = INSERT 觸發程序的數目。|  
|TableIsFake|Table|資料表不是真正的資料表。 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 在內部視需要而將它具體化。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableIsLockedOnBulkLoad|Table|資料表因 **bcp** 或 BULK INSERT 作業而鎖定。<br /><br /> 1 = True<br /><br /> 0 = False|  
|TableIsMemoryOptimized|Table|**適用對象**：[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及更新版本。<br /><br /> 資料表是記憶體最佳化的<br /><br /> 1 = True<br /><br /> 0 = False<br /><br /> 基底資料類型：**int**<br /><br /> 如需詳細資訊，請參閱[記憶體內部 OLTP &#40;記憶體內部最佳化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)。|  
|TableIsPinned|Table|資料表固定保留在資料快取中。<br /><br /> 0 = False<br /><br /> [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 及更新版本中不支援這項功能。|  
|TableTextInRowLimit|Table|text in row 所允許的最大位元組數目。<br /><br /> 如果未設定 text in row 選項，便是 0。|  
|TableUpdateTrigger|Table|資料表有 UPDATE 觸發程序。<br /><br /> >1 = 具有指定類型之第一個觸發程序的識別碼。|  
|TableUpdateTriggerCount|Table|資料表有指定數目的 UPDATE 觸發程序。<br /><br /> >0 = UPDATE 觸發程序的數目。|  
|TableHasColumnSet|Table|資料表有資料行集。<br /><br /> 0 = False<br /><br /> 1 = True<br /><br /> 如需詳細資訊，請參閱 [使用資料行集](../../relational-databases/tables/use-column-sets.md)。|  
|TableTemporalType|Table|**適用對象**：[!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本。<br /><br /> 指定資料表的類型。<br /><br /> 0 = 非時態表<br /><br /> 1 = 系統版本設定資料表的記錄資料表<br /><br /> 2 = 系統版本設定的時態表|  
  
## <a name="return-types"></a>傳回型別  
 **int**  
  
## <a name="exceptions"></a>例外狀況  
 當發生錯誤，或呼叫端沒有檢視物件的權限時，便會傳回 NULL。  
  
 使用者只能檢視使用者擁有或被授與某些權限之安全性實體的中繼資料。 這表示發出中繼資料的內建函數 (例如，OBJECTPROPERTY) 會在使用者不具有該物件任何權限時傳回 NULL。 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="remarks"></a>備註  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 假設 *object_id* 在目前資料庫內容中。 參考另一資料庫中之 *object_id* 的查詢會傳回 NULL 或不正確的結果。 例如，在下列查詢中，目前資料庫內容是 master 資料庫。 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會嘗試傳回該資料庫中指定之 *object_id* 的屬性值，而不是查詢中所指定的資料庫。 查詢會傳回不正確的結果，因為 `vEmployee` 檢視表不在 master 資料庫中。  
  
```sql  
USE master;  
GO  
SELECT OBJECTPROPERTY(OBJECT_ID(N'AdventureWorks2012.HumanResources.vEmployee'), 'IsView');  
GO  
```  
  
 OBJECTPROPERTY(*view_id*, 'IsIndexable') 可能耗用大量電腦資源，因為評估 IsIndexable 屬性需要剖析檢視定義、正規化和部分最佳化。 雖然 IsIndexable 屬性會識別能夠建立索引的資料表或檢視表，但如果不符合特定索引鍵需求，實際建立索引的作業仍可能失敗。 如需詳細資訊，請參閱 [CREATE INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/create-index-transact-sql.md)。  
  
 當至少加入資料表的一個資料行來建立索引時，OBJECTPROPERTY(*table_id*, 'TableHasActiveFulltextIndex') 會傳回 1 (true) 這個值。 只要加入第一個用來建立索引的資料行，全文檢索索引作業就會成為使用中，以便擴展。  
  
 當建立資料表時，一律會在資料表的中繼資料中，將 QUOTED IDENTIFIER 選項儲存成 ON，即使建立資料表時將選項設成 OFF，也是如此。 因此，OBJECTPROPERTY(*table_id*, 'IsQuotedIdentOn') 一律會傳回 1 (true) 這個值。  
  
## <a name="examples"></a>範例  
  
### <a name="a-verifying-that-an-object-is-a-table"></a>A. 確認物件是資料表  
 下列範例會測試 `UnitMeasure` 是否為 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫中的資料表。  
  
```sql  
USE AdventureWorks2012;  
GO  
IF OBJECTPROPERTY (OBJECT_ID(N'Production.UnitMeasure'),'ISTABLE') = 1  
   PRINT 'UnitMeasure is a table.'  
ELSE IF OBJECTPROPERTY (OBJECT_ID(N'Production.UnitMeasure'),'ISTABLE') = 0  
   PRINT 'UnitMeasure is not a table.'  
ELSE IF OBJECTPROPERTY (OBJECT_ID(N'Production.UnitMeasure'),'ISTABLE') IS NULL  
   PRINT 'ERROR: UnitMeasure is not a valid object.';  
GO  
```  
  
### <a name="b-verifying-that-a-scalar-valued-user-defined-function-is-deterministic"></a>B. 確認純量值使用者定義函數具決定性  
 下列範例會測試使用者定義的純量值函式 `ufnGetProductDealerPrice` (會傳回 **money** 值) 是否具確定性。  
  
```sql  
USE AdventureWorks2012;  
GO  
SELECT OBJECTPROPERTY(OBJECT_ID('dbo.ufnGetProductDealerPrice'), 'IsDeterministic');  
GO  
```  
  
 結果集會顯示 `ufnGetProductDealerPrice` 不是具決定性的函數。  
  
 ```
-----  
0
```  
  
### <a name="c-finding-the-tables-that-belong-to-a-specific-schema"></a>C：尋找屬於特定結構描述的資料表  
 下列範例會傳回 dbo 結構描述中的所有資料表。  
  
```sql  
-- Uses AdventureWorks  
  
SELECT name, object_id, type_desc  
FROM sys.objects   
WHERE OBJECTPROPERTY(object_id, N'SchemaId') = SCHEMA_ID(N'dbo')  
ORDER BY type_desc, name;  
GO  
```  
  
## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-verifying-that-an-object-is-a-table"></a>D：驗證物件是資料表  
 下列範例會測試 `dbo.DimReseller` 是否為 [!INCLUDE[ssawPDW](../../includes/ssawpdw-md.md)] 資料庫中的資料表。  
  
```sql  
-- Uses AdventureWorks  
  
IF OBJECTPROPERTY (OBJECT_ID(N'dbo.DimReseller'),'ISTABLE') = 1  
   SELECT 'DimReseller is a table.'  
ELSE   
   SELECT 'DimReseller is not a table.';  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [COLUMNPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/columnproperty-transact-sql.md)   
 [中繼資料函數 &#40;Transact-SQL&#41;](../../t-sql/functions/metadata-functions-transact-sql.md)   
 [OBJECTPROPERTYEX &#40;Transact-SQL&#41;](../../t-sql/functions/objectpropertyex-transact-sql.md)   
 [ALTER AUTHORIZATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-authorization-transact-sql.md)   
 [TYPEPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/typeproperty-transact-sql.md)   
 [sys.objects &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md)  
  
  

