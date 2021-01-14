---
title: 自主資料庫 | Microsoft 文件
description: 了解自主資料庫、部分自主資料庫的優點與限制，以及 SQL Server 2019 如何運作以隔離資料庫。
ms.custom: ''
ms.date: 08/24/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
helpviewer_keywords:
- contained database
- database_uncontained_usage event
- partially contained database
- contained database, understanding
ms.assetid: 36af59d7-ce96-4a02-8598-ffdd78cdc948
author: stevestein
ms.author: sstein
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: bbad843d482945a29cb03fde9545b1c19fb354f9
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098389"
---
# <a name="contained-databases"></a>自主資料庫
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  *「自主資料庫」* (Contained Database) 是與其他資料庫和裝載資料庫的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體隔離的資料庫。  [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 以四種方式協助使用者將其資料庫與執行個體隔離。  
  
-   描述資料庫的中繼資料大多是在資料庫中維護 (加上或取代在 master 資料庫中維護中繼資料)。  
  
-   所有中繼資料都是使用相同定序來定義。  
  
-   使用者驗證可由資料庫執行，減少 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體登入的資料庫相依性。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 環境 (DMV、XEvent 等) 會報告內含項目資訊並對其作用。  
  
 部分自主資料庫的某些功能，例如將中繼資料儲存在資料庫中，適用於所有 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 資料庫。 部分自主資料庫的某些優點，例如資料庫層級驗證和目錄定序，必須先啟用才可供使用。 部分內含項目是透過使用 **CREATE DATABASE** 和 **ALTER DATABASE** 陳述式或使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]來啟用。 如需有關如何啟用部分資料庫內含項目的詳細資訊，請參閱＜ [Migrate to a Partially Contained Database](../../relational-databases/databases/migrate-to-a-partially-contained-database.md)＞。  
  
