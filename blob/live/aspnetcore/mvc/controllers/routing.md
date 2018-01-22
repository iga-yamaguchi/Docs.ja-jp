---
title: "コント ローラー アクションへのルーティング"
author: rick-anderson
description: 
ms.author: riande
manager: wpickett
ms.date: 03/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/controllers/routing
ms.openlocfilehash: 7559fa270a012082d04161c1cccd1dc8151d0c1c
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2018
---
# <a name="routing-to-controller-actions"></a><span data-ttu-id="4236f-102">コント ローラー アクションへのルーティング</span><span class="sxs-lookup"><span data-stu-id="4236f-102">Routing to Controller Actions</span></span>

<span data-ttu-id="4236f-103">によって[Ryan Nowak](https://github.com/rynowak)と[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4236f-103">By [Ryan Nowak](https://github.com/rynowak) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4236f-104">ASP.NET Core MVC ルーティングを使用して[ミドルウェア](../../fundamentals/middleware.md)に受信した要求の Url と一致し、アクションにマップします。</span><span class="sxs-lookup"><span data-stu-id="4236f-104">ASP.NET Core MVC uses the Routing [middleware](../../fundamentals/middleware.md) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="4236f-105">ルートは、スタートアップ コードまたは属性で定義されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-105">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="4236f-106">ルートは、アクションへの URL パスの照合方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4236f-106">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="4236f-107">ルートは、応答で送信 (リンク用) の Url を生成するも使われます。</span><span class="sxs-lookup"><span data-stu-id="4236f-107">Routes are also used to generate URLs (for links) sent out in responses.</span></span> 

<span data-ttu-id="4236f-108">アクションは従来どおりルーティングするか、または属性にルーティングします。</span><span class="sxs-lookup"><span data-stu-id="4236f-108">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="4236f-109">コント ローラーまたはアクションのルートを配置すると、その属性がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="4236f-109">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="4236f-110">参照してください[ルーティングの混在](#routing-mixed-ref-label)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="4236f-110">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="4236f-111">このドキュメントでは、MVC とルーティング、およびどの標準的な MVC アプリの作成の間のやり取りを説明します。 ルーティング機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="4236f-111">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="4236f-112">参照してください[ルーティング](xref:fundamentals/routing)高度なルーティングの詳細。</span><span class="sxs-lookup"><span data-stu-id="4236f-112">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="4236f-113">ミドルウェアのルーティングを設定します。</span><span class="sxs-lookup"><span data-stu-id="4236f-113">Setting up Routing Middleware</span></span>

<span data-ttu-id="4236f-114">*構成*方法のようなコードを確認する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4236f-114">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="4236f-115">呼び出しの内部`UseMvc`、`MapRoute`と呼びますが単一のルートの作成に使用される、`default`ルート。</span><span class="sxs-lookup"><span data-stu-id="4236f-115">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="4236f-116">ほとんどのアプリで MVC はのようなテンプレートでルートを使用、`default`ルート。</span><span class="sxs-lookup"><span data-stu-id="4236f-116">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="4236f-117">ルート テンプレート`"{controller=Home}/{action=Index}/{id?}"`のような URL パスを照合できる`/Products/Details/5`およびルート値を抽出`{ controller = Products, action = Details, id = 5 }`でパスをトークン化します。</span><span class="sxs-lookup"><span data-stu-id="4236f-117">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="4236f-118">MVC はコント ローラーがという名前を検索を試みます`ProductsController`アクションを実行および`Details`:</span><span class="sxs-lookup"><span data-stu-id="4236f-118">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="4236f-119">この例ではモデル バインディングが使用するようの値`id = 5`を設定する、`id`パラメーターを`5`この操作を呼び出すときにします。</span><span class="sxs-lookup"><span data-stu-id="4236f-119">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="4236f-120">参照してください、[モデル バインド](../models/model-binding.md)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="4236f-120">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="4236f-121">使用して、`default`ルート。</span><span class="sxs-lookup"><span data-stu-id="4236f-121">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="4236f-122">ルート テンプレート。</span><span class="sxs-lookup"><span data-stu-id="4236f-122">The route template:</span></span>

* <span data-ttu-id="4236f-123">`{controller=Home}`定義`Home`既定値として`controller`</span><span class="sxs-lookup"><span data-stu-id="4236f-123">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="4236f-124">`{action=Index}`定義`Index`既定値として`action`</span><span class="sxs-lookup"><span data-stu-id="4236f-124">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="4236f-125">`{id?}`定義`id`オプションとして</span><span class="sxs-lookup"><span data-stu-id="4236f-125">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="4236f-126">既定値と省略可能なルートのパラメーターは、一致の URL パスに存在する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4236f-126">Default and optional route parameters do not need to be present in the URL path for a match.</span></span> <span data-ttu-id="4236f-127">参照してください[ルート テンプレート参照](../../fundamentals/routing.md#route-template-reference)ルート テンプレートの構文の詳細な説明をします。</span><span class="sxs-lookup"><span data-stu-id="4236f-127">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="4236f-128">`"{controller=Home}/{action=Index}/{id?}"`URL パスを照合できる`/`ルートの値を生成および`{ controller = Home, action = Index }`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-128">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="4236f-129">値は、`controller`と`action`ように既定値の使用`id`URL パスに対応するセグメントが存在しないために、値を生成しません。</span><span class="sxs-lookup"><span data-stu-id="4236f-129">The values for `controller` and `action` make use of the default values, `id` does not produce a value since there is no corresponding segment in the URL path.</span></span> <span data-ttu-id="4236f-130">MVC ではこれらのルート値を使用して、`HomeController`と`Index`アクション。</span><span class="sxs-lookup"><span data-stu-id="4236f-130">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="4236f-131">このコント ローラーの定義とルート テンプレートを使用して、`HomeController.Index`次の URL パスのいずれかのアクションが実行されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-131">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="4236f-132">便利なメソッド`UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="4236f-132">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="4236f-133">置換に使用できます。</span><span class="sxs-lookup"><span data-stu-id="4236f-133">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="4236f-134">`UseMvc`および`UseMvcWithDefaultRoute`のインスタンスに追加`RouterMiddleware`ミドルウェア パイプラインにします。</span><span class="sxs-lookup"><span data-stu-id="4236f-134">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="4236f-135">MVC は、ミドルウェアと直接やり取りしませんし、要求を処理するルーティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="4236f-135">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="4236f-136">MVC がのインスタンスを使用して、ルートに接続されている`MvcRouteHandler`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-136">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="4236f-137">内のコード`UseMvc`は、次に似ています。</span><span class="sxs-lookup"><span data-stu-id="4236f-137">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="4236f-138">`UseMvc`すべてのルートを直接定義されていません、プレース ホルダーのルートのコレクションに追加、`attribute`ルート。</span><span class="sxs-lookup"><span data-stu-id="4236f-138">`UseMvc` does not directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="4236f-139">オーバー ロードは、`UseMvc(Action<IRouteBuilder>)`を使用して、独自のルートを追加し、属性のルーティングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="4236f-139">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="4236f-140">`UseMvc`および属性ルートのプレース ホルダーが追加のすべてのバリエーションの - 属性のルーティングの構成方法に関係なく使用可能なは常に`UseMvc`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-140">`UseMvc` and all of its variations adds a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="4236f-141">`UseMvcWithDefaultRoute`既定のルートを定義し、属性のルーティングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="4236f-141">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="4236f-142">[属性がルーティング](#attribute-routing-ref-label)セクションでは、属性のルーティングの詳細についてを説明します。</span><span class="sxs-lookup"><span data-stu-id="4236f-142">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="4236f-143">従来のルーティング</span><span class="sxs-lookup"><span data-stu-id="4236f-143">Conventional routing</span></span>

<span data-ttu-id="4236f-144">`default`ルート。</span><span class="sxs-lookup"><span data-stu-id="4236f-144">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="4236f-145">例を示します、*従来のルーティング*です。</span><span class="sxs-lookup"><span data-stu-id="4236f-145">is an example of a *conventional routing*.</span></span> <span data-ttu-id="4236f-146">このスタイルを呼び出して*従来のルーティング*を作成できるので、*規約*の URL パスの場合。</span><span class="sxs-lookup"><span data-stu-id="4236f-146">We call this style *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="4236f-147">最初のパス セグメントは、コント ローラー名にマップします。</span><span class="sxs-lookup"><span data-stu-id="4236f-147">the first path segment maps to the controller name</span></span>

* <span data-ttu-id="4236f-148">2 つ目は、アクション名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="4236f-148">the second maps to the action name.</span></span>

* <span data-ttu-id="4236f-149">3 番目のセグメントは省略可能なための`id`モデルのエンティティにマップします。</span><span class="sxs-lookup"><span data-stu-id="4236f-149">the third segment is used for an optional `id` used to map to a model entity</span></span>

<span data-ttu-id="4236f-150">これを使用して`default`ルート、URL path`/Products/List`にマップ、`ProductsController.List`アクション、および`/Blog/Article/17`にマップ`BlogController.Article`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-150">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="4236f-151">このマッピングは、コント ローラーとアクションの名前に基づいて**のみ**名前空間、ソース ファイルの場所、またはメソッド パラメーターには基づいていません。</span><span class="sxs-lookup"><span data-stu-id="4236f-151">This mapping is based on the controller and action names **only** and is not based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="4236f-152">既定のルートで従来のルーティングを使用するを定義する各アクションの新しい URL パターンを考えつくしなくても、アプリケーションをすばやく構築することができます。</span><span class="sxs-lookup"><span data-stu-id="4236f-152">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="4236f-153">CRUD スタイルのアクションがあるアプリケーション、Url のコント ローラー間で一貫性を持つことができますをコードを容易にし、UI をより予測可能なためです。</span><span class="sxs-lookup"><span data-stu-id="4236f-153">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="4236f-154">`id`は省略可能なによって定義されたルート テンプレート、URL の一部として提供された ID を持たないユーザーの操作を実行できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="4236f-154">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="4236f-155">通常何が起こる場合`id`URL が省略されているに設定することは、`0`結果として、データベースの照合にエンティティがないと、モデル バインディングによって`id == 0`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-155">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="4236f-156">属性のルーティングすれば、および他のいくつかのアクションに必要な ID の詳細に制御します。</span><span class="sxs-lookup"><span data-stu-id="4236f-156">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="4236f-157">省略可能なパラメーターなどのドキュメントは、慣例`id`正しい使用法に表示される可能性がある時期。</span><span class="sxs-lookup"><span data-stu-id="4236f-157">By convention the documentation will include optional parameters like `id` when they are likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="4236f-158">複数のルート</span><span class="sxs-lookup"><span data-stu-id="4236f-158">Multiple routes</span></span>

<span data-ttu-id="4236f-159">内の複数のルートを追加する`UseMvc`に呼び出しを追加して`MapRoute`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-159">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="4236f-160">これにより、複数の規則を定義したりするなど、特定の操作専用従来のルートを追加します。</span><span class="sxs-lookup"><span data-stu-id="4236f-160">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="4236f-161">`blog`ルートここでは、*専用従来ルート*、従来のルーティング システムを使用しますが、特定の操作は専用のことを意味します。</span><span class="sxs-lookup"><span data-stu-id="4236f-161">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="4236f-162">`controller`と`action`パラメーターとしてルート テンプレートに表示されていない、既定値を持てるおよびためこのルートは、アクションにマップする常に`BlogController.Article`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-162">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="4236f-163">ルート コレクション内のルートは、並べられ、追加された順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-163">Routes in the route collection are ordered, and will be processed in the order they are added.</span></span> <span data-ttu-id="4236f-164">この例では、`blog`ルートは、前に実行されます、`default`ルート。</span><span class="sxs-lookup"><span data-stu-id="4236f-164">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="4236f-165">*従来のルートを専用*多くの場合と同様にキャッチ オール ルート パラメーターを使用して`{*article}`URL パスの残りの部分をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="4236f-165">*Dedicated conventional routes* often use catch-all route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="4236f-166">これにより、ルート 'すぎる最長一致' 他のルートに一致させるための Url と一致することを意味します。</span><span class="sxs-lookup"><span data-stu-id="4236f-166">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="4236f-167">後で、ルート テーブルのこの問題を解決するには、'最長一致' のルートを配置します。</span><span class="sxs-lookup"><span data-stu-id="4236f-167">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="4236f-168">フォールバック</span><span class="sxs-lookup"><span data-stu-id="4236f-168">Fallback</span></span>

<span data-ttu-id="4236f-169">要求の処理の一環として、MVC は、アプリケーション内でコント ローラーとアクションを検索するルートの値を使用できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4236f-169">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="4236f-170">アクションをルートの値が一致しない場合、ルートとは見なされません、一致して次のルートが試行されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-170">If the route values don't match an action then the route is not considered a match, and the next route will be tried.</span></span> <span data-ttu-id="4236f-171">これと呼ばれる*フォールバック*を従来のルートと重なる場合を簡略化するためのものでは、します。</span><span class="sxs-lookup"><span data-stu-id="4236f-171">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="4236f-172">アクションの明確化</span><span class="sxs-lookup"><span data-stu-id="4236f-172">Disambiguating actions</span></span>

<span data-ttu-id="4236f-173">2 つのアクションは、ルーティングで一致すると、MVC 必要がありますあいまいさを解消、例外をスローするか、または '最高' 候補を選択します。</span><span class="sxs-lookup"><span data-stu-id="4236f-173">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="4236f-174">例:</span><span class="sxs-lookup"><span data-stu-id="4236f-174">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="4236f-175">このコント ローラーには、URL パスに一致する、2 つのアクションが定義されて`/Products/Edit/17`し、データをルーティング`{ controller = Products, action = Edit, id = 17 }`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-175">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="4236f-176">これは、MVC コント ローラーの一般的なパターン場所`Edit(int)`、製品を編集するためのフォームを表示および`Edit(int, Product)`ポストされたフォームを処理します。</span><span class="sxs-lookup"><span data-stu-id="4236f-176">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="4236f-177">可能な MVC 必要がありますを選択する`Edit(int, Product)`要求が HTTP の場合`POST`と`Edit(int)`HTTP 動詞がそれ以外の場合。</span><span class="sxs-lookup"><span data-stu-id="4236f-177">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="4236f-178">`HttpPostAttribute` ( `[HttpPost]` ) の実装は、 `IActionConstraint` HTTP 動詞が場合に選択する操作のみを許可する`POST`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-178">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="4236f-179">存在、`IActionConstraint`により、 `Edit(int, Product)` 'より良い' の一致よりも`Edit(int)`ため、`Edit(int, Product)`最初に試行されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-179">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="4236f-180">カスタムを作成する必要がありますのみ`IActionConstraint`が特殊なシナリオでは、実装のような属性の役割を理解しておく必要`HttpPostAttribute`-その他の HTTP 動詞のような属性が定義されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-180">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="4236f-181">従来のルーティングでは操作の一部であるときに、同じアクション名を使用する一般的な`show form -> submit form`ワークフローです。</span><span class="sxs-lookup"><span data-stu-id="4236f-181">In conventional routing it's common for actions to use the same action name when they are part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="4236f-182">このパターンの利便性を確認した後より明白になります、[理解 IActionConstraint](#understanding-iactionconstraint)セクションです。</span><span class="sxs-lookup"><span data-stu-id="4236f-182">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="4236f-183">複数のルートが一致するし、MVC は、'最高' のルートを見つけることはできませんがスローされます、`AmbiguousActionException`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-183">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="4236f-184">ルート名</span><span class="sxs-lookup"><span data-stu-id="4236f-184">Route names</span></span>

<span data-ttu-id="4236f-185">文字列`"blog"`と`"default"`次の例では、ルート名。</span><span class="sxs-lookup"><span data-stu-id="4236f-185">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>


```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="4236f-186">ルート名は、名前付きのルートが URL の生成に使用できるようにするに、ルートの論理名を付けます。</span><span class="sxs-lookup"><span data-stu-id="4236f-186">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="4236f-187">URL の作成は、ルートの順序を行うことができる複雑になり、URL の生成時にこれが大幅に合理化されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-187">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="4236f-188">ルート名は一意のアプリケーション全体である必要があります。</span><span class="sxs-lookup"><span data-stu-id="4236f-188">Route names must be unique application-wide.</span></span>

<span data-ttu-id="4236f-189">ルート名なしに影響がある URL に一致するか、要求の処理URL の生成にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-189">Route names have no impact on URL matching or handling of requests; they are used only for URL generation.</span></span> <span data-ttu-id="4236f-190">[ルーティング](xref:fundamentals/routing)詳細は特定の MVC ヘルパーの URL の生成を含む URL の生成にします。</span><span class="sxs-lookup"><span data-stu-id="4236f-190">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="4236f-191">属性のルーティング</span><span class="sxs-lookup"><span data-stu-id="4236f-191">Attribute routing</span></span>

<span data-ttu-id="4236f-192">属性のルーティング アクションをルート テンプレートに直接マップするのに一連の属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="4236f-192">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="4236f-193">次の例では、`app.UseMvc();`で使用される、`Configure`メソッドとルートがありませんが渡されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-193">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="4236f-194">`HomeController`はどのような既定のルートのような Url のセットに一致`{controller=Home}/{action=Index}/{id?}`が一致します。</span><span class="sxs-lookup"><span data-stu-id="4236f-194">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="4236f-195">`HomeController.Index()` URL パスのいずれかで実行するアクション`/`、 `/Home`、または`/Home/Index`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-195">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="4236f-196">この例では、属性のルーティングと従来のルーティングとプログラミングの大きな違いが強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-196">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="4236f-197">さらに、ルートを指定の入力属性のルーティングが必要です。従来の既定のルートでは、簡潔にルートを処理します。</span><span class="sxs-lookup"><span data-stu-id="4236f-197">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="4236f-198">ただし、属性のルーティングにより、(およびが必要です) 各アクションに適用するルート テンプレートを正確に制御します。</span><span class="sxs-lookup"><span data-stu-id="4236f-198">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="4236f-199">コント ローラー名とアクション名をルーティングする属性を持つ再生**ありません**アクションが選択されているロールです。</span><span class="sxs-lookup"><span data-stu-id="4236f-199">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="4236f-200">この例では、前の例と同じ Url を検索します。</span><span class="sxs-lookup"><span data-stu-id="4236f-200">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="4236f-201">上記のルート テンプレートのルートのパラメーターを定義しない`action`、 `area`、および`controller`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-201">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="4236f-202">実際には、これらのルートのパラメーターは、属性のルートでは許可されません。</span><span class="sxs-lookup"><span data-stu-id="4236f-202">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="4236f-203">ルート テンプレートは、アクションに関連付けは既に、ために考えないから賢明を URL からアクション名を解析します。</span><span class="sxs-lookup"><span data-stu-id="4236f-203">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="4236f-204">Http [動詞] 属性を持つルーティング属性します。</span><span class="sxs-lookup"><span data-stu-id="4236f-204">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="4236f-205">属性がルーティングすることもできますの使用、`Http[Verb]`など属性`HttpPostAttribute`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-205">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="4236f-206">ルート テンプレートを受け入れるすべてこれらの属性のことができます。</span><span class="sxs-lookup"><span data-stu-id="4236f-206">All of these attributes can accept a route template.</span></span> <span data-ttu-id="4236f-207">この例では、同じルート テンプレートに一致する 2 つのアクションを示します。</span><span class="sxs-lookup"><span data-stu-id="4236f-207">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="4236f-208">ような URL パスの`/products`、 `ProductsApi.ListProducts` HTTP 動詞がときにアクションが実行されます`GET`と`ProductsApi.CreateProduct`HTTP 動詞がときに実行される`POST`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-208">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="4236f-209">ルート属性で定義されたルート テンプレートのセットに対して URL に一致属性が最初にルーティングします。</span><span class="sxs-lookup"><span data-stu-id="4236f-209">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="4236f-210">ルート テンプレートに一致すると、一度`IActionConstraint`のどのアクションを実行するかの制約が適用されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-210">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="4236f-211">まれを使用する REST API を作成するときに`[Route(...)]`がアクション メソッド。</span><span class="sxs-lookup"><span data-stu-id="4236f-211">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method.</span></span> <span data-ttu-id="4236f-212">詳細なを使用することをお勧め`Http*Verb*Attributes`API がサポートする内容を正確に指定します。</span><span class="sxs-lookup"><span data-stu-id="4236f-212">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="4236f-213">どのようなパスと HTTP 動詞にマップする特定の論理操作を把握するには、REST Api のクライアントが予想されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-213">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="4236f-214">属性ルートは、特定のアクションに適用するため、簡単にルート テンプレートの定義の一部として必要なパラメーターです。</span><span class="sxs-lookup"><span data-stu-id="4236f-214">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="4236f-215">この例では`id`が URL パスの一部として必要です。</span><span class="sxs-lookup"><span data-stu-id="4236f-215">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="4236f-216">`ProductsApi.GetProduct(int)`のような URL パスで実行するアクション`/products/3`のではなくのような URL パス`/products`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-216">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="4236f-217">参照してください[ルーティング](../../fundamentals/routing.md)ルート テンプレートと関連するオプションの詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="4236f-217">See [Routing](../../fundamentals/routing.md) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="4236f-218">ルート名</span><span class="sxs-lookup"><span data-stu-id="4236f-218">Route Name</span></span>

<span data-ttu-id="4236f-219">次のコード定義、*ルート名*の`Products_List`:</span><span class="sxs-lookup"><span data-stu-id="4236f-219">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="4236f-220">ルート名は、特定のルートに基づく URL の生成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="4236f-220">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="4236f-221">ルート名は、ルーティングの動作と一致する URL に影響を与えるなく、URL の生成にのみ使用します。</span><span class="sxs-lookup"><span data-stu-id="4236f-221">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="4236f-222">ルート名は一意のアプリケーション全体である必要があります。</span><span class="sxs-lookup"><span data-stu-id="4236f-222">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="4236f-223">これは、従来*既定のルート*を定義する、`id`パラメーターをオプションとして (`{id?}`)。</span><span class="sxs-lookup"><span data-stu-id="4236f-223">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="4236f-224">Api を正確に指定するには、この機能を許可するなどの利点を持つ`/products`と`/products/5`さまざまな操作にディスパッチされます。</span><span class="sxs-lookup"><span data-stu-id="4236f-224">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="4236f-225">結合ルート</span><span class="sxs-lookup"><span data-stu-id="4236f-225">Combining routes</span></span>

<span data-ttu-id="4236f-226">属性がルーティング小さい反復的なコント ローラーのルート属性は、個々 のアクションでのルート属性と結合されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-226">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="4236f-227">コント ローラーで定義されたルート テンプレートの前は、アクションのルート テンプレートに付加されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-227">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="4236f-228">コント ローラーのルート属性を配置すると、**すべて**コント ローラーのアクションは、属性のルーティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="4236f-228">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="4236f-229">この例の URL パスでは`/products`に適合する`ProductsApi.ListProducts`、URL パス`/products/5`に適合する`ProductsApi.GetProduct(int)`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-229">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="4236f-230">これらの操作にのみ一致する HTTP`GET`で装飾されているため、`HttpGetAttribute`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-230">Both of these actions only match HTTP `GET` because they are decorated with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="4236f-231">ルート テンプレートを起点とするアクションに適用されて、`/`は取得と組み合わせないでコント ローラーに適用されたルート テンプレート。</span><span class="sxs-lookup"><span data-stu-id="4236f-231">Route templates applied to an action that begin with a `/` do not get combined with route templates applied to the controller.</span></span> <span data-ttu-id="4236f-232">この例と同様の URL パスのセットに一致、*既定のルート*です。</span><span class="sxs-lookup"><span data-stu-id="4236f-232">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Does not combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="4236f-233">属性ルートの順序</span><span class="sxs-lookup"><span data-stu-id="4236f-233">Ordering attribute routes</span></span>

<span data-ttu-id="4236f-234">対照的に定義された順序で実行される従来のルートでは、属性のルーティングは、ツリーを構築し、同時にすべてのルートに一致します。</span><span class="sxs-lookup"><span data-stu-id="4236f-234">In contrast to conventional routes which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="4236f-235">これとして動作のルート エントリが、最適な順序付け; に配置されていた場合最も固有のルートには、一般的なルートの前に実行する機会があります。</span><span class="sxs-lookup"><span data-stu-id="4236f-235">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="4236f-236">たとえば、ルートなど`blog/search/{topic}`のようにルートよりも特定`blog/{*article}`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-236">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="4236f-237">論理的には、`blog/search/{topic}`ルート '実行' 最初に、既定では、実用的な唯一の順序付けされているためです。</span><span class="sxs-lookup"><span data-stu-id="4236f-237">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="4236f-238">従来のルーティングを使用して、開発者が目的の順序にルートを配置するためです。</span><span class="sxs-lookup"><span data-stu-id="4236f-238">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="4236f-239">属性ルートは、注文を構成することができますを使用して、`Order`フレームワークが提供するルート属性のすべてのプロパティです。</span><span class="sxs-lookup"><span data-stu-id="4236f-239">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="4236f-240">ルートは昇順に従って処理のソート、`Order`プロパティです。</span><span class="sxs-lookup"><span data-stu-id="4236f-240">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="4236f-241">既定の順序は`0`します。</span><span class="sxs-lookup"><span data-stu-id="4236f-241">The default order is `0`.</span></span> <span data-ttu-id="4236f-242">設定を使用して、ルート`Order = -1`順序が設定されていないルートの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-242">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="4236f-243">設定を使用して、ルート`Order = 1`既定ルートの順序付けした後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-243">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="4236f-244">に応じて回避`Order`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-244">Avoid depending on `Order`.</span></span> <span data-ttu-id="4236f-245">URL 空間には、明示的な順序の値を正しくルーティングが必要とする場合は、クライアントとの混乱を招く可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4236f-245">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="4236f-246">一般に属性のルーティングが URL に一致する正しいルートが選択されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-246">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="4236f-247">上書きは、適用するよりも通常簡単にルート名を使用して、URL の生成に使用される既定の順序が動作していない場合、`Order`プロパティです。</span><span class="sxs-lookup"><span data-stu-id="4236f-247">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="4236f-248">トークンの置換ルート テンプレートで ([コント ローラー]、[アクション]、[領域])</span><span class="sxs-lookup"><span data-stu-id="4236f-248">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="4236f-249">属性ルートをサポートして便宜上、*トークンの置換*角かっこで囲み、トークンを (`[`、 `]`)。</span><span class="sxs-lookup"><span data-stu-id="4236f-249">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="4236f-250">トークン`[action]`、 `[area]`、および`[controller]`はアクション名、領域名とルートが定義されているアクションからコント ローラー名の値に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="4236f-250">The tokens `[action]`, `[area]`, and `[controller]` will be replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="4236f-251">この例ではコメント」の説明に従って、アクションによって URL パスが一致することができます。</span><span class="sxs-lookup"><span data-stu-id="4236f-251">In this example the actions can match URL paths as described in the comments:</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="4236f-252">トークンの置換は、属性のルートの構築の最後のステップとして発生します。</span><span class="sxs-lookup"><span data-stu-id="4236f-252">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="4236f-253">上記の例は、次のコードと同じ動作にします。</span><span class="sxs-lookup"><span data-stu-id="4236f-253">The above example will behave the same as the following code:</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="4236f-254">継承を持つ属性ルートを組み合わせることもできます。</span><span class="sxs-lookup"><span data-stu-id="4236f-254">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="4236f-255">これは、トークンの置換と組み合わせる非常に強力です。</span><span class="sxs-lookup"><span data-stu-id="4236f-255">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPost("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="4236f-256">トークンの置換は、属性のルートが定義したルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-256">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="4236f-257">`[Route("[controller]/[action]", Name="[controller]_[action]")]`アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-257">`[Route("[controller]/[action]", Name="[controller]_[action]")]` will generate a unique route name for each action.</span></span>

<span data-ttu-id="4236f-258">トークンの置換がリテラルの区切り記号に一致する`[`または`]`、文字の繰り返しでエスケープ (`[[`または`]]`)。</span><span class="sxs-lookup"><span data-stu-id="4236f-258">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="4236f-259">複数のルート</span><span class="sxs-lookup"><span data-stu-id="4236f-259">Multiple Routes</span></span>

<span data-ttu-id="4236f-260">属性の操作と同じに到達する複数のルートを定義するルーティングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="4236f-260">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="4236f-261">動作を模倣するためには、これの最も一般的な使用法、*従来型の既定のルート*次の例で示すようにします。</span><span class="sxs-lookup"><span data-stu-id="4236f-261">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="4236f-262">コント ローラーに複数のルート属性を適用するには、それぞれ 1 つがアクション メソッドのルート属性の各に結合を意味します。</span><span class="sxs-lookup"><span data-stu-id="4236f-262">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="4236f-263">ときに複数のルート属性 (実装する`IActionConstraint`) は、各アクションの制約がルート テンプレートを定義する属性を使用して結合し、アクションに配置されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-263">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="4236f-264">操作で複数のルートを使用してできるように見えます強力、シンプルかつ適切に定義されたアプリケーションの URL のスペースを保持することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4236f-264">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="4236f-265">既存のクライアントをサポートする例については、必要な場所にのみ、アクションで複数のルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="4236f-265">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="4236f-266">属性のルートの省略可能なパラメーター、既定値、および制約を指定します。</span><span class="sxs-lookup"><span data-stu-id="4236f-266">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="4236f-267">属性ルートは、省略可能なパラメーター、既定値、および制約を指定する従来のルートとして同じインライン構文をサポートします。</span><span class="sxs-lookup"><span data-stu-id="4236f-267">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="4236f-268">参照してください[ルート テンプレート参照](../../fundamentals/routing.md#route-template-reference)ルート テンプレートの構文の詳細な説明をします。</span><span class="sxs-lookup"><span data-stu-id="4236f-268">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="4236f-269">使用してカスタム ルート属性`IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="4236f-269">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="4236f-270">すべてのフレームワークで提供されるルート属性 ( `[Route(...)]`、`[HttpGet(...)]`など) を実装、`IRouteTemplateProvider`インターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="4236f-270">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="4236f-271">MVC は、アプリが起動し、実装するものを使用するとコント ローラー クラスとアクション メソッドの属性の検索`IRouteTemplateProvider`ルートの初期セットを構築します。</span><span class="sxs-lookup"><span data-stu-id="4236f-271">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="4236f-272">実装できます`IRouteTemplateProvider`を独自のルート属性を定義します。</span><span class="sxs-lookup"><span data-stu-id="4236f-272">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="4236f-273">各`IRouteTemplateProvider`順序や名前をカスタム ルート テンプレートでルートが 1 つを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="4236f-273">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="4236f-274">上記の例からの属性が自動的に設定、`Template`に`"api/[controller]"`とき`[MyApiController]`を適用します。</span><span class="sxs-lookup"><span data-stu-id="4236f-274">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="4236f-275">アプリケーション モデルを使用して属性ルートをカスタマイズするには</span><span class="sxs-lookup"><span data-stu-id="4236f-275">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="4236f-276">*アプリケーション モデル*ですべての MVC のルーティングし、操作を実行するために使用するメタデータの起動時に作成されたオブジェクト モデルです。</span><span class="sxs-lookup"><span data-stu-id="4236f-276">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="4236f-277">*アプリケーション モデル*のすべてのルート属性から収集されたデータが含まれています (を通じて`IRouteTemplateProvider`)。</span><span class="sxs-lookup"><span data-stu-id="4236f-277">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="4236f-278">記述することができます*規則*ルーティングの動作方法をカスタマイズするスタートアップ時に、アプリケーション モデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="4236f-278">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="4236f-279">このセクションでは、アプリケーション モデルを使用してルーティングをカスタマイズする方法の簡単な例を示します。</span><span class="sxs-lookup"><span data-stu-id="4236f-279">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[Main](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="4236f-280">ルーティングの混在: 属性のルーティングと従来のルーティング</span><span class="sxs-lookup"><span data-stu-id="4236f-280">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="4236f-281">MVC アプリケーションでは、従来のルーティングやルーティングの属性の使用を混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="4236f-281">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="4236f-282">通常、ブラウザーの場合、HTML ページを提供しているコント ローラーに対して従来のルートを使用し、REST Api を提供しているコント ローラーのルーティングを属性であります。</span><span class="sxs-lookup"><span data-stu-id="4236f-282">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="4236f-283">アクションは従来どおりルーティングするか、または属性にルーティングします。</span><span class="sxs-lookup"><span data-stu-id="4236f-283">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="4236f-284">コント ローラーまたはアクションのルートを配置すると、その属性がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="4236f-284">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="4236f-285">属性ルートを定義する操作は、従来のルートと逆を介して到達できません。</span><span class="sxs-lookup"><span data-stu-id="4236f-285">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="4236f-286">**どの**コント ローラーのルート属性がルーティング コント ローラー属性のすべてのアクションをによりします。</span><span class="sxs-lookup"><span data-stu-id="4236f-286">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="4236f-287">ルーティングのシステムの 2 種類のような特徴は、URL に一致するルート テンプレート後に適用するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="4236f-287">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="4236f-288">従来のルーティングでは、一致したルートの値は、すべてのルーティング アクションを従来のルックアップ テーブルからアクションとコント ローラーを選択に使用されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-288">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="4236f-289">属性、ルーティングの各テンプレートの操作に関連付けられてし、これ以上の参照は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="4236f-289">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="4236f-290">URL の生成</span><span class="sxs-lookup"><span data-stu-id="4236f-290">URL Generation</span></span>

<span data-ttu-id="4236f-291">MVC アプリケーションでは、ルーティングの URL の生成機能を使用して、アクションへの URL リンクを生成します。</span><span class="sxs-lookup"><span data-stu-id="4236f-291">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="4236f-292">Url の生成、ハードコーディングする Url、堅牢性と保守が容易なので、コードがなくなります。</span><span class="sxs-lookup"><span data-stu-id="4236f-292">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="4236f-293">このセクションでは、MVC によって提供される URL の生成機能について説明し、URL の生成のしくみに関する基本事項を取り上げますのみです。</span><span class="sxs-lookup"><span data-stu-id="4236f-293">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="4236f-294">参照してください[ルーティング](../../fundamentals/routing.md)URL 生成の詳細な説明をします。</span><span class="sxs-lookup"><span data-stu-id="4236f-294">See [Routing](../../fundamentals/routing.md) for a detailed description of URL generation.</span></span>

<span data-ttu-id="4236f-295">`IUrlHelper`インターフェイスは、基になるインフラストラクチャの部分 MVC ルーティングの URL の生成とします。</span><span class="sxs-lookup"><span data-stu-id="4236f-295">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="4236f-296">インスタンスを検出します`IUrlHelper`を介して使用できる、`Url`プロパティ コント ローラー、ビュー、およびコンポーネントの表示にします。</span><span class="sxs-lookup"><span data-stu-id="4236f-296">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="4236f-297">この例では、`IUrlHelper`を介してインターフェイスを使用、`Controller.Url`プロパティを別のアクションへの URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="4236f-297">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="4236f-298">アプリケーションで従来の既定値が使用されている場合、ルーティングの値、`url`変数は、URL パスの文字列になります`/UrlGeneration/Destination`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-298">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="4236f-299">渡された値 (アンビエント値) の現在の要求からのルート値を組み合わせることによってルーティングが URL パスが作成された`Url.Action`とルート テンプレートにこれらの値を代入します。</span><span class="sxs-lookup"><span data-stu-id="4236f-299">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="4236f-300">ルート テンプレート内の各ルート パラメーター値とアンビエント値と一致する名前に置換値があります。</span><span class="sxs-lookup"><span data-stu-id="4236f-300">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="4236f-301">ルート パラメーター値がないは、既定値を使用できますが、いずれかまたは省略可能である場合は省略される場合 (の場合と`id`この例では)。</span><span class="sxs-lookup"><span data-stu-id="4236f-301">A route parameter that does not have a value can use a default value if it has one, or be skipped if it is optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="4236f-302">任意の必要なルートのパラメーターは、対応する値を持っていない場合、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="4236f-302">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="4236f-303">ルートの URL の生成に失敗した場合は、すべてのルートが試行されたか、一致が見つかるまでに次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-303">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="4236f-304">この例の`Url.Action`上では従来のルーティングが URL 生成の動作属性がルーティングと同じように概念が異なる場合。</span><span class="sxs-lookup"><span data-stu-id="4236f-304">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="4236f-305">従来のルーティングでルートの値を使用して、展開、テンプレートと、ルートの値を`controller`と`action`通常はルーティングに一致する Url に従うためこの方法でそのテンプレートに表示、*規約*.</span><span class="sxs-lookup"><span data-stu-id="4236f-305">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="4236f-306">属性、ルーティングのルートの値を`controller`と`action`テンプレート - に表示に使用するテンプレートを検索する代わりに使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="4236f-306">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they are instead used to look up which template to use.</span></span>

<span data-ttu-id="4236f-307">この例では、属性のルーティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="4236f-307">This example uses attribute routing:</span></span>

[!code-csharp[Main](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="4236f-308">MVC ルーティング属性のすべてのアクションのルックアップ テーブルを構築およびと一致する、`controller`と`action`URL の生成に使用するルート テンプレートを選択する値。</span><span class="sxs-lookup"><span data-stu-id="4236f-308">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="4236f-309">上記のサンプルで`custom/url/to/destination`が生成されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-309">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="4236f-310">アクション名によって Url の生成</span><span class="sxs-lookup"><span data-stu-id="4236f-310">Generating URLs by action name</span></span>

<span data-ttu-id="4236f-311">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="4236f-311">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="4236f-312">`Action`) 関連するすべてのオーバー ロードがすべて新機能にリンクするコント ローラー名とアクション名を指定して、指定することを考えに基づいているとします。</span><span class="sxs-lookup"><span data-stu-id="4236f-312">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="4236f-313">使用する場合`Url.Action`、現在のルートの値を`controller`と`action`- の値を指定した`controller`と`action`両方の一部である*アンビエント値***と***値*です。</span><span class="sxs-lookup"><span data-stu-id="4236f-313">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="4236f-314">メソッド`Url.Action`の現在の値は常に使用`action`と`controller`と現在のアクションにルーティングする URL パスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-314">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="4236f-315">アンビエントの値で提供されていません。 URL を生成するときに情報を入力する値を使用する試行をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="4236f-315">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="4236f-316">ようにルートを使用して`{a}/{b}/{c}/{d}`とアンビエント値`{ a = Alice, b = Bob, c = Carol, d = David }`ルーティングせず、追加の値の URL を生成するための十分な情報には、-、パラメーターが値を持つため、すべてをルーティングします。</span><span class="sxs-lookup"><span data-stu-id="4236f-316">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="4236f-317">値を追加した場合は`{ d = Donovan }`、値`{ d = David }`は無視されます、し、生成された URL パスが`Alice/Bob/Carol/Donovan`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-317">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="4236f-318">URL パスは、階層。</span><span class="sxs-lookup"><span data-stu-id="4236f-318">URL paths are hierarchical.</span></span> <span data-ttu-id="4236f-319">値を追加した場合に上記の例で`{ c = Cheryl }`、両方の値`{ c = Carol, d = David }`は無視されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-319">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="4236f-320">ここでの値が不要になったある`d`URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="4236f-320">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="4236f-321">目的の値を指定する必要があります`c`と`d`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-321">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="4236f-322">既定のルートでこの問題をヒットする予定である (`{controller}/{action}/{id?}`)-として実際には、この動作が発生することはほとんどありませんが、`Url.Action`常に明示的に指定する、`controller`と`action`値。</span><span class="sxs-lookup"><span data-stu-id="4236f-322">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="4236f-323">オーバー ロードをより長い`Url.Action`取得することも、追加*ルート値*以外の場合、ルート パラメーターの値を提供するオブジェクト`controller`と`action`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-323">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="4236f-324">最もよく表示されると共に使用`id`など`Url.Action("Buy", "Products", new { id = 17 })`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-324">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="4236f-325">慣例により、*ルート値*オブジェクトは、通常、匿名型のオブジェクトがある可能性も、`IDictionary<>`または*plain old .NET オブジェクト*です。</span><span class="sxs-lookup"><span data-stu-id="4236f-325">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="4236f-326">ルート パラメーターが一致しないすべての追加のルートの値は、クエリ文字列に格納されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-326">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="4236f-327">絶対 URL を作成するを受け入れるオーバー ロードを使用して、 `protocol`:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="4236f-327">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="4236f-328">ルートで Url の生成</span><span class="sxs-lookup"><span data-stu-id="4236f-328">Generating URLs by route</span></span>

<span data-ttu-id="4236f-329">上記のコードでは、URL の生成、コント ローラーとアクション名を渡すことによって示されています。</span><span class="sxs-lookup"><span data-stu-id="4236f-329">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="4236f-330">`IUrlHelper`用意されています、`Url.RouteUrl`メソッドのファミリです。</span><span class="sxs-lookup"><span data-stu-id="4236f-330">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="4236f-331">これらのメソッドはのような`Url.Action`はの現在の値をコピーしないで`action`と`controller`ルートの値にします。</span><span class="sxs-lookup"><span data-stu-id="4236f-331">These methods are similar to `Url.Action`, but they do not copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="4236f-332">最も一般的な使用状況は一般的に、URL を生成する、特定のルートを使用するルート名を指定する*せず*コント ローラーまたはアクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="4236f-332">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[Main](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="4236f-333">Html 形式で Url の生成</span><span class="sxs-lookup"><span data-stu-id="4236f-333">Generating URLs in HTML</span></span>

<span data-ttu-id="4236f-334">`IHtmlHelper`提供、`HtmlHelper`メソッド`Html.BeginForm`と`Html.ActionLink`を生成する`<form>`と`<a>`要素それぞれします。</span><span class="sxs-lookup"><span data-stu-id="4236f-334">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="4236f-335">これらのメソッドを使用して、 `Url.Action` URL を生成する方法のような引数を受け入れるとします。</span><span class="sxs-lookup"><span data-stu-id="4236f-335">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="4236f-336">`Url.RouteUrl`の仲間`HtmlHelper`は`Html.BeginRouteForm`と`Html.RouteLink`同様の機能であります。</span><span class="sxs-lookup"><span data-stu-id="4236f-336">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="4236f-337">TagHelpers で Url を生成する、 `form` TagHelper と`<a>`TagHelper です。</span><span class="sxs-lookup"><span data-stu-id="4236f-337">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="4236f-338">これらの両方を使用して`IUrlHelper`実装するためです。</span><span class="sxs-lookup"><span data-stu-id="4236f-338">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="4236f-339">参照してください[フォームを使用する](../views/working-with-forms.md)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="4236f-339">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="4236f-340">ビュー、内部、`IUrlHelper`を介して使用できますが、`Url`上記でカバーされないアドホック URL の生成に任意のプロパティです。</span><span class="sxs-lookup"><span data-stu-id="4236f-340">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="4236f-341">アクションの結果で URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="4236f-341">Generating URLS in Action Results</span></span>

<span data-ttu-id="4236f-342">上記の例を使用して表示が`IUrlHelper`アクションの結果の一部として URL を生成するコント ローラーの最も一般的な使用方法ですが、コント ローラーにします。</span><span class="sxs-lookup"><span data-stu-id="4236f-342">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="4236f-343">`ControllerBase`と`Controller`の基本クラスが別のアクションを参照するアクション結果の便利なメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="4236f-343">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="4236f-344">一般的な使用法の 1 つでは、ユーザー入力を受け入れた後リダイレクトです。</span><span class="sxs-lookup"><span data-stu-id="4236f-344">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public Task<IActionResult> Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
}
```

<span data-ttu-id="4236f-345">メソッドに類似のパターンに従うアクション結果のファクトリ メソッド`IUrlHelper`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-345">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="4236f-346">従来の専用のルートの特殊なケース</span><span class="sxs-lookup"><span data-stu-id="4236f-346">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="4236f-347">ルート定義と呼ばれる特殊なを使用できる従来のルーティング、*専用従来ルート*です。</span><span class="sxs-lookup"><span data-stu-id="4236f-347">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="4236f-348">次の例ではルートの名前`blog`専用従来ルートです。</span><span class="sxs-lookup"><span data-stu-id="4236f-348">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="4236f-349">これらのルート定義を使用して`Url.Action("Index", "Home")`URL パスが生成されます`/`で、`default`ルートでなく、なぜですか?</span><span class="sxs-lookup"><span data-stu-id="4236f-349">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="4236f-350">ルートの値を推測することがあります`{ controller = Home, action = Index }`使用する場合、URL の生成に十分な`blog`、ありの結果となる`/blog?action=Index&controller=Home`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-350">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="4236f-351">従来の専用のルーティングがルートがなることを防止する対応するルートのパラメーターがありません。 既定値の特別な動作に依存"すぎる最長"の URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="4236f-351">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="4236f-352">既定値は、ここでは`{ controller = Blog, action = Article }`、およびどちら`controller`も`action`ルートのパラメーターとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-352">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="4236f-353">ルーティング URL の生成を実行するときに指定された値は既定値に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4236f-353">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="4236f-354">URL の生成を使用して`blog`ために失敗値`{ controller = Home, action = Index }`が一致しない`{ controller = Blog, action = Article }`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-354">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="4236f-355">ルーティングし、フォールバックして再試行してください`default`、これは成功します。</span><span class="sxs-lookup"><span data-stu-id="4236f-355">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="4236f-356">区分</span><span class="sxs-lookup"><span data-stu-id="4236f-356">Areas</span></span>

<span data-ttu-id="4236f-357">[領域](areas.md)MVC 機能として、別のルーティングでの名前空間 (コント ローラーのアクション) と (views) 用のフォルダー構造をグループに関連する機能を整理するために使用します。</span><span class="sxs-lookup"><span data-stu-id="4236f-357">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="4236f-358">領域の使用を使用すると同じ名前の複数のコント ローラーを持つ異なるを持っていれば、*領域*です。</span><span class="sxs-lookup"><span data-stu-id="4236f-358">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="4236f-359">別のルート パラメーターを追加することでルーティングするために階層を作成する領域を使用して`area`に`controller`と`action`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-359">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="4236f-360">このセクションでは、領域 - と対話するルーティング方法について説明を参照してください[領域](areas.md)詳細ビューでの領域の使用方法についてはします。</span><span class="sxs-lookup"><span data-stu-id="4236f-360">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="4236f-361">次の例は、従来の既定のルートを使用する MVC を構成し、*領域ルート*という名前の領域の`Blog`:</span><span class="sxs-lookup"><span data-stu-id="4236f-361">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="4236f-362">ような URL パスを照合するときに`/Manage/Users/AddUser`、最初のルートは、ルート値を生成`{ area = Blog, controller = Users, action = AddUser }`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-362">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="4236f-363">`area`ルートの値がの既定値によって生成される`area`実際には、によって作成されたルート`MapAreaRoute`は、次に相当します。</span><span class="sxs-lookup"><span data-stu-id="4236f-363">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="4236f-364">`MapAreaRoute`既定値との制約の両方を使用してルートを作成`area`ここで指定された領域名を使用して`Blog`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-364">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="4236f-365">既定値は、ルートが常に生成することにより、 `{ area = Blog, ... }`、制約が値を必要`{ area = Blog, ... }`URL 生成のためです。</span><span class="sxs-lookup"><span data-stu-id="4236f-365">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="4236f-366">従来のルーティングは、順序によって異なります。</span><span class="sxs-lookup"><span data-stu-id="4236f-366">Conventional routing is order-dependent.</span></span> <span data-ttu-id="4236f-367">一般に、領域なしルートより限定的であるので、ルート テーブルの前半の領域を持つルートを配置してください。</span><span class="sxs-lookup"><span data-stu-id="4236f-367">In general, routes with areas should be placed earlier in the route table as they are more specific than routes without an area.</span></span>

<span data-ttu-id="4236f-368">上記の例を使用して、ルートの値は、次の操作を一致は。</span><span class="sxs-lookup"><span data-stu-id="4236f-368">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="4236f-369">`AreaAttribute`コント ローラーを表すものは、領域の一部として、ということにこのコント ローラーは、`Blog`領域。</span><span class="sxs-lookup"><span data-stu-id="4236f-369">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="4236f-370">せずコント ローラー、`[Area]`属性が任意の領域のメンバーではないと、**いない**一致する場合に、`area`ルーティングでルートの値が指定されてです。</span><span class="sxs-lookup"><span data-stu-id="4236f-370">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="4236f-371">次の例では、表示されている最初のコント ローラーのみがルートの値を照合できる`{ area = Blog, controller = Users, action = AddUser }`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-371">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[Main](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="4236f-372">各コント ローラーの名前空間が完全を期すのためここで示すように、それ以外の場合、コント ローラーが競合して、コンパイラ エラーが生成される名前付けされます。</span><span class="sxs-lookup"><span data-stu-id="4236f-372">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="4236f-373">クラスの名前空間は、MVC のルーティングに影響を与えるありません。</span><span class="sxs-lookup"><span data-stu-id="4236f-373">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="4236f-374">最初の 2 つのコント ローラーの領域のメンバーであるし、して、それぞれの領域名を指定した場合にのみ一致する、`area`値をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="4236f-374">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="4236f-375">3 番目のコント ローラーは、任意の領域、およびときの値がありませんが唯一の一致のメンバーではない`area`ルーティングによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-375">The third controller is not a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="4236f-376">照合の観点から*値はありません*がない場合、`area`値は、同じ場合は、値は、`area`が null または空の文字列。</span><span class="sxs-lookup"><span data-stu-id="4236f-376">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="4236f-377">場合、ルートの値の領域内で操作を実行するときに`area`として使用できる、*アンビエント値*の URL の生成に使用するルーティングします。</span><span class="sxs-lookup"><span data-stu-id="4236f-377">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="4236f-378">つまり、既定では領域機能する*付箋*URL 生成の次の例に示すようにします。</span><span class="sxs-lookup"><span data-stu-id="4236f-378">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[Main](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[Main](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="4236f-379">Understanding IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="4236f-379">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="4236f-380">このセクションでは、フレームワークの内部構造と MVC を実行するアクションを選択する方法に deep dive です。</span><span class="sxs-lookup"><span data-stu-id="4236f-380">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="4236f-381">一般的なアプリケーションは、カスタムを必要はありません。`IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="4236f-381">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="4236f-382">既に使用した可能性があります`IActionConstraint`インターフェイスとあまり詳しくない場合でもです。</span><span class="sxs-lookup"><span data-stu-id="4236f-382">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="4236f-383">`[HttpGet]`属性および同様`[Http-VERB]`属性は、実装`IActionConstraint`アクション メソッドの実行を制限するためにします。</span><span class="sxs-lookup"><span data-stu-id="4236f-383">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="4236f-384">既定の従来ルートの URL パスを想定して`/Products/Edit`と値が生成される`{ controller = Products, action = Edit }`、一致するよう**両方**次に示す操作のです。</span><span class="sxs-lookup"><span data-stu-id="4236f-384">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="4236f-385">`IActionConstraint`と言うの候補を考慮はこれらのアクションの両方のルート データを一致している両方の用語です。</span><span class="sxs-lookup"><span data-stu-id="4236f-385">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="4236f-386">ときに、`HttpGetAttribute`を実行することだとは*Edit()*の一致するものは、*取得*し、その他の HTTP 動詞の一致するものではありません。</span><span class="sxs-lookup"><span data-stu-id="4236f-386">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and is not a match for any other HTTP verb.</span></span> <span data-ttu-id="4236f-387">`Edit(...)`アクションが定義されている、すべての制約がないし、すべての HTTP 動詞と一致するためです。</span><span class="sxs-lookup"><span data-stu-id="4236f-387">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="4236f-388">これと仮定した場合、 `POST` - のみ`Edit(...)`と一致します。</span><span class="sxs-lookup"><span data-stu-id="4236f-388">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="4236f-389">`GET`両方のアクションできますも一致 - ただし、アクションと、`IActionConstraint`は常と見なされます*向上*なしアクションよりもします。</span><span class="sxs-lookup"><span data-stu-id="4236f-389">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="4236f-390">そのため`Edit()`が`[HttpGet]`より具体的と見なされ、両方のアクションを一致させる場合に選択されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-390">So because `Edit()` has `[HttpGet]` it is considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="4236f-391">概念的には、`IActionConstraint`の形式は、*オーバー ロード*、同じ名前のメソッドをオーバー ロードではなく、同じ URL に一致するアクション間でオーバー ロードには、します。</span><span class="sxs-lookup"><span data-stu-id="4236f-391">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it is overloading between actions that match the same URL.</span></span> <span data-ttu-id="4236f-392">属性のルーティングを使用しても`IActionConstraint`両方を検討している候補となるさまざまなコント ローラーから、操作に実現できます。</span><span class="sxs-lookup"><span data-stu-id="4236f-392">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="4236f-393">IActionConstraint を実装します。</span><span class="sxs-lookup"><span data-stu-id="4236f-393">Implementing IActionConstraint</span></span>

<span data-ttu-id="4236f-394">実装する最も簡単な方法、`IActionConstraint`から派生するクラスを作成するのには、`System.Attribute`アクションとコント ローラー上に配置します。</span><span class="sxs-lookup"><span data-stu-id="4236f-394">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="4236f-395">MVC は自動的に検出、`IActionConstraint`属性として適用されます。</span><span class="sxs-lookup"><span data-stu-id="4236f-395">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="4236f-396">アプリケーション モデルを使用するには、制約を適用してがある可能性が最も柔軟な方法でメタプログラムの適用方法を可能にします。</span><span class="sxs-lookup"><span data-stu-id="4236f-396">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they are applied.</span></span>

<span data-ttu-id="4236f-397">次の例では、制約がに基づいてアクションを選択、*国コード*ルート データから。</span><span class="sxs-lookup"><span data-stu-id="4236f-397">In the following example a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="4236f-398">[GitHub の全サンプル](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)です。</span><span class="sxs-lookup"><span data-stu-id="4236f-398">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/dev/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="4236f-399">実装を担当する場合、`Accept`メソッドと、'Order' 制約の実行を選択します。</span><span class="sxs-lookup"><span data-stu-id="4236f-399">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="4236f-400">ここでは、`Accept`メソッドを返します。`true`アクションが一致するものを示すときに、`country`値の一致をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="4236f-400">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="4236f-401">これに対し、`RouteValueAttribute`フォールバック非属性を操作することができます。</span><span class="sxs-lookup"><span data-stu-id="4236f-401">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="4236f-402">例を定義するかどうか、`en-US`アクション次のような国コード`fr-FR`がフォールバックしてより汎用的なコント ローラーを持たない`[CountrySpecific(...)]`適用します。</span><span class="sxs-lookup"><span data-stu-id="4236f-402">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that does not have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="4236f-403">`Order`プロパティ決定*ステージ*制約の一部であります。</span><span class="sxs-lookup"><span data-stu-id="4236f-403">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="4236f-404">アクション制約の実行に基づきグループ分け、`Order`です。</span><span class="sxs-lookup"><span data-stu-id="4236f-404">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="4236f-405">たとえば、すべてのフレームワーク提供 HTTP メソッドの属性を使用して同じ`Order`値の同じ段階で実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="4236f-405">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="4236f-406">多くの段階、目的のポリシーを実装する必要があることができます。</span><span class="sxs-lookup"><span data-stu-id="4236f-406">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="4236f-407">値を決定する`Order`HTTP メソッドより前に、制約を適用するかどうかについて考えます。</span><span class="sxs-lookup"><span data-stu-id="4236f-407">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="4236f-408">低い数値が最初に実行します。</span><span class="sxs-lookup"><span data-stu-id="4236f-408">Lower numbers run first.</span></span>