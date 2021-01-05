---
description: Replication System Stored Procedures Concepts
title: 複寫系統預存程序概念 | Microsoft 文件
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: replication
ms.topic: reference
dev_langs:
- TSQL
helpviewer_keywords:
- stored procedures [SQL Server replication], programming
- programming [SQL Server replication], system stored procedures
- programming interfaces [SQL Server replication]
- system stored procedures [SQL Server replication]
- replication [SQL Server], how-to topics
ms.assetid: 816d2bda-ed72-43ec-aa4d-7ee3dc25fd8a
author: MashaMSFT
ms.author: mathoma
monikerRange: =azuresqldb-mi-current||>=sql-server-2016
ms.openlocfilehash: eb082b29411b136edca7ed14f06effa354a5f0e6
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643088"
---
# <a name="replication-system-stored-procedures-concepts"></a>Replication System Stored Procedures Concepts
[!INCLUDE[sql-asdbmi](../../../includes/applies-to-version/sql-asdbmi.md)]

  在 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 中，系統預存程序可提供複寫拓撲中所有使用者可設定的功能之程式存取權。 雖然使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 或是 sqlcmd 命令列公用程式，可以個別執行預存程序，但是撰寫 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 指令碼檔案對於執行一連串的邏輯複寫工作非常有幫助。  
  
 指令碼複寫工作提供下列好處：  
  
-   永久保留用以部署複寫拓撲的步驟副本。  
  
-   使用單一指令碼來設定多個訂閱者。  
  
-   讓新資料庫管理員快速上手，讓他們能夠評估、了解、變更或是疑難排解程式碼。  
  
    > [!IMPORTANT]  
    >  指令碼有可能成為安全性弱點的根源；因為它們可以在使用者未查覺或介入的情況下叫用系統函數，且可能以純文字的方式包含安全性認證。 使用指令碼之前，請先檢閱它們是否有安全性問題。  
  
## <a name="creating-replication-scripts"></a>建立複寫指令碼  
 從複寫的觀點來看，指令碼是一系列的一或多個 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 陳述式，其中每個陳述式會執行複寫預存程序。 指令碼是文字檔案，通常具有 .sql 副檔名，可以使用 sqlcmd 公用程式來執行。 當執行指令碼檔案時，公用程式會執行儲存在檔案中的 SQL 陳述式。 同樣地，在 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 專案中可以將指令碼儲存為查詢物件。  
  
 複寫指令碼可以用下列方式建立：  
  
-   手動建立指令碼。  
  
-   使用在複寫精靈中提供的指令碼產生功能或是  
  
-   [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]. 如需詳細資訊，請參閱 [Scripting Replication](../../../relational-databases/replication/scripting-replication.md)。  
  
-   使用 Replication Management Objects (RMO) 以程式設計的方式產生指令碼來建立 RMO 物件。  
  
 當您手動建立複寫指令碼時，請記住下列考量：  
  
-   [!INCLUDE[tsql](../../../includes/tsql-md.md)] 指令碼有一或多個批次。 GO 命令代表批次的結尾。 如果 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 指令碼沒有任何 GO 命令，則會視為單一批次執行。  
  
-   當在單一批次中執行多個複寫預存程序時，在第一個程序之後，在批次中所有後續的程序，前面都必須加上 EXECUTE 關鍵字。  
  
-   在批次中所有預存程序都必須在批次將執行之前編譯。 不過，只要批次已編譯且執行計劃已建立，就可能會發生執行階段錯誤。  
  
-   當建立指令碼以設定複寫時，應該使用 Windows 驗證來避免在指令碼檔案中儲存安全性認證。 如果您必須將認證儲存在指令碼檔案中，則必須維護這個檔案的安全性，使他人無法在未獲授權的情況下擅自存取。  
  
## <a name="sample-replication-script"></a>範例複寫指令碼  
 您可以執行下列指令碼，在伺服器上安裝發行和散發。  
  
