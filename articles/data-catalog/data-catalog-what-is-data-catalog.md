<properties
   pageTitle="Azure Data Catalog のデータ カタログ"
   description="Microsoft Azure Data Catalog は、完全に管理されたクラウド サービスであり、エンタープライズ データ ソースの登録のシステムと検出のシステムとして機能します。Azure Data Catalog は、アナリストからデータ サイエンティスト、開発者まで、すべてのユーザーによるデータ ソースの登録、検出、理解、および使用を可能にする機能を備えています。"
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

# Azure Data Catalog とは何ですか

Microsoft **Azure Data Catalog** は、完全に管理されたクラウド サービスであり、エンタープライズ データ ソースの登録のシステムと検出のシステムとして機能します。**Azure Data Catalog** は、アナリストからデータ サイエンティスト、開発者まで、すべてのユーザーによるデータ ソースの登録、検出、理解、および使用を可能にする機能を備えています。

## 問題の説明 - 動機と概要

従来、エンタープライズ データ ソースの検出は、仲間の知識に基づいた組織的プロセスでした。このプロセスは、情報資産の活用を考える企業にとって多数の課題があります。

-	ユーザーは、別のプロセスの一環でデータ ソースを利用するまで、そのデータ ソースが存在することを知りません。データ ソースが一元的に登録されている場所はありません。
-	ユーザーがデータ ソースの場所を知らない場合、クライアント アプリケーションを使用してデータに接続できません。データを利用するには、ユーザーが接続文字列またはパスを知っている必要があります。
-	ユーザーがデータ ソースのドキュメントの場所を知らない場合、データの用途を理解できません。データ ソースとドキュメントが複数の場所に保存され、さまざまな状況で使用されます。
-	情報資産について不明な点がある場合、ユーザーはデータを担当するエキスパートまたはチームを特定し、オフラインでエキスパートに確認する必要があります。データと、データの用途に関するエキスパートの観点との間に明確なつながりはありません。
-  データ ソースへのアクセスを要求するプロセスを理解しない限り、データ ソースやそのドキュメントを検出できても、必要なデータにアクセスすることはできません。

データの利用者は、このような課題に直面します。一方、情報資産の作成と保守を担当するユーザーにも、独自の課題があります。

-	データ ソースに記述メタデータを使用して注釈を付ける作業は、多くの場合、無駄になります。通常、クライアント アプリケーションは、データ ソースに保存されている記述を無視するためです。
-	データ ソースのドキュメントを作成する作業も、多くの場合は無駄になります。ドキュメントとデータ ソースの同期を維持することは継続的な責任であり、内容が古いと見なされてドキュメントを信頼しなくなることがよくあります。
- データ ソースへのアクセスを制限し、データの利用者がアクセス権の要求方法を確実に理解するようにすることが、現在の課題です。

データ ソースのドキュメントの作成と保守は複雑で、時間がかかる作業です。データ ソースを使用する全員がすぐにドキュメントを利用できるようにするという課題は、さらに大変です。

このような課題が複数存在すると、エンタープライズ データの使用と理解を推奨したいと考える会社にとって大きな障壁になりまする

## サービスの説明

**Azure Data Catalog** は、このような問題に対処し、企業が既存の情報資産を活用できるように設計されています。管理しているデータを必要としているユーザーが簡単にデータを検出し、理解できるようになります。

**Azure Data Catalog** は、データ ソースを登録できるクラウドベースのサービスを提供しています。データは元の場所に残りますが、メタデータのコピーと、データ ソースの場所に対する参照が **Azure Data Catalog** に追加されます。このメタデータのインデックスも作成されるので、検索で簡単に各データ ソースを見つけられるようになり、検出するユーザーにとって理解しやすくなります。

データ ソースを登録すると、登録したユーザーまたは社内の他のユーザーがメタデータを強化できるようになります。すべてのユーザーが、記述、タグ、その他のメタデータ (ドキュメントやデータ ソースへのアクセス要求のプロセスなど) を指定してデータ ソースに注釈を設定できます。この記述メタデータで、データ ソースから登録される構造メタデータ (列名やデータ型など) が補足され、データ ソースの検出と理解が簡単になります。

データ ソースとその用途の検出することは、ソースの登録の主な目的です。エンタープライズ ユーザーが仕事 (ビジネス インテリジェンス、アプリケーション開発、データ サイエンスなど、適切なデータが必要な作業) のためにデータが必要な場合、**Azure Data Catalog** の検出エクスペリエンスを使用すると、ニーズに合うデータを簡単に検出し、データを理解して目的に合っているかどうかを評価し、好みのツールでデータ ソースを開いてデータを使用することができます。

