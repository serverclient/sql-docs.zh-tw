---
title: SQL Server 移轉小幫手 |Microsoft Docs
description: 瞭解 SQL Server 移轉小幫手，這是一種工具，可將資料庫從 Microsoft Access、DB2、MySQL、Oracle 和 SAP ASE 自動遷移至 SQL Server。
ms.custom: ''
ms.date: 10/10/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.assetid: d0233525-a83b-4279-813e-c554042abd0e
author: nahk-ivanov
ms.author: alexiva
ms.openlocfilehash: 2de9fc7fdc730cb09f96bff6633cab29521e175e
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596983"
---
# <a name="sql-server-migration-assistant"></a>SQL Server 移轉小幫手

Microsoft SQL Server 移轉小幫手 (SSMA) 工具的設計目的，是要自動地將資料庫從 Microsoft Access、DB2、MySQL、Oracle 和 SAP ASE 移轉至 SQL Server。  
  
## <a name="migration-sources"></a>移轉來源  
  
- [適用於 Access 的 SQL Server 移轉小幫手](../ssma/access/sql-server-migration-assistant-for-access-accesstosql.md)  
  
- [適用於 DB2 的 SQL Server 移轉小幫手](../ssma/db2/sql-server-migration-assistant-for-db2-db2tosql.md)  
  
- [適用於 MySQL 的 SQL Server 移轉小幫手](../ssma/mysql/sql-server-migration-assistant-for-mysql-mysqltosql.md)  
  
- [適用於 Oracle 的 SQL Server 移轉小幫手](../ssma/oracle/sql-server-migration-assistant-for-oracle-oracletosql.md)  
  
- [適用于 SAP ASE 的 SQL Server 移轉小幫手](../ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql.md)  

## <a name="supported-sources-and-target-versions"></a>支援的來源和目標版本

針對支援的來源，請參閱下載中心的 SSMA 下載資訊。

下列為 SSMA 支援的目標版本。

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016
- Windows 和 Linux 上的 SQL Server 2017
- Windows 和 Linux 上的 SQL Server 2019
- Azure SQL Database
- Azure SQL 受控執行個體
- Azure Synapse Analytics * *

* * 只有 SSMA for Oracle 支援這個目標。

## <a name="downloads"></a>下載

