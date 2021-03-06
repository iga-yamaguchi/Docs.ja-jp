---
uid: whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application
title: "方法: ASP.NET Web フォームにモバイル ページを追加する/MVC アプリケーション |Microsoft ドキュメント"
author: rick-anderson
description: "ここで説明するページ、ASP.NET Web フォームからのモバイル デバイス用に最適化に提供するさまざまな方法を説明します/MVC アプリケーション、アーキテクチャを提案してとしています。"
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/20/2011
ms.topic: article
ms.assetid: 3124f28e-cc32-418a-afe3-519fa56f4c36
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application
msc.type: content
ms.openlocfilehash: aac359b26c508784793a67260dc2e65c30db687a
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2018
---
<a name="how-to-add-mobile-pages-to-your-aspnet-web-forms--mvc-application"></a>方法: ASP.NET Web フォームにモバイル ページを追加する/MVC アプリケーション
====================
> **適用されます。**
> 
> - ASP.NET Web フォームのバージョン 4.0
> - ASP.NET MVC version 3.0
> 
> **まとめ**
> 
> ここで説明するページ、ASP.NET Web フォームからのモバイル デバイス用に最適化に提供するさまざまな方法を説明します。/MVC アプリケーション アーキテクチャを提案し、幅広い種類のデバイスを対象とする場合を考慮する問題をデザインします。 このドキュメントについても説明します 3.5 に ASP.NET 2.0 から ASP.NET モバイル コントロールの旧式である理由といくつかの最新の代替案について説明します。


## <a name="contents"></a>目次

- 概要
- アーキテクチャ上のオプション
- ブラウザーとデバイスの検出
- ASP.NET Web フォーム アプリケーションがモバイル固有のページを表示する方法
- ASP.NET MVC アプリケーションがモバイル固有のページを表示する方法
- その他の技術情報

