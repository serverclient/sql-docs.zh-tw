---
description: DBCC CHECKTABLE (Transact-SQL)
title: DBCC CHECKTABLE (Transact-SQL) | Microsoft Docs
ms.date: 11/14/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.custom: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- CHECKTABLE_TSQL
- DBCC_CHECKTABLE_TSQL
- DBCC CHECKTABLE
- CHECKTABLE
dev_langs:
- TSQL
helpviewer_keywords:
- indexed views [SQL Server], DBCC CHECKTABLE
- page integrity checks [SQL Server]
- consistency [SQL Server], tables
- consistency [SQL Server], indexed views
- DBCC CHECKTABLE statement
- integrity [SQL Server]
- low overhead checks
- table integrity checks [SQL Server]
ms.assetid: 0d6cb620-eb58-4745-8587-4133a1b16994
author: pmasl
ms.author: umajay
ms.openlocfilehash: 78a84099b202ca55588e1365b27d80004d2cdaa5
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172610"
---
# <a name="dbcc-checktable-transact-sql"></a>DBCC CHECKTABLE (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

檢查組成資料表或索引檢視表的所有頁面和結構的完整性。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
    
## <a name="syntax"></a>語法    
    
```syntaxsql
DBCC CHECKTABLE     
(    
    table_name | view_name    
    [ , { NOINDEX | index_id }    
     |, { REPAIR_ALLOW_DATA_LOSS | REPAIR_FAST | REPAIR_REBUILD }     
    ]     
)    
    [ WITH     
        { [ ALL_ERRORMSGS ]    
          [ , EXTENDED_LOGICAL_CHECKS ]     
          [ , NO_INFOMSGS ]    
          [ , TABLOCK ]     
          [ , ESTIMATEONLY ]     
          [ , { PHYSICAL_ONLY | DATA_PURITY } ]     
          [ , MAXDOP = number_of_processors ]    
        }    
    ]    
```    
    
