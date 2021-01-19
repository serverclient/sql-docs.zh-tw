---
title: 啟用外部指令碼伺服器設定選項
description: 深入了解 SQL Server 中外部指令碼已啟用的選項。 開啟之後，即可在支援的語言 (例如 R 或 Python) 中執行外部指令碼。
ms.date: 06/30/2020
ms.prod: sql
ms.technology: machine-learning-services
ms.reviewer: ''
ms.topic: language-reference
f1_keywords:
- external scripts enabled
- external_scripts_enabled_TSQL
helpviewer_keywords:
- external scripts enabled option
ms.assetid: 9d0ce165-8719-4007-9ae8-00f85cab3a0d
author: dphansen
ms.author: davidph
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15'
ms.openlocfilehash: 90a24ba11ce482e8d8608e1641d64ace067ad41b
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170970"
---
# <a name="external-scripts-enabled-server-configuration-option"></a>啟用外部指令碼伺服器設定選項
[!INCLUDE [SQL Server 2016 and later](../../includes/applies-to-version/sqlserver2016.md)]

使用 **external scripts enabled** 選項以啟用具有特定遠端語言擴充功能之指令碼的執行。 依預設，此屬性為 OFF。 若已安裝 **機器學習服務**，則安裝程式即可選擇將此屬性設定為 True。

## <a name="remarks"></a>備註

您必須先啟用 external script enabled 選項，才能使用 [sp_execute_external_script](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md) 程序來執行外部指令碼。 使用 **sp_execute_external_script** 執行以所支援語言 (例如 R 或 Python) 所撰寫的指令碼。 

+ 針對 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)]

    [!INCLUDE[rsql_productname](../../includes/rsql-productname-md.md)] 支援 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 中的 R 語言，以及一組 R 工作站工具和連線程式庫。

    在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式期間安裝 [R 服務] 功能，以啟用 R 指令碼的執行。

+ 適用於 [!INCLUDE[sssql17-md](../../includes/sssql17-md.md)] 及更新版本

    [!INCLUDE[rsql-productnamenew-md](../../includes/rsql-productnamenew-md.md)] 已同時支援 R 與 Python 語言。

    在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式期間安裝 [機器學習服務] 功能，以啟用外部指令碼的執行。 請務必在初始安裝期間選取至少一個語言：R、Python 或兩者。
    
+ 針對 [!INCLUDE[ssSQL15](../../includes/sssqlv15-md.md)] 和更新版本，[!INCLUDE[rsql-productnamenew-md](../../includes/rsql-productnamenew-md.md)] 支援所有 R、Python、JAVA 和其他協力廠商語言。

在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 安裝程式期間安裝機器學習服務與語言延伸模組功能，在支援的語言下啟用執行外部指令碼。

## <a name="additional-requirements"></a>其他需求

安裝後，若要啟用外部指令碼，請執行下列指令碼：

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;  
```

如需詳細資訊，請參閱[在 Windows 上安裝 SQL Server 機器學習服務 (Python 與 R)](../../machine-learning/install/sql-machine-learning-services-windows-install.md) 或 [Linux](../../linux/sql-server-linux-setup-machine-learning-docker.md?toc=/sql/machine-learning/toc.json)。

## <a name="see-also"></a>另請參閱

+ [sp_configure &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)
+ [RECONFIGURE &#40;Transact-SQL&#41;](../../t-sql/language-elements/reconfigure-transact-sql.md)
+ [sp_execute_external_script &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql.md)
+ [SQL 機器學習文件](../../machine-learning/index.yml)
