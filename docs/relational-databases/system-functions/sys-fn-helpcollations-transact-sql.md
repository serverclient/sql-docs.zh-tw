---
description: sys.fn_helpcollations (Transact-SQL)
title: sys.fn_helpcollations (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/23/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- fn_helpcollations
- fn_helpcollations_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.fn_helpcollations function
- collations [SQL Server], supported
- fn_helpcollations function
ms.assetid: b5082e81-1fee-4e2c-b567-5412eaee41c1
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016|| = azure-sqldw-latest ||=azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8185f4977f784a0b2590d6a5fdf4a210dd2cd328
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101401"
---
# <a name="sysfn_helpcollations-transact-sql"></a>sys.fn_helpcollations (Transact-SQL)

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  傳回所有支援之定序的清單。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```
fn_helpcollations ()  
```  
  
## <a name="tables-returned"></a>傳回的資料表

 **fn_helpcollations** 會傳回下列資訊。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|名稱|**sysname**|標準定序名稱|  
|描述|**nvarchar(1000)**|定序的描述|  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 支援 Windows 定序。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也支援有限數量的 ( # B0 80) 定序的定序 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，這些定序是在支援的 Windows 定序之前所開發 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 仍然支援定序以提供回溯相容性，但不應該用於新的開發工作。 如需 Windows 定序的詳細資訊，請參閱 [Windows 定序名稱 &#40;Transact-SQL&#41;](../../t-sql/statements/windows-collation-name-transact-sql.md)。 如需定序的詳細資訊，請參閱[定序和 Unicode 支援](../../relational-databases/collations/collation-and-unicode-support.md)。  
  
## <a name="examples"></a>範例

 下列範例會傳回開頭是 `L` 字母，而且是二進位排序定序的所有定序名稱。

> [!Note]
> Azure Synapse Analytics 對 fn_helpcollations ( # A1 的查詢必須在 master 資料庫中執行。  
  
```sql  
SELECT Name, Description FROM fn_helpcollations()  
WHERE Name like 'L%' AND Description LIKE '% binary sort';  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 ```
 Name                   Description  
 -------------------    ------------------------------------  
 Lao_100_BIN            Lao-100, binary sort  
 Latin1_General_BIN     Latin1-General, binary sort  
 Latin1_General_100_BIN Latin1-General-100, binary sort  
 Latvian_BIN            Latvian, binary sort  
 Latvian_100_BIN        Latvian-100, binary sort  
 Lithuanian_BIN         Lithuanian, binary sort  
 Lithuanian_100_BIN     Lithuanian-100, binary sort  
  
 (7 row(s) affected)  
 ```
  
## <a name="see-also"></a>另請參閱

[COLLATE &#40;Transact-SQL&#41;](~/t-sql/statements/collations.md)   
[COLLATIONPROPERTY &#40;Transact-sql&#41;](../../t-sql/functions/collation-functions-collationproperty-transact-sql.md)  
[Azure Synapse Analytics 的資料庫定序支援](https://azure.microsoft.com/blog/database-collation-support-for-azure-sql-data-warehouse-2)  
