---
title: 選取資料庫頁面 (新增可用性群組精靈 & 新增資料庫精靈)
description: 說明在 SQL Server Management Studio GUI 中新增可用性群組和新增資料庫精靈的 [選取資料庫] 頁面。
ms.custom: seo-lt-2019
ms.date: 05/17/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: conceptual
f1_keywords:
- sql13.swb.adddatabasewizard.selectdatabases.f1
- sql13.swb.newagwizard.selectdatabases.f1
ms.assetid: 929c5e15-d087-438d-b1f2-aa97c5f8bff8
author: cawrites
ms.author: chadam
ms.openlocfilehash: a6971231c42b91907ad668f5cb93c185f08c9120
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643344"
---
# <a name="select-databases-page-new-availability-group-wizard-and-add-database-wizard"></a>選取資料庫頁面 (新增可用性群組精靈和加入資料庫精靈)
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  本說明主題描述 **[指定資料庫]** 頁面的選項。 此主題適用於 [!INCLUDE[ssAoNewAgWiz](../../../includes/ssaonewagwiz-md.md)] 的 [!INCLUDE[ssAoAddDbWiz](../../../includes/ssaoadddbwiz-md.md)] 和 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]。  
  
##  <a name="select-databases-options"></a><a name="PageOptions"></a> 選取資料庫選項  
 **[這個 SQL Server 執行個體上的使用者資料庫]** 方格會列出每個本機使用者資料庫。 資料行如下：  
  
 **名稱**  
 顯示本機使用者資料庫的名稱。  

 **大小**  
 顯示資料庫大小 (如果精靈可使用大小)。  
  
 **狀態**  
 顯示超連結，其文字指出給定的資料庫是否符合加入可用性群組的必要條件。 如果狀態為 **[符合必要條件]** ，表示您可以將此資料庫加入至可用性群組。 如果資料庫不符合所有必要條件， **[狀態]** 超連結會提供為什麼資料庫不合格的簡短說明。 如需詳細資訊，請按一下此超連結。  
  
 對資料庫採取動作以符合必要條件時，您可以離開精靈的 **[選取資料庫]** 頁面。 當您返回 **[選取資料庫]** 頁面時，請按一下 **[重新整理]** 更新方格。  
  
 **密碼**  
 如果資料庫含有資料庫主要金鑰，請輸入資料庫主要金鑰的密碼。  
  
 **[重新整理]**  
 按一下以重新整理方格。 對資料庫採取動作以符合必要條件之後，這非常有用。  
  
##  <a name="related-tasks"></a><a name="RelatedTasks"></a> 相關工作  
  
-   [使用新增可用性群組對話方塊 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/use-the-new-availability-group-dialog-box-sql-server-management-studio.md)  
  
-   [使用將資料庫加入至可用性群組精靈 &#40;SQL Server Management Studio&#41;](../../../database-engine/availability-groups/windows/availability-group-add-database-to-group-wizard.md)  
  
## <a name="see-also"></a>另請參閱  
 [AlwaysOn 可用性群組概觀 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server.md)   
 [AlwaysOn 可用性群組的必要條件、限制和建議 &#40;SQL Server&#41;](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)  
  
  
