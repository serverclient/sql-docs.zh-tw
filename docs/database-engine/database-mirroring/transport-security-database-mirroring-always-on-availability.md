---
title: 傳輸安全性：可用性群組和資料庫鏡像
description: 了解如何保護參與裝載於 SQL Server 上 Always On 可用性群組或資料庫鏡像工作階段的資料庫之間所傳輸訊息。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- sessions [SQL Server], database mirroring
- cryptography [SQL Server], database mirroring
- certificates [SQL Server], database mirroring
- encryption [SQL Server], database mirroring
- Windows authentication [SQL Server]
- authentication [SQL Server], database mirroring
- transport security
- database mirroring [SQL Server], security
ms.assetid: 49239d02-964e-47c0-9b7f-2b539151ee1b
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 8ff60136994d809b78cf09ebd98e4cc36ceb1a02
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641628"
---
# <a name="transport-security---database-mirroring---always-on-availability"></a>傳輸安全性 - 資料庫鏡像 - AlwaysOn 可用性
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳輸安全性牽涉到驗證，以及對資料庫間交換的訊息進行加密 (選擇性)。 對於資料庫鏡像和 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]而言，驗證與加密是針對資料庫鏡像端點所設定。 如需資料庫鏡像端點的簡介，請參閱 [資料庫鏡像端點 &#40;SQL Server&#41;](../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)。  
  
 **本主題內容：**  
  
-   [驗證](#Authentication)  
  
-   [資料加密](#DataEncryption)  
  
-   [相關工作](#RelatedTasks)  
  
##  <a name="authentication"></a><a name="Authentication"></a> 驗證  
 驗證就是確認使用者即為使用者所宣稱身分的程序。 資料庫鏡像端點之間的連接必須進行驗證。 夥伴或見證 (若有的話) 所提出的連接要求，也必須進行驗證。  
  
 伺服器執行個體用於資料庫鏡像或 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 的驗證類型就是資料庫鏡像端點的屬性。 資料庫鏡像端點有兩種可用的傳輸安全性類型：Windows 驗證 (安全性支援提供者介面 (SSPI)) 與憑證式驗證。  
  
### <a name="windows-authentication"></a>Windows 驗證  
 在 Windows 驗證下，每個伺服器執行個體會使用執行程序之 Windows 使用者帳戶的 Windows 認證來登入另一端。 Windows 驗證可能需要對登入帳戶進行一些手動設定，如下所示：  
  
-   如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體會以服務的形式在相同的網域帳戶底下執行，就不需要進行額外設定。  
  
-   如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體會以服務的形式在不同的網域帳戶底下執行 (在相同或受信任的網域中)，您就必須在其他每個伺服器執行個體的 **master** 中建立每個帳戶的登入，而且該登入必須被授與端點的 CONNECT 權限。  
  
-   如果 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體會以網路服務帳戶的身分執行，您就必須在每個其他伺服器的 _master_ **\\** _中建立每個主機電腦帳戶的登入 (_ DomainName **ComputerName$** )，而且該登入必須被授與端點的 CONNECT 權限。 這是因為在 Network Service 帳戶底下執行的伺服器執行個體會使用主機電腦的網域帳戶進行驗證。  
  
> [!NOTE]  
>  如需使用 Windows 驗證設定資料庫鏡像工作階段的範例，請參閱[範例：使用 Windows 驗證設定資料庫鏡像 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql.md)。  
  
### <a name="certificates"></a>憑證  
 在某些狀況下，像是伺服器執行個體不在信任網域中，或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 當作本機服務執行時，無法使用 Windows 驗證。 在這種情況下，不適用使用者認證，而必須使用憑證來驗證連接要求。 每個伺服器執行個體的鏡像端點都必須以其本機建立的憑證進行設定。  
  
 在建立憑證時會建立加密方法。 如需詳細資訊，請參閱 [允許資料庫鏡像端點使用輸出連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-outbound-connections.md)。 請謹慎管理您所使用的憑證。  
  
 伺服器執行個體會在設定連接時使用本身憑證的私密金鑰來建立其識別。 收到連接要求的伺服器執行個體，會使用寄件者憑證的公開金鑰來驗證寄件者的識別。 例如，請考量 Server_A 與 Server_B 這兩個伺服器執行個體。 Server_A 在將連接要求傳送給 Server_B 之前，使用其私密金鑰進行連接標頭的加密。 Server_B 則使用 Server_A 之憑證的公開金鑰來解密連接標頭。 若解密後的標頭正確無誤，Server_B 即得知標頭是由 Server_A 所加密，如此即完成連接的驗證。 若解密後的標頭不正確，Server_B 即得知連接要求不可靠，而拒絕連接。  
  
##  <a name="data-encryption"></a><a name="DataEncryption"></a> 資料加密  
 根據預設，資料庫鏡像端點要求在透過鏡像連接傳送資料時進行資料加密。 在此情況下，端點只能連接到同樣使用加密的端點。 除非您可保證網路的安全無虞，否則建議您要求對資料庫鏡像連接進行加密。 不過，您也可以停用加密或使它成為支援項目，而非必要項目。 若停用加密，資料就不會進行加密，而端點就無法連接到要求加密的端點。 若支援加密，則只有在對應的端點支援或要求加密時，資料才會加密。  
  
> [!NOTE]  
>  [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 在建立鏡像端點時，其加密會設為必要或停用。 若要將加密設定變更為 SUPPORTED，請使用 ALTER ENDPOINT [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式。 如需詳細資訊，請參閱 [ALTER ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/alter-endpoint-transact-sql.md)。  
  
 (選擇性) 您可以對 CREATE ENDPOINT 陳述式或 ALTER ENDPOINT 陳述式中的 ALGORITHM 選項指定下列其中一值，來控制端點可使用的加密演算法：  
  
|ALGORITHM 值|描述|  
|---------------------|-----------------|  
|RC4|指定端點必須使用 RC4 演算法。 這是預設值。<br /><br /> <strong>\*\* 警告 \*\*</strong> RC4 演算法已被取代。 [!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)] 我們建議您改用 AES。|  
|AES|指定端點必須使用 AES 演算法。|  
|AES RC4|指定這兩個端點必須與這個偏好 AES 演算法的端點針對加密演算法進行交涉。|  
|RC4 AES|指定這兩個端點必須與這個偏好 RC4 演算法的端點針對加密演算法進行交涉。|  
  
 如果連接的端點指定這兩種演算法，但指定順序不同，則以接受連接的端點為準。  
  
> [!NOTE]  
>  只有 RC4 演算法支援回溯相容性。 只有在資料庫相容性層級為 90 或 100 時，才能使用 RC4 或 RC4_128 加密新資料 (不建議使用)。請改用較新的演算法，例如其中一個 AES 演算法。 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本中使用 RC4 或 RC4_128 加密的資料，可以在任何相容性層級進行解密。  
>   
>  雖然 RC4 比 AES 快許多，但是 RC4 相對而言是較弱的演算法，而 AES 相對而言則是較強的演算法。 因此，建議您使用 AES 演算法。  
  
 如需用以指定加密之 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語法的資訊，請參閱 [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/create-endpoint-transact-sql.md)。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
 **若要設定資料庫鏡像端點的傳輸安全性**  
  
-   [建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)  
  
-   [使用 Windows 驗證建立資料庫鏡像工作階段 &#40;SQL Server Management Studio&#41;](../../database-engine/database-mirroring/establish-database-mirroring-session-windows-authentication.md)  
  
-   [建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)  
  
-   [允許資料庫鏡像端點使用輸出連線的憑證 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/database-mirroring-use-certificates-for-outbound-connections.md)  
  
## <a name="see-also"></a>另請參閱  
 [選擇加密演算法](../../relational-databases/security/encryption/choose-an-encryption-algorithm.md)   
 [ALTER ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/alter-endpoint-transact-sql.md)   
 [DROP ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/drop-endpoint-transact-sql.md)   
 [SQL Server Database Engine 和 Azure SQL Database 的資訊安全中心](../../relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database.md)   
 [在另一個伺服器執行個體上提供可用的資料庫時，管理中繼資料 &#40;SQL Server&#41;](../../relational-databases/databases/manage-metadata-when-making-a-database-available-on-another-server.md)   
 [資料庫鏡像端點 &#40;SQL Server&#41;](../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)   
 [sys.database_mirroring_endpoints &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-database-mirroring-endpoints-transact-sql.md)   
 [sys.dm_db_mirroring_connections &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/database-mirroring-sys-dm-db-mirroring-connections.md)   
 [為資料庫鏡像組態進行疑難排解 &#40;SQL Server&#41; &#40;SQL Server&#41;](../../database-engine/database-mirroring/troubleshoot-database-mirroring-configuration-sql-server.md)   
 [疑難排解 AlwaysOn 可用性群組組態 &#40;SQL Server&#41;](../../database-engine/availability-groups/windows/troubleshoot-always-on-availability-groups-configuration-sql-server.md)  
  
  
