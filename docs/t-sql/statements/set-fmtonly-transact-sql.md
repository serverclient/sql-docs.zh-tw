---
description: SET FMTONLY (Transact-SQL)
title: SET FMTONLY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/03/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- FMTONLY_TSQL
- FMTONLY
- SET FMTONLY
- SET_FMTONLY_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- metadata [SQL Server], only metadata returned
- SET FMTONLY statement
- FMTONLY option
ms.assetid: 02a1d9ac-2e58-433c-9a07-2c5a4a2214e1
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6bb9fb1cf9b10f27bcf90b9d4deb56bcd6484044
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093298"
---
# <a name="set-fmtonly-transact-sql"></a>SET FMTONLY (Transact-SQL)

[!INCLUDE[tsql-appliesto-ss-all-md](../../includes/tsql-appliesto-ss-all-md.md)]

  只將中繼資料傳回用戶端。 可以用來測試回應的格式，而不需實際執行查詢。  

> [!NOTE]
> 請勿使用這個功能。 這項功能已由下列項目取代：
>
> - [sp_describe_first_result_set (Transact-SQL)](../../relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql.md)
> - [sp_describe_undeclared_parameters (Transact-SQL)](../../relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql.md)
> - [sys.dm_exec_describe_first_result_set (Transact-SQL)](../../relational-databases/system-dynamic-management-views/sys-dm-exec-describe-first-result-set-transact-sql.md)
> - [sys.dm_exec_describe_first_result_set_for_object (Transact-SQL)](../../relational-databases/system-dynamic-management-views/sys-dm-exec-describe-first-result-set-for-object-transact-sql.md)

 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
SET FMTONLY { ON | OFF }   
```  

## <a name="remarks"></a>備註

當 `FMTONLY` 是 `ON` 時，會傳回一個含資料行名稱的資料列集，但不含任何資料列。

剖析 Transact-SQL 批次時，`SET FMTONLY ON` 沒有任何作用； 在執行階段的執行期間才會發生效果。

預設值為 `OFF`。

## <a name="permissions"></a>權限  
 需要 public 角色中的成員資格。  

## <a name="examples"></a>範例

下列 Transact-SQL 程式碼範例會將 `FMTONLY` 設為 `ON`。 此設定會導致 SQL Server 僅傳回所選資料行的中繼資料資訊。 具體來說，它會傳回資料行名稱， 而不會傳回任何資料列。

在範例中，測試執行預存程序 `prc_gm29` 時會傳回下列項目：

- 多個資料列集。
- 多個資料表的資料行 (在它的其中一個 `SELECT` 陳述式中)。

<!--
Issue 2246 inspired this code example, and the replacement of the two pre-existing examples. 2019/June/03, GM.
-->

```sql
SET NOCOUNT ON;
GO

DROP PROCEDURE IF EXISTS prc_gm29;

DROP TABLE IF EXISTS #tabTemp41;
DROP TABLE IF EXISTS #tabTemp42;
GO

CREATE TABLE #tabTemp41
(
   KeyInt41        INT           NOT NULL,
   Name41          NVARCHAR(16)  NOT NULL,
   TargetDateTime  DATETIME      NOT NULL  DEFAULT GetDate()
);

CREATE TABLE #tabTemp42
(
   KeyInt42 INT          NOT NULL,   -- JOIN-able to KeyInt41.
   Name42   NVARCHAR(16) NOT NULL
);
GO

INSERT INTO #tabTemp41 (KeyInt41, Name41) VALUES (10, 't41-c');
INSERT INTO #tabTemp42 (KeyInt42, Name42) VALUES (10, 't42-p');
GO

CREATE PROCEDURE prc_gm29
AS
BEGIN
SELECT * FROM #tabTemp41;
SELECT * FROM #tabTemp42;

SELECT t41.KeyInt41, t41.TargetDateTime, t41.Name41, t42.Name42
   FROM
                 #tabTemp41 AS t41
      INNER JOIN #tabTemp42 AS t42 on t42.KeyInt42 = t41.KeyInt41
END;
GO

SET DATEFORMAT mdy;

SET FMTONLY ON;
EXECUTE prc_gm29;   -- Returns multiple tables.
SET FMTONLY OFF;
GO

DROP PROCEDURE IF EXISTS prc_gm29;

DROP TABLE IF EXISTS #tabTemp41;
DROP TABLE IF EXISTS #tabTemp42;
GO

/****  Actual Output:
[C:\JunkM\]
>> osql.exe -S myazuresqldb.database.windows.net -U somebody -P secret -d MyDatabase -i C:\JunkM\Issue-2246-a.SQL 

 KeyInt41    Name41           TargetDateTime
 ----------- ---------------- -----------------------

 KeyInt42    Name42
 ----------- ----------------

 KeyInt41    TargetDateTime          Name41           Name42
 ----------- ----------------------- ---------------- ----------------


[C:\JunkM\]
>>
****/
```

## <a name="see-also"></a>另請參閱  
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)  
  
  

