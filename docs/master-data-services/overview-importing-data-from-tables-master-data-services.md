---
title: 從資料表匯入資料
description: 在 Master Data Services 中為您的資料建立模型之後，從資料表匯入資料，並對資料進行變更。
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: mds
ms.reviewer: ''
ms.technology: master-data-services
ms.topic: conceptual
helpviewer_keywords:
- staging process [Master Data Services], about staging process
- importing data [Master Data Services]
- staging process [Master Data Services]
ms.assetid: 181d1e22-379c-45d1-b03c-e1e22ff14164
author: lrtoyou1223
ms.author: lle
ms.openlocfilehash: 6374bdcc748ab528c4cb70c2db6387d3b55f6cb6
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171530"
---
# <a name="overview-importing-data-from-tables-master-data-services"></a>概觀：從資料表匯入資料 (Master Data Services)

[!INCLUDE [SQL Server - Windows only ASDBMI  ](../includes/applies-to-version/sql-windows-only-asdbmi.md)]

  在 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)]中為資料建立模型之後，即可開始新增資料，並對資料進行變更。   可以使用 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] 暫存資料表、預存程序及主資料管理員。  
  
 如需如何新增和修改資料的指示，請參[從資料表匯入資料 &#40;Master Data Services&#41;](../master-data-services/import-data-from-tables-master-data-services.md)。  
  
> [!NOTE]
>  您也可以使用[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)][!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)][!INCLUDE[ssMDSXLS](../includes/ssmdsxls-md.md)]將資料從 Excel 新增至 MDS 存放庫 ([!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] 資料庫)。 如需詳細資訊，請參閱[概觀：從 Excel 匯入資料 &#40;適用於 Excel 的 MDS 增益集&#41;](../master-data-services/microsoft-excel-add-in/overview-importing-data-from-excel-mds-add-in-for-excel.md)。  
  
 當您新增和修改資料時，可以執行下列作業。  
  
-   載入及更新成員，及更新屬性值  
  
-   停用及刪除成員  
  
-   移動明確階層成員  
  
 新增及更新資料包含下列主要工作。  
  
1.  將資料匯入 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] 資料庫中的暫存資料表。  
  
2.  將資料從暫存資料表載入適當的 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] 資料表。  
  
     您可以使用暫存的預存程序或 [!INCLUDE[ssMDSmdm](../includes/ssmdsmdm-md.md)] 來載入資料。  
  
> [!NOTE]  
>  [!INCLUDE[ssSQL15](../includes/sssql16-md.md)]已淘汰對 [!INCLUDE[ssKilimanjaro](../includes/sskilimanjaro-md.md)] 暫存處理序提供支援。  
  
## <a name="deactivating-and-deleting-members-mds"></a>停用和刪除成員 (MDS)  
 停用表示可以重新啟用該成員。 如果重新啟用成員，即會還原成員在階層和集合中的屬性及成員資格。 之前的所有交易會完整無缺。 系統管理員可以在主要資料的 [版本管理]  功能區域中，看到停用的交易。  
  
 刪除表示從系統中永久清除該成員。 該成員的所有交易、所有關聯性及所有屬性都會遭到永久刪除。  
  
> [!NOTE]  
>  您無法使用暫存來重新啟用成員。 您必須在主資料管理員中手動進行。 如需詳細資訊，請參閱[重新啟用成員或集合 &#40;Master Data Services&#41;](../master-data-services/reactivate-a-member-or-collection-master-data-services.md)。  
>   
>  您無法使用暫存來刪除或重新啟用集合。 如需手動重新啟用集合的詳細資訊，請參閱[刪除成員或集合 &#40;Master Data Services&#41;](../master-data-services/delete-a-member-or-collection-master-data-services.md)。  
  
## <a name="moving-explicit-hierarchy-members-mds"></a>移動明確階層成員 (MDS)  
 大量移動明確階層的成員位置時，可以指定下列項目。  
  
-   合併成員做為合併成員的父系。  
  
-   合併成員做為分葉成員的父系。  
  
-   分葉成員做為分葉或合併成員的同層級。  
  
-   合併成員做為分葉或合併成員的同層級。  
  
## <a name="staging-tables-and-stored-procedures-mds"></a>暫存資料表和預存程序 (MDS)  
 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] 資料庫包含下列幾種可以填入您資料的暫存資料表類型。  
  
-   [分葉成員暫存資料表 &#40;Master Data Services&#41;](../master-data-services/leaf-member-staging-table-master-data-services.md)  
  
-   [合併成員暫存資料表 &#40;Master Data Services&#41;](../master-data-services/consolidated-member-staging-table-master-data-services.md)  
  
-   [關聯性暫存資料表 &#40;Master Data Services&#41;](../master-data-services/relationship-staging-table-master-data-services.md)  
  
 模型中的每個實體，都有一個暫存資料表。 資料表名稱表示對應的實體以及像是分葉成員的實體類型。 下圖顯示貨幣、客戶及產品實體的暫存資料表。  
  
 ![MDS 資料庫中的暫存資料表](../master-data-services/media/mds-staging-tables.png "MDS 資料庫中的暫存資料表")  
  
 建立實體時會指定資料表的名稱，且無法變更。 如果暫存資料表名稱包含 _1 或其他數字，則表示在建立實體時，該名稱的其他資料表已存在。  
  
 [!INCLUDE[ssMDSshort](../includes/ssmdsshort-md.md)] 包含下列幾種類型的暫存預存程序。  
  
-   stg.udp_ \<name> _Leaf  
  
-   stg.udp_ \<name> _Consolidated  
  
-   stg.udp_ \<name> _Relationship  
  
 模型中的每個實體，都有三個對應至分葉成員、合併的成員以及關聯性暫存資料表的預存程序。  下圖顯示貨幣、客戶及產品實體的暫存預存程序。  
  
 ![MDS 資料庫中的暫存預存程序](../master-data-services/media/mds-staging-storedprocedures.png "MDS 資料庫中的暫存預存程序")  
  
 如需預存程序的詳細資訊，請參閱[暫存預存程序 &#40;Master Data Services&#41;](../master-data-services/staging-stored-procedure-master-data-services.md)。  
  
## <a name="logging-transactions-mds"></a>記錄交易 (MDS)  
 可以記錄匯入或更新資料或關聯性時發生的所有交易。 預存程序中的選項可允許此記錄。 如果使用 [!INCLUDE[ssMDSmdm](../includes/ssmdsmdm-md.md)]起始暫存處理序，不會產生任何記錄。  
  
 在 [!INCLUDE[ssMDScfgmgr](../includes/ssmdscfgmgr-md.md)]中， **[記錄暫存交易]** 設定不會套用至暫存資料的這個方法。  
  
## <a name="related-content"></a>相關內容  
  
-   [驗證 &#40;Master Data Services&#41;](../master-data-services/validation-master-data-services.md)  
  
-   [商務規則 &#40;Master Data Services&#41;](../master-data-services/business-rules-master-data-services.md)  
  
  
