---
description: 剖析資料
title: 剖析資料 | Microsoft Docs
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
helpviewer_keywords:
- parsing [Integration Services]
- data parsing [Integration Services]
ms.assetid: 8893ea9d-634c-4309-b52c-6337222dcb39
author: chugugrace
ms.author: chugu
ms.openlocfilehash: 905c5988860aef1a2e495bb17d98a48eaf91c5ce
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98594833"
---
# <a name="parsing-data"></a>剖析資料

[!INCLUDE[sqlserver-ssis](../../includes/applies-to-version/sqlserver-ssis.md)]


  封裝中的資料流程會在異質資料存放區之間擷取和載入資料，這樣可以使用各種不同的標準和自訂資料類型。 在一個資料流程中， [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 來源執行擷取資料、剖析字串資料並將資料轉換為 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 資料類型的工作。 後續轉換可以剖析資料以便將其轉換成不同的資料類型，或者建立資料類型不同的資料行副本。 元件中使用的運算式同樣可以將引數和運算元轉換成不同的資料類型。 最後，當資料載入資料存放區時，目的地則可以剖析資料以便將其轉換成目的地使用的資料類型。 如需詳細資訊，請參閱 [Integration Services 資料類型](../../integration-services/data-flow/integration-services-data-types.md)。  
  
## <a name="two-types-of-parsing"></a>兩種剖析類型  
 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 提供兩種用於轉換資料的剖析類型：快速剖析和標準剖析。  
  
-   快速剖析是一組快速而簡單的剖析常式，它不支援地區設定特定的資料類型轉換，僅支援最常用的日期和時間格式。 
  
-   標準剖析是一組相當豐富的剖析常式，它支援由 Oleaut32.dll 和 Ole2dsip.dll 中可用的「自動」資料類型轉換 API 所提供的所有資料類型轉換。   
  
## <a name="fast-parse"></a>Fast Parse
快速剖析提供一組快速、簡單的常式用以剖析資料。 這些常式並不區分地區設定，而且只支援一組日期、時間和整數格式的子集。  
  
### <a name="requirements-and-limitations"></a>需求及限制  
 實作快速剖析時，封裝將無法解譯使用特定地區設定格式與許多常用之 ISO 8601 基本格式與擴充格式的日期、時間和數值資料，但卻可以提高封裝的效能。 例如，快速剖析僅支援最常用的日期格式表示法 (例如 YYYYMMDD 和 YYYY-MM-DD)，不會執行地區設定特定的剖析，也不會識別貨幣資料中的特殊字元，且無法轉換以十六進位表示或以科學記號表示的整數。  
  
 只有在使用「一般檔案」來源或「資料轉換」時，才能夠使用快速剖析。 效能的提高可能會很顯著，因此如果可能，您應該考慮在這些資料流程元件中使用快速剖析。  
  
 如果封裝中的資料流程需要區分地區設定的剖析，則建議使用標準剖析來代替快速剖析。 例如，快速剖析不會識別區分地區設定的資料，包括十進位符號 (例如逗號)、非年-月-日格式的日期格式以及貨幣符號。  
  
 快速剖析不會識別隱含一或多個日期部分 (例如紀元、年或月) 的截斷表示法。 例如，快速剖析無法識別以隱含紀元方式指定年份和月份的 '**-YYMM**' 格式，也無法識別以隱含年份方式指定月份的 '**--MM**' 格式。 不過，可以識別已降低有效位數的某些表示法。 例如，快速剖析可識別僅指出小時和分鐘的「hhmm;」格式，以及僅指出年份的「**YYYY**」格式。  
  
 快速剖析在資料行層級指定。 在「一般檔案」來源和「資料轉換」轉換中，可以在輸出資料行上指定「快速剖析」。 輸入和輸出均可包含區分地區設定和不區分地區設定的資料行。  
 
## <a name="numeric-data-formats-fast-parse"></a>數值資料格式 (快速剖析)
快速剖析提供一組快速、簡易且區分區域設定的常式集，以剖析資料。 快速剖析僅支援整數資料類型的有限格式集。  
  
### <a name="integer-data-type"></a>整數資料類型
 [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] 所提供的整數資料類型為 DT_I1、DT_UI1、DT_I2、DT_UI2、DT_I4、DT_UI4、DT_I8 及 DT_UI8。 如需詳細資訊，請參閱 [Integration Services 資料類型](../../integration-services/data-flow/integration-services-data-types.md)。  
  
 快速剖析支援整數資料類型的下列格式：  
  
-   含或不含開頭與尾端空格或定位點。 例如，值「  123  」為有效。 全部為空格的值會評估為零。  
  
-   開頭正號、負號或均不含。 例如，值 +123、-123 及 123 有效。  
  
-   一個或多個印度-阿拉伯數字 (0-9)。 例如，值 345 有效。 不支援其他語言的數字。  
  
 不支援的資料格式包括：  
  
-   特殊字元。 例如，不支援貨幣字元 $，因此無法剖析值 $20。  
  
-   空白字元，如換行字元、歸位字元及不中斷空格。 例如，無法剖析值「&#xA0;</ph>123」。  
  
-   整數的十六進位表示法。 例如，無法剖析值 2EE。  
  
-   整數的科學記號標記法。 例如，無法剖析值 1E+10。  
  
 下列格式是整數的輸出資料格式：  
  
-   減號代表負數，不含符號則代表正數。  
  
-   無空白。  
  
-   一個或多個印度-阿拉伯數字 (0-9)。  

## <a name="date-and-time-formats-fast-parse"></a>日期和時間格式 (快速剖析)
快速剖析提供一組快速、簡單的常式用以剖析資料。 快速剖析可支援下列格式的日期和時間資料類型。  
  
### <a name="date-data-type"></a>日期資料類型 
 快速剖析支援下列字串格式的日期資料：  
  
-   包含開頭空白字元的日期格式。 例如，值 " 2004- 02-03"為有效。  
  
-   ISO 8601 格式，如下表中所列：  
  
    |格式|描述|  
    |------------|-----------------|  
    |YYYYMMDD<br /><br /> YYYY-MM-DD|四位數年份、兩位數月份和兩位數天數的基本格式與擴充格式。 在擴充格式中，日期各部份以連字號 (-) 分隔。|  
    |YYYY-MM|降低有效位數的四位數年份和兩位數月份之基本格式與擴充格式。 在擴充格式中，日期各部份以連字號 (-) 分隔。|  
    |YYYY|降低有效位數的格式為四位數年份。|  
  
 快速剖析不支援下列格式的日期資料：  
  
-   字母月份值。 例如，日期格式 Oct-31-2003 無效。  
  
-   模稜兩可的格式，例如 DD-MM-YYYY 和 MM-DD-YYYY。 例如，日期 03-04-1995 和日期 04-03-1995 無效。  
  
-   四位數日曆年度和一年中三位數天數的基本截斷格式與擴充截斷格式 (YYYYDDD 和 YYYY-DDD)。  
  
-   四位數年份、一年中兩位數週數和一週中一位數天數的基本格式與擴充格式 (YYYYWwwD 和 YYYY-Www-D)。  
  
-   年份和週日期的基本截斷格式與擴充截斷格式為四位數年份和兩位數週數 (YYYWww 和 YYYY-Www)。  
  
 快速剖析會以 DT_DBDATE 輸出資料。 將填補以截斷格式表示的日期值。 例如，YYYY 會變為 YYYY0101。  
  
 如需詳細資訊，請參閱 [Integration Services 資料類型](../../integration-services/data-flow/integration-services-data-types.md)。  
  
### <a name="time-data-type"></a>時間資料類型
 快速剖析支援下列字串格式的時間資料：  
  
-   包含開頭空白字元的時間格式。 例如，值「 10:24」為有效。  
  
-   24 小時格式。 快速剖析不支援 AM 和 PM 標記法。  
  
-   ISO 8601 時間格式，如下表中所列：  
  
    |格式|描述|  
    |------------|-----------------|  
    |HHMISS<br /><br /> HH:MI:SS|四位數小時、兩位數分鐘和兩位數秒鐘的基本格式與擴充格式。 在擴充格式中，時間各部份以冒號 (:) 分隔。|  
    |HHMI<br /><br /> HH:MI|兩位數小時和兩位數分鐘之基本截斷格式與擴充截斷格式。 在擴充格式中，時間各部份以冒號 (:) 分隔。|  
    |HH|兩位數小時的截斷格式。|  
    |00:00:00<br /><br /> 000000<br /><br /> 0000<br /><br /> 00<br /><br /> 240000<br /><br /> 24:00:00<br /><br /> 2400<br /><br /> 24|午夜格式。|  
  
-   指定時區的時間格式，如下表中所列：  
  
    |格式|描述|  
    |------------|-----------------|  
    |+HH:MI<br /><br /> +HHMI|基本格式及擴充格式，指出要加入至「國際標準時間」(UTC) 以取得本地時間的小時和分鐘數。|  
    |-HH:MI<br /><br /> -HHMI|基本格式及擴充格式，指出要從 UTC 減去以取得本地時間的小時和分鐘數。|  
    |+HH|截斷格式，指出要加入至 UTC 以取得本地時間的小時數。|  
    |-HH|截斷格式，指出要從 UTC 減去以取得本地時間的小時數。|  
    |Z|0 的值，指出時間是以 UTC 表示。|  
  
     所有時間和日期/時間資料的格式都可以包含時區元素。 不過，除非資料是 DT_DBTIMESTAMPOFFSET 類型，否則系統會忽略時區值。 如需詳細資訊，請參閱 [Integration Services 資料類型](../../integration-services/data-flow/integration-services-data-types.md)。  
  
     在包含時區元素的格式中，在時間元素和時區元素之間沒有空格，如以下範例所示：  
  
     HH:MI:SS[+HH:MI]  
  
     前述範例中的方括號表示時區值是選擇性的。  
  
-   包含小數的時間格式，如下表中所列：  
  
    |格式|描述|  
    |------------|-----------------|  
    |HH[.nnnnnnn]|n 是介於 0 和 9999999 之間且代表小數時數的值。 方括號表示此值是選擇性的。<br /><br /> 例如，值 12.750 表示 12:45。|  
    |HHMI[.nnnnnnn]<br /><br /> HH:MI[.nnnnnnn]|n 是介於 0 和 9999999 之間且代表小數分鐘數的值。 方括號表示此值是選擇性的。<br /><br /> 例如，值 1220.500 表示 12:20:30。|  
    |HHMISS[.nnnnnnn]<br /><br /> HH:MI:SS[.nnnnnnn]|n 是介於 0 和 9999999 之間且代表小數秒數的值。 方括號表示此值是選擇性的。<br /><br /> 例如，值 122040.250 表示 12:20:40.15。|  
  
    > [!NOTE]  
    >  前述表格中時間格式的小數分隔符號可以是小數點或逗號。  
  
-   包含閏秒 (Leap Second) 的時間值，如下列範例所示：  
  
     23:59:60[.0000000]  
  
     235960[.0000000]  
  
 快速剖析會以 DT_DBTIME 和 DT_DBTIME2 輸出字串。 將填補以截斷格式表示的時間值。 例如，HH:MI 會變為 HH:MM:00.000。  
  
 如需詳細資訊，請參閱 [Integration Services 資料類型](../../integration-services/data-flow/integration-services-data-types.md)。  
  
### <a name="datetime-data-type"></a>日期/時間資料類型  
 快速剖析支援下列字串格式的日期/時間資料：  
  
-   包含開頭空白字元的格式。 例如，值 "  2003-01-10T203910" 有效。  
  
-   有效日期格式和有效時間格式的組合會以大寫的 T 及有效的時區格式分隔，例如 YYYYMMDDT[HHMISS][+HH:MI]。 時間和時區值不是必要值。 例如，"2003-10-14" 是有效值。  
  
 快速剖析不支援時間間隔。 例如，格式為 YYYYMMDDThhmmss/YYYYMMDDThhmmss 之開始日期和時間以及結束日期和時間所識別的時間間隔將無法剖析。  
  
 快速剖析會以 DT_DATE、DT_DBTIMESTAMP、DT_DBTIMESTAMP2 和 DT_DBTIMESTAMPOFFSET 輸出字串。 將填補以截斷格式表示的日期/時間值。 下表列出針對遺失的日期和時間部分而加入的值。  
  
|日期/時間部分|填補|  
|---------------------|-------------|  
|秒|加入 00。|  
|分鐘|加入 00:00。|  
|小時|加入 00:00:00。|  
|天|加入 01 做為此月的某個日期。|  
|月|加入 01 做為此年份的某個月份。|  
  
 如需詳細資訊，請參閱 [Integration Services 資料類型](../../integration-services/data-flow/integration-services-data-types.md)。  
  
## <a name="enable-fast-parse"></a>啟用快速剖析
使用快速剖析的來源或轉換，其每一個資料行都必須設定快速剖析屬性。 若要設定屬性，請使用「一般檔案」來源或「資料轉換」的進階編輯器。  
  
1.  以滑鼠右鍵按一下 [一般檔案] 來源或 [資料轉換]，然後按一下 [顯示進階編輯器]。  
  
2.  按一下 **[進階編輯器]** 對話方塊中的 **[輸入與輸出屬性]** 索引標籤。  
  
3.  在 **[輸入及輸出]** 窗格中，按一下您要啟用快速剖析的資料行。  
  
4.  在 [屬性] 視窗中，展開 **[自訂屬性]** 節點，然後將 **FastParse** 屬性設為 **True**。  
  
5.  按一下 [確定]。  

## <a name="standard-parse"></a>Standard Parse
標準剖析是一組區分地區設定的剖析常式，它支援 Oleaut32.dll 和 Ole2dsip.dll 中可用的 Automation 資料類型轉換 API 所提供的所有資料類型轉換。 標準剖析相當於 OLE DB 剖析 API。  
  
 標準剖析提供對國際性資料之資料類型轉換的支援，它應在「快速剖析」不支援資料格式時使用。 如需 Automation 資料類型轉換 API 的詳細資訊，請參閱 [MSDN Library](/documentation/)中的＜資料類型轉換 API＞。 
