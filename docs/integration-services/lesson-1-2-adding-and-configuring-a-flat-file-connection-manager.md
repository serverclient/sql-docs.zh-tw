---
description: 課程 1-2：新增及設定一般檔案連線管理員
title: 步驟 2:新增及設定一般檔案連線管理員 | Microsoft Docs
ms.custom: ''
ms.date: 01/03/2019
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: tutorial
ms.assetid: 9a77dd32-d8c2-4961-ad37-2a971f9d6043
author: chugugrace
ms.author: chugu
ms.openlocfilehash: 57bf9bd3863e5486c890088324641a5100581a02
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099221"
---
# <a name="lesson-1-2-add-and-configure-a-flat-file-connection-manager"></a>課程 1-2：新增及設定一般檔案連線管理員

[!INCLUDE[sqlserver-ssis](../includes/applies-to-version/sqlserver-ssis.md)]



在這項工作中，您將一般檔案連接管理員加入您剛才建立的封裝中。 一般檔案連接管理員可讓封裝從一般檔案擷取資料。 使用一般檔案連接管理員，您可以指定當封裝從一般檔案擷取資料時，要套用的檔案名稱和位置、地區設定和字碼頁及檔案格式 (包括資料行分隔符號)。 此外，您可以手動指定個別資料行的資料類型，或使用 [建議資料行類型] 對話方塊，將所擷取資料的資料行自動對應至 [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] 資料類型。  
  
您必須為您要使用的每一個檔案格式建立新的「一般檔案」連線管理員。 由於本教學課程會從多個全都具有相同資料格式的一般檔案擷取資料，因此您只需針對範例套件新增及設定一個「一般檔案」連線管理員。  
  
在本教學課程中，您會在「一般檔案」連線管理員中設定下列屬性：  
  
-   **資料行名稱：** 由於一般檔案沒有資料行名稱，因此「一般檔案」連線管理員會建立預設資料行名稱。 這些預設名稱無助於識別每一個資料行所代表的內容。 請將預設名稱變更為符合要載入一般檔案資料之事實資料表的名稱。  
  
-   **資料對應：** 您為「一般檔案」連線管理員指定的資料類型對應，會供所有參考此連線管理員的一般檔案資料來源元件使用。 您可以使用一般檔案連接管理員來手動對應資料類型，或使用 [建議資料行類型] 對話方塊。 在此工作中，您會檢視在 [建議資料行類型] 對話方塊中建議的對應，然後在 [一般檔案連線管理員編輯器] 對話方塊中手動建立必要的對應。  
  
> [!NOTE]
> 一般檔案連接管理員提供有關資料檔的地區設定資訊。 如果電腦未設定為使用 [英文 (美國)] 地區選項，您就必須在 [一般檔案連線管理員編輯器] 對話方塊中設定額外的屬性。  
  
## <a name="add-a-flat-file-connection-manager-to-the-ssis-package"></a>將一般檔案連線管理員新增至 SSIS 套件  
  
1.  在 [方案總管] 窗格中，於 [連線管理員] 上按一下滑鼠右鍵，然後選取 [新增連線管理員]。
1. 在 [加入 SSIS 連線管理員] 對話方塊中，依序選取 [FLATFILE] 和 [加入]。
  
2.  在 [一般檔案連線管理員編輯器] 對話方塊中，針對 [連線管理員名稱] 輸入 **範例一般檔案來源資料**。  
  
3.  選取 [瀏覽]。  
  
4.  在 [開啟] 對話方塊中，尋找電腦上的 **SampleCurrencyData.txt** 檔案。  
  
5.  清除 [第一個資料列的資料行名稱] 核取方塊。  
  
### <a name="set-locale-sensitive-properties"></a>選取區分地區設定的屬性  
  
1.  在 [一般檔案連線管理員編輯器] 對話方塊中，選取 [一般]。  
  
2.  將 [地區設定] 設定為 [英文 (美國)]，將 [字碼頁] 設定為 [1252]。  
  
