---
title: SQL 資料探索與分類 | Microsoft Docs
description: SQL 資料探索與分類
documentationcenter: ''
ms.reviewer: vanto
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.prod_service: sql-database,sql
ms.custom: security
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: datrigan
author: DavidTrigano
ms.openlocfilehash: eab3c943f1191c2d52f6d476c1e0a83b74b6f0df
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97638044"
---
# <a name="sql-data-discovery-and-classification"></a>SQL 資料探索與分類
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

資料探索與分類引進內建至 [SQL Server Management Studio (SSMS)](../../ssms/download-sql-server-management-studio-ssms.md) 的新工具，可用來 **探索**、**分類**、**標記** & **報告** 您資料庫中的敏感性資料。
探索和分類最敏感的資料 (商務、財務、醫療等等) 可以扮演組織資訊保護成長的關鍵角色。 它可以作為下列的基礎結構：
* 協助符合資料隱私權標準。
* 控制存取以及強化包含高敏感性資料之資料庫/資料行的安全性。

> [!NOTE]
> 資料探索與分類由 **SQL Server 2012 和更新版本所支援，且可搭配 [SSMS 17.5](../../ssms/download-sql-server-management-studio-ssms.md) 或更新版本使用**。 針對 Azure SQL Database，請參閱 [Azure SQL Database 的資料探索與分類](/azure/sql-database/sql-database-data-discovery-and-classification/)。

## <a name="overview"></a><a id="subheading-1"></a>概觀
資料探索與分類引進一組進階服務，形成目標為保護資料的新 SQL Information Protection 範例，而不只是資料庫：

* **探索與建議** - 分類引擎會掃描您的資料庫，並識別包含潛在敏感性資料的資料行。 它接著可讓您輕鬆地檢閱並套用適當的分類建議，以及手動分類資料行。
* **標記** - 在資料行上可以持續標示敏感度分類標籤。
* **可見性** - 資料庫分類狀態可以在詳細報表中進行檢視，而詳細報表可以進行列印/匯出，以用於合規性和稽核用途，以及其他需要。

## <a name="discovering-classifying--labeling-sensitive-columns"></a><a id="subheading-2"></a>探索、分類和標示敏感資料行
下節所描述的步驟是有關探索、分類和標示包含您資料庫中敏感性資料的資料行，以及檢視資料庫的目前分類狀態，並匯出報表。

分類包含兩個中繼資料屬性：
* 標籤 - 主分類屬性，用來定義資料行中所儲存資料的敏感度等級。  
* 資訊類型 - 提供資料行中所儲存資料類型的額外細微性。

**分類 SQL Server 資料庫：**

1. 在 SQL Server Management Studio (SSMS) 中，連線至 SQL Server。

2. 在 SSMS 物件總管中，以滑鼠右鍵按一下您想要分類的資料庫，然後選擇 [工作]   > [資料探索與分類]   > [分類資料]  。

   ![顯示 [SSMS 物件總管] 的螢幕擷取畫面，其中已選取 [工作] > [資料探索與分類] > [將資料分類...]。][0]

3. 分類引擎會掃描您資料庫中是否有資料行包含潛在敏感度資料，並提供 **建議的資料行分類** 清單：

    * 若要檢視建議的資料行分類清單，請按一下頂端的建議通知方塊或視窗底部的建議面板：

        ![顯示通知的螢幕擷取畫面，其指出我們找到 39 個資料行含有分類建議。 請按一下這裡進行檢視。][2]

        ![顯示通知的螢幕擷取畫面，其指出有 39 個資料行含有分類建議 (按一下以檢視)。][3]

    * 檢閱建議清單：
        * 若要接受特定資料行的建議，請核取相關資料列左側資料行中的核取方塊。 您也可以核取建議資料表標頭中的核取方塊，以將「所有建議」標記為已接受。

        * 您也可以使用下拉式方塊來變更建議的「資訊類型」和「敏感度標籤」。        

        ![顯示建議清單的螢幕擷取畫面。][4]

    * 若要套用選取的建議，請按一下藍色的 [Accept selected recommendations]\(接受選取的建議) 按鈕。若要套用選取的建議，請按一下藍色的 接受選取的建議按鈕。

        ![[接受選取的建議] 按鈕的螢幕擷取畫面。][5]

