<properties
   pageTitle="Visual Studio Code を使用した ASP.NET 5 Web アプリの作成"
   description="このチュートリアルでは、Visual Studio Code を使用して ASP.NET 5 Web アプリを作成する方法について説明します。"
   services="app-service\web"
   documentationCenter=".net"
   authors="erikre"
   manager="wpickett"
   editor="jimbe"/>

<tags
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/14/2015" 
	ms.author="erikre;tarcher"/>

# Visual Studio Code を使用した ASP.NET 5 Web アプリの作成

## 概要

このチュートリアルでは、[Visual Studio コード (VS コード)](http://code.visualstudio.com//Docs/whyvscode) を使用して ASP.NET 5 Web アプリを作成したり、それを [Azure App Service](../app-service/app-service-value-prop-what-is.md) にデプロイしたりする方法について説明します。ASP.NET 5 は、ASP.NET の刷新版です。ASP.NET 5 は、.NET を使用して最新のクラウドベースの Web アプリを構築するための、新しいオープン ソースのクロスプラットフォーム フレームワークです。詳細については、[ASP.NET 5 の概要](http://docs.asp.net/en/latest/conceptual-overview/aspnet.html)に関するページを参照してください。Azure Service Web Apps については、[Web Apps の概要](app-service-web-overview.md)に関するページを参照してください。

[AZURE.INCLUDE [app-service-web-try-app-service.md](../../includes/app-service-web-try-app-service.md)]

## 前提条件  

* [VS コード](http://code.visualstudio.com/Docs/setup)をインストールします。
* [Node.js](http://nodejs.org/download/) をインストールします。[Node.js](http://nodejs.org/) は、JavaScript を使用して高速かつスケーラブルなサーバー アプリケーションを構築するためのプラットフォームです。Node はランタイム (ノード) であり、[npm](http://www.npmjs.com/) は Node モジュールのパッケージ マネージャーです。このチュートリアルでは、npm を使用して、ASP.NET 5 Web アプリをスキャフォールディングします。
* Git をインストールします。これは、[Chocolatey](https://chocolatey.org/packages/git) または [git-scm.com](http://git-scm.com/downloads) のいずれかの場所からインストールできます。Git を初めて使う場合は、[git-scm.com](http://git-scm.com/downloads) を選択し、Windows コマンド プロンプトから Git と GitBash を使用するオプションを選択します。Git をインストールした後、(VS コードからコミットを実行する場合に) チュートリアルの後半で必要になるため、Git のユーザー名と電子メールも設定する必要があります。  

## ASP.NET 5 と DNX のインストール
ASP.NET 5 および DNX は、OS X、Linux、Windows 上で動作する最新のクラウドおよび Web アプリを構築するたの、効率の優れた .NET スタックです。ASP.NET 5 および DNX は、一から設計し直され、クラウドにデプロイされるアプリまたはオンプレミスで実行されるアプリ用に最適化された開発フレームワークを提供します。オーバーヘッドを最小に抑えたモジュラー コンポーネントから構成されるため、ソリューションを構築するときに柔軟性を保つことができます。

> [AZURE.NOTE]OS X および Linux の ASP.NET 5 および DNX (.NET Execution Environment) は、初期のベータまたはプレビュー状態にあります。

このチュートリアルでは、最新の開発バージョンの ASP.NET 5 と DNX を使用してアプリケーションの構築を開始する方法について説明します。次の手順は、Windows に固有の手順です。OS X、Linux、および Windows 用の詳細なインストール手順については、[ASP.NET 5 と DNX のインストール](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)に関するページを参照してください。

1. .NET Version Manager (DNVM) をインストールするには、コマンド プロンプトを開き、次のコマンドを実行します。

		@powershell -NoProfile -ExecutionPolicy unrestricted -Command "&{$Branch='dev';iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/aspnet/Home/dev/dnvminstall.ps1'))}"

	DNVM スクリプトがダウンロードされ、ユーザー プロファイル ディレクトリに配置されます。

2. DNVM のインストールを完了するには、Windows を再起動します。

3. コマンド プロンプトを開き、次のように入力して、DNVM の場所を確認します。

		where dnvm

	コマンド プロンプトで、パスは次のように表示されます。

	![dnvm の場所](./media/web-sites-create-web-app-using-vscode/00-where-dnvm.png)

4. これで、DNVM を利用できるようになりました。アプリケーションを実行するには、これを使って DNX をダウンロードする必要があります。コマンド プロンプトで、次のコマンドを実行します。

		dnvm upgrade

5. コマンド プロンプトで次のコマンドを実行して、DNVM を確認し、アクティブなランタイムを表示します。

		dnvm list

	コマンド プロンプトに、アクティブなランタイムの詳細が表示されます。

	![DNVM の場所](./media/web-sites-create-web-app-using-vscode/00b-dnvm-list.png)

6. 複数の DNX ランタイムが表示される場合は、コマンド プロンプトで次のコマンドを入力します。これにより、このチュートリアルで後に Web アプリを作成するときに、ASP.NET 5 ジェネレーターによって使用されるものと同じバージョンに、アクティブな DNX ランタイムを設定します。

		dnvm use 1.0.0-beta4 –p

> [AZURE.NOTE]OS X、Linux、および Windows 用の詳細なインストール手順については、[ASP.NET 5 と DNX のインストール](https://code.visualstudio.com/Docs/ASPnet5#_installing-aspnet-5-and-dnx)に関するページを参照してください。

## Web アプリの作成 

このセクションでは、新しい ASP.NET Web アプリをスキャフォールディングする方法について説明します。ノード パッケージ マネージャー (npm) を使用して、[Yeoman](http://yeoman.io/) (アプリケーション スキャフォールディング ツール - Visual Studio での **[ファイル] > [新しいプロジェクト]** 操作に相当する VS コード)、 [Grunt](http://gruntjs.com/) (JavaScript タスク ランナー)、および [Bower](http://bower.io/) (クライアント側のパッケージ マネージャー) をインストールします。

1. 管理者権限でコマンド プロンプトを開き、ASP.NET プロジェクトを作成する場所に移動します。

2. コマンド プロンプトで次のコマンドを入力して、Yeoman とサポート ツールをインストールします。

		npm install -g yo grunt-cli generator-aspnet bower

3. コマンド プロンプトで次のコマンドを入力して、プロジェクト フォルダーを作成し、アプリをスキャフォールディングします。

		yo aspnet

4. 方向キーを使用して、ASP.NET 5 ジェネレーター メニューから **Web アプリ**の種類を選択し、Enter キーを押します。

	![Yeoman - ASP.NET 5 ジェネレーター](./media/web-sites-create-web-app-using-vscode/01-yo-aspnet.png)

5. 新しい ASP.NET Web アプリの名前を **SampleWebApp** に設定します。この名前はチュートリアル全体で使用されるため、別の名前を選択する場合は、 **SampleWebApp** をすべてその名前に置き換える必要があります。Enter キーを押すと、Yeoman によって、**SampleWebApp** という名前の新しいフォルダーと新しいアプリに必要なファイルが作成されます。

6. コマンド プロンプトで次のコマンドを入力して、VS コードを開きます。

		code .

7. VS コードで**[ファイル] > [フォルダーを開く]** を選択し、ASP.NET Web アプリを含むフォルダーを選択します。

	![[フォルダー] ダイアログ ボックスの選択](./media/web-sites-create-web-app-using-vscode/02-open-folder.png)

	VS コードにプロジェクトが読み込まれ、**エクスプローラー** ウィンドウにそのファイルが表示されます。

	![SampleWebApp プロジェクトを表示する VS コード](./media/web-sites-create-web-app-using-vscode/03-vscode-project.png)

8. **[ビュー] > [コマンド パレット]** を選択します。

9. **コマンド パレット** で、次のコマンドを入力します。

		dnx:dnu restore - (SampleWebApp)

	入力を開始すると、一覧に完全なコマンド ラインが表示されます。

	![Restore コマンド](./media/web-sites-create-web-app-using-vscode/04-dnu-restore.png)

	Restore コマンドは、アプリケーションを実行するために必要な NuGet パッケージをインストールします。準備が整うと、コマンド プロンプトに "**復元が完了しました**" と表示されます。

## ローカルでの Web アプリの実行

Web アプリが作成され、アプリのすべての NuGet パッケージが取得されたため、Web アプリをローカルで実行できます。

1. VS コードの**コマンド パレット**で、次のコマンドを入力してアプリをローカルで実行します。

		dnx: kestrel - (SampleWebApp, Microsoft.AspNet.Hosting --server Kestrel --server.urls http://localhost:5001

	コマンド ウィンドウに "*開始しました*" と表示されます。コマンド ウィンドウに "*開始しました*" と表示されない場合は、VS コードの左下隅にプロジェクトのエラーが示されていないかどうかを確認します。

5. ブラウザーを開き、次の URL に移動します。

	**http://localhost:5001**

	Web アプリの既定のページが次のように表示されます。

	![ブラウザーでのローカル Web アプリ](./media/web-sites-create-web-app-using-vscode/08-web-app.png)

## Azure プレビュー ポータルで Web アプリを作成する

次の手順では、Azure プレビュー ポータルでの Web アプリの作成について説明します。

1. [Azure プレビュー ポータル](https://portal.azure.com)にログインします。

2. ポータルの左下にある **[新規]** をクリックします。

3. **[Web アプリ] > [Web アプリ]** をクリックします。

	![Azure の新しい Web アプリ](./media/web-sites-create-web-app-using-vscode/09-azure-newwebapp.png)

4. **[名前]** に **SampleWebAppDemo** などの値を入力します。この名前は、一意にする必要があります。ポータルで名前を入力しようとするときに、この一意性が要求されます。したがって、別の値を選択する場合は、このチュートリアルに表示されるすべての **SampleWebAppDemo** をこの値に置き換える必要があります。

5. 既存の **App Service プラン**を選択するか、新しいプランを作成します。新しいプランを作成する場合は、料金レベル、および場所などのオプションを選択します。App Service プランの詳細については、「[Azure App Service プランの詳細](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)」の記事を参照してください。

	![Azure の新しい Web アプリ ブレード](./media/web-sites-create-web-app-using-vscode/10-azure-newappblade.png)

6. **[作成]** をクリックします。

	![Web アプリ ブレード](./media/web-sites-create-web-app-using-vscode/11-azure-webappblade.png)

## 新しい Web アプリの Git 発行の有効化

Git は、Azure App Service の Web アプリをデプロイするために使用できる分散型バージョン コントロール システムです。Web アプリ用に記述したコードはローカルの Git リポジトリに格納されます。このコードをリモート リポジトリにプッシュして Azure にデプロイします。

1. [Azure プレビュー ポータル](https://portal.azure.com)にログインします。

2. **[すべて参照]** をクリックします。

3. **[Web アプリ]** をクリックして 、Azure サブスクリプションに関連付けられている Web アプリの一覧を表示します。

4. このチュートリアルで作成した Web アプリを選択します。

5. Web アプリのブレードで、**[デプロイ]** セクションにスクロールし、**[継続的デプロイの設定]** をクリックします。

	![Azure Web アプリ ホスト](./media/web-sites-create-web-app-using-vscode/14-azure-deployment.png)

6. **[ソースの選択]、[ローカル Git リポジトリ]** の順にクリックします。

7. **[OK]** をクリックします。

	![Azure のローカル Git リポジトリ](./media/web-sites-create-web-app-using-vscode/15-azure-localrepository.png)

8. Web アプリまたはその他の App Service アプリを発行するためのデプロイメント資格情報をまだ設定していない場合は、ここで設定します。

	* **[FTP]** をクリックして、デプロイメント資格情報を設定します。

	* ユーザー名とパスワードを作成します。このパスワードは、後で Git をセットアップするときに必要になります。

	* **[保存]** をクリックします。

	![Azure デプロイメント資格情報](./media/web-sites-create-web-app-using-vscode/16-azure-credentials.png)

9. Web アプリのブレードで、**[設定]、[プロパティ]** の順にクリックします。デプロイ先のリモート Git リポジトリの URL は、** の下に表示されます。
10.  URL**.

10. チュートリアルで後で使用するために、**GIT URL** の値をコピーします。

	![Azure Git の URL](./media/web-sites-create-web-app-using-vscode/17-azure-giturl.png)

## Azure App Service への Web アプリの発行

このセクションでは、ローカル Git リポジトリを作成し、そのリポジトリから Azure にプッシュして、Web アプリを Azure にデプロイします。

1. VS コードの左側のナビゲーション バーで、**[Git]** オプションを選択します。

	![VS コードでの Git アイコン](./media/web-sites-create-web-app-using-vscode/git-icon.png)

2. **[Git リポジトリの初期化]** を選択して、ワークスペースが Git によるソース管理の対象になるように設定します。

	![Git の初期化](./media/web-sites-create-web-app-using-vscode/19-initgit.png)

3. コミット メッセージを追加し、**[すべてコミット]** チェック アイコンをクリックします。

	![Git すべてコミット](./media/web-sites-create-web-app-using-vscode/20-git-commit.png)

4. Git の処理が完了した後には、[Git] ウィンドウの **[変更]** の下にいかなるファイルも表示されません。

	![Git 変更なし](./media/web-sites-create-web-app-using-vscode/no-changes.png)

5. コマンド プロンプトを開き、Web アプリを含むディレクトリに移動します。

6. コピーしておいた (".git" で終わる) Git URL を使用して、Web アプリに更新をプッシュするためのリモート参照を作成します。

		git remote add azure [URL for remote repository]

7. 次のコマンドを入力して、変更内容を Azure にプッシュします。

		git push azure master

	以前作成したパスワードを入力するように求められます。**注: パスワードは表示されません。**

	このコマンドからは、デプロイメントが成功したというメッセージが最後に出力されます。

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		[new branch]      master -> master

> [AZURE.NOTE]アプリを変更した場合は再パブリッシュできます。そのためには、VS コードで **[すべてコミット]** オプションを選択して、コマンド プロンプトで **git push azure master** コマンドを入力します。

## Azure でのアプリの実行
これで Web アプリがデプロイされたため、Azure でホストされているアプリを実行してみましょう。

これは、2 つの方法で実行できます。

* ブラウザーを開き、次のように、Web アプリの名前を入力します。   

		http://SampleWebAppDemo.azurewebsites.net
 
* Azure プレビュー ポータルで、Web アプリの Web アプリ ブレードを見つけて、**[参照]** をクリックし、既定のブラウザーでアプリを表示します。

![Azure の Web アプリ](./media/web-sites-create-web-app-using-vscode/21-azurewebapp.png)

## 概要
このチュートリアルでは、VS コードで、Web アプリを作成し、Azure にデプロイする方法を学習しました。VS コードの詳細については、[Visual Studio Code を使用する理由](https://code.visualstudio.com/Docs/)の記事を参照してください。App Service Web Apps の詳細については、[Web Apps の概要](app-service-web-overview.md)に関するページを参照してください。

<!---HONumber=62-->