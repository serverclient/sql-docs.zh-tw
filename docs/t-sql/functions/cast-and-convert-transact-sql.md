---
title: CAST 和 CONVERT (Transact-SQL) | Microsoft Docs
description: CAST 和 CONVERT Transact-SQL 函式的參考。 這些函式會將運算式從某種資料類型轉換成另一種資料類型。
ms.date: 08/23/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CAST_TSQL
- CONVERT_TSQL
- CAST
- CONVERT
dev_langs:
- TSQL
helpviewer_keywords:
- CAST function
- automatic data type conversion
- varbinary data type
- CONVERT function
- data types [SQL Server], converting
- large value data types
- implicit data type conversions
- image data type, converting
- explicit data type conversions [SQL Server]
- binary [SQL Server], converting
- text data type, converting
- date and time [SQL Server], cast and convert
- truncating conversions
- converting data types [SQL Server], conversion functions
- time zones [SQL Server]
- roundtrip conversions
ms.assetid: a87d0850-c670-4720-9ad5-6f5a22343ea8
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 24091fa04d0db4499f03e4722977a34cf70ae5ac
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097317"
---
# <a name="cast-and-convert-transact-sql"></a>CAST 和 CONVERT (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

這些函式會將某種資料類型的運算式轉換成另一種資料類型。  

## <a name="syntax"></a>語法  
  
```sqlsyntax 
-- CAST Syntax:  
CAST ( expression AS data_type [ ( length ) ] )  
  
-- CONVERT Syntax:  
CONVERT ( data_type [ ( length ) ] , expression [ , style ] )  
```  

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
*expression*  
任何有效的[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。
  
*data_type*  
目標資料類型。 這包括 **xml**、**bigint** 和 **sql_variant**。 無法使用別名資料類型。
  
*length*  
可指定目標資料類型長度的選擇性整數，用於允許使用者指定長度的資料類型。 預設值是 30。
  
*style*  
指定 CONVERT 函式如何轉譯 *expression* 的整數運算式。 針對樣式值 NULL，會傳回 NULL。 *data_type* 可決定範圍。 
  
## <a name="return-types"></a>傳回類型
傳回轉譯為 *data_type* 的 *expression*。
  
## <a name="date-and-time-styles"></a>日期和時間樣式  
針對日期或時間資料類型 *expression*，*style* 可以具有下表中所顯示的其中一個值。 其他值則當做 0 處理。 從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始，從日期和時間類型轉換成 **datetimeoffset** 時，唯一支援的樣式為 0 或 1。 所有其他轉換樣式都會傳回錯誤 9809。
  
> [!NOTE]
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 利用科威特演算法來支援阿拉伯文樣式的日期格式。
  
|不含世紀 (yy) (<sup>1</sup>)|含世紀 (yyyy)|標準|輸入/輸出 (<sup>3</sup>)|  
|---|---|--|---|
|-|**0** 或 **100** (<sup>1,</sup><sup>2</sup>)|datetime 和 smalldatetime 的預設值|mon dd yyyy hh:miAM (或 PM)|  
|**1**|**101**|美式英文|  1 = mm/dd/yy<br /> 101 = mm/dd/yyyy|  
|**2**|**102**|ANSI|  2 = yy.mm.dd<br /> 102 = yyyy.mm.dd|  
|**3**|**103**|英式英文/法文|  3 = dd/mm/yy<br /> 103 = dd/mm/yyyy|  
|**4**|**104**|德文|  4 = dd.mm.yy<br /> 104 = dd.mm.yyyy|  
|**5**|**105**|義大利文|  5 = dd-mm-yy<br /> 105 = dd-mm-yyyy|  
|**6**|**106** <sup>(1)</sup>|-|  6 = dd mon yy<br /> 106 = dd mon yyyy|  
|**7**|**107** <sup>(1)</sup>|-|  7 = Mon dd, yy<br /> 107 = Mon dd, yyyy|  
|**8** 或 **24**|**108**|-|hh:mi:ss|  
|-|**9** 或 **109** (<sup>1,</sup><sup>2</sup>)|預設值 + 毫秒|mon dd yyyy hh:mi:ss:mmmAM (或 PM)|  
|**10**|**110**|USA| 10 = mm-dd-yy<br /> 110 = mm-dd-yyyy|  
|**11**|**111**|日本| 11 = yy/mm/dd<br /> 111 = yyyy/mm/dd|  
|**12**|**112**|ISO| 12 = yymmdd<br /> 112 = yyyymmdd|  
|-|**13** 或 **113** (<sup>1,</sup><sup>2</sup>)|歐洲預設值 + 毫秒|dd mon yyyy hh:mi:ss:mmm (24 小時)|  
|**14**|**114**|-|hh:mi:ss:mmm (24 小時)|  
|-|**20** 或 **120** (<sup>2</sup>)|ODBC 標準|yyyy-mm-dd hh:mi:ss (24 小時)|  
|-|**21** 或 **25** 或 **121** (<sup>2</sup>)|time、date、datetime2 和 datetimeoffset 的 ODBC 標準 (使用毫秒) 預設值|yyyy-mm-dd hh:mi:ss.mmm (24 小時)|  
|**22**|-|美式英文| mm/dd/yy hh:mi:ss AM (或 PM)|
|-|**23**|ISO8601|yyyy-mm-dd|
|-|**126** (<sup>4</sup>)|ISO8601|yyyy-mm-ddThh:mi:ss.mmm (無空格)<br /><br /> **注意：** 針對毫秒 (mmm) 值 0，不會顯示毫秒十進位小數值。 例如，'2012-11-07T18:26:20.000' 值會顯示為 '2012-11-07T18:26:20'。| 
|-|**127**(<sup>6, 7</sup>)|具有時區 Z 的 ISO8601。|yyyy-MM-ddThh:mm:ss.fffZ (無空格)<br /><br /> **注意：** 針對毫秒 (mmm) 值 0，不會顯示毫秒十進位值。 例如，'2012-11-07T18:26:20.000' 值會顯示為 '2012-11-07T18:26:20'。|  
|-|**130** (<sup>1,</sup><sup>2</sup>)|回曆 (<sup>5</sup>)|dd mon yyyy hh:mi:ss:mmmAM<br /><br /> 在此樣式中，**mon** 代表完整月份名稱的多 Token 回曆 Unicode 表示法。 這個值無法在 SSMS 的預設美國安裝中正確呈現。|  
|-|**131** (<sup>2</sup>)|回曆 (<sup>5</sup>)|dd/mm/yyyy hh:mi:ss:mmmAM|  
  
<sup>1</sup> 這些樣式值會傳回不具決定性的結果。 其中包括所有 (yy) (不含世紀) 樣式和 (yyyy) (含世紀) 樣式的子集。
  
<sup>2</sup> 預設值 (**0** 或 **100**、**9** 或 **109**、**13** 或 **113**、**20** 或 **120**、**23** 和 **21** 或 **25** 或 **121**) 一律會傳回世紀 (yyyy)。

<sup>3</sup> 當轉換成 **datetime** 時輸入；當轉換成字元資料時輸出。

<sup>4</sup> 專為 XML 設計。 若要從 **datetime** 或 **smalldatetime** 轉換成字元資料，請參閱上表中的輸出格式。

<sup>5</sup> 阿拉伯回曆是具有多種變化的日曆系統。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用科威特演算法。

> [!IMPORTANT]
>  根據預設，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會根據截止年份 2049 來解譯兩位數的年份。 這表示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將兩位數年份 49 解譯為 2049，而將兩位數年份 50 解譯為 1950。 許多用戶端應用程式 (包含以 Automation 物件為基礎的應用程式) 都使用截止年份 2030。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 提供兩位數年份截止組態選項，以變更 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 所使用的截止年份。 這允許一致的日期處理。 我們建議您指定四位數的年份。

<sup>6</sup> 只有在從字元資料轉換為 **datetime** 或 **smalldatetime** 時才支援。 將只代表日期或只代表時間元件的字元資料轉換成 **datetime** 或 **smalldatetime** 資料類型時，未指定的時間元件會設定為 00:00:00.000，而未指定的日期元件則會設定為 1900-01-01。
  
<sup>7</sup> 使用選擇性時區指標 **Z** 更輕鬆地將 XML **datetime** 值 (具有時區資訊) 對應到沒有時區的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **datetime** 值。 Z 指出時區 UTC-0。 以 + 或 - 方向位移的 HH:MM 指出其他時區。 例如： `2006-12-12T23:45:12-08:00` 。
  
將 **smalldatetime** 轉換成字元資料時，包含秒或毫秒的樣式會在這些位置顯示零。 從 **datetime** 或 **smalldatetime** 值轉換時，請使用適當的 **char** 或 **varchar** 資料類型長度來截斷不需要的日期部分。
  
使用包含時間的樣式將字元資料轉換成 **datetimeoffset** 時，會將時區位移附加至結果。
  
## <a name="float-and-real-styles"></a>float 和 real 樣式
針對 **float** 或 **real** *expression*，*style* 可以具有下表中所顯示的其中一個值。 其他值則當做 0 處理。
  
|值|輸出|  
|---|---|
|**0** (預設)|最多 6 位數。 在適當時機，供科學記號標記法使用。|  
|**1**|一律 8 位數。 一律用在科學記號標記法中。|  
|**2**|一律 16 位數。 一律用在科學記號標記法中。|  
|**3**|一律 17 位數。 用於不失真的轉換。 透過這個樣式，可保證將每個不同的 float 或 real 值轉換成相異的字元字串。<br /><br /> **適用範圍：** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] 開始) 和 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。|  
|**126、128、129**|基於舊版原因而納入；未來版本可能會取代這些值。|  
  
