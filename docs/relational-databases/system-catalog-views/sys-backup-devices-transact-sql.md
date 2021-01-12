---
description: sys.backup_devices (Transact-SQL)
title: sys.backup_devices (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- backup_devices_TSQL
- backup_devices
- sys.backup_devices
- sys.backup_devices_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- backup devices [SQL Server], viewing information
- sys.backup_devices catalog view
ms.assetid: 457edaa4-aca1-4bd3-bf8d-734490b80fcd
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: cddc056b4e83c6bb3e01d901eb4106318b7cd71b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98099250"
---
# <a name="sysbackup_devices-transact-sql"></a>sys.backup_devices (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  針對使用 **sp_addumpdevice** 註冊或在中建立的每個備份裝置，各包含一個資料列 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 。  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**name**|**sysname**|備份裝置的名稱。 在整組中，這是唯一的。|  
|**type**|**tinyint**|備份裝置的類型：<br /><br /> 2 = 磁碟<br /><br /> 3 = 磁片 (已棄用)<br /><br /> 5 = 磁帶<br /><br /> 6 = 管道 (已棄用)<br /><br /> 7 = 虛擬裝置 (供協力廠商備份供應商選擇性使用)<br /><br /> 9 = URL<br /><br />通常只會使用磁片 (2) 和 URL (9) 。|  
|**type_desc**|**nvarchar(60)**|備份裝置類型的描述：<br /><br /> DISK<br /><br /> DISKETTE (已棄用)<br /><br /> TAPE<br /><br /> PIPE (已棄用)<br /><br /> VIRTUAL_DEVICE (供協力廠商備份供應商選擇性使用)<br /><br /> URL <br /><br /> 通常只會使用磁片和 URL。|  
|**physical_name**|**nvarchar(260)**|備份裝置的實體檔案名稱或路徑。|  
  
## <a name="permissions"></a>權限  
 [!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 如需相關資訊，請參閱 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)。  
  
## <a name="see-also"></a>另請參閱  
 [目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/catalog-views-transact-sql.md)   
 [BACKUP &#40;Transact-SQL&#41;](../../t-sql/statements/backup-transact-sql.md)   
 [備份裝置 &#40;SQL Server&#41;](../../relational-databases/backup-restore/backup-devices-sql-server.md)   
 [sp_addumpdevice &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addumpdevice-transact-sql.md)   
 [資料庫和檔案目錄檢視 &#40;Transact-SQL&#41;](../../relational-databases/system-catalog-views/databases-and-files-catalog-views-transact-sql.md)   
 [查詢 SQL Server 系統目錄 FAQ](../../relational-databases/system-catalog-views/querying-the-sql-server-system-catalog-faq.md)  
  
  
