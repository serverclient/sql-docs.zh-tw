---
title: 資料庫立即檔案初始化
description: 了解檔案立即初始化，以及如何在您的 SQL Server 資料庫上加以啟用。
ms.custom: contperf-fy20q4
ms.date: 07/24/2020
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
helpviewer_keywords:
- initializing files [SQL Server]
- instant file initialization [SQL Server]
- fast file initialization [SQL Server]
- file initialization [SQL Server]
- IFI [SQL Server]
- database instant file initialization [SQL Server]
ms.assetid: 1ad468f5-4f75-480b-aac6-0b01b048bd67
author: stevestein
ms.author: sstein
ms.openlocfilehash: 6598dc9812aed56b170c22d0ed1b4a470c5f7209
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878749"
---
# <a name="database-instant-file-initialization"></a>資料庫立即檔案初始化
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
在此文章中，您將了解檔案立即初始化，以及如何加以啟用來加速 SQL Server 資料庫檔案的成長。  

根據預設，系統會將資料和記錄檔初始化，以覆寫磁碟上先前刪除之檔案中所遺留的任何現有資料。 當您執行下列作業時，會先將資料檔與記錄檔清空 (填入 0)，以初始化檔案：  
  
- 建立資料庫。  
- 將資料或記錄檔新增至現有的資料庫。  
- 增加現有檔案的大小 (包括自動成長作業)。  
- 還原資料庫或檔案群組。  

在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，檔案立即初始化 (IFI) 可讓您更快速地執行先前提到的檔案作業，因為其會回收已使用的磁碟空間，而不需以零填滿該空間。 而是在新資料寫入檔案時將磁碟內容覆寫為新資料。 記錄檔無法立即初始化。


## <a name="enable-instant-file-initialization"></a>啟用檔案立即初始化

