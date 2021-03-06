---
uid: mvc/overview/older-versions-1/unit-testing/creating-unit-tests-for-asp-net-mvc-applications-vb
title: "ASP.NET MVC アプリケーション (VB) の単体テストの作成 |Microsoft ドキュメント"
author: StephenWalther
description: "コント ローラー アクションの単体テストを作成する方法を説明します。 このチュートリアルでは、Stephen Walther はコント ローラーのアクションが、parti を返すかどうかをテストする方法を示します."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/19/2008
ms.topic: article
ms.assetid: eb35710d-1d99-44ac-b61f-e50af8cb328a
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/unit-testing/creating-unit-tests-for-asp-net-mvc-applications-vb
msc.type: authoredcontent
ms.openlocfilehash: d92ee0c26787e5c482e8695001d8809d3ee9ee30
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2017
---
<a name="creating-unit-tests-for-aspnet-mvc-applications-vb"></a>ASP.NET MVC アプリケーション (VB) の単体テストの作成
====================
によって[Stephen Walther](https://github.com/StephenWalther)

[PDF をダウンロードします。](http://download.microsoft.com/download/8/4/8/84843d8d-1575-426c-bcb5-9d0c42e51416/ASPNET_MVC_Tutorial_07_VB.pdf)

> コント ローラー アクションの単体テストを作成する方法を説明します。 このチュートリアルでは、Stephen Walther はコント ローラーのアクションの特定のビューを返します、特定のデータのセットを返しますまたは別の種類のアクションの結果を返すかどうかをテストする方法を示します。


このチュートリアルの目標を記述する方法、コント ローラーの単体テスト、ASP.NET MVC でのアプリケーションを示すことです。 次の 3 つの異なる種類の単体テストを作成する方法について説明します。 コント ローラーのアクションによって返されるビューをテストする方法、コント ローラーのアクションによって返されるデータの表示をテストする方法、および 1 つのコント ローラー アクションが 2 番目のコント ローラー アクションにリダイレクトするかどうかをテストする方法を説明します。

## <a name="creating-the-controller-under-test"></a>テスト コント ローラーを作成します。

テストしようとするコント ローラーの作成を始めます。 という名前のコント ローラー、 `ProductController`1 のリストに含まれています。

**1 – を一覧表示します。`ProductController.vb`**

[!code-vb[Main](creating-unit-tests-for-asp-net-mvc-applications-vb/samples/sample1.vb)]

`ProductController`という 2 つのアクション メソッドを含む`Index()`と`Details()`です。 どちらのアクション メソッドは、ビューを返します。 注意して、`Details()`操作 id をという名前のパラメーターを受け入れます。

## <a name="testing-the-view-returned-by-a-controller"></a>ビューをテスト コント ローラーによって返される

テストすることを想像してくださいかどうか、`ProductController`右側のビューを返します。 確認するときに、`ProductController.Details()`アクションが呼び出されると、詳細ビューが返されます。 リスト 2 でテスト クラスには、テストによって返されるビューの単体テストが含まれています、`ProductController.Details()`アクション。

**2 – を一覧表示します。`ProductControllerTest.vb`**

[!code-vb[Main](creating-unit-tests-for-asp-net-mvc-applications-vb/samples/sample2.vb)]

2 のリスト内のクラスには、という名前のテスト メソッドが含まれています。`TestDetailsView()`です。 このメソッドには、次の 3 つの行コードにはが含まれています。 コードの最初の行の新しいインスタンスを作成する、`ProductController`クラスです。 コードの 2 行目は、コント ローラーを呼び出す`Details()`アクション メソッド。 ビューがによって返されるかどうか、コードのチェックの最後の行、最後に、`Details()`操作は、詳細ビューです。

`ViewResult.ViewName`プロパティは、コント ローラーによって返されるビューの名前を表します。 このプロパティのテストの 1 つの大きな警告します。 これには、コント ローラーが、ビューを返すことができます 2 つの方法があります。 コント ローラーは、次のようにビューを明示的に返すことができます。

[!code-vb[Main](creating-unit-tests-for-asp-net-mvc-applications-vb/samples/sample3.vb)]

また、ビューの名前は、次のようにコント ローラー アクションの名前から推論することができます。

[!code-vb[Main](creating-unit-tests-for-asp-net-mvc-applications-vb/samples/sample4.vb)]

という名前のビューを返すコント ローラー アクション`Details`です。 ただし、ビューの名前は、アクション名から推測されます。 ビューの名前をテストする場合は、コント ローラーのアクションからビューの名前に明示的に返す必要があります。

リスト 2 で、単体テストを実行するには、キーボードの組み合わせを入力するか、 **R ctrl キーを押し、A**かをクリックして、**ソリューション内のすべてのテストを実行**(図 1 を参照してください) ボタンをクリックします。 テストに合格した場合は、図 2 でテスト結果 ウィンドウが表示されます。


[![ソリューション内のすべてのテストを実行します。](creating-unit-tests-for-asp-net-mvc-applications-vb/_static/image2.png)](creating-unit-tests-for-asp-net-mvc-applications-vb/_static/image1.png)

**図 01**: ソリューション内のすべてのテストの実行 ([フルサイズのイメージを表示するをクリックして](creating-unit-tests-for-asp-net-mvc-applications-vb/_static/image3.png))


[![正常にされました。](creating-unit-tests-for-asp-net-mvc-applications-vb/_static/image5.png)](creating-unit-tests-for-asp-net-mvc-applications-vb/_static/image4.png)

**図 02**: 成功! ([フルサイズのイメージを表示するをクリックして](creating-unit-tests-for-asp-net-mvc-applications-vb/_static/image6.png))


## <a name="testing-the-view-data-returned-by-a-controller"></a>コント ローラーによって返されるデータの表示のテスト

MVC のコント ローラーと呼ばれるものを使用してビューにデータを渡します *`View Data`*です。 たとえばを呼び出すときは、特定の製品の詳細を表示すること、`ProductController Details()`アクション。 インスタンスを作成する場合は、 `Product` (モデルで定義されている) クラスをインスタンスに渡すと、`Details`活用してビュー`View Data`です。

変更された`ProductController`リスト 3 の更新が含まれています`Details()`製品を返すアクションです。

**3 – を一覧表示します。`ProductController.vb`**

[!code-vb[Main](creating-unit-tests-for-asp-net-mvc-applications-vb/samples/sample5.vb)]

最初に、`Details()`アクションの新しいインスタンスを作成する、`Product`ラップトップ コンピューターを表すクラスです。 次のインスタンス、`Product`クラスが 2 番目のパラメーターとして渡される、`View()`メソッドです。

単体テストを記述することができます、必要なデータがあるかどうかをテストするデータが含まれて表示されます。 単体テストに、4 の一覧表示するテストで呼び出すときに、ラップトップ コンピューターを表す製品が返されるかどうか、`ProductController Details()`アクション メソッド。

**4 – を一覧表示します。`ProductControllerTest.vb`**

[!code-vb[Main](creating-unit-tests-for-asp-net-mvc-applications-vb/samples/sample6.vb)]

4 の一覧で、`TestDetailsView()`メソッド呼び出しによって返されるデータの表示のテスト、`Details()`メソッドです。 `ViewData`にプロパティとして公開される、`ViewResult`呼び出しによって返される、`Details()`メソッドです。 `ViewData.Model`プロパティにはビューに渡される、製品が含まれています。 テストでは、単に名ラップトップがビュー データに含まれている製品であることを確認します。

## <a name="testing-the-action-result-returned-by-a-controller"></a>アクションの結果をテスト コント ローラーによって返される

複雑なコント ローラーのアクションは、さまざまな種類のコント ローラーのアクションに渡されるパラメーターの値によってアクション結果を返す場合があります。 コント ローラーのアクションはさまざまな種類のなどのアクション結果を返すことができます、 `ViewResult`、 `RedirectToRouteResult`、または`JsonResult`です。

たとえば、変更された`Details()`5 の一覧でアクションを返します、`Details`アクションに有効な製品 Id を渡す場合に表示します。 1--後にリダイレクトするよりも小さく、無効な製品 Id--値を持つ Id を渡す場合、`Index()`アクション。

**5 – を一覧表示します。`ProductController.vb`**

[!code-vb[Main](creating-unit-tests-for-asp-net-mvc-applications-vb/samples/sample7.vb)]

動作をテストすることができます、`Details()`リスト 6 で、単体テストで操作します。 単体テストを一覧表示する 6 にリダイレクトされていることを確認、`Index`に値が-1 の Id が渡されるときの表示、`Details()`メソッドです。

**6 – を一覧表示します。`ProductControllerTest.vb`**

[!code-vb[Main](creating-unit-tests-for-asp-net-mvc-applications-vb/samples/sample8.vb)]

呼び出すと、`RedirectToAction()`コント ローラーのアクションで、コント ローラーのアクションを返します、`RedirectToRouteResult`です。 テスト チェックするかどうか、`RedirectToRouteResult`という名前のコント ローラー アクションをユーザーをリダイレクト`Index`です。

## <a name="summary"></a>概要

このチュートリアルでは、MVC コント ローラー アクションの単体テストを作成する方法について学習しました。 最初に、右側のビューがコント ローラーのアクションによって返されるかどうかを確認する方法を学習します。 使用する方法を学習しました、`ViewResult.ViewName`プロパティをビューの名前を確認します。

内容をテストする方法を確認して次に、`View Data`です。 適切な製品が返されたかどうかを確認する方法を学習した`View Data`コント ローラーのアクションの呼び出し後にします。

最後に、さまざまな種類のアクションの結果はコント ローラーのアクションから返されるかどうかをテストする方法について説明します。 コント ローラーを返すかどうかをテストする方法を学習、`ViewResult`または`RedirectToRouteResult`です。

>[!div class="step-by-step"]
[前へ](creating-unit-tests-for-asp-net-mvc-applications-cs.md)
