---
description: RESTORE 陳述式 - VERIFYONLY (Transact-SQL)
title: RESTORE VERIFYONLY (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 03/30/2018
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- VERIFYONLY
- RESTORE VERIFYONLY
- VERIFYONLY_TSQL
- RESTORE_VERIFYONLY_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- RESTORE VERIFYONLY statement
- backups [SQL Server], verifying
- verifying backups
- checking backups
ms.assetid: cba3b6a0-b48e-4c94-812b-5b3cbb408bd6
author: MikeRayMSFT
ms.author: mikeray
monikerRange: =azuresqldb-mi-current||>=sql-server-2016||>=sql-server-linux-2017
ms.openlocfilehash: eeae562c4cfbf093d3b7237a044c51084331e8a9
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172240"
---
# <a name="restore-statements---verifyonly-transact-sql"></a>RESTORE 陳述式 - VERIFYONLY (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-asdbmi-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-asdbmi-xxxx-xxx-md.md )]

  驗證備份，但不還原它，同時也會檢查備份組是否已完成，整個備份是否可讀取。 不過，RESTORE VERIFYONLY 並不會嘗試驗證備份磁碟區所包含之資料的結構。 在 [!INCLUDE[msCoName](../../includes/msconame-md.md)] [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 中，已增強 RESTORE VERIFYONLY 的功能，可對資料執行額外檢查，藉此提升偵測到錯誤的機率。 目標是盡可能接近實際的還原作業。 如需詳細資訊，請參閱＜備註＞一節。  

 如果備份有效，[!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] 會傳回成功的訊息。  
  
> [!NOTE]  
>  如需引數的描述，請參閱 [RESTORE 引數 &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-arguments-transact-sql.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
RESTORE VERIFYONLY  
FROM <backup_device> [ ,...n ]  
[ WITH    
 {  
   LOADHISTORY   
  
--Restore Operation Option  
 | MOVE 'logical_file_name_in_backup' TO 'operating_system_file_name'   
          [ ,...n ]   
  
--Backup Set Options  
 | FILE = { backup_set_file_number | @backup_set_file_number }   
 | PASSWORD = { password | @password_variable }   
  
--Media Set Options  
 | MEDIANAME = { media_name | @media_name_variable }   
 | MEDIAPASSWORD = { mediapassword | @mediapassword_variable }  
  
--Error Management Options  
 | { CHECKSUM | NO_CHECKSUM }   
 | { STOP_ON_ERROR | CONTINUE_AFTER_ERROR }  
  
--Monitoring Options  
 | STATS [ = percentage ]   
  
--Tape Options  
 | { REWIND | NOREWIND }   
 | { UNLOAD | NOUNLOAD }    
 } [ ,...n ]  
]  
[;]  
  
<backup_device> ::=  
{   
   { logical_backup_device_name |  
      @logical_backup_device_name_var }  
   | { DISK | TAPE | URL } = { 'physical_backup_device_name' |  
       @physical_backup_device_name_var }   
}  
  
```  
 > [!NOTE] 
> URL 是用來指定 Microsoft Azure Blob 儲存體位置和檔案名稱的格式，從 [!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] SP1 CU2 開始支援。 雖然 Microsoft Azure 儲存體是一項服務，但其實作方式類似於磁碟和磁帶，以便為這三種裝置提供一致且順暢的還原體驗。
 
## <a name="arguments"></a>引數  
 如需 RESTORE VERIFYONLY 引數的描述，請參閱 [RESTORE 引數 &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-arguments-transact-sql.md)。  
  
## <a name="general-remarks"></a>一般備註  
 媒體集或備份組必須包含最起碼的正確資訊，才能夠解譯成 Microsoft Tape Format。 如果沒有包含最起碼的正確資訊，RESTORE VERIFYONLY 會指出備份格式無效，並停止作業。  
  
 RESTORE VERIFYONLY 所執行的檢查包括：  
  
-   備份組是否完整，所有磁碟區是否可讀取。  
  
-   部分資料庫頁面的標頭欄位，如頁面識別碼 (如同即將寫入資料)。  
  
-   總和檢查碼 (如果媒體有總和檢查碼)。  
  
-   檢查目的地裝置的空間是否足夠。  
  
> [!NOTE]  
>  RESTORE VERIFYONLY 無法處理資料庫快照集。 若要在還原作業之前驗證資料庫快照集，您可以執行 DBCC CHECKDB。  
  
> [!NOTE]  
>  使用快照集備份時，RESTORE VERIFYONLY 會確認備份檔案指定的位置是否存在快照集。 快照集備份是 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 的新功能。 如需快照集備份的詳細資訊，請參閱 [Azure 資料庫檔案的檔案快照集備份](../../relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure.md)。  
  
## <a name="security"></a>安全性  
 備份作業可以選擇性地指定媒體集的密碼及 (或) 備份組的密碼。 當在媒體集或備份組上定義密碼時，您必須在 RESTORE 陳述式中，指定一個或多個正確的密碼。 這些密碼可以防止他人利用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 工具，在未獲授權的情況下，在媒體上執行還原作業及附加備份組。 不過，密碼無法防止使用者利用 BACKUP 陳述式的 FORMAT 選項來覆寫媒體。  
  
> [!IMPORTANT]  
>  這個密碼所提供的保護很弱。 這是為了防止已獲授權或未獲授權的使用者使用 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 工具進行不正確的還原。 它無法防止透過其他方式或以取代密碼的方式來讀取備份資料。 [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)]保護備份的最佳做法是將備份磁帶存放在安全位置，或備份至受適當存取控制清單 (ACL) 保護的磁碟檔案中。 ACL 應該設在備份建立所在的根目錄下。  
  
### <a name="permissions"></a>權限  
 從 [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 開始，取得有關備份組或備份裝置的資訊需要 CREATE DATABASE 權限。 如需詳細資訊，請參閱 [GRANT 資料庫權限 &#40;Transact-SQL&#41;](../../t-sql/statements/grant-database-permissions-transact-sql.md)。  
 
## <a name="examples"></a>範例  
 下列範例會驗證來自磁碟的備份。
  
```sql  
RESTORE VERIFYONLY FROM DISK = 'D:\AdventureWorks.bak';
GO
```  
  
## <a name="see-also"></a>另請參閱  
 [BACKUP &#40;Transact-SQL&#41;](../../t-sql/statements/backup-transact-sql.md)   
 [媒體集、媒體家族與備份組 &#40;SQL Server&#41;](../../relational-databases/backup-restore/media-sets-media-families-and-backup-sets-sql-server.md)   
 [RESTORE REWINDONLY &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-rewindonly-transact-sql.md)   
 [RESTORE &#40;Transact-SQL&#41;](../../t-sql/statements/restore-statements-transact-sql.md)   
 [備份記錄與標頭資訊 &#40;SQL Server&#41;](../../relational-databases/backup-restore/backup-history-and-header-information-sql-server.md)  
  
  
