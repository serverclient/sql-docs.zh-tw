---
title: SQL Server 移轉小幫手適用于 Sybase (SybaseToSQL) |Microsoft Docs
description: 瞭解適用于 Sybase 的 SSMA，並遵循將 ASE 資料庫移轉至 SQL Server 或 Azure SQL Database 的逐步指示。
ms.custom: ''
ms.date: 10/10/2019
ms.prod: sql
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.assetid: 59e63eac-8a7e-4d54-be1c-0633a9bf510d
author: nahk-ivanov
ms.author: alexiva
manager: alexiva
ms.openlocfilehash: d961301d94eb9c14bb4fc924cf826c769843483e
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596612"
---
# <a name="sql-server-migration-assistant-for-sybase-sybasetosql"></a>SQL Server 移轉小幫手適用于 Sybase (SybaseToSQL) 

[!INCLUDE[msCoName](../../includes/msconame_md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Migration Assistant (SSMA) 適用于 Sybase 調適型 Server Enterprise (ASE) 是將 ASE 資料庫移轉至 [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的工具[!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Windows 和 linux 上的2012、2014、 [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 2016、 [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 2017、 [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] windows 和 linux 上的2019或 [!INCLUDE[msCoName](../../includes/msconame_md.md)] Azure SQL Database。 SSMA for Sybase 會將 ASE 資料庫物件轉換為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫物件、在或 Azure SQL Database 中建立這些物件 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，然後將資料從 ASE 遷移至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 Azure SQL Database。
  
本檔會介紹如何針對 Sybase 進行 SSMA，並提供將 ASE 資料庫移轉至或 Azure SQL Database 的逐步指示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ，以及遷移之後可能發生之問題的相關資訊。 若要深入瞭解，請參閱下列文章。  
  
## <a name="contents"></a>目錄  
  
|區段|描述|
|-----------|---------------|
|[SSMA for Sybase 的新功能 &#40;SybaseToSQL&#41;](../../ssma/sybase/what-s-new-in-ssma-for-sybase-sybasetosql.md)|列出 SSMA 版本的變更。|  
|[安裝適用于 Sybase &#40;SybaseToSQL&#41;的 SSMA ](../../ssma/sybase/installing-ssma-for-sybase-sybasetosql.md)|包含的文章會提供在執行實例的電腦上安裝 SSMA for Sybase 用戶端和必要元件的必要條件和指示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。|  
|[消費者入門適用于 Sybase &#40;SybaseToSQL&#41;的 SSMA ](../../ssma/sybase/getting-started-with-ssma-for-sybase-sybasetosql.md)|介紹使用者介面、專案和設定選項。|  
|[將 Sybase ASE 資料庫移轉至 SQL Server Azure SQL Database &#40;SybaseToSQL&#41;](../../ssma/sybase/migrating-sybase-ase-databases-to-sql-server-azure-sql-db-sybasetosql.md)|提供轉換程式的總覽，以及程式中每個步驟的詳細資訊。|  
|[消費者介面參考 &#40;SybaseToSQL&#41;](../../ssma/sybase/user-interface-reference-sybasetosql.md)|包含適用于 Sybase 對話方塊的 SSMA 檔。|  
|[使用 SSMA for Sybase 主控台](working-with-ssma-for-sybase-console-sybasetosql.md)|包含 SSMA 主控台應用程式的檔。|  
|[取得 Sybase 協助的 SSMA](../sql-server-migration-assistant.md)|提供有關取得其他協助的資訊。|