ASP.NET Web フォームと MVC の両方のこのホワイト ペーパーの手法を示すダウンロード可能なコード サンプルでは、次を参照してください。 [Mobile Apps と ASP.NET を使用したサイト](https://docs.microsoft.com/aspnet/mobile/overview)です。

## <a name="overview"></a>概要

– スマート フォン、機能の携帯電話およびタブレット – モバイル デバイスは、Web にアクセスするための手段として人気を拡張し続けます。 多くの web 開発者および企業の web 指向、つまりすることがますます重要これらのデバイスを使用する訪問者の閲覧エクスペリエンスを向上します。

### <a name="how-earlier-versions-of-aspnet-supported-mobile-browsers"></a>どの以前のバージョンの ASP.NET がサポートされているモバイル ブラウザー

ASP.NET のバージョン 2.0 に 3.5 は含まれている*ASP.NET モバイル コントロール*: でのモバイル デバイス用のサーバー コントロールのセット、 *System.Web.Mobile.dll*アセンブリおよび*System.Web.UI.MobileControls*名前空間。 ASP.NET 4 のアセンブリが掲載されていますが、推奨されていません。 開発者は、このホワイト ペーパーに記載されているなどのより新しいアプローチに移行することをお勧めします。

ASP.NET モバイル コントロールをなぜ廃止としてマークされている理由が 2005 およびそれ以前の周囲に共通する携帯電話の周囲のデザインを指向することです。 主に、コントロールはその時代 (年号) の WAP ブラウザーの (通常の HTML) ではなく WML または cHTML のマークアップを表示するために設計されています。 WAP を WML および cHTML が不要ほとんどのプロジェクトに関連する HTML がモバイルとデスクトップ ブラウザーと同様の場所を問わずマークアップ言語になるようになりました。

### <a name="the-challenges-of-supporting-mobile-devices-today"></a>今日のモバイル デバイスをサポートするという課題

モバイル ブラウザーほぼ例外なくサポート HTML、場合でも優れたモバイル ブラウジング エクスペリエンスを作成するときに、多くの課題が直面します。

- ***画面のサイズ***- フォームで、モバイル デバイスが大きく変化し、その画面は、多くの場合と非常デスクトップ モニターよりも小さいです。 そのため、完全に別のページ レイアウトをデザインする必要があります。
- ***入力方式***– 一部のデバイスがあるキーパッド、いくつか styluses、タッチを使用するものです。 複数のナビゲーション方法とデータの入力方法を考慮する必要があります。
- ***標準への準拠***– 多くのモバイル ブラウザーは、最新の HTML、CSS、または JavaScript の標準をサポートしていません。
- ***帯域幅***– 携帯データ ネットワークのパフォーマンスは、こうした見通しは異なります、エンド ユーザーがいるメガバイト単位で課金される関税率。

汎用型のソリューションはありません。アプリケーションは、外観し、動作が異なるに従って、デバイスにアクセスする必要があります。 モバイル デバイスのサポートのレベルを指定、に応じて入れ替えて web 開発者用デスクトップ「ブラウザー戦争」に変わりはよりも指定できます。

初めてのモバイル ブラウザー サポートを多くの場合、最初に近づいている開発者は、のみであるため、開発者が多くの場合、個人所有など、おそらく、最新で最も高度なスマート フォン (Windows Phone 7、iPhone や Android など) をサポートするために重要と思われるデバイス。 ただし、安い電話は非常に人気の高いも、その所有者が携帯電話がブロード バンド接続よりも簡単に取得する場所の国で特に – web の閲覧に使用しないでください。 お客様のビジネスは、その可能性の高い顧客を考慮してサポートするためにデバイスの範囲を決定する必要があります。 おそらく小さい強力な機能によって、訪問者に対応する必要があるの映画のチケット予約システムを作成する場合、高度なスマート フォンをターゲットにのみビジネスの意思決定を行う可能性があります luxury ヘルス spa のオンラインのパンフレットを作成する場合携帯電話します。

## <a name="architectural-options"></a>アーキテクチャ上のオプション

ASP.NET Web フォームまたは MVC の特定の技術的な詳細を始める前に、web 開発者に一般的にモバイル ブラウザーをサポートするための 3 つの主な可能なオプションがあることに注意してください。

1. ***何もしない –***だけで、デスクトップ指向の標準的な web アプリケーションを作成して許容可能な表示にするためにモバイル ブラウザーに依存します。 

    - **利点**: これは最も安価なオプションが実装と管理作業は必要ありません
    - **欠点**: 最悪のエンド ユーザー エクスペリエンスを提供します。 

        - 最新のスマート フォンがデスクトップのブラウザーと同様、HTML を表示することがありますが、ユーザーがズームおよび水平方向にスクロールし、垂直方向に小さな画面上で、コンテンツを使用するが強制されます。 これは最適なかけ離れていることです。
        - 満足のいく方法で、マークアップを表示するために古いデバイスと機能の携帯電話があります。
        - でも、最新のタブレット デバイス上 (の画面はラップトップの画面サイズで使用できます)、さまざまな相互作用の規則が適用されます。 タッチ入力はボタン サイズの拡大に最適スプレッドさらに、間隔のリンクし、ポップアップ メニューにマウス カーソルをポイントする方法はありません。
2. ***クライアントで問題を解決*–** CSS を使用することで、[段階的な強化](http://en.wikipedia.org/wiki/Progressive_enhancement)マークアップ、スタイル、およびどのようなブラウザーでは、それらが実行を調整するスクリプトを作成することができます。 たとえば、 [CSS 3 メディア クエリ](http://www.w3.org/TR/2010/CR-css3-mediaqueries-20100727/)デバイスの画面が選択されたしきい値よりも幅の狭いで 1 つの列のレイアウトに変換する複数列のレイアウトを作成する可能性があります。 

    - **利点**: に従って、どのような画面および入力の特性がある不明な将来のデバイスであっても、使用中で特定のデバイスのレンダリングを最適化
    - **利点**: 簡単にすべてのデバイスの種類 – コードまたは作業量を最小限に抑える複製サーバー側ロジックを共有することができます
    - **欠点**: モバイル デバイスが可能性があること、モバイル ページで、デスクトップのページから完全に異なるさまざまな情報を示すデスクトップ デバイスとは異なるためです。 このようなバリエーションが効率にすることがやを確実に実現不可能なだけでも、CSS から方法一貫性特に考慮すると古いデバイスの解釈 CSS 規則。 これは特に、CSS 3 属性です。
    - **欠点**: さまざまなサーバー側ロジックおよび各種デバイス用のワークフローのサポートはありません。 CSS のみで使用してモバイル ユーザーの簡略化されたショッピング カート チェック アウト ワークフローを実装することはできませんなど。
    - **欠点**: 非効率的な帯域幅の使用。 サーバーは、ターゲット デバイスはその情報のサブセットのみを使用しても、マークアップとすべての可能なデバイスに適用されるスタイルを送信する必要があります。
3. ***サーバー上の問題を解決*–** – どのデバイスがアクセスして、サーバーが認識されている場合または入力方式は、その画面サイズなど、デバイスの最低限の特性と別のロジックを実行できます: モバイル デバイスであるかどうかと異なる HTML マークアップを出力します。 

    - **利点:**最大柔軟性です。 モバイルのサーバー側ロジックを変更したり、必要なデバイスに固有のレイアウトのマークアップを最適化する作業がどの程度の制限はありません。
    - **利点:**効率的な帯域幅の使用。 のみのマークアップとターゲット デバイスを使用する予定のスタイル情報を送信する必要があります。
    - **短所:**強制的残存作業時間またはコードの繰り返しになることがあります (などを行う Web フォーム ページまたは MVC ビューの似ていますが、若干異なるコピーを作成する)。 ここで基になるレイヤーまたはサービスですが、まだ、UI コードまたはマークアップの一部に共通のロジックをファクタリングが可能な限りを複製し、並列で管理し、必要があります。
    - **短所:**デバイスの検出が容易ではありません。 リストまたは既知のデバイスの種類とその特性 (できない可能性があります常に完全に最新の状態) のデータベースが必要ですし、すべての受信要求を正確に一致する保証はありません。 このドキュメントでは、いくつかのオプションとその問題点を後でについて説明します。

最良の結果を得るには、ほとんどの開発者は、オプション (2) と (3) を結合する必要があるを検索します。 データ、ワークフロー、またはマークアップの主な相違点がサーバー側コードで実装が最も効果的に対し、で始め、多少異なるは CSS または偶数の JavaScript を使用して、クライアントで対応最適。

### <a name="this-paper-focuses-on-server-side-techniques"></a>このホワイト ペーパーは、サーバー側の技法に焦点を当てています

ASP.NET Web フォームと MVC は、主にサーバー側の両方のテクノロジであるため、このホワイト ペーパーは異なるマークアップとモバイル ブラウザーのロジックを作成するのに便利なサーバー側技法に説明します。 もちろん、任意のクライアント側手法 (たとえば、CSS 3 メディア クエリ、プログレッシブ拡張 JavaScript)、これも組み合わせることができますが、ASP.NET 開発するよりも web デザインの問題より。

## <a name="browser-and-device-detection"></a>ブラウザーとデバイスの検出

モバイル デバイスをサポートするためのすべてのサーバー側手法のキーの前提条件を知り、ビジターを使用してどのようなデバイスです。 実際には、そのデバイスの製造元とモデルの数を知ることがわからないより良いもので、*特性*デバイスのです。 特性があります。

- モバイル デバイスですか。
- 入力方式 (マウスまたはキーボード、タッチ、キーパッド、ジョイスティック、...)
- 画面のサイズ (物理的と (ピクセル単位))
- サポートされているメディアとデータの形式
- などです。

意思決定を行うモデル番号より特性に基づくためことをお勧めしてより設備が将来のデバイスの処理ができます。

### <a name="using-aspnets-built-in-browser-detection-support"></a>ASP を使用します。NET の組み込みブラウザー検出のサポート

プロパティを調べることで ASP.NET Web フォームと MVC の開発者が訪問ブラウザーの重要な特性をすぐに検出、 *Request.Browser*オブジェクト。 たとえばを参照してください。

- Request.Browser.IsMobileDevice
- Request.Browser.MobileDeviceManufacturer、Request.Browser.MobileDeviceModel
- Request.Browser.ScreenPixelsWidth
- Request.Browser.SupportsXmlHttp
- .. 参照できます他の多くのユーザー

背後では、ASP.NET プラットフォームには、入力方向が一致する*User-agent*に対して一連のブラウザーの定義 XML ファイルでの正規表現 (UA) HTTP ヘッダー。 既定では、プラットフォームには、多くの一般的なモバイル デバイスの定義が含まれます、他のユーザーが認識するカスタムのブラウザー定義ファイルを追加することができます。 詳細については、MSDN のページを参照してください。 [ASP.NET Web サーバー コントロールとブラウザーの機能](https://msdn.microsoft.com/library/x3k2ssx2.aspx)します。

### <a name="using-the-wurfl-device-database-via-51degreesmobi-foundation"></a>データベースを使用してください、WURFL デバイス 51Degrees.mobi Foundation を使用して

ASP の中にします。できない可能性があるのに十分な場合に 2 つの主要なケースがある NET の組み込みブラウザー検出のサポートは、多くのアプリケーションのための十分なされます。

- ***最新のデバイスを認識する***(なし、それらのブラウザー定義ファイルを手動で作成) します。 .NET 4 のブラウザー定義ファイルが Windows Phone 7、Android フォン、Opera Mobile ブラウザーの場合、または Apple Ipad を認識するのに十分な最新でないことに注意してください。
- ***デバイスの機能の詳細な情報が必要な***します。 デバイスの入力方式 (タッチ vs キーパッドなど) について知っておく必要がありますか、どのようなオーディオ形式のブラウザーをサポートしています。 この情報は、標準のブラウザー定義ファイルでは使用できません。

[*ワイヤレス ユニバーサル リソース ファイル*(WURFL) プロジェクト](http://wurfl.sourceforge.net/)はるかに最新の状態と詳細に関する情報を保持使用しているモバイル デバイス今日です。

.NET 開発者向けのすばらしいは、その ASP です。NET のブラウザーの検出機能は、ため機能を拡張してこれらの問題を解決することは、拡張可能です。 たとえば、オープン ソースを追加することができます[ *51Degrees.mobi Foundation* ](https://github.com/51Degrees/dotNET-Device-Detection)をプロジェクトにライブラリです。 ASP.NET IHttpModule またはブラウザー機能プロバイダー (Web フォームと MVC アプリケーションの両方で使用できる)、直接 WURFL データを読み取り、ASP にフックするをお勧めします。NET の組み込みブラウザー検出メカニズムです。 モジュールをインストールしたら*Request.Browser*もっと正確かつ詳細な情報が含まれます突然: 正しくの前に説明したデバイスの多くが認識され、(などの機能を一覧表示されます追加機能の入力方法など)。 詳細については、プロジェクトのドキュメントを参照してください。

## <a name="how-web-forms-applications-can-present-mobile-specific-pages"></a>Web フォーム アプリケーションがモバイル固有のページを表示する方法

既定では、次に一般的なモバイル デバイスで新しい Web フォーム アプリケーションを表示する方法を示します。

![](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/_static/image1.png)

明確に、どちらもレイアウトは非常にモバイル対応 – このページは、縦向きで小さな画面ではなく、規模が大きく、横向きのモニター用に設計されました。 したがって何を実行できることについてしますか。

この記事で前述したよう、モバイル デバイスの場合、ページを調整するためのさまざまな方法があります。 一部のテクニックは、サーバー ベースで、他のユーザーがクライアントで実行します。

### <a name="creating-a-mobile-specific-master-page"></a>特定のモバイル マスター ページを作成します。

要件に応じて、ことができますを同じ Web フォームを使用して、すべてのビジターに対しては、2 つのマスター ページを異なる: デスクトップ訪問者の 1 つは、モバイル ユーザー用に別のです。 これは、ビューでは、柔軟、CSS スタイル シート、またはページ、任意のロジックを複製することを強制することがなく、モバイル デバイスに合わせて、最上位の HTML マークアップを変更したことができます。

これは簡単に行えます。 たとえば、Web フォームに、次のよう PreInit ハンドラーを追加することができます。

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample1.cs)]

ここで、アプリケーションの最上位のフォルダーで Mobile.Master と呼ばれるマスター ページを作成し、モバイル デバイスが検出されたときに使用されます。 モバイル マスター ページには、必要な場合、mobile に固有の CSS スタイル シートを参照できます。 デスクトップの訪問者が、既定のマスター ページ、モバイルのものではなく表示されます。

### <a name="creating-independent-mobile-specific-web-forms"></a>独立した特定のモバイル Web フォームを作成します。

柔軟性を最大限に高めることができます移動するさまざまなデバイスの種類別のマスター ページを単にない場合に比べてはるかにさらに。 2 つを実装することができます*Web フォーム ページのセットを完全に分離*– 1 つ別デスクトップ ブラウザーの設定、モバイルの設定。 これは最適をモバイル閲覧者に非常に異なる情報またはワークフローを表示する場合です。 このセクションの残りの部分では、この方法の詳細について説明します。

デスクトップ ブラウザー用に設計された Web フォーム アプリケーションが既にあると仮定した場合、続行する最も簡単な方法をプロジェクト内で「モバイル」をという名前のサブフォルダーを作成し、モバイル ページがありますを構築します。 すべてが同じ他の Web フォーム アプリケーションで使用する手法を使用して、独自のマスター ページ、スタイル シート、およびページで、全体のサブ サイトを構築できます。 モバイルに相当するを生成するために必ずしも必要*すべて*デスクトップ サイトのページ以外の場合はどのような機能のサブセットが納得できるモバイル閲覧者を選択できます。

モバイル ページは、共通の静的なリソースを共有できます (イメージ、JavaScript、CSS などのファイル) とする場合、通常のページ。 「モバイル」フォルダー*いない*マークする、別のアプリケーションとして (Web フォーム ページの単純なサブフォルダーだけである)、IIS でホストされている場合は共有もすべてが同じ構成、セッション データ、およびその他のインフラストラクチャとして、デスクトップのページです。

> [!NOTE]
> このアプローチには、通常のコードの一部の重複が含まれているので (モバイル ページにはデスクトップ ページといくつかの類似点を共有する可能性の高い、)、一般的なビジネス ロジックまたはデータ アクセス コード共有の基になるレイヤーまたはサービスに排除することが重要です。 それ以外の場合、倍精度浮動小数点を作成して、アプリケーションの保守作業があります。


#### <a name="redirecting-mobile-visitors-to-your-mobile-pages"></a>モバイル ページをモバイル閲覧者のリダイレクト

のみモバイル ページをモバイル閲覧者をリダイレクトすると便利なことがよくあります、*最初*のために、参照しているセッションで、自身のセッションですべての要求ではなく) を要求します。

- リンクを「デスクトップ バージョン」に移動するマスター ページだけ置く – 希望する場合、デスクトップのページにアクセスするモバイル訪問者を簡単に許可できます。 訪問者は、ことがなくなったため、最初の要求自身のセッションで、モバイル ページにリダイレクトされません。
- (IFRAME、または特定の Ajax ハンドラーに、サイトのデスクトップとモバイルの両方のパーツを表示する一般的な Web フォームがある) 場合などに、サイトのデスクトップおよびモバイルの部分の間で共有するすべての動的なリソースに対する要求に干渉することのリスクを回避できます。

これを行うには、リダイレクト ロジックを配置することができます、**セッション\_開始**メソッドです。 たとえば、Global.asax.cs ファイルに次のメソッドを追加します。

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample2.cs)]

