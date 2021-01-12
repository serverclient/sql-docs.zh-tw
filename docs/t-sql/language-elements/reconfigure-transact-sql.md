---
description: RECONFIGURE (Transact-SQL)
title: RECONFIGURE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 05/20/2016
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- RECONFIGURE
- RECONFIGURE_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- reconfiguring configuration options
- configuration options [SQL Server], reconfiguring
- updating configuration options
- RECONFIGURE, RECONFIGURE statement
- RECONFIGURE
- RECONFIGURE, WITH OVERRIDE statement
ms.assetid: 2e6e4eeb-b70b-4f45-a253-28ac4e595d75
author: cawrites
ms.author: chadam
ms.openlocfilehash: 2fa09a4ac7fb59ec39524a709387ee4ff91aed63
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98082952"
---
# <a name="reconfigure-transact-sql"></a>RECONFIGURE (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  更新 **sp_configure** 系統預存程序所變更之設定選項目前所設定的值 (**sp_configure** 結果集中的 **config_value** 資料行)。 由於部分設定選項需要停止再重新啟動伺服器，才能更新目前正在執行的值，因此，RECONFIGURE 不一定會針對變更的設定值來更新目前正在執行的值 (**sp_configure** 結果集中的 **run_value** 資料行)。    
    
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)    
    
## <a name="syntax"></a>語法    
    
```syntaxsql
RECONFIGURE [ WITH OVERRIDE ]    
```    
    
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 RECONFIGURE    
 指定如果組態設定不需要停止再重新啟動伺服器，便應該更新目前在執行的值。 另外，RECONFIGURE 也會檢查新的設定值中是否存有無效值 (例如，不存在於 **syscharsets** 的排列順序值) 或非建議值。 當使用不需要停止再重新啟動伺服器的組態選項時，在指定 RECONFIGURE 之後，組態選項目前在執行的值和目前已設定的值應該相同。    
    
 WITH OVERRIDE    
 停用 **recovery interval** 進階設定選項的設定值檢查 (用以找出無效值或非建議值)。    
    
 幾乎所有設定選項都可使用 WITH OVERRIDE 選項重新設定，不過，仍要避免一些嚴重錯誤。 例如，您無法將 **min server memory** 設定選項的值設為大於 **max server memory** 設定選項所指定的值。
      
## <a name="remarks"></a>備註    
 **sp_configure** 不接受針對各個設定選項，使用超出所記載之有效範圍的新設定選項值。    
    
 在明確或隱含的交易中，不允許使用 RECONFIGURE。 當您同時重新設定數個選項時，若有任何重新設定作業失敗，則所有重新設定作業都不會生效。    
    
 重新設定 Resource Governor 時，請參閱 [ALTER RESOURCE GOVERNOR &#40;Transact-SQL&#41;](../../t-sql/statements/alter-resource-governor-transact-sql.md) 的 RECONFIGURE 選項。    
    
## <a name="permissions"></a>權限    
 RECONFIGURE 權限預設給 ALTER SETTINGS 權限的被授與者。 **sysadmin** 和 **serveradmin** 固定伺服器角色會隱含地持有這個權限。    
    
## <a name="examples"></a>範例    
 下列範例將 `recovery interval` 組態選項的上限設為 `75` 分鐘，並利用 `RECONFIGURE WITH OVERRIDE` 來安裝它。 不建議您使用超出 60 分鐘的復原間隔，依預設，不會接受這個值。 不過，由於指定了 `WITH OVERRIDE` 選項，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 並不會檢查指定的值 (`75`) 是否為 `recovery interval` 組態選項的有效值。    
    
```sql    
EXEC sp_configure 'recovery interval', 75    
RECONFIGURE WITH OVERRIDE;    
GO    
```    
    
## <a name="see-also"></a>另請參閱    
 [伺服器組態選項 &#40;SQL Server&#41;](../../database-engine/configure-windows/server-configuration-options-sql-server.md)     
 [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)    
    
  