##  <a name="partially-contained-database-concepts"></a><a name="Concepts"></a> 部分自主資料庫概念  
 完全自主資料庫包含了定義資料庫所需的所有設定和中繼資料，而且對於已安裝資料庫的 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 執行個體沒有組態相依性。 在舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]中，分隔資料庫與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體既耗時又需要資料庫與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體之間關聯性的詳細知識。 部分自主資料庫讓分隔 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中的資料庫與其他資料庫變得更容易。  
  
 自主資料庫會將功能視為與內含項目相關。 只仰賴位於資料庫內部之功能的任何使用者定義實體會被視為完全自主。 仰賴位於資料庫外部之功能的任何使用者定義實體會被視為非內含性 (如需詳細資訊，請參閱本主題稍後的 [內含項目](#containment) 一節)。  
  
 下列詞彙適用於自主資料庫模型。  
  
 資料庫界限  
 資料庫與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體之間的界限。 某個資料庫與其他資料庫之間的界限。  
  
 包含  
 完全存在於資料庫界限內的元素。  
  
 未包含  
 跨資料庫界限的元素。  
  
 非自主資料庫  
 內含項目設定為 **NONE** 的資料庫。 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 之前的版本中，所有資料庫都是非自主資料庫。 根據預設，所有 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本的資料庫都會將內含項目設定為 **NONE**。  
  
 部分自主資料庫  
 部分自主資料庫就是允許跨越資料庫界限之某些功能的自主資料庫。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 包含判斷何時跨越內含項目界限的功能。  
  
 包含的使用者  
 自主資料庫具有兩種使用者類型。  
  
-   **具有密碼之自主資料庫使用者**  
  
     具有密碼之自主資料庫使用者是由資料庫驗證。 如需詳細資訊，請參閱 [自主的資料庫使用者 - 使資料庫可攜](../../relational-databases/security/contained-database-users-making-your-database-portable.md)。  
  
-   **Windows 主體**  
  
     已授權的 Windows 使用者和已授權之 Windows 群組的成員可以直接連接至資料庫，而且不需要 **master** 資料庫的登入。 資料庫信任 Windows 驗證。  
  
 以 **master** 資料庫中之登入為基礎的使用者可被授與自主資料庫的存取權，不過這樣做會建立 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的相依性。 因此，根據登入建立使用者需要部分內含項目。
  
> [!IMPORTANT]  
>  若啟用部分自主資料庫，會將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的存取控制權委派給資料庫擁有者。 如需詳細資訊，請參閱 [Security Best Practices with Contained Databases](../../relational-databases/databases/security-best-practices-with-contained-databases.md)。  
  
 資料庫界限  
 因為部分自主資料庫會分隔資料庫功能與執行個體功能，所以兩個元素之間有一條明確定義的線，稱為 *「資料庫界限」* (Database Boundary)。  
  
 *「資料庫模型」* (Database Model) 位於資料庫界限內部，其中進行資料庫的開發和管理作業。 位於資料庫內部的實體範例包括 **sys.tables** 等系統資料表、具有密碼之自主資料庫使用者，以及目前資料庫中由兩部分名稱所參考的使用者資料表。  
  
 *「管理模型」* (Management Model) 位於資料庫界限外部，它與執行個體層級功能和管理有關。 位於資料庫界限外部的實體範例包括 **sys.endpoints** 等系統資料表、對應至登入的使用者，以及另一個資料庫中由三部分名稱所參考的使用者資料表。  
  
##  <a name="containment"></a><a name="containment"></a> 內含項目  
 完全位於資料庫內部的使用者實體會被視為 *「自主」* (Contained)。 位於資料庫外部或仰賴與資料庫外部之功能互動的任何實體會被視為 *「非內含性」* (Uncontained)。  
  
 一般而言，使用者實體可分為下列內含項目類別：  
  
-   完全自主使用者實體 (絕對不會跨越資料庫界限的實體)，例如 sys.indexes。 任何使用這些功能的程式碼或任何只參考這些實體的物件也完全包含。  
  
-   非內含性使用者實體 (跨越資料庫界限的實體)，例如 sys.server_principals 或伺服器主體 (登入) 本身。 任何使用這些實體的程式碼或任何參考這些實體的功能為非內含性。  
  
###  <a name="partially-contained-database"></a><a name="partial"></a> Partially Contained Database  
 自主資料庫功能目前只能在部分包含的狀態下使用。 部分自主資料庫就是允許使用未自主功能的自主資料庫。  
  
 您可以使用 [sys.dm_db_uncontained_entities](../../relational-databases/system-dynamic-management-views/sys-dm-db-uncontained-entities-transact-sql.md) 和 [sys.sql_modules &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-sql-modules-transact-sql.md) 檢視以傳回有關非內含性物件或功能的資訊。 透過判斷資料庫元素的自主狀態，您就可以找出必須取代或改變哪些物件或功能，以升級內含項目。  
  
> [!IMPORTANT]  
>  因為某些物件的預設內含項目設定為 **NONE**，所以此檢視可能會傳回誤判。  
  
 部分自主資料庫行為與非自主資料庫行為最明確的差異在於定序。 如需有關定序問題的詳細資訊，請參閱＜ [Contained Database Collations](../../relational-databases/databases/contained-database-collations.md)＞。  
  
##  <a name="benefits-of-using-partially-contained-databases"></a><a name="benefits"></a> 使用部分自主資料庫的優點  
 您可以使用部分自主資料庫來解決一些與非自主資料庫相關聯的問題和複雜性。  
  
### <a name="database-movement"></a>資料庫移動  
 在移動資料庫時所面臨的其中一個問題就是，當資料庫從某個執行個體移至另一個執行個體時，某些重要資訊可能無法使用。 例如，登入資訊儲存在執行個體而不是在資料庫中。 當您將非自主資料庫從某個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體移至另一個執行個體時，就會遺留這項資訊。 您必須識別遺失的資料，並且將這項資料與資料庫一起移至新的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體。 這個程序可能既困難又耗時。  
  
 部分自主資料庫可將重要資訊儲存在資料庫中，因此在移動後資料庫仍然有此項資訊。  
  
> [!NOTE]  
>  部分自主資料庫可以提供有關資料庫所用、不能與執行個體分隔之功能的文件集。 這包含其他彼此相關的資料庫、資料庫所需要但無法包含的系統設定等的清單。  
  
### <a name="benefit-of-contained-database-users-with-always-on"></a>搭配使用自主資料庫使用者與 AlwaysOn 的優點  
 透過減少與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]執行個體的關聯，在使用 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]時，部分自主資料庫在容錯移轉期間就很有用。  
  
 建立包含的使用者可讓使用者直接連接到自主資料庫。 在高可用性和災害復原案例 (例如 AlwaysOn 方案) 中，這是非常重要的功能。 如果使用者是包含的使用者，萬一發生容錯移轉時，使用者無需在裝載次要副本的執行個體上建立登入，就能夠連接到次要副本。 這為使用者提供直接的好處。 如需詳細資訊，請參閱 [AlwaysOn 可用性群組概觀 (SQL Server)](../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md) 和 [AlwaysOn 可用性群組的必要條件、限制和建議 (SQL Server)](../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)。  
  
