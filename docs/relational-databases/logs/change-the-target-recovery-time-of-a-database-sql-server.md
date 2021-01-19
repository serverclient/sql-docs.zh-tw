---
title: 變更資料庫的目標復原時間
description: 了解如何使用 SQL Server Management Studio 或 Transact-SQL，在 SQL Server 中設定或變更 SQL Server 資料庫的目標復原時間。
ms.date: 08/24/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: supportability
ms.topic: conceptual
ms.assetid: e466419a-d8a4-48f7-8d97-13a903ad6b15
author: MashaMSFT
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 00c7778583545c56b886bfc30c4763bacd1404b3
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171560"
---
# <a name="change-the-target-recovery-time-of-a-database-sql-server"></a>變更資料庫的目標復原時間 (SQL Server)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  本主題描述如何使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 設定或 [!INCLUDE[tsql](../../includes/tsql-md.md)] 在 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 中設定或變更 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫的目標復原時間。 根據預設，目標復原時間為 60 秒，而且資料庫使用「間接檢查點」。 目標復原時間會建立此資料庫的復原時間上限。  
  
> [!NOTE]  
>  如果長時間執行的交易造成過多的復原次數，可能會超過目標復原時間設定針對給定資料庫所指定的上限。  
  
-   **開始之前：** [限制事項](#Restrictions)、[安全性](#Security)  
  
-   **使用以下方式變更目標復原時間：** [SQL Server Management Studio](#SSMSProcedure) 或 [Transact-SQL](#TsqlProcedure)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 限制事項 
  
> [!CAUTION]  
>  設定間接檢查點的資料庫線上交易式工作負載可能會導致效能降低。 間接檢查點可確定中途分頁的數目，低於特定臨界值，如此即可在目標復原時間內完成資料庫的復原。 相對於使用中途分頁數目的間接檢查點，復原間隔設定選項會使用交易數目來判斷復原時間。 在接收到大量 DML 作業的資料庫上啟用間接檢查點時，背景寫入器就會開始積極地將中途緩衝區排清到磁碟，以確保執行復原所需的時間會在資料庫上所設定的目標復原時間內。 這會在某些系統上造成額外的 I/O 活動，如果磁碟子系統的運作超過或接近 I/O 閾值，就會形成效能瓶頸。  
  
###  <a name="security"></a><a name="Security"></a> Security  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要資料庫的 ALTER 權限。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
 **若要變更目標復原時間**  
  
1.  在 [物件總管] 中，連接到 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)]的執行個體，然後展開該執行個體。  
  
2.  展開 [資料庫] 容器、以滑鼠右鍵按一下您想要變更的資料庫，然後按一下 [屬性] 命令。  
  
3.  在 [資料庫屬性]  對話方塊中按一下 [選項]  頁面。  
  
4.  在 [復原] 面板的 [目標復原時間 (秒)] 欄位中，指定您想要作為此資料庫復原時間上限的秒數。  

##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
 **若要變更目標復原時間**  
  
1.  連接到資料庫所在的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。  
  
2.  使用下列 [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql-set-options.md) 陳述式，如下所示：  
  
     TARGET_RECOVERY_TIME **=** _target_recovery_time_ { SECONDS | MINUTES }  
  
     *目標復原時間*  
     從 [!INCLUDE[ssSQL15_md](../../includes/sssql16-md.md)]開始，預設值為 1 分鐘。 如果大於 0 (舊版的預設值)，便指定發生損毀時，指定之資料庫的復原時間上限。  
  
     SECONDS  
     指出 *target_recovery_time* 應以秒數表示。  
  
     MINUTES  
     指出 *target_recovery_time* 應以分鐘數表示。  
  
     下列範例會將 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫的目標復原時間設定為 `60` 秒。  
  
    ```  
    ALTER DATABASE AdventureWorks2012 SET TARGET_RECOVERY_TIME = 60 SECONDS;  
    ```  
  
## <a name="see-also"></a>另請參閱  
 [資料庫檢查點 &#40;SQL Server&#41;](../../relational-databases/logs/database-checkpoints-sql-server.md)   
 [ALTER DATABASE SET 選項 &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)  
  
  