## <a name="money-and-smallmoney-styles"></a>money 和 smallmoney 樣式
針對 **money** 或 **smallmoney** *expression*，*style* 可以具有下表中所顯示的其中一個值。 其他值則當做 0 處理。
  
|值|輸出|  
|---|---|
|**0** (預設)|小數點左側並不會每隔三位數加一個逗號，小數點右側則會有兩位數<br /><br />範例：4235.98。|  
|**1**|小數點左側會每隔三位數加一個逗號，小數點右側則會有兩位數<br /><br />範例：3,510.92。|  
|**2**|小數點左側並不會每隔三位數加一個逗號，小數點右側則會有四位數<br /><br />範例：4235.9819。|  
|**126**|轉換成 char(n) 或 varchar(n) 時，相當於樣式 2|  
  
## <a name="xml-styles"></a>xml 樣式
針對 **xml** *expression*，*style* 可以具有下表中所顯示的其中一個值。 其他值則當做 0 處理。
  
|值|輸出|  
|---|---|
|**0** (預設)|使用預設剖析行為，以捨棄無意義的空白字元，而且不允許內部 DTD 子集。<br /><br />**注意：** 轉換成  資料類型時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 無意義空白字元的處理方式會與 XML 1.0 不同。 如需詳細資訊，請參閱[建立 XML 資料的執行個體](../../relational-databases/xml/create-instances-of-xml-data.md)。|  
|**1**|保留無意義的空格。 此樣式設定會設定預設 **xml:space** 處理，以符合 **xml:space="preserve"** 的行為。|  
|**2**|啟用有限的內部 DTD 子集處理。<br /><br /> 啟用時，伺服器可以使用內部 DTD 子集所提供的下列資訊來執行非驗證的剖析作業。<br /><br />   - 已套用屬性的預設值<br />   - 已解決和展開內部實體參考<br />   - 已檢查 DTD 內容模型來確定語法正確性<br /><br /> 剖析器會忽略外部 DTD 子集。 此外，它不會評估 XML 宣告，以查看 **standalone** 屬性具有 **yes** 還是 **no** 值。 相反地，它會將 XML 執行個體剖析為獨立文件。|  
|**3**|保留無意義的空白字元，並啟用有限的內部 DTD 子集處理。|  
  
## <a name="binary-styles"></a>二進位樣式
針對 **binary(n)** 、**char(n)** 、**varbinary(n)** 或 **varchar(n)** *expression*，*style* 可以具有下表中所顯示的其中一個值。 此表中未列出的樣式值將傳回錯誤。
  
