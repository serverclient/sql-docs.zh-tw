---
title: 保護連線資訊
description: 了解連接字串中的安全性弱點，其可能是因為建構和保存連接字串的方法及驗證類型所導致。
ms.date: 11/13/2020
ms.assetid: 1471f580-bcd4-4046-bdaf-d2541ecda2f4
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 1d170d712269bf169d069ef7b93f975de855f8ec
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771400"
---
# <a name="protecting-connection-information"></a>保護連線資訊

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

保護應用程式時的最重要目標之一就是保護資料來源的存取。 連接字串如果沒有受到保護，就可能造成安全性漏洞。 以純文字儲存連接資訊，或在記憶體中保存連接資訊，都會危及整個系統的安全性。 內嵌於原始程式碼的連接字串可以使用 [Ildasm.exe (IL 反組譯工具)](/dotnet/framework/tools/ildasm-exe-il-disassembler) 進行讀取，進而檢視已編譯組件中的 Microsoft Intermediate Language (MSIL)。

涉及連接字串的安全性漏洞，可能會根據使用的驗證類型、連接字串在記憶體及磁碟中的保存方式，以及在執行階段用來建構連接字串的技巧而引發。

## <a name="use-windows-authentication"></a>使用 Windows 驗證

為了限制他人存取您的資料來源，您必須保護連線資訊，例如使用者 ID、密碼和資料來源名稱。 為了避免公開使用者資訊，建議您盡可能使用 Windows 驗證 (有時也稱為「整合式安全性」)。 Windows 驗證是藉由 `Integrated Security` 或 `Trusted_Connection` 關鍵字指定於連接字串中，可免除使用使用者 ID 和密碼的需要。 在使用 Windows 驗證時，使用者會由 Windows 進行驗證，而對伺服器和資料庫資源的存取權則是藉由授權給 Windows 使用者和群組來決定。

在無法使用 WIndows 驗證時必須特別小心，因為使用者認證會在連接字串中公開。 在 ASP.NET 應用程式中，可以將 Windows 帳戶設定為用於連接到資料庫和其他網路資源的固定識別 (Identity)。 您可以在 **web.config** 檔案的身分識別元素中啟用模擬，然後指定使用者名稱與密碼。

```xml  
<identity impersonate="true"
        userName="MyDomain\UserAccount"
        password="*****" />  
```  

固定識別帳戶應該是低權限的帳戶，僅為其授與資料庫中的必要權限。 此外，您也應該將組態檔加密，讓使用者名稱和密碼不至於以純文字方式公開。

## <a name="avoid-injection-attacks-with-connection-string-builders"></a>使用連接字串建立器來避免插入式攻擊

當使用動態字串串連來根據使用者輸入建立連接字串時，就可能發生連接字串插入式攻擊。 如果使用者輸入未經驗證且未逸出惡意的文字或字元，攻擊者就可能得以存取伺服器上的機密資料或其他資源。 為了解決此問題，Microsoft SqlClient Data Provider for SQL Server 引進了新的連接字串建立器類別，來驗證連接字串語法，並確保未引進其他參數。 如需詳細資訊，請參閱[連接字串建置器](connection-string-builders.md)。

## <a name="use-persist-security-infofalse"></a>使用 "Persist Security Info=false"

`Persist Security Info` 的預設值為 False；建議您在所有連接字串中都使用此預設值。 如果將 `Persist Security Info` 設定為 `true` 或 `yes`，則在開啟連接後，可透過連接取得安全機密資訊，包括使用者 ID 和密碼。 當 `Persist Security Info` 是設定為 `false` 或 `no` 時，安全性資訊會在用來開啟連接之後捨棄，以確保未受信任的來源無法存取安全機密資訊。

## <a name="encrypt-configuration-files"></a>加密組態檔

[!INCLUDE[appliesto-netfx-netcore-xxxx-md](../../includes/appliesto-netfx-netcore-xxxx-md.md)]

連接字串也可以儲存在組態檔案中，這麼做可免除將連接字串嵌入應用程式程式碼的需要。 組態檔是標準的 XML 檔案，.NET Framework 已為其定義了共用元素組。 組態檔中的連接字串通常儲存在 **\<connectionStrings>** 元素內，此元素位於 **app.config** (適用於 Windows 應用程式) 或 **web.config** 檔案 (適用於 ASP.NET 應用程式)。 如需從組態檔儲存、擷取及加密連接字串之基本概念的詳細資訊，請參閱[連接字串與組態檔](connection-strings-and-configuration-files.md)。

## <a name="see-also"></a>另請參閱

- [使用受保護的設定來加密設定資訊](/previous-versions/aspnet/53tyfkaw(v=vs.100))
- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
