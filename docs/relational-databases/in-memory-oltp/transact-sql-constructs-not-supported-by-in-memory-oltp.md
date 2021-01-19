---
title: 記憶體內部 OLTP 不支援 T-SQL
description: 了解經記憶體最佳化的資料表、原生編譯的預存程序和使用者定義函數不支援哪些 Transact-SQL 功能。
ms.custom: seo-dt-2019
ms.date: 11/21/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: in-memory-oltp
ms.topic: conceptual
ms.assetid: e3f8009c-319d-4d7b-8993-828e55ccde11
author: MightyPen
ms.author: genemi
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 514c5f035094b2a74a32ac54183136210801b5ce
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98169808"
---
# <a name="transact-sql-constructs-not-supported-by-in-memory-oltp"></a>記憶體中的 OLTP 不支援 Transact-SQL 建構
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  記憶體最佳化資料表、原生編譯預存程序和使用者定義函數，不支援磁碟資料表、轉譯過的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 預存程序和使用者定義函數所支援的全部 [!INCLUDE[tsql](../../includes/tsql-md.md)] 介面區。 嘗試使用其中一項不支援的功能時，伺服器會傳回錯誤。  
  
 錯誤訊息文字會說明 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的類型 (例如功能、作業、選項等) 以及功能的名稱或 [!INCLUDE[tsql](../../includes/tsql-md.md)] 關鍵字。 大部分不支援的功能會傳回錯誤 10794，並提供錯誤訊息文字指出不支援的功能。 下表列出可能出現在錯誤訊息文字中的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 功能和關鍵字，以及解決錯誤的更正動作。  
  
 如需記憶體最佳化資料表和原生方式編譯預存程序之支援功能的詳細資訊，請參閱：  
  
-   [原生編譯預存程序的移轉問題](./a-guide-to-query-processing-for-memory-optimized-tables.md)  
  
-   [記憶體內部 OLTP 的 Transact-SQL 支援](../../relational-databases/in-memory-oltp/transact-sql-support-for-in-memory-oltp.md)  
  
-   [記憶體內部 OLTP 不支援的 SQL Server 功能](../../relational-databases/in-memory-oltp/unsupported-sql-server-features-for-in-memory-oltp.md)  
  
-   [原生編譯的預存程序](./a-guide-to-query-processing-for-memory-optimized-tables.md)  
  
## <a name="databases-that-use-in-memory-oltp"></a>使用記憶體中 OLTP 的資料庫  
 下表列出不支援的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 功能，以及會出現在與記憶體內部 OLTP 資料庫有關之錯誤訊息文字中的關鍵字。 這個表格也會列出錯誤的解決方法。  
  
|類型|名稱|解決方案|  
|----------|----------|----------------|  
|選項|AUTO_CLOSE|具有 MEMORY_OPTIMIZED_DATA 檔案群組的資料庫不支援資料庫選項 AUTO_CLOSE=ON。|  
|選項|ATTACH_REBUILD_LOG|具有 MEMORY_OPTIMIZED_DATA 檔案群組的資料庫不支援 CREATE 資料庫選項 ATTACH_REBUILD_LOG。|  
|功能|資料庫快照|具有 MEMORY_OPTIMIZED_DATA 檔案群組的資料庫無法建立資料庫快照。|  
|功能|使用 sync_method 'database snapshot' 或 'database snapshot character' 進行複寫|具有 MEMORY_OPTIMIZED_DATA 檔案群組的資料庫無法使用 sync_method 'database snapshot' 或 'database snapshot character' 進行複寫。|  
|功能|DBCC CHECKDB<br /><br /> DBCC CHECKTABLE|DBCC CHECKDB 會略過資料庫中記憶體最佳化資料表。<br /><br /> DBCC CHECKTABLE 無法檢查記憶體最佳化資料表。|  
  
## <a name="memory-optimized-tables"></a>記憶體最佳化的資料表  
 下表列出不支援的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 功能，和會出現在與記憶體最佳化資料表有關的錯誤訊息文字中的關鍵字。 這個表格也會列出錯誤的解決方法。  
  