## データ ソースの登録

データ ソースの登録には、**Azure Data Catalog** のデータ ソース登録ツールを使用します。このアプリケーションは、**Azure Data Catalog** ポータルからダウンロードできます。

登録プロセスには、次の 3 つの基本的な手順があります。

1.	データ ソースに接続する - ユーザーは、データ ソースの場所と、データ ソースに接続する資格情報を指定します (SQL Server インスタンスなど)。
2.	登録するオブジェクトを選択する - ユーザーは、指定した場所にあるオブジェクトを選択します。オブジェクトは、**Azure Data Catalog** に登録しておく必要があります。たとえば、サーバー上のすべてのデータベースのテーブル一式や、明示的に選択したテーブルやビューの一部などのオブジェクトです。
3.	登録を完了する - ユーザーがプロセスを完了すると、データ ソース登録ツールで、データ ソースから構造メタデータが抽出され、構造メタデータは **Azure Data Catalog** クラウド サービスに送信されます。

> [AZURE.NOTE]現在、**Azure Data Catalog** のプレビューでは、次のデータ ソースと資産の種類をサポートしています。

- SQL Server テーブル
- SQL Server ビュー
- Oracle Database テーブル
- Oracle Database ビュー
- SQL Server Analysis Services Multidimensional Dimension
- SQL Server Analysis Services Multidimensional Measure
- SQL Server Analysis Services Multidimensional KPI
- SQL Server Analysis Services 表形式テーブル
- SQL Server Reporting Services レポート

**Azure Data Catalog** のプレビュー期間中に、データ ソースと資産の種類は追加される予定です。

> [AZURE.IMPORTANT]**Azure Data Catalog** にデータ ソースを登録しても、データ ソース登録ツールで [プレビューを含める] を選択しない限り、データ ソースのデータはコピーされません。登録によって、データ ソースのデータではなくメタデータがコピーされます。メタデータには、たとえばテーブルや他のデータ ソース オブジェクトの名前や、列などのデータ ソース属性の名前やデータ型などがあります。また、データ ソースの場所も含まれるので、ユーザーが **Azure Data Catalog** を使用してデータ ソースを検出すると、データ ソースに接続することができます。[プレビューを含める] を選択すると、データ ソース登録ツールによって少数のレコードが **Azure Data Catalog** にコピーされます。このレコードは、ユーザーが **Azure Data Catalog** ポータルでデータ ソースを検出したときに表示されます。

## データ ソース メタデータの強化

登録が完了すると、データ ソースを検出し、使用できるようになりますが、**Azure Data Catalog** の本当の価値は、データ ソースから抽出された構造メタデータと同様に、ビジネスの記述メタデータを登録できる点にあります。この追加のメタデータには、3 つの大きなメリットがあります。

-	登録済みデータ ソースをさらに簡単に検出できるようになります。ユーザーが指定したメタデータが **Azure Data Catalog** の検索インデックスに追加されます。そのため、ユーザーは、元のデータ ソースには存在しない用語や概念でも、データの検出に使用できるようになります。たとえば、"tbl\_c45" という顧客データを含むデータベース テーブルがある場合、"顧客" というフレンドリ名を付けることで、顧客データを探しているユーザーが簡単に検出できるようになります。同様に、データを使用するレポート、ダッシュボード、またはプロセスの名前を含む記述を指定することで、下流のアーティファクトを検索用語として使用するユーザーが、データ ソースを簡単に見つけられるようになります。
-	登録済みのデータ ソースは、検出後にさらに理解しやすくなります。ユーザーが指定したメタデータは、注釈付きのデータ ソースを閲覧するすべての **Azure Data Catalog** ユーザーに表示され、追加の文脈と情報として役立ちます。通常、多くのデータ ソースには、意味のある記述やドキュメントは含まれていません。含まれる場合でも、多くは技術的な DBA またはデータベース開発者向けです。**Azure Data Catalog** で利用者に適した記述とタグを使用してデータ ソースを強化することで、データを検出したユーザーがデータの詳細と用途を理解できるようになります。
-  登録されている各データ ソースにアクセスを要求する情報を含めることで、ユーザーは該当するデータ ソースとそのデータへのアクセスを要求する既存のプロセスを簡単に理解し、実行できるようになります。

