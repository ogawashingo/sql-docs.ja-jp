---
title: フォーマットファイルを使用した一括コピー (ODBC) |Microsoft Docs
ms.custom: ''
ms.date: 10/18/2019
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- bulk copy using format file [ODBC]
- ODBC, bulk copy operations
ms.assetid: 970fd3af-f918-4fc3-a5b1-92596515d4de
author: MightyPen
ms.author: genemi
manager: craigg
ms.openlocfilehash: a2dc0ac57b132a0e681337b358a951a0e33f56db
ms.sourcegitcommit: 82a1ad732fb31d5fa4368c6270185c3f99827c97
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "72688906"
---
# <a name="bulk-copy-by-using-a-format-file-odbc"></a>フォーマット ファイルを使用した一括コピー (ODBC)
  このサンプルでは、ODBC bcp_init 関数をフォーマット ファイルと共に使用する方法を示します。  
  
### <a name="to-bulk-copy-by-using-a-format-file"></a>フォーマット ファイルを使用して一括コピーするには  
  
1.  環境ハンドルおよび接続ハンドルを割り当てます。  
  
2.  一括コピー操作が有効になるように SQL_COPT_SS_BCP および SQL_BCP_ON を設定します。  
  
3.  Microsoft SQL Server に接続します。  
  
4.  [Bcp_init](../../native-client-odbc-extensions-bulk-copy-functions/bcp-init.md)を呼び出して、次の情報を設定します。  
  
    -   一括コピー操作の対象になるテーブルまたはビューの名前  
  
    -   データベースにコピーするデータが含まれたデータ ファイルの名前、またはデータベースからのコピー時にデータを受け取るデータ ファイルの名前  
  
    -   一括コピー エラー メッセージを受信するデータ ファイルの名前 (メッセージ ファイルが不要な場合は NULL を指定)。  
  
    -   コピーの方向 (ファイルからテーブルまたはビューへのコピーの場合は DB_IN)  
  
5.  [Bcp_readfmt](../../native-client-odbc-extensions-bulk-copy-functions/bcp-readfmt.md)を呼び出して、一括コピー操作で使用されるデータファイルを記述するフォーマットファイルを読み取ります。  
  
6.  [Bcp_exec](../../native-client-odbc-extensions-bulk-copy-functions/bcp-exec.md)を呼び出して、一括コピー操作を実行します。  
  
