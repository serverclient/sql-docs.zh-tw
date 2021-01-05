---
title: 建立資料庫鏡像端點 (Transact-SQL)
description: 使用 Transact-SQL 建立在 SQL Server 中使用 Windows 驗證的資料庫鏡像端點。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], deployment
- database mirroring [SQL Server], endpoint
- endpoints [SQL Server], database mirroring
- Windows authentication [SQL Server]
- database mirroring [SQL Server], security
ms.assetid: baf1a4b1-6790-4275-b261-490bca33bdb9
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 4a05341397185deefcc6de5d25ded34e910d7fac
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642141"
---
# <a name="create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql"></a>建立 Windows 驗證的資料庫鏡像端點 (Transact-SQL)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  此主題描述如何使用 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] ，在 [!INCLUDE[tsql](../../includes/tsql-md.md)]中建立使用 Windows 驗證的資料庫鏡像端點。 若要支援資料庫鏡像或 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] ， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的每個執行個體都需要一個資料庫鏡像端點。 伺服器執行個體只可有一個資料庫鏡像端點，而這個端點具有單一通訊埠。 建立資料庫鏡像端點後，該資料庫鏡像端點即可使用本機系統上的任何可用通訊埠。 伺服器執行個體上的所有資料庫鏡像工作階段都會接聽該通訊埠，且資料庫鏡像的所有內送連接也都會使用該通訊埠。  
  
> [!IMPORTANT]  
>  若資料庫鏡像端點存在且已在使用中，我們建議您使用該端點。 卸除使用中端點會中斷現有工作階段。  
  
 **本主題內容**  
  
