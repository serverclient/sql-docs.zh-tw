---
title: SQL Server 2017 中已淘汰的資料庫引擎功能 | Microsoft Docs
titleSuffix: SQL Server 2019
description: 了解 SQL Server 2017 (14.x) 中仍然可以使用，但由於已淘汰，因此不應在新應用程式中使用的資料庫引擎功能。
ms.custom: seo-lt-2019
ms.date: 12/13/2019
ms.prod: sql
ms.prod_service: high-availability
ms.reviewer: ''
ms.technology: release-landing
ms.topic: conceptual
helpviewer_keywords:
- deprecated features [SQL Server]
- Database Engine [SQL Server], backward compatibility
- deprecation [SQL Server], feature list
ms.assetid: ''
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=sql-server-2017||>=sql-server-linux-2017'
ms.openlocfilehash: 5d9f07f96f83767a84f7ee0128d674f4ab955eed
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98170850"
---
# <a name="deprecated-database-engine-features-in-sql-server-2017"></a>SQL Server 2017 中已取代的資料庫引擎功能

[!INCLUDE[SQL Server 2017](../includes/applies-to-version/sqlserver2017.md)]

  本主題描述 [!INCLUDE[ssDEnoversion](../includes/ssdenoversion-md.md)] 中仍然可用但已被取代的 [!INCLUDE[sssqlv14-md](../includes/sssqlv14-md.md)]功能。 已被取代的功能不應在新應用程式中使用。  
  
當功能標示為已淘汰時，即表示：\

- 功能僅限維護模式。 不會進行任何新的變更，包括與新功能互通的相關變更。
- 為了讓升級更容易，我們盡量不從未來版本中移除已淘汰的功能。 不過，在極少數的情況下，如果該功能限制未來創新，我們可能會選擇從 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] 永久移除這些功能。
- 若要進行新的開發工作，不建議使用已淘汰的功能。      

您可以使用 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] Deprecated Features Object 效能計數器和追蹤事件來監視已被取代之功能的使用。 如需詳細資訊，請參閱 [使用 SQL Server 物件](../relational-databases/performance-monitor/use-sql-server-objects.md)。  

您也可以透過執行下列陳述式來使用這些計數器的值：  

```sql
SELECT * FROM sys.dm_os_performance_counters
WHERE object_name = 'SQLServer:Deprecated Features';
```

> [!NOTE]
> 這份清單與 [!INCLUDE[sssql15-md](../includes/sssql16-md.md)] 清單完全一致。 [!INCLUDE[sssqlv14-md](../includes/sssqlv14-md.md)] 未宣告任何新中已取代或已停止的資料庫引擎功能。

## <a name="features-deprecated-in-the-next-version-of-sql-server"></a>下一版的 SQL Server 中已淘汰的功能

下列 SQL Server 資料庫引擎功能將在下一版的 SQL Server 中淘汰。 請勿在新的開發工作中使用這些功能，並且儘速修改使用這些功能的應用程式。 **功能名稱** 值會作為 ObjectName 出現在追蹤事件中，並在效能計數器與 `sys.dm_os_performance_counters` 中作為執行個體名稱。 [功能識別碼] 值會出現在追蹤事件中當做 ObjectId。

### <a name="back-up-and-restore"></a>備份與還原

