---
title: 可用性グループからのプライマリ データベースの削除 (SQL Server) | Microsoft Docs
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: high-availability
ms.topic: conceptual
f1_keywords:
- sql12.swb.availabilitygroup.removeprimarydb.f1
helpviewer_keywords:
- primary databases [SQL Server], in availability group
- Availability Groups [SQL Server], removing
- Availability Groups [SQL Server], configuring
- Availability Groups [SQL Server], databases
ms.assetid: 6d4ca31e-ddf0-44bf-be5e-a5da060bf096
author: MashaMSFT
ms.author: mathoma
manager: craigg
ms.openlocfilehash: 06b9dac5f9074b335afff7c6b71980618a3020ce
ms.sourcegitcommit: a165052c789a327a3a7202872669ce039bd9e495
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72782869"
---
# <a name="remove-a-primary-database-from-an-availability-group-sql-server"></a>可用性グループからのプライマリ データベースの削除 (SQL Server)
  このトピックでは、[!INCLUDE[ssManStudioFull](../../../includes/ssmanstudiofull-md.md)] の [!INCLUDE[tsql](../../../includes/tsql-md.md)]、[!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)]、または PowerShell を使用して、AlwaysOn 可用性グループからプライマリ データベースおよび対応するセカンダリ データベースの両方を削除する方法について説明します。  
  
