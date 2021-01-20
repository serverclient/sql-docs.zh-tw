---
description: '連接到 DB2 (DB2ToSQL) '
title: 連接到 DB2 (DB2ToSQL) |Microsoft Docs
ms.prod: sql
ms.custom: ''
ms.date: 01/19/2017
ms.reviewer: ''
ms.technology: ssma
ms.topic: conceptual
ms.assetid: 9d485fd0-ab5d-402a-a59a-e9982a61b7de
author: nahk-ivanov
ms.author: alexiva
ms.openlocfilehash: c590a76696f2729bf5d1c6c5d4ca5b0242a63df6
ms.sourcegitcommit: d8cdbb719916805037a9167ac4e964abb89c3909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98596727"
---
# <a name="connect-to-db2-db2tosql"></a>連接到 DB2 (DB2ToSQL) 
使用 [ **連接到 DB2** ] 對話方塊來連接到您想要遷移的 DB2 資料庫。  
  
若要存取這個對話方塊，請在 **[檔案** ] 功能表上選取 **[連接到 DB2]**。 如果您先前已連線，命令會 **重新連接至 DB2**。  
  
## <a name="options"></a>選項  
**提供者**  
選取與 DB2 資料庫連接的資料存取提供者。 可用的提供者有 DB2 用戶端提供者和 OLE DB 提供者。 預設值是 DB2 用戶端提供者。  
  
**模式**  
請選取 [標準]、[TNSNAME] 或 [連接字串] 模式。  
  
-   在 [標準] 模式中，您可以輸入或選取提供者、伺服器名稱、伺服器埠、DB2 SID、使用者名稱和密碼的值。  
  
-   在 [TNSNAME] 模式中，您可以輸入 DB2 資料庫的 [連接識別碼] (TNS 別名) 、使用者名稱和密碼。  
  
-   在 [連接字串] 模式中，您會提供連接字串。  
  
    > [!IMPORTANT]  
    > 我們不建議您使用連接字串模式，因為文字可能包含密碼，並以純文字傳送。  
  
預設值為 [標準] 模式。  
  
**伺服器名稱**  
輸入 DB2 伺服器名稱。 預設伺服器名稱與電腦名稱稱相同。 這是標準模式選項。  
  
**伺服器通訊埠**  
如果您使用1521以外的埠號碼 (預設) 連接到 DB2，請輸入埠號碼。 這是標準模式選項。  
  
**連接識別碼**  
輸入 DB2 connect 識別碼。 這是在本機 tnsnames.ora. tnsnames.ora 檔案中定義的資料庫別名。  
  
這是 TNSNAME 模式選項。  
  
**DB2 SID**  
輸入資料庫的 SID。 SID 是區分電腦上 DB2 資料庫的識別碼。 資料庫的預設 SID 是資料庫名稱的前八個字元。  
  
這是標準模式選項。  
  
**使用者名稱**  
輸入 SSMA 將用來連接到 DB2 資料庫的使用者名稱。  
  
**密碼**  
請輸入使用者名稱的密碼。  
  
**連接字串**  
> [!IMPORTANT]  
> 我們不建議您使用連接字串模式，因為文字可能包含密碼，並以純文字傳送。  
  
如果您使用連接字串模式，請輸入 DB2 連接的完整連接字串。  
  
連接字串是由參數名稱和值配對所組成。  
  
-   如 OLE DB 連接字串資訊，請參閱 MSDN Library 上的 [Microsoft OLE DB Provider for DB2](../../ado/guide/appendixes/microsoft-ole-db-provider-for-oracle.md) 文章。  
  
若為 SSMA 連接字串，請一律包含 Provider 參數。 此外，當您連接到 DB2 時，請確定您包含 Port 參數。  
