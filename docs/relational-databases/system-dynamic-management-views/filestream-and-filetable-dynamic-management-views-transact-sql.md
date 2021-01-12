---
description: Filestream 及 FileTable 動態管理檢視 (Transact-SQL)
title: Filestream 和 FileTable 動態管理檢視 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/03/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- FileTables [SQL Server], dynamic management views
ms.assetid: e50a135d-6644-42a4-a0df-1c7a2b722051
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 3af7a4d72a93d2c44f470f83f6a0010737d3adcc
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099028"
---
# <a name="filestream-and-filetable-dynamic-management-views-transact-sql"></a>Filestream 及 FileTable 動態管理檢視 (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  本節說明 FILESTREAM 與 FileTable 功能相關的動態管理檢視。  
  
## <a name="filestream-dynamic-management-views-and-functions"></a>Filestream Dynamic Management 檢視及函數  
 [sys.dm_filestream_file_io_handles &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-filestream-file-io-handles-transact-sql.md)  
 顯示目前所開啟之異動檔案的控制代碼。  
  
 [sys.dm_filestream_file_io_requests &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-filestream-file-io-requests-transact-sql.md)  
 顯示目前的檔案輸入與檔案輸出要求。  
  
## <a name="filetable-dynamic-management-views-and-functions"></a>FileTable Dynamic Management 檢視及函數  
 [sys.dm_filestream_non_transacted_handles &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-filestream-non-transacted-handles-transact-sql.md)  
 顯示 FileTable 資料目前開啟的非交易式檔案控制代碼。  

## <a name="see-also"></a>另請參閱
[檔案資料流](../../relational-databases/blob/filestream-sql-server.md)
<br>[Filetable](../../relational-databases/blob/filetables-sql-server.md)
<br>[Filestream 和 FileTable 目錄檢視 (Transact-SQL)](../system-catalog-views/filestream-and-filetable-catalog-views-transact-sql.md)
<br>[Filestream 和 FileTable 系統預存程序 (Transact-SQL)](../system-stored-procedures/filestream-and-filetable-system-stored-procedures.md)
  
