---
title: Database Engine 權限使用者入門 | Microsoft Docs
description: 請檢閱 SQL Server 中的一些基本安全性概念，並了解資料庫引擎權限的一般實作。
ms.custom: ''
ms.date: 01/03/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: quickstart
helpviewer_keywords:
- permissions [SQL Server], getting started
ms.assetid: 051af34e-bb5b-403e-bd33-007dc02eef7b
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 41b69dfc9cc5564025fd7b28d14094389ff52450
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171690"
---
# <a name="getting-started-with-database-engine-permissions"></a>資料庫引擎權限使用者入門
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  透過登入與伺服器角色的伺服器等級，以及資料庫使用者與資料庫角色的資料庫等級，管理 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 中的權限。 適用於 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 的模型會在每個資料庫中公開同個系統，但不提供伺服器等級權限。 本主題會檢閱一些基本的安全性概念，並接著說明典型的權限實作資訊。  
  
## <a name="security-principals"></a>安全性主體  
 安全性主體是使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 且可獲指派權限採取動作之身分識別的正式名稱。 這些身分識別通常為人員或人員群組，但亦可為偽裝成人員的其他實體。 您可使用列示的 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 或是使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]，建立和管理安全性主體。  
  
##### <a name="logins"></a>登入  
 登入係指用於登入 [!INCLUDE[ssDEnoversion](../../../includes/ssdenoversion-md.md)]的個別使用者帳戶。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 支援根據 Windows 驗證的登入，以及根據 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 驗證的登入。 如需有關兩種登入類型的資訊，請參閱 [Choose an Authentication Mode](../../../relational-databases/security/choose-an-authentication-mode.md)。  
  
##### <a name="fixed-server-roles"></a>固定伺服器角色  
 在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]，固定伺服器角色係指一組預先設定的角色，其提供方便的伺服器等級權限群組。 您可使用 `ALTER SERVER ROLE ... ADD MEMBER` 陳述式將登入新增至角色。 如需詳細資訊，請參閱 [ALTER SERVER ROLE &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-server-role-transact-sql.md)。 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 不支援固定伺服器角色，但在 master 資料庫中具有兩個可做為伺服器角色運作的角色 (`dbmanager` 和 `loginmanager`)。  
  
##### <a name="user-defined-server-roles"></a>使用者定義伺服器角色  
 在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]，您可建立專屬的伺服器角色並為其指派伺服器等級權限。 您可使用 `ALTER SERVER ROLE ... ADD MEMBER` 陳述式將登入新增至伺服器角色。 如需詳細資訊，請參閱 [ALTER SERVER ROLE &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-server-role-transact-sql.md)。 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 不支援使用者定義伺服器角色。  
  