#### <a name="configuring-forms-authentication-to-respect-your-mobile-pages"></a>モバイル ページを尊重するフォーム認証を構成します。

訪問者をリダイレクトと、認証プロセスの中に、場所に関する仮定がフォーム認証を行うことに注意してください。

- ユーザーを認証する場合、フォーム認証をリダイレクトするそれらデスクトップまたはモバイル ユーザーいるかどうかに関係なく、デスクトップ ログイン ページ (だけの概念があるため*1*ログイン URL)。 モバイル ユーザーを別のモバイル ログイン ページにリダイレクトできるように、デスクトップのログイン ページを強化するために、モバイルのログイン ページを異なる方法でスタイルを設定すると仮定すると、必要があります。 たとえば、次のコードを追加、**デスクトップ**ログイン ページのコード ビハインド。 

    [!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample3.cs)]
- ユーザーがログインに成功、フォーム認証は既定ではリダイレクトに、デスクトップのホーム ページに (だけの概念があるため*1*既定のページ)。 成功したログの後に、モバイルのホーム ページにリダイレクトできるように、モバイルのログイン ページを強化する必要があります。 たとえば、次のコードを追加、**モバイル**ログイン ページのコード ビハインド。 

    [!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample4.cs)]
  
 このコードでは、ページに既定のプロジェクト テンプレートと同様に、LoginUser と呼ばれるログイン サーバー コントロールが前提としています。

