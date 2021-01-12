---
description: CREATE ROUTE (Transact-SQL)
title: CREATE ROUTE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/30/2018
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CREATE_ROUTE_TSQL
- ROUTE
- CREATE ROUTE
- ROUTE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- lifetimes [Service Broker]
- routes [Service Broker], creating
- forwarding brokers [SQL Server]
- service names [Service Broker]
- database mirroring [SQL Server], routing
- expired routes [SQL Server]
- activating routes
- CREATE ROUTE statement
ms.assetid: 7e695364-1a98-4cfd-8ebd-137ac5a425b3
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-mi-current||>=sql-server-2016||>=sql-server-linux-2017
ms.openlocfilehash: fc34a3eb5987a7348733755ee69aafda87e16ac3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091954"
---
# <a name="create-route-transact-sql"></a>CREATE ROUTE (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-asdbmi-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-asdbmi-xxxx-xxx-md.md )]

  將新的路由加入目前資料庫的路由表中。 如果是外寄郵件，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會檢查本機資料庫中的路由表來判斷路由。 如果是另一個執行個體所引發之交談的訊息，其中包括要轉寄的訊息，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會檢查 **msdb** 中的路由。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE ROUTE route_name  
[ AUTHORIZATION owner_name ]  
WITH    
   [ SERVICE_NAME = 'service_name', ]  
   [ BROKER_INSTANCE = 'broker_instance_identifier' , ]  
   [ LIFETIME = route_lifetime , ]  
   ADDRESS =  'next_hop_address'  
   [ , MIRROR_ADDRESS = 'next_hop_mirror_address' ]  
[ ; ]  
```  
  
## <a name="arguments"></a>引數  
 *route_name*  
 這是要建立的路由名稱。 新路由會建立在目前的資料庫中，擁有者是 AUTHORIZATION 子句所指定的主體。 您不可指定伺服器、資料庫和結構描述名稱。 *route_name* 必須是有效的 **sysname**。  
  
 AUTHORIZATION *owner_name*  
 將路由的擁有者設為指定的資料庫使用者或角色。 當目前使用者是 *db_owner* 固定資料庫角色的成員或 **sysadmin** 固定伺服器角色的成員時，**owner_name** 可以是任何有效使用者或角色的名稱。 否則，*owner_name* 必須是目前使用者的名稱、目前使用者有其 IMPERSONATE 權限的使用者名稱，或目前使用者所屬的角色名稱。 當略過這個子句時，路由會屬於目前的使用者。  
  
 WITH  
 導入定義所建立之路由的子句。  
  
 SERVICE_NAME = **'** _service\_name_ **'**  
 指定這個路由所指向的遠端服務名稱。 *service_name* 必須與遠端服務所使用的名稱完全相符。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會使用逐位元組的比較方式來比對 *service_name*。 換言之，這項比較會區分大小寫，且不會考慮目前的定序。 如果省略 SERVICE_NAME，這個路由會符合任何服務名稱，但符合的優先權低於指定 SERVICE_NAME 的路由。 服務名稱是 **'SQL/ServiceBroker/BrokerConfiguration'** 的路由，是指向 Broker Configuration Notice 服務的路由。 指向這項服務的路由不能指定 Broker 執行個體。  
  
 BROKER_INSTANCE = **'** _broker\_instance\_identifier_ **'**  
 指定主控目標服務的資料庫。 *broker_instance_identifier* 參數必須是遠端資料庫的 Broker 執行個體識別碼，您可以在所選資料庫中執行下列查詢來取得這個識別碼：  
  
```sql  
SELECT service_broker_guid  
FROM sys.databases  
WHERE database_id = DB_ID()  
```  
  
 當省略 BROKER_INSTANCE 子句時，這項路由會符合任何 Broker 執行個體。 當交談並未指定 Broker 執行個體時，符合任何 Broker 執行個體的路由，其相符優先權會高於含明確 Borker 執行個體的路由。 如果交談指定了 Broker 執行個體，含 Borker 執行個體的路由之優先權會高於符合任何 Broker 執行個體的路由。  
  
 LIFETIME **=** _route\_lifetime_  
 指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將路由保留在路由表中的時間量 (以秒為單位)。 在存留期間結束時，路由會到期，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在選擇新交談的路由時，不會再考慮這個路由。 如果省略這個子句，*route_lifetime* 便是 NULL，且路由永遠不會到期。  
  
 ADDRESS **='** _next\_hop\_address_ **'**  
針對 SQL 受控執行個體，`ADDRESS` 必須為本機。 

指定這個路由的網路位址。 *next_hop_address* 以下列格式指定 TCP/IP 位址：  
  
 **TCP://** { *dns_name* | *netbios_name* | *ip_address* } **:** _port\_number_  
  
 指定的 *port_number* 必須符合在指定電腦的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 執行個體之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 端點的連接埠號碼。 這可以在選取的資料庫中執行下列查詢來取得：  
  
```sql  
SELECT tcpe.port  
FROM sys.tcp_endpoints AS tcpe  
INNER JOIN sys.service_broker_endpoints AS ssbe  
   ON ssbe.endpoint_id = tcpe.endpoint_id  
