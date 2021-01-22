---
title: Transact-SQL 參考 (資料庫引擎)
description: Transact-SQL 參考 (資料庫引擎)
ms.custom: ''
ms.date: 04/29/2020
ms.prod: sql
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- sql13.tsqlref.f1
- devlang-tsql
helpviewer_keywords:
- Transact-SQL
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: ''
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: dfe9a07eb1b37f0bddf128b1cbdf39de3d74a643
ms.sourcegitcommit: 713e5a709e45711e18dae1e5ffc190c7918d52e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689077"
---
# <a name="transact-sql-reference-database-engine"></a>Transact-SQL 參考 (資料庫引擎)
[!INCLUDE[tsql-appliesto-ss2008-all-md](../includes/tsql-appliesto-ss2008-all-md.md)]

本主題說明如何尋找及使用 Microsoft [!INCLUDE[tsql](../includes/tsql-md.md)] (T-SQL) 參考主題的基本概念。 對於使用 Microsoft SQL 產品和服務而言，T-SQL 相當重要。 所有與 SQL 資料庫通訊的工具和應用程式都透過傳送 T-SQL 命令與 SQL 資料庫通訊。  

## <a name="t-sql-compliance-with-sql-standard"></a>SQL Standard 的 t-sql 合規性
如需如何在 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 中實作特定標準的詳細技術文件，請參閱 [Microsoft SQL Server 標準支援文件](/openspecs/sql_standards/ms-sqlstandlp/89fb00b1-4b9e-4296-92ce-a2b3f7ca01d2)。

## <a name="tools-that-use-t-sql"></a>使用 T-SQL 的工具
發出 T-SQL 命令的一些 Microsoft 工具如下：

- [SQL Server Management Studio (SSMS)](../ssms/download-sql-server-management-studio-ssms.md)
- [Azure Data Studio](../azure-data-studio/download-azure-data-studio.md)
- [SQL Server Data Tools (SSDT)](../ssdt/download-sql-server-data-tools-ssdt.md)
- [sqlcmd](../tools/sqlcmd-utility.md)

## <a name="locate-the-transact-sql-reference-topics"></a>尋找 Transact-SQL 參考主題  
若要尋找 T-SQL 主題，請使用此頁面右上方的搜尋，或使用頁面左邊的目錄尋找。 您也可以在 Management Studio 查詢編輯器視窗中，鍵入 T-SQL 關鍵字，然後按下 F1。

## <a name="find-system-views"></a>尋找系統檢視
若要尋找系統資料表、視圖、函式和程式，請參閱 SQL 檔的「 [使用關係資料庫](../relational-databases/databases/databases.md) 」一節中的這些連結。

- [系統目錄檢視](../relational-databases/system-catalog-views/catalog-views-transact-sql.md)
- [系統相容性檢視](../relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)
- [系統動態管理檢視](../relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)
- [系統函數](../relational-databases/system-functions/system-functions-category-transact-sql.md)
- [系統資訊結構描述檢視](../relational-databases/system-information-schema-views/system-information-schema-views-transact-sql.md)
- [系統預存程序](../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)
- [系統資料表](../relational-databases/system-tables/system-tables-transact-sql.md)

## <a name="applies-to-references"></a>「適用於」參考  

T-sql 參考主題包含多個版本的 SQL Server （從2008開始）和其他 Azure SQL 服務。 在接近每個主題的頂端會有一節說明適用於該主題主旨的產品和服務種類。 

例如，本主題適用於所有版本，並具有下列標籤。

[!INCLUDE[tsql-appliesto-ss2008-all_md](../includes/tsql-appliesto-ss2008-all-md.md)]

另一個範例如下，下列標籤指出主題僅適用於 Azure Synapse Analytics 和平行處理資料倉儲。

[!INCLUDE[tsql-appliesto-xxxxxx-xxxx-asdw-pdw_md](../includes/applies-to-version/asa-pdw.md)]

在某些情形下，主題適用於某產品或服務，但卻不支援所有引數。 在此情況下，其他 **適用于** 區段的會在主題主體中插入適當的引數描述。

## <a name="get-help-from-microsoft-q--a"></a>透過 Microsoft 問答集求助

如需線上說明，請參閱 [Microsoft 問答集 Transact-SQL 論壇](/answers/topics/sql-server-transact-sql.html)。

## <a name="see-other-language-references"></a>查看其他語言參考

SQL 文件包括這些其他語言參考：

- [XQuery 語言參考](../xquery/xquery-language-reference-sql-server.md)
- [Integration Services 語言參考](../integration-services/integration-services-language-reference.md)
- [複寫語言參考](../relational-databases/replication/replication-language-reference.md)
- [Analysis Services 語言參考](../mdx/multidimensional-expressions-mdx-reference.md)

## <a name="next-steps"></a>後續步驟
既然您已經瞭解如何尋找 T-sql 參考主題，就可以開始：

- 透過簡短教學課程逐步了解如何撰寫 T-SQL，請參閱[教學課程：撰寫 Transact-SQL 陳述式](../t-sql/tutorial-writing-transact-sql-statements.md)。
- 檢視 [Transact-SQL 語法慣例 &#40;Transact-SQL&#41;](../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)。