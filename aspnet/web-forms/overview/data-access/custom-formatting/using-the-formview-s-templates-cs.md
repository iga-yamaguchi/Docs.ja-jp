---
uid: web-forms/overview/data-access/custom-formatting/using-the-formview-s-templates-cs
title: "FormView のテンプレート (c#) を使用して |Microsoft ドキュメント"
author: rick-anderson
description: "DetailsView とは異なり FormView いないフィールドで構成されます。 代わりに、テンプレートを使用して、FormView がレンダリングされます。 このチュートリアルでは、F. を使用して検証."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2010
ms.topic: article
ms.assetid: d3f062af-88cf-426d-af44-e41f32c41672
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/custom-formatting/using-the-formview-s-templates-cs
msc.type: authoredcontent
ms.openlocfilehash: 18e76a763e22c0d1046acc60e095bbd11960c5e6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2017
---
<a name="using-the-formviews-templates-c"></a>FormView のテンプレート (c#) を使用します。
====================
によって[Scott Mitchell](https://twitter.com/ScottOnWriting)

[サンプル アプリをダウンロード](http://download.microsoft.com/download/9/6/9/969e5c94-dfb6-4e47-9570-d6d9e704c3c1/ASPNET_Data_Tutorial_14_CS.exe)または[PDF のダウンロード](using-the-formview-s-templates-cs/_static/datatutorial14cs1.pdf)

> DetailsView とは異なり FormView いないフィールドで構成されます。 代わりに、テンプレートを使用して、FormView がレンダリングされます。 このチュートリアルについて見ていきましょうで FormView コントロールを使用して、データの小さい固定表示を表示します。


## <a name="introduction"></a>はじめに

最後の 2 つのチュートリアルでは、TemplateFields を使用して、GridView と DetailsView コントロールの出力をカスタマイズする方法を説明しました。 TemplateFields では、高度にカスタマイズするには、特定のフィールドの内容が最終的に、GridView と DetailsView 角張ったではなく、グリッドのような外観にします。 多くのシナリオのようなグリッドのようなレイアウトは、理想的なですより滑らかな小さい固定表示が必要な場合です。 1 つのレコードを表示するには、このような滑らかなレイアウトが FormView コントロールを使用できます。

DetailsView とは異なり FormView いないフィールドで構成されます。 BoundField または TemplateField をフォーム ビューに追加することはできません。 代わりに、テンプレートを使用して、FormView がレンダリングされます。 FormView の単一 TemplateField を表す DetailsView コントロールとして考えます。 FormView では、次のテンプレートがサポートされています。

- `ItemTemplate`FormView で表示される特定のレコードを表示するために使用します。
- `HeaderTemplate`省略可能なヘッダー行を指定するために使用
- `FooterTemplate`省略可能なフッター行を指定するために使用
- `EmptyDataTemplate`ときに FormView の`DataSource`、レコードがない、`EmptyDataTemplate`の代わりに使用される、`ItemTemplate`コントロールのマークアップを表示するため
- `PagerTemplate`インターフェイスをカスタマイズする、ページングを有効になっているページングを持つ FormViews を使用できます。
- `EditItemTemplate` / `InsertItemTemplate`このような機能をサポートする FormViews の編集用のインターフェイスまたは挿入するインターフェイスをカスタマイズするために使用

このチュートリアルについて見ていきましょうで FormView コントロールを使用して、製品の小さい固定表示を表示します。 名前、カテゴリ、供給業者、およびな、FormView のためのフィールドではなく`ItemTemplate`ヘッダー要素の組み合わせを使用してこれらの値が表示されます、 `<table>` (図 1 を参照してください)。


[![FormView に DetailsView で表示されるグリッドのようなレイアウトの分類します。](using-the-formview-s-templates-cs/_static/image2.png)](using-the-formview-s-templates-cs/_static/image1.png)

**図 1**: FormView Grid-Like レイアウト表示外 DetailsView で改行を ([フルサイズのイメージを表示するをクリックして](using-the-formview-s-templates-cs/_static/image3.png))


## <a name="step-1-binding-the-data-to-the-formview"></a>手順 1: FormView へのデータのバインド

開く、`FormView.aspx`ページして、FormView をツールボックスからデザイナーにドラッグします。 私たちを指示する灰色のボックスとして表示されます、FormView を最初に追加するときにする、`ItemTemplate`が必要です。


[![FormView は ItemTemplate が提供されるまで、デザイナーで表示することはできません。](using-the-formview-s-templates-cs/_static/image5.png)](using-the-formview-s-templates-cs/_static/image4.png)

**図 2**: フォーム ビューで表示できない、デザイナーになるまで、`ItemTemplate`は提供 ([フルサイズのイメージを表示するをクリックして](using-the-formview-s-templates-cs/_static/image6.png))


`ItemTemplate` (を介して宣言の構文) を手動で作成できるまたは FormView をデザイナーでデータ ソース コントロールにバインドすることによって自動作成を指定できます。 自動的に作成されたこの`ItemTemplate`HTML、リストの各フィールドとラベルの名前を制御が含まれている`Text`プロパティ、フィールドの値にバインドします。 このアプローチも自動で作成、`InsertItemTemplate`と`EditItemTemplate`、どちらもはの各データ ソース コントロールによって返されるデータ フィールドの入力コントロールに設定されます。

テンプレートを自動的に作成する場合は、フォーム ビューのスマート タグから新しいコントロールを追加 ObjectDataSource を呼び出す、`ProductsBLL`クラスの`GetProducts()`メソッドです。 これで、フォーム ビューが作成されます、 `ItemTemplate`、 `InsertItemTemplate`、および`EditItemTemplate`です。 ソース ビューから削除、`InsertItemTemplate`と`EditItemTemplate`に興味がないため、フォーム ビューの作成をサポートしている編集、またはまだを挿入します。 次に、内のマークアップをクリアします、`ItemTemplate`お白紙から作業できるようにします。

代わり構築する場合、 `ItemTemplate` 、手動で追加し、ツールボックスからデザイナーにドラッグして、ObjectDataSource を構成します。 ただし、しないと、デザイナーから FormView のデータ ソースを設定します。 代わりに、ソース ビューに移動し、手動で設定して、FormView`DataSourceID`プロパティを`ID`ObjectDataSource の値。 次に、手動で追加、`ItemTemplate`です。

どのような方法に関係なくすることが決定するために、この時点で、フォーム ビューの宣言型マークアップように見える必要があります。


[!code-aspx[Main](using-the-formview-s-templates-cs/samples/sample1.aspx)]

FormView のスマート タグのページングを有効にするチェック ボックスを確認します。これが追加されます、`AllowPaging="True"`属性をフォーム ビューの宣言の構文にします。 また、設定、`EnableViewState`プロパティを False にします。

## <a name="step-2-defining-theitemtemplates-markup"></a>手順 2: を定義する、`ItemTemplate`のマークアップ

FormView ObjectDataSource コントロールにバインドされ、構成されているページングをサポートするために使用する準備ができましたのコンテンツを指定、`ItemTemplate`です。 このチュートリアルに表示される製品の名前を持つみましょう、`<h3>`見出し。 次に、HTML を使ってみましょう`<table>`4 つの列とテーブルを最初と 3 番目の列に、プロパティ名を表示する、2 番目と 4 番目の値の一覧に、残りの製品プロパティを表示します。

このマークアップのデザイナーで、フォーム ビューのテンプレートの編集インターフェイス経由での入力または宣言の構文を手動で入力できます。 テンプレートを使用するときに通常の検索、宣言の構文を直接操作が、自由に最も慣れている手法を使用する方が手軽です。

次のマークアップが後にフォーム ビューの宣言型マークアップを示しています、`ItemTemplate`の構造が完了しました。


[!code-aspx[Main](using-the-formview-s-templates-cs/samples/sample2.aspx)]

注意して、データ バインド構文`<%# Eval("ProductName") %>`の例は、テンプレートの出力に直接挿入できます。 つまり、必要ないに割り当てることがラベル コントロールの`Text`プロパティです。 たとえば、ある、`ProductName`に表示される値、`<h3>`要素を使用して`<h3><%# Eval("ProductName") %></h3>`、製品の Chai としてレンダーする`<h3>Chai</h3>`です。

`ProductPropertyLabel`と`ProductPropertyValue`CSS クラスは、製品プロパティの名前と値のスタイルを指定するために使用されます、`<table>`です。 これらの CSS クラスが定義されている`Styles.css`太字と右揃えにして、右のプロパティの値に余白を追加するプロパティの名前が発生するとします。

表示するのには、フォーム ビューで使用可能な CheckBoxFields がないので、`Discontinued`値チェック ボックスとして独自のチェック ボックス コントロールを追加します。 `Enabled`プロパティが False、読み取り専用、およびチェック ボックスの`Checked`プロパティの値にバインド、`Discontinued`データ フィールドです。

`ItemTemplate`完了すると、製品情報が表示されますより滑らかな方法でします。 (図 4) このチュートリアルでは、フォーム ビューによって生成される出力と前回のチュートリアル (図 3) からの出力を DetailsView を比較します。


[![固定された DetailsView 出力](using-the-formview-s-templates-cs/_static/image8.png)](using-the-formview-s-templates-cs/_static/image7.png)

**図 3**: 固定 DetailsView 出力 ([フルサイズのイメージを表示するをクリックして](using-the-formview-s-templates-cs/_static/image9.png))


[![流体 FormView 出力](using-the-formview-s-templates-cs/_static/image11.png)](using-the-formview-s-templates-cs/_static/image10.png)

**図 4**: 流体 FormView 出力 ([フルサイズのイメージを表示するをクリックして](using-the-formview-s-templates-cs/_static/image12.png))


## <a name="summary"></a>概要

GridView と DetailsView コントロールには、その出力 TemplateFields を使用してカスタマイズできますがある、中に両方も、データ形式で表示する、グリッドのような角張ったです。 1 つのレコードを表示する必要がある場合も、小さい固定レイアウトを使用して、FormView では、最適な選択肢です。 FormView が 1 つのレコードを表示、DetailsView のようにその`DataSource`が DetailsView とは異なり、テンプレートのだけで構成されて、フィールドをサポートしていません。

このチュートリアルで説明したとおり、FormView より柔軟なレイアウトを単一のレコードを表示するときにします。 将来的にチュートリアルについて見ていきましょう、DataList リピータ コントロール、柔軟性、FormsView としての同じレベルで提供が (GridView) などの複数のレコードを表示することができます。

満足プログラミング!

## <a name="about-the-author"></a>作成者について

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml)、7 つ受け取りますブックとの創設者の作成者[4GuysFromRolla.com](http://www.4guysfromrolla.com)、1998 年からマイクロソフトの Web テクノロジで取り組んできました。 Scott は、コンサルタント、トレーナー、ライターとして機能します。 最新の著書[ *Sam 学べる自分で ASP.NET 2.0 が 24 時間以内に*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco)です。 彼に到達できる[ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)彼のブログを使用して含まれているのか[http://ScottOnWriting.NET](http://ScottOnWriting.NET)です。

## <a name="special-thanks-to"></a>感謝の特別な

このチュートリアルの系列は既に多くの便利なレビュー担当者によって確認済みです。 このチュートリアルのレビュー担当者の潜在顧客が E.R. Gilmore です。 今後、MSDN の記事を確認することに関心のあるですか。 場合は、ドロップ me 一度に 1 行ずつ[mitchell@4GuysFromRolla.comです。](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[前へ](using-templatefields-in-the-detailsview-control-cs.md)
[次へ](displaying-summary-information-in-the-gridview-s-footer-cs.md)