WHERE ssbe.name = N'MyServiceBrokerEndpoint';  
```  
  
 當服務在鏡像資料庫中，您也必須指定主控鏡像資料庫之其他執行個體的 MIRROR_ADDRESS。 否則，這個路由不會進行容錯移轉，將工作交給鏡像。  
  
 當路由在 **next_hop_address** 中指定 *'LOCAL'* 時，訊息會傳遞給在目前 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體內的服務。  
  
 當路由在 **next_hop_address** 中指定 *'TRANSPORT'* 時，會根據服務名稱中的網路位址來決定網路位址。 指定 **'TRANSPORT'** 的路由不能指定服務名稱或 Broker 執行個體。  
  
 MIRROR_ADDRESS **='** _next\_hop\_mirror\_address_ **'**  
 指定有一個鏡像資料庫在 *next_hop_address* 之鏡像資料庫的網路位址。 *next_hop_mirror_address* 以下列格式指定 TCP/IP 位址：  
  
 **TCP://** { *dns_name* | *netbios_name* | *ip_address* } **:** *port_number*  
  
 指定的 *port_number* 必須符合在指定電腦的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 執行個體之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 端點的連接埠號碼。 這可以在選取的資料庫中執行下列查詢來取得：  
  
```sql  
SELECT tcpe.port  
FROM sys.tcp_endpoints AS tcpe  
INNER JOIN sys.service_broker_endpoints AS ssbe  
   ON ssbe.endpoint_id = tcpe.endpoint_id  
WHERE ssbe.name = N'MyServiceBrokerEndpoint';  
```  
  
 當指定 MIRROR_ADDRESS 時，路由必須指定 SERVICE_NAME 子句和 BROKER_INSTANCE 子句。 在 **next_hop_address** 中指定 **'LOCAL'** 或 *'TRANSPORT'* 的路由可能不會指定鏡像位址。  
  
## <a name="remarks"></a>備註  
 儲存路由的路由表是能夠利用 **sys.routes** 目錄檢視來讀取的中繼資料表。 您只能利用 CREATE ROUTE、ALTER ROUTE 和 DROP ROUTE 陳述式來更新這個目錄檢視。  
  
 依預設，每個使用者資料庫中的路由表都包含一個路由。 這個路由的名稱是 **AutoCreatedLocal**。 這個路由在 **next_hop_address** 中指定 *'LOCAL'* ，且會比對任何服務名稱和 Broker 執行個體識別碼。  
  
 當路由在 **next_hop_address** 中指定 *'TRANSPORT'* 時，會根據服務名稱來決定網路位址。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可以順利處理開頭是網路位址且格式對 *next_hop_address* 有效的服務名稱。  
  
 路由表可以包含指定相同服務、網路位址和 Broker 執行個體識別碼之任意數目的路由。 在這個情況下，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會利用在交談所指定的資訊和路由表所指定的資訊之間，設計用來尋找完全相符項目的程序，來選擇路由。  
  
 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 不會從路由表中移除過期的路由。 您可以利用 ALTER ROUTE 陳述式，使過期的路由成為在使用中。  
  
 路由不能是暫存物件。 您可以使用開頭是 **#** 的路由名稱，但它們是永久物件。  
  
## <a name="permissions"></a>權限  
 建立路由的權限預設為 **db_ddladmin** 或 **db_owner** 固定資料庫角色的成員，以及 **sysadmin** 固定伺服器角色的成員。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-a-tcpip-route-by-using-a-dns-name"></a>A. 使用 DNS 名稱來建立 TCP/IP 路由  
 下列範例會建立服務 `//Adventure-Works.com/Expenses` 的路由。 這個路由指定送往這項服務的訊息要通過 TCP 而到達 DNS 名稱 `1234` 所識別之主機的通訊埠 `www.Adventure-Works.com`。 目標伺服器會在到達時將訊息傳遞給唯一識別碼 `D8D4D268-00A3-4C62-8F91-634B89C1E315` 所識別的 Broker 執行個體。  
  
```sql 
CREATE ROUTE ExpenseRoute  
    WITH  
    SERVICE_NAME = '//Adventure-Works.com/Expenses',  
    BROKER_INSTANCE = 'D8D4D268-00A3-4C62-8F91-634B89C1E315',  
    ADDRESS = 'TCP://www.Adventure-Works.com:1234' ;  
```  
  
