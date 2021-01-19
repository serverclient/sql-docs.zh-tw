---
title: 建立資料庫使用者 | Microsoft Docs
description: 了解如何使用 SQL Server Management Studio 或 Transact-SQL 來建立最常見的資料庫使用者類型。
ms.custom: ''
ms.date: 04/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
f1_keywords:
- sql13.swb.user.securables.f1
- SQL13.SWB.DATABASEUSER.GENERAL.F1
helpviewer_keywords:
- database users, creating
- creating users with Management Studio
- mapping users
- users [SQL Server], creating
- database user additions [SQL Server]
- database users, mapping
- CREATE USER [Management Studio]
- users [SQL Server], adding
- mapping database users
ms.assetid: 782798d3-9552-4514-9f58-e87be4b264e4
author: VanMSFT
ms.author: vanto
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 5088cb94c4c4387cd77835bbe02502764d3c2384
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171700"
---
# <a name="create-a-database-user"></a>建立資料庫使用者
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  本主題說明如何建立最常見的資料庫使用者類型。 有 11 種使用者類型。 [CREATE USER &#40;Transact-SQL&#41;](../../../t-sql/statements/create-user-transact-sql.md) 主題中提供完整的清單。 各種 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 均支援資料庫使用者，但不一定支援所有類型的使用者。  
  
 您可以使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 或使用 [!INCLUDE[tsql](../../../includes/tsql-md.md)]來建立資料庫使用者。  
  
##  <a name="understanding-the-types-of-users"></a><a name="Understanding"></a> 了解使用者的類型  
 [!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)] 會顯示 6 個選項。 下圖在綠色方塊中顯示 6 個選項，並指出它們代表的意義。  
  
 ![說明不同使用者類型的圖表。](../../../relational-databases/security/authentication-access/media/typesofusers.png "TypesOfUsers")  
  
### <a name="selecting-the-type-of-user"></a>選取使用者的類型  
 **登入或未對應到登入的使用者**  
  
 如果您不熟悉 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]，就很難判斷您要建立的使用者類型。 先問問自己，需要存取資料庫的個人或群組是否有登入？ Master 資料庫中的登入通常是管理 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的人員，以及需要在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]的執行個體上存取許多或所有資料庫的人員。 針對此情況，您將建立 **有登入的 SQL 使用者**。 連接到資料庫時，資料庫使用者是登入的識別。 資料庫使用者可以使用相同的名稱做為登入，但是並非必要。 本主題假設 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]中已有登入存在。 如需如何建立登入的相關資訊，請參閱 [建立登入](../../../relational-databases/security/authentication-access/create-a-login.md)。  
  
 如果需要存取資料庫的個人或群組沒有登入，以及如果他們只需要存取一個或數個資料庫，請建立 **Windows 使用者** 或 **有密碼的 SQL 使用者**。 也稱為自主資料庫使用者，它不會與 master 資料庫中的登入相關聯。 當您想要能夠輕鬆地在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體之間移動資料庫時，這是很好的選擇。 若要在 [!INCLUDE[ssSQL15](../../../includes/sssql16-md.md)]上使用此選項，系統管理員必須先針對 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]啟用自主資料庫，以及針對內含項目啟用資料庫。 如需詳細資訊，請參閱 [自主的資料庫使用者 - 使資料庫可攜](../../../relational-databases/security/contained-database-users-making-your-database-portable.md)。  
  
> **重要！** 以自主資料庫使用者身分連接時，您必須提供資料庫的名稱做為連接字串的一部分。 若要在 [!INCLUDE[ssManStudio](../../../includes/ssmanstudio-md.md)]中指定資料庫，可在 [連接到]  對話方塊中，按一下 [選項] ，然後按一下 [連接屬性]  索引標籤。  
  
 當連接的人員無法使用 Windows 驗證時，請根據 [SQL Server 驗證登入]  選取 [有密碼的 SQL 使用者]  或 [有登入的 SQL 使用者] 。 當您組織外部的人員 (例如客戶) 要連接到您的 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]時，這是很常見的情況。  
  
