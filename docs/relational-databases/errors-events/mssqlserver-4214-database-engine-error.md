---
description: MSSQLSERVER_
title: MSSQLSERVER_4214
ms.custom: ''
ms.date: 08/20/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, Masha
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 4214 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: b448b97f5f6eeaf403b0c5c218edb8cd7dd7f8ca
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101847"
---
# <a name="mssqlserver_4214"></a>MSSQLSERVER_4214
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|4214|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|DMPX_NODBBACKUP|
|訊息文字|無法執行 BACKUP LOG，因為沒有目前的資料庫備份|
||

## <a name="explanation"></a>說明

當在執行資料庫的完整備份之前嘗試交易記錄備份時，就會引發此錯誤。 在嘗試備份資料庫的交易記錄之前，必須先執行完整資料庫備份。 此外，您會在錯誤記錄檔中收到下列訊息：

> \<Datetime> 備份   錯誤：3041，嚴重性：16，狀態：1。  
\<Datetime>  備份     BACKUP 無法完成命令 BACKUP LOG \<db_name>。 詳細訊息請檢查備份應用程式記錄檔。

## <a name="user-action"></a>使用者動作

在嘗試備份資料庫的交易記錄之前，請先執行完整資料庫備份。

## <a name="more-information"></a>詳細資訊

如需詳細資訊，請參閱：[SQL Server 資料庫的備份與還原](../backup-restore/back-up-and-restore-of-sql-server-databases.md)。