| 已被取代的功能 | 取代 | 功能名稱 | 功能識別碼 |
|--------------------|-------------|--------------|------------|
| RESTORE { DATABASE &#124; LOG } WITH [MEDIA]PASSWORD 繼續被取代。<br /><br />BACKUP { DATABASE &#124; LOG } WITH PASSWORD 和 BACKUP { DATABASE &#124; LOG } WITH MEDIAPASSWORD 已停用。 | 無。 | BACKUP DATABASE 或 LOG WITH PASSWORD<br /><br />BACKUP DATABASE 或 LOG WITH MEDIAPASSWORD | 104<br /><br /> 103 |

### <a name="compatibility-levels"></a>相容性層級

| 已被取代的功能 | 取代 | 功能名稱 | 功能識別碼 |
|--------------------|-------------|--------------|------------|
從 100 版 (SQL Server 2008 和 SQL Server 2008 R2) 升級。 | 當 SQL Server 版本不再受到[支援](/lifecycle/products/?products=sql-server)時，相關聯的資料庫相容性層級會標示為已淘汰。 不過，為了讓升級更容易，我們會盡可能繼續支援任何支援資料庫相容性層級上已認證的應用程式。 如需相容性層級的詳細資訊，請參閱 [ALTER DATABASE 相容性層級 &#40;Transact-SQL&#41;](../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。 | 資料庫相容性層級 100 | 108 |

### <a name="database-objects"></a>資料庫物件

| 已被取代的功能 | 取代 | 功能名稱 | 功能識別碼 |
|--------------------|-------------|--------------|------------|
| 從觸發程序傳回結果集的能力 | None | 從觸發程序傳回結果 | 12 |

### <a name="encryption"></a>加密

| 已被取代的功能 | 取代 | 功能名稱 | 功能識別碼 |
|--------------------|-------------|--------------|------------|
| 使用 RC4 或 RC4_128 的加密已被取代，並預計在下一版移除。 不會淘汰解密 RC4 和 RC4_128 的功能。 | 請使用其他加密演算法，例如 AES。 | 已被取代的加密演算法 | 253 |
| 使用 MD2、MD4、MD5、SHA 和 SHA1 已淘汰。 | 請改用 SHA2_256 或 SHA2_512。 較舊的演算法會繼續運作，但會引發淘汰事件。 |已被取代的雜湊演算法 | None |

### <a name="remote-servers"></a>遠端伺服器

| 已被取代的功能 | 取代 | 功能名稱 | 功能識別碼 |
|--------------------|-------------|--------------|------------|
| sp_addremotelogin<br /><br />sp_addserver <br /><br /> sp_dropremotelogin <br /><br /> sp_helpremotelogin <br /><br /> sp_remoteoption|使用連結的伺服器取代遠端伺服器。 sp_addserver 只能搭配本機選項使用。 | sp_addremotelogin<br /><br />sp_addserver <br /><br /> sp_dropremotelogin <br /><br /> sp_helpremotelogin <br /><br > sp_remoteoption | 70 <br /><br /> 69 <br /><br /> 71 <br /><br /> 72 <br /><br /> 73 |
| \@\@remserver | 使用連結的伺服器取代遠端伺服器。 | None | None |
| SET REMOTE_PROC_TRANSACTIONS|使用連結的伺服器取代遠端伺服器。 | SET REMOTE_PROC_TRANSACTIONS | 110 |

### <a name="transact-sql"></a>Transact-SQL

| 已被取代的功能 | 取代 | 功能名稱 | 功能識別碼 |
|--------------------|-------------|--------------|------------|
| 用於 **SET ROWCOUNT** 、 **INSERT**, **UPDATE** 陳述式的 **DELETE** | TOP 關鍵字 | SET ROWCOUNT | 109 |
| 沒有括號的 HOLDLOCK 資料表提示。 | 請使用有括號的 HOLDLOCK。 | 沒有括號的 HOLDLOCK 資料表提示。 | 167 |

## <a name="features-deprecated-in-a-future-version-of-sql-server"></a>SQL Server 的未來版本中已淘汰的功能

下一版 SQL Server 中會支援下列 SQL Server 資料庫引擎功能。 確實的 SQL Server 版本尚未決定。

### <a name="back-up-and-restore"></a>備份及還原

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| BACKUP { DATABASE &#124; LOG } TO TAPE <br /><br /> BACKUP { DATABASE &#124; LOG } TO *磁帶裝置*|BACKUP { DATABASE &#124; LOG } TO DISK <br /><br /> BACKUP { DATABASE &#124; LOG } TO *磁碟裝置* | BACKUP DATABASE 或 LOG TO TAPE |
| sp_addumpdevice '**tape**' | sp_addumpdevice '**disk**' | ADDING TAPE DEVICE |
| sp_helpdevice | sys.backup_devices | sp_helpdevice |

### <a name="compatibility-levels"></a>相容性層級

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| sp_dbcmptlevel|ALTER DATABASE ...SET COMPATIBILITY_LEVEL。 如需詳細資訊，請參閱 [ALTER DATABASE 相容性層級 &#40;Transact-SQL&#41;](../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。 | sp_dbcmptlevel |
| 資料庫相容性層級 110 和 120。 | 請針對將來的版本規劃升級資料庫和應用程式。 不過，為了讓升級更容易，我們會盡可能繼續支援任何支援資料庫相容性層級上已認證的應用程式。 如需相容性層級的詳細資訊，請參閱 [ALTER DATABASE 相容性層級 &#40;Transact-SQL&#41;](../t-sql/statements/alter-database-transact-sql-compatibility-level.md)。 | 資料庫相容性層級 110 <br /><br /> 資料庫相容性層級 120 |

### <a name="collations"></a>定序

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| Korean_Wansung_Unicode <br /><br /> Lithuanian_Classic <br /><br /> SQL_AltDiction_CP1253_CS_AS | 無。 這些定序存在於 SQL Server 2005 (9.x)，但無法透過 fn_helpcollations 顯示出來。 | Korean_Wansung_Unicode <br /><br /> Lithuanian_Classic <br /><br /> SQL_AltDiction_CP1253_CS_AS |
| Hindi <br /><br /> 馬其頓文 | 這些定序存在於 SQL Server 2005 (9.x) 和更高版本，但無法透過 fn_helpcollations 顯示出來。 請改用 Macedonian_FYROM_90 和 Indic_General_90。|Hindi <br /><br /> 馬其頓文 |
| Azeri_Latin_90 <br /><br /> Azeri_Cyrilllic_90 | Azeri_Latin_100 <br /><br /> Azeri_Cyrilllic_100 | Azeri_Latin_90 <br /><br /> Azeri_Cyrilllic_90 |

### <a name="data-types"></a>資料類型

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| sp_addtype <br /><br /> sp_droptype|CREATE TYPE<br /><br /> DROP TYPE | sp_addtype<br /><br /> sp_droptype |
| **timestamp** 資料類型的 **rowversion** 語法 | **rowversion** 資料類型語法 | timestamp |
| 能夠將 null 值插入 **timestamp** 資料行中。 | 請改用 DEFAULT。 | INSERT NULL 到 TIMESTAMP 資料行中 |
| 'text in row' 資料表選項|使用 **varchar(max)** 、 **nvarchar(max)** 和 **varbinary(max)** 資料類型。 如需詳細資訊，請參閱 [sp_tableoption &#40;Transact-SQL&#41;](../relational-databases/system-stored-procedures/sp-tableoption-transact-sql.md)。|Text in row 資料表選項 |
| 資料類型：<br /><br /> **text**<br /><br /> **ntext**<br /><br /> **image**|使用 **varchar(max)** 、 **nvarchar(max)** 和 **varbinary(max)** 資料類型。|資料類型：**text**、**ntext** 或 **image** |

### <a name="database-management"></a>資料庫管理

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| sp_attach_db <br /><br /> sp_attach_single_file_db|具有 FOR ATTACH 選項的 CREATE DATABASE 陳述式。 當一個或多個記錄檔有新位置時，若要重建多個記錄檔，請使用 FOR ATTACH_REBUILD_LOG 選項。 | sp_attach_db <br /><br /> sp_attach_single_file_db |
| sp_certify_removable<br /><br /> sp_create_removable|sp_detach_db|sp_certify_removable<br /><br /> sp_create_removable |
| sp_dbremove | DROP DATABASE | sp_dbremove |
| sp_renamedb | ALTER DATABASE 中的 MODIFY NAME | sp_renamedb |

### <a name="database-objects"></a>資料庫物件

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| CREATE DEFAULT<br /><br /> DROP DEFAULT<br /><br /> sp_bindefault<br /><br /> sp_unbindefault|CREATE TABLE 和 ALTER TABLE 中的 DEFAULT 關鍵字|CREATE_DROP_DEFAULT<br /><br /> sp_bindefault<br /><br /> sp_unbindefault |
| CREATE RULE<br /><br /> DROP RULE<br /><br /> sp_bindrule<br /><br /> sp_unbindrule | CREATE TABLE 和 ALTER TABLE 中的 CHECK 關鍵字 | CREATE_DROP_RULE<br /><br /> sp_bindrule<br /><br /> sp_unbindrule |
| sp_change_users_login | 請使用 ALTER USER。 | sp_change_users_login |
| sp_depends | sys.dm_sql_referencing_entities 和 sys.dm_sql_referenced_entities | sp_depends |
| sp_getbindtoken | 使用 MARS 或分散式交易。 | sp_getbindtoken |

### <a name="database-options"></a>資料庫選項

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| sp_bindsession | 使用 MARS 或分散式交易。 | sp_bindsession |
| sp_resetstatus | ALTER DATABASE SET { ONLINE &#124; EMERGENCY } | sp_resetstatus
| ALTER DATABASE 的 TORN_PAGE_DETECTION 選項|ALTER DATABASE 的 PAGE_VERIFY TORN_PAGE_DETECTION 選項 | ALTER DATABASE WITH TORN_PAGE_DETECTION |

### <a name="dbcc"></a>DBCC

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| DBCC DBREINDEX|ALTER INDEX 的 REBUILD 選項。 | DBCC DBREINDEX |
| DBCC INDEXDEFRAG|ALTER INDEX 的 REORGANIZE 選項。 | DBCC INDEXDEFRAG |
| DBCC SHOWCONTIG|sys.dm_db_index_physical_stats | DBCC SHOWCONTIG |
| DBCC PINTABLE<br /><br /> DBCC UNPINTABLE | 這個選項無效。 | DBCC [UN]PINTABLE |

### <a name="extended-properties"></a>擴充屬性

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| Level0type = 'type' 和 Level0type='USER'，將擴充屬性加入至層級 1 或層級 2 類型物件中。 | 使用 Level0type = 'USER' 只會將擴充屬性直接加入至使用者或角色中。<br /><br /> 使用 Level0type = 'SCHEMA' 可將擴充屬性加入至層級 1 類型 (例如 TABLE 或 VIEW)，或是層級 2 類型 (例如 COLUMN 或 TRIGGER)。 如需詳細資訊，請參閱 [sp_addextendedproperty &#40;Transact-SQL&#41;](../relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql.md)。 | EXTPROP_LEVEL0TYPE<br /><br /> EXTPROP_LEVEL0USER |

### <a name="extended-stored-procedures"></a>擴充預存程序

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| xp_grantlogin<br /><br /> xp_revokelogin<br /><br /> xp_loginConfig|使用 CREATE LOGIN<br /><br /> 使用 SERVERPROPERTY 的 DROP LOGIN IsIntegratedSecurityOnly 引數|xp_grantlogin<br /><br /> xp_revokelogin<br /><br /> xp_loginConfig |

### <a name="extended-stored-procedures-programming"></a>擴充預存程序程式設計

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| srv_alloc<br /><br /> srv_convert<br /><br /> srv_describe<br /><br /> srv_getbindtoken<br /><br /> srv_got_attention<br /><br /> srv_message_handler<br /><br /> srv_paramdata<br /><br /> srv_paraminfo<br /><br /> srv_paramlen<br /><br /> srv_parammaxlen<br /><br /> srv_paramname<br /><br /> srv_paramnumber<br /><br /> srv_paramset<br /><br /> srv_paramsetoutput<br /><br /> srv_paramstatus<br /><br /> srv_paramtype<br /><br /> srv_pfield<br /><br /> srv_pfieldex<br /><br /> srv_rpcdb<br /><br /> srv_rpcname<br /><br /> srv_rpcnumber<br /><br /> srv_rpcoptions<br /><br /> srv_rpcowner<br /><br /> srv_rpcparams<br /><br /> srv_senddone<br /><br /> srv_sendmsg<br /><br /> srv_sendrow<br /><br /> srv_setcoldata<br /><br /> srv_setcollen<br /><br /> srv_setutype<br /><br /> srv_willconvert<br /><br /> srv_wsendmsg | 請改用 CLR 整合。 | XP_API |
| sp_addextendedproc<br /><br /> sp_dropextendedproc<br /><br /> sp_helpextendedproc | 請改用 CLR 整合。 | sp_addextendedproc<br /><br /> sp_dropextendedproc<br /><br /> sp_helpextendedproc |
| xp_grantlogin<br /><br /> xp_revokelogin<br /><br /> xp_loginConfig|使用 CREATE LOGIN<br /><br /> 使用 SERVERPROPERTY 的 DROP LOGIN IsIntegratedSecurityOnly 引數 | xp_grantlogin<br /><br /> xp_revokelogin<br /><br /> xp_loginConfig |

### <a name="high-availability"></a>高可用性

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| 資料庫鏡像 | Always On 可用性群組<br /><br /> 如果 SQL Server 版本不支援 Always On 可用性群組，請使用記錄傳送。 | DATABASE_MIRRORING |

### <a name="index-options"></a>索引選項

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| sp_indexoption | ALTER INDEX | sp_indexoption |
| CREATE TABLE、ALTER TABLE 或 CREATE INDEX 語法，但是選項周圍沒有括號。 | 請重寫陳述式來使用目前的語法。 | INDEX_OPTION |

### <a name="instance-options"></a>執行個體選項

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| sp_configure 選項 'allow updates'|系統資料表不再可更新， 此設定無效。 | sp_configure 'allow updates' |
| sp_configure 選項： <br /><br /> 'locks' <br /><br /> 'open objects'<br /><br /> 'set working set size' | 現在會自動設定。 此設定無效。 | sp_configure 'locks'<br /><br /> sp_configure 'open objects'<br /><br /> sp_configure 'set working set size' |
| sp_configure 選項 'priority boost' | 系統資料表不再可更新， 此設定無效。 請改用 Windows start /high ... program.exe 選項。 | sp_configure 'priority boost' |
| sp_configure 選項 'remote proc trans' | 系統資料表不再可更新， 此設定無效。 | sp_configure 'remote proc trans' |

### <a name="linked-servers"></a>連結的伺服器

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| 為連結的伺服器指定 SQLOLEDB 提供者。 | SQL Server Native Client (SQLNCLI) | 連結的伺服器適用的 SQLOLEDDB |

### <a name="metadata"></a>中繼資料

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| FILE_ID<br /><br /> INDEXKEY_PROPERTY | FILE_IDEX<br /><br /> sys.index_columns | FILE_ID<br /><br /> INDEXKEY_PROPERTY |

### <a name="native-xml-web-services"></a>原生 XML Web Service

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| 具有 FOR SOAP 選項的 CREATE ENDPOINT 或 ALTER ENDPOINT 陳述式。<br /><br /> sys.endpoint_webmethods<br /><br /> sys.soap_endpoints|請改用 Windows Communications Foundation (WCF) 或 ASP.NET。 | CREATE/ALTER ENDPOINT<br /><br /> sys.endpoint_webmethods<br /><br /> EXT_soap_endpoints<br /><br /> sys.soap_endpoints |

### <a name="other"></a>其他

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| DB-Library<br /><br />Embedded SQL for C|雖然資料庫引擎仍支援使用 DB-Library 和內嵌式 SQL API 的現有應用程式連線，但不包含要在使用這些 API 的應用程式上執行程式設計工作所需檔案或文件。 SQL Server 資料庫引擎未來版本將卸除對 DB-Library 或內嵌式 SQL 應用程式連線的支援。 請勿使用 DB-Library 或內嵌式 SQL 來開發新的應用程式。 在修改現有的應用程式時，請移除對 DB-Library 或內嵌式 SQL 的相依性。 如果不想要使用這些 API，請使用 SQLClient 命名空間或 API (例如 ODBC)。 SQL Server 2019 (15.x) 不包含執行這些應用程式所需的 DB-Library DLL。 若要執行 DB-Library 或內嵌式 SQL 應用程式，則必須可從 SQL Server 6.5 版、SQL Server 7.0 或 SQL Server 2000 (8.x) 使用 DB-Library DLL。 | None |

### <a name="security"></a>安全性

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| ALTER LOGIN WITH SET CREDENTIAL 語法 | 以新的 ALTER LOGIN ADD 和 DROP CREDENTIAL 語法取代 | ALTER LOGIN WITH SET CREDENTIAL |
| sp_addapprole<br /><br /> sp_dropapprole | CREATE APPLICATION ROLE<br /><br /> DROP APPLICATION ROLE | sp_addapprole<br /><br /> sp_dropapprole |
| sp_addlogin<br /><br /> sp_droplogin | CREATE LOGIN<br /><br /> DROP LOGIN|sp_addlogin<br /><br /> sp_droplogin |
| sp_adduser<br /><br /> sp_dropuser | CREATE USER<br /><br /> DROP USER|sp_adduser<br /><br /> sp_dropuser |
| sp_grantdbaccess<br /><br /> sp_revokedbaccess|CREATE USER<br /><br /> DROP USER|sp_grantdbaccess<br /><br /> sp_revokedbaccess |
| sp_addrole<br /><br /> sp_droprole | CREATE ROLE<br /><br /> DROP ROLE|sp_addrole<br /><br /> sp_droprole |
| sp_approlepassword<br /><br /> sp_password | ALTER APPLICATION ROLE<br /><br /> ALTER LOGIN|sp_approlepassword<br /><br /> sp_password |
| sp_changedbowner|ALTER AUTHORIZATION | sp_changedbowner |
| sp_changeobjectowner|ALTER SCHEMA 或 ALTER AUTHORIZATION | sp_changeobjectowner |
| sp_control_dbmasterkey_password | 主要金鑰必須存在且密碼必須正確。|sp_control_dbmasterkey_password |
| sp_defaultdb<br /><br /> sp_defaultlanguage | ALTER LOGIN|sp_defaultdb<br /><br /> sp_defaultlanguage |
| sp_denylogin<br /><br /> sp_grantlogin<br /><br /> sp_revokelogin|ALTER LOGIN DISABLE<br /><br /> CREATE LOGIN<br /><br /> DROP LOGIN|sp_denylogin<br /><br /> sp_grantlogin<br /><br /> sp_revokelogin |
| USER_ID|DATABASE_PRINCIPAL_ID | USER_ID |
| sp_srvrolepermission<br /><br /> sp_dbfixedrolepermission|這些預存程序會傳回 [!INCLUDE[ssVersion2000](../includes/ssversion2000-md.md)]中的正確資訊。 這項輸出未反映 SQL Server 2008 中所實作權限階層的變更。 如需詳細資訊，請參閱 [固定伺服器角色的權限](https://msdn.microsoft.com/library/ms175892\(SQL.100\).aspx)。 | sp_srvrolepermission<br /><br /> sp_dbfixedrolepermission |
| GRANT ALL<br /><br /> DENY ALL<br /><br /> REVOKE ALL|GRANT、DENY 和 REVOKE 等特定權限。|ALL 權限 |
| PERMISSIONS 內建函數 | 請改為查詢 sys.fn_my_permissions。 | PERMISSIONS |
| SETUSER | EXECUTE AS | SETUSER |
| RC4 和 DESX 加密演算法|請使用其他演算法，例如 AES。 | DESX 演算法 |

### <a name="server-configuration-options"></a>伺服器組態選項

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| c2 audit 選項、default trace enabled 選項<br /><br /> default trace enabled 選項 | [通用條件符合已啟用伺服器組態選項](../database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option.md)<br /><br /> [擴充事件](../relational-databases/extended-events/extended-events.md) | sp_configure 'c2 audit mode'<br /><br />sp_configure 'default trace enabled' |

### <a name="smo-classes"></a>SMO 類別

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| *Microsoft.SQLServer.Management.Smo.Information* 類別<br /><br />*Microsoft.SQLServer.Management.Smo.Settings* 類別<br /><br />*Microsoft.SQLServer.Management.Smo.DatabaseOptions* 類別<br /><br />*Microsoft.SqlServer.Management.Smo.DatabaseDdlTrigger.NotForReplication* 屬性 | *Microsoft.SqlServer.Management.Smo.Server* 類別<br /><br />**Microsoft.SqlServer.Management.Smo.Server* 類別<br /><br />* Microsoft.SqlServer. Management.Smo.Database* 類別<br /><br />None | None |

### <a name="sql-server-agent"></a>SQL Server Agent

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| **net send** 通知<br /><br />呼叫器通知 | 電子郵件通知<br /><br />電子郵件通知 | None |

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| SQL Server Management Studio 中的 [方案總管] 整合 | | None |

### <a name="system-stored-procedures-and-functions"></a>系統預存程序和函式

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| sp_db_increased_partitions | 無。 SQL Server 2019 (15.x) 預設可支援增加的分割區。 | sp_db_increased_partitions |
| fn_virtualservernodes<br /><br />fn_servershareddrives | sys.dm_os_cluster_nodes<br /><br />sys.dm_io_cluster_shared_drives | fn_virtualservernodes<br /><br /> fn_servershareddrives |
| fn_get_sql | sys.dm_exec_sql_text | fn_get_sql |
| sp_lock | sys.dm_tran_locks | sp_lock |

### <a name="system-tables"></a>系統資料表

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| sysaltfiles<br /><br />syscacheobjects<br /><br />syscolumns<br /><br />syscomments<br /><br />sysconfigures<br /><br />sysconstraints<br /><br />syscurconfigs<br /><br />sysdatabases<br /><br />sysdepends<br /><br />sysdevices<br /><br />sysfilegroups<br /><br />sysfiles<br /><br />sysforeignkeys<br /><br />sysfulltextcatalogs<br /><br />sysindexes<br /><br />sysindexkeys<br /><br />syslockinfo<br /><br />syslogins<br /><br />sysmembers<br /><br />sysmessages<br /><br />sysobjects<br /><br />sysoledbusers<br /><br />sysopentapes<br /><br />sysperfinfo<br /><br />syspermissions<br /><br />sysprocesses<br /><br />sysprotects<br /><br />sysreferences<br /><br />sysremotelogins<br /><br />sysservers<br /><br />systypes<br /><br />sysusers|相容性檢視。 如需詳細資訊，請參閱[相容性檢視 &#40;Transact-SQL&#41;](~/relational-databases/system-compatibility-views/system-compatibility-views-transact-sql.md)。<br /><br />**重要：** 相容性檢視不會公開 SQL Server 2005 (9.x) 中所引進功能的中繼資料。 我們建議您升級應用程式來使用目錄檢視。 如需詳細資訊，請參閱[目錄檢視 &#40;Transact-SQL&#41;](../relational-databases/system-catalog-views/catalog-views-transact-sql.md)。 | sysaltfiles<br /><br />syscacheobjects<br /><br />syscolumns<br /><br />syscomments<br /><br />sysconfigures<br /><br />sysconstraints<br /><br />syscurconfigs<br /><br />sysdatabases<br /><br />sysdepends<br /><br />sysdevices<br /><br />sysfilegroups<br /><br />sysfiles<br /><br />sysforeignkeys<br /><br />sysfulltextcatalogs<br /><br />sysindexes<br /><br />sysindexkeys<br /><br />syslockinfo<br /><br />syslogins<br /><br />sysmembers<br /><br />sysmessages<br /><br />sysobjects<br /><br />sysoledbusers<br /><br />sysopentapes<br /><br />sysperfinfo<br /><br />syspermissions<br /><br />sysprocesses<br /><br />sysprotects<br /><br />sysreferences<br /><br />sysremotelogins<br /><br />sysservers<br /><br />systypes<br /><br />sysusers |
| sys.numbered_procedures<br /><br />sys.numbered_procedure_parameters | None | numbered_procedures<br /><br />numbered_procedure_parameters |

### <a name="sql-trace-stored-procedures-functions-and-catalog-views"></a>SQL 追蹤預存程序、函數和目錄檢視

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| sp_trace_create<br /><br />sp_trace_setevent<br /><br />sp_trace_setfilter<br /><br />sp_trace_setstatus<br /><br />fn_trace_geteventinfo<br /><br />fn_trace_getfilterinfo<br /><br />fn_trace_getinfo<br /><br />fn_trace_gettable<br /><br />sys.traces<br /><br />sys.trace_events<br /><br />sys.trace_event_bindings<br /><br />sys.trace_categories<br /><br />sys.trace_columns<br /><br />sys.trace_subclass_values|[擴充事件](../relational-databases/extended-events/extended-events.md) | sp_trace_create<br /><br />sp_trace_setevent<br /><br />sp_trace_setfilter<br /><br />sp_trace_setstatus<br /><br />fn_trace_geteventinfo<br /><br />fn_trace_getfilterinfo<br /><br />fn_trace_getinfo<br /><br />fn_trace_gettable<br /><br />sys.traces<br /><br />sys.trace_events<br /><br />sys.trace_event_bindings<br /><br />sys.trace_categories<br /><br />sys.trace_columns<br /><br />sys.trace_subclass_values |

### <a name="system-views"></a>系統檢視表

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| sys.sql_dependencies|sys.sql_expression_dependencies|sys.sql_dependencies |

### <a name="table-compression"></a>資料表壓縮

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| 使用 Vardecimal 儲存格式。 | Vardecimal 儲存格式已被取代。 SQL Server 2019 (15.x) 資料壓縮會壓縮十進位值及其他資料類型。 我們建議您使用資料壓縮，而不要使用 Vardecimal 儲存格式。 | Vardecimal 儲存格式 |
| 使用 sp_db_vardecimal_storage_format 程序。|Vardecimal 儲存格式已被取代。 SQL Server 2019 (15.x) 資料壓縮會壓縮十進位值及其他資料類型。 我們建議您使用資料壓縮，而不要使用 Vardecimal 儲存格式。 | sp_db_vardecimal_storage_format |
| 使用 sp_estimated_rowsize_reduction_for_vardecimal 程序。|請改用資料壓縮和 sp_estimate_data_compression_savings 程序。 |sp_estimated_rowsize_reduction_for_vardecimal |

### <a name="text-pointers"></a>文字指標

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| WRITETEXT<br /><br />UPDATETEXT<br /><br />READTEXT|None|UPDATETEXT 或 WRITETEXT<br /><br />READTEXT |
| TEXTPTR()<br /><br />TEXTVALID() | None | TEXTPTR<br /><br />TEXTVALID |

### <a name="transact-sql"></a>Transact-SQL

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| :: 函數呼叫順序 | 取代為 SELECT *column_list* FROM sys.\<*function_name*>()。<br /><br />例如，以 `SELECT * FROM ::fn_virtualfilestats(2,1)`取代 `SELECT * FROM sys.fn_virtualfilestats(2,1)`。 | '::' 函數呼叫語法 |
| 三部分和四部分資料行參考。 | 兩部分名稱是符合標準的行為。|兩部分以上的資料行名稱 |
| 加上引號的字串，在 SELECT 清單中當做運算式的資料行別名使用：<br /><br />'*string_alias*' = *expression* | *expression* [AS] *column_alias*<br /><br />*expression* [AS] [*column_alias*]<br /><br />*expression* [AS] "*column_alias*"<br /><br />*expression* [AS] '*column_alias*'<br /><br />*column_alias* = *expression* | 當做資料行別名的字串常值 |
| 編號程序。 | 無。 請勿使用。 | ProcNums |
| DROP INDEX 中的 *table_name.index_name* 語法|DROP INDEX 中的 *index_name* ON *table_name* 語法。|具有兩部分名稱的 DROP INDEX |
| 不是以分號結束 Transact-SQL 陳述式。|以分號 ( ; ) 結束 Transact-SQL 陳述式。 | None |
| GROUP BY ALL|搭配 UNION 或衍生資料表使用自訂的依案例方案。 | GROUP BY ALL |
| 當做 DML 陳述式中之資料行名稱的 ROWGUIDCOL。|使用 $rowguid。|ROWGUIDCOL |
| 當做 DML 陳述式中之資料行名稱的 IDENTITYCOL。|使用 $identity。|IDENTITYCOL |
| 使用 # 和 ## 當做暫存資料表和暫存預存程序名稱。 | 請至少使用一個其他字元。|做為暫存資料表和預存程序名稱的 '#' 和 '##'。
| 使用 \@、\@\@ 或 \@\@ 作為 Transact-SQL 識別碼。 | 請勿使用 \@ 或 \@\@，或是以 \@\@ 開頭的名稱，作為識別碼。 | '\@' 和以 '\@\@' 開頭的名稱作為 Transact-SQL 識別碼 |
| 使用 DEFAULT 關鍵字當做預設值。|請勿使用 DEFAULT 字當做預設值。 | 當做預設值的 DEFAULT 關鍵字 |
| 使用空格當做資料表提示之間的分隔符號。|使用逗號來分隔資料表提示。 | 沒有逗號的多個資料表提示 |
| 在 90 相容性模式中，彙總索引檢視表的 SELECT 清單必須包含 COUNT_BIG (\*) | 請使用 COUNT_BIG (\*)。 | 沒有 COUNT_BIG(\*) 的索引檢視表 SELECT 清單 |
| 透過檢視表將資料表提示間接套用到多重陳述式資料表值函式 (TVF) 的引動過程。|無。|間接 TVF 提示 |
| ALTER DATABASE 語法：<br /><br />MODIFY FILEGROUP READONLY<br /><br />MODIFY FILEGROUP READWRITE | MODIFY FILEGROUP READ_ONLY<br /><br />MODIFY FILEGROUP READ_WRITE | MODIFY FILEGROUP READONLY<br /><br />MODIFY FILEGROUP READWRITE |
| SET ANSI_NULLS OFF 和 ANSI_NULLS OFF 資料庫選項<br /><br />SET ANSI_PADDING OFF 和 ANSI_PADDING OFF 資料庫選項<br /><br />SET CONCAT_NULL_YIELDS_NULL OFF 和 CONCAT_NULL_YIELDS_NULL OFF 資料庫選項<br /><br />SET OFFSETS | 無。 <br /><br /> ANSI_NULLS、ANSI_PADDING 和 CONCAT_NULLS_YIELDS_NULL 一律會設定為 ON。 SET OFFSETS 則無法使用。 | SET ANSI_NULLS OFF <br /><br /> SET ANSI_PADDING OFF<br /><br />SET CONCAT_NULL_YIELDS_NULL OFF<br /><br />SET OFFSETS<br /><br />ALTER DATABASE SET ANSI_NULLS OFF<br /><br />ALTER DATABASE SET ANSI_PADDING OFF <br /><br /> ALTER DATABASE SET CONCAT_NULL_YIELDS_NULL OFF |
| SET FMTONLY | [sys.dm_exec_describe_first_result_set &#40;Transact-SQL&#41;](../relational-databases/system-dynamic-management-views/sys-dm-exec-describe-first-result-set-transact-sql.md)、[sys.dm_exec_describe_first_result_set_for_object &#40;Transact-SQL&#41;](../relational-databases/system-dynamic-management-views/sys-dm-exec-describe-first-result-set-for-object-transact-sql.md)、[sp_describe_first_result_set &#40;Transact-SQL&#41;](../relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql.md) 和 [sp_describe_undeclared_parameters &#40;Transact-SQL&#41;](../relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql.md)。 | SET FMTONLY |
| 在 UPDATE 或 DELETE 陳述式的 FROM 子句中指定 NOLOCK 或 READUNCOMMITTED。 | 請從 FROM 子句中移除 NOLOCK 或 READUNCOMMITTED 資料表提示。 | UPDATE 或 DELETE 中的 NOLOCK 或 READUNCOMMITTED |
| 指定資料表提示，而不使用 WITH 關鍵字。 | 使用 WITH。 | 沒有 WITH 的資料表提示 |
| INSERT_HINTS | | INSERT_HINTS |

### <a name="tools"></a>工具

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| SQL Server Profiler for Trace Capture | 請使用 SQL Server Management Studio 內嵌的擴充事件分析工具。|SQL Server Profiler |
| SQL Server Profiler for Trace Replay | [SQL Server Distributed Replay](../tools/distributed-replay/sql-server-distributed-replay.md) |

### <a name="trace-management-objects"></a>追蹤管理物件

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| Microsoft.SqlServer.Management.Trace 命名空間 (包含 SQL Server 追蹤和重新執行物件的 API)|追蹤組態︰ <xref:Microsoft.SqlServer.Management.XEvent><br /><br />追蹤讀取︰ <xref:Microsoft.SqlServer.XEvent.Linq><br /><br />追蹤重新執行：None | |

### <a name="xml"></a>XML

| 已被取代的功能 | 取代 | 功能名稱 |
|--------------------|-------------|--------------|
| 內嵌 XDR 結構描述的產生 | FOR XML 選項的 XMLDATA 指示詞已被取代。 在 RAW 和 AUTO 模式的情況下，請使用 XSD 產生。 EXPLICT 模式中沒有 XMLDATA 指示詞的替代項目。 | XMLDATA |

> [!NOTE]
> **sp_setapprole** 的 Cookie **OUTPUT** 參數目前記載成 **varbinary(8000)** ，這是正確的長度上限。 但目前的實作會傳回 **varbinary(50)** 。 如果開發人員已配置 **varbinary(50)** ，則未來版本的 Cookie 傳回大小如有增加，應用程式可能需要變更。 雖然這不是取代問題，但是由於應用程式調整很類似，因此在本主題中提及。 如需詳細資訊，請參閱 [sp_setapprole &#40;Transact-SQL&#41;](../relational-databases/system-stored-procedures/sp-setapprole-transact-sql.md)。  
  
## <a name="see-also"></a>另請參閱  
 [SQL Server 2016 中已停止的 Database Engine 功能](./discontinued-database-engine-functionality-in-sql-server.md)  