只有在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務啟動帳戶被授與 *SE_MANAGE_VOLUME_NAME* 時，才能使用檔案立即初始化。 Windows Administrator 群組的成員擁有此權限，並可將此權限授與其他使用者 (方法是將他們新增到「執行磁碟區維護工作」  安全性原則。  
> [!IMPORTANT]
> 使用[透明資料加密 (TDE)](../../relational-databases/security/encryption/transparent-data-encryption.md) 等某些功能時，會防止檔案立即初始化。  

> [!NOTE]
> 從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始，此權限可在安裝期間進行安裝時授與給服務帳戶。 <br><br>如果使用[命令提示字元安裝](../../database-engine/install-windows/install-sql-server-from-the-command-prompt.md)，請新增 /SQLSVCINSTANTFILEINIT 引數，或在[安裝精靈](../../database-engine/install-windows/install-sql-server-from-the-installation-wizard-setup.md)中核取 [對 SQL Server Database Engine 服務授與「執行磁碟區維護工作」權限] 方塊。
  
授與帳戶「 `Perform volume maintenance tasks` 」權限：  
  
1.  在要建立資料檔案的電腦上，開啟 [本機安全性原則] 應用程式 (`secpol.msc`)。  
  
1.  在左窗格中，展開 [本機原則] ，然後按一下 [使用者權限指派] 。  
  
1.  在右窗格中，按兩下 [執行磁碟區維護工作]。  
  
1.  按一下 [新增使用者或群組] 並新增執行 SQL Server 服務的帳戶。  
  
1.  按一下 [套用] ，然後關閉所有 [本機安全性原則]  對話方塊。  

1. 重新啟動 SQL Server 服務。

1. 請在啟動時檢查 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔。
   
  
    **適用範圍：** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP4、[!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 與 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 和更新版本開始)。
    1. 如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務啟動帳戶獲授與 *SE_MANAGE_VOLUME_NAME*，會記錄如下資訊訊息：

        `Database Instant File Initialization: enabled. For security and performance considerations see the topic 'Database Instant File Initialization' in SQL Server Books Online. This is an informational message only. No user action is required.`

    1. 如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務啟動帳戶 **未** 獲授與 *SE_MANAGE_VOLUME_NAME*，會記錄如下資訊訊息：

        `Database Instant File Initialization: disabled. For security and performance considerations see the topic 'Database Instant File Initialization' in SQL Server Books Online. This is an informational message only. No user action is required.`
    > [!NOTE]
    > 您也可以使用 [sys.dm_server_services](../../relational-databases/system-dynamic-management-views/sys-dm-server-services-transact-sql.md) DMV 中的資料行 *instant_file_initialization_enabled* 來識別是否已啟用檔案立即初始化。

## <a name="security-considerations"></a>安全性考量

我們建議您啟用檔案立即初始化，因為這麼做的好處可能超過安全性風險。

使用檔案立即初始化時，只有在將新資料寫入檔案時，才會覆寫已刪除的磁碟內容。 基於這個理由，直到其他資料寫入資料檔特定區域之前，未經授權的主體可能得以存取刪除的內容。

當資料庫檔案附加到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體之際，檔案上的判別存取控制清單 (DACL) 可降低此一資訊洩漏風險。 此 DACL 只允許 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶和本機系統管理員存取檔案。 但是，當檔案卸離後，沒有 *SE_MANAGE_VOLUME_NAME* 的使用者或服務便能存取該檔案。

在下列情況中，有類似的考量因素：

* *資料庫已備份。* 如果備份檔案未使用適當的 DACL 保護，未經授權的使用者或服務便可存取刪除的內容。  

* *使用 IFI 讓檔案成長*。 SQL Server 系統管理員可能會存取原始頁面內容，並查看先前刪除的內容。

* *資料庫檔案裝載於存放區域網路*。 存放區域網路也可能會一律以預先初始化方式顯示新頁面，因此讓作業系統重新初始化頁面可能是不必要的額外負荷。

如果洩漏刪除的內容可能是一項隱憂，您應該採取下列其中一或兩個項動作：  
  
- 務必確保所有卸離的資料檔和備份檔都具有限制的 DACL。  
- 針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體停用檔案立即初始化。    若要這樣做，請從 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務啟動帳戶撤銷 *SE_MANAGE_VOLUME_NAME*。
    
    > [!NOTE]
    > 停用將增加資料檔案的配置時間，並只會影響使用者權限撤銷後建立或大小增加的檔案。
  
### <a name="se_manage_volume_name-user-right"></a>SE_MANAGE_VOLUME_NAME 使用者權限

您可在 [Windows 系統管理工具]、[本機安全性原則] 小程式中，指派 *SE_MANAGE_VOLUME_NAME* 使用者權限。 在 [本機原則] 下，選取 [使用者權限指派] 並修改 [執行磁碟區維護工作] 屬性。

## <a name="performance-considerations"></a>效能考量

資料庫檔案初始化程序會將零寫入至檔案中要初始化的新區域。 此程序其時間長短取決於所初始化的檔案部分大小，以及儲存系統的回應時間和容量。 如果初始化需要很長的時間，則可能會在 SQL Server 錯誤記錄檔和應用程式記錄檔中看到記錄了下列訊息。

```
Msg 5144
Autogrow of file '%.*ls' in database '%.*ls' was cancelled by user or timed out after %d milliseconds.  Use ALTER DATABASE to set a smaller FILEGROWTH value for this file or to explicitly set a new file size.
```

```
Msg 5145
Autogrow of file '%.*ls' in database '%.*ls' took %d milliseconds.  Consider using ALTER DATABASE to set a smaller FILEGROWTH for this file.
```

資料庫和/或交易記錄檔的長時間自動成長可能會導致查詢效能問題。 這是因為需要檔案自動成長的作業會在檔案成長作業期間保留鎖定或閂鎖等資源。 您可能會看到配置頁面的閂鎖等候時間很長。 需要長時間自動成長的作業會顯示 PREEMPTIVE_OS_WRITEFILEGATHER 其等候類型。





## <a name="see-also"></a>另請參閱  
 [CREATE DATABASE &#40;SQL Server Transact-SQL&#41;](../../t-sql/statements/create-database-transact-sql.md)