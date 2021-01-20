---
title: AdventureWorks 範例資料庫
description: 依照下列指示，使用 Transact-sql (T-sql) 、SQL Server Management Studio (SSMS) 或 Azure Data Studio，下載並安裝 AdventureWorks 範例資料庫至 SQL Server。
ms.prod: sql
ms.prod_service: sql
ms.technology: samples
ms.date: 06/16/2020
ms.reviewer: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: ec38d7eeed89d03bab4ebd92446517542e2e51a0
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98597001"
---
# <a name="adventureworks-sample-databases"></a>AdventureWorks 範例資料庫
[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

本文提供下載 AdventureWorks 範例資料庫的直接連結，以及將其還原為 SQL Server 和 Azure SQL Database 的指示。 

如需範例的詳細資訊，請參閱 [範例 GitHub 存放庫](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)。 

## <a name="prerequisites"></a>必要條件

- [SQL Server](https://www.microsoft.com/evalcenter/evaluate-sql-server-2019) 或 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
- [SQL Server Management Studio](../ssms/download-sql-server-management-studio-ssms.md) 或 [Azure Data Studio](../azure-data-studio/download-azure-data-studio.md)


## <a name="download-backup-files"></a>下載備份檔案 

使用這些連結可針對您的案例下載適當的範例資料庫。 

- **OLTP** 資料適用于最常見的線上交易處理工作負載。 
- **資料倉儲 (DW)** 資料適用于資料倉儲工作負載。 
- **輕量 (LT)** 資料是 **OLTP** 範例的輕量且精簡的低版本。 

如果您不確定所需的專案，請從符合您 SQL Server 版本的 OLTP 版本開始。 

|**OLTP** |**資料倉儲** |**輕量型**|
|---------|---------|---------|
|[AdventureWorks2019 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak)|[AdventureWorksDW2019 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksDW2019.bak)|[AdventureWorksLT2019 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksLT2019.bak)|
|[AdventureWorks2017 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2017.bak)|[AdventureWorksDW2017 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksDW2017.bak)|[AdventureWorksLT2017 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksLT2017.bak)|
|[AdventureWorks2016 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2016.bak)|[AdventureWorksDW2016 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksDW2016.bak)|[AdventureWorksLT2016 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksLT2016.bak)|
|[AdventureWorks2016_EXT .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2016_EXT.bak)|[AdventureWorksDW2016_EXT .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksDW2016_EXT.bak)| 不適用 |
|[AdventureWorks2014 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2014.bak)|[AdventureWorksDW2014 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksDW2014.bak)|[AdventureWorksLT2014 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksLT2014.bak)|
|[AdventureWorks2012 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2012.bak)|[AdventureWorksDW2012 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksDW2012.bak)|[AdventureWorksLT2012 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksLT2012.bak)|
|[AdventureWorks2008R2 .bak](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks2008r2/adventure-works-2008r2-oltp.bak)| [AdventureWorksDW2008R2 .bak](https://github.com/microsoft/sql-server-samples/releases/download/adventureworks2008r2/adventure-works-2008r2-dw.bak) | 不適用 |

您可以直接在 GitHub 上找到其他檔案： 

- [SQL Server 2014-2019](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks)
- [SQL Server 2012](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks2012)
- [SQL Server 2008 和2008R2](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks2008r2)


## <a name="restore-to-sql-server"></a>還原至 SQL Server 

您可以使用檔案 `.bak` 將範例資料庫還原至 SQL Server 實例。 您可以使用 [RESTORE (transact-sql) ](../t-sql/statements/restore-statements-transact-sql.md) 命令，或使用 [SQL Server Management Studio](../ssms/download-sql-server-management-studio-ssms.md) 或 [Azure Data Studio](../azure-data-studio/download-azure-data-studio.md)中的圖形化介面 (GUI) 。

# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

如果您不熟悉如何使用 SQL Server Management Studio (SSMS) ，可以看到 [[連接 & 查詢]](../ssms/quickstarts/ssms-connect-query-sql-server.md) 以開始使用。 

若要在 SQL Server Management Studio 中還原資料庫，請遵循下列步驟：

1. `.bak`從 [[下載備份檔案](#download-backup-files)] 區段中提供的其中一個連結下載適當的檔案。
2. 將檔案移 `.bak` 至 SQL Server 的備份位置。 這會根據您的安裝位置、實例名稱和 SQL Server 版本而有所不同。 例如，SQL Server 2019 預設實例的預設位置是：

   `C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\Backup`. 

3. 開啟 SQL Server Management Studio (SSMS) 並連接到中的 SQL Server。 
4. 以滑鼠右鍵 **按一下****物件總管**  >  **還原資料庫**] 中的 [資料庫]，以啟動 [**還原資料庫**]。 

   :::image type="content" source="media/adventureworks-install-configure/restore-db-ssms.png" alt-text="顯示如何選擇以滑鼠右鍵按一下 [資料庫] 物件總管然後選取 [還原資料庫] 來還原資料庫的螢幕擷取畫面。":::


1. 選取 [ **裝置** ]，然後選取省略號 **( ... )** 選擇裝置。 
1. 選取 [ **新增** ]，然後選擇 `.bak` 您最近移至這個位置的檔案。 如果您將檔案移到這個位置，但卻無法在嚮導中看到它，這通常表示許可權問題-SQL Server 或登入 SQL Server 的使用者沒有此資料夾中此檔案的許可權。 
1. 選取 **[確定]** 以確認您的資料庫備份選項，然後關閉 [ **選取備份裝置** ] 視窗。 
1. 核取 [**檔案] 索引** 標籤，在 [**還原資料庫**] 嚮導中確認 **還原** 的位置和檔案名符合您預期的位置和檔案名。 
1. 選取 [確定] 以還原您的資料庫。 

   :::image type="content" source="media/adventureworks-install-configure/restore-db-wizard-ssms.png" alt-text="顯示 [還原資料庫] 視窗的螢幕擷取畫面，其中已醒目提示要還原的備份組，並已呼叫 [確定] 選項。":::

如需還原 SQL Server 資料庫的詳細資訊，請參閱 [使用 SSMS 還原資料庫備份](../relational-databases/backup-restore/restore-a-database-backup-using-ssms.md)。

# <a name="transact-sql-t-sql"></a>[Transact-sql (T-sql) ](#tab/tsql)

您可以使用 Transact-sql (T-sql) 來還原範例資料庫。 以下提供還原 AdventureWorks2019 的範例，但資料庫名稱和安裝檔案路徑可能會根據您的環境而有所不同。 

若要將 AdventureWorks2019 還原至 **Windows**，請適當地修改環境的值，然後執行下列 Transact-sql (t-sql) 命令：


```sql
USE [master]
RESTORE DATABASE [AdventureWorks2019] 
FROM  DISK = N'C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\Backup\AdventureWorks2019.bak' 
WITH  FILE = 1,  NOUNLOAD,  STATS = 5
GO

```

若要將 AdventureWorks2019 還原至 **linux**，請將 Windows 檔案系統路徑變更為 linux，然後執行下列 Transact-sql (t-sql) 命令： 


```sql
USE [master]
RESTORE DATABASE [AdventureWorks2019]
FROM DISK = '/var/opt/mssql/backup/AdventureWorks2019.bak'
WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf',
MOVE 'AdventureWorks2017_log' TO '/var/opt/mssql/data/AdventureWorks2019_log.ldf',
FILE = 1,  NOUNLOAD,  STATS = 5
GO
```

# <a name="azure-data-studio"></a>[Azure Data Studio](#tab/data-studio)

如果您不熟悉如何使用 [Azure Data Studio Studio](../azure-data-studio/download-azure-data-studio.md)，您可以看到 [connect & 查詢](../azure-data-studio/quickstart-sql-server.md) 以開始使用

若要在 Azure Data Studio 中還原資料庫，請遵循下列步驟：

1. `.bak`從 [[下載備份檔案](#download-backup-files)] 區段中提供的其中一個連結下載適當的檔案。
1. 將檔案移 `.bak` 至 SQL Server 的備份位置。 這會根據您的安裝位置、實例名稱和 SQL Server 版本而有所不同。 例如，SQL Server 2019 預設實例的預設位置是：

    `C:\Program Files\Microsoft SQL Server\MSSQL15.MSSQLSERVER\MSSQL\Backup`.

1. 開啟 Azure Data Studio Studio，然後連接到您的 SQL Server 實例。
1. 以滑鼠右鍵按一下您的伺服器，然後選取 [ **管理**]。

   :::image type="content" source="media/adventureworks-install-configure/ads-manage.png" alt-text="顯示 Azure Data Studio 醒目提示和呼叫 [管理] 選項的螢幕擷取畫面。":::

1. 選取 **還原**

   :::image type="content" source="media/adventureworks-install-configure/ads-restore-database.png" alt-text="從頂端功能表中選取 [還原]，以還原您的資料庫。":::

1. 在 [ **一般** ] 索引標籤上，填入 [ **來源**] 底下列出的值。
    1. 在 [ **還原來源**] 底下，選取 [ *備份檔案*]。
    1. 在 [ **備份檔案路徑**] 下，選取您儲存 .bak 檔案的位置。 
    
   :::image type="content" source="media/adventureworks-install-configure/ads-source.png" alt-text="選取您的備份檔案路徑":::
    
    這會自動填入其餘的欄位，例如 **資料庫**、 **目標資料庫** 和 **還原至**。 

   :::image type="content" source="media/adventureworks-install-configure/ads-destination-restore-plan.png" alt-text="當您選擇備份檔案路徑之後，其餘的欄位自動填入":::

1. 選取 [ **還原** ] 以還原您的資料庫。 

   :::image type="content" source="media/adventureworks-install-configure/ads-restore.png" alt-text="當您準備好時，請選取 [還原] 以還原您的資料庫。":::

---

## <a name="deploy-to-azure-sql-database"></a>部署至 Azure SQL Database

您有兩個選項可查看範例 Azure SQL Database 資料。 當您建立新的資料庫時，可以使用範例，也可以使用 SQL Server Management Studio (SSMS) ，將資料庫從 SQL Server 直接部署到 Azure。

若要改為取得 Azure SQL 受控執行個體的範例資料，請參閱 [將 World Wide 匯入工具還原至 SQL 受控執行個體](/azure/azure-sql/managed-instance/restore-sample-database-quickstart)。 

### <a name="deploy-new-sample-database"></a>部署新的範例資料庫

當您在 Azure SQL Database 中建立新的資料庫時，您可以選擇建立空白資料庫或範例資料庫。 

遵循下列步驟以使用範例資料庫來建立新的資料庫： 

1. 連接到您的 Azure 入口網站。
1. 選取導覽窗格左上方的 [ **建立資源** ]。 
1. 選取 [ **資料庫** ]，然後選取 [ **SQL Database**]。 
1. 填寫要求的資訊以建立您的資料庫。 
1. 在 [ **其他設定** ] 索引標籤上，選擇 [ **範例** ] 作為 [ **資料來源**] 底下的現有資料： 

   :::image type="content" source="media/adventureworks-install-configure/deploy-sample-to-azure.png" alt-text="當您建立 Azure SQL Database 時，請在 Azure 入口網站的 [其他設定] 索引標籤上選擇 [範例] 作為資料來源":::

1. 選取 [ **建立** ] 以建立新的 SQL Database，也就是 AdventureWorksLT 資料庫的還原複本。 


### <a name="deploy-database-from-sql-server"></a>從 SQL Server 部署資料庫

SQL Server Management Studio 能讓您直接將資料庫部署到 Azure SQL Database。 這個方法目前不會提供資料驗證，因此適用于開發和測試，不應該用於生產環境。 

若要從 SQL Server 將範例資料庫部署至 Azure SQL Database，請遵循下列步驟：

1. 在 SQL Server Management Studio 中連接到您的 SQL Server。 
1. 如果您尚未這麼做，請將 [範例資料庫還原至 SQL Server](#restore-to-sql-server)。 
1. 以滑鼠右鍵按一下您在 [ 將  >    >  **資料庫部署到 Microsoft Azure SQL Database**...] 物件總管工作中還原的資料庫。 

   :::image type="content" source="media/adventureworks-install-configure/deploy-db-to-azure.png" alt-text="選擇以滑鼠右鍵按一下您的資料庫，然後選取 [工作]，將資料庫部署到 Microsoft Azure SQL Database":::

1. 依照嚮導連接到 Azure SQL Database 並部署您的資料庫。 


## <a name="creation-scripts"></a>建立腳本

除了還原資料庫之外，您也可以使用腳本來建立 AdventureWorks 資料庫（不論版本為何）。 

您可以使用下列腳本來建立整個 AdventureWorks 資料庫： 

- [AdventureWorks OLTP 腳本 Zip](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks-oltp-install-script.zip)
- [AdventureWorks DW 腳本 Zip](https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorksDW-data-warehouse-install-script.zip)

您可以在 [GitHub](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks)上找到使用腳本的其他相關資訊。 

## <a name="next-steps"></a>後續步驟

當您還原範例資料庫之後，請使用下列教學課程來開始使用 SQL Server： 


[SQL Server 資料庫引擎的教學課程](../relational-databases/database-engine-tutorials.md)   
[使用 SQL Server Management Studio (SSMS) 進行連線及查詢 ](../ssms/quickstarts/ssms-connect-query-sql-server.md)   
[使用 Azure Data Studio 連接和查詢](../ssms/quickstarts/ssms-connect-query-sql-server.md)