4. 您也可以 **手動分類** 資料行作為 (以及) 建議分類的替代方法：

    * 按一下視窗上方功能表中的 [新增分類]。

        ![顯示頂端功能表的螢幕擷取畫面，其中已標註 [新增分類] 選項。][6]

    * 在開啟的內容視窗中，選取結構描述 > 資料表 > 您想要分類的資料行，以及資訊類型和敏感度標籤。 然後按一下內容視窗底部的藍色 [新增分類] 按鈕。

        ![顯示 [新增分類] 內容視窗的螢幕擷取畫面。][7]

5. 若要完成您的分類，並使用新的分類中繼資料持續標示 (標記) 資料庫資料行，請按一下視窗上方功能表中的 [儲存]。

    ![顯示頂端功能表的螢幕擷取畫面，其中已標註 [儲存] 選項。][8]


6. 若要產生具有完整資料庫分類狀態摘要的報表，請按一下視窗上方功能表中的 [檢視報告]。 (您也可以使用 SSMS 產生報告。 以滑鼠右鍵按一下您想要產生報告的資料庫，然後選擇 [工作] > [資料探索與分類] > [產生報告])

    ![顯示頂端功能表的螢幕擷取畫面，其中已標註 [檢視報表] 選項。][9]

    ![顯示 [SQL 資料分類報表] 的螢幕擷取畫面。][10]

## <a name="manage-information-protection-policy-with-ssms"></a><a id="subheading-3"></a>搭配 SSMS 管理資訊保護原則

您可以使用 [SSMS 18.4](../../ssms/download-sql-server-management-studio-ssms.md) 或更新版本來管理資訊保護原則：

1. 在 SQL Server Management Studio (SSMS) 中，連線至 SQL Server。

2. 在 SSMS 物件總管中，以滑鼠右鍵按一下您的其中一個資料庫，然後選擇 [工作] > [資料探索與分類]。

   下列功能表選項可讓您管理資訊保護原則：

* **設定資訊保護原則檔案**：使用在所選取 JSON 檔案中所定義的資訊保護原則。

* **匯出資訊保護原則**：將資訊保護原則匯出為 JSON 檔案。

* **重設資訊保護原則**：將資訊保護原則重設為預設資訊保護原則。

> [!IMPORTANT]
> 資訊保護原則檔案不會儲存在 SQL Server 中。
> SSMS 會使用預設資訊保護原則。 如果自訂的資訊保護原則失敗，SSMS 便無法使用預設原則。 資料分類失敗。 若要解決此問題，請按一下 [重設資訊保護原則] 以使用預設原則，然後重新啟用資料分類。

## <a name="accessing-the-classification-metadata"></a><a id="subheading-4"></a>存取分類中繼資料

SQL Server 2019 引進 [`sys.sensitivity_classifications`](../system-catalog-views/sys-sensitivity-classifications-transact-sql.md) 系統目錄檢視。 此檢視會傳回資訊類型和敏感度標籤。 

> [!NOTE]
> 此檢視需要 **VIEW ANY SENSITIVITY CLASSIFICATION** 權限。 如需相關資訊，請參閱 [Metadata Visibility Configuration](./metadata-visibility-configuration.md)。

在 SQL Server 2019 執行個體上，查詢 `sys.sensitivity_classifications` 以檢閱所有分類資料行，以及其對應的分類。 例如： 

```sql
SELECT 
    schema_name(O.schema_id) AS schema_name,
    O.NAME AS table_name,
    C.NAME AS column_name,
    information_type,
    label,
    rank,
    rank_desc
FROM sys.sensitivity_classifications sc
    JOIN sys.objects O
    ON  sc.major_id = O.object_id
    JOIN sys.columns C 
    ON  sc.major_id = C.object_id  AND sc.minor_id = C.column_id
```

在 SQL Server 2019 之前，資訊類型和敏感度標籤的分類中繼資料位於下列擴充屬性中： 

* `sys_information_type_name`
* `sys_sensitivity_label_name`

您可以使用擴充屬性目錄檢視 [`sys.extended_properties`](../system-catalog-views/extended-properties-catalog-views-sys-extended-properties.md) 來存取中繼資料。

