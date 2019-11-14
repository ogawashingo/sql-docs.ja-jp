---
title: データベースを別のサーバーで使用できるようにするときのメタデータの管理 | Microsoft Docs
ms.custom: ''
ms.date: 08/24/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
helpviewer_keywords:
- cross-database queries [SQL Server]
- logins [SQL Server], recreating on another server instance
- triggers [SQL Server], DLL
- user-defined error messages [SQL Server]
- permissions [SQL Server], metadata access
- Full-Text Engine [SQL Server]
- metadata [SQL Server], databases available to other instances
- jobs [SQL Server Agent], recreating on another server instance
- failover [SQL Server], managing metadata
- event notifications [SQL Server], metadata
- database mirroring [SQL Server], metadata
- startup options [SQL Server]
- restoring [SQL Server], onto another server instance
- linked servers [SQL Server], metadata
- WMI Provider for Server Events, metadata
- attaching databases [SQL Server]
- log shipping [SQL Server], metadata
- encryption [SQL Server], metadata
- server configuration [SQL Server]
- distributed queries [SQL Server], metadata
- extended stored procedures [SQL Server], metadata
- credentials [SQL Server], metadata
- copying databases
ms.assetid: 5d98cf2a-9fc2-4610-be72-b422b8682681
author: stevestein
ms.author: sstein
ms.openlocfilehash: d61d42a5b684032898a0eabc3dbc4a1b350653d1
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67940725"
---
# <a name="manage-metadata-when-making-a-database-available-on-another-server"></a>データベースを別のサーバーで使用できるようにするときのメタデータの管理
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]
  この記事は、次の状況に関連しています。  
  
-   [!INCLUDE[ssHADR](../../includes/sshadr-md.md)] 可用性グループの可用性レプリカを構成する場合。  
  
-   データベースのデータベース ミラーリングを設定する場合。  
  
-   ログ配布構成内のプライマリ サーバーとセカンダリ サーバー間でロールの変更を準備する場合。  
  
-   別のサーバー インスタンスにデータベースを復元する場合。  
  
-   別のサーバー インスタンスにデータベースのコピーをアタッチする場合。  
  
 アプリケーションによっては、シングル ユーザー データベースのスコープの外部にある情報、エンティティ、オブジェクトに依存することがあります。 通常、アプリケーションには、 **master** データベースと **msdb** データベースに対する依存関係があります。また、ユーザー データベースにも依存関係があります。 ユーザー データベースの外部に格納される (ユーザー データベースを正しく機能させるために必要な) すべてのデータは、対象のサーバー インスタンスで使用できるようにする必要があります。 たとえば、アプリケーションのログインが、 **master** データベースにメタデータとして格納されているため、それらを移行先サーバーで再作成する必要があります。 アプリケーションまたはデータベースのメンテナンス プランが、メタデータが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] msdb **データベースに格納される** エージェント ジョブに依存する場合、対象のサーバー インスタンスでこれらのジョブを再作成する必要があります。 同様に、サーバーレベルのトリガーのメタデータは **master**に格納されます。  
  
 アプリケーションのデータベースを別のサーバー インスタンスに移動するときは、移行先サーバー インスタンス上の **master** および **msdb** に、依存しているエンティティとオブジェクトのすべてのメタデータを再作成する必要があります。 たとえば、データベース アプリケーションでサーバーレベルのトリガーを使用している場合、そのデータベースを新しいシステムでアタッチまたは復元するだけでは不十分です。 このデータベースは、 **master** データベース内にこれらのトリガーのメタデータを手動で再作成しない限り、正常に動作しません。  
  
##  <a name="information_entities_and_objects"></a> ユーザー データベースの外部に格納される情報、エンティティ、およびオブジェクト  
 ここからは、別のサーバー インスタンスで使用できるようにしたデータベースに影響を及ぼすことが考えられる問題の概要を説明します。 次の一覧に示す情報、エンティティ、またはオブジェクトのうち、1 種類以上の項目を再作成する必要が生じる場合があります。 概要を参照するには、該当する項目のリンクをクリックしてください。  
  
