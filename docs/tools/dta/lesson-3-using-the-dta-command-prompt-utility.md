---
title: 使用 dta 命令提示字元公用程式
description: 除 SQL Server Database Engine Tuning Advisor 提供的功能之外，亦請了解 dta 命令提示公用程式提供的功能。
ms.custom: seo-lt-2019
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: sql-tools
ms.technology: performance
ms.topic: conceptual
helpviewer_keywords:
- Database Engine [SQL Server], tutorials
ms.assetid: 30f27f4d-8852-4b12-ba62-57f63e496f1d
author: markingmyname
ms.author: maghan
ms.reviewer: ''
ms.openlocfilehash: 064d7fc2d47cbd4f688d113f60be6080ca90fee1
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97642585"
---
# <a name="lesson-3-using-the-dta-command-prompt-utility"></a>第 3 課：使用 dta 命令提示字元公用程式
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]
除了 Database Engine Tuning Advisor 所提供的功能，**dta** 命令提示字元公用程式還提供額外的功能。  
  
您可以利用您愛用的 XML 工具和 Database Engine Tuning Advisor XML 結構描述來建立這個公用程式的輸入檔。 這個結構描述會在您安裝 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 時一併安裝，並且位於：C:\Program Files (x86)\Microsoft SQL Server\110\Tools\Binn\schemas\sqlserver\2004\07\dta\dtaschema.xsd。  
  
