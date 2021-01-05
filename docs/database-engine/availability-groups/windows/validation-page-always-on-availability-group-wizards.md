---
title: 可用性群組精靈：驗證頁面
description: 此主題描述可在 [Always On 可用性群組精靈] 的 [驗證] 頁面上找到的選項。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.addreplicawizard.validation.f1
- sql13.swb.adddatabasewizard.validation.f1
- sql13.swb.newagwizard.validation.f1
helpviewer_keywords:
- ', listeners'
ms.assetid: c8971556-240c-491a-bc86-9cc72f71a3dd
author: cawrites
ms.author: chadam
ms.openlocfilehash: 59c65191c2d69d3ebb7b144a6b254f1695fd5b5b
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641713"
---
# <a name="validation-page-always-on-availability-group-wizards"></a>驗證頁面 (AlwaysOn 可用性群組精靈)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
    
  本說明主題描述 [驗證]  頁面的選項。 本主題適用於 [!INCLUDE[ssAoNewAgWiz](../../../includes/ssaonewagwiz-md.md)] 的[!INCLUDE[ssAoAddRepWiz](../../../includes/ssaoaddrepwiz-md.md)]、[!INCLUDE[ssAoAddDbWiz](../../../includes/ssaoadddbwiz-md.md)]和[!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]。 使用此頁面可以驗證環境是否支援您在精靈之先前頁面上所做的所有組態選擇。  
  
##  <a name="validation-page-options"></a><a name="PageOptions"></a> 驗證頁面選項  
 **可用性群組驗證的結果。**  
 此方格會顯示每個已完成驗證步驟的結果。 方格資料行如下所示：  
  
 **名稱**  
 顯示描述特定步驟的片語。  
  
 **結果**  
 顯示下列其中一個超連結文字。 如需有關給定驗證步驟之結果的詳細資訊，請按一下超連結。  
  
|結果|描述|  
|------------|-----------------|  
|**錯誤**|表示驗證步驟失敗。 按一下連結可檢視錯誤訊息。|  
|**已略過**|表示已略過驗證步驟，因為您的選項不需要驗證。 按一下連結可檢視略過步驟的原因。|  
|「成功」 |表示驗證步驟已順利完成。|  
|**警告**|表示可用性群組組態可能有問題。  按一下連結可檢視警告訊息。|  
  
 **重新執行驗證**  
 如果您在精靈外部進行變更以回應驗證錯誤，按一下可重複驗證步驟。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md)  
  
-   [使用 [將複本加入可用性群組中精靈] &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio.md)  
  
-   [使用將資料庫加入至可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/availability-group-add-database-to-group-wizard.md)  
  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)  
  
  
