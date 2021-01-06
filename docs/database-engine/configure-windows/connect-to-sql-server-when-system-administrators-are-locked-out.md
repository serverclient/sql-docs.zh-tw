---
title: 當系統管理員遭鎖定時連線到 SQL Server | Microsoft Docs
description: 了解如果您誤遭鎖定，如何以系統管理員身分重新取得 SQL Server 的存取權。
ms.custom: contperf-fy20q4
ms.date: 05/20/2020
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
helpviewer_keywords:
- sa account
- connecting when locked out [SQL Server]
- locked out [SQL Server]
ms.assetid: c0c0082e-b867-480f-a54b-79f2a94ceb67
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 3b4cf5946fba4eb3c79150b29ef44bd232f9c155
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878941"
---
# <a name="connect-to-sql-server-when-system-administrators-are-locked-out"></a>當系統管理員遭到鎖定時連線到 SQL Server 
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  
此文章描述如果您遭到鎖定，如何以系統管理員身分重新取得 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 的存取權。系統管理員可能因為下列其中一個原因而失去 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的存取權：  
  
-   屬於 sysadmin 固定伺服器角色之成員的所有登入都因為錯誤而遭到移除。  
  
-   屬於 sysadmin 固定伺服器角色之成員的所有 Windows 群組都因為錯誤而遭到移除。  
  
-   屬於 sysadmin 固定伺服器角色之成員的登入供已經離開公司或沒有空的個人使用。  
  
-   sa 帳戶已遭到停用或沒有人知道密碼。  
  
## <a name="resolution"></a>解決方案

