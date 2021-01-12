---
description: CREATE ENDPOINT (Transact-SQL)
title: CREATE ENDPOINT (Transact-SQL)
ms.custom: ''
ms.date: 08/10/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ENDPOINT
- CREATE ENDPOINT
- ENDPOINT_TSQL
- CREATE_ENDPOINT_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- database mirroring [SQL Server], endpoint
- HTTP SOAP support [SQL Server]
- CREATE ENDPOINT statement
- Availability Groups [SQL Server], configuring
- endpoints [SQL Server], creating
- SOAP [SQL Server built-in support], endpoints
- SOAP [SQL Server built-in support], sqlbatch
- DATABASE_MIRRORING option
- HTTP protocol option [SQL Server]
- SOAP [SQL Server built-in support], ad hoc
- TCP protocol option [SQL Server]
- SERVICE_BROKER option
- Availability Groups [SQL Server], endpoint
ms.assetid: 6405e7ec-0b5b-4afd-9792-1bfa5a2491f6
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: d731a2b41fb98d5ccf5ac9890e9c6de5ee94eacf
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98100863"
---
# <a name="create-endpoint-transact-sql"></a>CREATE ENDPOINT (Transact-SQL)

[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  建立端點及定義其屬性，包括用戶端應用程式可用的方法。 如需相關的權限資訊，請參閱 [GRANT 端點權限 &#40;Transact-SQL&#41;](../../t-sql/statements/grant-endpoint-permissions-transact-sql.md)。  
  
 在邏輯上，CREATE ENDPOINT 的語法可分為兩個部分：  
  
-   第一部分從 AS 開始，並在 FOR 子句之前結束。  
  
     在這個部分，您需要提供傳輸通訊協定 (TCP) 的詳細資訊，並設定端點的接聽通接埠編號，以及端點驗證的方法及/或您要限制其存取端點的 IP 位址 (若有) 清單。  
  
-   第二個部分從 FOR 子句開始。  
  
     在這個部分，您定義端點上支援的裝載。 裝載可以是下列數個支援類型之一：[!INCLUDE[tsql](../../includes/tsql-md.md)]、Service Broker 及資料庫鏡像。 在這個部分，您還會併入特定語言資訊。  
  
> **注意：** [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 已移除原生 XML Web Service (SOAP/HTTP 端點)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE ENDPOINT endPointName [ AUTHORIZATION login ]  
[ STATE = { STARTED | STOPPED | DISABLED } ]  
AS { TCP } (  
   <protocol_specific_arguments>  
        )  
FOR { TSQL | SERVICE_BROKER | DATABASE_MIRRORING } (  
   <language_specific_arguments>  
        )  
  
<AS TCP_protocol_specific_arguments> ::=  
AS TCP (  
  LISTENER_PORT = listenerPort  
  [ [ , ] LISTENER_IP = ALL | ( xx.xx.xx.xx IPv4 address ) | ( '__:__1' IPv6 address ) ]  
  
)  
  
<FOR SERVICE_BROKER_language_specific_arguments> ::=  
FOR SERVICE_BROKER (  
   [ AUTHENTICATION = {   
            WINDOWS [ { NTLM | KERBEROS | NEGOTIATE } ]   
      | CERTIFICATE certificate_name   
      | WINDOWS [ { NTLM | KERBEROS | NEGOTIATE } ] CERTIFICATE certificate_name   
      | CERTIFICATE certificate_name WINDOWS [ { NTLM | KERBEROS | NEGOTIATE } ]   
    } ]  
   [ [ , ] ENCRYPTION = { DISABLED | { { SUPPORTED | REQUIRED }   
       [ ALGORITHM { AES | RC4 | AES RC4 | RC4 AES } ] }   
   ]  
   [ [ , ] MESSAGE_FORWARDING = { ENABLED | DISABLED } ]  
   [ [ , ] MESSAGE_FORWARD_SIZE = forward_size ]  
)  
  

<FOR DATABASE_MIRRORING_language_specific_arguments> ::=  
FOR DATABASE_MIRRORING (  
   [ AUTHENTICATION = {   
            WINDOWS [ { NTLM | KERBEROS | NEGOTIATE } ]   
      | CERTIFICATE certificate_name   
      | WINDOWS [ { NTLM | KERBEROS | NEGOTIATE } ] CERTIFICATE certificate_name   
      | CERTIFICATE certificate_name WINDOWS [ { NTLM | KERBEROS | NEGOTIATE } ]   
   [ [ [ , ] ] ENCRYPTION = { DISABLED | { { SUPPORTED | REQUIRED }   
       [ ALGORITHM { AES | RC4 | AES RC4 | RC4 AES } ] }   
  
    ]   
   [ , ] ROLE = { WITNESS | PARTNER | ALL }  
)  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *endPointName*  
 這是您要建立之端點的指派名稱。 更新或刪除端點時可使用該名稱。  
  
 AUTHORIZATION *login*  
 指定被指派新建立之端點物件擁有權的有效 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 Windows 登入。 如果未指定 AUTHORIZATION，依預設，呼叫者會成為新建立之物件的擁有者。  
  
 若要指定 AUTHORIZATION 來指派擁有權，呼叫者必須具備指定 *login* 的 IMPERSONATE 權限。  
  
 若要重新指派擁有權，請參閱 [ALTER ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/alter-endpoint-transact-sql.md)。  
  
 STATE **=** { STARTED | **STOPPED** | DISABLED }  
 這是建立端點時的端點狀態。 如果在建立端點時沒有指定狀態，STOPPED 是預設值。  
  
 STARTED  
 端點已啟動，並主動接聽連接。  
  
 DISABLED  
 端點已停用。 在這種狀態下，伺服器會接聽通訊埠要求，但會將錯誤傳回用戶端。  
  
 **STOPPED**  
 端點已停止。 在這種狀態下，伺服器不接聽端點埠或不回應任何嘗試使用該端點的要求。  
  
 若要變更狀態，請使用 [ALTER ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/alter-endpoint-transact-sql.md)。  
  
 AS { TCP }  
 指定要使用的傳輸通訊協定。  
  
 FOR { TSQL | SERVICE_BROKER | DATABASE_MIRRORING }  
 指定裝載類型。  
  
 目前沒有任何 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語言特定的引數要傳入 `<language_specific_arguments>` 參數。  
  
 **TCP 通訊協定選項**  
  
 下列引數只適用於 TCP 通訊協定選項。  
  
 LISTENER_PORT **=** _listenerPort_  
 指定 Service Broker TCP/IP 通訊協定用來接聽連接時所用的通訊埠編號。 依慣例會使用 4022，但介於 1024 和 32767 之間的任何數字都有效。  
  
 LISTENER_IP **=** ALL | **(** _4-part-ip_ **)**  |  **(** "*ip_address_v6*" **)**  
 指定端點將接聽的 IP 位址。 預設值是 ALL。 這表示接聽程式會接受與任何有效 IP 位址的連接。  
  
 如果您使用 IP 位址來設定資料庫鏡像，而不是使用完整網域名稱 (`ALTER DATABASE SET PARTNER = partner_IP_address` 或 `ALTER DATABASE SET WITNESS = witness_IP_address`)，則當您建立鏡像端點時，您必須指定 `LISTENER_IP =IP_address` 而非 `LISTENER_IP=ALL`。  
  
 **SERVICE_BROKER 和 DATABASE_MIRRORING 選項**  
  
 下列 AUTHENTICATION 和 ENCRYPTION 引數由 SERVICE_BROKER 和 DATABASE_MIRRORING 選項共用。  
  
> [!NOTE]  
>  有關 SERVICE_BROKER 特定的選項，請參閱本節稍後的「SERVICE_BROKER 選項」。 有關 DATABASE_MIRRORING 特定的選項，請參閱本節稍後的「DATABASE_MIRRORING 選項」。  
  
 AUTHENTICATION **=** \<authentication_options> 指定此端點連接的 TCP/IP 驗證需求。 預設值是 WINDOWS。  
  
 支援的驗證方法包括 NTLM 及 (或) Kerberos。  
  
> [!IMPORTANT]  
>  伺服器執行個體中的所有鏡像連接全都使用單一資料庫鏡像端點。 建立其他資料庫鏡像端點的任何嘗試都會失敗。  
  
 **\<authentication_options> ::=**  
  
 **WINDOWS** [ { NTLM \| KERBEROS \| **NEGOTIATE** } ]  
 指定端點要利用 Windows 驗證通訊協定連接來驗證端點。 這是預設值。  
  
 如果您指定授權方法 (NTLM 或 KERBEROS)，一律以該方法做為驗證通訊協定。 預設值 NEGOTIATE 會導致端點利用 Windows 交涉通訊協定來選擇 NTLM 或 Kerberos。  
  
 CERTIFICATE *certificate_name*  
 指定端點必須利用 *certificate_name* 所指定的憑證驗證連接，來建立授權的身分識別。 遠端點必須有一個特定憑證，該憑證含有符合指定憑證之私密金鑰的公開金鑰。  
  
 WINDOWS [ { NTLM \| KERBEROS \| **NEGOTIATE** } ] CERTIFICATE *certificate_name*  
 指定端點必須嘗試利用 Windows 驗證連接，而且，如果該嘗試失敗，則嘗試使用指定的憑證。  
  
 CERTIFICATE *certificate_name* WINDOWS [ { NTLM \| KERBEROS \| **NEGOTIATE** } ]  
 指定端點必須嘗試利用指定的憑證連接，而且，如果該嘗試失敗，則嘗試使用 Windows 驗證。  
  
 ENCRYPTION = { DISABLED \| SUPPORTED \| **REQUIRED** } [ALGORITHM { **AES** \| RC4 \| AES RC4 \| RC4 AES } ]  
 指定是否要在處理序中使用加密。 預設值是 REQUIRED。  
  
 DISABLED  
 指定透過連接傳送的資料不加密。  
  
 SUPPORTED  
 指定只有在相對端點指定為 SUPPORTED 或 REQUIRED 時才加密資料。  
  
 REQUIRED  
 指定這個端點的連接必須使用加密。 因此，若要連接這個端點，其他端點必須將 ENCRYPTION 設為 SUPPORTED 或 REQUIRED。  
  
 您也可以選擇性地利用 ALGORITHM 引數來指定端點使用的加密格式，如下所示：  
  
 **AES**  
 指定端點必須使用 AES 演算法。 這在 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 和更新版本中為預設值。  
  
 RC4  
 指定端點必須使用 RC4 演算法。 這是透過 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 的預設值。  
  
> [!NOTE]  
>  只有 RC4 演算法支援回溯相容性。 只有在資料庫相容性層級為 90 或 100 時，才能使用 RC4 或 RC4_128 加密新資料 (不建議使用)。請改用較新的演算法，例如其中一個 AES 演算法。 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本中使用 RC4 或 RC4_128 加密的資料，可以在任何相容性層級進行解密。  
  
 AES RC4  
 指定這兩個端點必須與這個偏好 AES 演算法的端點針對加密演算法進行交涉。  
  
 RC4 AES  
 指定這兩個端點必須與這個偏好 RC4 演算法的端點針對加密演算法進行交涉。  
  
> [!NOTE]  
>  RC4 演算法已被取代。 [!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)] 我們建議您改用 AES。  
  
 如果這兩個端點都指定了這兩種演算法 (但指定順序不同)，則以接受連接的端點為準。  
  
 **SERVICE_BROKER 選項**  
  
 以下是 SERVICE_BROKER 選項特定的引數。  
  
 MESSAGE_FORWARDING **=** { ENABLED | **DISABLED** }  
 判斷是否要轉送這個端點接收的訊息 (適用於位在其他位置的服務)。  
  
 ENABLED  
 如果已提供轉送位址，則會轉送訊息。  
  
 DISABLED  
 捨棄適用於其他位置的服務之訊息。 這是預設值。  
  
 MESSAGE_FORWARD_SIZE **=** _forward_size_  
 指定當儲存即將要轉送的訊息時，配置給端點使用的最大儲存體數量 (以 MB 為單位)。  
  
 **DATABASE_MIRRORING 選項**  
  
 以下是 DATABASE_MIRRORING 選項特定的引數。  
  
 ROLE **=** { WITNESS | PARTNER | ALL }  
 指定端點支援的資料庫鏡像一或多個角色。  
  
 WITNESS  
 使端點在鏡像處理序中能夠以見證角色來執行。  
  
> [!NOTE]  
>  如果是 [!INCLUDE[ssExpressEd2005](../../includes/ssexpressed2005-md.md)]，WITNESS 是唯一的可用選項。  
  
 PARTNER  
 使端點在鏡像處理序中能夠以夥伴角色來執行。  
  
 ALL  
 使端點在鏡像處理序中能夠同時以見證和夥伴角色來執行。  
  
 如需這些角色的詳細資訊，請參閱[資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)。  
  
> [!NOTE]  
>  DATABASE_MIRRORING 沒有預設的通訊埠。  
  
## <a name="remarks"></a>備註  
 ENDPOINT DDL 陳述式不能在使用者交易內執行。 即使使用中的快照隔離等級正在使用變更的端點，ENDPOINT DDL 陳述式也不會失敗。  
  
 可利用下列方式，針對 ENDPOINT 來執行要求：  
  
-   **系統管理員 (sysadmin)** 固定伺服器角色的成員  
  
-   端點的擁有者  
  
-   已被授與端點之 CONNECT 權限的使用者或群組  
  
## <a name="permissions"></a>權限  
 需要 CREATE ENDPOINT 權限或 **系統管理員 (sysadmin)** 固定伺服器角色的成員資格。 如需詳細資訊，請參閱 [GRANT 端點權限 &#40;Transact-SQL&#41;](../../t-sql/statements/grant-endpoint-permissions-transact-sql.md)。  
  
## <a name="example"></a>範例  
  
### <a name="creating-a-database-mirroring-endpoint"></a>建立資料庫鏡像端點  
 下列範例建立資料庫鏡像端點。 該端點使用的是通訊埠編號 `7022` (雖然任何可用通訊埠編號都適用)。 該端點設定為只利用 Kerberos 來使用 Windows 驗證。 `ENCRYPTION` 選項設定為非預設值 `SUPPORTED`，來支援加密或未加密資料。 該端點設定為同時支援夥伴和見證角色。  
  
```sql  
CREATE ENDPOINT endpoint_mirroring  
    STATE = STARTED  
    AS TCP ( LISTENER_PORT = 7022 )  
    FOR DATABASE_MIRRORING (  
       AUTHENTICATION = WINDOWS KERBEROS,  
       ENCRYPTION = SUPPORTED,  
       ROLE=ALL);  
GO  
```  

### <a name="create-a-new-endpoint-pointing-to-a-specific-ipv4-address-and-port"></a>建立指向特定 IPv4 位址和連接埠的新端點

```sql
CREATE ENDPOINT ipv4_endpoint_special
STATE = STARTED
AS TCP (
    LISTENER_PORT = 55555, LISTENER_IP = (10.0.75.1)
)
FOR TSQL ();

GRANT CONNECT ON ENDPOINT::[TSQL Default TCP] TO public; -- Keep existing public permission on default endpoint for demo purpose
GRANT CONNECT ON ENDPOINT::ipv4_endpoint_special
TO login_name;
```

### <a name="create-a-new-endpoint-pointing-to-a-specific-ipv6-address-and-port"></a>建立指向特定 IPv6 位址和連接埠的新端點

```sql
CREATE ENDPOINT ipv6_endpoint_special
STATE = STARTED
AS TCP (
    LISTENER_PORT = 55555, LISTENER_IP = ('::1')
)
FOR TSQL ();

GRANT CONNECT ON ENDPOINT::[TSQL Default TCP] TO public;
GRANT CONNECT ON ENDPOINT::ipv6_endpoint_special

```
  
## <a name="see-also"></a>另請參閱  
 [ALTER ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/alter-endpoint-transact-sql.md)   
 [選擇加密演算法](../../relational-databases/security/encryption/choose-an-encryption-algorithm.md)   
 [DROP ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/drop-endpoint-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
  

