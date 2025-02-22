---
title: 環境変数を使って SQL Server の設定を構成する
description: この記事では、Linux 上で、環境変数を使って SQL Server 2017 の特定の設定を構成する方法について説明します。
author: VanMSFT
ms.author: vanto
ms.date: 07/24/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: linux
ms.assetid: ''
ms.openlocfilehash: 019878cd1dde4425ba69e0c69ce7a4a4a1d0dd62
ms.sourcegitcommit: f912c101d2939084c4ea2e9881eb98e1afa29dad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72807469"
---
# <a name="configure-sql-server-settings-with-environment-variables-on-linux"></a>Linux 上で環境変数を使って SQL Server の設定を構成する

[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-linuxonly](../includes/appliesto-ss-xxxx-xxxx-xxx-md-linuxonly.md)]

<!--SQL Server 2017 on Linux-->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

Linux では、いくつかの異なる環境変数を使って SQL Server 2017 を構成することができます。 このような変数は、次の 2 つのシナリオで使用されます。

::: moniker-end
<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 || =sqlallproducts-allversions"

Linux では、いくつかの異なる環境変数を使って SQL Server 2019 プレビューを構成することができます。 このような変数は、次の 2 つのシナリオで使用されます。

::: moniker-end

- `mssql-conf setup` コマンドを使って初期セットアップを構成する。
- 新しい [Docker の SQL Server コンテナー](quickstart-install-connect-docker.md)を構成する。

> [!TIP]
> これらのセットアップ シナリオの後に SQL Server を構成する必要がある場合は、「[mssql-conf ツールを使用して SQL Server on Linux を構成する](sql-server-linux-configure-mssql-conf.md)」をご覧ください。

## <a name="environment-variables"></a>環境変数

<!--SQL Server 2017 on Linux-->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