針對 SQL Server 2017 及較舊版本的執行個體，下列範例會傳回所有分類資料行，以及其對應的分類：

```sql
SELECT
    schema_name(O.schema_id) AS schema_name,
    O.NAME AS table_name,
    C.NAME AS column_name,
    information_type,
    sensitivity_label 
FROM
    (
        SELECT
            IT.major_id,
            IT.minor_id,
            IT.information_type,
            L.sensitivity_label 
        FROM
        (
            SELECT
                major_id,
                minor_id,
                value AS information_type 
            FROM sys.extended_properties 
            WHERE NAME = 'sys_information_type_name'
        ) IT 
        FULL OUTER JOIN
        (
            SELECT
                major_id,
                minor_id,
                value AS sensitivity_label 
            FROM sys.extended_properties 
            WHERE NAME = 'sys_sensitivity_label_name'
        ) L 
        ON IT.major_id = L.major_id AND IT.minor_id = L.minor_id
    ) EP
    JOIN sys.objects O
    ON  EP.major_id = O.object_id 
    JOIN sys.columns C 
    ON  EP.major_id = C.object_id AND EP.minor_id = C.column_id
```

## <a name="manage-classifications"></a><a id="subheading-5"></a>管理分類

# <a name="t-sql"></a>[T-SQL](#tab/t-sql)
您可以使用 T-SQL 新增/移除資料行分類，以及擷取整個資料庫的所有分類。

- 新增/更新一或多個資料行的分類：[ADD SENSITIVITY CLASSIFICATION](../../t-sql/statements/add-sensitivity-classification-transact-sql.md)
- 從一或多個資料行移除分類：[DROP SENSITIVITY CLASSIFICATION](../../t-sql/statements/drop-sensitivity-classification-transact-sql.md)

# <a name="powershell-cmdlet"></a>[PowerShell Cmdlet](#tab/sql-powelshell)
您可以使用 PowerShell Cmdlet 來新增/移除資料行分類，以及擷取所有分類並取得整個資料庫的建議。

- [Get-SqlSensitivityClassification](/powershell/module/sqlserver/Get-SqlSensitivityClassification) \(英文\)
- [Get-SqlSensitivityRecommendations](/powershell/module/sqlserver/Get-SqlSensitivityRecommendations) \(英文\)
- [Set-SqlSensitivityClassification](/powershell/module/sqlserver/Set-SqlSensitivityClassification) \(英文\)
- [Remove-SqlSensitivityClassification](/powershell/module/sqlserver/Remove-SqlSensitivityClassification) \(英文\)

## <a name="next-steps"></a><a id="subheading-6"></a>後續步驟

針對 Azure SQL Database，請參閱 [Azure SQL Database 的資料探索與分類](/azure/azure-sql/database/data-discovery-and-classification-overview)。

請考慮套用資料行層級安全性機制來保護敏感資料行：

* [動態資料遮罩](./dynamic-data-masking.md)以模糊化使用中的敏感資料行。
* [Always Encrypted](./encryption/always-encrypted-database-engine.md) 以加密靜止的敏感資料行。

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Manage information protection policy with SSMS]: #subheading-3
[Accessing the classification metadata]: #subheading-4
[Manage classifications]: #subheading-5
[Next Steps]: #subheading-6

<!--Image references-->

[0]: ./media/sql-data-discovery-and-classification/0-data-classification-explorer.png
[2]: ./media/sql-data-discovery-and-classification/2-recommendations-notification-box.png
[3]: ./media/sql-data-discovery-and-classification/3-recommendations-panel.png
[4]: ./media/sql-data-discovery-and-classification/4-recommendations.png
[5]: ./media/sql-data-discovery-and-classification/5-accept-recommendations-button.png
[6]: ./media/sql-data-discovery-and-classification/6-add-classification-button.png
[7]: ./media/sql-data-discovery-and-classification/7-manual-classification.png
[8]: ./media/sql-data-discovery-and-classification/8-save.png
[9]: ./media/sql-data-discovery-and-classification/9-view-report.png
[10]: ./media/sql-data-discovery-and-classification/10-report.png