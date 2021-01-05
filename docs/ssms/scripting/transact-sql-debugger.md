---
title: Transact-SQL 偵錯工具
description: 了解如何使用 Transact-SQL 偵錯工具來逐步執行程式碼並檢視執行狀態，以及如何執行其他各種偵錯工作。
titleSuffix: T-SQL debugger
ms.prod: sql
ms.technology: ssms
ms.topic: conceptual
helpviewer_keywords:
- Transact-SQL debugger, introduction
ms.assetid: 6e914699-0d85-46c2-aa2d-3e339ac2c4ce
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.custom: seo-lt-2019
ms.date: 07/22/2020
monikerRange: '>= sql-server-2016'
ms.openlocfilehash: 0a62b9bae20ec9f45d73c2a891b50dda746d568d
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642294"
---
# <a name="transact-sql-debugger"></a>Transact-SQL 偵錯工具

 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

[!INCLUDE[tsql](../../includes/tsql-md.md)] 偵錯工具可協助您透過調查 [!INCLUDE[tsql](../../includes/tsql-md.md)] 程式碼的執行階段行為，找出程式碼中的錯誤。 在您將 [ [!INCLUDE[ssDE](../../includes/ssde-md.md)] 查詢編輯器] 視窗設定成偵錯模式之後，就可以在特定的程式碼行上暫停執行作業，然後檢查這些 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式所使用或傳回的資訊和資料。

[!INCLUDE[ssms-old-versions](../../includes/ssms-old-versions.md)]

## <a name="stepping-through-transact-sql-code"></a>逐步執行 Transact-SQL 程式碼

[!INCLUDE[tsql](../../includes/tsql-md.md)] 偵錯工具提供下列選項，可讓您在 [ [!INCLUDE[tsql](../../includes/tsql-md.md)] 查詢編輯器] 視窗處於偵錯模式時，逐一巡覽 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 程式碼：

- 在個別的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式上設定中斷點。

    中斷點是指您要暫停執行以便檢查資料的點。 當您啟動偵錯工具時，它會在 [查詢編輯器] 視窗的第一行程式碼上暫停。 若要執行到您已設定的第一個中斷點，可以使用 [繼續] 功能。 您也可以使用 [繼續] 功能，從視窗目前暫停的任何位置執行到下一個中斷點。 您可以編輯中斷點以指定動作，例如中斷點應暫停執行的條件、指向 [輸出] 視窗的資訊，以及變更中斷點的位置。  

- 逐步執行下一個陳述式。  

    這個選項可讓您逐一導覽一組陳述式，以及在進行的過程中觀察其行為。  

- 逐步執行或不進入預存程序或函數的呼叫。  

    如果您確定預存程序沒有任何錯誤，就可以不進入此預存程序。 此程序會以完整模式執行，而且結果會傳回程式碼。  

    如果您想要偵錯預存程序或函數，則可以逐步執行模組。 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 會開啟新的 [ [!INCLUDE[ssDE](../../includes/ssde-md.md)] 查詢編輯器] 視窗 (填入模組的原始程式碼)，並讓此視窗進入偵錯模式，然後暫停執行模組的第一個陳述式。 接著，您就可以透過設定中斷點或逐步執行程式碼，逐一導覽模組程式碼。  

如需偵錯工具如何讓您巡覽程式碼的詳細資訊，請參閱 [逐步執行 Transact-SQL 程式碼](./step-through-transact-sql-code.md)。  

## <a name="viewing-debugger-information"></a>檢視偵錯工具資訊

每當偵錯工具在特定的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式上暫停執行作業時，您就可以使用下列偵錯工具視窗來檢視目前的執行狀態：  

- [本機] 和 [監看式] **。** 這些視窗會顯示目前配置的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 運算式。 運算式是評估成單一純量運算式的 [!INCLUDE[tsql](../../includes/tsql-md.md)] 子句。 [!INCLUDE[tsql](../../includes/tsql-md.md)] 偵錯工具支援檢視參考 [!INCLUDE[tsql](../../includes/tsql-md.md)] 變數、參數或內建函式 (名稱以 @@ 為開頭) 的運算式。 這些視窗也會顯示目前指派給運算式的資料值。  

- **[快速監看式]。** 這個視窗會顯示 [!INCLUDE[tsql](../../includes/tsql-md.md)] 運算式的值，而且可讓您將該運算式儲存至 [監看式] 視窗。  

- **[中斷點]。** 這個視窗會顯示目前已設定的中斷點，而且可讓您管理它們。  

- **[呼叫堆疊]。** 這個視窗會顯示目前的執行位置。 此外，它也會提供有關執行作業如何從原始 [查詢編輯器] 視窗通過任何函數、預存程序或觸發程序而到達目前執行位置的資訊。  

- **[輸出]。** 這個視窗會顯示各種訊息和程式資料，例如偵錯工具的系統訊息。  

- [結果] 和 [訊息]。 [查詢編輯器] 視窗上的這些索引標籤會顯示先前執行之 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式的結果。  

## <a name="transact-sql-debugger-tasks"></a>Transact-SQL 偵錯工具工作  

|工作描述|主題|  
|----------------------|-----------|  
|描述如何設定 [!INCLUDE[tsql](../../includes/tsql-md.md)] 偵錯工具進行遠端偵錯。|[先設定防火牆規則再執行 TSQL 偵錯工具](./configure-firewall-rules-before-running-the-tsql-debugger.md)|  
|描述如何啟動、停止和控制偵錯工具的作業。|[執行 Transact-SQL 偵錯工具](./run-the-transact-sql-debugger.md)|  
|描述如何使用 [!INCLUDE[tsql](../../includes/tsql-md.md)] 偵錯工具來逐步執行程式碼。|[逐步執行 Transact-SQL 程式碼](./step-through-transact-sql-code.md)|  
|描述如何使用偵錯工具來檢視 [!INCLUDE[tsql](../../includes/tsql-md.md)] 資料 (例如參數和變數) 以及系統資訊。|[Transact-SQL 偵錯工具資訊](./transact-sql-debugger-information.md)|  

## <a name="see-also"></a>另請參閱

[查詢與文字編輯器 &#40;SQL Server Management Studio&#41;](../f1-help/database-engine-query-editor-sql-server-management-studio.md)