-   **開始之前：** [安全性](#Security)  
  
-   **使用下列項目建立資料庫鏡像端點：** [Transact-SQL](#TsqlProcedure)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="security"></a><a name="Security"></a> Security  
 伺服器執行個體的驗證及加密方法是由系統管理員所建立。  
  
> [!WARNING]  
>  RC4 演算法已被取代。 [!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)] 我們建議您改用 AES。  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 需要 `CREATE ENDPOINT` 權限或系統管理員 `sysadmin` 固定伺服器角色中的成員資格。 如需詳細資訊，請參閱 [GRANT 端點權限 &#40;Transact-SQL&#41;](../../t-sql/statements/grant-endpoint-permissions-transact-sql.md)。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
  
#### <a name="to-create-a-database-mirroring-endpoint-that-uses-windows-authentication"></a>建立使用 Windows 驗證的資料庫鏡像端點  
  
1.  連結至您要建立資料庫鏡像端點的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體。  
  
2.  在標準列中，按一下 **[新增查詢]** 。  
  
3.  使用下列陳述式決定資料庫鏡像端點是否已經存在。  
  
    ```sql  
    SELECT name, role_desc, state_desc FROM sys.database_mirroring_endpoints;   
    ```  
  
    > [!IMPORTANT]  
    >  如果伺服器執行個體已經有資料庫鏡像端點，則在您於伺服器執行個體上建立的其他任何工作階段，都會使用該端點。  
  
4.  若要以 Transact-SQL 來建立使用 Windows 驗證的端點，請使用 `CREATE ENDPOINT` 陳述式。 陳述式會採用下列一般形式：  
  
     ```syntaxsql
     CREATE ENDPOINT *\<endpointName>*  
  
     STATE=STARTED  
  
     AS TCP ( LISTENER_PORT = *\<listenerPortList>* )  
  
     FOR DATABASE_MIRRORING  
  
     (  
  
     [ AUTHENTICATION = **WINDOWS** [ *\<authorizationMethod>* ]  
  
     ]  
  
     [ [**,**] ENCRYPTION = **REQUIRED**  
  
     [ ALGORITHM { *\<algorithm>* } ]  
  
     ]  
  
     [**,**] ROLE = *\<role>*  
  
     )  
     ```
     
     其中：  
  
    -   *\<endpointName>* 是伺服器執行個體的資料庫鏡像端點唯一名稱。  
  
    -   STARTED 指定要啟動及要開始接聽連接的端點。 資料庫鏡像端點通常是在 STARTED 狀態下建立。 您也可以在 STOPPED 狀態 (預設值) 或 DISABLED 狀態下啟動工作階段。  
  
    -   *\<listenerPortList>* 是想要伺服器用來接聽資料庫鏡像訊息的單一連接埠號碼 (*nnnn*)。 只允許 TCP；指定任何其他通訊協定將造成錯誤。  
  
         每個電腦系統僅能使用某個通訊埠編號一次。 建立資料庫鏡像端點後，該資料庫鏡像端點即可使用本機系統上的任何可用通訊埠。 若要識別系統上 TCP 端點目前使用的通訊埠，請使用下列 Transact-SQL 陳述式：  
  
        ```sql  
        SELECT name, port FROM sys.tcp_endpoints;  
        ```  
  
        > [!IMPORTANT]  
        >  每個伺服器執行個體都需要一個且唯一的接聽程式通訊埠。  
  
    -   若為 Windows 驗證，除非您想要端點只使用 NTLM 或 Kerberos 來驗證連接，否則 AUTHENTICATION 是選擇性選項。 *\<authorizationMethod>* 會指定下列任一方法以用於驗證連線：NTLM、KERBEROS 或 NEGOTIATE。 預設值 NEGOTIATE，將導致端點使用 Windows 交涉通訊協定來選擇 NTLM 或 Kerberos。 視相對端點的驗證層級而定，交涉可讓連接需要或不需要驗證。  
  
    -   依預設，ENCRYPTION 是設定為 REQUIRED。 這表示此端點的所有連接都必須使用加密。 不過，您可以停用加密或使其在端點上為選擇性的。 替代方案如下所示：  
  
        |值|定義|  
        |-----------|----------------|  
        |DISABLED|指定透過連接傳送的資料不加密。|  
        |SUPPORTED|指定只有在相對端點指定為 SUPPORTED 或 REQUIRED 時才加密資料。|  
        |REQUIRED|指定透過連接所傳送的資料必須加密。|  
  
         如果端點需要加密，其他的端點必須將 ENCRYPTION 設定為 SUPPORTED 或 REQUIRED。  
  
    -   *\<algorithm>* 提供用於指定端點加密標準的選項。 *\<algorithm>* 值可為下列任一演算法或演算法的組合：RC4、AES、AES RC4 或 RC4 AES。  
  
         AES RC4 指定此端點將交涉加密演算法，將優先權指定給 AES 演算法。 RC4 AES 指定此端點將交涉加密演算法，將優先權指定給 RC4 演算法。 如果這兩個端點都指定了這兩種演算法 (但指定順序不同)，則以接受連接的端點為準。 明確提供相同的演算法，以避免不同伺服器之間發生連線錯誤。
  
        > [!WARNING]  
        >  RC4 演算法已被取代。 [!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)] 我們建議您改用 AES。  
  
    -   *\<role>* 定義伺服器可執行的一或多個角色。 指定所需的 ROLE。 然而，端點的角色只與資料庫鏡像有關。 對於 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]，端點的角色會被忽略。  
  
         若要允許伺服器執行個體做為一個資料庫鏡像工作階段的一個角色，並做為另一個工作階段的其他角色，請指定 ROLE=ALL。 若要限制伺服器執行個體作為夥伴或見證伺服器，請分別指定 `ROLE = PARTNER` 或 `ROLE = WITNESS`。  
  
        > [!NOTE]  
        >  如需適用不同版本 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]之資料庫鏡像選項的詳細資訊，請參閱 [SQL Server 2016 版本支援的功能](~/sql-server/editions-and-supported-features-for-sql-server-2016.md)。  
  
     如需 CREATE ENDPOINT 語法的完整說明，請參閱 [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/create-endpoint-transact-sql.md)中建立使用 Windows 驗證的資料庫鏡像端點。  
  
    > [!NOTE]  
    >  若要變更現有的端點，請使用 [ALTER ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/alter-endpoint-transact-sql.md)中建立使用 Windows 驗證的資料庫鏡像端點。  
  