|類型|名稱|解決方案|  
|----------|----------|----------------|  
|功能|開啟|記憶體最佳化資料表不可放置在檔案群組或分割區配置上。 請從 **CREATE TABLE** 陳述式中移除 ON 子句。<br /><br /> 所有記憶體最佳化資料表都會對應到記憶體最佳化檔案群組。|  
|資料類型|*資料類型名稱*|不支援所指的資料類型。 請用其中一種支援的資料類型取代該類型。 如需詳細資訊，請參閱 [記憶體內部 OLTP 支援的資料類型](../../relational-databases/in-memory-oltp/supported-data-types-for-in-memory-oltp.md)。|  
|功能|計算資料行|**適用於：** [!INCLUDE[ssSQL14-md](../../includes/sssql14-md.md)] 和 [!INCLUDE[ssSQL15-md](../../includes/sssql16-md.md)]<br/>記憶體最佳化資料表中不支援計算資料行。 請從 **CREATE TABLE** 陳述式中移除計算資料行。<br/><br/>[!INCLUDE[ssSDSFull_md](../../includes/ssSDSFull-md.md)] 和 SQL Server (從 [!INCLUDE[sssqlv14-md](../../includes/sssqlv14-md.md)] 起)，即開始支援經記憶體最佳化的資料表和索引中的計算資料行。|  
|功能|複寫|記憶體最佳化資料表不支援複寫。|  
|功能|FILESTREAM|FILESTREAM 儲存體不是記憶體最佳化資料表支援的資料行。 請從資料行定義中移除 **FILESTREAM** 關鍵字。|  
|功能|SPARSE|記憶體最佳化資料表的資料行不可定義為 SPARSE。 請從資料行定義中移除 **SPARSE** 關鍵字。|  
|功能|ROWGUIDCOL|ROWGUIDCOL 選項不是記憶體最佳化資料表支援的資料行。 請從資料行定義中移除 **ROWGUIDCOL** 關鍵字。|  
|功能|FOREIGN KEY|**適用於：** [!INCLUDE[ssSDSFull_md](../../includes/ssSDSFull-md.md)] 和 SQL Server (從 [!INCLUDE[ssSQL15-md](../../includes/sssql16-md.md)] 起)<br/>若是經記憶體最佳化的資料表，FOREIGN KEY 條件約束只支援參考其他經記憶體最佳化資料表之主索引鍵的外部索引鍵。 如果外部索引鍵參考的是唯一條件約束，請移除資料表定義的條件約束。<br/><br/>在 [!INCLUDE[ssSQL14-md](../../includes/sssql14-md.md)] 中，經記憶體最佳化的資料表不支援 FOREIGN KEY 條件約束。|  
|功能|叢集索引|指定非叢集索引。 對於主索引鍵索引，請務必指定 **PRIMARY KEY NONCLUSTERED**。|  
|功能|交易內的 DDL|記憶體最佳化資料表和原生編譯預存程序無法在使用者交易內容中建立或卸除。 請不要啟動交易，並確認工作階段設定 IMPLICIT_TRANSACTIONS 為 OFF，再執行 CREATE 或 DROP 陳述式。|  
|功能|DDL 觸發程序|如果該 DDL 作業有伺服器或資料庫觸發程序，則無法建立或卸除記憶體最佳化資料表和原生編譯預存程序。 請移除 CREATE/DROP TABLE 和 CREATE/DROP PROCEDURE 上的伺服器和資料庫觸發程序。|  
|功能|EVENT NOTIFICATION|如果該 DDL 作業有伺服器或資料庫事件通知，便無法建立或卸除記憶體最佳化資料表和原生編譯的預存程序。 移除 CREATE TABLE 或 DROP TABLE 與 CREATE PROCEDURE 或 DROP PROCEDURE 上的伺服器和資料庫事件通知。|  
|功能|FileTable|記憶體最佳化資料表不可建立為檔案資料表。 請從 **AS FileTable** 陳述式中移除 **CREATE TABLE** 引數|  
|作業|主索引鍵資料行的更新。|記憶體最佳化資料表中的主索引鍵資料行和資料類型無法更新。 如果主索引鍵需要更新，請刪除舊資料列，並插入包含更新之主索引鍵的新資料列。|  
|作業|CREATE INDEX|記憶體最佳化資料表上的索引必須採用內嵌於 **CREATE TABLE** 陳述式或 **ALTER TABLE** 陳述式的方式指定。|  
|作業|CREATE FULLTEXT INDEX|記憶體最佳化資料表中不支援全文檢索索引。|  
|作業|結構描述變更|經記憶體最佳化的資料表和原生編譯預存程序不支援特定結構描述變更：<br/> [!INCLUDE[ssSDSFull_md](../../includes/ssSDSFull-md.md)] 和 SQL Server (從 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 起)：支援 ALTER TABLE、ALTER PROCEDURE 和 sp_rename 作業。 不支援其他結構描述變更 (例如新增擴充屬性)。<br/><br/>[!INCLUDE[ssSQL15-md](../../includes/sssql16-md.md)]:支援 ALTER TABLE 和 ALTER PROCEDURE 作業。 不支援其他結構描述變更 (包括 sp_rename)。<br/><br/>[!INCLUDE[ssSQL14-md](../../includes/sssql14-md.md)]：不支援結構描述變更。 若要變更經記憶體最佳化資料表或原生編譯預存程序的定義，請先卸除物件，然後使用所需的定義重新建立即可。| 
|作業|TRUNCATE TABLE|記憶體最佳化資料表不支援 TRUNCATE 作業。 若要移除資料表中的所有資料列，請使用 **DELETE FROM**_table_ 刪除所有資料列，或卸除後再重新建立資料表。|  
|作業|ALTER AUTHORIZATION|不支援變更現有記憶體最佳化資料表或原生編譯預存程序的擁有者。 請卸除後再重新建立資料表或程序，以變更擁有權。|  
|作業|ALTER SCHEMA|不支援傳輸現有資料表或原生編譯預存程序的其他結構描述。 若要在結構描述之間傳輸，請卸除並重新建立物件。|  
|作業|DBCC CHECKTABLE|經記憶體最佳化的資料表中不支援 DBCC CHECKTABLE。 若要確認磁碟檢查點檔案的完整性，請執行 MEMORY_OPTIMIZED_DATA 檔案群組的備份。|  
|功能|ANSI_PADDING OFF|建立記憶體最佳化資料表或原生編譯預存程序時，工作階段選項 **ANSI_PADDING** 必須為 ON。 執行 CREATE 陳述式之前，請先執行 **SET ANSI_PADDING ON** 。|  
|選項|DATA_COMPRESSION|記憶體最佳化資料表中不支援資料壓縮。 請從資料表定義中移除此選項。|  
|功能|DTC|記憶體最佳化資料表和原生編譯預存程序無法從分散式交易中存取。 請改用 SQL 交易。|  
|作業|以記憶體最佳化資料表做為 MERGE 的目標|記憶體最佳化資料表不可以是 **MERGE** 作業的目標。 請改用 **INSERT**、**UPDATE** 或 **DELETE** 陳述式。|  
  
