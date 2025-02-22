---
title: index_resumable_operations (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 01/14/2019
ms.prod: sql
ms.prod_service: database-engine, sql-database
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sys.index_resumable_operations_TSQL
- sys.indexes_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sys.indexes
- sys.index_resumable_operations
ms.assetid: ''
author: CarlRabeler
ms.author: carlrab
monikerRange: =azuresqldb-current||>=sql-server-2017||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: d4f79da2af2630fa54a06dc26b32cf22287f7c1d
ms.sourcegitcommit: 853c2c2768caaa368dce72b4a5e6c465cc6346cf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71227194"
---
# <a name="sysindex_resumable_operations-transact-sql"></a>index_resumable_operations (Transact-sql)

[!INCLUDE[tsql-appliesto-ss2017-asdb-xxxx-xxx-md](../../includes/tsql-appliesto-ss2017-asdb-xxxx-xxx-md.md)]
**index_resumable_operations**は、再開可能なインデックス再構築の現在の実行状態を監視および確認するシステムビューです。  
**適用対象**:SQL Server 2017 と Azure SQL Database
  
|列名|データ型|説明|  
|-----------------|---------------|-----------------|  
|**object_id**|**int**|このインデックスが所属するオブジェクトの ID (null 値は許容されません)。|  
|**index_id**|**int**|インデックスの ID (null 値は許容されません)。 **index_id**は、オブジェクト内でのみ一意です。|
|**name**|**sysname**|インデックスの名前です。 **name**は、オブジェクト内でのみ一意です。|  
|**sql_text**|**nvarchar(max)**|DDL T-sql ステートメントのテキスト|
|**last_max_dop**|**smallint**|最後に使用された MAX_DOP (既定値 = 0)|
|**partition_number**|**int**|所有しているインデックスまたはヒープ内のパーティション番号。 パーティション分割されていないテーブルとインデックスの場合、またはすべてのパーティションが再構築される場合は、この列の値が NULL になります。|
|**state**|**tinyint**|再開可能なインデックスの動作状態:<br /><br />0 = 実行中<br /><br />1 = 一時停止|
|**state_desc**|**nvarchar(60)**|再開可能なインデックスの動作状態の説明 (実行中または一時停止)|  
|**start_time**|**datetime**|インデックス操作の開始時刻 (null 非許容)|
|**last_pause_time**|**datatime**| インデックス操作の最後の一時停止時刻 (null 許容)。 操作が実行中で一時停止されていない場合は NULL です。|
|**total_execution_time**|**int**|開始時刻からの合計実行時間 (分) (null 非許容)|
|**percent_complete**|**real**|インデックス操作の進行状況は% で完了しました (null 非許容)。|
|**page_count**|**bigint**|新しいインデックスおよびマッピングインデックスのインデックス構築操作によって割り当てられたインデックスページの合計数 (null 非許容)。

## <a name="permissions"></a>アクセス許可

[!INCLUDE[ssCatViewPerm](../../includes/sscatviewperm-md.md)] 詳細については、「 [Metadata Visibility Configuration](../../relational-databases/security/metadata-visibility-configuration.md)」を参照してください。  

## <a name="example"></a>例

 一時停止状態にある再開可能なインデックス再構築操作をすべて一覧表示します。

```sql
SELECT * FROM  sys.index_resumable_operations WHERE STATE = 1;  
```

## <a name="see-also"></a>関連項目

- [ALTER INDEX](../../t-sql/statements/alter-index-transact-sql.md)
- [CREATE INDEX](../../t-sql/statements/create-index-transact-sql.md)
- [カタログビュー](catalog-views-transact-sql.md)
- [オブジェクトカタログビュー](object-catalog-views-transact-sql.md)
- [sys.indexes](sys-xml-indexes-transact-sql.md)
- [sys.index_columns](sys-index-columns-transact-sql.md)
- [sys.xml_indexes](sys-xml-indexes-transact-sql.md)
- [sys.objects](sys-index-columns-transact-sql.md)
- [sys.key_constraints](sys-key-constraints-transact-sql.md)
- [sys.filegroups](sys-filegroups-transact-sql.md)
- [sys.partition_schemes](sys-partition-schemes-transact-sql.md)
- [SQL Server システム カタログに対するクエリに関してよくあるご質問](querying-the-sql-server-system-catalog-faq.md)
