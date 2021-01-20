---
title: 適用于 MySQL 的 SQL Server 移轉小幫手 (MySQLToSQL) |Microsoft Docs
description: 瞭解適用于 MySQL 的 SSMA，並遵循將 MySQL 資料庫遷移至 SQL Server 或 Azure SQL Database 的逐步指示。
ms.prod: sql
ms.custom: ''
ms.date: 10/10/2019
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.assetid: 2793bc33-38d3-46ed-8277-b8580cf78ced
author: nahk-ivanov
ms.author: alexiva
manager: alexiva
ms.openlocfilehash: 5947ea6dad4a87258476b9b98d2bc4bc1a10a73d
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596810"
---
# <a name="sql-server-migration-assistant-for-mysql-mysqltosql"></a>適用于 MySQL 的 SQL Server 移轉小幫手 (MySQLToSQL) 

[!INCLUDE[msCoName](../../includes/msconame_md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]適用于 MySQL 的 Migration Assistant (SSMA) 是一種工具，可將 MySQL 資料庫遷移至 [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] windows 和 linux 上的2012、2014、 [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 2016、2017、windows 和 Linux 上的 [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 2019，或 Azure [!INCLUDE[msCoName](../../includes/msconame_md.md)] 資料庫。 適用于 MySQL 的 SSMA 會將 MySQL 資料庫物件轉換成 SQL Server 資料庫物件、在中建立這些物件， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 然後將資料從 MySQL 遷移至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。  
  
本檔將為您介紹 SSMA for MySQL，並提供將 MySQL 資料庫遷移至的逐步指示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 下表顯示的文章可協助您深入瞭解：  
  
## <a name="contents"></a>目錄  
  
|區段|描述|
|-----------|---------------|
|[SSMA for MySQL 的新功能](./what-s-new-in-ssma-for-mysql-mysqltosql.md)|此版本的 SSMA for MySQL 的新功能|  
|[安裝適用于 MySQL 的 SSMA &#40;MySqlToSql&#41;](../../ssma/mysql/installing-ssma-for-mysql-mysqltosql.md)|包含的文章提供在執行 SQL Server 的電腦上安裝適用于 MySQL 的 SSMA 用戶端和必要元件的必要條件和指示。|  
|[使用 SSMA for MySQL 進行消費者入門 &#40;MySQLToSQL&#41;](../../ssma/mysql/getting-started-with-ssma-for-mysql-mysqltosql.md)|介紹使用者介面、專案和設定選項。|  
|[將 MySQL 資料庫遷移至 SQL Server Azure SQL Database &#40;MySQLToSql&#41;](../../ssma/mysql/migrating-mysql-databases-to-sql-server-azure-sql-db-mysqltosql.md)|提供轉換程式的總覽，以及程式中每個步驟的詳細資訊。|  
|[消費者介面參考 &#40;MySQLToSQL&#41;](../../ssma/mysql/user-interface-reference-mysqltosql.md)|包含適用于 MySQL 的 SSMA 檔對話方塊。|  
|[使用 SSMA for MySQL 主控台](working-with-ssma-for-mysql-console-mysqltosql.md)|包含 SSMA 主控台應用程式的檔|  
|[取得 MySQL 的 SSMA 協助](../sql-server-migration-assistant.md)|提供有關取得其他協助的資訊。|