-   **作業を開始する準備:**  
  
     [前提条件と制限](#Prerequisites)  
  
     [Security](#Security)  
  
-   **可用性データベースを削除する方法:**  
  
     [SQL Server Management Studio](#SSMSProcedure)  
  
     [Transact-SQL](#TsqlProcedure)  
  
     [PowerShell](#PowerShellProcedure)  
  
-   **補足情報**  [可用性グループから可用性データベースを削除した後](#FollowUp)  
  
##  <a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="Prerequisites"></a> 前提条件と制限  
  
-   このタスクは、プライマリ レプリカ上でのみサポートされます。 プライマリ レプリカをホストするサーバー インスタンスに接続されている必要があります。  
  
###  <a name="Security"></a> Security  
  
####  <a name="Permissions"></a> アクセス許可  
 可用性グループの ALTER AVAILABILITY GROUP 権限、CONTROL AVAILABILITY GROUP 権限、ALTER ANY AVAILABILITY GROUP 権限、または CONTROL SERVER 権限が必要です。  
  
##  <a name="SSMSProcedure"></a> SQL Server Management Studio の使用  
 **可用性データベースを削除するには**  
  
1.  オブジェクト エクスプローラーで、削除するデータベースのプライマリ レプリカをホストするサーバー インスタンスに接続し、サーバー ツリーを展開します。  
  
2.  **[AlwaysOn 高可用性]** ノードと **[可用性グループ]** ノードを展開します。  
  
3.  可用性グループを選択し、 **[可用性データベース]** ノードを展開します。  
  
4.  削除するデータベースが複数であるか 1 つのみであるかによって、次のように実行する手順が異なります。  
  
    -   複数のデータベースを削除するには、 **[オブジェクト エクスプローラーの詳細]** ペインを使用して削除するデータベースを表示し、すべてを選択します。 詳細については、「[[オブジェクト エクスプローラーの詳細] を使用した可用性グループの監視 &#40;SQL Server Management Studio&#41;](use-object-explorer-details-to-monitor-availability-groups.md) を参照してください。  
  
    -   1 つのデータベースを削除するには、 **[オブジェクト エクスプローラー]** ペインまたは **[オブジェクト エクスプローラーの詳細]** ペインでそれを選択します。  
  
5.  選択したデータベースを右クリックし、コマンド メニューの **[可用性グループからデータベースを削除]** を選択します。  
  
6.  **[可用性グループからデータベースを削除]** ダイアログ ボックスで、表示されたすべてのデータベースを削除するには、 **[OK]** をクリックします。 すべて削除しない場合は、 **[キャンセル]** をクリックします。  
  
##  <a name="TsqlProcedure"></a> Transact-SQL の使用  
 **可用性データベースを削除するには**  
  
1.  プライマリ レプリカをホストするサーバー インスタンスに接続します。  
  
2.  [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql) ステートメントを使用します。次にその例を示します。  
  
     ALTER AVAILABILITY GROUP *group_name* REMOVE DATABASE *availability_database_name*  
  
     ここで、 *group_name* は可用性グループの名前、 *database_name* は削除するデータベースの名前です。  
  
     次の例では、 `Db6` というデータベースを `MyAG` 可用性グループから削除します。  
  
    ```sql
    ALTER AVAILABILITY GROUP MyAG REMOVE DATABASE Db6;  
    ```  
  
##  <a name="PowerShellProcedure"></a> PowerShell の使用  
 **可用性データベースを削除するには**  
  
1.  プライマリ レプリカをホストするサーバー インスタンスにディレクトリを変更 (`cd`) します。  
  
2.  可用性グループから削除する可用性データベース名を指定して、`Remove-SqlAvailabilityDatabase` コマンドレットを使用します。 プライマリ レプリカをホストするサーバー インスタンスに接続している場合は、プライマリ データベースおよび対応するセカンダリ データベースがすべて可用性グループから削除されます。  
  
     たとえば、次のコマンドは、可用性データベース `MyDb9` を `MyAg`という名前の可用性グループから削除します。 このコマンドはプライマリ レプリカをホストするサーバー インスタンスで実行されるため、プライマリ データベースおよび対応するすべてのセカンダリ データベースが可用性グループから削除されます。 どのセカンダリ レプリカでも、このデータベースに対してデータ同期は行われなくなります。  
  
    ```powershell
    Remove-SqlAvailabilityDatabase -Path SQLSERVER:\Sql\PrimaryComputer\InstanceName\AvailabilityGroups\MyAg\Databases\MyDb9  
    ```  
  
    > [!NOTE]  
    >  コマンドレットの構文を表示するには、[!INCLUDE[ssCurrent](../../../includes/sscurrent-md.md)] PowerShell 環境で `Get-Help` コマンドレットを使用します。 詳細については、「 [Get Help SQL Server PowerShell](../../../powershell/sql-server-powershell.md)」を参照してください。  
  
 **SQL Server PowerShell プロバイダーを設定して使用するには**  
  
-   [SQL Server PowerShell プロバイダー](../../../powershell/sql-server-powershell-provider.md)  
  
##  <a name="FollowUp"></a> 補足情報: 可用性グループから可用性データベースを削除した後  
 可用性グループから可用性データベースを削除すると、以前のプライマリ データベースおよび対応するセカンダリ データベース間のデータの同期が終了します。 以前のプライマリ データベースはオンラインのまま残ります。 すべての対応するセカンダリ データベースは RESTORING 状態になります。  
  
 この時点で、削除されたセカンダリ データベースを処理する別の方法は次のとおりです。  
  
-   特定のセカンダリ データベースが不要の場合は、削除できます。  
  
     詳細については、「 [データベースの削除](../../../relational-databases/databases/delete-a-database.md)」を参照してください。  
  
-   可用性グループから削除された後に、削除されたセカンダリ データベースにアクセスする必要がある場合は、データベースを復元できます。 ただし、削除されたセカンダリ データベースを復元すると、異なる 2 つのデータベースが同じ名前でオンラインになります。 クライアントからはどちらか一方のデータベース (通常は最新のプライマリ データベース) にしかアクセスできないようにする必要があります。  
  
     詳細については、「[データを復元しないデータベースの復旧 &#40;Transact-SQL&#41;](../../../relational-databases/backup-restore/recover-a-database-without-restoring-data-transact-sql.md)」を参照してください。  
  
## <a name="see-also"></a>「  
 [AlwaysOn 可用性グループ&#40;SQL Server&#41;の概要](overview-of-always-on-availability-groups-sql-server.md)   
 [可用性グループからのセカンダリ データベースの削除 &#40;SQL Server&#41;](remove-a-secondary-database-from-an-availability-group-sql-server.md)  
