---
description: FileTableRootPath (Transact-SQL)
title: FileTableRootPath (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- FileTableRootPath_TSQL
- FileTableRootPath
dev_langs:
- TSQL
helpviewer_keywords:
- FileTableRootPath function
ms.assetid: 0cba908a-c85c-4b09-b16a-df1cb333c629
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: f08273adcf261ecfdafaa70793d50b31360f7e40
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093855"
---
# <a name="filetablerootpath-transact-sql"></a>FileTableRootPath (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回特定 FileTable 或目前資料庫的根層級 UNC 路徑。  
  
## <a name="syntax"></a>語法  
  
```  
  
FileTableRootPath ( [ '[schema_name.]FileTable_name' ], @option )  
```  
  
## <a name="arguments"></a>引數  
 *FileTable_name*  
 FileTable 的名稱。 *FileTable_name* 的類型為 **Nvarchar**。 這是選擇性參數。 預設值為目前的資料庫。 指定 *schema_name* 也是選擇性的。 您可以傳遞 Null 給 *FileTable_name* ，以使用預設參數值  
  
 *\@選項*  
 定義路徑之伺服器元件格式化方式的整數運算式。 *\@ 選項* 可以有下列其中一個值：  
  
|值|描述|  
|-----------|-----------------|  
|**0**|傳回轉換成 NetBIOS 格式的伺服器名稱，例如：<br /><br /> `\\SERVERNAME\MSSQLSERVER\MyDocumentDatabase`<br /><br /> 這是預設值。|  
|**1**|在不轉換的情況下傳回伺服器名稱，例如：<br /><br /> `\\ServerName\MSSQLSERVER\MyDocumentDatabase`|  
|**2**|傳回完整伺服器路徑，例如：<br /><br /> `\\ServerName.MyDomain.com\MSSQLSERVER\MyDocumentDatabase`|  
  
## <a name="return-type"></a>傳回類型  
 **nvarchar(4000)**  
  
 當資料庫屬於 Always On 可用性群組時， **FileTableRootPath** 函數會傳回虛擬網路名稱 (VNN) ，而不是電腦名稱稱。  
  
## <a name="general-remarks"></a>一般備註  
 當下列其中一個條件成立時， **FileTableRootPath** 函數會傳回 Null：  
  
-   *FileTable_name* 的值無效。  
  
-   呼叫端沒有足以參考指定資料表或目前資料庫的權限。  
  
-   未針對目前資料庫設定 *database_directory* 的 FILESTREAM 選項。  
  
 如需詳細資訊，請參閱 [Work with Directories and Paths in FileTables](../../relational-databases/blob/work-with-directories-and-paths-in-filetables.md)。  
  
## <a name="best-practices"></a>最佳做法  
 若要讓程式碼和應用程式獨立於目前的電腦和資料庫之外，請避免撰寫依賴絕對檔案路徑的程式碼。 請改為在執行時間使用 **FileTableRootPath** 和 **GetFileNamespacePath** 函數來取得檔案的完整路徑，如下列範例所示。 根據預設， **GetFileNamespacePath** 函數會傳回資料庫根路徑之下的檔案相對路徑。  
  
```sql  
USE MyDocumentDatabase;  
  
@root varchar(100)  
SELECT @root = FileTableRootPath();  
@fullPath = varchar(1000);  
  
SELECT @fullPath = @root + file_stream.GetFileNamespacePath()  
FROM DocumentStore  
WHERE Name = N'document.docx';  
```  
  
## <a name="security"></a>安全性  
  
### <a name="permissions"></a>權限  
 **FileTableRootPath** 函式需要：  
  
-   可以取得特定 FileTable 根路徑之 FileTable 的 SELECT 權限。  
  
-   **db_datareader** 或更高的許可權，以取得目前資料庫的根路徑。  
  
## <a name="examples"></a>範例  
 下列範例示範如何呼叫 **FileTableRootPath** 函數。  
  
```  
USE MyDocumentDatabase;  
-- returns "\\MYSERVER\MSSQLSERVER\MyDocumentDatabase"  
SELECT FileTableRootPath();  
  
-- returns "\\MYSERVER\MSSQLSERVER\MyDocumentDatabase\MyFileTable"  
SELECT FileTableRootPath(N'dbo.MyFileTable');  
  
-- returns "\\MYSERVER\MSSQLSERVER\MyDocumentDatabase\MyFileTable"  
SELECT FileTableRootPath(N'MyFileTable');  
```  
  
## <a name="see-also"></a>另請參閱  
 [使用 FileTable 中的目錄與路徑](../../relational-databases/blob/work-with-directories-and-paths-in-filetables.md)  
  
  
