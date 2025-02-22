---
title: Reporting Services のアンインストール | Microsoft Docs
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: install
ms.topic: conceptual
ms.assetid: 5c764a00-d4bc-465d-b32e-e4efce052ce4
author: maggiesMSFT
ms.author: maggies
manager: craigg
ms.openlocfilehash: 3909d6adb64b798fa17926620a7e7bd5914bf504
ms.sourcegitcommit: ffe2fa1b22e6040cdbd8544fb5a3083eed3be852
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71952678"
---
# <a name="uninstall-reporting-services"></a>Reporting Services のアンインストール
  [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] をアンインストールすると、作成したコンテンツや変更した構成が削除されます。 ただし、アンインストールの完了後に必要なコンテンツがある場合は、アンインストール プロセスを開始する前に、コンテンツのコピーを作成することをお勧めします。  
  
## <a name="uninstall-sharepoint-mode"></a>SharePoint モードのアンインストール  
 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] SharePoint モードをアンインストールすると、次のものが削除されます。  
  
-   [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] のサービスおよびサービス プロキシ。  
  
-   [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] のインストールに使用するファイル  
  
 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] サービス アプリケーションは削除されません。 サービス アプリケーションが必要なくなった場合は、Windows PowerShell または SharePoint サーバーの全体管理を使用して削除してください。  
  
 レポート アイテムと関連するメタデータは削除されません。 この情報は、 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] サービス アプリケーションに関連するコンテンツ データベースと構成データベースに格納されます。 これらのデータベースは削除されません。SharePoint モードの [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] の別のインストールにデータベースを手動で移行できます。 情報が必要なくなった場合は、データベースを削除してください。 詳細については、「 [Upgrade and Migrate Reporting Services](../../reporting-services/install-windows/upgrade-and-migrate-reporting-services.md)」を参照してください。  
  
 削除されない 3 つの [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] データベースの名前の例を次に示します。  
  
-   **レポート サーバー データベース:** ReportingService_7f616e2d253040e8ab5653b3c09a065e  
  
-   **レポート サーバーの一時データベース:** ReportingService_7f616e2d253040e8ab5653b3c09a065eTempDB  
  
-   **レポート サーバーの警告データベース:** ReportingService_7f616e2d253040e8ab5653b3c09a065e_Alerting  
  
### <a name="uninstall-the-add-in-for-sharepoint-products"></a>SharePoint 製品用のアドインのアンインストール  
 コンピューターからアドインをアンインストールする場合は、ファイルのみをアンインストールするか、ファームから [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] の機能も削除するかを選択できます。 SharePoint 製品用の @no__t 0 アドインのアンインストールの詳細については、「sharepoint [2010 および sharepoint 2013 &#40;&#41;用の Reporting Services アドインのインストールまたはアンインストール](../../reporting-services/install-windows/install-or-uninstall-the-reporting-services-add-in-for-sharepoint.md)」を参照してください。  
  
## <a name="uninstall-native-mode"></a>ネイティブ モードのアンインストール  
 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] ネイティブ モードをアンインストールすると、インストール後に **作成** または **変更** された内容がすべて残ります。 たとえば、データベース ファイル、ログ ファイル、 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 構成ファイル、コンテンツ アイテム (レポートやデータソース ファイルなど) がこれに含まれます。  
  
 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] はインスタンス機能であるため、Windows のコントロール パネルの [プログラムと機能] には表示されません。 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] ネイティブ モードをアンインストールするには  
  
1.  Windows のコントロール パネルで **[プログラムと機能]** をクリックします。  
  
2.  **[プログラムと機能]** で **[Microsoft SQL Server 2012]** を選択します。  
  
3.  アンインストール ウィザードで、 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] インスタンス機能 **RS**を含むインスタンスを選択します。  
  
     ![rs_nativemode_uninstall_selectinstance](../../../2014/sql-server/install/media/rs-nativemode-uninstall-selectinstance.gif "rs_nativemode_uninstall_selectinstance")  
  
4.  インスタンスを選択した後で、 [!INCLUDE[ssRSnoversion](../../includes/ssrsnoversion-md.md)] 機能を選択します。  
  
     ![rs_nativemode_uninstall_selectinstance](../../../2014/sql-server/install/media/rs-nativemode-uninstall-selectfeatures.gif "rs_nativemode_uninstall_selectinstance")  
  
5.  ウィザードを完了します。  
  
## <a name="see-also"></a>関連項目  
 [SQL Server の既存のインスタンスのアンインストール &#40;セットアップ&#41;](../../../2014/sql-server/install/uninstall-an-existing-instance-of-sql-server-setup.md)   
 [PowerPivot for SharePoint &#40;アドイン SharePoint 2013&#41;をインストールまたはアンインストールする](https://docs.microsoft.com/analysis-services/instances/install-windows/install-or-uninstall-the-power-pivot-for-sharepoint-add-in-sharepoint-2013)   
 [SharePoint &#40;2010 および sharepoint 2013 用の Reporting Services アドインのインストールまたはアンインストール&#41;](../../reporting-services/install-windows/install-or-uninstall-the-reporting-services-add-in-for-sharepoint.md)  
  
  
