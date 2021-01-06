---
title: 新增或取代鏡像見證 (SSMS)
description: 了解當資料庫鏡像端點使用 Windows 驗證時，如何使用 SQL Server Management Studio 來新增或取代資料庫鏡像見證。
ms.custom: seo-lt-2019
ms.date: 03/07/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- witness [SQL Server], establishing
- database mirroring [SQL Server], witness
ms.assetid: 4b5ecffd-f025-4ab7-b69d-8958c6477127
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 397a7b11c5188a94186f1618ced09cf2043bec5a
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643622"
---
# <a name="add-or-replace-a-database-mirroring-witness-sql-server-management-studio"></a>加入或取代資料庫鏡像見證 (SQL Server Management Studio)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  如果資料庫鏡像端點使用 Windows 驗證，您就可以使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 來加入或取代見證。 在 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 中加入見證也會將作業模式變更為具有自動容錯移轉的高安全性模式。  
  
> [!NOTE]  
>  我們強烈建議見證應該位在任何夥伴的另一台電腦上。 見證使用的服務帳戶必須與主體和鏡像伺服器執行個體使用的服務帳戶位於相同的網域中，否則此帳戶就必須位於受信任網域中。  
  
### <a name="to-add-or-replace-a-witness"></a>若要加入或取代見證  
  
1.  連接到主體伺服器執行個體後，在 [物件總管] 中按一下伺服器名稱，以展開伺服器樹狀目錄。  
  
2.  展開 **[資料庫]** ，然後選取您要加入或取代見證之工作階段的主體資料庫。  
  
3.  以滑鼠右鍵按一下資料庫，選取 [工作]，然後按一下 [鏡像]。 這將會開啟在 **[資料庫屬性]** 對話方塊中的 **[鏡像]** 頁面。  
  
4.  按一下 **[設定安全性]** 。  
  
5.  如果出現 **[設定資料庫鏡像安全性精靈]** 歡迎畫面，請按 **[下一步]** 。  
  
6.  在 **[包含見證伺服器]** 對話方塊中，按一下 **[是]** ，然後按 **[下一步]** 。  
  
7.  在 **[選擇要設定的伺服器]** 對話方塊中，會自動核取 **[見證伺服器執行個體]** 核取方塊。 按 [下一步] 。  
  
8.  在 **[主體伺服器執行個體]** 對話方塊中，保留現有的通訊埠和端點。 按 [下一步] 。  
  
9. 在 **[見證伺服器執行個體]** 對話方塊中，按一下 **[連接]** 。  
  
10. 在 [連接到伺服器] 對話方塊的 [伺服器名稱] 欄位中，指定見證伺服器執行個體，並使用 Windows 驗證 (預設值)。 按一下 [ **連接**]。  
  
11. 一旦連接建立後， **[見證伺服器執行個體]** 對話方塊中就會顯示見證伺服器執行個體的接聽程式通訊埠和資料庫鏡像端點。 按 [下一步] 。  
  
12. **[服務帳戶]** 對話方塊會包含主體、鏡像及見證伺服器執行個體之網域服務帳戶的欄位。  
  
    -   如果這些伺服器執行個體都使用相同的服務帳戶，請將這些欄位保留空白。  
  
    -   如果見證伺服器執行個體與其中一個夥伴使用不同的服務帳戶，請在 **[主體]** 、 **[鏡像]** 及 **[見證]** 欄位中填入帳戶名稱：  
  
         網域名稱 **\\** 使用者名稱  
  
         網域名稱必須使用大寫。  
  
     按 [下一步] 。  
  
13. 在 **[完成精靈]** 摘要畫面中，選擇性地驗證見證組態，然後按一下 **[完成]** 。  
  
14. 完成之後，精靈就會讓您返回 **[資料庫屬性]** 對話方塊，而且見證的伺服器網路位址現在會顯示在 **[見證]** 欄位中。 此外，會自動選取見證所需的 [具有自動容錯移轉的高安全性 (同步)]。  
  
     若要啟用見證並將工作階段變更為具有自動容錯移轉的高安全性模式，請按一下 [確定]。  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像見證](../../database-engine/database-mirroring/database-mirroring-witness.md)   
 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)   
 [資料庫屬性 &#40;鏡像頁面&#41;](../../relational-databases/databases/database-properties-mirroring-page.md)   
 [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md)   
 [資料庫鏡像見證](../../database-engine/database-mirroring/database-mirroring-witness.md)  
  
  
