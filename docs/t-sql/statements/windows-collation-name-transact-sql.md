---
description: Windows 定序名稱 (Transact-SQL)
title: Windows 定序名稱 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/06/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- Windows collations [SQL Server]
- names [SQL Server], collations
- collations [SQL Server], Windows collations
- Collation Designator
ms.assetid: acceef84-2c68-46e2-a021-be019b7ab14e
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: e89cac604654a1793a65bdd43dcd38cf77484b06
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099374"
---
# <a name="windows-collation-name-transact-sql"></a>Windows 定序名稱 (Transact-SQL)

[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]，指定 COLLATE 子句中的 Windows 定序名稱。 Windows 定序名稱是由定序指示項和比較樣式組成。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

```syntaxsql
<Windows_collation_name> :: =
<CollationDesignator>_<ComparisonStyle>

<ComparisonStyle> :: =
{ <CaseSensitivity>_<AccentSensitivity> [ _<KanatypeSensitive> ] [ _<WidthSensitive> ] [ _<VariationSelectorSensitive> ] 
}
| { _UTF8 }
| { _BIN | _BIN2 }
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

*CollationDesignator*   
指定 Windows 定序所用的基底定序規則。 基底定序規則涵蓋下列項目：

- 在指定了字典排序時，會套用的排序與比較規則。 排序規則根據字母或語言而定。
- 用於儲存 **varchar** 資料的字碼頁。

部份範例如下：

- Latin1\_General 或 French：兩者都使用字碼頁 1252。
- Turkish：使用字碼頁 1254。

*CaseSensitivity*  
**CI** 指定不區分大小寫，**CS** 指定區分大小寫。

*AccentSensitivity*  
**AI** 指定不區分腔調字，**AS** 指定區分腔調字。

*KanatypeSensitive*  
省略此選項指定不區分假名，**KS** 指定區分假名。

*WidthSensitivity*  
省略此選項指定不區分全半形，**WS** 指定區分全半形。

*VariationSelectorSensitivity*  
- **適用於**：從 [!INCLUDE[ssSQL15](../../includes/sssqlv14-md.md)] 開始 

- 省略此選項指定不區分變化選取器，**VSS** 則指定區分變化選取器。

[UTF8]  
- **適用於**：從 [!INCLUDE[sql-server-2019](../../includes/sssqlv15-md.md)] 開始   

- 指定要用於合格資料類型的 UTF-8 編碼。 如需詳細資訊，請參閱 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)。

**BIN**  
指定要用的回溯相容性二進位排序次序。

**BIN2**  
指定使用字碼指標比較語意的二進位排序順序。

## <a name="remarks"></a>備註
由於定序版本的不同，有些字碼元素可能不會有已定義的排序權重及 (或) 大寫/小寫對應。 例如，比較 `LOWER` 函式在得到相同字元，但在相同定序不同版本時的輸出：

```sql
SELECT NCHAR(504) COLLATE Latin1_General_CI_AS AS [Uppercase],
       NCHAR(505) COLLATE Latin1_General_CI_AS AS [Lowercase];
-- Ǹ    ǹ


SELECT LOWER(NCHAR(504) COLLATE Latin1_General_CI_AS) AS [Version80Collation],
       LOWER(NCHAR(504) COLLATE Latin1_General_100_CI_AS) AS [Version100Collation];
