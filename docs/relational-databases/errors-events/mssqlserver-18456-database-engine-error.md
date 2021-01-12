---
title: MSSQLSERVER_18456 | Microsoft Docs
description: 因為 SQL Server 中的密碼或使用者名稱不正確，所以導致連線嘗試遭到拒絕。 請參閱錯誤的說明及可能解決方法。
ms.custom: ''
ms.date: 06/09/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 18456 (Database Engine error)
ms.assetid: c417631d-be1f-42e0-8844-9f92c77e11f7
author: MashaMSFT
ms.author: mathoma
ms.openlocfilehash: 9eec7ad0a11116b1eb9e8bf1c33b57ff3bd2b419
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099290"
---
# <a name="mssqlserver_18456"></a>MSSQLSERVER_18456
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  
## <a name="details"></a>詳細資料  
  
| 屬性 | 值 |  
| :-------- | :---- |  
|產品名稱|SQL Server|  
|事件識別碼|18456|  
|事件來源|MSSQLSERVER|  
|元件|SQLEngine|  
|符號名稱|LOGON_FAILED|  
|訊息文字|使用者 '%.*ls'.%.\*ls 登入失敗|  
  
## <a name="explanation"></a>說明  
當連線嘗試因為密碼或使用者名稱不正確而驗證失敗並遭到拒絕時，將會傳回與下列內容相似的訊息到用戶端：「使用者 '<user_name>' 登入失敗。 (Microsoft SQL Server，錯誤：18456)」。  
  
其他傳回給用戶端的資訊還包含下列項目：  
  
「使用者 '<user_name>' 登入失敗。 (.Net SqlClient 資料提供者)」  
  
-----------------------------\-  
  
「伺服器名稱：<computer_name>」  
  
「錯誤號碼：18456」  
  
「嚴重性：14」  
  
「狀態：1」  
  
「行號：65536」  
  
也可能傳回下列訊息：  
  
「訊息 18456，層級 14，狀態 1，伺服器 <computer_name>，行 1」  
  
「使用者 '<user_name>' 登入失敗。」  
  
## <a name="additional-error-information"></a>其他錯誤資訊  
為增加安全性，傳回用戶端的錯誤訊息會刻意隱藏驗證錯誤的原本形式。 不過，在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔中，會有對應的錯誤包含對應至驗證失敗狀況的錯誤狀態。 請將錯誤狀態與下列清單做比較，以判斷登入失敗的原因。  
  
|State|描述|  
|---------|---------------|  
|1|無錯誤資訊。 這個狀態通常表示您沒有接收錯誤詳細資料的權限。 如需詳細資訊，請連絡 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 管理員。|  
|2|使用者識別碼無效。|  
|5|使用者識別碼無效。|  
|6|嘗試將 Windows 登入名稱用於 SQL Server 驗證。|  
|7|已停用登入且密碼不正確。|  
|8|密碼不正確。|  
|9|密碼無效。|  
|11|登入有效，但伺服器存取失敗。 導致此錯誤的一個可能原因是：Windows 使用者可以使用本機 Administrators 群組成員的身分存取 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，但 Windows 不會提供系統管理員認證。 若要連接，請使用 [以系統管理員身分執行] 選項開始連接程式，然後以特定登入，將 Windows 使用者新增至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。|  
|12|登入是有效的登入，但伺服器存取失敗。|  
|18|密碼必須變更。|  
|38, 46|找不到使用者所要求的資料庫。|
|58| 當 SQL Server 設定為僅限使用 Windows 驗證，而用戶端嘗試使用 SQL 驗證登入時。 另一個原因是當 SID 不符時。|
|102 - 111|AAD 失敗。|
|122 - 124|因為使用者名稱或密碼空白而失敗。|
|126|使用者所要求的資料庫不存在。|
|132 - 133|AAD 失敗。|
  
其他錯誤狀態存在，表示有未預期的內部處理錯誤。  
  
**另一個不尋常的可能原因**  
  
如有下列情況，可能會傳回這個錯誤原因：**嘗試使用 SQL 驗證登入失敗。伺服器設定成只允許 Windows 驗證。** 會在下列情況傳回。  
  
-   伺服器是設定成混合模式驗證，而 ODBC 連接使用了 TCP 通訊協定，且此連接並未明確指定連接應使用信任連接。  
  
-   伺服器是設定成混合模式驗證，而 ODBC 連接使用了具名管道，同時用戶端用以開啟具名管道的認證已用於自動模擬使用者，且此連接並未明確指定連接應使用信任連接。  
  
若要解決此問題，請在連接字串中加上 **TRUSTED_CONNECTION = TRUE**。  
  
## <a name="examples"></a>範例  
在此範例中，驗證錯誤狀態為 8。 這表示密碼不正確。  
  
|Date|來源|訊息|  
|--------|----------|-----------|  
|2007-12-05 20:12:56.34|登入|錯誤：18456，嚴重性：14，狀態：8.|  
|2007-12-05 20:12:56.34|登入|使用者 '<user_name>' 登入失敗。 [CLIENT: <ip address>]|  
  
> [!NOTE]  
> 若您使用 Windows 驗證模式來安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，並於之後將其變更為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 與 Windows 驗證模式，就會先停用 **sa** 登入。 這將造成狀態 7 錯誤：「使用者 'sa' 登入失敗。」若要啟用 **sa** 登入，請參閱 [變更伺服器驗證模式](~/database-engine/configure-windows/change-server-authentication-mode.md)。  
  
## <a name="user-action"></a>使用者動作  
如果您正嘗試使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證進行連接，請確定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 是在混合驗證模式下設定。 如需變更 SQL Server 驗證模式的詳細資訊，請檢閱[變更伺服器驗證模式](../../database-engine/configure-windows/change-server-authentication-mode.md)
  
如果您正嘗試使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 驗證進行連接，請確定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入存在而且您的拼字正確無誤。  
  
如果您正嘗試使用 Windows 驗證進行連接，請確定您已適當地登入正確的網域。  
  
如果您的錯誤表示狀態 1，請連絡 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 管理員。  
  
如果您正嘗試使用系統管理員認證進行連接，請使用 [以系統管理員身分執行] 選項啟動應用程式。 連接之後，請將您的 Windows 使用者當做個別登入加入。  
  
如果 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 支援自主資料庫，請確認在移轉至自主資料庫使用者之後，登入不會遭到刪除。  
  
本機連接到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體時，如果要從以 **NT AUTHORITY\NETWORK SERVICE** 執行的服務來連線，則必須使用電腦的完整網域名稱進行驗證。 如需詳細資訊，請參閱本主題中的[如何：使用網路服務帳戶存取 ASP.NET 中的資源](/previous-versions/msp-n-p/ff647402(v=pandp.10))