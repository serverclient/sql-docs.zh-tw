---
description: 教學課程：使用具有安全記憶體保護區的 Always Encrypted 開發 .NET 應用程式
title: 教學課程：使用具有安全記憶體保護區的 Always Encrypted 開發 .NET 應用程式 | Microsoft Docs
ms.custom: ''
ms.date: 07/09/2020
ms.reviewer: v-kaywon
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.tgt_pltfrm: ''
ms.topic: tutorial
author: karinazhou
ms.author: v-jizho2
ms.openlocfilehash: 59c377d5055e8eb3858e1005c50d9c4dfb8334ab
ms.sourcegitcommit: c938c12cf157962a5541347fcfae57588b90d929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2020
ms.locfileid: "97771524"
---
# <a name="tutorial-develop-a-net-application-using-always-encrypted-with-secure-enclaves"></a>教學課程：使用具有安全記憶體保護區的 Always Encrypted 開發 .NET 應用程式

[!INCLUDE [sqlserver2019-windows-only](../../../includes/applies-to-version/sqlserver2019-windows-only.md)]

[!INCLUDE [appliesto-netfx-netcore-xxxx-md](../../../includes/appliesto-netfx-netcore-xxxx-md.md)]

本教學課程會教您如何開發簡單的應用程式來發出資料庫查詢，其使用[具有安全記憶體保護區 Always Encrypted](../../../relational-databases/security/encryption/always-encrypted-enclaves.md) 的伺服器端安全記憶體保護區。

> [!NOTE]
> 只有 Windows 支援具有安全記憶體保護區的 Always Encrypted。

## <a name="prerequisites"></a>必要條件

本教學課程是[教學課程：使用 SSMS，開始使用具有安全記憶體保護區的 Always Encrypted](../../../relational-databases/security/tutorial-getting-started-with-always-encrypted-enclaves.md)。 請確定您已完成之後，再繼續進行下列步驟。

