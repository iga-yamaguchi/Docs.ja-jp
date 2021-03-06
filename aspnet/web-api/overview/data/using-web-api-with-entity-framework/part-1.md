---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-1
title: "Entity Framework 6 と Web API 2 の使用 |Microsoft ドキュメント"
author: MikeWasson
description: "このチュートリアル学びますバック エンドを ASP.NET Web API を使用して web アプリケーションの作成の基本。 チュートリアルでは、データ レイアウトの Entity Framework 6 を使用しています."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/28/2015
ms.topic: article
ms.assetid: e879487e-dbcd-4b33-b092-d67c37ae768c
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-1
msc.type: authoredcontent
ms.openlocfilehash: cceefa128f90b4c3e23dd31119f44e6ffc55f46f
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2018
---
<a name="using-web-api-2-with-entity-framework-6"></a>Entity Framework 6 と Web API 2 の使用
====================
によって[Mike Wasson](https://github.com/MikeWasson)

[完成したプロジェクトをダウンロードします。](https://github.com/MikeWasson/BookService)

> このチュートリアル学びますバック エンドを ASP.NET Web API を使用して web アプリケーションの作成の基本。 チュートリアルでは、クライアント側 JavaScript アプリケーションのデータ層と Knockout.js Entity Framework 6 を使用します。 このチュートリアルでは、Azure App Service Web アプリにアプリを配置する方法も示します。
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>このチュートリアルで使用されているソフトウェアのバージョン
> 
> 
> - Web API 2.1
> - [Visual Studio 2013 Update 2](https://www.visualstudio.com/downloads/download-visual-studio-vs)
> - Entity Framework 6
> - .NET 4.5
> - [Knockout.js](http://knockoutjs.com/) 3.1


このチュートリアルでは、Entity Framework 6 のバックエンド データベースを処理する web アプリケーションを作成するのに ASP.NET Web API 2 を使用します。 作成するアプリケーションのスクリーン ショットを次に示します。

[![](part-1/_static/image2.png)](part-1/_static/image1.png)

アプリでは、単一ページ アプリケーション (SPA) の設計を使用します。 「単一ページ アプリケーション」は、1 つの HTML ページが読み込まれ、新しいページを読み込むのではなく、ページを動的に更新する web アプリケーションの一般的な用語です。 最初のページ読み込み後、アプリは、AJAX 要求を使用してサーバーを説明します。 AJAX では、UI を更新するアプリを使用する、戻り値の JSON データを要求します。

AJAX 新しいが、現在では、構築し、高度な SPA の大規模アプリケーションの管理を簡略化するための JavaScript フレームワークです。 このチュートリアルでは使用[Knockout.js](http://knockoutjs.com/)、JavaScript クライアント フレームワークを使用することができます。

このアプリのメインのビルド ブロックを次に示します。

- ASP.NET MVC では、HTML ページを作成します。
- ASP.NET Web API は、AJAX 要求を処理し、JSON データを返します。
- Knockout.js にデータをバインド HTML 要素を JSON データ。
- Entity Framework は、データベースを説明します。

## <a name="see-this-app-running-on-azure"></a>Azure で実行されているこのアプリを参照してください。

ライブ web アプリとして実行している完成したサイトを参照してもよろしいですか。 Azure アカウントに、アプリの完全なバージョンを展開するには、次のボタンをクリックするだけです。

[![](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/BookService)

Azure アカウントを Azure にこのソリューションを展開する必要があります。 アカウントがない場合は、次のオプションがあります。

- [無料の Azure アカウントを開設](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604)-クレジットを取得するでも使用されているアカウントを維持する最大と使用する無料の Azure サービスおよび有料の Azure サービスを試す使用できます。
- [MSDN サブスクライバー特典をアクティブ化](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604)-お客様の MSDN サブスクリプションでは、クレジット有料の Azure サービスを使用できるすべての月です。

## <a name="create-the-project"></a>プロジェクトの作成

Visual Studio を開きます。 **ファイル**メニューの **新規**選択してから、**プロジェクト**です。 (をクリックしてまたは**新しいプロジェクト**スタート ページです)。

**新しいプロジェクト**ダイアログ ボックスで、をクリックして**Web**左側のウィンドウでと**ASP.NET Web アプリケーション**中央のペインでします。 BookService プロジェクトの名前を指定し、をクリックして**OK**です。

[![](part-1/_static/image4.png)](part-1/_static/image3.png)

**新しい ASP.NET プロジェクト**ダイアログで、選択、 **Web API**テンプレート。

[![](part-1/_static/image6.png)](part-1/_static/image5.png)

Azure App service プロジェクトをホストする場合のままにして、**クラウド内のホスト**チェック ボックスをオンします。

をクリックして**OK**プロジェクトを作成します。

## <a name="configure-azure-settings-optional"></a>(省略可能) Azure の設定を構成します。

中断した場合、**クラウドでホスト**オプションのチェック、Visual Studio には、Microsoft Azure にサインインするように求められます

[![](part-1/_static/image8.png)](part-1/_static/image7.png)

Azure にサインインした後 Visual Studio では、web アプリを構成するように求められます。 サイトの名前を入力、Azure サブスクリプションを選択し、地理的地域を選択します。 **データベース サーバー****を作成する新しいサーバー**です。 管理者のユーザー名とパスワードを入力します。

[![](part-1/_static/image10.png)](part-1/_static/image9.png)

>[!div class="step-by-step"]
[次へ](part-2.md)
