---
title: DBCC DROPRESULTSETCACHE (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 07/03/2019
ms.prod: sql
ms.technology: data-warehouse
ms.reviewer: ''
ms.topic: language-reference
dev_langs:
- TSQL
ms.assetid: 73f598cf-b02a-4dba-8d89-9fc0b55a12b8
author: XiaoyuMSFT
ms.author: xiaoyul
monikerRange: = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 86320f3c3f8288d92234356d43b1a7e8559a4929
ms.sourcegitcommit: 9c993112842dfffe7176decd79a885dbb192a927
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72452853"
---
# <a name="dbcc-dropresultsetcache--transact-sql"></a>DBCC DROPRESULTSETCACHE  (Transact-SQL)

[!INCLUDE[tsql-appliesto-xxxxxx-xxxx-asdw-xxx-md](../../includes/tsql-appliesto-xxxxxx-xxxx-asdw-xxx-md.md)]

Azure [!INCLUDE[ssSDW](../../includes/sssdw-md.md)] データベースからすべての結果セットのキャッシュエントリを削除します。
  
![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則 &#40;Transact-SQL&#41;](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)
  
## <a name="syntax"></a>構文  
  
```sql
DBCC DROPRESULTSETCACHE
[;]  
```  

## <a name="permissions"></a>アクセス許可

DB_OWNER 固定サーバー ロールのメンバーシップが必要です。

## <a name="remarks"></a>Remarks

- このコマンドでは、すべてのクエリの結果セット キャッシュが空になります。  

- データベースの結果セット キャッシュ機能をオフにしても、キャッシュされたすべての結果が削除されます。  

- 結果セット キャッシュが有効になっているデータベースを一時停止すると、キャッシュされた結果は削除されません。  

## <a name="see-also"></a>参照

[結果セットのキャッシュを使用したパフォーマンス チューニング](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/performance-tuning-result-set-caching)</br>
[ALTER DATABASE SET のオプション &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)</br>
[ALTER DATABASE &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest)</br>
[SET RESULT SET CACHING &#40;Transact-SQL&#41;](/sql/t-sql/statements/set-result-set-caching-transact-sql)</br>
[DBCC SHOWRESULTCACHESPACEUSED &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql)
