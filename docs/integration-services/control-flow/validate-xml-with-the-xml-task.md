---
description: Validate XML with the XML Task
title: 使用 XML 工作驗證 XML | Microsoft Docs
ms.custom: ''
ms.date: 09/08/2020
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: conceptual
helpviewer_keywords:
- XML validation
- XML, validating
ms.assetid: 224fc025-c21f-4d43-aa9d-5ffac337f9b0
author: chugugrace
ms.author: chugu
ms.openlocfilehash: 97e0ea80dcc5cffefc99cbbc928eace94ade942a
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172590"
---
# <a name="validate-xml-with-the-xml-task"></a>Validate XML with the XML Task

[!INCLUDE[sqlserver-ssis](../../includes/applies-to-version/sqlserver-ssis.md)]


  驗證 XML 文件，並啟用 XML 工作的 **ValidationDetails** 屬性以取得詳細的錯誤輸出。  
  
 下列螢幕擷取畫面顯示 [XML 工作編輯器]，內含具有豐富錯誤輸出之 XML 驗證所需的設定。  
  
 ![[XML 工作編輯器] 中的 XML 工作屬性](../../integration-services/control-flow/media/xmltaskproperties.jpg "[XML 工作編輯器] 中的 XML 工作屬性")  
  
 在提供 **ValidationDetails** 屬性前，XML 工作所執行的 XML 驗證只會傳回結果為 True 或 False，而不會有錯誤的相關資訊及其位置。 現在，當您將 **ValidationDetails** 設定為 True 時，輸出檔案即涵蓋每項錯誤的詳細資訊，包括行號及位置。 您可以使用此資訊來了解、尋找及修正 XML 文件中的錯誤。  
  
 XML 驗證功能可針對大型 XML 文件和大量的錯誤輕鬆地進行調整。 因為輸出檔案本身是 XML 格式，所以您可以查詢和分析輸出。 例如，如果輸出包含大量錯誤，您可以使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢將錯誤分組 (如本主題所述)。  
  
> [!NOTE]
>  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[ssISnoversion](../../includes/ssisnoversion-md.md)] ( [!INCLUDE[ssIS](../../includes/ssis-md.md)]) 在 **Service Pack 2 中引進** ValidationDetails[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 屬性。 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] 和 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]也提供此屬性。  
  
## <a name="sample-output-for-xml-thats-valid"></a>有效 XML 範例輸出  
 以下範例輸出檔案具有有效 XML 檔案的驗證結果。  
  
```xml  
  
<root xmlns:ns="https://schemas.microsoft.com/xmltools/2002/xmlvalidation">  
    <metadata>  
        <result>true</result>  
        <errors>0</errors>  
        <warnings>0</warnings>  
        <startTime>2015-05-28T10:27:22.087</startTime>  
        <endTime>2015-05-28T10:29:07.007</endTime>  
        <xmlFile>d:\Temp\TestData.xml</xmlFile>  
        <xsdFile>d:\Temp\TestSchema.xsd</xsdFile>  
    </metadata>  
    <messages />  
</root>  
```  
  
## <a name="sample-output-for-xml-thats-not-valid"></a>無效 XML 範例輸出  
 以下範例輸出檔案具有含有少量錯誤之 XML 檔案的驗證結果。 \<error> 元素的文字已換行，以增加可讀性。  
  
```xml  
  
<root xmlns:ns="https://schemas.microsoft.com/xmltools/2002/xmlvalidation">  
    <metadata>  
        <result>false</result>  
        <errors>2</errors>  
        <warnings>0</warnings>  
        <startTime>2015-05-28T10:45:09.538</startTime>  
        <endTime>2015-05-28T10:45:09.558</endTime>  
        <xmlFile>C:\Temp\TestData.xml</xmlFile>  
        <xsdFile>C:\Temp\TestSchema.xsd</xsdFile>  
    </metadata>  
    <messages>  
        <error line="5" position="26">The 'ApplicantRole' element is invalid - The value 'wer3' is invalid  
    according to its datatype 'ApplicantRoleType' - The Enumeration constraint failed.</error>  
        <error line="16" position="28">The 'Phone' element is invalid - The value 'we3056666666' is invalid  
     according to its datatype 'phone' - The Pattern constraint failed.</error>  
    </messages>  
</root>  
```  
  
## <a name="analyze-xml-validation-output-with-a-transact-sql-query"></a>使用 Transact-SQL 查詢分析 XML 驗證輸出  
 如果 XML 驗證的輸出包含大量錯誤，您可以使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢，以載入 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]中的輸出。 然後，您可以使用 T-SQL 語言的所有功能 (包括 WHERE、GROUP BY、ORDER BY、JOIN 等) 來分析錯誤清單。  
  
```sql  
DECLARE @xml XML;  
  
SELECT @xml = XmlDoc     
FROM OPENROWSET (BULK N'C:\Temp\XMLValidation_2016-02-212T10-41-00.xml', SINGLE_BLOB) AS Tab(XmlDoc);  
  
-- Query # 1, flat list of errors  
-- convert to relational/rectangular  
;WITH XMLNAMESPACES ('https://schemas.microsoft.com/xmltools/2002/xmlvalidation' AS ns), rs AS  
(  
SELECT col.value('@line','INT') AS line  
     , col.value('@position','INT') AS position  
     , col.value('.','VARCHAR(1024)') AS error  
FROM @XML.nodes('/root/messages/error') AS tab(col)  
)  
SELECT * FROM rs;  
-- WHERE error LIKE '%whatever_string%'  
  
-- Query # 2, count of errors grouped by the error message  
-- convert to relational/rectangular  
;WITH XMLNAMESPACES ('https://schemas.microsoft.com/xmltools/2002/xmlvalidation' AS ns), rs AS  
(  
SELECT col.value('@line','INT') AS line  
     , col.value('@position','INT') AS position  
     , col.value('.','VARCHAR(1024)') AS error  
FROM @XML.nodes('/root/messages/error') AS tab(col)  
)  
SELECT COALESCE(error,'Total # of errors:') AS [error], COUNT(*) AS [counter]  
FROM rs  
GROUP BY GROUPING SETS ((error), ())  
ORDER BY 2 DESC, COALESCE(error, 'Z');  
  
```  
  
 以下是先前文字所示之第二個範例查詢的 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 中的結果。  
  
 ![在 Management Studio 中使用查詢將 XML 錯誤分組](../../integration-services/control-flow/media/query-for-xml-errors.png "在 Management Studio 中使用查詢將 XML 錯誤分組")  
  
## <a name="see-also"></a>另請參閱  
 [XML 工作](../../integration-services/control-flow/xml-task.md)   
 [XML 工作編輯器 &#40;一般頁面&#41;](./xml-task.md)  
  