### <a name="initial-database-development"></a>初始資料庫開發  
 因為開發人員可能不知道新資料庫的部署位置，所以限制資料庫的部署環境影響會減少開發人員的工作和顧慮。 在非自主模型中，開發人員必須據此考慮新資料庫和程式的可能環境影響。 但是，透過使用部分自主資料庫，開發人員可以偵測資料庫的執行個體層級影響以及開發人員的執行個體層級顧慮。  
  
### <a name="database-administration"></a>資料庫管理  
 在資料庫而不是在 master 資料庫中維護資料庫設定，讓每個資料庫擁有者對其資料庫有較多的控制，而不需要將 **系統管理員 (sysadmin)** 權限授與資料庫擁有者。  
  
##  <a name="limitations"></a><a name="Limitations"></a> 限制  
 部分自主資料庫不允許下列功能。  
  
-   複寫、異動資料擷取或變更追蹤。  
  
-   編號程序。  
  
-   相依於具有定序變更之內建功能的結構描述繫結物件。  
  
-   定序變更所產生的繫結變更，包括物件、資料行、符號或類型的參考。
  
> [!WARNING]  
>  目前允許暫存預存程序。 由於暫存預存程序違反內含項目，因此預計未來自主資料庫版本中不會支援這些預存程序。  
  
##  <a name="identifying-database-containment"></a><a name="Identifying"></a> 識別資料庫內含項目  
 有兩項工具可協助識別資料庫的內含項目狀態。 [sys.dm_db_uncontained_entities &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-uncontained-entities-transact-sql.md) 是顯示資料庫中所有可能非內含性實體的檢視。 在執行階段中識別任何實際非內含性實體時，就會發生 database_uncontained_usage 事件。  
  
### <a name="sysdm_db_uncontained_entities"></a>sys.dm_db_uncontained_entities  
 這個檢視會顯示資料庫中任何可能的非內含性實體，例如跨越資料庫界限的實體。 這包括可能使用資料庫模型外部之物件的使用者實體。 不過，因為某些實體 (例如，使用動態 SQL 的實體) 的內含項目要等到執行階段才能判斷，所以此檢視可能會顯示一些實際上包含的實體。 如需詳細資訊，請參閱 [sys.dm_db_uncontained_entities &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-db-uncontained-entities-transact-sql.md)。  
  
### <a name="database_uncontained_usage-event"></a>database_uncontained_usage 事件  
 在執行階段識別非內含性實體時，會發生此 XEvent。 這包括起源自用戶端程式碼的實體。 這個 XEvent 只會針對實際非內含性實體發生。 不過，此事件只會在執行階段中發生。 因此，這個 XEvent 不會識別您尚未執行的任何非內含性使用者實體。  
  
## <a name="see-also"></a>另請參閱  
 [修改的功能 &#40;自主資料庫&#41;](../../relational-databases/databases/modified-features-contained-database.md)   
 [自主資料庫定序](../../relational-databases/databases/contained-database-collations.md)   
 [自主資料庫的安全性最佳做法](../../relational-databases/databases/security-best-practices-with-contained-databases.md)   
 [Migrate to a Partially Contained Database](../../relational-databases/databases/migrate-to-a-partially-contained-database.md)   
 [自主資料庫使用者 - 讓資料庫具有可攜性](../../relational-databases/security/contained-database-users-making-your-database-portable.md)  
  
  
