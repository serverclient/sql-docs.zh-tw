---
description: " (Transact-sql) 的系統目錄檢視"
title: " (Transact-sql) 的目錄檢視 |Microsoft Docs"
ms.custom: ''
ms.date: 05/02/2016
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sql13.SysViewExpandPortal.f1
dev_langs:
- TSQL
helpviewer_keywords:
- catalog metadata [SQL Server]
- system views [SQL Server], catalog
- metadata [SQL Server], views
- catalogs [SQL Server], metadata
- catalog views [SQL Server]
- Database Engine [SQL Server], metadata
- catalog views [SQL Server], about catalog views
ms.assetid: 13bccc2f-ed3c-4b58-abd0-ca8bf34a66b8
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 6763194c3a7d12df95caa6e4696c37befe8f6ca8
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093221"
---
# <a name="system-catalog-views-transact-sql"></a> (Transact-sql) 的系統目錄檢視

[!INCLUDE[appliesto-ss-asdb-asdw-pdw-md](../../includes/appliesto-ss-asdb-asdw-pdw-md.md)]

目錄檢視會傳回 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 所用的資訊。 建議您使用目錄檢視，因為它們是目錄中繼資料最一般性的介面，提供了取得、轉換和呈現這項資訊之自訂形式的最有效方法。 所有使用者能夠使用的目錄中繼資料都是利用目錄檢視公開的。

> [!NOTE]
> 目錄檢視不包含複寫、備份、資料庫維護計畫或 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 目錄資料的相關資訊。

 部分目錄檢視繼承其他目錄檢視的資料列。 例如， [sys. 資料表](../../relational-databases/system-catalog-views/sys-tables-transact-sql.md) 目錄檢視繼承自 [sys. objects](../../relational-databases/system-catalog-views/sys-objects-transact-sql.md) 目錄檢視。 sys.objects 目錄檢視稱為基底檢視，而 sys.tables 檢視則稱為衍生檢視。 sys.tables 目錄檢視會傳回資料表特定的資料行，以及 sys.objects 目錄檢視傳回的所有資料行。 sys.objects 目錄檢視會傳回資料表以外的物件，例如預存程序和檢視。 建立資料表之後，這兩份檢視中都會傳回資料表的中繼資料。 雖然這兩個目錄檢視會傳回不同層級的資料表相關資訊，但是這份資料表的中繼資料中只有一個項目，其中包含一個名稱和一個 object_id。 這點可以摘要如下：

- 基底檢視包含資料行的子集和資料列的超集。
- 衍生檢視包含資料行的超集和資料列的子集。

> [!IMPORTANT]
> 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的未來版本中，[!INCLUDE[msCoName](../../includes/msconame-md.md)] 可能會在資料行清單結尾加入資料行，擴充任何系統目錄檢視的定義。 建議您不要 \* 在實際執行程式碼中使用從 *SYS.CATALOG_VIEW_NAME* 中選取的語法，因為傳回的資料行數目可能會變更並中斷您的應用程式。

 將目錄檢視組織成下列類別目錄：

:::row:::
    :::column:::
        [AlwaysOn 可用性群組目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/always-on-availability-groups-catalog-views-transact-sql.md)
        
        [Azure SQL Database 目錄檢視](../../relational-databases/system-catalog-views/azure-sql-database-catalog-views.md)
        
        [變更追蹤 &#40;Transact-sql&#41;的目錄檢視 ](../system-catalog-views/change-tracking-catalog-views-sys-change-tracking-databases.md)
        
        [&#40;Transact-sql&#41;的 CLR 元件類別目錄檢視 ](../../relational-databases/system-catalog-views/clr-assembly-catalog-views-transact-sql.md)
        
        [資料收集器檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/data-collector-views-transact-sql.md)
        
        [資料空間 &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/data-spaces-transact-sql.md)
        
        [Database Mail Views &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/database-mail-views-transact-sql.md)
        
        [資料庫鏡像見證目錄 Views &#40;Transact-sql&#41;](../system-catalog-views/database-mirroring-witness-catalog-views-sys-database-mirroring-witnesses.md)
        
        [資料庫和檔案目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/databases-and-files-catalog-views-transact-sql.md)
        
        [端點目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/endpoints-catalog-views-transact-sql.md)
        
        [擴充的事件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/extended-events-catalog-views-transact-sql.md)
        
        [擴充屬性目錄檢視 &#40;Transact-SQL&#41;](../system-catalog-views/extended-properties-catalog-views-sys-extended-properties.md)
        
        [&#40;Transact-sql&#41;的外部作業目錄檢視 ](../../relational-databases/system-catalog-views/external-operations-catalog-views-transact-sql.md)
        
        [Filestream 和 FileTable 目錄檢視 &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/filestream-and-filetable-catalog-views-transact-sql.md)
        
        [全文檢索搜尋和語義搜尋目錄檢視 &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/full-text-search-and-semantic-search-catalog-views-transact-sql.md)
        
        [連結的伺服器目錄檢視 &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/linked-servers-catalog-views-transact-sql.md)
    :::column-end:::
    :::column:::
        [訊息 &#40;&#41; 目錄檢視的錯誤 &#40;Transact-sql&#41;](../system-catalog-views/messages-for-errors-catalog-views-sys-messages.md)
        
        [物件目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/object-catalog-views-transact-sql.md)
        
        [&#40;Transact-sql&#41;的資料分割函數類別目錄檢視 ](../../relational-databases/system-catalog-views/partition-function-catalog-views-transact-sql.md)
        
        [以原則為基礎的管理檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/policy-based-management-views-transact-sql.md)
        
        [Resource Governor &#40;Transact-sql&#41;的目錄檢視 ](../../relational-databases/system-catalog-views/resource-governor-catalog-views-transact-sql.md)
        
        [查詢存放區目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/query-store-catalog-views-transact-sql.md)
        
        [純量類型目錄檢視 &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/scalar-types-catalog-views-transact-sql.md)
        
        [&#40;Transact-sql&#41;的架構目錄檢視 ](../system-catalog-views/schemas-catalog-views-sys-schemas.md)
        
        [安全性目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/security-catalog-views-transact-sql.md)
        
        [Service Broker 目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/service-broker-catalog-views-transact-sql.md)
        
        [&#40;Transact-sql&#41;的全伺服器設定目錄檢視 ](../../relational-databases/system-catalog-views/server-wide-configuration-catalog-views-transact-sql.md)
        
        [空間資料目錄檢視](../../relational-databases/system-catalog-views/spatial-data-catalog-views.md)
        
        [Azure Synapse Analytics 和平行處理資料倉儲目錄檢視](../../relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views.md)
        
        [Stretch Database &#40;Transact-sql&#41;的目錄檢視 ](../system-catalog-views/stretch-database-catalog-views-sys-remote-data-archive-databases.md)
        
        [XML 架構 &#40;XML 類型系統&#41; 目錄檢視 &#40;Transact-sql&#41;](../../relational-databases/system-catalog-views/xml-schemas-xml-type-system-catalog-views-transact-sql.md)
    :::column-end:::
:::row-end:::

## <a name="see-also"></a>另請參閱

- [&#40;Transact-sql&#41;的資訊架構視圖 ](../../relational-databases/system-information-schema-views/system-information-schema-views-transact-sql.md)
- [系統資料表 &#40;Transact-SQL&#41;](../../relational-databases/system-tables/system-tables-transact-sql.md)
- [查詢 SQL Server 系統目錄 FAQ](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)