- [SSMA for Access](https://aka.ms/ssmaforaccess)
- [SSMA for DB2](https://aka.ms/ssmafordb2)
- [SSMA for MySql](https://aka.ms/ssmaformysql)
- [SSMA for Oracle](https://aka.ms/ssmafororacle)
- [SSMA for SAP ASE](https://aka.ms/ssmaforsybase)
 
## <a name="getting-ssma-support"></a>取得 SSMA 支援  

**Microsoft SQL Server 移轉小幫手 (SSMA) 的說明和支援：**  
  
- **產品協助** ：若要存取產品支援，請啟動 SSMA，然後選取 [說明] 功能表或按 F1 鍵。  
  
- **SQL Server 的社區論壇** -在 SQL Server 社區中詢問問題  
  
  - [SQL Server 的社團](../sql-server/index.yml) -SQL Server 社區所監視的新聞群組和論壇。 此網站還會列出社群資訊來源，例如部落格或網站。  
  
  - [SQL Server Developer Center 的社區](../sql-server/index.yml) -新聞群組、論壇和其他對 SQL Server 開發人員很有用的資源  
  
- **輔助支援** -移至 [https://support.microsoft.com/assistedsupportproducts](https://support.microsoft.com/assistedsupportproducts) 並搜尋「SQL Server 移轉小幫手」。  選取您的版本，然後選取 [啟動要求]。  SQL Server 移轉小幫手工具組含協助支援。  
  
- 頂級 **支援**-如果您有頂級合約，您可以在 [premier Online 入口網站](https://premier.microsoft.com/)上取得頂級支援。  
  
- **諮詢服務** -如需合作夥伴輔助的遷移，請移至 [Azure 資料庫移轉指南](https://datamigration.microsoft.com/)。
  
## <a name="legal-notice-ssma"></a>法律注意事項 (SSMA)

本文件集 (包括其中所用的範例應用程式) 僅為資訊用途而提供，本文件集亦不提供任何明示或默示之擔保。 本文件集中的資訊，包括 URL 及其他網際網路網站參考資料，如有變更恕不另行通知。 本文件集之使用或因使用本文件集所衍生之一切後果，概由使用者自行承擔所有風險。  
  
本文件內所含範例的主要目的在於闡釋某個概念，或說明特定陳述式或子句的合理使用。 大部分的範例都不包含通常可在完整生產系統中找到的所有程式碼，因為某些一般資料驗證和錯誤處理已移除，以將範例集中在特定概念或語句上。 這些範例或任何包含的原始程式碼均無法使用技術支援。  
  
除非另有特別聲明，否則本文件範例所舉之公司、組織、產品、網域名稱、電子郵件地址、人名、地名和事件均屬虛構，並非特定意指任何真實的公司、組織、產品、網域名稱、電子郵件地址、人名、地名和事件。 遵守所有適用之著作權法係使用者的責任。 在不限制任何依著作權本得享有之權利，未經 Microsoft Corporation 書面許可，貴用戶不得為任何目的使用任何形式或方法 (電子形式、機械形式、影印、記錄或其他方式) 複製或傳送本文件集的任何部分，也不得將本文件集的任何部分儲存或放入檢索系統。  
  
Microsoft 可能擁有本文件集所提及內容中所含之專利權、專利優先權、商標、著作權，或其他智慧財產權。 除非 Microsoft 書面授權合約所明示規定者外，提供本文件集並不授與貴用戶上述專利權、商標、著作權或其他智慧財產權。  
  
© 2019 Microsoft Corporation。 著作權所有，並保留一切權利。  
  
Microsoft、Windows、Windows NT、Windows Server、Active Directory、ActiveX、BackOffice、bCentral、BizTalk、DirectX、Excel、Hotmail、IntelliSense、J/Direct、Jscript、Microsoft Press、MSDN、MS-DOS、Outlook、PivotChart、PivotTable、PowerPoint、SharePoint、SQL Server、Visual Basic、Visual C#、Visual C++、Visual FoxPro、Visual InterDev、Visual J#、Visual J++、Visual SourceSafe、Visual Studio、Win32、Win32s、Windows Mobile、Windows Server System 及 WinFX 係 Microsoft Corporation 在美國及 (或) 其他國家/地區的註冊商標或商標。  
  
SAP NetWeaver 是 SAP AG 在德國和其他數個國家/地區的註冊商標。  
  
所有其他商標為各所有人所有之商標。  
  
## <a name="documentation-policy-for-sql-server-support-and-upgrade"></a>SQL Server 支援及升級的文件集原則

SQL Server 文件集中的內容均經過充分測試才發佈。 產品檔集-SQL Server 線上叢書、讀我檔案、已知問題檔和知識庫文章-包含 SQL Server 功能的相關內容，而且此功能足以確保所有客戶的一般使用安全。 此原則適用於所有 SQL Server 文件集，包括版本及 Service Pack 的讀我檔案；讀我檔案可視為《線上叢書》的延伸部分。  
  
在某些情況下，某項特定功能未供客戶直接使用，因此未受記載。 除非 Microsoft 所發佈的 SQL Server 文件集也討論到某項功能，否則 Microsoft 客戶支援並不支援來自協力廠商叢書或網站的內容，並且不得用於產品資料庫或應用程式。  
  
客戶不得使用未記載的 API，包括 (但不限於)：預存程序、擴充預存程序、函式、檢視、資料表、資料行、屬性或中繼資料。 Microsoft 客戶支援不支援利用或使用未記載之進入點的資料庫或應用程式。  
  
對於利用及使用未記載之進入點的應用程式或資料庫，則不保證能夠升級至 SQL Server 未來版本的伺服器或資料庫。 SQL Server 功能的用途不得超出 Microsoft SQL Server 文件集所含內容。 如果某功能未記載於 Microsoft SQL Server 文件集，即屬於 SQL Server 不支援的部分。