---
title: 資料庫屬性 (查詢存放區頁面) | Microsoft 文件
description: 了解如何使用 [資料庫屬性] 對話方塊中的 [查詢存放區] 索引標籤來設定查詢存放區模式、間隔、閾值和其他屬性。
ms.custom: ''
ms.date: 11/09/2015
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
f1_keywords:
- sql13.swb.databaseproperties.querystore.f1
ms.assetid: da47d75e-291a-4305-acef-4b0aaf5215da
author: stevestein
ms.author: sstein
ms.openlocfilehash: a0a7612c37ff63f851e5c3a42215192f9f982bdf
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171820"
---
# <a name="database-properties-query-store-page"></a>資料庫屬性 (查詢存放區頁面)
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  請從主體資料庫存取此頁面，並將其用於設定及修改資料庫查詢存放區的屬性。 這些選項也可使用 [ALTER DATABASE SET 選項](../../t-sql/statements/alter-database-transact-sql-set-options.md)加以設定。 如需查詢存放區的相關資訊，請參閱 [Monitoring Performance By Using the Query Store](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)  
  
||  
|-|  
|**適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 至 [目前版本](../../sql-server/what-s-new-in-sql-server-2016.md))、[!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)]。|  
  
## <a name="options"></a>選項。  
 作業模式  
 有效值為 OFF、READ_ONLY 和 READ_WRITE。 OFF 會停用查詢存放區。 在 READ_WRITE 模式中，查詢存放區會收集並保存查詢計劃和執行階段執行統計資料資訊。 在 READ_ONLY 模式中，可以從查詢存放區讀取資訊，但不會加入新資訊。 如果已用到了查詢存放區的配置空間上限，查詢存放區就會將其作業模式變更為 READ_ONLY。  
  
 作業模式 (實際)  
 取得查詢存放區的實際作業模式。  
  
 作業模式 (要求的)  
 取得及設定查詢存放區所需的作業模式。  
  
 資料排清間隔 (分鐘)  
 決定將寫入查詢存放區的資料保存到磁碟的頻率。 為了獲得最佳效能，查詢存放區所收集的資料會以非同步方式寫入磁碟。 已設定進行此非同步傳輸的頻率。  
  
 統計資料收集間隔  
 取得及設定統計資料收集間隔值。  
  
 大小上限 (MB)  
 取得及設定配置給查詢存放區的總空間。  
  
 查詢存放區擷取模式  
 -   [無] 不會擷取新查詢。  
  
-   全部擷取全部查詢。  
  
-   根據資源耗用量自動擷取查詢。  
  
 過時查詢臨界值 (天)  
 取得及設定過時查詢臨界值。 設定 STALE_QUERY_THRESHOLD_DAYS 引數，可指定在查詢存放區中保留資料的天數。  
  
 清除查詢資料  
 移除查詢存放區的內容。  
  
 圓形圖  
 左圖顯示在磁碟上耗用總資料庫檔案，以及填有查詢存放區資料的檔案比率。  
  
 右圖顯示目前用完的查詢存放區配額比率。 請注意，配額不會顯示在左圖中。 配額可能超過目前資料庫的大小。  
  
## <a name="remarks"></a>備註  
 查詢存放區功能為 DBA 提供查詢計劃選擇及效能的深入了解。 它能讓您快速找出因為查詢計劃中的變更所導致的效能差異，以簡化效能疑難排解。 該功能會自動擷取查詢、計劃及執行階段統計資料的記錄，並會保留這些記錄供您檢閱。 其會以時段來區分資料、供您查看資料庫使用模式，並了解何時在伺服器上發生查詢計劃變更。 可藉由使用這個查詢存放區資料庫屬性頁面，或是使用 [ALTER DATABASE SET](../../t-sql/statements/alter-database-transact-sql-set-options.md) 選項，設定查詢存放區。 使用 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 對話方塊，查詢存放區即可呈現資訊。 如需查詢存放區的詳細資訊，請參閱 [Monitoring Performance By Using the Query Store](../../relational-databases/performance/monitoring-performance-by-using-the-query-store.md)。  
  
## <a name="see-also"></a>另請參閱  
 [查詢存放區預存程序 &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/query-store-stored-procedures-transact-sql.md)   
 [查詢存放區目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/query-store-catalog-views-transact-sql.md)