| 環境変数 | [説明] |
|-----|-----|
| **ACCEPT_EULA** | **ACCEPT_EULA** 変数を任意の値に設定し、[使用許諾契約書](https://go.microsoft.com/fwlink/?LinkId=746388)の承諾を確定します。 SQL Server イメージの設定が必要です。 |
| **MSSQL_SA_PASSWORD** | SA ユーザーのパスワードを構成します。 |
| **MSSQL_PID** | SQL Server のエディションまたはプロダクト キーを設定します。 有効な値は次のとおりです。 </br></br>**Evaluation**</br>**開発者**</br>**Express**</br>**Web**</br>**Standard**</br>**Enterprise**</br>**プロダクト キー**</br></br>プロダクト キーを指定する場合は、#####-#####-#####-#####-##### という形式にする必要があります。ここで、'#' は数字または文字を表します。|
| **MSSQL_LCID** | SQL Server 用に使う言語 ID を設定します。 たとえば、1036 はフランス語です。 |
| **MSSQL_COLLATION** | SQL Server の既定の照合順序を設定します。 これにより、言語 ID (LCID) から照合順序への既定のマッピングがオーバーライドされます。 |
| **MSSQL_MEMORY_LIMIT_MB** | SQL Server が使用できるメモリの最大容量 (MB 単位) を設定します。 既定では、これは物理メモリの合計容量の 80% です。 |
| **MSSQL_TCP_PORT** | SQL Server がリッスンする TCP ポートを構成します (既定値は 1433)。 |
| **MSSQL_IP_ADDRESS** | IP アドレスを設定します。 現在、IP アドレスは IPv4 形式 (0.0.0.0) である必要があります。 |
| **MSSQL_BACKUP_DIR** | 既定のバックアップ ディレクトリの場所を設定します。 |
| **MSSQL_DATA_DIR** | 新しい SQL Server データベースのデータ ファイル (.mdf) が作成されるディレクトリを変更します。 |
| **MSSQL_LOG_DIR** | 新しい SQL Server データベースのログ (.ldf) ファイルが作成されるディレクトリを変更します。 |
| **MSSQL_DUMP_DIR** | SQL Server が、メモリ ダンプやその他のトラブルシューティング ファイルを格納する既定のディレクトリを変更します。 |
| **MSSQL_ENABLE_HADR** | 可用性グループを有効にします。 たとえば、'1' は有効、'0' は無効です |
| **MSSQL_AGENT_ENABLED** | SQL Server エージェントを有効にします。 たとえば、'true' は有効、'false' は無効です。 既定では、エージェントは無効になっています。  |
| **MSSQL_MASTER_DATA_FILE** | マスター データベースのデータ ファイルの場所を設定します。 SQL Server を最初に実行するまでは、**master.mdf** という名前を付ける必要があります。 |
| **MSSQL_MASTER_LOG_FILE** | マスター データベースのログ ファイルの場所を設定します。 SQL Server を最初に実行するまでは、**mastlog.ldf** という名前を付ける必要があります。 |
| **MSSQL_ERROR_LOG_FILE** | エラー ログ ファイルの場所を設定します。 |

::: moniker-end
<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 || =sqlallproducts-allversions"

| 環境変数 | [説明] |
|-----|-----|
| **ACCEPT_EULA** | **ACCEPT_EULA** 変数を任意の値に設定し、[使用許諾契約書](https://go.microsoft.com/fwlink/?LinkId=746388)の承諾を確定します。 SQL Server イメージの設定が必要です。 |
| **MSSQL_SA_PASSWORD** | SA ユーザーのパスワードを構成します。 |
| **MSSQL_PID** | SQL Server のエディションまたはプロダクト キーを設定します。 有効な値は次のとおりです。 </br></br>**Evaluation**</br>**開発者**</br>**Express**</br>**Web**</br>**Standard**</br>**Enterprise**</br>**プロダクト キー**</br></br>プロダクト キーを指定する場合は、#####-#####-#####-#####-##### という形式にする必要があります。ここで、'#' は数字または文字を表します。|
| **MSSQL_LCID** | SQL Server 用に使う言語 ID を設定します。 たとえば、1036 はフランス語です。 |
| **MSSQL_COLLATION** | SQL Server の既定の照合順序を設定します。 これにより、言語 ID (LCID) から照合順序への既定のマッピングがオーバーライドされます。 |
| **MSSQL_MEMORY_LIMIT_MB** | SQL Server が使用できるメモリの最大容量 (MB 単位) を設定します。 既定では、これは物理メモリの合計容量の 80% です。 |
| **MSSQL_TCP_PORT** | SQL Server がリッスンする TCP ポートを構成します (既定値は 1433)。 |
| **MSSQL_IP_ADDRESS** | IP アドレスを設定します。 現在、IP アドレスは IPv4 形式 (0.0.0.0) である必要があります。 |
| **MSSQL_BACKUP_DIR** | 既定のバックアップ ディレクトリの場所を設定します。 |
| **MSSQL_DATA_DIR** | 新しい SQL Server データベースのデータ ファイル (.mdf) が作成されるディレクトリを変更します。 |
| **MSSQL_LOG_DIR** | 新しい SQL Server データベースのログ (.ldf) ファイルが作成されるディレクトリを変更します。 |
| **MSSQL_DUMP_DIR** | SQL Server が、メモリ ダンプやその他のトラブルシューティング ファイルを格納する既定のディレクトリを変更します。 |
| **MSSQL_ENABLE_HADR** | 可用性グループを有効にします。 たとえば、'1' は有効、'0' は無効です |
| **MSSQL_AGENT_ENABLED** | SQL Server エージェントを有効にします。 たとえば、'true' は有効、'false' は無効です。 既定では、エージェントは無効になっています。  |
| **MSSQL_MASTER_DATA_FILE** | マスター データベースのデータ ファイルの場所を設定します。 SQL Server を最初に実行するまでは、**master.mdf** という名前を付ける必要があります。 |
| **MSSQL_MASTER_LOG_FILE** | マスター データベースのログ ファイルの場所を設定します。 SQL Server を最初に実行するまでは、**mastlog.ldf** という名前を付ける必要があります。 |
| **MSSQL_ERROR_LOG_FILE** | エラー ログ ファイルの場所を設定します。 |

::: moniker-end

## <a name="use-with-initial-setup"></a>初期セットアップでの使用

この例では、構成済みの環境変数を使って `mssql-conf setup` を実行します。 以下の環境変数が指定されています。

- **ACCEPT_EULA**: エンド ユーザー使用許諾契約に同意します。
- **MSSQL_PID**: 非運用環境で使用するために、無料でライセンスが付与される Developer Edition の SQL Server を指定します。
- **MSSQL_SA_PASSWORD**: 強力なパスワードを設定します。
- **MSSQL_TCP_PORT**: SQL Server がリッスンする TCP ポートを 1234 に設定します。

```bash
sudo ACCEPT_EULA='Y' MSSQL_PID='Developer' MSSQL_SA_PASSWORD='<YourStrong!Passw0rd>' MSSQL_TCP_PORT=1234 /opt/mssql/bin/mssql-conf setup
```

## <a name="use-with-docker"></a>Docker での使用

この docker コマンドの例では、以下の環境変数を使って新しい SQL Server コンテナーを作成します。

- **ACCEPT_EULA**: エンド ユーザー使用許諾契約に同意します。
- **MSSQL_PID**: 非運用環境で使用するために、無料でライセンスが付与される Developer Edition の SQL Server を指定します。
- **MSSQL_SA_PASSWORD**: 強力なパスワードを設定します。
- **MSSQL_TCP_PORT**: SQL Server がリッスンする TCP ポートを 1234 に設定します。 つまり、この例では、ホストのポートにポート 1433 (既定値) をマップするのではなく、`-p 1234:1234` コマンドを使ってカスタム TCP ポートをマップする必要があります。

<!--SQL Server 2017 on Linux -->
::: moniker range="= sql-server-linux-2017 || = sql-server-2017"

Linux/macOS 上で Docker を実行している場合は、単一引用符を含む次の構文を使います。

```bash
docker run -e ACCEPT_EULA=Y -e MSSQL_PID='Developer' -e MSSQL_SA_PASSWORD='<YourStrong!Passw0rd>' -e MSSQL_TCP_PORT=1234 -p 1234:1234 -d mcr.microsoft.com/mssql/server:2017-latest
```

Windows 上で Docker を実行している場合は、二重引用符を含む次の構文を使います。

```bash
docker run -e ACCEPT_EULA=Y -e MSSQL_PID="Developer" -e MSSQL_SA_PASSWORD="<YourStrong!Passw0rd>" -e MSSQL_TCP_PORT=1234 -p 1234:1234 -d mcr.microsoft.com/mssql/server:2017-latest
```

> [!NOTE]
> コンテナーで実稼働エディションを実行するためのプロセスは若干異なります。 詳細については、「[Run production container images](sql-server-linux-configure-docker.md#production)」(実稼働のコンテナー イメージを実行する) を参照してください。

::: moniker-end
<!--SQL Server 2019 on Linux-->
::: moniker range=">= sql-server-linux-ver15 || >= sql-server-ver15 || =sqlallproducts-allversions"

Linux/macOS 上で Docker を実行している場合は、単一引用符を含む次の構文を使います。

```bash
docker run -e ACCEPT_EULA=Y -e MSSQL_PID='Developer' -e MSSQL_SA_PASSWORD='<YourStrong!Passw0rd>' -e MSSQL_TCP_PORT=1234 -p 1234:1234 -d mcr.microsoft.com/mssql/server:2019-CTP3.2-ubuntu
```

Windows 上で Docker を実行している場合は、二重引用符を含む次の構文を使います。

```bash
docker run -e ACCEPT_EULA=Y -e MSSQL_PID="Developer" -e MSSQL_SA_PASSWORD="<YourStrong!Passw0rd>" -e MSSQL_TCP_PORT=1234 -p 1234:1234 -d mcr.microsoft.com/mssql/server:2019-CTP3.2-ubuntu
```

::: moniker-end

## <a name="next-steps"></a>次の手順

ここに記載されていないその他の SQL Server 設定については、「[mssql-conf ツールを使用して SQL Server on Linux を構成する](sql-server-linux-configure-mssql-conf.md)」をご覧ください。

Linux 上に SQL Server をインストールして実行する方法について詳しくは、[Linux への SQL Server のインストール](sql-server-linux-setup.md)に関する記事をご覧ください。
