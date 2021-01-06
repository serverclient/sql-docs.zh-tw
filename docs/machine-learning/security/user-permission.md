---
title: 授與執行 Python 和 R 指令碼的權限
description: 了解如何對使用者授與在 SQL Server 機器學習服務中執行外部 Python 及 R 指令碼的權限，並將讀取、寫入或資料定義語言 (DDL) 權限授與資料庫。
ms.prod: sql
ms.technology: machine-learning-services
ms.date: 10/14/2020
ms.topic: how-to
author: dphansen
ms.author: davidph
ms.custom: seo-lt-2019, contperf-fy20q4
monikerRange: '>=sql-server-2016||>=sql-server-linux-ver15||=azuresqldb-mi-current'
ms.openlocfilehash: 498021cc74f93ed216088ae1756f386604c07bb5
ms.sourcegitcommit: cb8e2ce950d8199470ff1259c9430f0560f0dc1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878731"
---
# <a name="grant-users-permission-to-execute-python-and-r-scripts-with-sql-server-machine-learning-services"></a>使用 SQL Server 機器學習服務，授與使用者執行 Python 及 R 指令碼的權限
[!INCLUDE [SQL Server 2016 SQL MI](../../includes/applies-to-version/sqlserver2016-asdbmi.md)]

了解如何對使用者授與在 [SQL Server 機器學習服務](../sql-server-machine-learning-services.md)中執行外部 Python 及 R 指令碼的權限，並將讀取、寫入或資料定義語言 (DDL) 權限授與資料庫。

如需詳細資訊，請在[擴充性架構的安全性概觀](../../machine-learning/concepts/security.md#permissions)中參閱＜權限＞區段。

<a name="permissions-external-script"></a>

## <a name="permission-to-run-scripts"></a>執行指令碼的權限

針對使用 SQL Server 機器學習服務執行 R 或 Python 指令碼的每位使用者，以及非系統管理員的使用者，您都必須授與權限，使其可在使用該語言的每個資料庫中執行外部指令碼。

若要授與執行外部指令碼的權限，請執行下列指令碼：

```sql
USE <database_name>
GO
GRANT EXECUTE ANY EXTERNAL SCRIPT TO [UserName]
```

> [!NOTE]
> 權限並非限定於支援的指令碼語言。 換句話說，R 指令碼與 Python 指令碼並沒有不同的權限層級。

<a name="permissions-db"></a>

## <a name="grant-databases-permissions"></a>授與資料庫權限

當使用者執行指令碼時，使用者可能需要讀取其他資料庫的資料。 使用者也可能需要建立新的資料表來儲存結果，並將資料寫入資料表。

請確定執行 R 或 Python 指令碼的所有 Windows 使用者帳戶或 SQL 登入，具有特定資料庫的適當權限： 

+ `db_datareader` 可讀取資料。
+ `db_datawriter` 可將物件儲存至資料庫。
+ `db_ddladmin` 可建立包含定型及序列化資料的預存程序或資料表等物件。

例如，下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式提供 SQL 登入 *MySQLLogin* 在  資料庫中執行 T-SQL 查詢的權限。 SQL 登入必須存在於伺服器的安全性內容中，才能執行此陳述式。

```sql
USE ML_Samples
GO
EXEC sp_addrolemember 'db_datareader', 'MySQLLogin'
```

## <a name="next-steps"></a>後續步驟

如需每個角色中所包含的權限詳細資訊，請參閱[資料庫層級角色](../../relational-databases/security/authentication-access/database-level-roles.md)。
