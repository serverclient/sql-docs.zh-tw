---
title: 為可用性群組設定分散式交易
description: '描述如何為 Always On 可用性群組中的資料庫設定分散式交易。 '
ms.custom: seodec18
ms.date: 02/06/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
helpviewer_keywords:
- database mirroring [SQL Server], interoperability
- cross-database transactions [SQL Server]
- transactions [database mirroring]
- Availability Groups [SQL Server], interoperability
- troubleshooting [SQL Server], cross-database transactions
ms.assetid: ''
author: cawrites
ms.author: chadam
ms.openlocfilehash: 93876b488d5e265bca363d213c04cb0611c185f4
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642174"
---
# <a name="configure-distributed-transactions-for-an-always-on-availability-group"></a>為 Always On 可用性群組設定分散式交易
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]

[!INCLUDE[SQL2017](../../../includes/sssqlv14-md.md)] 支援可用性群組中所有的分散式交易，包括資料庫。 本文說明如何設定分散式交易的可用性群組。  

為確保分散式交易，必須設定可用性群組，將資料庫註冊為分散式交易資源管理員。  

>[!NOTE]
>[!INCLUDE[SQL2016](../../../includes/sssql15-md.md)] Service Pack 2 和更新版本提供對於可用性群組中分散式交易的完整支援。 在 Service Pack 2 之前的 [!INCLUDE[SQL2016](../../../includes/sssql15-md.md)] 版本中，不支援牽涉到可用性群組中資料庫的跨資料庫分散式交易 (也就是使用相同 SQL Server 執行個體上資料庫的交易)。 [!INCLUDE[SQL2017](../../../includes/sssqlv14-md.md)] 沒有這項限制。 
>
>[!INCLUDE[SQL2016](../../../includes/sssql15-md.md)] 和 [!INCLUDE[SQL2017](../../../includes/sssqlv14-md.md)] 的組態步驟相同。

在分散式交易中，用戶端應用程式使用 Microsoft Distributed Transaction Coordinator (MS DTC 或 DTC) 保證跨多個資料來源的交易一致性。 DTC 是受支援的 Windows Server 型作業系統上提供的服務。 就分散式交易而言，DTC 是「交易協調器」  。 一般而言，SQL Server 執行個體是「資源管理員」  。 當資料庫位在可用性群組中時，每個資料庫都需要是自己的資源管理員。 

