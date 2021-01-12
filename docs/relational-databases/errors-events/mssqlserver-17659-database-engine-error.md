---
description: MSSQLSERVER_17659
title: MSSQLSERVER_17659
ms.custom: ''
ms.date: 08/20/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, Masha
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 17659 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: b906310eb23bd1f9416cd792d95adfa79f72302c
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099350"
---
# <a name="mssqlserver_17659"></a>MSSQLSERVER_17659
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|17659|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|DEMO_SYSCATUPDATE|
|訊息文字|已直接在資料庫識別碼 \%d 更新系統資料表識別碼 \%d，且可能未能保持快取的連貫性。 <br/> 應該重新啟動 SQL Server。|
||

## <a name="explanation"></a>說明

此錯誤表示已直接更新系統物件。 不支援手動更新系統資料表。 系統資料表應僅由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫引擎進行更新。 當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 偵測到使用者對系統資料表進行變更時，就會引發錯誤 17659。 與下列情況類似的事件會記錄在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔中，在此案例中，則會記錄在事件檢視器的應用程式記錄檔中。

> 記錄名稱：Application  
來源：MSSQLServer  
事件識別碼：17659  
工作類別：伺服器  
等級：資訊  
描述：警告：已直接在資料庫識別碼 %d 更新系統資料表識別碼 \%d，且可能未能保持快取的連貫性。 應該重新啟動 SQL Server。

## <a name="user-action"></a>使用者動作

若要解決這個問題，請使用下列其中一個方法。

- 方法 1  
    若有資料庫的乾淨備份，請使用該備份還原資料庫。  
    > [!NOTE]
    > 只有當備份的中繼資料沒有任何不一致時，才適用此方法。  

- 方法 2  
    如果無法從備份還原資料庫，請將資料與物件匯出至新的資料庫。 然後，將手動更新資料庫的內容傳送到新資料庫。 請注意，您無法使用 DBCC CHECKDB 命令其 REPAIR 選項來修復系統目錄中的不一致。 因此，因為命令無法修復中繼資料損毀，所以命令不會提供任何建議的修復層級。

> [!NOTE]
> 您可透過系統目錄檢視來查看系統資料表中的資料。

## <a name="more-information"></a>詳細資訊

如需詳細資訊，請參閱：[系統基底資料表](../system-tables/system-base-tables.md)。