---
description: 將 AUTO_SHRINK 資料庫選項設定為 OFF
title: 將 AUTO_SHRINK 資料庫選項設定為 OFF | Microsoft Docs
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- Best Practices [Database Engine]
ms.assetid: 16403850-d745-4754-b84f-5f01aaecd24e
author: VanMSFT
ms.author: vanto
ms.openlocfilehash: 9a8815d5958ca8cb2aabb498636c631eb91744d8
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596745"
---
# <a name="set-the-auto_shrink-database-option-to-off"></a>將 AUTO_SHRINK 資料庫選項設定為 OFF
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  這個規則會檢查 AUTO_SHRINK 資料庫選項是否設定為 OFF。 通常壓縮和展開資料庫可能會導致實體片段。  
  
## <a name="best-practices-recommendations"></a>最佳做法建議  
 將 AUTO_SHRINK 資料庫選項設定為 OFF。 如果您知道您所回收的空間不需要在將來使用，您可以手動壓縮資料庫來回收該空間。  
  
## <a name="for-more-information"></a>詳細資訊  
 Microsoft 知識庫文件 [315512](/troubleshoot/sql/admin/considerations-autogrow-autoshrink)  
  
## <a name="see-also"></a>另請參閱  
 [使用原則式管理來監視和強制最佳做法](../../relational-databases/policy-based-management/monitor-and-enforce-best-practices-by-using-policy-based-management.md)  
  
