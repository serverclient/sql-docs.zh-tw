---
description: isPoolable 方法 (SQLServerStatement)
title: isPoolable 方法 (SQLServerStatement) | Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: connectivity
ms.reviewer: ''
ms.technology: connectivity
ms.topic: conceptual
ms.assetid: b8a12ac5-57cb-4288-9973-c7d5cebd197c
author: David-Engel
ms.author: v-daenge
ms.openlocfilehash: b1b8b3e817ddaf2983d1cfec64099c220e012e16
ms.sourcegitcommit: e700497f962e4c2274df16d9e651059b42ff1a10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88433480"
---
# <a name="ispoolable-method-sqlserverstatement"></a>isPoolable 方法 (SQLServerStatement)
[!INCLUDE[Driver_JDBC_Download](../../../includes/driver_jdbc_download.md)]

  傳回值，這個值指出陳述式是否可以加入至使用者提供的陳述式集區。  
  
## <a name="syntax"></a>語法  
  
```  
  
public boolean isPoolable() throws SQLException  
```  
  
## <a name="return-value"></a>傳回值  
 若陳述式可以新增至使用者提供的陳述式集區，即為 **true**；否則為 **false**。  
  
## <a name="exceptions"></a>例外狀況  
 [SQLServerException](../../../connect/jdbc/reference/sqlserverexception-class.md)  
  
## <a name="remarks"></a>備註  
 [setPoolable](../../../connect/jdbc/reference/setpoolable-method-sqlserverstatement.md) 會變更物件的可共用行為。  
  
## <a name="see-also"></a>另請參閱  
 [SQLServerStatement 成員](../../../connect/jdbc/reference/sqlserverstatement-members.md)   
 [SQLServerStatement 類別](../../../connect/jdbc/reference/sqlserverstatement-class.md)  
  
  
