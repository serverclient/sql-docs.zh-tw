---
description: 複寫資料分割資料表及索引
title: 複寫資料分割資料表及索引 | Microsoft Docs
ms.custom: ''
ms.date: 09/10/2015
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: conceptual
helpviewer_keywords:
- partitioned indexes [SQL Server], replicating
- partitioned tables [SQL Server], replicating
- replication [SQL Server], partitioned tables
- publishing [SQL Server replication], partitioned tables
- transactional replication, partitioned tables
ms.assetid: c9fa81b1-6c81-4c11-927b-fab16301a8f5
author: MashaMSFT
ms.author: mathoma
monikerRange: =azuresqldb-mi-current||>=sql-server-2016
ms.openlocfilehash: 75c0f38e67fd4d02791c5d2b953f4354a5945dc2
ms.sourcegitcommit: e5664d20ed507a6f1b5e8ae7429a172a427b066c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697140"
---
# <a name="replicate-partitioned-tables-and-indexes"></a>複寫資料分割資料表及索引
[!INCLUDE[sql-asdbmi](../../../includes/applies-to-version/sql-asdbmi.md)]
  分割大型資料表或索引將更易於管理，因為分割可讓您快速並有效率地管理及存取資料子集，同時又可維護資料收集的完整性。 如需詳細資訊，請參閱＜ [Partitioned Tables and Indexes](../../../relational-databases/partitions/partitioned-tables-and-indexes.md)＞。 複寫可支援資料分割，其方式是提供一組屬性來指定應該如何處理資料分割資料表和索引。  
  
## <a name="article-properties-for-transactional-and-merge-replication"></a>交易式和合併式複寫的發行項屬性  
 下表列出用來分割資料的物件。  
  
|Object|建立的方法|  
|------------|----------------------|  
|資料分割資料表或索引|CREATE TABLE 或 CREATE INDEX|  
|分割區函數|CREATE PARTITION FUNCTION|  
|分割區配置|CREATE PARTITION SCHEME|  
  
 資料分割屬性為發行項結構描述選項，可決定是否應將資料分割物件複製到訂閱者。 可透過下列方式來設定這些結構描述選項：  
  
-   在新增發行集精靈的 **[發行項屬性]** 頁面中，或是在 [發行集屬性] 對話方塊中。 若要複製上表所列的物件，請針對 **[複製資料表資料分割配置]** 和 **[複製索引資料分割配置]** 屬性指定 **true** 的值。 如需如何存取 [發行項屬性] 頁面的詳細資訊，請參閱[檢視和修改發行集屬性](../../../relational-databases/replication/publish/view-and-modify-publication-properties.md)。  
  
-   藉由使用下列其中一個預存程序的 *schema_option* 參數：  
  
    -   用於異動複寫的[sp_addarticle](../../../relational-databases/system-stored-procedures/sp-addarticle-transact-sql.md) 或 [sp_changearticle](../../../relational-databases/system-stored-procedures/sp-changearticle-transact-sql.md) f或 transactional replication  
  
    -   用於合併式複寫的[sp_addmergearticle](../../../relational-databases/system-stored-procedures/sp-addmergearticle-transact-sql.md) 或 [sp_changemergearticle](../../../relational-databases/system-stored-procedures/sp-changemergearticle-transact-sql.md) f或 merge replication  
  
     若要複製上表所列的物件，請指定適當的結構描述選項值。 如需有關如何指定結構描述選項的詳細資訊，請參閱＜ [Specify Schema Options](../../../relational-databases/replication/publish/specify-schema-options.md)＞。  
  
 複寫會在初始同步處理期間將物件複製到訂閱者。 如果資料分割配置使用 PRIMARY 以外的檔案群組，這些檔案群組必須在初始同步處理之前存在於訂閱者上。  
  
 在初始化訂閱者之後，資料變更會傳播到訂閱者，並套用到適當的資料分割。 但是，不支援資料分割配置的變更。 交易式及合併複寫無法複寫下列命令：ALTER PARTITION FUNCTION、ALTER PARTITION SCHEME 或 ALTER INDEX 的 REBUILD WITH PARTITION 陳述式。 這些命令所關聯的變更不會自動複寫到訂閱者。 使用者必須在訂閱者中手動進行類似的變更。  
  
