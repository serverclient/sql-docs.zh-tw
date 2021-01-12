---
title: 在 Azure 虛擬機器上部署 SQL Server
description: 本教學課程示範如何在 Azure 虛擬機器上建立 SQL Server
author: ninarn
ms.author: ninarn
ms.reviewer: alayu, maghan
ms.topic: tutorial
ms.prod: azure-data-studio
ms.technology: azure-data-studio
ms.custom: ''
ms.date: 10/14/2020
ms.openlocfilehash: d73e4b95059dab183f9a0af4896c5a6f86b7e564
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091755"
---
# <a name="create-sql-server-on-azure-virtual-machines-using-azure-data-studio"></a>使用 Azure Data Studio 在 Azure 虛擬機器上建立 SQL Server

您可透過部署精靈和筆記本，使用 Azure Data Studio 建立 SQL 虛擬機器 (VM)。

## <a name="pre-requisites"></a>必要條件

- 已安裝 [Azure Data Studio](download-azure-data-studio.md)
- 作用中的 Azure 帳戶和訂閱。 如果您沒有訂用帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="use-the-deployment-wizard"></a>使用部署精靈

請遵循下列步驟使用部署精靈，這會引導您完成簡單 UI 體驗的必要設定。

首先，在部署精靈中尋找並選取 Azure SQL VM。

1. 在 Azure Data Studio 中，選取左側導覽中的 [連線] viewlet。

2. 選取 [連線] 面板頂端的 [...] 按鈕，然後選擇 [新增部署]。

3. 在部署精靈中，選取 [Azure SQL VM] 磚，然後勾選 [接受條款] 核取方塊

4. 如果出現提示，請安裝必要的工具，然後選取底部的 [選取] 按鈕。

接下來，在 Azure SQL VM 精靈中輸入所有必要的參數。

1. 如果尚未登入，請登入 Azure 帳戶。 如果在此精靈頁面遇到問題，您可重新整理連線。

2. 選取所需要的訂閱、資源群組和區域。 然後選取 [下一步]  。

3. 輸入唯一的虛擬機器名稱和您的使用者名稱及密碼認證。

4. 選取您偏好的映像、SKU 和版本，然後選取您慣用的 VM 大小。 您可深入了解[可用的 VM 大小](/azure/virtual-machines/sizes)以利選擇。 然後選取 [下一步]  。

5. 從下拉式清單中選取現有的虛擬網路，或勾選 [新增虛擬網路] 核取方塊，輸入新虛擬網路的名稱。

6. 請執行相同的動作，選取或建立子網路和公用 IP 位址。

7. 如果您想要透過遠端桌面 (RDP) 連線到 VM，請勾選 [Enable RDP inbound port] \(啟用 RDP 輸入連接埠\) 核取方塊。 然後選取 [下一步]  。

8. 輸入您慣用的 SQL Server 設定。 測試此體驗的建議，是將 SQL 連線設定為 **公用 (網際網路)** 、輸入連接埠 1433，然後使用您慣用的使用者名稱和密碼啟用 SQL 驗證。 然後選取 [下一步]  。

9. 檢查您輸入的參數，然後選取 [Script to Notebook] \(將指令碼編寫至筆記本\)。

筆記本開啟之後，您就可以檢閱內容和程式碼，並隨意變更。 但不建議有所變更，因為這可能會導致驗證錯誤。

最後一個步驟是選取 [全部執行]，以執行筆記本中的所有資料格。 完成後，您應已完全建立並執行：

- Azure 虛擬機器
- SQL 虛擬機器
- 虛擬網路、子網路和公用 IP 位址
- 網路安全性群組和網路介面
- 在 VM 中存取 RDP
- 存取各種 SQL Server 管理功能，以輕鬆控制備份排程、修補排程、SQL Server 版本和授權、儲存體效能組態及其他更多內容。

## <a name="next-steps"></a>後續步驟

若要深入了解如何將資料移轉至新的 SQL VM，請參閱下列文章。

> [!div class="nextstepaction"]
> [將資料庫移轉至 SQL VM](/azure/azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server)

如需在 Azure 中使用 SQL Server 的其他資訊，請參閱 [Azure 虛擬機器上的 SQL Server](/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview) 和[常見問題集](/azure/azure-sql/virtual-machines/windows/frequently-asked-questions-faq)。