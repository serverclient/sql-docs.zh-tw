---
description: 引發並在自訂工作中定義事件
title: 在自訂工作中引發和定義事件 | Microsoft Docs
ms.custom: ''
ms.date: 03/04/2017
ms.prod: sql
ms.prod_service: integration-services
ms.reviewer: ''
ms.technology: integration-services
ms.topic: reference
dev_langs:
- VB
- CSharp
helpviewer_keywords:
- SSIS events, custom
- status information [SQL Server], task events
- custom tasks [Integration Services], events
- SSIS custom tasks, events
- IDTSComponentEvents interface
- events [Integration Services], custom
- events [Integration Services], runtime
- custom events [Integration Services]
- SSIS events, runtime
- IDTSEvents interface
ms.assetid: e0898aa1-e90c-4c4e-99d4-708a76efddfd
author: chugugrace
ms.author: chugu
ms.openlocfilehash: 1fcafce23542f1d5d1ea1756cdb361d5197286b6
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98091588"
---
# <a name="raising-and-defining-events-in-a-custom-task"></a>引發並在自訂工作中定義事件

[!INCLUDE[sqlserver-ssis](../../../includes/applies-to-version/sqlserver-ssis.md)]


  [!INCLUDE[ssISnoversion](../../../includes/ssisnoversion-md.md)] 執行階段引擎提供事件的集合，可以顯示驗證與執行工作時的工作進度狀態。 <xref:Microsoft.SqlServer.Dts.Runtime.IDTSComponentEvents> 介面會定義這些事件，而且是以 <xref:Microsoft.SqlServer.Dts.Runtime.Executable.Validate%2A> 和 <xref:Microsoft.SqlServer.Dts.Runtime.Executable.Execute%2A> 方法的參數提供給工作。  
  
 另一組定義在 <xref:Microsoft.SqlServer.Dts.Runtime.IDTSEvents> 介面的事件，是由 <xref:Microsoft.SqlServer.Dts.Runtime.TaskHost> 代表工作而引發。 <xref:Microsoft.SqlServer.Dts.Runtime.TaskHost> 會引發在驗證和執行前後發生的事件，而工作會引發在執行和驗證期間發生的事件。  
  
## <a name="creating-custom-events"></a>建立自訂事件  
 自訂工作開發人員可以透過在 <xref:Microsoft.SqlServer.Dts.Runtime.EventInfo> 方法的覆寫實作中建立新的 <xref:Microsoft.SqlServer.Dts.Runtime.Task.InitializeTask%2A>，以定義新自訂事件。 在建立 <xref:Microsoft.SqlServer.Dts.Runtime.EventInfo> 之後，會使用 <xref:Microsoft.SqlServer.Dts.Runtime.EventInfos.Add%2A> 方法，將它新增至 **EventInfos** 集合。 <xref:Microsoft.SqlServer.Dts.Runtime.EventInfos.Add%2A> 方法的方法簽章如下所示：  
  
 `public void Add(string eventName, string description, bool allowEventHandlers, string[] parameterNames, TypeCode[] parameterTypes, string[] parameterDescriptions);`  
  
 下列程式碼範例會示範自訂工作的 **InitializeTask** 方法，其中建立了兩個自訂事件並設定其屬性。 然後會將新事件加入 <xref:Microsoft.SqlServer.Dts.Runtime.EventInfos> 集合。  
  
 第一個自訂事件的 *eventName* 為 "**OnBeforeIncrement**"，而 *description* 為「**更新初始值之後觸發**」。 下一個參數 **true** 值表示此事件應允許建立事件處理常式容器以處理事件。 事件處理常式是提供封裝中結構與工作服務的容器，就像封裝、時序、ForLoop 和 ForEachLoop 等其他容器一樣。 當 *allowEventHandlers* 參數是 **true** 時，會針對事件建立 <xref:Microsoft.SqlServer.Dts.Runtime.DtsEventHandler> 物件。 為事件定義的任何參數現在可供 <xref:Microsoft.SqlServer.Dts.Runtime.DtsEventHandler> 的變數集合中之 <xref:Microsoft.SqlServer.Dts.Runtime.DtsEventHandler> 使用。  
  
```csharp  
public override void InitializeTask(Connections connections,  
   VariableDispenser variables, IDTSInfoEvents events,  
   IDTSLogging log, EventInfos eventInfos,  
   LogEntryInfos logEntryInfos, ObjectReferenceTracker refTracker)  
{  
    this.eventInfos = eventInfos;  
    string[] paramNames = new string[1];  
    TypeCode[] paramTypes = new TypeCode[1]{TypeCode.Int32};  
    string[] paramDescriptions = new string[1];  
  
    paramNames[0] = "InitialValue";  
    paramDescriptions[0] = "The value before it is incremented.";  
  
    this.eventInfos.Add("OnBeforeIncrement",   
      "Fires before the task increments the value.",  
      true,paramNames,paramTypes,paramDescriptions);  
    this.onBeforeIncrement = this.eventInfos["OnBeforeIncrement"];  
  
    paramDescriptions[0] = "The value after it has been incremented.";  
    this.eventInfos.Add("OnAfterIncrement",  
      "Fires after the initial value is updated.",  
      true,paramNames, paramTypes,paramDescriptions);  
    this.onAfterIncrement = this.eventInfos["OnAfterIncrement"];  
}  
```  
  