##### <a name="database-users"></a>資料庫使用者  
 在資料庫中建立資料庫使用者，並將該資料庫使用者對應至登入，以針對登入授與資料庫存取權。 資料庫使用者名稱通常會與登入名稱相同，但這兩種名稱不一定非得相同。 每個資料庫使用者皆會對應至單一登入。 登入僅可對應至一個資料庫中的單一使用者，但其可對應做為數個不同資料庫中的資料庫使用者。  
  
 您亦可建立未具備對應登入的資料庫使用者。 這些使用者稱為「自主資料庫使用者」 。 [!INCLUDE[msCoName](../../../includes/msconame-md.md)] 建議您使用自主資料庫使用者，原因在於其可更輕鬆地將資料庫移至不同的伺服器。 自主資料庫與登入相似，可使用 Windows 驗證或 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 驗證。 如需詳細資訊，請參閱 [自主的資料庫使用者 - 使資料庫可攜](../../../relational-databases/security/contained-database-users-making-your-database-portable.md)。  
  
 共有 12 個類型的使用者，其僅在驗證方式與顯示對象方面略有差異。 若要查看使用者清單，請參閱 [CREATE USER &#40;Transact-SQL&#41;](../../../t-sql/statements/create-user-transact-sql.md)。  
  
##### <a name="fixed-database-roles"></a>固定資料庫角色  
 固定伺服器角色係指一組預先設定的角色，其提供方便的資料庫等級權限群組。 您可使用 `ALTER ROLE ... ADD MEMBER` 陳述式，將資料庫使用者與使用者定義資料庫角色新增至固定資料庫角色。 如需詳細資訊，請參閱 [ALTER ROLE &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-role-transact-sql.md)。  
  
##### <a name="user-defined-database-roles"></a>使用者定義資料庫角色  
 具有 `CREATE ROLE` 權限的使用者可建立新的使用者定義資料庫角色，以代表具有通用權限的使用者群組。 通常會針對整個角色授與或拒絕權限，以精簡權限管理與監視作業。 您可使用 `ALTER ROLE ... ADD MEMBER` 陳述式，將資料庫使用者新增至資料庫角色。 如需詳細資訊，請參閱 [ALTER ROLE &#40;Transact-SQL&#41;](../../../t-sql/statements/alter-role-transact-sql.md)。  
  
##### <a name="other-principals"></a>其他主體  
 此處不討論其他安全性主體，包括應用程式角色，以及根據憑證或非對稱式索引鍵的登入和使用者。  
  
 如需顯示 Windows 使用者、Windows 群組、登入和資料庫使用者之間關係的說明圖，請參閱 [Create a Database User](../../../relational-databases/security/authentication-access/create-a-database-user.md)。  
  
## <a name="typical-scenario"></a>典型案例  
 下列範例表示通用和建議的權限設定方法。  
  
#### <a name="in-active-directory-or-azure-active-directory"></a>在 Active Directory 或 Azure Active Directory 中：  
  
1.  針對每個人員建立 Windows 使用者。  
  
2.  建立代表工作單位與工作職務的 Windows 群組。  
  
3.  將 Windows 使用者新增至 Windows 群組。  

#### <a name="if-the-person-connecting-will-be-connecting-to-many-databases"></a>若連接的人員將會連接至眾多資料庫  
  
1.  針對 Windows 群組建立登入。 (若使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 驗證，請略過 Active Directory 步驟，並在此處建立 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 驗證登入。)  
  
2.  在使用者資料庫中，建立代表 Windows 群組的登入資料庫使用者。  
  
3.  在使用者資料庫中，建立一或多個使用者定義資料庫角色，每個角色皆代表類似的職務。 例如財務分析師和銷售分析師。  
  
4.  將資料庫使用者新增至一或多個使用者定義資料庫角色。  
  
5.  授與權限至使用者定義資料庫角色。  
  
#### <a name="if-the-person-connecting-will-be-connecting-to-only-one-database"></a>若連接的人員僅會連接至單一資料庫  
  
1.  在使用者資料庫中，針對 Windows 群組建立自主資料庫。 (若使用 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 驗證，請略過 Active Directory 步驟，並在此處建立自主資料庫使用者 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 驗證。)  
  
1.  在使用者資料庫中，建立一或多個使用者定義資料庫角色，每個角色皆代表類似的職務。 例如財務分析師和銷售分析師。  
  
1.  將資料庫使用者新增至一或多個使用者定義資料庫角色。  
  
1.  授與權限至使用者定義資料庫角色。  
  
 此時顯示的結果，通常會是屬於 Windows 群組成員的 Windows 使用者。 Windows 群組在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 或 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]中具有登入。 登入會對應至使用者資料庫中的使用者身分識別。 使用者是資料庫角色的成員。 現在您必須新增權限至角色。  
  
## <a name="assigning-permissions"></a>指派權限  
 大部分的權限陳述式皆具有以下格式︰  
  
```  
AUTHORIZATION  PERMISSION  ON  SECURABLE::NAME  TO  PRINCIPAL;  
```  
  
-   `AUTHORIZATION` 必須是 `GRANT`、 `REVOKE` 或 `DENY`。  
  
-   `PERMISSION` 會建立允許或禁止的動作。 [!INCLUDE[ssSQL15](../../../includes/sssql16-md.md)] 可指定 230 個權限。 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] 具有較少的權限，這是因為在 Azure 中具有某些不相關的動作。 權限會列示在下面的[權限 &#40;Database Engine&#41;](../../../relational-databases/security/permissions-database-engine.md) 主題和參照圖表中。  
  
-   `ON SECURABLE::NAME` 是安全性實體類型 (伺服器、伺服器物件、資料庫或資料庫物件) 及其名稱。 某些權限不需要 `ON SECURABLE::NAME` ，這是因為其在內容當中不明確或不適當。 例如，`CREATE TABLE` 權限不需要 `ON SECURABLE::NAME` 子句。 (例如 `GRANT CREATE TABLE TO Mary;` 允許 Mary 建立資料表。)  
  
-   `PRINCIPAL` 是接收或失去權限的安全性主體 (登入、使用者或角色)。 盡可能授與權限給角色。  
  
 下列範例 grant 陳述式，會將位於 `UPDATE` 資料表或檢視 (包含於 `Parts` 結構描述) 中的 `Production` 權限，授與至名為 `PartsTeam`的角色：  
  
```  
GRANT UPDATE ON OBJECT::Production.Parts TO PartsTeam;  
```  
  
 使用 `GRANT` 陳述式，將權限授與至安全性主體 (登入、使用者和角色)。 使用  `DENY` 命令明確拒絕權限。 使用 `REVOKE` 陳述式移除先前授與或拒絕的權限。 權限會累計，使用者會接收授與至使用者登入以及任何群組成員資格的所有權限；不過，任何權限拒絕皆會覆寫所有授與。  
  
> [!TIP]  
>  常見的錯誤是嘗試使用 `GRANT` 而非 `DENY` 來移除 `REVOKE`。 此做法在使用者從多個來源接收權限時可能會造成問題；此為常見狀況。 下列範例將示範主體。  
  
 「銷售」群組透過陳述式 `SELECT` 接收 OrderStatus 資料表上的 `GRANT SELECT ON OBJECT::OrderStatus TO Sales;`權限。 使用者 Ted 是「銷售」角色的成員。 Ted 亦已透過陳述式 `SELECT` ，在其名稱下獲得授與 OrderStatus 資料表的  `GRANT SELECT ON OBJECT::OrderStatus TO Ted;`權限。 假設系統管理員想要針對「銷售」角色移除 `GRANT` 。  
  
-   若系統管理員正確執行 `REVOKE SELECT ON OBJECT::OrderStatus TO Sales;`，則 Ted 會透過其個別的 `SELECT` 陳述式，保留 OrderStatus 資料表的 `GRANT` 存取權。  
  
-   若系統管理員未正確執行 `DENY SELECT ON OBJECT::OrderStatus TO Sales;` ，則 Ted 「銷售」角色成員的 `SELECT` 權限將會遭到拒絕，這是因為「銷售」的 `DENY` 會覆寫其個別的  `GRANT`。  
  
> [!NOTE]  
>  您可使用 [!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)]來設定權限。 在 [物件總管] 中找到安全性實體，以滑鼠右鍵按一下安全性實體，然後按一下 [屬性]。 選取 [權限]  頁面。 如需使用權限頁面的說明，請參閱 [Permissions or Securables Page](../../../relational-databases/security/permissions-or-securables-page.md)。  
  
