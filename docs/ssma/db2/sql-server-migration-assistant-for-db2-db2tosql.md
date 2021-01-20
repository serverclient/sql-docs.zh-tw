---
title: SQL Server 移轉小幫手 for DB2 (DB2ToSQL) |Microsoft Docs
description: 瞭解 SSMA for DB2，並遵循將 DB2 資料庫移轉至 SQL Server 或 Azure SQL Database 的逐步指示。
ms.prod: sql
ms.custom: ''
ms.date: 10/10/2019
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.assetid: 7633f631-ffad-469a-8441-8831a6a9f932
author: nahk-ivanov
ms.author: alexiva
manager: alexiva
ms.openlocfilehash: e87f43a74d76f51faf51b7877b6aa1d526a7eafb
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98594745"
---
# <a name="sql-server-migration-assistant-for-db2-db2tosql"></a>SQL Server 移轉小幫手 for DB2 (DB2ToSQL) 
[!INCLUDE[msCoName](../../includes/msconame_md.md)][!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]Migration Assistant (SSMA) FOR db2 是將 db2 資料庫移轉至 [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的工具[!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Windows 和 linux 上的2012、2014、 [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 2016、 [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 2017、 [!INCLUDE[msCoName](../../includes/msconame_md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] windows 和 linux 上的2019或 Azure SQL Database。 SSMA for DB2 會將 DB2 資料庫物件轉換為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫物件，在中建立這些物件， [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 然後將資料從 DB2 遷移至 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 或 Azure SQL Database。  
  
本檔將為您介紹 SSMA for DB2，並提供將 DB2 資料庫移轉至的逐步指示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。 下表顯示的文章可協助您深入瞭解：  
  
## <a name="contents"></a>目錄  
  
|區段|描述|  
|-----------|---------------|
|[SSMA for DB2 的新功能](./what-s-new-in-ssma-for-db2-db2tosql.md)|此版本的 SSMA for DB2 的新功能|  
|[安裝 SSMA for DB2 用戶端 &#40;DB2ToSQL&#41;](../../ssma/db2/installing-ssma-for-db2-client-db2tosql.md)|包含的文章會提供在執行的電腦上安裝 SSMA for DB2 用戶端和必要元件的必要條件和指示 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 。|  
|[消費者入門與 SSMA for DB2 &#40;DB2ToSQL&#41;](../../ssma/db2/getting-started-with-ssma-for-db2-db2tosql.md)|介紹使用者介面、專案和設定選項。|  
|[將 DB2 資料庫移轉至 SQL Server &#40;DB2ToSQL&#41;](../../ssma/db2/migrating-db2-databases-to-sql-server-db2tosql.md)|提供轉換程式的總覽，以及程式中每個步驟的詳細資訊。|  
|[消費者介面參考 &#40;DB2ToSQL&#41;](../../ssma/db2/user-interface-reference-db2tosql.md)|包含 SSMA for DB2 對話方塊的檔。|  
|[使用 SSMA for DB2 主控台](./working-with-ssma-for-oracle-console-db2tosql.md)|包含 SSMA 主控台應用程式的檔|  
|[取得 SSMA for DB2 協助](../sql-server-migration-assistant.md)|提供有關取得其他協助的資訊。|