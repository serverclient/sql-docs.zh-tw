---
description: MSSQLSERVER_3859
title: MSSQLSERVER_3859
ms.custom: ''
ms.date: 08/20/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, Masha
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 3859 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: 3228a21b43fd5b5ed2a23c5e13b47353e55cd028
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98093266"
---
# <a name="mssqlserver_3859"></a>MSSQLSERVER_3859
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|3859|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|DBCC_CHECKCAT_DIRECT_UPDATE|
|訊息文字|警告：已直接在資料庫識別碼 \%d 中更新系統目錄，最近一次在 %S_DATE。|
||

## <a name="explanation"></a>說明

此錯誤表示使用者對系統資料表進行變更。 不支援手動更新系統資料表。 系統資料表應僅由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫引擎進行更新。 當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 偵測到使用者對系統資料表進行變更時，就會在下列兩個案例中引發錯誤 3859：

- 實例 1

    當手動更新系統資料表，並啟動包含該系統資料表的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫時，與下列情況類似的事件就會記錄在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔中，或記錄在事件檢視器的應用程式記錄檔中：

    > 記錄名稱：Application  
    來源：MSSQLSERVER 事件識別碼：3859  
    工作類別：伺服器  
    等級：資訊  
    描述：警告：已直接在資料庫識別碼 \%d 中更新系統目錄，最近一次在 **date_time**  

- 案例 2  

    在手動更新系統資料表後執行 `DBCC_CHECKDB` 命令時，即會傳回以下警告訊息：

    > '**database_name**' 的 DBCC 結果。  
    訊息 8992，層級 16，狀態 1，第 1 行  
    檢查目錄訊息 3859，狀態 1：警告：已直接在資料庫識別碼 \%d 中更新系統目錄，最近一次在 **date_time**。  
    CHECKDB 在資料庫 '**db_name**' 中發現了 0 個配置錯誤與 0 個一致性錯誤。  
    DBCC 的執行已經完成。 如果 DBCC 印出錯誤訊息，請連絡您的系統管理員。

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