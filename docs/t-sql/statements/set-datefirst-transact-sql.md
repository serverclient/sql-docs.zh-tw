---
description: SET DATEFIRST (Transact-SQL)
title: SET DATEFIRST (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- SET DATEFIRST
- SET_DATEFIRST_TSQL
- DATEFIRST_TSQL
- DATEFIRST
dev_langs:
- TSQL
helpviewer_keywords:
- first day of week [SQL Server]
- day of week [SQL Server]
- SET DATEFIRST option [SQL Server]
- DATEFIRST option [SQL Server]
- weekdays [SQL Server]
- options [SQL Server], date
ms.assetid: 6b0d0e52-8ac1-4f88-b091-f98d6fb8574a
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 447f679f5cb57fceabf3831c8f62a4a6a19059fa
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093408"
---
# <a name="set-datefirst-transact-sql"></a>SET DATEFIRST (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  將一週的第一天設為 1-7 其中一個數字。  
  
 如需所有 [!INCLUDE[tsql](../../includes/tsql-md.md)] 日期和時間資料類型與函數的概觀，請參閱[日期和時間資料類型與函數 &#40;Transact-SQL&#41;](../../t-sql/functions/date-and-time-data-types-and-functions-transact-sql.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
-- Syntax for SQL Server and Azure SQL Database  
  
SET DATEFIRST { number | @number_var }   
```  
  
```syntaxsql
-- Syntax for Azure Synapse Analytics and Parallel Data Warehouse  
  
SET DATEFIRST 7 ;  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *number* |  **@** _number_var_  
 這是一個整數，代表一週的第一天。 它可以是下列值之一。  
  
|值|每週的第一天是|  
|-----------|------------------------------|  
|**1**|星期一|  
|**2**|Tuesday|  
|**3**|星期三|  
|**4**|Thursday|  
|**5**|星期五|  
|**6**|星期六|  
|**7** (預設，U.S.英文)|星期日|  
  
## <a name="remarks"></a>備註  
 若要查看 SET DATEFIRST 的目前設定，請使用 [@@DATEFIRST](../../t-sql/functions/datefirst-transact-sql.md) 函數。  
  
 SET DATEFIRST 的設定是在執行階段進行設定，而不是在剖析階段進行設定。  
  
 指定 SET DATEFIRST 對 DATEDIFF 沒有任何作用。 DATEDIFF 一定會使用星期天當做一週的第一天，以確保此函數具決定性。  
  
## <a name="permissions"></a>權限  
 需要 **public** 角色的成員資格。  
  
## <a name="examples"></a>範例  
 下列範例會顯示每週日期來作為日期值，且會顯示變更 `DATEFIRST` 設定的作用。  
  
```sql
-- SET DATEFIRST to U.S. English default value of 7.  
SET DATEFIRST 7;  
  
SELECT CAST('1999-1-1' AS datetime2) AS SelectDate  
    ,DATEPART(dw, '1999-1-1') AS DayOfWeek;  
-- January 1, 1999 is a Friday. Because the U.S. English default   
-- specifies Sunday as the first day of the week, DATEPART of 1999-1-1  
-- (Friday) yields a value of 6, because Friday is the sixth day of the   
-- week when you start with Sunday as day 1.  
  
SET DATEFIRST 3;  
-- Because Wednesday is now considered the first day of the week,  
-- DATEPART now shows that 1999-1-1 (a Friday) is the third day of the   
-- week. The following DATEPART function should return a value of 3.  
SELECT CAST('1999-1-1' AS datetime2) AS SelectDate  
    ,DATEPART(dw, '1999-1-1') AS DayOfWeek;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [SET 陳述式 &#40;Transact-SQL&#41;](../../t-sql/statements/set-statements-transact-sql.md)  
  
  

