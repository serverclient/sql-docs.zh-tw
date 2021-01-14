---
title: Xquery 處理關聯式資料 |Microsoft Docs
description: '瞭解如何使用 XQuery 擴充功能 sql：資料行 ( # A1 和 sql： variable ( # A3，將非 XML 關聯式資料系結至 XML。'
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.prod_service: sql
ms.reviewer: ''
ms.technology: xml
ms.topic: language-reference
dev_langs:
- XML
helpviewer_keywords:
- relational data [XQuery]
- XQuery, relational data
ms.assetid: 9812b71a-52ec-48a0-92f3-016a93660229
author: rothja
ms.author: jroth
ms.openlocfilehash: 6ac32119090ba7973ad628c35f6b5b994eb03561
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172350"
---
# <a name="xqueries-handling-relational-data"></a>XQueries 處理關聯式資料
[!INCLUDE [SQL Server Azure SQL Database ](../includes/applies-to-version/sqlserver.md)]

  您可以使用其中一個 [Xml 資料類型方法](../t-sql/xml/xml-data-type-methods.md)，對 **Xml** 類型的資料行或變數指定 XQuery。 這些包括 **查詢 ( # B1**、 **值 ( # B3**、 **存在 ( # B5**，或 **修改 ( # B7**。 對查詢中所識別出的 XML 執行個體執行 XQuery，以產生 XML 。  
  
 執行 XQuery 而產生的 XML，可以包含從其他 Transact-SQL 變數或資料列集資料行擷取的值。 若要將非 XML 關聯式資料繫結到產生的 XML，SQL Server 可提供以下虛擬函數做為 XQuery 延伸模組：  
  
-   **sql:column()** function  
  
-   **sql:variable()** function  
  
 當您在查詢中指定 XQuery 時，可以使用這些 XQuery 擴充 ( **xml** 資料類型的 **# B1** 方法。 如此一來， **( # B1** 方法的查詢就可以產生結合 xml 和非 **xml** 資料類型之資料的 xml。  
  
 當您使用 **xml** 資料類型方法 **Modify ( # B1**、 **value ( # B3**、 **query ( # B5**）時，也可以使用這些函式， **( # B7** 來公開 xml 內的關聯式值。  
  
 如需詳細資訊，請參閱 [sql： column ( # A1 函數 (xquery) ](../xquery/xquery-extension-functions-sql-column.md) 和 [sql： Variable ( # A5 函式 (xquery) ](../xquery/xquery-extension-functions-sql-variable.md)。  
  
## <a name="see-also"></a>另請參閱  
 [XML 資料 &#40;SQL Server&#41;](../relational-databases/xml/xml-data-sql-server.md)   
 [XQuery 語言參考 &#40;SQL Server&#41;](../xquery/xquery-language-reference-sql-server.md)   
 [XML 結構 &#40;XQuery&#41;](../xquery/xml-construction-xquery.md)  
  
  
