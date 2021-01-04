---
title: 結構描述限制
description: 說明使用 Microsoft SqlClient Data Provider for SQL Server 時，可以搭配 GetSchema 使用的結構描述限制。
ms.date: 11/26/2020
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 8d72e2dd020f1345ceb0b68249cb3d3acd1024dc
ms.sourcegitcommit: 2add15a99df7b85d271adb261523689984dfd134
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051277"
---
# <a name="schema-restrictions"></a>結構描述限制

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

**GetSchema** 方法的第二個選擇性參數是用於限制所傳回結構描述資訊量的限制，會以字串陣列傳遞至 **GetSchema** 方法。 陣列中的位置決定您可以傳遞的值，它相當於限制號碼。  
  
例如，下表說明使用 Microsoft SqlClient Data Provider for SQL Server 的 "Tables" 結構描述集合所支援的限制。 SQL Server 結構描述集合的其他限制將列於本主題的結尾。  

|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|目錄|@Catalog|TABLE_CATALOG|1|  
|擁有者|@Owner|TABLE_SCHEMA|2|  
|Table|@Name|TABLE_NAME|3|  
|TableType|@TableType|TABLE_TYPE|4|  
 
## <a name="specifying-restriction-values"></a>指定限制值  

若要使用 "Tables" 結構描述集合的其中一個限制，只需使用四個元素建立字串陣列，然後將符合限制數目的值置於元素中。 例如，若要將 **GetSchema** 方法所傳回的資料表限制為只有 "Sales" 結構描述中的資料表，請先將陣列的第二個元素設定為 "Sales"，再將其傳送至 **GetSchema** 方法。  
  
> [!NOTE]
> - `SqlClient` 的限制集合有額外的 `ParameterName` 資料行。 限制預設值資料行仍存在，用於回溯相容性，但目前會忽略它. 指定限制值時，應使用參數化查詢而不是字串取代，來將 SQL 資料隱碼攻擊的風險減至最小。  
> - 陣列中的項目數目必須少於或等於指定結構描述集合所支援的限制數目，否則將擲回 <xref:System.ArgumentException>。 可以少於限制的最大數目。 假設遺漏的限制為 Null (未限制)。  
  
您可以藉由呼叫 **GetSchema** 方法 (具有限制結構描述集合的名稱 "Restrictions")，以查詢 Microsoft SqlClient Data Provider for SQL Server，以判定支援的限制清單。 這將傳回 <xref:System.Data.DataTable>，其包含集合名稱、限制名稱、預設限制值及限制號碼的清單。  
  
### <a name="example"></a>範例  

下列範例將示範如何使用 Microsoft SqlClient Data Provider for SQL Server <xref:Microsoft.Data.SqlClient.SqlConnection> 類別的 <xref:Microsoft.Data.SqlClient.SqlConnection.GetSchema%2A> 方法以擷取 **AdventureWorks** 範例資料庫中包含的所有資料表結構描述資訊，並將傳回的資訊限制為只有 "Sales" 結構描述中的資料表：  

[!code-csharp[SqlClient GetSchema with restrictions#1](~/../sqlclient/doc/samples/SqlConnection_GetSchema_Restriction.cs#1)]  

## <a name="sql-server-schema-restrictions"></a>SQL Server 結構描述限制  

 下表將列出 SQL Server 結構描述集合的限制。  
  
### <a name="users"></a>使用者  
  
|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|User_Name|@Name|NAME|1|  
  
### <a name="databases"></a>資料庫  
  
|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|名稱|@Name|名稱|1|  
  
### <a name="tables"></a>資料表  
  
|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|目錄|@Catalog|TABLE_CATALOG|1|  
|擁有者|@Owner|TABLE_SCHEMA|2|  
|Table|@Name|TABLE_NAME|3|  
|TableType|@TableType|TABLE_TYPE|4|  
  
### <a name="columns"></a>資料行  
  
|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|目錄|@Catalog|TABLE_CATALOG|1|  
|擁有者|@Owner|TABLE_SCHEMA|2|  
|Table|@Table|TABLE_NAME|3|  
|資料行|@Column|COLUMN_NAME|4|  
  
### <a name="structuredtypemembers"></a>StructuredTypeMembers  
  
|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|目錄|@Catalog|TABLE_CATALOG|1|  
|擁有者|@Owner|TABLE_SCHEMA|2|  
|Table|@Table|TABLE_NAME|3|  
|資料行|@Column|COLUMN_NAME|4|  
  
### <a name="views"></a>檢視  
  
|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|目錄|@Catalog|TABLE_CATALOG|1|  
|擁有者|@Owner|TABLE_SCHEMA|2|  
|Table|@Table|TABLE_NAME|3|  
  
### <a name="viewcolumns"></a>ViewColumns  
  
|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|目錄|@Catalog|VIEW_CATALOG|1|  
|擁有者|@Owner|VIEW_SCHEMA|2|  
|Table|@Table|VIEW_NAME|3|  
|資料行|@Column|COLUMN_NAME|4|  
  
### <a name="procedureparameters"></a>ProcedureParameters  
  
|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|目錄|@Catalog|SPECIFIC_CATALOG|1|  
|擁有者|@Owner|SPECIFIC_SCHEMA|2|  
|Name|@Name|SPECIFIC_NAME|3|  
|參數|@Parameter|PARAMETER_NAME|4|  
  
### <a name="procedures"></a>程序  
  
|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|目錄|@Catalog|SPECIFIC_CATALOG|1|  
|擁有者|@Owner|SPECIFIC_SCHEMA|2|  
|Name|@Name|SPECIFIC_NAME|3|  
|類型|@Type|ROUTINE_TYPE|4|  
  
### <a name="indexcolumns"></a>IndexColumns  
  
|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|目錄|@Catalog|db_name()|1|  
|擁有者|@Owner|user_name()|2|  
|Table|@Table|o.name|3|  
|ConstraintName|@ConstraintName|x.name|4|  
|資料行|@Column|c.name|5|  
  
### <a name="indexes"></a>索引  
  
|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|目錄|@Catalog|db_name()|1|  
|擁有者|@Owner|user_name()|2|  
|Table|@Table|o.name|3|  
  
### <a name="userdefinedtypes"></a>UserDefinedTypes  
  
|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|assembly_name|@AssemblyName|assemblies.name|1|  
|udt_name|@UDTName|types.assembly_class|2|  
  
### <a name="foreignkeys"></a>ForeignKeys  
  
|限制名稱|參數名稱|預設限制值|限制號碼|  
|----------------------|--------------------|-------------------------|------------------------|  
|目錄|@Catalog|CONSTRAINT_CATALOG|1|  
|擁有者|@Owner|CONSTRAINT_SCHEMA|2|  
|Table|@Table|TABLE_NAME|3|  
|名稱|@Name|CONSTRAINT_NAME|4|  
  
## <a name="see-also"></a>請參閱

- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