### <a name="rename-columns-in-the-flat-file-connection-manager"></a>將一般檔案連線管理員中的資料行重新命名  
  
1.  在 [一般檔案連線管理員編輯器] 對話方塊中，選取 [進階]。  
  
2.  在屬性窗格中，進行下列變更：  
  
    -   將 [資料行 0] 名稱屬性變更為 **AverageRate**。  
  
    -   將 [資料行 1] 名稱屬性變更為 **CurrencyID**。  
  
    -   將 [資料行 2] 名稱屬性變更為 **CurrencyDate**。  
  
    -   將 [資料行 3] 名稱屬性變更為 **EndOfDayRate**。  
  
### <a name="remap-column-data-types"></a>重新對應資料行資料類型  
  
根據預設，所有 4 個資料行一開始是設為字串資料類型 [DT_STR]，且 **OutputColumnWidth** 為 50。  

1.  在 [一般檔案連線管理員編輯器] 對話方塊中，選取 [建議類型]。  
  
    [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] 會依據最前面的 200 個資料列，自動建議合適的資料類型。 您也可以變更這些建議選項，以增加或減少取樣資料、指定整數或布林資料的預設資料類型，或是加入空格以填補字串資料行。  
  
    目前，請勿對 [建議資料行類型] 對話方塊中的選項進行任何變更，然後選取 [確定]，讓 [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] 為資料行建議資料類型。 此動作會讓您回到 [一般檔案連線管理員編輯器] 對話方塊的 [進階] 窗格，您可以在此檢視 [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] 所建議的資料行資料類型。 或者，如果您選取 [取消]，系統就不會對資料行中繼資料提出任何建議，而會使用預設的字串 (DT_STR) 資料類型。  
  
    在這個教學課程中， [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] 會對 SampleCurrencyData.txt 檔案的資料建議下列資料表中第二個資料行所顯示的資料類型。 第四個資料行提供目的地中資料行所需的資料類型，在後續步驟中將會定義。  
  
    |一般檔案資料行|建議類型|目的地資料行|目的地類型|  
    |--------------------|------------------|----------------------|--------------------|  
    |AverageRate|float [DT_R4]|FactCurrencyRate.AverageRate|FLOAT|  
    |CurrencyID|string [DT_STR]|DimCurrency.CurrencyAlternateKey|nchar(3)|  
    |CurrencyDate|date [DT_DATE]|DimDate.FullDateAlternateKey|date|  
    |EndOfDayRate|float [DT_R4]|FactCurrencyRate.EndOfDayRate|FLOAT|  
  
    對 [CurrencyID] 資料行建議的資料類型與目的地資料表中的欄位資料類型不相容。 因為 `DimCurrency.CurrencyAlternateKey` 的資料類型是 nchar(3)，所以 [CurrencyID] 必須從 [DT_STR] 字串變更為 [DT_WSTR] Unicode 字串。 另外，`DimDate.FullDateAlternateKey` 欄位是定義為 date 資料類型，因此 [CurrencyDate] 的類型必須從 [DT_Date] 日期變更為 [DT_DBDATE] 資料庫日期。  
  
2.  在此清單中，選取 [CurrencyID] 資料行，然後在屬性窗格中，將 [CurrencyID] 資料行的 [資料類型] 從 [DT_STR] 字串變更為 [DT_WSTR] Unicode 字串。  
  
3.  在屬性窗格中，將資料行 [CurrencyDate] 的資料類型從日期 [DT_DATE] 變更為資料庫日期 [DT_DBDATE]。  
  
4.  選取 [確定]  。  
  
## <a name="go-to-next-task"></a>移至下一個工作
[步驟 3：新增及設定 OLE DB 連線管理員](../integration-services/lesson-1-3-adding-and-configuring-an-ole-db-connection-manager.md)  
  
## <a name="see-also"></a>請參閱  
[一般檔案連接管理員](../integration-services/connection-manager/flat-file-connection-manager.md)  
[Integration Services 資料類型](../integration-services/data-flow/integration-services-data-types.md)  
  
  
  
