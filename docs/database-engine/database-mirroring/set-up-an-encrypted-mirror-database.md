---
title: 設定加密鏡像資料庫 | Microsoft Docs
description: 了解如何使用 sp_control_dbmasterkey_password 來建立認證，以啟用鏡像資料庫之資料庫主要金鑰的自動解密。
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: database-mirroring
ms.topic: conceptual
helpviewer_keywords:
- cryptography [SQL Server], database mirroring
- encryption [SQL Server], database mirroring
- database master key [SQL Server], database mirroring
- mirror database [SQL Server]
- database mirroring [SQL Server], security
ms.assetid: 7329a575-be29-46e0-abc6-1344db37920c
author: MikeRayMSFT
ms.author: mikeray
ms.openlocfilehash: 6611528bd3d5b12f01ecebb48e63ee3b01d73215
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641974"
---
# <a name="set-up-an-encrypted-mirror-database"></a>設定加密鏡像資料庫
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
  若要啟用鏡像資料庫之資料庫主要金鑰的自動解密，則必須將用來加密主要金鑰的密碼提供給鏡像伺服器執行個體。 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 和更新版本包含傳送密碼的機制。 啟動資料庫鏡像之前，請先使用 **sp_control_dbmasterkey_password** 來建立資料庫主要金鑰的認證。 您必須為每個要進行鏡像的資料庫重複此程序。 如需詳細資訊，請參閱 [sp_control_dbmasterkey_password &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-control-dbmasterkey-password-transact-sql.md)。  
  
> [!CAUTION]  
>  對於不想讓 **sa** 和其他具備高度權限的伺服器主體存取的資料庫，請不要啟用容錯移轉解密。 您可以將資料庫設為不能由服務主要金鑰解密它的金鑰階層。 此選項為資料庫提供深度的保護，該資料庫含有 **sa** 和其他具備高度權限的伺服器主體不應存取的資訊。 啟用此種資料庫的容錯移轉解密會移除此深度的保護，讓 **sa** 和其他具備高度權限的伺服器主體可解密資料庫。  
  
## <a name="see-also"></a>另請參閱  
 [sp_control_dbmasterkey_password &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-control-dbmasterkey-password-transact-sql.md)   
 [CREATE MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/create-master-key-transact-sql.md)   
 [ALTER MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-master-key-transact-sql.md)   
 [加密階層](../../relational-databases/security/encryption/encryption-hierarchy.md)   
 [設定資料庫鏡像 &#40;SQL Server&#41;](../../database-engine/database-mirroring/setting-up-database-mirroring-sql-server.md)  
  
  
