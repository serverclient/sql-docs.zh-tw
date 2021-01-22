---
description: MSSQLSERVER_15401
title: MSSQLSERVER_15401
ms.custom: ''
ms.date: 12/25/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, vencher, tejasaks, docast
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 15401 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: 8d996c6e48f90cf54b962481cc1e7cd1ed7b088e
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596326"
---
# <a name="mssqlserver_15401"></a>MSSQLSERVER_15401
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|15401|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|SEC_INVALIDLOGINORGROUP|
|訊息文字|找不到 Windows NT 使用者或群組 '%s'。 請再一次檢查名稱。|
||

## <a name="explanation"></a>說明

當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 無法根據 Windows 主體 (例如網域使用者或 Windows 網域群組) 建立登入時，就會發生此錯誤。 使用者會收到類似下列的錯誤訊息回報

> 錯誤 15401:找不到 Windows NT 使用者或群組 '%s'。 請再一次檢查名稱。

## <a name="cause"></a>原因

下列任何原因均會導致發生此錯誤：

- Active Directory 中沒有此登入。
- 網域控制站無法使用。
- 您在新增本機帳戶時，未使用 BUILTIN 作為網域名稱。
- 名稱解析問題。

## <a name="user-action"></a>使用者動作

請檢閱下列各節以了解您可以針對上述每個不同原因採取的動作。

### <a name="verify-the-login-you-are-trying-to-add"></a>確認您正在嘗試新增的登入

1. 確認 Windows 登入仍然存在於網域中。 您的網路系統管理員基於特定原因可能已移除 Windows 登入，而且您可能無法將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的權限授與該登入。
1. 請確認您已正確拼寫網域和登入名稱，而且是使用下列格式：`Domain\User`
1. 如果登入存在且正確無誤，而且您仍然收到錯誤，請繼續按照此文章中的下列各節操作。

### <a name="verify-if-the-domain-controller-is-available"></a>確認網域控制站可用

當登入所在之網域 (可能是相同或不同的網域) 的網域控制站基於某些原因而無法使用時，您可能會收到錯誤 15401。

如果登入所在的網域與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不同，請確認網域之間存在正確的信任。

從執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的電腦使用 ping 命令，確認可以存取登入的網域控制站。 檢查網域控制站的 IP 位址和名稱。

### <a name="verify-the-domain-name-for-local-accounts"></a>確認本機帳戶的網域名稱

本機 (非網域) 帳戶需以特殊方式處理。 如果您嘗試從執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的本機電腦新增本機帳戶，請確認您是使用 BUILTIN 作為網域名稱。

### <a name="check-for-name-resolution-issues"></a>檢查名稱解析問題

如果您在解析新增登入或群組所涉及的電腦名稱時遇到問題，您可能會收到錯誤 15401。

請確認您已正確設定名稱解析機制 (例如 WINS、DNS、HOSTS 或 LMHOSTS)。

## <a name="see-also"></a>請參閱

- [測試本機電腦和其網域之間的通道](/powershell/module/microsoft.powershell.management/test-computersecurechannel#example-1--test-a-channel-between-the-local-computer-and-its-domain)
- [LogonSessions v1.4](/sysinternals/downloads/logonsessions)
- [sp_change_users_login (Transact-SQL)](../system-stored-procedures/sp-change-users-login-transact-sql.md)