---
title: 註冊鏡像資料庫 | Microsoft Docs
description: 了解如何藉由將鏡像資料庫新增至資料庫鏡像監視器，在伺服器執行個體上註冊這些鏡像資料庫，以快取資料庫的相關資訊。
ms.custom: ''
ms.date: 03/07/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
f1_keywords:
- sql13.swb.dbmmonitor.registermirroreddb.f1
ms.assetid: 6acd02b9-2311-49b0-a5f8-3852beecb4b0
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 5dfb3293071982720908c61368a475376db24dd3
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641305"
---
# <a name="register-mirrored-database"></a>註冊鏡像資料庫
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  使用這個對話方塊可在特定的伺服器執行個體上註冊一或多個鏡像資料庫，只要將資料庫加入至「資料庫鏡像監視器」即可。 加入資料庫之後，「資料庫鏡像監視器」就會在本機快取有關資料庫、其夥伴以及如何連接到夥伴的資訊。  
  
> [!IMPORTANT]  
>  如果您在主體伺服器執行個體上是 **系統管理員** 固定伺服器角色的成員，但是在鏡像伺服器執行個體上不是該角色的成員，則只能在主體伺服器執行個體上查看狀態。  
  
 **若要使用 SQL Server Management Studio 監視資料庫鏡像**  
  
-   [啟動資料庫鏡像監視器 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-database-mirroring-monitor-sql-server-management-studio.md)  
  
## <a name="options"></a>選項。  
 **伺服器執行個體**  
 從包含「資料庫鏡像監視器」已經儲存連接之伺服器執行個體的清單中選取一個伺服器執行個體，或是按一下 [連接] 。 若要為清單中所列的伺服器執行個體指定新的認證，請按一下 [連接]  並使用新的認證來連接。  
  
> [!NOTE]  
>  若要在多個伺服器執行個體上註冊資料庫，當您檢查完其中一個伺服器執行個體所需的資料庫之後，請按一下 [套用] ，然後選取另一個伺服器執行個體。  
  
 **[連接]**  
 若要為伺服器執行個體指定新的認證，請按一下 [連接]  並使用新的認證來連接。 在連接到伺服器執行個體時，「資料庫鏡像監視器」會顯示 [正在等候資料] 。  
  
 **鏡像資料庫**  
 [鏡像資料庫]  方格會列出伺服器執行個體上的鏡像資料庫。  
  
 方格包含下列資料行：  
  
|資料行名稱|描述|  
|-----------------|-----------------|  
|**註冊**|檢查您要註冊的每一個資料庫。 如果資料庫目前受到監視，則其核取方塊為已選取和停用狀態。<br /><br /> 注意:若要取消註冊資料庫，請關閉 [註冊鏡像資料庫] 對話方塊，在導覽樹狀目錄中選取資料庫，然後選取 [動作] 功能表中的 [取消註冊]。|  
|**Database**|選取之伺服器執行個體上的鏡像資料庫名稱。|  
|**目前的角色**|資料庫目前在選取之伺服器執行個體上的鏡像角色，可為 [主體] 或 [鏡像]。|  
|**夥伴 (連接為)**|資料庫的容錯移轉夥伴名稱。 括弧內會顯示 **主控台使用者的 Windows 驗證，** 或 **登入 '** _\<login name>_ *_' 的 SQL Server 驗證_*。 如果之前已加入這個執行個體，則這是目前使用的驗證資訊；如果尚未將這個執行個體加入至監視器，則這是將要使用的驗證資訊。|  
  
 **當我按一下確定時顯示管理伺服器連接對話方塊**  
 依預設，若是先前未給予認證的夥伴伺服器執行個體，「資料庫鏡像監視器」會使用「Windows 驗證」認證。 當您完成資料庫的註冊時，啟用此選項即可變更一個或多個伺服器執行個體的認證。  
  
 如果已啟用此選項，當您按一下 [確定] 時，[管理伺服器連接]  對話方塊便會開啟。 您可以在這個對話方塊中選擇您要指定認證的伺服器執行個體，以供監視器連接到特定容錯移轉夥伴時使用。  
  
 若要編輯夥伴的認證，請在 [伺服器執行個體]  方格中找到其項目，然後按一下該資料列上的 [編輯]  。 此時便會開啟該伺服器執行個體名稱的 [連接到伺服器]  對話方塊，而且會將認證控制項初始化為目前的快取值。 視需要變更認證，然後按一下 [連接] 。 如果認證具有足夠的權限，就會以新的認證更新 [連接方式]  資料行。  
  
 **套用**  
 按一下這個按鈕即可註冊選取的資料庫 (並儲存夥伴伺服器執行個體的認證)，同時將對話方塊保持為開啟狀態。  
  
## <a name="see-also"></a>另請參閱  
 [啟動資料庫鏡像監視器 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-database-mirroring-monitor-sql-server-management-studio.md)   
 [監視資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/monitoring-database-mirroring-sql-server.md)   
 [啟動設定資料庫鏡像安全性精靈 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/start-the-configuring-database-mirroring-security-wizard.md)  
  
  
