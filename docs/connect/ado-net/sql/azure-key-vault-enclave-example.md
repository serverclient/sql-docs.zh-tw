---
description: 示範如何搭配透過安全記憶體保護區啟用的 Always Encrypted 使用 Azure Key Vault 提供者的範例
title: 示範如何搭配透過安全記憶體保護區啟用的 Always Encrypted 使用 Azure Key Vault 提供者的範例 | Microsoft Docs
ms.custom: ''
ms.date: 11/17/2020
ms.reviewer: v-kaywon
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.tgt_pltfrm: ''
ms.topic: tutorial
author: karinazhou
ms.author: v-jizho2
ms.openlocfilehash: 1feed9699d925c47ae7d38ab72db5b366ad00ba8
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771532"
---
# <a name="example-demonstrating-use-of-azure-key-vault-provider-with-always-encrypted-enabled-with-secure-enclaves"></a>示範如何搭配透過安全記憶體保護區啟用的 Always Encrypted 使用 Azure Key Vault 提供者的範例

[!INCLUDE [sqlserver2019-windows-only](../../../includes/applies-to-version/sqlserver2019-windows-only.md)]

[!INCLUDE [appliesto-netfx-netcore-xxxx-md](../../../includes/appliesto-netfx-netcore-netst-md.md)]

此範例示範如何在存取加密資料行時使用 Azure Key Vault 提供者。

[!code-csharp [Azure Key Vault Provider with Enclave Example#1](~/../sqlclient/doc/samples/AzureKeyVaultProviderWithEnclaveProviderExample.cs#1)]

> [!NOTE]
> - 若要針對 .NET Standard 應用程式使用具有安全記憶體保護區的 Always Encrypted，需要 **Microsoft.Data.SqlClient** 2.1.0 版或更新版本。 支援的 .NET Standard 版本為 2.1 或更新版本。 
>
> - 若要在 Linux 與 macOS 上使用具有安全記憶體保護區的 Always Encrypted，需要 **Microsoft.Data.SqlClient** 2.1.0 版或更新版本。

## <a name="see-also"></a>請參閱

- [示範如何搭配 Always Encrypted 使用 Azure Key Vault 提供者的範例](azure-key-vault-example.md)
- [教學課程：使用具有安全記憶體保護區的 Always Encrypted 開發 .NET 應用程式](tutorial-always-encrypted-enclaves-develop-net-apps.md)
- [搭配 Microsoft .NET Data Provider for SQL Server 使用 Always Encrypted](sqlclient-support-always-encrypted.md)