### <a name="working-with-output-caching"></a>出力キャッシュの使用

出力キャッシュを使用している場合は、キャッシュされたデスクトップの出力を受信するモバイル ユーザー続く既定ではデスクトップのユーザー (キャッシュに保存するには、その出力の原因と)、特定の URL に移動することも可能である、ことに注意してください。 この警告は、さまざまなデバイスの種類で、マスター ページまたはデバイスの種類ごとにまったく別の Web フォームを実装しているだけかどうかを適用します。

この問題を避けるためには、訪問者がモバイル デバイスを使用しているかどうかに従って、キャッシュ エントリを変更するための ASP.NET に指示できます。 ページの OutputCache 宣言によう VaryByCustom パラメーターを追加します。

[!code-aspx[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample5.aspx)]

次に、定義*isMobileDevice* Global.asax.cs ファイルに次のメソッドを追加することでパラメーターを上書きするカスタム キャッシュとして。

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample6.cs)]

ページのモバイル訪問者がデスクトップの訪問者によってキャッシュに格納した出力を受信しないようになります。

### <a name="a-working-example"></a>実際の例

これらの手法の動作を表示するには、ダウンロード[このホワイト ペーパーのコード サンプル](https://docs.microsoft.com/aspnet/mobile/overview)です。 Web フォーム サンプル アプリケーションは、Mobile をという名前のサブフォルダー内の特定のモバイル ページのセットにモバイル ユーザーを自動的にリダイレクトします。 マークアップとそれらのページのスタイルがより適切に最適化モバイル ブラウザーは、次のスクリーン ショットからわかることができます。

![](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/_static/image2.png)

モバイル ブラウザー用のマークアップと CSS の最適化に関する詳細については、「スタイリング モバイル ページ モバイル ブラウザーの」このドキュメントの後半のセクションを参照してください。

## <a name="how-aspnet-mvc-applications-can-present-mobile-specific-pages"></a>ASP.NET MVC アプリケーションがモバイル固有のページを表示する方法

モデル ビュー コント ローラー パターンでは、アプリケーション ロジックを (コント ローラー) (ビュー) でのプレゼンテーション ロジックからを切り離して、ために、サーバー側コードでモバイル デバイスのサポートを処理するように、次のアプローチのいずれかから選択できます。

1. ***デスクトップとモバイルの両方のブラウザーでは、同じコント ローラーとビューを使用してが、デバイスの種類 * に応じてさまざまな Razor レイアウトでビューをレンダリングします。** このオプションは、単に別の CSS スタイル シートを指定するか、モバイルの最上位の HTML 要素をいくつかを変更する場合、すべてのデバイス上の同一のデータを表示している場合に最適です。
2. ***デスクトップとモバイルの両方のブラウザーでは、同じコント ローラーを使用して、デバイスの種類に応じて異なるビューをレンダリング***です。 ほぼ同じデータを表示するエンドユーザーのため、同じワークフローを提供する場合、このオプションは最適が使用されているデバイスに合わせて、非常に異なる HTML マークアップを表示するためにします。
3. ***デスクトップとモバイル ブラウザーは、それぞれを独立したコント ローラーとビューを実装する独立した領域の作成 * です。** このオプションは、非常にさまざまな画面を表示する、さまざまな情報を含むおよび先頭のユーザー、デバイスの種類用に最適化されたさまざまなワークフローを実行している場合に最適です。 コードの一部の繰り返しがありますが、基になるレイヤーまたはサービスを一般的なロジックをファクタリングする最小化できます。

実行する場合、**最初**オプションを選択し、Razor レイアウトのみを異なるデバイスの種類、あたりは非常に簡単です。 変更するだけ、 \_ViewStart.cshtml ファイルの次のようにします。

[!code-cshtml[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample7.cshtml)]

呼ばれるモバイル固有のレイアウトを作成できるようになりました\_ページ構造を持つ LayoutMobile.cshtml と CSS 規則をモバイル デバイス用に最適化します。

実行する場合、 **2 番目**訪問者のデバイスの種類に応じてまったく異なるビューをレンダリングをオプションを参照してください[Scott Hanselman のブログの投稿](http://www.hanselman.com/blog/ABetterASPNETMVCMobileDeviceCapabilitiesViewEngine.aspx)です。

このホワイト ペーパーの残りの部分に焦点、 **3 番目**オプション – 別のコント ローラーを作成する*と*正確にどのような機能のサブセットが表示されます。 モバイル閲覧者を管理することがモバイル デバイスのビューです。

### <a name="setting-up-a-mobile-area-within-your-aspnet-mvc-application"></a>ASP.NET MVC アプリケーション内でのモバイル領域の設定

通常の方法で既存の ASP.NET MVC アプリケーションには、「モバイル」と呼ばれる部分を追加することができます。 ソリューション エクスプ ローラーでプロジェクト名を右クリックし、追加 à 領域を選択します。 ASP.NET MVC アプリケーション内の他の任意の領域の場合と同様、し、コント ローラーとビューを追加することができます。 モバイル閲覧者のホームページとしてテンプレートを使用すると呼ばれる新しいコント ローラーをモバイル領域など、追加します。

### <a name="ensuring-the-url-mobile-reaches-the-mobile-homepage"></a>モバイルのホームページに達すると URL/Mobile を確保します。

URL/Mobile HomeController のインデックスの操作をモバイルの領域の内側に到達する場合は、ルーティング構成に次の 2 つの小さな変更を加える必要があります。 最初に、更新、MobileAreaRegistration クラス テンプレートを使用するが、モバイル領域で、既定のコント ローラーになるよう、次のコードに示すように。

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample8.cs)]

