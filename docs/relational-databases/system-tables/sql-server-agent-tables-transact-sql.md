---
description: SQL Server Agent 資料表 (Transact-SQL)
title: SQL Server Agent 資料表 (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- SQL Server Agent, system tables
- system tables [SQL Server], SQL Server Agent
ms.assetid: 6cb39bfd-079e-4be4-9c42-2fa234c65ce1
author: cawrites
ms.author: chadam
ms.openlocfilehash: 3cf53dd13bd94c9632c4f3c2464bda38d50e3742
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98096138"
---
# <a name="sql-server-agent-tables-transact-sql"></a>SQL Server Agent 資料表 (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  此章節的主題描述儲存 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 所用之資訊的系統資料表。 所有資料表都在 msdb 資料庫的 dbo 結構描述中。  
  
## <a name="in-this-section"></a>本節內容  
 [dbo.sysalerts](../../relational-databases/system-tables/dbo-sysalerts-transact-sql.md)  
 針對每個警示各包含一個資料列。  
  
 [dbo.syscategories](../../relational-databases/system-tables/dbo-syscategories-transact-sql.md)  
 包含 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 用來組織作業、警示和運算子的類別目錄。  
  
 [dbo.sysdownloadlist](../../relational-databases/system-tables/dbo-sysdownloadlist-transact-sql.md)  
 存放所有目標伺服器的下載指示佇列。  
  
 [dbo.sysjobactivity](../../relational-databases/system-tables/dbo-sysjobactivity-transact-sql.md)  
 包含目前 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 作業的活動和狀態的相關資訊。  
  
 [dbo.sysjobhistory](../../relational-databases/system-tables/dbo-sysjobhistory-transact-sql.md)  
 包含 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 執行排程作業的相關資訊。  
  
 [dbo.sysjobs](../../relational-databases/system-tables/dbo-sysjobs-transact-sql.md)  
 儲存 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 所要執行之各項排程作業的資訊。  
  
 [dbo.sysjobschedules](../../relational-databases/system-tables/dbo-sysjobschedules-transact-sql.md)  
 包含 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 要執行之作業的排程資訊。  
  
 [dbo.sysjobservers](../../relational-databases/system-tables/dbo-sysjobservers-transact-sql.md)  
 儲存特定作業與一部或多部目標伺服器的關聯或關係。  
  
 [dbo.sysjobsteps](../../relational-databases/system-tables/dbo-sysjobsteps-transact-sql.md)  
 包含 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 所要執行之作業中各個步驟的資訊。  
  
 [dbo.sysjobstepslogs](../../relational-databases/system-tables/dbo-sysjobstepslogs-transact-sql.md)  
 包含作業步驟記錄的相關資訊。  
  
 [dbo.sysnotifications](../../relational-databases/system-tables/dbo-sysnotifications-transact-sql.md)  
 針對每項通知，各包含一個資料列。  
  
 [dbo.sysoperators](../../relational-databases/system-tables/dbo-sysoperators-transact-sql.md)  
 針對每個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 操作員，各包含一個資料列。  
  
 [dbo.sysproxies](../../relational-databases/system-tables/dbo-sysproxies-transact-sql.md)  
 包含 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent Proxy 帳戶的相關資訊。  
  
 [dbo.sysproxylogin](../../relational-databases/system-tables/dbo-sysproxylogin-transact-sql.md)  
 記錄每個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent Proxy 帳戶的相關 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 登入。  
  
 [dbo.sysproxysubsystem](../../relational-databases/system-tables/dbo-sysproxysubsystem-transact-sql.md)  
 記錄每個 Proxy 帳戶使用哪個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 子系統。  
  
 [dbo.sysschedules](../../relational-databases/system-tables/dbo-sysschedules-transact-sql.md)  
 包含 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 作業排程的相關資訊。  
  
 [dbo.syssessions](../../relational-databases/system-tables/dbo-syssessions-transact-sql.md)  
 包含每個 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 工作階段的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 開始日期。 每次啟動 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent 服務時，都會建立一個工作階段。  
  
 [dbo.syssubsystems](../../relational-databases/system-tables/dbo-sysproxysubsystem-transact-sql.md)  
 包含所有可用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Agent Proxy 子系統的相關資訊。  
  
 [dbo.systargetservergroupmembers](../../relational-databases/system-tables/dbo-systargetservergroupmembers-transact-sql.md)  
 記錄在這個多伺服器群組中，目前編列了哪些目標伺服器。  
  
 [dbo.systargetservergroups](../../relational-databases/system-tables/dbo-systargetservergroups-transact-sql.md)  
 記錄在這個多伺服器環境中，目前編列了哪些目標伺服器群組。  
  
 [dbo.systargetservers](../../relational-databases/system-tables/dbo-systargetservers-transact-sql.md)  
 記錄在這個多伺服器的作業網域中，目前編列了哪些目標伺服器。  
  
 [dbo.systaskids](../../relational-databases/system-tables/dbo-systaskids-transact-sql.md)  
 包含舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中所建立的工作與目前版本中 [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] 作業的對應。  
  
  
