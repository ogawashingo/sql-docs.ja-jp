---
title: 可用性グループへのセカンダリ レプリカの追加 (SQL Server) | Microsoft Docs
ms.custom: ''
ms.date: 06/14/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: high-availability
ms.topic: conceptual
helpviewer_keywords:
- Availability Groups [SQL Server], availability replicas
- Availability Groups [SQL Server], configuring
ms.assetid: 6669dcce-85f9-495f-aadf-7f62cff4a9da
author: MashaMSFT
ms.author: mathoma
manager: craigg
ms.openlocfilehash: 00cf7a7fab52640cc3fc19a3d9da051d281be7c2
ms.sourcegitcommit: a165052c789a327a3a7202872669ce039bd9e495
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72783010"
---
# <a name="add-a-secondary-replica-to-an-availability-group-sql-server"></a>可用性グループへのセカンダリ レプリカの追加 (SQL Server)
  このトピックでは、 [!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)]で、 [!INCLUDE[tsql](../../../includes/tsql-md.md)]、 [!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]、または PowerShell を使用して、既存の AlwaysOn 可用性グループにセカンダリ レプリカを追加する方法について説明します。  
  
-   **作業を開始する準備:**  
  
     [前提条件と制限](#PrerequisitesRestrictions)  
  
     [Security](#Security)  
  
-   **レプリカを追加するには (次を使用):**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
     [PowerShell](#PowerShellProcedure)  
  
-   **補足情報:**  [セカンダリ レプリカを追加した後](#FollowUp)  
  
## <a name="before-you-begin"></a>作業を開始する準備  
 可用性グループを初めて作成する場合は、あらかじめこのセクションに目を通しておくことを強くお勧めします。  
  
##  <a name="PrerequisitesRestrictions"></a> 前提条件と制限  
  
-   プライマリ レプリカをホストするサーバー インスタンスに接続されている必要があります。  
  
 詳細については、「[AlwaysOn 可用性グループの前提条件、制限事項、および推奨事項 &#40;SQL Server&#41;](prereqs-restrictions-recommendations-always-on-availability.md)」を参照してください。  
  
##  <a name="Security"></a> Security  
  
###  <a name="Permissions"></a> アクセス許可  
 可用性グループの ALTER AVAILABILITY GROUP 権限、CONTROL AVAILABILITY GROUP 権限、ALTER ANY AVAILABILITY GROUP 権限、または CONTROL SERVER 権限が必要です。  
  
##  <a name="SSMSProcedure"></a> SQL Server Management Studio の使用  
 **レプリカを追加するには**  
  
1.  オブジェクト エクスプローラーで、プライマリ レプリカをホストするサーバー インスタンスに接続し、サーバー ツリーを展開します。  
  
2.  **[AlwaysOn 高可用性]** ノードと **[可用性グループ]** ノードを展開します。  
  
3.  可用性グループを右クリックし、次のコマンドのどちらかを選択します。  
  
    -   可用性グループへのレプリカ追加ウィザードを起動するには、 **[レプリカの追加]** をクリックします。 詳細については、「[可用性グループへのレプリカ追加ウィザードの使用 &#40;SQL Server Management Studio&#41;](use-the-add-replica-to-availability-group-wizard-sql-server-management-studio.md)」を参照してください。  
  
    -   または、 **[可用性グループのプロパティ]** ダイアログ ボックスで、 **[プロパティ]** をクリックします。 このダイアログ ボックスでレプリカを追加する手順は以下のとおりです。  
  
        1.  ダイアログ ボックスの **[可用性レプリカ]** ペインで、 **[追加]** をクリックします。 これにより、レプリカのエントリが作成され、空白の [サーバー インスタンス] フィールドが選択された状態になります。  
  
        2.  可用性レプリカをホストするための前提条件を満たしているサーバー インスタンスの名前を入力します。  
  
         さらにレプリカを追加するには、上記の手順を繰り返します。 レプリカの指定を完了したら、 **[OK]** をクリックして操作を完了します。  
  
##  <a name="TsqlProcedure"></a> Transact-SQL の使用  
 **レプリカを追加するには**  
  
1.  プライマリ レプリカをホストする [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] のインスタンスに接続します。  
  
2.  ALTER AVAILABILITY GROUP ステートメントの ADD REPLICA ON 句を使用して、可用性グループに新しいセカンダリ レプリカを追加します。 ADD REPLICA ON 句には、ENDPOINT_URL、AVAILABILITY_MODE、および FAILOVER_MODE オプションが必要です。 他のレプリカ オプション (BACKUP_PRIORITY、SECONDARY_ROLE、PRIMARY_ROLE、SESSION_TIMEOUT) は省略可能です。 詳細については、「 [ALTER AVAILABILITY GROUP &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-availability-group-transact-sql)、または PowerShell を使用して、既存の AlwaysOn 可用性グループにセカンダリ レプリカを追加する方法について説明します。  
  
     たとえば、次の [!INCLUDE[tsql](../../../includes/tsql-md.md)] ステートメントは、 `MyAG` によってホストされるデフォルト サーバー インスタンス (エンドポイント URL が `COMPUTER04`) の `TCP://COMPUTER04.Adventure-Works.com:5022'`という名前の可用性グループに新しいレプリカを作成します。 このレプリカは、手動フェールオーバーと非同期コミット可用性モードをサポートします。  
  
    ```sql
    ALTER AVAILABILITY GROUP MyAG ADD REPLICA ON 'COMPUTER04'   
       WITH (  
             ENDPOINT_URL = 'TCP://COMPUTER04.Adventure-Works.com:5022',  
             AVAILABILITY_MODE = ASYNCHRONOUS_COMMIT,  
             FAILOVER_MODE = MANUAL  
             );  
    ```  
  
##  <a name="PowerShellProcedure"></a> PowerShell の使用  
 **レプリカを追加するには**  
  
1.  プライマリ レプリカをホストするサーバー インスタンスにディレクトリを変更 (`cd`) します。  
  
2.  **New-SqlAvailabilityReplica** コマンドレットを使用します。  
  
     たとえば、次のコマンドは、可用性レプリカを `MyAg`という名前の可用性グループに追加します。 このレプリカは、手動フェールオーバーと非同期コミット可用性モードをサポートします。 セカンダリ ロールでは、このレプリカは読み取りアクセス接続をサポートして、読み取り専用の処理をこのレプリカにオフロードできるようにします。  
  
    ```powershell
    $agPath = "SQLSERVER:\Sql\PrimaryServer\InstanceName\AvailabilityGroups\MyAg"  
    $endpointURL = "TCP://PrimaryServerName.domain.com:5022"  
    $failoverMode = "Manual"  
    $availabilityMode = "AsynchronousCommit"  
    $secondaryReadMode = "AllowAllConnections"  
  
    New-SqlAvailabilityReplica -Name SecondaryServer\Instance `   
    -EndpointUrl $endpointURL `   
    -FailoverMode $failoverMode `   
    -AvailabilityMode $availabilityMode `   
    -ConnectionModeInSecondaryRole $secondaryReadMode `   
    -Path $agPath  
    ```  
  
    > [!NOTE]  
    >  コマンドレットの構文を表示するには、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] PowerShell 環境で `Get-Help` コマンドレットを使用します。 詳細については、「 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)」を参照してください。  
  
 **SQL Server PowerShell プロバイダーを設定して使用するには**  
  
-   [SQL Server PowerShell プロバイダー](../../../powershell/sql-server-powershell-provider.md)  
  
##  <a name="FollowUp"></a> 補足情報: セカンダリ レプリカを追加した後  
 既存の可用性グループのレプリカを追加するには、次の手順を実行する必要があります。  
  
1.  新しいセカンダリ レプリカをホストする予定のサーバー インスタンスに接続します。  
  
2.  新しいセカンダリ レプリカを可用性グループに参加させます。 詳細については、「 [可用性グループへのセカンダリ レプリカの参加 &#40;SQL Server&#41;](join-a-secondary-replica-to-an-availability-group-sql-server.md)、または PowerShell を使用して、既存の AlwaysOn 可用性グループにセカンダリ レプリカを追加する方法について説明します。  
  
3.  可用性グループ内の各データベースについて、セカンダリ レプリカをホストしているサーバー インスタンス上でセカンダリ データベースを作成します。 詳細については、「 [可用性グループに対するセカンダリ データベースの手動準備 &#40;SQL Server&#41;](manually-prepare-a-secondary-database-for-an-availability-group-sql-server.md)で Always On 可用性グループを作成および構成する方法について説明します。  
  
4.  新しいセカンダリ データベースのそれぞれを可用性グループに参加させます。 詳細については、「 [可用性グループへのセカンダリ データベースの参加 &#40;SQL Server&#41;](join-a-secondary-database-to-an-availability-group-sql-server.md)のインスタンスに AlwaysOn 可用性グループを作成する方法について説明します。  
  
##  <a name="RelatedTasks"></a> 関連タスク  
 **可用性レプリカを管理するには**  
  
-   [可用性グループへのセカンダリ レプリカの参加 &#40;SQL Server&#41;](join-a-secondary-replica-to-an-availability-group-sql-server.md)  
  
-   [可用性グループからのセカンダリ レプリカの削除 &#40;SQL Server&#41;](remove-a-secondary-replica-from-an-availability-group-sql-server.md)  
  
-   [可用性レプリカでの読み取り専用アクセスの構成 &#40;SQL Server&#41;](configure-read-only-access-on-an-availability-replica-sql-server.md)  
  
-   [可用性レプリカの可用性モードの変更 &#40;SQL Server&#41;](change-the-availability-mode-of-an-availability-replica-sql-server.md)  
  
-   [可用性レプリカのフェールオーバー モードの変更 &#40;SQL Server&#41;](change-the-failover-mode-of-an-availability-replica-sql-server.md)  
  
-   [可用性レプリカのセッション タイムアウト期間の変更 &#40;SQL Server&#41;](change-the-session-timeout-period-for-an-availability-replica-sql-server.md)  
  
-   [可用性レプリカのセッション タイムアウト期間の変更 &#40;SQL Server&#41;](change-the-session-timeout-period-for-an-availability-replica-sql-server.md)  
  
## <a name="see-also"></a>「  
 [ALTER AVAILABILITY GROUP &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-availability-group-transact-sql)   
 [AlwaysOn 可用性グループ&#40;SQL Server&#41;の概要](overview-of-always-on-availability-groups-sql-server.md)   
 [可用性グループの作成と構成 &#40;SQL Server&#41;](creation-and-configuration-of-availability-groups-sql-server.md)   
 [AlwaysOn ダッシュボード&#40;SQL Server Management Studio&#41;を使用する](use-the-always-on-dashboard-sql-server-management-studio.md)   
 [可用性グループの監視 &#40;Transact-SQL&#41;](monitor-availability-groups-transact-sql.md)  
  
  
