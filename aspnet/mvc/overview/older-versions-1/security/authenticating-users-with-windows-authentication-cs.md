---
uid: mvc/overview/older-versions-1/security/authenticating-users-with-windows-authentication-cs
title: "Windows 認証 (c#) を使用してユーザーを認証 |Microsoft ドキュメント"
author: microsoft
description: "MVC アプリケーションのコンテキストで Windows 認証を使用する方法を説明します。 アプリケーションの web co 内で Windows 認証を有効にする方法を学習するとしています."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/27/2009
ms.topic: article
ms.assetid: 418bb07e-f369-4119-b4b0-08f890f7abb2
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/security/authenticating-users-with-windows-authentication-cs
msc.type: authoredcontent
ms.openlocfilehash: d52597e65272fa202ef4980924f669dcc4cec593
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2018
---
<a name="authenticating-users-with-windows-authentication-c"></a>Windows 認証 (c#) を使用してユーザーを認証
====================
によって[Microsoft](https://github.com/microsoft)

> MVC アプリケーションのコンテキストで Windows 認証を使用する方法を説明します。 IIS で認証を構成する方法と、アプリケーションの web 構成ファイル内で Windows 認証を有効にする方法を説明します。 最後に、[Authorize] 属性を使用して、特定の Windows ユーザーまたはグループにコント ローラー アクションへのアクセスを制限する方法を説明します。


このチュートリアルの目標は、利用を実行できる方法について説明する、セキュリティのパスワードには、インターネット インフォメーション サービスに組み込まれている機能は、MVC アプリケーション内のビューを保護します。 特定の Windows ユーザーまたは特定の Windows グループのメンバーであるユーザーによってのみ呼び出されるコント ローラーのアクションを許可する方法を学びます。

Windows 認証を使用して意味は会社の内部 web サイト (イントラネット サイト) を作成して、ユーザーが、web サイトにアクセスするときに、標準の Windows ユーザー名とパスワードを使用できるようにするときにします。 Web サイト (インターネット web サイト) が直面している、外側へを構築する場合は、フォーム認証の代わりに使用を検討してください。

#### <a name="enabling-windows-authentication"></a>Windows 認証を有効にします。

新しい ASP.NET MVC アプリケーションを作成するときに、Windows 認証は既定では無効です。 フォーム認証は、MVC アプリケーションを有効になっている既定の認証の種類です。 MVC アプリケーションの web の構成 (web.config) ファイルを変更して、Windows 認証を有効にする必要があります。 検索、&lt;認証&gt;セクションし、次のようにフォーム認証ではなく Windows を使用するように変更します。

[!code-xml[Main](authenticating-users-with-windows-authentication-cs/samples/sample1.xml)]

Windows 認証を有効にすると、web サーバーはユーザーの認証を担当になります。 通常、2 つの異なる種類の作成して、ASP.NET MVC アプリケーションを展開するときに使用する web サーバーがあります。

最初に、MVC アプリケーションの開発中には、Visual Studio に付属する ASP.NET 開発 Web サーバーを使用します。 既定では、ASP.NET 開発 Web サーバーは、現在の Windows アカウント (任意のアカウントが Windows にログオンするため) のコンテキストですべてのページを実行します。

ASP.NET 開発 Web サーバーには、NTLM 認証もサポートしています。 NTLM 認証を有効にするには、ソリューション エクスプ ローラー ウィンドウで、プロジェクトの名前を右クリックし、プロパティ を選択します。 次に、[Web] タブを選択し、NTLM のチェック ボックスをオン (図 1 を参照してください)。

**図 1 – 有効にする ASP.NET 開発 Web サーバーの NTLM 認証**

![clip_image002](authenticating-users-with-windows-authentication-cs/_static/image1.jpg)

実稼働 web アプリケーションの場合、手の形でとして使用する IIS web サーバー。 IIS では、認証などのいくつかの種類をサポートします。

- 基本認証 – は、HTTP 1.0 プロトコルの一部として定義されます。 ユーザー名とパスワードをクリア テキスト (Base64 エンコード) で、インターネット経由で送信します。 ダイジェスト認証 – は、インターネット経由でそれ自体には、パスワードの代わりに、パスワードのハッシュを送信します。 -統合された Windows (NTLM) 認証 – windows を使用するイントラネット環境で使用する認証の最適な型です。 -証明書の認証: クライアント側の証明書を使用して認証を有効します。 証明書は、Windows ユーザー アカウントにマップされます。

> [!NOTE] 
> 
> これらのさまざまな種類の認証の詳細については、次を参照してください[https://msdn.microsoft.com/library/aa292114(VS.71).aspx](https://msdn.microsoft.com/library/aa292114(VS.71).aspx).。


インターネット インフォメーション サービス マネージャーを使用して、特定の種類の認証を有効にすることができます。 すべての種類の認証はすべてのオペレーティング システムの場合は使用できないことに注意してください。 さらに、Windows vista では IIS 7.0 を使用している場合は、インターネット インフォメーション サービス マネージャーに表示される前に、さまざまな種類の Windows 認証を有効にする必要があります。 開いている**コントロール パネル、プログラム、プログラムと機能、Windows の機能のオンまたはオフ**、インターネット インフォメーション サービス ノードを展開し、(図 2 を参照してください)。

**図 2 – 有効にする Windows の IIS の機能**

![clip_image004](authenticating-users-with-windows-authentication-cs/_static/image2.jpg)

インターネット インフォメーション サービスを使用して、有効にするにまたは、さまざまな種類の認証を無効にできます。 たとえば、図 3 を示しています無効にすると匿名認証と統合 Windows (NTLM) 認証を有効にすると IIS 7.0 を使用する場合。

**図 3: 統合 Windows 認証を有効にします。**

![clip_image006](authenticating-users-with-windows-authentication-cs/_static/image3.jpg)

#### <a name="authorizing-windows-users-and-groups"></a>Windows を承認するユーザーとグループ

Windows 認証を有効にした後は、コント ローラーのアクションまたはコント ローラーへのアクセスを制御するのに [Authorize] 属性を使用することができます。 この属性は、全体の MVC コント ローラーまたは特定のコント ローラーのアクションに適用できます。

たとえば、1 の一覧で、Home コント ローラーは、Index()、CompanySecrets()、および StephenSecrets() という 3 つのアクションを公開します。 Index() アクションだれでも呼び出すことができます。 ただし、Windows のローカル管理者グループのメンバーだけでは、CompanySecrets() アクションを呼び出すことができます。 最後に、のみ、Windows ドメイン ユーザー (Redmond ドメイン) 内の Stephen をという名前では、StephenSecrets() アクションを呼び出すことができます。

**1 – controllers \homecontroller.cs を一覧表示します。**

[!code-csharp[Main](authenticating-users-with-windows-authentication-cs/samples/sample2.cs)]

> [!NOTE] 
> 
> ため Windows ユーザー アカウント制御 (UAC)、Windows Vista または Windows Server 2008 を使用する場合、ローカルの Administrators グループの他のグループとは異なる動作が。 [Authorize] 属性、コンピューターの UAC 設定を変更する場合を除き、ローカルの Administrators グループのメンバーが正しく認識されません。


適切なアクセス許可がないコント ローラーのアクションを起動するときの動作については、有効になっている認証の種類によって異なります。 既定では、ASP.NET 開発サーバーを使用する場合、単に空白のページを取得します。 ページが表示されると、 **401 が承認されていない**HTTP 応答のステータス。

かどうか、その一方で、無効になっている匿名認証と基本認証を有効にすると、IIS を使用しているし、保護されているページを要求するたびに、ログイン ダイアログ プロンプト続けて表示 (図 4 を参照してください)。

**図 4-基本認証のログイン ダイアログ ボックス**

![clip_image008](authenticating-users-with-windows-authentication-cs/_static/image4.jpg)

#### <a name="summary"></a>まとめ

このチュートリアルでは、ASP.NET MVC アプリケーションのコンテキストで Windows 認証を使用する方法について説明します。 IIS で認証を構成する方法と、アプリケーションの web 構成ファイル内で Windows 認証を有効にする方法を学習しました。 最後に、[Authorize] 属性を使用して、特定の Windows ユーザーまたはグループにコント ローラー アクションへのアクセスを制限する方法を学習します。

>[!div class="step-by-step"]
[前へ](authenticating-users-with-forms-authentication-cs.md)
[次へ](preventing-javascript-injection-attacks-cs.md)
