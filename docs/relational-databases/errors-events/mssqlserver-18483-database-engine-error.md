---
description: MSSQLSERVER_18483
title: MSSQLSERVER_18483
ms.custom: ''
ms.date: 12/25/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, Masha
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 18483 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: 40a75d8ad0bd6237b594f38bbb5cb83be8cca788
ms.sourcegitcommit: d819173fb91af6f20ca6ee59686c35c71b060fbc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797824"
---
# <a name="mssqlserver_18483"></a>MSSQLSERVER_18483
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|18483|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|REMLOGIN_INVALID_USER|
|訊息文字|由於未將 '%.ls' 定義為伺服器的遠端登入而無法連接到伺服器 '%. ls'。 請確認您已經指定正確的登入名稱。 %.*ls。|
||

## <a name="explanation"></a>說明

當您嘗試在某個系統上設定複寫散發者，而且該系統是使用原本安裝 SQL 執行個體之另一部電腦的硬碟映像所還原時，就會發生此錯誤。 使用者會收到類似下列的錯誤訊息回報：

> SQL Server Management Studio 無法將 '\<Server>\<Instance>' 設定為 '\<Server>\<Instance>' 的散發者。 錯誤 18483：無法連接到伺服器 '\<Server>\<Instance>'，因為 'distributor_admin' 並未定義為伺服器的遠端登入。 請確認您已經指定正確的登入名稱。 %.*ls。

## <a name="cause"></a>原因

當您從安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之另一部電腦的硬碟映像部署 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時，會在新安裝中保留已製作映像之電腦的網路名稱。 錯誤的網路名稱會導致複寫散發者的設定失敗。 如果您在安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之後將電腦重新命名，就會發生相同的問題。

## <a name="user-action"></a>使用者動作

若要因應此問題，請以正確的電腦網路名稱取代 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 伺服器名稱。 若要這樣做，請依照下列步驟執行：

1. 登入您從磁碟映像部署 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的電腦，然後在 SSMS 中執行下列 Transact-SQL 陳述式：

    ```sql
    -- Use the Master database
    USE master
    GO

    -- Declare local variables
    DECLARE @serverproperty_servername varchar(100),
    @servername varchar(100);

    -- Get the value returned by the SERVERPROPERTY system function
    SELECT @serverproperty_servername = CONVERT(varchar(100), SERVERPROPERTY('ServerName'));

    -- Get the value returned by @@SERVERNAME global variable
    SELECT @servername = CONVERT(varchar(100), @@SERVERNAME);

    -- Drop the server with incorrect name
    EXEC sp_dropserver @server=@servername;

    -- Add the correct server as a local server
    EXEC sp_addserver @server=@serverproperty_servername, @local='local';
    ```

2. 將執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的電腦重新啟動。
3. 若要確認 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 名稱與電腦的網路名稱一樣，請執行下列 Transact-SQL 陳述式：

    ```sql
    SELECT @@SERVERNAME, SERVERPROPERTY('ServerName');
    ```

## <a name="more-information"></a>詳細資訊

您可以使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中的 `@@SERVERNAME` 全域變數或 `SERVERPROPERTY`('ServerName') 函式，來尋找執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 之電腦的網路名稱。 當您重新啟動電腦與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務時，`SERVERPROPERTY` 函式的 ServerName 屬性會自動報告電腦網路名稱的變更。 `@@SERVERNAME` 全域變數會保留原始的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 電腦名稱，直到手動重設 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 名稱為止。

### <a name="steps-to-reproduce-the-problem"></a>重現問題的步驟

在您從磁碟映像部署 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的電腦上，依照下列步驟執行：

1. 啟動 [!INCLUDE[ssManStudio](../../includes/ssManStudio-md.md)]。
2. 在 [物件總管] 中，展開您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體名稱。
3. 以滑鼠右鍵按一下 [複寫] 資料夾，然後依序按一下 [設定散發複寫] 與 [設定發行、訂閱者和散發]。
4. 在 [設定散發] 組態精靈對話方塊中，按一下 [下一步]。
5. 在 [散發者] 對話方塊中，按一下以選取將扮演本身散發者的 [\<**Server**>\<**Instance**>]；SQL Server 將建立散發資料庫和記錄選項按鈕，然後按一下 [下一步]。
6. 在 [SQL Server Agent 啟動] 對話方塊中，按一下 [下一步]。
7. 在 [快照集資料夾] 對話方塊中，按一下 [下一步]。

    > [!NOTE]
    > 如果您收到確認快照集資料夾路徑的訊息，請按一下 [是]。
8. 在 [散發資料庫] 對話方塊中，按一下 [下一步]。
9. 在 [發行者] 對話方塊中，按一下 [下一步]。
10. 在 [精靈動作] 對話方塊中，按一下 [下一步]。
11. 在 [完成精靈] 對話方塊中，按一下 [完成]。

## <a name="see-also"></a>請參閱

- [重新命名主控 SQL Server 獨立式執行個體的電腦](/sql/database-engine/install-windows/rename-a-computer-that-hosts-a-stand-alone-instance-of-sql-server)
- [@@SERVERNAME (Transact-SQL)](/sql/t-sql/functions/servername-transact-sql)
- [SERVERPROPERTY (Transact-SQL)](/sql/t-sql/functions/serverproperty-transact-sql)
- [sp_addserver (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-addserver-transact-sql)