## <a name="permission-hierarchy"></a>權限階層  
 權限具有父子式階層。 也就是說，若您在資料庫上授與 `SELECT` 權限，則該權限在資料庫中會包含所有 (子系) 結構描述的 `SELECT` 權限。 若您在結構描述上授與 `SELECT` 權限，則其在結構描述中會包含所有 (子系) 資料表的 `SELECT` 權限。 權限可轉移；亦即若您在資料庫上授與 `SELECT` 權限，則其會包含所有 (子系) 結構描述以及所有 (孫系) 資料表與檢視上的 `SELECT` 權限。  
  
 權限亦具有涵蓋的權限。 物件上的 `CONTROL` 權限通常會提供物件的其他所有權限。  
  
 由於父子式階層與涵蓋階層皆可做為同個權限，因此讓權限系統變得複雜。 例如就資料表 (區域) 而言，其會位於結構描述 (客戶) 與資料庫 (SalesDB) 中。  
  
-   `CONTROL` 權限會包含「區域」資料表上的其他所有權限，包括 `ALTER`、 `SELECT`、 `INSERT`、  `UPDATE`、 `DELETE`、 and some other permissions.  
  
-   `SELECT` ，會包含「區域」資料表上的 `SELECT` 權限。  
  
 因此，您可透過以下六種陳述式之一，在「區域」資料表上達成 `SELECT` 權限：  
  