您也可以透過 [Microsoft 網站](https://go.microsoft.com/fwlink/?linkid=43100&clcid=0x409)，線上取得 Database Engine Tuning Advisor XML 結構描述。  
  
在微調選項的設定上，Database Engine Tuning Advisor XML 結構描述非常靈活。 例如，它可讓您進行「假設」分析。 「假設」分析包括針對您要微調的資料庫來指定一組現有的實體設計結構和假設的實體設計結構，再利用 Database Engine Tuning Advisor 來分析它，以了解這個假設的實體設計是否能改進查詢處理效能。 這類分析的好處是既能夠評估新的組態，又免除了實際實作的負擔。 如果假設的實體設計所改進的效能不符需求，您很容易改變它，再分析它，直到產生的結果符合需求的組態出現為止。  
  
此外，在使用 Database Engine Tuning Advisor XML 結構描述和 **dta** 命令提示字元公用程式時，您也可以將 Database Engine Tuning Advisor 功能納入指令碼中，再搭配其他資料庫設計工具來使用它。  
  
如何使用 Database Engine Tuning Advisor 的 XML 輸入功能不在這個課程的範圍內。  
  
 這項工作會帶您逐步啟動 **dta** 公用程式、檢視它的說明，再從命令提示字元之下，使用它來微調工作負載。 它會使用您針對 Database Engine Tuning Advisor 圖形化使用者介面 (GUI) [微調工作負載](lesson-2-using-database-engine-tuning-advisor.md#tuning-a-workload)練習所建立的 MyScript.sql 工作負載  
  
本教學課程使用 AdventureWorks2017 範例資料庫。 基於安全性的考量，依預設，不會安裝範例資料庫。 若要安裝範例資料庫，請參閱＜ [安裝 SQL Server 範例和範例資料庫](../../samples/adventureworks-install-configure.md)＞。  
  
下列工作將帶您逐步開啟命令提示字元、啟動 **dta** 命令提示字元公用程式、檢視其語法說明，以及微調您在 [微調工作負載](./lesson-2-using-database-engine-tuning-advisor.md)中所建立的簡單工作負載 MyScript.sql。  

## <a name="prerequisites"></a>必要條件 

若要完成本教學課程，您需要 SQL Server Management Studio、執行 SQL Server 伺服器的存取權，以及 AdventureWorks 資料庫。

- 安裝 [SQL Server 2017 Developer Edition](https://www.microsoft.com/sql-server/sql-server-downloads)。
- 下載 [AdventureWorks2017 範例資料庫](../../samples/adventureworks-install-configure.md) \(機器翻譯\)。


如需在 SSMS 中還原資料庫的指示，請參閱：[還原資料庫。](../../relational-databases/backup-restore/restore-a-database-backup-using-ssms.md)

  >[!NOTE]
  > 本教學課程適用於熟悉使用 SQL Server Management Studio 與基本資料庫管理工作的使用者。 

## <a name="access-dta-command-prompt-utility-help-menu"></a>存取 DTA 命令提示字元公用程式說明功能表
  
  
1.  在 [開始] 功能表上，依序指向 [所有程式] 和 [附屬應用程式]，再按一下 [命令提示字元]。  
  
2.  在命令提示字元之下，輸入下列字串，再按 ENTER 鍵：  
  
    ```  
    dta -? | more  
    ```  
  
    這個命令的 `| more` 部份是選擇性的。 不過，您可以利用它來逐頁閱讀公用程式的語法說明。 按 ENTER 鍵會在說明文字中，每次前進一行，按空白鍵則會每次前進一頁。  

  ![搭配 DTA Cmd 公用程式使用說明](media/dta-tutorials/dta-cmd-help.png)

## <a name="tune-simple-workload-using-the-dta-command-prompt-utility"></a>使用 DTA 命令提示字元公用程式來微調簡易的工作負載  


  
1.  在命令提示字元之下，導覽到儲存 MyScript.sql 檔的目錄。  
  
2.  在命令提示字元之下，輸入下列字串，再按 ENTER 鍵來執行命令，以及啟動微調工作階段 (請注意，當剖析命令時，這個公用程式會區分大小寫)：  
  
    ```  
    dta -S YourServerName\YourSQLServerInstanceName -E -D AdventureWorks2012 -if MyScript.sql -s MySession2 -of MySession2OutputScript.sql -ox MySession2Output.xml -fa IDX_IV -fp NONE -fk NONE  
    ```  
  
    其中 `-S` 指定您的伺服器名稱以及安裝了 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料庫的 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 執行個體。 `-E` 設定值指定您要使用執行個體的信任連接，當您利用 Windows 網域帳戶來連接時，適合採用這個方式。 `-D` 設定值指定您要微調的資料庫， `-if` 指定工作負載檔案， `-s` 指定工作階段名稱， `-of` 指定工具要將 [!INCLUDE[tsql](../../includes/tsql-md.md)] 建議指令碼寫入其中的檔案， `-ox` 指定工具要將 XML 格式的建議寫入其中的檔案。 最後三個參數依照下列方式來指定微調選項： `-fa IDX_IV` 指定 Database Engine Tuning Advisor 只應考慮加入索引 (叢集和非叢集) 和索引檢視； `-fp NONE` 指定在分析期間，完全不應考慮任何資料分割策略； `-fk NONE` 指定 Database Engine Tuning Advisor 在產生建議時，不需要保留資料庫中任何現有的實體設計結構。  

  ![搭配 DTA 使用 Cmd](media/dta-tutorials/dta-cmd.png)
  
3.  在 Database Engine Tuning Advisor 微調好工作負載之後，它會顯示一則訊息，指出微調工作階段已順利完成。 您可以利用 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 來開啟 MySession2OutputScript.sql 和 MySession2Output.xml 檔，以檢視微調結果。 另外，您也可以在 Database Engine Tuning Advisor GUI 中開啟 MySession2 微調工作階段，依照 [檢視微調建議](./lesson-2-using-database-engine-tuning-advisor.md) 和 [檢視微調報表](./lesson-2-using-database-engine-tuning-advisor.md)中的相同方式來檢視其建議和報表。  
  
 
## <a name="after-you-finish-this-tutorial"></a>完成這個教學課程之後  
完成這個教學課程中的課程之後，請參閱下列主題，以取得有關 Database Engine Tuning Advisor 的詳細資訊：  
  
-   ＜[Database Engine Tuning Advisor](../../relational-databases/performance/database-engine-tuning-advisor.md) ＞提供如何利用這個工具來執行工作的描述。 
-   ＜[dta Utility](../../tools/dta/dta-utility.md) ＞提供有關命令提示字元公用程式的參考資料，以及可用來控制公用程式作業的選擇性 XML 檔案。  
  
若要返回教學課程的起始處，請參閱[教學課程：Database Engine Tuning Advisor](../../tools/dta/tutorial-database-engine-tuning-advisor.md)。  
  
## <a name="see-also"></a>另請參閱  
[Database Engine 教學課程](../../relational-databases/database-engine-tutorials.md)  