## <a name="example"></a>例  
 このサンプルは IA64 ではサポートされていません。  
  
 AdventureWorks と呼ばれる ODBC データ ソース (既定のデータベースは AdventureWorks サンプル データベース) が必要です (AdventureWorks サンプルデータベースは、 [Microsoft SQL Server のサンプルとコミュニティのプロジェクト](https://go.microsoft.com/fwlink/?LinkID=85384)のホームページからダウンロードできます)。このデータソースは、オペレーティングシステムによって提供される ODBC ドライバーに基づいている必要があります (ドライバー名は "SQL Server")。 このサンプルを 64 ビット オペレーティング システムで 32 ビット アプリケーションとしてビルドし、実行する場合、%windir%\SysWOW64\odbcad32.exe の ODBC アドミニストレーターを使用して ODBC データ ソースを作成する必要があります。  
  
 このサンプルでは、コンピューターの既定の [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] インスタンスに接続します。 名前付きインスタンスに接続するには、ODBC データ ソースの定義を変更し、server\namedinstance 形式でそのインスタンスを指定します。 [!INCLUDE[ssExpress](../../../includes/ssexpress-md.md)] は、既定で名前付きインスタンスとしてインストールされます。  
  
 最初の ([!INCLUDE[tsql](../../../includes/tsql-md.md)]) コードリストを実行して、サンプルで使用するテーブルを作成します。  
  
 2 つ目のコード リストをコピーし、Bcpfmt.fmt という名前のファイルに貼り付けます。 テーブルの各列はタブ文字で区切られています。  
  
 3 つ目のコード リストをコピーし、Bcpodbc.bcp という名前のファイルに貼り付けます。 ファイル内の各キャリッジ リターンの前にはタブ文字があります。  
  
 odbc32.lib と odbcbcp.lib を使用して 4 つ目の (C++) コード リストをコンパイルします。 MSBuild.exe でビルドした場合は、まずプロジェクト ディレクトリの Bcpfmt.fmt と Bcpodbc.bcp を .exe があるディレクトリにコピーし、次に .exe を起動します。  
  
 5 つ目の ([!INCLUDE[tsql](../../../includes/tsql-md.md)]) コード リストを実行して、サンプルで使用したテーブルを削除します。  
  
```sql
use AdventureWorks  
CREATE TABLE BCPDate (cola int, colb datetime)  
```  
  
```  
8.0  
2  
1SQLCHAR04"\t"1colaSQL_Latin1_General_Cp437_Bin  
2SQLCHAR08"\r\n"2colbSQL_Latin1_General_Cp437_Bin  
```  
  
```  
1  
2  
```  
  
```cpp
// compile with: odbc32.lib odbcbcp.lib  
#include <stdio.h>  
#include <windows.h>  
#include <sql.h>  
#include <sqlext.h>  
#include <odbcss.h>  
  
SQLHENV henv = SQL_NULL_HENV;  
HDBC hdbc1 = SQL_NULL_HDBC;   
  
void Cleanup() {  
   if (hdbc1 != SQL_NULL_HDBC) {  
      SQLDisconnect(hdbc1);  
      SQLFreeHandle(SQL_HANDLE_DBC, hdbc1);  
   }  
  
   if (henv != SQL_NULL_HENV)  
      SQLFreeHandle(SQL_HANDLE_ENV, henv);  
}  
  
int main() {  
   RETCODE retcode;  
   SDWORD cRows;  
  
   // Allocate the ODBC environment and save handle.  
   retcode = SQLAllocHandle (SQL_HANDLE_ENV, NULL, &henv);  
   if ( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLAllocHandle(Env) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Notify ODBC that this is an ODBC 3.0 app.  
   retcode = SQLSetEnvAttr(henv, SQL_ATTR_ODBC_VERSION, (SQLPOINTER) SQL_OV_ODBC3, SQL_IS_INTEGER);  
   if ( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLSetEnvAttr(ODBC version) Failed\n\n");  
      Cleanup();  
      return(9);      
   }  
  
   // Allocate ODBC connection handle, set BCP mode, and connect.  
   retcode = SQLAllocHandle(SQL_HANDLE_DBC, henv, &hdbc1);  
   if ( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLAllocHandle(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   retcode = SQLSetConnectAttr(hdbc1, SQL_COPT_SS_BCP, (void *)SQL_BCP_ON, SQL_IS_INTEGER);  
   if ( (retcode != SQL_SUCCESS_WITH_INFO) && (retcode != SQL_SUCCESS)) {  
      printf("SQLSetConnectAttr(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Sample uses Integrated Security. Create SQL Server DSN using Windows NT authentication.  
   retcode = SQLConnect(hdbc1, (UCHAR*)"AdventureWorks", SQL_NTS, (UCHAR*)"", SQL_NTS, (UCHAR*)"", SQL_NTS);  
   if ( (retcode != SQL_SUCCESS) && (retcode != SQL_SUCCESS_WITH_INFO) ) {  
      printf("SQLConnect() Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Initialize the bulk copy.  
   retcode = bcp_init(hdbc1, "BCPDate", "BCPODBC.bcp", NULL, DB_IN);  
   if ( (retcode != SUCCEED) ) {  
      printf("bcp_init(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Read the format file.  
   retcode = bcp_readfmt(hdbc1, "BCPFMT.fmt");  
   if ( (retcode != SUCCEED) ) {  
      printf("bcp_readfmt(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   // Execute the bulk copy.  
   retcode = bcp_exec(hdbc1, &cRows);  
   if ( (retcode != SUCCEED) ) {  
      printf("bcp_exec(hdbc1) Failed\n\n");  
      Cleanup();  
      return(9);  
   }  
  
   printf("Number of rows bulk copied in = %d.\n", cRows);  
  
   // Cleanup  
   SQLDisconnect(hdbc1);  
   SQLFreeHandle(SQL_HANDLE_DBC, hdbc1);  
   SQLFreeHandle(SQL_HANDLE_ENV, henv);  
}  
```  
  
```sql
use AdventureWorks  
IF EXISTS (SELECT name FROM sysobjects WHERE name = 'BCPDate')  
     DROP TABLE BCPDate  
GO  
```  
  
## <a name="see-also"></a>「  
 [SQL Server odbc ドライバーを使用した一括コピーの操作&#40;方法&#41;に関するトピック odbc](bulk-copying-with-the-sql-server-odbc-driver-how-to-topics-odbc.md)    
 [データ ファイルとフォーマット ファイルの使用](../../native-client-odbc-bulk-copy-operations/using-data-files-and-format-files.md)  
  
  
