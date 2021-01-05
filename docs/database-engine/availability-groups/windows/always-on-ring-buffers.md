---
title: 可用性群組健全狀況資訊的信號緩衝區
description: 使用 SQL Server 環形緩衝區取得 Always On 可用性群組的特定診斷資訊。
ms.custom: seo-lt-2019
ms.date: 06/13/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: availability-groups
ms.topic: how-to
ms.assetid: 47bb7a1a-c0a5-473c-a7db-d9f4bf3ee650
author: rothja
ms.author: jroth
ms.openlocfilehash: 1feb461ad05167b4669da2ee53c17342bba98195
ms.sourcegitcommit: 370cab80fba17c15fb0bceed9f80cb099017e000
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97641428"
---
# <a name="use-ring-buffers-to-obtain-health-information-about-always-on-availability-groups"></a>使用環形緩衝區取得 Always On 可用性群組的健全狀況資訊
[!INCLUDE [SQL Server](../../../includes/applies-to-version/sqlserver.md)]
  某些診斷的 Always On 可用性群組資訊可從 SQL Server 信號緩衝區，或是 sys.dm_os_ring_buffers 動態管理檢視 (DMV) 取得。 信號緩衝區在 SQL Server 啟動期間建立，並針對內部診斷記錄 SQL Server 系統中的警示。 系統不支援它們，但您仍然可以在針對問題進行疑難排解時，從它們擷取有用的資訊。 這些信號緩衝區在 SQL Server 當機或損毀時，提供診斷的其他資源。  
  
 以下 Transact-SQL (T-SQL) 查詢從可用性群組信號緩衝區擷取所有事件記錄。  
  
```sql  
SELECT * FROM sys.dm_os_ring_buffers WHERE ring_buffer_type LIKE '%HADR%'  
```  
  
 若要讓資料更容易管理，請依日期和信號緩衝區類型來篩選資料。 以下查詢會從今天出現的指定信號緩衝區擷取記錄。  
  