## <a name="replication-support-for-partition-switching"></a>資料分割切換的複寫支援  
 資料表資料分割的其中一個重要優點，就是能夠快速及有效率地在資料分割之間移動資料子集。 資料的移動是利用 SWITCH PARTITION 命令。 根據預設，當啟用資料表進行複寫時，會基於以下理由而封鎖 SWITCH PARTITION 作業：  
  
-   如果資料移入或移出存在於發行者上但不存在於訂閱者上的資料表，則發行者和訂閱者彼此可能會不一致。 當資料移入或移出臨時資料表時，通常會發生這個問題。  
  
-   如果訂閱者對於資料分割資料表的定義與發行者不同，則當散發代理程式嘗試在訂閱者上套用變更時，將會失敗。  
  
雖然有這些潛在的問題，還是可以啟用資料分割切換來進行異動複寫。 在您啟用資料分割切換之前，請確定與資料分割切換有關的所有資料表都存在於發行者和訂閱者上，並確定資料表定義和資料分割定義是相同的。  
  
當資料分割在發行者端和訂閱者端擁有完全相同的資料分割配置時，您可以開啟 *allow_partition_switch* 和 *replication_partition_switch*，其只會將資料分割切換陳述式複寫至訂閱者。 您也可以在不複寫 DDL 的情況下，開啟 *allow_partition_switch* 。 當您想要將舊的月份傳出資料分割，但針對其他年份，則將複寫的資料分割保留在原位以便在訂閱者端進行備份時，這非常實用。  
  
若您透過目前的版本啟用了 SQL Server 2008 R2 的資料分割切換功能，您可能很快就會需要執行分割及合併作業。 對複寫或啟用 CDC 的資料表執行分割或合併作業之前，請確認目標資料分割並沒有任何擱置的複寫命令。 此外也必須確定未在分割及合併作業期間，對分割區執行任何 DML 作業。 如果有記錄讀取器或 CDC 擷取作業尚未處理的交易，或是在執行分割或合併作業時，對複寫或啟用 CDC 之資料表的資料分割執行 DML 作業 (涉及相同的資料分割)，這可能會導致記錄讀取器代理程式或 CDC 擷取作業發生處理錯誤 (錯誤 608 - 找不到資料分割識別碼的目錄項目)。 若要更正此錯誤，可能需要將訂用帳戶重新初始化或停用該資料表或資料庫上的 CDC。 

### <a name="unsupported-scenarios"></a>不支援的案例

使用複寫進行資料分割切換時，不支援下列案例： 

**點對點複寫**   
資料分割切換不支援點對點複寫。 

**使用變數進行資料分割切換**   

針對 `ALTER TABLE ... SWITCH TO ... PARTITION ...` 陳述式，不支援在使用異動複寫或異動資料擷取 (CDC) 發佈的資料表上搭配資料分割切換使用變數。

例如，在資料庫上啟用 CDC，或 TableA 參與交易式發行集的情況下，將無法使用下列資料分割切換程式碼： 

```sql
DECLARE @SomeVariable INT = $PARTITION.pf_test(10);
ALTER TABLE dbo.TableA
SWITCH TO dbo.TableB 
PARTITION @SomeVariable;
```

相反地，請直接使用資料分割函數來切換資料分割，如下列範例所示： 

```sql
ALTER TABLE NonPartitionedTable 
SWITCH TO PartitionedTable PARTITION $PARTITION.pf_test(10);
```


### <a name="enabling-partition-switching"></a>啟用資料分割切換  
 交易式發行集的下列屬性可讓使用者控制複寫環境中的資料分割切換行為：  
  
-   `@allow_partition_switch`，當設定為 `true` 時，SWITCH PARTITION 可以針對發行集資料庫來執行。  
  
-   `@replicate_partition_switch` 會決定 SWITCH PARTITION DDL 陳述式是否應該複寫到訂閱者。 只有當 `@allow_partition_switch` 設定為 `true` 時，這個選項才有效。  
  
 您可以在建立發行集時使用 [sp_addpublication](../../../relational-databases/system-stored-procedures/sp-addpublication-transact-sql.md) 來設定這些屬性，或是在建立發行集之後使用 [sp_changepublication](../../../relational-databases/system-stored-procedures/sp-changepublication-transact-sql.md) 來設定。 如前所述，合併式複寫不支援資料分割切換。 若要在已啟用合併式複寫的資料表上執行 SWITCH PARTITION，請從發行集中移除此資料表。  
  
## <a name="see-also"></a>另請參閱  
 [發行資料和資料庫物件](../../../relational-databases/replication/publish/publish-data-and-database-objects.md)  
  
  