```  
-- This script uses sqlcmd scripting variables. They are in the form  
-- $(MyVariable). For information about how to use scripting variables    
-- on the command line and in SQL Server Management Studio, see the   
-- "Executing Replication Scripts" section in the topic  
-- "Programming Replication Using System Stored Procedures".  
  
-- Install the Distributor and the distribution database.  
DECLARE @distributor AS sysname;  
DECLARE @distributionDB AS sysname;  
DECLARE @publisher AS sysname;  
DECLARE @directory AS nvarchar(500);  
DECLARE @publicationDB AS sysname;  
-- Specify the Distributor name.  
SET @distributor = $(DistPubServer);  
-- Specify the distribution database.  
SET @distributionDB = N'distribution';  
-- Specify the Publisher name.  
SET @publisher = $(DistPubServer);  
-- Specify the replication working directory.  
SET @directory = N'\\' + $(DistPubServer) + '\repldata';  
-- Specify the publication database.  
SET @publicationDB = N'AdventureWorks2012';   
  
-- Install the server MYDISTPUB as a Distributor using the defaults,  
-- including autogenerating the distributor password.  
USE master  
EXEC sp_adddistributor @distributor = @distributor;  
  
-- Create a new distribution database using the defaults, including  
-- using Windows Authentication.  
USE master  
EXEC sp_adddistributiondb @database = @distributionDB,   
    @security_mode = 1;  
GO  
  
-- Create a Publisher and enable AdventureWorks2012 for replication.  
-- Add MYDISTPUB as a publisher with MYDISTPUB as a local distributor  
-- and use Windows Authentication.  
DECLARE @distributionDB AS sysname;  
DECLARE @publisher AS sysname;  
-- Specify the distribution database.  
SET @distributionDB = N'distribution';  
-- Specify the Publisher name.  
SET @publisher = $(DistPubServer);  
  
USE [distribution]  
EXEC sp_adddistpublisher @publisher=@publisher,   
    @distribution_db=@distributionDB,   
    @security_mode = 1;  
GO  
  
```  
  
 在本機上可以將這個指令碼儲存為 `instdistpub.sql`，以便在需要時執行或重新執行它。  
  
 前述指令碼包括 **sqlcmd** 指令碼變數，在《[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 線上叢書》中有許多複寫程式碼範例使用這些變數。 指令碼變數是藉由使用 `$(MyVariable)` 語法所定義。 在命令列或是在 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 中，可以將變數值傳遞給指令碼。 如需詳細資訊，請參閱本主題中的下一節「執行複寫指令碼」。  
  
## <a name="executing-replication-scripts"></a>執行複寫指令碼  
 建立之後，複寫指令碼可以用下列其中一種方式來執行：  
  
### <a name="creating-a-sql-query-file-in-sql-server-management-studio"></a>在 SQL Server Management Studio 中建立 SQL 查詢檔案  
 在 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 專案中，可以將複寫 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 指令碼檔案建立成 SQL 查詢檔案。 在寫入指令碼之後，可以為此查詢檔案建立資料庫的連接，而且可以執行指令碼。 如需如何使用 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 建立 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 的詳細資訊，請參閱[查詢與文字編輯器 &#40;SQL Server Management Studio&#41;](../../../ssms/f1-help/database-engine-query-editor-sql-server-management-studio.md)。  
  
 若要使用包含指令碼變數的指令碼，[!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] 必須在 **sqlcmd** 模式下執行。 在 **sqlcmd** 模式中，查詢編輯器會接受 **sqlcmd** 特有的其他語法，例如 `:setvar`，它是用於變數值。 如需 **sqlcmd** 模式的詳細資訊，請參閱 [使用查詢編輯器編輯 SQLCMD 指令碼](../../../ssms/scripting/edit-sqlcmd-scripts-with-query-editor.md)。 在下列指令碼中，`:setvar` 是用以提供 `$(DistPubServer)` 變數的值。  
  
```  
:setvar DistPubServer N'MyPublisherAndDistributor';  
  
-- Install the Distributor and the distribution database.  
DECLARE @distributor AS sysname;  
DECLARE @distributionDB AS sysname;  
DECLARE @publisher AS sysname;  
DECLARE @directory AS nvarchar(500);  
DECLARE @publicationDB AS sysname;  
-- Specify the Distributor name.  
SET @distributor = $(DistPubServer);  
-- Specify the distribution database.  
SET @distributionDB = N'distribution';  
-- Specify the Publisher name.  
SET @publisher = $(DistPubServer);  
  
--  
-- Additional code goes here  
--  
```  
  
### <a name="using-the-sqlcmd-utility-from-the-command-line"></a>從命令列使用 sqlcmd 公用程式  
 下列範例示範如何在命令列使用 [sqlcmd 公用程式](../../../tools/sqlcmd-utility.md)來執行 `instdistpub.sql` 指令碼檔案：  
  
```  
sqlcmd.exe -E -S sqlserverinstance -i C:\instdistpub.sql -o C:\output.log -v DistPubServer="N'MyDistributorAndPublisher'"  
```  
  
 在此範例中，`-E` 參數指出連接到 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 時所使用的 Windows 驗證。 在使用 Windows 驗證時，在指令碼檔案中不需要儲存使用者名稱與密碼。 指令碼檔案的名稱與路徑是由 `-i` 參數所指定，而輸出檔案的名稱則是由 `-o` 參數所指定 (使用這個參數時，[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 的輸出會寫入這個檔案，而不是主控台)。 `sqlcmd` 公用程式可讓您在執行階段，使用 `-v` 參數，將指令碼變數傳遞到 [!INCLUDE[tsql](../../../includes/tsql-md.md)] 指令碼。 在此範例中，`sqlcmd` 會在執行之前，以值 `N'MyDistributorAndPublisher'` 取代指令碼中 `$(DistPubServer)` 的每個執行個體。  
  
> [!NOTE]  
>  `-X` 參數會停用指令碼變數。  
  
### <a name="automating-tasks-in-a-batch-file"></a>以批次檔自動化工作  
 透過使用批次檔，可以在相同的批次檔中，自動化複寫管理工作、複寫同步處理工作以及其他工作。 下列批次檔使用 **sqlcmd** 公用程式，卸除和重新建立訂閱資料庫並加入合併提取訂閱。 接著檔案會叫用合併代理程式以同步處理新訂閱：  
  
```  
REM ----------------------Script to synchronize merge subscription ----------------------  
REM -- Creates subscription database and   
REM -- synchronizes the subscription to MergeSalesPerson.  
REM -- Current computer acts as both Publisher and Subscriber.  
REM -------------------------------------------------------------------------------------  
  
SET Publisher=%computername%  
SET Subscriber=%computername%  
SET PubDb=AdventureWorks  
SET SubDb=AdventureWorksReplica  
SET PubName=AdvWorksSalesOrdersMerge  
  
REM -- Drop and recreate the subscription database at the Subscriber  
sqlcmd /S%Subscriber% /E /Q"USE master IF EXISTS (SELECT * FROM sysdatabases WHERE name='%SubDb%' ) DROP DATABASE %SubDb%"  
sqlcmd /S%Subscriber% /E /Q"USE master CREATE DATABASE %SubDb%"  
  
REM -- Add a pull subscription at the Subscriber  
sqlcmd /S%Subscriber% /E /Q"USE %SubDb% EXEC sp_addmergepullsubscription @publisher = %Publisher%, @publication = %PubName%, @publisher_db = %PubDb%"  
sqlcmd /S%Subscriber% /E /Q"USE %SubDb%  EXEC sp_addmergepullsubscription_agent @publisher = %Publisher%, @publisher_db = %PubDb%, @publication = %PubName%, @subscriber = %Subscriber%, @subscriber_db = %SubDb%, @distributor = %Publisher%"  
  
REM -- This batch file starts the merge agent at the Subscriber to   
REM -- synchronize a pull subscription to a merge publication.  
REM -- The following must be supplied on one line.  
"\Program Files\Microsoft SQL Server\130\COM\REPLMERG.EXE"  -Publisher  %Publisher% -Subscriber  %Subscriber%  -Distributor %Publisher%  -PublisherDB  %PubDb% -SubscriberDB %SubDb% -Publication %PubName% -PublisherSecurityMode 1 -OutputVerboseLevel 1  -Output  -SubscriberSecurityMode 1  -SubscriptionType 1 -DistributorSecurityMode 1 -Validate 3  
  
```  
  
## <a name="scripting-common-replication-tasks"></a>撰寫一般複寫工作的指令碼  
 下列是一些最常使用系統預存程序來編寫指令碼的複寫工作：  
  
-   設定發行和散發  
  
-   修改發行者和散發者屬性  
  
-   停用發行與散發  
  
-   建立發行集及定義發行項  
  
-   刪除發行集及發行項  
  
-   建立提取訂閱  
  
-   修改提取訂閱  
  
-   刪除提取訂閱  
  
-   建立發送訂閱  
  
-   修改提取訂閱  
  
-   刪除發送訂閱  
  
-   同步處理提取訂閱  
  
## <a name="see-also"></a>另請參閱  
 [複寫程式設計概念](../../../relational-databases/replication/concepts/replication-programming-concepts.md)   
 [複寫預存程序 &#40;Transact-SQL&#41;](../../../relational-databases/system-stored-procedures/replication-stored-procedures-transact-sql.md)   
 [編寫複寫指令碼](../../../relational-databases/replication/scripting-replication.md)  
  