[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數
 *table_name* | *view_name*  
 這是要執行完整性檢查的資料表或索引檢視。 資料表或檢視表名稱必須符合[識別碼](../../relational-databases/databases/database-identifiers.md)的規則。  
    
NOINDEX  
 指定不應該執行使用者資料表之非叢集索引的大量檢查。 這會減少整體的執行時間。 NOINDEX 不會影響系統資料表，因為所有系統資料表索引一律會執行完整性檢查。  
    
 *index_id*  
 這是要執行完整性檢查的索引識別 (ID) 碼。 如果指定 *index_id*，DBCC CHECKTABLE 只會執行那個索引及堆積或叢集索引上執行完整性檢查。  
    
REPAIR_ALLOW_DATA_LOSS | REPAIR_FAST | REPAIR_REBUILD  
 指定 DBCC CHECKTABLE 修復找到的錯誤。 若要使用修復選項，資料庫必須在單一使用者模式中。  
    
REPAIR_ALLOW_DATA_LOSS  
 嘗試修復所有報告的錯誤。 這些修復可能會造成某些資料的遺失。  
    
REPAIR_FAST  
 保留語法的目的，只是為了與舊版相容。 不會執行任何修復動作。  
    
REPAIR_REBUILD  
 執行不可能造成資料遺失的修復， 這可包含快速修復 (例如，修復非叢集索引中遺失的資料列) 以及更耗時的修復 (例如，重建索引)。  
 此引數不會修復涉及 FILESTREAM 資料的錯誤。  
    
 > [!NOTE]  
 > 最好不要使用這些 REPAIR 選項。 若要修復錯誤，我們建議您從備份中還原。 修復作業並不考慮資料表或資料表之間的任何條件約束。 如果指定的資料表涉及一或多項條件約束，建議您在修復作業之後執行 `DBCC CHECKCONSTRAINTS`。
 > 如果您必須使用 REPAIR，請執行不含修復選項的 `DBCC CHECKTABLE` 來尋找要使用的修復層級。 如果您要使用 REPAIR_ALLOW_DATA_LOSS 層級，建議您在搭配這個選項執行 `DBCC CHECKTABLE` 之前先備份資料庫。  
    
ALL_ERRORMSGS  
 顯示沒有限制的錯誤數目。 系統預設會顯示所有錯誤訊息。 指定或省略這個選項沒有任何作用。  
    
EXTENDED_LOGICAL_CHECKS  
 如果相容性層級為 100 ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]) 或更高，則會針對索引檢視表、XML 索引和空間索引 (如果有的話) 執行邏輯一致性檢查。  
 如需詳細資訊，請參閱本主題稍後[備註](#remarks)一節中的＜對索引執行邏輯一致性檢查＞。  
    
NO_INFOMSGS  
 隱藏所有參考訊息。  
    
TABLOCK  
 使 DBCC CHECKTABLE 取得共用資料表鎖定，而不使用內部資料庫快照集。 TABLOCK 會使 DBCC CHECKTABLE 在負荷很重的情況下，仍能在資料表上執行得比較快，但當 DBCC CHECKTABLE 在執行中，它會降低資料表上所能使用的並行性。  
    
ESTIMATEONLY  
 顯示使用所有其他指定選項來執行 DBCC CHECKTABLE 所需的 tempdb 估計空間量。  
    
PHYSICAL_ONLY  
 將檢查限制於頁面實體結構、記錄標頭和 B 型樹狀目錄之實體結構的完整性。 這個項目是專為了提供資料表實體一致性的小型負擔檢查而設計的，這項檢查也能夠偵測到損毀的頁面以及可能危及資料的一般硬體失敗。 完整執行 DBCC CHECKTABLE 所需要的時間可能比舊版多許多。 這個行為的原因如下：  
 -   邏輯檢查更完整。  
 -   部分要檢查的基礎結構更複雜。  
 -   導入了許多新的檢查，以包含新的功能。  
   
因此，使用 PHYSICAL_ONLY 選項可能使大型資料表 DBCC CHECKTABLE 的執行階段縮短許多，因此，建議您在實際系統上經常使用它。 我們仍建議您定期完整執行 DBCC CHECKTABLE。 這些執行動作的頻率取決於個別商務和實際執行環境特有的因素。 PHYSICAL_ONLY 一律隱含 NO_INFOMSGS，不允許使用任何修復選項。  
    
 > [!NOTE]  
 > 指定 PHYSICAL_ONLY 會造成 DBCC CHECKTABLE 略過 FILESTREAM 資料的所有檢查。  
    
DATA_PURITY  
 使 DBCC CHECKTABLE 檢查資料表，找出無效或超出範圍的資料行值。 例如，DBCC CHECKTABLE 偵測到資料行具有大於或小於 **datetime** 資料類型可接受範圍的日期和時間值；或者，**decimal** 或近似數值資料類型資料行具有無效的小數位數或有效位數值。  
 預設會啟用資料行值的完整性檢查，而不需要 DATA_PURITY 選項。 對於從舊版 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 升級的資料庫，您可以使用 DBCC CHECKTABLE WITH DATA_PURITY 尋找並更正特定資料表的錯誤，不過，必須先在毫無錯誤的情況下完成對資料庫執行 DBCC CHECKDB WITH DATA_PURITY，否則依預設不對資料表啟用資料行值檢查。 此後，依預設 DBCC CHECKDB 和 DBCC CHECKTABLE 會檢查資料行值的完整性。  
 這個選項報告的驗證錯誤無法使用 DBCC 修復選項更正。 如需手動更正這些錯誤的相關資訊，請參閱知識庫文章 923247：[針對 SQL Server 2005 和更新版本中的 DBCC 錯誤 2570 進行疑難排解](https://support.microsoft.com/kb/923247)。  
 如果指定 PHYSICAL_ONLY，則不會執行資料行完整性檢查。  
    
MAXDOP  
 **適用於**：[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] (從 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] SP2 及更新版本開始)。  
 
 覆寫陳述式之 **sp_configure** 的 **max degree of parallelism** 設定選項。 MAXDOP 可能會超過使用 sp_configure 所設定的值。 如果 MAXDOP 超過使用 Resource Governor 所設定的值，資料庫引擎就會使用 ALTER WORKLOAD GROUP (Transact-SQL) 中所描述的 Resource Governor MAXDOP 值。 當您使用 MAXDOP 查詢提示時，適用所有搭配 max degree of parallelism 組態選項使用的語意規則。 如需詳細資訊，請參閱 [設定 max degree of parallelism 伺服器組態選項](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)。  
    
 > [!NOTE]  
 > 如果 MAXDOP 設定為零，則伺服器會選擇平行處理原則的最大程度。  
    
## <a name="remarks"></a>備註    
    
> [!NOTE]    
> 若要在資料庫的每份資料表上執行 DBCC CHECKTABLE，請使用 [DBCC CHECKDB](../../t-sql/database-console-commands/dbcc-checkdb-transact-sql.md)。    
    
DBCC CHECKTABLE 會確認指定資料表的下列項目：
-   已正確連結索引、in-row、LOB 和資料列溢位資料頁面。    
-   索引符合正確的排序次序。    
-   指標一致。    
-   每個頁面中的資料都是合理的，其中包括計算資料行。    
-   頁面位移合理。    
-   基底資料表的每個資料列在每個非叢集索引中都有相符的資料列，反之亦然。    
-   資料分割資料表或索引中的每個資料列都在正確的資料分割中。    
-   使用 FILESTREAM 將 **varbinary(max)** 資料儲存在檔案系統內時，檔案系統與資料表之間的連結層級一致性。    
    
## <a name="performing-logical-consistency-checks-on-indexes"></a>對索引執行邏輯一致性檢查    
對索引進行的邏輯一致性檢查會根據資料庫的相容性層級而異，如下所示：
-   如果相容性層級為 100 ([!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)]) 或更高：    
    -   除非指定了 NOINDEX，否則 DBCC CHECKTABLE 會針對單一資料表及它的所有非叢集索引進行實體和邏輯一致性檢查。 但是根據預設，XML 索引、空間索引和索引檢視表只會進行實體一致性檢查。    
    -   如果指定了 WITH EXTENDED_LOGICAL_CHECKS，將會針對索引檢視表、XML 索引和空間索引 (如果有的話) 執行邏輯檢查。 根據預設，實體一致性檢查會在邏輯一致性檢查之前執行。 如果也指定了 NOINDEX，則只會執行邏輯檢查。    
         這些邏輯一致性檢查會交叉檢查索引物件的內部索引資料表 (其中包含它所參考的使用者資料表)。 若要尋找外圍的資料列，則會建構內部查詢來執行內部和使用者資料表的完整交集。 執行這個查詢對於效能會有極大的影響，而且無法追蹤其進度。 因此，只有當您懷疑發生了與實體損毀無關的索引問題，或是頁面層級總和檢查碼已經關閉，而且您懷疑發生了資料行層級的硬體損毀時，才建議您指定 WITH EXTENDED_LOGICAL_CHECKS。    
    -   如果此索引為已篩選的索引，DBCC CHECKDB 會執行一致性檢查，以確認索引項目可滿足篩選述詞。   
      
