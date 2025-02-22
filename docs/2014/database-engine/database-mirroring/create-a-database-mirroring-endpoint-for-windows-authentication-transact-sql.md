---
title: Windows 認証でのデータベース ミラーリング エンドポイントの作成 (Transact-SQL) | Microsoft Docs
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: high-availability
ms.topic: conceptual
helpviewer_keywords:
- database mirroring [SQL Server], deployment
- database mirroring [SQL Server], endpoint
- endpoints [SQL Server], database mirroring
- Windows authentication [SQL Server]
- database mirroring [SQL Server], security
ms.assetid: baf1a4b1-6790-4275-b261-490bca33bdb9
author: MikeRayMSFT
ms.author: mikeray
manager: craigg
ms.openlocfilehash: 43bb7fdd5b9c8cf8a73c423ac21e8ba7f779ec79
ms.sourcegitcommit: f912c101d2939084c4ea2e9881eb98e1afa29dad
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72797931"
---
# <a name="create-a-database-mirroring-endpoint-for-windows-authentication-transact-sql"></a>Windows 認証でのデータベース ミラーリング エンドポイントの作成 (Transact-SQL)
  このトピックでは、 [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] に、Windows 認証を使用するデータベース ミラーリング エンドポイントを [!INCLUDE[tsql](../../includes/tsql-md.md)]で作成する方法について説明します。 データベース ミラーリングまたは [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] をサポートするには、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の各インスタンスにデータベース ミラーリング エンドポイントが必要となります。 サーバー インスタンスは、単一のポートを備えたデータベース ミラーリング エンドポイントを 1 つだけ持つことができます。 データベース ミラーリング エンドポイントは、作成される際に、ローカル システムで利用できる任意のポートを使用できます。 サーバー インスタンス上のすべてのデータベース ミラーリング セッションはそのポートでリッスンし、データベース ミラーリングに対するすべての着信接続はそのポートを使用します。  
  
> [!IMPORTANT]  
>  データベース ミラーリング エンドポイントが存在し、既に使用されている場合、そのエンドポイントを使用することをお勧めします。 使用中のエンドポイントを削除すると、既存のセッションが切断されます。  
  
 **このトピックの内容**  
  
