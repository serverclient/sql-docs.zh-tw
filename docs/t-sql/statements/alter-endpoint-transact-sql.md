---
description: ALTER ENDPOINT (Transact-SQL)
title: ALTER ENDPOINT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER ENDPOINT
- ALTER_ENDPOINT_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- ALTER ENDPOINT statement
- modifying endpoints
- endpoints [SQL Server], modifying
ms.assetid: 70f35566-30cf-47c6-8394-dfe5d71629d3
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 361f91c88f597b18324d56ac3d331899bd73881c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099548"
---
# <a name="alter-endpoint-transact-sql"></a>ALTER ENDPOINT (Transact-SQL)
[!INCLUDE[sqlserver](../../includes/applies-to-version/sqlserver.md)]

  以下列方法修改現有端點：  
  
-   在現有端點加入新的方法。  
  
-   從端點修改或卸除現有的方法。  
  
-   變更端點的屬性。  
  
> [!NOTE]  
>  這個主題描述 ALTER ENDPOINT 特定的語法和引數。 如需 CREATE ENDPOINT 和 ALTER ENDPOINT 通用的引數之描述，請參閱 [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/create-endpoint-transact-sql.md)。  
  
 從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始會將原生 XML Web Services (SOAP/HTTP 端點) 移除。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
ALTER ENDPOINT endPointName [ AUTHORIZATION login ]  
[ STATE = { STARTED | STOPPED | DISABLED } ]  
[ AS { TCP } ( <protocol_specific_items> ) ]  
[ FOR { TSQL | SERVICE_BROKER | DATABASE_MIRRORING } (  
   <language_specific_items>  
        ) ]  
  
<AS TCP_protocol_specific_arguments> ::=  
AS TCP (  
  LISTENER_PORT = listenerPort  
  [ [ , ] LISTENER_IP = ALL | ( 4-part-ip ) | ( "ip_address_v6" ) ]  
)  
<FOR SERVICE_BROKER_language_specific_arguments> ::=  
FOR SERVICE_BROKER (  
   [ AUTHENTICATION = {   
      WINDOWS [ { NTLM | KERBEROS | NEGOTIATE } ]  
      | CERTIFICATE certificate_name   
      | WINDOWS [ { NTLM | KERBEROS | NEGOTIATE } ] CERTIFICATE certificate_name   
      | CERTIFICATE certificate_name WINDOWS [ { NTLM | KERBEROS | NEGOTIATE } ]   
    } ]  
   [ , ENCRYPTION = { DISABLED   
       |   
         {{SUPPORTED | REQUIRED }   
       [ ALGORITHM { RC4 | AES | AES RC4 | RC4 AES } ] }   
   ]  
  
  [ , MESSAGE_FORWARDING = {ENABLED | DISABLED} ]  
  [ , MESSAGE_FORWARD_SIZE = forwardSize  
)  
  
<FOR DATABASE_MIRRORING_language_specific_arguments> ::=  
FOR DATABASE_MIRRORING (  
   [ AUTHENTICATION = {   
      WINDOWS [ { NTLM | KERBEROS | NEGOTIATE } ]  
      | CERTIFICATE certificate_name   
      | WINDOWS [ { NTLM | KERBEROS | NEGOTIATE } ] CERTIFICATE certificate_name   
      | CERTIFICATE certificate_name WINDOWS [ { NTLM | KERBEROS | NEGOTIATE } ]   
    } ]  
   [ , ENCRYPTION = { DISABLED   
       |   
         {{SUPPORTED | REQUIRED }   
       [ ALGORITHM { RC4 | AES | AES RC4 | RC4 AES } ] }   
    ]   
   [ , ] ROLE = { WITNESS | PARTNER | ALL }  
)  
  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
  
> [!NOTE]  
>  以下是 ALTER ENDPOINT 特定的引數。 如需其餘引數的描述，請參閱 [CREATE ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/create-endpoint-transact-sql.md)。  
  
 **AS** { **TCP** }  
 您不能使用 **ALTER ENDPOINT** 來變更傳輸通訊協定。  
  
 **AUTHORIZATION** *login*  
 **ALTER ENDPOINT** 中無法使用 **AUTHORIZATION** 選項。 只有在建立端點時，才可指派擁有權。  
  
 **FOR** { **TSQL** | **SERVICE_BROKER** | **DATABASE_MIRRORING** }  
 您不能使用 **ALTER ENDPOINT** 來變更承載類型。  
  
## <a name="remarks"></a>備註  
 當您使用 ALTER ENDPOINT 時，只要指定您要更新的參數即可。 現有端點的所有屬性，都將保持不變，除非您明確變更它們。  
  
 ENDPOINT DDL 陳述式不能在使用者交易內執行。  
  
 如需選擇加密演算法來和端點搭配使用的資訊，請參閱[選擇加密演算法](../../relational-databases/security/encryption/choose-an-encryption-algorithm.md)。  
  
> [!NOTE]  
>  只有 RC4 演算法支援回溯相容性。 只有在資料庫相容性層級為 90 或 100 時，才能使用 RC4 或 RC4_128 加密新資料 (不建議使用)。請改用較新的演算法，例如其中一個 AES 演算法。 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 和更新版本中使用 RC4 或 RC4_128 加密的資料，可以在任何相容性層級進行解密。  
>   
>  RC4 是相對的弱式演算法，而 AES 則是相對的強式演算法。 但是 AES 的速度顯著較 RC4 的速度慢。 如果您認為安全性比速度更重要，建議您使用 AES。  
  
## <a name="permissions"></a>權限  
 使用者必須是系統管理員 (**sysadmin**) 固定伺服器角色的成員、端點擁有者，或者已被授與 ALTER ANY ENDPOINT 權限。  
  
 若要變更現有端點的擁有權，必須使用 ALTER AUTHORIZATION 陳述式。 如需詳細資訊，請參閱 [ALTER AUTHORIZATION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-authorization-transact-sql.md)。  
  
 如需詳細資訊，請參閱 [GRANT 端點權限 &#40;Transact-SQL&#41;](../../t-sql/statements/grant-endpoint-permissions-transact-sql.md)。  
  
## <a name="see-also"></a>另請參閱  
 [DROP ENDPOINT &#40;Transact-SQL&#41;](../../t-sql/statements/drop-endpoint-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)  
  
  