此外，您還需要 Visual Studio (建議使用版本 2019)；您可以從 [https://visualstudio.microsoft.com/](https://visualstudio.microsoft.com) 進行下載。 您的應用程式開發環境必須使用 .NET Framework 4.6 或更新版本，或是 .NET Core 2.1 或更新版本。

## <a name="step-1-set-up-your-visual-studio-project"></a>步驟 1:設定 Visual Studio 專案

若要在 .NET Framework 應用程式中使用具有安全記憶體保護區的 Always Encrypted，您必須確定您的應用程式目標為 .NET Framework 4.6 或更新版本。 若要在 .NET Core 應用程式中使用具有安全記憶體保護區的 Always Encrypted，您必須確定您的應用程式目標為 .NET Core 2.1 或更新版本。

此外，如果您將資料行主要金鑰儲存於 Azure Key Vault，也需要整合應用程式與 [Microsoft.Data.SqlClient.AlwaysEncrypted.AzureKeyVaultProvider NuGet](https://www.nuget.org/packages/Microsoft.Data.SqlClient.AlwaysEncrypted.AzureKeyVaultProvider) \(英文\)。

1. 開啟 Visual Studio。

2. 建立新的 C\# 主控台應用程式 (.NET Framework / Core) 專案。

3. 確定您的專案目標至少為 .NET Framework 4.6 或 .NET Core 2.1。 以滑鼠右鍵按一下 [方案總管] 中的專案、選取 [屬性]，然後設定目標 Framework。

4. 移至 [工具]\(主功能表) > [NuGet 套件管理員] > [套件管理員主控台]，以安裝下列 NuGet 套件。 在 [套件管理員主控台] 中，執行下列程式碼。

   ```powershell
   Install-Package Microsoft.Data.SqlClient -Version 1.1.0
   ```

5. 如果您使用 Azure Key Vault 來儲存資料行主要金鑰，請移至 [工具]\(主功能表) > [NuGet 套件管理員] > [套件管理員主控台]，以安裝下列 NuGet 套件。 在 [套件管理員主控台] 中，執行下列程式碼。

   ```powershell
   Install-Package Microsoft.Data.SqlClient.AlwaysEncrypted.AzureKeyVaultProvider -Version 1.0.0
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

6. 在連接字串中指定 `Attestation Protocol` 和 `Enclave Attestation Url`，以便在應用程式中用來與 SQL Server 通訊。

  ```cs
   Attestation Protocol = HGS; Enclave Attestation Url = http://hgs.bastion.local/Attestation; Column Encryption Setting = Enabled
   ```

## <a name="step-2-implement-your-application-logic"></a>步驟 2:實作您的應用程式邏輯

您的應用程式將會連線到 **ContosoHR** 資料庫 (來自 [教學課程：使用 SSMS 開始使用具有安全記憶體保護區的 Always Encrypted](../../../relational-databases/security/tutorial-getting-started-with-always-encrypted-enclaves.md))，且其將會執行查詢，其中包含 **SSN** 資料行上的 `LIKE` 述詞和 **Salary** 資料行上的範圍比較。

1. 將 Program.cs 檔案 (由 Visual Studio 產生) 的內容取代為下列程式碼。 使用您伺服器名稱和環境的記憶體保護區證明 URL 來更新資料庫連接字串。 您也可以更新資料庫驗證設定。

    ```cs
    using System;
    using Microsoft.Data.SqlClient;
    using System.Data;

    namespace ConsoleApp1
    {
        class Program
        {
            static void Main(string[] args)
            {

                string connectionString = "Data Source = myserver; Initial Catalog = ContosoHR; Column Encryption Setting = Enabled;Attestation Protocol = HGS; Enclave Attestation Url = http://hgs.bastion.local/Attestation; Integrated Security = true";

                using (SqlConnection connection = new SqlConnection(connectionString))
                {
                    connection.Open();

                    SqlCommand cmd = connection.CreateCommand();
                    cmd.CommandText = @"SELECT [SSN], [FirstName], [LastName], [Salary] FROM [dbo].[Employees] WHERE [SSN] LIKE @SSNPattern AND [Salary] > @MinSalary;";

                    SqlParameter paramSSNPattern = cmd.CreateParameter();

                    paramSSNPattern.ParameterName = @"@SSNPattern";
                    paramSSNPattern.DbType = DbType.AnsiStringFixedLength;
                    paramSSNPattern.Direction = ParameterDirection.Input;
                    paramSSNPattern.Value = "%1111";
                    paramSSNPattern.Size = 11;

                    cmd.Parameters.Add(paramSSNPattern);

                    SqlParameter MinSalary = cmd.CreateParameter();

                    MinSalary.ParameterName = @"@MinSalary";
                    MinSalary.DbType = DbType.Currency;
                    MinSalary.Direction = ParameterDirection.Input;
                    MinSalary.Value = 900;

                    cmd.Parameters.Add(MinSalary);
                    cmd.ExecuteNonQuery();

                    SqlDataReader reader = cmd.ExecuteReader();
                    while (reader.Read())

                    {
                        Console.WriteLine(reader);
                        Console.WriteLine(reader[0] + ", " + reader[1] + ", " + reader[2] + ", " + reader[3]);
                    }
                    Console.ReadKey();
                }
            }
        }
    }
    ```

2. 建置並執行應用程式。

## <a name="see-also"></a>請參閱

- [搭配 Microsoft .NET Data Provider for SQL Server 使用 Always Encrypted](sqlclient-support-always-encrypted.md)
- [示範如何搭配 Always Encrypted 使用 Azure Key Vault 提供者的範例](azure-key-vault-example.md)
- [示範如何搭配透過安全記憶體保護區啟用的 Always Encrypted 使用 Azure Key Vault 提供者的範例](azure-key-vault-enclave-example.md)