為了解決您的存取權問題，建議您以單一使用者模式啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體。 此模式可防止當您嘗試重新取得存取權時發生其他連線。 從這裡，您可以連線到 SQL Server 執行個體，並將登入新增至 **系統管理員 (sysadmin)** 伺服器角色。 [逐步指示](#step-by-step-instructions)一節會提供此解決方案的詳細步驟。


您可以從命令列中使用 `-m` 或 `-f` 選項，以單一使用者模式啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體。 電腦本機系統管理員群組的所有成員接著就能利用 **系統管理員 (sysadmin)** 固定伺服器角色的成員身分，連線到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體。  

當您以單一使用者模式啟動執行個體時，請先停止 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務。 否則，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 可能會先連線，取得與伺服器的唯一可用連線，並封鎖您登入。

在您能夠登入之前，未知的用戶端應用程式也可能取得唯一可用的連線。 為了避免發生這種情況，您可以使用 `-m` 選項，後面接著應用程式名稱，以將連線限制為來自指定應用程式的單一連線。 例如，使用 `-mSQLCMD` 啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，會將連線限制為單一連線，以將自己識別為 **sqlcmd** 用戶端程式。 若要在 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 中透過查詢編輯器連線，請使用 `-m"Microsoft SQL Server Management Studio - Query"`。  


> [!IMPORTANT]  
> 請勿搭配應用程式名稱使用 `-m` 作為安全性功能。 用戶端應用程式會透過連接字串設定來指定應用程式名稱，因此其可利用假的名稱輕鬆地偽造。

下表摘要說明在命令列中，以單一使用者模式啟動執行個體的各種方式。

| 選項 | 描述 | 使用時機 |
|:---|:---|:---|
|`-m` | 將連線限制為單一連線 | 當沒有其他使用者嘗試連線到執行個體，或您不確定用來連線到執行個體的應用程式名稱時。 |
|`-mSQLCMD`| 將連線限制為單一連線，其必須將自己識別為 **sqlcmd** 用戶端程式| 當您計畫使用 **sqlcmd** 連線到執行個體，而且您想要防止其他應用程式取得唯一可用的連線時。 |
|`-m"Microsoft SQL Server Management Studio - Query"`| 將連線限制為單一連線，其必須將自己識別為 **Microsoft SQL Server Management Studio - 查詢** 應用程式。| 當您計畫透過 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 中的查詢編輯器連線到執行個體，而且您想要防止其他應用程式取得唯一可用的連線時。 |
|`-f`| 將連線限制為單一連線，並以基本組態來啟動執行個體 | 當某些其他組態導致您無法啟動時。 |
| &nbsp; | &nbsp; | &nbsp; |
  
如需如何以單一使用者模式啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的逐步指示，請參閱[以單一使用者模式啟動 SQL Server](../../database-engine/configure-windows/start-sql-server-in-single-user-mode.md)。

## <a name="step-by-step-instructions"></a>逐步指示

下列逐步指示描述如何將系統管理員權限授與誤以為不再有存取權的 SQL Server 登入。

這些指示假設：

* [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在 Windows 8 或更新版本上執行。 適當地針對舊版 SQL Server 或 Windows 提供些微調整。

* [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 已安裝於電腦上。  

以本機系統管理員群組成員身分登入 Windows 時，執行這些指示。

1.  在 Windows [開始] 功能表中，以滑鼠右鍵按一下 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員的圖示，然後選擇 [以系統管理員身分執行]，以將您的系統管理員認證傳遞給組態管理員。  
  
2.  在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員的左窗格中，選取 [SQL Server 服務]。 在右窗格中，尋找您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體 ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的預設執行個體會在電腦名稱後面加上 **(MSSQLSERVER)** 。 具名執行個體會以 [已註冊的伺服器] 中顯示的相同大寫名稱出現)。以滑鼠右鍵按一下 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體，然後按一下 [屬性]。  
  
3.  在 [啟動參數] 索引標籤的 [指定啟動參數] 方塊中，輸入 `-m`，然後按一下 [加入] (這是虛線，然後接著小寫字母 m)。  
  
    > [!NOTE]  
    >  如果是某些舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，則沒有 [啟動參數] 索引標籤。在該情況下，請在 [進階] 索引標籤上，按兩下 [啟動參數]。 這些參數就會在非常小的視窗中開啟。 請小心不要變更任何現有參數。 在結尾處，加上新的參數 `;-m`，然後按一下 [確定] (這是分號，然後接著虛線和小寫字母 m)。  
  
4.  按一下 [確定]，在重新啟動的訊息後面，以滑鼠右鍵按一下您的伺服器名稱，然後按一下 [重新啟動]。  
  
5.  重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之後，您的伺服器將處於單一使用者模式。 確定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 並未執行。 如果已啟動，它將會佔用您的唯一連接。  
  
6.  從 Windows [開始] 功能表中，以滑鼠右鍵按一下 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 的圖示，然後選取 [以系統管理員身分執行]。 這樣會將您的系統管理員認證傳遞給 SSMS。
  
    > [!NOTE]  
    >  如果是舊版 Windows，[以系統管理員身分執行] 選項會顯示成子功能表。  
  
     在某些組態中，SSMS 會嘗試建立許多連接。 多重連接將會失敗，因為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處於單一使用者模式。 根據您的情況，執行下列其中一個動作。  
  
    1.  使用 Windows 驗證 (包含您的系統管理員認證) 透過 [物件總管] 連線。 依序展開 [安全性] 和 [登入]，然後按兩下您自己的登入。 在 [伺服器角色] 頁面上，選取 [系統管理員]，然後按一下 [確定]。  
  
    2.  不透過 [物件總管] 連接，而是使用 Windows 驗證 (包含您的系統管理員認證) 透過查詢視窗連接 (如果您沒有透過 [物件總管] 連接，就只能以這種方式連接)。執行程式碼 (例如下列程式碼) 來加入屬於 **系統管理員** 固定伺服器角色成員的新 Windows 驗證登入。 下列範例會加入名為 `CONTOSO\PatK` 的網域使用者。  
  
        ```  
        CREATE LOGIN [CONTOSO\PatK] FROM WINDOWS;  
        ALTER SERVER ROLE sysadmin ADD MEMBER [CONTOSO\PatK];  
        ```  
  
    3.  如果您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 是以混合驗證模式執行，請使用 Windows 驗證 (包含您的系統管理員認證) 透過查詢視窗連接。 執行程式碼 (例如下列程式碼) 來建立屬於 **系統管理員 (sysadmin)** 固定伺服器角色成員的新 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證登入。  
  
        ```  
        CREATE LOGIN TempLogin WITH PASSWORD = '************';  
        ALTER SERVER ROLE sysadmin ADD MEMBER TempLogin;  
        ```  
  
        > [!WARNING]  
        >  請將 ************ 取代成增強式密碼。  
  
    4.  如果您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 是以混合驗證模式執行，而且您想要重設 **sa** 帳戶的密碼，請使用 Windows 驗證 (包含您的系統管理員認證) 透過查詢視窗連接。 您可以使用下列語法來變更 **sa** 帳戶的密碼。  
  
        ```  
        ALTER LOGIN sa WITH PASSWORD = '************';  
        ```  
  
        > [!WARNING]  
        >  請將 ************ 取代成增強式密碼。  

7. 關閉 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)]。  
  
8. 接下來的數個步驟會變更 SQL Server 以回到多使用者模式。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 組態管理員的左窗格中，選取 [SQL Server 服務]。

9. 在右窗格中，以滑鼠右鍵按一下 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體，然後按一下 [屬性]。  
  
10. 在 [啟動參數] 索引標籤的 [現有參數] 方塊中，選取 `-m`，然後按一下 [移除]。  
  
    > [!NOTE]  
    >  如果是某些舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，則沒有 [啟動參數] 索引標籤。在該情況下，請在 [進階] 索引標籤上，按兩下 [啟動參數]。 這些參數就會在非常小的視窗中開啟。 移除您先前加入的 `;-m`，然後按一下 [確定]。  
  
11. 以滑鼠右鍵按一下您的伺服器名稱，然後按一下 [重新啟動]。 如果您在以單一使用者模式啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之前停止了 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent，請務必再次加以啟動。
  
現在，您應該能夠使用其中一個目前屬於 **系統管理員** 固定伺服器角色成員的帳戶進行正常連接。  
  
## <a name="see-also"></a>另請參閱  

* [設定伺服器啟動選項](../../database-engine/configure-windows/scm-services-configure-server-startup-options.md)
* [Database Engine 服務啟動選項](../../database-engine/configure-windows/database-engine-service-startup-options.md)  
