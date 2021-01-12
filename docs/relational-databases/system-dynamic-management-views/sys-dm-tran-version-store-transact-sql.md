---
description: sys.dm_tran_version_store (Transact-SQL)
title: sys.dm_tran_version_store (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/30/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.dm_tran_version_store_TSQL
- sys.dm_tran_version_store
- dm_tran_version_store
- dm_tran_version_store_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.dm_tran_version_store dynamic management view
ms.assetid: 7ab44517-0351-4f91-bdd9-7cf940f03c51
author: WilliamDAssafMSFT
ms.author: wiassaf
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: a85bd124db2e6308f4c09b306e89c6e92a4d7b20
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98101388"
---
# <a name="sysdm_tran_version_store-transact-sql"></a>sys.dm_tran_version_store (Transact-SQL)
[!INCLUDE [SQL Server SQL Database](../../includes/applies-to-version/sql-asdb.md)]

  傳回在版本存放區中顯示所有版本記錄的虛擬資料表。 **sys.dm_tran_version_store** 的執行效率不佳，因為它會查詢整個版本存放區，而版本存放區可能非常龐大。  
  
 每一個版本控制記錄是以二進位資料連同一些追蹤或狀態資訊儲存在一起。 與資料庫資料表中的記錄類似，版本存放區記錄也是儲存在 8192 位元組的頁面中。 如果記錄超出 8192 位元組，該記錄便會分割成兩個不同的記錄。  
  
 因為版本控制記錄是以二進位儲存，所以不同資料庫的不同定序不會造成問題。 使用 **sys.dm_tran_version_store** 以二進位標記法找出舊版的資料列，因為它們存在於版本存放區中。  
  
  
## <a name="syntax"></a>語法  
  
```  
sys.dm_tran_version_store  
```  
  
## <a name="table-returned"></a>傳回的資料表  
  
|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|  
|**transaction_sequence_num**|**bigint**|產生記錄版本之交易的序號。|  
|**version_sequence_num**|**bigint**|版本記錄序號。 這個值在版本產生交易內是唯一的。|  
|**database_id**|**int**|版本控制記錄的資料庫識別碼。|  
|**rowset_id**|**bigint**|記錄的資料列集識別碼。|  
|**status**|**tinyint**|指出版本控制記錄是否已分割成兩個記錄。 如果值設為 0，表示記錄是儲存在一個頁面。 如果值設為 1，表示記錄分割成儲存在兩個不同頁面的兩個記錄。|  
|**min_length_in_bytes**|**smallint**|記錄的最小長度 (以位元組為單位)。|  
|**record_length_first_part_in_bytes**|**smallint**|版本控制記錄第一部份的長度 (以位元組為單位)。|  
|**record_image_first_part**|**varbinary(8000)**|版本記錄第一部份的二進位檔映像。|  
|**record_length_second_part_in_bytes**|**smallint**|版本記錄第二部份的長度 (以位元組為單位)。|  
|**record_image_second_part**|**varbinary(8000)**|版本記錄第二部份的二進位檔映像。|  
  
## <a name="permissions"></a>權限

在上 [!INCLUDE[ssNoVersion_md](../../includes/ssnoversion-md.md)] ，需要 `VIEW SERVER STATE` 許可權。   
在 SQL Database Basic、S0 和 S1 服務目標上，以及針對彈性集區中的資料庫，則 `Server admin` `Azure Active Directory admin` 需要或帳戶。 在所有其他 SQL Database 服務目標上， `VIEW DATABASE STATE` 資料庫中都需要有許可權。   
  
## <a name="examples"></a>範例  
 下列範例使用的測試案例中有四筆並行交易正在 ALLOW_SNAPSHOT_ISOLATION 和 READ_COMMITTED_SNAPSHOT 選項都設為 ON 的資料庫中執行，而每一筆交易都由一個交易序號 (XSN) 識別。 正在執行的交易包括：  
  
-   XSN-57 是在可序列化隔離下執行的更新作業。  
  
-   XSN-58 與 XSN-57 相同。  
  
-   XSN-59 是在快照集隔離下執行的選取作業。  
  
-   XSN-60 與 XSN-59 相同。  
  
 執行以下查詢：  
  
```  
SELECT  
    transaction_sequence_num,  
    version_sequence_num,  
    database_id rowset_id,  
    status,  
    min_length_in_bytes,  
    record_length_first_part_in_bytes,  
    record_image_first_part,  
    record_length_second_part_in_bytes,  
    record_image_second_part  
  FROM sys.dm_tran_version_store;  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
transaction_sequence_num version_sequence_num database_id  
------------------------ -------------------- -----------  
57                      1                    9             
57                      2                    9             
57                      3                    9             
58                      1                    9             
  
rowset_id            status min_length_in_bytes  
-------------------- ------ -------------------  
72057594038321152    0      12                   
72057594038321152    0      12                   
72057594038321152    0      12                   
72057594038386688    0      16                   
  
record_length_first_part_in_bytes  
---------------------------------  
29                                 
29                                 
29                                 
33                                 
  
record_image_first_part                                               
--------------------------------------------------------------------  
0x50000C0073000000010000000200FCB000000001000000270000000000          
0x50000C0073000000020000000200FCB000000001000100270000000000          
0x50000C0073000000030000000200FCB000000001000200270000000000          
0x500010000100000002000000030000000300F800000000000000002E0000000000  
  
record_length_second_part_in_bytes record_image_second_part  
---------------------------------- ------------------------  
0                                  NULL  
0                                  NULL  
0                                  NULL  
0                                  NULL  
```  
  
 輸出中顯示 XSN-57 已從一個資料表建立三個資料列版本，而 XSN-58 則從另一個資料表建立一個資料列版本。  
  
## <a name="see-also"></a>另請參閱  
 [動態管理檢視與函數 &#40;Transact-SQL&#41;](~/relational-databases/system-dynamic-management-views/system-dynamic-management-views.md)   
 [交易相關的動態管理檢視和函數 &#40;Transact-SQL&#41;](../../relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql.md)  
  
  