```sql  
DECLARE @runtime datetime  
SET @runtime = GETDATE()  
SELECT CONVERT (varchar(30), @runtime, 121) as data_collection_runtime,   
DATEADD (ms, -1 * (inf.ms_ticks - ring.[timestamp]), GETDATE()) AS ring_buffer_record_time,   
ring.[timestamp] AS record_timestamp, inf.ms_ticks AS cur_timestamp, ring.*   
FROM sys.dm_os_ring_buffers ring  
CROSS JOIN sys.dm_os_sys_info inf where ring_buffer_type='<RING_BUFFER_TYPE>'  
```  
  
 每個記錄中的 Record 資料行都包含 XML 格式的診斷資料。 不同的信號緩衝區類型有不同的 XML 資料。 如需每個信號緩衝區類型的詳細資訊，請參閱[可用性群組信號緩衝區類型](#BKMK_RingBufferTypes)。 若要讓 XML 資料更易於閱讀，您需要自訂您的 T-SQL 查詢以擷取所需的 XML 元素。 例如，以下查詢會從 RING_BUFFER_HADRDBMGR_API 信號緩衝區擷取所有事件，並將 XML 資料格式化為個別的資料表資料行。  
  
```sql  
WITH hadr(ts, type, record) AS  
(  
  SELECT timestamp AS ts, ring_buffer_type AS type, CAST(record AS XML) AS record   
  FROM sys.dm_os_ring_buffers WHERE ring_buffer_type = 'RING_BUFFER_HADRDBMGR_API'  
)  
SELECT   
  ts,  
  type,  
  record.value('(./Record/@id)[1]','bigint') AS [Record ID],  
  record.value('(./Record/@time)[1]','bigint') AS [Time],  
  record.value('(./Record/HadrDbMgrAPI/dbId)[1]', 'bigint') AS [DBID],  
  record.value('(/Record/HadrDbMgrAPI/API)[1]', 'varchar(50)') AS [API],  
  record.value('(/Record/HadrDbMgrAPI/Action)[1]', 'varchar(50)') AS [Action],  
  record.value('(/Record/HadrDbMgrAPI/role)[1]', 'int') AS [Role],  
  record.value('(/Record/Stack)[1]', 'varchar(100)') AS [Call Stack]  
FROM hadr  
ORDER BY record.value('(./Record/@time)[1]','bigint') DESC  
GO  
```  
  
##  <a name="availability-groups-ring-buffer-types"></a><a name="BKMK_RingBufferTypes"></a> 可用性群組信號緩衝區類型  
 sys.dm_os_ring_buffers 中有四個可用性群組信號緩衝區。 下表描述信號緩衝區類型和每個信號緩衝區類型的 Record 資料行內容的範例。  
  
 **RING_BUFFER_HADRDBMGR_API**  
  
 記錄已發生或正在發生的狀態轉換。 查看狀態轉換時，請特別注意 objectType 值。  
  
```xml  
<Record id="11" type="RING_BUFFER_HADRDBMGR_STATE" time="860243">  
  <HadrDbMgrState>  
    <objectType>HadrUsers</objectType>  
    <currentState>HDbMState_Starting</currentState>  
    <proposedState>HDbMState_Started</proposedState>  
    <targetState>HDbMState_Started</targetState>  
    <legalTransition>Y</legalTransition>  
    <role>1</role>  
  </HadrDbMgrState>  
</Record>  
```  
  
 **RING_BUFFER_HADRDBMGR_STATE**  
  
 記錄 Always On 活動所建立的內部方法或函式呼叫。 它可以顯示暫止、繼續或角色變更等資訊，且包括進入和離開點。  
  
```xml  
<Record id="45" type="RING_BUFFER_HADRDBMGR_STATE" time="1723487912">  
  <HadrDbMgrState>  
    <dbId>5</dbId>  
    <objectType>HadrDbMgr</objectType>  
    <currentState>HDbMState_Starting</currentState>  
    <proposedState>HDbMState_Started</proposedState>  
    <targetState>HDbMState_Started</targetState>  
    <legalTransition>Y</legalTransition>  
    <role>2</role>  
  </HadrDbMgrState>  
</Record>  
```  
  
 **RING_BUFFER_HADRDBMGR_COMMIT**  
  
```xml  
<Record id="0" type="RING_BUFFER_HADRDBMGR_COMMIT" time="1723475368">  
  <HadrDbMgrCommitPolicy>  
    <dbId>5</dbId>  
    <replicaId>883a18f5-97d5-450f-8f8f-9983a4fa5299</replicaId>  
    <dbHardenPolicy>KillAll</dbHardenPolicy>  
    <dbSyncConfig>0x0</dbSyncConfig>  
    <syncPartnerCount>0</syncPartnerCount>  
    <minSyncPartnerConfig>0</minSyncPartnerConfig>  
    <partnerHardenPolicy>KillAll</partnerHardenPolicy>  
    <partnerSyncConfig>0x0</partnerSyncConfig>  
    <logBlock>0x0000000000000000</logBlock>  
    <leaseExpired>Y</leaseExpired>  
    <partnerChange>N</partnerChange>  
    <role>2</role>  
  </HadrDbMgrCommitPolicy>  
</Record>  
```  
  
 **RING_BUFFER_HADR_TRANSPORT_STATE**  
  
```xml  
<Record id="3" type="RING_BUFFER_HADR_TRANSPORT_STATE" time="1723485399">  
  <HadrTransportState>  
    <agId>08264B79-D10B-412F-B38D-CA07B08E9BD8</agId>  
    <localArId>883A18F5-97D5-450F-8F8F-9983A4FA5299</localArId>  
    <targetArId>628D6349-72DD-4D18-A6E1-1272645660BA</targetArId>  
    <currentState>HadrSession_Configuring</currentState>  
    <targetState>HadrSession_Connected</targetState>  
    <legalTransition>Y</legalTransition>  
  </HadrTransportState>  
</Record>  
```  
  
## <a name="parse-xml-data-from-a-ring-buffer"></a>從信號緩衝區剖析 XML 資料  
 您可以在查詢中使用 [value&#40;&#41; 方法 &#40;xml 資料型別&#41;](~/t-sql/xml/value-method-xml-data-type.md)，剖析正在檢查的信號緩衝區的 Record 欄。 若要使用這個方法，您必須先使用 [CAST](~/t-sql/functions/cast-and-convert-transact-sql.md) 將信號緩衝區中的記錄資料行轉換成 XML。 例如，以下查詢示範如何使用此方法將 RING_BUFFER_HADRDBMGR_API 剖析成可讀取的格式。  
  
```sql 
WITH hadr(ts, type, record) AS  
   (SELECT timestamp AS ts, ring_buffer_type AS type, CAST(record AS XML) AS record   
FROM sys.dm_os_ring_buffers   
WHERE ring_buffer_type = 'RING_BUFFER_HADRDBMGR_API')  
SELECT ts,  
type,  
record.value('(./Record/@id)[1]','bigint') AS [Record id],  
record.value('(./Record/@time)[1]','bigint') AS [Time],  
record.value('(./Record/HadrDbMgrAPI/dbId)[1]', 'bigint') AS [dbid],  
record.value('(/Record/HadrDbMgrAPI/API)[1]', 'varchar(50)') AS [API],  
record.value('(/Record/HadrDbMgrAPI/Action)[1]', 'varchar(50)') AS [Action],  
record.value('(/Record/HadrDbMgrAPI/role)[1]', 'int') AS [Role],  
record.value('(/Record/Stack)[1]', 'varchar(100)') AS [Call Stack]  
FROM hadr  
ORDER BY record.value('(./Record/@time)[1]','bigint') DESC  
GO  
```  
  
  
