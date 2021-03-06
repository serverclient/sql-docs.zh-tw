---
description: 開放式並行存取
title: 開放式平行存取 |Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
helpviewer_keywords:
- transactions [ODBC], concurrency control
- concurrency control [ODBC]
- optimistic concurrency [ODBC]
ms.assetid: 9d71e09e-bc68-4c1f-9229-ed2a7be7d324
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: dce1982edbb8f5a417404c6e24e8a40d25b58e0b
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88429130"
---
# <a name="optimistic-concurrency"></a>開放式並行存取
*開放式並行* 存取衍生自開放式假設的名稱，不會發生交易之間的衝突;當另一個交易在目前交易讀取的時間與更新或刪除的時間之間，有另一筆交易更新或刪除資料列時，就會發生衝突。 這與封閉式 *並行* 存取或鎖定相反，因為應用程式開發人員認為這類衝突很普遍。  
  
 在開放式平行存取中，資料列會保持解除鎖定，直到更新或刪除資料列為止。 屆時，資料列會重新讀取並進行檢查，以查看上次讀取之後是否已變更。 如果資料列已變更，則更新或刪除會失敗，而且必須再試一次。  
  
 若要判斷資料列是否已變更，會根據快取的資料列版本檢查其新版本。 這項檢查可以根據資料列版本，例如 SQL Server 中的時間戳記資料行，或資料列中每個資料行的值。 許多 Dbms 不支援資料列版本。  
  
 開放式平行存取可由資料來源或應用程式來執行。 無論是哪一種情況，應用程式都應使用低交易隔離等級，例如讀取認可;使用較高的層級，會使用開放式平行存取來否定提高的平行存取。  
  
 如果開放式平行存取是由資料來源所執行，則應用程式會將 SQL_ATTR_CONCURRENCY 語句屬性設定為 SQL_CONCUR_ROWVER 或 SQL_CONCUR_VALUES。 若要更新或刪除資料列，它會執行定位的 update 或 delete 語句，或呼叫 **SQLSetPos** ，就像使用封閉式平行存取一樣：如果更新或刪除因為衝突而失敗，則驅動程式或資料來源會傳回 SQLSTATE 01001 (資料指標操作衝突) 。  
  
 如果應用程式本身會執行開放式平行存取，則會將 SQL_ATTR_CONCURRENCY 語句屬性設定為 SQL_CONCUR_READ_ONLY 以讀取資料列。 如果它將比較資料列版本，但不知道資料列版本資料行，它會使用 SQL_ROWVER 選項來呼叫 **SQLSpecialColumns** ，以決定此資料行的名稱。  
  
 應用程式會藉由將平行存取增加至 SQL_CONCUR_LOCK (來更新或刪除資料列，以取得資料列的寫入存取) 以及使用**WHERE**子句來執行**UPDATE**或**DELETE**語句，以指定資料列在應用程式讀取時所擁有的版本或值。 如果資料列自從那時之後已經變更，則語句將會失敗。 如果 **WHERE** 子句無法唯一識別資料列，則語句可能也會更新或刪除其他資料列;資料列版本一律會唯一識別資料列，但只有當資料列包含主鍵時，資料列值才會唯一識別資料列。
