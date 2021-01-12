---
description: FULLTEXTSERVICEPROPERTY (Transact-SQL)
title: FULLTEXTSERVICEPROPERTY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- FULLTEXTSERVICEPROPERTY_TSQL
- FULLTEXTSERVICEPROPERTY
dev_langs:
- TSQL
helpviewer_keywords:
- full-text search [SQL Server], properties
- FULLTEXTSERVICEPROPERTY function
- services [SQL Server], full-text search properties
- test
ms.assetid: b7dcacb0-af83-4807-9d1e-49148b56b59c
author: cawrites
ms.author: chadam
ms.openlocfilehash: b5d14ac6097fb6b1199124046c5780139e644949
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98087131"
---
# <a name="fulltextserviceproperty-transact-sql"></a>FULLTEXTSERVICEPROPERTY (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  傳回與全文檢索引擎之屬性相關的資訊。 您可以使用 **sp_fulltext_service** 來設定和擷取這些屬性。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
FULLTEXTSERVICEPROPERTY ('property')  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *property*  
 這是一個包含全文檢索服務層級屬性名稱的運算式。 下表列出各個屬性，並提供傳回資訊的描述。  
  
> [!NOTE]
>  下列屬性將會在未來的 [!INCLUDE[msCoName](../../includes/msconame-md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中移除：**ConnectTimeout**、**DataTimeout** 和 **ResourceUsage**。 請避免在新的開發工作中使用這些屬性，並規劃修改目前使用任何這些屬性的應用程式。  
  
|屬性|值|  
|--------------|-----------|  
|**ResourceUsage**|傳回 0。 支援這個項目的目的，只是為了與舊版相容。|  
|**ConnectTimeout**|傳回 0。 支援這個項目的目的，只是為了與舊版相容。|  
|**IsFulltextInstalled**|隨目前的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體而安裝的全文檢索元件。<br /><br /> 0 = 未安裝全文檢索。<br /><br /> 1 = 已安裝全文檢索。<br /><br /> NULL = 無效的輸入，或錯誤。|  
|**DataTimeout**|傳回 0。 支援這個項目的目的，只是為了與舊版相容。|  
|**LoadOSResources**|指出是否註冊作業系統斷詞工具和篩選，以及是否搭配這個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體來使用它們。 依預設，會停用這個屬性來防止因更新作業系統 (OS) 而意外變更行為。 啟用 OS 資源會提供 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 索引服務所登錄，但並未安裝特定執行個體專用資源之語言和文件類型的存取權。 如果您啟用 OS 資源的載入，請確定這些 OS 資源是受信任之已簽署的二進位檔，否則當 **VerifySignature** 設為 1 時，便無法載入它們。<br /><br /> 0 = 只用這個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體專用的篩選和斷詞工具。<br /><br /> 1 = 載入 OS 篩選和斷詞工具。|  
|**VerifySignature**|指定 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 搜尋服務是否只載入已簽署的二進位檔。 依預設，只會載入受信任的已簽署之二進位檔。<br /><br /> 0 = 不驗證是否已簽署二進位檔。<br /><br /> 1 = 確認只載入受信任的已簽署之二進位檔。|  
  
## <a name="return-types"></a>傳回型別  
 **int**  
  
## <a name="examples"></a>範例  
 下列範例會檢查是否只載入已簽署的二進位檔，而且傳回值指示這項驗證並未發生。  
  
```sql  
SELECT fulltextserviceproperty('VerifySignature');  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
-----------   
0  
```  
  
 請注意，若要將簽章驗證設回預設值 1，您可以使用下列 `sp_fulltext_service` 陳述式：  
  
```sql  
EXEC sp_fulltext_service @action='verify_signature', @value=1;  
GO  
```  
  
## <a name="see-also"></a>另請參閱  
 [FULLTEXTCATALOGPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/fulltextcatalogproperty-transact-sql.md)   
 [中繼資料函數 &#40;Transact-SQL&#41;](../../t-sql/functions/metadata-functions-transact-sql.md)   
 [sp_fulltext_service &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-fulltext-service-transact-sql.md)  
  
  
