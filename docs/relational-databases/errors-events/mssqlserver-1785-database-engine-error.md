---
description: MSSQLSERVER_1785
title: MSSQLSERVER_1785
ms.custom: ''
ms.date: 12/25/2020
ms.prod: sql
ms.reviewer: ramakoni1, pijocoder, suresh-kandoth, vencher, tejasaks, docast
ms.technology: supportability
ms.topic: language-reference
helpviewer_keywords:
- 1785 (Database Engine error)
ms.assetid: ''
author: suresh-kandoth
ms.author: ramakoni
ms.openlocfilehash: 7f300583da4c034da2963590c81e0aedbed86beb
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596263"
---
# <a name="mssqlserver_1785"></a>MSSQLSERVER_1785
 [!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

## <a name="details"></a>詳細資料

|屬性|值|
|---|---|
|產品名稱|SQL Server|
|事件識別碼|1785|
|事件來源|MSSQLSERVER|
|元件|SQLEngine|
|符號名稱|CRTFKINVTOPO|
|訊息文字|在資料表 '%.ls'上導入 FOREIGN KEY 條件約束 '%. ls' 可能造成循環或多個串聯路徑。 請指定 ON DELETE NO ACTION 或 ON UPDATE NO ACTION，或者修改其他 FOREIGN KEY 條件約束。|
||

## <a name="explanation"></a>說明

您收到此錯誤訊息是因為在 SQL Server 中，資料表不能在由 `DELETE` 或 `UPDATE` 陳述式所啟動的所有串聯式參考動作清單中出現一次以上。 在串聯式參考動作樹狀結構上，串聯式參考動作的樹狀結構只能有一個特定資料表的路徑。

使用者會收到類似下列的錯誤訊息回報：

> 伺服器：訊息 1785，層級 16，狀態 1，第 1 行 在資料表 'table2' 導入 FOREIGN KEY 條件約束 'fk_two' 可能造成循環或多個串聯路徑。 請指定 ON DELETE NO ACTION 或 ON UPDATE NO ACTION，或者修改其他 FOREIGN KEY 條件約束。 伺服器：訊息 1750，層級 16，狀態 1，第 1 行 無法建立條件約束。 請查看先前的錯誤

## <a name="user-action"></a>使用者動作

若要解決此問題，請建立一個外部索引鍵，其將在串聯式參考動作清單中建立某個資料表的單一路徑。

您可以透過數種方式強制執行參考完整性。 宣告式參考完整性 (DRI) 是最基本的方式，但也是彈性最小的方式。 如果您需要更大的彈性，但仍想要有高度完整性，則可改為使用觸發程序。

## <a name="more-information"></a>詳細資訊

下列範例程式碼是嘗試建立外部索引鍵且會產生錯誤訊息的範例：

```sql
USE tempdb
GO

CREATE TABLE table1 (user_ID INTEGER NOT NULL PRIMARY KEY, user_name
CHAR(50) NOT NULL)
GO

CREATE TABLE table2 (author_ID INTEGER NOT NULL PRIMARY KEY, author_name
CHAR(50) NOT NULL, lastModifiedBy INTEGER NOT NULL, addedby INTEGER NOT NULL)
GO

ALTER TABLE table2 ADD CONSTRAINT fk_one FOREIGN KEY (lastModifiedby)
REFERENCES table1 (user_ID) ON DELETE CASCADE ON UPDATE cascade
GO

ALTER TABLE table2 ADD CONSTRAINT fk_two FOREIGN KEY (addedby)
REFERENCES table1(user_ID) ON DELETE NO ACTION ON UPDATE cascade
GO
--this fails with the error because it provides a second cascading path to table2.

ALTER TABLE table2 ADD CONSTRAINT fk_two FOREIGN KEY (addedby)
REFERENCES table1 (user_ID) ON DELETE NO ACTION ON UPDATE NO ACTION
GO
-- this works.
```

### <a name="see-also"></a>請參閱

[串聯式參考完整性](../tables/primary-and-foreign-key-constraints.md#referential-integrity)