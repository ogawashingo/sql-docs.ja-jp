---
title: sp_addtype (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sp_addtype
- sp_addtype_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_addtype
ms.assetid: ed72cd8e-5ff7-4084-8458-2d8ed279d817
author: stevestein
ms.author: sstein
ms.openlocfilehash: ab825ce5eb1310f3ff502965e409731b8741932e
ms.sourcegitcommit: 43c3d8939f6f7b0ddc493d8e7a643eb7db634535
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2019
ms.locfileid: "72305138"
---
# <a name="sp_addtype-transact-sql"></a>sp_addtype (Transact-sql)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-xxxx-xxx-md.md)]

  別名データ型を作成します。  
  
> [!IMPORTANT]  
>  [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)] 代わりに[CREATE TYPE](../../t-sql/statements/create-type-transact-sql.md)を使用してください。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_addtype [ @typename = ] type,   
    [ @phystype = ] system_data_type   
    [ , [ @nulltype = ] 'null_type' ] ;  
```  
  
## <a name="arguments"></a>引数  
`[ @typename = ] type` 別名データ型の名前を指定します。 別名データ型名は、[識別子](../../relational-databases/databases/database-identifiers.md)の規則に従う必要があり、各データベース内で一意である必要があります。 *種類*は**sysname**で、既定値はありません。  
  
`[ @phystype = ] system_data_type` は、指定された物理データ型または @no__t 指定されたデータ型で、別名データ型の基になるデータ型です。*system_data_type*は**sysname**で、既定値はありません。次のいずれかの値を指定できます。  
  
||||  
|-|-|-|  
|**bigint**|**binary(n)**|**bit**|  
|**char(n)**|**datetime**|**decimal**|  
|**float**|**image**|**int**|  
|**money**|**nchar(n)**|**ntext**|  
|**numeric**|**nvarchar(n)**|**real**|  
|**smalldatetime**|**smallint**|**smallmoney**|  
|**sql_variant**|**text**|**tinyint**|  
|**uniqueidentifier**|**varbinary(n)**|**varchar(n)**|  
  
 空白または句読点が埋め込まれているすべてのパラメーターには引用符が必要です。 使用できるデータ型の詳細については、「 [ &#40;データ型&#41;transact-sql](../../t-sql/data-types/data-types-transact-sql.md)」を参照してください。  
  
 *n*  
 選択したデータ型の長さを示す負以外の整数を指定します。  
  
 *IRTRAN-P*  
 小数点の左側と右側に格納できる 10 進数の最大合計桁数を示す、負以外の整数を指定します。 詳細については、次を参照してください[decimal and numeric &#40;Transact-SQL&#41;](../../t-sql/data-types/decimal-and-numeric-transact-sql.md).  
  
 *s*  
 小数点の右側に格納できる10進数の最大桁数を示す負以外の整数で、有効桁数以下である必要があります。 詳細については、次を参照してください[decimal and numeric &#40;Transact-SQL&#41;](../../t-sql/data-types/decimal-and-numeric-transact-sql.md).  
  
`[ @nulltype = ] 'null_type'` は、別名データ型が null 値を処理する方法を示します。 *null_type*は**varchar (** 8 **)** ,、既定値は null の場合、単一引用符で囲む必要があります (' null '、' not NULL '、または ' NONULL ')。 *Null_type*が**sp_addtype**によって明示的に定義されていない場合は、現在の既定の null 値の許容属性に設定されます。 GETANSINULL システム関数を使用して、現在の既定の null 値の許容属性を決定します。 これは、SET ステートメントまたは ALTER DATABASE を使用して調整できます。 NULL 値の許容属性は、明示的に定義してください。 **@No__t-1phの**種類が**ビット**で、 **\@nulltype**が指定されていない場合、既定値は NULL ではありません。  
  
> [!NOTE]  
>  *Null_type*パラメーターは、このデータ型の既定の null 値を許容するかどうかのみを定義します。 テーブルの作成時に別名データ型を使用するときに null 値の許容属性が明示的に定義されている場合は、定義された null 値許容属性よりも優先されます。 詳細については、「 [ALTER TABLE &#40;transact-sql&#41; ](../../t-sql/statements/alter-table-transact-sql.md) 」と「 [CREATE TABLE &#40;transact-sql&#41;](../../t-sql/statements/create-table-transact-sql.md)」を参照してください。  
  
## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または 1 (失敗)  
  
## <a name="result-sets"></a>結果セット  
 なし  
  
## <a name="remarks"></a>コメント  
 別名データ型の名前は、データベース内で一意であることが必要ですが、異なる名前の別名データ型で同じ定義を使用することは可能です。  
  
 **Sp_addtype**を実行すると、特定のデータベースの**タイプ**カタログビューに表示される別名データ型が作成されます。 別名データ型をすべての新しいユーザー定義データベースで使用できるようにする必要がある場合は、それを**モデル**に追加します。 作成した別名データ型は、CREATE TABLE または ALTER TABLE で使用できます。別名データ型にデフォルトやルールをバインドすることもできます。 **Sp_addtype**を使用して作成されるすべてのスカラー別名データ型は、 **dbo**スキーマに含まれています。  
  
 別名データ型は、データベースの既定の照合順序を継承します。 別名型の列と変数の照合順序は、[!INCLUDE[tsql](../../includes/tsql-md.md)] CREATE TABLE、ALTER TABLE、および DECLARE @*local_variable*ステートメントで定義されています。 データベースの既定の照合順序の変更は、新しい列および型の変数にのみ適用されます。既存の照合順序は変更されません。  
  
> [!IMPORTANT]  
>  旧バージョンとの互換性のために、 **sp_addtype**を使用して作成された別名データ型に対する REFERENCES 権限が、 **public**データベースロールに自動的に付与されます。 注**sp_addtype**の代わりに CREATE TYPE ステートメントを使用して別名データ型を作成すると、そのような自動許可は行われません。  
  
 別名データ型は、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] **timestamp**、 **table**、 **xml**、 **varchar (max)** 、 **nvarchar (max** )、または**varbinary (max)** データ型を使用して定義することはできません。  
  
## <a name="permissions"></a>アクセス許可  
 **Db_owner**固定データベースロールまたは**db_ddladmin**固定データベースロールのメンバーシップが必要です。  
  
## <a name="examples"></a>使用例  
  
### <a name="a-creating-an-alias-data-type-that-does-not-allow-for-null-values"></a>A. Null 値を許容しない別名データ型を作成する  
 次の例では、@no__t の `ssn` (社会保障番号) という名前の別名データ型を作成します。このデータ型は、-1 によって指定された**varchar**データ型に基づいています。 `ssn` 型は、11 桁の社会保障番号 (999-99-9999) を格納する列で使用されます。 この列で NULL 値は許容されません。  
  
 `varchar(11)` には区切り記号 (かっこ) が含まれているため、単一引用符で囲みます。  
  
```  
USE master;  
GO  
EXEC sp_addtype ssn, 'varchar(11)', 'NOT NULL';  
GO  
```  
  
### <a name="b-creating-an-alias-data-type-that-allows-for-null-values"></a>B. NULL 値を許容する別名データ型を作成する  
 次の例では、NULL 値を許容する、`datetime` 型に基づく `birthday` という別名データ型を作成します。  
  
```  
USE master;  
GO  
EXEC sp_addtype birthday, datetime, 'NULL';  
```  
  
### <a name="c-creating-additional-alias-data-types"></a>C. 追加の別名データ型を作成する  
 次の例では、2つの追加の別名データ型 (`telephone` と `fax`) を作成します。これは、国内電話番号と fax 番号の両方に使用されます。  
  
```  
USE master;  
GO  
EXEC sp_addtype telephone, 'varchar(24)', 'NOT NULL';  
GO  
EXEC sp_addtype fax, 'varchar(24)', 'NULL';  
GO  
```  
  
## <a name="see-also"></a>参照  
 [データベース エンジン ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/database-engine-stored-procedures-transact-sql.md)   
 [CREATE TYPE &#40;Transact-SQL&#41;](../../t-sql/statements/create-type-transact-sql.md)   
 [CREATE DEFAULT &#40;Transact-SQL&#41;](../../t-sql/statements/create-default-transact-sql.md)   
 [CREATE RULE &#40;Transact-SQL&#41;](../../t-sql/statements/create-rule-transact-sql.md)   
 [sp_bindefault &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-bindefault-transact-sql.md)   
 [sp_bindrule &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-bindrule-transact-sql.md)   
 [sp_droptype &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-droptype-transact-sql.md)   
 [sp_rename &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-rename-transact-sql.md)   
 [sp_unbindefault &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-unbindefault-transact-sql.md)   
 [sp_unbindrule &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-unbindrule-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
  
