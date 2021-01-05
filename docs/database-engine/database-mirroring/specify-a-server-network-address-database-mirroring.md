---
title: 指定伺服器網路位址 (資料庫鏡像)
description: 了解如何指定資料庫鏡像端點的伺服器網路位址。 資料庫鏡像工作階段需要針對每個伺服器執行個體具備位址。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], deployment
- database mirroring [SQL Server], endpoint
- endpoints [SQL Server], database mirroring
- server network addresses [SQL Server]
ms.assetid: a64d4b6b-9016-4f1e-a310-b1df181dd0c6
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 28214674672887e57abeb2b8f7381eab2d744405
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641613"
---
# <a name="specify-a-server-network-address-database-mirroring"></a>指定伺服器網路位址 (資料庫鏡像)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  設定資料庫鏡像工作階段時，需要有每一個伺服器執行個體的伺服器網路位址。 伺服器執行個體的伺服器網路位址必須透過提供系統位址和執行個體所接聽的通訊埠編號，以明確識別該執行個體。  
  
 伺服器執行個體上必須有資料庫鏡像端點，您才能在伺服器網路位址中指定通訊埠。 如需詳細資訊，請參閱[建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)。  
  
  
##  <a name="syntax-for-a-server-network-address"></a><a name="Syntax"></a> 伺服器網路位址的語法  
 伺服器網路位址的語法採用下列格式：  
  
 TCP <strong>://</strong> *\<system-address>* <strong>:</strong> *\<port>*  
  
 其中  
  
-   *\<system-address>* 是可明確識別目的地電腦系統的字串。 伺服器位址通常是系統名稱 (如果系統位於同一個網域內)、完整網域名稱或 IP 位址。  
  
    -   如果系統位於同一個網域，您可以使用電腦系統的名稱，例如 `SYSTEM46`。  
  
    -   若要使用 IP 位址，則它在您的環境中必須是唯一的。 建議您只使用靜態的 IP 位址。 此 IP 位址可以是 IP 第 4 版 (IPv4) 或 IP 第 6 版 (IPv6)。 IPv6 位址必須使用方括弧括住，例如： **[** <IPv6 位址> **]** 。  
  
         若要取得系統的 IP 位址，請在 Windows 命令提示字元下，輸入 **ipconfig** 命令。  
  
    -   完整網域名稱保證可以運作。 這是在不同位置會有不同格式的本機定義位址字串。 完整網域名稱通常 (但不一定) 都是複合名稱，包含電腦名稱及一系列以句號分隔的網域區段，並採用下列格式：  
  
         _電腦名稱_ **.** _網域區段_[... **.** _網域區段_]  
  
         其中 *電腦名稱* 是執行伺服器執行個體之電腦的網路名稱，而 *網域區段*[... **.** _網域區段_] 則是伺服器的其餘網域資訊；例如： `localinfo.corp.Adventure-Works.com`。  
  
         網域區段的內容和數目是在公司或組織的內部決定的。 如果您不知道伺服器的完整網域名稱，請洽詢您的系統管理員。  
  
        > [!NOTE]  
        >  如需有關如何尋找完整網域名稱的詳細資訊，請參閱本主題稍後的「尋找完整網域名稱」。  
  
-   *\<port>* 是夥伴伺服器執行個體鏡像端點所使用的連接埠號碼。 如需指定端點的資訊，請參閱 [建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)。  
  
     資料庫鏡像端點可以使用電腦系統上任何可用的通訊埠。 電腦系統上的每個通訊埠編號必須只與一個端點產生關聯，而且每個端點會與單一伺服器執行個體產生關聯，因此相同伺服器上的不同伺服器執行個體會利用不同通訊埠接聽不同端點。 因此，當您設定資料庫鏡像工作階段時，在伺服器網路位址中指定的通訊埠，會永遠把工作階段導向到端點與該通訊埠產生關聯的伺服器執行個體。  
  
     在伺服器執行個體的伺服器網路位址中，只有與鏡像端點產生關聯的通訊埠編號，會從電腦上的任何其他執行個體中區分該執行個體。 下圖說明單一電腦上兩個伺服器執行個體的伺服器網路位址。 預設的執行個體會使用通訊埠 `7022` ，而具名執行個體則使用通訊埠 `7033`。 這兩個伺服器執行個體的伺服器網路位址分別為： `TCP://MYSYSTEM.Adventure-works.MyDomain.com:7022` 和 `TCP://MYSYSTEM.Adventure-works.MyDomain.com:7033`。 請注意，位址不包含伺服器執行個體的名稱。  
  
     ![預設執行個體的伺服器網路位址](../../database-engine/availability-groups/windows/media/dbm-2-instances-ports-1-system.gif "預設執行個體的伺服器網路位址")  
  
     若要識別目前關聯於伺服器執行個體之資料庫鏡像端點的通訊埠，請使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式：  
  
    ```  
    SELECT type_desc, port FROM sys.tcp_endpoints  
    ```  
  
     尋找 **type_desc** 值是 "DATABASE_MIRRORING" 的資料列，並使用對應通訊埠編號。  
  
