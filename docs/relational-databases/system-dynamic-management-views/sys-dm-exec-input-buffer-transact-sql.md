---
description: 'sys.dm_exec_input_buffer (Transact-sql) '
title: sys.dm_exec_input_buffer (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 11/14/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_exec_input_buffer
- sys.dm_exec_input_buffer _tsql
- dm_exec_input_buffer
- dm_exec_input_buffer_tsql
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_exec_input_buffer dynamic management function
ms.assetid: fb34a560-bde9-4ad9-aa96-0d4baa4fc104
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: e6b15da477c8fb5a4eb8cdc7ef302ebd8a41fae6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099735"
---
# <a name="sysdm_exec_input_buffer-transact-sql"></a>sys.dm_exec_input_buffer (Transact-sql) 

[!INCLUDE[tsql-appliesto-2014sp2-asdb-xxxx-xxx-md](../../includes/tsql-appliesto-2014sp2-asdb-xxxx-xxx-md.md)]

傳回已提交至實例之語句的相關資訊 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。

## <a name="syntax"></a>語法

```
sys.dm_exec_input_buffer ( session_id , request_id )
```

## <a name="arguments"></a>引數

*session_id* 這是執行要查閱之批次的會話識別碼。 *session_id* 為 **Smallint**。 您可以從下列動態管理物件中取得 *session_id* ：

- [sys.dm_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)
- [sys.dm_exec_sessions](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)
- [sys.dm_exec_connections](../../relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql.md)

*request_id*[Sys.dm_exec_requests](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)的 request_id。 *request_id* 為 **int**。

## <a name="table-returned"></a>傳回的資料表

|資料行名稱|資料類型|描述|
|-----------------|---------------|-----------------|
|**event_type**|**nvarchar(256)**|給定 spid 之輸入緩衝區中的事件種類。|
|**parameters**|**smallint**|針對語句提供的任何參數。|
|**event_info**|**nvarchar(max)**|給定 spid 之輸入緩衝區中的語句文字。|

## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，如果使用者有 VIEW SERVER STATE 許可權，使用者將會在實例上看到所有執行中的會話 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ; 否則，使用者只會看到目前的會話。

> [!IMPORTANT]
> 在 SQL Server Management Studio 以外的 SQL Server 執行此 DMV，而不需要 VIEW SERVER STATE 許可權 (例如，在觸發程式、預存程式或函數) 中，會在 master 資料庫上擲回許可權錯誤。

在上 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] ，如果使用者是資料庫擁有者，使用者將會在上看到所有執行中的會話 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] ; 否則，使用者只會看到目前的會話。

> [!IMPORTANT]
> 針對沒有擁有者許可權的 Azure SQL Database 在 SQL Server Management Studio 之外執行此 DMV (例如，在觸發程式、預存程式或函數) 中，會在 master 資料庫上擲回許可權錯誤。

## <a name="remarks"></a>備註

這項動態管理函式可搭配 sys.dm_exec_sessions 或 sys.dm_exec_requests，藉由執行 **CROSS APPLY** 來使用。

## <a name="examples"></a>範例

### <a name="a-simple-example"></a>A. 簡單範例

下列範例示範如何將會話識別碼 (SPID) 和要求識別碼傳遞至函式。

```sql
SELECT * FROM sys.dm_exec_input_buffer (52, 0);
GO
```

### <a name="b-using-cross-apply-to-additional-information"></a>B. 使用 cross apply 的其他資訊

下列範例會列出會話識別碼大於50之會話的輸入緩衝區。

```sql
SELECT es.session_id, ib.event_info
FROM sys.dm_exec_sessions AS es
CROSS APPLY sys.dm_exec_input_buffer(es.session_id, NULL) AS ib
WHERE es.session_id > 50;
GO
```

## <a name="see-also"></a>另請參閱

- [執行相關的動態管理檢視和函式 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/execution-related-dynamic-management-views-and-functions-transact-sql.md)
- [sys.dm_exec_sessions &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql.md)
- [sys.dm_exec_requests &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql.md)
- [DBCC INPUTBUFFER &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-inputbuffer-transact-sql.md)
