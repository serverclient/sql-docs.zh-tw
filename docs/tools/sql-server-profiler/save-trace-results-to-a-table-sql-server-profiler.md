---
title: 將追蹤結果儲存至資料表
titleSuffix: SQL Server Profiler
description: 了解如何使用 SQL Server Profiler 將追蹤結果儲存到 SQL Server 資料庫中的資料表。 了解如何指定要儲存的資料列數上限。
ms.prod: sql
ms.prod_service: sql-tools
ms.reviewer: ''
ms.technology: profiler
ms.topic: conceptual
ms.assetid: edbecf74-683b-4e43-a1ef-7a3d5f5e27f6
author: markingmyname
ms.author: maghan
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.openlocfilehash: 9e1dbe5def1065874cf840245bfaa3f2676af042
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171590"
---
# <a name="save-trace-results-to-a-table-sql-server-profiler"></a>將追蹤結果儲存到資料表 (SQL Server Profiler)

 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

此主題描述如何使用 [!INCLUDE[ssSqlProfiler](../../includes/sssqlprofiler-md.md)]將追蹤結果儲存到資料庫資料表。  
  
## <a name="to-save-trace-results-to-a-table"></a>若要將追蹤結果儲存至資料表
  
1.  在 [檔案] 功能表上，按一下 [新增追蹤]，接著連接到 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體。  
  
     會出現 [追蹤屬性] **[追蹤屬性]** 對話方塊。  
  
    > [!NOTE]  
    >  如果選取 [進行連接後立即啟動追蹤]，將不會顯示 [追蹤屬性] 對話方塊，而是開始追蹤。 於 [工具] 功能表，按一下 [選項]，並清除 [連接後立即啟動追蹤] 核取方塊，以關閉這項設定。  
  
2.  在 [追蹤名稱] 方塊中，輸入追蹤的名稱，然後按一下 [儲存至資料表]。  
  
3.  在 [連接至伺服器] 對話方塊中，連接至將包含追蹤資料表的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫。  
  
4.  在 [目的地資料表] 對話方塊中，從 [資料庫] 清單中選取資料庫。  
  
5.  在 [擁有者] 清單中，選取追蹤的擁有者。  
  
6.  在 [資料表] 清單中，輸入或選取追蹤結果的資料表名稱。 按一下 [確定]。  
  
7.  在 [追蹤屬性] 對話方塊中，選取 [設定最大資料列數 (單位: 千)] 核取方塊，以指定要儲存的最大資料列數。  
  
## <a name="see-also"></a>另請參閱  
 [SQL Server Profiler](../../tools/sql-server-profiler/sql-server-profiler.md)  
  
  
