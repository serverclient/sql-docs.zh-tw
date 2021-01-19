---
title: 使用記憶體最佳化資料表的需求 | Microsoft 文件
description: 了解使用記憶體內部 OLTP 的需求，包括 SQL Database 版本、記憶體和儲存體考量事項，以及安裝。
ms.custom: ''
ms.date: 11/24/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: in-memory-oltp
ms.topic: conceptual
ms.assetid: 47d9a7e8-c597-4b95-a58a-dcf66df8e572
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 0cbe2b75a46e63b5e388b91ace3d74c0db3be49b
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170730"
---
# <a name="requirements-for-using-memory-optimized-tables"></a>使用記憶體最佳化資料表的需求
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  若要在 Azure DB 中使用記憶體內部 OLTP，請參閱 [在 SQL Database 中開始使用 In-Memory](/azure/azure-sql/in-memory-oltp-overview)。  
  
 除了[安裝 SQL Server 的硬體與軟體需求](../../sql-server/install/hardware-and-software-requirements-for-installing-sql-server.md)之外，使用記憶體內部 OLTP 的還有以下需求：  
  
-   若為 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 (或更新版本)，任何版本皆可。 若為 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 及 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] RTM (SP1 以前的版本)，則需要 Enterprise、Developer 或 Evaluation 版本。
    
    > [!NOTE]
    > 記憶體內部 OLTP 需要 64 位元版本的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 需要有足夠的記憶體來容納記憶體最佳化資料表和索引中的資料，以及額外的記憶體來支援線上工作負載。 如需詳細資訊，請參閱 [估計記憶體最佳化資料表的記憶體需求](../../relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables.md) 。  

-   在虛擬機器 (VM) 中執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時，請確定配置給 VM 的記憶體足以支援記憶體最佳化資料表和索引所需的記憶體。 視 VM 主應用程式而定，保證配置記憶體給 VM 的組態選項可稱為「保留的記憶體」；使用動態記憶體時，則稱為「RAM 下限」。 請確定這些設定對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]中的資料庫需求已足夠。
  
-   釋放持久的記憶體最佳化資料表大小的兩倍磁碟空間。  
  
-   處理器需要支援指令 **cmpxchg16b** 以使用記憶體內部 OLTP。 所有新型 64 位元處理器都支援 **cmpxchg16b**。  
  
     如果您使用虛擬機器，而 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 顯示較舊處理器造成的錯誤，請查看 VM 主應用程式是否有允許 **cmpxchg16b** 的組態選項。 如果沒有，您可以使用 Hyper-V，它可以支援 **cmpxchg16b** ，而不需要修改組態選項。  
  
-   記憶體內部 OLTP 會安裝為 **Database Engine Services** 的一部分。  
  
     若要安裝報表產生功能 ([判斷是否應將資料表或預存程序移植到記憶體內部 OLTP](../../relational-databases/in-memory-oltp/determining-if-a-table-or-stored-procedure-should-be-ported-to-in-memory-oltp.md)) 以及 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] (透過 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 物件總管來管理記憶體內部 OLTP)，請 [下載 SQL Server Management Studio (SSMS)](../../ssms/download-sql-server-management-studio-ssms.md)。   
  
## <a name="important-notes-on-using-hek_2"></a>使用 [!INCLUDE[hek_2](../../includes/hek-2-md.md)] 的重要注意事項  
  
-   自 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 起，記憶體最佳化資料表除了可用的記憶體之外，沒有任何大小限制。 

-   在 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 中，資料庫內所有耐久資料表的記憶體中大小總計，不應超過 250 GB。 如需詳細資訊，請參閱 [估計記憶體最佳化資料表的記憶體需求](../../relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables.md)。  

> [!NOTE]
> 自 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] SP1 起，Standard 和 Express 版本皆支援記憶體內部 OLTP，但會用到在指定資料庫用於記憶體最佳化資料表的記憶體數量的配額。 Standard 版本是每個資料庫 32 GB，Express 版本是每個資料庫 352 MB。 
  
-   如果您建立一或多個具有記憶體最佳化資料表的資料庫，即應將 *SE_MANAGE_VOLUME_NAME* 使用者權利授與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務啟動帳戶，以啟用檔案立即初始化 (IFI)。 如果沒有 IFI，記憶體最佳化儲存體檔案 (資料與差異檔案) 將會在建立時進行初始化，而如此可能會對工作負載的效能產生負面的影響。 如需 IFI 的詳細資訊及啟用方式，請參閱[資料庫檔案立即初始化](../../relational-databases/databases/database-instant-file-initialization.md)。
  
## <a name="see-also"></a>另請參閱  
 [記憶體內部 OLTP &#40;記憶體內部最佳化&#41;](../../relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization.md)  
 [資料庫檔案立即初始化](../../relational-databases/databases/database-instant-file-initialization.md)  
 [記憶體架構指南](../../relational-databases/memory-management-architecture-guide.md)
