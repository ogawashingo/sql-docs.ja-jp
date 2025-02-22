---
title: bcp_getcolfmt |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
apiname:
- bcp_getcolfmt
apilocation:
- sqlncli11.dll
apitype: DLLExport
helpviewer_keywords:
- bcp_getcolfmt function
ms.assetid: f8bdada5-7b2d-4475-8c98-f93e9d77b130
author: markingmyname
ms.author: maghan
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 40e7f63438bbe6ff68284aa573e1307b17087234
ms.sourcegitcommit: 8732161f26a93de3aa1fb13495e8a6a71519c155
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71707543"
---
# <a name="bcp_getcolfmt"></a>bcp_getcolfmt
[!INCLUDE[appliesto-ss-asdb-asdw-pdw-md](../../includes/appliesto-ss-asdb-asdw-pdw-md.md)]
[!INCLUDE[SNAC_Deprecated](../../includes/snac-deprecated.md)]

  列形式のプロパティ値を確認するために使用します。  
  
## <a name="syntax"></a>構文  
  
```  
  
RETCODE bcp_getcolfmt (  
        HDBC hdbc,  
        INT field,  
        INT property,  
        void* pValue,  
        INT cbvalue,  
        INT* pcbLen);  
```  
  
## <a name="arguments"></a>引数  
 *hdbc*  
 一括コピーが有効な ODBC 接続ハンドルです。  
  
 *field*  
 プロパティを取得する列番号です。  
  
 *property*  
 プロパティ定数のいずれかを指定します。  
  
 *pValue*  
 プロパティ値を取得するバッファーへのポインターです。  
  
 *cbValue*  
 プロパティ バッファーのバイト単位の長さです。  
  
 *pcbLen*  
 プロパティ バッファーに返されるデータの長さへのポインターです。  
  
## <a name="returns"></a>戻り値  
 SUCCEED または FAIL。  
  
## <a name="remarks"></a>コメント  
 列形式のプロパティ値については、 [bcp_setcolfmt](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/bcp-setcolfmt.md)のトピックを参照してください。 列形式のプロパティ値は、 **bcp_setcolfmt**関数を呼び出すことによって設定され、 **bcp_getcolfmt**関数は列形式のプロパティ値を検索するために使用されます。  
  
 以前の @no__t バージョンと比較して、@no__t 0 (またはそれ以降) のサーバーコンピューターに接続するときに、動作の変更が検出される場合があります。 詳細については、「[メタデータの検出](../../relational-databases/native-client/features/metadata-discovery.md)」を参照してください。  
  
## <a name="bcp_getcolfmt-support-for-enhanced-date-and-time-features"></a>bcp_getcolfmt による機能強化された日付と時刻のサポート  
 日付型または時刻型の**BCP_FMT_TYPE**プロパティで使用される型は、 [OLE DB および&#40;ODBC&#41;の拡張された日付と時刻の型に対する一括コピーの変更](../../relational-databases/native-client-odbc-date-time/bulk-copy-changes-for-enhanced-date-and-time-types-ole-db-and-odbc.md)で指定されています。  
  
 詳細については、「[日付と&#40;時刻&#41;の機能強化 ODBC](../../relational-databases/native-client-odbc-date-time/date-and-time-improvements-odbc.md)」を参照してください。  
  
## <a name="see-also"></a>参照  
 [一括コピー関数](../../relational-databases/native-client-odbc-extensions-bulk-copy-functions/sql-server-driver-extensions-bulk-copy-functions.md)  
  
  
