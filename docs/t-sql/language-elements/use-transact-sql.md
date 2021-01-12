---
description: USE (Transact-SQL)
title: USE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 11/28/2016
ms.prod: sql
ms.prod_service: pdw, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- USE_TSQL
- USE
dev_langs:
- TSQL
helpviewer_keywords:
- USE statement
- database context [SQL Server]
- context changes [SQL Server]
- modifying database context
ms.assetid: c05acac8-c063-4770-8e36-d7f71d500b10
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: f31381a2c43d02af5b3c19478fcf03e7bcc608d6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98095886"
---
# <a name="use-transact-sql"></a>USE (Transact-SQL)
[!INCLUDE [sql-asdbmi-pdw](../../includes/applies-to-version/sql-asdbmi-pdw.md)]

  在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中將資料庫內容變更為指定的資料庫或資料庫快照集。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
USE { database_name }   
[;]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *database_name*  
 這是使用者內容所要切換的資料庫或資料庫快照集的名稱。 資料庫和資料庫快照集名稱必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
  
 在 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 中，資料庫參數只能參考目前資料庫。 如果提供目前資料庫以外的資料庫，則 `USE` 陳述式不會在資料庫之間切換，並且會傳回錯誤碼 40508。 若要變更資料庫，您必須直接連接到資料庫。 USE 陳述式在此頁面頂端標示為不適用於 SQL Database，因為即使您可在批次中加入 `USE` 陳述式，它也不會執行任何動作。
  
## <a name="remarks"></a>備註  
 當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入連接到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時，會自動連接到預設的資料庫並取得資料庫使用者的安全性內容。 如果尚未針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入建立資料庫使用者，登入就會以 guest 的身分連接。 如果資料庫使用者沒有資料庫的 CONNECT 權限，USE 陳述式將會失敗。 如果尚未為登入指派預設的資料庫，則其預設資料庫將會設定為 master。  
  
 USE 執行於編譯和執行階段，且會立即生效。 因此，會在指定的資料庫中，執行批次中出現在 USE 陳述式之後的陳述式。  
  
## <a name="permissions"></a>權限  
 需要目標資料庫的 CONNECT 權限。  
  
## <a name="examples"></a>範例  
 下列範例會將資料庫內容變更為 `AdventureWorks2012` 資料庫。  
  
```sql  
USE AdventureWorks2012;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE LOGIN &#40;Transact-SQL&#41;](../../t-sql/statements/create-login-transact-sql.md)   
 [CREATE USER &#40;Transact-SQL&#41;](../../t-sql/statements/create-user-transact-sql.md)   
 [主體 &#40;Database Engine&#41;](../../relational-databases/security/authentication-access/principals-database-engine.md)   
 [CREATE DATABASE &#40;SQL Server Transact-SQL&#41;](../statements/create-database-transact-sql.md)   
 [DROP DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-database-transact-sql.md)   
 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)  
  
