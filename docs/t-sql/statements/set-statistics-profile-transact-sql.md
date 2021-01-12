---
description: SET STATISTICS PROFILE (Transact-SQL)
title: SET STATISTICS PROFILE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- PROFILE
- SET_STATISTICS_PROFILE_TSQL
- PROFILE_TSQL
- SET STATISTICS PROFILE
dev_langs:
- TSQL
helpviewer_keywords:
- profiles [SQL Server], displaying
- statements [SQL Server], profile information
- SET STATISTICS PROFILE statement
- STATISTICS PROFILE option
- statistical information [SQL Server], profiles
ms.assetid: c635e262-35fa-421a-aa6f-a1c30f351647
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 87b6bf68704f65319faebd2c0cdf947329b6118d
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98079452"
---
# <a name="set-statistics-profile-transact-sql"></a>SET STATISTICS PROFILE (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  顯示陳述式的設定檔資訊。 STATISTICS PROFILE 適用於隨選查詢、檢視和預存程序。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
SET STATISTICS PROFILE { ON | OFF }  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="remarks"></a>備註
 當 STATISTICS PROFILE 是 ON 時，每個執行的查詢都會傳回它的正規結果集，後面再接著顯示查詢執行設定檔的其他結果集。  
  
 其他結果集包含查詢的 SHOWPLAN_ALL 資料行及這些其他資料行。  
  
|資料行名稱|描述|  
|-----------------|-----------------|  
|**資料列**|每個運算子所產生的實際資料列數|  
|**Executes**|運算子已執行的次數|  
  
## <a name="permissions"></a>權限  
 若要使用 SET STATISTICS PROFILE 和檢視輸出，使用者必須有下列許可權：  
  
-   執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的適當許可權。  
  
-   包含 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式所參考的物件之所有資料庫的 SHOWPLAN 權限。  
  
 如果 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式不會產生 STATISTICS PROFILE 結果集，就只需要執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的適當權限。 如果 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式會產生 STATISTICS PROFILE 結果集，檢查 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式執行權限和 SHOWPLAN 權限都必須成功，否則，便會中止執行 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式，且不會產生任何顯示計畫資訊。  
  
## <a name="see-also"></a>另請參閱  
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)   
 [SET SHOWPLAN_ALL &#40;Transact-SQL&#41;](../../t-sql/statements/set-showplan-all-transact-sql.md)   
 [SET STATISTICS TIME &#40;Transact-SQL&#41;](../../t-sql/statements/set-statistics-time-transact-sql.md)   
 [SET STATISTICS IO &#40;Transact-SQL&#41;](../../t-sql/statements/set-statistics-io-transact-sql.md)  
  
  
