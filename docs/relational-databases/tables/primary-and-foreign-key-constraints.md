---
description: 主要與外部索引鍵條件約束
title: 主要與外部索引鍵條件約束 | Microsoft Docs
ms.custom: ''
ms.date: 07/25/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
helpviewer_keywords:
- foreign keys [SQL Server], cascading referential integrity
- FOREIGN KEY constraints
- foreign keys [SQL Server]
- foreign keys [SQL Server], about foreign key constraints
ms.assetid: 31fbcc9f-2dc5-4bf9-aa50-ed70ec7b5bcd
author: stevestein
ms.author: sstein
monikerRange: =azuresqldb-current||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 091ebaf16d46cdde5604e532a80f1dd1177586c6
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98172460"
---
# <a name="primary-and-foreign-key-constraints"></a>主要與外部索引鍵條件約束

[!INCLUDE [sqlserver2016-asdb-asdbmi](../../includes/applies-to-version/sqlserver2016-asdb-asdbmi.md)]

  主索引鍵和外部索引鍵是兩種類型的條件約束，可用以強制執行 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 資料表中的資料完整性。 這些都是重要的資料庫物件。  
  
 本主題包含下列各節。  
  
 [主索引鍵條件約束](../../relational-databases/tables/primary-and-foreign-key-constraints.md#PKeys)  
  
 [Foreign Key Constraints](../../relational-databases/tables/primary-and-foreign-key-constraints.md#FKeys)  
  
 [相關工作](../../relational-databases/tables/primary-and-foreign-key-constraints.md#Tasks)  
  
##  <a name="primary-key-constraints"></a><a name="PKeys"></a> 主索引鍵條件約束  
 資料表中通常會有一個或多個資料行包含可唯一識別資料表中每個資料列的值。 此資料行稱為資料表的主索引鍵 (PK)，強制資料表具有實體完整性。 主索引鍵條件約束保證唯一的資料，因此通常是定義在識別欄位上。  
  
 當您為資料表指定主索引鍵條件約束時， [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會自動為主索引鍵資料行建立唯一的索引，以強制資料的唯一性。 當主索引鍵用於查詢時，此索引也可讓您快速地存取資料。 若主索引鍵條件約束定義於多個資料行，則某個資料行內的值可能會重複，但主索引鍵條件約束定義中所有資料行的每個值組合都必須是唯一的。  
  
 如下圖所示， **Purchasing.ProductVendor** 資料表中的 **ProductID** 和 **VendorID** 資料行形成此資料表的複合主索引鍵條件約束。 這樣可確保 **ProductVendor** 資料表中的每個資料列都有唯一的 **ProductID** 與 **VendorID** 組合。 如此可防止插入重複的資料列。  
  
 ![複合 PRIMARY KEY 條件約束](../../relational-databases/tables/media/fund04.gif "複合 PRIMARY KEY 條件約束")  
  
-   一份資料表只能有一個主索引鍵條件約束。  
  
-   主索引鍵不能超出 16 個資料行，總索引鍵長度則不能超出 900 個位元組。  
  
-   主索引鍵條件約束所產生的索引無法讓資料表的索引數目超出 999 個非叢集索引和 1 個叢集索引。  
  
-   如果未指定主索引鍵條件約束的叢集或非叢集，則會在資料表上沒有叢集索引時使用叢集。  
  
-   主索引鍵條件約束內定義的所有資料行都必須定義為非 Null。 如果未指定 Null 屬性，參與主索引鍵條件約束之所有資料行的 Null 屬性都會設成非 Null。  
  
-   如果在 CLR 使用者定義的類型資料行上定義主索引鍵，類型的實作必須支援二進位排序。  
  
##  <a name="foreign-key-constraints"></a><a name="FKeys"></a> Foreign Key Constraints  
 外部索引鍵 (FK) 是可用來建立與強制兩資料表的資料之間連結的一個資料行或資料行組合，以控制外部索引鍵資料表中可儲存的資料。 在外部索引鍵參考中，當存放一個資料表的主索引鍵值的資料行被另一個資料表的資料行參考時，兩資料表之間會建立連結。 此資料行會成為第二個資料表的外部索引鍵。  
  
 例如， **Sales.SalesOrderHeader** 資料表具有與 **Sales.SalesPerson** 資料表的外部索引鍵連結，因為銷售訂單與銷售人員之間存在邏輯關聯性。 **SalesOrderHeader** 資料表中的 **SalesPersonID** 資料行符合 **SalesPerson** 資料表的主索引鍵資料行。 **SalesOrderHeader** 資料表中的 **SalesPersonID** 資料行是 **SalesPerson** 資料表的外部索引鍵。 透過建立這個外部索引鍵關聯性，如果 **SalesPersonID** 的值尚未存在於 **SalesPerson** 資料表中，就不能將其插入至 **SalesOrderHeader** 資料表。  
  
 一個資料表最多可以參考其他 253 個資料表和資料行作為外部索引鍵 (連出參考)。 [!INCLUDE[ssSQL15](../../includes/sssql16-md.md)] 將單一資料表中資料行可以參考的其他資料表和資料行數目限制 (連入參考) 從 253 提高至 10,000。 (至少需要 130 相容性層級)。此增加具有下列限制：  
  
-   只有 DELETE DML 作業才支援大於 253 的外部索引鍵參考數目。 不支援 UPDATE 和 MERGE 作業。  
  
-   如果資料表具有參考本身的外部索引鍵，則仍會限制為 253 個外部索引鍵參考。  
  
-   大於 253 的外部索引鍵參考數目目前不適用於資料行存放區索引、記憶體最佳化資料、Stretch Database 或資料分割外部索引鍵資料表。  
  
### <a name="indexes-on-foreign-key-constraints"></a>外部索引鍵條件約束上的索引  
 與主索引鍵條件約束不同，建立外部索引鍵條件約束並不會自動建立對應的索引。 不過，基於下列原因，在外部索引鍵上手動建立索引通常很有幫助：  
  
-   當關聯資料表的資料藉著將資料表的外部索引鍵條件約束和另一個資料表的主要或唯一索引鍵資料行進行比對，而合併於查詢中時，通常會使用外部索引鍵資料行來聯結準則。 索引可讓 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 在外部索引鍵資料表中快速尋找相關資料。 不過，建立此索引並非必要。 即使資料表之間未定義主索引鍵或外部索引鍵條件約束，仍可合併兩個相關資料表的資料，不過兩個資料表之間的外部索引鍵關聯性代表這兩個資料表已經過最佳化，可合併於使用該索引鍵做為準則的查詢中。  
  
-   系統會檢查主索引鍵條件約束的變更與相關資料表中的外部索引鍵條件約束。  
  
### <a name="referential-integrity"></a>參考完整性  
 雖然外部索引鍵條件約束的主要用途是控制可儲存在外部索引鍵資料表中的資料，但是它也可控制主索引鍵資料表中資料的變更。 例如，將銷售員的資料列從 **Sales.SalesPerson** 資料表中刪除，而該銷售員的識別碼是用於 **Sales.SalesOrderHeader** 資料表的銷售訂單中，則會中斷這兩個資料表的關聯完整性；會遺棄 **SalesOrderHeader** 資料表中所刪除銷售員的銷售訂單，因為無法連結到 **SalesPerson** 資料表中的資料。  
  
 外部索引鍵條件約束可防止這種情況。 若針對主索引鍵資料表的資料所做的變更，會讓指到外部索引鍵資料表內資料的連結無效，則此條件約束會禁止執行此變更，以強制參考完整性。 若您想刪除主索引鍵資料表中的資料列，或變更主索引鍵值，則在刪除或變更的主索引鍵值對應至另一個資料表之外部索引鍵條件約束中的值時，此動作將會失敗。 若要順利變更或刪除外部索引鍵條件約束中的資料列，則必須先刪除外部索引鍵資料表中的外部索引鍵資料，或變更外部索引鍵資料表中的外部索引鍵資料，這樣會將外部索引鍵連結至不同的主索引鍵資料。  
  
#### <a name="cascading-referential-integrity"></a>串聯式參考完整性  
 使用串聯式參考完整性條件約束，就可以定義使用者嘗試刪除或更新現有外部索引鍵所指向的索引鍵時， [!INCLUDE[ssDE](../../includes/ssde-md.md)] 所採取的動作。 可以定義下列串聯式動作。  
  
 NO ACTION  
 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 會引發錯誤，而且會回復對父資料表中資料列的刪除或更新動作。  
  
 CASCADE  
 更新或刪除父資料表中的對應資料列時，也會更新或刪除參考資料表中的該資料列。 如果 **timestamp** 資料行是外部索引鍵或被參考索引鍵的一部分，就無法指定 CASCADE。 如果資料表有 INSTEAD OF DELETE 觸發程序，則不能指定 ON DELETE CASCADE。 如果資料表有 INSTEAD OF UPDATE 觸發程序，則不能指定 ON UPDATE CASCADE。  
  
 SET NULL  
 更新或刪除父資料表中的對應資料列時，所有組成外部索引鍵的值都會設定為 NULL。 若要執行這個條件約束，外部索引鍵資料行必須可為 Null。 如果資料表有 INSTEAD OF UPDATE 觸發程序，則不能予以指定。  
  
 SET DEFAULT  
 如果更新或刪除父資料表中的對應資料列，則所有組成外部索引鍵的值都會設定為其預設值。 若要執行這個條件約束，所有外部索引鍵資料行都必須有預設定義。 如果有可為 Null 的資料行，但沒有設定明確的預設值，NULL 便成為這個資料行的隱含預設值。 如果資料表有 INSTEAD OF UPDATE 觸發程序，則不能予以指定。  
  
 您可以在相互具有參考關聯性的資料表上，組合 CASCADE、SET NULL、SET DEFAULT 和 NO ACTION。 如果 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 發現 NO ACTION，它會停止和回復相關的 CASCADE、SET NULL 和 SET DEFAULT 動作。 當 DELETE 陳述式造成 CASCADE、SET NULL、SET DEFAULT 和 NO ACTION 等動作的組合時，在 [!INCLUDE[ssDE](../../includes/ssde-md.md)] 檢查任何 NO ACTION 之前，會先套用 CASCADE、SET NULL 及 SET DEFAULT 等動作。  
  
### <a name="triggers-and-cascading-referential-actions"></a>觸發程序與串聯式參考動作  
 串聯式參考動作會以下列方式引發 AFTER UPDATE 或 AFTER DELETE 觸發程序：  
  
-   直接由原始 DELETE 或 UPDATE 造成的所有串聯式參考動作會最先執行。  
  
-   如果已在受影響的資料表上定義任何 AFTER 觸發程序，這些觸發程序將會在執行所有串聯式動作後引發。 這些觸發程序的引發順序，將會與串聯式動作相反。 如果單一資料表上有多個觸發程序，則除非資料表有專用的第一個或最後一個觸發程序，否則將會以隨機順序引發。 這個順序是使用 [sp_settriggerorder](../../relational-databases/system-stored-procedures/sp-settriggerorder-transact-sql.md)指定。  
  
-   若有多個串聯式鏈結源自 UPDATE 或 DELETE 動作的直接目標資料表，則這些鏈結引發其各自觸發程序的順序未定。 不過，一定會等到一個鏈結引發完其所有觸發程序後，才引發另外一個鏈結。  
  
-   不論是否有任何資料列受到影響，UPDATE 或 DELETE 動作都會引發直接目標資料表上的 AFTER 觸發程序。 在此情況下，沒有任何其他資料會受到串聯的影響。  
  
-   若有任何一個先前的觸發程序在其他資料表上執行 UPDATE 或 DELETE 作業，這些動作便形成次要串聯式鏈結。 每個 UPDATE 或 DELETE 作業的次要鏈結會在所有主要鏈結的所有觸發程序之後的某個時間處理。 這個處理序可能會針對後續的 UPDATE 或 DELETE 作業不斷重複。  
  
-   在觸發程序內執行 CREATE、ALTER、DELETE 或其他資料定義語言 (DDL) 作業，可能會導致引發 DDL 觸發程序。 這可能會接著執行 DELETE 或 UPDATE 作業，開始其他串聯式鏈結與觸發程序。  
  
-   如果任何特定串聯式參考動作鏈結內產生錯誤，則會引發錯誤，此時並不會在該鏈結中引發任何 AFTER 觸發程序，而且會回復建立該鏈結的 DELETE 或 UPDATE 作業。  
  
-   具有 INSTEAD OF 觸發程序的資料表不能也具有指定串聯動作的 REFERENCES 子句。 不過，串聯式動作所處理之資料表上的 AFTER 觸發程序，可在另一個資料表或檢視表上執行 INSERT、UPDATE 或 DELETE 陳述式，以引發該物件所定義的 INSTEAD OF 觸發程序。  
  
##  <a name="related-tasks"></a><a name="Tasks"></a> 相關工作  
 下表列出與主索引鍵和外部索引鍵條件約束相關聯的一般工作。  
  
|Task|主題|  
|----------|-----------|  
|描述如何建立主索引鍵。|[建立主索引鍵](../../relational-databases/tables/create-primary-keys.md)|  
|描述如何刪除主索引鍵。|[刪除主索引鍵](../../relational-databases/tables/delete-primary-keys.md)|  
|描述如何修改主索引鍵。|[修改主索引鍵](../../relational-databases/tables/modify-primary-keys.md)|  
|描述如何建立外部索引鍵關聯性。|[建立外部索引鍵關聯性](../../relational-databases/tables/create-foreign-key-relationships.md)|  
|描述如何修改外部索引鍵關聯性。|[修改外部索引鍵關聯性](../../relational-databases/tables/modify-foreign-key-relationships.md)|  
|描述如何刪除外部索引鍵關聯性。|[刪除外部索引鍵關聯性](../../relational-databases/tables/delete-foreign-key-relationships.md)|  
|描述如何檢視外部索引鍵屬性。|[檢視外部索引鍵屬性](../../relational-databases/tables/view-foreign-key-properties.md)|  
|描述如何停用複寫的外部索引鍵條件約束。|[停用複寫的外部索引鍵條件約束](../../relational-databases/tables/disable-foreign-key-constraints-for-replication.md)|  
|描述如何在 INSERT 或 UPDATE 陳述式期間停用外部索引鍵條件約束。|[停用 INSERT 和 UPDATE 陳述式的外部索引鍵條件約束](../../relational-databases/tables/disable-foreign-key-constraints-with-insert-and-update-statements.md)|  
  
  
