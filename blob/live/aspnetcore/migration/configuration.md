---
title: "構成を移行します。"
author: ardalis
description: 
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: migration/configuration
ms.openlocfilehash: 90d9f730d31c2c70aec3d47610b9031a7d8e621b
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2018
---
# <a name="migrating-configuration"></a><span data-ttu-id="68d42-102">構成を移行します。</span><span class="sxs-lookup"><span data-stu-id="68d42-102">Migrating Configuration</span></span>

<span data-ttu-id="68d42-103">によって[Steve Smith](https://ardalis.com/)と[Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="68d42-103">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="68d42-104">開始前の記事で[ASP.NET Core MVC に ASP.NET MVC プロジェクトを移行する](mvc.md)です。</span><span class="sxs-lookup"><span data-stu-id="68d42-104">In the previous article, we began [migrating an ASP.NET MVC project to ASP.NET Core MVC](mvc.md).</span></span> <span data-ttu-id="68d42-105">この記事では、構成を移行します。</span><span class="sxs-lookup"><span data-stu-id="68d42-105">In this article, we migrate configuration.</span></span>

<span data-ttu-id="68d42-106">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/configuration/samples)します ([ダウンロード方法](xref:tutorials/index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="68d42-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="68d42-107">セットアップの構成</span><span class="sxs-lookup"><span data-stu-id="68d42-107">Setup Configuration</span></span>

<span data-ttu-id="68d42-108">ASP.NET Core を使用しない、 *Global.asax*と*web.config* ASP.NET の以前のバージョンを使用するファイル。</span><span class="sxs-lookup"><span data-stu-id="68d42-108">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="68d42-109">ASP.NET の以前のバージョンではアプリケーションのスタートアップ ロジックに格納された、`Application_StartUp`メソッド内で*Global.asax*です。</span><span class="sxs-lookup"><span data-stu-id="68d42-109">In earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="68d42-110">その後、ASP.NET MVC では、 *Startup.cs*ファイルがプロジェクトのルートに含まれているし、アプリケーションの起動時に呼び出されました。</span><span class="sxs-lookup"><span data-stu-id="68d42-110">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="68d42-111">ASP.NET Core を採用していますこのアプローチ完全にすべてのスタートアップ ロジックを配置することによって、 *Startup.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="68d42-111">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="68d42-112">*Web.config* ASP.NET Core でのファイルが置き換えられてもいます。</span><span class="sxs-lookup"><span data-stu-id="68d42-112">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="68d42-113">説明されているアプリケーションのスタートアップ プロシージャの一部として、構成自体を構成ようになりましたことができます*Startup.cs*です。</span><span class="sxs-lookup"><span data-stu-id="68d42-113">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="68d42-114">構成は、XML ファイルにも引き続き使用できますが、通常 ASP.NET Core プロジェクトが配置構成値 JSON 形式のファイルになど*される appsettings.json*です。</span><span class="sxs-lookup"><span data-stu-id="68d42-114">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="68d42-115">ASP.NET Core の構成システムは、環境変数は、環境固有の値より安全かつ堅牢な場所を指定することができますをも簡単にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="68d42-115">ASP.NET Core's configuration system can also easily access environment variables, which can provide a more secure and robust location for environment-specific values.</span></span> <span data-ttu-id="68d42-116">これは、接続文字列およびソース管理にチェックインする必要がありますいない API キーなどの機密情報に特に当てはまります。</span><span class="sxs-lookup"><span data-stu-id="68d42-116">This is especially true for secrets like connection strings and API keys that should not be checked into source control.</span></span> <span data-ttu-id="68d42-117">参照してください[構成](xref:fundamentals/configuration/index)ASP.NET Core の構成に関する詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="68d42-117">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="68d42-118">この記事では、私たちは、開始から ASP.NET Core プロジェクトを部分的に移行[前の記事](mvc.md)です。</span><span class="sxs-lookup"><span data-stu-id="68d42-118">For this article, we are starting with the partially-migrated ASP.NET Core project from [the previous article](mvc.md).</span></span> <span data-ttu-id="68d42-119">構成をセットアップで、次のコンス トラクターとプロパティを追加する、 *Startup.cs*プロジェクトのルートにあるファイル。</span><span class="sxs-lookup"><span data-stu-id="68d42-119">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[Main](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-21)]

<span data-ttu-id="68d42-120">この時点で、メモ、 *Startup.cs*ファイルはコンパイルされず、次を追加する必要があります、`using`ステートメント。</span><span class="sxs-lookup"><span data-stu-id="68d42-120">Note that at this point, the *Startup.cs* file will not compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="68d42-121">追加、*される appsettings.json*適切な項目テンプレートを使用して、プロジェクトのルートにファイル。</span><span class="sxs-lookup"><span data-stu-id="68d42-121">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![AppSettings の JSON を追加します。](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="68d42-123">Web.config ファイルから構成設定を移行します。</span><span class="sxs-lookup"><span data-stu-id="68d42-123">Migrate Configuration Settings from web.config</span></span>

<span data-ttu-id="68d42-124">ASP.NET MVC プロジェクトに必要なデータベース接続文字列が含まれている*web.config*で、`<connectionStrings>`要素。</span><span class="sxs-lookup"><span data-stu-id="68d42-124">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="68d42-125">プロジェクトでは、ASP.NET Core、この情報を格納するつもりが、*される appsettings.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="68d42-125">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="68d42-126">開いている*される appsettings.json*、および次既に含まれていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="68d42-126">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[Main](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]


<span data-ttu-id="68d42-127">強調表示された行に上に示されているからデータベースの名前を変更する**_CHANGE_ME**データベースの名前にします。</span><span class="sxs-lookup"><span data-stu-id="68d42-127">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="68d42-128">まとめ</span><span class="sxs-lookup"><span data-stu-id="68d42-128">Summary</span></span>

<span data-ttu-id="68d42-129">ASP.NET Core は、単一のファイルを順番に必要なサービスと依存関係の定義し、構成に、アプリケーションのすべてのスタートアップ ロジックを配置します。</span><span class="sxs-lookup"><span data-stu-id="68d42-129">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="68d42-130">置き換えられます、 *web.config*ファイルのさまざまな環境変数と同様に、JSON などのファイル形式を利用できる柔軟な構成機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="68d42-130">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>