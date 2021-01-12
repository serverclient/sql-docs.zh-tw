---
description: 彈性檔案來源
title: 彈性檔案來源 | Microsoft Docs
ms.custom: ''
ms.date: 05/22/2019
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
f1_keywords:
- sql13.dts.designer.afpextfilesrc.f1
- sql14.dts.designer.afpextfilesrc.f1
author: chugugrace
ms.author: chugu
ms.openlocfilehash: 4be8d36cb6bff0b8cc50bf479d41fcca2e7064f1
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091668"
---
# <a name="flexible-file-source"></a>彈性檔案來源

[!INCLUDE[sqlserver-ssis](../../includes/applies-to-version/sqlserver-ssis.md)]

**彈性檔案來源** 元件可讓 SSIS 套件從各種支援的儲存體服務讀取資料。
以下為目前支援的儲存體服務：

- [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction) \(部分機器翻譯\)
  
若要查看適用於彈性檔案來源的編輯器，可在資料流程設計師上拖放 [彈性檔案來源]，然後按兩下以開啟編輯器。
  
**彈性檔案來源** 是 [SQL Server Integration Services (SSIS) Feature Pack for Azure](../../integration-services/azure-feature-pack-for-integration-services-ssis.md) 的元件。  
  
**彈性檔案來源編輯器** 提供下列屬性。

- **檔案連線管理員類型：** 指定來源連線管理員類型。 然後選擇一個現有的指定類型，或建立新的。
- **資料夾路徑：** 指定來源資料夾路徑。
- **檔案名稱：** 指定來源檔案名稱。
- **檔案格式：** 指定來源檔案格式。 支援的格式為 **Text**、**Avro**、**ORC**、**Parquet**。 ORC/Parquet 需要 Java。 如需詳細資料，請參閱[這裡](../../integration-services/azure-feature-pack-for-integration-services-ssis.md#dependency-on-java)。
- **資料行分隔符號字元：** 指定要用來作為資料行分隔符號的字元 (不支援多字元分隔符號)。
- **第一個資料列作為資料行名稱：** 指定是否要將第一個資料列視為資料行名稱。
- **解壓縮檔案：** 指定是否要解壓縮來源檔案。
- **壓縮類型：** 指定來源檔案壓縮格式。 支援的格式為 **GZIP**、**DEFLATE**、**BZIP2**。
  
**進階編輯器** 提供下列屬性。

- **rowDelimiter：** 用來分隔檔案中資料列的字元。 只允許一個字元。 **預設** 值為 \r\n。
- **escapeChar：** 用來逸出輸入檔內容中資料行分隔符號的特殊字元。 您無法為資料表同時指定 escapeChar 和 quoteChar。 只允許一個字元。 無預設值。
- **quoteChar：** 用來為字串值加上引號的字元。 系統會將引號字元內資料行和資料列分隔符號視為字串值的一部分。 這個屬性同時適用於輸入和輸出資料集。 您無法為資料表同時指定 escapeChar 和 quoteChar。 只允許一個字元。 無預設值。
- **nullValue：** 用來代表 Null 值的一或多個字元。 **預設** 值是 \N。
- **encodingName：** 指定編碼名稱。 請參閱 [Encoding.EncodingName](/dotnet/api/system.text.encoding) 屬性。
- **skipLineCount：** 表示從輸入檔讀取資料時會略過非空白資料列的數目。 如果同時指定 skipLineCount 和 firstRowAsHeader，則會先略過行，然後從輸入檔讀取標頭資訊。
- **treatEmptyAsNull：** 指定從輸入檔讀取資料時是否將 Null 或空字串視為 Null 值。 **預設** 值為 True。

指定連線資訊後，請切換至 [資料行] 頁面，將來源資料行對應至 SSIS 資料流程的目的地資料行。

**服務主體權限設定的注意事項**

若要讓 **測試連線** 正常執行 (Blob 儲存體或 Data Lake Storage Gen2)，則應將服務主體至少指派給儲存體帳戶的 **儲存體 Blob 資料讀取者** 角色。
此操作可透過 [RBAC](/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal) 完成。

針對 Blob 儲存體，至少指派 **儲存體 Blob 資料讀取器** 角色來授與讀取權限。

針對 Data Lake Storage Gen2，權限由 RBAC 和 [ACL](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer) 決定。
請注意，ACL 會使用應用程式註冊服務主體的物件識別碼 (OID) 進行設定，如[此處](/azure/storage/blobs/data-lake-storage-access-control#how-do-i-set-acls-correctly-for-a-service-principal)所詳述。
這不同於與 RBAC 設定搭配使用的應用程式 (用戶端) 識別碼。
當安全性主體透過內建角色或自訂角色獲得 RBAC 資料權限時，在要求授權時會先評估這些權限。
如果要求作業是由安全性主體的 RBAC 指派授權，則會立即解決授權，且不會執行任何額外的 ACL 檢查。
或者，如果安全性主體不具有 RBAC 指派，或要求作業不符合指派的權限，則會執行 ACL 檢查來判斷安全性主體是否已獲授權執行要求作業。
針對讀取權限，請至少授與 **執行** 權限 (從來源檔案系統開始)，以及所要讀取檔案的 **讀取** 權限。
或者，使用 RBAC 至少授與 **儲存體 Blob 資料讀取者** 角色。
如需詳細資料，請參閱[這篇](/azure/storage/blobs/data-lake-storage-access-control)文章。