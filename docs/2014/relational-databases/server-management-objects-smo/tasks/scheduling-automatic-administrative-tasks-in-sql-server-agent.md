---
title: SQL Server エージェントでの自動管理タスクのスケジュール設定 |Microsoft Docs
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: ''
ms.topic: reference
helpviewer_keywords:
- scheduling administrative tasks [SMO]
- SQL Server Agent [SMO]
- automatic administrative SMO tasks
ms.assetid: 900242ad-d6a2-48e9-8a1b-f0eea4413c16
author: stevestein
ms.author: sstein
manager: craigg
ms.openlocfilehash: 715a60d4a6f4a138a71e7de6095234a9e0d2a461
ms.sourcegitcommit: a165052c789a327a3a7202872669ce039bd9e495
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72782209"
---
# <a name="scheduling-automatic-administrative-tasks-in-sql-server-agent"></a>SQL Server エージェントでの自動管理タスクのスケジュール設定
  SMO では、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] エージェントは次のオブジェクトで表現されます。  
  
-   <xref:Microsoft.SqlServer.Management.Smo.Agent.JobServer> オブジェクトには、ジョブ、警告、オペレーターの 3 つのコレクションがあります。  
  
-   <xref:Microsoft.SqlServer.Management.Smo.Agent.OperatorCollection> オブジェクトは、Microsoft [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] エージェントによって自動的にイベントの通知を行える、ポケットベル、電子メール アドレス、およびネット送信オペレーターのリストを表します。  
  
-   <xref:Microsoft.SqlServer.Management.Smo.Agent.AlertCollection> オブジェクトは、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] によって監視される、システム イベントやパフォーマンス条件などの状況のリストを表します。  
  
-   <xref:Microsoft.SqlServer.Management.Smo.Agent.JobCollection> オブジェクトは、やや複雑です。 このオブジェクトは、指定されたスケジュールで実行されるマルチステップ タスクのリストを表します。 ステップおよびスケジュール情報は、<xref:Microsoft.SqlServer.Management.Smo.Agent.JobStep> オブジェクトおよび <xref:Microsoft.SqlServer.Management.Smo.Agent.JobSchedule> オブジェクトに格納されます。  
  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] エージェント オブジェクトは、<xref:Microsoft.SqlServer.Management.Smo.Agent> 名前空間にあります。  
  
