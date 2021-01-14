---
title: bcp 公用程式
description: 大量複製程式 (bcp) 公用程式會在 SQL Server 執行個體與使用者指定格式的資料檔之間大量複製資料。
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: tools-other
ms.topic: conceptual
helpviewer_keywords:
- bcp utility [SQL Server]
- exporting data
- row exporting [SQL Server]
- copying data [SQL Server], bcp utility
- command prompt utilities [SQL Server], bcp
- tables [SQL Server], importing data
- column importing [SQL Server]
- bcp utility [SQL Server], command options
- file exporting [SQL Server]
- bulk copy [SQL Server]
- bcp utility [SQL Server], about bcp utility
- tables [SQL Server], exporting data
- row importing [SQL Server]
- importing data, bcp utility
- file importing [SQL Server]
- column exporting [SQL Server]
ms.assetid: c0af54f5-ca4a-4995-a3a4-0ce39c30ec38
author: markingmyname
ms.author: maghan
ms.reviewer: v-daenge
ms.custom: seo-lt-2019
ms.date: 09/11/2020
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017'
ms.openlocfilehash: 018508727950aa370fb7b3879541d04ff8384ced
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98169379"
---
# <a name="bcp-utility"></a>bcp 公用程式

[!INCLUDE[SQL Server Azure SQL Database Synapse Analytics PDW ](../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

> 如需在 Linux 上使用 bcp 的資訊，請參閱[在 Linux 上安裝 sqlcmd 與 bcp](../linux/sql-server-linux-setup-tools.md)。
>
> 如需使用 bcp 搭配 Azure Synapse Analytics 的詳細資訊，請參閱[使用 bcp 載入資料](/azure/sql-data-warehouse/sql-data-warehouse-load-with-bcp)。

**b** ulk **c** opy **p** rogram 公用程式 (**bcp**) 會以使用者指定的格式在 [!INCLUDE[msCoName](../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體與資料檔案之間大量複製資料。 您可以利用 **bcp** 公用程式，將大量的新資料列匯入 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 資料表，或將資料表的資料匯出至資料檔案。 除了搭配 **bcp** 選項使用之外，此公用程式不需要任何 [!INCLUDE[tsql](../includes/tsql-md.md)]方面的知識。 若要將資料匯入資料表中，您必須使用專為這份資料表而建立的格式檔，或了解資料表的結構及其資料行的有效資料類型。  

![主題連結圖示](../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") 針對用於 **bcp** 語法的語法慣例，請參閱 [Transact-SQL 語法慣例 &#40;Transact-SQL&#41;](../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。  

> [!NOTE]
> 若您使用 **bcp** 備份資料，請建立格式檔案以記錄資料格式。 **bcp** 資料檔案 **不包含** 任何結構描述或格式資訊，所以如果資料表或檢視表遭到卸除，而您又沒有格式檔案，即可能就無法匯入資料。

## <a name="download-the-latest-version-of-bcp-utility"></a>下載最新版的 bcp 公用程式

**[![下載 bcp for x64](../ssdt/media/download.png) 下載適用於 SQL Server (x64) 的 Microsoft 命令列公用程式 15](https://go.microsoft.com/fwlink/?linkid=2142258)**
<br>**[![下載 bcp for x86](../ssdt/media/download.png) 下載適用於 SQL Server (x86) 的 Microsoft 命令列公用程式 15](https://go.microsoft.com/fwlink/?linkid=2142257)**

命令列工具已正式推出，但是將會透過 [!INCLUDE[sql-server-2019](../includes/sssqlv15-md.md)] 的安裝程式套件發行。

### <a name="version-information"></a>版本資訊

版本號碼：15.0.2 <br>
組建編號：15.0.2000.5<br>
發行日期：2020 年 9 月 11 日

新版本的 SQLCMD 支援 Azure AD 驗證，包括 SQL Database、Azure Synapse Analytics 和 Always Encrypted 功能的 Multi-Factor Authentication (MFA) 支援。
新的 BCP 支援 Azure AD 驗證，包括 SQL Database 與 Azure Synapse Analytics 的 Multi-Factor Authentication (MFA) 支援。

### <a name="system-requirements"></a>系統需求

Windows 10、Windows 7、Windows 8、Windows 8.1、Windows Server 2008、Windows Server 2008 R2、Windows Server 2008 R2 SP1、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016、Windows Server 2019

此元件同時需要 [Windows Installer 4.5](https://www.microsoft.com/download/details.aspx?id=8483) 及 [Microsoft ODBC Driver 17 for SQL Server](https://aka.ms/downloadmsodbcsql)。

若要檢查 BCP 版本，請執行 `bcp /v` 命令並確認正在使用 15.0.2000.5 版或更新版本。

<table><th>語法</th><tr><td><pre>
bcp [<a href="#db_name">database_name.</a>] <a href="#schema">schema</a>.{<a href="#tbl_name">table_name</a> | <a href="#vw_name">view_name</a> | <a href="#query">"query"</a>}
    {<a href="#in">in</a> <a href="#data_file">data_file</a> | <a href="#out">out</a> <a href="#data_file">data_file</a> | <a href="#qry_out">queryout</a> <a href="#data_file">data_file</a> | <a href="#format">format</a> <a href="#format">nul</a>}
<a>                                                                                                         </a>
    [<a href="#a">-a packet_size</a>]
    [<a href="#b">-b batch_size</a>]
    [<a href="#c">-c</a>]
    [<a href="#C">-C { ACP | OEM | RAW | code_page } </a>]
    [<a href="#d">-d database_name</a>]
    [<a href="#D">-D</a>]
    [<a href="#e">-e err_file</a>]
    [<a href="#E">-E</a>]
    [<a href="#f">-f format_file</a>]
    [<a href="#F">-F first_row</a>]
    [<a href="#G">-G Azure Active Directory Authentication</a>]
    [<a href="#h">-h"hint [,...n]"</a>]
    [<a href="#i">-i input_file</a>]
    [<a href="#k">-k</a>]
    [<a href="#K">-K application_intent</a>]
    [<a href="#l">-l login_timeout</a>]
    [<a href="#L">-L last_row</a>]
    [<a href="#m">-m max_errors</a>]
    [<a href="#n">-n</a>]
    [<a href="#N">-N</a>]
    [<a href="#o">-o output_file</a>]
    [<a href="#P">-P password</a>]
    [<a href="#q">-q</a>]
    [<a href="#r">-r row_term</a>]
    [<a href="#R">-R</a>]
    [<a href="#S">-S [server_name[\instance_name]</a>]
    [<a href="#t">-t field_term</a>]
    [<a href="#T">-T</a>]
    [<a href="#U">-U login_id</a>]
    [<a href="#v">-v</a>]
    [<a href="#V">-V (80 | 90 | 100 | 110 | 120 | 130 ) </a>]
    [<a href="#w">-w</a>]
    [<a href="#x">-x</a>]
</pre></td></tr></table>

## <a name="arguments"></a>引數

 _**data\_file**_<a name="data_file"></a>  
 這是資料檔案的完整路徑。 當資料大量匯入 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]時，資料檔案會包含要複製到指定資料表或檢視表的資料。 當從 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]大量匯出資料時，資料檔案會包含從資料表或檢視表複製的資料。 路徑可以有 1 至 255 個字元。 資料檔案最多可以包含 2^63 - 1 個資料列。  

 _**database\_name**_<a name="db_name"></a>  
 這是指定之資料表或檢視表所在的資料庫名稱。 若未指定，這就是使用者的預設資料庫。  

 您也可以使用 **-d** 明確指定資料庫名稱。  

 **in** *data_file* \| **out** *data_file* \| **queryout** *data_file* \| **format nul**  
 請依照下列方式指定大量複製的方向：  
  
- **in**<a name="in"></a> 會從檔案複製到資料庫資料表或檢視。  
  
- **out**<a name="out"></a> 會從資料庫資料表或檢視複製到檔案。 若您指定現有的檔案，將會覆寫該檔案。 擷取資料時，**bcp** 公用程式會以 Null 代表空白字串，並以空白字串代表 Null 字串。  
  
- **queryout**<a name="qry_out"></a> 會從查詢複製資料，而且只有從查詢複製大量資料時才可指定。  
  
- **format**<a name="format"></a> 會依據指定的選項 ( **-n**、 **-c**、 **-w** 或 **-N**) 及資料表或檢視分隔符號建立格式檔案。 大量複製資料時， **bcp** 命令可以參考格式檔案，讓您不需要以互動方式重新輸入格式資訊。 **format** 選項需要 **-f** 選項；建立 XML 格式檔案也需要 **-x** 選項。 如需詳細資訊，請參閱[建立格式檔案 &#40;SQL Server&#41;](../relational-databases/import-export/create-a-format-file-sql-server.md)。 您必須將 **nul** 指定為值 (**format nul**)。  
  
 _**owner**_<a name="schema"></a>  
 這是資料表或檢視表的擁有者名稱。 如果執行該作業的使用者擁有指定的資料表或檢視表，則可選擇是否要使用 *owner* 。 如果未指定 *owner* ，且執行作業的使用者並不擁有指定的資料表或檢視表，則 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 會傳回錯誤訊息，並取消作業。  
  
**"** _**query**_ **"** <a name="query"></a> 是傳回結果集的 [!INCLUDE[tsql](../includes/tsql-md.md)] 查詢。 如果查詢傳回多個結果集，則只會將第一個結果集複製到資料檔案中，並會忽略接下來的結果集。 請利用雙引號括住查詢，利用單引號括住內嵌在查詢中的任何項目。 從查詢中複製大量資料時，也必須指定 **queryout** 。  
  
 只要在預存程序內參考的所有資料表在 bcp 陳述式執行之前就已存在，查詢就可以參考預存程序。 例如，如果預存程序產生暫存資料表，則 **bcp** 陳述式會失敗，這是因為暫存資料表只可在執行階段使用，而無法在陳述式執行階段使用。 在此種情況下，請考慮將預存程序的結果插入資料表中，然後使用 **bcp** 將資料表的資料複製到資料檔案。  
  
 _**table\_name**_<a name="tbl_name"></a>  
 這是將資料匯入 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] (**in**) 時的目的地資料表名稱，以及從 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] (**out**) 匯出資料時的來源資料表名稱。  
  
 _**view\_name**_<a name="vw_name"></a>   
 這是將資料複製到 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] (**in**) 時的目的地檢視表名稱，以及從 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] (**out**) 中複製資料時的來源檢視表名稱。 只有所有資料行都參考相同資料表的檢視表，才能用來做為目的地檢視表。 如需將資料複製到檢視表之限制的詳細資訊，請參閱 [INSERT &#40;Transact-SQL&#41;](../t-sql/statements/insert-transact-sql.md)。  
  
 **-a** _**packet\_size**_<a name="a"></a>  
 指定伺服器所收送之每個網路封包的位元組數。 您可以利用 [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] (或 **sp_configure** 系統預存程序) 來設定伺服器組態選項。 但是使用此選項可以個別地覆寫伺服器組態選項。 *packet_size* 可介於 4096 位元組到 65535 個位元組；預設值是 4096。  
  
 增加封包大小可以增強大量複製作業的效能。 若要求了較大的封包但無法為您授與該封包，便會使用預設值。 **bcp** 公用程式所產生的效能統計資料，會顯示所用的封包大小。  
  
 **-b** _**batch\_size**_<a name="b"></a>  
 指定每一批次匯入資料的資料列數。 每一批次會以個別交易的方式 (在認可之前匯入整個批次) 匯入及記錄。 根據預設，資料檔案中的所有資料列是以一個批次匯入。 若要在多個批次之間分散資料列，請指定小於資料檔案之資料列數目的 *batch_size* 。 如果有任何批次的交易失敗，只回復目前批次的插入項。 之後的失敗不會影響已認可的交易所匯入的批次。  
  
 請勿同時使用此選項與 **-h "** ROWS_PER_BATCH **=** _bb_ **"** 選項。  
 
 **-c**<a name="c"></a>  
 利用字元資料類型來執行作業。 並非每個欄位都有這個選項的提示。它使用 **char** 作為儲存類型 (沒有前置詞)、欄位分隔符號是 **\t** (定位字元)，且資料列結束字元是 **\r\n** (新行字元)。 **-c** 與 **-w** 不相容。  
  
 如需詳細資訊，請參閱[使用 Unicode 字元格式匯入或匯出資料 &#40;SQL Server&#41;](../relational-databases/import-export/use-character-format-to-import-or-export-data-sql-server.md)。  
  
 **-C** { **ACP** \| **OEM** \| **RAW** \| *code_page* }<a name="C"></a>   
 指定資料檔案中之資料的字碼頁。 只有當資料包含字元值大於 127 或小於 32 的 *code_page* 、 **code_page**, **varcode_page** 資料行時， **code_page** columns with code_pageacter values greater than 127 or less than 32.  
  
> [!NOTE]
> 除非您希望 65001 選項的優先順序高於定序/字碼頁規格，否則建議您在格式檔案中指定每個資料行的定序名稱。
  
|字碼頁值|描述|  
|---------------------|-----------------|  
|ACP|[!INCLUDE[vcpransi](../includes/vcpransi-md.md)]/Microsoft Windows (ISO 1252)。|  
|OEM|用戶端所用的預設字碼頁。 如果未指定 **-C** ，這是預設字碼頁。|  
|RAW|不會將字碼頁轉換成另一種字碼頁。 這是最快的選項，因為不進行轉換。|  
|*code_page*|特定字碼頁編號；如 850。<br /><br /> 13 版之前的版本 ([!INCLUDE[ssSQL15](../includes/sssql16-md.md)]) 不支援字碼頁 65001 (UTF-8 編碼)。 從 13 版開始，可以將 UTF-8 編碼匯入舊版的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]。|  
|||
  
 **-d** _**database\_name**_<a name="d"></a>   
 指定要連接的資料庫。 根據預設，bcp.exe 會連線到使用者的預設資料庫。 如果指定 -d database_name 和三部分名稱 (database_name.schema.table，作為第一個參數傳遞至 bcp.exe)，將會發生錯誤，因為您無法指定資料庫名稱兩次。 如果 *database_name* 的開頭是連字號 (-) 或斜線 (/)，請勿在 **-d** 與資料庫名稱之間加上空格。  

**-D**<a name="D"></a>  
使傳遞至 `bcp` `-S` 選項的值解譯為資料來源名稱 (DSN)。 DSN 可用來內嵌驅動程式選項，以簡化命令列；施行無法從命令列存取的驅動程式選項，例如 MultiSubnetFailover；或協助保護機密認證不被視為命令列引數。 如需詳細資訊，請參閱[使用 sqlcmd 進行連線](../connect/odbc/linux-mac/connecting-with-sqlcmd.md)中的 「sqlcmd 和 bcp 中的 DSN 支援」。


 **-e** _**err\_file**_<a name="e"></a>  
 指定錯誤檔的完整路徑，該錯誤檔用來儲存 **bcp** 公用程式無法從檔案傳送至資料庫的任何資料列。 **bcp** 命令所產生的錯誤訊息，會送往使用者的工作站。 如果未使用這個選項，就不會建立錯誤檔。  
  
 如果 *err_file* 的開頭是連字號 (-) 或斜線 (/)，請勿在 **-e** 與 *err_file* 值之間加上空格。  
  
**-E**<a name="E"></a>

指定識別欄位要使用匯入之資料檔案中的一個或多個識別值。 如果未提供 **-E** ，就會略過匯入的資料檔案中此資料行的識別值，且 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 會根據建立資料表期間所指定的初始值與遞增值，自動指派唯一值。  如需詳細資訊，請參閱 [DBCC CHECKIDENT](../t-sql/database-console-commands/dbcc-checkident-transact-sql.md)。
  
 如果資料檔案中沒有資料表或檢視表中之識別欄位的值，請利用格式檔指定，在匯入資料時，應該略過資料表或檢視表中的識別欄位； [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 會自動指派資料行的唯一值。
  
 **-E** 選項有特殊權限需求。 如需詳細資訊，請參閱本主題稍後的[備註](#remarks)一節。  
   
 **-f** _**format\_file**_<a name="f"></a>  
 指定格式檔的完整路徑。 這個選項的意義會隨著使用它的環境而有所不同，如下所示：  
  
- 如果 **-f** 與 **format** 選項一起使用，會對指定的資料表或檢視表建立所指定的 *format_file* 。 若要建立 XML 格式檔案，也請指定 **-x** 選項。 如需詳細資訊，請參閱[建立格式檔案 &#40;SQL Server&#41;](../relational-databases/import-export/create-a-format-file-sql-server.md)。  
  
- 搭配 **in** 或 **out** 選項一起使用時， **-f** 需要現有的格式檔案。  
  
    > [!NOTE]
    > 使用格式檔，其中的 **in** 或 **out** 選項為選擇性。 沒有 **-f** 選項時，如果未指定 **-n**、 **-c**、 **-w** 或 **-N** ，命令會提示您輸入格式資訊，且可讓您將回應儲存在格式檔案中 (預設檔案名稱是 Bcp.fmt)。
  
 如果 *format_file* 的開頭是連字號 (-) 或斜線 (/)，請勿在 **-f** 與 *format_file* 值之間加上空格。  
  
**-F** _**first\_row**_<a name="F"></a>  
 指定要從資料表匯出或從資料檔案匯入之第一個資料列的號碼。 這個參數需要大於 (>) 0、小於 (<) 或等於 (=) 總列數的值。 如果沒有這個參數，預設值是檔案中的第一個資料列。  
  
 *first_row* 可以是值高達 2^63-1 的正整數。 **-F** *first_row* 以 1 為基礎。  

**-G**<a name="G"></a>

 這個參數在連線到 Azure SQL Database 或 Azure Synapse Analytics 時由用戶端使用，以指定使用 Azure Active Directory 驗證來驗證使用者。 -G 參數需要 [14.0.3008.27 版或更新版本](https://go.microsoft.com/fwlink/?LinkID=825643)。 若要判斷您的版本，請執行 bcp -v。 如需詳細資訊，請參閱[使用 Azure Active Directory 驗證以用於 SQL Database 或 Azure Synapse Analytics 驗證](/azure/sql-database/sql-database-aad-authentication)。 

> [!IMPORTANT]
> **-G** 選項只適用於 Azure SQL Database 和 Azure 資料倉儲。
> Linux 或 macOS 目前不支援 AAD 整合式與互動式驗證。

> [!TIP]
> 若要檢查 bcp 版本是否包含 Azure Active Directory 驗證 (AAD) 的支援，請鍵入 **bcp --** (bcp\<space>\<dash>\<dash>)，並確認在可用引數的清單中看到 -G。

- **Azure Active Directory 使用者名稱和密碼：** 

    當您想要使用 Azure Active Directory 使用者名稱和密碼時，您可以提供 **-G** 選項，並同時提供 **-U** 和 **-P** 選項來使用使用者名稱和密碼。 

    下列範例使用 Azure AD 使用者名稱和密碼來匯出資料，其中使用者和密碼是 AAD 認證。 此範例會從 `aadserver.database.windows.net` Azure 伺服器的 `testdb` 資料庫匯出 `bcptest` 資料表，並將資料儲存在 `c:\last\data1.dat` 檔案中：

    ```cmd
    bcp bcptest out "c:\last\data1.dat" -c -t -S aadserver.database.windows.net -d testdb -G -U alice@aadtest.onmicrosoft.com -P xxxxx
    ```

    下列範例使用 Azure AD 使用者名稱和密碼來匯入資料，其中使用者和密碼是 AAD 認證。 此範例會使用 Azure AD 使用者/密碼，將資料從 `c:\last\data1.dat` 檔案匯入 `aadserver.database.windows.net` Azure 伺服器上 `testdb` 資料庫的 `bcptest` 資料表：

    ```cmd
    bcp bcptest in "c:\last\data1.dat" -c -t -S aadserver.database.windows.net -d testdb -G -U alice@aadtest.onmicrosoft.com -P xxxxx
    ```

- **Azure Active Directory 整合式**

    若是 Azure Active Directory 整合式驗證，請提供 **-G** 選項，但不提供使用者名稱或密碼。 此組態假設目前的 Windows 使用者帳戶 (執行 bcp 命令的帳戶) 與 Azure AD 同盟：

    下列範例使用 Azure AD 整合帳戶來匯出資料。 範例會使用從 Azure server `bcptest` 整合的 Azure AD，從資料庫 `testdb``aadserver.database.windows.net` 匯出資料表，並將資料儲存在檔案中 `c:\last\data2.dat`：

    ```cmd
    bcp bcptest out "c:\last\data2.dat" -S aadserver.database.windows.net -d testdb -G -c -t
    ```

    下列範例使用 Azure AD 整合驗證來匯入資料。此範例會使用 Azure AD 整合驗證將資料從 `c:\last\data2.txt` 檔案匯入 `aadserver.database.windows.net` Azure 伺服器上 `testdb` 資料庫的 `bcptest` 資料表：

    ```cmd
    bcp bcptest in "c:\last\data2.dat" -S aadserver.database.windows.net -d testdb -G -c -t
    ```

- **Azure Active Directory 互動式**  

   Azure SQL Database 與 Azure Synapse Analytics 的 Azure AD 互動式驗證，其允許使用支援 Multi-Factor Authentication 的互動式方法。 如需詳細資訊，請參閱 [Active Directory 互動式驗證](../ssdt/azure-active-directory.md#active-directory-interactive-authentication)。

   Azure AD 互動需要 **bcp** [15.0.1000.34 版](#download-the-latest-version-of-bcp-utility) 或更新版本，以及 [ODBC 17.2 版或更新版本](https://aka.ms/downloadmsodbcsql)。  

   若要啟用互動式驗證，請在不使用密碼的情況下，僅以使用者名稱 (-U) 提供 -G 選項。

   下列範例會使用 Azure AD 互動模式來匯出資料，其中指出使用者代表 AAD 帳戶的使用者名稱。 此為與在上一區段中所使用的相同範例：*Azure Active Directory 使用者名稱與密碼*。  

   互動模式需要手動輸入密碼，若為已啟用多重要素驗證的帳戶，請完成您已設定的 MFA 驗證方法。

   ```cmd
   bcp bcptest out "c:\last\data1.dat" -c -t -S aadserver.database.windows.net -d testdb -G -U alice@aadtest.onmicrosoft.com
   ```

   如果 Azure AD 使用者是使用 Windows 帳戶的網域同盟使用者，則命令列中所需的使用者名稱會包含其網域帳戶 (例如，joe@contoso.com 如下所示)：

   ```cmd
   bcp bcptest out "c:\last\data1.dat" -c -t -S aadserver.database.windows.net -d testdb -G -U joe@contoso.com
   ```

   如果來賓使用者存在於特定 Azure AD 中，且屬於 SQL Database 中具有執行 bcp 命令的資料庫權限群組，則會使用其來賓使用者別名 (例如， *keith0@adventureworks.com* )。
  
**-h** _**"load hints**_[ ,... *n*] **"** <a name="h"></a> 指定將資料大量匯入資料表或檢視期間所要使用的一或多個提示。  
  
* **ORDER**(**_column_[ASC | DESC] [** , **..._n_])**  
資料檔案中之資料的排序順序。 如果匯入資料時是依照資料表的叢集索引來排序，將可提升大量匯入的效能。 如果不是依照叢集索引鍵的順序排序資料檔案，或是資料表沒有叢集索引，便會忽略 ORDER 子句。 提供的資料行名稱必須是目的地資料表中的有效資料行名稱。 根據預設， **bcp** 會假設資料檔案沒有排序。 為了達到最佳的大量匯入效果， [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 也會驗證匯入的資料是否已排序。  
  
* **ROWS_PER_BATCH** **=** _**bb**_  
每一批資料的資料列數目 (如 *bb*)。 在未指定 **-b** 時使用，結果會將整個資料檔案當做單一交易來傳給伺服器。 伺服器根據 *bb* 值，將大量載入最佳化。 根據預設，ROWS_PER_BATCH 是未知的。  
  
* **KILOBYTES_PER_BATCH** **=** _**cc**_  
每一批資料的近似 KB 數 (如 *cc*)。 依預設，KILOBYTES_PER_BATCH 是未知的。  
  
* **TABLOCK**  
指定在大量載入作業期間，取得大量更新資料表層級鎖定；否則，便取得資料列層級鎖定。 這個提示會大幅提升效能，因為在大量複製作業期間保留鎖定，會減少競爭資料表鎖定的情況。 如果資料表沒有索引，且指定了 **TABLOCK** ，多個用戶端便可以同時載入這份資料表。 根據預設，鎖定行為由資料表選項 **table lock on bulk load** 決定。  
  
  > [!NOTE]
  > 如果目標資料表是叢集資料行存放區索引，則不需要 TABLOCK 提示即可載入多個並行用戶端，因為每個並行執行緒皆會在索引內獲得指派得到個別資料列群組，並將資料載入其中。 如需詳細資料，請參閱資料行存放區索引概念性主題。
  
  **CHECK_CONSTRAINTS**  
  指定在大量匯入作業期間，必須檢查目標資料表或檢視表的所有條件約束。 當沒有 CHECK_CONSTRAINTS 提示時，會忽略所有 CHECK 和 FOREIGN KEY 條件約束，在作業之後，會將資料表的條件約束標記為不受信任。  
  
  > [!NOTE]
  > 一律強制實施 UNIQUE、PRIMARY KEY 和 NOT NULL 條件約束。
  
  在某些時候，您必須檢查整份資料表的條件約束。 如果在大量匯入作業之前，資料表不是空的，重新驗證條件約束的成本，可能會超出在累加資料上套用 CHECK 條件約束的成本。 因此，建議您在進行累加大量匯入期間，通常要啟用條件約束檢查。  
  
  如果輸入資料包含違反條件約束的資料列，您可能會想停用條件約束 (預設行為)。 當停用 CHECK 條件約束時，您可以先匯入資料，再利用 [!INCLUDE[tsql](../includes/tsql-md.md)] 陳述式來移除無效的資料。  
  
  > [!NOTE]
  > **bcp** 現在會強制進行資料驗證與資料檢查，若針對資料檔案中無效的資料執行指令碼，這些資料驗證與檢查作業可能會導致指令碼失敗。

  > [!NOTE]
  > **-m** *max_errors* 參數不適用於條件約束檢查。
  
* **FIRE_TRIGGERS**  
利用 **in** 引數加以指定，任何定義於目的地資料表上的插入觸發程序，都會在大量複製作業期間執行。 如果未指定 FIRE_TRIGGERS，就不會執行任何插入觸發程序。 **out**、 **queryout** 和 **format** 引數會略過 FIRE_TRIGGERS。  
  
**-i** _**input\_file**_<a name="i"></a>  
指定回應檔的名稱，回應檔包含利用互動模式 (未指定 **-n**、 **-c**、 **-w** 或 **-N** ) 執行大量複製時，在命令提示字元處對於每個資料欄位問題的回應。  
  
如果 *input_file* 的開頭是連字號 (-) 或斜線 (/)，請勿在 **-i** 與 *input_file* 值之間加上空格。  
  
**-k**<a name="k"></a>  
指定空白資料行在作業過程中應保持 Null 值，而非保有插入之資料行的任何預設值。 如需詳細資訊，請參閱[大量匯入期間保留 Null 或使用預設值 &#40;SQL Server&#41;](../relational-databases/import-export/keep-nulls-or-use-default-values-during-bulk-import-sql-server.md)。  
  
**-K** _**application\_intent**_<a name="K"></a>   
宣告連接到伺服器時的應用程式工作負載類型。 唯一可能的值是 **ReadOnly**。 若未指定 **-K** ，bcp 公用程式將不會支援在 AlwaysOn 可用性群組中連接次要複本。 如需詳細資訊，請參閱[使用中次要：可讀取的次要複本 &#40;Always On 可用性群組&#41;](../database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups.md)。  
  
**-l** _**login\_timeout**_<a name="l"></a>  
指定登入逾時。 -l 選項會指定在您嘗試連線到伺服器時，登入 SQL Server 逾時之前的秒數。 預設登入逾時為 15 秒。 登入逾時必須是介於 0 與 65534 之間的數字。 如果所提供的值不是數值或不在該範圍內，bcp 就會產生錯誤訊息。 值為 0 指定無限的逾時時間。
  
**-L** _**last\_row**_<a name="L"></a>  
指定要從資料表匯出或從資料檔案匯入的最後一個資料列的號碼。 這個參數需要大於 (>) 0 但小於 (<) 或等於 (=) 最後一個資料列號碼的值。 如果沒有這個參數，預設值是檔案中的最後一個資料列。  
  
*last_row* 可以是值高達 2^63-1 的正整數。  
  
**-m** _**max\_errors**_<a name="m"></a>  
指定 **bcp** 作業取消前，可以出現的語法錯誤數上限。 語法錯誤也暗示著對於目的地資料類型的資料轉換錯誤。 *max_errors* 總計將只能在伺服器偵測的錯誤排除在外，例如條件約束違規。  
  
 將會忽略 **bcp** 公用程式無法複製的資料列，並計算為一次錯誤。 如果未併入這個選項，預設值是 10。  
  
> [!NOTE]
> **-m** 選項也不適用於轉換 **money** 或 **bigint** 資料類型。
  
**-n**<a name="n"></a>  
利用資料的原生 (資料庫) 資料類型來執行大量複製作業。 並非每個欄位都有這個選項的提示。它會使用原生值。  
  
如需詳細資訊，請參閱[使用原生格式匯入或匯出資料 &#40;SQL Server&#41;](../relational-databases/import-export/use-native-format-to-import-or-export-data-sql-server.md)。  
  
**-N**<a name="N"></a>  
如果是非字元資料，請使用資料的原生 (資料庫) 資料類型執行大量複製作業；如果是字元資料，請使用 Unicode 字元執行大量複製作業。 此選項為 **-w** 選項提供較佳的高效能替代方式，它的用途在於利用資料檔案，在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的執行個體之間傳送資料。 並非每個欄位都有這項提示。 當您要傳送的資料包含 ANSI 擴充字元而且您要利用原生模式的效能時，請使用這個選項。  
  
 如需詳細資訊，請參閱 [使用 Unicode 原生格式匯入或匯出資料 &#40;SQL Server&#41;](../relational-databases/import-export/use-unicode-native-format-to-import-or-export-data-sql-server.md)。  
  
 當您要使用 bcp.exe 搭配 **-N** 匯出資料後再將該資料匯入相同的資料表結構描述時，如果有固定長度的非 Unicode 字元資料行 (例如 **char(10)** )，可能會看到一個截斷警告。  
  
 可以忽略此警告。 解決這個警告的其中一種方式是使用 **-n** 取代 **-N**。  
  
 **-o** _**output\_file**_<a name="o"></a>  
 指定接收來自命令提示字元重新導向之輸出的檔案名稱。  
  
 如果 *output_file* 的開頭是連字號 (-) 或斜線 (/)，請勿在 **-o** 與 *output_file* 值之間加上空格。  
  
 **-P** _**password**_<a name="P"></a>  
 指定登入識別碼的密碼。 如果未使用此選項， **bcp** 命令會提示您輸入密碼。 如果在未使用密碼的情況下，於命令提示字元尾端使用此選項， **bcp** 就會使用預設密碼 (NULL)。  
  
> [!IMPORTANT]
> [!INCLUDE[ssNoteStrongPass](../includes/ssnotestrongpass-md.md)]
  
 若要遮罩您的密碼，請勿連同 **-P** 選項指定 **-U** 選項。 而是改為在搭配 **-U** 選項及其他參數 (不指定 **-P** ) 指定 **bcp** 之後，按 ENTER，該命令就會提示您輸入密碼。 這個方法可確保在輸入密碼時，遮罩您的密碼。  
  
 如果 *password* 的開頭是連字號 (-) 或斜線 (/)，請勿在 **-P** 與 *password* 值之間加上空格。  
  
 **-q**<a name="q"></a>  
 在 **bcp** 公用程式與 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]執行個體之間的連接中，執行 SET QUOTED_IDENTIFIERS ON 陳述式。 請利用這個選項來指定包含空格或單引號的資料庫、擁有者、資料表或檢視表名稱。 請用引號 ("") 括住整個三部分資料表或檢視表名稱。  
  
 若要指定包含空格或單引號的資料庫名稱，您必須使用 **-q** 選項。  
  
 **-q** 不適用於傳遞給 **-d** 的值。  
  
 如需詳細資訊，請參閱本主題稍後的 [備註](#remarks)一節。  
  
 **-r** _**row\_term**_<a name="r"></a>  
 指定資料列結束字元。 預設值是 **\n** (新行字元)。 請利用這個參數來覆寫預設的資料列結束字元。 如需詳細資訊，請參閱 [指定欄位與資料列結束字元 &#40;SQL Server&#41;](../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)。  
  
 如果您在 bcp.exe 命令中使用十六進位表示法來指定資料列結束字元，該值將會在 0x00 處截斷。 例如，如果您指定 0x410041，將會使用 0x41。  
  
 如果 *row_term* 的開頭是連字號 (-) 或斜線 (/)，請勿在 **-r** 與 *row_term* 值之間加上空格。  
  
 **-R**<a name="R"></a>  
 指定要使用定義給用戶端電腦地區設定的區域格式，將貨幣、日期和時間資料大量複製到 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 中。 依預設，會忽略地區設定。  
  
 **-S** _**server\_name**_ [\\_**instance\_name**_]<a name="S"></a> 指定要連接的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體。 如果未指定任何伺服器， **bcp** 公用程式會連接至本機電腦的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 預設執行個體。 透過網路上的遠端電腦或本機具名執行個體執行 **bcp** 指令時，此選項為必要選項。 若要連接到伺服器上的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 預設執行個體，只要指定 *server_name*。 若要連接到 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 的具名執行個體，請指定 _server\_name_ **\\** _instance\_name_。  
  
 **-t** _**field\_term**_<a name="t"></a>  
 指定欄位結束字元。 預設值是 **\t** (定位字元)。 請利用這個參數來覆寫預設的欄位結束字元。 如需詳細資訊，請參閱 [指定欄位與資料列結束字元 &#40;SQL Server&#41;](../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)。  
  
 如果您在 bcp.exe 命令中使用十六進位表示法來指定欄位結束字元，該值將會在 0x00 處截斷。 例如，如果您指定 0x410041，將會使用 0x41。  
  
 如果 *field_term* 的開頭是連字號 (-) 或斜線 (/)，請勿在 **-t** 與 *field_term* 值之間加上空格。  
  
 **-T**<a name="T"></a>  
 指定 **bcp** 公用程式使用整合式安全性的信任連接，連接至 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 。 網路使用者的安全性認證、 *login_id* 及 *password* 不是必要的選項。 如果未指定 **-T** ，則必須指定 **-U** 與 **-P** ，才能順利登入。

> [!IMPORTANT]
> 指定 **bcp** 公用程式要使用整合式安全性的信任連接，連接到 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 時，請使用 **-T** 選項 (信任連接)，而非「使用者名稱」和「密碼」的組合。 當 **bcp** 公用程式連線到 SQL Database 或 Azure Synapse Analytics 時，不支援使用 Windows 驗證或 Azure Active Directory 驗證。 請使用 **-U** 和 **-P** 選項。 
  
 **-U** _**login\_id**_<a name="U"></a>  
 指定用來連接至 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]的登入識別碼。  
  
> [!IMPORTANT]
> 指定 **bcp** 公用程式要使用整合式安全性的信任連接，連接到 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 時，請使用 **-T** 選項 (信任連接)，而非「使用者名稱」和「密碼」的組合。 當 **bcp** 公用程式連線到 SQL Database 或 Azure Synapse Analytics 時，不支援使用 Windows 驗證或 Azure Active Directory 驗證。 請使用 **-U** 和 **-P** 選項。
  
 **-v**<a name="v"></a>  
 報告 **bcp** 公用程式版本號碼和著作權。  
  
 **-V** (**80** \| **90** \| **100** \| **110** \| **120** \| **130**)<a name="V"></a>  
 利用舊版 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]的資料類型執行大量複製作業。 不是每個欄位都有這個選項的提示，它會使用預設值。  
  
 **80** = [!INCLUDE[ssVersion2000](../includes/ssversion2000-md.md)]  
  
 **90** = [!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)]  
  
 **100** = [!INCLUDE[ssKatmai](../includes/sskatmai-md.md)] 及 [!INCLUDE[ssKilimanjaro](../includes/sskilimanjaro-md.md)]  
  
 **110** = [!INCLUDE[ssSQL11](../includes/sssql11-md.md)]  
  
 **120** = [!INCLUDE[ssSQL14](../includes/sssql14-md.md)]  
  
 **130** = [!INCLUDE[ssSQL15](../includes/sssql16-md.md)]  
  
 例如，若要產生 [!INCLUDE[ssVersion2000](../includes/ssversion2000-md.md)]不支援但在新版 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]中所引入的資料類型，請使用 -V80 選項。  
  
 如需詳細資訊，請參閱 [從舊版 SQL Server 匯入原生與字元格式資料](../relational-databases/import-export/import-native-and-character-format-data-from-earlier-versions-of-sql-server.md)。  
  
 **-w**<a name="w"></a>  
 利用 Unicode 字元執行大量複製作業。 並非每個欄位都有這個選項的提示。它會使用 **nchar** 作為儲存類型 (沒有前置詞)、欄位分隔字元是 **\t** (定位字元)，而且資料列結束字元是 **\n** (新行字元)。 **-w** 與 **-c** 不相容。  
  
 如需詳細資訊，請參閱 [使用 Unicode 字元格式匯入或匯出資料 &#40;SQL Server&#41;](../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)。  
  
 **-x**<a name="x"></a>  
 與 **format** 和 **-f** *format_file* 選項一起使用會產生以 XML 為基礎的格式檔案，而非預設的非 XML 格式檔案。 匯入或匯出資料時， **-x** 無法運作。 如果沒有與 **format** 和 **-f** *format_file* 一起使用，則會產生錯誤。  

## <a name="remarks"></a>備註<a name="remarks"></a>

- 您可以利用 **bcp** 工具時，也會安裝 [!INCLUDE[msCoName](../includes/msconame-md.md)][!INCLUDE[ssCurrent](../includes/sscurrent-md.md)] 13.0 用戶端。 如果同時為 [!INCLUDE[ssCurrent](../includes/sscurrent-md.md)] 和舊版 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]安裝工具，則根據 PATH 環境變數值順序的不同，您也可以使用舊版的 **bcp** 用戶端來取代 **bcp** 13.0 用戶端。 這個環境變數定義了 Windows 用來搜尋可執行檔的一組目錄。 若要確定您正在使用的版本，請在 Windows 命令提示字元處執行 **bcp /v** 或 **bcp -v** 命令。 如需如何在 PATH 環境變數中設定命令路徑的詳細資訊，請參閱[環境變數](/windows/win32/shell/user-environment-variables) (英文) 或在 Windows 說明中搜尋環境變數。

    若要確保正在執行 BCP 公用程式的最新版本，您必須移除所有舊版的 BCP 公用程式。

    若要判斷所有已安裝 BCP 公用程式版本的位置，請在命令提示字元中鍵入：

    ```cmd
    where bcp.exe
    ```

- Bcp 公用程式也可以從 [Microsoft SQL Server 2016 功能套件](https://www.microsoft.com/download/details.aspx?id=56833)個別下載。  選取 `ENU\x64\MsSqlCmdLnUtils.msi` 或 `ENU\x86\MsSqlCmdLnUtils.msi`。

- 只有在同時安裝 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 工具和 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Native Client 時，才能支援 XML 格式檔案。

- 如需 **bcp** 公用程式的尋找位置與執行方式，以及命令提示字元公用程式語法慣例的資訊，請參閱 [命令提示字元公用程式參考 &#40;Database Engine&#41;](../tools/command-prompt-utility-reference-database-engine.md)。

- 如需準備資料進行大量匯入或匯出作業的資訊，請參閱 [準備大量匯出或匯入的資料 &#40;SQL Server&#41;](../relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server.md)方面的知識。

- 如需大量匯入所執行的資料列插入作業於何時記錄到交易記錄的資訊，請參閱 [大量匯入採用最低限度記錄的必要條件](../relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import.md)。

- [使用其他特殊字元](/windows-server/administration/windows-commands/set_1#remarks)

    <、>、|、&、^ 等字元為特殊的命令殼層字元，且在字串中使用這些字元時，必須在前方加上逸出字元 (^) 或括在引號中 (例如，"StringContaining&Symbol")。 如果您使用引號來括住包含特殊字元的字串，則引號會被視為環境變數值的一部分。

## <a name="native-data-file-support"></a>原生資料檔案支援

 在 [!INCLUDE[ssCurrent](../includes/sscurrent-md.md)]中， **bcp** 公用程式可支援與 [!INCLUDE[ssVersion2000](../includes/ssversion2000-md.md)]、 [!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)]、 [!INCLUDE[ssKatmai](../includes/sskatmai-md.md)]、 [!INCLUDE[ssKilimanjaro](../includes/sskilimanjaro-md.md)]和 [!INCLUDE[ssSQL11](../includes/sssql11-md.md)]相容的原生資料檔案。  

## <a name="computed-columns-and-timestamp-columns"></a>計算資料行和時間戳記資料行

 會忽略針對計算資料行或 **timestamp** 資料行匯入之資料檔案中的值，而且 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 會自動指派值。 如果資料檔案不包含資料表中計算資料行或 **timestamp** 資料行的值，請使用格式檔案指定在匯入資料時應略過資料表中的計算資料行或 **timestamp** 資料行； [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 會自動指派資料行的值。  
  
 計算資料行和 **timestamp** 資料行會照常從 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 大量複製到資料檔案中。  

## <a name="specifying-identifiers-that-contain-spaces-or-quotation-marks"></a>指定包含空格或引號的識別碼

 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 識別碼可以包括內嵌空格和引號之類的字元。 您必須依照下列方式來處理這些識別碼：  
  
- 當您在命令提示字元之下，指定包含空格或引號的識別碼或檔案名稱時，請用引號 ("") 括住識別碼。  

    例如，下列 `bcp out` 命令會建立名稱為 `Currency Types.dat`的資料檔案：  

    ```cmd
    bcp AdventureWorks2012.Sales.Currency out "Currency Types.dat" -T -c  
    ```
  
- 若要指定包含空格或引號的資料庫名稱，您必須使用 **-q** 選項。  
  
- 如果擁有者、資料表或檢視表名稱包含內嵌的空格或引號，您可以執行下列動作：  
  
    - 指定 **-q** 選項，或  
  
    - 在引號內，用方括號 ([]) 括住擁有者、資料表或檢視表名稱。  

## <a name="data-validation"></a>資料驗證

 **bcp** 現在會強制進行資料驗證與資料檢查，若針對資料檔案中無效的資料執行指令碼，這些資料驗證與檢查作業可能會導致指令碼失敗。 例如， **bcp** 現在會驗證：  
  
- float 或 real 資料類型的原生表示法是否有效。  
  
- Unicode 資料的長度是否為偶數位元組。  
  
 可在舊版 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 中大量匯入的無效資料形式，現在可能無法載入。不過，在舊版中，除非用戶端嘗試存取無效資料，否則不會發生作業失敗的情況。 新增的驗證會使大量載入之後的資料查詢，將出現意外的狀況減到最少。  

## <a name="bulk-exporting-or-importing-sqlxml-documents"></a>大量匯出或匯入 SQLXML 文件

若要大量匯出或匯入 SQLXML 資料，請在格式檔案中使用下列其中一種資料類型。  
  
|資料類型|效果|  
|---------------|------------|  
|SQLCHAR 或 SQLVARYCHAR|資料是使用用戶端字碼頁或定序所隱含的字碼頁所傳送。 其效果與指定 **-c** 參數但不指定格式檔案相同。|  
|SQLNCHAR 或 SQLNVARCHAR|以 Unicode 格式傳送這份資料。 其效果與指定 **-w** 參數但不指定格式檔案相同。|  
|SQLBINARY 或 SQLVARYBIN|未經任何轉換即傳送這份資料。|  

## <a name="permissions"></a>權限

 **bcp out** 作業需要來源資料表的 SELECT 權限。  
  
 **bcp in** 作業至少需要目標資料表的 SELECT/INSERT 權限。 另外，如果符合下列中的任何狀況，便需要 ALTER TABLE 權限：  
  
- 存在有條件約束，而且未指定 CHECK_CONSTRAINTS 提示。  
  
    > [!NOTE]
    > 停用條件約束是預設行為。 若要明確啟用條件約束，請搭配 CHECK_CONSTRAINTS 提示使用 **-h** 選項。
  
- 存在有觸發程序，而且未指定 FIRE_TRIGGER 提示。  
  
    > [!NOTE]
    > 依預設不會引發觸發程序。 若要明確引發觸發程序，請搭配 FIRE_TRIGGERS 提示使用 **-h** 選項。
  
- 您利用 **-E** 選項，從資料檔案匯入識別值。  
  
> [!NOTE]
> 在 [!INCLUDE[ssVersion2005](../includes/ssversion2005-md.md)]中，目標資料表的 ALTER TABLE 權限是一項新的需求。 如果使用者帳戶沒有目標資料表的 ALTER 資料表權限，這項新的需求可能會讓不會強制進行觸發程序和條件約束檢查的 **bcp** 指令碼失敗。

## <a name="character-mode--c-and-native-mode--n-best-practices"></a>字元模式 (-c) 與原生模式 (-n) 最佳做法

 本節具有字元模式 (-c) 與原生模式 (-n) 的建議事項。  
  
- (管理員/使用者) 盡可能使用原生格式 (-n) 來避免分隔符號問題。 您可以使用原生格式，透過 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]匯出和匯入。 如果資料將匯入非 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 資料庫，請使用 -c 或 -w 選項，從[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 匯出資料。  
  
- (管理員) 使用 BCP OUT 時確認資料。 例如，當您依序使用 BCP OUT、BCP IN 和 BCP OUT 時，請確認資料正確匯出，而且結束字元值並未當做某些資料值的一部分使用。 請考慮使用隨機十六進位值來覆寫預設結束字元 (使用 -t 和 -r 選項)，避免結束字元值與資料值之間發生衝突。  
  
- (使用者) 使用長且唯一的結束字元 (任何位元組或字元序列)，將實際字串值發生衝突的可能性降到最低。 這可透過使用 -t 和 -r 選項完成。  

## <a name="examples"></a>範例

本區段包含下列範例：

A. 識別 **bcp** 公用程式版本

B. 將資料表資料列複製到資料檔案中 (使用信任連接)

C. 將資料表資料列複製到資料檔案中 (使用混合模式驗證)

D. 將檔案資料複製到資料表中

E. 將特定資料行複製到資料檔案中

F. 將特定資料列複製到資料檔案中

G. 將查詢的資料複製到資料檔案中

H. 建立格式檔案

I. 使用格式檔案以 **bcp** 進行大量匯入

J. 指定字碼頁

### <a name="example-test-conditions"></a>**範例測試條件**

以下範例針對 SQL Server (從 2016 開始) 和 Azure SQL Database 利用 `WideWorldImporters` 範例資料庫。  您可以從 [https://github.com/Microsoft/sql-server-samples/releases/tag/wide-world-importers-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/wide-world-importers-v1.0) 下載 `WideWorldImporters` 。  如需還原範例資料庫的語法，請參閱 [RESTORE (TRANSACT-SQL)](../t-sql/statements/restore-statements-transact-sql.md) 。  除非另有指定，否則範例假設您使用 Windows 驗證，且有信任連接通往您在執行 **bcp** 命令的伺服器執行個體。  名為 `D:\BCP` 的目錄將用於許多範例。

下面的指令碼會建立 `WideWorldImporters.Warehouse.StockItemTransactions` 資料表的空複本，然後新增主索引鍵條件約束。  在 SQL Server Management Studio (SSMS) 中執行下列 T-SQL 指令碼

```sql  
USE WideWorldImporters;  
GO  

SET NOCOUNT ON;

IF NOT EXISTS (SELECT * FROM sys.tables WHERE name = 'Warehouse.StockItemTransactions_bcp')
BEGIN
    SELECT * INTO WideWorldImporters.Warehouse.StockItemTransactions_bcp
    FROM WideWorldImporters.Warehouse.StockItemTransactions  
    WHERE 1 = 2;  

    ALTER TABLE Warehouse.StockItemTransactions_bcp 
    ADD CONSTRAINT PK_Warehouse_StockItemTransactions_bcp PRIMARY KEY NONCLUSTERED 
    (StockItemTransactionID ASC);
END
```

> [!NOTE]
> 視需要截斷 `StockItemTransactions_bcp` 資料表。
>
> TRUNCATE TABLE WideWorldImporters.Warehouse.StockItemTransactions_bcp;

### <a name="a--identify-bcp-utility-version"></a>A.  識別 **bcp** 公用程式版本

請在命令提示字元之下，輸入下列命令：

```cmd
bcp -v
```
  
### <a name="b-copying-table-rows-into-a-data-file-with-a-trusted-connection"></a>B. 將資料表資料列複製到資料檔案中 (使用信任連接)

下列範例說明 **資料表上的** out `WideWorldImporters.Warehouse.StockItemTransactions` 選項。

- **基本** 這個範例會建立一個名稱為 `StockItemTransactions_character.bcp` 的資料檔案，且會利用 **字元** 格式，將資料表的資料複製到這個資料檔案中。

  請在命令提示字元之下，輸入下列命令：

  ```cmd
  bcp WideWorldImporters.Warehouse.StockItemTransactions out D:\BCP\StockItemTransactions_character.bcp -c -T
  ```

- **擴充** 這個範例會建立一個名稱為 `StockItemTransactions_native.bcp` 的資料檔案，且會利用 **原生** 格式，將資料表的資料複製到這個資料檔案中。  此範例也指定語法錯誤的數目上限、錯誤檔和輸出檔。

    請在命令提示字元之下，輸入下列命令：

    ```cmd
    bcp WideWorldImporters.Warehouse.StockItemTransactions OUT D:\BCP\StockItemTransactions_native.bcp -m 1 -n -e D:\BCP\Error_out.log -o D:\BCP\Output_out.log -S -T
    ```

檢閱 `Error_out.log` 和 `Output_out.log`。  `Error_out.log` 應為空白。  比較 `StockItemTransactions_character.bcp` 和 `StockItemTransactions_native.bcp`之間的檔案大小。

### <a name="c-copying-table-rows-into-a-data-file-with-mixed-mode-authentication"></a>C. 將資料表資料列複製到資料檔案中 (使用混合模式驗證)

下列範例說明 **資料表上的** out `WideWorldImporters.Warehouse.StockItemTransactions` 選項。  這個範例會建立一個名稱為 `StockItemTransactions_character.bcp` 的資料檔案，且會利用 **字元** 格式，將資料表的資料複製到這個資料檔案中。  
  
 此範例假設您使用的是混合模式驗證，您必須使用 **-U** 參數指定您的登入識別碼。 同時，除非您要連接到本機電腦上的 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 預設執行個體，否則請使用 **-S** 參數指定系統名稱，並選擇性地指定執行個體名稱。  

請在命令提示字元之下，輸入下列命令：\(系統會提示您輸入密碼。\)

```cmd
bcp WideWorldImporters.Warehouse.StockItemTransactions out D:\BCP\StockItemTransactions_character.bcp -c -U<login_id> -S<server_name\instance_name>
```

### <a name="d-copying-data-from-a-file-to-a-table"></a>D. 將檔案資料複製到資料表中

下列範例會使用上方建立的檔案說明 **資料表上的** in `WideWorldImporters.Warehouse.StockItemTransactions_bcp` 選項。

- **基本** 這個範例會使用先前建立的 `StockItemTransactions_character.bcp` 資料檔案。

  請在命令提示字元之下，輸入下列命令：

  ```cmd
  bcp WideWorldImporters.Warehouse.StockItemTransactions_bcp IN D:\BCP\StockItemTransactions_character.bcp -c -T
  ```

- **展開** 這個範例會使用先前建立的 `StockItemTransactions_native.bcp` 資料檔案。  此範例也使用提示 **TABLOCK**，並指定語法錯誤的數目上限、錯誤檔和輸出檔。
  
請在命令提示字元之下，輸入下列命令：

```cmd
bcp WideWorldImporters.Warehouse.StockItemTransactions_bcp IN D:\BCP\StockItemTransactions_native.bcp -b 5000 -h "TABLOCK" -m 1 -n -e D:\BCP\Error_in.log -o D:\BCP\Output_in.log -S -T
```

  檢閱 `Error_in.log` 和 `Output_in.log`。

### <a name="e-copying-a-specific-column-into-a-data-file"></a>E. 將特定資料行複製到資料檔案中

若要複製特定資料行，您可以使用 **queryout** 選項。  下列範例只會將 `StockItemTransactionID` 資料表的 `Warehouse.StockItemTransactions` 資料行複製到資料檔案中。
  
請在命令提示字元之下，輸入下列命令：

```cmd
bcp "SELECT StockItemTransactionID FROM WideWorldImporters.Warehouse.StockItemTransactions WITH (NOLOCK)" queryout D:\BCP\StockItemTransactionID_c.bcp -c -T
```

### <a name="f-copying-a-specific-row-into-a-data-file"></a>F. 將特定資料列複製到資料檔案中

若要複製特定資料列，您可以使用 **queryout** 選項。 下列範例只會將 `Amy Trefl` 資料表中名稱為 `WideWorldImporters.Application.People` 之連絡人的資料列複製到 `Amy_Trefl_c.bcp`資料檔案中。  注意︰ **-d** 參數用來識別資料庫。
  
請在命令提示字元之下，輸入下列命令：

```cmd
bcp "SELECT * from Application.People WHERE FullName = 'Amy Trefl'" queryout D:\BCP\Amy_Trefl_c.bcp -d WideWorldImporters -c -T
```

### <a name="g-copying-data-from-a-query-to-a-data-file"></a>G. 將查詢的資料複製到資料檔案中

若要將 Transact-SQL 陳述式的結果集複製到資料檔案中，請使用 **queryout** 選項。  下列範例會依照全名的方式，將 `WideWorldImporters.Application.People` 資料表中的名稱複製到 `People.txt` 資料檔案中。  注意︰ **-t** 參數會用來建立以逗號分隔的檔案。

請在命令提示字元之下，輸入下列命令：

```cmd
bcp "SELECT FullName, PreferredName FROM WideWorldImporters.Application.People ORDER BY FullName" queryout D:\BCP\People.txt -t, -c -T
```

### <a name="h-creating-format-files"></a>H. 建立格式檔案

下列範例會針對 `Warehouse.StockItemTransactions` 資料庫中的 `WideWorldImporters` 資料表來建立三個不同的格式檔案。  檢閱每個建立檔案的內容。

請在命令提示字元之下，輸入下列命令：

```cmd
REM non-XML character format
bcp WideWorldImporters.Warehouse.StockItemTransactions format nul -f D:\BCP\StockItemTransactions_c.fmt -c -T 

REM non-XML native format
bcp WideWorldImporters.Warehouse.StockItemTransactions format nul -f D:\BCP\StockItemTransactions_n.fmt -n -T

REM XML character format
bcp WideWorldImporters.Warehouse.StockItemTransactions format nul -f D:\BCP\StockItemTransactions_c.xml -x -c -T
```

> [!NOTE]
> 若要使用 **-x** 參數，您必須使用 **bcp** 9.0 用戶端。 如需有關如何使用 **bcp** 9.0 用戶端的詳細資訊，請參閱 [備註](#remarks)一節。
  
 如需詳細資訊，請參閱[非 XML 格式檔案 &#40;SQL Server&#41;](../relational-databases/import-export/non-xml-format-files-sql-server.md) 和 [XML 格式檔案 &#40;SQL Server&#41;](../relational-databases/import-export/xml-format-files-sql-server.md)。
  
### <a name="i-using-a-format-file-to-bulk-import-with-bcp"></a>I. 使用格式檔案以 bcp 進行大量匯入

若要在將資料匯入 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]的執行個體時使用先前所建立的格式檔案，請搭配 **in** 選項使用 **-f** 參數。  例如，下列命令會利用先前建立的格式檔案 `StockItemTransactions_character.bcp`，將 `Warehouse.StockItemTransactions_bcp` 資料檔案的內容大量複製到 `StockItemTransactions_c.xml`資料表的複本中。  注意︰ **-L** 參數用來只匯入前 100 個記錄。

請在命令提示字元之下，輸入下列命令：

```cmd
bcp WideWorldImporters.Warehouse.StockItemTransactions_bcp in D:\BCP\StockItemTransactions_character.bcp -L 100 -f D:\BCP\StockItemTransactions_c.xml -T
```

> [!NOTE]
> 當資料檔案欄位與資料表資料行不同 (如號碼、排序或資料類型) 時，格式檔案就非常有用。 如需詳細資訊，請參閱 [匯入或匯出資料的格式檔案 &#40;SQL Server&#41;](../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)＞。  

### <a name="j-specifying-a-code-page"></a>J. 指定字碼頁

下列節錄的程式碼範例在指定字碼頁 65001 的同時顯示 bcp 匯入︰

```cmd
bcp.exe MyTable in "D:\data.csv" -T -c -C 65001 -t , ...  
```

## <a name="additional-examples"></a>其他範例

|下列主題包含使用 bcp 的範例： |
|---|
|大量匯入或大量匯出的資料格式 (SQL Server)<br />&emsp;&#9679;&emsp;[使用原生格式匯入或匯出資料 (SQL Server)](../relational-databases/import-export/use-native-format-to-import-or-export-data-sql-server.md)<br />&emsp;&#9679;&emsp;[使用字元格式匯入或匯出資料 (SQL Server)](../relational-databases/import-export/use-character-format-to-import-or-export-data-sql-server.md)<br />&emsp;&#9679;&emsp;[使用 Unicode 原生格式匯入或匯出資料 (SQL Server)](../relational-databases/import-export/use-unicode-native-format-to-import-or-export-data-sql-server.md)<br />&emsp;&#9679;&emsp;[使用 Unicode 字元格式匯入或匯出資料 (SQL Server)](../relational-databases/import-export/use-unicode-character-format-to-import-or-export-data-sql-server.md)<br /><br />[指定欄位與資料列結束字元 (SQL Server)](../relational-databases/import-export/specify-field-and-row-terminators-sql-server.md)<br /><br />[大量匯入期間保留 Null 或使用預設值 (SQL Server)](../relational-databases/import-export/keep-nulls-or-use-default-values-during-bulk-import-sql-server.md)<br /><br />[大量匯入資料時保留識別值 (SQL Server)](../relational-databases/import-export/keep-identity-values-when-bulk-importing-data-sql-server.md)<br /><br />匯入或匯出資料的格式檔案 (SQL Server)<br />&emsp;&#9679;&emsp;[建立格式檔案 (SQL Server)](../relational-databases/import-export/create-a-format-file-sql-server.md)<br />&emsp;&#9679;&emsp;[使用格式檔案大量匯入資料 (SQL Server)](../relational-databases/import-export/use-a-format-file-to-bulk-import-data-sql-server.md)<br />&emsp;&#9679;&emsp;[使用格式檔案略過資料表資料行 (SQL Server)](../relational-databases/import-export/use-a-format-file-to-skip-a-table-column-sql-server.md)<br />&emsp;&#9679;&emsp;[使用格式檔案略過資料欄位 (SQL Server)](../relational-databases/import-export/use-a-format-file-to-skip-a-data-field-sql-server.md)<br />&emsp;&#9679;&emsp;[使用格式檔案將資料表資料行對應至資料檔案的欄位 (SQL Server)](../relational-databases/import-export/use-a-format-file-to-map-table-columns-to-data-file-fields-sql-server.md)<br /><br />[大量匯入與匯出 XML 文件的範例 (SQL Server)](../relational-databases/import-export/examples-of-bulk-import-and-export-of-xml-documents-sql-server.md)<br /><p>  </p>|

## <a name="considerations-and-limitations"></a>考量與限制

- Bcp 公用程式有一項限制，就是錯誤訊息只會顯示 512 個位元組字元。 只會顯示錯誤訊息的前 512 個位元組。

## <a name="next-steps"></a>後續步驟

- [準備大量匯出或匯入的資料 &#40;SQL Server&#41;](../relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server.md)
- [BULK INSERT &#40;Transact-SQL&#41;](../t-sql/statements/bulk-insert-transact-sql.md)
- [OPENROWSET &#40;Transact-SQL&#41;](../t-sql/functions/openrowset-transact-sql.md)
- [SET QUOTED_IDENTIFIER &#40;Transact-SQL&#41;](../t-sql/statements/set-quoted-identifier-transact-sql.md)
- [sp_configure &#40;Transact-SQL&#41;](../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)
- [sp_tableoption &#40;Transact-SQL&#41;](../relational-databases/system-stored-procedures/sp-tableoption-transact-sql.md)
- [匯入或匯出資料的格式檔案 &#40;SQL Server&#41;](../relational-databases/import-export/format-files-for-importing-or-exporting-data-sql-server.md)

[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]