つまり、モバイルのホーム ページは今すぐに配置される家庭用/Mobile ではなく/Mobile、「ホーム」がであるため、モバイル ページ コント ローラー名を暗黙的に既定値です。

次に、こと、2 番目のテンプレートを使用するアプリケーション (つまり、モバイルもの、既存のデスクトップ 1 つだけでなく) に追加すると、ありますが破損した正規デスクトップ ブラウザーのホーム ページに注意してください。 これは、エラーで失敗"*'Home' という名前のコント ローラーに一致する複数の種類が見つかりました*"です。 これを解決するには、優先を指定する、デスクトップ テンプレートを使用する必要がありますあいまいさがある場合に (Global.asax.cs) で、最上位レベルのルーティングの構成を更新します。

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample9.cs)]

退席中、および URL http:// エラーになるようになりました*目的のサイト*http://、デスクトップのホーム ページに到達/*目的のサイト*/mobile/はモバイルのホーム ページにアクセスされます。

### <a name="redirecting-mobile-visitors-to-your-mobile-area"></a>モバイルお住まいの地域をモバイル閲覧者のリダイレクト

多くのさまざまな機能拡張ポイントがある ASP.NET mvc でリダイレクト ロジックを組み込む可能な方法はたくさんありますようにします。 1 つの便利なオプションは、属性を作成するフィルターを [RedirectMobileDevicesToMobileArea]、次の条件が満たされた場合、リダイレクトを実行するには。

