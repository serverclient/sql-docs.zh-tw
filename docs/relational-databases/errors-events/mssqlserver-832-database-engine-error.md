---
description: MSSQLSERVER_832
title: MSSQLSERVER_832
ms.custom: ''
ms.date: 08/20/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, Masha
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 832 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: 5252cae293cd4c700f359b8168e0d5d5d10a70cf
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099233"
---
# <a name="mssqlserver_832"></a>MSSQLSERVER_832
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|832|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|B_CONSTPAGECHANGED|
|訊息文字|原本應為固定的頁面已經變更 (總和檢查碼應為：\<expected value>，實際的總和檢查碼：\<actual value>，資料庫 \<dbid>，檔案 \'<filename>'，頁面 \<pageno>)。 這通常代表記憶體錯誤，或其他硬體或作業系統損毀。|
||

## <a name="explanation"></a>說明

外部因素導致在用來變更資料庫頁面的一般 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 引擎程式碼之外，修改資料庫頁面。  將況可能是：  

- 在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理序中執行的執行緒，在資料庫頁面上不正確的寫入。 這通常稱為「塗鴉者」
- 硬體或作業系統問題是支援資料庫頁面的記憶體不正確地修改或已損毀  

當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 偵測到此行為時，就會引發錯誤 832。

## <a name="user-action"></a>使用者動作

若要找出錯誤的原因，請考慮下列選項：

- 您應該執行任何一般硬體或系統檢查，以判斷是否存在記憶體、CPU 或其他硬體相關問題。 請確定所有系統驅動程式、作業系統更新及硬體更新已套用至我們的系統。 請考慮執行任何硬體製造商診斷，包括記憶體相關測試。
- 評估可能在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中載入了哪些會造成此問題的「外部」DLL，包括擴充預存程序、COM 物件或其他 DLL，而這些 DLL 可能會不正確地修改 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 保留給資料庫頁面的記憶體。  

每當看到此錯誤時，應該立即考慮針對錯誤訊息中 \<dbid> 所參考的資料庫執行 `DBCC CHECKDB`。

## <a name="more-information"></a>詳細資訊

背景工作 (通常稱為 LazyWriter) 會偵測到此錯誤。 (此工作的「命令」會視為 LAZY WRITER)。 因此，此錯誤不會傳回給用戶端應用程式。 錯誤將會以 EventID = 832 的形式寫入 Windows 應用程式事件記錄檔中。  

只會檢查目前未在快取中修改 (或「已變更」) 的頁面。 這就是為什麼訊息使用「固定」一詞的原因，因為該頁面從磁碟讀入後從未變更過。 此外，該頁面會從磁碟「乾淨」讀入，因為其在頁面上有總和檢查碼值，且尚未發生總和檢查碼失敗 (訊息 824)。 不過，該頁面可能會在這個錯誤之後的某個時間點進行修改，再以不正確的修改寫入磁碟中。 如果發生此錯誤，則會根據所有修改來計算新的總和檢查碼，再寫入磁碟。 因此，該頁面在磁碟上可能已損毀，但後續從磁碟讀取可能不會觸發總和檢查碼失敗。 請務必在此錯誤所參考的任何資料庫上執行 `DBCC CHECKDB`。  

即使 `DBCC CHECKDB` 在寫入磁碟之後，也可能不會針對處於此狀態的頁面報告錯誤。 這是因為可能會在未保存任何資料的頁面上，也未包含任何重要頁面或資料列結構資訊的位置上不正確的修改，或者可能是 CHECKDB 無法偵測到的資料修改。  

如需訊息 832 的詳細資料和詳細資訊，另請參閱 [SQL Server I/O 基本概念第 2 章](/previous-versions/sql/sql-server-2005/administrator/cc917726(v=technet.10)) (英文) 白皮書。