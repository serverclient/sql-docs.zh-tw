---
title: 資料庫鏡像和資料庫快照集
description: 了解如何搭配資料庫鏡像使用資料庫快照集，以在 SQL Server 中卸載報表的互通性。
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], interoperability
- snapshots [SQL Server database snapshots], database mirroring
- database snapshots [SQL Server], database mirroring
ms.assetid: 0bf1be90-7ce4-484c-aaa7-f8a782f57c5f
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: d4dc48ce78438ef6b2a50576cde51cbe2bb12085
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642132"
---
# <a name="database-mirroring-and-database-snapshots-sql-server"></a>資料庫鏡像和資料庫快照集 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  您可以利用為了提供可用性而維護的鏡像資料庫卸載報表。 若要對報表使用鏡像資料庫，請在鏡像資料庫上建立資料庫快照集，然後將用戶端連接要求導向最近一次的快照集。 資料庫快照集是其來源資料庫的一個靜態、唯讀、交易一致的快照集，存在於快照集建立時。 若要在鏡像資料庫上建立資料庫快照集，資料庫必須處於同步處理的鏡像狀態。  
  
 不像鏡像資料庫，用戶端可以存取資料庫快照集。 只要鏡像伺服器仍與主體伺服器進行通訊，就可以將報表用戶端導向連接到快照集。 請注意，因為資料庫快照集是靜態的，所以不會有新的資料可用。 若要使較新的資料可供使用者使用，您必須定期建立新的資料庫快照集，並使應用程式與最新快照集之間有直接傳入用戶端的連接。  
  
 新的資料庫快照集幾乎是空的，但它會隨著時間成長，因為有愈來愈多資料庫頁面都是第一次更新。 由於資料庫上的每個快照集都會以這種方式累加成長，所以每個資料庫快照集會與一般資料庫取用同等的資源。 根據鏡像伺服器和主體伺服器的組態而定，如果鏡像資料庫上有太多資料庫快照集，可能就會降低主體資料庫的效能。 因此，我們建議您僅在鏡像資料庫上保留一些較新的快照集。 通常，在建立取代快照集之後，您應該將傳入的查詢重新導向至新的快照集，並在完成任何現行查詢之後卸除較早的快照集。  
  
> [!NOTE]  
>  如需資料庫快照集的詳細資訊，請參閱 [資料庫快照集 &#40;SQL Server&#41;](../../relational-databases/databases/database-snapshots-sql-server.md)。  
  
 如果發生角色切換，會重新啟動資料庫及其快照集，並暫時中斷使用者。 然後，資料庫快照集會留在當初建立它們的伺服器執行個體上，該執行個體已變成新的主體資料庫。 使用者可在容錯移轉之後，繼續使用快照集。 不過，這會為新的主體伺服器帶來額外負荷。 如果效能是您環境中的一項考量，我們建議您在新的鏡像資料庫 (當它可用時) 建立快照集，再將用戶端重新導向至新的快照集，然後從先前的鏡像資料庫中卸除所有資料庫快照集。  
  
> [!NOTE]  
>  如需能有效擴充的專用報表解決方案，請考慮複寫。 如需詳細資訊，請參閱 [SQL Server Replication](../../relational-databases/replication/sql-server-replication.md)。  
  
## <a name="example"></a>範例  
 此範例會在鏡像資料庫上建立快照集。  
  
 假設資料庫鏡像工作階段的資料庫為 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)]。 此範例會在 `AdventureWorks` 資料庫的鏡像副本上建立三個資料庫快照集，該資料庫位於 `F` 磁碟機上。 這些快照集名為 `AdventureWorks_0600`、 `AdventureWorks_1200`及 `AdventureWorks_1800` ，用以識別其大約的建立時間。  
  
1.  在 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)]的鏡像上建立第一個資料庫快照集。  
  
    ```  
    CREATE DATABASE AdventureWorks_0600  
    ON (NAME = 'datafile', FILENAME = 'F:\AdventureWorks_0600.SNP')  
       AS SNAPSHOT OF AdventureWorks2012  
    ```  
  
2.  在 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)]的鏡像上建立第二個資料庫快照集： 仍在使用 `AdventureWorks_0600` 的使用者可以繼續使用。  
  
    ```  
    CREATE DATABASE AdventureWorks_1200  
    ON (NAME = 'datafile', FILENAME = 'F:\AdventureWorks_1200.SNP')  
       AS SNAPSHOT OF AdventureWorks2012  
    ```  
  
     如此一來，可以利用程式將新用戶端連接導向最新的快照集。  
  
3.  在 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)]鏡像上建立第三個快照集。 仍在使用 `AdventureWorks_0600` 或 `AdventureWorks_1200` 的使用者可以繼續使用。  
  
    ```  
    CREATE DATABASE AdventureWorks_1800  
    ON (NAME = 'datafile', FILENAME = 'F:\AdventureWorks_1800.SNP')  
        AS SNAPSHOT OF AdventureWorks2012  
    ```  
  
     如此一來，可以利用程式將新用戶端連接導向最新的快照集。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [建立資料庫快照集 &#40;Transact-SQL&#41;](../../relational-databases/databases/create-a-database-snapshot-transact-sql.md)  
  
-   [檢視資料庫快照集 &#40;SQL Server&#41;](../../relational-databases/databases/view-a-database-snapshot-sql-server.md)  
  
-   [卸除資料庫快照集 &#40;Transact-SQL&#41;](../../relational-databases/databases/drop-a-database-snapshot-transact-sql.md)  
  
  
## <a name="see-also"></a>另請參閱  
 [資料庫快照集 &#40;SQL Server&#41;](../../relational-databases/databases/database-snapshots-sql-server.md)   
 [將用戶端連接至資料庫鏡像工作階段 &#40;SQL Server&#41;](../../database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server.md)  
  
  
