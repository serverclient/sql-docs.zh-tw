---
description: '附加和 CreateParameter 方法範例 (JScript) '
title: 附加和 CreateParameter 方法範例 (JScript) |Microsoft Docs
ms.prod: sql
ms.prod_service: connectivity
ms.technology: ado
ms.custom: ''
ms.date: 01/19/2017
ms.reviewer: ''
ms.topic: conceptual
dev_langs:
- JScript
helpviewer_keywords:
- CreateParameter method [ADO], JScript example
- Append method [ADO], JScript example
ms.assetid: 37000833-68f4-45f1-b2dd-7f75893d09d9
author: rothja
ms.author: jroth
ms.openlocfilehash: 3b4de61bfa05e1dfa5fef778773105b93aeb0c5f
ms.sourcegitcommit: 18a98ea6a30d448aa6195e10ea2413be7e837e94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88976129"
---
# <a name="append-and-createparameter-methods-example-jscript"></a>附加和 CreateParameter 方法範例 (JScript) 
這個範例會使用 [Append](./append-method-ado.md) 和 [CreateParameter](./createparameter-method-ado.md) 方法，以輸入參數來執行預存程式。 將下列程式碼剪下並貼到 [記事本] 或其他文字編輯器，然後將它儲存為**AppendJS。**  
  
```  
<!-- BeginAppendJS -->  
<%@LANGUAGE="JScript" %>  
<%// use this meta tag instead of adojavas.inc%>  
<!--METADATA TYPE="typelib" uuid="00000205-0000-0010-8000-00AA006D2EA4" -->  
  
<html>  
<head>  
    <title>Append and CreateParameter Methods Example (JScript)</title>  
<style>  
<!--  
body {  
   font-family: 'Verdana','Arial','Helvetica',sans-serif;  
   BACKGROUND-COLOR:white;  
   COLOR:black;  
    }  
-->  
</style>  
</head>  
  
<body>  
<h1>Append and CreateParameter Methods Example (JScript)</h1>  
<%  
    // verify user-input   
    var iRoyalty = parseInt(Request.Form("RoyaltyValue"));  
    if (iRoyalty > -1)  
    {  
  
        // connection, recordset and command variables  
        var strCnxn = "Provider='sqloledb';Data Source=" + Request.ServerVariables("SERVER_NAME") + ";" +  
            "Initial Catalog='pubs';Integrated Security='SSPI';";  
        var Cnxn = Server.CreateObject("ADODB.Connection");  
        var cmdByRoyalty = Server.CreateObject("ADODB.Command");  
        var rsByRoyalty = Server.CreateObject("ADODB.Recordset");  
        var rsAuthor = Server.CreateObject("ADODB.Recordset");  
        // display variables  
        var strMessage;  
  
        try  
        {  
            // open connection and set cursor location  
            Cnxn.Open(strCnxn);  
            Cnxn.CursorLocation = adUseClient;  
  
            // command object initial parameters  
            cmdByRoyalty.CommandText = "byroyalty";  
            cmdByRoyalty.CommandType = adCmdStoredProc;  
  
            // create the new parameter and append to  
            // the Command object's parameters collection  
            var prmByRoyalty = cmdByRoyalty.CreateParameter("percentage", adInteger, adParamInput);  
            cmdByRoyalty.Parameters.Append(prmByRoyalty);  
            prmByRoyalty.Value = iRoyalty;  
  
            cmdByRoyalty.ActiveConnection = Cnxn;  
  
            // execute command  
            rsByRoyalty = cmdByRoyalty.Execute();  
  
            // display results  
            rsAuthor.Open("Authors", Cnxn);  
  
            while (!rsByRoyalty.EOF)  
            {  
                rsAuthor.Filter = "au_id='" + rsByRoyalty.Fields("au_id") + "'";  
  
                // start new line  
                strMessage = "<P>";  
  
                // recordset data  
                strMessage += rsAuthor.Fields("au_fname") + " ";   
                strMessage += rsAuthor.Fields("au_lname") + " ";  
  
                // end the line  
                strMessage += "</P>";  
  
                // show result  
                Response.Write(strMessage);  
  
                // et next record  
                rsByRoyalty.MoveNext;  
            }  
        }  
        catch (e)  
        {  
            Response.Write(e.message);  
        }  
        finally  
        {  
            // clean up  
            if (rsByRoyalty.State == adStateOpen)  
                rsByRoyalty.Close;  
            if (rsAuthor.State == adStateOpen)  
                rsAuthor.Close;  
            if (Cnxn.State == adStateOpen)  
                Cnxn.Close;  
            rsByRoyalty = null;  
            rsAuthor = null;  
            Cnxn = null;  
        }  
    }  
%>  
  
<hr>  
  
<form method="POST" action="AppendJS.asp" id=form1 name=form1>  
  <p align="left">Enter royalty percentage to find (e.g., 40): <input type="text" name="RoyaltyValue" size="5"></p>  
  <p align="left"><input type="submit" value="Submit" name="B1"><input type="reset" value="Reset" name="B2"></p>  
</form>  
  
</body>  
  
</html>  
<!-- EndAppendJS -->  
```  
  
## <a name="see-also"></a>另請參閱  
 [ (ADO) 的 Append 方法 ](./append-method-ado.md)   
 [ (ADO) 的 CreateParameter 方法 ](./createparameter-method-ado.md)   
 [Field 物件](./field-object.md)   
 [ (ADO) 的欄位集合 ](./fields-collection-ado.md)   
 [Parameter 物件](./parameter-object.md)