## <a name="examples"></a>使用例  
 提供されているコード例を使用するには、アプリケーションを作成するプログラミング環境、プログラミング テンプレート、およびプログラミング言語を選択する必要があります。 詳細については、「 [Visual studio .net での VISUAL BASIC SMO プロジェクトの作成](../../../database-engine/dev-guide/create-a-visual-basic-smo-project-in-visual-studio-net.md)」または「visual [studio .Net での Visual C&#35; SMO プロジェクトの作成](../how-to-create-a-visual-csharp-smo-project-in-visual-studio-net.md)」を参照してください。  
  
1.  プログラムで [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] エージェントを使用する場合、エージェント名前空間を修飾する `Imports` ステートメントを含める必要があります。 アプリケーションの宣言の前、かつ他の `Imports` ステートメントの後に、次のようにステートメントを挿入します。  
  
 `Imports Microsoft.SqlServer.Management.Smo`  
  
 `Imports Microsoft.SqlServer.Management.Common`  
  
 `Imports Microsoft.SqlServer.Management.Smo.Agent`  
  
## <a name="creating-a-job-with-steps-and-a-schedule-in-visual-basic"></a>Visual Basic でのステップを持つジョブとスケジュールの作成  
 このコード例では、ステップを持つジョブとスケジュールを作成し、オペレーターへの通知を行います。  
  
<!-- TODO: review snippet reference  [!CODE [SMO How to#SMO_VBAgent1](SMO How to#SMO_VBAgent1)]  -->  
  
## <a name="creating-a-job-with-steps-and-a-schedule-in-visual-c"></a>Visual C# でのステップを持つジョブとスケジュールの作成  
 このコード例では、ステップを持つジョブとスケジュールを作成し、オペレーターへの通知を行います。  
  
```csharp
{  
            //Connect to the local, default instance of SQL Server.  
            Server srv = new Server();  
  
            //Define an Operator object variable by supplying the Agent (parent JobServer object) and the name in the constructor.   
            Operator op = new Operator(srv.JobServer, "Test_Operator");  
  
            //Set the Net send address.   
            op.NetSendAddress = "Network1_PC";  
  
            //Create the operator on the instance of SQL Server Agent.   
            op.Create();  
  
            //Define a Job object variable by supplying the Agent and the name arguments in the constructor and setting properties.   
            Job jb = new Job(srv.JobServer, "Test_Job");  
  
            //Specify which operator to inform and the completion action.   
            jb.OperatorToNetSend = "Test_Operator";  
            jb.NetSendLevel = CompletionAction.Always;  
  
            //Create the job on the instance of SQL Server Agent.   
            jb.Create();  
  
            //Define a JobStep object variable by supplying the parent job and name arguments in the constructor.   
            JobStep jbstp = new JobStep(jb, "Test_Job_Step");  
            jbstp.Command = "Test_StoredProc";  
            jbstp.OnSuccessAction = StepCompletionAction.QuitWithSuccess;  
            jbstp.OnFailAction = StepCompletionAction.QuitWithFailure;  
  
            //Create the job step on the instance of SQL Agent.   
            jbstp.Create();  
  
            //Define a JobSchedule object variable by supplying the parent job and name arguments in the constructor.   
  
            JobSchedule jbsch = new JobSchedule(jb, "Test_Job_Schedule");  
  
            //Set properties to define the schedule frequency, and duration.   
            jbsch.FrequencyTypes = FrequencyTypes.Daily;  
            jbsch.FrequencySubDayTypes = FrequencySubDayTypes.Minute;  
            jbsch.FrequencySubDayInterval = 30;  
            TimeSpan ts1 = new TimeSpan(9, 0, 0);  
            jbsch.ActiveStartTimeOfDay = ts1;  
  
            TimeSpan ts2 = new TimeSpan(17, 0, 0);  
            jbsch.ActiveEndTimeOfDay = ts2;  
            jbsch.FrequencyInterval = 1;  
  
            System.DateTime d = new System.DateTime(2003, 1, 1);  
            jbsch.ActiveStartDate = d;  
  
            //Create the job schedule on the instance of SQL Agent.   
            jbsch.Create();  
        }  
```  
  
## <a name="creating-a-job-with-steps-and-a-schedule-in-powershell"></a>PowerShell でのステップを持つジョブとスケジュールの作成  
 このコード例では、ステップを持つジョブとスケジュールを作成し、オペレーターへの通知を行います。  
  
```powershell
#Get a server object which corresponds to the default instance  
$srv = New-Object -TypeName Microsoft.SqlServer.Management.SMO.Server  
  
#Define an Operator object variable by supplying the Agent (parent JobServer object) and the name in the constructor.  
$op = New-Object -TypeName Microsoft.SqlServer.Management.SMO.Agent.Operator -argumentlist $srv.JobServer, "Test_Operator"  
  
#Set the Net send address.  
$op.NetSendAddress = "Network1_PC"  
  
#Create the operator on the instance of SQL Agent.  
$op.Create()  
  
#Define a Job object variable by supplying the Agent and the name arguments in the constructor and setting properties.   
$jb = New-Object -TypeName Microsoft.SqlServer.Management.SMO.Agent.Job -argumentlist $srv.JobServer, "Test_Job"   
  
#Specify which operator to inform and the completion action.   
$jb.OperatorToNetSend = "Test_Operator";   
$jb.NetSendLevel = [Microsoft.SqlServer.Management.SMO.Agent.CompletionAction]::Always  
  
#Create the job on the instance of SQL Server Agent.   
$jb.Create()  
  
#Define a JobStep object variable by supplying the parent job and name arguments in the constructor.   
$jbstp = New-Object -TypeName Microsoft.SqlServer.Management.SMO.Agent.JobStep -argumentlist $jb, "Test_Job_Step"   
$jbstp.Command = "Test_StoredProc";   
$jbstp.OnSuccessAction = [Microsoft.SqlServer.Management.SMO.Agent.StepCompletionAction]::QuitWithSuccess;   
$jbstp.OnFailAction =[Microsoft.SqlServer.Management.SMO.Agent.StepCompletionAction]::QuitWithFailure;   
  
#Create the job step on the instance of SQL Agent.   
$jbstp.Create();   
  
#Define a JobSchedule object variable by supplying the parent job and name arguments in the constructor.   
$jbsch = New-Object -TypeName Microsoft.SqlServer.Management.SMO.Agent.JobSchedule -argumentlist $jb, "Test_Job_Schedule"   
  
#Set properties to define the schedule frequency, and duration.   
$jbsch.FrequencyTypes =  [Microsoft.SqlServer.Management.SMO.Agent.FrequencyTypes]::Daily  
  
$jbsch.FrequencySubDayTypes = [Microsoft.SqlServer.Management.SMO.Agent.FrequencySubDayTypes]::Minute  
$jbsch.FrequencySubDayInterval = 30  
$ts1 =  New-Object -TypeName TimeSpan -argumentlist 9, 0, 0  
$jbsch.ActiveStartTimeOfDay = $ts1  
$ts2 = New-Object -TypeName TimeSpan -argumentlist 17, 0, 0  
$jbsch.ActiveEndTimeOfDay = $ts2  
$jbsch.FrequencyInterval = 1  
$jbsch.ActiveStartDate = "01/01/2003"  
  
#Create the job schedule on the instance of SQL Agent.   
$jbsch.Create();  
```  
  
## <a name="creating-an-alert-in-visual-basic"></a>Visual Basic での警告の作成  
 このコード例では、パフォーマンス条件によってトリガーされる警告を作成しています。 条件は、次の形式で指定する必要があります。  
  
 **ObjectName |CounterName |Instance |ComparisionOp |CompValue**  
  
 警告の通知にはオペレーターが必要です。 `operator` は Visual Basic キーワードであるため、<xref:Microsoft.SqlServer.Management.Smo.Agent.Operator> 型には角かっこが必要です。  
  
<!-- TODO: review snippet reference  [!CODE [SMO How to#SMO_VBAgent3](SMO How to#SMO_VBAgent3)]  -->  
  
## <a name="creating-an-alert-in-visual-c"></a>Visual C# での警告の作成  
 このコード例では、パフォーマンス条件によってトリガーされる警告を作成しています。 条件は、次の形式で指定する必要があります。  
  
 **ObjectName |CounterName |Instance |ComparisionOp |CompValue**  
  
 警告の通知にはオペレーターが必要です。 `operator` は [!INCLUDE[csprcs](../../../includes/csprcs-md.md)] キーワードであるため、<xref:Microsoft.SqlServer.Management.Smo.Agent.Operator> 型には角かっこが必要です。  
  
```csharp
{  
             //Connect to the local, default instance of SQL Server.   
            Server srv = new Server();  
  
            //Define an Alert object variable by supplying the SQL Server Agent and the name arguments in the constructor.   
            Alert al = new Alert(srv.JobServer, "Test_Alert");  
  
            //Specify the performance condition string to define the alert.   
            al.PerformanceCondition = "SQLServer:General Statistics|User Connections||>|3";  
  
            //Create the alert on the SQL Agent.   
            al.Create();  
  
            //Define an Operator object variable by supplying the SQL Server Agent and the name arguments in the constructor.   
  
            Operator op = new Operator(srv.JobServer, "Test_Operator");  
            //Set the net send address.   
            op.NetSendAddress = "NetworkPC";  
            //Create the operator on the SQL Agent.   
            op.Create();  
            //Run the AddNotification method to specify the operator is notified when the alert is raised.   
            al.AddNotification("Test_Operator", NotifyMethods.NetSend);  
        }  
```  
  
## <a name="creating-an-alert-in-powershell"></a>PowerShell での警告の作成  
 このコード例では、パフォーマンス条件によってトリガーされる警告を作成しています。 条件は、次の形式で指定する必要があります。  
  
 **ObjectName |CounterName |Instance |ComparisionOp |CompValue**  
  
 警告の通知にはオペレーターが必要です。 `operator` は [!INCLUDE[csprcs](../../../includes/csprcs-md.md)] キーワードであるため、<xref:Microsoft.SqlServer.Management.Smo.Agent.Operator> 型には角かっこが必要です。  
  
```powershell
#Get a server object which corresponds to the default instance  
$srv = New-Object -TypeName Microsoft.SqlServer.Management.SMO.Server  
  
#Define an Alert object variable by supplying the SQL Agent and the name arguments in the constructor.  
$al = New-Object -TypeName Microsoft.SqlServer.Management.SMO.Agent.Alert -argumentlist $srv.JobServer, "Test_Alert"  
  
#Specify the performance condition string to define the alert.  
$al.PerformanceCondition = "SQLServer:General Statistics|User Connections||>|3"  
  
#Create the alert on the SQL Agent.  
$al.Create()  
  
#Define an Operator object variable by supplying the Agent (parent JobServer object) and the name in the constructor.  
$op = New-Object -TypeName Microsoft.SqlServer.Management.SMO.Agent.Operator -argumentlist $srv.JobServer, "Test_Operator"  
  
#Set the Net send address.  
$op.NetSendAddress = "Network1_PC"  
  
#Create the operator on the instance of SQL Agent.  
$op.Create()  
  
#Run the AddNotification method to specify the operator is notified when the alert is raised.  
$ns = [Microsoft.SqlServer.Management.SMO.Agent.NotifyMethods]::NetSend  
$al.AddNotification("Test_Operator", $ns)  
  
#Drop the alert and the operator  
$al.Drop()  
$op.Drop()  
```  
  
## <a name="allowing-user-access-to-subsystem-by-using-a-proxy-account-in-visual-basic"></a>Visual Basic でプロキシ アカウントを使用することでユーザーがサブシステムにアクセスできるようにする  
 このコード例では、<xref:Microsoft.SqlServer.Management.Smo.Agent.ProxyAccount.AddSubSystem%2A> オブジェクトの <xref:Microsoft.SqlServer.Management.Smo.Agent.ProxyAccount> メソッドを使用して、ユーザーが指定されたサブシステムにアクセスできるようにする方法を示します。  
  
<!-- TODO: review snippet reference  [!CODE [SMO How to#SMO_VBAgent2](SMO How to#SMO_VBAgent2)]  -->  
  
## <a name="allowing-user-access-to-subsystem-by-using-a-proxy-account-in-visual-c"></a>Visual C# でプロキシ アカウントを使用することでユーザーがサブシステムにアクセスできるようにする  
 このコード例では、<xref:Microsoft.SqlServer.Management.Smo.Agent.ProxyAccount.AddSubSystem%2A> オブジェクトの <xref:Microsoft.SqlServer.Management.Smo.Agent.ProxyAccount> メソッドを使用して、ユーザーが指定されたサブシステムにアクセスできるようにする方法を示します。  
  
```csharp
//Connect to the local, default instance of SQL Server.   
{   
Server srv = default(Server);   
srv = new Server();   
//Declare a JobServer object variable and reference the SQL Server Agent.   
JobServer js = default(JobServer);   
js = srv.JobServer;   
//Define a Credential object variable by supplying the parent server and name arguments in the constructor.   
Credential c = default(Credential);   
c = new Credential(srv, "Proxy_accnt");   
//Set the identity to a valid login represented by the vIdentity string variable.   
//The sub system will run under this login.   
c.Identity = vIdentity;   
//Create the credential on the instance of SQL Server.   
c.Create();   
//Define a ProxyAccount object variable by supplying the SQL Server Agent, the name, the credential, the description arguments in the constructor.   
ProxyAccount pa = default(ProxyAccount);   
pa = new ProxyAccount(js, "Test_proxy", "Proxy_accnt", true, "Proxy account for users to run job steps in command shell.");   
//Create the proxy account on the SQL Agent.   
pa.Create();   
//Add the login, represented by the vLogin string variable, to the proxy account.   
pa.AddLogin(vLogin);   
//Add the CmdExec subsytem to the proxy account.   
pa.AddSubSystem(AgentSubSystem.CmdExec);   
}   
//Now users logged on as vLogin can run CmdExec job steps with the specified credentials.   
```  
  
## <a name="see-also"></a>「  
 [SQL Server エージェント](../../../ssms/agent/sql-server-agent.md)   
 [ジョブの実装](../../../ssms/agent/implement-jobs.md)  
