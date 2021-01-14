---
description: sp_dbcmptlevel (Transact-SQL)
title: sp_dbcmptlevel (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sp_dbcmptlevel
- sp_dbcmptlevel_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_dbcmptlevel
ms.assetid: 508c686d-2bd4-41ba-8602-48ebca266659
author: markingmyname
ms.author: maghan
ms.openlocfilehash: fbf4875ec03cc961e696ae5f39bd0e29abccb4e9
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98168331"
---
# <a name="sp_dbcmptlevel-transact-sql"></a>sp_dbcmptlevel (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  設定要相容於指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本的資料庫行為。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)] 請改用 [ALTER DATABASE 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sp_dbcmptlevel [ [ @dbname = ] name ]   
    [ , [ @new_cmptlevel = ] version ]  
```  
  
## <a name="arguments"></a>引數  
`[ @dbname = ] name` 這是要變更相容性層級的資料庫名稱。 資料庫名稱必須符合識別碼的規則。 *名稱* 是 **sysname**，預設值是 Null。  
  
`[ @new_cmptlevel = ] version`[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]這是要使資料庫相容的版本。 *版本* 是 **Tinyint**，預設值是 Null。 此值必須是下列其中之一：  
  
 **90** = [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]  
  
 **100** = [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]  
  
 **110** = [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)]  
  
 **120** = [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)]  
  
 **130** = [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]  
  
## <a name="return-code-values"></a>傳回碼值  
 0 (成功) 或 1 (失敗)  
  
## <a name="result-sets"></a>結果集  
 如果未指定任何參數，或未指定 *name* 參數， **sp_dbcmptlevel** 會傳回錯誤。  
  
 如果指定的 *名稱* 沒有 *version*，會傳回 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 訊息，顯示指定資料庫目前的相容性層級。  
  
## <a name="remarks"></a>備註  
 如需相容性層級的說明，請參閱 [&#40;transact-sql&#41;的 ALTER DATABASE 相容性層級 ](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。  
  
## <a name="permissions"></a>權限  
 只有資料庫擁有者、 **系統管理員（sysadmin** ）固定伺服器角色的成員，以及 **db_owner** 固定資料庫角色 (如果您要變更目前的資料庫) 可以執行此程式。  
  
## <a name="see-also"></a>另請參閱  
 [&#40;Transact-sql&#41;的資料庫引擎預存程式 ](../../relational-databases/system-stored-procedures/database-engine-stored-procedures-transact-sql.md)   
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [保留關鍵字 &#40;Transact-SQL&#41;](../../t-sql/language-elements/reserved-keywords-transact-sql.md)   
 [系統預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
  
