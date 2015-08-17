<properties
   pageTitle="Azure Data Catalog の一般的なシナリオ"
   description="Azure Data Catalog での一般的なシナリオ: データ ソースの登録、拡充、調査、理解、使用、およびデータ ソース メタデータの削除を確認します。"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/31/2015"
   ms.author="maroche"/>


# Azure Data Catalog の一般的なシナリオ

この記事では、Azure Data Catalog を使用して組織が既存のデータ ソースからさらなる価値を得ることができる一般的なシナリオを紹介します。

## シナリオ 1 - 中央データ ソースの登録

多くの場合、組織には多くの重要なデータ ソースがあります。これらのデータ ソースには、一連のビジネス OLTP システム、データ ウェアハウス、ビジネス インテリジェンス/分析データベースなどが含まれます。ビジネスのニーズが進化し、また、吸収合併によってビジネス自体が進化すると、多くの場合、システムの数およびシステム間の重複が増大していきます。

多くの場合、こうしたデータ ソース内のどこにデータがあるかをユーザーが把握するのは困難です。次のような疑問が生じがちです。

- この種のレポートを作成するには、企業内で使用される 3 つの HR システムのうちどれを使用すればよいか。
- 終了した会計年度について、認定済みの販売数はどこで取得できるか。
- データ ウェアハウスへのアクセス方法についてだれに聞けばよいか。また、アクセスのためにどのようなプロセスを用いるべきか。
- これらの数値が正しいかどうかわからないが、このダッシュボードをチームと共有する前に、このデータの使用方法についてだれに相談すればよいか。

このようなシナリオでは、Azure Data Catalog が役立ちます。多くの場合、組織全体で使用される、IT で管理された高価値の中央データ ソースが、カタログの設定における論理的な起点となります。いずれのユーザーもデータ ソースを登録できますが、最も多くのユーザーに価値を提供する可能性があるデータ ソースからカタログを開始すれば、ドライブを導入しシステムを使用する上で便利です。Azure Data Catalog を使用し始めるお客様にとって、多くの異なるデータ コンシューマーのチームで使用される主要なデータ ソースを識別して登録することが、成功のための第一歩となりえます。

このシナリオは、高価値のデータ ソースの理解とアクセスを容易にするために注釈を付ける機会も示します。この取り組みの 1 つの重要な側面は、ユーザーがデータ ソースへのアクセスを要求する方法に関する情報を含めることです。Azure Data Catalog では、データ ソースへのアクセスを管理するユーザーまたはチームの電子メール アドレス、既存のツールまたはドキュメントへのリンク、またはアクセス要求プロセスを説明する自由書式のテキストを提供することができます。このような情報をカタログに含めることにより、登録されているデータ ソースを検出したにもかかわらずそのデータにアクセスする権限のないユーザーが、データ ソースの所有者によって定義および管理されているプロセスを使用して簡単にアクセスを要求できます。

## シナリオ 2 - セルフ サービス ビジネス インテリジェンス

多くの組織において、従来の企業のビジネス インテリジェンス ソリューションがデータ ランドス ケープの非常に重要な部分であり続ける一方で、ビジネスの変化速度に対応するために、セルフ サービス BI がますます重要なものとなっています。セルフ サービス BI を使用すると、インフォメーション ワーカーおよびアナリストは、中央の IT チームに依存したり、IT チームのスケジュールや可用性によって制限されたりすることなく、独自のレポート、ワークブック、およびダッシュボードを作成することができます。

セルフ サービス BI のシナリオでは、通常、ユーザーは複数のソースのデータを結合します。これらの多くは、BI および分析のために以前に使用されていません。これらのデータ ソースには既知のものもありますが、多くの場合、所定のタスク用の潜在的なデータ ソースを特定して評価するために必要なものを探索するプロセスが発生します。

従来、この検出プロセスは手動であり、アナリストはピア ネットワーク接続を使用して、検出するデータを使用する他のユーザーを特定します。データ ソースが見つかると使用できますが、複数のユーザーが同じ冗長な手動検出プロセスを実行すると、後続のセルフ サービス BI 作業ごとにプロセス自体が繰り返されます。

Azure Data Catalog を使用すると、ユーザーはこの冗長な作業のサイクルを中断できます。従来の方法でデータ ソースが検出されたら、アナリストは、今後データ ソースをより簡単に検出できるように登録することができます。ユーザーは、登録済みのデータ アセットに注釈を付けることによってさらに価値を付加できますが、これは登録と同時に実行する必要はありません。ユーザーは、スケジュールに合わせて経時的に共同作成を行い、カタログに登録されているデータ ソースに徐々に価値を付加することができます。

このカタログ コンテンツの有機的成長により、中央データ ソースの初期登録が自然に補完されていきます。多くのユーザーが必要とするデータをカタログに事前設定すれば、初期の使用と検出の動機付けとなります。ユーザーが追加のソースを登録して注釈を設定できるようにすることで、ユーザーおよびその同僚を継続的に作業に関与させることができます。

また、このシナリオではセルフ サービス BI に焦点が当てられていますが、同じパターンと課題が大規模な企業 BI プロジェクトにも当てはまることにも注目してください。データ ソース検出の手動プロセスに関連するすべての作業で、Azure Data Catalog を使用して組織に価値を付加することができます。

## シナリオ 3 - 組織固有の知識のキャプチャ

仕事に必要なデータとそのデータの検索場所を知るには、どうすればよいでしょうか。

仕事に一定期間従事している場合は、自ずと把握できるでしょう。段階的な学習プロセスを経て、日々の作業にとって重要なデータ ソースについて理解していきます。

チームに参加したての新しい従業員の場合は、仕事に必要なデータとその検索場所をどのようにして知ることができるでしょうか。

あなたに質問するかもしれません。

この組織固有の知識の持続的な伝達は、大小の組織におけるデータ ソース検出プロセスの一環です。熟練した先輩のチーム メンバーは、長年にわたって知識を蓄積しており、新しいチーム メンバーは、質問があればこれらの先輩に質問すればよいとわかってきます。多くの場合、最も重要な情報は少数の従業員の頭の中だけに存在しており、これらの従業員が休暇中またはチームを離れた場合、組織は困難に直面します。

場合によっては、これらのデータ エキスパートは、電子メール経由、またはチームの SharePoint サイトで Word 文書を共有して、知識を文書化する取り組みをします。この取り組みは有用かもしれませんが、どのようなドキュメントがどこに存在するかをどのようにして知ることができるか、という検出に関する新しい問題が生じます。

Azure Data Catalog は、この組織固有の知識を共有し、簡単に検出できるようにするための場所を提供します。データ エキスパートは、データ アセットに直接注釈を付けるほか、既存のドキュメントにリンクを含めることもできます。これにより、知識自体がキャプチャされるだけでなく、データ ソース検出に使用するのと同じエクスペリエンス内に知識が格納されます。カタログを使用してデータ ソースを検出すると、ソース自体だけでなく、以前はエキスパート自身の頭の中だけに存在していた知識も見つけることができます。

<!---HONumber=August15_HO6-->