-   [サーバー構成の設定](#server_configuration_settings)  
  
-   [[資格情報]](#credentials)  
  
-   [複数データベースにまたがるクエリ](#cross_database_queries)  
  
-   [データベースの所有権](#database_ownership)  
  
-   [分散クエリおよびリンク サーバー](#distributed_queries_and_linked_servers)  
  
-   [暗号化データ](#encrypted_data)  
  
-   [[ユーザー定義エラー メッセージ]](#user_defined_error_messages)  
  
-   [イベント通知と Windows Management Instrumentation (WMI) イベント (サーバー レベル)](#event_notif_and_wmi_events)  
  
-   [拡張ストアド プロシージャ](#extended_stored_procedures)  
  
-   [Full-Text Engine for SQL Server プロパティ](#ifts_service_properties)  
  
-   [ジョブ](#jobs)  
  
-   [ログイン](#logins)  
  
-   [アクセス許可](#permissions)  
  
-   [レプリケーションの設定](#replication_settings)  
  
-   [Service Broker アプリケーション](#sb_applications)  
  
-   [スタートアップ プロシージャ](#startup_procedures)  
  
-   [トリガー (サーバー レベル)](#triggers)  
  
##  <a name="server_configuration_settings"></a> Server Configuration Settings  
 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] 以降のバージョンでは、主要なサービスや機能のインストールと開始を選択的に行います。 これにより、外部からのアクセスを制限し、攻撃を防ぐことができます。 新規インストール時の既定の構成では、多くの機能が有効化されていません。 データベースが、既定で無効になっているサービスまたは機能に依存している場合、対象のサーバー インスタンスでそのサービスまたは機能を有効にする必要があります。  
  
 これらの設定、およびその有効化と無効化に関する詳細については、「[サーバー構成オプション &#40;SQL Server&#41;](../../database-engine/configure-windows/server-configuration-options-sql-server.md)」を参照してください。  
  
  
##  <a name="credentials"></a> [資格情報]  
 資格情報は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]外部のリソースへの接続に必要な認証情報を含むレコードです。 多くの資格情報は、Windows ログインとパスワードで構成されています。  
  
 この機能の詳細については、「[資格情報 &#40;データベース エンジン&#41;](../../relational-databases/security/authentication-access/credentials-database-engine.md)」を参照してください。  
  
> **注:** [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント プロキシ アカウントでは資格情報を使用します。 プロキシ アカウントの資格情報 ID については、 [sysproxies](../../relational-databases/system-tables/dbo-sysproxies-transact-sql.md) システム テーブルを使用してください。  
  
  
##  <a name="cross_database_queries"></a> Cross-Database Queries  
 DB_CHAINING データベース オプションと TRUSTWORTHY データベース オプションは、既定では OFF になっています。 これらのオプションのいずれかが元のデータベースで ON に設定されていると、対象のサーバー インスタンスのデータベースで、これらの設定を有効にする必要がある場合があります。 詳細については、「[ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql-set-options.md)」を参照してください。  
  
 アタッチおよびデタッチ操作により、複数データベースにまたがる組み合わせ所有権が無効になります。 チェーンを有効にする方法については、「[cross db ownership chaining サーバー構成オプション](../../database-engine/configure-windows/cross-db-ownership-chaining-server-configuration-option.md)」を参照してください。  
  
 詳細については、「[TRUSTWORTHY プロパティを使用するようにミラー データベースを設定する方法 &#40;Transact-SQL&#41;](../../database-engine/database-mirroring/set-up-a-mirror-database-to-use-the-trustworthy-property-transact-sql.md)」も参照してください  
  
  
##  <a name="database_ownership"></a> データベースの所有権  
 データベースを別のコンピューターに復元すると、復元操作を開始した [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログイン ユーザーまたは Windows ユーザーが自動的に新しいデータベースの所有者になります。 復元されたデータベースのシステム管理者または新しいデータベース所有者は、そのデータベースの所有権を変更できます。  
  
##  <a name="distributed_queries_and_linked_servers"></a> 分散クエリおよびリンク サーバー  
 分散クエリおよびリンク サーバーは OLE DB アプリケーションでサポートされます。 分散クエリは、同一コンピューター上または異なるコンピューター上の複数の異種データ ソースのデータにアクセスします。 リンク サーバーを構成すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はリモート サーバー上の OLE DB データ ソースに対してコマンドを実行できます。 詳しくは、「[リンク サーバー &#40;データベース エンジン&#41;](../../relational-databases/linked-servers/linked-servers-database-engine.md)」を参照してください。  
  
  
##  <a name="encrypted_data"></a> 暗号化データ  
 別のサーバー インスタンスで使用できるようにするデータベースに暗号化データが含まれていて、データベース マスター キーが、元のサーバーのサービス マスター キーによって保護されている場合、そのサービス マスター キーを再度暗号化することが必要になる場合があります。 *データベース マスター キー* は対称キーで、証明書の秘密キーや暗号化されたデータベース内の非対称キーを保護するときに使用します。 データベース マスター キーを作成するときには、トリプル DES アルゴリズムとユーザー指定のパスワードを使用してデータベース マスター キーを暗号化します。  
  
 サーバー インスタンスでデータベース マスター キーの暗号化を自動的に解除できるようにするには、サービス マスター キーを使用してデータベース マスター キーのコピーを暗号化します。 この暗号化されたコピーをデータベースと **master**データベースの両方に格納します。 通常、 **master** に格納されたコピーは、マスター キーが変更されるたびに暗黙的に更新されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] により、インスタンスのサービス マスター キーを使用して、データベース マスター キーの暗号化解除が最初に試行されます。 暗号化解除が失敗した場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では資格情報ストア内で、マスター キーが必要なデータベースと同じファミリ GUID のマスター キー資格情報が検索されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、次に、一致した資格情報を順に使用してデータベースのマスター キーの暗号化解除が試行されます。これは暗号化解除が成功するか、資格情報がなくなった時点で終了します。 サービス マスター キーによって暗号化されていないマスター キーは、OPEN MASTER KEY ステートメントとパスワードを使用して開かれている必要があります。  
  
 暗号化されたデータベースがコピー、復元、または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の新しいインスタンスにアタッチされた場合、サービス マスター キーによって暗号化されたデータベース マスター キーのコピーは、対象のサーバー インスタンスの **master** データベースに格納されません。 対象のサーバー インスタンスで、データベースのマスター キーを開く必要があります。 マスター キーを開くには、次のステートメントを実行します:OPEN MASTER KEY DECRYPTION BY PASSWORD **='** _password_ **'** 。 その後、次のステートメントを実行して、データベース マスター キーの自動暗号化解除を有効にすることをお勧めします:ALTER MASTER KEY ADD ENCRYPTION BY SERVICE MASTER KEY。 この ALTER MASTER KEY ステートメントにより、サービス マスター キーを使用して暗号化されたデータベース マスター キーのコピーが対象のサーバー インスタンスに提供されます。 詳細については、「[OPEN MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/open-master-key-transact-sql.md)」と「[ALTER MASTER KEY &#40;Transact-SQL&#41;](../../t-sql/statements/alter-master-key-transact-sql.md)」を参照してください。  
  
 ミラー データベースのデータベース マスター キーの自動暗号化解除を有効にする方法については、「[暗号化されたミラー データベースの設定](../../database-engine/database-mirroring/set-up-an-encrypted-mirror-database.md)」を参照してください。  
  
 詳細については、次のトピックも参照してください。  
  
-   [暗号化階層](../../relational-databases/security/encryption/encryption-hierarchy.md)  
  
-   [暗号化されたミラー データベースの設定](../../database-engine/database-mirroring/set-up-an-encrypted-mirror-database.md)  
  
-   [2 台のサーバーでの同じ対称キーの作成](../../relational-databases/security/encryption/create-identical-symmetric-keys-on-two-servers.md)  
  
  
##  <a name="user_defined_error_messages"></a> User-defined Error Messages  
 ユーザー定義エラー メッセージは、 [sys.messages](../../relational-databases/system-catalog-views/messages-for-errors-catalog-views-sys-messages.md) カタログ ビューに存在します。 このカタログ ビューは、 **master**データベースに格納されています。 データベース アプリケーションがユーザー定義エラー メッセージに依存していて、データベースを別のサーバー インスタンスで使用できる場合は、 [sp_addmessage](../../relational-databases/system-stored-procedures/sp-addmessage-transact-sql.md) を使用してユーザー定義エラー メッセージを対象のサーバー インスタンスに追加できます。  

  
##  <a name="event_notif_and_wmi_events"></a> イベント通知と Windows Management Instrumentation (WMI) イベント (サーバー レベル)  
  
### <a name="server-level-event-notifications"></a>サーバーレベルのイベント通知  
 サーバーレベルのイベント通知は **msdb**に格納されます。 したがって、データベース アプリケーションがサーバーレベルのイベント通知に依存している場合、そのイベント通知を対象のサーバー インスタンスで再作成する必要があります。 サーバー インスタンスでイベント通知を表示するには、 [sys.server_event_notifications](../../relational-databases/system-catalog-views/sys-server-event-notifications-transact-sql.md) カタログ ビューを使用します。 詳しくは、「 [Event Notifications](../../relational-databases/service-broker/event-notifications.md)」をご覧ください。  
  
 また、イベント通知は [!INCLUDE[ssSB](../../includes/sssb-md.md)]を使用して配信されます。 着信メッセージのルートは、サービスを格納したデータベースには含まれていません。 代わりに、明示的なルートが **msdb**に格納されます。 サービスが、サービス宛ての着信メッセージをルーティングするために **msdb** データベース内の明示的なルートを使用する場合、別のインスタンスにデータベースをアタッチするには、このルートを再作成する必要があります。  
  
### <a name="windows-management-instrumentation-wmi-events"></a>Windows Management Instrumentation (WMI) イベント  
 WMI Provider for Server Events を使用すれば、Windows Management Instrumentation (WMI) を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のイベントを監視できます。 データベースが依存する WMI プロバイダーによって公開されているサーバーレベルのイベントに依存するすべてのアプリケーションは、対象のサーバー インスタンスのコンピューターで定義されている必要があります。 WMI イベント プロバイダーは、 **msdb**で定義されている対象サービスでイベント通知を作成します。  
  
> **注:** 詳細については、「 [WMI Provider for Server Events の概念](../../relational-databases/wmi-provider-server-events/wmi-provider-for-server-events-concepts.md)」を参照してください。  
  
 **SQL Server Management Studio を使用して WMI 警告を作成するには**  
  
-   [WMI イベント警告の作成](../../ssms/agent/create-a-wmi-event-alert.md)  
  
### <a name="how-event-notifications-work-for-a-mirrored-database"></a>ミラー化されたデータベースに対するイベント通知の動作のしくみ  
 ミラー化されたデータベースはフェールオーバーできるため、ミラー化されたデータベースに関するイベント通知の複数データベースにまたがる配信は、定義上はリモートで行われます。 [!INCLUDE[ssSB](../../includes/sssb-md.md)] には、 *ミラー化されたルート*の形式で、ミラー化されたデータベース用の特別なサポートが用意されています。 ミラー化されたルートには、プリンシパル サーバー インスタンス用とミラー サーバー インスタンス用の 2 つのアドレスがあります。  
  
 ミラー化されたルートをセットアップすることにより、 [!INCLUDE[ssSB](../../includes/sssb-md.md)] のルーティングでデータベース ミラーリングが認識されるようにします。 ミラー化されたルートを使用すると、 [!INCLUDE[ssSB](../../includes/sssb-md.md)] で現在のプリンシパル サーバー インスタンスに透過的にメッセージ交換をリダイレクトできます。 たとえば、ミラー化されたデータベース Database_A によってホストされているサービス Service_A について考えてみます。 Database_B によってホストされているもう 1 つのサービス Service_B には Service_A との対話が必要であるとします。 この対話を可能にするには、Database_B に Service_A 用のミラー化されたルートを含める必要があります。 さらに、Database_A には Service_B へのミラー化されていない TCP 転送ルートを含める必要があります。このルートはローカル ルートとは異なり、フェールオーバー後も有効なままです。 これらのルートによって、フェールオーバー後に ACK が返されるようになります。 送信側のサービスは常に同じ形式で名前指定されるので、ルートではブローカー インスタンスを指定する必要があります。  
  
 ミラー化されたルートには、ミラー化されたデータベースのサービスが発信側サービスか発信先サービスかにかかわらず、次の要件が適用されます。  
  
-   発信先サービスがミラー化されたデータベースにある場合、発信側サービスに、発信先に戻るためのミラー化されたルートがあること。 ただし、発信先は通常のルートで発信側に戻れること。  
  
-   発信側サービスがミラー化されたデータベースにある場合、発信先に、受信確認や応答の配信のために発信側サービスに戻るミラー化されたルートがあること。 ただし、発信側は通常のルートで発信先に戻れること。  
  
  
##  <a name="extended_stored_procedures"></a> Extended Stored Procedures  
  
> **重要:** [!INCLUDE[ssNoteDepFutureAvoid](../../includes/ssnotedepfutureavoid-md.md)] 代わりに [CLR 統合](../../relational-databases/clr-integration/common-language-runtime-integration-overview.md) を使用してください。  
  
 拡張ストアド プロシージャは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 拡張ストアド プロシージャ API を使用してプログラミングされます。 **sysadmin** 固定サーバー ロールのメンバーは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスを使用して拡張ストアド プロシージャを登録し、ユーザーにその拡張ストアド プロシージャを実行する権限を与えることができます。 拡張ストアド プロシージャは、 **master** データベースにのみ追加できます。  
  
 拡張ストアド プロシージャは [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスのアドレス空間で直接実行されるので、メモリ リークや、サーバーのパフォーマンスと信頼性を低下させるその他の問題が発生する原因になる場合があります。 参照データを含んでいる [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスとは別のインスタンスに拡張ストアド プロシージャを格納することを検討してください。 また、データベースへのアクセスには分散クエリを使用することも検討してください。  
  
  > [!IMPORTANT]
  > システム管理者は、拡張ストアド プロシージャをサーバーに追加して他のユーザーに Execute 権限を許可する前に、各拡張ストアド プロシージャに有害なコードや悪意のあるコードが含まれていないことを十分に確認する必要があります。  
  
 詳細については、「[GRANT (オブジェクトの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-object-permissions-transact-sql.md)」、「[DENY (オブジェクトの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-object-permissions-transact-sql.md)」、および「[REVOKE (オブジェクトの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-object-permissions-transact-sql.md)」を参照してください。  
  
  
##  <a name="ifts_service_properties"></a> Full-Text Engine for SQL Server プロパティ  
 Full-Text Engine のプロパティは、 [sp_fulltext_service](../../relational-databases/system-stored-procedures/sp-fulltext-service-transact-sql.md)によって設定されます。 対象のサーバー インスタンスで、これらのプロパティに必要な設定が行われていることを確認してください。 これらのプロパティの詳細については、「[FULLTEXTSERVICEPROPERTY &#40;Transact-SQL&#41;](../../t-sql/functions/fulltextserviceproperty-transact-sql.md)」を参照してください。  
  
 さらに、[ワード ブレーカーとステマー](../../relational-databases/search/configure-and-manage-word-breakers-and-stemmers-for-search.md)のコンポーネント、または[フルテキスト検索フィルター](../../relational-databases/search/configure-and-manage-filters-for-search.md) コンポーネントのバージョンが、元のサーバー インスタンスと対象のサーバー インスタンスで異なると、フルテキスト インデックスおよびクエリの動作が異なる場合があります。 また、 [類義語辞典](../../relational-databases/search/configure-and-manage-thesaurus-files-for-full-text-search.md) はインスタンス固有のファイルに格納されています。 それらのファイルのコピーを対象のサーバー インスタンスの該当する場所にコピーするか、新しいインスタンス上でこれらのファイルを再作成する必要があります。  
  
> **注:** フルテキスト カタログ ファイルを含む [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] データベースを [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] サーバー インスタンスにアタッチする場合、カタログ ファイルは [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]と同様に他のデータベース ファイルと一緒に以前の場所からアタッチされます。 詳細については、「 [フルテキスト検索のアップグレード](../../relational-databases/search/upgrade-full-text-search.md)」を参照してください。  
  
 詳細については、次のトピックも参照してください。  
  
-   [フルテキスト カタログとフルテキスト インデックスのバックアップおよび復元](../../relational-databases/search/back-up-and-restore-full-text-catalogs-and-indexes.md)  
  
-   [データベース ミラーリングとフルテキスト カタログ &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-and-full-text-catalogs-sql-server.md)  

  
##  <a name="jobs"></a> ジョブ  
 データベースが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント ジョブに依存する場合、対象のサーバー インスタンス上でそれらの SQL Server エージェント ジョブを再作成する必要があります。 ジョブは環境に依存します。 対象のサーバー インスタンス上で既存のジョブを再作成する場合、元のサーバー インスタンス上のジョブの環境に一致するように対象のサーバー インスタンスを変更することが必要になる場合があります。 次の環境的要因は重要です。  
  
-   ジョブによって使用されるログイン  
  
     [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント ジョブを作成または実行するには、まず、ジョブに必要なすべての [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインを対象のサーバー インスタンスに追加する必要があります。 詳細については、「 [SQL Server エージェント ジョブ ステップを作成および管理するユーザーの構成](../../ssms/agent/configure-a-user-to-create-and-manage-sql-server-agent-jobs.md)」を参照してください。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントのサービス開始アカウント  
  
     サービス開始アカウントにより、 [!INCLUDE[msCoName](../../includes/msconame-md.md)] エージェントを実行する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Windows アカウントとそのネットワーク権限が定義されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントは、指定されたユーザー アカウントで実行されます。 SQL Server エージェント サービスのコンテキストは、ジョブとその実行環境の設定に影響します。 アカウントは、ジョブで必要とされるネットワーク共有などのリソースにアクセスできる必要があります。 サービス開始アカウントの選択方法と変更方法の詳細については、「 [SQL Server エージェント サービスのアカウントの選択](../../ssms/agent/select-an-account-for-the-sql-server-agent-service.md)」を参照してください。  
  
     正しく稼働するには、適切なドメイン、ファイル システム、およびレジストリの権限を持つようにサービス開始アカウントを構成する必要があります。 また、サービス アカウント用に構成する必要がある共有ネットワーク リソースがジョブで必要になる場合があります。 詳細については、「 [Windows サービス アカウントと権限の構成](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md)」を参照してください。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の特定のインスタンスに関連付けられている [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]エージェント サービスには独自のレジストリ ハイブが設定されているので、SQL Server エージェント サービスのジョブは、通常、このレジストリ ハイブの 1 つ以上の設定に依存します。 ジョブが適切に動作するには、それらのレジストリ設定が必要です。 スクリプトを使用して別の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービスにジョブを再作成した場合、その SQL Server エージェント サービスのレジストリには、再作成されたジョブに適したレジストリ設定が行われない場合があります。 再作成したジョブを対象のサーバー インスタンスで適切に動作させるには、元の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービスと対象の SQL Server エージェント サービスのレジストリ設定が同じである必要があります。  
  
    > [!CAUTION]  
    >  現在のレジストリ設定が他のジョブで必要な場合、ジョブの再作成を処理するために対象の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービスのレジストリ設定を変更すると、問題が発生する可能性があります。 さらに、レジストリを誤って編集すると、システムに重大な障害が発生する場合があります。 レジストリを変更する前に、コンピューター上の重要なすべてのデータをバックアップすることをお勧めします。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント プロキシ  
  
     [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント プロキシでは、指定されたジョブ ステップのセキュリティ コンテキストを定義します。 ジョブを対象のサーバー インスタンス上で実行する場合、そのジョブに必要なすべてのプロキシをそのインスタンス上で手動で再作成する必要があります。 詳細については、「 [SQL Server エージェント プロキシの作成](../../ssms/agent/create-a-sql-server-agent-proxy.md) 」および「 [プロキシを使用するマルチサーバー ジョブのトラブルシューティング](../../ssms/agent/troubleshoot-multiserver-jobs-that-use-proxies.md)」を参照してください。  
  
 詳細については、次のトピックも参照してください。  
  
-   [ジョブの実装](../../ssms/agent/implement-jobs.md)  
  
-   [役割の交代後のログインとジョブの管理 &#40;SQL Server&#41;](../../sql-server/failover-clusters/management-of-logins-and-jobs-after-role-switching-sql-server.md) (データベース ミラーリングの場合)  
  
-   [Windows サービス アカウントと権限の構成](../../database-engine/configure-windows/configure-windows-service-accounts-and-permissions.md) ([!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスをインストールする場合)  
  
-   [SQL Server エージェントの構成](../../ssms/agent/configure-sql-server-agent.md) ( [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスをインストールする場合)  
  
-   [SQL Server エージェントのセキュリティの実装](../../ssms/agent/implement-sql-server-agent-security.md)  
  
 **既存のジョブと各ジョブのプロパティを表示するには**  
  
-   [ジョブの利用状況の監視](../../ssms/agent/monitor-job-activity.md)  
  
-   [sp_help_job &#40;Transact-SQL&#41;](../../relational-databases/system-stored-procedures/sp-help-job-transact-sql.md)  
  
-   [ジョブ ステップ情報の表示](../../ssms/agent/view-job-step-information.md)  
  
-   [dbo.sysjobs &#40;Transact-SQL&#41;](../../relational-databases/system-tables/dbo-sysjobs-transact-sql.md)  
  
 **ジョブを作成するには**  
  
-   [ジョブの作成](../../ssms/agent/create-a-job.md)  

#### <a name="best-practices-for-using-a-script-to-re-create-a-job"></a>ジョブを再作成するスクリプトを使用する場合の推奨事項  
 まず、簡単なジョブのスクリプトを作成し、別の [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービスでジョブを再作成し、そのジョブを実行して適切に動作するかどうかを確認することをお勧めします。 これにより、互換性のない部分を確認し、それらの解決に取り組むことができます。 スクリプト化したジョブが新しい環境で正常に動作しない場合、新しい環境で正常に動作する同等のジョブを作成することをお勧めします。  
  

##  <a name="logins"></a> ログイン  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスにログインするには、有効な [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインが必要です。 このログインは、プリンシパルが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスに接続できるかどうかを確認する認証プロセスで使用されます。 対応する [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] ログインが未定義のデータベース ユーザー、またはサーバー インスタンスで適切に定義されていないデータベース ユーザーは、インスタンスにログインできません。 このようなユーザーは、そのサーバー インスタンスのデータベースの *孤立ユーザー* と呼ばれます。 データベースを [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]の別のインスタンスに復元、アタッチ、またはコピーした後、データベース ユーザーが孤立する可能性があります。  
  
 元のデータベースのコピーに含まれている一部またはすべてのオブジェクトに対するスクリプトは、SQL Server スクリプト生成ウィザードを使用して、 **[スクリプト オプションの選択]** ページで **[スクリプト ログイン]** オプションを **[True]** に設定することで作成できます。  
  
> **注:** ミラー化されたデータベースのログインを設定する方法については、「[データベース ミラーリングまたは Always On 可用性グループのログイン アカウントの設定 (SQL Server)](../../database-engine/database-mirroring/set-up-login-accounts-database-mirroring-always-on-availability.md)」および「[役割の交代後のログインとジョブの管理 &#40;SQL Server&#41;](../../sql-server/failover-clusters/management-of-logins-and-jobs-after-role-switching-sql-server.md)」をご覧ください。  
  
  
##  <a name="permissions"></a> Permissions  
 次の種類の権限は、データベースが別のサーバー インスタンスで使用できるようになったときに影響を受ける場合があります。  
  
-   システム オブジェクトに対する GRANT 権限、REVOKE 権限、または DENY 権限  
  
-   サーバー インスタンスに対する GRANT 権限、REVOKE 権限、または DENY 権限 (*サーバーレベルの権限*)  
  
### <a name="grant-revoke-and-deny-permissions-on-system-objects"></a>システム オブジェクトに対する GRANT 権限、REVOKE 権限、または DENY 権限  
 ストアド プロシージャ、拡張ストアド プロシージャ、関数、ビューなどのシステム オブジェクトに対する権限は、 **master** データベースに格納されるので、対象のサーバー インスタンスで構成する必要があります。  
  
 元のデータベースのコピーに含まれている一部またはすべてのオブジェクトに対するスクリプトは、SQL Server スクリプト生成ウィザードを使用して、 **[スクリプト オプションの選択]** ページで **[オブジェクトレベル権限のスクリプトを作成]** オプションを **[True]** に設定することで作成できます。  
  
   > [!IMPORTANT]
   > ログインのスクリプトを作成する場合、パスワードはスクリプトに含まれません。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 認証を使用するログインがある場合、対象のサーバー インスタンスでスクリプトを変更する必要があります。  
  
 システム オブジェクトは、 [sys.system_objects](../../relational-databases/system-catalog-views/sys-system-objects-transact-sql.md) カタログ ビューで確認できます。 システム オブジェクトの権限は、**master** データベースの [sys.database_permissions](../../relational-databases/system-catalog-views/sys-database-permissions-transact-sql.md) カタログ ビューで確認できます。 これらのカタログ ビューに対するクエリの実行およびシステム オブジェクト権限の付与に関する詳細については、「[GRANT (システム オブジェクトの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-system-object-permissions-transact-sql.md)」を参照してください。 詳細については、「[REVOKE (サーバーの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-system-object-permissions-transact-sql.md)」および「[DENY (サーバーの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-system-object-permissions-transact-sql.md)」を参照してください。  
  
### <a name="grant-revoke-and-deny-permissions-on-a-server-instance"></a>サーバー インスタンスに対する GRANT 権限、REVOKE 権限、および DENY 権限  
 サーバー スコープの権限は **master** データベースに格納されるので、対象のサーバー インスタンスで構成する必要があります。 サーバー インスタンスのサーバー権限の詳細については [sys.server_permissions](../../relational-databases/system-catalog-views/sys-server-permissions-transact-sql.md) カタログ ビュー、サーバー プリンシパルの詳細については [sys.server_principals](../../relational-databases/system-catalog-views/sys-server-principals-transact-sql.md) カタログ ビュー、サーバー ロールのメンバーシップの詳細については [sys.server_role_members](../../relational-databases/system-catalog-views/sys-server-role-members-transact-sql.md) カタログ ビューに対してクエリを実行してください。  
  
 詳細については、「[GRANT (サーバーの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-server-permissions-transact-sql.md)」、「[REVOKE (サーバーの権限の取り消し) &#40;Transact-SQL&#41;](../../t-sql/statements/revoke-server-permissions-transact-sql.md)」、および「[DENY (サーバーの権限の拒否) &#40;Transact-SQL&#41;](../../t-sql/statements/deny-server-permissions-transact-sql.md)」を参照してください。  
  
#### <a name="server-level-permissions-for-a-certificate-or-asymmetric-key"></a>証明書または非対称キーのサーバー レベルの権限  
 サーバー レベルの権限を証明書または非対称キーに直接与えることはできません。 代わりに、特定の証明書または非対称キー用に排他的に作成された、マップされたログインにサーバー レベルの権限を与えます。 したがって、サーバー レベルの権限が必要な証明書または非対称キーには、独自の *証明書にマップされたログイン* または *非対称キーにマップされたログイン*が個別に必要です。 証明書または非対称キーにサーバー レベルの権限を与えるには、それぞれのマップされるログインに権限を与えます。  
  
> **注:** マップされたログインは、対応する証明書または非対称キーを使用して署名されたコードの承認にのみ使用されます。 マップされたログインを認証に使用することはできません。  
  
 マップされたログインとその権限は、どちらも **master**データベースに存在します。 証明書または非対称キーが **master**以外のデータベースに存在する場合は、 **master** データベースで証明書または非対称キーを再作成して任意のログインにマップする必要があります。 そのデータベースを別のサーバー インスタンスに移動、コピー、または復元する場合は、対象のサーバー インスタンスの **master** データベースに存在する証明書または非対称キーを再作成してログインにマップし、必要なサーバー レベルの権限をそのログインに与える必要があります。  
  
 **証明書または非対称キーを作成するには**  
  
-   [CREATE CERTIFICATE &#40;Transact-SQL&#41;](../../t-sql/statements/create-certificate-transact-sql.md)  
  
-   [CREATE ASYMMETRIC KEY &#40;Transact-SQL&#41;](../../t-sql/statements/create-asymmetric-key-transact-sql.md)  
  
 **ログインに証明書または非対称キーをマップするには**  
  
-   [CREATE LOGIN &#40;Transact-SQL&#41;](../../t-sql/statements/create-login-transact-sql.md)  
  
 **マップされたログインに権限を割り当てるには**  
  
-   [GRANT (サーバーの権限の許可) &#40;Transact-SQL&#41;](../../t-sql/statements/grant-server-permissions-transact-sql.md)  
  
 証明書および非対称キーの詳細については、「 [Encryption Hierarchy](../../relational-databases/security/encryption/encryption-hierarchy.md)」を参照してください。  
  
## <a name="trustworthy-property"></a>TRUSTWORTHY プロパティ
TRUSTWORTHY データベース プロパティを使用して、SQL Server インスタンスがデータベースとその内容を信頼するかどうかを示します。 データベースがアタッチされているとき、既定で、かつ、安全上の理由から、このオプションは OFF に設定されます。このことは、元のサーバーでこのオプションが ON に設定されている場合も当てはまります。 このプロパティの詳細については、「[TRUSTWORTHY データベース プロパティ](../security/trustworthy-database-property.md)」を参照してください。このオプションをオンにする方法については、「[ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)」を参照してください。  


##  <a name="replication_settings"></a> Replication Settings  
 レプリケートされたデータベースのバックアップを別のサーバーまたはデータベースに復元する場合は、レプリケーションの設定は保存できません。 この場合、バックアップの復元後にすべてのパブリケーションおよびサブスクリプションを再作成する必要があります。 この処理を簡単にするには、現在のレプリケーションの設定を行うスクリプトと、レプリケーションの有効化および無効化を行うスクリプトを作成します。 レプリケーションの設定の再作成を容易にするには、これらのスクリプトをコピーし、対象のサーバー インスタンスで動作するようにサーバー名の参照を変更します。  
  
 詳細については、「[レプリケートされたデータベースのバックアップと復元](../../relational-databases/replication/administration/back-up-and-restore-replicated-databases.md)」、「[データベース ミラーリングとレプリケーション &#40;SQL Server&#41;](../../database-engine/database-mirroring/database-mirroring-and-replication-sql-server.md)」、および「[ログ配布とレプリケーション &#40;SQL Server&#41;](../../database-engine/log-shipping/log-shipping-and-replication-sql-server.md)」を参照してください。  
  
  
##  <a name="sb_applications"></a> Service Broker アプリケーション  
 [!INCLUDE[ssSB](../../includes/sssb-md.md)] アプリケーションに関連する多くの要素は、データベースに伴って使用できます。 ただし、アプリケーションの一部の要素は、新しい場所で再作成または再構成する必要があります。  既定で、かつ、安全上の理由から、データベースが別のサーバーからアタッチされているとき、*is_broker_enabled* と *is_honoor_broker_priority_on* のオプションが OFF に設定されます。 これらのオプションを ON に設定する方法については「[ALTER DATABASE &#40;Transact-SQL&#41;](../../t-sql/statements/alter-database-transact-sql.md)」を参照してください。  
  
  
##  <a name="startup_procedures"></a> Startup Procedures  
 スタートアップ プロシージャは、自動的に実行するように設定され、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] を起動するたびに実行されるストアド プロシージャです。 データベースがスタートアップ プロシージャに依存している場合、それらのスタートアップ プロシージャを対象のサーバー インスタンスで定義し、スタートアップ時に自動的に実行されるように構成する必要があります。  

  
##  <a name="triggers"></a> Triggers (at Server Level)  
 DDL トリガーにより、さまざまなデータ定義言語 (DDL) イベントに応答してストアド プロシージャが起動されます。 これらのイベントは、主にキーワード CREATE、ALTER、および DROP で始まる [!INCLUDE[tsql](../../includes/tsql-md.md)] ステートメントに対応します。 DDL と同様の操作を実行する特定のシステム ストアド プロシージャも DDL トリガーを起動できます。  
  
 この機能の詳細については、「 [DDL Triggers](../../relational-databases/triggers/ddl-triggers.md)」を参照してください。  
  
  
## <a name="see-also"></a>参照  
 [包含データベース](../../relational-databases/databases/contained-databases.md)   
 [他のサーバーへのデータベースのコピー](../../relational-databases/databases/copy-databases-to-other-servers.md)   
 [データベースのデタッチとアタッチ &#40;SQL Server&#41;](../../relational-databases/databases/database-detach-and-attach-sql-server.md)   
 [ログ配布のセカンダリへのフェールオーバー &#40;SQL Server&#41;](../../database-engine/log-shipping/fail-over-to-a-log-shipping-secondary-sql-server.md)   
 [データベース ミラーリング セッション中の役割の交代 &#40;SQL Server&#41;](../../database-engine/database-mirroring/role-switching-during-a-database-mirroring-session-sql-server.md)   
 [暗号化されたミラー データベースの設定](../../database-engine/database-mirroring/set-up-an-encrypted-mirror-database.md)   
 [SQL Server 構成マネージャー](../../relational-databases/sql-server-configuration-manager.md)   
 [孤立ユーザーのトラブルシューティング &#40;SQL Server&#41;](../../sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server.md)  
  
  
