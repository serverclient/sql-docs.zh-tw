---
description: GetPathLocator (Transact-SQL)
title: GetPathLocator (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 06/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- GetPathLocator
- GetPathLocator_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- GetPathLocator function
ms.assetid: 78b7e220-445b-4fdf-811b-7253f4f2b058
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 155921b08c936bfb758f60c65b4ad737546141f5
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096349"
---
# <a name="getpathlocator-transact-sql"></a>GetPathLocator (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回 FileTable 中指定之檔案或目錄的路徑定位程式識別碼值。  
  
## <a name="syntax"></a>語法  
  
```  
  
GetPathLocator(filenamespace_path)  
```  
  
## <a name="arguments"></a>引數  
 *filenamespace_path*  
 FileTable 中的命名空間路徑。 命名空間路徑的類型為 **Nvarchar (max)**。  
  
 當資料庫屬於 Always On 可用性群組時， **GetPathLocator** 函數會接受虛擬網路名稱 (VNN) 或電腦名稱稱。  
  
## <a name="return-type"></a>傳回類型  
 **hierarchyid**  
  
## <a name="general-remarks"></a>一般備註  
 如需詳細資訊，請參閱 [Work with Directories and Paths in FileTables](../../relational-databases/blob/work-with-directories-and-paths-in-filetables.md)。  
  
## <a name="examples"></a>範例  
 當您將檔案伺服器中的檔案遷移至 FileTable 時，可以使用 **GetPathLocator** 函數。 在這個案例中，您要將檔案移入 FileTable，然後用 FileTable UNC 路徑取代每個檔案的原始 UNC 路徑。 如需完整範例，請參閱將檔案 [載入 filetable](../../relational-databases/blob/load-files-into-filetables.md)。  
  
## <a name="see-also"></a>另請參閱  
 [使用 FileTable 中的目錄與路徑](../../relational-databases/blob/work-with-directories-and-paths-in-filetables.md)  
  
  
