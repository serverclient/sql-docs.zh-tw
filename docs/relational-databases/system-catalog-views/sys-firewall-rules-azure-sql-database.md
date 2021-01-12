---
description: sys.firewall_rules (Azure SQL Database)
title: sys.firewall_rules (Azure SQL Database) Microsoft Docs
ms.date: 03/26/2019
ms.prod: sql
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.firewall_rules
- firewall_rules
- sys.firewall_rules_TSQL
- firewall_rules_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- firewall_rules
- sys.firewall_rules
ms.assetid: 140d2cd8-9aa1-4cc5-870d-e1dbc873b3fe
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: = azuresqldb-current
ms.openlocfilehash: 351ca530c49051b61b6f44b8dc2fbff83623cf8f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98097986"
---
# <a name="sysfirewall_rules-azure-sql-database"></a>sys.firewall_rules (Azure SQL Database)
[!INCLUDE[Azure SQL Database Azure SQL Managed Instance](../../includes/applies-to-version/asdb-asdbmi.md)]

  傳回與相關聯之伺服器層級防火牆設定的相關資訊 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] 。  
  
  檢視表包含下列資料行：  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|id|**INT**|伺服器層級防火牆設定的識別碼。|  
|name|**NVARCHAR (128)**|您選擇用來描述和區分伺服器層級防火牆設定的名稱。|  
|start_ip_address|**VARCHAR (45)**|伺服器層級防火牆設定範圍中最低的 IP 位址。 等於或大於這個位址的 IP 位址可以嘗試連接至 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 伺服器。 可能的最低 IP 位址為 `0.0.0.0`。|  
|end_ip_address|**VARCHAR (45)**|伺服器層級防火牆設定範圍中最高的 IP 位址。 等於或小於這個位址的 IP 位址可以嘗試連接至 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] 伺服器。 可能的最高 IP 位址為 `255.255.255.255`。<br /><br /> 注意：當這個欄位和 [ **start_ip_address** ] 欄位等於時，便允許 Azure 連接嘗試 `0.0.0.0` 。|  
|create_date|**Datetime**|建立伺服器層級防火牆設定時的 UTC 日期和時間。<br /><br /> 注意： UTC 是國際標準時間的縮寫。|  
|modify_date|**Datetime**|上次修改伺服器層級防火牆設定時的 UTC 日期和時間。|  
  
## <a name="remarks"></a>備註

 若要傳回與您 Microsoft Azure SQL Database 相關聯之資料庫層級防火牆設定的相關資訊，請使用 [sys.database_firewall_rules &#40;Azure SQL Database&#41;](../../relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database.md)。  
  
## <a name="permissions"></a>權限

 此視圖的唯讀存取權可供具有連接 **master** 資料庫之許可權的所有使用者使用。  
  
## <a name="see-also"></a>另請參閱

[sp_set_firewall_rule &#40;Azure SQL Database&#41;](../../relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database.md)  
[sp_delete_firewall_rule &#40;Azure SQL Database&#41;](../../relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database.md)   
[sp_set_database_firewall_rule &#40;Azure SQL Database&#41;](../../relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database.md)  
[sp_delete_database_firewall_rule &#40;Azure SQL Database&#41;](../../relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database.md)  
[sys.database_firewall_rules &#40;Azure SQL Database&#41;](../../relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database.md)  
[設定資料庫引擎存取的 Windows 防火牆](../../database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access.md)     
[為 FILESTREAM 存取設定防火牆](../../relational-databases/blob/configure-a-firewall-for-filestream-access.md)  
[設定供報表伺服器存取的防火牆](../../reporting-services/report-server/configure-a-firewall-for-report-server-access.md) 
