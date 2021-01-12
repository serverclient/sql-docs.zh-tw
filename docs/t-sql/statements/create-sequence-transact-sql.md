---
description: CREATE SEQUENCE (Transact-SQL)
title: CREATE SEQUENCE (Transact-SQL)
ms.custom: ''
ms.date: 04/11/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SEQUENCE
- CREATE SEQUENCE
- SEQUENCE_TSQL
- CREATE_SEQUENCE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- CREATE SEQUENCE statement
- sequence number object, creating
- sequence object
- number, sequence
ms.assetid: 419f907b-8a72-4d6c-80cb-301df44c24c1
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 57dc922f5be1aac4a3a2fce713894353728a0e1c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096993"
---
# <a name="create-sequence-transact-sql"></a>CREATE SEQUENCE (Transact-SQL)

[!INCLUDE [SQL Server Azure SQL Database ](../../includes/applies-to-version/sql-asdb.md)]

  建立順序物件，並指定其屬性。 序列是使用者定義之結構描述繫結的物件，該物件會根據建立序列所使用的規格產生一連串的數值。 數值序列會在定義的間隔依照遞增或遞減順序來產生，而且在用完時可設定為重新啟動 (循環)。 順序不會與特定資料表產生關聯，與識別欄位不同。 應用程式會參考順序物件，以擷取它的下一個值。 順序與資料表之間的關聯性是由應用程式所控制。 使用者的應用程式可以參考序列物件，並協調跨越多個資料列和資料表的值。  
  
 不同於插入資料列時產生的識別資料行值，應用程式可以藉由呼叫 [NEXT VALUE FOR 函式](../../t-sql/functions/next-value-for-transact-sql.md)取得下一個序號，而不需要插入資料列。 您可以使用 [sp_sequence_get_range](../../relational-databases/system-stored-procedures/sp-sequence-get-range-transact-sql.md) 一次取得多個序號。  
  
 如需使用 **CREATE SEQUENCE** 和 **NEXT VALUE FOR** 函數的相關資訊和案例，請參閱 [序號](../../relational-databases/sequence-numbers/sequence-numbers.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CREATE SEQUENCE [schema_name . ] sequence_name  
    [ AS [ built_in_integer_type | user-defined_integer_type ] ]  
    [ START WITH <constant> ]  
    [ INCREMENT BY <constant> ]  
    [ { MINVALUE [ <constant> ] } | { NO MINVALUE } ]  
    [ { MAXVALUE [ <constant> ] } | { NO MAXVALUE } ]  
    [ CYCLE | { NO CYCLE } ]  
    [ { CACHE [ <constant> ] } | { NO CACHE } ]  
    [ ; ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*sequence_name*  
指定資料庫中順序的唯一識別名稱。 類型是 **sysname**。  
  
[ built_in_integer_type | user-defined_integer_type  
順序可以定義為任何整數類型。 允許使用下列類型。  
  
-   **tinyint** - 範圍為 0 到 255  
-   **smallint** - 範圍為 -32,768 到 32,767  
-   **int** - 範圍為 -2,147,483,648 到 2,147,483,647  
-   **bigint** - 範圍為 -9,223,372,036,854,775,808 到 9,223,372,036,854,775,807  
-   小數位數為 0 的 **decimal** 和 **numeric**。  
-   以其中一個允許類型為基礎的任何使用者定義的資料類型 (別名類型)。  
  
如果未提供資料類型，則會使用 **bigint** 資料類型作為預設值。  
  
START WITH \<constant>  
順序物件傳回的第一個值。 **START** 值必須是小於或等於順序物件的最大值，而且大於或等於最小值。 新順序物件的預設開始值是遞增順序物件的最小值，是遞減順序物件的最大值。  
  
INCREMENT BY \<constant>  
每次呼叫 **NEXT VALUE FOR** 函式時，用來遞增順序物件值的值 (如果是負數則遞減)。 如果增量是負值，則會遞減順序物件，否則會遞增。 增量不能為 0。 新順序物件的預設增量為 1。  
  
[ MINVALUE \<constant> | **NO MINVALUE** ]  
指定順序物件的界限。 新序列物件的預設最小值是序列物件之資料類型的最小值。 如果是 **tinyint** 資料類型，這是零，如果是所有其他資料類型，則為負數。  
  
[ MAXVALUE \<constant> | **NO MAXVALUE**  
指定順序物件的界限。 新序列物件的預設最大值是序列物件之資料類型的最大值。  
  
[ CYCLE | **NO CYCLE** ]  
屬性，指定當超出其最小值或最大值時，順序物件應該從最小值 (或是遞減順序物件的最大值) 重新啟動，還是擲回例外狀況。 新順序物件的預設循環選項是 NO CYCLE。  
  
> [!NOTE]
> 將 SEQUENCE 循環會從最小值或最大值重新開始，而不是從開始值重新開始。  
  
[ **CACHE** [\<constant> ] | NO CACHE ]  
藉由減少產生序號所需的磁碟 IO 數目，對使用順序物件的應用程式提升效能。 預設為 CACHE。  
  
例如，如果所選擇的快取大小為 50，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 並不會保留 50 個個別的快取值。 它只快取目前值和留在快取中的值數目。 這表示，儲存快取所需的記憶體數量永遠是順序物件之資料類型的兩個執行個體。  
  
> [!NOTE]  
> 如果已啟用快取選項但未指定快取大小，Database Engine 將選取大小。 但是使用者不應該依賴此選取來取得一致的結果。 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 可能變更計算快取大小的方法，而不另行通知。  
  
以 **CACHE** 選項建立時，非預期關機 (例如停電) 可能會導致留在快取中的序號遺失。  
  
## <a name="general-remarks"></a>一般備註  
 序號是在目前交易範圍之外產生的。 無論使用序號的交易被認可或回復交易，都會耗用序號。 只有在填完記錄之後才會出現重複驗證。 這會導致在某些情況下，同一個數字會在建立期間用於多筆記錄，但會識別為重複項目。 如果發生這種情況，而且已將其他自動編號值套用至後續記錄，這會導致自動編號值之間出現間距，這是預期的行為。
  
### <a name="cache-management"></a>快取管理  
 為了改善效能，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會預先配置 **CACHE** 引數所指定之序號的數目。  
  
 例如，建立起始值為 1 和快取大小為 15 的新順序。 當需要第一個值時，會從記憶體中提供 1 到 15 的值。 最後一個快取的值 (15) 會寫入至磁碟上的系統資料表。 已使用所有 15 個數字時，下一個要求 (針對數字 16) 將會造成快取重新配置。 新的最後一個快取值 (30) 會寫入至系統資料表。  
  
 如果在您使用 22 個數字之後 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 停止，記憶體中的下一個預定序號 (23) 會寫入至系統資料表，取代先前儲存的數字。  
  
 在 SQL Server 重新啟動，而且需要一個序號之後，會從系統資料表讀取起始數字 (23)。 15 個數字 (23-38) 的快取數量會配置到記憶體，而且下一個非快取數字 (39) 會寫入至系統資料表。  
  
 如果 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 因停電等事件而異常停止，順序會以讀取自系統資料表的數字重新啟動 (39)。 任何配置到記憶體 (但使用者或應用程式從未要求) 的序號都會遺失。 這項功能可能會留下間距，但保證絕不會對同一個順序物件發出相同的值兩次，除非順序物件定義為 **CYCLE**，或以手動方式重新啟動。  
  
 快取是透過追蹤目前值 (最後一個發出的值) 以及留在快取中的值數目，保留在記憶體中。 因此，快取所使用的記憶體數量永遠是順序物件之資料類型的兩個執行個體。  
  
 如果將快取引數設定為 **NO CACHE**，每次使用順序時會將目前的順序值寫入至系統資料表。 這可能會因為增加磁碟的存取而降低效能，但是可以減少非預期間距的機會。 如果透過 **NEXT VALUE FOR** 或 **sp_sequence_get_range** 函式要求數字，但數字未使用或用於未認可的交易，仍然會發生間距。  
  
 當順序物件使用 **CACHE** 選項時，如果您重新啟動順序物件，或改變 **INCREMENT**、**CYCLE**、**MINVALUE**、**MAXVALUE** 或快取大小屬性，則會導致快取在發生變更之前寫入至系統資料表。 然後快取會從目前的值開始重新載入 (也就是不略過任何數字)。 快取大小的變更會立即生效。  
  
 **當快取的值可用時的 CACHE 選項**  
  
 每次要求順序物件產生 **CACHE** 選項的下一個值時，如果記憶體快取中有未使用的值可供順序物件使用，則會發生下列處理序。  
  
1.  計算順序物件的下一個值。  
  
2.  更新記憶體中順序物件的新的目前值。  
  
3.  將計算值傳回給呼叫的陳述式。  
  
**當快取已用盡時的 CACHE 選項**  
  
 每次要求順序物件產生 **CACHE** 選項的下一個值時，如果快取已用盡，則會發生下列處理序：  
  
1.  計算順序物件的下一個值。  
  
2.  計算新快取的最後一個值。  
  
3.  鎖定順序物件的系統資料表資料列，而且將步驟 2 中的計算值 (最後一個值) 寫入至系統資料表。 引發快取用盡 xevent，通知使用者此新持續值。  
  
**NO CACHE 選項**  
  
 每次要求順序物件產生 **NO CACHE** 選項的下一個值時，都會發生下列處理序：  
  
1.  計算順序物件的下一個值。  
  
2.  將順序物件的新的目前值寫入至系統資料表。  
  
3.  將計算值傳回給呼叫的陳述式。  
  
## <a name="metadata"></a>中繼資料  
 如需有關順序的詳細資訊，請查詢 [sys.sequences](../../relational-databases/system-catalog-views/sys-sequences-transact-sql.md)。  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 需要 SCHEMA 的 **CREATE SEQUENCE**、 **ALTER** 或 **CONTROL** 權限。  
  
-   db_owner 和 db_ddladmin 固定資料庫角色的成員可以建立、改變及卸除順序物件。  
  
-   db_owner 和 db_datawriter 固定資料庫角色的成員可藉由要求順序物件產生數字，更新順序物件。  
  
 下列範例會授與 AdventureWorks\Larry 使用者權限，以在 Test 結構描述中建立順序。  
  
```sql  
GRANT CREATE SEQUENCE ON SCHEMA::Test TO [AdventureWorks\Larry]  
```  
  
 順序物件的擁有權可透過 **ALTER AUTHORIZATION** 陳述式來轉移。  
  
 如果順序使用使用者定義的資料類型，順序的建立者必須擁有類型的 REFERENCES 權限。  
  
### <a name="audit"></a>稽核  
 若要稽核 **CREATE SEQUENCE**，請監視 **SCHEMA_OBJECT_CHANGE_GROUP**。  
  
## <a name="examples"></a>範例  
 如需建立順序和使用 **NEXT VALUE FOR** 函式產生序號的範例，請參閱 [序號](../../relational-databases/sequence-numbers/sequence-numbers.md)。  
  
 下列大部分的範例會在名為 Test 的結構描述中建立順序物件。  
  
 若要建立 Test 結構描述，請執行下列陳述式。  
  
```sql  
CREATE SCHEMA Test ;  
GO  
```  
  
### <a name="a-creating-a-sequence-that-increases-by-1"></a>A. 建立以 1 遞增的順序  
 在下列範例中，Thierry 會建立名為 CountBy1 的順序，此順序每次使用時遞增一。  
  
```sql  
CREATE SEQUENCE Test.CountBy1  
    START WITH 1  
    INCREMENT BY 1 ;  
GO  
```  
  
### <a name="b-creating-a-sequence-that-decreases-by-1"></a>B. 建立以 1 遞減的順序  
 下列範例會從 0 開始，並且每次使用時遞減一。  
  
```sql  
CREATE SEQUENCE Test.CountByNeg1  
    START WITH 0  
    INCREMENT BY -1 ;  
GO  
```  
  
### <a name="c-creating-a-sequence-that-increases-by-5"></a>C. 建立以 5 遞增的順序  
 下列範例會建立每次使用時遞增 5 的順序。  
  
```sql  
CREATE SEQUENCE Test.CountBy1  
    START WITH 5  
    INCREMENT BY 5 ;  
GO  
```  
  
### <a name="d-creating-a-sequence-that-starts-with-a-designated-number"></a>D. 建立以指定數字開頭的順序  
 在匯入資料表之後，Thierry 發現使用的最高識別碼值是 24,328。 Thierry 需要產生以 24,329 為起始值的順序。 下列程式碼會建立開頭為 24,329 且遞增量為 1 的順序。  
  
```sql  
CREATE SEQUENCE Test.ID_Seq  
    START WITH 24329  
    INCREMENT BY 1 ;  
GO  
```  
  
### <a name="e-creating-a-sequence-using-default-values"></a>E. 建立使用預設值的順序  
 下列範例會建立使用預設值的順序。  
  
```sql  
CREATE SEQUENCE Test.TestSequence ;  
```  
  
 執行下列陳述式，以檢視順序的屬性。  
  
```sql  
SELECT * FROM sys.sequences WHERE name = 'TestSequence' ;  
```  
  
 輸出的部分清單示範預設值。  
  
| 輸出 | 預設值|  
|-|-|  
|`start_value`|`-9223372036854775808`|  
|`increment`|`1`|  
|`mimimum_value`|`-9223372036854775808`|  
|`maximum_value`|`9223372036854775807`|  
|`is_cycling`|`0`|  
|`is_cached`|`1`|  
|`current_value`|`-9223372036854775808`|  
  
### <a name="f-creating-a-sequence-with-a-specific-data-type"></a>F. 建立具有特定資料類型的順序

下列範例會建立使用 **smallint** 資料類型而且在 -32,768 到 32,767 範圍內的順序。  
  
```sql  
CREATE SEQUENCE SmallSeq 
    AS smallint ;  
```  
  
### <a name="g-creating-a-sequence-using-all-arguments"></a>G. 建立使用所有引數的順序  
 下列範例會建立使用 **decimal** 資料類型、在 0 到 255 範圍內，而且名為 DecSeq 的順序。 順序開頭為 125，而且每次產生數字時會遞增 25。 因為順序設定為循環，所以當值超過最大值 200 時，順序會從最小值 100 重新啟動。  
  
```sql  
CREATE SEQUENCE Test.DecSeq  
    AS decimal(3,0)   
    START WITH 125  
    INCREMENT BY 25  
    MINVALUE 100  
    MAXVALUE 200  
    CYCLE  
    CACHE 3  
;  
```  
  
 執行下列陳述式以查看第一個值，即 `START WITH` 選項值 125。  
  
```sql  
SELECT NEXT VALUE FOR Test.DecSeq;  
```  
  
 再執行陳述式三次，以傳回 150、175 和 200。  
  
 重新執行陳述式，以查看開始值循環回到 `MINVALUE` 選項值 100。  
  
 執行下列程式碼，以確認快取大小，並查看目前的值。  
  
```sql  
SELECT cache_size, current_value   
FROM sys.sequences  
WHERE name = 'DecSeq' ;  
```  
  
## <a name="see-also"></a>另請參閱  
 [ALTER SEQUENCE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-sequence-transact-sql.md)   
 [DROP SEQUENCE &#40;Transact-SQL&#41;](../../t-sql/statements/drop-sequence-transact-sql.md)   
 [NEXT VALUE FOR &#40;Transact-SQL&#41;](../../t-sql/functions/next-value-for-transact-sql.md)   
 [序號](../../relational-databases/sequence-numbers/sequence-numbers.md)  
  
  