> **提示！** 對組織內部的人員來說，Windows 驗證是比較好的選擇，因為他們不需要記住額外的密碼，以及因為 Windows 驗證可提供額外的安全性功能，例如 Kerberos。  
  
##  <a name="background"></a><a name="Restrictions"></a> 背景  
 使用者是資料庫層級的安全性主體。 登入必須對應到資料庫使用者，才能連接到資料庫。 登入可以做為不同的使用者對應到不同的資料庫，但在每一個資料庫中只能對應為一位使用者。 在部分自主資料庫中，可以建立沒有登入的使用者。 如需自主資料庫使用者的詳細資訊，請參閱 [CREATE USER &#40;Transact-SQL&#41;](../../../t-sql/statements/create-user-transact-sql.md)。 如果資料庫中啟用 guest 使用者，則未對應到資料庫使用者的登入就可以用 guest 使用者的身分進入資料庫。  
  
> **重要！** guest 使用者通常為停用狀態。 除非必要，否則不要啟用 guest 使用者。  
  
 使用者做為安全性主體時，可以將權限授與使用者。 使用者的範圍為資料庫。 若要連接到 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體上的特定資料庫，則登入必須對應到資料庫使用者。 資料庫內的權限是對資料庫使用者授與或拒絕，而不是登入。  
  
##  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要資料庫的 **ALTER ANY USER** 權限。  
  
##  <a name="create-a-user-with-ssms"></a><a name="SSMSProcedure"></a> 使用 SSMS 建立使用者  
  
 
1.  在 [物件總管] 中，展開 **[資料庫]** 資料夾。  
  
2.  展開要建立新資料庫使用者的資料庫。  
  
3.  以滑鼠右鍵按一下 [安全性] 資料夾，指向 [新增]，然後選取 [使用者...]。  
  
4.  在 [資料庫使用者 - 新增] 對話方塊的 [一般] 頁面上，從 [使用者類型] 清單中選取下列其中一個使用者類型：  
  
    -   **有登入的 SQL 使用者**  
  
    -   **有密碼的 SQL 使用者**  
  
    -   **沒有登入的 SQL 使用者**  
  
    -   **對應到憑證的使用者**  
  
    -   **對應到非對稱金鑰的使用者**  
  
    -   **Windows 使用者**  
  
5.  當您選取某個選項時，可能會變更對話方塊中其餘的選項。 某些選項僅適用於特定類型的資料庫使用者。 某些選項可以保留空白且將使用預設值。  
  
     **使用者名稱**  
     輸入新使用者的名稱。 如果您已經從 [使用者類型] 清單中選擇 [Windows 使用者]，也可以按一下省略符號 **(...)** 開啟 [選取使用者或群組] 對話方塊。  
  
     **登入名稱**  
     輸入使用者的登入。 或者，按一下省略符號 **(...)** ，開啟 [選取登入] 對話方塊。 如果您從 **[使用者類型]** 清單中選取 **[有登入的 SQL 使用者]** 或 **[Windows 使用者]** ， **[登入名稱]** 就是可用的。  
  
     **[密碼]** 與 **[確認密碼]**  
     針對在資料庫上進行驗證的使用者輸入密碼。  
  
     **預設語言**  
     輸入使用者的預設語言。  
  
     **預設的結構描述**  
     輸入將擁有此使用者建立之物件的結構描述。 或者，按一下省略符號 **(...)** ，開啟 [選取結構描述] 對話方塊。 如果您從 **[使用者類型]** 清單中選取 **[有登入的 SQL 使用者]** , **[沒有登入的 SQL 使用者]** 或 **[Windows 使用者]** ， **[預設結構描述]** 就是可用的。  
  
     **憑證名稱**  
     輸入要用於資料庫使用者的憑證。 或者，按一下省略符號 **(...)** ，開啟 [選取憑證] 對話方塊。 如果您從 **[使用者類型]** 清單中選取 **[對應到憑證的使用者]** ， **[憑證名稱]** 就是可用的。  
  
     **非對稱金鑰名稱**  
     輸入要用於資料庫使用者的金鑰。 或者，按一下省略符號 **(...)** ，開啟 [選取非對稱金鑰] 對話方塊。 如果您從 **[使用者類型]** 清單中選取 **[對應到非對稱金鑰的使用者]** ， **[非對稱金鑰名稱]** 就是可用的。  
  
6.  [!INCLUDE[clickOK](../../../includes/clickok-md.md)]  
  
### <a name="additional-options"></a>其他選項  
 [資料庫使用者 - 新增] 對話方塊也會在其他四個頁面上提供選項：[自有結構描述]、[成員資格]、[安全性實體] 和 [延伸屬性]。  
  
-   **[擁有的結構描述]** 頁面列出新資料庫使用者可擁有的所有可能結構描述。 若要在資料庫使用者中加入或移除結構描述，請在 **[這個使用者擁有的結構描述]** 底下選取或清除結構描述旁邊的核取方塊。  
  
-   **[成員資格]** 頁面列出新資料庫使用者可擁有的所有可能的資料庫角色成員資格。 若要在資料庫使用者中加入或移除角色，請在 **[資料庫角色成員資格]** 底下選取或清除角色旁邊的核取方塊。  
  
-   **[安全性實體]** 頁面列出所有可能的安全性實體以及可授與登入的安全性實體權限。  
  
-   **[擴充屬性]** 頁面讓您能夠將自訂屬性加入至資料庫使用者。 此頁面提供下列選項。  
  
     **Database**  
     顯示選取之資料庫的名稱。 此欄位是唯讀的。  
  
     **定序**  
     顯示用於所選取資料庫的定序。 此欄位是唯讀的。  
  
     **屬性**  
     檢視或指定物件的擴充屬性。 每個擴充屬性都包含與物件相關聯的一對名稱/值中繼資料。  
  
     **省略符號 (...)**  
     按一下 [值] 後面的省略符號 **(...)** ，開啟 [擴充屬性的值] 對話方塊。 在這個較大的位置輸入或檢視擴充屬性的值。 如需詳細資訊，請參閱＜ [擴充屬性的值對話方塊](../../databases/value-for-extended-property-dialog-box.md)＞。  
  
     **刪除**  
     移除選取的擴充屬性。  
  
##  <a name="create-a-user-using-t-sql"></a><a name="TsqlProcedure"></a> 使用 T-SQL 建立使用者  
    
1.  在 **[物件總管]** 中，連接到 [!INCLUDE[ssDE](../../../includes/ssde-md.md)]的執行個體。  
  
2.  在 [標準]  列上，按一下 [新增查詢] 。  
  
3.  複製下列範例並將其貼到查詢視窗中，然後按一下 **[執行]** 。  
  
    ```  
    -- Creates the login AbolrousHazem with password '340$Uuxwp7Mcxo7Khy'.  
    CREATE LOGIN AbolrousHazem   
        WITH PASSWORD = '340$Uuxwp7Mcxo7Khy';  
    GO  
  
    -- Creates a database user for the login created above.  
    CREATE USER AbolrousHazem FOR LOGIN AbolrousHazem;  
    GO  
    ```  
  
 如需詳細資訊，請參閱 [CREATE USER &#40;Transact-SQL&#41;](../../../t-sql/statements/create-user-transact-sql.md)，其中包含更多 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 範例。  
  
## <a name="see-also"></a>另請參閱  
 [主體 &#40;Database Engine&#41;](../../../relational-databases/security/authentication-access/principals-database-engine.md)   
 [建立登入](../../../relational-databases/security/authentication-access/create-a-login.md)   
 [CREATE LOGIN &#40;Transact-SQL&#41;](../../../t-sql/statements/create-login-transact-sql.md)  
  
  
