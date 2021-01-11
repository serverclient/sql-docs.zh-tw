---
title: 伺服器屬性 (處理器頁面)
description: 熟悉 SQL Server 中的處理器設定。 了解哪些選項可以控制背景工作執行緒的數目、處理器指派及其他屬性。
ms.prod: sql
ms.prod_service: high-availability
ms.technology: configuration
ms.topic: conceptual
f1_keywords:
- sql13.swb.serverproperties.processor.f1
ms.assetid: cc1581a2-492b-41f0-bda5-17909b65c4f7
author: markingmyname
ms.author: maghan
ms.reviewer: drskwier, matteot
ms.custom: ''
ms.date: 12/17/2020
ms.openlocfilehash: 874cbbae2b418e9b9e06c7a95d62d34a99af38f5
ms.sourcegitcommit: a81823f20262227454c0b5ce9c8ac607aaf537e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97684210"
---
# <a name="server-properties-processors-page"></a>伺服器屬性 (處理器頁面)

[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

使用此頁面來檢視或修改處理器選項。 只有在安裝了一個以上的處理器時，處理器相似性設定才會啟用。  

## <a name="options"></a>選項。

### <a name="processor-affinity"></a>處理器相似性
將處理器指派給特定的執行緒，以避免執行處理器重新載入，並減少在處理器間進行執行緒的移轉。 如需詳細資訊，請參閱 [affinity mask 伺服器組態選項](../../database-engine/configure-windows/affinity-mask-server-configuration-option.md)。

### <a name="io-affinity"></a>I/O 相似性
將 [!INCLUDE[msCoName](../../includes/msconame-md.md)] SQL Server 磁碟 I/O 繫結至指定的 CPU 子集。 如需詳細資訊，請參閱 [affinity I/O mask 伺服器組態選項](../../database-engine/configure-windows/affinity-input-output-mask-server-configuration-option.md)。

### <a name="automatically-set-processor-affinity-mask-for-all-processors"></a>自動設定所有處理器的處理器相似性遮罩
允許 SQL Server 設定處理器親和性。

### <a name="automatically-set-io-affinity-mask-for-all-processors"></a>自動設定所有處理器的 I/O 相似性遮罩
允許 SQL Server 設定 I/O 親和性。

### <a name="maximum-worker-threads"></a>最大工作者執行緒
0 允許 SQL Server 動態設定背景工作執行緒的數目。 此設定對大多數系統都是最佳的。 然而，視系統組態而定，將此選項設定成特定的值，有時候可以提高效能。 如需詳細資訊，請參閱 [設定 max worker threads 伺服器組態選項](../../database-engine/configure-windows/configure-the-max-worker-threads-server-configuration-option.md)。  

### <a name="boost-sql-server-priority"></a>提高 SQL Server 優先權
指定 SQL Server 是否應使用比同一部電腦上其他處理序更高的 Microsoft Windows 排程優先順序來執行。 如需詳細資訊，請參閱 [設定 priority boost 伺服器組態選項](../../database-engine/configure-windows/configure-the-priority-boost-server-configuration-option.md)。  

> [!Note]
> 此選項不適用 SSMS 18.x 與更新版本。

### <a name="use-windows-fibers-lightweight-pooling"></a>使用 Windows Fibers (輕量型共用)
針對 SQL Server 服務使用 Windows Fiber 而非執行緒。 這只適用於 Windows 2003 Server Edition。 如需詳細資訊，請參閱 [輕量型共用伺服器組態選項](../../database-engine/configure-windows/lightweight-pooling-server-configuration-option.md)。

> [!Note]
> 此選項不適用 SSMS 18.x 與更新版本。

### <a name="configured-values"></a>設定的值
針對此窗格中的選項，顯示設定的值。 如果您變更這些值，請選取 [執行中的值]，即可查看變更是否已生效。 如果沒有，則必須先重新啟動 SQL Server 的執行個體。

### <a name="running-values"></a>[執行中的值]
針對此窗格中的選項，檢視目前執行中的值。 這些值是唯讀的。

## <a name="see-also"></a>另請參閱
[伺服器組態選項 &#40;SQL Server&#41;](../../database-engine/configure-windows/server-configuration-options-sql-server.md)  


