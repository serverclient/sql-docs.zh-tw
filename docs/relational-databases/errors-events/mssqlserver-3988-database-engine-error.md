---
description: MSSQLSERVER_3988
title: MSSQLSERVER_3988
ms.custom: ''
ms.date: 12/25/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, vencher, tejasaks, docast
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 3988 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: 46070023b397f949971cf35a7c6ced936fc91e6d
ms.sourcegitcommit: d819173fb91af6f20ca6ee59686c35c71b060fbc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797776"
---
# <a name="mssqlserver_3988"></a>MSSQLSERVER_3988
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|3988|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|XACT_UNSUPPORT_PARALLEL_TRAN2|
|訊息文字|不允許進行新交易，因為有其他執行緒正在工作階段中執行。|
||

## <a name="explanation"></a>說明

當您執行分散式查詢以聯結 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 遠端執行個體所裝載的多個資料表，同時將 `XACT_ABORT` 工作階段設定為 **ON** 時，就會發生此錯誤。 使用者會收到類似下列的錯誤訊息回報：

> 訊息 3988，層級 16，狀態 1，行號  
不允許進行新交易，因為有其他執行緒正在工作階段中執行。

## <a name="cause"></a>原因

當下列條件成立時，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 處理分散式查詢 (DQ) 的方式有一些設計限制：

- SQL Server 會聯結一個遠端 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料來源的多個資料表。
- 發出查詢的工作階段並未在分散式交易中登錄。

在這種情況下，嘗試執行查詢可能會引發 **說明** 一節中所述的兩個錯誤之一。

## <a name="user-action"></a>使用者動作

若要因應此問題，請將分散式查詢括在 'begin distributed transaction' 陳述式中：

```sql
BEGIN DISTRIBUTED TRANSACTION <Distributed Query> COMMIT TRANSACTION
```
