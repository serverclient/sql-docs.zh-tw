---
description: ALTER ROUTE (Transact-SQL)
title: ALTER ROUTE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/30/2018
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_ROUTE_TSQL
- ALTER ROUTE
dev_langs:
- TSQL
helpviewer_keywords:
- ALTER ROUTE statement
- dropping routes
- modifying routes
- removing routes
- routes [Service Broker], modifying
ms.assetid: 8dfb7b16-3dac-4e1e-8c97-adf2aad07830
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-mi-current||>=sql-server-2016||>=sql-server-linux-2017
ms.openlocfilehash: 504f1eed0f50a711a671301b10001b44d6097330
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98092033"
---
# <a name="alter-route-transact-sql"></a>ALTER ROUTE (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

  在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中修改現有路由的路由資訊。 

  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER ROUTE route_name  
WITH    
  [ SERVICE_NAME = 'service_name' [ , ] ]  
  [ BROKER_INSTANCE = 'broker_instance' [ , ] ]  
  [ LIFETIME = route_lifetime [ , ] ]  
  [ ADDRESS =  'next_hop_address' [ , ] ]  
  [ MIRROR_ADDRESS = 'next_hop_mirror_address' ]  
[ ; ]  
  
```  
  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *route_name*  
 這是要變更的路由名稱。 您不可指定伺服器、資料庫和結構描述名稱。  
  
 WITH  
 導入定義所改變之路由的子句。  
  
 SERVICE_NAME **='** _service\_name_ **'**  
 指定這個路由所指向的遠端服務名稱。 *service_name* 必須與遠端服務所使用的名稱完全相符。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 會使用逐位元組的比較方式來比對 *service_name*。 換言之，這項比較會區分大小寫，且不會考慮目前的定序。 服務名稱是 **'SQL/ServiceBroker/BrokerConfiguration'** 的路由，是指向 Broker Configuration Notice 服務的路由。 指向這項服務的路由不能指定 Broker 執行個體。  
  
 如果省略 SERVICE_NAME 子句，路由的服務名稱就會維持不變。  
  
 BROKER_INSTANCE **='** _broker\_instance_ **'**  
 指定主控目標服務的資料庫。 *broker_instance* 參數必須是遠端資料庫的 Broker 執行個體識別碼，您可以在所選資料庫中執行下列查詢來取得這個識別碼：  
  
```sql  
SELECT service_broker_guid  
FROM sys.databases  
WHERE database_id = DB_ID();  
```  
  
 當省略 BROKER_INSTANCE 子句時，路由的 Broker 執行個體就會維持不變。  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
 LIFETIME **=** _route\_lifetime_  
 指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 將路由保留在路由表中的時間量 (以秒為單位)。 在存留期間結束時，路由會到期，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 在選擇新交談的路由時，不會再考慮這個路由。 如果省略這個子句，路由的存留期間會維持不變。  
  
 ADDRESS **='** _next\_hop\_address_'  

 針對 Azure SQL 受控執行個體，`ADDRESS` 必須為本機。

 指定這個路由的網路位址。 *next_hop_address* 以下列格式指定 TCP/IP 位址：  
  
 **TCP://** { *dns_name* | *netbios_name* |*ip_address* } **:** *port_number*  
  
 指定的 *port_number* 必須符合在指定電腦的 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 執行個體之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 端點的連接埠號碼。 這可以在選取的資料庫中執行下列查詢來取得：  
  
```sql  
SELECT tcpe.port  
FROM sys.tcp_endpoints AS tcpe  
INNER JOIN sys.service_broker_endpoints AS ssbe  
   ON ssbe.endpoint_id = tcpe.endpoint_id  
