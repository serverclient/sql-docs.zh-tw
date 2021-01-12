---
description: GET_FILESTREAM_TRANSACTION_CONTEXT (Transact-SQL)
title: GET_FILESTREAM_TRANSACTION_CONTEXT (Transact-SQL)
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: sql-database
ms.reviewer: ''
ms.technology: t-sql
ms.topic: language-reference
f1_keywords:
- GET_FILESTREAM_TRANSACTION_CONTEXT_TSQL
- GET_FILESTREAM_TRANSACTION_CONTEXT
dev_langs:
- TSQL
helpviewer_keywords:
- GET_FILESTREAM_TRANSACTION_CONTEXT FILESTREAM [SQL Server]
ms.assetid: 459e6b79-4420-41e6-85bf-89d90f43b4f1
author: cawrites
ms.author: chadam
ms.openlocfilehash: 2a0977a8447d9a84ed364217b285d71b894a092f
ms.sourcegitcommit: a9e982e30e458866fcd64374e3458516182d604c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98087128"
---
# <a name="get_filestream_transaction_context-transact-sql"></a>GET_FILESTREAM_TRANSACTION_CONTEXT (Transact-SQL)
[!INCLUDE [SQL Server](../../includes/applies-to-version/sqlserver.md)]

  傳回代表工作階段之目前交易內容的 Token。 應用程式會使用此 Token 將 FILESTREAM 檔案系統資料流作業繫結至此交易。 如需 FILESTREAM 主題的清單，請參閱[二進位大型物件 &#40;Blob&#41; 資料 &#40;SQL Server&#41;](../../relational-databases/blob/binary-large-object-blob-data-sql-server.md)。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```syntaxsql
  
GET_FILESTREAM_TRANSACTION_CONTEXT ()  
```

[!INCLUDE[sql-server-tsql-previous-offline-documentation](../../includes/sql-server-tsql-previous-offline-documentation.md)]

## <a name="return-type"></a>傳回類型  
 **varbinary(max)**  
  
## <a name="return-value"></a>傳回值  
 如果此交易尚未啟動或是已被取消或認可，則會傳回 NULL。  
  
## <a name="remarks"></a>備註  
 交易必須是明確的。 請使用 BEGIN TRANSACTION，後面緊接著 COMMIT TRANSACTION 或 ROLLBACK TRANSACTION。  
  
 當您呼叫 GET_FILESTREAM_TRANSACTION_CONTEXT 時，呼叫端在交易期間會被授與此交易的檔案系統存取權。 若要允許另一個使用者透過檔案系統存取此交易，請使用 EXECUTE AS，以另一個使用者身分執行 GET_FILESTREAM_TRANSACTION_CONTEXT。  
  
## <a name="examples"></a>範例  
 下列範例會在 [!INCLUDE[tsql](../../includes/tsql-md.md)] 交易中使用 `GET_FILESTREAM_TRANSACTION_CONTEXT` 取得交易內容。  
  
```csharp  
using System;  
using System.Data.SqlClient;  
using System.Data;  
  
namespace ConsoleApplication  
{  
    /// <summary>  
    /// This class is a wrapper that contains methods for:  
    ///   
    ///     GetTransactionContect() - Returns the current transaction context.  
    ///     BeginTransaction() - Begins a transaction.  
    ///     CommmitTransaction() - Commits the current transaction.  
    ///   
    /// </summary>  
  
    class SqlAccessWrapper  
    {  
        /// <summary>  
        /// Returns a byte array that contains the current transaction  
        /// context.  
        /// </summary>  
        /// <param name="sqlConnection">  
        /// SqlConnection object that represents the instance of SQL Server  
        /// from which to obtain the transaction context.   
        /// </param>  
        /// <returns>  
        /// If there is a current transaction context, the return  
        /// value is an Object that represents the context.  
        /// If there is not a current transaction context, the  
        /// value returned is DBNull.Value.  
        /// </returns>  
  
        public Object GetTransactionContext(SqlConnection sqlConnection)  
        {  
            SqlCommand cmd = new SqlCommand();  
            cmd.CommandText = "SELECT GET_FILESTREAM_TRANSACTION_CONTEXT()";  
            cmd.CommandType = CommandType.Text;  
            cmd.Connection = sqlConnection;  
  
            return cmd.ExecuteScalar();  
  
        }  
  
        /// <summary>  
        /// Begins the transaction.  
        /// </summary>  
        /// <param name="sqlConnection">  
        /// SqlConnection object that represents the server  
        /// on which to run the BEGIN TRANSACTION statement.  
        /// </param>  
  
        public void BeginTransaction(SqlConnection sqlConnection)  
        {  
            SqlCommand cmd = new SqlCommand();  
  
            cmd.CommandText = "BEGIN TRANSACTION";  
            cmd.CommandType = CommandType.Text;  
            cmd.Connection = sqlConnection;  
  
            cmd.ExecuteNonQuery();  
        }  
  
        /// <summary>  
        /// Commits the transaction.  
        /// </summary>  
        /// <param name="sqlConnection">  
        /// SqlConnection object that represents the instance of SQL Server  
        /// on which to run the COMMIT statement.  
        /// </param>  
  
        public void CommitTransaction(SqlConnection sqlConnection)  
        {  
            SqlCommand cmd = new SqlCommand();  
  
            cmd.CommandText = "COMMIT TRANSACTION";  
            cmd.CommandType = CommandType.Text;  
            cmd.Connection = sqlConnection;  
  
            cmd.ExecuteNonQuery();  
        }  
    }  
  
    class Program  
    {  
        static void Main(string[] args)  
        {  
            //Open a connection to the local instance of SQL Server.  
  
            SqlConnection sqlConnection = new SqlConnection("Integrated Security=true;server=(local)");  
            sqlConnection.Open();  
  
            SqlAccessWrapper sql = new SqlAccessWrapper();  
  
            //Create a transaction so that sql.GetTransactionContext() will succeed.  
            sql.BeginTransaction(sqlConnection);  
  
            //The transaction context will be stored in this array.  
            Byte[] transactionToken;     
  
            Object txObj = sql.GetTransactionContext(sqlConnection);  
            if (DBNull.Value != txObj)  
            {  
                transactionToken = (byte[])txObj;  
                Console.WriteLine("Transaction context obtained.\n");  
            }  
  
            sql.CommitTransaction(sqlConnection);  
        }  
    }  
}  
```  
  
```vb  
Imports System  
Imports System.Data.SqlClient  
Imports System.Data  
  
Namespace ConsoleApplication  
    ''' <summary>   
    ''' This class is a wrapper that contains methods for:   
    '''   
    ''' GetTransactionContect() - Returns the current transaction context.   
    ''' BeginTransaction() - Begins a transaction.   
    ''' CommmitTransaction() - Commits the current transaction.   
    '''   
    ''' </summary>   
  
    Class SqlAccessWrapper  
        ''' <summary>   
        ''' Returns a byte array that contains the current transaction   
        ''' context.   
        ''' </summary>   
        ''' <param name="sqlConnection">   
        ''' SqlConnection object that represents the instance of SQL Server   
        ''' from which to obtain the transaction context.   
        ''' </param>   
        ''' <returns>   
        ''' If there is a current transaction context, the return   
        ''' value is an Object that represents the context.   
        ''' If there is not a current transaction context, the   
        ''' value returned is DBNull.Value.   
        ''' </returns>   
  
        Public Function GetTransactionContext(ByVal sqlConnection As SqlConnection) As Object  
            Dim cmd As New SqlCommand()  
            cmd.CommandText = "SELECT GET_FILESTREAM_TRANSACTION_CONTEXT()"  
            cmd.CommandType = CommandType.Text  
            cmd.Connection = sqlConnection  
  
            Return cmd.ExecuteScalar()  
  
        End Function  
  
        ''' <summary>   
        ''' Begins the transaction.   
        ''' </summary>   
        ''' <param name="sqlConnection">   
        ''' SqlConnection object that represents the server   
        ''' on which to run the BEGIN TRANSACTION statement.   
        ''' </param>   
  
        Public Sub BeginTransaction(ByVal sqlConnection As SqlConnection)  
            Dim cmd As New SqlCommand()  
  
            cmd.CommandText = "BEGIN TRANSACTION"  
            cmd.CommandType = CommandType.Text  
            cmd.Connection = sqlConnection  
  
            cmd.ExecuteNonQuery()  
        End Sub  
  
        ''' <summary>   
        ''' Commits the transaction.   
        ''' </summary>   
        ''' <param name="sqlConnection">   
        ''' SqlConnection object that represents the instance of SQL Server   
        ''' on which to run the COMMIT statement.   
        ''' </param>   
  
        Public Sub CommitTransaction(ByVal sqlConnection As SqlConnection)  
            Dim cmd As New SqlCommand()  
  
            cmd.CommandText = "COMMIT TRANSACTION"  
            cmd.CommandType = CommandType.Text  
            cmd.Connection = sqlConnection  
  
            cmd.ExecuteNonQuery()  
        End Sub  
    End Class  
  
    Class Program  
        Shared Sub Main()  
            '''Open a connection to the local instance of SQL Server.  
  
            Dim sqlConnection As New SqlConnection("Integrated Security=true;server=(local)")  
            sqlConnection.Open()  
  
            Dim sql As New SqlAccessWrapper()  
  
            '''Create a transaction so that sql.GetTransactionContext() will succeed.   
            sql.BeginTransaction(sqlConnection)  
  
            '''The transaction context will be stored in this array.   
            Dim transactionToken As Byte()  
  
            Dim txObj As Object = sql.GetTransactionContext(sqlConnection)  
  
            '''If the returned object is not NULL, there is a valid transaction  
            '''token, and it must be converted into a format that is usable within  
            '''the application.  
  
            If Not txObj.Equals(DBNull.Value) Then  
                transactionToken = DirectCast(txObj, Byte())  
                Console.WriteLine("Transaction context obtained." & Chr(10) & "")  
            End If  
  
            sql.CommitTransaction(sqlConnection)  
        End Sub  
    End Class  
End Namespace  
```  
  
## <a name="see-also"></a>另請參閱  
 [PathName &#40;Transact-SQL&#41;](../../relational-databases/system-functions/pathname-transact-sql.md)   
 [二進位大型物件 &#40;Blob&#41; 資料 &#40;SQL Server&#41;](../../relational-databases/blob/binary-large-object-blob-data-sql-server.md)  
  
  