### <a name="b-creating-a-tcpip-route-by-using-a-netbios-name"></a>B. 使用 NetBIOS 名稱來建立 TCP/IP 路由  
 下列範例會建立服務 `//Adventure-Works.com/Expenses` 的路由。 這個路由指定送往這項服務的訊息要通過 TCP 而到達 NetBIOS 名稱 `1234` 所識別之主機的通訊埠 `SERVER02`。 在到達時，目標 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將訊息傳遞給唯一識別碼 `D8D4D268-00A3-4C62-8F91-634B89C1E315` 所識別的資料庫執行個體。  
  
```sql  
CREATE ROUTE ExpenseRoute  
    WITH   
    SERVICE_NAME = '//Adventure-Works.com/Expenses',  
    BROKER_INSTANCE = 'D8D4D268-00A3-4C62-8F91-634B89C1E315',  
    ADDRESS = 'TCP://SERVER02:1234' ;  
```  
  
### <a name="c-creating-a-tcpip-route-by-using-an-ip-address"></a>C. 使用 IP 位址來建立 TCP/IP 路由  
 下列範例會建立服務 `//Adventure-Works.com/Expenses` 的路由。 這個路由指定送往這項服務的訊息要通過 TCP 而到達在 IP 位址 `1234` 之主機的通訊埠 `192.168.10.2`。 在到達時，目標 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將訊息傳遞給唯一識別碼 `D8D4D268-00A3-4C62-8F91-634B89C1E315` 所識別的 Broker 執行個體。  
  
```sql  
CREATE ROUTE ExpenseRoute  
    WITH  
    SERVICE_NAME = '//Adventure-Works.com/Expenses',  
    BROKER_INSTANCE = 'D8D4D268-00A3-4C62-8F91-634B89C1E315',  
    ADDRESS = 'TCP://192.168.10.2:1234' ;  
```  
  
### <a name="d-creating-a-route-to-a-forwarding-broker"></a>D. 建立通往轉送 Broker 的路由  
 下列範例會建立通往伺服器 `dispatch.Adventure-Works.com` 之轉送 Broker 的路由。 由於既未指定服務名稱，也未指定 Broker 執行個體識別碼，因此，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將這個路由用在未定義任何其他路由的服務上。  
  
```sql  
CREATE ROUTE ExpenseRoute  
    WITH  
    ADDRESS = 'TCP://dispatch.Adventure-Works.com' ;   
```  
  
### <a name="e-creating-a-route-to-a-local-service"></a>E. 建立通往本機服務的路由  
 下列範例會建立通往與路由位於相同執行個體之服務 `//Adventure-Works.com/LogRequests` 的路由。  
  
```sql  
CREATE ROUTE LogRequests  
    WITH  
    SERVICE_NAME = '//Adventure-Works.com/LogRequests',  
    ADDRESS = 'LOCAL' ;  
```  
  
### <a name="f-creating-a-route-with-a-specified-lifetime"></a>F. 建立含指定存留期間的路由  
 下列範例會建立服務 `//Adventure-Works.com/Expenses` 的路由。 路由的存留期間是 `259200` 秒，相當於 72 小時。  
  
```sql  
CREATE ROUTE ExpenseRoute  
    WITH  
    SERVICE_NAME = '//Adventure-Works.com/Expenses',  
    LIFETIME = 259200,  
    ADDRESS = 'TCP://services.Adventure-Works.com:1234' ;  
```  
  
### <a name="g-creating-a-route-to-a-mirrored-database"></a>G. 建立通往鏡像資料庫的路由  
 下列範例會建立服務 `//Adventure-Works.com/Expenses` 的路由。 服務在鏡像的資料庫中。 其中一個鏡像資料庫所在的位址是 `services.Adventure-Works.com:1234`，另一個資料庫所在的位址是 `services-mirror.Adventure-Works.com:1234`。  
  
```sql  
CREATE ROUTE ExpenseRoute  
    WITH  
    SERVICE_NAME = '//Adventure-Works.com/Expenses',  
    BROKER_INSTANCE = '69fcc80c-2239-4700-8437-1001ecddf933',  
    ADDRESS = 'TCP://services.Adventure-Works.com:1234',   
    MIRROR_ADDRESS = 'TCP://services-mirror.Adventure-Works.com:1234' ;  
```  
  
### <a name="h-creating-a-route-that-uses-the-service-name-for-routing"></a>H. 建立使用服務名稱來進行傳遞的路由  
 下列範例會建立一個路由，利用服務名稱來判斷訊息所要送往的網路位址。 請注意，在網路位址中指定 `'TRANSPORT'` 的路由，符合的優先權低於其他路由。  
  
```sql  
CREATE ROUTE TransportRoute  
    WITH ADDRESS = 'TRANSPORT' ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER ROUTE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-route-transact-sql.md)   
 [DROP ROUTE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-route-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
  
