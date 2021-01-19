---
title: 選擇加密演算法 | Microsoft Docs
description: 使用本指導方針來選擇加密演算法，以協助保護 SQL Server 的執行個體，以支援數個常見的演算法。
ms.custom: ''
ms.date: 08/14/2018
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: vanto
ms.technology: security
ms.topic: conceptual
helpviewer_keywords:
- cryptography [SQL Server], algorithms
- encryption [SQL Server], algorithms
- security [SQL Server], encryption
- algorithms [SQL Server encryption]
ms.assetid: 8227028c-a9c9-489d-bd27-fbf8242634ae
author: jaszymas
ms.author: jaszymas
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 8fb31365f95cddc23739db02df9ba4ad9db0345e
ms.sourcegitcommit: f29f74e04ba9c4d72b9bcc292490f3c076227f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98171810"
---
# <a name="choose-an-encryption-algorithm"></a>選擇加密演算法
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sql-asdb-asdbmi-asa-pdw.md)]
  加密是全面防禦中的一環，可提供給想要維護 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)]執行個體安全的管理員使用。  
  
 加密演算法會定義資料轉換，讓未經授權的使用者無法輕鬆地反轉資料轉換。 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 可讓管理員和開發人員在數種演算法中進行選擇，包括 DES、Triple DES、TRIPLE_DES_3KEY、RC2、RC4、128 位元 RC4、DESX、128 位元 AES、192 位元 AES 和 256 位元 AES。  
  
> [!NOTE]  
>  開頭為 [!INCLUDE[ssSQL15](../../../includes/sssql16-md.md)]，取代 AES_128、AES_192 和 AES_256 以外的所有演算法。 若要使用較舊的演算法 (不建議使用)，您必須將資料庫相容性層級設定為 120 或更低。  
  
 同一種演算法不可能適用於所有情況，各種演算法優缺點的指南也超出《 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 線上叢書》的範疇。 不過，下列為一般適用的原則：  
  
-   強式加密通常比弱式加密耗用更多 CPU 資源。  
  
-   長金鑰通常會比短金鑰產生更強的加密。  
  
-   非對稱式加密比對稱式加密還要慢。  
  
-   複雜的長密碼比短密碼更強。  

-   只有在對稱式加密金鑰儲存在本機時，通常才會建議使用對稱式加密，而在需要跨網路共用金鑰時，則建議使用非對稱式加密。
  
-   如果您要加密很多資料，應該使用對稱金鑰來加密資料，並使用非對稱金鑰來加密對稱金鑰。  
  
-   加密的資料無法壓縮，但壓縮資料可以加密。 如果您使用壓縮，應該在加密之前先壓縮資料。  
  
> [!IMPORTANT]  
>  只有 RC4 演算法支援回溯相容性。 只有在資料庫相容性層級為 90 或 100 時，才能使用 RC4 或 RC4_128 加密新資料 (不建議使用)。請改用較新的演算法，例如其中一個 AES 演算法。 在 [!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] 和更新版本中使用 RC4 或 RC4_128 加密的資料，可以在任何相容性層級進行解密。  
>   
>  在不同的資料區塊上重複使用相同的 RC4 或 RC4_128 KEY_GUID，結果會是相同的 RC4 金鑰，因為 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] 不會自動提供 Salt。 重複使用相同的 RC4 金鑰是已知的錯誤，此錯誤會造成加密變弱。 因此，我們取代了 RC4 和 RC4_128 關鍵字。 [!INCLUDE[ssNoteDepFutureDontUse](../../../includes/ssnotedepfuturedontuse-md.md)]  
  
 如需加密演算法和加密技術的詳細資訊，請參閱 MSDN 上《.NET Framework 開發人員手冊》中的 [重要的安全性概念](/previous-versions/aa720225(v=vs.71)) 。  
  
 **釐清有關 DES 演算法的資訊：**  
  
-   DESX 未正確命名。 以 ALGORITHM = DESX 建立的對稱金鑰實際上使用具有 192 位元金鑰的 TRIPLE DES 加密。 未提供 DESX 演算法。 [!INCLUDE[ssNoteDepFutureAvoid](../../../includes/ssnotedepfutureavoid-md.md)]  
  
-   以 ALGORITHM = TRIPLE_DES_3KEY 建立的對稱金鑰使用具有 192 位元金鑰的 TRIPLE DES。  
  
-   以 ALGORITHM = TRIPLE_DES 建立的對稱金鑰使用具有 128 位元金鑰的 TRIPLE DES。  
  
## <a name="related-tasks"></a>相關工作  
  
| Task | 類型 |
| ---- | ---- |
|使用對稱金鑰加密。|[CREATE SYMMETRIC KEY &#40;Transact-SQL&#41;](../../../t-sql/statements/create-symmetric-key-transact-sql.md)|  
|使用非對稱金鑰加密。|[CREATE ASYMMETRIC KEY &#40;Transact-SQL&#41;](../../../t-sql/statements/create-asymmetric-key-transact-sql.md)|  
|使用憑證加密。|[CREATE CERTIFICATE &#40;Transact-SQL&#41;](../../../t-sql/statements/create-certificate-transact-sql.md)|  
|使用透明資料加密來加密資料庫檔案。|[透明資料加密 &#40;TDE&#41;](../../../relational-databases/security/encryption/transparent-data-encryption.md)|  
|如何加密資料表的一個資料行。|[加密資料行](../../../relational-databases/security/encryption/encrypt-a-column-of-data.md)|  
  
## <a name="see-also"></a>另請參閱  
 [SQL Server 加密](../../../relational-databases/security/encryption/sql-server-encryption.md)   
 [加密階層](../../../relational-databases/security/encryption/encryption-hierarchy.md)  
  
