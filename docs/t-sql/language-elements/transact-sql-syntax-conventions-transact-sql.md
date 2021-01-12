---
description: Transact-SQL 語法慣例 (Transact-SQL)
title: Transact-SQL 語法慣例 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/09/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- sql13.TSQLExpandPortal.f1
dev_langs:
- TSQL
helpviewer_keywords:
- conventions [SQL Server]
- Applies to section in Transact-SQL topics
- code example conventions [SQL Server]
- objects [SQL Server], names
- code [SQL Server], conventions
- multipart names [SQL Server]
- Transact-SQL syntax conventions
- syntax conventions [SQL Server]
- code [SQL Server]
- Transact-SQL
- naming conventions [SQL Server]
- syntax [SQL Server], Transact-SQL
ms.assetid: 35fbcf7f-8b55-46cd-a957-9b8c7b311241
author: cawrites
ms.author: chadam
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 35c349190b7ebe24ae243bac518e21406a3861ba
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98102433"
---
# <a name="transact-sql-syntax-conventions-transact-sql"></a>Transact-SQL 語法慣例 (Transact-SQL)
[!INCLUDE [sql-asdb-asdbmi-asa-pdw](../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]

下表列出和描述 [!INCLUDE[tsql](../../includes/tsql-md.md)] 參考中之語法圖所用的慣例。  
  
|慣例|用於|  
|----------------|--------------|  
|大寫|[!INCLUDE[tsql](../../includes/tsql-md.md)] 關鍵字。|  
|_斜體_|使用者提供的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 語法參數。|  
|**粗體字**|請完全依照顯示的方式鍵入資料庫名稱、資料表名稱、資料行名稱、索引名稱、預存程序、公用程式、資料類型名稱和文字。|  
|底線|指出省略陳述式中包含附加底線之值的子句時，所套用的預設值。|  
|&#124; (分隔號)|加上括號或大括號來分隔語法項目。 您只可以選擇其中一個項目。|  
|`[ ]` (方括弧)|選擇性的語法項目。 不要鍵入方括弧。|  
|{ } (大括號)|必要的語法項目。 不要鍵入大括弧。|  
|[ **,** ..._n_]|指出先前項目可以重複 _n_ 次。 以逗號分隔項目。|  
|[..._n_]|指出先前項目可以重複 _n_ 次。 以空白分開各次出現項目。|  
|;|[!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式結束字元。 雖然在這個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 版本中大多數陳述式都不需要使用分號，但在未來的版本中將會需要。|  
|\<label> ::=|語法區塊的名稱。 針對冗長語法的區段，或可用於陳述式中多個位置的語法單位，請使用這個慣例進行分組與標示。 每個能夠使用語法區塊的位置，都會以括在＜＞內的標籤來指示：\<label>。<br /><br /> set 是運算式的集合，例如 \<grouping set>。而 list 則是 set 的集合，例如 \<composite element list>。|  
  
## <a name="multipart-names"></a>多部分名稱  
除非另有指定，否則，所有指向資料庫物件名稱的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 參考都可以是四部分的名稱，格式如下：  
  
_server\_name_.[_database\_name_].[_schema\_name_]._object\_name_  
  
| _database\_name_.[_schema\_name_]._object\_name_  
 
| _schema\_name_._object\_name_  
  
| _object\_name_  
  
_server\_name_  
指定連結伺服器名稱或遠端伺服器名稱。  
  
_database\_name_  
指定當物件在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的本機執行個體中之 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫的名稱。 當物件是在連結伺服器中時，*database_name* 會指定一個 OLE DB 目錄。  
  
_schema\_name_  
指定如果物件是在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫中，包含物件的結構描述名稱。 當物件是在連結伺服器中，*schema_name* 會指定一個 OLE DB 結構描述名稱。  
  
_object\_name_  
指向物件名稱。  
  
當參考特定物件時，您不一定需要指定伺服器、資料庫和結構描述以供 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 識別物件。 不過，如果找不到物件，就會傳回錯誤。  
  
> [!NOTE]  
> 若要避免名稱解析錯誤，我們建議您每當指定結構描述範圍的物件時，都要指定結構描述名稱。  
  
若要省略中繼節點，請利用句點來表示這些位置。 下表顯示物件名稱的有效格式。  
  
|物件參考格式|描述|  
|-----------------------------|-----------------|  
|_server_._database_._schema_._object_|四部分名稱。|  
|_server_._database_.._object_|省略結構描述名稱。|  
|_server_.._schema_._object_|省略資料庫名稱。|  
|_server_..._object_|省略資料庫和結構描述名稱。|  
|_database_._schema_._object_|省略伺服器名稱。|  
|_database_.._object_|省略伺服器和結構描述名稱。|  
|_schema_._object_|省略伺服器和資料庫名稱。|  
|_object_|省略伺服器、資料庫和結構描述名稱。|  
  
## <a name="code-example-conventions"></a>程式碼範例慣例  
除非另有指示，否則 [!INCLUDE[tsql](../../includes/tsql-md.md)] 參考中所提供的範例都是利用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 及其下列選項的預設值來測試的：  
  
-   ANSI_NULLS  
-   ANSI_NULL_DFLT_ON  
-   ANSI_PADDING  
-   ANSI_WARNINGS  
-   CONCAT_NULL_YIELDS_NULL  
-   QUOTED_IDENTIFIER  
  
[!INCLUDE[tsql](../../includes/tsql-md.md)] 參考中的大部分程式碼範例都已在執行排序順序會區分大小寫的伺服器中測試過。 測試伺服器通常都是執行 ANSI/ISO 1252 字碼頁。  
  
許多程式碼範例使用字母 **N** 作為 Unicode 字元字串常數的前置詞。若沒有 **N** 前置詞，字串會被轉換為資料庫的預設字碼頁。 這個預設字碼頁可能無法辨識特定字元。  
  
## <a name="applies-to-references"></a>「適用於」參考  
[!INCLUDE[tsql](../../includes/tsql-md.md)] 參考包含與 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 及更新版本)、[!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 和 [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] 相關的文章。   

在每個文章頂端附近，都有一個章節指出支援該文章主題的產品。 如果未列出產品，即表示文章描述的功能不適用於該產品。 例如，可用性群組於 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 引進。 **CREATE AVAILABILITY GROUP** 一文指出其適用於 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ([!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 及更新版本)，因為其不適用於 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]、[!INCLUDE[ssKilimanjaro](../../includes/sskilimanjaro-md.md)] 或 [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)]。  
  
文章的一般主題可能適用於某產品，但在某些情況下並非所有的引數都受支援。 例如自主資料庫使用者於 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 引進。 您可以在任何 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 產品中使用 **CREATE USER** 陳述式，但 **WITH PASSWORD** 語法就無法在舊版中使用。 文章本文會於適當引數描述中插入額外的＜適用對象＞  一節。  
  
## <a name="see-also"></a>另請參閱  
[Transact-SQL 參考 &#40;資料庫引擎41;](../language-reference.md)    
[保留關鍵字 &#40;Transact-SQL&#41;](../../t-sql/language-elements/reserved-keywords-transact-sql.md)      
[Transact-SQL 設計問題](/previous-versions/visualstudio/visual-studio-2010/dd193411(v=vs.100))    
[Transact-SQL 命名問題](/previous-versions/visualstudio/visual-studio-2010/dd193246(v=vs.100))        
[Transact-SQL 效能問題](/previous-versions/visualstudio/visual-studio-2010/dd172117(v=vs.100))