---
description: COLLATE (Transact-SQL)
title: COLLATE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 08/30/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- COLLATE
- COLLATE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- collations [SQL Server], COLLATE clause
- COLLATE clause
ms.assetid: 76763ac8-3e0d-4bbb-aa53-f5e7da021daa
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: d456379700ba48513105569a8486cf6f7a615ce3
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098443"
---
# <a name="collate-transact-sql"></a>COLLATE (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

定義資料庫或資料表資料行的定序，或套用至字元字串運算式時的定序轉換作業。 定序名稱可以是 Windows 定序名稱或 SQL 定序名稱。 若在資料庫建立期間未指定，會將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的預設定序指派給資料庫。 若未於資料表資料行建立期間指定，會將資料庫的預設定序指派給資料行。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

```syntaxsql
COLLATE { <collation_name> | database_default }
<collation_name> :: =
    { Windows_collation_name } | { SQL_collation_name }
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

*collation_name* 套用至運算式、資料行定義或資料庫定義的定序名稱。 *collation_name* 僅可以是指定的 *Windows_collation_name* 或 *SQL_collation_name*。 *collation_name* 必須是常值。 *collation_name* 不可以變數或運算式表示。

*Windows_collation_name* 是 [Windows 定序名稱](../../t-sql/statements/windows-collation-name-transact-sql.md)的定序名稱。

*SQL_collation_name* 是 [SQL Server 定序名稱](../../t-sql/statements/sql-server-collation-name-transact-sql.md)的定序名稱。

**database_default** 使 COLLATE 子句繼承目前資料庫的定序。

## <a name="remarks"></a>備註

您可以在許多層級指定 COLLATE 子句。 其中包括下列各項：

1. 建立或變更資料庫。

    您可以使用 `CREATE DATABASE` 或 `ALTER DATABASE` 陳述式的 COLLATE 子句來指定資料庫的預設定序。 您也可以在利用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 來建立資料庫時指定定序。 如果您沒有指定定序，就會將 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體的預設定序指派給資料庫。

    > [!NOTE]
    > 僅限 Windows Unicode 定序只能搭配 COLLATE 子句使用，以便將定序套用至資料行層級和運算式層級資料的 **nchar**、**nvarchar** 和 **ntext** 資料類型。這些無法搭配 COLLATE 子句使用，來定義或變更資料庫或伺服器執行個體的定序。

2. 建立或變更資料表資料行。

    您可以利用 `CREATE TABLE` 或 `ALTER TABLE` 陳述式的 COLLATE 子句來指定每個字元字串資料行的定序。 您也可以在利用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 來建立資料表時指定定序。 如果您沒有指定定序，就會將資料庫的預設定序指派給資料行。

    您也可以利用 COLLATE 子句中的 `database_default` 選項，指定暫存資料表中的資料行使用連接目前的使用者資料庫 (而非 **tempdb**) 之定序預設值。

3. 轉換運算式的定序。

    您可以利用 COLLATE 子句，將字元運算式套用至特定定序。 字元常值和變數會被指派目前資料庫的預設定序。 資料行參考會被指派資料行的定義定序。

識別碼的定序會隨定義的層級而不同。 執行個體層級物件 (如登入和資料庫名稱) 的識別碼會被指派執行個體的預設定序。 資料庫內之物件 (如資料表、檢視和資料行名稱) 的識別碼會被指派資料庫的預設定序。 例如，兩份大小寫不同的同名資料表，可以建立在定序區分大小寫的資料庫中，但不能建立在定序不區分大小寫的資料庫中。 如需詳細資訊，請參閱＜ [Database Identifiers](../../relational-databases/databases/database-identifiers.md)＞。

當連接內容只有一個相關聯資料庫時，可以建立變數、GOTO 標籤、暫時預存程序和暫存資料表，之後當內容切換到另一個資料庫時，可以參考它們。 變數、GOTO 標籤、暫存預存程序和暫存資料表的識別碼都位於伺服器執行個體的預設定序中。

COLLATE 子句僅適用於 **char**、**varchar**、**text**、**nchar**、**nvarchar** 和 **ntext** 資料類型。

COLLATE 會使用 *collate_name*，參考要套用至運算式、資料行定義或資料庫定義的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 定序名稱或 Windows 定序名稱。 *collation_name* 只可以是指定的 *Windows_collation_name* 或 *SQL_collation_name*，同時此參數必須包含常值。 *collation_name* 不可以變數或運算式表示。

定序通常是用定序名稱來識別，但在安裝程式中除外。 在安裝程式中，您會改為指定 Windows 定序的根定序指示項 (定序地區設定)，然後再指定區分或不區分大小寫或腔調字的排序選項。

您可以執行系統函數 [fn_helpcollations](../../relational-databases/system-functions/sys-fn-helpcollations-transact-sql.md) 來擷取 Windows 定序和 SQL Server 定序的所有有效定序名稱清單：

```sql
SELECT name, description
FROM fn_helpcollations();
```

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 只能支援基礎作業系統所支援的字碼頁。 當您執行視定序而定的動作時，所參考的物件使用的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 定序必須使用電腦上執行的作業系統所支援的字碼頁。 這些動作包括：

- 當您建立或變更資料庫時，指定資料庫的預設定序。
- 當您建立或變更資料表時，指定資料行的定序。
- 當還原或附加資料庫時，作業系統必須支援資料庫的預設定序及資料庫中任何 **char**、**varchar** 和 **text** 資料行或參數的定序。

> [!NOTE]
> 支援 **char** 和 **varchar** 資料類型的字碼頁轉換，但不支援 **text** 資料類型的字碼頁轉換。 不會報告字碼頁轉換期間所遺失的資料。
>
> 如果指定的定序或所參考物件所用的定序使用 Windows 不支援的字碼頁，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 就會顯示錯誤。

## <a name="examples"></a>範例

### <a name="a-specifying-collation-during-a-select"></a>A. 在 SELECT 期間指定定序

下列範例會建立簡單的資料表並且插入 4 個資料列。 然後範例會在從資料表選取資料時套用兩個定序，並且示範如何以不同的方式排序 `Chiapas`。

```sql
CREATE TABLE Locations
(Place varchar(15) NOT NULL);
GO
INSERT Locations(Place) VALUES ('Chiapas'),('Colima')
                             , ('Cinco Rios'), ('California');