```  
GRANT SELECT ON OBJECT::Region TO Ted;   
  
GRANT CONTROL ON OBJECT::Region TO Ted;   
  
GRANT SELECT ON SCHEMA::Customers TO Ted;   
  
GRANT CONTROL ON SCHEMA::Customers TO Ted;   
  
GRANT SELECT ON DATABASE::SalesDB TO Ted;   
  
GRANT CONTROL ON DATABASE::SalesDB TO Ted;  
```  
  
## <a name="grant-the-least-permission"></a>授與最小權限  
 以上所列的第一個權限 (`GRANT SELECT ON OBJECT::Region TO Ted;`) 最為細微，亦即該陳述式是可授與 `SELECT`的最小權限。 其並無任何從屬物件權限。 一律授與最小權限但處於 (有點衝突) 較高等級是良好的做法，因為這樣可精簡授與系統。 因此，若 Ted 需要整個結構描述的權限，則只要在結構描述等級授與一次 `SELECT` 即可，而無須在資料表或檢視等級多次授與 `SELECT` 。 資料庫設計對於此策略是否獲致成功有極大影響。 若資料庫經過設計，在單一結構描述中包含需要相同權限的物件，則最能發揮此策略的優勢。  
  
## <a name="list-of-permissions"></a>權限清單  
 [!INCLUDE[ssSQL15](../../../includes/sssql16-md.md)] 具有 230 個權限。 [!INCLUDE[ssSQL14](../../../includes/sssql14-md.md)] 具有 219 個權限。 [!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] 具有 214 個權限。 [!INCLUDE[ssKilimanjaro](../../../includes/sskilimanjaro-md.md)] 具有 195 個權限。 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]、 [!INCLUDE[ssDW](../../../includes/ssdw-md.md)]和 [!INCLUDE[ssAPS](../../../includes/ssaps-md.md)] 具有較少的權限，這是因為其僅會公開部分的資料庫引擎，但每個資料庫引擎皆具有不適用於 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]的部分權限。 
 
 [!INCLUDE[database-engine-permissions](../../../includes/paragraph-content/database-engine-permissions.md)]
 
 如需顯示 [!INCLUDE[ssDE](../../../includes/ssde-md.md)] 主體與伺服器和資料庫物件間關係的說明圖，請參閱[權限階層 &#40;Database Engine&#41;](../../../relational-databases/security/permissions-hierarchy-database-engine.md)。  
  
## <a name="permissions-vs-fixed-server-and-fixed-database-roles"></a>權限與固定伺服器與固定資料庫角色  
 固定伺服器角色與固定資料庫角色的權限近似，但並非完全相同的細微權限。 例如， `sysadmin` 固定伺服器角色的成員具有 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體的所有權限，這是因為其是以 `CONTROL SERVER` 權限登入。 不過，授與 `CONTROL SERVER` 權限並不會登入 sysadmin 固定伺服器角色成員，而新增登入至 `sysadmin` 固定伺服器角色並不會明確授與登入 `CONTROL SERVER` 權限。 有時，預存程序在檢查權限時會檢查固定角色，但不會檢查細微權限。 例如，卸離資料庫需要具有 `db_owner` 固定資料庫角色的成員資格。 對等的 `CONTROL DATABASE` 權限不足。 這兩個系統採平行方式運作，但彼此鮮少互動。 Microsoft 建議盡可能使用最新且更細微的權限系統，來取代固定角色。
  
## <a name="monitoring-permissions"></a>監視權限  
 下列檢視會傳回安全性資訊。  
  
-   您可使用 `sys.server_principals` 檢視，檢驗伺服器上的登入與使用者定義伺服器角色。 此檢視在 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]中無法使用。  
  