```vb  
Public Overrides Sub InitializeTask(ByVal connections As Connections, _  
ByVal variables As VariableDispenser, ByVal events As IDTSInfoEvents, _  
ByVal log As IDTSLogging, ByVal eventInfos As EventInfos, _  
ByVal logEntryInfos As LogEntryInfos, ByVal refTracker As ObjectReferenceTracker)   
  
    Dim paramNames(0) As String  
    Dim paramTypes(0) As TypeCode = {TypeCode.Int32}  
    Dim paramDescriptions(0) As String  
  
    Me.eventInfos = eventInfos  
  
    paramNames(0) = "InitialValue"  
    paramDescriptions(0) = "The value before it is incremented."  
  
    Me.eventInfos.Add("OnBeforeIncrement", _  
      "Fires before the task increments the value.", _  
      True, paramNames, paramTypes, paramDescriptions)  
    Me.onBeforeIncrement = Me.eventInfos("OnBeforeIncrement")  
  
    paramDescriptions(0) = "The value after it has been incremented."  
    Me.eventInfos.Add("OnAfterIncrement", _  
      "Fires after the initial value is updated.", True, _  
      paramNames, paramTypes, paramDescriptions)  
    Me.onAfterIncrement = Me.eventInfos("OnAfterIncrement")  
  
End Sub  
```  
  
## <a name="raising-custom-events"></a>引發自訂事件  
 自訂事件是透過呼叫 <xref:Microsoft.SqlServer.Dts.Runtime.IDTSComponentEvents.FireCustomEvent%2A> 方法來引發。 下列程式碼行會引發自訂事件。  
  
```csharp  
componentEvents.FireCustomEvent(this.onBeforeIncrement.Name,  
   this.onBeforeIncrement.Description, ref arguments,  
   null, ref bFireOnBeforeIncrement);  
```  
  
```vb  
componentEvents.FireCustomEvent(Me.onBeforeIncrement.Name, _  
Me.onBeforeIncrement.Description, arguments, _  
Nothing,  bFireOnBeforeIncrement)  
```  
  
## <a name="sample"></a>範例  
 下列範例顯示的工作會在 **InitializeTask** 方法中定義自訂事件，將自訂事件新增至 <xref:Microsoft.SqlServer.Dts.Runtime.EventInfos> 集合，然後透過呼叫 <xref:Microsoft.SqlServer.Dts.Runtime.IDTSComponentEvents.FireCustomEvent%2A> 方法在其 **Execute** 方法期間引發自訂事件。  
  
```csharp  
[DtsTask(DisplayName = "CustomEventTask")]  
    public class CustomEventTask : Task  
    {  
        public override DTSExecResult Execute(Connections connections,   
          VariableDispenser variableDispenser, IDTSComponentEvents componentEvents,  
           IDTSLogging log, object transaction)  
        {  
            bool fireAgain;  
            object[] args = new object[1] { "The value of the parameter." };  
            componentEvents.FireCustomEvent( "MyCustomEvent",   
              "Firing the custom event.", ref args,  
              "CustomEventTask" , ref fireAgain );  
            return DTSExecResult.Success;  
        }  
  
        public override void InitializeTask(Connections connections,  
          VariableDispenser variableDispenser, IDTSInfoEvents events,  
          IDTSLogging log, EventInfos eventInfos,  
          LogEntryInfos logEntryInfos, ObjectReferenceTracker refTracker)  
        {  
            string[] names = new string[1] {"Parameter1"};  
            TypeCode[] types = new TypeCode[1] {TypeCode.String};  
            string[] descriptions = new string[1] {"Parameter description." };  
  
            eventInfos.Add("MyCustomEvent",  
             "Fires when my interesting event happens.",  
             true, names, types, descriptions);  
  
        }  
   }  
```  
  
```vb  
<DtsTask(DisplayName = "CustomEventTask")> _   
    Public Class CustomEventTask  
     Inherits Task  
        Public Overrides Function Execute(ByVal connections As Connections, _  
          ByVal variableDispenser As VariableDispenser, _  
          ByVal componentEvents As IDTSComponentEvents, _  
          ByVal log As IDTSLogging, ByVal transaction As Object) _  
          As DTSExecResult  
  
            Dim fireAgain As Boolean  
            Dim args() As Object =  New Object(1) {"The value of the parameter."}  
  
            componentEvents.FireCustomEvent("MyCustomEvent", _  
              "Firing the custom event.", args, _  
              "CustomEventTask" ,  fireAgain)  
            Return DTSExecResult.Success  
        End Function  
  
        Public Overrides  Sub InitializeTask(ByVal connections As Connections, _  
          ByVal variableDispenser As VariableDispenser,  
          ByVal events As IDTSInfoEvents,  
          ByVal log As IDTSLogging, ByVal eventInfos As EventInfos, ByVal logEnTryInfos As LogEnTryInfos, ByVal refTracker As ObjectReferenceTracker)  
  
            Dim names() As String =  New String(1) {"Parameter1"}  
            Dim types() As TypeCode =  New TypeCode(1) {TypeCode.String}  
            Dim descriptions() As String =  New String(1) {"Parameter description."}  
  
            eventInfos.Add("MyCustomEvent", _  
              "Fires when my interesting event happens.", _  
              True, names, types, descriptions)  
  
        End Sub  
  
    End Class  
```  
  
## <a name="see-also"></a>另請參閱  
 [Integration Services &#40;SSIS&#41; 事件處理常式](../../../integration-services/integration-services-ssis-event-handlers.md)   
 [將事件處理常式新增至套件](../../integration-services-ssis-event-handlers.md)  
  
