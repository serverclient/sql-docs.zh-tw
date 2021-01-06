---
description: 將搜尋所使用的文字分隔還原為舊版 (SQL Server 搜尋)
title: 將搜尋所使用的文字分隔還原為舊版
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: search, sql-database
ms.technology: search
ms.topic: conceptual
ms.assetid: 29b4488e-4c6a-4bf0-a64d-19e2fdafa7ae
author: pmasl
ms.author: pelopes
ms.reviewer: mikeray
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.custom: seo-lt-2019
ms.openlocfilehash: 4a2624566444f5cc3b9531d5b6767937dabb2da6
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643986"
---
# <a name="revert-word-breakers-used-by-search-to-previous-version-sql-server-search"></a>將搜尋所使用的文字分隔還原為舊版 (SQL Server 搜尋)
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 對於全文檢索搜尋支援的所有語言 (韓文除外)，都會安裝並啟用特定版本的斷詞工具和字幹分析器。 本文描述如何從這些元件的這個版本切換成舊版，或從舊版切換回新版。  
  
 本文不討論以下語言：  
  
-   **英文**。 若要還原英文元件，請參閱＜ [變更用於美式英文與英式英文的斷詞工具](../../relational-databases/search/change-the-word-breaker-used-for-us-english-and-uk-english.md)＞。  
  
-   **丹麥文、波蘭文和土耳其文**： 舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 隨附之丹麥文、波蘭文及土耳其文的協力廠商斷詞工具已取代為 [!INCLUDE[msCoName](../../includes/msconame-md.md)] 元件。  
  
-   **捷克文和希臘文**： 目前有捷克文和希臘文的新斷詞工具。 舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 全文檢索搜尋不支援這兩種語言。  
  
-   **韓文**： 韓文的斷詞工具和字幹分析器在此版本中未升級。  
  
 如需斷詞工具與字幹分析器的一般資訊，請參閱 [設定及管理搜尋的斷詞工具與字幹分析器](../../relational-databases/search/configure-and-manage-word-breakers-and-stemmers-for-search.md)。  
  
##  <a name="overview-of-reverting-and-restoring-word-breakers-and-stemmers"></a><a name="overview"></a> 還原斷詞工具和字幹分析器的概觀  
 還原斷詞工具和字幹分析器的指示取決於語言。 下表摘要說明還原為舊版元件可能需要的三組動作。  
  
