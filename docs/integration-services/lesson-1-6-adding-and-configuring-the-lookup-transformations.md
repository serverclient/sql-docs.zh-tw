---
description: 課程 1-6：新增及設定查閱轉換
title: 步驟 6：新增及設定查閱轉換 | Microsoft Docs
ms.custom: ''
ms.date: 03/19/2019
ms.prod: sql
ms.technology: integration-services
ms.topic: tutorial
ms.assetid: 5c59f723-9707-4407-80ae-f05f483cf65f
author: chugugrace
ms.author: chugu
ms.reviewer: ''
ms.openlocfilehash: dbfe5a2fd1472ce67edddeb2eff8a223352dccce
ms.sourcegitcommit: 1f826eb3f73bd4d94bc9638b9cdd60991a2e2fa0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98125608"
---
# <a name="lesson-1-6-add-and-configure-the-lookup-transformations"></a>課程 1-6：新增及設定查閱轉換

[!INCLUDE[sqlserver-ssis](../includes/applies-to-version/sqlserver-ssis.md)]



在設定「一般檔案」來源以從來源檔案擷取資料之後，您需定義取得 **CurrencyKey** 和 **DateKey** 值所需的「查閱」轉換。 查閱轉換是藉由聯結指定輸入資料行中的資料與參考資料集內的資料行來執行查閱。 參考資料集可以是現有的資料表或檢視、新資料表，或 SQL 陳述式的結果。 在本教學課程中，「查閱」轉換會使用 OLE DB 連線管理員，以連線到包含參考資料集之來源資料的資料庫。  
  
> [!NOTE]  
> 您也可以將查閱轉換設定為連接到包含參考資料集的快取。 如需詳細資訊，請參閱[查閱轉換](../integration-services/data-flow/transformations/lookup-transformation.md)。  
  
在此工作中，您會在套件中新增及設定下列兩個「查閱」轉換元件：  
  
-   一個轉換會根據一般檔案中相符的 **CurrencyID** 資料行值，查閱 **DimCurrency** 維度資料表之 **CurrencyKey** 資料行的值。  
  
-   一個轉換會根據一般檔案中相符的 **CurrencyDate** 資料行值，查閱 **DimDate** 維度資料表之 **DateKey** 資料行的值。  
  
在這兩種情況下，「查閱」轉換都會使用您先前建立的 OLE DB 連線管理員。  
  
## <a name="add-and-configure-the-lookup-currency-key-transformation"></a>新增及設定查閱貨幣索引鍵轉換  
  
1.  在 [SSIS 工具箱] 中，展開 [通用]，然後將 [查閱] 拖曳至 [資料流程] 索引標籤的設計介面中。將 [查閱] 直接放在 [擷取範例貨幣資料] 來源下面。  
  
2.  選取 [擷取範例貨幣資料] 一般檔案來源，然後將其藍色箭頭拖曳至剛新增的 [查閱] 轉換，以連接這兩個元件。  
  
3.  在 [資料流程] 設計介面中，於 [查閱] 轉換中選取 [查閱]，然後將名稱變更為 **查閱貨幣索引鍵**。  
  
4.  按兩下 [查閱貨幣索引鍵] 轉換，以顯示 [查閱轉換編輯器]。  
  
5.  在 [一般] 頁面上，進行下列選擇：  
  
    1.  選取 [完整快取]。  
  
    2.  在 **[連接類型]** 區域中，選取 **[OLE DB 連接管理員]**。  
  
6.  在 [連接] 頁面上，進行下列選擇：  
  
    1.  在 [OLE DB 連接管理員] 對話方塊中，確定 [localhost.AdventureWorksDW2012] 已顯示。  
  
    2.  選取 [使用 SQL 查詢的結果]，然後輸入或貼上下列 SQL 陳述式：  
  
        ```sql
        SELECT * FROM [Sales].[Currency]
        WHERE [CurrencyCode]
        IN ('ARS', 'AUD', 'BRL', 'CAD', 'CNY',
            'DEM', 'EUR', 'FRF', 'GBP', 'JPY',
            'MXN', 'SAR', 'USD', 'VEB')
        ```  
    3.  選取 [預覽] 以確認查詢結果。
  
7.  在 [資料行] 頁面上，進行下列選擇：  
  
    1.  在 [可用的輸入資料行] 面板中，將 [CurrencyID] 拖曳至 [可用的查閱資料行] 面板，並將它放在 [CurrencyAlternateKey] 中。  
  
    2.  在 [可用的查閱資料行] 清單中，選取 [CurrencyKey] 左邊的核取方塊。  
  
8.  選取 [確定]以返回 [資料流程] 設計介面。  
  
9. 在 [查閱貨幣索引鍵] 轉換上按一下滑鼠右鍵，然後選取 [屬性]。  
  
10. 在 [屬性] 視窗中，確認 [LocaleID] 屬性是 [英文 (美國)]，[DefaultCodePage] 屬性是 [1252]。  
  
## <a name="add-and-configure-the-lookup-date-key-transformation"></a>新增及設定查閱日期索引鍵轉換  
  
1.  在 [SSIS 工具箱] 中，將 [查閱] 拖曳至 [資料流程] 設計介面中。 將這個 [查閱] 直接放在 [查閱貨幣索引鍵] 轉換下面。  
  
2.  選取 [查閱貨幣索引鍵] 轉換，然後將其藍色箭頭拖曳至新的 [查閱] 轉換，以連接這兩個元件。  
  
3.  在 [輸入輸出選擇] 對話方塊的 [輸出] 清單方塊中，選取 [查閱比對輸出]，然後選取 [確定]。  
  
4.  在 [資料流程] 設計介面上，於剛新增的 [查閱] 轉換中選取 [查閱] 名稱，然後將名稱變更為 **查閱日期索引鍵**。  
  
5.  按兩下 [Lookup Date Key (查閱日期索引鍵)] 轉換。  
  
6.  在 [一般] 頁面上，選取 [部分快取]。  
  
7.  在 [連接] 頁面上，進行下列選擇：  
  
    1.  在 [OLE DB 連線管理員] 對話方塊中，確定已顯示 **localhost.AdventureWorksDW2012**。  
  
    2.  在 [使用資料表或檢視] 方塊中，輸入或選取 **[dbo].[DimDate]**。  
  
8.  在 [資料行] 頁面上，進行下列選擇：  
  
    1.  在 [可用的輸入資料行] 面板中，將 [CurrencyDate] 拖曳至 [可用的查閱資料行] 面板，並將它放在 [FullDateAlternateKey] 中。  如果您看到指出資料類型不符的訊息，請將 CurrencyDate 的資料類型變更為 [DT_DBDATE]。
  
    2.  在 [可用的查閱資料行] 清單中，選取 [DateKey] 左邊的核取方塊。  
  
9. 在 [進階] 頁面上，檢閱快取選項。  
  
10. 選取 [確定]以返回 [資料流程] 設計介面。  
  
11. 在 [查閱日期索引鍵] 轉換上按一下滑鼠右鍵，然後選取 [屬性]。
  
12. 在 [屬性] 視窗中，確認 [LocaleID] 屬性是 [英文 (美國)]，[DefaultCodePage] 屬性是 [1252]。  
  
## <a name="go-to-next-task"></a>移至下一個工作
[步驟 7：新增及設定 OLE DB 目的地](../integration-services/lesson-1-7-adding-and-configuring-the-ole-db-destination.md)  
  
## <a name="see-also"></a>請參閱  
[查閱轉換](../integration-services/data-flow/transformations/lookup-transformation.md)  