1. ユーザーのセッションでは最初の要求 (つまり、Session.IsNewSession が true に等しい)
2. モバイル ブラウザーから、要求を受け取った (つまり、Request.Browser.IsMobileDevice が true に等しい)
3. ユーザー、モバイルの領域内のリソースが既に要求されていない (つまり、*パス*URL の一部で始まらない/Mobile)

このホワイト ペーパーに含まれているダウンロード可能なサンプルには、このロジックの実装が含まれています。 (それ以外の場合場合デスクトップ ビジター最初アクセス特定の URL、デスクトップの出力をキャッシュおよびに提供し、可能性がありますも出力キャッシュが使用している場合でもに正しく処理できることを意味する AuthorizeAttribute から派生した、承認フィルターとして実装されます。その後モバイル ユーザーの場合)。

フィルターは、ことができますに特定のコント ローラーとアクションを適用するいずれかの例。

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample10.cs)]

… MVC 3 としてすべてのコント ローラーとアクションに適用できるまたは*グローバル フィルター* Global.asax.cs ファイルに。

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample11.cs)]

ダウンロード可能なサンプルでは、モバイル、領域内の特定の場所にリダイレクトする、この属性のサブクラスを作成する方法も示します。 つまり、たとえば、することができます。

- グローバル フィルターとして表示されている上記を既定では、モバイルのホームページをモバイル閲覧者に送信を登録します。
- 特殊な [RedirectMobileDevicesToMobileProductPage] フィルターをモバイル閲覧者が要求した任意の製品ページのモバイル バージョンには、"ビュー product"アクションにも適用されます。
- フィルターの他の特殊なサブクラスをモバイル閲覧者と同じモバイル ページにリダイレクトする、特定の操作にも適用します。

