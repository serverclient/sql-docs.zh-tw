---
description: 複寫至記憶體最佳化資料表訂閱者
title: 複寫至記憶體最佳化資料表訂閱者 | Microsoft 文件
ms.custom: ''
ms.date: 11/21/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: conceptual
ms.assetid: 1a8e6bc7-433e-471d-b646-092dc80a2d1a
author: MashaMSFT
ms.author: mathoma
monikerRange: =azuresqldb-mi-current||>=sql-server-2016
ms.openlocfilehash: 71a245f6263b938cef7a3841b3570a3a9137a1db
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172200"
---
# <a name="replication-to-memory-optimized-table-subscribers"></a>複寫至記憶體最佳化資料表訂閱者
[!INCLUDE [SQL Server SQL MI](../../includes/applies-to-version/sql-asdbmi.md)]

  作為快照集和異動複寫訂閱者的資料表 (不包括點對點異動複寫) 可以設定為記憶體最佳化資料表。 其他複寫組態與記憶體最佳化資料表不相容。 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]開始，將提供這項功能。  
  
## <a name="two-configurations-are-required"></a>需要兩個組態  
  
-   **將訂閱者資料庫設定為支援對記憶體最佳化資料表的複寫**  
  
     藉由使用 [sp_addsubscription &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addsubscription-transact-sql.md) 或 [sp_changesubscription &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-changesubscription-transact-sql.md)，將 **\@memory_optimized** 屬性設定為 **true**。  
  
-   **將發行項設定為支援對記憶體最佳化資料表的複寫**  
  
     使用 [sp_addarticle &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addarticle-transact-sql.md) 或 [sp_changearticle &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-changearticle-transact-sql.md) 來設定 `@schema_option = 0x40000000000` 發行項選項。  
  
#### <a name="to-configure-a-memory-optimized-table-as-a-subscriber"></a>將記憶體最佳化資料表設定為訂閱者  
  
1.  建立交易式發行集。 如需詳細資訊，請參閱[建立發行集](../../relational-databases/replication/publish/create-a-publication.md)。  
  
2.  將發行項加入至發行集。 如需詳細資訊，請參閱 [定義發行項](../../relational-databases/replication/publish/define-an-article.md)。  
  
     如果使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 進行設定，將 **sp_addarticle** 預存程序的 **\@schema_option** 參數設為   
    **0x40000000000** 開始，將提供這項功能。  
  
3.  在 [發行項屬性] 視窗中，將 啟用記憶體最佳化  設定為 [true] 。  
  
4.  啟動快照集代理程式作業來產生此發行集的初始快照集。 如需詳細資訊，請參閱 [建立和套用初始快照集](../../relational-databases/replication/create-and-apply-the-initial-snapshot.md)。  
  
5.  現在建立一項新訂閱。 在 [新增訂閱精靈]  中，將 記憶體最佳化訂閱  設定為 [true] 。  

 記憶體最佳化資料表現在應該會開始接收來自發行者的更新。  
  
#### <a name="reconfigure-an-existing-transaction-replication"></a>重新設定現有的交易複寫  
  
1.  移至 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 的訂閱屬性，並將 記憶體最佳化訂閱  設定為 [true] 。 重新初始化訂閱之前，不會套用所做的變更。  
  
     如果使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 進行設定，將 **sp_addsubscription** 預存程序的 **\@memory_optimized** 參數設為 true。  
  
2.  移至 [發行項屬性] 視窗 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 內的發行集，將 啟用記憶體  設定為 [true]。  
  
     如果使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 進行設定，將 **sp_addarticle** 預存程序的 **\@schema_option** 參數設為   
    **0x40000000000** 開始，將提供這項功能。  
  
3.  記憶體最佳化資料表不支援叢集索引。 若要藉由在目的地上將複寫轉換為非叢集索引來處理此項目的話，請將 為記憶體最佳化發行項將叢集索引轉換為非叢集索引  設定為 [true]。  
  
     如果使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 進行設定，將 **sp_addarticle** 預存程序的 **\@schema_option** 參數設為 **0x0000080000000000**。  
  
4.  重新產生快照集。  
  
5.  重新初始化這項訂閱。  
  
## <a name="remarks-and-restrictions"></a>備註與限制  
 僅支援單向異動複寫。 不支援點對點異動複寫。  
  
 無法發行記憶體最佳化資料表。  
  
 散發者端的複寫資料表無法設定為記憶體最佳化資料表。  
  
 合併式複寫無法包含記憶體最佳化資料表。  
  
 在訂閱者端，異動複寫中包含的資料表可以設定為記憶體最佳化資料表，但是訂閱者資料表必須符合記憶體最佳化資料表的需求。 需求的限制如下。  
 
-   複寫至訂閱者端記憶體最佳化資料表的資料表限制，則為記憶體最佳化資料表中允許的資料類型。 如需詳細資訊，請參閱 [記憶體內部 OLTP 支援的資料類型](../../relational-databases/in-memory-oltp/supported-data-types-for-in-memory-oltp.md)。  
  
-   記憶體最佳化資料表中並未支援所有的 Transact-SQL 功能。 如需詳細資訊，請參閱[記憶體內部 OLTP 不支援的 Transact-SQL 建構](../../relational-databases/in-memory-oltp/transact-sql-constructs-not-supported-by-in-memory-oltp.md)。  
  
##  <a name="modifying-a-schema-file"></a><a name="Schema"></a> 修改結構描述檔案  
  
-   如果使用記憶體最佳化資料表選項 `DURABILITY = SCHEMA_AND_DATA` ，則資料表必須具有非叢集主索引鍵索引。  
  
-   ANSI_PADDING 必須為 ON。  
  
  
  
