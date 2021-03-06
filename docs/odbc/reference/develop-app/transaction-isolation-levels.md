---
description: " (ODBC) 的交易隔離等級"
title: " (ODBC) 的交易隔離等級 |Microsoft Docs"
ms.custom: seo-dt-2019
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
helpviewer_keywords:
- dirty reads [ODBC]
- isolation levels [ODBC]
- nonrepeatable reads [ODBC]
- read uncommitted [ODBC]
- read committed [ODBC]
- serializable reads [ODBC]
- phantoms [ODBC]
- transaction isolation [ODBC]
- repeatable reads [ODBC]
- transactions [ODBC], isolation
ms.assetid: 0d638d55-ffd0-48fb-834b-406f466214d4
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: aa4f5b0269760b648e0a499ea550901e7db6e74f
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88487471"
---
# <a name="transaction-isolation-levels-odbc"></a> (ODBC) 的交易隔離等級
*交易隔離等級* 是交易隔離成功程度的量值。 尤其是，交易隔離等級是由下列現象所定義：  
  
-   中途**讀取**當交易讀取尚未認可的資料時，就會發生中途*讀取*。 例如，假設交易1會更新資料列。 交易2會在交易1認可更新之前讀取更新的資料列。 如果交易1復原變更，交易2將會有視為永遠不存在的讀取資料。  
  
-   **不可重複讀取** 當交易讀取相同的資料列兩次，但每次都取得不同的資料時，就會發生 *不可重複讀取* 。 例如，假設交易1讀取資料列。 交易2會更新或刪除該資料列，並認可更新或刪除。 如果交易1讀取資料列，則會抓取不同的資料列值，或發現資料列已刪除。  
  
-   **幻像** 虛設 *專案是符合* 搜尋條件的資料列，但一開始並不會出現。 例如，假設交易1讀取符合某些搜尋準則的一組資料列。 交易2會透過更新或符合交易1搜尋準則的插入) ，來產生新的資料列 (。 如果交易 1 reexecutes 讀取資料列的語句，則會取得一組不同的資料列。  
  
  (如 SQL-92) 所定義的四個交易隔離等級，則是根據這些現象來定義。 在下表中，「X」會標示每個可能發生的現象。  
  
|交易隔離等級|中途讀取|不可重複讀取|幽靈|  
|---------------------------------|-----------------|-------------------------|--------------|  
|讀取未認可|X|X|X|  
|讀取認可|--|X|X|  
|可重複讀取|--|--|X|  
|可序列化|--|--|--|  
  
 下表描述 DBMS 可能會執行交易隔離等級的簡單方式。  
  
> [!IMPORTANT]  
>  大部分的 Dbms 使用比這些配置更複雜的配置來增加並行。 這些範例僅供說明之用。 尤其是，ODBC 不會規定特定 Dbms 如何彼此隔離交易。  
  
|交易隔離|可能的執行|  
|---------------------------|-----------------------------|  
|讀取未認可|交易不會彼此隔離。 如果 DBMS 支援其他交易隔離等級，則會忽略它用來執行這些層級的任何機制。 因此，它們不會對其他交易造成負面影響，在讀取未認可層級執行的交易通常是唯讀的。|  
|讀取認可|交易會等到其他交易鎖定的資料列被解除鎖定為止;這可防止它讀取任何「中途」的資料。<br /><br /> 如果交易在目前的資料列上更新或刪除資料列) 以防止其他交易更新或刪除，則交易會保存讀取鎖定 (如果它唯讀取資料列) 或寫入鎖定 (。 當交易移出目前的資料列時，就會釋放讀取鎖定。 它會保留寫入鎖定，直到認可或回復為止。|  
|可重複讀取|交易會等到其他交易鎖定的資料列被解除鎖定為止;這可防止它讀取任何「中途」的資料。<br /><br /> 交易會在其傳回給應用程式的所有資料列上保存讀取鎖定，並在其插入、更新或刪除的所有資料列上寫入鎖定。 例如，如果交易包含 **SELECT \* FROM ORDERS**的 SQL 語句，當應用程式提取資料列時，交易就會讀取鎖定資料列。 如果交易包含的 SQL 語句 **從 Status = ' CLOSED ' 的訂單中刪除**，則交易會在刪除資料列時將其鎖定。<br /><br /> 由於其他交易無法更新或刪除這些資料列，因此目前的交易可避免任何不可重複讀取。 交易被認可或回復時，會釋放鎖定。|  
|可序列化|交易會等到其他交易鎖定的資料列被解除鎖定為止;這可防止它讀取任何「中途」的資料。<br /><br /> 交易會保存讀取鎖定 (如果它唯讀取資料列) 或寫入鎖定 (如果它可以更新或刪除資料列所影響之資料列的範圍) 。 例如，如果交易包含 **SELECT \* FROM ORDERS**的 SQL 語句，則範圍是整個 orders 資料表; 交易讀取鎖定資料表，且不允許將任何新的資料列插入其中。 如果交易包含的 SQL 語句 **從 status = ' closed ' 的訂單中刪除**，則範圍為狀態為 "closed" 的所有資料列;交易寫入-鎖定 Orders 資料表中狀態為「已關閉」的所有資料列，而且不允許插入或更新任何資料列，使結果資料列的狀態為「已關閉」。<br /><br /> 由於其他交易無法更新或刪除範圍中的資料列，因此目前的交易會避免任何不可重複讀取。 因為其他交易無法插入範圍中的任何資料列，所以目前的交易會避免任何幻像。 交易被認可或回復時，會釋放鎖定。|  
  
 請務必注意，交易隔離等級不會影響交易查看其本身變更的能力;交易一律可以看到它們所做的任何變更。 例如，交易可能包含兩個 **UPDATE** 語句，第一個會將所有員工的款項提高10%，而第二個則會將任何員工的付款金額設定為該數量。 這會成功成為單一交易，因為第二個 **UPDATE** 語句可以看到第一個的結果。
