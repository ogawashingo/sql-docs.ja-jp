---
title: sp_send_dbmail (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/10/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sendmail_sp_TSQL
- sendmail_sp
- SP_SEND_DBMAIL_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_send_dbmail
ms.assetid: f1d7a795-a3fd-4043-ac4b-c781e76dab47
author: stevestein
ms.author: sstein
ms.openlocfilehash: 42c763b37f5c721a259fbe87eca804e22f5c27b5
ms.sourcegitcommit: f6bfe4a0647ce7efebaca11d95412d6a9a92cd98
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974374"
---
# <a name="sp_send_dbmail-transact-sql"></a>sp_send_dbmail (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-asdbmi-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-asdbmi-xxxx-xxx-md.md)]

  指定した受信者に電子メール メッセージを送信します。 このメッセージには、クエリ結果セット、添付ファイル、またはその両方が含まれる場合があります。 メールがデータベースメールキューに正常に配置されると、 **sp_send_dbmail**はメッセージの**mailitem_id**を返します。 このストアドプロシージャは**msdb**データベースにあります。  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_send_dbmail [ [ @profile_name = ] 'profile_name' ]  
    [ , [ @recipients = ] 'recipients [ ; ...n ]' ]  
    [ , [ @copy_recipients = ] 'copy_recipient [ ; ...n ]' ]  
    [ , [ @blind_copy_recipients = ] 'blind_copy_recipient [ ; ...n ]' ]  
    [ , [ @from_address = ] 'from_address' ]  
    [ , [ @reply_to = ] 'reply_to' ]   
    [ , [ @subject = ] 'subject' ]   
    [ , [ @body = ] 'body' ]   
    [ , [ @body_format = ] 'body_format' ]  
    [ , [ @importance = ] 'importance' ]  
    [ , [ @sensitivity = ] 'sensitivity' ]  
    [ , [ @file_attachments = ] 'attachment [ ; ...n ]' ]  
    [ , [ @query = ] 'query' ]  
    [ , [ @execute_query_database = ] 'execute_query_database' ]  
    [ , [ @attach_query_result_as_file = ] attach_query_result_as_file ]  
    [ , [ @query_attachment_filename = ] query_attachment_filename ]  
    [ , [ @query_result_header = ] query_result_header ]  
    [ , [ @query_result_width = ] query_result_width ]  
    [ , [ @query_result_separator = ] 'query_result_separator' ]  
    [ , [ @exclude_query_output = ] exclude_query_output ]  
    [ , [ @append_query_error = ] append_query_error ]  
    [ , [ @query_no_truncate = ] query_no_truncate ]   
    [ , [ @query_result_no_padding = ] @query_result_no_padding ]   
    [ , [ @mailitem_id = ] mailitem_id ] [ OUTPUT ]  
```  
  
## <a name="arguments"></a>引数  
`[ @profile_name = ] 'profile_name'` は、メッセージを送信するプロファイルの名前です。 *Profile_name*の型は**sysname**で、既定値は NULL です。 *Profile_name*には、既存のデータベースメールプロファイルの名前を指定する必要があります。 No *profile_name*を指定した場合、 **sp_send_dbmail**は、現在のユーザーの既定のプライベートプロファイルを使用します。 ユーザーが既定のプライベートプロファイルを持っていない場合、 **sp_send_dbmail**は**msdb**データベースの既定のパブリックプロファイルを使用します。 ユーザーに既定のプライベートプロファイルがなく、データベースの既定のパブリックプロファイルがない場合は、 **@no__t 1profile_name**を指定する必要があります。  
  
`[ @recipients = ] 'recipients'` は、メッセージの送信先となる電子メールアドレスのセミコロン区切りの一覧です。 受信者リストの型は**varchar (max)** です。 このパラメーターは省略可能ですが、少なくとも1つの **@no__t の受信者**、 **\@copy5blind_copy_recipients**、または **@no__t**を指定する必要があります。または、 **sp_send_dbmail**はエラーを返します。  
  
`[ @copy_recipients = ] 'copy_recipients'` は、メッセージをカーボンコピーする電子メールアドレスのセミコロン区切りのリストです。 コピーの受信者リストの型は**varchar (max)** です。 このパラメーターは省略可能ですが、少なくとも1つの **@no__t の受信者**、 **\@copy5blind_copy_recipients**、または **@no__t**を指定する必要があります。または、 **sp_send_dbmail**はエラーを返します。  
  
`[ @blind_copy_recipients = ] 'blind_copy_recipients'` は、メッセージをブラインドカーボンコピーする電子メールアドレスのセミコロン区切りのリストです。 ブラインドコピーの受信者リストの型は**varchar (max)** です。 このパラメーターは省略可能ですが、少なくとも1つの **@no__t の受信者**、 **\@copy5blind_copy_recipients**、または **@no__t**を指定する必要があります。または、 **sp_send_dbmail**はエラーを返します。  
  
`[ @from_address = ] 'from_address'` は、電子メールメッセージの [差出人アドレス] の値です。 これは、メールプロファイルの設定を上書きするために使用される省略可能なパラメーターです。 このパラメーターの型は**varchar (MAX)** です。 オーバーライドが許可されるかどうかは、SMTP のセキュリティ設定によって決まります。 パラメーターを指定しない場合、既定値は NULL になります。  
  
`[ @reply_to = ] 'reply_to'` は、電子メールメッセージの返信先アドレスの値です。 有効な値として1つの電子メールアドレスのみを受け入れます。 これは、メールプロファイルの設定を上書きするために使用される省略可能なパラメーターです。 このパラメーターの型は**varchar (MAX)** です。 オーバーライドが許可されるかどうかは、SMTP のセキュリティ設定によって決まります。 パラメーターを指定しない場合、既定値は NULL になります。  
  
`[ @subject = ] 'subject'` は、電子メールメッセージの件名です。 サブジェクトの型は**nvarchar (255)** です。 件名が指定されていない場合、既定値は ' SQL Server Message ' です。  
  
`[ @body = ] 'body'` は電子メールメッセージの本文です。 メッセージの本文の型は**nvarchar (max)** で、既定値は NULL です。  
  
`[ @body_format = ] 'body_format'` はメッセージ本文の形式です。 パラメーターの型は**varchar (20)** ,、既定値は NULL です。 指定した場合、送信メッセージのヘッダーには、メッセージの本文が指定の形式であることを示す文字列が設定されます。 パラメーターには、次のいずれかの値を含めることができます。  
  
-   [TEXT]  
  
-   HTML (HTML)  
  
 既定値は TEXT です。  
  
`[ @importance = ] 'importance'` はメッセージの重要度です。 パラメーターの型は**varchar (6)** です。 パラメーターには、次のいずれかの値を含めることができます。  
  
-   Low  
  
-   標準  
  
-   高  
  
 既定値は Normal です。  
  
`[ @sensitivity = ] 'sensitivity'` は、メッセージの秘密度です。 パラメーターの型は**varchar (12)** です。 パラメーターには、次のいずれかの値を含めることができます。  
  
-   標準  
  
-   Personal  
  
-   プライベート  
  
-   部外  
  
 既定値は Normal です。  
  
`[ @file_attachments = ] 'file_attachments'` は、電子メールメッセージに添付するファイル名のセミコロン区切りの一覧です。 リスト内のファイルは絶対パスとして指定する必要があります。 添付ファイルの一覧は、 **nvarchar (max)** 型です。 既定では、データベース メールの添付ファイルは 1 ファイルにつき 1 MB に制限されます。  
 
 > [!IMPORTANT]
 > このパラメーターは、ローカルファイルシステムにアクセスできないため、Azure SQL Managed Instance では使用できません。
  
`[ @query = ] 'query'` は実行するクエリです。 クエリの結果は、ファイルとして添付するか、電子メールメッセージの本文に含めることができます。 クエリの型は**nvarchar (max)** で、任意の有効な [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントを含めることができます。 クエリは別のセッションで実行されるため、 **sp_send_dbmail**を呼び出すスクリプトのローカル変数はクエリで使用できません。  
  
`[ @execute_query_database = ] 'execute_query_database'` は、ストアドプロシージャでクエリを実行するデータベースコンテキストです。 パラメーターのデータ型は**sysname**で、既定値は現在のデータベースです。 このパラメーターは **、\@ のクエリ**が指定されている場合にのみ適用されます。  
  
`[ @attach_query_result_as_file = ] attach_query_result_as_file` は、クエリの結果セットが添付ファイルとして返されるかどうかを指定します。 *attach_query_result_as_file*の型は**bit**で、既定値は0です。  
  
 値が0の場合、クエリの結果は、 **\@body**パラメーターの内容の後に、電子メールメッセージの本文に含まれます。 値が1の場合、結果は添付ファイルとして返されます。 このパラメーターは **、\@ のクエリ**が指定されている場合にのみ適用されます。  
  
`[ @query_attachment_filename = ] query_attachment_filename` は、クエリの添付ファイルの結果セットに使用するファイル名を指定します。 *query_attachment_filename*の型は**nvarchar (255)** ,、既定値は NULL です。 *Attach_query_result*が0の場合、このパラメーターは無視されます。 *Attach_query_result*が1で、このパラメーターが NULL の場合、データベースメールによって任意のファイル名が作成されます。  
  
`[ @query_result_header = ] query_result_header` クエリの結果に列ヘッダーを含めるかどうかを指定します。 Query_result_header 値の型は**bit**です。 値が1の場合、クエリ結果には列ヘッダーが含まれます。 値が 0 の場合、クエリの結果には列のヘッダーは含まれません。 このパラメーターの既定値は**1**です。 このパラメーターは **、\@ のクエリ**が指定されている場合にのみ適用されます。  
 
   >[!NOTE]
   > @No__t-0query_result_header を0に設定し、\@query_no_truncate を1に設定すると、次のエラーが発生する可能性があります。
   > <br> メッセージ22050、レベル16、状態1、行 12:エラー番号-2147024809 を使用して sqlcmd ライブラリを初期化できませんでした。
  
`[ @query_result_width = ] query_result_width` は、クエリの結果の書式設定に使用する行の幅を文字数で指定します。 *Query_result_width*の型は**int**で、既定値は256です。 指定する値は10から32767の間である必要があります。 このパラメーターは **、\@ のクエリ**が指定されている場合にのみ適用されます。  
  
`[ @query_result_separator = ] 'query_result_separator'` は、クエリ出力で列を区切るために使用される文字です。 区切り記号の型は**char (1)** です。 既定値は ' ' (スペース) です。  
  
`[ @exclude_query_output = ] exclude_query_output` は、電子メールメッセージでクエリ実行の出力を返すかどうかを指定します。 **exclude_query_output**はビット,、既定値は0です。 このパラメーターが0の場合、 **sp_send_dbmail**ストアドプロシージャを実行すると、クエリの実行結果として返されたメッセージがコンソールに出力されます。 このパラメーターが1の場合、 **sp_send_dbmail**ストアドプロシージャを実行しても、クエリ実行メッセージはコンソールに出力されません。  
  
`[ @append_query_error = ] append_query_error` **\@query**引数で指定されたクエリからエラーが返されたときに電子メールを送信するかどうかを指定します。 **append_query_error**は**ビット**,、既定値は0です。 このパラメーターが1の場合、データベースメールは電子メールメッセージを送信し、電子メールメッセージの本文にクエリエラーメッセージを含めます。 このパラメーターが0の場合、データベースメールは電子メールメッセージを送信せず、 **sp_send_dbmail**は失敗を示すリターンコード1で終了します。  
  
`[ @query_no_truncate = ] query_no_truncate` は、大規模な可変長データ型 (**varchar (max)** 、 **nvarchar (max)** 、 **varbinary (max)** 、 **xml**、 **text**、 **ntext**、image) の切り捨てを回避するオプションを使用してクエリを実行するかどうかを指定します。、、およびユーザー定義データ型)。 設定すると、クエリ結果に列ヘッダーは含まれません。 *Query_no_truncate*値の型は**bit**です。 値が 0 の場合や指定されていない場合、クエリ内の列は 256 文字に切り捨てられます。 値が1の場合、クエリ内の列は切り捨てられません。 このパラメーターの既定値は0です。  
  
> [!NOTE]  
>  大量のデータを使用する場合、\@**query_no_truncate**オプションは追加のリソースを消費するため、サーバーのパフォーマンスが低下する可能性があります。  
  
`[ @query_result_no_padding ] @query_result_no_padding` 型は bit です。 既定値は 0 です。 を1に設定すると、クエリ結果は埋め込まれず、ファイルサイズが小さくなる可能性があります。@No__t-0query_result_no_padding を1に設定し、@no__t 1query_result_width パラメーターを設定した場合 @no__t、2query_result_no_padding パラメーターを指定すると、@no__t 3query_result_width パラメーターが上書きされます。  
  
 この場合、エラーは発生しません。  
 
  >[!NOTE]
  > @No__t-0query_result_no_padding を1に設定し、1query_no_truncate @no__t のパラメーターを指定すると、次のエラーが発生する可能性があります。
  > <br> メッセージ22050、レベル16、状態1、行 0:0query_result_no_append オプションと @no__t 1query_no_truncate オプションが同時に指定されていない @no__t ため、クエリを実行できませんでした。 
  
 @No__t-0query_result_no_padding を1に設定し、@no__t 1query_no_truncate パラメーターを設定した場合は、エラーが発生します。  
  
`[ @mailitem_id = ] mailitem_id [ OUTPUT ]` 省略可能な出力パラメーターは、メッセージの*mailitem_id*を返します。 *Mailitem_id*の型は**int**です。  
  
## <a name="return-code-values"></a>リターン コードの値  
 リターン コードが 0 の場合は成功を示します。 それ以外の値は失敗を意味します。 失敗したステートメントのエラーコードは、\@ @ no__t-1 エラー変数に格納されます。  
  
## <a name="result-sets"></a>結果セット  
 成功すると、"メールがキューに登録されました。" というメッセージが返されます。  
  
## <a name="remarks"></a>コメント  
 使用する前に、データベースメール構成ウィザードまたは**sp_configure**を使用してデータベースメールを有効にする必要があります。  
  
 **sysmail_stop_sp**は、外部プログラムが使用する Service Broker オブジェクトを停止することによってデータベースメールを停止します。 **sp_send_dbmail**は、 **sysmail_stop_sp**を使用してデータベースメールが停止した場合でもメールを受け入れます。 データベースメールを開始するには、 **sysmail_start_sp**を使用します。  
  
 **@No__t-1 プロファイル**が指定されていない場合、 **sp_send_dbmail**では既定のプロファイルが使用されます。 電子メール メッセージを送信するユーザーに既定のプライベート プロファイルがある場合、データベース メールではそのプロファイルが使用されます。 ユーザーに既定のプライベートプロファイルがない場合、 **sp_send_dbmail**では既定のパブリックプロファイルが使用されます。 ユーザーの既定のプライベートプロファイルがなく、既定のパブリックプロファイルもない場合、 **sp_send_dbmail**はエラーを返します。  
  
 **sp_send_dbmail**では、コンテンツのない電子メールメッセージはサポートされません。 電子メールメッセージを送信するには、少なくとも1つの **\@body**、 **\@query**、 **\@fileattachments**、または **\@subject**を指定する必要があります。 それ以外の場合、 **sp_send_dbmail**はエラーを返します。  
  
 データベース メールでは、ファイルへのアクセス制御に現在のユーザーの [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows セキュリティ コンテキストが使用されます。 したがって、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証で認証されたユーザーは、 **\@fileattachments**を使用してファイルを添付することはできません。 Windows では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を使用してリモート コンピューター間で資格情報を交換することは許可されません。 このため、を実行して @no__t いるコンピューター以外のコンピューターからコマンドを実行した場合、データベースメールはネットワーク共有からファイルを添付できない可能性があります。  
  
 **@No__t-1query**と **\@fileattachments**の両方が指定されていて、ファイルが見つからない場合でも、クエリは実行されますが、電子メールは送信されません。  
  
 クエリを指定すると、結果セットはインラインテキストとして書式設定されます。 結果セットにあるバイナリ データは 16 進数形式で送信されます。  
  
 パラメーター **\@ 受信者**、 **\@copy受信者**、および **@no__t 5blind_copy_recipients**は、セミコロンで区切られた電子メールアドレスのリストです。 これらのパラメーターのうち、少なくとも1つを指定する必要があります。指定しないと、 **sp_send_dbmail**からエラーが返されます。  
  
 トランザクションコンテキストを使用せずに**sp_send_dbmail**を実行すると、データベースメールが開始され、暗黙のトランザクションがコミットされます。 既存のトランザクション内から**sp_send_dbmail**を実行する場合、データベースメールは、変更をコミットまたはロールバックするためにユーザーに依存します。 内部トランザクションは開始されません。  
  
## <a name="permissions"></a>アクセス許可  
 **Sp_send_dbmail**の実行権限は、既定では、 **Msdb**データベースの**databasemailuser**データベースロールのすべてのメンバーに与えます。 ただし、メッセージを送信するユーザーに要求のプロファイルを使用するアクセス許可がない場合、 **sp_send_dbmail**はエラーを返し、メッセージを送信しません。  
  
## <a name="examples"></a>使用例  
  
### <a name="a-sending-an-e-mail-message"></a>A. 電子メールメッセージの送信  
 この例では、電子メールアドレス `myfriend@Adventure-Works.com` を使用して、友人に電子メールメッセージを送信します。 メッセージの件名 `Automated Success Message` です。 メッセージの本文には `'The stored procedure finished successfully'` という文が含まれています。  
  
```  
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'Adventure Works Administrator',  
    @recipients = 'yourfriend@Adventure-Works.com',  
    @body = 'The stored procedure finished successfully.',  
    @subject = 'Automated Success Message' ;  
```  
  
### <a name="b-sending-an-e-mail-message-with-the-results-of-a-query"></a>B. 電子メール メッセージをクエリの結果と共に送信する  
 この例では、電子メールアドレス `yourfriend@Adventure-Works.com` を使用して、友人に電子メールメッセージを送信します。 メッセージの件名は `Work Order Count` で、このメッセージでは `DueDate` が 2004 年 4 月 30 日から 2 日以内となっている作業指示の番号を表示するクエリが実行されます。 データベースメール結果をテキストファイルとして添付します。  
  
```  
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'Adventure Works Administrator',  
    @recipients = 'yourfriend@Adventure-Works.com',  
    @query = 'SELECT COUNT(*) FROM AdventureWorks2012.Production.WorkOrder  
                  WHERE DueDate > ''2004-04-30''  
                  AND  DATEDIFF(dd, ''2004-04-30'', DueDate) < 2' ,  
    @subject = 'Work Order Count',  
    @attach_query_result_as_file = 1 ;  
```  
  
### <a name="c-sending-an-html-e-mail-message"></a>C. HTML 電子メールメッセージの送信  
 この例では、電子メールアドレス `yourfriend@Adventure-Works.com` を使用して、友人に電子メールメッセージを送信します。 メッセージの件名は 0 @no__t で、2004年4月30日から2日以内の @no__t を持つ作業指示を示す HTML ドキュメントが含まれています。 データベース メールでは、このメッセージが HTML 形式で送信されます。  
  
```  
DECLARE @tableHTML  NVARCHAR(MAX) ;  
  
SET @tableHTML =  
    N'<H1>Work Order Report</H1>' +  
    N'<table border="1">' +  
    N'<tr><th>Work Order ID</th><th>Product ID</th>' +  
    N'<th>Name</th><th>Order Qty</th><th>Due Date</th>' +  
    N'<th>Expected Revenue</th></tr>' +  
    CAST ( ( SELECT td = wo.WorkOrderID,       '',  
                    td = p.ProductID, '',  
                    td = p.Name, '',  
                    td = wo.OrderQty, '',  
                    td = wo.DueDate, '',  
                    td = (p.ListPrice - p.StandardCost) * wo.OrderQty  
              FROM AdventureWorks.Production.WorkOrder as wo  
              JOIN AdventureWorks.Production.Product AS p  
              ON wo.ProductID = p.ProductID  
              WHERE DueDate > '2004-04-30'  
                AND DATEDIFF(dd, '2004-04-30', DueDate) < 2   
              ORDER BY DueDate ASC,  
                       (p.ListPrice - p.StandardCost) * wo.OrderQty DESC  
              FOR XML PATH('tr'), TYPE   
    ) AS NVARCHAR(MAX) ) +  
    N'</table>' ;  
  
EXEC msdb.dbo.sp_send_dbmail @recipients='yourfriend@Adventure-Works.com',  
    @subject = 'Work Order List',  
    @body = @tableHTML,  
    @body_format = 'HTML' ;  
```  
  
## <a name="see-also"></a>関連項目  
 [データベース メール](../../relational-databases/database-mail/database-mail.md)   
 [データベースメール構成オブジェクト](../../relational-databases/database-mail/database-mail-configuration-objects.md)   
 [ストアドプロシージャ&#40;のデータベースメール transact-sql&#41;](../../relational-databases/system-stored-procedures/database-mail-stored-procedures-transact-sql.md)   
 [sp_addrolemember &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-addrolemember-transact-sql.md)  
  
  
