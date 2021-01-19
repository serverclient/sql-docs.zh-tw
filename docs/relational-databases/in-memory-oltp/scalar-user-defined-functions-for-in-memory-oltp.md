---
title: 記憶體內部 OLTP 的純量使用者定義函式 | Microsoft Docs
description: 了解如何在 SQL Server 中，針對記憶體內部 OLTP 建立及卸除原生編譯的純量使用者定義函數。 原生編譯可改善效能。
ms.custom: ''
ms.date: 03/20/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: in-memory-oltp
ms.topic: conceptual
ms.assetid: d2546e40-fdfc-414b-8196-76ed1f124bf5
author: MightyPen
ms.author: genemi
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 9fd391acc854c4ae337789149955c331ac9543eb
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172030"
---
# <a name="scalar-user-defined-functions-for-in-memory-oltp"></a>記憶體內部 OLTP 的純量使用者定義函數
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  在 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]中，您可建立和卸除原生編譯的純量使用者定義函數。 您亦可改變這些使用者定義函數。 原生編譯可改善 Transact-SQL 中使用者定義函數評估的效能。  
  
 若改變原生編譯的純量使用者定義函數，則在執行作業以及編譯新版本函數時仍可繼續使用應用程式。  
  
 如需支援的 T-SQL 建構，請參閱 [原生編譯的預存程序中支援的建構](../../relational-databases/in-memory-oltp/supported-features-for-natively-compiled-t-sql-modules.md)。  
  
## <a name="creating-dropping-and-altering-user-defined-functions"></a>建立、卸除和改變使用者定義函數  
 您可使用 CREATE FUNCTION 建立原生編譯的純量使用者定義函數，使用 DROP FUNCTION 移除使用者定義函數，以及使用 ALTER FUNCTION 變更函數。 使用者定義函數需要使用 BEGIN ATOMIC WITH。  
  
 如需關於支援語法與所有限制的資訊，請參閱下列主題。  
  
-   [CREATE FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-function-transact-sql.md)  
  
-   [ALTER FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/alter-function-transact-sql.md)  
  
-   [DROP FUNCTION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-function-transact-sql.md)  
  
     原生編譯純量使用者定義函數的 DROP FUNCTION 語法，與解譯使用者定義函數相同。  
  
