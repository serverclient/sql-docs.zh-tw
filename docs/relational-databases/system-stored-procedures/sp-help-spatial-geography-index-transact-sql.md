---
description: sp_help_spatial_geography_index (Transact-SQL)
title: sp_help_spatial_geography_index (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sp_help_spatial_geography_index
- sp_help_spatial_geography_index_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_help_spatial_geography_index procedure
ms.assetid: c9bf5675-eafc-4d71-bfdb-da963384fa0c
author: markingmyname
ms.author: maghan
ms.openlocfilehash: b2b519fd8e52af2a67c37941a4868622aa254599
ms.sourcegitcommit: 04cf7905fa32e0a9a44575a6f9641d9a2e5ac0f8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91810331"
---
# <a name="sp_help_spatial_geography_index-transact-sql"></a>sp_help_spatial_geography_index (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回有關 **地理位置** 空間索引的指定屬性集的名稱和值。 結果會以資料表格式傳回。 您可以選擇傳回核心屬性集或索引的所有屬性。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
  
sp_help_spatial_geography_index [ @tabname =] 'tabname'   
     [ , [ @indexname = ] 'indexname' ]   
     [ , [ @verboseoutput = ] 'verboseoutput' ]   
     [ , [ @query_sample = ] 'query_sample' ]   
```  
  
## <a name="arguments"></a>引數  
 請參閱 [空間索引預存程式的引數和屬性](../../relational-databases/system-stored-procedures/spatial-index-stored-procedures-arguments-and-properties.md)。  
  
## <a name="properties"></a>屬性  
 請參閱 [空間索引預存程式的引數和屬性](../../relational-databases/system-stored-procedures/spatial-index-stored-procedures-arguments-and-properties.md)。  
  
## <a name="permissions"></a>權限  
 必須為使用者指派 PUBLIC 角色才能存取此程序。 需要在伺服器和物件上具有 READ ACCESS 權限。  
  
## <a name="remarks"></a>備註  
  
## <a name="example"></a>範例  
 下列範例會使用 `sp_help_spatial_geography_index` 來調查** \@ qs**中指定查詢範例在資料表**geography_col**上定義的**地理**空間索引**SIndx_SpatialTable_geography_col2** 。 這個範例只會傳回指定索引的核心屬性。  
  
```  
declare @qs geography  
        ='POLYGON((-90.0 -180, -90 180.0, 90 180.0, 90 -180, -90 -180.0))';  
exec sp_help_spatial_geography_index 'geography_col', 'SIndx_SpatialTable_geography_col2', 0, @qs;  
```  
  
 **Geography**實例的周框方塊是整個地球。  
  
## <a name="requirements"></a>需求  
  
## <a name="see-also"></a>另請參閱  
 [空間索引預存程式](./spatial-index-stored-procedures-arguments-and-properties.md)   
 [sp_help_spatial_geography_index](../../relational-databases/system-stored-procedures/sp-help-spatial-geography-index-transact-sql.md)   
 [空間索引概觀](../../relational-databases/spatial/spatial-indexes-overview.md)   
 [空間資料 &#40;SQL Server&#41;](../../relational-databases/spatial/spatial-data-sql-server.md)  
  
