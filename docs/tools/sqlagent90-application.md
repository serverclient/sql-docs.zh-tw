---
title: sqlagent90 應用程式
description: sqlagent90 應用程式可從命令提示字元啟動 SQL Server Agent。 在診斷 SQL Server Agent 或收到您的支援提供者的指示時，請加以使用。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: sql-tools
ms.reviewer: ''
ms.technology: tools-other
ms.topic: conceptual
helpviewer_keywords:
- starting SQL Server Agent
- sqlagent90 application
- SQL Server Agent, starting
- command prompt utilities [SQL Server], sqlagent90
ms.assetid: e8b80e8d-d0c9-4500-a868-0ce08233da08
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 63f185f686d21c5c9a490bee83d2c9afaf449b8f
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641884"
---
# <a name="sqlagent90-application"></a>sqlagent90 應用程式
[!INCLUDE[sqlserver](../includes/applies-to-version/sqlserver.md)]
  **sqlagent90** 應用程式可從命令提示字元啟動 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Agent。 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Agent 通常應該從 [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] 執行，或利用應用程式中的 SQL-SMO 方法來執行。 請只在診斷 **Agent 時，或您的主要支援提供者指示您這麼做時，才從命令提示字元執行** sqlagent90 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 。  
  
## <a name="syntax"></a>語法  
  
```  
  
sqlagent90  
-c [-v] [-i instance_name]  
```  
  
## <a name="arguments"></a>引數  
 **-c**  
 指出 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Agent 是在命令提示字元之下執行，與 Microsoft Windows 服務控制管理員無關。 當使用 **-c** 時， [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Agent 無法利用 [系統管理工具] 中的 [服務] 應用程式來控制，也無法利用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 組態管理員來控制。 這是強制引數。  
  
 **-v**  
 指出 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Agent 執行詳細模式，且將診斷資訊寫入命令提示字元視窗中。 診斷資訊與寫入 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Agent 錯誤記錄的資訊相同。  
  
 **-i** *instance_name*  
 指出 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Agent 連接到 <執行個體名稱> 所指定的具名 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 執行個體。  
  
## <a name="remarks"></a>備註  
 顯示著作權訊息之後，只有在指定了 **-v** 參數的情況下， **sqlagent90** 才會在命令提示字元視窗中顯示輸出。 若要停止 **sqlagent90**，請在命令提示字元按 CTRL+C。 在停止 **sqlagent90** 之前，請勿關閉命令提示字元視窗。  
  
## <a name="see-also"></a>另請參閱  
 [自動化管理工作 &#40;SQL Server Agent&#41;](../ssms/agent/automated-administration-tasks-sql-server-agent.md)  
  