- 從 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 開始，預設不會在保存的計算資料行、UDT 資料行和篩選的索引上執行額外檢查，以避免需耗費大量資源的運算式評估作業。 這項變更可大幅減少對包含這些物件的資料庫執行 CHECKDB 的持續時間。 不過，系統一律會完成這些物件的實體一致性檢查。 只有在指定 EXTENDED_LOGICAL_CHECKS 選項時，才會執行運算式評估，以及執行 EXTENDED_LOGICAL_CHECKS 選項中已存在的邏輯性檢查 (索引檢視表、XML 索引及空間索引)。
-  如果相容性層級為 90 ([!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]) 以下，則除非指定了 NOINDEX，否則 DBCC CHECKTABLE 會針對單一資料表或索引檢視表及其所有非叢集索引和 XML 索引進行實體和邏輯一致性檢查。 不支援空間索引。
    
 **了解資料庫的相容性層級**    
[檢視或變更資料庫的相容性層級](../../relational-databases/databases/view-or-change-the-compatibility-level-of-a-database.md)    
    
## <a name="internal-database-snapshot"></a>內部資料庫快照集    
DBCC CHECKTABLE 會利用內部資料庫快照集來提供執行這些檢查所需具備的交易一致性。 如需詳細資訊，請參閱[檢視資料庫快照集的疏鬆檔案大小 &#40;Transact-SQL&#41;](../../relational-databases/databases/view-the-size-of-the-sparse-file-of-a-database-snapshot-transact-sql.md) 和 [DBCC &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-transact-sql.md) 中的＜DBCC 內部資料庫快照集使用方式＞一節。
如果無法建立快照集，或指定了 TABLOCK，DBCC CHECKTABLE 會取得共用資料表鎖定來取得必要的一致性。
    
> [!NOTE]    
> 如果是針對 tempdb 執行 DBCC CHECKTABLE，它必須取得共用資料表鎖定。 這是因為基於效能的考量，tempdb 並無法使用資料庫快照集。 這表示無法取得必要的交易一致性。    
    
## <a name="checking-and-repairing-filestream-data"></a>檢查及修復 FILESTREAM 資料    
針對資料庫和資料表啟用 FILESTREAM 時，您可以選擇將 **varbinary(max)** 二進位大型物件 (BLOB) 儲存在檔案系統中。 當您針對將 BLOB 儲存於檔案系統中的資料表使用 DBCC CHECKTABLE 時，DBCC 會檢查檔案系統與資料庫之間的連結層級一致性。
例如，如果資料表包含使用 FILESTREAM 屬性的 **varbinary(max)** 資料行，DBCC CHECKTABLE 將會檢查檔案系統目錄和檔案與資料表資料列、資料行和資料行值之間是否有一對一的對應。 如果您指定 REPAIR_ALLOW_DATA_LOSS 選項，則 DBCC CHECKTABLE 可以修復損毀。 若要修復 FILESTREAM 損毀，DBCC 將會刪除遺失檔案系統資料的任何資料表資料列，而且將會刪除未對應到資料表資料列、資料行或資料行值的任何檔案和目錄。
    
## <a name="checking-objects-in-parallel"></a>平行檢查物件    
依預設，DBCC CHECKTABLE 會執行物件的平行檢查。 查詢處理器會自動判斷平行處理原則的程度。 最大平行處理原則程度的設定方式與平行查詢相同。 若要限制 DBCC 檢查所能使用的最大處理器數目，請使用 [sp_configure](../../relational-databases/system-stored-procedures/sp-configure-transact-sql.md)。 如需詳細資訊，請參閱 [設定 max degree of parallelism 伺服器組態選項](../../database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option.md)。
您可以利用追蹤旗標 2528 來停用平行檢查。 如需詳細資訊，請參閱[追蹤旗標 &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql.md)。
    
> [!NOTE]    
> 在 DBCC CHECKTABLE 作業期間，儲存在按位元組排序之使用者定義型別資料行的位元組，必須等於使用者定義型別值的計算序列化。 如果不是這樣，DBCC CHECKTABLE 常式將會報告一致性錯誤。    
    
## <a name="understanding-dbcc-error-messages"></a>了解 DBCC 錯誤訊息    
DBCC CHECKTABLE 命令執行完成之後，[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 錯誤記錄檔中會寫入一則訊息。 如果 DBCC 命令執行成功，該訊息將指出命令已順利完成，並顯示命令執行的時間量。 如果 DBCC 命令由於發生錯誤而在完成檢查之前停止執行，則訊息中會指出命令已經終止，並顯示狀態值以及命令執行的時間量。 下表列出並描述可以包含在訊息中的狀態值。
    
|State|描述|    
|-----------|-----------------|    
|0|已引發錯誤號碼 8930。 這表示中繼資料損毀使 DBCC 命令終止。|    
|1|已引發錯誤號碼 8967。 發生內部 DBCC 錯誤。|    
|2|修復緊急模式資料庫期間發生失敗。|    
|3|這表示中繼資料損毀使 DBCC 命令終止。|    
|4|偵測到判斷提示或存取違規。|    
|5|發生使 DBCC 命令終止的未知錯誤。|    
    
## <a name="error-reporting"></a>錯誤報告    
每當 DBCC CHECKTABLE 偵測到損毀錯誤時，都會在 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] LOG 目錄中建立小型傾印檔案 (`SQLDUMP*nnnn*.txt`)。 當針對 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的執行個體啟用 *功能使用方式* 資料收集及 *錯誤報告* 功能時，這個檔案會自動轉送到 [!INCLUDE[msCoName](../../includes/msconame-md.md)]。 收集的資料是用來提升 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 的功能。
傾印檔案包含 DBCC CHECKTABLE 命令的結果以及其他診斷輸出。 這個檔案具有限制的任意存取控制清單 (DACL)。 存取權會限制為 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 服務帳戶及系統管理員 (sysadmin) 角色的成員。 依預設，系統管理員 (sysadmin) 角色包含 Windows BUILTIN\Administrators 群組及本機系統管理員群組的所有成員。 如果資料收集程序失敗，DBCC 命令不會失敗。
    
## <a name="resolving-errors"></a>解決錯誤    
 如果 DBCC CHECKTABLE 報告任何錯誤，建議您從資料庫備份還原資料庫，而不要使用下列其中一個 REPAIR 選項來執行 REPAIR。 如果沒有任何備份，執行 REPAIR 可以更正所報告的錯誤。 要使用的 REPAIR 選項會指定在報告的錯誤清單尾端。 不過，利用 REPAIR_ALLOW_DATA_LOSS 選項來更正錯誤，可能需要刪除某些頁面，因而也需要刪除某些資料。    
您可以在使用者交易之下執行修復，讓使用者回復已進行的變更。 如果回復修復，資料庫仍會包含錯誤，且必須從備份中還原。 完成所有修復之後，請備份資料庫。
    
## <a name="result-sets"></a>結果集    
DBCC CHECKTABLE 會傳回下列結果集。 如果您只指定了資料表名稱或任何選項，就會傳回相同的結果集。
    
```
DBCC results for 'HumanResources.Employee'.    
There are 288 rows in 13 pages for object 'Employee'.    
DBCC execution completed. If DBCC printed error messages, contact your system administrator.    
```    
    
如果指定了 ESTIMATEONLY 選項，DBCC CHECKTABLE 會傳回下列結果集：
    
```
Estimated TEMPDB space needed for CHECKTABLES (KB)     
--------------------------------------------------     
21    
(1 row(s) affected)    
DBCC execution completed. If DBCC printed error messages, contact your system administrator.    
```    
    
## <a name="permissions"></a>權限    
使用者必須擁有資料表，或是 系統管理員 (sysadmin) 固定伺服器角色、db_owner 固定資料庫角色，或 db_ddladmin 固定資料庫角色的成員。    
    
## <a name="examples"></a>範例    
    
### <a name="a-checking-a-specific-table"></a>A. 檢查特定資料表    
下列範例會檢查 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `HumanResources.Employee` 資料表的資料頁完整性。
    
```sql    
DBCC CHECKTABLE ('HumanResources.Employee');    
GO    
```    
    
### <a name="b-performing-a-low-overhead-check-of-the-table"></a>B. 執行資料表的低負擔檢查    
 下列範例會執行 [!INCLUDE[ssSampleDBnormal](../../includes/sssampledbnormal-md.md)] 資料庫中 `Employee` 資料表的低額外負荷檢查。    
    
```sql    
DBCC CHECKTABLE ('HumanResources.Employee') WITH PHYSICAL_ONLY;    
GO    
```    
    
### <a name="c-checking-a-specific-index"></a>C. 檢查特定索引    
 下列範例會檢查存取 `sys.indexes` 所取得的特定索引。    
    
```sql    
DECLARE @indid int;    
SET @indid = (SELECT index_id     
              FROM sys.indexes    
              WHERE object_id = OBJECT_ID('Production.Product')    
                    AND name = 'AK_Product_Name');    
DBCC CHECKTABLE ('Production.Product',@indid);    
```    
    
## <a name="see-also"></a>另請參閱    
[DBCC &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-transact-sql.md)     
[DBCC CHECKDB &#40;Transact-SQL&#41;](../../t-sql/database-console-commands/dbcc-checkdb-transact-sql.md)    
    
  
