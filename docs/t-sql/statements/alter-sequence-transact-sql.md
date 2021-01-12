---
description: ALTER SEQUENCE (Transact-SQL)
title: ALTER SEQUENCE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/08/2015
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_SEQUENCE_TSQL
- ALTER SEQUENCE
dev_langs:
- TSQL
helpviewer_keywords:
- sequence number object, altering
- ALTER SEQUENCE statement
ms.assetid: decc0760-029e-4baf-96c9-4a64073df1c2
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: c32f4e9a6a020df9f7be879eb6e4327bbb9d51f2
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094669"
---
# <a name="alter-sequence-transact-sql"></a>ALTER SEQUENCE (Transact-SQL)
[!INCLUDE [SQL Server Azure SQL Database ](../../includes/applies-to-version/sql-asdb.md)]

  修改現有順序物件的引數。 如果順序是以 **CACHE** 選項建立，改變順序時會重新建立快取。  
  
 順序物件是使用 [CREATE SEQUENCE](../../t-sql/statements/create-sequence-transact-sql.md) 陳述式建立的。 順序是整數值，可以是任何傳回整數的資料類型。 資料類型無法透過 ALTER SEQUENCE 陳述式來變更。 若要變更資料類型，請卸除並重新建立順序物件。  
  
 順序是使用者定義的結構描述繫結物件，該物件會根據規格產生數值序列。 藉由呼叫 NEXT VALUE FOR 函數，從順序產生新值。 您可以使用 **sp_sequence_get_range** 一次取得多個序號。 如需使用 CREATE SEQUENCE、**sp_sequence_get_range** 和 NEXT VALUE FOR 函式的詳細資料和案例，請參閱 [序號](../../relational-databases/sequence-numbers/sequence-numbers.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER SEQUENCE [schema_name. ] sequence_name  
    [ RESTART [ WITH <constant> ] ]  
    [ INCREMENT BY <constant> ]  
    [ { MINVALUE <constant> } | { NO MINVALUE } ]  
    [ { MAXVALUE <constant> } | { NO MAXVALUE } ]  
    [ CYCLE | { NO CYCLE } ]  
    [ { CACHE [ <constant> ] } | { NO CACHE } ]  
    [ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *sequence_name*  
 指定資料庫中順序的唯一識別名稱。 類型是 **sysname**。  
  
 RESTART [ WITH \<constant> ]  
 順序物件會傳回的下一個值。 如果提供的話，RESTART WITH 值必須是小於或等於順序物件最大值，而且大於或等於最小值的整數。 如果省略 WITH 值，順序編號會依據原始 CREATE SEQUENCE 選項重新啟動。  
  
 INCREMENT BY \<constant>  
 每次呼叫 NEXT VALUE FOR 函式時，用來遞增順序物件基底值的值 (如果是負數則遞減)。 如果增量是負值，則會遞減順序物件，否則會遞增。 增量不能為 0。  
  
 [ MINVALUE \<constant> | NO MINVALUE ]  
 指定順序物件的界限。 如果指定 NO MINVALUE，則使用順序資料類型的最小可能值。  
  
 [ MAXVALUE \<constant> | NO MAXVALUE  
 指定順序物件的界限。 如果指定 NO MAXVALUE，則使用順序資料類型的最大可能值。  
  
 [ CYCLE | NO CYCLE ]  
 這個屬性指定當超出其最小值或最大值時，順序物件應該從最小值 (或是遞減順序物件的最大值) 重新啟動，還是擲回例外狀況。  
  
> [!NOTE]  
>  在循環之後，下一個值是順序的最小值或最大值，而不是 START VALUE。  
  
 [ CACHE [\<constant> ] | NO CACHE ]  
 藉由減少產生的值保存至系統資料表時所需的 IO 數目，對使用順序物件的應用程式提升效能。  
  
 如需快取行為的詳細資訊，請參閱 [CREATE SEQUENCE &#40;Transact-SQL&#41;](../../t-sql/statements/create-sequence-transact-sql.md)。  
  
## <a name="remarks"></a>備註  
 如需如何建立順序與管理順序快取的資訊，請參閱 [CREATE SEQUENCE &#40;Transact-SQL&#41;](../../t-sql/statements/create-sequence-transact-sql.md)。  
  
 遞增順序的 MINVALUE 和遞減順序的 MAXVALUE 值不可改變成不允許順序的 START WITH 值。 若要將遞增順序的 MINVALUE 變更為大於 START WITH 值的數字或將遞減順序的 MAXVALUE 變更為小於 START WITH 的數字，請在最小值和最大值範圍內的所需落點包含 RESTART WITH 引數來重新啟動順序。  
  
## <a name="metadata"></a>中繼資料  
 如需有關順序的詳細資訊，請查詢 [sys.sequences](../../relational-databases/system-catalog-views/sys-sequences-transact-sql.md)。  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要順序的 **ALTER** 權限，或結構描述的 **ALTER** 權限。 若要授與順序的 **ALTER** 權限，請使用下列格式的 **ALTER ON OBJECT**：  
  
```sql  
GRANT ALTER ON OBJECT::Test.TinySeq TO [AdventureWorks\Larry]  
```  
  
 順序物件的擁有權可透過 **ALTER AUTHORIZATION** 陳述式來轉移。  
  
### <a name="audit"></a>稽核  
 若要稽核 **ALTER SEQUENCE**，請監視 **SCHEMA_OBJECT_CHANGE_GROUP**。  
  
## <a name="examples"></a>範例  
 如需建立順序和使用 **NEXT VALUE FOR** 函式產生序號的範例，請參閱 [序號](../../relational-databases/sequence-numbers/sequence-numbers.md)。  
  
### <a name="a-altering-a-sequence"></a>A. 改變順序  
 下列範例會建立名為 Test 的結構描述以及名為 TestSeq 的順序，其使用 **int** 資料類型，且範圍介於 100 到 200 之間。 順序開頭為 125，而且每次產生數字時會遞增 25。 因為順序設定為循環，所以當值超過最大值 200 時，順序會從最小值 100 重新啟動。  
  
```sql  
CREATE SCHEMA Test ;  
GO  
  
CREATE SEQUENCE Test.TestSeq  
    AS int   
    START WITH 125  
    INCREMENT BY 25  
    MINVALUE 100  
    MAXVALUE 200  
    CYCLE  
    CACHE 3  
;  
GO  
```  
  
 下列範例會將 TestSeq 順序的範圍改成介於 50 到 200 之間。 順序會從 100 重新啟動編號數列，而且每次產生數字時遞增 50。  
  
```sql  
ALTER SEQUENCE Test. TestSeq  
    RESTART WITH 100  
    INCREMENT BY 50  
    MINVALUE 50  
    MAXVALUE 200  
    NO CYCLE  
    NO CACHE  
;  
GO  
```  
  
 由於順序不會循環，因此當順序超過 200 時，**NEXT VALUE FOR** 函式會造成錯誤。  
  
### <a name="b-restarting-a-sequence"></a>B. 重新啟動順序  
 下列範例會建立名稱為 CountBy1 的順序。 順序會使用預設值。  
  
```sql  
CREATE SEQUENCE Test.CountBy1 ;  
```  
  
 為了產生順序值，擁有者接著執行下列陳述式：  
  
```sql  
SELECT NEXT VALUE FOR Test.CountBy1  
```  
  
 傳回值 -9,223,372,036,854,775,808 是 **bigint** 資料類型的最小可能值。 擁有者發現自己需要的是開頭為 1 的順序，但在建立順序時未指示 **START WITH** 子句。 為了更正這個錯誤，擁有者執行下列陳述式。  
  
```sql  
ALTER SEQUENCE Test.CountBy1 RESTART WITH 1 ;  
```  
  
 擁有者接著重新執行下列陳述式產生序號。  
  
```sql  
SELECT NEXT VALUE FOR Test.CountBy1;  
```  
  
 數字現在為 1，如預期。  
  
 CountBy1 順序是使用 NO CYCLE 預設值建立的，所以它在產生 9,223,372,036,854,775,807 數字之後會停止運作。 順序物件的後續呼叫會傳回錯誤 11728。 下列陳述式會將順序物件變更為循環，並設定 20 的快取。  
  
```sql  
ALTER SEQUENCE Test.CountBy1  
    CYCLE  
    CACHE 20 ; 
```  
  
 現在當順序物件達到 9,223,372,036,854,775,807 時，它會循環，而且下一個數字在循環之後將會是資料類型的最小值 -9,223,372,036,854,775,808。  
  
 擁有者發現每次使用 **bigint** 資料類型時都會使用 8 個位元組。 不過，使用 4 個位元組的 **int** 資料類型就已足夠。 但是順序物件的資料類型無法改變。 若要變更為 **int** 資料類型，擁有者必須卸除順序物件，並重新建立具有適當資料類型的物件。  
  
## <a name="see-also"></a>另請參閱  
 [CREATE SEQUENCE &#40;Transact-SQL&#41;](../../t-sql/statements/create-sequence-transact-sql.md)   
 [DROP SEQUENCE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-sequence-transact-sql.md)   
 [NEXT VALUE FOR &#40;Transact-SQL&#41;](../../t-sql/functions/next-value-for-transact-sql.md)   
 [序號](../../relational-databases/sequence-numbers/sequence-numbers.md)   
 [sp_sequence_get_range &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-sequence-get-range-transact-sql.md)  
  
  
