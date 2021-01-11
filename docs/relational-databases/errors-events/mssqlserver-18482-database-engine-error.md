---
description: MSSQLSERVER_18482
title: MSSQLSERVER_18482
ms.custom: ''
ms.date: 12/25/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, vencher, tejasaks, docast
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 18482 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: 42e8bf7a80659467c489d86b8a3ca944ceebe360
ms.sourcegitcommit: d819173fb91af6f20ca6ee59686c35c71b060fbc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797795"
---
# <a name="mssqlserver_18482"></a>MSSQLSERVER_18482
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|18482|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|REMLOGIN_INVALID_SITE|
|訊息文字|由於未將 '%.ls' 定義為遠端伺服器而無法連接到伺服器 '%. ls'。 請確認您已經指定正確的伺服器名稱。 %.*ls|
||

## <a name="explanation"></a>說明

當您嘗試從某部伺服器針對另一部伺服器執行遠端程序呼叫 (RPC) 時 (例如，透過使用 `EXEC SERV_REMOTE.pubs..byroyalty` 之類的陳述式在遠端電腦上執行預存程序)，就會發生此錯誤。 使用者會收到類似下列的錯誤訊息回報

> 錯誤 18482：由於未將 \<SERV_REMOTE> 定義為遠端伺服器而無法連接到伺服器 \<SERV_REMOTE>。 請確認您已經指定正確的伺服器名稱。

## <a name="cause"></a>原因

當 SQL Server 無法執行遠端程序呼叫時，就會發生此錯誤。 這可能是因為本機伺服器設定不正確所造成。 為了進行遠端程序呼叫，SQL Server 會先透過在 sysservers 中尋找 srvid = 0 的伺服器名稱，來判斷哪一部是本機伺服器。 如果在 sysservers 中找不到 srvid = 0 的項目，或者 srvid = 0 的伺服器名稱屬於與本機 Windows 電腦名稱不同的伺服器名稱，您將會收到此錯誤。

## <a name="user-action"></a>使用者動作

若要判斷本機伺服器是否已設定正確，請檢查 master..sysservers 中的 `srvstatus` 資料行。 本機伺服器的這個值應該是 0。

例如，假設您的本機伺服器名稱為 SERV_LOCAL、遠端伺服器名稱為 *SERV_REMOTE*，而且 sysservers 包含下列資訊：

|srvid|srvstatus|srvname|srvname|
|---|---|---|---|
|1|2|SERV_LOCAL|SERV_LOCAL|
|2|1|SERV_REMOTE|SERV_REMOTE|
||||

在上述輸出中，SERV_LOCAL 是本機伺服器，但其 srvid 為 1；其應該是 0。 若要更正此錯誤，請遵循下列步驟：

1. 執行 `sp_dropserver` local_server_name (droplogins)，在此範例中，您會執行 `sp_dropserver SERV_LOCAL, droplogins`。
1. 執行 `sp_addserver` local_server_name (LOCAL)，在此範例中，您會執行 `sp_addserver SERV_LOCAL, LOCAL`。
1. 停止並重新啟動 SQL Server。

執行那些步驟之後，sysservers 資料表看起來應該如下所示：

|srvid|srvstatus|srvname|srvname|
|---|---|---|---|
|0|0|SERV_LOCAL|SERV_LOCAL|
|2|1|SERV_REMOTE|SERV_REMOTE|
||||

> [!NOTE]
> 本機伺服器的伺服器識別碼 (srvid) 應該是 0。

在某些情況下，sysservers 資料表中的項目看似正確，但當您執行 `select @@servername` 時，其會傳回 NULL。 在這些情況下，您仍應執行上述步驟 1 到 3 來修正問題。

## <a name="more-information"></a>詳細資訊

您可能會在安裝複寫時收到此錯誤訊息，因為安裝程序會在複寫所涉及的伺服器之間進行遠端程序呼叫。
