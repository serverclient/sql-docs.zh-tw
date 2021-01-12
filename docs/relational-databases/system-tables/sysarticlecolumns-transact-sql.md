---
description: sysarticlecolumns (Transact-SQL)
title: sysarticlecolumns (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: language-reference
f1_keywords:
- sysarticlecolumns
- sysarticlecolumns_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sysarticlecolumns system table
ms.assetid: 55918592-e05d-43b6-843b-7e4d82fa6275
author: cawrites
ms.author: chadam
ms.openlocfilehash: 8e6a7f9bcc3a7c300366a761564c45465988d60a
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094679"
---
# <a name="sysarticlecolumns-transact-sql"></a>sysarticlecolumns (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  **Sysarticlecolumns** 資料表會針對快照式或交易式發行集中發行的每個資料表資料行，各包含一個資料列，並將每個資料行對應到它的發行項。 這份資料表儲存在發行集資料庫中。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**>artid**|**int**|識別發行項。|  
|**colid**|**smallint**|識別發行項中的資料行。|  
|**is_udt**|**bit**|指出資料行是否為使用者自訂資料類型 (UDT) 資料行。 值為 **1** 表示 UDT 資料行。|  
|**is_xml**|**bit**|指出資料行是否為 **xml** 資料行。 值為 **1** 表示 xml 資料行。|  
|**is_max**|**bit**|指出資料行是否為大數值資料類型資料行、 **Varchar (max)**、 **Nvarchar (max)** 和 **Varbinary (max)**。 值為 **1** 表示大型值資料行。|  
  
## <a name="see-also"></a>另請參閱  
 [複寫資料表 &#40;Transact-sql&#41;](../../relational-databases/system-tables/replication-tables-transact-sql.md)   
 [複寫檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-views/replication-views-transact-sql.md)  
  
  
