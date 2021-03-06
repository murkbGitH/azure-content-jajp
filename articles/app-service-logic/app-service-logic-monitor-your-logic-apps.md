<properties 
	pageTitle="Logic Apps を監視する | Microsoft Azure" 
	description="Logic Apps の作業内容を表示する方法。" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/29/2015"
	ms.author="stepsic"/>

#Logic Apps を監視する

「[ロジック アプリの作成](app-service-logic-create-a-logic-app.md)」で説明されている手順に従ってロジック アプリを作成した後、Azure ポータルで実行の完全な履歴を確認できます。履歴を表示するには、ポータルの画面の左側にある **[参照]** をクリックして **[Logic Apps]** を選択します。サブスクリプション内の Logic Apps の一覧が表示されます。ロジック アプリは**有効**または**無効**にできます。**有効**な Logic Apps は、トリガーによるトリガー イベントに反応してロジック アプリが実行されることを意味します。**無効**なロジック アプリは、イベントに反応して実行されることはありません。

![概要](./media/app-service-logic-monitor-your-logic-apps/overview.png)

表示されたロジック アプリのブレードには、役に立つセクションが 2 つあります。

- **[概要]** は、最新の状態を示す、ロジック アプリを編集するためのエントリ ポイントです。
- **[すべての実行]** は、このロジック アプリの実行内容の一覧を示します。

##実行

![すべての実行](./media/app-service-logic-monitor-your-logic-apps/allruns.png)

この実行の一覧には、特定の実行の**開始時刻**、**実行識別子** (これを使用して REST API を呼び出すことができる)、**期間**が表示されます。実行の詳細を表示する行をクリックします。

詳細ブレードには、グラフが実行時間とその実行でのすべてのアクションの順序と共に表示されます。その下に、実行されたすべてのアクションの完全な一覧が表示されます。

![実行とアクション](./media/app-service-logic-monitor-your-logic-apps/runandaction.png)

最後に、特定のアクションに関して、そのアクションに渡されたすべてのデータやアクションから受信したすべてのデータが、**[入力]** セクションおよび **[出力]** セクションに表示されます。すべてのコンテンツを表示するには、リンクをクリックします (リンクをコピーして、コンテンツをダウンロードすることもできます)。

もう 1 つの重要な情報は、**トレース ID** です。この ID は、すべてのアクションの呼び出しのヘッダーで渡されます。独自のサービス内でログを記録している場合、トレース ID を記録することをお勧めします。これによって、この ID を使用して独自のログと相互参照することができます。

##トリガー履歴 

ポーリング トリガーはある間隔を置いて API を確認しますが、必ずしも実行が開始されるわけではなく、開始されるかどうかは応答によって決まります (たとえば、`200` は実行することを、`202` は実行しないことを意味します) 。トリガー履歴には、発生したがロジック アプリが実行されていないすべての呼び出しが表示されます (`202` 応答)。

![トリガー履歴](./media/app-service-logic-monitor-your-logic-apps/triggerhistory.png)

トリガーごとに、その状態 (**実行した**、実行しなかった、または何らかのエラーが発生した (**失敗した**)) を確認できます。トリガーが失敗した理由を調べるために、**[出力]** リンクをクリックできます。実行しなかった場合は、**[実行]** リンクをクリックして、実行後に何が発生するかを確認します。

Push トリガーの場合、実行を開始した時間はここには表示されません。代わりに、コールバック登録呼び出しが表示されます。これらは、コールバックを取得するためにロジック アプリをいつ登録したかを示します。Push トリガーが機能しない場合、登録に問題がある可能性がありますが ([出力] で確認できます)、それ以外の場合は、その API を特に調べる必要があります。

##バージョン管理

現在の UI では利用できませんが、まもなく [REST API](http://go.microsoft.com/fwlink/?LinkID=525617&clcid=0x409) 経由で利用できるようになる追加機能があります。ロジック アプリの定義を更新すると、定義の前のバージョンが保存されます。その理由は、実行が既に進行中の場合、その実行は、実行開始時のロジック アプリのバージョンを参照するためです。実行の定義は進行中に変更できません。バージョン履歴 REST API によって、この情報にアクセスできます。
 

<!---HONumber=Oct15_HO3-->