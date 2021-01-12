---
description: ALTER XML SCHEMA COLLECTION (Transact-SQL)
title: ALTER XML SCHEMA COLLECTION (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- ALTER_XML_SCHEMA_COLLECTION_TSQL
- ALTER XML SCHEMA COLLECTION
dev_langs:
- TSQL
helpviewer_keywords:
- schema collections [SQL Server], altering
- xml_schema_namespace function
- adding schema components
- ALTER XML SCHEMA COLLECTION statement
- XML schemas [SQL Server], adding
- XML schema collections [SQL Server], modifying
- schema collections [SQL Server], adding components
- XML schema collections [SQL Server], adding components
- importing schemas
- XML schema collections [SQL Server], altering
- schema collections [SQL Server], modifying
- multiple schema namespaces
ms.assetid: e311c425-742a-4b0d-b847-8b974bf66d53
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f10572e2ceec916d930d8efd9cd2f2402294c926
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98098477"
---
# <a name="alter-xml-schema-collection-transact-sql"></a>ALTER XML SCHEMA COLLECTION (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  將結構描述元件加入至現有的 XML 結構描述集合中。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
ALTER XML SCHEMA COLLECTION [ relational_schema. ]sql_identifier ADD 'Schema Component'  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *relational_schema*  
 識別關聯式結構描述名稱。 若未指定，則會假設使用預設的關聯式結構描述。  
  
 *sql_identifier*  
 這是 XML 結構描述集合的 SQL 識別碼。  
  
 **'** *Schema Component* **'**  
 這是要插入的結構描述元件。  
  
## <a name="remarks"></a>備註  
 請利用 ALTER XML SCHEMA COLLECTION 來加入新的 XML 結構描述，其命名空間尚未存在於 XML 結構描述集合中，或將新的元件加入至集合的現有命名空間中。  
  
 下列範例會將新 \<element> 新增至集合 `MyColl` 的現有命名空間 `https://MySchema/test_xml_schema` 中。  
  
```sql  
-- First create an XML schema collection.  
CREATE XML SCHEMA COLLECTION MyColl AS '  
   <schema   
    xmlns="http://www.w3.org/2001/XMLSchema"   
    targetNamespace="https://MySchema/test_xml_schema">  
      <element name="root" type="string"/>   
  </schema>'  
-- Modify the collection.   
ALTER XML SCHEMA COLLECTION MyColl ADD '  
  <schema xmlns="http://www.w3.org/2001/XMLSchema"   
         targetNamespace="https://MySchema/test_xml_schema">   
     <element name="anotherElement" type="byte"/>   
 </schema>';  
```  
  
 `ALTER XML SCHEMA` 會將元素 `<anotherElement>` 加入至先前定義的命名空間 `https://MySchema/test_xml_schema` 中。  
  
 請注意，如果您要在集合中加入的部份元件參考了同一集合中已存在的元件，您必須使用 `<import namespace="referenced_component_namespace" />`。 然而，在 `<xsd:import>` 中使用目前結構描述命名空間是無效的，因此會自動匯入視為目前結構描述命名空間之同一目標命名空間的元件。  
  
 若要移除集合，請使用 [DROP XML SCHEMA COLLECTION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-xml-schema-collection-transact-sql.md)。  
  
 如果此結構描述集合已經包含 Lax 驗證萬用字元或是 **xs:anyType** 類型的元素，則將新的全域元素、類型或屬性宣告加入到此結構描述集合時，將會重新驗證受到此結構描述集合限制的所有已儲存資料。  
  
## <a name="permissions"></a>權限  
 若要變更 XML SCHEMA COLLECTION，需要集合的 ALTER 權限。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-xml-schema-collection-in-the-database"></a>A. 在資料庫中建立 XML 結構描述集合  
 下列範例會建立 XML 結構描述集合 `ManuInstructionsSchemaCollection`。 這個集合只有一個結構描述命名空間。  
  
```sql  
-- Create a sample database in which to load the XML schema collection.  
CREATE DATABASE SampleDB;  
GO  
USE SampleDB;  
GO  
CREATE XML SCHEMA COLLECTION ManuInstructionsSchemaCollection AS  
N'<?xml version="1.0" encoding="UTF-16"?>  
<xsd:schema targetNamespace="https://schemas.microsoft.com/sqlserver/2004/07/adventure-works/ProductModelManuInstructions"   
   xmlns          ="https://schemas.microsoft.com/sqlserver/2004/07/adventure-works/ProductModelManuInstructions"   
   elementFormDefault="qualified"   
   attributeFormDefault="unqualified"  
   xmlns:xsd="http://www.w3.org/2001/XMLSchema" >  
  
    <xsd:complexType name="StepType" mixed="true" >  
        <xsd:choice  minOccurs="0" maxOccurs="unbounded" >   
            <xsd:element name="tool" type="xsd:string" />  
            <xsd:element name="material" type="xsd:string" />  
            <xsd:element name="blueprint" type="xsd:string" />  
            <xsd:element name="specs" type="xsd:string" />  
            <xsd:element name="diag" type="xsd:string" />  
        </xsd:choice>   
    </xsd:complexType>  
  
    <xsd:element  name="root">  
        <xsd:complexType mixed="true">  
            <xsd:sequence>  
                <xsd:element name="Location" minOccurs="1" maxOccurs="unbounded">  
                    <xsd:complexType mixed="true">  
                        <xsd:sequence>  
                            <xsd:element name="step" type="StepType" minOccurs="1" maxOccurs="unbounded" />  
                        </xsd:sequence>  
                        <xsd:attribute name="LocationID" type="xsd:integer" use="required"/>  
                        <xsd:attribute name="SetupHours" type="xsd:decimal" use="optional"/>  
                        <xsd:attribute name="MachineHours" type="xsd:decimal" use="optional"/>  
                        <xsd:attribute name="LaborHours" type="xsd:decimal" use="optional"/>  
                        <xsd:attribute name="LotSize" type="xsd:decimal" use="optional"/>  
                    </xsd:complexType>  
                </xsd:element>  
            </xsd:sequence>  
        </xsd:complexType>  
    </xsd:element>  
</xsd:schema>' ;  
GO  
-- Verify - list of collections in the database.  
SELECT *  
FROM sys.xml_schema_collections;  
-- Verify - list of namespaces in the database.  
SELECT name  
FROM sys.xml_schema_namespaces;  
  
-- Use it. Create a typed xml variable. Note the collection name   
-- that is specified.  
DECLARE @x xml (ManuInstructionsSchemaCollection);  
GO  
--Or create a typed xml column.  
CREATE TABLE T (  
        i int primary key,   
        x xml (ManuInstructionsSchemaCollection));  
GO  
-- Clean up.  
DROP TABLE T;  
GO  
DROP XML SCHEMA COLLECTION ManuInstructionsSchemaCollection;  
Go  
USE master;  
GO  
DROP DATABASE SampleDB;  
```  
  
 或者，您可以指派結構描述集合到變數中，並在 `CREATE XML SCHEMA COLLECTION` 陳述式中指定變數，如下所示：  
  
```sql  
DECLARE @MySchemaCollection nvarchar(max);  
SET @MySchemaCollection  = N' copy the schema collection here';  
CREATE XML SCHEMA COLLECTION AS @MySchemaCollection;   
```  
  
 範例中的變數屬於 `nvarchar(max)` 類型。 變數也可以屬於 **xml** 資料類型，在這個情況下，變數會被隱含地轉換成字串。  
  
 如需詳細資訊，請參閱 [檢視儲存的 XML 結構描述集合](../../relational-databases/xml/view-a-stored-xml-schema-collection.md)。  
  
 您可以在 **xml** 類型資料行中儲存結構描述集合。 在這個情況下，若要建立 XML 結構描述集合，請執行下列步驟：  
  
1.  使用 SELECT 陳述式從資料行擷取結構描述集合，並將它指派給 **xml** 類型或 **varchar** 類型的變數。  
  
2.  在 CREATE XML SCHEMA COLLECTION 陳述式中指定變數名稱。  
  
 CREATE XML SCHEMA COLLECTION 只會儲存 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 暸解的結構描述元件；XML 結構描述中的所有項目都不會儲存在資料庫中。 因此，如果您想要 XML 結構描述集合回復成和原來所提供的完全相同，建議您將 XML 結構描述儲存於資料庫資料行或電腦的其他資料夾中。  
  
### <a name="b-specifying-multiple-schema-namespaces-in-a-schema-collection"></a>B. 在結構描述集合中指定多個結構描述命名空間  
 建立 XML 結構描述集合時，您也可以指定多個 XML 結構描述。 例如：  
  
```sql  
CREATE XML SCHEMA COLLECTION N'  
<xsd:schema>....</xsd:schema>  
<xsd:schema>...</xsd:schema>';  
```  
  
 下列範例會建立包括兩個 XML 結構描述命名空間的 XML 結構描述集合 `ProductDescriptionSchemaCollection`。  
  
```sql  
CREATE XML SCHEMA COLLECTION ProductDescriptionSchemaCollection AS   
'<xsd:schema targetNamespace="https://schemas.microsoft.com/sqlserver/2004/07/adventure-works/ProductModelWarrAndMain"  
    xmlns="https://schemas.microsoft.com/sqlserver/2004/07/adventure-works/ProductModelWarrAndMain"   
    elementFormDefault="qualified"   
    xmlns:xsd="http://www.w3.org/2001/XMLSchema" >  
    <xsd:element name="Warranty"  >  
        <xsd:complexType>  
            <xsd:sequence>  
                <xsd:element name="WarrantyPeriod" type="xsd:string"  />  
                <xsd:element name="Description" type="xsd:string"  />  
            </xsd:sequence>  
        </xsd:complexType>  
    </xsd:element>  
</xsd:schema>  
 <xs:schema targetNamespace="https://schemas.microsoft.com/sqlserver/2004/07/adventure-works/ProductModelDescription"   
    xmlns="https://schemas.microsoft.com/sqlserver/2004/07/adventure-works/ProductModelDescription"   
    elementFormDefault="qualified"   
    xmlns:mstns="https://tempuri.org/XMLSchema.xsd"   
    xmlns:xs="http://www.w3.org/2001/XMLSchema"  
    xmlns:wm="https://schemas.microsoft.com/sqlserver/2004/07/adventure-works/ProductModelWarrAndMain" >  
    <xs:import   
namespace="https://schemas.microsoft.com/sqlserver/2004/07/adventure-works/ProductModelWarrAndMain" />  
    <xs:element name="ProductDescription" type="ProductDescription" />  
        <xs:complexType name="ProductDescription">  
            <xs:sequence>  
                <xs:element name="Summary" type="Summary" minOccurs="0" />  
            </xs:sequence>  
            <xs:attribute name="ProductModelID" type="xs:string" />  
            <xs:attribute name="ProductModelName" type="xs:string" />  
        </xs:complexType>  
        <xs:complexType name="Summary" mixed="true" >  
            <xs:sequence>  
                <xs:any processContents="skip" namespace="http://www.w3.org/1999/xhtml" minOccurs="0" maxOccurs="unbounded" />  
            </xs:sequence>  
        </xs:complexType>  
</xs:schema>'  
;  
GO   
-- Clean up  
DROP XML SCHEMA COLLECTION ProductDescriptionSchemaCollection;  
GO  
```  
  
### <a name="c-importing-a-schema-that-does-not-specify-a-target-namespace"></a>C. 匯入不指定目標命名空間的結構描述  
 如果將不包含 **targetNamespace** 屬性的結構描述匯入集合中，其元件會與空字串目標命名空間關聯，如下列範例所示。 請注意，不對匯入集合之一或多個結構描述進行關聯，會造成多個結構描述元件 (可能無關) 與預設空字串命名空間相關聯。  
  
```sql  
-- Create a collection that contains a schema with no target namespace.  
CREATE XML SCHEMA COLLECTION MySampleCollection AS '  
<schema xmlns="http://www.w3.org/2001/XMLSchema"  xmlns:ns="http://ns">  
<element name="e" type="dateTime"/>  
</schema>';  
GO  
-- query will return the names of all the collections that   
--contain a schema with no target namespace  
SELECT sys.xml_schema_collections.name   
FROM   sys.xml_schema_collections   
JOIN   sys.xml_schema_namespaces   
ON     sys.xml_schema_collections.xml_collection_id =   
       sys.xml_schema_namespaces.xml_collection_id   
WHERE  sys.xml_schema_namespaces.name='';  
```  
  
## <a name="see-also"></a>另請參閱  
 [CREATE XML SCHEMA COLLECTION &#40;Transact-SQL&#41;](../../t-sql/statements/create-xml-schema-collection-transact-sql.md)   
 [DROP XML SCHEMA COLLECTION &#40;Transact-SQL&#41;](../../t-sql/statements/drop-xml-schema-collection-transact-sql.md)   
 [EVENTDATA &#40;Transact-SQL&#41;](../../t-sql/functions/eventdata-transact-sql.md)   
 [比較具類型的 XML 與不具類型的 XML](../../relational-databases/xml/compare-typed-xml-to-untyped-xml.md)   
 [伺服器上 XML 結構描述集合的需求與限制](../../relational-databases/xml/requirements-and-limitations-for-xml-schema-collections-on-the-server.md)  
  
  
