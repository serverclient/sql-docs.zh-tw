---
title: 可用性群組的叢集 DTC 服務
description: '描述針對 Always On 可用性群組叢集化 Microsoft Distributed Transaction Coordinator (DTC) 服務的需求和步驟。 '
ms.custom: seo-lt-2019
ms.date: 08/30/2016
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
ms.assetid: a47c5005-20e3-4880-945c-9f78d311af7a
author: cawrites
ms.author: chadam
monikerRange: '>=sql-server-2016'
ms.openlocfilehash: 4fd420fd7d07af5a6efa81cdb7e716020c03a89b
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091796"
---
# <a name="how-to-cluster-the-dtc-service-for-an-always-on-availability-group"></a>如何叢集化 Always On 可用性群組的 DTC 服務

[!INCLUDE[sql windows only](../../../includes/applies-to-version/sql-windows-only.md)]

本主題描述針對 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]叢集化 Microsoft Distributed Transaction Coordinator (DTC) 服務的需求和步驟。 如需有關分散式交易和 [!INCLUDE[ssHADR](../../../includes/sshadr-md.md)]的其他資訊，請參閱 [AlwaysOn 可用性群組和資料庫鏡像的跨資料庫交易和分散式交易 (SQL Server)](../../../database-engine/availability-groups/windows/transactions-always-on-availability-and-database-mirroring.md)。

 ## <a name="checklist-preliminary-requirements"></a>檢查清單︰初步需求

|Task|參考|  
|-----------------|----------|  
|確定所有節點、服務和可用性群組皆已正確設定。|[AlwaysOn 可用性群組的必要條件、限制和建議 (SQL Server)](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)|
|確定已符合可用性群組 DTC 需求。|[AlwaysOn 可用性群組和資料庫鏡像的跨資料庫交易和分散式交易 (SQL Server)](../../../database-engine/availability-groups/windows/transactions-always-on-availability-and-database-mirroring.md)

## <a name="checklist-clustered-dtc-resource-dependencies"></a>檢查清單：叢集 DTC 資源相依性

|Task|參考|  
|-----------------|----------|  
|共用存放磁碟機。|[Configuring the Shared-Storage Drive](https://msdn.microsoft.com/library/cc982358(v=bts.10).aspx)(設定共用存放磁碟機)。 請考慮使用磁碟機代號 **M**。|
|唯一的 DTC 網路名稱資源。  此名稱將會在 Active Directory 中註冊作為叢集電腦物件。<br /><br />請確定下列任一條件成立：<br /><br />• 建立 DTC 網路名稱資源的使用者，具有 DTC 網路名稱資源所在 OU 或容器的建立電腦物件權限。<br /><br />• 如果使用者沒有建立電腦物件權限，則請網域系統管理員為 DTC 網路名稱資源預先設置叢集電腦物件。|[在 Active Directory 網域服務中預先設置叢集電腦物件](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn466519(v=ws.11))|
|有效的可用靜態 IP 位址及該 IP 位址的適當子網路遮罩。||

## <a name="cluster-the-dtc-resource"></a>叢集化 DTC 資源
建立可用性群組資源之後，請建立叢集 DTC 資源，並將它加入可用性群組。  您可以在[建立 AlwaysOn 可用性群組的叢集 DTC](../../../database-engine/availability-groups/windows/create-clustered-dtc-for-an-always-on-availability-group.md)中看到範例指令碼。


## <a name="checklist-post-clustered-dtc-resource-configurations"></a>檢查清單：張貼叢集 DTC 資源設定

|Task|參考|  
|-----------------|----------|  
|安全地啟用叢集 DTC 資源的網路存取。|[安全地啟用 MS DTC 的網路存取](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753620(v=ws.10))|
|停止並停用本機 DTC 服務。|[設定如何啟動服務](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc755249(v=ws.11))|
|針對可用性群組中的每個執行個體輪流使用 SQL Server 服務。  視需要容錯移轉可用性群組。|[執行可用性群組的已規劃手動容錯移轉 (SQL Server)](../../../database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server.md)<br /><br />[啟動、停止、暫停、繼續、重新啟動 Database Engine、SQL Server Agent 或 SQL Server Browser 服務](../../../database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services.md)|

- 如果伺服器是 Windows Server 2012 R2，則作業系統必須套用 [KB 3030373](https://support.microsoft.com/kb/3090973) 。

- 根據 [AlwaysOn 可用性群組的必要條件、限制和建議](../../../database-engine/availability-groups/windows/prereqs-restrictions-recommendations-always-on-availability.md)中的檢查清單來準備可用性群組的伺服器。

- 設定 [**AlwaysOn 可用性群組**](../../../database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server.md)的伺服器執行個體。

### <a name="resources"></a>資源


[在可用性群組上測試 DTC 的詳細資訊：](/archive/blogs/dataplatform/sql-server-2016-dtc-support-in-availability-groups)

[監視 AlwaysOn 可用性群組系統檢視](monitor-availability-groups-transact-sql.md)

[逐步建立可用性群組](create-an-availability-group-transact-sql.md)


[SQL Server 2016 DTC Support in Availability Groups](/archive/blogs/dataplatform/sql-server-2016-dtc-support-in-availability-groups) (可用性群組中的 SQL Server 2016 DTC 支援) 

[External link:Configure DTC for a clustered instance of SQL Server with Windows Server 2008 R2](https://sqlha.com/2013/03/12/how-to-properly-configure-dtc-for-clustered-instances-of-sql-server-with-windows-server-2008-r2/) (外部連結：使用 Windows Server 2008 R2 設定 SQL Server 叢集執行個體的 DTC)