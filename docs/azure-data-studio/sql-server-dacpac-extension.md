---
title: SQL Server dacpac の拡張機能
titleSuffix: Azure Data Studio
description: Azure Data Studio 用の SQL Server dacpac の拡張機能 (プレビュー) をインストールして使用する
ms.custom: seodec18
ms.date: 10/21/2019
ms.reviewer: alayu; sstein
ms.prod: sql
ms.technology: azure-data-studio
ms.topic: conceptual
author: yualan
ms.author: alayu
ms.openlocfilehash: 769e6157e7d84702716dfce79d0217efeee83076
ms.sourcegitcommit: a165052c789a327a3a7202872669ce039bd9e495
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72783328"
---
# <a name="sql-server-dacpac-extension-preview"></a>SQL Server dacpac の拡張機能 (プレビュー)

**データ層アプリケーション ウィザード**には、dacpac ファイルの配置と抽出、および bacpac ファイルのインポートとエクスポートを行うための、使いやすいウィザード エクスペリエンスが用意されています。

このエクスペリエンスは、現在、初期のプレビュー段階にあります。 問題と機能に関する要望を[こちら](https://github.com/microsoft/azuredatastudio/issues)にご報告ください。


## <a name="features"></a>[機能]

* dacpac を SQL Server インスタンスに配置する
* SQL Server インスタンスを dacpac に抽出する
* bacpac からデータベースを作成する
* bacpac にスキーマとデータをエクスポートする

![dacpac 拡張機能のデモ gif](media/extensions/sql-server-dacpac-extension/dacpac-extension-demo.gif)


## <a name="why-would-i-use-the-data-tier-application-wizard"></a>データ層アプリケーション ウィザードを使用する理由

このウィザードを使用すると、dacpac および bacpac ファイルを管理しやすくなり、お客様のアプリケーションをサポートするデータ層要素の開発と配置が簡単になります。 データ層アプリケーションの使用方法について詳しくは、[Microsoft のドキュメントを参照してください。](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications?view=sql-server-2017)


## <a name="install-the-extension"></a>拡張機能をインストールする

1. 拡張機能アイコンを選択すると、使用可能な拡張機能が表示されます。

    ![拡張機能マネージャーのアイコン](media/extensions/extension-manager-icon.png)

2. **SQL Server dacpac** の拡張機能を検索し、それを選択して詳細を表示します。 **[インストール]** をクリックして、拡張機能を追加します。

3. インストール後、 **[再度読み込む]** をクリックすると、Azure Data Studio で拡張機能が有効になります (初めて拡張機能をインストールするときにのみ必須)。


## <a name="launch-the-data-tier-application-wizard"></a>データ層アプリケーション ウィザードを起動する

ウィザードを起動するには、[データベース] フォルダーを右クリックするか、オブジェクト エクスプローラーで特定のデータベースを右クリックします。 次に、 **[Data-tier Application Wizard]\(データ層アプリケーション ウィザード\)** をクリックします。

![dacpac 拡張機能の起動メニュー](media/extensions/sql-server-dacpac-extension/dacpac-extension-launch.png)


## <a name="next-steps"></a>次の手順

dacpac について詳しくは、[Microsoft のドキュメントを参照してください。](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications?view=sql-server-2017)