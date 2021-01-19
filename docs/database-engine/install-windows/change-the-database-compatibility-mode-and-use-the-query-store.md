---
title: 在升級後使用查詢存放區
description: 此文章描述使用查詢存放區來建立基準，並在 SQL Server 升級中變更資料庫相容性層級的位置。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
helpviewer_keywords:
- query plans [SQL Server], migrating
- upgrading SQL Server, migrating query plans
- plan guides [SQL Server], migrating query plans
ms.assetid: 7e02a137-6867-4f6a-a45a-2b02674f7e65
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 7d4394c5fe4d790668bcc6733ce0aba0f643f7f3
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170890"
---
# <a name="change-the-database-compatibility-level-and-use-the-query-store"></a>變更資料庫相容性層級並使用查詢存放區

[!INCLUDE [SQL Server -Windows Only](../../includes/applies-to-version/sql-windows-only.md)]

在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 及更新版本中，某些變更只在[資料庫相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)變更後才會啟用。 有數種原因可以完成這項作業：  
  
- 因為升級是單向作業 (不可能降級檔案格式)，所以可以將啟用新功能區隔到資料庫內的個別作業。 設定可還原為先前的資料庫相容性層級。  新的模型可減少必須在中斷期間發生的事項數目。  
  
- 查詢處理器的變更會有複雜的影響。 即使對系統進行「良好」變更對多數工作負載而言可能有益，但可能造成其他工作負載之重要查詢發生無法接受的迴歸。 將此邏輯與升級程序區隔可讓功能 (例如查詢存放區) 快速降低計畫選擇迴歸，或甚至在生產伺服器中予以完全避免。  
  
> [!IMPORTANT]  
> 附加或還原資料庫以及就地升級之後，[!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 預期會有下列行為：
> - 如果使用者資料庫的相容性層級在升級前為 100 或更高層級，則在升級後仍會保持相同。    
> - 如果使用者資料庫在升級前的相容性層級為 90，則在升級後的資料庫中，相容性層級會設定為 100，這是 [!INCLUDE[ssSQL17](../../includes/sssql17-md.md)] 支援的最低相容性層級)。    
> - tempdb、model、msdb 和 Resource 資料庫的相容性層級在升級之後會設定為目前相容性層級。   
> - master 系統資料庫會繼續保有升級前的相容性層級。    
  
啟用新查詢處理器功能的升級程序與產品的發行後服務模型有關。  其中某些修正是在[追蹤旗標 4199](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md#4199) 下所發行。  需要修正程式的客戶可以選擇這些修正程式，而不會造成其他客戶的非預期衰退。 查詢處理器 Hotfix 的發行後服務模型記載在 [這裡](https://support.microsoft.com/kb/974006)。 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，移到新的相容性層級表示不再需要追蹤旗標 4199，原因是現在預設會在最新的相容性層級中啟用那些修正。 因此，在升級程序期間，一定要確認在升級程序完成之後未啟用 4199。  

> [!NOTE]
> 不過，如果適用的話，仍需追蹤旗標 4199，以啟用在 RTM 後發行的任何新查詢處理器修正。
  
如需將查詢處理器程式碼升級至最新版，建議遵循以下工作流程，其說明請參閱[查詢存放區使用案例的在升級至較新 SQL Server 期間保持效能穩定性一節](../../relational-databases/performance/query-store-usage-scenarios.md#CEUpgrade)。  
  
![將查詢處理器升級至最新版程式碼的建議工作流程圖表。](../../relational-databases/performance/media/query-store-usage-5.png "query-store-usage-5") 

從 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] v18 開始，可以使用 [查詢調整小幫手] 引導使用者完成建議的工作流程。 如需詳細資訊，請參閱[使用查詢調整小幫手來升級資料庫](../../relational-databases/performance/upgrade-dbcompat-using-qta.md)。
 
## <a name="see-also"></a>另請參閱  
[檢視或變更資料庫的相容性層級](../../relational-databases/databases/view-or-change-the-compatibility-level-of-a-database.md)     
[查詢存放區使用案例](../../relational-databases/performance/query-store-usage-scenarios.md)     
[ALTER DATABASE &#40;Transact-SQL&#41; 相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md)     
[使用查詢調整小幫手來升級資料庫](../../relational-databases/performance/upgrade-dbcompat-using-qta.md)        
  
