---
title: 浮點數
description: 了解在 Microsoft SqlClient Data Provider for SQL Server 中使用浮點數時的一些問題。
ms.date: 11/13/2020
ms.assetid: 73c218c6-1c44-4402-a167-4f6262629a91
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 1ca251c6de5ca6157ace8ee481bae02afa9fb2fb
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771556"
---
# <a name="floating-point-numbers"></a>浮點數

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

此主題描述當開發人員在 Microsoft SqlClient Data Provider for SQL Server 中使用浮點數時經常遇到的一些問題。 這些問題是由電腦儲存浮點數的方式所導致，而不是特定提供者 (例如 <xref:Microsoft.Data.SqlClient>) 特有的。

## <a name="floating-point-pitfall"></a>浮點數 pitfall

一般而言，浮點數值並不具有完全符合的二進位表示， 而是由電腦儲存該數值的近似值。 在不同的時間可能會使用不同的位元來表示該數值。 當浮點數值從一種表示法轉換為另一種時，該數值的最小顯著性數字可能會稍微變更。 轉換通常發生在當數值從一種型別轉型為另一種型別時。 不論轉換是發生在資料庫內、在表示資料庫值的型別之間或是在型別之間，都會造成最小顯著性數字的變更。 因為有這些變更，所以邏輯上應該相同的數值，可能因為最小顯著性數字變更而導致它們擁有不同的值。 數值的精確度位數可能會比預期的多或少。 如果將數值的格式設為字串，則該數值可能不會顯示預期的值。

## <a name="suggested-workarounds"></a>建議的因應措施

若要盡可能減少這些效果，您應該就可用的項目中，選用最接近的數值型別相符項目。 例如，當您使用 SQL Server 時，如果將實數型別的 Transact-SQL 值轉換成浮點型別的值，則確切的數值可能會變更。 在 .NET 中，將 <xref:System.Single> 轉換成 <xref:System.Double> 可能也會造成非預期的結果。 在上述兩種情況中，將應用程式中的所有值設定為使用相同的數字型別 (Numeric Type) 是不錯的策略。 您也可以使用固定有效位數十進位型別，或者將浮點數值轉型為固定有效位數十進位型別，再加以使用。

若要解決等號比較的問題，請考慮撰寫應用程式的程式碼，使最小顯著性數字中的變更可以忽略。 例如，與其比較兩個數字是否相等，請從一個數字中減去另一個數字。 如果差異是在可接受的捨入範圍內，則應用程式可以將這兩個數字視為相同。

## <a name="see-also"></a>請參閱

- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
