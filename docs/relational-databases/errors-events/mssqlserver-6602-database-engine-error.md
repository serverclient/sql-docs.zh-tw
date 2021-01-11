---
description: MSSQLSERVER_6602
title: MSSQLSERVER_6602
ms.custom: ''
ms.date: 12/25/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, vencher, tejasaks, docast
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 6602 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: dc40432af6994b184678414efba4dcd098dc400b
ms.sourcegitcommit: d819173fb91af6f20ca6ee59686c35c71b060fbc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797747"
---
# <a name="mssqlserver_6602"></a>MSSQLSERVER_6602
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|6602|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|XMLERR_PARSEERR2|
|訊息文字|錯誤描述為 '%.*ls'。|
||

## <a name="explanation"></a>說明

當您嘗試在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中執行 `sp_xml_preparedocument` 預存程序時，如果 `xmltext` 參數的內容為複雜的 XML 文件，就會發生此錯誤，而使用者會收到類似下列的錯誤訊息回報

> 行號 1，接近 XML 文字 "\<XML document sample>" 之處發生 XML 剖析錯誤 0x80004005  
訊息 6602，層級 16，狀態 2，程序 sp_xml_preparedocument，第 1 行  
錯誤描述為「未指定的錯誤」。

## <a name="cause"></a>原因

此問題發生的原因是 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 使用的 MSXML 剖析器 (Msxmlsql.dll) 的設計限制。

此問題並不完全與 XML 文件的大小有關，而是與其複雜的結構有關。 XML 元素的結構深度、屬性的數目和大小，以及屬性實體數目的組合，可能會造成此問題。 不過，您可以在數 MB 的 XML 文件中找到達到此限制所需的複雜度層級。

## <a name="user-action"></a>使用者動作

若要因應此問題，請嘗試降低 XML 文件的複雜度。

> [!NOTE]
> 請注意包含許多 XML \ 實體的超大單一字串屬性。