WHERE ssbe.name = N'MyServiceBrokerEndpoint';  
```  
  
 當路由在 **next_hop_address** 中指定 *'LOCAL'* 時，訊息會傳遞給在目前 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體內的服務。  
  
 當路由在 **next_hop_address** 中指定 *'TRANSPORT'* 時，會根據服務名稱中的網路位址來決定網路位址。 指定 **'TRANSPORT'** 的路由可以指定服務名稱或 Broker 執行個體。  
  
 當 *next_hop_address* 是資料庫鏡像的主體伺服器時，您也必須指定鏡像伺服器的 MIRROR_ADDRESS。 否則，這個路由不會自動進行容錯移轉，將工作交給鏡像伺服器。  
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
 MIRROR_ADDRESS **='** _next\_hop\_mirror\_address_ **'**  
 指定鏡像組 (其主體伺服器位於 *next_hop_address*) 之鏡像伺服器的網路位址。 *next_hop_mirror_address* 以下列格式指定 TCP/IP 位址：  
  
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
  
> [!NOTE]  
>  自主資料庫無法使用這個選項。  
  
## <a name="remarks"></a>備註  
 儲存路由的路由表是可利用 **sys.routes** 目錄檢視來讀取的中繼資料表。 您只能利用 CREATE ROUTE、ALTER ROUTE 和 DROP ROUTE 陳述式來更新路由表。  
  
 ALTER ROUTE 命令所未指定的子句會維持不變。 因此，您無法變更 (ALTER) 路由來指定路由不逾時、路由符合任何服務名稱，或是路由符合任何 Broker 執行個體。 若要變更路由的這些特性，您必須卸除現有的路由，再以新的資訊建立新的路由。  
  
 當路由在 **next_hop_address** 中指定 *'TRANSPORT'* 時，會根據服務名稱來決定網路位址。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 可以順利處理開頭是網路位址且格式對 *next_hop_address* 有效的服務名稱。 名稱包含有效網路位址的服務會遞送到服務名稱中的網路位址。  
  
 路由表可以包含指定相同服務、網路位址及/或 Broker 執行個體識別碼之任意數目的路由。 在這個情況下，[!INCLUDE[ssSB](../../includes/sssb-md.md)] 會利用在交談所指定的資訊和路由表所指定的資訊之間，設計用來尋找完全相符項目的程序，來選擇路由。  
  
 若要改變服務的 AUTHORIZATION，請使用 ALTER AUTHORIZATION 陳述式。  
  
## <a name="permissions"></a>權限  
 改變路由的權限預設為路由的擁有者、**db_ddladmin** 或 **db_owner** 固定資料庫角色的成員，以及系統管理員 (**sysadmin**) 固定伺服器角色的成員。  
  
## <a name="examples"></a>範例  
  
### <a name="a-changing-the-service-for-a-route"></a>A. 變更路由的服務  
 下列範例會修改 `ExpenseRoute` 路由來指向遠端服務 `//Adventure-Works.com/Expenses`。  
  
```sql  
ALTER ROUTE ExpenseRoute  
   WITH   
     SERVICE_NAME = '//Adventure-Works.com/Expenses';  
```  
  
### <a name="b-changing-the-target-database-for-a-route"></a>B. 變更路由的目標資料庫  
 下列範例將 `ExpenseRoute` 路由的目標資料庫變更為唯一識別碼 `D8D4D268-00A3-4C62-8F91-634B89B1E317.` 所識別的資料庫。  
  
```sql  
ALTER ROUTE ExpenseRoute  
   WITH   
     BROKER_INSTANCE = 'D8D4D268-00A3-4C62-8F91-634B89B1E317';  
```  
  
### <a name="c-changing-the-address-for-a-route"></a>C. 變更路由的位址  
 下列範例會將 `ExpenseRoute` 路由的網路位址改成 IP 位址是 `1234` 之主機的 TCP 通訊埠 `10.2.19.72`。  
  
```sql  
ALTER ROUTE ExpenseRoute   
   WITH   
     ADDRESS = 'TCP://10.2.19.72:1234';  
```  
  
### <a name="d-changing-the-database-and-address-for-a-route"></a>D. 變更路由的資料庫和位址  
 下列範例會將 `ExpenseRoute` 路由的網路位址改成 DNS 名稱是 `1234` 之主機的 TCP 通訊埠 `www.Adventure-Works.com`。 它也會將目標資料庫變更為唯一識別碼 `D8D4D268-00A3-4C62-8F91-634B89B1E317` 所識別的資料庫。  
  
```sql  
ALTER ROUTE ExpenseRoute  
   WITH   
     BROKER_INSTANCE = 'D8D4D268-00A3-4C62-8F91-634B89B1E317',  
     ADDRESS = 'TCP://www.Adventure-Works.com:1234';  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE ROUTE &#40;Transact-SQL&#41;](../../t-sql/statements/create-route-transact-sql.md)   
 [DROP ROUTE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-route-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
  
