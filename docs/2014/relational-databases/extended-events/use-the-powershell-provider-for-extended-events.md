---
title: 拡張イベントへの PowerShell プロバイダーの使用 | Microsoft Docs
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: xevents
ms.topic: conceptual
helpviewer_keywords:
- PowerShell [SQL Server], xevent
- extended events [SQL Server], PowerShell
- PowerShell [SQL Server], extended events
ms.assetid: 0b10016f-a479-4444-a484-46cb4677cf64
author: MightyPen
ms.author: genemi
manager: craigg
ms.openlocfilehash: ea4432b07007ce1bbc4ec5b944594b204a7ad808
ms.sourcegitcommit: a165052c789a327a3a7202872669ce039bd9e495
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72782907"
---
# <a name="use-the-powershell-provider-for-extended-events"></a>拡張イベントへの PowerShell プロバイダーの使用
  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] PowerShell プロバイダーを使用して、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 拡張イベントを管理できます。 XEvent サブフォルダーは、SQLSERVER ドライブで利用可能です。 このフォルダーには、次のいずれかの方法でアクセスできます。  
  
-   コマンドプロンプトで、「`sqlps`」と入力し、enter キーを押します。 「`cd xevent`」と入力して Enter キーを押します。 そこから、 **cd**と `dir` コマンド **(または** **get-childitem**コマンドレット) を使用して、サーバー名とインスタンス名に移動できます。  
  
-   オブジェクト エクスプローラーで、インスタンス名、 **[管理]** の順に展開し、 **[拡張イベント]** を右クリックして、 **[PowerShell の起動]** をクリックします。 これにより、次のパスで PowerShell が起動します。  
  
     PS SQLSERVER:\XEvent\\*ServerName*\\*InstanceName*>  
  
    > [!NOTE]  
    >  PowerShell は、 **[拡張イベント]** の下の任意のノードから起動できます。 たとえば、 **[Sessions]** を右クリックし、 **[PowerShell の起動]** をクリックできます。 これにより、1 レベル深い Sessions フォルダーで PowerShell が起動します。  
  
 XEvent フォルダー ツリーを参照して、既存の拡張イベント セッションと、関連するイベント、ターゲット、および述語を表示できます。 たとえば、PS SQLSERVER: \ XEvent \\*ServerName* \\*InstanceName*> パスで、「`cd sessions`」と入力し、enter キーを押して「`dir`」と入力し、enter キーを押すと、そのインスタンスに格納されているセッションの一覧が表示されます。 また、セッションが実行中かどうか (および実行中の場合は実行時間)、およびセッションがインスタンスの起動時に開始されるように構成されているかどうかも表示されます。  
  
 セッションに関連付けられているイベント、述語、およびターゲットを表示するには、ディレクトリをセッション名に変更してから、events フォルダーまたは targets フォルダーを表示します。 たとえば、既定のシステム正常性セッションに関連付けられているイベントとその述語を表示するには、PS SQLSERVER:\ XEvent \\*ServerName* \\*InstanceName*\ Sessions > パスで、「」と入力 `cd system_health\events,` enter キー @no を押します。__ 5 を入力し、enter キーを押します。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] PowerShell プロバイダーは、拡張イベント セッションの作成、変更、および管理に使用できる強力なツールです。 次のセクションでは、拡張イベントに PowerShell スクリプトを使用する基本的な例をいくつか紹介します。  
  
## <a name="examples"></a>使用例  
 以下の例では、次の点に注意してください。  
  
-   スクリプトは、PS SQLSERVER: \\ > プロンプトから実行する必要があります (コマンドプロンプトで「`sqlps`」と入力すると使用できます)。  
  
-   スクリプトでは、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の既定のインスタンスを使用します。  
  
-   スクリプトは、.ps1 拡張子を付けて保存してください。  
  
-   PowerShell 実行ポリシーで、スクリプトを実行できるようにする必要があります。 実行ポリシーを設定するには、 **Set-Executionpolicy** コマンドレットを使用します (詳細については、「`get-help set-executionpolicy -detailed`」と入力し、Enter キーを押します)。  
  
 次のスクリプトでは、"TestSession" という名前の新しいセッションが作成されます。  
  
```powershell
#Script for creating a session.  
cd XEvent  
$h = hostname  
cd $h  
  
#Use the default instance.  
$store = dir | where {$_.DisplayName -ieq 'default'}  
$session = New-Object Microsoft.SqlServer.Management.XEvent.Session -ArgumentList $store, "TestSession"  
$event = $session.AddEvent("sqlserver.file_written")  
$event.AddAction("package0.callstack")  
$session.Create()  
```  
  
 次のスクリプトでは、前の例で作成したセッションにリング バッファー ターゲットが追加されます (この例では、`Alter` メソッドの使用方法を示しています。 ターゲットは、最初にセッションを作成するときに追加できます)。  
  
```powershell
#Script to alter a session.  
cd XEvent  
$h = hostname  
cd $h  
cd DEFAULT\Sessions  
  
#Used to find the specified session.  
$session = dir | where {$_.Name -eq 'TestSession'}  
  
#Add the ring buffer target and call the Alter method.  
$session.AddTarget("package0.ring_buffer")  
$session.Alter()  
```  
  
 次のスクリプトでは、述語式を使用する新しいセッションが作成されます。 この例では、sqlserver.file_written イベントを利用して、c:\temp.log ファイルに書き込まれたときの情報を収集します。  
  
```powershell
#Script for creating a session.  
cd XEvent  
$h = hostname  
cd $h  
  
#Use the default instance.  
$store = dir | where {$_.DisplayName -ieq 'default'}  
$session = New-Object Microsoft.SqlServer.Management.XEvent.Session -ArgumentList $store, "TestSession2"  
$event = $session.AddEvent("sqlserver.file_written")  
  
#Construct a predicate "equal_i_unicode_string(path, N'c:\temp.log')".  
$column = $store.SqlServerPackage.EventInfoSet["file_written"].DataEventColumnInfoSet["path"]  
$operand = new-object Microsoft.SqlServer.Management.XEvent.PredOperand -argumentlist $column  
$value = new-object Microsoft.SqlServer.Management.XEvent.PredValue -argumentlist "c:\temp.log"  
$compare = $store.Package0Package.PredCompareInfoSet["equal_i_unicode_string"]  
$predicate = new-object Microsoft.SqlServer.Management.XEvent.PredFunctionExpr -ArgumentList $compare, $operand, $value  
$event.SetPredicate($predicate)  
$session.Create()  
```  
  
## <a name="security"></a>セキュリティ  
 拡張イベント セッションを作成、変更、または削除するには、ALTER ANY EVENT SESSION 権限が必要です。  
  
## <a name="see-also"></a>「  
 [SQL Server PowerShell](../../powershell/sql-server-powershell.md)   
 [system_health セッションの使用](use-the-ssms-xe-profiler.md)   
 [拡張イベントのツール](extended-events-tools.md)  