## <a name="indexes-on-memory-optimized-tables"></a>記憶體最佳化資料表上的索引  
 下表列出可能出現於涉及記憶體最佳化資料表上某個索引錯誤之訊息文字中的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 功能和關鍵字，以及解決錯誤的更正動作。  
  
|類型|名稱|解決方案|  
|----------|----------|----------------|  
|功能|已篩選的索引|記憶體最佳化資料表中不支援篩選的索引。 請從索引指定中省略 **WHERE** 子句。|  
|功能|包含的資料行|記憶體最佳化資料表並不需要指定內含資料行。 記憶體最佳化資料表內所有的資料行都是隱含包含在每一個記憶體最佳化的索引中。|  
|作業|DROP INDEX|不支援卸除記憶體最佳化資料表上的索引。 您可以使用 ALTER TABLE 刪除索引。<br /><br /> 如需詳細資訊，請參閱 [改變記憶體最佳化資料表](../../relational-databases/in-memory-oltp/altering-memory-optimized-tables.md)。|  
|索引選項|*索引選項*|只支援一個索引選項 - HASH 索引的 BUCKET_COUNT。|  
  
## <a name="nonclustered-hash-indexes"></a>非叢集雜湊索引  
 下表列出可能出現於涉及非叢集雜湊索引之錯誤訊息文字中的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 功能和關鍵字，以及解決錯誤的更正動作。  
  
|類型|名稱|解決方案|  
|----------|----------|----------------|  
|選項|ASC/DESC|非叢集雜湊索引不會排序。 請從索引鍵指定中移除關鍵字 **ASC** 和 **DESC** 。|  
  
