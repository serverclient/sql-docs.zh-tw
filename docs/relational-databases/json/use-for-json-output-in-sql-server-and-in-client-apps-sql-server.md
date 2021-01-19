---
description: 在 SQL Server 和用戶端應用程式中使用 FOR JSON 輸出 (SQL Server)
title: 在 SQL Server 和用戶端應用程式中使用 FOR JSON 輸出
ms.date: 06/03/2020
ms.prod: sql
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- FOR JSON, using in client apps
- FOR JSON, using in SQL Server
ms.assetid: 302e5397-b499-4ea3-9a7f-c24ccad698eb
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jroth
ms.custom: seo-dt-2019
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 4bd5911552144084550abdadb3d432ab08f3bf9a
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172500"
---
# <a name="use-for-json-output-in-sql-server-and-in-client-apps-sql-server"></a>在 SQL Server 和用戶端應用程式中使用 FOR JSON 輸出 (SQL Server)
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sqlserver2016-asdb.md)]

下列範例示範在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或用戶端應用程式中使用 **FOR JSON** 子句及其 JSON 輸出的部分方式。  
  
## <a name="use-for-json-output-in-sql-server-variables"></a>在 SQL Server 變數中使用 FOR JSON 輸出  
FOR JSON 子句的輸出屬於 NVARCHAR(MAX) 類型，因此您可以將它指派給任何變數，如下列範例所示。  
  
```sql  
DECLARE @x NVARCHAR(MAX) =
  (SELECT TOP 10 *
     FROM Sales.SalesOrderHeader
     FOR JSON AUTO)  
```  
  
## <a name="use-for-json-output-in-sql-server-user-defined-functions"></a>在 SQL Server 使用者定義函數中使用 FOR JSON 輸出  
 您可以建立使用者定義函數，將結果集格式化為 JSON，再傳回此 JSON 輸出。 下列範例會建立使用者定義函數，以擷取一些銷售訂單詳細資料列，並將其格式化為 JSON 陣列。  
  
```sql  
CREATE FUNCTION GetSalesOrderDetails(@salesOrderId int)  
 RETURNS NVARCHAR(MAX)  
AS  
BEGIN  
   RETURN (SELECT UnitPrice, OrderQty  
           FROM Sales.SalesOrderDetail  
           WHERE SalesOrderID = @salesOrderId  
           FOR JSON AUTO)  
END
```  
  
 您可以在批次或查詢中使用此函數，如下列範例所示。  
  
```sql  
DECLARE @x NVARCHAR(MAX) = dbo.GetSalesOrderDetails(43659)

PRINT dbo.GetSalesOrderDetails(43659)

SELECT TOP 10
  H.*, dbo.GetSalesOrderDetails(H.SalesOrderId) AS Details
FROM Sales.SalesOrderHeader H
```  
  
## <a name="merge-parent-and-child-data-into-a-single-table"></a>將父資料和子資料合併至單一資料表中  
在下列範例中，每組子資料列會格式化為 JSON 陣列。 JSON 陣列便成為父資料表中的 [詳細資料] 資料行的值。  
  
```sql  
SELECT TOP 10 SalesOrderId, OrderDate,  
      (SELECT TOP 3 UnitPrice, OrderQty  
         FROM Sales.SalesOrderDetail D  
         WHERE H.SalesOrderId = D.SalesOrderID  
         FOR JSON AUTO) AS Details  
INTO SalesOrder  
FROM Sales.SalesOrderHeader H  
```  
  
## <a name="update-the-data-in-json-columns"></a>更新 JSON 資料行中的資料  
 下列範例示範如何更新包含 JSON 文字之資料行的值。  
  
```sql  
UPDATE SalesOrder  
SET Details =  
     (SELECT TOP 1 UnitPrice, OrderQty  
       FROM Sales.SalesOrderDetail D  
       WHERE D.SalesOrderId = SalesOrder.SalesOrderId  
      FOR JSON AUTO) 
```  
  
## <a name="use-for-json-output-in-a-c-client-app"></a>在 C# 用戶端應用程式中使用 FOR JSON 輸出  
 下列範例示範如何將查詢的 JSON 輸出，擷取至 C# 用戶端應用程式中的 StringBuilder 物件。 假設變數 `queryWithForJson` 包含 SELECT 陳述式的文字及 FOR JSON 子句。  
  
```csharp  
var queryWithForJson = "SELECT ... FOR JSON";
using(var conn = new SqlConnection("<connection string>"))
{
    using(var cmd = new SqlCommand(queryWithForJson, conn))
    {
        conn.Open();
        var jsonResult = new StringBuilder();
        var reader = cmd.ExecuteReader();
        if (!reader.HasRows)
        {
            jsonResult.Append("[]");
        }
        else
        {
            while (reader.Read())
            {
                jsonResult.Append(reader.GetValue(0).ToString());
            }
        }
    }
}
```  

## <a name="learn-more-about-json-in-sql-server-and-azure-sql-database"></a>深入了解 SQL Server 和 Azure SQL Database 中的 JSON  
  
### <a name="microsoft-videos"></a>Microsoft 影片

如需 SQL Server 和 Azure SQL Database 中內建 JSON 支援的觀看式簡介，請參閱下列影片：

-   [SQL Server 2016 和 JSON 支援](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

-   [使用 SQL Server 2016 和 Azure SQL Database 中的 JSON](https://channel9.msdn.com/Shows/Data-Exposed/Using-JSON-in-SQL-Server-2016-and-Azure-SQL-Database)

-   [NoSQL 與關聯式領域之間的橋樑 JSON](https://channel9.msdn.com/events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds)
 
## <a name="see-also"></a>另請參閱  
 [使用 FOR JSON 將查詢結果格式化為 JSON &#40;SQL Server&#41;](../../relational-databases/json/format-query-results-as-json-with-for-json-sql-server.md)  
  
  
