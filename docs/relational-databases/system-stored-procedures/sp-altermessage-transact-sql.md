---
title: sp_altermessage (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/09/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sp_altermessage_TSQL
- sp_altermessage
dev_langs:
- TSQL
helpviewer_keywords:
- sp_altermessage
ms.assetid: 1b28f280-8ef9-48e9-bd99-ec14d79abaca
author: stevestein
ms.author: sstein
ms.openlocfilehash: 4949307cdaf2cc712e56525e872381c2af8256fd
ms.sourcegitcommit: 43c3d8939f6f7b0ddc493d8e7a643eb7db634535
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2019
ms.locfileid: "72304794"
---
# <a name="sp_altermessage-transact-sql"></a>sp_altermessage (Transact-sql)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-xxxx-xxx-md.md)]

  @No__t-0 のインスタンス内のユーザー定義メッセージまたはシステムメッセージの状態を変更します。 ユーザー定義のメッセージは、**システム**カタログビューを使用して表示できます。  

  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_altermessage [ @message_id = ] message_number   ,[ @parameter = ]'write_to_log'  
   ,[ @parameter_value = ]'value'   
```  
  
## <a name="arguments"></a>引数  
 [ **@message_id =** ] *message_number*  
 **Sys. messages**から変更するメッセージのエラー番号を指定します。 *message_number*は**int**で、既定値はありません。  
  
`[ @parameter = ] 'write\_to\_log_'` は、メッセージが @no__t 3 @no__t Windows アプリケーションログに書き込まれることを示すために、 **2parameter_value**と共に使用されます。 *write_to_log*は**sysname**既定値はありません。 *write_to_log*は WITH_LOG または NULL に設定する必要があります。 *Write_to_log*が WITH_LOG または NULL に設定されていて、 **@no__t**の値が**true**の場合、メッセージは Windows アプリケーションログに書き込まれます。 *Write_to_log*が WITH_LOG または NULL に設定されていて、 **@no__t**の値が**false**の場合、メッセージは常に Windows アプリケーションログには書き込まれませんが、エラーが発生した方法によっては書き込まれることがあります。 *Write_to_log*を指定する場合は、 **@no__t 2parameter_value**の値も指定する必要があります。  
  
> [!NOTE]  
>  Windows のアプリケーション ログにメッセージを書き込む場合は、[!INCLUDE[ssDE](../../includes/ssde-md.md)]のエラー ログ ファイルにも同じ内容が書き込まれます。  
  
`[ @parameter_value = ]'value_'` は、エラーが @no__t 3 の Windows アプリケーションログに書き込まれることを示すために **、\@parameter**と共に使用されます。 *値*は**varchar (5)** ,、既定値はありません。 **True**の場合、エラーは常に Windows アプリケーションログに書き込まれます。 **False**の場合、エラーは常に Windows アプリケーションログに書き込まれませんが、エラーが発生した方法によっては書き込まれることがあります。 *Value*を指定する場合は、 **\@parameter**の*write_to_log*も指定する必要があります。  
  
## <a name="return-code-values"></a>リターン コードの値  
 0 (成功) または 1 (失敗)  
  
## <a name="result-sets"></a>結果セット  
 なし  
  
## <a name="remarks"></a>コメント  
 **Sp_altermessage**と WITH_LOG オプションの効果は、 **sp_altermessage**が既存のメッセージのログ記録の動作を変更する点を除いて、RAISERROR with LOG パラメーターと似ています。 メッセージが WITH_LOG に変更された場合は、ユーザーがエラーをどのように呼び出したかに関係なく、常に Windows アプリケーションログに書き込まれます。 WITH_LOG オプションを指定せずに RAISERROR を実行した場合でも、エラーは Windows アプリケーションログに書き込まれます。  
  
 システムメッセージは、 **sp_altermessage**を使用して変更できます。  
  
## <a name="permissions"></a>アクセス許可  
 **Serveradmin**固定サーバーロールのメンバーシップが必要です。  
  
## <a name="examples"></a>使用例  
 次の例では、既存のメッセージ `55001` が Windows アプリケーションログに記録されます。  
  
```  
EXECUTE sp_altermessage 55001, 'WITH_LOG', 'true';  
GO  
```  
  
## <a name="see-also"></a>関連項目  
 [RAISERROR &#40;Transact-SQL&#41;](../../t-sql/language-elements/raiserror-transact-sql.md)   
 [sp_addmessage &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addmessage-transact-sql.md)   
 [sp_dropmessage &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-dropmessage-transact-sql.md)   
 [システム ストアド プロシージャ &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/system-stored-procedures-transact-sql.md)  
  
  
