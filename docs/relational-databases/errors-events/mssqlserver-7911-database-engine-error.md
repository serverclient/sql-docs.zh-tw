---
description: MSSQLSERVER_7911
title: MSSQLSERVER_7911 | Microsoft Docs
ms.custom: ''
ms.date: 04/04/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 7911 (Database Engine error)
ms.assetid: dd8390f3-0f77-4fb2-ba94-631a56e42bc6
author: MashaMSFT
ms.author: mathoma
robots: noindex,nofollow
ms.openlocfilehash: 57258696ea1d8051817a1e0f99aa089c4c60ad83
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88470822"
---
# <a name="mssqlserver_7911"></a>MSSQLSERVER_7911
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  
## <a name="details"></a>詳細資料  
  
| 屬性 | 值 |  
| :-------- | :---- |  
|產品名稱|SQL Server|  
|事件識別碼|7911|  
|事件來源|MSSQLSERVER|  
|元件|SQLEngine|  
|符號名稱|DBCC2_REPAIR_PAGE_DEALLOCATED|  
|訊息文字|修復:頁面 P_ID 已經從物件識別碼 O_ID，索引識別碼 I_ID，分割區識別碼 PN_ID，配置單位識別碼 A_ID (類型 TYPE) 解除配置。|  
  
## <a name="explanation"></a>說明  
這是 REPAIR 傳回的參考用訊息，說明頁面已經從索引配置對應 (IAM) 頁面的單頁位置陣列取消配置。  
  
## <a name="user-action"></a>使用者動作  
None  
  
