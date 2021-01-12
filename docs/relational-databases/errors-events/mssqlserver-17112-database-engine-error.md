---
description: MSSQLSERVER_17112
title: MSSQLSERVER_17112
ms.custom: ''
ms.date: 08/20/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, Masha
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 17112 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: 8b9d79dc7b23339b5b152695daf5efc5c47847a4
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099343"
---
# <a name="mssqlserver_17112"></a>MSSQLSERVER_17112
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|17112|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|INIT_INVCOMMAND|
|訊息文字|從登錄或命令提示字元提供了無效的啟動選項 a。 請更正或移除此選項。|
||

## <a name="explanation"></a>說明

此錯誤表示指定的 [Database Engine 服務啟動選項](../../database-engine/configure-windows/database-engine-service-startup-options.md)無效。 如果未正確指定啟動選項，SQL Server 可能無法啟動，或無法如預期般執行。 此狀況也會引發錯誤 17112。

儘管在某些情況下，仍可能會啟動執行個體，但當檢查 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔時，啟動參數看起來不正確：

> \<Datetime> 伺服器登錄啟動參數：  
\<Datetime> 伺服器 -d D:\Program Files\Microsoft SQL Server\MSSQL.1\MSSQL\DATA\master.mdf  
\<Datetime> 伺服器 -e D:\Program Files\Microsoft SQL Server\MSSQL.1\MSSQL\LOG\ERRORLOG  
\<Datetime> 伺服器 -l D:\Program Files\Microsoft SQL Server\MSSQL.1\MSSQL\DATA\mastlog.ldf  
\<Datetime> 伺服器 -T1118 -g512

請注意，最後兩個啟動參數位於同一行。

在某些情況下，您可能也會注意到，新增必要的啟動參數並未對伺服器行為產生預期效果。

## <a name="possible-causes"></a>可能的原因

您會因為下列原因而遇到這些問題：

- 未使用有效啟動參數清單中的啟動參數
- 未使用適當分隔符號 [;] 指定啟動參數
- 從包含某些隱藏特殊字元的文字編輯器中，複製並貼上啟動參數 (例如，在 -T 之前的空格)
- 啟動參數未使用正確的大小寫

## <a name="user-action"></a>使用者動作

使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Configuration Manager 工具來提供並驗證為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體所指定的啟動參數。 確認每個啟動參數都已正確分隔，且未存在任何特殊字元。

## <a name="more-information"></a>詳細資訊

如需本主題的詳細資訊，請參閱下列主題：

- [Database Engine 服務啟動選項](../../database-engine/configure-windows/database-engine-service-startup-options.md)
- [SCM 服務 - 設定伺服器啟動選項](../../database-engine/configure-windows/scm-services-configure-server-startup-options.md)