GO
--Apply an typical collation
SELECT Place FROM Locations
ORDER BY Place
COLLATE Latin1_General_CS_AS_KS_WS ASC;
GO
-- Apply a Spanish collation
SELECT Place FROM Locations
ORDER BY Place
COLLATE Traditional_Spanish_ci_ai ASC;
GO
```

以下是第一個查詢的結果。

```output
Place
-------------
California
Chiapas
Cinco Rios
Colima
```

以下是第二個查詢的結果。

```output
Place
-------------
California
Cinco Rios
Colima
Chiapas
```

### <a name="b-additional-examples"></a>B. 其他範例

如需使用 **COLLATE** 的其他範例，請參閱 [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md#examples) 範例 **G. 建立資料庫並指定定序名稱和選項** 與 [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md#alter_column) 範例 **V. 變更資料行定序**。

## <a name="see-also"></a>另請參閱

- [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)
- [定序與 Unicode 支援](../../relational-databases/collations/collation-and-unicode-support.md)
- [定序優先順序](../../t-sql/statements/collation-precedence-transact-sql.md)
- [常數](../../t-sql/data-types/constants-transact-sql.md)
- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)
- [CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md)
- [DECLARE @local_variable](../../t-sql/language-elements/declare-local-variable-transact-sql.md)
- [資料表資料類型](../../t-sql/data-types/table-transact-sql.md)