-   **Before you begin:**  [Security](#Security)  
  
-   **以下を使用してデータベース ミラーリング エンドポイントを作成するには:** [Transact-SQL](#TsqlProcedure)  
  
##  <a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="Security"></a> Security  
 サーバー インスタンスの認証方法と暗号化方法は、システム管理者が設定します。  
  
> [!IMPORTANT]  
>  RC4 アルゴリズムは非推奨とされます。 [!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)] AES を使用することをお勧めします。  
  
####  <a name="Permissions"></a> アクセス許可  
 CREATE ENDPOINT 権限、または sysadmin 固定サーバー ロールのメンバーシップが必要です。 詳細については、「 [GRANT (エンドポイントの権限の許可) &#40;Transact-SQL&#41;](/sql/t-sql/statements/grant-endpoint-permissions-transact-sql)」を参照してください。  
  
##  <a name="TsqlProcedure"></a> Transact-SQL の使用  
  
#### <a name="to-create-a-database-mirroring-endpoint-that-uses-windows-authentication"></a>Windows 認証を使用するデータベース ミラーリング エンドポイントを作成するには  
  
1.  データベース ミラーリング エンドポイントを作成する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスに接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次のステートメントを使用して、データベース ミラーリング エンドポイントが既に存在するかどうかを判断します。  
  
    ```sql
    SELECT name, role_desc, state_desc FROM sys.database_mirroring_endpoints;
    ```  
  
    > [!IMPORTANT]  
    >  サーバー インスタンスにデータベース ミラーリング エンドポイントが既に存在する場合、サーバー インスタンス上に確立する他のセッションにそのエンドポイントが使用されます。  
  
4.  Transact-SQL を使用して、Windows 認証を使用するエンドポイントを作成するには、CREATE ENDPOINT ステートメントを使用します。 ステートメントは、通常、次のような形式になります。  
  
     CREATE ENDPOINT \<*endpointName*>  
  
     STATE=STARTED  
  
     AS TCP ( LISTENER_PORT = \<*listenerPortList*> )  
  
     FOR DATABASE_MIRRORING  
  
     (  
  
     [ AUTHENTICATION = **WINDOWS** [ \<*authorizationMethod*> ]  
  
     ]  
  
     [ **[,]** ENCRYPTION = **REQUIRED**  
  
     [ ALGORITHM { \<*algorithm*> } ]  
  
     ]  
  
     **[,]** ROLE = \<*role*>  
  
     」を参照)。  
  
     パラメーターの説明  
  
    -   \<*endpointName*> は、サーバー インスタンスのデータベース ミラーリング エンドポイントの一意名です。  
  
    -   STARTED によって、エンドポイントが開始され、接続のリッスンが開始されることを指定します。 データベース ミラーリング エンドポイントは、通常、STARTED 状態で作成されます。 STOPPED 状態 (既定) または DISABLED 状態でセッションを開始することもできます。  
  
    -   *\<listenerPortList>* は、サーバーがデータベース ミラーリング メッセージをリッスンする単一のポート番号 (*nnnn*) です。 TCP のみ使用できます。他のプロトコルを指定するとエラーが発生します。  
  
         ポート番号は、コンピューター システムにつき 1 つだけ使用できます。 データベース ミラーリング エンドポイントは、作成される際に、ローカル システムで利用できる任意のポートを使用できます。 システムの TCP エンドポイントによって現在使用されているポートを識別するには、次の Transact-SQL ステートメントを使用します。  
  
        ```  
        SELECT name, port FROM sys.tcp_endpoints;  
        ```  
  
        > [!IMPORTANT]  
        >  各サーバー インスタンスには、一意のリスナー ポートが 1 つだけ必要です。  
  
    -   Windows 認証の場合、エンドポイントで接続の認証に NTLM または Kerberos だけを使用する場合を除き、AUTHENTICATION オプションは省略可能です。 \<*authorizationMethod*> では、NTLM、KERBEROS、NEGOTIATE のいずれかで、接続の認証に使用する方法を指定します。 既定値の NEGOTIATE を使用すると、エンドポイントでは、使用する Windows ネゴシエーション プロトコルに NTLM または Kerberos のいずれかが選択されます。 ネゴシエーションでは、相手側のエンドポイントの認証レベルに応じて、認証ありまたは認証なしの接続が可能になります。  
  
    -   既定では、ENCRYPTION は REQUIRED に設定されます。 これは、このエンドポイントへのすべての接続に暗号化を使用する必要があることを意味します。 ただし、エンドポイントで暗号化を無効にしたり、オプションにできます。 選択肢は次のとおりです。  
  
        |の値|[定義]|  
        |-----------|----------------|  
        |DISABLED|接続を経由して送信されたデータが暗号化されないことを指定します。|  
        |SUPPORTED|反対側のエンドポイントで SUPPORTED または REQUIRED が指定されている場合に限り、データを暗号化することを指定します。|  
        |REQUIRED|接続を経由して送信されるデータを暗号化する必要があることを示します。|  
  
         あるエンドポイントで暗号化が必要な場合は、他のエンドポイントで ENCRYPTION が SUPPORTED または REQUIRED に設定されている必要があります。  
  
    -   \<*algorithm*> には、エンドポイントの暗号化標準を指定するオプションが用意されています。 \<*algorithm*> の値は、RC4、AES、AES RC4、または RC4 AES の各アルゴリズムまたはそれらの組み合わせになります。  
  
         AES RC4 では、エンドポイントが暗号化アルゴリズムのネゴシエートを行う際に、AES アルゴリズムを優先することが示されます。 RC4 AES では、エンドポイントが暗号化アルゴリズムのネゴシエートを行う際に、RC4 アルゴリズムを優先することが示されます。 両方のエンドポイントで両方のアルゴリズムを異なる順序で指定した場合、接続を受け入れた方のエンドポイントが優先されます。  
  
        > [!NOTE]  
        >  RC4 アルゴリズムは非推奨とされます。 [!INCLUDE[ssNoteDepFutureDontUse](../../includes/ssnotedepfuturedontuse-md.md)] AES を使用することをお勧めします。  
  
    -   \<*role*> では、サーバーが実行できるロールが定義されます。 ROLE の指定は必須です。 ただし、エンドポイントのロールが適用されるのは、データベース ミラーリングの場合のみです。 [!INCLUDE[ssHADR](../../includes/sshadr-md.md)]では、エンドポイントのロールが無視されます。  
  
         サーバー インスタンスが、あるデータベース ミラーリング セッションではあるロールを使用し、他のセッションでは別のロールを使用できるようにするには、ROLE=ALL を指定します。 パートナーまたはミラーリング監視サーバーのいずれかになるようにサーバー インスタンスを制限するには、ROLE=PARTNER または ROLE=WITNESS をそれぞれ指定します。  
  
        > [!NOTE]  
        >  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の各エディションのデータベースミラーリングオプションの詳細については、「 [SQL Server 2014 の各エディションがサポートする機能](../../getting-started/features-supported-by-the-editions-of-sql-server-2014.md)」を参照してください。  
  
     CREATE ENDPOINT 構文の詳細な説明については、「 [CREATE ENDPOINT &#40;Transact-SQL&#41;](/sql/t-sql/statements/create-endpoint-transact-sql)で作成する方法について説明します。  
  
    > [!NOTE]  
    >  既存のエンドポイントを変更するには、「 [ALTER ENDPOINT &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-endpoint-transact-sql)で作成する方法について説明します。  
  
###  <a name="TsqlExample"></a> 例: データベース ミラーリングをサポートするエンドポイントの作成 (Transact-SQL)  
 次の例では、3 台の異なるコンピューター システムに既定のサーバー インスタンスのデータベース ミラーリング エンドポイントを作成します。  
  
|サーバー インスタンスの役割|ホスト コンピューター名|  
|-----------------------------|---------------------------|  
|パートナー (初期はプリンシパル)|`SQLHOST01\.`|  
|パートナー (初期はミラー)|`SQLHOST02\.`|  
|ミラーリング監視サーバー|`SQLHOST03\.`|  
  
 この例では、3 つのエンドポイントすべてでポート番号 7022 を使用しますが、利用可能なポート番号であればどれでも使用できます。 エンドポイントでは既定の Windows 認証を使用するので、AUTHENTICATION オプションは不要です。 エンドポイントはいずれも Windows 認証の既定動作に従い接続のための認証方法をネゴシエートする仕様なので、ENCRYPTION オプションも不要です。 また、すべてのエンドポイントで、既定動作に従い暗号化が必要です。  
  
 各サーバー インスタンスは、パートナーまたはミラーリング監視サーバーのいずれかとしてのみ機能するように制限します。各サーバーのエンドポイントは役割を明示 (ROLE=PARTNER または ROLE=WITNESS) します。  
  
> [!IMPORTANT]  
>  各サーバー インスタンスにはエンドポイントを 1 つしか作成できません。 したがって、1 つのサーバー インスタンスをセッションによってパートナーにしたりミラーリング監視サーバーにしたりする場合は、ROLE=ALL を指定します。  
  
```sql
--Endpoint for initial principal server instance, which  
--is the only server instance running on SQLHOST01.  
CREATE ENDPOINT endpoint_mirroring  
    STATE = STARTED  
    AS TCP ( LISTENER_PORT = 7022 )  
    FOR DATABASE_MIRRORING (ROLE=PARTNER);  
GO  
--Endpoint for initial mirror server instance, which  
--is the only server instance running on SQLHOST02.  
CREATE ENDPOINT endpoint_mirroring  
    STATE = STARTED  
    AS TCP ( LISTENER_PORT = 7022 )  
    FOR DATABASE_MIRRORING (ROLE=PARTNER);  
GO  
--Endpoint for witness server instance, which  
--is the only server instance running on SQLHOST03.  
CREATE ENDPOINT endpoint_mirroring  
    STATE = STARTED  
    AS TCP ( LISTENER_PORT = 7022 )  
    FOR DATABASE_MIRRORING (ROLE=WITNESS);  
GO  
```  
  
##  <a name="RelatedTasks"></a> 関連タスク  

### <a name="to-configure-a-database-mirroring-endpoint"></a>データベース ミラーリング エンドポイントを構成するには
  
-   [AlwaysOn 可用性グループ&#40;SQL Server PowerShell のデータベースミラーリングエンドポイントを作成する&#41;](../availability-groups/windows/database-mirroring-always-on-availability-groups-powershell.md)  
  
-   [データベース ミラーリング エンドポイントでの証明書の使用 &#40;Transact-SQL&#41;](use-certificates-for-a-database-mirroring-endpoint-transact-sql.md)  
  
    -   [データベース ミラーリング エンドポイントで発信接続に証明書を使用できるようにする &#40;Transact-SQL&#41;](database-mirroring-use-certificates-for-outbound-connections.md)  
  
    -   [データベース ミラーリング エンドポイントで着信接続に証明書を使用できるようにする &#40;Transact-SQL&#41;](database-mirroring-use-certificates-for-inbound-connections.md)  
  
-   [サーバー ネットワーク アドレスの指定 &#40;データベース ミラーリング&#41;](specify-a-server-network-address-database-mirroring.md)  
  
-   [可用性レプリカを追加または変更する場合のエンドポイント URL の指定 &#40;SQL Server&#41;](../availability-groups/windows/specify-endpoint-url-adding-or-modifying-availability-replica.md)  
  
 **データベース ミラーリング エンドポイントに関する情報を表示するには**  
  
-   [sys.database_mirroring_endpoints &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-database-mirroring-endpoints-transact-sql)  
  
## <a name="see-also"></a>「  
 [ALTER ENDPOINT &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-endpoint-transact-sql)   
 [暗号化アルゴリズムの選択](../../relational-databases/security/encryption/choose-an-encryption-algorithm.md)   
 [CREATE ENDPOINT &#40;Transact-SQL&#41;](/sql/t-sql/statements/create-endpoint-transact-sql)   
 [サーバー ネットワーク アドレスの指定 &#40;データベース ミラーリング&#41;](specify-a-server-network-address-database-mirroring.md)   
 [Windows 認証を使用したデータベース ミラーリングの設定の例 &#40;Transact-SQL&#41;](example-setting-up-database-mirroring-using-windows-authentication-transact-sql.md)   
 [データベース ミラーリング エンドポイント &#40;SQL Server&#41;](the-database-mirroring-endpoint-sql-server.md)  
