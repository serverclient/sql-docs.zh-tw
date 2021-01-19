---
title: 資料庫引擎：重大變更 | Microsoft Docs
titleSuffix: SQL Server 2016
description: 了解升級時 SQL Server 2016 (13.x) 及更早版本中可能會中斷先前版本功能的資料庫引擎變更。
ms.custom: seo-lt-2019
ms.date: 07/22/2020
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: release-landing
ms.topic: conceptual
helpviewer_keywords:
- Database Engine [SQL Server], what's new
- breaking changes [SQL Server]
ms.assetid: 47edefbd-a09b-4087-937a-453cd5c6e061
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: ea6799570df43f5d49cadd93b98dc89528acacdd
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171050"
---
# <a name="breaking-changes-to-database-engine-features-in-sql-server-2016"></a>SQL Server 2016 資料庫引擎功能的重大變更

[!INCLUDE [SQL Server 2016](../includes/applies-to-version/sqlserver2016.md)]  

  此主題描述 [!INCLUDE[sssql15-md](../includes/sssql16-md.md)] [!INCLUDE[ssDE](../includes/ssde-md.md)] 及舊版 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的重大變更。 這些變更可能會中斷以舊版 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]為根據的應用程式、指令碼或功能。 當您升級時可能會遇到這些問題。  
  
##  <a name="breaking-changes-in-sssql15"></a><a name="SQL15"></a>[!INCLUDE[ssSQL15](../includes/sssql16-md.md)] 的重大變更  
  
-   `sys.dm_io_virtual_file_stats` 的 *sample_ms* 資料行已經從 **int** 擴充到 **bigint** 資料類型。  
  
-   `sys.fn_virtualfilestats` 的 *TimeStamp* 資料行已經從 **int** 擴充到 **bigint** 資料類型。  

-   在資料庫相容性層級 130 下，藉由考量小數部分的毫秒從 **datetime** 隱含轉換至 **datetime2** 資料類型顯示改善的精確度，會導致不同的轉換值。 只要 datetime 和 datetime2 資料類型之間存在混合的比較案例，就明確轉換為 datetime2 資料類型。 如需詳細資訊，請參閱此 [Microsoft 支援服務文章](https://support.microsoft.com/help/4010261)。

-   在資料庫相容性層級 130 之下，在特定數值與日期時間資料類型之間執行隱含轉換的作業會顯示改善的精確度，而且可能導致不同的轉換值。 這包括使用需要計算的函數，例如 `DATEDIFF` 和 `ROUND`。 如需詳細資訊，請參閱此 [Microsoft 支援服務文章](https://support.microsoft.com/help/4010261)。

## <a name="previous-versions"></a><a name="previous-versions"></a> 舊版  

如需 [!INCLUDE[ssSQL14](../includes/sssql14-md.md)] 和某些舊版的重大變更相關資訊，請參閱 [SQL Server 2014 中對於資料庫引擎功能的重大變更](/previous-versions/sql/2014/database-engine/breaking-changes-to-database-engine-features-in-sql-server-2016)。

#### <a name="archived-documentation-for-very-old-versions-of-sql-server"></a>非常舊版的 SQL Server 封存文件

[!INCLUDE[Archived documentation for very old versions of SQL Server](../includes/paragraph-content/previous-versions-archive-documentation-sql-server.md)]

## <a name="see-also"></a>另請參閱  
 [SQL Server 2016 中已被取代的 Database Engine 功能](../database-engine/deprecated-database-engine-features-in-sql-server-2016.md)   
 [SQL Server 2016 中已停止的 Database Engine 功能](./discontinued-database-engine-functionality-in-sql-server.md)   
 [SQL Server Database Engine 回溯相容性](./discontinued-database-engine-functionality-in-sql-server.md)   
 [ALTER DATABASE 相容性層級 &#40;Transact-SQL&#41;](../t-sql/statements/alter-database-transact-sql-compatibility-level.md)   
 [Windows 上處理某些資料類型和不常見作業的 SQL Server 2016 或 SQL Server 2017 改善](https://support.microsoft.com/help/4010261)