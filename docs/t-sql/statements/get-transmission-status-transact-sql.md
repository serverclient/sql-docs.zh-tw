---
description: GET_TRANSMISSION_STATUS (Transact-SQL)
title: GET_TRANSMISSION_STATUS (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/26/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- STATUS_TSQL
- TRANSMISSION
- TRANSMISSION_TSQL
- GET_TRANSMISSION_STATUS
- STATUS
- GET_TRANSMISSION_STATUS_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- conversations [Service Broker], transmission status
- Service Broker errors, transmission status
- transmission status information
- status information [SQL Server], conversations
- GET_TRANSMISSION_STATUS statement
ms.assetid: 621805d5-49ed-4764-b3cb-2ae4a3bf797e
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f5a59676494b664e11b3dbd0ad86a2c9d4d9f434
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091838"
---
# <a name="get_transmission_status-transact-sql"></a>GET_TRANSMISSION_STATUS (Transact-SQL)
[!INCLUDE [SQL Server - ASDBMI](../../includes/applies-to-version/sql-asdbmi.md)]

  傳回交談一方上次傳輸的狀態。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
GET_TRANSMISSION_STATUS ( conversation_handle )  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *conversation_id*  
 這是交談的交談控制代碼。 此參數類型為 **uniqueidentifier**。  
  
## <a name="return-types"></a>傳回型別  
 **nchar**  
  
## <a name="remarks"></a>備註  
 傳回一個字串，描述指定交談的上次嘗試傳輸狀態。 如果上次嘗試傳輸成功、未嘗試進行任何傳輸，或 *conversation_handle* 不存在，則傳回空字串。  
  
 這個函數所傳回的資訊，與管理檢視 sys.transmission_queue 的 last_transmission_error 資料行所顯示的資訊一樣。 不過，這個函數可以用來尋找目前傳輸佇列中沒有訊息之交談的傳輸狀態。  
  
> [!NOTE]  
>  GET_TRANSMISSION_STATUS 並未針對目前執行個體中沒有交談端點的訊息提供資訊。 也就是說，要轉送的訊息，並沒有可用的訊息。  
  
## <a name="examples"></a>範例  
 下列範例會報告交談控制代碼為 `58ef1d2d-c405-42eb-a762-23ff320bddf0` 之交談的傳輸狀態。  
  
```sql  
SELECT Status =  
    GET_TRANSMISSION_STATUS('58ef1d2d-c405-42eb-a762-23ff320bddf0') ;  
```  
  
 範例結果集如下 (行的長度經過編輯)：  
  
 ```
 Status  
 ------------------------------- 
 The Service Broker protocol transport is disabled or not configured.
 ```  
  
 在這種情況下，並未設定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 來允許 [!INCLUDE[ssSB](../../includes/sssb-md.md)] 透過網路通訊。  
  
## <a name="see-also"></a>另請參閱  
 [sys.conversation_endpoints &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-conversation-endpoints-transact-sql.md)   
 [sys.transmission_queue &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/sys-transmission-queue-transact-sql.md)  
  
  