### <a name="configuring-forms-authentication-to-respect-your-mobile-pages"></a>モバイル ページを尊重するフォーム認証を構成します。

フォーム認証を使用している場合、ことのログインに、ユーザーに必要なときに、自動的にはユーザーをリダイレクト、1 つ特定「ログオン」の URL、既定では注意してください**/アカウント/ログオン**です。 つまり、デスクトップ「ログオン」アクションにモバイル ユーザーをリダイレクトする場合があります。

この問題を回避するには、モバイル「ログオン」アクションをモバイル ユーザーに再びリダイレクトできるように、デスクトップのログオン アクションにロジックを追加します。 既定の ASP.NET MVC アプリケーション テンプレートを使用している場合は、次のように AccountController のログオン操作を更新します。

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample12.cs)]

… モバイルお住まいの地域で AccountController と呼ばれるコント ローラーで適切なモバイル固有「ログオン」アクションを実装します。

### <a name="working-with-output-caching"></a>出力キャッシュの使用

[OutputCache] フィルターを使用している場合は、デバイスの種類によって異なるキャッシュ エントリを強制する必要があります。 たとえば、次のように記述します。

[!code-javascript[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample13.js)]

その後、Global.asax.cs ファイルに次のメソッドを追加します。

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample14.cs)]

ページのモバイル訪問者がデスクトップの訪問者によってキャッシュに格納した出力を受信しないようになります。

### <a name="a-working-example"></a>実際の例

これらの手法の動作を表示するには、ダウンロード[このホワイト ペーパーのコードには、サンプルが関連付けられている](https://docs.microsoft.com/aspnet/mobile/overview)です。 このサンプルには、上記の方法を使用してモバイル デバイスをサポートするために強化された ASP.NET MVC 3 (リリース候補) アプリケーションが含まれています。

## <a name="further-guidance-and-suggestions"></a>さらにガイダンスと推奨事項

次の説明には、Web フォームと MVC の開発者がこのドキュメントで説明する方法を使用している両方が適用されます。

### <a name="enhancing-your-redirection-logic-using-51degreesmobi-foundation"></a>51Degrees.mobi Foundation を使用して、リダイレクトのロジックを強化

このドキュメントで示すリダイレクト ロジックは、アプリケーションの完全に十分な可能性がありますが、セッションを無効にする必要がある場合は機能しませんか、または特定の要求かどうかがわかりません (これらは、セッションを持つことはできません)、cookie を却下するモバイル ブラウザーでは、そのビジターから最初の 1 つ。

既に、オープン ソース 51Degrees.mobi Foundation が ASP の精度を向上する方法について説明しました。NET のブラウザーの検出。 Web.config で構成されている特定の場所をモバイル閲覧者のリダイレクトを使用できる、組み込みの機能もあります。によっては、ASP.NET セッションがないと機能することができます (つまり cookie) 訪問者の HTTP ヘッダーと IP アドレスのハッシュの一時的なログを保存すると、それが認識できるように各要求に指定された vistor から最初の 1 つがかどうか。

次の要素を web.config ファイルの fiftyOne セクションに追加のページに検出されたモバイル デバイスから、最初の要求がリダイレクト ~/Mobile/Default.aspx です。 モバイルのフォルダーの下のページへの要求は*いない*デバイスの種類に関係なく、リダイレクトされます。 モバイル デバイスが非アクティブの状態期間忘れては 20 分または複数のデバイスのし、後続の要求がリダイレクトのために新しいものとして扱われます。

[!code-xml[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample15.xml)]

詳細については、次を参照してください。 [51degrees.mobi Foundation ドキュメント](https://github.com/51Degrees/dotNET-Device-Detection)です。

> [!NOTE]
> *できます*51Degrees.mobi Foundation のリダイレクト機能が ASP.NET MVC アプリケーションではプレーンな Url、ルーティングのパラメーターは使用しませんか、または配置 MVC フィルターの観点から、リダイレクトの構成を定義する必要がありますで操作します。 これは、ため (ドキュメントの執筆時) フィルターまたはルーティング 51Degrees.mobi Foundation が認識されません。


### <a name="disabling-transcoders-and-proxy-servers"></a>各トランスコーダーとプロキシ サーバーを無効にします。

モバイルのインターネットへのアプローチで 2 つの広範な目標があるモバイル ネットワーク オペレーター。

1. できるだけはるかに関連するコンテンツとして提供します。
2. 制限付きの無線ネットワークの帯域幅を共有する顧客の数を最大化します。

ほとんどの web ページは、デスクトップ サイズの大きな画面と固定行高速ブロード バンド接続用に設計された、ため演算子の多くを使用して*トランスコーダーが*または*プロキシ サーバー* web コンテンツを動的に変更します。 小さい画面に合わせて (特に"機能フォン用"複雑なレイアウトを処理する処理能力がない)、CSS、HTML マークアップに変更される可能性があり、イメージを (それらの品質を大幅に下げること) を再圧縮 ページの配信速度を向上させるために、可能性があります。

サイトのモバイルに最適化されたバージョンを生成するために努力を取得できたので、おそらくたくない場合に干渉すること、さらに、ネットワーク演算子。 次の行を追加するには、ページに\_ASP.NET Web フォームでの Load イベント。

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample16.cs)]

