---
description: MSSQLSERVER_17892
title: MSSQLSERVER_17892
ms.custom: ''
ms.date: 08/20/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, Masha
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 17892 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: 905b961e2fbf882f59b050a3acb7ba0f9c2f9046
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099322"
---
# <a name="mssqlserver_17892"></a>MSSQLSERVER_17892
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|17892|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|SRV_LOGON_FAILED_BY_TRIGGER|
|訊息文字|登入名稱為 \<Login Name> 的登入因觸發程序執行而失敗。|
||

## <a name="explanation"></a>說明

當登入觸發程序程式碼無法順利執行時，就會引發錯誤 17892。 [登入觸發程序](../triggers/logon-triggers.md)會引發預存程序來回應 LOGON 事件。 當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體建立使用者工作階段時，就會引發這個事件。 使用者會收到類似下列的錯誤訊息回報：

> 訊息 17892，層級 14，狀態 1，伺服器 \<Server Name>，第 1 行  
登入名稱為 \<Login Name> 的登入因觸發程序執行而失敗。

## <a name="possible-causes"></a>可能的原因

如果為該特定使用者帳戶執行觸發程序程式碼時發生錯誤，就會發生此問題。 其中一些案例包括：

- 觸發程序嘗試將資料插入至不存在的資料表。
- 登入沒有登入觸發程序所參考物件的權限。

## <a name="user-action"></a>使用者動作

您可根據所在案例，使用以下其中一項解決方案。

- **案例 1**：您目前可使用管理員帳戶來存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中開啟的工作階段

  在此情況下，您可採取修正觸發程序程式碼所需的矯正措施。

  - 範例 1：如果觸發程序程式碼所參考的物件不存在，請建立該物件，讓登入觸發程序可順利執行。

  - 範例 2：如果觸發程序程式碼所參考的物件存在，但使用者沒有權限，則請授與使用者存取物件所需的權限。  
  
  或者，您可直接卸除或停用登入觸發程序，讓使用者可繼續登入 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。  

- **案例 2**：您目前沒有任何以管理員權限開啟的工作階段，但已在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上啟用專用管理員連接 (DAC)。

    在此情況下，您可使用 DAC 連接來採取與案例 1 相同的步驟，因為 DAC 連接不會受到登入觸發程序的影響。 如需 DAC 連接的詳細資訊，請參閱：[資料庫管理員的診斷連接](../../database-engine/configure-windows/diagnostic-connection-for-database-administrators.md)。

    若要檢查 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中是否已啟用 DAC，您可檢查 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔中是否有類似下列的訊息：

    > 2020-02-09 16:17:44.150 為了在通訊埠 1434 上進行本機接聽，已經建立伺服器專用管理員連接支援。  

- **案例 3**：您尚未在伺服器上啟用 DAC，在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中也沒有現有的管理員工作階段。

    在此案例中，唯一可補救問題的方法是執行下列步驟：
  
    1. 停止 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 與相關的服務。
    2. 使用啟動參數 `-c`、`-m` 以及 `-f`，以從[命令提示字元](/previous-versions/sql/sql-server-2008-r2/ms180965(v=sql.105)) 啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。 這麼做會停用登入觸發程序，並可讓您執行與上述 **案例 1** 相同的補救措施。
  
        > [!NOTE]
        > 上述程序需要 *SA* 或同等的系統管理員帳戶。
  
         如需這些與其他啟動選項的詳細資訊，請參閱：[Database Engine 服務啟動選項](../../database-engine/configure-windows/database-engine-service-startup-options.md)。

## <a name="more-information"></a>詳細資訊

另一個登入觸發程序失敗的情況是使用 `EVENTDATA` 函式時。 此函式會傳回 XML (區分大小寫)。  因此，在建立下列登入觸發程序，以根據 IP 位址來封鎖存取時，您可能會遇到此問題：

``` sql
 CREATE TRIGGER tr_logon_CheckIP  
 ON ALL SERVER  
 FOR LOGON  
 AS
 BEGIN
  IF IS_SRVROLEMEMBER ( 'sysadmin' ) = 1  
     BEGIN
         DECLARE @IP NVARCHAR ( 15 );  
         SET @IP = ( SELECT EVENTDATA ().value ( '(/EVENT_INSTANCE/ClientHost)[1]' , 'NVARCHAR(15)' ));  
         IF NOT EXISTS( SELECT IP FROM DBAWork.dbo.ValidIP WHERE IP = @IP )  
         ROLLBACK ;  
     END ;  
 END ;  
 GO
```

當使用者從網際網路的此部分觸發程序複製此指令碼時，並不會保留大小寫：

```sql
 SELECT EVENTDATA ().value ( '(/event_instance/clienthost)[1]' , 'NVARCHAR(15)' ));  
```

因此，`EVENTDATA` 一律會傳回 **NULL**，而其所有與 SA 同等的登入都會被拒絕存取。 在此情況下，DAC 連接並未啟用，因此我們沒有任何選擇，只能以上方所列的啟動參數來重新啟動伺服器，以捨棄觸發程序。