|值|輸出|  
|---|---|
|**0** (預設)|將 ASCII 字元轉譯成二進位位元組，或將二進位位元組轉譯成 ASCII 字元。 每個字元或位元組都會以 1:1 的方式轉換。<br /><br /> 針對二進位 *data_type*，會在結果的左側新增字元 0x。|  
|**1**、**2**|針對二進位 *data_type*，運算式必須是字元運算式。 *expression* 必須具有 **偶數** 個十六進位數字 (0、1、2、3、4、5、6、7、8、9、A、B、C、D、E、F、a、b、c、d、e、f)。 如果 *style* 設為 1，則運算式的前兩個字元必須是 0x。 如果運算式包含奇數個字元，或者有任何字元無效，則會引發錯誤。<br /><br /> 如果所轉換運算式的長度超過 *data_type* 的長度，結果就是自右截斷。<br /><br /> 大於已轉換結果的固定長度 *data_type* 會在結果的右側新增零。<br /><br /> 字元類型的 *data_type* 需要二進位運算式。 每個二進位字元都會轉換成兩個十六進位字元。 假設所轉換運算式的長度超過 *data_type* 的長度。 在此情況下，將會截斷其長度。<br /><br /> 針對固定大小字元類型 *data_type*，如果已轉換結果的長度小於 *data_type* 的長度，則會在所轉換運算式的右側新增空格，以維持偶數個十六進位數字。<br /><br /> 字元 0x 不會新增到 *style* 2 已轉換結果的左側。|  
  
## <a name="implicit-conversions"></a>隱含的轉換
隱含轉換不需要指定 CAST 函式或 CONVERT 函式。 明確轉換需要指定 CAST 函式或 CONVERT 函式。 下圖顯示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 系統提供之資料類型所能使用的所有明確和隱含資料類型轉換。 這些包含 **bigint**、**sql_variant** 和 **xml**。 從 **sql_variant** 資料類型進行指派時，不可使用隱含轉換，但可以隱含轉換成 **sql_variant**。
  
> [!TIP]  
> [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=35834)提供此圖表，可下載為 PNG 檔案。  
  
![資料類型轉換資料表](../../t-sql/data-types/media/lrdatahd.png "資料類型轉換資料表")
  
上圖說明 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中允許的所有明確和隱含轉換，但是轉換的結果資料類型取決於正在執行的作業：

-  針對明確轉換，陳述式本身便會判斷結果資料類型。    
-  針對隱含轉換，指派陳述式 (例如設定變數值或將值插入資料行) 會產生變數宣告或資料行定義所定義的資料類型。    
-  針對比較運算子或其他運算式，結果資料類型將取決於[資料類型優先順序](../../t-sql/data-types/data-type-precedence-transact-sql.md)的規則。

