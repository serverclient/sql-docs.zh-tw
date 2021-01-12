---
description: ALTER DATABASE 資料庫鏡像 (Transact-SQL)
title: ALTER DATABASE 資料庫鏡像 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 02/21/2019
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
dev_langs:
- TSQL
helpviewer_keywords:
- witness [SQL Server], establishing
- manual failover [SQL Server]
- ALTER DATABASE statement, database mirroring
- database mirroring [SQL Server], Transact-SQL
ms.assetid: 27a032ef-1cf6-4959-8e67-03d28c4b3465
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.openlocfilehash: 60dcccf63cb8ca8116f5fdbfcfe7482e0a1560a4
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98094676"
---
# <a name="alter-database-transact-sql-database-mirroring"></a>ALTER DATABASE (Transact-SQL) 資料庫鏡像

[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

> [!NOTE]
> [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)] 請改用 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]。

控制資料庫的資料庫鏡像。 資料庫鏡像選項所指定的值會套用至資料庫的這兩個複本，而且會當做一個整體套用在資料庫鏡像工作階段上。 每個 ALTER DATABASE 陳述式只允許一個 \<database_mirroring_option> 選項。

> [!NOTE]
> 我們建議您將資料庫鏡像作業設定在離峰時間執行，因為組態會影響效能。

針對其他 ALTER DATABASE 選項，請參閱 [ALTER DATABASE](../../t-sql/statements/alter-database-transact-sql.md)。 針對 ALTER DATABASE SET 選項，請參閱 [ALTER DATABASE SET 選項](../../t-sql/statements/alter-database-transact-sql-set-options.md)。

![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)

## <a name="syntax"></a>語法

