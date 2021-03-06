---
title: "Visual Studio Tools for Docker と ASP.NET Core"
author: spboyer
description: "Visual Studio 2017 ツールと Docker for Windows を使用して、ASP.NET Core アプリをコンテナー化する方法を説明します。"
manager: wpickett
ms.author: scaddie
ms.custom: mvc
ms.date: 12/12/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 590d32342b1724a0cbc937655c35631938eb09b2
ms.sourcegitcommit: 53ee14b9c8200f44705d8997c3619fa874192d45
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="visual-studio-tools-for-docker-with-aspnet-core"></a>Visual Studio Tools for Docker と ASP.NET Core

[Visual Studio 2017](https://www.visualstudio.com/) は、.NET Framework または .NET Core をターゲットとする ASP.NET Core アプリのビルド、デバッグ、実行をサポートします。 Windows と Linux の両方のコンテナーがサポートされます。

## <a name="prerequisites"></a>必須コンポーネント

* [Visual Studio 2017](https://www.visualstudio.com/) と **[.NET Core クロスプラットフォームの開発]** ワークロード
* [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="installation-and-setup"></a>インストールとセットアップ

Docker をインストールする場合、「[Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)」 (Docker for Windows: インストール前に知っておくべきこと) の情報を確認し、[Docker for Windows](https://docs.docker.com/docker-for-windows/install/) をインストールします。

ボリュームのマッピングとデバッグをサポートするように、Docker for Windows で**[共有ドライブ](https://docs.docker.com/docker-for-windows/#shared-drives)**を構成する必要があります。 システム トレイの Docker のアイコンを右クリックし、選択**設定しています.**を選択して**ドライブ共有**です。 Docker がファイルを格納するドライブを選択します。 選択**適用**です。

![共有ドライブ](./visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> Visual Studio 2017 バージョン 15.6 以降を求めるときに**ドライブ共有**が構成されていません。

## <a name="add-docker-support-to-an-app"></a>アプリに Docker サポートを追加する

ASP.NET Core プロジェクトに Docker のサポートを追加するためにプロジェクトは .NET Core をターゲットする必要があります。 Linux と Windows の両方のコンテナーがサポートされます。

Docker のサポートをプロジェクトに追加するときに、Windows または Linux コンテナーを選択します。 Docker ホストが同じコンテナーの種類を実行している必要があります。 実行中の Docker インスタンスでコンテナーの種類を変更するには、システム トレイの Docker アイコンを右クリックして、**[Switch to Windows containers...]\(Windows コンテナーに切り替える...\)** または **[Switch to Linux containers...]\(Linux コンテナーに切り替える...\)** を選択します。

### <a name="new-app"></a>新しいアプリ

**ASP.NET Core Web アプリケーション** プロジェクト テンプレートを使用して新しいアプリを作成する場合は、次のように **[Enable Docker Support]\(Docker サポートを有効にする\)** チェック ボックスをオンにします。

![[Enable Docker Support]\(Docker サポートを有効にする\) チェック ボックス](visual-studio-tools-for-docker/_static/enable-docker-support-checkbox.png)

ターゲット フレームワークが .NET Core の場合、 **OS**ボックスにより、コンテナーの種類を選択します。

### <a name="existing-app"></a>既存のアプリ

Visual Studio Tools for Docker では、.NET Framework をターゲットとする既存の ASP.NET Core プロジェクトへの Docker の追加はサポートされません。 .NET Core をターゲットとする ASP.NET Core プロジェクトの場合、ツールを使用して Docker サポートを追加するための 2 つのオプションがあります。 Visual Studio でプロジェクトを開き、次のオプションのいずれかを選択します。

* **[プロジェクト]** メニューから **[Docker サポート]** を選択します。
* ソリューション エクスプローラーでプロジェクトを右クリックして、**[追加]** > **[Docker サポート]** の順に選択します。

## <a name="docker-assets-overview"></a>Docker 資産の概要

Visual Studio Tools for Docker は、ソリューションに *docker-compose* プロジェクトを追加します。以下のものを含みます。

* *.dockerignore*: ビルド コンテキストを生成するときに除外するファイルとディレクトリのパターンの一覧が含まれています。
* *docker-compose.yml*: `docker-compose build` と `docker-compose run` を使用してビルドされ実行されるイメージのコレクションを定義するために使用されるベースとなる [Docker Compose](https://docs.docker.com/compose/overview/) ファイル。
* *docker-compose.override.yml*: サービスの構成オーバーライドを含む、Docker Compose によって読み取られるオプション ファイル。 Visual Studio は `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` を実行してこれらのファイルをマージします。

*Dockerfile* (Docker の最終イメージを作成するためのレシピ) は、プロジェクトのルートに追加されます。 その中に含まれるコマンドの詳細については、「[Dockerfile reference](https://docs.docker.com/engine/reference/builder/)」 (Dockerfile リファレンス) を参照してください。 この特定の *Dockerfile* では、次のような、4 つの異なる名前付きのビルド ステージを含む、[multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) を使用します。

[!code-dockerfile[](visual-studio-tools-for-docker/samples/HelloDockerTools/HelloDockerTools/Dockerfile?highlight=1,5,14,17)]

*Dockerfile* は、[microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore) イメージに基づいています。 このベース イメージには、Just-In-Time (JIT) にコンパイルされて起動時のパフォーマンスが向上した、ASP.NET Core NuGet パッケージが含まれます。

*Docker compose.yml*ファイルには、プロジェクトの実行時に作成されるイメージの名前が含まれています。

[!code-yaml[](visual-studio-tools-for-docker/samples/HelloDockerTools/docker-compose.yml?highlight=5)]

上の例では、`image: hellodockertools` によって、アプリが**デバッグ** モードで実行されるときに、イメージ `hellodockertools:dev` が生成されます。 `hellodockertools:latest` イメージは、アプリが**リリース** モードで実行されるときに生成されます。

イメージ名のプレフィックス、 [Docker Hub](https://hub.docker.com/)ユーザー名 (たとえば、 `dockerhubusername/hellodockertools`) 場合は、イメージは、レジストリにプッシュします。 プライベート レジストリの url イメージの名前を変更する代わりに、(たとえば、 `privateregistry.domain.com/hellodockertools`) によっては、構成します。

## <a name="debug"></a>デバッグ

ツールバーのデバッグ ドロップダウンから **[Docker]** を選択し、アプリのデバッグを開始します。 **[出力]** ウィンドウの **[Docker]** ビューには、行われている次のアクションが表示されます。

* *Microsoft/aspnetcore* (いない内の場合、キャッシュ) ランタイム イメージを取得します。
* *Microsoft/aspnetcore-ビルド*(いない内の場合、キャッシュ) コンパイル/発行のイメージを取得します。
* *ASPNETCORE_ENVIRONMENT* 環境変数は、コンテナー内で `Development` に設定されます。
* ポート 80 が公開され、localhost 用に動的に割り当てられているポートにマップされます。 このポートは、Docker ホストによって決定され、`docker ps` コマンドでクエリを実行することができます。
* アプリは、コンテナーにコピーされます。
* 既定のブラウザーが動的に割り当てられたポートを使用して、コンテナーにアタッチされたデバッガーを起動します。 

結果の Docker イメージは、*デベロッパー*を使用してアプリのイメージ、 *microsoft/aspnetcore*基本イメージとしてイメージ。 **パッケージ マネージャー コンソール** (PMC) ウィンドウで `docker images` コマンドを実行します。 コンピューター上の画像が表示されます。

```console
REPOSITORY                   TAG                   IMAGE ID            CREATED             SIZE
hellodockertools             latest                f8f9d6c923e2        About an hour ago   391MB
hellodockertools             dev                   85c5ffee5258        About an hour ago   389MB
microsoft/aspnetcore-build   2.0-nanoserver-1709   d7cce94e3eb0        15 hours ago        1.86GB
microsoft/aspnetcore         2.0-nanoserver-1709   8872347d7e5d        40 hours ago        389MB
```

> [!NOTE]
> 開発用のイメージとして、アプリの内容がない**デバッグ**構成では、反復的なエクスペリエンスを提供するボリュームのマウントを使用します。 イメージをプッシュするには、**リリース**構成を使用します。

PMC で `docker ps` コマンドを実行します。 アプリがコンテナーを使用して実行されていることがわかります。

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>エディット コンティニュ

静的なファイルや Razor ビューに対する変更は、コンパイルを行う必要はなく、自動的に更新されます。 変更を行って保存し、ブラウザーを更新し、更新を確認します。  

コード ファイルを変更した場合、コンパイルを行い、コンテナー内で Kestrel を再起動する必要があります。 変更後、CTRL キーを押しながら F5 キーを使用して、プロセスを実行して、コンテナー内でアプリを起動します。 Docker コンテナーは再構築がないか、停止しました。 PMC で `docker ps` コマンドを実行します。 元のコンテナーが 10 分前と同じ状態で実行されていることを確認できます。

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>Docker イメージの発行

アプリの開発およびデバッグのサイクルを完了すると、Visual Studio Tools for Docker は、アプリの実稼働のイメージの作成に役立ちます。 構成ドロップダウンを **[リリース]** に変更し、アプリを構築します。 ツールでイメージを生成する、*最新*タグで、プライベート レジストリまたは Docker Hub にプッシュできます。 

PMC で `docker images` コマンドを実行して、次のようにイメージの一覧を表示します。

```console
REPOSITORY                   TAG                   IMAGE ID            CREATED             SIZE
hellodockertools             latest                4cb1fca533f0        19 seconds ago      391MB
hellodockertools             dev                   85c5ffee5258        About an hour ago   389MB
microsoft/aspnetcore-build   2.0-nanoserver-1709   d7cce94e3eb0        16 hours ago        1.86GB
microsoft/aspnetcore         2.0-nanoserver-1709   8872347d7e5d        40 hours ago        389MB
```

> [!NOTE]
> `docker images` コマンドは、*\<none>* として識別されるリポジトリ名とタグを持つ中間イメージを返します (上にはリストされていません)。 これらの名前のないイメージは、[multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile* によって生成されます。 これにより、最終イメージの構築効率が向上します&mdash;変更時には必要なレイヤーのみが再構築されます。 中間のイメージが不要になったときでは、削除を使用して、 [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/)コマンド。

*dev* イメージと比較した場合、実稼働またはリリース イメージはサイズが小さいと思うかもしれません。 ボリュームのマッピング、デバッガーとアプリ、実行されていたローカルのコンピューターから、コンテナー内ではなくです。 *latest* イメージには、ホスト コンピューターでアプリを実行するために必要なアプリ コードがパッケージ化されています。 そのため、デルタは、アプリ コードのサイズです。