-- Ǹ    ǹ
```

第一個陳述式以較舊的順序顯示這個字元的大寫和小寫形式 (在處理 Unicode 資料時，定序不會影響字元的可用性)。 不過，第二個陳述式顯示當定序為 Latin1\_General\_CI\_AS 時，傳回了大寫字元，因為該定序中未定義此字碼元素的小寫對應。

使用某些語言時，避免使用較舊的定序很重要。 例如，對於 Telegu 就是如此。

在某些情況下，Windows 定序與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 定序對相同的查詢會產生不同的查詢計劃。

## <a name="examples"></a>範例

以下是一些 Windows 定序名稱的範例：

- **Latin1\_General\_100\_CI\_AS**

  定序會使用 Latin1 General 字典排序規則，而且對應至字碼頁 1252。 這是版本 \_100 的定序，不區分大小寫 (CI) 且區分重音符號 (AS)。

- **Estonian\_CS\_AS**

  定序會使用 Estonian 字典排序規則，並對應到字碼頁 1257。 這是版本 \_80 的定序(從名稱中沒有版本號碼得知)，區分大小寫 (CS) 且區分重音符號 (AS)。

- **Japanese\_Bushu\_Kakusu\_140\_BIN2**

  定序使用二進位字碼元素排序規則，並對應到字碼頁 932。 這是版本 \_140 的定序，並略過 Japanese Bushu Kakusu 字典排序規則。

## <a name="windows-collations"></a>Windows 定序

若要列出您的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體支援的 Windows 定序，請執行下列查詢。

```sql
SELECT * FROM sys.fn_helpcollations() WHERE [name] NOT LIKE N'SQL%';
```

下表列出 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 支援的所有 Windows 定序。

|Windows 地區設定|定序版本 100|定序版本 90|
|--------------------|---------------------------|--------------------------|
|亞爾薩斯語 (法國)|Latin1_General_100_|無法使用|
|阿姆哈拉文 (衣索比亞)|Latin1_General_100_|無法使用|
|亞美尼亞文 (亞美尼亞)|Cyrillic_General_100_|無法使用|
|阿薩姆文 (印度)|Assamese_100_ <sup>1</sup>|無法使用|
|孟加拉文 (孟加拉)|Bengali_100_<sup>1</sup>|無法使用|
|巴什喀爾文 (俄羅斯)|Bashkir_100_|無法使用|
|巴斯克文 (巴斯克)|Latin1_General_100_|無法使用|
|孟加拉文 (印度)|Bengali_100_<sup>1</sup>|無法使用|
|波士尼亞文 (波士尼亞赫塞哥維納，斯拉夫)|Bosnian_Cyrillic_100_|無法使用|
|波士尼亞文 (波士尼亞赫塞哥維納，拉丁)|Bosnian_Latin_100_|無法使用|
|布里敦文 (法國)|Breton_100_|無法使用|
|中文 (澳門特別行政區)|Chinese_Traditional_Pinyin_100_|無法使用|
|中文 (澳門特別行政區)|Chinese_Traditional_Stroke_Order_100_|無法使用|
|中文 (新加坡)|Chinese_Simplified_Stroke_Order_100_|無法使用|
|科西嘉文 (法國)|Corsican_100_|無法使用|
|克羅埃西亞文 (波士尼亞赫塞哥維納，拉丁)|Croatian_100_|無法使用|
|達利語 (阿富汗)|Dari_100_|無法使用|
|英文 (印度)|Latin1_General_100_|無法使用|
|英文 (馬來西亞)|Latin1_General_100_|無法使用|
|英文 (新加坡)|Latin1_General_100_|無法使用|
|菲律賓文 (菲律賓)|Latin1_General_100_|無法使用|
|夫里斯蘭文 (荷蘭)|Frisian_100_|無法使用|
|喬治亞文 (喬治亞)|Cyrillic_General_100_|無法使用|
|格陵蘭文 (格陵蘭)|Danish_Greenlandic_100_|無法使用|
|古吉拉特文 (印度)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|豪撒文 (奈及利亞，拉丁)|Latin1_General_100_|無法使用|
|印度文 (印度)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|伊布文 (奈及利亞)|Latin1_General_100_|無法使用|
|因紐特文 (加拿大，拉丁)|Latin1_General_100_|無法使用|
|因紐特文 (語音節) 加拿大|Latin1_General_100_|無法使用|
|愛爾蘭文 (愛爾蘭)|Latin1_General_100_|無法使用|
|日文 (日本 XJIS)|Japanese_XJIS_100_|Japanese_90_、Japanese_|
|日文 (日本)|Japanese_Bushu_Kakusu_100_|無法使用|
|坎那達文 (印度)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|高棉文 (柬埔寨)|Khmer_100_<sup>1</sup>|無法使用|
|基切語 (瓜地馬拉)|Modern_Spanish_100_|無法使用|
|金揚萬答文 (盧安達)|Latin1_General_100_|無法使用|
|貢根文 (印度)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|寮文 (寮國人民共合國)|Lao_100_<sup>1</sup>|無法使用|
|下索布語 (德國)|Latin1_General_100_|無法使用|
|盧森堡文 (盧森堡)|Latin1_General_100_|無法使用|
|馬來亞拉姆文 (印度)|Indic_General_100_<sup>1</sup>|無法使用|
|馬爾他文 (馬爾他)|Maltese_100_|無法使用|
|毛利文 (紐西蘭)|Maori_100_|無法使用|
|馬普切文 (智利)|Mapudungan_100_|無法使用|
|馬拉提文 (印度)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|莫霍克文 (加拿大)|Mohawk_100_|無法使用|
|蒙古文 (中國)|Cyrillic_General_100_|無法使用|
|尼泊爾文 (尼泊爾)|Nepali_100_<sup>1</sup>|無法使用|
|挪威文 (巴克摩，挪威)|Norwegian_100_|無法使用|
|挪威文 (耐諾斯克，挪威)|Norwegian_100_|無法使用|
|奧西坦文 (法國)|French_100_|無法使用|
|歐迪亞文 (印度)|Indic_General_100_<sup>1</sup>|無法使用|
|普什圖文 (阿富汗)|Pashto_100_<sup>1</sup>|無法使用|
|波斯文 (伊朗)|Persian_100_|無法使用|
|旁遮普文 (印度)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|蓋楚瓦文 (玻利維亞)|Latin1_General_100_|無法使用|
|蓋楚瓦文 (厄瓜多)|Latin1_General_100_|無法使用|
|蓋楚瓦文 (秘魯)|Latin1_General_100_|無法使用|
|羅曼斯文 (瑞士)|Romansh_100_|無法使用|
|沙米文 (伊納立，芬蘭)|Sami_Sweden_Finland_100_|無法使用|
|沙米文 (盧勒，挪威)|Sami_Norway_100_|無法使用|
|沙米文 (盧勒，瑞典)|Sami_Sweden_Finland_100_|無法使用|
|沙米文 (北，芬蘭)|Sami_Sweden_Finland_100_|無法使用|
|沙米文 (北，挪威)|Sami_Norway_100_|無法使用|
|沙米文 (北，瑞典)|Sami_Sweden_Finland_100_|無法使用|
|沙米文 (斯科特，芬蘭)|Sami_Sweden_Finland_100_|無法使用|
|沙米文 (南，挪威)|Sami_Norway_100_|無法使用|
|沙米文 (南，瑞典)|Sami_Sweden_Finland_100_|無法使用|
|梵文 (印度)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|塞爾維亞文 (波士尼亞赫塞哥維納，斯拉夫)|Serbian_Cyrillic_100_|無法使用|
|塞爾維亞文 (波士尼亞赫塞哥維納，拉丁)|Serbian_Latin_100_|無法使用|
|塞爾維亞文 (塞爾維亞，斯拉夫)|Serbian_Cyrillic_100_|無法使用|
|塞爾維亞文 (塞爾維亞，拉丁)|Serbian_Latin_100_|無法使用|
|賴索托文/北索托文 (南非)|Latin1_General_100_|無法使用|
|塞茲瓦納文/班圖文 (南非)|Latin1_General_100_|無法使用|
|僧伽羅文 (斯里蘭卡)|Indic_General_100_<sup>1</sup>|無法使用|
|斯瓦希里文 (肯亞)|Latin1_General_100_|無法使用|
|敘利亞文 (敘利亞)|Syriac_100_<sup>1</sup>|Syriac_90_|
|塔吉克文 (塔吉克)|Cyrillic_General_100_|無法使用|
|塔馬塞特文 (阿爾及利亞，拉丁)|Tamazight_100_|無法使用|
|坦米爾文 (印度)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|特拉古文 (印度)|Indic_General_100_<sup>1</sup>|Indic_General_90_|
|藏文 (中國)|Tibetan_100_<sup>1</sup>|無法使用|
|土庫曼文 (土庫曼)|Turkmen_100_|無法使用|
|維吾爾文 (中國)|Uighur_100_|無法使用|
|上索布語 (德國)|Upper_Sorbian_100_|無法使用|
|烏都文 (巴基斯坦)|Urdu_100_|無法使用|
|威爾斯文 (英國)|Welsh_100_|無法使用|
|沃洛夫文 (塞內加爾)|French_100_|無法使用|
|科薩文/科薩文 (南非)|Latin1_General_100_|無法使用|
|薩哈文 (俄羅斯)|Yakut_100_|無法使用|
|爨文 (中國)|Latin1_General_100_|無法使用|
|優魯巴文 (奈及利亞)|Latin1_General_100_|無法使用|
|祖魯文/祖魯文 (南非)|Latin1_General_100_|無法使用|
|已被取代，無法用於 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 或更新版本的伺服器層級|Hindi|Hindi|
|已被取代，無法用於 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 或更新版本的伺服器層級|Korean_Wansung_Unicode|Korean_Wansung_Unicode|
|已被取代，無法用於 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 或更新版本的伺服器層級|Lithuanian_Classic|Lithuanian_Classic|
|已被取代，無法用於 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 或更新版本的伺服器層級|馬其頓文|馬其頓文|
||||

<sup>1</sup> 僅限 Unicode 的 Windows 定序只能套用至資料行層級或運算式層級的資料。 它們無法當做伺服器或資料庫定序使用。

<sup>2</sup> 中文 (澳門) 與中文 (台灣) 定序相同之處在於兩者都使用簡體中文的規則；中文 (澳門) 與中文 (台灣) 不同之處則在於前者使用字碼頁 950。

## <a name="see-also"></a>另請參閱

- [定序與 Unicode 支援](../../relational-databases/collations/collation-and-unicode-support.md)
- [ALTER TABLE](../../t-sql/statements/alter-table-transact-sql.md)
- [常數](../../t-sql/data-types/constants-transact-sql.md)
- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)
- [CREATE TABLE](../../t-sql/statements/create-table-transact-sql.md)
- [DECLARE @local_variable](../../t-sql/language-elements/declare-local-variable-transact-sql.md)
- [table](../../t-sql/data-types/table-transact-sql.md)
- [sys.fn_helpcollations](../../relational-databases/system-functions/sys-fn-helpcollations-transact-sql.md)
