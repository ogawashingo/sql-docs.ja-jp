---
title: 一括コピー関数 |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- SQL Server Native Client ODBC driver, bulk copy
- bulk copy [ODBC], functions
- ODBC, bulk copy operations
- functions [ODBC]
ms.assetid: 6526b892-1d58-4f55-8335-f09887f6ea02
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: ce1ee4536769ea1531a11ca3d57f049310ade042
ms.sourcegitcommit: 8732161f26a93de3aa1fb13495e8a6a71519c155
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71707591"
---
# <a name="sql-server-driver-extensions---bulk-copy-functions"></a>SQL Server ドライバーの拡張機能 - 一括コピー関数
[!INCLUDE[appliesto-ss-asdb-asdw-pdw-md](../../includes/appliesto-ss-asdb-asdw-pdw-md.md)]
[!INCLUDE[SNAC_Deprecated](../../includes/snac-deprecated.md)]

  ODBC (Open Database Connectivity) は、ODBC データ ソース内のデータにアクセスするアプリケーションで使用される、Microsoft Win32 アプリケーション プログラミング インターフェイスです。 この [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバー リファレンスは、すべての ODBC 関数呼び出しを記載したものではありません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーと共に使用される、ドライバー固有のパラメーターや動作を備えた ODBC 関数だけについて説明しています。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーは ODBC 3.51 仕様に準拠しています。 ODBC 3.51 の総合的なリファレンスについては、[データアクセスおよびストレージデベロッパーセンター](https://go.microsoft.com/fwlink?linkid=4173)から Microsoft Data ACCESS Components SDK をダウンロードするか、 [odbc プログラマーズリファレンス](https://go.microsoft.com/fwlink/?LinkId=45250)をオンラインで参照してください。  
 
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client ODBC ドライバーが持つ [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 専用の一括コピー API 拡張機能を使用すると、クライアント アプリケーションでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] テーブルへのデータ行の追加や、テーブルからのデータ行の抽出をすばやく行うことができます。  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client を使用している場合は、SQLNCLI11.LIB と SQLNCLI.H 内にある一括コピー関数 (BCP) を参照できます。  
  
 アプリケーションで BCP API 関数呼び出しを使用する場合は、アプリケーションで使用するドライバー (.dll) に付属するライブラリ (.lib) とアプリケーションをリンクする必要があります。 BCP アプリケーションを複数のドライバー ライブラリとリンクしないでください。  
  
## <a name="in-this-section"></a>このセクションの内容  
  
-   [bcp_batch](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-batch.md)  
  
-   [bcp_bind](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-bind.md)  
  
-   [bcp_colfmt](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-colfmt.md)  
  
-   [bcp_collen](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-collen.md)  
  
-   [bcp_colptr](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-colptr.md)  
  
-   [bcp_columns](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-columns.md)  
  
-   [bcp_control](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-control.md)  
  
-   [bcp_done](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-done.md)  
  
-   [bcp_exec](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-exec.md)  
  
-   [bcp_getcolfmt](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-getcolfmt.md)  
  
-   [bcp_gettypename](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-gettypename.md)  
  
-   [bcp_init](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-init.md)  
  
-   [bcp_moretext](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-moretext.md)  
  
-   [bcp_readfmt](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-readfmt.md)  
  
-   [bcp_sendrow](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-sendrow.md)  
  
-   [bcp_setbulkmode](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-setbulkmode.md)  
  
-   [bcp_setcolfmt](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-setcolfmt.md)  
  
-   [bcp_writefmt](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-writefmt.md)  
  
## <a name="see-also"></a>関連項目  
 [SQL Server ドライバーの拡張子](https://msdn.microsoft.com/library/1043bc93-965d-4939-bd1c-21e9d8d3e9ac)   
 [一括コピー操作&#40;の実行 (ODBC)&#41;](../../relational-databases/native-client-odbc-bulk-copy-operations/performing-bulk-copy-operations-odbc.md)  
  
  