[!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 不能防止可用性群組中的資料庫分散式交易，即使未針對分散式交易設定可用性群組。 但是當未針對分散式交易設定可用性群組時，容錯移轉在某些情況下可能不會成功。 特別是新的主要複本 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 執行個體可能無法從 DTC 取得交易結果。 若要在容錯移轉之後，讓 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 執行個體從 DTC 取得可疑交易的結果，請針對分散式交易設定可用性群組。 

除非資料庫也是容錯移轉叢集的成員，否則 DTC 不會牽涉到可用性群組處理。 在可用性群組內，複本之間的一致性是由可用性群組邏輯所維護：主要複本不會完成認可並認同對呼叫端的認可，直到次要複本確認主要複本已將記錄保存在永久性儲存體中。 只有在此時，主要複本會宣告交易完成。 在非同步模式中，我們不會等候次要複本認可，且的確有可能遺失少量資料。

## <a name="prerequisites"></a>Prerequisites

設定可用性群組支援分散式交易之前，必須先符合下列必要條件：

* 所有參與分散式交易的 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 執行個體都必須是 [!INCLUDE[SQL2016](../../../includes/sssql15-md.md)] 或更新版本。

* 可用性群組必須在 Windows Server 2016 或 Windows Server 2012 R2 上執行。 針對 Windows Server 2012 R2，您必須安裝 [https://support.microsoft.com/kb/3090973](https://support.microsoft.com/kb/3090973) 上所提供 KB3090973 中的更新。  

## <a name="create-an-availability-group-for-distributed-transactions"></a>建立分散式交易的可用性群組

設定可用性群組支援分散式交易。 設定可用性群組允許每個資料庫註冊為資源管理員。 本文會說明如何設定可用性群組，讓每個資料庫在 DTC 中都是資源管理員。



您可以在 [!INCLUDE[SQL2016](../../../includes/sssql15-md.md)] 或更新版本上建立分散式交易的可用性群組。 在可用性群組定義中建立分散式交易的可用性群組，包括 `DTC_SUPPORT = PER_DB`。 下列指令碼會建立分散式交易的可用性群組。 

```sql
CREATE AVAILABILITY GROUP MyAG
   WITH (
      DTC_SUPPORT = PER_DB  
      )
   FOR DATABASE DB1, DB2
   REPLICA ON
      'Server1' WITH (
         ENDPOINT_URL = 'TCP://SERVER1.corp.com:5022',  
         AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,  
         FAILOVER_MODE = AUTOMATIC  
         ),
      'Server2' WITH (
         ENDPOINT_URL = 'TCP://SERVER2.corp.com:5022',  
         AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,  
         FAILOVER_MODE = AUTOMATIC  
         )
```

>[!NOTE]
>上述指令碼是簡單的可用性群組範例，不是針對任何特定的生產環境所設計。 

## <a name="alter-an-availability-group-for-distributed-transactions"></a>改變分散式交易的可用性群組

您可以在 [!INCLUDE[SQL2017](../../../includes/sssqlv14-md.md)] 或更新版本上改變分散式交易的可用性群組。 以 `ALTER AVAILABILITY GROUP` 指令碼改變分散式交易的可用性群組，包括 `DTC_SUPPORT = PER_DB`。 範例指令碼變更可用性群組，以支援分散式交易。 

```sql
ALTER AVAILABILITY GROUP MyaAG
   SET (
      DTC_SUPPORT = PER_DB  
      );
```

>[!NOTE]
>從 [!INCLUDE[SQL2016](../../../includes/sssql15-md.md)] Service Pack 2 開始，您可以改變分散式交易的可用性群組。 若為 Service Pack 2 之前的 [!INCLUDE[SQL2016](../../../includes/sssql15-md.md)] 版本，您必須卸除可用性群組並使用 `DTC_SUPPORT = PER_DB` 設定來重新建立。 

若要停用分散式交易，請使用下列 Transact-SQL 命令：

```sql
ALTER AVAILABILITY GROUP MyaAG
   SET (
      DTC_SUPPORT = NONE  
      );
```

## <a name="distributed-transactions---technical-concepts"></a><a name="distTran"/>分散式交易 - 技術概念

分散式交易跨越二或多個資料庫。 身為交易管理員，DTC 會協調 SQL Server 執行個體和其他資料來源之間的交易。 每個 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 資料庫引擎的執行個體都可以當成資源管理員運作。 當可用性群組設定有 `DTC_SUPPORT = PER_DB` 時，資料庫可以當成資源管理員運作。 如需詳細資訊，請參閱 MS DTC 文件集。

在資料庫引擎的單一執行個體中，有二或多個資料庫的交易，實際上就是分散式交易。 執行個體是由內部來管理分散式交易；而對於使用者而言則是以本機交易來運作。 在可用性群組中使用 `DTC_SUPPORT = PER_DB` 設定資料庫時，即使是在單一的 SQL Server 執行個體中，[!INCLUDE[SQL2017](../../../includes/sssqlv14-md.md)] 都會升級所有 DTC 的跨資料庫交易。 

在應用程式中，分散式交易的管理與本機交易大致相同。 交易結束時，應用程式便要求認可或回復交易。 分散式認可必須另由交易管理員來管理，以便將網路失敗可能會造成某些資源管理員成功認可而其他資源管理員卻將交易回復的風險降至最低。 這可藉由在兩個階段 (準備階段與認可階段) 中管理認可過程來達到，稱為兩階段認可交易。

- **準備階段**
   
   當交易管理員接收到認可的要求時，便傳送準備命令給所有參與交易的資源管理員。 然後再由每個資源管理員進行可讓交易持續，並把放置交易記錄檔影像的所有緩衝區排清到磁碟上所需的一切動作。 當每個資源管理員完成準備階段時，便將準備的成功或失敗結果傳回交易管理員。

- **認可階段**
   
   如果交易管理員從所有的資源管理員接收到準備成功，便傳送認可命令給每個資源管理員。 然後資源管理員即可完成認可。 如果全部的資源管理員都報告認可成功，交易管理員便傳送成功的通知給應用程式。 若有任何資源管理員報告準備失敗，交易管理員便傳送回復命令給每個資源管理員並告知應用程式認可失敗。

### <a name="detailed-steps"></a>詳細步驟

下列清單說明應用程式如何利用 DTC 完成分散式交易。

1. SQL Server 執行個體登錄在 DTC 交易中。 當交易中有多個資源管理員，或若用戶端要求將交易升級為 DTC 交易時，就可能發生這種情況。
2. 用戶端在 DTC 交易下的 SQL Server 執行個體中完成某些工作。
3. 用戶端發出認可或中止 DTC 交易。
    - 如果用戶端發出中止，就會立即中止交易。
    - 如果用戶端發出認可，DTC 會要求位在交易中的所有資源管理員準備交易，開始兩階段認可通訊協定。
4. 在所有資源管理員都成功認可準備階段之後，DTC 會通知所有資源管理員認可交易。 如有任何事阻礙成功認可，DTC 會中止交易。 

### <a name="effects-of-configuring-an-availability-group-for-distributed-transactions"></a>設定分散式交易可用性群組的效果

每個參與分散式交易的實體都稱為資源管理員。 資源管理員的範例包括：

* [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 執行個體。 
* 可用性群組中已針對分散式交易設定的資料庫。
* DTC 服務 - 也可以是交易管理員。
* 其他資料來源。 

為參與分散式交易，[!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 的執行個體向 DTC 登錄。 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 執行個體一般是向本機伺服器上的 DTC 登錄。 每個 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 執行個體都會建立具有唯一資源管理員識別碼 (RMID) 的資源管理員，並向 DTC 登錄。 在預設組態中，[!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 執行個體上的所有資料庫都使用相同的 RMID。 

當資料庫位在可用性群組中時，資料庫的讀寫複本 (或主要複本) 可以移到其他的 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 執行個體。 為在此移動期間支援分散式交易，每個資料庫都應該像個別的資源管理員一樣行動，並必須有唯一的 RMID。 當可用性群組有 `DTC_SUPPORT = PER_DB` 時，[!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 會為每個資料庫建立資源管理員，並使用唯一的 RMID 向 DTC 登錄。 在此組態中，資料庫是 DTC 交易的資源管理員。

如需 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 中的分散式交易詳細資料，請參閱[分散式交易](#distTran)。

## <a name="manage-unresolved-transactions"></a>管理未解析的交易

RMID 變更期間存在的使用中交易結果，無法在容錯移轉之後復原。 這是因為用來登錄的 RMID SQL Server 和用來復原的 RMID SQL Server 不同。 下列情況會發生 RMID 變更：

* 變更可用性群組的 `DTC_SUPPORT`。 
* 新增或移除可用性群組中的資料庫。 
* 置放可用性群組。

在上述情況中，如果主要複本容錯移轉到新的 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 執行個體，執行個體會嘗試連絡 DTC 以識別交易結果。 因為資料庫在復原期間用來取得可疑交易結果的 RMID 未曾登錄過，所以 DTC 無法傳回結果。 因此，資料庫會進入 SUSPECT 狀態。

新的 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 錯誤記錄檔有一個類似下例的項目：

```
Microsoft Distributed Transaction Coordinator (MS DTC) 
failed to reenlist citing that the database RMID does 
not match the RMID [xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx] 
associated with the transaction.  Please manually resolve
the transaction.
    
SQL Server detected a DTC/KTM in-doubt transaction with UOW 
{yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy}.Please resolve it 
following the guideline for Troubleshooting DTC Transactions.
```

上例示範 DTC 無法從新的主要複本重新登錄資料庫，而該主要複本位在容錯移轉後所建立的交易中。 [!INCLUDE[SQLServer](../../../includes/ssnoversion-md.md)] 執行個體無法判斷分散式交易的結果，因此它會將資料庫標示為可疑。 交易標示為工作單位 (UOW)，為 GUID 所參考。 為復原資料庫，請以手動方式認可或復原交易。 

>[!WARNING]
>當您以手動方式認可或復原交易時，會影響應用程式。 確認認可或復原動作是否與應用程式需求一致。 

執行下列指令碼之一：

   * 認可交易，請更新並執行下列指令碼：以前一則錯誤訊息中的可疑交易 UOW 取代 `yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy`，然後執行：

   ```sql
   KILL 'yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy' WITH COMMIT
   ```

   * 復原交易，請更新並執行下列指令碼：以前一則錯誤訊息中的可疑交易 UOW 取代 `yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy`，然後執行：

   ```sql
   KILL 'yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy' WITH ROLLBACK
   ```

認可或復原交易之後，您可以使用 `ALTER DATABASE` 將資料庫設定為線上。 更新並執行下列指令碼：將資料庫名稱設為可疑資料庫的名稱：

   ```sql
   ALTER DATABASE [DB1] SET ONLINE
   ```

如需解析可疑交易的詳細資訊，請參閱[手動解析交易](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754134(v=ws.10))。

## <a name="next-steps"></a>後續步驟  

[分散式交易](/dotnet/framework/data/adonet/distributed-transactions)

[Always On 可用性群組：互通性 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/always-on-availability-groups-interoperability-sql-server.md)  
  
[交易 - AlwaysOn 可用性群組和資料庫鏡像](transactions-always-on-availability-and-database-mirroring.md)  

[支援 XA 交易](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753563(v=ws.10))

[How It Works:Session/SPID (-2) for DTC Transactions](/archive/blogs/bobsql/how-it-works-sessionspid-2-for-dtc-transactions) (運作方式：DTC 交易的工作階段/SPID (-2))