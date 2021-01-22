---
description: MSSQLSERVER_15581
title: MSSQLSERVER_15581
ms.custom: ''
ms.date: 09/03/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, Masha, VenCher
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 15581 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: 018252ff40ebf2ea7bd52b34f03ed25ef124a8d7
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596316"
---
# <a name="mssqlserver_15581"></a>MSSQLSERVER_15581
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|15581|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|SEC_NODBMASTERKEYERR|
|訊息文字|執行此作業之前，請在資料庫中建立主要金鑰或在工作階段中開啟主要金鑰。|
||

## <a name="explanation"></a>說明

當 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 無法復原已啟用透明資料加密 (TDE) 的資料庫時，就會引發錯誤 15581。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔中會記錄類似下列的錯誤訊息

> 2020-01-14 22:16:26.47 spid20s Error:15581, Severity:16，狀態：3.  
2020-01-14 22:16:26.47 spid20s Please create a master key in the database or open the master key in the session before performing this operation.

## <a name="possible-cause"></a>可能的原因

在執行下列命令時，如果移除 master 資料庫中資料庫主要金鑰的服務主要金鑰加密，就會發生此問題：

```sql
Use master
go
alter master key drop encryption by service master key
```

服務主要金鑰是用於加密資料庫主要金鑰所使用的憑證。 嘗試使用已啟用 TDE 的資料庫時，需要存取 master 資料庫中的資料庫主要金鑰。 不是由服務主要金鑰加密的主要金鑰，必須在需要存取主要金鑰的每個工作階段上，使用 [OPEN MASTER KEY (Transact-SQL)](../../t-sql/statements/open-master-key-transact-sql.md) 陳述式以及密碼開啟。 因為此命令無法在系統工作階段上執行，所以無法在已啟用 TDE 的資料庫上完成復原。

## <a name="user-action"></a>使用者動作

若要解決此問題，請啟用主要金鑰的自動解密。 若要這樣做，請執行下列命令：

```sql
Use master
go
open master key DECRYPTION BY PASSWORD = 'password'
alter master key add encryption by service master key
```

使用下列查詢，以判斷是否已針對 master 資料庫停用服務主要金鑰的主要金鑰自動解密：

```sql
select is_master_key_encrypted_by_server from sys.databases where name = 'master'
```

如果此查詢傳回值為 0，則會停用服務主要金鑰的主要金鑰自動解密。

## <a name="more-information"></a>詳細資訊

在某些情況下，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體可能會沒有回應。 如果查詢 `sys.dm_exec_requests` 動態管理檢視，您會注意到 `LogWriter` 執行緒和其他執行 DML 作業的執行緒會以 WRITELOG wait_type 無限期地等候。 其他工作階段也可能會在嘗試取得鎖定時等候。