|目前檔案|舊版檔案|受影響語言的數目|檔案的動作|登錄項目的動作|  
|------------------|-------------------|----------------------------------|----------------------|---------------------------------|  
|NaturalLanguage6.dll|NaturalLanguage6.dll|34|取得及安裝舊版 NaturalLanguage6.dll，並覆寫目前版本的檔案。|不需要任何動作。<br /><br /> 此版本中未變更登錄機碼和值。|  
|(其他檔案名稱)|NaturalLanguage6.dll|5|取得及安裝舊版 NaturalLanguage6.dll，並覆寫目前版本的檔案。|變更一組登錄項目以指定舊版元件。|  
|(其他檔案名稱)|(其他檔案名稱)|6|不需要任何動作。<br /><br /> [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 安裝程式會將目前版本和舊版元件都複製到 Binn 資料夾。|變更一組登錄項目以指定舊版元件。|  
  
> [!WARNING]  
>  如果您以其他版本取代目前版本的 NaturalLanguage6.dll 檔案，則使用此檔案的所有語言的行為都會受到影響。  
  
 本文描述的檔案是安裝在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體之 `MSSQL\Binn` 資料夾中的 DLL 檔案。 完整路徑通常是以下路徑：  
  
 `C:\Program Files\Microsoft SQL Server\<instance>\MSSQL\Binn`  
  
##  <a name="languages-for-which-the-file-name-of-both-the-current-and-previous-word-breaker-is-naturallanguage6dll"></a><a name="nl6nl6"></a> 目前和舊版斷詞工具的檔案名稱都是 NaturalLanguage6.dll 的語言  
 在下表中的語言，目前和舊版斷詞工具的檔案名稱都是 NaturalLanguage6.dll。 若要還原這些元件，您必須以相同檔案的不同版本覆寫 NaturalLanguage6.dll。 您不必變更任何登錄項目，因為此版本中未變更這些登錄項目。  
  
> [!WARNING]  
>  如果您以其他版本取代目前版本的 NaturalLanguage6.dll 檔案，則使用此檔案的所有語言的行為都會受到影響。  
  
 **受影響語言的清單**  
  
|Language|縮寫<br />用於<br />登錄|LCID|  
|--------------|---------------------------------------|----------|  
|孟加拉文|ben|1093|  
|保加利亞文|bgr|1026|  
|卡達隆尼亞文|cat|1027|  
|西班牙文|esn|3082|  
|法文|fra|1036|  
|古吉拉特文|guj|1095|  
|Hebrew|heb|1037|  
|Hindi|hin|1081|  
|克羅埃西亞文|hrv|1050|  
|印尼文|ind|1057|  
|冰島文|isl|1039|  
|義大利文|ita|1040|  
|坎那達文|kan|1099|  
|立陶宛文|lth|1063|  
|拉脫維亞文|lvi|1062|  
|馬來亞拉姆文|mal|1100|  
|馬拉地文|mar|1102|  
|馬來文|msl|1086|  
|中性|中性|0000|  
|挪威文 (巴克摩)|nor|1044|  
|旁遮普文|平底鍋|1094|  
|葡萄牙文 (巴西)|ptb|1046|  
|葡萄牙文|ptg|2070|  
|羅馬尼亞文|rom|1048|  
|斯洛伐克文|sky|1051|  
|斯洛維尼亞文|slv|1060|  
|塞爾維亞文 (斯拉夫)|srb|3098|  
|塞爾維亞文 (拉丁)|srl|2074|  
|瑞典文|sve|1053|  
|坦米爾文|tam|1097|  
|泰盧固文|tel|1098|  
|烏克蘭文|ukr|1058|  
|烏都文|urd|1056|  
|越南文|vit|1066|  
  
 上表依縮寫資料行的字母順序排序。  
  
###  <a name="to-revert-to-the-previous-components"></a><a name="nl6nl6revert"></a> 若要還原為舊版元件  
  
1.  導覽至上述 Binn 資料夾。  
  
2.  將 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 版本的 NaturalLanguage6.dll 備份至另一個位置。  
  
3.  將舊版 NaturalLanguage6.dll 從 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 或 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 執行個體的 Binn 資料夾中複製到 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 執行個體的 Binn 資料夾。  
  
    > [!WARNING]  
    >  此變更影響在目前版本和舊版中都使用 NaturalLanguage6.dll 的所有語言。  
  
4.  重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。  

###  <a name="to-restore-the-current-components"></a><a name="nl6nl6restore"></a> 若要還原目前元件  
  
1.  導覽至 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 版本 NaturalLanguage6.dll 的備份位置。  
  
2.  將目前版本的 NaturalLanguage6.dll 從備份位置複製到 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 執行個體的 Binn 資料夾。  
  
    > [!WARNING]  
    >  此變更影響在目前版本和舊版中都使用 NaturalLanguage6.dll 的所有語言。  
  
3.  重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。  
  
##  <a name="languages-for-which-the-file-name-of-the-previous-word-breaker-only-is-naturallanguage6dll"></a><a name="newnl6"></a> 僅舊版斷詞工具的檔案名稱是 NaturalLanguage6.dll 的語言  
 在下表中的語言，舊版斷詞工具的檔案名稱不同於新版檔案名稱。 舊版檔案名稱為 NaturalLanguage6.dll。 若要還原為舊版元件，您必須以舊版的相同檔案覆寫目前版本的 NaturalLanguage6.dll。 您也必須變更一組登錄項目，以指定舊版或目前版本的元件。  
  
> [!WARNING]  
>  如果您以其他版本取代目前版本的 NaturalLanguage6.dll 檔案，則使用此檔案的所有語言的行為都會受到影響。  
  
 **受影響語言的清單**  
  
|Language|縮寫<br />用於<br />登錄|LCID|  
|--------------|---------------------------------------|----------|  
|阿拉伯文|ara|1025|  
|德文|deu|1031|  
|日文|jpn|1041|  
|荷蘭文|nld|1043|  
|俄文|rus|1049|  
  
 上表依縮寫資料行的字母順序排序。  
  
 將以下指示與＜ [用於還原斷詞工具和字幹分析器的檔案名稱和登錄值](#newnl6values)＞一節中的值清單一起使用。  
  
###  <a name="to-revert-to-the-previous-components"></a><a name="newnl6revert"></a> 若要還原為舊版元件  
  
1.  導覽至上述 Binn 資料夾。  
  
2.  不要從 Binn 資料夾中移除目前元件版本的檔案。  
  
3.  將 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 版本的 NaturalLanguage6.dll 備份至另一個位置。  
  
4.  將舊版 NaturalLanguage6.dll 從 [!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 或 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 執行個體的 Binn 資料夾中複製到 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 執行個體的 Binn 資料夾。  
  
    > [!WARNING]  
    >  此變更影響在目前版本和舊版中都使用 NaturalLanguage6.dll 的所有語言。  
  
5.  在登錄中，巡覽至下列節點：**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\<InstanceRoot>\MSSearch\CLSID**。  
  
6.  使用下列步驟，針對選定語言的舊版斷詞工具和字幹分析器介面加入 COM ClassID 的新機碼：  
  
    1.  加入新機碼，其具有表格中舊版斷詞工具的值。  
  
    2.  將該機碼值的 (預設值) 資料更新為表格中舊版斷詞工具的檔案名稱。  
  
    3.  如果選取的語言使用字幹分析器，則加入具有表格中舊版字幹分析器值的新機碼。  
  
    4.  如果選取的語言使用字幹分析器，則將該機碼值的 (預設值) 資料更新為表格中舊版字幹分析器的檔案名稱。  
  
7.  在登錄中，巡覽至下列節點：**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\<InstanceRoot>\MSSearch\Language\<language_key>** 。 *<language_key>* 代表登錄中所用語言的縮寫，例如，"fra" 代表法文，"esn" 則代表西班牙文。  
  
8.  將 **WBreakerClass** 機碼值更新為表格中目前斷詞工具的值。  
  
9. 如果選取的語言使用字幹分析器，則將 **StemmerClass** 機碼值更新為表格中目前字幹分析器的值。  
  
10. 重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。  
  
###  <a name="to-restore-the-current-components"></a><a name="newnl6restore"></a> 若要還原目前元件  
  
1.  導覽至 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 版本 NaturalLanguage6.dll 的備份位置。  
  
2.  將目前版本的 NaturalLanguage6.dll 從備份位置複製到 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 執行個體的 Binn 資料夾。  
  
    > [!WARNING]  
    >  此變更影響在目前版本和舊版中都使用 NaturalLanguage6.dll 的所有語言。  
  
3.  在登錄中，巡覽至下列節點：**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\<InstanceRoot>\MSSearch\CLSID**。  
  
4.  如果下列機碼不存在，請使用下列步驟，針對選定語言的目前斷詞工具和字幹分析器介面加入 COM ClassID 的新機碼：  
  
    1.  加入新機碼，其具有表格中目前斷詞工具的值。  
  
    2.  將該機碼值的 (預設值) 資料更新為表格中目前斷詞工具的檔案名稱。  
  
    3.  如果選取的語言使用字幹分析器，則加入具有表格中目前字幹分析器值的新機碼。  
  
    4.  如果選取的語言使用字幹分析器，則將該機碼值的 (預設值) 資料更新為表格中目前字幹分析器的檔案名稱。  
  
5.  在登錄中，巡覽至下列節點：**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\<InstanceRoot>\MSSearch\Language\<language_key>** 。 *<language_key>* 代表登錄中所用語言的縮寫，例如，"fra" 代表法文，"esn" 則代表西班牙文。  
  
6.  將 **WBreakerClass** 機碼值更新為表格中舊版斷詞工具的值。  
  
7.  如果選取的語言使用字幹分析器，則將 **StemmerClass** 機碼值更新為表格中舊版字幹分析器的值。  
  
8.  重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。  
  
###  <a name="file-names-and-registry-values-for-reverting-and-restoring-word-breakers-and-stemmers"></a><a name="newnl6values"></a> 用於還原斷詞工具和字幹分析器的檔案名稱和登錄值  
 將下列的檔案名稱和登錄項目清單與上一節中的指示一起使用。 使用舊版值還原為舊版，或使用目前值還原目前版本的元件。  
  
 下列清單依各語言縮寫的字母順序排序。  
  
 **阿拉伯文 (ara)，LCID 1025**  
  
|元件|斷詞工具|詞幹分析器|  
|---------------|------------------|-------------|  
|舊版 CLSID|7EFD3C7E-9E4B-4a93-9503-DECD74C0AC6D|483B0283-25DB-4c92-9C15-A65925CB95CE|  
|舊版檔案名稱|NaturalLanguage6.dll|NaturalLanguage6.dll|  
|目前 CLSID|04b37e30-c9a9-4a7d-8f20-792fc87ddf71|None|  
|目前檔案名稱|MSWB7.dll|None|  
  
 **德文 (deu)，LCID 1031**  
  
|元件|斷詞工具|詞幹分析器|  
|---------------|------------------|-------------|  
|舊版 CLSID|45EACA36-DBE9-4e4a-A26D-5C201902346D|65170AE4-0AD2-4fa5-B3BA-7CD73E2DA825|  
|舊版檔案名稱|NaturalLanguage6.dll|NaturalLanguage6.dll|  
|目前 CLSID|dfa00c33-bf19-482e-a791-3c785b0149b4|8a474d89-6e2f-419c-8dd5-9b50edc8c787|  
|目前檔案名稱|MSWB7.dll|MSWB7.dll|  
  
 **日文 (jpn)，LCID 1041**  
  
|元件|斷詞工具|詞幹分析器|  
|---------------|------------------|-------------|  
|舊版 CLSID|E1E8F15E-8BEC-45df-83BF-50FF84D0CAB5|3D5DF14F-649F-4cbc-853D-F18FEDE9CF5D|  
|舊版檔案名稱|NaturalLanguage6.dll|NaturalLanguage6.dll|  
|目前 CLSID|04096682-6ece-4e9e-90c1-52d81f0422ed|None|  
|目前檔案名稱|MsWb70011.dll|None|  
  
 **荷蘭文 (nld)，LCID 1043**  
  
|元件|斷詞工具|詞幹分析器|  
|---------------|------------------|-------------|  
|舊版 CLSID|2C9F6BEB-C5B0-42b6-A5EE-84C24DC0D8EF|F7A465EE-13FB-409a-B878-195B420433AF|  
|舊版檔案名稱|NaturalLanguage6.dll|NaturalLanguage6.dll|  
|目前 CLSID|69483c30-a9af-4552-8f84-a0796ad5285b|CF923CB5-1187-43ab-B053-3E44BED65FFA|  
|目前檔案名稱|MSWB7.dll|MSWB7.dll|  
  
 **俄文 (rus)，LCID 1049**  
  
|元件|斷詞工具|詞幹分析器|  
|---------------|------------------|-------------|  
|舊版 CLSID|2CB6CDA4-1C14-4392-A8EC-81EEF1F2E079|E06A0DDD-E81A-4e93-8A8D-F386C3A1B670|  
|舊版檔案名稱|NaturalLanguage6.dll|NaturalLanguage6.dll|  
|目前 CLSID|aaa3d3bd-6de7-4317-91a0-d25e7d3babc3|d42c8b70-adeb-4b81-a52f-c09f24f77dfa|  
|目前檔案名稱|MSWB7.dll|MSWB7.dll|  
  
##  <a name="languages-for-which-neither-the-previous-nor-the-current-file-name-is-naturallanguage6dll"></a><a name="newnew"></a> 舊版或目前檔案名稱都不是 NaturalLanguage6.dll 的語言  
 在下表中的語言，舊版斷詞工具和字幹分析器的檔案名稱不同於新版檔案名稱。 舊版或目前檔案名稱都不是 NaturalLanguage6.dll。 您不必取代任何檔案，因為 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 安裝程式會將目前版本和舊版元件都複製到 Binn 資料夾。 不過，您必須變更一組登錄項目，以指定舊版或目前版本的元件。  
  
 **受影響語言的清單**  
  
|Language|縮寫<br />用於<br />登錄|LCID|  
|--------------|---------------------------------------|----------|  
|簡體中文|chs|2052|  
|繁體中文|cht|1028|  
|泰文|tha|1054|  
|繁體中文|zh-hk|3076|  
|繁體中文|zh-mo|5124|  
|簡體中文|zh-sg|4100|  
  
 上表依縮寫資料行的字母順序排序。  
  
 將以下指示與＜ [用於還原斷詞工具和字幹分析器的檔案名稱和登錄值](#newnewvalues)＞一節中的值清單一起使用。  
  
###  <a name="to-revert-to-the-previous-components"></a><a name="newnewrevert"></a> 若要還原為舊版元件  
  
1.  不要從 Binn 資料夾中移除目前元件版本的檔案。  
  
2.  在登錄中，巡覽至下列節點：**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\<InstanceRoot>\MSSearch\CLSID**。  
  
3.  使用下列步驟，針對選定語言的舊版斷詞工具和字幹分析器介面加入 COM ClassID 的新機碼：  
  
    1.  加入新機碼，其具有表格中舊版斷詞工具的值。  
  
    2.  將該機碼值的 (預設值) 資料更新為表格中舊版斷詞工具的檔案名稱。  
  
    3.  如果選取的語言使用字幹分析器，則加入具有表格中舊版字幹分析器值的新機碼。  
  
    4.  如果選取的語言使用字幹分析器，則將該機碼值的 (預設值) 資料更新為表格中舊版字幹分析器的檔案名稱。  
  
4.  在登錄中，巡覽至下列節點：**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\<InstanceRoot>\MSSearch\Language\<language_key>** 。 *<language_key>* 代表登錄中所用語言的縮寫，例如，"fra" 代表法文，"esn" 則代表西班牙文。  
  
5.  將 **WBreakerClass** 機碼值更新為表格中目前斷詞工具的值。  
  
6.  如果選取的語言使用字幹分析器，則將 **StemmerClass** 機碼值更新為表格中目前字幹分析器的值。  
  
7.  重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。  
  
###  <a name="to-restore-the-previous-components"></a><a name="newnewrestore"></a> 若要還原舊版元件  
  
1.  不要從 Binn 資料夾中移除舊版元件的檔案。  
  
2.  在登錄中，巡覽至下列節點：**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\<InstanceRoot>\MSSearch\CLSID**。  
  
3.  如果下列機碼不存在，請使用下列步驟，針對選定語言的目前斷詞工具和字幹分析器介面加入 COM ClassID 的新機碼：  
  
    1.  加入新機碼，其具有表格中目前斷詞工具的值。  
  
    2.  將該機碼值的 (預設值) 資料更新為表格中目前斷詞工具的檔案名稱。  
  
    3.  如果選取的語言使用字幹分析器，則加入具有表格中目前字幹分析器值的新機碼。  
  
    4.  如果選取的語言使用字幹分析器，則將該機碼值的 (預設值) 資料更新為表格中目前字幹分析器的檔案名稱。  
  
4.  在登錄中，巡覽至下列節點：**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\<InstanceRoot>\MSSearch\Language\<language_key>** 。 *<language_key>* 代表登錄中所用語言的縮寫，例如，"fra" 代表法文，"esn" 則代表西班牙文。  
  
5.  將 **WBreakerClass** 機碼值更新為表格中舊版斷詞工具的值。  
  
6.  如果選取的語言使用字幹分析器，則將 **StemmerClass** 機碼值更新為表格中舊版字幹分析器的值。  
  
7.  重新啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]。  
  
###  <a name="file-names-and-registry-values-for-reverting-and-restoring-word-breakers-and-stemmers"></a><a name="newnewvalues"></a> 用於還原斷詞工具和字幹分析器的檔案名稱和登錄值  
 將下列的檔案名稱和登錄項目清單與上一節中的指示一起使用。 使用舊版值還原為舊版，或使用目前值還原目前版本的元件。  
  
 下列清單依各語言縮寫的字母順序排序。  
  
 **簡體中文 (chs)，LCID 2052**  
  
|元件|斷詞工具|  
|---------------|------------------|  
|舊版 CLSID|12CE94A0-DEFB-11D2-B31D-00600893A857|  
|舊版檔案名稱|chsbrkr.dll|  
|目前 CLSID|E0831C90-BAB0-4ca5-B9BD-EA254B538DAC|  
|目前檔案名稱|MsWb70804.dll|  
  
 **繁體中文 (cht)，LCID 1028**  
  
|元件|斷詞工具|  
|---------------|------------------|  
|舊版 CLSID|1680E7C3-9430-4A51-9B82-1E7E7AEE5258|  
|舊版檔案名稱|chtbrkr.dll|  
|目前 CLSID|E9B1DF65-08F1-438b-8277-EF462B23A792|  
|目前檔案名稱|MsWb70404.dll|  
  
 **泰文 (tha)，LCID 1054**  
  
|元件|斷詞工具|詞幹分析器|  
|---------------|------------------|-------------|  
|舊版 CLSID|CCA22CF4-59FE-11D1-BBFF-00C04FB97FDA|CEDC01C7-59FE-11D1-BBFF-00C04FB97FDA|  
|舊版檔案名稱|Thawbrkr.dll|Thawbrkr.dll|  
|目前 CLSID|F70C0935-6E9F-4ef1-9F06-7876536DB900|None|  
|目前檔案名稱|MsWb7001e.dll|None|  
  
 **繁體中文 (zh-hk)，LCID 3076**  
  
|元件|斷詞工具|  
|---------------|------------------|  
|舊版 CLSID|1680E7C3-9430-4A51-9B82-1E7E7AEE5258|  
|舊版檔案名稱|chtbrkr.dll|  
|目前 CLSID|E9B1DF65-08F1-438b-8277-EF462B23A792|  
|目前檔案名稱|MsWb70404.dll|  
  
 **繁體中文 (zh-mo)，LCID 5124**  
  
|元件|斷詞工具|  
|---------------|------------------|  
|舊版 CLSID|1680E7C3-9430-4A51-9B82-1E7E7AEE5258|  
|舊版檔案名稱|chtbrkr.dll|  
|目前 CLSID|E9B1DF65-08F1-438b-8277-EF462B23A792|  
|目前檔案名稱|MsWb70404.dll|  
  
 **簡體中文 (zh-sg)，LCID 4100**  
  
|元件|斷詞工具|  
|---------------|------------------|  
|舊版 CLSID|12CE94A0-DEFB-11D2-B31D-00600893A857|  
|舊版檔案名稱|chsbrkr.dll|  
|目前 CLSID|E0831C90-BAB0-4ca5-B9BD-EA254B538DAC|  
|目前檔案名稱|MsWb70804.dll|  
  
## <a name="see-also"></a>另請參閱  
 [變更用於美式英文與英式英文的斷詞工具](../../relational-databases/search/change-the-word-breaker-used-for-us-english-and-uk-english.md)   
 [全文檢索搜尋的行為變更](./full-text-search.md)
