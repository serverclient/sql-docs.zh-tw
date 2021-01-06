---
title: 設定見證
description: '描述如何使用 Transact-SQL 搭配 Windows 驗證設定資料庫鏡像見證。 '
ms.custom: seo-lt-2019
ms.date: 03/07/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- witness [SQL Server], establishing
- Windows authentication [SQL Server]
- database mirroring [SQL Server], witness
ms.assetid: bf5e87df-91a4-49f9-ae88-2a6dcf644510
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 01e95d971af1a9eeea18bbf699cb19014065fb18
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643625"
---
# <a name="add-a-database-mirroring-witness-using-windows-authentication-transact-sql"></a>使用 Windows 驗證加入資料庫鏡像見證 (Transact-SQL)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  為設定資料庫的見證，資料庫擁有者會指派 Database Engine 執行個體給見證伺服器的角色。 見證伺服器執行個體可以與主體或鏡像伺服器執行個體在相同電腦上執行，但是這會大幅地減少自動容錯移轉的強固性。  
  
 我們強烈建議見證應該位在另一台電腦上。 給定的伺服器可以參與具有相同或不同夥伴的多個並行資料庫鏡像工作階段。 一個給定的伺服器可以在某些工作階段中是夥伴，在其他工作階段中是見證。  
  
 見證是專門用於具有自動容錯移轉的高安全性模式。 在您設定見證之前，我們強烈建議您確定 SAFETY 屬性目前已設定為 FULL。  
  
> [!IMPORTANT]  
>  我們建議您在離峰時間設定資料庫鏡像，因為組態會影響效能。  
  
## <a name="establish-a-witness"></a>建立見證  
  
1.  在見證伺服器執行個體上，確定資料庫鏡像有端點存在。 不管要支援的鏡像工作階段有多少個，伺服器執行個體都必須只有一個資料庫鏡像端點。 如果您打算將這個伺服器執行個體專門作為資料庫鏡像工作階段中的見證，請指派見證角色給端點 (ROLE **=** WITNESS)。 如果您打算使用這個伺服器執行個體，作為一或多個其他資料庫鏡像工作階段中的夥伴，請將端點的角色指派為 ALL。  
  
     若要執行 SET WITNESS 陳述式，資料庫鏡像工作階段必須已經啟動 (在夥伴之間)，而且見證端點的 STATE 必須設為 STARTED。  
  
     若要了解見證伺服器執行個體是否具有它的資料庫鏡像端點，以及了解其角色和狀態，請在該執行個體上，使用下列 Transact-SQL 陳述式：  
  
    ```  
    SELECT role_desc, state_desc FROM sys.database_mirroring_endpoints  
    ```  
  
    > [!IMPORTANT]  
    >  若資料庫鏡像端點存在且已在使用中，我們建議您在該伺服器執行個體上為每個工作階段使用該端點。 卸除使用中端點會中斷現有工作階段的連接。 如果已針對工作階段設定見證，則卸除資料庫鏡像端點可能會導致該工作階段的主體伺服器失去仲裁；若發生此情況，則資料庫會離線，並中斷連接到資料庫的所有使用者。 如需詳細資訊，請參閱[仲裁：見證如何影響資料庫可用性 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/quorum-how-a-witness-affects-database-availability-database-mirroring.md)。  
  
     如果見證缺少端點，請參閱[建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)。  
  
2.  若夥伴執行個體是以不同網域使用者帳戶執行，請為每個執行個體中 master 資料庫的不同帳戶建立登入。 如需詳細資訊，請參閱 [使用 Windows 驗證允許資料庫鏡像的網路存取 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-allow-network-access-windows-authentication.md)。  
  
