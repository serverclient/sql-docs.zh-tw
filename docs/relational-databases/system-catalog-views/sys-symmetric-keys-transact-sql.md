---
description: sys.symmetric_keys (Transact-SQL)
title: sys.symmetric_keys (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/07/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- symmetric_keys
- sys.symmetric_keys
- sys.symmetric_keys_TSQL
- symmetric_keys_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.symmetric_keys catalog view
ms.assetid: d410eae1-3a52-45de-b9a1-52d2bd93a8eb
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: de9933199a37f3b5acd071fbfbe9354173247885
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093011"
---
# <a name="syssymmetric_keys-transact-sql"></a>sys.symmetric_keys (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

  為透過 CREATE SYMMETRIC KEY 陳述式所建立的各個對稱金鑰傳回一個資料列。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|金鑰的名稱。 在資料庫中，這是唯一的。|  
|**principal_id**|**int**|擁有金鑰的資料庫主體識別碼。|  
|**symmetric_key_id**|**int**|金鑰的識別碼。 在資料庫中，這是唯一的。|  
|**key_length**|**int**|金鑰的長度 (以位元為單位)。|  
|**key_algorithm**|**char(2)**|金鑰使用的演算法：<br /><br /> R2 = RC2<br /><br /> R4 = RC4<br /><br /> D = DES<br /><br /> D3 = Triple DES<br /><br /> DT = TRIPLE_DES_3KEY<br /><br /> DX = DESX<br /><br /> A1 = AES 128<br /><br /> A2 = AES 192<br /><br /> A3 = AES 256<br /><br /> NA = EKM 金鑰|  
|**algorithm_desc**|**nvarchar(60)**|金鑰使用之演算法的描述：<br /><br /> RC2<br /><br /> RC4<br /><br /> DES<br /><br /> Triple_DES<br /><br /> TRIPLE_DES_3KEY<br /><br /> DESX<br /><br /> AES_128<br /><br /> AES_192<br /><br /> AES_256<br /><br /> NULL (僅限 Extensible Key Management 演算法)|  
|**create_date**|**datetime**|建立金鑰的日期。|  
|**modify_date**|**datetime**|修改金鑰的日期。|  
|**key_guid**|**uniqueidentifier**|與金鑰相關聯的全域唯一識別碼 (GUID)。 它會為保存的金鑰自動產生。 暫時金鑰的 GUID 衍生自使用者提供的複雜密碼。|  
|**key_thumbprint**|**sql_variant**|金鑰的 SHA-1 雜湊。 此雜湊是全域唯一的。 如果是非 Extensible Key Management 金鑰，這個值將會是 NULL。|  
|**provider_type**|**nvarchar(120)**|密碼編譯提供者的類型：<br /><br /> CRYPTOGRAPHIC PROVIDER = Extensible Key Management 金鑰<br /><br /> NULL = 非 Extensible Key Management 金鑰|  
|**cryptographic_provider_guid**|**uniqueidentifier**|密碼編譯提供者的 GUID。 如果是非 Extensible Key Management 金鑰，這個值將會是 NULL。|  
|**cryptographic_provider_algid**|**sql_variant**|密碼編譯提供者的演算法識別碼。 如果是非 Extensible Key Management 金鑰，這個值將會是 NULL。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="remarks"></a>備註  
 RC4 演算法已被取代。 [!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)]  
  
> [!NOTE]  
>  只有 RC4 演算法支援回溯相容性。 只有在資料庫相容性層級為 90 或 100 時，才能使用 RC4 或 RC4_128 加密新資料 (不建議使用)。請改用較新的演算法，例如其中一個 AES 演算法。 在 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 中，使用 RC4 或 RC4_128 加密的資料可以在任何相容性層級進行解密。  
  
 **釐清有關 DES 演算法的資訊：**  
  
-   DESX 未正確命名。 以 ALGORITHM = DESX 建立的對稱金鑰實際上使用具有 192 位元金鑰的 TRIPLE DES 加密。 未提供 DESX 演算法。 [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]  
  
-   以 ALGORITHM = TRIPLE_DES_3KEY 建立的對稱金鑰使用具有 192 位元金鑰的 TRIPLE DES。  
  
-   以 ALGORITHM = TRIPLE_DES 建立的對稱金鑰使用具有 128 位元金鑰的 TRIPLE DES。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [可延伸金鑰管理 &#40;EKM&#41;](../../relational-databases/security/encryption/extensible-key-management-ekm.md)   
 [安全性目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/security-catalog-views-transact-sql.md)   
 [加密階層](../../relational-databases/security/encryption/encryption-hierarchy.md)   
 [CREATE SYMMETRIC KEY &#40;Transact-SQL&#41;](../../t-sql/statements/create-symmetric-key-transact-sql.md)  
  
  
