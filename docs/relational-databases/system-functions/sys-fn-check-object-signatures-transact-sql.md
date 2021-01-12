---
description: sys.fn_check_object_signatures (Transact-SQL)
title: sys.fn_check_object_signatures (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.fn_check_object_signatures_TSQL
- fn_check_object_signatures_TSQL
- fn_check_object_signatures
- sys.fn_check_object_signatures
dev_langs:
- TSQL
helpviewer_keywords:
- sys.fn_check_object_signatures function
ms.assetid: 47509566-d3d7-46a9-89c1-91b4895d56b9
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 4efb55bdeb3455c2c98278e8b90021c5cf194146
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099660"
---
# <a name="sysfn_check_object_signatures-transact-sql"></a>sys.fn_check_object_signatures (Transact-SQL)
[!INCLUDE [sql-asdbmi-pdw](../../includes/applies-to-version/sql-asdbmi-pdw.md)]

  傳回所有可簽署物件的清單，並指出物件是否由指定的憑證或非對稱金鑰所簽署。 如果此物件是由指定的憑證或非對稱金鑰所簽署，它也會傳回此物件的簽章是否有效。  
  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
fn_ check_object_signatures (   
    { '@class' } , { @thumbprint }   
  )   
```  
  
## <a name="arguments"></a>引數  
 { '\@*類別*'}  
 識別所提供之指模的類型：  
  
-   'certificate'  
  
-   'asymmetric key'  
  
 \@*類別* 為 **sysname**。  
  
 { \@ *thumbprint* }  
 用來加密金鑰的憑證 SHA-1 雜湊，或用來加密金鑰的非對稱金鑰 GUID。 \@*指紋* 是 **Varbinary (20)**。  
  
## <a name="tables-returned"></a>傳回的資料表  
 下表列出 **fn_check_object_signatures** 傳回的資料行。  
  
|Column|類型|描述|  
|------------|----------|-----------------|  
|type|**nvarchar(120)**|傳回類型描述或組件。|  
|entity_id|**int**|傳回所評估之物件的物件識別碼。|  
|is_signed|**int**|當此物件並非由提供的指模所簽署時，傳回 0。 當此物件是由提供的指模所簽署時，傳回 1。|  
|is_signature_valid|**int**|如果 is_signed 值是 1，就會在簽章無效時傳回 0。 當簽章有效時，則傳回 1。<br /><br /> 如果 is_signed 值是 0，則一律傳回 0。|  
  
## <a name="remarks"></a>備註  
 使用 **fn_check_object_signatures** 來確認惡意使用者未遭篡改物件。  
  
## <a name="permissions"></a>權限  
 需要憑證或非對稱金鑰的 VIEW DEFINITION。  
  
## <a name="examples"></a>範例  
 下列範例會尋找 `master` 資料庫的結構描述簽署憑證，然後針對由結構描述簽署憑證所簽署而且具有有效簽章的物件，傳回 `is_signed` 值 1 和 `is_signature_valid` 值 1。  
  
```  
USE master;  
-- Declare a variable to hold the thumbprint.  
DECLARE @thumbprint varbinary(20) ;  
-- Populate the thumbprint variable with the master database schema signing certificate.  
SELECT @thumbprint = thumbprint   
FROM sys.certificates   
WHERE name LIKE '%SchemaSigningCertificate%' ;  
-- Evaluates the objects signed by the schema signing certificate  
SELECT type, entity_id, OBJECT_NAME(entity_id) AS [object name], is_signed, is_signature_valid  
FROM sys.fn_check_object_signatures ('certificate', @thumbprint) ;  
GO  
  
```  
  
## <a name="see-also"></a>另請參閱  
 [IS_OBJECTSIGNED &#40;Transact-sql&#41;](../../t-sql/functions/is-objectsigned-transact-sql.md)  
  
  