### <a name="examples"></a>範例  
  
#### <a name="a-using-a-system-name"></a>A. 使用系統名稱  
 下列伺服器網路位址會指定一個系統名稱 `SYSTEM46`和通訊埠 `7022`。  
  
```  
ALTER DATABASE AdventureWorks SET PARTNER ='tcp://SYSTEM46:7022';  
```  
  
#### <a name="b-using-a-fully-qualified-domain-name"></a>B. 使用完整網域名稱  
 下列伺服器網路位址會指定一個完整網域名稱 `DBSERVER8.manufacturing.Adventure-Works.com`和通訊埠 `7024`。  
  
```  
ALTER DATABASE AdventureWorks SET PARTNER ='tcp://DBSERVER8.manufacturing.Adventure-Works.com:7024';  
```  
  
#### <a name="c-using-ipv4"></a>C. 使用 IPv4  
 下列伺服器網路位址會指定一個 IPv4 位址 `10.193.9.134`和通訊埠 `7023`。  
  
```  
ALTER DATABASE AdventureWorks SET PARTNER ='tcp://10.193.9.134:7023';  
```  
  
#### <a name="d-using-ipv6"></a>D. 使用 IPv6  
 下列伺服器網路位址會包含一個 IPv6 位址 `2001:4898:23:1002:20f:1fff:feff:b3a3`和通訊埠 `7022`。  
  
```  
ALTER DATABASE AdventureWorks SET PARTNER ='tcp://[2001:4898:23:1002:20f:1fff:feff:b3a3]:7022';  
```  
  
## <a name="finding-the-fully-qualified-domain-name"></a>尋找完整網域名稱  
 若要尋找系統的完整網域名稱，請在該系統的 Windows 命令提示字元下，輸入：  
  
 **IPCONFIG /ALL**  
  
 若要形成完整的網域名稱，請串連 <主機名稱> 和 <主要 DNS 尾碼> 的值，如下所示：  
  
 _<主機名稱>_ **.** _<主要 DNS 尾碼>_  
  
 例如，IP 組態  
  
 `Host Name  .  .  .  .  .  .  : MYSERVER`  
  
 `Primary Dns Suffix  .  .  .  : mydomain.Adventure-Works.com`  
  
 等於下列完整的網域名稱：  
  
 `MYSERVER.mydomain.Adventure-Works.com`  
  
##  <a name="examples"></a><a name="Examples"></a> 範例  
 下列範例會顯示另一個網域中名為 `REMOTESYSTEM3` 之電腦系統上伺服器執行個體的伺服器網路位址。 網域資訊為 `NORTHWEST.ADVENTURE-WORKS.COM`，而且資料庫鏡像端點的通訊埠為 `7025`。 如果有這些範例元件，伺服器網路位址就是：  
  
 `TCP://REMOTESYSTEM3.NORTHWEST.ADVENTURE-WORKS.COM:7025`  
  
 下列範例會顯示名為 `DBSERVER1`之電腦系統上伺服器執行個體的伺服器網路位址。 此系統位於本機網域，並由其系統名稱明確識別。 資料庫鏡像端點的通訊埠為 `7022`。  
  
 `TCP://DBSERVER1:7022`  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [建立 Windows 驗證的資料庫鏡像端點 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)  
  
## <a name="see-also"></a>另請參閱  
 [資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-sql-server.md)   
 [資料庫鏡像端點 &#40;SQL Server&#41;](../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)  
  
  