```syntaxsql

ALTER DATABASE database_name
SET { <partner_option> | <witness_option> }
  <partner_option> ::=
    PARTNER { = 'partner_server'
            | FAILOVER
            | FORCE_SERVICE_ALLOW_DATA_LOSS
            | OFF
            | RESUME
            | SAFETY { FULL | OFF }
            | SUSPEND
            | TIMEOUT integer
            }
  <witness_option> ::=
    WITNESS { = 'witness_server'
            | OFF
            }
  
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="arguments"></a>引數

> [!IMPORTANT]
> SET PARTNER 或 SET WITNESS 命令可能會在輸入時成功完成，但在之後失敗。
> [!NOTE]
> 自主資料庫無法使用 ALTER DATABASE 資料庫鏡像選項。

*database_name*：這是要修改的資料庫名稱。

PARTNER \<partner_option> 控制定義資料庫鏡像工作階段的容錯移轉夥伴及其行為的資料庫屬性。 部分 SET PARTNER 選項可以在任何一個夥伴上設定，但其他選項只適用於主體伺服器或鏡像伺服器。 如需詳細資訊，請參閱下面的個別 PARTNER 選項。 SET PARTNER 子句會影響資料庫的這兩個複本，不論哪個夥伴指定它，都是如此。

若要執行 SET PARTNER 陳述式，兩個夥伴的端點狀態 (STATE) 必須都設為 STARTED。 另外，也請注意，每個夥伴伺服器執行個體之資料庫鏡像端點的 ROLE 都必須設成 PARTNER 或 ALL。 如需如何指定端點的資訊，請參閱[建立 Windows 驗證的資料庫鏡像端點](../../database-engine/database-mirroring/create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql.md)。 若要了解伺服器執行個體之資料庫鏡像端點的角色和狀態，請在這個執行個體上，使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式：

```sql
SELECT role_desc, state_desc FROM sys.database_mirroring_endpoints
```

**\<partner_option> ::=**

> [!NOTE]
> 每個 SET PARTNER 子句都只能有一個 \<partner_option>。

**'** _partner_server_ **'** 指定 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 執行個體伺服器網路位址作為新資料庫鏡像工作階段的容錯移轉夥伴。 每個工作階段都需要兩個夥伴：一個啟動做為主體伺服器，一個啟動做為鏡像伺服器。 我們建議您將這些夥伴放在不同電腦中。

在每個夥伴上，每個工作階段都指定一次這個選項。 起始資料庫鏡像工作階段需要兩個 ALTER DATABASE *database* SET PARTNER **='** _partner_server_ **'** 陳述式。 它們的順序很重要。 首先是連線到鏡像伺服器，將主體伺服器執行個體指定為 *partner_server* (SET PARTNER **='** _principal_server_ **'** )。 其次是連線到主體伺服器，將鏡像伺服器執行個體指定為 *partner_server* (SET PARTNER **='** _mirror_server_ **'** )；這會在這兩個夥伴之間，啟動一個資料庫鏡像工作階段。 如需詳細資訊，請參閱[設定資料庫鏡像](../../database-engine/database-mirroring/setting-up-database-mirroring-sql-server.md)。

*partner_server* 的值是一個伺服器網路位址。 這個值的語法如下：

TCP **://** _\<system-address>_ **:** _\<port>_

其中

- *\<system-address>* 這是明確識別目的地電腦系統的字串，例如系統名稱、完整網域名稱或 IP 位址。
- *\<port>* 是與夥伴伺服器執行個體鏡像端點相關聯的連接埠號碼。

如需詳細資訊，請參閱[指定伺服器網路位址 - 資料庫鏡像](../../database-engine/database-mirroring/specify-a-server-network-address-database-mirroring.md)。

下列範例說明 SET PARTNER **='** _partner_server_ **'** 子句：

```
'TCP://MYSERVER.mydomain.Adventure-Works.com:7777'
```

> [!IMPORTANT]
> 如果工作階段是利用 ALTER DATABASE 陳述式而不是 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] 來設定的，依預設，工作階段會設為完整交易安全 (SAFETY 設為 FULL)，並以不具自動容錯移轉的高安全性模式執行。 若要允許自動容錯移轉，請設定見證；若要以高效能模式執行，請關閉交易安全性 (SAFETY OFF)。

FAILOVER 以手動方式將主體伺服器容錯移轉到鏡像伺服器。 您只能在主體伺服器上指定 FAILOVER。 只有在 SAFETY 設定是 FULL (預設值) 時，這個選項才有效。

FAILOVER 選項需要 **master** 作為資料庫內容。

FORCE_SERVICE_ALLOW_DATA_LOSS 在主體伺服器失敗之後 (未發生自動容錯移轉且資料庫處於尚未同步或同步的狀態)，將資料庫服務強制移轉到鏡像資料庫。

我們強烈建議您只在主體伺服器已不執行時，才強制執行服務。 否則，某些用戶端可能仍會繼續存取原始的主體資料庫，而不是新的主體資料庫。
您只能在鏡像伺服器上使用 FORCE_SERVICE_ALLOW_DATA_LOSS，且只適用於下列情況：

- 主體伺服器已關閉。
- WITNESS 設定為 OFF 或見證連接到鏡像伺服器。

請只在您願意承擔失去某些資料的風險，以便在資料庫中立即還原服務時，才強制服務。

強制服務會暫停工作階段，暫時將所有資料保留在原始主體資料庫中。 原始的主體資料庫一旦可以提供服務且與新的主體伺服器開始通訊後，資料庫管理員就可以繼續提供服務。 恢復工作階段時，便會失去任何尚未傳送的記錄和對應的更新。

OFF 移除資料庫鏡像工作階段並從資料庫移除鏡像。 您可以在任何一個夥伴上指定 OFF。 如需移除鏡像所造成影響的資訊，請參閱[移除資料庫鏡像](../../database-engine/database-mirroring/removing-database-mirroring-sql-server.md)。

RESUME 繼續暫止的資料庫鏡像工作階段。 您只能在主體伺服器上指定 RESUME。

SAFETY { FULL | OFF } 設定交易安全層級。 您只能在主體伺服器上指定 SAFETY。

預設值是 FULL。 使用完整安全性時，資料庫鏡像工作階段會同步執行 (在「高安全性模式」 中)。 如果 SAFETY 設定為 OFF，則資料庫鏡像工作階段會非同步執行 (在「高效能模式」中)。

高安全性模式行為會依見證而有部分的不同，如下所述：

- 當安全性設定為 FULL，且設定了工作階段的見證時，工作階段會以具有自動容錯移轉的高安全性模式執行。 當主體伺服器遺失時，如果資料庫已同步處理而且鏡像伺服器執行個體及見證仍彼此連接 (也就是擁有仲裁)，工作階段就會自動進行容錯移轉。 如需詳細資訊，請參閱[仲裁：見證影響資料庫可用性的方式 - 資料庫鏡像](../../database-engine/database-mirroring/quorum-how-a-witness-affects-database-availability-database-mirroring.md)。

  如果設定了工作階段的見證，但目前已中斷連接，則遺失鏡像伺服器會使主體伺服器降低效能。

- 當安全性設定為 FULL，且見證設定為 OFF 時，工作階段會以不具自動容錯移轉的高安全性模式執行。 如果鏡像伺服器執行個體降低效能，主體伺服器執行個體不受影響。 如果主體伺服器執行個體降低效能，您可以對鏡像伺服器執行個體強制進行服務 (資料可能會遺失)。

- 如果 SAFETY 設定為 OFF，則工作階段會以高效能模式執行，且不支援自動容錯移轉和手動容錯移轉。 不過，鏡像上的問題不會影響主體，而且如果主體伺服器執行個體無法使用，您可以在必要時將服務強制移轉至鏡像伺服器執行個體 (資料可能會遺失) - 如果 WITNESS 設定為 OFF 或見證目前仍與鏡像連接。 如需有關強制服務的詳細資訊，請參閱本節稍早的 "FORCE_SERVICE_ALLOW_DATA_LOSS"。

> [!IMPORTANT]
> 高效能模式不使用見證。 不過，每當您將 SAFETY 設定為 OFF 時，強烈建議您確認 WITNESS 已設定為 OFF。

SUSPEND 暫停資料庫鏡像工作階段。

您可以在任何一個夥伴上指定 SUSPEND。

TIMEOUT *integer* 指定逾時週期 (以秒為單位)。 逾時期間是伺服器執行個體等待接收鏡像工作階段中之另一執行個體發出的 PING 訊息的最長時間，之後，便將其他執行個體視為已經中斷連接。

您只能在主體伺服器上指定 TIMEOUT 選項。 如果您沒有指定這個選項，依預設，這個期間是 10 秒。 如果您指定 5 或以上，逾時期間便設為指定的秒數。 如果您指定的逾時值是 0 - 4 秒，逾時期間會自動設為 5 秒。

> [!IMPORTANT]
> 我們建議您讓逾時期限保持在 10 秒或更久。 將這個值設定為小於 10 秒，可能會使負荷重的系統遺漏 PING 以及宣告假失敗。

如需詳細資訊，請參閱 [資料庫鏡像期間可能發生的失敗](../../database-engine/database-mirroring/possible-failures-during-database-mirroring.md)。

WITNESS \<witness_option> 控制定義資料庫鏡像見證的資料庫屬性。 SET WITNESS 子句會影響資料庫的這兩個複本，但您只能在主體伺服器上指定 SET WITNESS。 如果為工作階段設定見證，則不論 SAFETY 設定為何，都需要仲裁才能為資料庫提供服務；如需詳細資訊，請參閱[仲裁：見證影響資料庫可用性的方式 - 資料庫鏡像](../../database-engine/database-mirroring/quorum-how-a-witness-affects-database-availability-database-mirroring.md)。

我們建議您將見證和容錯移轉夥伴放在不同的電腦中。 如需如何加入或移除見證的資訊，請參閱[資料庫鏡像見證](../../database-engine/database-mirroring/database-mirroring-witness.md)。

若要執行 SET WITNESS 陳述式，主體和見證伺服器執行個體的端點 STATE 都必須設為 STARTED。 另外，也請注意，見證伺服器執行個體之資料庫鏡像端點的 ROLE 必須設成 WITNESS 或 ALL。 如需指定端點的資訊，請參閱[資料庫鏡像端點](../../database-engine/database-mirroring/the-database-mirroring-endpoint-sql-server.md)。

若要了解伺服器執行個體之資料庫鏡像端點的角色和狀態，請在這個執行個體上，使用下列 [!INCLUDE[tsql](../../includes/tsql-md.md)] 陳述式：

```sql
SELECT role_desc, state_desc FROM sys.database_mirroring_endpoints
```

> [!NOTE]
> 在見證上，無法設定資料庫屬性。

 **\<witness_option> ::=**

> [!NOTE]
> 每個 SET WITNESS 子句只能有一個 \<witness_option>。

 **'** _witness_server_ **'** 指定 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 執行個體作為資料庫鏡像工作階段的見證伺服器。 您只能在主體伺服器上指定 SET WITNESS。

在 **='** _witness_server_ **'** 陳述式中，*witness_server* 的語法與 *partner_server* 的語法相同。

OFF 從資料庫鏡像工作階段中移除見證。 將見證設成 OFF 會停用自動容錯移轉。 如果資料庫設為 FULL SAFETY，且見證設為 OFF，鏡像伺服器失敗，會使主體伺服器將資料庫設為無法使用。

## <a name="remarks"></a>備註

## <a name="examples"></a>範例

### <a name="a-creating-a-database-mirroring-session-with-a-witness"></a>A. 利用見證建立資料庫鏡像工作階段

利用見證來設定資料庫鏡像需要設定安全性和準備鏡像資料庫，另外，還需要利用 ALTER DATABASE 來設定夥伴。 如需完整的設定程序範例，請參閱[設定資料庫鏡像](../../database-engine/database-mirroring/setting-up-database-mirroring-sql-server.md)。

### <a name="b-manually-failing-over-a-database-mirroring-session"></a>B. 資料庫鏡像工作階段的手動容錯移轉

您可以從任何一個資料庫鏡像夥伴來起始手動容錯移轉。 在容錯移轉之前，您應該先確認您認為是目前主體伺服器的伺服器，實際上就是主體伺服器。 例如，如果是 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] 資料庫，請在您認為是目前主體伺服器的伺服器執行個體上，執行下列查詢：

```sql
SELECT db.name, m.mirroring_role_desc
FROM sys.database_mirroring m
JOIN sys.databases db
ON db.database_id = m.database_id
WHERE db.name = N'AdventureWorks2012';
GO
```

如果伺服器執行個體實際上就是主體，`mirroring_role_desc` 值便是 `Principal`。 如果這個伺服器執行個體是鏡像伺服器，`SELECT` 陳述式會傳回 `Mirror`。

下列範例假設伺服器是目前的主體。

1. 以手動方式執行容錯移轉，將工作交給資料庫鏡像夥伴：

    ```sql
    ALTER DATABASE AdventureWorks2012 SET PARTNER FAILOVER;
    GO
    ```
  
2. 若要驗證在新鏡像上的容錯移轉結果，請執行下列查詢：
  
    ```sql
    SELECT db.name, m.mirroring_role_desc
    FROM sys.database_mirroring m
    JOIN sys.databases db
    ON db.database_id = m.database_id
    WHERE db.name = N'AdventureWorks2012';
    GO
    ```
  現在，`mirroring_role_desc` 的目前值是 `Mirror`。

## <a name="see-also"></a>另請參閱

- [CREATE DATABASE](../../t-sql/statements/create-database-transact-sql.md)
- [DATABASEPROPERTYEX](../../t-sql/functions/databasepropertyex-transact-sql.md)
- [sys.database_mirroring_witnesses](../../relational-databases/system-catalog-views/database-mirroring-witness-catalog-views-sys-database-mirroring-witnesses.md)
