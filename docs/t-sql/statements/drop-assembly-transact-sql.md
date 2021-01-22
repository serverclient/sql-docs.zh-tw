---
description: DROP ASSEMBLY (Transact-SQL)
title: DROP ASSEMBLY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/10/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- DROP ASSEMBLY
- DROP_ASSEMBLY_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- removing assemblies
- DROP ASSEMBLY statement
- deleting assemblies
- assemblies [CLR integration], removing
- dropping assemblies
- WITH NO DEPENDENTS option
ms.assetid: 452d181a-a8e6-44a3-975d-29966d01b18d
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: fcbf21adb6116153b067c0cd28f50e42a8a1a416
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689139"
---
# <a name="drop-assembly-transact-sql"></a>DROP ASSEMBLY (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  從目前資料庫移除組件及其所有相關聯的檔案。 組件是使用 [CREATE ASSEMBLY](../../t-sql/statements/create-assembly-transact-sql.md) 來建立，並且使用 [ALTER ASSEMBLY](../../t-sql/statements/alter-assembly-transact-sql.md) 來修改。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
DROP ASSEMBLY [ IF EXISTS ] assembly_name [ ,...n ]  
[ WITH NO DEPENDENTS ]  
[ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *IF EXISTS*  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))。  
  
 只有在組件已存在時，才有條件地將它卸除。  
  
 *assembly_name*  
 這是您要卸除的組件名稱。  
  
 WITH NO DEPENDENTS   
 如果指定的話，只會卸除 *assembly_name*，而不會卸除該組件參考的任何相依組件。 若未指定，DROP ASSEMBLY 就會卸除 *assembly_name* 和所有的相依組件。  
  
## <a name="remarks"></a>備註  
 卸除一個組件，會從資料庫移除組件及其所有相關聯的檔案 (例如，原始程式碼和偵錯檔案)。  
  
 如果未指定 WITH NO DEPENDENTS，DROP ASSEMBLY 就會卸除 *assembly_name* 和所有的相依組件。 如果卸除任何相依組件失敗，DROP ASSEMBLY 便會傳回錯誤。  
  
 如果組件是由資料庫中的另一個組件所參考，或者如果它是由目前資料庫中的 Common Language Runtime (CLR) 函數、程序、觸發程序、使用者自訂類型或彙總所使用，DROP ASSEMBLY 就會傳回錯誤。  
  
 DROP ASSEMBLY 不會影響任何參考目前正在執行之組件的程式碼。 不過，DROP ASSEMBLY 執行之後，嘗試叫用該組件程式碼就會失敗。  
  
## <a name="permissions"></a>權限  
 需要組件的擁有權或 CONTROL 權限。  
  
## <a name="examples"></a>範例  
 下列範例假設組件 `HelloWorld` 已經建立在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體中。  
  
```sql  
DROP ASSEMBLY Helloworld ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE ASSEMBLY &#40;Transact-SQL&#41;](../../t-sql/statements/create-assembly-transact-sql.md)   
 [ALTER ASSEMBLY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-assembly-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [取得組件的相關資訊](../../relational-databases/clr-integration/assemblies-getting-information.md)  
