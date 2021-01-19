---
description: 為現有加密資料行啟用具有安全記憶體保護區的 Always Encrypted
title: 為現有加密資料行啟用具有安全記憶體保護區的 Always Encrypted | Microsoft Docs
ms.custom: ''
ms.date: 01/15/2021
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
monikerRange: '>= sql-server-ver15'
ms.openlocfilehash: 03a57949624897b2cd2e0552892803c21a408b52
ms.sourcegitcommit: 8ca4b1398e090337ded64840bcb8d6c92d65c29e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98534807"
---
# <a name="enable-always-encrypted-with-secure-enclaves-for-existing-encrypted-columns"></a>為現有加密資料行啟用具有安全記憶體保護區的 Always Encrypted 
[!INCLUDE [sqlserver2019-windows-only-asdb](../../../includes/applies-to-version/sqlserver2019-windows-only-asdb.md)]

本文描述如何解除鎖定具有安全記憶體保護區的 Always Encrypted 功能，以及如何針對現有的加密資料行啟用記憶體保護區計算。  

如果現有資料行是以未啟用記憶體保護區的金鑰加密，您可以使用已啟用記憶體保護區的金鑰來加密資料行。 這麼做可讓您使用安全記憶體保護區在資料行上進行查詢。

您可以使用幾種不同的方式，為現有的加密資料行啟用記憶體保護區計算，視下列情況而定：

- **範圍/細微性：** 您要啟用一小部分資料行的記憶體保護區功能，還是啟用使用指定資料行主要金鑰保護之所有資料行的記憶體保護區功能？
- **資料大小：** 包含您要設為啟用記憶體保護區之資料行的資料表大小為何？
- 您也要變更資料行的加密類型嗎？ 請記住，唯一的隨機化加密支援豐富計算 (模式比對、比較運算子)。 如果資料行是使用確定性加密來加密，則也需要使用隨機化加密來重新加密，以解除鎖定豐富計算。

下列各節描述為現有資料行啟用記憶體保護區的三種方法。

## <a name="method-1-rotate-the-column-master-key-to-replace-it-with-an-enclave-enabled-column-master-key"></a>方法 1：輪替資料行主要金鑰，將其取代為已啟用記憶體保護區的資料行主要金鑰
將現有資料行主要金鑰 (未啟用記憶體保護區) 更換成已啟用記憶體保護區的新資料行主要金鑰，會有效地將所有資料行加密金鑰 (與資料行主要金鑰建立關聯) 設為已啟用記憶體保護區。

- 優點：
  - 不會涉及重新加密資料，因此它通常是最快的方法。 如果資料行包含大量資料、已啟用豐富計算並使用確定性加密，則建議使用這個方法。
  - 可以啟用多個任意大小之資料行的記憶體保護區功能。 將資料行主要金鑰取代為已啟用記憶體保護區的資料行主要金鑰，會將與原始資料行主要金鑰建立關聯的所有資料行加密金鑰和所有加密資料行設為已啟用記憶體保護區。
  
- 缺點：
  - 不支援將加密類型從確定性變更為隨機化。 雖然可針對使用確定性加密進行加密的資料行解除鎖定就地加密，但不會啟用豐富計算。 您需要使用隨機化加密來重新加密資料行。
  - 不允許選擇性地轉換與指定資料行主要金鑰建立關聯的部分資料行。
  - 引進金鑰管理額外負荷。 您需要建立新的資料行主要金鑰，並提供給查詢受影響資料行的應用程式使用。

如需如何輪替資料行主要金鑰的資訊，請參閱[輪替已啟用記憶體保護區的金鑰](always-encrypted-enclaves-rotate-keys.md)。

## <a name="method-2-rotate-the-column-master-key-and-re-encrypt-columns-using-randomized-encryption-in-place"></a>方法 2：輪替資料行主要金鑰並使用隨機化加密就地重新加密資料行
這個方法會涉及執行方法 1 作為第一個步驟，然後重新加密資料行。 資料行一開始是使用確定性加密，然後使用隨機化加密來重新加密，以解除鎖定豐富查詢。

- 優點：
  - 就地重新加密資料。 如果您需要針對目前使用確定性加密並包含大量資料的加密資料行來啟用豐富查詢，則建議使用這個方法。 步驟 1 (資料行主要金鑰輪替) 會使用確定性加密來解除鎖定資料行的就地加密，以允許就地執行步驟 2 (重新加密資料行)。
  - 可以啟用多個任意大小之資料行的記憶體保護區功能。
  
- 缺點：
  - 不允許選擇性地轉換與指定資料行主要金鑰建立關聯的部分資料行。
  - 引進金鑰管理額外負荷。 您需要建立新的資料行主要金鑰，並提供給查詢受影響資料行的應用程式使用。

如需如何輪替資料行主要金鑰，以及就地重新加密資料行以輪替資料行加密金鑰的資訊，請參閱[輪替已啟用記憶體保護區的金鑰](always-encrypted-enclaves-rotate-keys.md)。

## <a name="method-3-re-encrypt-a-selected-column-with-an-enclave-enabled-column-encryption-key-on-the-client-side"></a>方法 3：在用戶端使用已啟用記憶體保護區的資料行加密金鑰來重新加密選取的資料行
這個方法會涉及使用已啟用記憶體保護區的資料行加密金鑰來重新加密資料行，然後使用隨機化機密來啟用豐富查詢。 由於目前的資料行加密金鑰未啟用記憶體保護區，因此無法就地重新加密資料行。 請使用 [Always Encrypted 精靈] 或 Set-SqlColumnEncryption Cmdlet 來重新加密資料庫外部的資料行。

- 優點：
  - 可讓您選擇性地啟用一個資料行或一小部分資料行的記憶體保護區功能 (就地加密，以及使用隨機化加密來重新加密資料行時的豐富查詢)。
  - 可透過一個步驟來針對資料行啟用豐富計算。
  - 不需要建立新的資料行主要金鑰，因此對應用程式的影響較小。
  
- 缺點：
  - 若要重新加密資料，此工具會將資料移出資料庫，這可能需要很長時間且容易發生網路錯誤。

如需如何透過用戶端工具輪替資料行加密金鑰的詳細資訊，請參閱[使用 SQL Server Management Studio 輪替 Always Encrypted 金鑰](rotate-always-encrypted-keys-using-ssms.md)和[使用 PowerShell 輪替 Always Encrypted 金鑰](rotate-always-encrypted-keys-using-powershell.md)。

## <a name="next-steps"></a>後續步驟
- [使用安全記憶體保護區執行 Transact-SQL 陳述式](always-encrypted-enclaves-query-columns.md)
- [使用具有安全記憶體保護區的 Always Encrypted 開發應用程式](always-encrypted-enclaves-client-development.md)
