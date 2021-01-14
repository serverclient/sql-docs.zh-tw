---
description: 修改資料分割配置
title: 修改資料分割配置 | Microsoft Docs
ms.custom: ''
ms.date: 1/5/2021
ms.prod: sql
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
ms.assetid: 515de63f-dfc5-434d-9adb-f3b5992f745a
author: julieMSFT
ms.author: jrasnick
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: fb261e69d3491f717be8b34bb2b0f699a4d37840
ms.sourcegitcommit: 629229a7c33a3ed99db63b89127bb016449f7d3d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97952030"
---
# <a name="modify-a-partition-scheme"></a>修改資料分割配置
[!INCLUDE [SQL Server Azure SQL Database](../../includes/applies-to-version/sql-asdb.md)]
  您可以使用 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 或 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 來指定檔案群組以保存下一個要加入資料分割資料表的資料分割，藉此在 [!INCLUDE[tsql](../../includes/tsql-md.md)]中修改資料分割配置。 作法是，將 NEXT USED 屬性指派給檔案群組。 NEXT USED 屬性可以指派給空的檔案群組或已保存資料分割的檔案群組。 換句話說，檔案群組可以保存一個以上的資料分割。  
  
 **本主題內容**  
  
-   **開始之前：**  
  
     [限制事項](#Restrictions)  
  
     [安全性](#Security)  
  
-   **使用下列方法建立分割區資料表或索引：**  
  
     [Transact-SQL](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
##  <a name="before-you-begin"></a><a name="BeforeYouBegin"></a> 開始之前  
  
###  <a name="limitations-and-restrictions"></a><a name="Restrictions"></a> 限制事項  
 只要是 ALTER PARTITION SCHEME 影響所及的檔案群組都必須在線上。  

> [!NOTE]
> Azure [!INCLUDE[sqldbesa](../../includes/sqldbesa-md.md)] 只支援主要檔案群組。  
  
###  <a name="security"></a><a name="Security"></a> Security  
  
####  <a name="permissions"></a><a name="Permissions"></a> 權限  
 您可以使用下列權限來執行 ALTER PARTITION SCHEME：  
  
-   ALTER ANY DATASPACE 權限。 這個權限預設會授與 **sysadmin** 固定伺服器角色以及 **db_owner** 和 **db_ddladmin** 固定資料庫角色的成員。  
  
-   建立資料分割結構描述之資料庫的 CONTROL 或 ALTER 權限。  
  
-   在建立資料分割結構描述的資料庫中，其伺服器的 CONTROL SERVER 或 ALTER ANY DATABASE 權限。  
  
##  <a name="using-sql-server-management-studio"></a><a name="SSMSProcedure"></a> 使用 SQL Server Management Studio  
 **若要修改資料分割配置：**  
  
 您無法使用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]來執行這項特定動作。 若要修改資料分割配置，您必須先刪除此配置，然後再使用 [建立資料分割精靈] 來建立具有所需屬性的新配置。 如需詳細資訊，請參閱 [建立分割區資料表及索引](../../relational-databases/partitions/create-partitioned-tables-and-indexes.md)[建立分割區資料表及索引](../../relational-databases/partitions/create-partitioned-tables-and-indexes.md#SSMSProcedure) 下方的 **使用 SQL Server Management Studio**。  
  
#### <a name="to-delete-a-partition-scheme"></a>若要刪除資料分割配置  
  
1.  按一下加號，展開您想要在其中刪除資料分割配置的資料庫。  
  
2.  按一下加號展開 **[儲存體]** 資料夾。  
  
3.  按一下加號展開 **[資料分割配置]** 資料夾。  
  
4.  以滑鼠右鍵按一下您想要刪除的資料分割配置，然後選取 [刪除]。  
  
5.  在 **[刪除物件]** 對話方塊中，確定已選取正確的資料分割配置，然後按一下 **[確定]**。  
  
##  <a name="using-transact-sql"></a><a name="TsqlProcedure"></a> 使用 Transact-SQL  
  
#### <a name="to-modify-a-partition-scheme"></a>若要修改資料分割配置  
  
1.  在 **[物件總管]** 中，連接到 [!INCLUDE[ssDE](../../includes/ssde-md.md)]的執行個體。  
  
2.  在標準列上，按一下 **[新增查詢]** 。  
  
3.  複製下列範例並將其貼到查詢視窗中，然後按一下 **[執行]** 。  
  
    ```  
    USE AdventureWorks2012;  
    GO  
    -- add five new filegroups to the AdventureWorks2012 database  
    ALTER DATABASE AdventureWorks2012  
    ADD FILEGROUP test1fg;  
    GO  
    ALTER DATABASE AdventureWorks2012  
    ADD FILEGROUP test2fg;  
    GO  
    ALTER DATABASE AdventureWorks2012  
    ADD FILEGROUP test3fg;  
    GO  
    ALTER DATABASE AdventureWorks2012  
    ADD FILEGROUP test4fg;  
    GO  
    ALTER DATABASE AdventureWorks2012  
    ADD FILEGROUP test5fg;  
    GO  
    -- if the "myRangePF1" partition function and the "myRangePS1" partition scheme exist,  
    -- drop them from the AdventureWorks2012 database  
    IF EXISTS (SELECT * FROM sys.partition_functions  
        WHERE name = 'myRangePF1')  
    DROP PARTITION FUNCTION myRangePF1;  
    GO  
    IF EXISTS (SELECT * FROM sys.partition_schemes  
        WHERE name = 'myRangePS1')  
    DROP PARTITION SCHEME myRangePS1;  
    GO  
    -- create the new partition function "myRangePF1" with four partition groups  
    CREATE PARTITION FUNCTION myRangePF1 (int)  
    AS RANGE LEFT FOR VALUES ( 1, 100, 1000 );  
    GO  
    -- create the new partition scheme "myRangePS1"that will use   
    -- the "myRangePF1" partition function with five file groups.  
    -- The last filegroup, "test5fg," will be kept empty but marked  
    -- as the next used filegroup in the partition scheme.  
    CREATE PARTITION SCHEME myRangePS1  
    AS PARTITION myRangePF1  
    TO (test1fg, test2fg, test3fg, test4fg, test5fg);  
    GO  
    --Split "myRangePS1" between boundary_values 100 and 1000  
    --to create two partitions between boundary_values 100 and 500  
    --and between boundary_values 500 and 1000.  
    ALTER PARTITION FUNCTION myRangePF1 ()  
    SPLIT RANGE (500);  
    GO  
    -- Allow the "myRangePS1" partition scheme to use the filegroup "test5fg"  
    -- for the partition with boundary_values of 100 and 500  
    ALTER PARTITION SCHEME myRangePS1  
    NEXT USED test5fg;  
    GO  
    ```  
  
 如需詳細資訊，請參閱 [ALTER PARTITION SCHEME &#40;Transact-SQL&#41;](../../t-sql/statements/alter-partition-scheme-transact-sql.md)。  
  
  
