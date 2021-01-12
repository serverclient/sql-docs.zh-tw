---
description: CHECKPOINT (Transact-SQL)
title: CHECKPOINT (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/27/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CHECKPOINT_TSQL
- CHECKPOINT
dev_langs:
- TSQL
helpviewer_keywords:
- events [SQL Server], checkpoints
- automatic checkpoints
- writing dirty pages to disk
- pages [SQL Server], dirty
- checkpoints [SQL Server]
- CHECKPOINT statement
- log truncate mode [SQL Server]
- dirty pages
- logs [SQL Server], checkpoints
- manual checkpoints [SQL Server]
- pages [SQL Server], checkpoints
ms.assetid: ccdfc689-ad4e-44c0-83f7-0f2cfcfb6406
author: julieMSFT
ms.author: jrasnick
ms.openlocfilehash: 257bdc81d1f5f2ab6a5599cb155f71453ac27c34
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097143"
---
# <a name="checkpoint-transact-sql"></a>CHECKPOINT (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  在您目前所連接的 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫中產生手動檢查點。  
  
> [!NOTE]  
>  如需不同類型資料庫檢查點與一般檢查點作業的詳細資訊，請參閱[資料庫檢查點 &#40;SQL Server&#41;](../../relational-databases/logs/database-checkpoints-sql-server.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
CHECKPOINT [ checkpoint_duration ]  
```  
  
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *checkpoint_duration*  
 指定所需要之手動檢查點作業完成的時間 (以秒為單位)。 指定 *checkpoint_duration* 時，[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 會嘗試在要求的持續時間內執行檢查點。 *checkpoint_duration* 必須是 **int** 類型的運算式，且必須大於零。 如果省略此參數，[!INCLUDE[ssDE](../../includes/ssde-md.md)] 會調整檢查點持續時間，將資料庫應用程式所受到的效能影響降到最低。 *checkpoint_duration* 是進階選項。  
  
## <a name="factors-affecting-the-duration-of-checkpoint-operations"></a>影響檢查點作業持續時間的因素  
 一般而言，檢查點作業必須寫入的中途分頁數愈多，檢查點作業所需要的時間也會愈長。 為能將其他應用程式所受到的效能影響降到最低，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 預設會調整檢查點作業執行的寫入頻率。 降低寫入頻率會增加完成檢查點作業所需要的時間。 除非在 CHECKPOINT 命令中指定 *checkpoint_duration* 值，否則 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 會將此策略應用於手動檢查點。  
  
 使用 *checkpoint_duration* 對效能的影響會隨著中途分頁數、系統上的活動，以及所指定的實際持續時間而不同。 例如，如果檢查點通常會在 120 秒內完成，將 *checkpoint_duration* 指定為 45 秒會造成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 用於檢查點的資源比預設指派的還多。 相反地，將 *checkpoint_duration* 指定為 180 秒則會造成 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 指派的資源比預設指派的還少。 一般而言，縮短 *checkpoint_duration* 將增加檢查點所佔用的資源；而加長 *checkpoint_duration* 則會減少檢查點所佔用的資源。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 一律會盡其所能地完成檢查點，並會在檢查點完成時，立即傳回 CHECKPOINT 陳述式。 因此，在某些情況下，檢查點作業的完成會比指定的持續時間快，執行時間也有可能超出指定的持續時間。  
  
##  <a name="security"></a><a name="Security"></a> Security  
  
### <a name="permissions"></a>權限  
 CHECKPOINT 權限是 **sysadmin** 固定伺服器角色成員及 **db_owner** 與 **db_backupoperator** 固定資料庫角色成員的預設權限，且不可轉移。  
  
## <a name="see-also"></a>另請參閱  
 [ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)   
 [資料庫檢查點 &#40;SQL Server&#41;](../../relational-databases/logs/database-checkpoints-sql-server.md)   
 [設定 recovery interval 伺服器設定選項](../../database-engine/configure-windows/configure-the-recovery-interval-server-configuration-option.md)   
 [SHUTDOWN &#40;Transact-SQL&#41;](../../t-sql/language-elements/shutdown-transact-sql.md)  
  
  
