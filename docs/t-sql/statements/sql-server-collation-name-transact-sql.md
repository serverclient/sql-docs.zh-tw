---
description: SQL Server 定序名稱 (Transact-SQL)
title: SQL Server 定序名稱 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 02/21/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- collations [SQL Server], SQL collations
- SQL collations
- names [SQL Server], collations
ms.assetid: 56483d24-add7-483d-9b96-c6fda460ddbc
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8fa8fc550e166b24fcbadd1ca38587a63bc34204
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98079918"
---
# <a name="sql-server-collation-name-transact-sql"></a>SQL Server 定序名稱 (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

這是指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 定序之定序名稱的單一字串。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 支援 Windows 定序。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也支援有限數目 (<80) 的定序，稱為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 定序，這些定序是在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 支援 Windows 定序之前開發。 為了回溯相容性，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 定序仍然受到支援，但是不應該用於新的開發工作。 如需 Windows 定序的詳細資訊，請參閱 [Windows 定序名稱](../../t-sql/statements/windows-collation-name-transact-sql.md)。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

```syntaxsql
<SQL_collation_name> :: =
SQL_SortRules[_Pref]_CPCodepage_<ComparisonStyle>

<ComparisonStyle> ::=
_CaseSensitivity_AccentSensitivity | _BIN
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

*SortRules* 用來識別指定字典排序時套用排序規則之字母或語言的字串。 例如 Latin1_General 或 Polish。

**Pref** 指定預設為大寫。 即使比較不區分大小寫，但是當沒有其他區分方式時，字母的大寫版本還是會排在小寫版本的前面。

*Codepage* 指定識別定序所用字碼頁的一至四位數。 **CP1** 指定字碼頁 1252；若是其他所有字碼頁，則必須指定完整的字碼頁編號。 例如，**CP1251** 指定字碼頁 1251，而 **CP850** 則指定字碼頁 850。

*CaseSensitivity*
**CI** 指定不區分大小寫，**CS** 指定區分大小寫。

*AccentSensitivity*
**AI** 指定不區分腔調字，**AS** 指定區分腔調字。

**BIN** 指定要使用二進位排序次序。

## <a name="remarks"></a>備註

若要列出您的伺服器支援的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 定序，請執行下列查詢。

```sql
SELECT * FROM sys.fn_helpcollations()
WHERE name LIKE 'SQL%';
```

> [!NOTE]
> 針對排序順序識別碼 80，請使用任何 Window 定序與字碼頁 1250，以及二進位順序。 例如：Albanian_BIN、Croatian_BIN、Czech_BIN、Romanian_BIN、Slovak_BIN、Slovenian_BIN。

## <a name="see-also"></a>另請參閱

- [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)
- [常數](../../t-sql/data-types/constants-transact-sql.md)
- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)
- [CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md)
- [DECLARE @local_variable](../../t-sql/language-elements/declare-local-variable-transact-sql.md)
- [table](../../t-sql/data-types/table-transact-sql.md)
- [sys.fn_helpcollations](../../relational-databases/system-functions/sys-fn-helpcollations-transact-sql.md)
