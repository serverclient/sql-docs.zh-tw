---
title: 將功能新增至 SQL Server 的執行個體 (安裝程式)
description: 此文章提供將執行個體感知功能新增至 SQL Server 2019 執行個體的逐步程序。
ms.prod: sql
ms.technology: install
ms.topic: conceptual
helpviewer_keywords:
- feature adding [SQL Server]
- " SQL Server, features"
- adding features to  SQL Server
ms.assetid: 97931fdc-d943-48dd-81b9-ae8b8d2c6dad
author: cawrites
ms.author: chadam
ms.reviewer: ''
ms.custom: ''
ms.date: 09/07/2019
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: a8a0e3db546f4a74e1ec9bcc3c6312d8ca848777
ms.sourcegitcommit: 3ec49252e82590de0fe559a8574606ae213f6f3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97975471"
---
# <a name="add-features-to-an-instance-of-sql-server-setup"></a>將功能新增至 SQL Server 的執行個體 (安裝程式)

[!INCLUDE [ SQL Server - Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

本文提供將功能新增至 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 執行個體的逐步程序。 某些 SQL Server 元件或服務為 SQL Server 執行個體特有， 也可感知執行個體。 這些元件或服務也會與裝載它們的執行個體共用相同的版本，並專門用於該執行個體。 如果還未安裝執行個體感知元件與其共用元件，可以將其加入執行個體 SQL Server。 如需不同 SQL Server 版本支援的功能清單，請參閱 [SQL Server 2017 版本和支援的功能](../../sql-server/editions-and-components-of-sql-server-2017.md)或 [SQL Server 2019](../../sql-server/editions-and-components-of-sql-server-version-15.md)。

若要從命令提示字元，將功能新增至 SQL Server 的執行個體，請參閱[從命令提示字元安裝 SQL Server](./install-sql-server-from-the-command-prompt.md)。

## <a name="prerequisites"></a>必要條件

在繼續進行之前，請檢閱[規劃 SQL Server 安裝](../../sql-server/install/planning-a-sql-server-installation.md)中的文章。

> [!NOTE]
> 如果是本機安裝，您必須以管理員身分執行安裝程式。 如果您是從遠端共用位置安裝 SQL Server，則必須使用對遠端共用位置具有讀取權限的網域帳戶。  
  
> [!NOTE]
> 當您將功能加入 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 的執行個體時，現有的使用方式報表設定會套用至新加入的功能。 若要變更這些設定，請使用 SQL Server [組態工具] 功能表中的 [SQL Server 錯誤和使用方式報表] 工具。

## <a name="procedures"></a>程序

#### <a name="to-add-features-to-an-instance-of-sscurrent"></a>若要從命令提示字元，將功能加入 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]

1. 插入 SQL Server 安裝媒體。 在根資料夾中，按兩下 setup.exe。 若要從網路共用進行安裝，請導覽至共用上的根資料夾，然後按兩下 setup.exe。 若出現 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] [安裝程式] 對話方塊，請選取 [確定] 以安裝先決條件，然後選取 [取消] 以結束 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 安裝。  

2. 安裝精靈將會啟動 SQL Server 安裝中心。 若將功能加入現有的 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 執行個體，請在左側導覽區域中，選取 [安裝]，然後選取 [新增 SQL Server 獨立安裝或將功能加入至現有安裝]。

3. 系統組態檢查將會在電腦上執行探索作業。 若要檢視驗證詳細資料，請選取 [檢視詳細資料]。 若要繼續，請選取 [確定]。

4. 最新可用的 SQL Server 產品更新隨即會顯示在 [產品更新] 頁面上。 如果不想包含更新，請清除 [包含 SQL Server 產品更新程式] 核取方塊。 如果未偵測到任何產品更新，SQL Server 安裝程式將不會顯示此頁面，而會自動前往 [安裝安裝程式檔案] 頁面。

5. 安裝程式會在 [安裝安裝程式檔案] 頁面上，顯示下載、擷取及安裝安裝程式檔案的進度。 如有找到 SQL Server 安裝程式的更新，並指定要包含該更新，將會一併安裝。 選取 [安裝]，以安裝安裝程式支援檔案。  

6. 系統組態檢查將會先確認電腦的系統狀態，然後安裝程式才會繼續進行。  

7. 在 [安裝類型] 頁面上，選取 [將功能加入到現有的 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 執行個體] 選項，然後選取要更新的執行個體。

8. 在 [特徵選取] 頁面上，選取要安裝的元件。 當您選取功能名稱之後，每一個元件群組的描述就會出現在右手邊窗格中。 您可以選取核取方塊的任何組合。 如需詳細資訊，請參閱 [SQL Server 2017 的版本和支援功能](../../sql-server/editions-and-components-of-sql-server-2017.md)和 [SQL Server 2019](../../sql-server/editions-and-components-of-sql-server-version-15.md)。 在給定的 SQL Server 執行個體上，每個元件都只能安裝一次。 若要安裝多個元件，您必須安裝其他 SQL Server 執行個體。

    右窗格會顯示選取功能的必要條件。 SQL Server 安裝程式將會在這個程序稍後說明的安裝步驟期間安裝尚未安裝的必要條件。

    系統組態檢查將會先確認電腦的系統狀態，然後安裝程式才會繼續進行。 選取 [下一步] 以繼續進行操作。

9. [磁碟空間需求] 頁面會計算您所指定之功能的所需磁碟空間，並且比較空間需求與執行安裝程式之電腦的可用磁碟空間。

10. 依據您針對安裝所指定的功能而定，本文其餘部分的工作流程會有所不同。 您可能不會看到所有頁面，端視您的選取項目而定。

11. 在 [伺服器設定 - 服務帳戶] 頁面中，指定 SQL Server 服務的登入帳戶。 在這個頁面上所設定的實際服務隨著您選取要安裝的功能而不同。

    您可以將相同登入帳戶指派給所有 SQL Server 服務，或個別設定每一個服務帳戶。 此外，您也可以指定要自動啟動服務、手動啟動服務或停用服務。 Microsoft 建議您個別設定服務帳戶，以針對每個服務提供最低權限，其中 SQL Server 服務會獲授與完成其工作所需的最低權限。 如需詳細資訊，請參閱 [伺服器組態 - 服務帳戶](./install-sql-server.md) 和 [設定 Windows 服務帳戶與權限](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)。

    若要針對此 SQL Server 執行個體中的所有服務帳戶指定相同的登入帳戶，請在頁面底部的欄位中提供認證。

    **安全性注意事項** ： [!INCLUDE[ssNoteStrongPass](../../includes/ssnotestrongpass-md.md)]

    當您完成為 SQL Server 服務指定登入資訊之後，請選取 [下一步]。

12. 使用 [伺服器組態 - 定序] 索引標籤，為資料庫引擎與 Analysis Services 指定非預設的定序。 如需詳細資訊，請參閱[伺服器組態 - 定序](./install-sql-server.md)。

13. 使用 [資料庫引擎組態 - 帳戶提供] 頁面來指定以下項目：  

    - 安全性模式 - 為 SQL Server 執行個體選取 Windows 驗證或混合模式驗證。 如果選取 [混合模式驗證]，就必須為內建的 SQL Server 系統管理員帳戶 (SA) 提供強式密碼。

        當裝置與 SQL Server 建立成功的連線之後，Windows 驗證和混合模式的安全性機制是相同的。 如需詳細資訊，請參閱 [Database Engine 組態 - 伺服器組態](./install-sql-server.md)。  

    - SQL Server 管理員 - 您在 SQL Server 執行個體上至少必須指定一個系統管理員。 若要新增正在執行 SQL Server 安裝程式的帳戶，請選取 [新增目前的使用者]。 若要從系統管理員清單中加入或移除帳戶，請選取 [加入] 或 [移除]，然後編輯在 SQL Server 執行個體中將會有系統管理員權限的使用者、群組或電腦清單。 如需詳細資訊，請參閱 [Database Engine 組態 - 伺服器組態](./install-sql-server.md)。

    當您完成清單的編輯之後，請選取 [確定]。 然後，在組態對話方塊中確認管理員的清單。 當清單完成後，請選取 [下一步]。

14. 使用 [資料庫引擎組態 - 資料目錄] 頁面來指定非預設的安裝目錄。 若要安裝到預設目錄，請選取 [下一步]。  

    > [!IMPORTANT]
    > 若要指定非預設的安裝目錄，請確定安裝資料夾對於此 SQL Server 執行個體是唯一的。 此對話方塊上的任何目錄都不應該與其他 SQL Server 執行個體中的目錄共用。

    如需詳細資訊，請參閱 [Database Engine 組態 - 資料目錄](./install-sql-server.md)。

15. 使用 [資料庫引擎組態 - FILESTREAM] 頁面針對您的 SQL Server 執行個體啟用 FILESTREAM。 如需 FILESTREAM 的詳細資訊，請參閱 [Database Engine 組態 - Filestream](./install-sql-server.md)。 若要繼續，請選取 [下一步]。

16. 使用 [Analysis Services 設定 - 帳戶佈建] 頁面指定伺服器模式以及擁有 Analysis Services 系統管理員權限的使用者或帳戶。 伺服器模式會決定在伺服器上所要使用的記憶體及儲存體子系統。 不同方案類型會在不同的伺服器模式中執行。 如果要在伺服器上執行多維度 Cube 資料庫，請選擇預設選項 [多維度及資料採礦伺服器模式]。 關於系統管理員權限，至少必須為 Analysis Services 指定一個系統管理員。 若要新增正在執行 SQL Server 安裝程式的帳戶，請選取 [新增目前的使用者]。 若要從系統管理員清單中加入或移除帳戶，請選取 [加入] 或 [移除]，然後編輯在 Analysis Services 中將會有系統管理員權限的使用者、群組或電腦清單。 如需伺服器模式及系統管理員權限的詳細資訊，請參閱 [Analysis Services 組態 - 帳戶提供](./install-sql-server.md)。

    當您完成清單的編輯之後，請選取 [確定]。 然後，在組態對話方塊中確認管理員的清單。 當清單完成後，請選取 [下一步]。

17. 使用 [Analysis Services 組態 - 資料目錄] 頁面來指定非預設的安裝目錄。 若要安裝到預設目錄，請選取 [下一步]。

    > [!IMPORTANT]
    > 若要指定非預設的安裝目錄，請確定安裝資料夾對於此 SQL Server 執行個體是唯一的。 此對話方塊上的任何目錄都不應該與其他 SQL Server 執行個體中的目錄共用。

    如需詳細資訊，請參閱 [Analysis Services 組態 - 資料目錄](./install-sql-server.md)。

18. 使用 [Reporting Services 組態] 頁面，指定要建立的 Reporting Services 安裝類型。 如需 Reporting Services 組態模式的詳細資訊，請參閱 [Reporting Services 組態選項 &#40;SSRS&#41;](./install-sql-server.md)。

19. 您可以使用 [Distributed Replay Controller 組態] 頁面，指定要授與 Distributed Replay Controller 服務之管理權限的使用者。 擁有管理權限的使用者將可不受限制地存取 Distributed Replay Controller 服務。

    選取 [新增目前使用者] 按鈕，以新增要授與 Distributed Replay Controller 服務存取權限的使用者。 選取 [新增] 按鈕，以新增 Distributed Replay Controller 服務的存取權限。 選取 [移除] 按鈕，以移除 Distributed Replay Controller 服務的存取權限。

    若要繼續，請選取 [下一步]。

20. 您可以使用 [Distributed Replay Client 組態] 頁面，指定要授與 Distributed Replay Client 服務之管理權限的使用者。 擁有管理權限的使用者將可不受限制地存取 Distributed Replay Client 服務。

    **Controller Name** 是選擇性參數，且預設值為 \<*blank*>。 針對 Distributed Replay Client 服務，輸入將與用戶端電腦進行通訊之控制器的名稱。 請注意：

    - 如果您已經設定了控制器，請在設定每個用戶端時輸入該控制器的名稱。

    - 如果您尚未設定控制器，則可以將控制器名稱留白。 不過，您必須在 [用戶端組態]  檔中，手動輸入控制器名稱。

    指定 Distributed Replay Client 服務的 **工作目錄** 。 預設工作目錄為 \<*drive letter*>:\Program Files\Microsoft\SQL Server\DReplayClient\WorkingDir。

    指定 Distributed Replay Client 服務的 **結果目錄** 。 預設結果目錄為 \<*drive letter*>:\Program Files\Microsoft\SQL Server\DReplayClient\ResultDir。

    若要繼續，請選取 [下一步]。

21. 在 [錯誤報告] 頁面上，指定您想要傳送給 Microsoft 的資訊，以便協助改善 SQL Server。 錯誤報告選項預設為啟用。

22. System Configuration Checker 將會執行一組額外的規則，以便使用您已指定的 SQL Server 功能來驗證電腦組態。  

23. [準備安裝] 頁面會顯示在安裝期間指定之安裝選項的樹狀檢視。 在此頁面上，安裝程式會指出產品更新功能為啟用或停用，以及最後的更新版本。 選取 [安裝] 之後，SQL Server 安裝程式會先安裝所選功能所需的先決條件，然後再安裝功能。  

24. 在安裝期間，[安裝進度] 頁面會提供狀態，所以您可以在安裝程式進行時監視安裝進度。  

25. 安裝之後，[完成] 頁面會提供安裝和其他重要注意事項之摘要記錄檔的連結。 若要完成 SQL Server 安裝程序，請選取 [關閉]。

26. 如果指示您重新啟動電腦，請立刻執行。 當您完成安裝時，請務必閱讀安裝精靈所提供的訊息。 如需安裝程式記錄檔的資訊，請參閱 [檢視與讀取 SQL Server 安裝程式記錄檔](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)。

## <a name="next-steps"></a>後續步驟

設定 SQL Server 安裝

- 為了減少系統的可攻擊介面區，SQL Server 可以選擇性地安裝和啟用主要服務和功能。 如需詳細資訊，請參閱＜ [Surface Area Configuration](../../relational-databases/security/surface-area-configuration.md)＞。

## <a name="see-also"></a>另請參閱
- [檢視與讀取 SQL Server 安裝程式記錄檔](../../database-engine/install-windows/view-and-read-sql-server-setup-log-files.md)
- [驗證 SQL Server 安裝](../../database-engine/install-windows/validate-a-sql-server-installation.md)
- [修復失敗的 SQL Server 2016 安裝](../../database-engine/install-windows/repair-a-failed-sql-server-installation.md)
- [使用安裝精靈升級 SQL Server &#40;安裝程式&#41;](../../database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup.md)
- [從命令提示字元安裝 SQL Server](./install-sql-server-from-the-command-prompt.md)
