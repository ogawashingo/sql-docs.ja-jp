---
title: sp_delete_jobsteplog (Transact-sql) |Microsoft Docs
ms.custom: ''
ms.date: 08/09/2016
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: system-objects
ms.topic: language-reference
f1_keywords:
- sp_delete_jobsteplog
- sp_delete_jobsteplog_TSQL
dev_langs:
- TSQL
helpviewer_keywords:
- sp_delete_jobsteplog
ms.assetid: e9ef4c99-abde-4038-b6a3-a25dcbaf0958
author: stevestein
ms.author: sstein
ms.openlocfilehash: 66b353c7fc79b49cb9cd3fb9fe228075f3a0d473
ms.sourcegitcommit: 43c3d8939f6f7b0ddc493d8e7a643eb7db634535
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2019
ms.locfileid: "72305093"
---
# <a name="sp_delete_jobsteplog-transact-sql"></a>sp_delete_jobsteplog (Transact-sql)
[!INCLUDE[tsql-appliesto-ss2008-xxxx-xxxx-xxx-md](../../includes/tsql-appliesto-ss2008-xxxx-xxxx-xxx-md.md)]

  引数で指定したすべての [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント ジョブ ステップ ログを削除します。 このストアドプロシージャを使用して、 **msdb**データベースの**sysjobstepslogs**テーブルを保持します。  
  
  
 ![トピック リンク アイコン](../../database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文表記規則](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>構文  
  
```  
  
sp_delete_jobsteplog { [ @job_id = ] 'job_id' | [ @job_name = ] 'job_name' }  
       [ , [ @step_id = ] step_id | [ @step_name = ] 'step_name' ]  
       [ , [ @older_than = ] 'date' ]  
       [ , [ @larger_than = ] 'size_in_bytes' ]  
```  
  
## <a name="arguments"></a>引数  
`[ @job_id = ] 'job_id'` 削除するジョブステップのログを含むジョブのジョブ識別番号を指定します。 *job_id*は**int**,、既定値は NULL です。  
  
`[ @job_name = ] 'job_name'` ジョブの名前。 *job_name*は**sysname**,、既定値は NULL です。  
  
> **注:** *Job_id*または*job_name*のいずれかを指定する必要がありますが、両方を指定することはできません。  
  
`[ @step_id = ] step_id` ジョブステップのログを削除するジョブのステップの識別番号を指定します。 含まれていない場合、1older_than または **@no__t**が指定されてい **@no__t**ない限り、ジョブ内のすべてのジョブステップのログが削除されます。 *step_id*は**int**,、既定値は NULL です。  
  
`[ @step_name = ] 'step_name'` ジョブステップのログを削除するジョブ内のステップの名前。 *step_name*は**sysname**,、既定値は NULL です。  
  
> **注:** *Step_id*または*step_name*のいずれかを指定できますが、両方を指定することはできません。  
  
`[ @older_than = ] 'date'` を保持する最も古いジョブステップログの日付と時刻。 この日時より前のジョブ ステップ ログはすべて削除されます。 *日付*は**datetime**,、既定値は NULL です。 1older_than と **@no__t の** **@no__t**両方を指定できます。  
  
`[ @larger_than = ] 'size_in_bytes'` で保持する最大のジョブステップログのサイズ (バイト単位)。 このサイズより大きいすべてのジョブ ステップ ログは、削除されます。 1larger_than と **@no__t の** **@no__t**両方を指定できます。  
  
## <a name="return-code-values"></a>リターン コードの値  
 **0** (成功) または**1** (失敗)  
  
## <a name="result-sets"></a>結果セット  
 なし  
  
## <a name="remarks"></a>コメント  
 **sp_delete_jobsteplog**は**msdb**データベースにあります。  
  
 1job_id または **@no__t**を **@no__t**除く引数が指定されていない場合は、指定されたジョブのすべてのジョブステップログが削除されます。  
  
## <a name="permissions"></a>アクセス許可  
 既定では、このストアド プロシージャを実行できるのは、 **sysadmin** 固定サーバー ロールのメンバーです。 他のユーザーには、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] msdb **データベースの次のいずれかの** エージェント固定データベース ロールが許可されている必要があります。  
  
-   **SQLAgentUserRole**  
  
-   **SQLAgentReaderRole**  
  
-   **SQLAgentOperatorRole**  
  
 これらのロールの権限の詳細については、「 [SQL Server エージェントの固定データベース ロール](../../ssms/agent/sql-server-agent-fixed-database-roles.md)」を参照してください。  
  
 別のユーザーが所有するジョブステップログを削除できるのは、 **sysadmin**のメンバーだけです。  
  
## <a name="examples"></a>使用例  
  
### <a name="a-removing-all-job-step-logs-from-a-job"></a>A. ジョブからすべてのジョブステップログを削除する  
 次の例では、ジョブのすべてのジョブステップログを削除 `Weekly Sales Data Backup` です。  
  
```  
USE msdb ;  
GO  
  
EXEC dbo.sp_delete_jobsteplog  
    @job_name = N'Weekly Sales Data Backup';  
GO  
```  
  
### <a name="b-removing-the-job-step-log-for-a-particular-job-step"></a>B. 特定のジョブステップのジョブステップログを削除する  
 次の例では、ジョブ `Weekly Sales Data Backup` のステップ 2 に対するジョブ ステップ ログを削除します。  
  
```  
USE msdb ;  
GO  
  
EXEC dbo.sp_delete_jobsteplog  
    @job_name = N'Weekly Sales Data Backup',  
    @step_id = 2;  
GO  
```  
  
### <a name="c-removing-all-job-step-logs-based-on-age-and-size"></a>C. 有効期間とサイズに基づいてすべてのジョブステップログを削除する  
 次の例では、2005年10月25日の正午より前のジョブステップログをすべて削除し、ジョブから100メガバイト (MB) を超えています。 `Weekly Sales Data Backup`。  
  
```  
USE msdb ;  
GO  
  
EXEC dbo.sp_delete_jobsteplog  
    @job_name = N'Weekly Sales Data Backup',  
    @older_than = '10/25/2005 12:00:00',  
    @larger_than = 104857600;  
GO  
```  
  
## <a name="see-also"></a>関連項目  
 [sp_help_jobsteplog &#40;transact-sql&#41;](../../relational-databases/system-stored-procedures/sp-help-jobsteplog-transact-sql.md)   
 [ストアドプロシージャ&#40;の SQL Server エージェント transact-sql&#41;](../../relational-databases/system-stored-procedures/sql-server-agent-stored-procedures-transact-sql.md)  
  
  
