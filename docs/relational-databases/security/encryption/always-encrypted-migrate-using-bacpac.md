---
description: 使用 Always Encrypted 匯出和匯入資料庫
title: 使用 Always Encrypted 匯出和匯入資料庫 | Microsoft Docs
ms.custom: ''
ms.date: 10/30/2019
ms.prod: sql
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- Always Encrypted, configure with SSMS
ms.assetid: 29816a41-f105-4414-8be1-070675d62e84
author: jaszymas
ms.author: jaszymas
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 1930de1abe9a27f27ae771e67c139c3c3dfa6cc5
ms.sourcegitcommit: 866554663ca3191748b6e4eb4d8d82fa58c4e426
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97559310"
---
# <a name="export-and-import-databases-using-always-encrypted"></a>使用 Always Encrypted 匯出和匯入資料庫 
[!INCLUDE [SQL Server Azure SQL Database](../../../includes/applies-to-version/sql-asdb.md)]

本文描述如何匯出和匯入包含以 [Always Encrypted](../../../relational-databases/security/encryption/always-encrypted-database-engine.md) 所保護資料行的資料庫。

當您匯出資料庫時，加密資料行中所儲存所有資料都會自加密格式 (加密文字) 的資料庫中擷取並放入所產生 [BACPAC](../../data-tier-applications/data-tier-applications.md)。 產生的 BACPAC 也會包含永遠加密金鑰的中繼資料。

當您將 BACPAC 匯入資料庫中時，會將 BACPAC 中的加密資料載入資料庫中，並且重新建立永遠加密金鑰中繼資料。 

如果應用程式設定成查詢儲存在來源資料庫 (您匯出的資料庫) 的加密資料行，則您不需要執行任何特殊作業就可讓應用程式查詢目標資料庫中的加密資料，因為兩個資料庫中的金鑰相同。

如需如何匯出和匯入資料庫的詳細資訊，請參閱：
- [匯出資料層應用程式](../../data-tier-applications/export-a-data-tier-application.md)
- [匯入 BACPAC 檔案以建立新的使用者資料庫](../../data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database.md)
- [將 Azure SQL 資料庫匯出至 BACPAC 檔案](/azure/sql-database/sql-database-export)
- [將 BACPAC 檔案匯入到 Azure SQL Database 中的資料庫](/azure/sql-database/sql-database-import)
- [SqlPackage.exe](../../../tools/sqlpackage/sqlpackage.md)

## <a name="permissions-for-migrating-databases-with-encrypted-columns"></a>移轉具有加密資料行資料庫的權限

您需要具有來源資料庫的 *ALTER ANY COLUMN MASTER KEY* 和 *ALTER ANY COLUMN ENCRYPTION KEY* 。 您需要具有目標資料庫的 *ALTER ANY COLUMN MASTER KEY*、*ALTER ANY COLUMN ENCRYPTION KEY*、*VIEW ANY COLUMN MASTER KEY DEFINITION* 和 *VIEW ANY COLUMN ENCRYPTION DEFINITION*。

您不需要存取針對加密資料行設定的資料行主要金鑰，因為在匯出和匯入作業期間，資料會保持加密狀態。

## <a name="next-steps"></a>後續步驟
- [使用 Always Encrypted 開發應用程式](always-encrypted-client-development.md)

## <a name="see-also"></a>另請參閱
- [一律加密](../../../relational-databases/security/encryption/always-encrypted-database-engine.md)
- [使用 Always Encrypted 備份和還原資料庫](always-encrypted-migrate-using-backup-restore.md)
- [使用 Always Encrypted 與 SQL Server [匯入及匯出精靈]，將資料移轉進或移轉出資料行](always-encrypted-migrate-using-import-export-wizard.md)
- [使用 Always Encrypted 將加密資料大量載入至資料行](migrate-sensitive-data-protected-by-always-encrypted.md)