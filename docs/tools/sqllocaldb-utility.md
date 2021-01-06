---
title: SqlLocalDB 公用程式
description: SqlLocalDB 公用程式命令列工具可讓使用者與開發人員建立及管理 SQL Server Express LocalDB 的執行個體。
ms.custom: seo-lt-2019
ms.date: 08/09/2016
ms.prod: sql
ms.prod_service: sql-tools
ms.reviewer: ''
ms.technology: tools-other
ms.topic: conceptual
helpviewer_keywords:
- SqlLocalDB utility [SQL Server]
- local database runtime utility
- LocalDB, SqlLocalDB Utility
ms.assetid: d785cdb7-1ea0-4871-bde9-1ae7881190f5
author: markingmyname
ms.author: maghan
ms.openlocfilehash: 65646c84e7e0840e2dfeb3ec4e926deb847237fe
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97643377"
---
# <a name="sqllocaldb-utility"></a>SqlLocalDB 公用程式
[!INCLUDE[sqlserver](../includes/applies-to-version/sqlserver.md)]
  使用 **SqlLocalDB** 公用程式建立 [!INCLUDE[msCoName](../includes/msconame-md.md)][!INCLUDE[ssExpCurrent](../includes/ssexpcurrent-md.md)] **LocalDB** 的執行個體。 **SqlLocalDB** 公用程式 (SqlLocalDB.exe) 是一個簡單的命令列工具，可讓使用者和開發人員建立及管理 [!INCLUDE[ssExpress](../includes/ssexpress-md.md)] **LocalDB** 的執行個體。 如需如何使用 **LocalDB** 的詳細資訊，請參閱 [SQL Server 2016 Express LocalDB](../database-engine/configure-windows/sql-server-express-localdb.md)。  
  
## <a name="syntax"></a>語法  
  
```  
SqlLocalDB.exe   
{  
      [ create   | c ] \<instance-name>  \<instance-version> [-s ]  
    | [ delete   | d ] \<instance-name>  
    | [ start    | s ] \<instance-name>  
    | [ stop     | p ] \<instance-name>  [ -i ] [ -k ]  
    | [ share    | h ] [" <user_SID> " | " <user_account> " ] " \<private-name> " " \<shared-name> "  
    | [ unshare  | u ] " \<shared-name> "  
    | [ info     | i ] \<instance-name>  
    | [ versions | v ]  
    | [ trace    | t ] [ on | off ]  
    | [ help     | -? ]  
}  
```  
  
## <a name="arguments"></a>引數  
 [ **create** | **c** ] *\<instance-name>* *\<instance-version>* [ **-s** ]  
 建立 [!INCLUDE[ssExpress](../includes/ssexpress-md.md)]**LocalDB** 的新執行個體。 **SqlLocalDB** 會使用 *\<instance-version>* 引數所指定之 [!INCLUDE[ssExpress](../includes/ssexpress-md.md)] 二進位檔案的版本。 使用至少一個十進位數的數字格式指定版本號碼。 次要版本號碼 (Service Pack) 為選擇性。 例如，下列兩個版本號碼都可接受：11.0 或 11.0.1186。 電腦上必須安裝指定的版本。 如果未指定，版本號碼會預設為 **SqlLocalDB** 公用程式的版本。 加入 **-s** 會啟動新的 **LocalDB** 執行個體。  
  
 [ **share** | **h** ]  
 使用指定的共用名稱來共用指定的 **LocalDB** 私用執行個體。 如果省略使用者 SID 或帳戶名稱，會預設為目前的使用者。  
  
 [ **unshared** | **u** ]  
 停止共用指定的 **LocalDB** 共用執行個體。  
  
 [ **delete** | **d** ] *\<instance-name>*  
 刪除指定的 [!INCLUDE[ssExpress](../includes/ssexpress-md.md)]**LocalDB** 執行個體。  
  
 [ **start** | **s** ] " *\<instance-name>* "  
 啟動指定的 [!INCLUDE[ssExpress](../includes/ssexpress-md.md)]**LocalDB** 執行個體。 當成功的陳述式傳回 **LocalDB** 的具名管道位址時。  
  
 [ **stop** | **p** ] *\<instance-name>* [ **-i** ] [ **-k** ]  
 停止指定的 [!INCLUDE[ssExpress](../includes/ssexpress-md.md)]**LocalDB** 執行個體。 加入 **-i** 會要求使用 **NOWAIT** 選項關閉此執行個體。 加入 **-k** 會在未經連絡的情況下終止執行個體處理序。  
  
 [ **info** | **i** ] [ *\<instance-name>* ]  
 列出目前使用者擁有的所有 [!INCLUDE[ssExpress](../includes/ssexpress-md.md)]**LocalDB** 執行個體。  
  
 *\<instance-name>* 會傳回指定之 [!INCLUDE[ssExpress](../includes/ssexpress-md.md)]**LocalDB** 執行個體的名稱、版本、狀態 (執行中或已停止)、上次啟動時間，以及 **LocalDB** 的本機管道名稱。  
  
 [ **trace** | **t** ] **on** | **off**  
 **trace on** 會針對目前使用者啟用 **SqlLocalDB** API 呼叫的追蹤。 **trace off** 停用追蹤。  
  
 **-?**  
 傳回每一個 **SqlLocalDB** 選項的簡短描述。  
  
## <a name="remarks"></a>備註  
 <執行個體名稱> 引數必須遵循 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 識別碼的規則，或者必須以雙引號括住。  
  
 不使用任何引數執行 SqlLocalDB 會傳回說明文字。  
  
 啟動以外的作業只能在屬於目前登入之使用者的執行個體上執行。 SQLLOCALDB 執行個體，當共用時，只能由該執行個體的擁有者啟動和停止。  
  
## <a name="examples"></a>範例  
  
### <a name="a-creating-an-instance-of-localdb"></a>A. 建立 LocalDB 的執行個體  
 下列範例會使用 [!INCLUDE[ssExpress](../includes/ssexpress-md.md)]**二進位檔建立名為** 的 `DEPARTMENT` LocalDB [!INCLUDE[ssCurrent](../includes/sscurrent-md.md)] 執行個體，並啟動此執行個體。  
  
```  
SqlLocalDB.exe create "DEPARTMENT" 12.0 -s  
```  
  
### <a name="b-working-with-a-shared-instance-of-localdb"></a>B. 使用 LocalDB 的共用執行個體  
 使用管理員權限開啟命令提示字元。  
  
```  
SqlLocalDB.exe create "DeptLocalDB"  
SqlLocalDB.exe share "DeptLocalDB" "DeptSharedLocalDB"  
SqlLocalDB.exe start "DeptLocalDB"  
SqlLocalDB.exe info "DeptLocalDB"  
REM The previous statement outputs the Instance pipe name for the next step  
sqlcmd -S np:\\.\pipe\LOCALDB#<use your pipe name>\tsql\query  
CREATE LOGIN NewLogin WITH PASSWORD = 'Passw0rd!!@52';   
GO  
CREATE USER NewLogin;  
GO  
EXIT  
```  
  
 使用 **登入執行以下程式碼以連接到** LocalDB `NewLogin` 的共用執行個體。  
  
```  
sqlcmd -S (localdb)\.\DeptSharedLocalDB -U NewLogin -P Passw0rd!!@52  
```  
  
## <a name="see-also"></a>另請參閱  
 [SQL Server 2016 Express LocalDB](../database-engine/configure-windows/sql-server-express-localdb.md)  
[命令列管理工具：SqlLocalDB.exe](../relational-databases/express-localdb-instance-apis/command-line-management-tool-sqllocaldb-exe.md)  
