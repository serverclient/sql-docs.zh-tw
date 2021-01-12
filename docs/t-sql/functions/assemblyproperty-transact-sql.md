---
description: ASSEMBLYPROPERTY (Transact-SQL)
title: ASSEMBLYPROPERTY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/24/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ASSEMBLYPROPERTY_TSQL
- ASSEMBLYPROPERTY
dev_langs:
- TSQL
helpviewer_keywords:
- ASSEMBLYPROPERTY statement
- assemblies [CLR integration], properties
ms.assetid: cf03d1b1-724c-48bf-a8df-3fe2586b150a
author: cawrites
ms.author: chadam
ms.openlocfilehash: 05dc6035cc4303fb6dc4de5074d13f65c9fc645e
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98088951"
---
# <a name="assemblyproperty-transact-sql"></a>ASSEMBLYPROPERTY (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

此函數傳回組件屬性的相關資訊。
  
![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ASSEMBLYPROPERTY('assembly_name', 'property_name')  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*assembly_name*  
組件的名稱。
  
*property_name*  
針對要擷取相關資訊的屬性，此屬性的名稱。 *property_name* 可為以下其中一個值：
  
|值|描述|  
|---|---|
|**CultureInfo**|組件的地區設定。|  
|**PublicKey**|組件的公開金鑰或公開金鑰 Token。|  
|**MvID**|由編譯器產生的完整組件版本識別碼。|  
|**VersionMajor**|組件之四部份版本識別碼的主要元件 (第一部份)。|  
|**VersionMinor**|組件之四部份版本識別碼的次要元件 (第二部份)。|  
|**VersionBuild**|組件之四部份版本識別碼的建置元件 (第三部份)。|  
|**VersionRevision**|組件之四部份版本識別碼的修訂元件 (第四部份)。|  
|**SimpleName**|組件的簡單名稱。|  
|**架構**|組件的處理器架構。|  
|**CLRName**|將簡單名稱、版本號碼、文化、公開金鑰和組件架構加以編碼的標準字串。 這個值可以唯一識別 Common Language Runtime (CLR) 端的組件。|  
  
## <a name="return-type"></a>傳回類型
**sql_variant**
  
## <a name="examples"></a>範例  
下列範例假設 `HelloWorld` 組件是已在 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫中註冊的組件。 如需詳細資訊，請參閱 [Hello World 範例](/previous-versions/sql/sql-server-2016/ff878250(v=sql.130))。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT ASSEMBLYPROPERTY ('HelloWorld' , 'PublicKey');  
```  
  
## <a name="see-also"></a>另請參閱
[CREATE ASSEMBLY &#40;Transact-SQL&#41;](../../t-sql/statements/create-assembly-transact-sql.md)  
[DROP ASSEMBLY &#40;Transact-SQL&#41;](../../t-sql/statements/drop-assembly-transact-sql.md)
  