-   您可使用 `sys.database_principals` 檢視，檢驗資料庫中的使用者與使用者定義角色。  
  
-   您可使用 `sys.server_permissions` 檢視，檢驗授與至登入以及使用者定義伺服器角色的權限。 此檢視在 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]中無法使用。  
  
-   您可使用 `sys.database_permissions` 檢視，檢驗授與至使用者以及使用者定義固定資料庫角色的權限。  
  
-   您可使用 `sys. sys.database_role_members` 檢視來檢驗資料庫角色成員資格。  
  
-   您可使用 `sys.server_role_members` 檢視來檢驗伺服器角色成員資格。 此檢視在 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)]中無法使用。  
  
-   如需其他的安全性相關檢視，請參閱 [安全性目錄檢視 &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/security-catalog-views-transact-sql.md) ，建立和管理安全性主體。  
  
### <a name="useful-transact-sql-statements"></a>實用的 Transact-SQL 陳述式  
 下列陳述式會傳回關於權限的實用資訊。  
  
 若要傳回資料庫中已授與或拒絕的明確權限 ([!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)])，請在資料庫中執行下列陳述式。  
  
```sql  
SELECT   
    perms.state_desc AS State,   
    permission_name AS [Permission],   
    obj.name AS [on Object],   
    dPrinc.name AS [to User Name]  
FROM sys.database_permissions AS perms  
JOIN sys.database_principals AS dPrinc  
    ON perms.grantee_principal_id = dPrinc.principal_id  
JOIN sys.objects AS obj  
    ON perms.major_id = obj.object_id;  
```  
  
 若要傳回伺服器角色的成員 (僅限 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)])，請執行下列陳述式。  
  
```sql  
SELECT sRole.name AS [Server Role Name] , sPrinc.name AS [Members]  
FROM sys.server_role_members AS sRo  
JOIN sys.server_principals AS sPrinc  
    ON sRo.member_principal_id = sPrinc.principal_id  
JOIN sys.server_principals AS sRole  
    ON sRo.role_principal_id = sRole.principal_id;  
```  
  
 
 若要傳回資料庫角色的成員 ([!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 和 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)])，請在資料庫中執行下列陳述式。  
  
```sql  
SELECT dRole.name AS [Database Role Name], dPrinc.name AS [Members]  
FROM sys.database_role_members AS dRo  
JOIN sys.database_principals AS dPrinc  
    ON dRo.member_principal_id = dPrinc.principal_id  
JOIN sys.database_principals AS dRole  
    ON dRo.role_principal_id = dRole.principal_id;  
```  
  
## <a name="next-steps"></a>後續步驟  
 如需更多關於快速入門的主題資訊，請參閱：  
  
-   [教學課程：資料庫引擎使用者入門](../../../relational-databases/tutorial-getting-started-with-the-database-engine.md) 

-   [建立資料庫 &#40;教學課程&#41;](../../../t-sql/lesson-1-creating-database-objects.md)  
  
-   [教學課程：SQL Server Management Studio](../../../ssms/quickstarts/connect-query-sql-server.md)  
  
-   [教學課程：撰寫 Transact-SQL 陳述式](../../../t-sql/tutorial-writing-transact-sql-statements.md)  
  
## <a name="see-also"></a>另請參閱  
 [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](../../../relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database.md)   
 [安全性函數 &#40;Transact-SQL&#41;](../../../t-sql/functions/security-functions-transact-sql.md)   
 [安全性相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](../../../relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql.md)   
 [安全性目錄檢視 &#40;Transact-SQL&#41;](../../../relational-databases/system-catalog-views/security-catalog-views-transact-sql.md)   
 [sys.fn_builtin_permissions &#40;Transact-SQL&#41;](../../../relational-databases/system-functions/sys-fn-builtin-permissions-transact-sql.md)   
 [判斷有效的 Database Engine 權限](../../../relational-databases/security/authentication-access/determining-effective-database-engine-permissions.md)
  