> [AZURE.NOTE]各 **Azure Data Catalog** ユーザーは、データ資産と属性について独自のタグと記述を追加できます。**Azure Data Catalog** で各注釈の値とソースが追跡され、注釈を追加したユーザーと日付が表示されます。このメタデータのクラウドソーシング アプローチによって、データと用途について観点を持っているすべてのユーザーは、意見やリソースをユーザー コミュニティ全体と共有できるようになります。

## 探索、検出、理解

**Azure Data Catalog** にデータ ソースを登録し、強化する作業の目標は、社内の全ユーザーがデータ ソースを検出し、理解し、使用できるようにすることです。**Azure Data Catalog** ポータルは、このプロセスの主要ツールです。

**Azure Data Catalog** ポータルには、データの探索と検出のために、検索とフィルターという 2 つの主要メカニズムがあります。

**Azure Data Catalog** のデータ ソースを検索するには、**Azure Data Catalog** ポータルの検索ボックスに検索用語を入力します。その検索用語と一致する各登録済みデータ ソースのタイルが表示されます。タイルには、名前、説明、データ ソースに割り当てられているタグだけでなく、他の概要情報が含まれます。

**Azure Data Catalog** のコンテンツをフィルターするには、**Azure Data Catalog** ポータルにある 1 つ以上のフィルターを選択します。フィルターを選択すると、指定したフィルター条件に合うタイルのみがポータルに表示されます。検索せずにデータ ソースをフィルターできます。また、検索結果をフィルターすることもできます。

データ ソースの詳細情報を表示し、作業に適しているデータ ソースかどうかをすぐに理解するには、データ ソースのタイルをクリックします。すべてのメタデータを含むプロパティ ウィンドウが表示されます。

プロパティ ウィンドウの上部には、追加のボタンがあります。

1.	プレビュー: データ ソースの登録時にプレビューを選択した場合、このボタンをクリックすると、データ ソースの静的なプレビュー レコード セットが表示されます。
2.	スキーマ: このボタンをクリックすると、データ ソースのスキーマ (列の名前とデータ型、**Azure Data Catalog** の列レベルのメタデータなど) が表示されます。

> [AZURE.NOTE]**探索**エクスペリエンスは、**使用**エクスペリエンスのエントリ ポイントだけでなく、**強化**エクスペリエンスのエントリ ポイントにもなりえます。**Azure Data Catalog** で実現されるクラウドソーシング アプローチによって、登録済みデータ ソースを検出するユーザーは、検出したデータを使用できるだけでなく、データに関する自分の意見を共有できるようになります。

## データ ソースのメタデータの削除

データ ソースを登録した後に、**Azure Data Catalog** からのデータ ソース参照の削除が必要になる場合があります。たとえば、ビジネス要件の変化や、ソース システムの使用停止などのためです。理由にかかわらず、**Azure Data Catalog** では、削除するデータ ソースを選択するだけで簡単に削除できます。削除したデータは、検出も使用もできなくなります。

> [AZURE.IMPORTANT]**Azure Data Catalog** からデータ ソースを削除すると、**Azure Data Catalog** サービスに保存されているメタデータのみが削除されます。元のデータ ソースは、一切影響を受けません。

## データ ソースの使用 

データ検出の最終的な目標は、必要なデータを見つけて、選択したデータ ツールで使用することです。Azure Data Catalog のデータ使用エクスペリエンスでは、2 つの方法でこの機能を利用できます。

1.	**Azure Data Catalog** で直接サポートされているクライアント アプリケーションの場合、ユーザーはポータルにあるデータ ソース タイルの **[開く]** メニューをクリックします。クライアント アプリケーションで、選択したデータ ソースへの接続が開始されます。
2.	ユーザーは、すべてのクライアント アプリケーションで、選択したデータ ソースのプロパティ ウィンドウに表示される接続情報を使用できます。接続情報には、データへの接続に必要なすべての詳細 (サーバー名、データベース名、オブジェクト名) が含まれます。接続情報は、クライアント ツールの接続エクスペリエンスにコピーできます。データ ソースにアクセス要求の詳細が提供されている場合、その情報は接続の詳細の横に表示されます。

> [AZURE.NOTE]Azure Data Catalog のプレビューでは、直接サポートされているのは Microsoft Excel と SQL Server Reporting Services レポート マネージャーのみです。これらは **[開く]** メニューで使用できます。

<!---HONumber=August15_HO6-->