3.  連接到主體伺服器並執行以下陳述式：  
  
     ALTER DATABASE *<資料庫名稱>* SET WITNESS **=** _<伺服器網路位址>_  
  
     其中 *<資料庫名稱>* 是要鏡像的資料庫名稱 (此名稱在兩個夥伴中都相同)，而 *<伺服器網路位址>* 是見證伺服器執行個體的伺服器網路位址。  
  
     伺服器網路位址的語法如下：  
  
     TCP <b>://</b> _\<system-address>_ <b>:</b> _\<port>_  
  
     其中 \<*system-address>* 是清楚識別目的地電腦系統的字串，而 \<*port>* 是合作夥伴伺服器執行個體之鏡像端點所使用的連接埠號碼。 如需詳細資訊，請參閱 [指定伺服器網路位址 &#40;資料庫鏡像&#41;](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)。  
  
     例如，在主體伺服器執行個體上，下列 ALTER DATABASE 陳述式會設定見證。 資料庫名稱是 **AdventureWorks**、系統位址是 DBSERVER3 (見證系統的名稱)，而見證之資料庫鏡像端點所使用的連接埠是 `7022`：  
  
    ```  
    ALTER DATABASE AdventureWorks   
      SET WITNESS = 'TCP://DBSERVER3:7022'  
    ```  
  
## <a name="example"></a>範例  
 以下範例會建立資料庫鏡像見證。 在見證伺服器執行個體 ( `WITNESSHOST4`上的預設執行個體) 上：  
  
1.  使用通訊埠 `7022`僅為 WITNESS 角色的此伺服器執行個體建立端點。  
  
    ```  
    CREATE ENDPOINT Endpoint_Mirroring  
        STATE=STARTED   
        AS TCP (LISTENER_PORT=7022)   
        FOR DATABASE_MIRRORING (ROLE=WITNESS)  
    GO  
    ```  
  
2.  若見證是以 `SOMEDOMAIN\witnessuser`執行，但夥伴是以 `MYDOMAIN\dbousername`執行，請為夥伴執行個體的使用者帳戶建立登入。 為夥伴建立登入，如下所示：  
  
    ```  
    --Create a login for the partner server instances,  
    --which are both running as MYDOMAIN\dbousername:  
    USE master ;  
    GO  
    CREATE LOGIN [MYDOMAIN\dbousername] FROM WINDOWS ;  
    GO  
    --Grant connect permissions on endpoint to login account   
    --of partners  
    GRANT CONNECT ON ENDPOINT::Endpoint_Mirroring TO [MYDOMAIN\dbousername];  
    GO  
    ```  
  
3.  在每一個伺服器執行個體上，為見證伺服器執行個體建立登入：  
  
    ```  
    --Create a login for the witness server instance,  
    --which is running as SOMEDOMAIN\witnessuser:  
    USE master ;  
    GO  
    CREATE LOGIN [SOMEDOMAIN\witnessuser] FROM WINDOWS ;  
    GO  
    --Grant connect permissions on endpoint to login account   
    --of partners  
    GRANT CONNECT ON ENDPOINT::Endpoint_Mirroring TO [SOMEDOMAIN\witnessuser];  
    GO  
    ```  
  
4.  在主體伺服器上設定見證 (位於 `WITNESSHOST4`)：  
  
    ```  
    ALTER DATABASE AdventureWorks   
        SET WITNESS =   
        'TCP://WITNESSHOST4:7022'  
    GO  
    ```  
  
> [!NOTE]  
>  伺服器網路位址依通訊埠編號表示目標伺服器執行個體，它對應到執行個體的鏡像端點。  
  
 如需顯示安全性設定、準備鏡像資料庫、設定夥伴及新增見證的完整範例，請參閱[設定資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/setting-up-database-mirroring-sql-server.md)。  
  
## <a name="see-also"></a>另請參閱  
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [使用 Windows 驗證允許資料庫鏡像的網路存取 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-allow-network-access-windows-authentication.md)   
 [建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)   
 [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-establish-session-windows-authentication.md)   
 [從資料庫鏡像工作階段移除見證 &#40;SQL Server&#41;](../../database-engine/database-mirroring/remove-the-witness-from-a-database-mirroring-session-sql-server.md)   
 [資料庫鏡像見證](../../database-engine/database-mirroring/database-mirroring-witness.md)  
  
  
