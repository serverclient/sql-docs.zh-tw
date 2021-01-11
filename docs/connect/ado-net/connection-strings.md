---
title: 連接字串
description: 了解 Microsoft SqlClient Data Provider for SQL Server 中的連接字串，其中包含可當做參數從資料提供者傳遞至資料來源的初始化資訊。
ms.date: 11/13/2020
ms.assetid: 745c5f95-2f02-4674-b378-6d51a7ec2490
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-daenge
ms.reviewer: v-chmalh
ms.openlocfilehash: 257ed7d43f8ab204c7c7e7575c69251be6f2efdf
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771620"
---
# <a name="connection-strings-in-adonet"></a>ADO.NET 中的連接字串

[!INCLUDE[appliesto-netfx-netcore-netst-md](../../includes/appliesto-netfx-netcore-netst-md.md)]

[!INCLUDE[Driver_ADONET_Download](../../includes/driver_adonet_download.md)]

連接字串 (Connection String) 包含可當做參數從資料提供者 (Data Provider) 傳遞至資料來源的初始化資訊。 資料提供者會接收連接字串作為 <xref:System.Data.Common.DbConnection.ConnectionString?displayProperty=nameWithType> 屬性的值。 此提供者會剖析連接字串，並確保語法正確且支援關鍵字。 然後，<xref:System.Data.Common.DbConnection.Open?displayProperty=nameWithType> 方法會將剖析的連線參數傳遞至資料來源。 資料來源會執行進一步驗證，並建立連線。

## <a name="connection-string-syntax"></a>連接字串語法

連接字串是分號分隔的機碼/值參數組清單：

```csharp
keyword1=value; keyword2=value;
```

關鍵字不區分大小寫。 不過，視資料來源而定，值可能區分大小寫。 關鍵字與值均可包含[空白字元](https://en.wikipedia.org/wiki/Whitespace_character#Unicode) \(英文\)。 關鍵字與不具引號的值均會忽略開頭與尾端空白字元。

如果值包含分號、[Unicode 控制字元](https://en.wikipedia.org/wiki/Unicode_control_characters) \(英文\)，或者開頭或尾端空白字元，則必須以單引號或雙引號括起來。 例如：

```csharp
Keyword=" whitespace  ";
Keyword='special;character';
```

含括字元可能不會出現在其括起來的值內。 因此，包含單引號的值只能用雙引號括起來，反之亦然：

```csharp
Keyword='double"quotation;mark';
Keyword="single'quotation;mark";
```

您也可以透過一併使用這兩者來逸出含括字元：

```csharp
Keyword="double""quotation";
Keyword='single''quotation';
```

引號本身與等號均不需逸出，因此下列連接字串是有效的：

```csharp
Keyword=no "escaping" 'required';
Keyword=a=b=c
```

因為每個值都會讀取到下一個分號或字串結尾，所以，後者範例中的值是 `a=b=c`，而且最後一個分號是選擇性的。

所有連接字串都會共用上述的相同基本語法。 已辨識的關鍵字組合取決於提供者。 *Microsoft SqlClient* Data Provider for *SQL Server* 支援許多來自較舊 API 的關鍵字，但通常更有彈性，而且可接受許多常用連接字串關鍵字的同義字。

輸入錯誤可能導致錯誤。 例如，`Integrated Security=true` 是有效的，但 `IntegratedSecurity=true` 會導致錯誤。

在執行階段從未經驗證的使用者輸入手動建構的連接字串可能成為字串插入式攻擊的弱點，進而危及資料來源的安全性。 為了解決這些問題，已建立 <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder> 類別。 此[連接字串建立器](connection-string-builders.md)類別會公開參數作為強型別屬性，而且能夠在將連接字串傳送至資料來源之前進行驗證。

## <a name="in-this-section"></a>本節內容

[連接字串建立器](connection-string-builders.md)\
示範如何在執行階段使用 `ConnectionStringBuilder` 類別來建構有效的連接字串。

[連接字串與組態檔](connection-strings-and-configuration-files.md)\
示範如何在組態檔中儲存及擷取連接字串。

[連接字串語法](connection-string-syntax.md)\
描述如何為 `SqlClient` 設定提供者特定的連接字串。

[保護連線資訊](protecting-connection-information.md)\
示範的技術可保護用於連接至資料來源的資訊。

## <a name="see-also"></a>請參閱

- [Microsoft ADO.NET for SQL Server](microsoft-ado-net-sql-server.md)
