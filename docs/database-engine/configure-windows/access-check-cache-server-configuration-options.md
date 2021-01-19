---
title: access check cache 伺服器組態選項 | Microsoft Docs
description: 了解存取檢查結果快取，以及控制快取行為的選項。 查看在 SQL Server 中變更這些選項的時機。
ms.custom: ''
ms.date: 03/02/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: configuration
ms.topic: conceptual
helpviewer_keywords:
- access check cache option
- access check cache bucket count
- access check cache quota
ms.assetid: 0a992ea8-3ec6-4a4d-97b5-460ae7326247
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 893400411c6f3d2faf6313923aa0f6127a3f2cf0
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171070"
---
# <a name="access-check-cache-server-configuration-options"></a>存取檢查快取伺服器組態選項
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

當資料庫物件是由 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]所存取時，存取檢查會在稱為 **access check result cache** 的內部結構中進行快取。 
  
[存取檢查快取 Bucket 計數] 選項可控制用於存取檢查結果快取的雜湊貯體數目。 

[存取檢查快取配額] 選項可控制儲存在存取檢查結果快取中的項目數。 達到最大項目數時，會從存取檢查結果快取中移除最舊的項目。
  
預設值 0 表示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 正在管理這些選項。 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，預設值會轉譯為下列內部組態：
-   針對存取檢查快取 Bucket 計數，值 0 會設定預設值 256 個貯體。
-   針對存取檢查快取配額，值 0 會設定預設值 1,024 個項目。

在罕見的情況下，可以變更這些選項來提升效能。 例如，如果使用太多記憶體，則建議減少存取檢查結果快取的大小。 或者，如果在重新計算權限時出現高 CPU 使用量，則建議增加存取檢查結果快取的大小。
 
> [!IMPORTANT]
> Microsoft 建議只要變更 Microsoft 客戶支援服務所導向的那些選項。 如果必須變更 [存取檢查快取 Bucket 計數] 和 [存取檢查快取配額] 的值，請使用 1:4 的比率。 例如，如果將 [存取檢查快取 Bucket 計數] 的值變更為 512，則應將 [存取檢查快取配額] 的值變更為 2,048。 
  
## <a name="see-also"></a>另請參閱  
 [伺服器組態選項 &#40;SQL Server&#41;](../../database-engine/configure-windows/server-configuration-options-sql-server.md)   
 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)  
  
  