-   [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)  
  
 [sp_recompile &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-recompile-transact-sql.md) 預存程序可搭配原生編譯純量使用者定義函數併用。 其會導致使用中繼資料的現有定義來重新編譯函數。  
  
 下列範例展示來自 [AdventureWorks2016CTP3](https://github.com/microsoft/sql-server-samples/releases/tag/adventureworks) 範例資料庫的純量 UDF。  
  
```sql  
CREATE FUNCTION [dbo].[ufnLeadingZeros_native](@Value int)   
RETURNS varchar(8)   
WITH NATIVE_COMPILATION, SCHEMABINDING  
AS   
BEGIN ATOMIC WITH (TRANSACTION ISOLATION LEVEL = SNAPSHOT, LANGUAGE = N'English')  
  
    DECLARE @ReturnValue varchar(8);  
    SET @ReturnValue = CONVERT(varchar(8), @Value);  
       DECLARE @i int = 0, @count int = 8 - LEN(@ReturnValue)  
  
    WHILE @i < @count  
       BEGIN  
            SET @ReturnValue = '0' + @ReturnValue;  
            SET @i += 1  
       END  
  
    RETURN (@ReturnValue);  
  
END  
```  
  
## <a name="calling-user-defined-functions"></a>呼叫使用者自訂函數  
 在運算式中，原生編譯純量使用者定義函數會使用於與內建純量函數和解譯純量使用者定義函數相同的位置。 原生編譯純量使用者定義函數，在 Transact-SQL 陳述式與原生編譯預存程序中亦可搭配 EXECUTE 陳述式併用。  
  
 您可在原生編譯預存程序以及原生編譯使用者定義函數，以及允許內建函數的情況下，使用這些純量使用者定義函數。 您亦可在傳統的 Transact-SQL 模組中使用原生編譯純量使用者定義函數。  
  
 只要可以使用解譯的純量使用者定義函數，您就可在 interop 模式中使用這些純量使用者定義函數。 此使用方式會受到跨容器交易限制所影響，如 **Transactions with Memory-Optimized Tables** (使用記憶體最佳化資料表的交易) 的 [Supported Isolation Levels for Cross-Container Transactions](../../relational-databases/in-memory-oltp/transactions-with-memory-optimized-tables.md)(跨容器交易支援的隔離等級) 一節中所述。 如需 interop 模式的詳細資訊，請參閱 [使用解譯的 Transact-SQL 存取記憶體最佳化的資料表](../../relational-databases/in-memory-oltp/accessing-memory-optimized-tables-using-interpreted-transact-sql.md)。  
  
 原生編譯純量使用者定義函數需要明確的執行內容。 如需詳細資訊，請參閱 [EXECUTE AS 子句 &#40;Transact-SQL&#41;](../../t-sql/statements/execute-as-clause-transact-sql.md)。 不支援 EXECUTE AS CALLER。 如需詳細資訊，請參閱 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)。  
  
 如需適用於 Transact-SQL Execute 陳述式以及原生編譯純量使用者定義函數的支援語法資訊，請參閱 [EXECUTE &#40;Transact-SQL&#41;](../../t-sql/language-elements/execute-transact-sql.md)。 如需在原生編譯預存程序中執行使用者定義函數的支援語法資訊，請參閱 [原生編譯的預存程序中支援的建構](../../relational-databases/in-memory-oltp/supported-features-for-natively-compiled-t-sql-modules.md)。  
  
## <a name="hints-and-parameters"></a>提示和參數  
 適用於原生編譯純量使用者定義函數內部資料表、聯結和查詢提示的支援，等同於這些原生編譯預存程序提示的適用支援。 如同解譯純量使用者定義函數，查詢提示會隨附於參考原生編譯純量使用者定義函數的 Transact-SQL 查詢，其不會影響此使用者定義函數的查詢計劃。  
  
 若受純量使用者定義函數允許，則適用於原生編譯純量使用者定義函數的支援參數，皆為適用於原生編譯預存程序的支援參數。 資料表值參數即是受支援參數的範例。  
  
## <a name="schema-bound"></a>結構描述繫結  
 下列項目會套用至原生編譯純量使用者定義函數。  
  
-   必須使用 CREATE FUNCTION 和 ALTER FUNCTION 中的 WITH SCHEMABINDING 引數，執行結構描述繫結。  
  
-   由結構描述繫結預存程序或使用者定義函數參考時，無法卸除或變更。  
  
## <a name="showplan_xml"></a>SHOWPLAN_XML  
 原生編譯純量使用者定義函數支援 SHOWPLAN_XML。 由於搭配原生編譯預存程序，因此其符合一般 SHOWPLAN_XML 結構描述。 使用者定義函數的基底項目為 `<UDF>`。  
  
 STATISTICS XML 不支援原生編譯純量使用者定義函數。 若您執行參考使用者定義函數且啟用 STATISTICS XML 的查詢，則會傳回無使用者定義函數部分的 XML 內容。  
  
## <a name="permissions"></a>權限  
 如同原生編譯預存程序，建立函數時會檢查參考自原生編譯純量使用者定義函數的物件權限。 若模擬使用者不具正確權限，則 CREATE FUNCTION 將會失敗。 若權限變更導致模擬使用者不再具有正確權限，則後續的使用者定義函數執行作業亦會失敗。  
  
 若您在原生編譯預存程序中使用原生編譯純量使用者定義函數，則在建立外部程序時會檢查執行使用者定義函數的權限。 由外部程序模擬的使用者若不具備使用者定義函數的 EXEC 權限，則會導致預存程序建立作業失敗。 若權限變更導致使用者不再具有 EXEC 權限，則會導致外部程序執行作業失敗。  
  
## <a name="see-also"></a>另請參閱  
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [以 XML 格式儲存執行計畫](../../relational-databases/performance/save-an-execution-plan-in-xml-format.md)  
  
  