> [!TIP]
> 本節稍後會有[轉換中資料類型優先順序效果](#precedence-example)的實際範例。

當您在 **datetimeoffset** 與字元類型 **char**、**nchar**、**nvarchar** 和 **varchar** 之間轉換時，轉換過之時區位移部分的 HH 和 MM 應該永遠是兩位數。 例如 -08:00。
  
> [!NOTE]   
> 由於 Unicode 資料使用的位元組數目一律是偶數，因此，在 **binary** 或 **varbinary** 和 Unicode 支援的資料類型之間轉換時，要特別小心。 例如，下列轉換不會傳回十六進位值 41。 它會傳回十六進位值 4100：`SELECT CAST(CAST(0x41 AS nvarchar) AS varbinary)`。 如需詳細資訊，請參閱 [Collation and Unicode Support](../../relational-databases/collations/collation-and-unicode-support.md)。 
  
## <a name="large-value-data-types"></a>大數值資料類型
大數值資料類型具有與小數值資料類型相同的隱含和明確轉換行為，明確地說，就是 **nvarchar**、**varbinary** 和 **varchar** 資料類型。 不過，請考慮下列指導方針：
-   從 **image** 轉換成 **varbinary(max)** (反之亦然) 是一種隱含轉換，而 **text** 與 **varchar(max)** 以及 **ntext** 與 **nvarchar(max)** 之間的轉換亦是如此。  
-   從大數值資料類型 (如 **varchar(max)** ) 轉換成較小的對應資料類型 (如 **varchar**) 是隱含的轉換，但如果大數值的大小超過較小資料類型的指定長度，則會予以截斷。  
-   從 **nvarchar**、**varbinary** 或 **varchar** 轉換成其對應的大數值資料類型是透過隱含的方式進行。  
-   從 **sql_variant** 資料類型轉換為大數值資料類型為明確的轉換。  
-   大數值資料類型無法轉換成 **sql_variant** 資料類型。  
  
如需從 **xml** 資料類型轉換的詳細資訊，請參閱 [建立 XML 資料的執行個體](../../relational-databases/xml/create-instances-of-xml-data.md)。
  
## <a name="xml-data-type"></a>xml 資料型別
當您將 **xml** 資料類型明確或隱含地轉換成字串或二進位資料類型時，**xml** 資料類型的內容會根據定義的一組規則進行序列化。 如需如需這些規則的相關資訊，請參閱[定義 XML 資料的序列化](../../relational-databases/xml/define-the-serialization-of-xml-data.md)。 如需從其他資料類型轉換成 **xml** 資料類型的資訊，請參閱 [建立 XML 資料的執行個體](../../relational-databases/xml/create-instances-of-xml-data.md)。
  
## <a name="text-and-image-data-types"></a>text 和 image 資料類型
**text** 和 **image** 資料類型不支援自動資料類型轉換。 您可以將 **text** 資料明確地轉換成字元資料，並將 **image** 資料轉換成 **binary** 或 **varbinary**，但最大長度是 8000 位元組。 如果您嘗試進行不正確的轉換 (例如，將包含字母的字元運算式轉換成 **int**)，則 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回一則錯誤訊息。
  
## <a name="output-collation"></a>輸出定序  
如果 CAST 或 CONVERT 函式輸出字元字串，並且收到字元字串輸入，則輸出的定序和定序標籤會與輸入相同。 如果輸入不是字元字串，輸出會使用預設的資料庫定序及強制預設的定序標籤。 如需詳細資訊，請參閱[定序優先順序 &#40;Transact-SQL&#41;](../../t-sql/statements/collation-precedence-transact-sql.md)。
  
若要將不同的定序指派給輸出，請將 COLLATE 子句套用至 CAST 或 CONVERT 函數結果運算式上。 例如：
  
`SELECT CAST('abc' AS varchar(5)) COLLATE French_CS_AS`
  
## <a name="truncating-and-rounding-results"></a>截斷和捨入結果
將字元或二進位運算式 (**binary**、**char**、**nchar**、**nvarchar**、**varbinary** 或 **varchar**) 轉換成不同資料類型的運算式時，轉換作業可能會截斷輸出資料、僅局部顯示輸出資料，或傳回錯誤。 如果結果太短無法顯示，則會發生這些情況。 會截斷轉換成 **binary**、**char**、**nchar**、**nvarchar**、**varbinary** 或 **varchar** (下表中所顯示的轉換除外)。
  
|來源資料類型|目標資料類型|結果|  
|---|---|---|
|**int**、**smallint** 或 **tinyint**|**char**|*|  
||**varchar**|*|  
||**nchar**|E|  
||**nvarchar**|E|  
|**money**、**smallmoney**、**numeric**、**decimal**、**float** 或 **real**|**char**|E|  
||**varchar**|E|  
||**nchar**|E|  
||**nvarchar**|E|  
  
\* = 結果太短，無法顯示<br /><br />E = 因結果太短無法顯示，而傳回錯誤。
  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 保證只有往返轉換 (亦即，從原始資料類型轉換成某種資料類型之後再轉換回來) 才會各版本都產生相同的值。 下列範例會顯示這類往返轉換：
  
```sql
DECLARE @myval DECIMAL (5, 2);  
SET @myval = 193.57;  
SELECT CAST(CAST(@myval AS VARBINARY(20)) AS DECIMAL(10,5));  
-- Or, using CONVERT  
SELECT CONVERT(DECIMAL(10,5), CONVERT(VARBINARY(20), @myval));  
```  
  
> [!WARNING]  
> 請不要嘗試建構 **binary** 值，然後將其轉換成數值資料類型類別的資料類型。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 不保證在不同的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本之間，將 **decimal** 或 **numeric** 資料類型轉換成 **binary** 的結果都會相同。  
  
下列範例會顯示因太小而無法顯示的結果運算式。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT p.FirstName, p.LastName, SUBSTRING(p.Title, 1, 25) AS Title,
    CAST(e.SickLeaveHours AS char(1)) AS [Sick Leave]  
FROM HumanResources.Employee e JOIN Person.Person p 
    ON e.BusinessEntityID = p.BusinessEntityID  
WHERE NOT e.BusinessEntityID > 5;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```   
FirstName   LastName      Title   Sick Leave
---------   ------------- ------- --------`
Ken         Sanchez       NULL   *
Terri       Duffy         NULL   *
Roberto     Tamburello    NULL   *
Rob         Walters       NULL   *
Gail        Erickson      Ms.    *

(5 row(s) affected)  
```
  
當您轉換具有不同小數位數的資料類型時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 有時會傳回截斷的結果值，有時會傳回捨入的值。 此表格顯示這個行為。
  
|從|至|行為|  
|---|---|---|
|**numeric**|**numeric**|Round|  
|**numeric**|**int**|Truncate|  
|**numeric**|**money**|Round|  
|**money**|**int**|Round|  
|**money**|**numeric**|Round|  
|**float**|**int**|Truncate|  
|**float**|**numeric**|Round<br /><br /> 如果您將使用科學記號標記法的 **float** 值轉換成 **decimal** 或 **numeric**，就會限制為只有 17 個有效位數的值。 有效位數超過 17 的任何值都會捨入為零。|  
|**float**|**datetime**|Round|  
|**datetime**|**int**|Round|  
  
例如，10.6496 和-10.6496 這兩個值可能會被截斷，或在轉換成 **int** 或 **numeric** 類型期間被捨入：
  
```sql
SELECT  CAST(10.6496 AS INT) as trunc1,
        CAST(-10.6496 AS INT) as trunc2,
        CAST(10.6496 AS NUMERIC) as round1,
        CAST(-10.6496 AS NUMERIC) as round2;
 ```
下表將顯示該查詢的結果：
 
|trunc1|trunc2|round1|round2|
|---|---|---|---|
|10|-10|11|-11|
 
轉換目標資料類型的小數位數小於來源資料類型的資料類型時，會將值捨入。 例如，這項轉換會傳回 `$10.3497`：
  
`SELECT CAST(10.3496847 AS money);`
  
將非數值 **char**、**nchar**、**nvarchar** 或 **varchar** 資料轉換成 **decimal**、**float**、**int** 或 **numeric** 時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會傳回錯誤訊息。 當空字串 (" ") 被轉換為 **numeric** 或 **decimal** 時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 也會傳回錯誤。
  
## <a name="certain-datetime-conversions-are-nondeterministic"></a>某些日期時間轉換不具決定性

字串對日期時間轉換不具決定性的樣式如下所示：
  
- 100 以下的所有樣式<sup>1</sup>
- 106  
- 107
- 109
- 113
- 130  
  
<sup>1</sup> 樣式 20 和 21 除外

如需詳細資訊，請參閱[將常值日期字串轉換成 DATE 值的非決定性轉換](../data-types/nondeterministic-convert-date-literals.md)。

## <a name="supplementary-characters-surrogate-pairs"></a>補充字元 (代理字組)
從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 開始，使用補充字元 (SC) 定序時，從 **nchar** 或 **nvarchar** 到較小長度之 **nchar** 或 **nvarchar** 類型的 CAST 作業將不會在代理字組內截斷。 相反地，作業會在補充字元之前截斷。 例如，下列程式碼片段會讓 `@x` 只保留 `'ab'`。 空間不足，無法保留補充字元。
  
```sql
DECLARE @x NVARCHAR(10) = 'ab' + NCHAR(0x10000);  
SELECT CAST (@x AS NVARCHAR(3));  
```  
  
使用 SC 定序時，`CONVERT` 的行為類似 `CAST` 的行為。 如需詳細資訊，請參閱[定序和 Unicode 支援 - 增補字元](../../relational-databases/collations/collation-and-unicode-support.md#Supplementary_Characters)。
  
## <a name="compatibility-support"></a>相容性支援
在舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，**time** 和 **datetime2** 資料類型上之 CAST 和 CONVERT 作業的預設樣式為 121，但任一類型用於計算資料行運算式時除外。 若為計算資料行，預設樣式為 0。 當您建立計算資料行、將它們用於包含自動參數化的查詢或用於條件約束定義時，這種行為就會影響計算資料行。
  
在 [相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#compatibility-levels-and-database-engine-upgrades)高於及等於 110 的情況下，**time** 和 **datetime2** 資料類型上的 CAST 和 CONVERT 作業一律具有 121 作為預設樣式。 如果查詢仰賴舊的行為，則請使用低於 110 的相容性層級，或在受影響的查詢中明確指定 0 樣式。

|[相容性層級](../../t-sql/statements/alter-database-transact-sql-compatibility-level.md#compatibility-levels-and-database-engine-upgrades)值|CAST 和 CONVERT 的預設樣式<sup>1</sup>|計算資料行的預設樣式|  
|------------|------------|------------|
|< **110**|121|0|  
|> = **110**|121|121|  

<sup>1</sup> 計算資料行除外

將資料庫升級為相容性層級高於及等於 110 並不會變更已經儲存至磁碟的使用者資料。 您必須依適當情況手動更正這項資料。 例如，如果您已使用 SELECT INTO，根據包含上述計算資料行運算式的來源建立資料表，則系統會儲存資料 (使用樣式 0) 而非計算資料行定義本身。 您必須手動更新這項資料，以符合樣式 121。
  
## <a name="examples"></a><a name="BKMK_examples"></a> 範例  
  
### <a name="a-using-both-cast-and-convert"></a>A. 使用 CAST 和 CONVERT  
這些範例會擷取定價第一位數是 `3` 的產品名稱，而且會將它們的 `ListPrice` 轉換成 `int`。
  
```sql
-- Use CAST  
USE AdventureWorks2012;  
GO  
SELECT SUBSTRING(Name, 1, 30) AS ProductName, ListPrice  
FROM Production.Product  
WHERE CAST(ListPrice AS int) LIKE '33%';  
GO  
  
-- Use CONVERT.  
USE AdventureWorks2012;  
GO  
SELECT SUBSTRING(Name, 1, 30) AS ProductName, ListPrice  
FROM Production.Product  
WHERE CONVERT(int, ListPrice) LIKE '33%';  
GO  
```  

[!INCLUDE[ssResult](../../includes/ssresult-md.md)] CAST 和 CONVERT 的範例結果集都相同。 

```
ProductName                    ListPrice
------------------------------ ---------------------
LL Road Frame - Black, 58      337.22
LL Road Frame - Black, 60      337.22
LL Road Frame - Black, 62      337.22
LL Road Frame - Red, 44        337.22
LL Road Frame - Red, 48        337.22
LL Road Frame - Red, 52        337.22
LL Road Frame - Red, 58        337.22
LL Road Frame - Red, 60        337.22
LL Road Frame - Red, 62        337.22
LL Road Frame - Black, 44      337.22
LL Road Frame - Black, 48      337.22
LL Road Frame - Black, 52      337.22
Mountain-100 Black, 38         3374.99
Mountain-100 Black, 42         3374.99
Mountain-100 Black, 44         3374.99
Mountain-100 Black, 48         3374.99
HL Road Front Wheel            330.06
LL Touring Frame - Yellow, 62  333.42
LL Touring Frame - Blue, 50    333.42
LL Touring Frame - Blue, 54    333.42
LL Touring Frame - Blue, 58    333.42
LL Touring Frame - Blue, 62    333.42
LL Touring Frame - Yellow, 44  333.42
LL Touring Frame - Yellow, 50  333.42
LL Touring Frame - Yellow, 54  333.42
LL Touring Frame - Yellow, 58  333.42
LL Touring Frame - Blue, 44    333.42
HL Road Tire                   32.60

(28 rows affected)
```
  
### <a name="b-using-cast-with-arithmetic-operators"></a>B. 搭配算術運算子來使用 CAST  
此範例會將年度目前的總銷售額 (`SalesYTD`) 除以佣金百分比 (`CommissionPCT`) 來計算單一資料行 (`Computed`)。 此值會四捨五入為最接近的整數，接著轉換 (CAST) 成 `int` 資料類型。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT CAST(ROUND(SalesYTD/CommissionPCT, 0) AS INT) AS Computed  
FROM Sales.SalesPerson   
WHERE CommissionPCT != 0;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
```  
Computed
------
379753754
346698349
257144242
176493899
281101272
0  
301872549
212623750
298948202
250784119
239246890
101664220
124511336
97688107

(14 row(s) affected)  
```  
  
### <a name="c-using-cast-to-concatenate"></a>C. 利用 CAST 來串連  
此範例會使用 CAST 來串連非字元運算式。 它會使用 AdventureWorksDW 資料庫。
  
```sql
SELECT 'The list price is ' + CAST(ListPrice AS VARCHAR(12)) AS ListPrice  
FROM dbo.DimProduct  
WHERE ListPrice BETWEEN 350.00 AND 400.00;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
ListPrice
------------------------
The list price is 357.06
The list price is 364.09
The list price is 364.09
The list price is 364.09
The list price is 364.09  
```  
  
### <a name="d-using-cast-to-produce-more-readable-text"></a>D. 利用 CAST 來產生其他可讀取的文字  
此範例會使用 SELECT 清單中的 CAST，將 `Name` 資料行轉換成 **char(10)** 資料行。 它會使用 AdventureWorksDW 資料庫。
  
```sql
SELECT DISTINCT CAST(EnglishProductName AS CHAR(10)) AS Name, ListPrice  
FROM dbo.DimProduct  
WHERE EnglishProductName LIKE 'Long-Sleeve Logo Jersey, M';  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Name        ListPrice
----------  ---------
Long-Sleev  31.2437
Long-Sleev  32.4935
Long-Sleev  49.99  
```  
  
### <a name="e-using-cast-with-the-like-clause"></a>E. 搭配 LIKE 子句使用 CAST  
此範例會先將 `money` 資料行 `SalesYTD` 值轉換成資料類型 `int`，接著轉換成資料類別 `char(20)`，讓 `LIKE` 子句可以使用它。
  
```sql
USE AdventureWorks2012;  
GO  
SELECT p.FirstName, p.LastName, s.SalesYTD, s.BusinessEntityID  
FROM Person.Person AS p   
JOIN Sales.SalesPerson AS s   
    ON p.BusinessEntityID = s.BusinessEntityID  
WHERE CAST(CAST(s.SalesYTD AS INT) AS char(20)) LIKE '2%';  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
FirstName        LastName            SalesYTD         BusinessEntityID
---------------- ------------------- ---------------- -------------
Tsvi             Reiter              2811012.7151      279
Syed             Abbas               219088.8836       288
Rachel           Valdez              2241204.0424      289

(3 row(s) affected)  
```
  
### <a name="f-using-convert-or-cast-with-typed-xml"></a>F. 搭配具類型的 XML 來使用 CONVERT 或 CAST  
這些範例示範如何使用 [XML 資料類型和資料行 &#40;SQL Server&#41;](../../relational-databases/xml/xml-data-type-and-columns-sql-server.md)，利用 CONVERT 將資料轉換成 XML 類型。
  
此範例會將含有空白字元、文字和標記的字串轉換成 XML 類型，而且會移除所有無意義的空白字元 (節點之間的界限空白字元)：
  
```sql
SELECT CONVERT(XML, '<root><child/></root>')  
```  
  
這個範例會將含有空格、文字和標記的類似字串轉換成 XML 類型，而且會保留所有無意義的空白字元 (節點之間的界限空白字元)：
  
```sql
SELECT CONVERT(XML, '<root>          <child/>         </root>', 1)  
```  
  
這個範例會將含有空白字元、文字和標記的字串轉換成 XML 類型：
  
```sql
SELECT CAST('<Name><FName>Carol</FName><LName>Elliot</LName></Name>'  AS XML)  
```  
  
如需其他範例，請參閱[建立 XML 資料的執行個體](../../relational-databases/xml/create-instances-of-xml-data.md)。
  
### <a name="g-using-cast-and-convert-with-datetime-data"></a>G. 搭配 datetime 資料使用 CAST 和 CONVERT  
從 `GETDATE()` 值開始，此範例會顯示目前的日期和時間、使用 `CAST` 將目前的日期和時間變更成字元資料類型，然後使用 `CONVERT` 來以 `ISO 8601` 格式顯示日期和時間。
  
```sql
SELECT   
   GETDATE() AS UnconvertedDateTime,  
   CAST(GETDATE() AS NVARCHAR(30)) AS UsingCast,  
   CONVERT(nvarchar(30), GETDATE(), 126) AS UsingConvertTo_ISO8601  ;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
UnconvertedDateTime     UsingCast              UsingConvertTo_ISO8601
----------------------- ---------------------- ------------------------------
2006-04-18 09:58:04.570 Apr 18 2006  9:58AM    2006-04-18T09:58:04.570

(1 row(s) affected)  
```
  
此範例幾乎是上一個範例的相反操作。 此範例會將日期和時間顯示成字元資料，並使用 `CAST` 將字元資料變更成 `datetime` 資料類型，然後使用 `CONVERT` 將字元資料變更成 `datetime` 資料類型。
  
```sql
SELECT   
   '2006-04-25T15:50:59.997' AS UnconvertedText,  
   CAST('2006-04-25T15:50:59.997' AS datetime) AS UsingCast,  
   CONVERT(datetime, '2006-04-25T15:50:59.997', 126) AS UsingConvertFrom_ISO8601 ;  
GO  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
UnconvertedText         UsingCast               UsingConvertFrom_ISO8601
----------------------- ----------------------- ------------------------
2006-04-25T15:50:59.997 2006-04-25 15:50:59.997 2006-04-25 15:50:59.997

(1 row(s) affected)  
```
  
### <a name="h-using-convert-with-binary-and-character-data"></a>H. 搭配二進位和字元資料使用 CONVERT  
這些範例示範如何使用不同的樣式轉換二進位和字元資料的結果。
  
```sql
--Convert the binary value 0x4E616d65 to a character value.  
SELECT CONVERT(CHAR(8), 0x4E616d65, 0) AS [Style 0, binary to character];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Style 0, binary to character
----------------------------
Name  

(1 row(s) affected)  
```
 
此範例示範樣式 1 可以強制截斷結果。 結果集中的字元 0 x 會強制截斷。  
```sql  
SELECT CONVERT(CHAR(8), 0x4E616d65, 1) AS [Style 1, binary to character];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Style 1, binary to character
------------------------------
0x4E616D

(1 row(s) affected)  
```  
 
此範例示範樣式 2 不會截斷結果，因為結果未包含字元 0x。  
```sql  
SELECT CONVERT(CHAR(8), 0x4E616d65, 2) AS [Style 2, binary to character];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Style 2, binary to character
------------------------------
4E616D65

(1 row(s) affected)  
```
  
將字元值 'Name' 轉換成二進位值。  
```sql
SELECT CONVERT(BINARY(8), 'Name', 0) AS [Style 0, character to binary];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Style 0, character to binary
----------------------------
0x4E616D6500000000

(1 row(s) affected)  
```
  
```sql
SELECT CONVERT(BINARY(4), '0x4E616D65', 1) AS [Style 1, character to binary];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Style 1, character to binary
---------------------------- 
0x4E616D65

(1 row(s) affected)  
```  

```sql
SELECT CONVERT(BINARY(4), '4E616D65', 2) AS [Style 2, character to binary];  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Style 2, character to binary  
----------------------------------  
0x4E616D65

(1 row(s) affected)  
```  
  
### <a name="i-converting-date-and-time-data-types"></a>I. 轉換 date 和 time 資料類型  
此範例示範 date、time 和 datetime 資料類型的轉換。
  
```sql
DECLARE @d1 DATE, @t1 TIME, @dt1 DATETIME;  
SET @d1 = GETDATE();  
SET @t1 = GETDATE();  
SET @dt1 = GETDATE();  
SET @d1 = GETDATE();  
-- When converting date to datetime the minutes portion becomes zero.  
SELECT @d1 AS [DATE], CAST (@d1 AS DATETIME) AS [date as datetime];  
-- When converting time to datetime the date portion becomes zero   
-- which converts to January 1, 1900.  
SELECT @t1 AS [TIME], CAST (@t1 AS DATETIME) AS [time as datetime];  
-- When converting datetime to date or time non-applicable portion is dropped.  
SELECT @dt1 AS [DATETIME], CAST (@dt1 AS DATE) AS [datetime as date], 
   CAST (@dt1 AS TIME) AS [datetime as time];  
```  

### <a name="j-using-convert-with-datetime-data-in-different-formats"></a>J. 以不同的 datetime 資料使用 CONVERT  
從 `GETDATE()` 值開始，此範例會使用 `CONVERT` 來顯示本文[＜日期和時間樣式＞](#date-and-time-styles)一節中的所有日期和時間樣式。

|格式 #|範例查詢|範例結果|
|--------|------------------------------------|------------------|
|0|`SELECT CONVERT(NVARCHAR, GETDATE(), 0)`|2019 年 8 月 23 日下午 1:39|
|1|`SELECT CONVERT(NVARCHAR, GETDATE(), 1)`|08/23/19|
|2|`SELECT CONVERT(NVARCHAR, GETDATE(), 2)`|19.08.23|
|3|`SELECT CONVERT(NVARCHAR, GETDATE(), 3)`|23/08/19|
|4|`SELECT CONVERT(NVARCHAR, GETDATE(), 4)`|23.08.19|
|5|`SELECT CONVERT(NVARCHAR, GETDATE(), 5)`|23-08-19|
|6|`SELECT CONVERT(NVARCHAR, GETDATE(), 6)`|23 Aug 19|
|7|`SELECT CONVERT(NVARCHAR, GETDATE(), 7)`|Aug 23, 19|
|8 或 24 或108|`SELECT CONVERT(NVARCHAR, GETDATE(), 8)`|13:39:17|
|9 或 109|`SELECT CONVERT(NVARCHAR, GETDATE(), 9)`|2019 年 8 月 23 日下午 1:39:17:090|
|10|`SELECT CONVERT(NVARCHAR, GETDATE(), 10)`|08-23-19|
|11|`SELECT CONVERT(NVARCHAR, GETDATE(), 11)`|19/08/23|
|12|`SELECT CONVERT(NVARCHAR, GETDATE(), 12)`|190823|
|13 或 113|`SELECT CONVERT(NVARCHAR, GETDATE(), 13)`|2019 年 8 月 23 日 13:39:17:090|
|14 或 114|`SELECT CONVERT(NVARCHAR, GETDATE(), 14)`|13:39:17:090|
|20 或 120|`SELECT CONVERT(NVARCHAR, GETDATE(), 20)`|2019-08-23 13:39:17|
|21 或 25 或 121|`SELECT CONVERT(NVARCHAR, GETDATE(), 21)`|2019-08-23 13:39:17.090|
|22|`SELECT CONVERT(NVARCHAR, GETDATE(), 22)`|08/23/19 下午 1:39:17|
|23|`SELECT CONVERT(NVARCHAR, GETDATE(), 23)`|2019-08-23|
|101|`SELECT CONVERT(NVARCHAR, GETDATE(), 101)`|08/23/2019|
|102|`SELECT CONVERT(NVARCHAR, GETDATE(), 102)`|2019.08.23|
|103|`SELECT CONVERT(NVARCHAR, GETDATE(), 103)`|23/08/2019|
|104|`SELECT CONVERT(NVARCHAR, GETDATE(), 104)`|23.08.2019|
|105|`SELECT CONVERT(NVARCHAR, GETDATE(), 105)`|23-08-2019|
|106|`SELECT CONVERT(NVARCHAR, GETDATE(), 106)`|23 Aug 2019|
|107|`SELECT CONVERT(NVARCHAR, GETDATE(), 107)`|Aug 23, 2019|
|110|`SELECT CONVERT(NVARCHAR, GETDATE(), 110)`|08-23-2019|
|111|`SELECT CONVERT(NVARCHAR, GETDATE(), 111)`|2019/08/23|
|112|`SELECT CONVERT(NVARCHAR, GETDATE(), 112)`|20190823|
|113|`SELECT CONVERT(NVARCHAR, GETDATE(), 113)`|23 Aug 2019 13:39:17.090|
|120|`SELECT CONVERT(NVARCHAR, GETDATE(), 120)`|2019-08-23 13:39:17|
|121|`SELECT CONVERT(NVARCHAR, GETDATE(), 121)`|2019-08-23 13:39:17.090|
|126|`SELECT CONVERT(NVARCHAR, GETDATE(), 126)`|2019-08-23T13:39:17.090|
|127|`SELECT CONVERT(NVARCHAR, GETDATE(), 127)`|2019-08-23T13:39:17.090|
|130|`SELECT CONVERT(NVARCHAR, GETDATE(), 130)`|22 ذو الحجة 1440  1:39:17.090P|
|131|`SELECT CONVERT(NVARCHAR, GETDATE(), 131)`|22/12/1440  1:39:17.090PM|

### <a name="k-effects-of-data-type-precedence-in-allowed-conversions"></a><a name="precedence-example"></a> K. 經允許轉換中的資料類型優先順序效果  
下列範例會定義 VARCHAR 類型的變數、將整數值指派給變數，然後使用字串選取變數的串連。

```sql
DECLARE @string VARCHAR(10);
SET @string = 1;
SELECT @string + ' is a string.' AS Result
```

[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
Result
-----------------------
1 is a string.
```  

Int 值 1 已轉換成 VARCHAR。

這個範例會顯示類似的查詢，但改為使用 int 變數：

```sql
DECLARE @notastring INT;
SET @notastring = '1';
SELECT @notastring + ' is not a string.' AS Result
```

在此情況下，SELECT 陳述式會擲回下列錯誤：

```
Msg 245, Level 16, State 1, Line 3
Conversion failed when converting the varchar value ' is not a string.' to data type int.
```

為了評估運算式 `@notastring + ' is not a string.'`，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會遵循資料類型優先順序規則，在計算運算式的結果前完成隱含轉換。 因為 int 具有比 VARCHAR 更高的優先順序，所以 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會嘗試將字串轉換成整數，但由於此字串無法轉換成整數，因此會失敗。 

若提供可進行轉換的字串，則陳述式會成功，如下列範例所示：

```SQL
DECLARE @notastring INT;
SET @notastring = '1';
SELECT @notastring + '1'
```

在此情況下，字串 `'1'` 可轉換成整數值 1，所以此 SELECT 陳述式將會傳回值 2。 在提供的資料類型為整數時，+ 運算子會變成加法數學運算子，而非字串串連。

## <a name="examples-sssdwfull-and-sspdw"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 和 [!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="l-using-cast-and-convert"></a>L. 使用 CAST 和 CONVERT  
此範例會擷取定價第一位數是 `3` 的產品名稱，而且會將這些產品的 `ListPrice` 轉換成 **int**。其會使用 `AdventureWorksDW2016` 資料庫。
  
```sql
SELECT EnglishProductName AS ProductName, ListPrice  
FROM dbo.DimProduct  
WHERE CAST(ListPrice AS int) LIKE '3%';  
```  
  
此範例會示範相同的查詢，但使用的是 CONVERT 而非 CAST。 其會使用 `AdventureWorksDW2016` 資料庫。
  
```sql
SELECT EnglishProductName AS ProductName, ListPrice  
FROM dbo.DimProduct  
WHERE CONVERT(INT, ListPrice) LIKE '3%';  
```  
  
### <a name="m-using-cast-with-arithmetic-operators"></a>M. 搭配算術運算子來使用 CAST  
此範例會將產品單價 (`UnitPrice`) 除以折扣百分比 (`UnitPriceDiscountPct`) 來計算單一資料行值。 此結果接著會四捨五入為最接近的整數，最後轉換 (CAST) 成 `int` 資料類型。 這個範例會使用 `AdventureWorksDW2016` 資料庫。
  
```sql
SELECT ProductKey, UnitPrice,UnitPriceDiscountPct,  
       CAST(ROUND (UnitPrice*UnitPriceDiscountPct,0) AS int) AS DiscountPrice  
FROM dbo.FactResellerSales  
WHERE SalesOrderNumber = 'SO47355'   
      AND UnitPriceDiscountPct > .02;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
ProductKey  UnitPrice  UnitPriceDiscountPct  DiscountPrice
----------  ---------  --------------------  -------------
323         430.6445   0.05                  22
213         18.5043    0.05                  1
456         37.4950    0.10                  4
456         37.4950    0.10                  4
216         18.5043    0.05                  1  
```  
  
### <a name="n-using-cast-with-the-like-clause"></a>N. 搭配 LIKE 子句使用 CAST  
此範例會先將 **money** 資料行 `ListPrice` 轉換成 **int** 類型，接著轉換成 **char(20)** 類型，讓 LIKE 子句可以使用它。 這個範例會使用 `AdventureWorksDW2016` 資料庫。  
  
```sql
SELECT EnglishProductName AS Name, ListPrice  
FROM dbo.DimProduct  
WHERE CAST(CAST(ListPrice AS INT) AS CHAR(20)) LIKE '2%';  
```  
  
### <a name="o-using-cast-and-convert-with-datetime-data"></a>O. 搭配 datetime 資料使用 CAST 和 CONVERT  
此範例會顯示目前的日期和時間，並使用 CAST 將目前的日期和時間變更成字元資料類型，最後使用 CONVERT 以 ISO 8601 格式顯示日期和時間。 這個範例會使用 `AdventureWorksDW2016` 資料庫。
  
```sql
SELECT TOP(1)  
   SYSDATETIME() AS UnconvertedDateTime,  
   CAST(SYSDATETIME() AS NVARCHAR(30)) AS UsingCast,  
   CONVERT(NVARCHAR(30), SYSDATETIME(), 126) AS UsingConvertTo_ISO8601  
FROM dbo.DimCustomer;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
UnconvertedDateTime     UsingCast                     UsingConvertTo_ISO8601  
---------------------   ---------------------------   ---------------------------  
07/20/2010 1:44:31 PM   2010-07-20 13:44:31.5879025   2010-07-20T13:44:31.5879025  
```  
  
此範例是上一個範例的相反操作。 此範例會將日期和時間顯示為字元資料，並使用 CAST 將字元資料變更成 **datetime** 資料類型，然後使用 CONVERT 將字元資料變更成 **datetime** 資料類型。 這個範例會使用 `AdventureWorksDW2016` 資料庫。
  
```sql
SELECT TOP(1)   
   '2010-07-25T13:50:38.544' AS UnconvertedText,  
CAST('2010-07-25T13:50:38.544' AS DATETIME) AS UsingCast,  
   CONVERT(DATETIME, '2010-07-25T13:50:38.544', 126) AS UsingConvertFrom_ISO8601  
FROM dbo.DimCustomer;  
```  
  
[!INCLUDE[ssResult](../../includes/ssresult-md.md)]
  
```  
UnconvertedText         UsingCast               UsingConvertFrom_ISO8601
----------------------- ----------------------- ------------------------
2010-07-25T13:50:38.544 07/25/2010 1:50:38 PM   07/25/2010 1:50:38 PM  
```  

## <a name="see-also"></a>另請參閱
[資料類型優先順序 &#40;Transact-SQL&#41;](../../t-sql/data-types/data-type-precedence-transact-sql.md)       
[資料類型轉換 &#40;資料庫引擎&#41;](../../t-sql/data-types/data-type-conversion-database-engine.md)     
[FORMAT &#40;Transact-SQL&#41;](../../t-sql/functions/format-transact-sql.md)      
[STR &#40;Transact-SQL&#41;](../../t-sql/functions/str-transact-sql.md)     
[SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)      
[系統函數 &#40;Transact-SQL&#41;](../../relational-databases/system-functions/system-functions-category-transact-sql.md)      
[定序與 Unicode 支援](../../relational-databases/collations/collation-and-unicode-support.md)      
[撰寫國際通用的 Transact-SQL 陳述式](../../relational-databases/collations/write-international-transact-sql-statements.md)       
  
