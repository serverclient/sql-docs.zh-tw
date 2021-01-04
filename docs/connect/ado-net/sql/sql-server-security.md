---
title: SQL Server 安全性
description: 提供 SQL Server 安全性功能的概觀及應用程式案例，適用於建立以 SQL Server 為目標的安全 ADO.NET 應用程式。
ms.date: 09/26/2019
ms.assetid: 9053724d-a1fb-4f0f-b9dc-7f6dd893e8ff
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-kaywon
ms.openlocfilehash: e70f6da7755b28c400cc80a5ee6de79a004e4e42
ms.sourcegitcommit: 2add15a99df7b85d271adb261523689984dfd134
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97038916"
---
# <a name="sql-server-security"></a>SQL Server 安全性

[!INCLUDE[Driver_ADONET_Download](../../../includes/driver_adonet_download.md)]

SQL Server 具有許多功能，可支援安全資料庫應用程式的建立。  
  
資料竊取或破壞等一般的安全性考量，則適用於所使用的 SQL Server 版本。 資料完整性也應被視為安全性問題。 若資料未受到保護，那麼，如果允許進行特定的資料操作，並且利用不正確的值意外或惡意地修改資料或將其完全刪除，則它可能就會變得毫無價值。 此外，通常還必須遵守法律需求，例如正確儲存機密資訊。 視特定管轄區所適用的法律而定，會完全禁止儲存某些類型的個人資料。  
  
每個 SQL Server 版本和每個 Windows 版本一樣，都具有不同的安全性功能，而越新的版本功能越強。 請務必了解，安全性功能本身無法保證安全的資料庫應用程式。 每個資料庫應用程式在其需求、執行環境、部署模型、實體位置及使用者群體中都是唯一的。 範圍內的某些本機應用程式可能只需要最低的安全性，而透過網際網路部署的其他本機應用程式或應用程式可能就需要嚴格的安全性措施以及持續的監視和評估。  
  
您應該在設計階段就考量 SQL Server 資料庫應用程式的安全性需求，而不是事後才加以考量。 在開發週期的早期評估威脅，可讓您有機會減輕偵測到弱點之處所受到的潛在損害。  
  
即使應用程式的初始設計很健全，但隨著系統的發展，可能就會出現新的威脅。 藉由在資料庫周圍建立多道防線，您就能將安全性缺口所造成的損害降到最低。 您的第一道防線是減少受攻擊面的範圍，除非絕對必要，否則不要授與權限。  
  
本節中的主題簡要說明與開發人員相關的 SQL Server 安全性功能，並提供連結至《SQL Server 線上叢書》中相關主題的連結，以及提供更深入探討的其他資源。  
  
## <a name="in-this-section"></a>本節內容  
[在 SQL Server 中進行驗證](authentication-sql-server.md)  
描述 SQL Server 中的登入和驗證，並提供其他資源的連結。 

[Azure Active Directory 驗證](azure-active-directory-authentication.md)  
說明如何使用支援的 Azure Active Directory 驗證模式，透過 SqlClient 連線到 Azure SQL 資料來源。
  
[SQL Server 中的應用程式安全性案例](application-security-scenarios-sql-server.md)  
包含討論 ADO.NET 和 SQL Server 應用程式之各種應用程式安全性案例的主題。  
  
[SQL Server Express 安全性](sql-server-express-security.md)  
說明 SQL Server Express 的安全性考量。  
  
## <a name="related-sections"></a>相關章節  
[SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](../../../relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database.md)  
描述 SQL Server 和 Azure SQL Database 的安全性考量。

[SQL Server 安裝的安全性考量](../../../sql-server/install/security-considerations-for-a-sql-server-installation.md)  
描述安裝 SQL Server 之前要考慮的安全性考量。

## <a name="next-steps"></a>後續步驟
- [SQL Server and ADO.NET](index.md) (SQL Server 和 ADO.NET)
