---
description: NEWSEQUENTIALID (Transact-SQL)
title: NEWSEQUENTIALID (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/08/2015
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- NEWSEQUENTIALID
- NEWSEQUENTIALID_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- NEWSEQUENTIALID function
- GUIDs [SQL Server]
ms.assetid: e06d2cab-f1ff-42f1-8550-6aaec57be36f
author: julieMSFT
ms.author: jrasnick
ms.openlocfilehash: 7bed17ea5142acb2e9e2168491046b13d5868c44
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98597313"
---
# <a name="newsequentialid-transact-sql"></a>NEWSEQUENTIALID (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  建立一個 GUID，該 GUID 會大於這個函數先前在指定的電腦啟動 Windows 後所產生的任何 GUID。 重新啟動 Windows 後，GUID 可以從較低的範圍再次啟動，但仍是全域唯一的。 將 GUID 資料行當做資料列識別碼使用時，使用 NEWSEQUENTIALID 可能比使用 NEWID 函數更快。 這是因為 NEWID 函數會造成隨機活動，因此會使用較少的快取資料頁面。 使用 NEWSEQUENTIALID 也有助於完全填滿資料與索引頁面。  
  
> [!IMPORTANT]  
>  如果您有隱私權顧慮，請勿使用這個函數。 因為使用者不難猜出下一個產生的 GUID 值，進而存取與該 GUID 相關聯的資料。  
  
 NEWSEQUENTIALID 是 Windows [UuidCreateSequential](/windows/win32/api/rpcdce/nf-rpcdce-uuidcreatesequential) 函式上的一個包裝函式，其[套用了一些隨機位元組](/archive/blogs/dbrowne/how-to-generate-sequential-guids-for-sql-server-in-net)。
  
> [!WARNING]  
>  UuidCreateSequential 函式有硬體相依性。 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 上，當資料庫 (例如自主資料庫) 移動到另一部電腦時，順序值的叢集便可進行開發。 使用 Always On 和在 [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] 上時，若資料庫容錯移轉至不同的電腦，順序值的叢集便可進行開發。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
NEWSEQUENTIALID ( )  
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]


## <a name="return-type"></a>傳回類型  
 **uniqueidentifier**  
  
## <a name="remarks"></a>備註  
 NEWSEQUENTIALID() 只能搭配使用 **uniqueidentifier** 類型之資料表資料行的 DEFAULT 條件約束。 例如：  
  
```sql  
CREATE TABLE myTable (ColumnA uniqueidentifier DEFAULT NEWSEQUENTIALID());   
```  
  
 當 NEWSEQUENTIALID() 用於 DEFAULT 運算式時，不能與其他純量運算子結合。 例如，您不可以執行下列作業：  
  
```sql 
CREATE TABLE myTable (ColumnA uniqueidentifier DEFAULT dbo.myfunction(NEWSEQUENTIALID()));  
```  
  
 在上一個範例中，`myfunction()` 是一個純量使用者自訂的純量函數，可以接受和傳回 `uniqueidentifier` 值。  
  
 NEWSEQUENTIALID 無法於查詢中參考。  
  
 您可以使用 NEWSEQUENTIALID 來產生 GUID，以減少在索引分葉層級的網頁競爭。  
  
 使用 NEWSEQUENTIALID 所產生的 GUID 在該電腦上都是唯一的。 唯有在來源電腦具有網路卡時，使用 NEWSEQUENTIALID 所產生的 GUID 在多部電腦上才是唯一的。  
  
## <a name="see-also"></a>另請參閱  
 [NEWID &#40;Transact-SQL&#41;](../../t-sql/functions/newid-transact-sql.md)   
 [比較運算子 &#40;Transact-SQL&#41;](../../t-sql/language-elements/comparison-operators-transact-sql.md)  
  