または、そのコント ローラー上のすべてのアクションに適用されるように ASP.NET MVC のコント ローラーに次のメソッドのオーバーライドを追加する可能性があります。

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample17.cs)]

結果の HTTP メッセージは、W3C トランスコーダーが準拠しているとコンテンツを変更するには、いないプロキシに通知します。 もちろん、モバイル ネットワーク オペレーターがこのメッセージを尊重する保証はありません。

### <a name="styling-mobile-pages-for-mobile-browsers"></a>モバイル ブラウザー用にモバイル ページのスタイルを設定

詳しく説明するには、このドキュメントの範囲外の HTML マークアップの作業の種類正しくやな web デザインの手法は、特定のデバイスでの操作性を最大化です。 十分に簡単なレイアウトを検索するために用に最適化 mobile サイズの画面では、信頼性の低いを使用せず HTML または CSS トリックを配置します。 留意、1 つの重要な手法が、*ビューポートのメタ タグ*です。

ブラウザーによっては最近モバイル、デスクトップのモニタのことを意図した工数表示 web ページで「ビューポート」とも呼ばれる仮想キャンバス上のページを表示する (たとえば、仮想ビューポートは 850 ピクセル Opera Mobile で既定では、iphone、幅 980 ピクセル) し、画面の物理的なピクセルに合わせて、結果のスケール ダウンします。 ユーザー ズーム インし、そのビューポート パンします。 これは、利点は、ブラウザーのレイアウトでは、意図した、ページを表示できますも欠点も強制的ズームとパン、ユーザーにとって便利ではないです。 Mobile をデザインする場合お勧めデザインに狭い画面のズームまたは水平方向にスクロールは必要ありませんようにします。

ビューポートは幅べきモバイル ブラウザーに指示する方法は、非標準*ビューポート*メタ タグ。 たとえば、ページの先頭のセクションに、次を追加します。

[!code-html[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample18.html)]

… 480 ピクセル全体の仮想キャンバス上のページをレイアウトは smartphone ブラウザーをサポートします。 これは、HTML 要素は、割合の幅を定義する場合、割合はこの 480 ピクセル幅、既定のビューポートの幅されませんに関して解釈することを意味します。 その結果、ユーザーはズームとパン水平方向に – かなりモバイル ブラウザー エクスペリエンスを向上する可能性は低くです。

ビューポートの幅、デバイスの物理的なピクセルに一致する場合は、次を指定できます。

[!code-html[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample19.html)]

これが正しく機能するには、する必要があります明示的に強制しませんをその幅を超える要素 (などを使用して、*幅*属性または CSS プロパティ)、それ以外の場合、ブラウザーが強制されるようにに関係なく大きいビューポートを使用します。 関連項目:[の詳細については、非標準のビューポート タグ](https://developer.apple.com/library/safari/#documentation/AppleApplications/Reference/SafariHTMLRef/Articles/MetaTags.html)です。

最近のほとんどのスマート フォンのサポート*デュアル向き*: 縦または横モードではそれらを保持できます。 そのため、することが重要いないようにについて想定する画面の幅 (ピクセル単位) です。 でもと仮定しないで画面の幅が固定されているため、ページになっている間に、ユーザーは自分のデバイスを回転再ことができます。

また場合があります古い Windows Mobile および Blackberry デバイスを使用するは、それらのコンテンツを通知するために、ページ ヘッダーには、次のメタ タグ mobile に対して最適化されているし、したがっては変換されません。

[!code-html[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample20.html)]

## <a name="additional-resources"></a>その他のリソース

モバイル デバイス エミュレーターやシミュレーターのモバイルの ASP.NET web アプリケーションをテストに使用できるの一覧は、ページを参照してください。[テスト用の一般的なモバイル デバイスをシミュレート](../mobile/device-simulators.md)です。

## <a name="credits"></a>謝辞

- プライマリ作成者: Steven Sanderson
- レビュー担当者/追加コンテンツの作成者: James Rosewell、Mikael Söderström、Scott Hanselman、Scott ハンター