## <a name="natively-compiled-stored-procedures-and-user-defined-functions"></a>原生編譯預存程序和使用者定義函數  
 下表列出可能出現於涉及原生編譯預存程序和使用者定義函數之錯誤訊息文字中的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 功能和關鍵字，以及解決錯誤的更正動作。  
  
|類型|功能|解決方案|  
|----------|-------------|----------------|  
|功能|內嵌資料表變數|資料表類型無法以內嵌於變數宣告中的方式宣告。 資料表類型必須使用 **CREATE TYPE** 陳述式明確宣告。|  
|功能|資料指標|原生編譯預存程序上或內部不支援資料指標。<br /><br /> 從用戶端執行預存程序時，請使用 RPC，而不要使用資料指標 API。 使用 ODBC 時，避免 [!INCLUDE[tsql](../../includes/tsql-md.md)] 引數 **EXECUTE**，請改為直接指定程序的名稱。<br /><br /> 從 [!INCLUDE[tsql](../../includes/tsql-md.md)] 批次或其他預存程序執行程序時，避免將資料指標與原生編譯預存程序一起使用。<br /><br /> 建立原生編譯預存程序時，請不要使用資料指標，而是使用集合為基礎的邏輯或 **WHILE** 迴圈。|  
|功能|非常數參數的預設值|在原生編譯預存程序上使用具有參數的預設值時，值必須為常數。 請從參數宣告中移除任何萬用字元。|  
|功能|EXTERNAL|無法對 CLR 預存程序執行原生編譯。 從 CREATE PROCEDURE 陳述式中移除 AS EXTERNAL 子句或 NATIVE_COMPILATION 選項。|  
|功能|編號的預存程序|原生編譯預存程序不可編號。 從 **CREATE PROCEDURE**_陳述式移除_ ; **number** 。|  
|功能|多資料列 INSERT ...VALUES 陳述式|無法使用相同的 **INSERT** 陳述式在原生編譯預存程序中插入多個資料列。 請為每個資料列建立 **INSERT** 陳述式。|  
|功能|通用資料表運算式 (CTE)|原生編譯預存程序中不支援通用資料表運算式 (CTE)。 重寫查詢。|  
|功能|COMPUTE|不支援 **COMPUTE** 子句。 請從查詢中將它移除。|  
|功能|SELECT INTO|不支援 **INTO** 子句與 **SELECT** 陳述式一起使用。 請將查詢重新撰寫為 **INSERT INTO** _Table_ **SELECT**。|  
|功能|不完整的插入資料行清單|一般而言，在 INSERT 陳述式中，必須為資料表中的所有資料行指定值。<br /><br /> 不過，我們支援記憶體最佳化資料表上的 DEFAULT 條件約束和 IDENTITY(1,1) 資料行。 INSERT 資料行清單中可以省略這些資料行，但 IDENTITY 資料行必須省略這些資料行。|  
|功能|*Function*|原生編譯預存程序中不支援某些內建函數。 請從預存程序中移除拒絕的函數。 如需所支援內建函數的詳細資訊，請參閱<br />[原生編譯的 T-SQL 模組支援的功能](../../relational-databases/in-memory-oltp/supported-features-for-natively-compiled-t-sql-modules.md)，或<br />[原生編譯的預存程序](./a-guide-to-query-processing-for-memory-optimized-tables.md)。|  
|功能|CASE|**適用於：** [!INCLUDE[ssSQL14-md](../../includes/sssql14-md.md)] 和 SQL Server (從 [!INCLUDE[ssSQL15-md](../../includes/sssql16-md.md)] 起)<br/>原生編譯的預存程序不支援 **CASE** 運算式。 請為每個案例建立查詢。 如需詳細資訊，請參閱 [在原生編譯的預存程序中實作 CASE 運算式](../../relational-databases/in-memory-oltp/implementing-a-case-expression-in-a-natively-compiled-stored-procedure.md)。<br/><br/>[!INCLUDE[ssSDSFull_md](../../includes/ssSDSFull-md.md)] 和 SQL Server (從 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 起) 可支援 CASE 運算式。|  
|功能|INSERT EXECUTE|移除參考。|  
|功能|執行 CREATE 陳述式之前，請先執行|僅支援執行原生編譯預存程序和使用者定義函數。|  
|功能|使用者定義彙總|使用者定義彙總函式不可在原生編譯預存程序中使用。 請從程序中移除函式的參考。|  
|功能|瀏覽模式中繼資料|原生編譯預存程序不支援瀏覽模式中繼資料。 請確定工作階段選項 **NO_BROWSETABLE** 設為 OFF。|  
|功能|FROM 子句中的 DELETE|原生編譯預存程序中具有資料表來源的 **FROM** 陳述式不支援 **DELETE** 子句。<br /><br /> 支援搭配使用 **DELETE** 和 **FROM** 子句來指定要刪除的資料表來源。|  
|功能|FROM 子句中的 UPDATE|原生編譯預存程序中的 **FROM** 陳述式不支援 **UPDATE** 子句。| 
|功能|暫存程序|不可對暫存預存程序進行原生編譯。 請建立永久的原生編譯預存程序，或暫存的解譯 [!INCLUDE[tsql](../../includes/tsql-md.md)] 預存程序。|  
|隔離等級|READ UNCOMMITTED|原生編譯預存程序中不支援隔離等級 READ UNCOMMITTED。 請使用支援的隔離等級，例如 SNAPSHOT。|  
|隔離等級|READ COMMITTED|原生編譯預存程序中不支援隔離等級 READ COMMITTED。 請使用支援的隔離等級，例如 SNAPSHOT。|  
|功能|暫存資料表|tempdb 中的資料表不可在原生編譯預存程序中使用。 請改用 DURABILITY=SCHEMA_ONLY 的資料表變數或記憶體最佳化資料表。|  
|功能|DTC|記憶體最佳化資料表和原生編譯預存程序無法從分散式交易中存取。 請改用 SQL 交易。|  
|功能|EXECUTE WITH RECOMPILE|原生編譯預存程序中不支援 **WITH RECOMPILE** 選項。|  
|功能|從專用管理員連接執行。|原生編譯預存程序無法從專用管理員連接 (DAC) 執行。 請改用正常連接。|  
|作業|儲存點|原生編譯預存程序不可從擁有使用中儲存點的交易中叫用。 請從交易中移除儲存點。|  
|作業|ALTER AUTHORIZATION|不支援變更現有記憶體最佳化資料表或原生編譯預存程序的擁有者。 請卸除後再重新建立資料表或程序，以變更擁有權。|  
|運算子|OPENROWSET|不支援此運算子。 請從原生編譯預存程序中移除 **OPENROWSET** 。|  
|運算子|OPENQUERY|不支援此運算子。 請從原生編譯預存程序中移除 **OPENQUERY** 。|  
|運算子|OPENDATASOURCE|不支援此運算子。 請從原生編譯預存程序中移除 **OPENDATASOURCE** 。|  
|運算子|OPENXML|不支援此運算子。 請從原生編譯預存程序中移除 **OPENXML** 。|  
|運算子|CONTAINSTABLE|不支援此運算子。 請從原生編譯預存程序中移除 **CONTAINSTABLE** 。|  
|運算子|FREETEXTTABLE|不支援此運算子。 請從原生編譯預存程序中移除 **FREETEXTTABLE** 。|  
|功能|資料表值函式|資料表值函式不可從原生編譯預存程序參考。 此限制的可行因應措施是將資料表值函式內的邏輯加入程序主體中。|  
|運算子|CHANGETABLE|不支援此運算子。 請從原生編譯預存程序中移除 **CHANGETABLE** 。|  
|運算子|GOTO|不支援此運算子。 請使用其他程序性建構，例如 WHILE。|  
|運算子|OFFSET|不支援此運算子。 請從原生編譯預存程序中移除 **OFFSET** 。|  
|運算子|INTERSECT|不支援此運算子。 請從原生編譯預存程序中移除 **INTERSECT** 。 在某些情況下，可以使用 INNER JOIN 獲得相同結果。|  
|運算子|EXCEPT|不支援此運算子。 請從原生編譯預存程序中移除 **EXCEPT** 。|  
|運算子|APPLY|**適用於：** [!INCLUDE[ssSQL14-md](../../includes/sssql14-md.md)] 和 SQL Server (從 [!INCLUDE[ssSQL15-md](../../includes/sssql16-md.md)] 起)<br/>不支援此運算子。 請從原生編譯預存程序中移除 **APPLY** 。<br/><br/>[!INCLUDE[ssSDSFull_md](../../includes/ssSDSFull-md.md)] 和 SQL Server (從 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 起) 可支援原生編譯模組中的 APPLY 運算子。|  
|運算子|PIVOT|不支援此運算子。 請從原生編譯預存程序中移除 **PIVOT** 。|  
|運算子|UNPIVOT|不支援此運算子。 請從原生編譯預存程序中移除 **UNPIVOT** 。|  
|運算子|CONTAINS|不支援此運算子。 請從原生編譯預存程序中移除 **CONTAINS** 。|  
|運算子|FREETEXT|不支援此運算子。 請從原生編譯預存程序中移除 **FREETEXT** 。|  
|運算子|TSEQUAL|不支援此運算子。 請從原生編譯預存程序中移除 **TSEQUAL** 。|  
|運算子|LIKE|不支援此運算子。 請從原生編譯預存程序中移除 **LIKE** 。|  
|運算子|NEXT VALUE FOR|原生編譯預存程序內可以參考順序。 請使用解譯的 [!INCLUDE[tsql](../../includes/tsql-md.md)]取得值，並將它傳遞至原生編譯預存程序。 如需詳細資訊，請參閱 [在記憶體最佳化資料表中實作 IDENTITY](../../relational-databases/in-memory-oltp/implementing-identity-in-a-memory-optimized-table.md)。|  
|Set 選項|*選項*|原生編譯預存程序內的 SET 選項不可變更。 某些選項可以透過 BEGIN ATOMIC 陳述式設定。 如需詳細資訊，請參閱＜ [Natively Compiled Stored Procedures](./a-guide-to-query-processing-for-memory-optimized-tables.md)＞中的＜Atonic 區塊＞一節。|  
|運算元|TABLESAMPLE|不支援此運算子。 請從原生編譯預存程序中移除 **TABLESAMPLE** 。|  
|選項|RECOMPILE|原生編譯預存程序是在建立時編譯。 請從程序定義中移除 **RECOMPILE** 。<br /><br /> 您可以在原生編譯預存程序上執行 sp_recompile，下次執行時就會重新編譯它。|  
|選項|ENCRYPTION|不支援這個選項。 請從程序定義中移除 **ENCRYPTION** 。|  
|選項|FOR REPLICATION|不可針對複寫建立原生編譯預存程序。 已從程序定義中移除 **FOR REPLICATION** 。|  
|選項|FOR XML|不支援這個選項。 請從原生編譯預存程序中移除 **FOR XML** 。|  
|選項|FOR BROWSE|不支援這個選項。 請從原生編譯預存程序中移除 **FOR BROWSE** 。|  
|聯結提示|HASH、MERGE|原生編譯預存程序僅支援巢狀迴圈聯結。 不支援雜湊和合併聯結。 請移除聯結提示。|  
|查詢提示|*查詢提示*|此查詢提示不在原生編譯預存程序內。 如需支援的查詢提示，請參閱[查詢提示 &#40;Transact-SQL&#41;](../../t-sql/queries/hints-transact-sql-query.md)。|  
|選項|PERCENT|**TOP** 子句不支援此選項。 請從原生編譯預存程序的查詢中移除 **PERCENT** 。|  
|選項|WITH TIES|**適用於：** [!INCLUDE[ssSDS14_md](../../includes/sssql14-md.md)] 和 [!INCLUDE[ssSQL15-md](../../includes/sssql16-md.md)]<br/>**TOP** 子句不支援此選項。 請從原生編譯預存程序的查詢中移除 **WITH TIES** 。<br/><br/>[!INCLUDE[ssSDSFull_md](../../includes/ssSDSFull-md.md)] 和 SQL Server (從 [!INCLUDE[ssSQLv14_md](../../includes/sssqlv14-md.md)] 起) 可支援 **TOP WITH TIES**。|  
|彙總函式|*彙總函式*|並非所有彙總函式都受支援。 如需原生編譯 T-SQL 模組中支援的彙總函式詳細資訊，請參閱[原生編譯 T-SQL 模組支援的功能](../../relational-databases/in-memory-oltp/supported-features-for-natively-compiled-t-sql-modules.md)。|  
|排名函數|*次序函數*|原生編譯預存程序中不支援排名函數。 請從程序定義中將它們移除。|  
|函式|*Function*|不支援此函數。 如需原生編譯 T-SQL 模組中支援的函式詳細資訊，請參閱[原生編譯 T-SQL 模組支援的功能](../../relational-databases/in-memory-oltp/supported-features-for-natively-compiled-t-sql-modules.md)。|  
|引數|*陳述式*|不支援此陳述式。 如需原生編譯 T-SQL 模組中支援的函式詳細資訊，請參閱[原生編譯 T-SQL 模組支援的功能](../../relational-databases/in-memory-oltp/supported-features-for-natively-compiled-t-sql-modules.md)。|  
|功能|MIN 和 MAX 可與二進位和字元字串並用|彙總函式 **MIN** 和 **MAX** 不可用於原生編譯預存程序內的字元和二進位字串值。|  
|功能|GROUP BY ALL|在原生編譯的預存程序中，ALL 不得與 GROUP BY 子句並用。 從 GROUP BY 子句中移除 ALL。|  
|功能|GROUP BY ()|無法使用空白清單做為群組依據。 請移除 GROUP BY 子句，或在群組清單中加入資料行。|  
|功能|ROLLUP|**ROLLUP** 無法與原生編譯預存程序中的 **GROUP BY** 子句一起使用。 請從程序定義中移除 **ROLLUP** 。|  
|功能|CUBE|**CUBE** 無法與原生編譯預存程序中的 **GROUP BY** 子句一起使用。 請從程序定義中移除 **CUBE** 。|  
|功能|GROUPING SETS|**GROUPING SETS** 無法與原生編譯預存程序中的 **GROUP BY** 子句一起使用。 請從程序定義中移除 **GROUPING SETS** 。|  
|功能|BEGIN TRANSACTION、COMMIT TRANSACTION 與 ROLLBACK TRANSACTION。|使用 ATOMIC 區塊控制交易和錯誤處理。 如需詳細資訊，請參閱 [Atomic Blocks](../../relational-databases/in-memory-oltp/atomic-blocks-in-native-procedures.md)。|  
|功能|內嵌資料表變數宣告。|資料表變數必須明確參考定義的記憶體最佳化資料表類型。 您應該建立記憶體最佳化資料表的類型，並使用該類型宣告變數，而不要指定內嵌類型。|  
|功能|磁碟型資料表|磁碟型資料表無法從原生編譯的預存程序中存取。 從原生編譯的預存程序移除磁碟型資料表類型的參考。 或是將磁碟型資料表移轉至經過最佳化的記憶體。|  
|功能|檢視|檢視無法從原生編譯的預存程序中存取。 請參考基底資料表，而不要使用檢視。|  
|功能|資料表值函式|**適用於：** [!INCLUDE[ssSDSFull_md](../../includes/ssSDSFull-md.md)] 和 SQL Server (從 [!INCLUDE[ssSQL15-md](../../includes/sssql16-md.md)] 起)<br/>您無法透過原生編譯的預存程序來存取多重陳述式資料表函式。 系統支援內嵌資料表值函式，但您必須使用 NATIVE_COMPILATION 來建立。<br/><br/>**適用於**：[!INCLUDE[ssSQL14-md](../../includes/ssSQL14-md.md)]<br/>您無法透過原生編譯的 T-SQL 模組來參考資料表值函式。|  
|選項|PRINT|移除參考|  
|功能|DDL|原生編譯 T-SQL 模組內不支援任何 DDL。|  
|選項|STATISTICS XML|不支援。 當您執行查詢時，只要啟用了 STATISTICS XML，就會傳回沒有原生編譯預存程序這部分的 XML 內容。|  
  
## <a name="transactions-that-access-memory-optimized-tables"></a>存取記憶體最佳化資料表的交易  
 下表列出可能出現於涉及存取記憶體最佳化資料表之交易錯誤的訊息文字中的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 功能和關鍵字，以及解決錯誤的更正動作。  
  
|類型|名稱|解決方案|  
|----------|----------|----------------|  
|功能|儲存點|不支援在存取記憶體最佳化資料表的交易中建立明確儲存點。|  
|功能|繫結式交易|繫結工作階段無法參與存取記憶體最佳化資料表的交易。 在執行程序之前，請不要繫結工作階段。|  
|功能|DTC|存取記憶體最佳化資料表的交易不可以是分散式交易。|  
  
## <a name="see-also"></a>另請參閱  
 [移轉至 In-Memory OLTP](./plan-your-adoption-of-in-memory-oltp-features-in-sql-server.md)  
  