###  <a name="example-creating-endpoints-to-support-for-database-mirroring-transact-sql"></a><a name="TsqlExample"></a> 範例：建立端點以支援資料庫鏡像 (Transact-SQL)  
 下列範例會在三部不同的電腦系統上建立預設伺服器執行個體的資料庫鏡像端點：  
  
|伺服器執行個體的角色|主機電腦的名稱|  
|-----------------------------|---------------------------|  
|夥伴 (一開始為主體角色)|`SQLHOST01\.`|  
|夥伴 (一開始為鏡像角色)|`SQLHOST02\.`|  
|見證|`SQLHOST03\.`|  
  
 在此範例中，雖然任何可用的通訊埠編號都可以使用，不過三個終止點都會使用通訊埠編號 7022。 AUTHENTICATION 選項是不必要的，因為終止點會使用預設類型「Windows 驗證」。 ENCRYPTION 選項也沒有必要，因為終止點隨時會交涉連線的驗證方法，這是「Windows 驗證」的預設行為。 另外，所有終止點都需要加密，這也是預設行為。  
  
 每個伺服器執行個體都限制為擔任夥伴或見證，每個伺服器的終止點都會明確地指定其角色 (ROLE=PARTNER 或 ROLE=WITNESS)。  
  
> [!IMPORTANT]  
>  每個伺服器執行個體都僅能有一個終止點。 因此，如果您要伺服器執行個體成為某些工作階段中的夥伴，而其他的為見證，請指定 ROLE=ALL。  
  
```sql  
--Endpoint for initial principal server instance, which  
--is the only server instance running on SQLHOST01.  
CREATE ENDPOINT endpoint_mirroring  
    STATE = STARTED  
    AS TCP ( LISTENER_PORT = 7022 )  
    FOR DATABASE_MIRRORING (ROLE=PARTNER);  
GO  
--Endpoint for initial mirror server instance, which  
--is the only server instance running on SQLHOST02.  
CREATE ENDPOINT endpoint_mirroring  
    STATE = STARTED  
    AS TCP ( LISTENER_PORT = 7022 )  
    FOR DATABASE_MIRRORING (ROLE=PARTNER);  
GO  
--Endpoint for witness server instance, which  
--is the only server instance running on SQLHOST03.  
CREATE ENDPOINT endpoint_mirroring  
    STATE = STARTED  
    AS TCP ( LISTENER_PORT = 7022 )  
    FOR DATABASE_MIRRORING (ROLE=WITNESS);  
GO  
```  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **若要設定資料庫鏡像端點**  
  
-   [針對 AlwaysOn 可用性群組建立資料庫鏡像端點 &#40;SQL Server PowerShell&#41;](../../database-engine/availability-groups/windows/database-mirroring-always-on-availability-groups-powershell.md)  
  
-   [使用資料庫鏡像端點憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)  
  
    -   [允許資料庫鏡像端點使用輸出連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-outbound-connections.md)  
  
    -   [允許資料庫鏡像端點使用輸入連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-inbound-connections.md)  
  
-   [指定伺服器網路位址 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)  
  
-   [在新增或修改可用性複本時指定端點 URL &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)  
  
 **若要檢視有關資料庫鏡像端點的資訊**  
  
-   [sys.database_mirroring_endpoints &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-mirroring-endpoints-transact-sql.md)  
  
## <a name="see-also"></a>另請參閱  
 [ALTER ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/alter-endpoint-transact-sql.md)   
 [選擇加密演算法](../../relational-databases/security/encryption/choose-an-encryption-algorithm.md)   
 [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/create-endpoint-transact-sql.md)   
 [指定伺服器網路位址 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)   
 [範例：使用 Windows 驗證設定資料庫鏡像 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql.md)   
 [資料庫鏡像端點 &#40;SQL Server&#41;](../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)  
  
  

