﻿<properties 
	pageTitle="Bottle を使用した Python Web サイト - Azure チュートリアル" 
	description="Azure での Python Web サイトの実行について説明するチュートリアル。" 
	services="web-sites" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="12/17/2014" 
	ms.author="huvalo"/>




# Bottle を使用した Web サイトの作成

このチュートリアルでは、Azure Web サイトで Python を実行するための基本的な方法について説明します。Azure Web サイトでは、制限付きの無料のホスティングや迅速な展開を実行できます。また、Python も使用できます。アプリケーションの拡張に合わせて、有料のホスティングに切り替えることができます。また、他のすべての Azure サービスと統合することもできます。

Bottle Web フレームワークを使用して (このチュートリアルの [Django](../web-sites-python-create-deploy-django-app) と [Flask](../web-sites-python-create-deploy-flask-app バージョンを参照))、アプリケーションを作成できます。Azure ギャラリーから Web サイトを作成し、Git デプロイ[CO1]を設定し、リポジトリをローカルで複製します。その後、ローカルでアプリケーションを実行し、変更を行い、それらをコミットして Azure に送信します。チュートリアルでは、Windows または Mac/Linux での手順を紹介します。

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


+ [前提条件](#prerequisites)
+ [ポータルでの Web サイトの作成](#website-creation-on-portal)
+ [アプリケーションの概要](#application-overview)
+ Web サイトの開発
  + [Windows - Python Tools for Visual Studio](#website-development-windows-ptvs)
  + [Windows - コマンド ライン](#website-development-windows-command-line)
  + [Mac と Linux - コマンド ライン](#website-development-mac-linux-command-line)
+ [トラブルシューティング - デプロイ](#troubleshooting-deployment)
+ [トラブルシューティング - パッケージのインストール](#troubleshooting-package-installation)
+ [トラブルシューティング - 仮想環境](#troubleshooting-virtual-environment)
+ [次のステップ](#next-steps)


<h2><a name="prerequisites"></a>前提条件</h2>

- Windows、Mac、Linux
- Python 2.7 または 3.4
- setuptools、pip、virtualenv (Python 2.7 のみ)
- Git
- Python Tools for Visual Studio (オプション)

**注**:現在、TFS 発行は Python プロジェクトではサポートされていません。

### Windows

Python 2.7 または 3.4 (32 ビット) がインストールされていない場合は、Web Platform Installer を使用して [Azure SDK for Python 2.7](http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409) または [Azure SDK for Python 3.4](http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409) をインストールすることをお勧めします。いずれかをインストールすると、Python の 32 ビット バージョン、setuptools、pip、virtualenv などがインストールされます (32 ビット Python は Azure ホスト マシンにインストールされます)。[python.org](http://www.python.org/) から Python を取得することもできます。

Git については、[Git for Windows](http://msysgit.github.io/) または [GitHub for Windows](https://windows.github.com/) をお勧めします。Visual Studio を使用している場合は、統合 Git サポートを使用できます。

さらに、[Python Tools for Visual Studio](http://pytools.codeplex.com) のインストールもお勧めします。これはオプションですが、無料版の Visual Studio Express 2013 for Web を含む [Visual Studio](http://www.visualstudio.com/) を使用すれば、Python IDE を有効に活用できます。

### Mac と Linux

Python と Git は既にインストールされていると思いますが、Python 2.7 または 3.4 であることをご確認ください。


<h2><a name="website-creation-on-portal"></a>ポータルでの Web サイトの作成</h2>

アプリケーションを作成する最初の手順では、Azure の管理ポータルで Web サイトを作成します。そのためには、ポータルにログインし、画面左下にある **[新規]** ボタンをクリックします。ウィンドウが表示されます。**[コンピューティング]**、**[Web サイト]**、**[簡易作成]** の順にクリックします。

![](./media/web-sites-python-create-deploy-bottle-app/portal-create-site.png)

ウィンドウが開き、ギャラリーに使用可能なアプリケーションが一覧表示されます。左側の **[APP FRAMEWORKS (アプリケーション フレームワーク)[CO2]]** カテゴリをクリックし、**[Bottle]** を選択します。

![](./media/web-sites-python-create-deploy-bottle-app/portal-gallery-bottle.png)

次のページでサイト名とリージョンを入力し、[complete (完了)] ボタンを押します。

サイトがすぐに設定されます。下のツールバーにある **[参照]** ボタンをクリックすると、Azure で実行中の新しい Bottle アプリケーションが表示されます。

![](./media/web-sites-python-create-deploy-bottle-app/portal-website-bottle.png)
 
次に、Git を使って発行する際に必要となるサポートを追加するため、**[ソース管理からの展開の設定]** をクリックします。

![](./media/web-sites-python-create-deploy-bottle-app/portal-site-created.png)

**[展開の設定]** ダイアログで、下へスクロールして **[ローカル Git リポジトリ]** オプションを選択します。右矢印をクリックして次へ進みます。

![](./media/web-sites-python-create-deploy-bottle-app/portal-setup-deployment.png)

Git 発行を設定した後で、リポジトリが作成中であることを示すページがすぐに表示されます。準備が整ったら、接続方法に関する手順を確認します。  

![](./media/web-sites-python-create-deploy-bottle-app/portal-repo-created.png)

これらの手順については次のセクションで見ていきます。


<h2><a name="application-overview"></a>アプリケーションの概要</h2>

### Git リポジトリのコンテンツ

ここでは、初期の Git リポジトリにあるファイルの概要を説明します。次のセクションではこれらのファイルを複製します。

    \routes.py
    \static\content\
    \static\fonts\
    \static\scripts\
    \views\about.tpl
    \views\contact.tpl
    \views\index.tpl
    \views\layout.tpl

アプリケーションのメイン ソース。マスター レイアウトを使用した 3 つのページ (index、about、contact) で構成されます。静的コンテンツとスクリプト (bootstrap、jquery、modernizr、respond など)。

    \app.py

ローカル開発サーバーのサポート。これを使用すると、アプリケーションをローカルで実行できます。

    \BottleWebProject.pyproj
    \BottleWebProject.sln

[Python Tools for Visual Studio](http://pytools.codeplex.com) で使用するプロジェクト ファイル。

    \ptvs_virtualenv_proxy.py

仮想環境とPTVS リモート デバッグのサポートのための IIS プロキシ。

    \requirements.txt

このアプリケーションで必要な外部パッケージ。デプロイ スクリプトにより、このファイルに記載してあるパッケージが pip インストールされます。
 
    \web.2.7.config
    \web.3.4.config

IIS 構成ファイル。デプロイ スクリプトは適切な web.x.y.config を使用して web.config としてコピーします。

### オプションのファイル - デプロイのカスタマイズ

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../includes/web-sites-python-customizing-deployment.md)]

### オプションのファイル - Python ランタイム

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

### サーバー上の追加のファイル

サーバー上にはいくつかのファイルが存在しますが、Git リポジトリには追加されません。これらのファイルはデプロイ スクリプトによって作成されるものです。

    \web.config

IIS 構成ファイル。デプロイごとに web.x.y.config から作成されます。

    \env\

Python 仮想環境。互換性のある仮想環境がまだサイト上にない場合に、デプロイ時に作成されます。requirements.txt に指定されたパッケージが pip インストールされますが、パッケージが既にインストールされている場合はインストールがスキップされます。

次の 3 つのセクションでは、以下の 3 つの異なる環境における Web サイト開発を使用した処理方法を説明します。

- Windows、Python Tools for Visual Studio を使用
- Windows、コマンド ラインを使用
- Mac と Linux、コマンド ラインを使用


<h2><a name="website-development-windows-ptvs"></a>Web サイトの開発 - Windows - Python Tools for Visual Studio</h2>

### リポジトリの複製

最初に、Azure ポータルで提供される URL を使用して、リポジトリを複製します。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-git-clone.png)

リポジトリのルートにあるソリューション ファイル (.sln) を開きます。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-solution-bottle.png)

### 仮想環境の作成

次にローカル開発用の仮想環境を作成します。**[Python 環境]** を右クリックし、**[仮想環境の追加]** を選択します。

- 環境名が、 `env` となっていることを確認します。

- ベース インタープリターを選択します。サイトに選択したものと同じバージョンの Python を使用していることを確認します (runtime.txt またはサイト構成ページ)。

- パッケージのダウンロードとインストールのオプションが選択されていることを確認します。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-add-virtual-env-27.png)

**[作成]** をクリックします。これにより、仮想環境が作成され、requirements.txt に指定されている依存関係がインストールされます。

### 開発サーバーを使用した実行

F5 キーを押してデバッグを開始します。ローカルに実行されているページが Web ブラウザーで自動的に開きます。

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

ソースにブレークポイントを設定したり、ウォッチ ウィンドウを使用したりできます。各種機能の詳細については、[PTVS のドキュメント](http://pytools.codeplex.com/documentation)をご覧ください。

### 変更を加える

次に、アプリケーション ソースやテンプレートに変更を加えてテストを行うことができます。

変更内容をテストしたら、Git リポジトリにコミットします。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-commit-bottle.png)

### その他のパッケージのインストール

アプリケーションが、Python と Bottle 以外の依存関係を持つ場合があります。

pip を使用して追加のパッケージをインストールできます。パッケージをインストールするには、仮想環境を右クリックし、**[Install Python Package]** を選択します。

たとえば、Azure ストレージ、サービス、バスにアクセスできるようになる Azure SDK for Python をインストールするには、 `azure` を入力します。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-install-package-dialog.png)

仮想環境を右クリックして、**[Generate requirements.txt (requirements.txt の生成)]** を選択して requirements.txt を更新します。[CO3]

その後、requirements.txt への変更内容を Git リポジトリにコミットします。

### Azure への展開

デプロイを開始するには、**[Sync (同期)]** または **[Push (プッシュ)[CO4]]** をクリックします。同期ではプッシュとプルの両方が実行されます。

![](./media/web-sites-python-create-deploy-bottle-app/ptvs-git-push.png)

仮想環境を作成したり、パッケージをインストールしたりするため、最初のデプロイには時間がかかります。

Visual Studio にはデプロイの進捗状況は表示されません。出力を確認する場合は、「[トラブルシューティング - デプロイ]」のセクションをご覧ください(#troubleshooting-deployment)。

Azure URL を参照して、変更内容を表示します。


<h2><a name="website-development-windows-command-line"></a>Web サイトの開発 - Windows - コマンド ライン</h2>

### リポジトリの複製

最初に、Azure ポータルで提供される URL を使用してリポジトリを複製し、Azure リポジトリにリモートとして追加します。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### 仮想環境の作成

開発環境用の新しい仮想環境を作成します (リポジトリには追加しません)。Python の仮想環境を再配置することはできません。そのため、アプリケーションで作業する開発者ごとにローカルに仮想環境を作成することになります。

サイトに選択したものと同じバージョンの Python を使用していることを確認します (runtime.txt またはサイト構成ページ)。

Python 2.7。

    c:\python27\python.exe -m virtualenv env

Python 3.4。

    c:\python34\python.exe -m venv env

アプリケーションに必要な外部パッケージをインストールします。リポジトリのルートにある requirements.txt ファイルを使用すると、仮想環境にパッケージをインストールできます。

    env\scripts\pip install -r requirements.txt

### 開発サーバーを使用した実行

次のコマンドを使用すると、開発サーバーでアプリケーションを起動できます。

    env\scripts\python app.py

コンソールには、URL とサーバーがリッスンするポートが表示されます。

![](./media/web-sites-python-create-deploy-bottle-app/windows-run-local-bottle.png)

次に、Web ブラウザーを開き、その URL に移動します。

![](./media/web-sites-python-create-deploy-bottle-app/windows-browser-bottle.png)

### 変更を行う

次に、アプリケーション ソースやテンプレートに変更を加えてテストを行うことができます。

変更内容をテストしたら、Git リポジトリにコミットします。

    git add <modified-file>
    git commit -m "<commit-comment>"

### その他のパッケージのインストール

アプリケーションは、Python と Bottle 以外の依存関係を持つ場合があります。

pip を使用して追加のパッケージをインストールできます。たとえば、Azure ストレージ、サービス、バスにアクセスできるようになる Azure SDK for Python をインストールするには、次のように入力します。

    env\scripts\pip install azure

requirements.txt が更新されていることを確認します。

    env\scripts\pip freeze > requirements.txt

変更をコミットします。

    git add requirements.txt
    git commit -m "Added azure package"

### Azure への展開

デプロイを開始するには、変更を Azure にプッシュします。

    git push azure master

仮想環境の作成、パッケージのインストール、web.config の作成といった、デプロイ スクリプトの出力が表示されます。

Azure URL を参照して、変更内容を表示します。


<h2><a name="website-development-mac-linux-command-line"></a>Web サイトの開発 - Mac と Linux - コマンド ライン</h2>

### リポジトリの複製

最初に、Azure ポータルで提供される URL を使用してリポジトリを複製し、Azure リポジトリにリモートとして追加します。

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### 仮想環境の作成

開発環境用の新しい仮想環境を作成します (リポジトリには追加しません)。Python の仮想環境を再配置することはできません。そのため、アプリケーションで作業する開発者ごとにローカルに仮想環境を作成することになります。

サイトに選択したものと同じバージョンの Python を使用していることを確認します (runtime.txt またはサイト構成ページ)。

Python 2.7。

    python -m virtualenv env

Python 3.4。

    python -m venv env

アプリケーションに必要な外部パッケージをインストールします。リポジトリのルートにある requirements.txt ファイルを使用すると、仮想環境にパッケージをインストールできます。

    env/bin/pip install -r requirements.txt

### 開発サーバーを使用した実行

次のコマンドを使用すると、開発サーバーでアプリケーションを起動できます。

    env/bin/python app.py

コンソールには、URL とサーバーがリッスンするポートが表示されます。

![](./media/web-sites-python-create-deploy-bottle-app/mac-run-local-bottle.png)

次に、Web ブラウザーを開き、その URL に移動します。

![](./media/web-sites-python-create-deploy-bottle-app/mac-browser-bottle.png)

### 変更を行う

次に、アプリケーション ソースやテンプレートに変更を加えてテストを行うことができます。

変更内容をテストしたら、Git リポジトリにコミットします。

    git add <modified-file>
    git commit -m "<commit-comment>"

### その他のパッケージのインストール

アプリケーションは、Python と Bottle 以外の依存関係を持つ場合があります。

pip を使用して追加のパッケージをインストールできます。たとえば、Azure ストレージ、サービス、バスにアクセスできるようになる Azure SDK for Python をインストールするには、次のように入力します。

    env/bin/pip install azure

requirements.txt が更新されていることを確認します。

    env/bin/pip freeze > requirements.txt

変更をコミットします。

    git add requirements.txt
    git commit -m "Added azure package"

### Azure への展開

デプロイを開始するには、変更を Azure にプッシュします。

    git push azure master

仮想環境の作成、パッケージのインストール、web.config の作成といった、デプロイ スクリプトの出力が表示されます。

Azure URL を参照して、変更内容を表示します。


<h2><a name="troubleshooting-deployment"></a>トラブルシューティング - デプロイ</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


<h2><a name="troubleshooting-package-installation"></a>トラブルシューティング - パッケージのインストール</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


<h2><a name="troubleshooting-virtual-environment"></a>トラブルシューティング - 仮想環境</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


<h2><a name="next-steps"></a>次のステップ</h2>

Bottle と Python Tools for Visual Studio の詳細については、次のリンクをご覧ください。 
 
- [Bottle のドキュメント][]
- [Python Tools for Visual Studio のドキュメント][]

Azure テーブル ストレージと MongoDB の使用方法については、次のリンクをご覧ください。

- [Azure における Bottle と MongoDB (Python Tools 2.1 for Visual Studio の使用方法)][]
- [Azure における Bottle と Azure テーブル ストレージ (Python Tools 2.1 for Visual Studio の使用方法)][]


<!--Link references-->
[Azure における Bottle と MongoDB (Python Tools 2.1 for Visual Studio の使用方法)]: ../web-sites-python-ptvs-bottle-table-storage
[Azure における Bottle と Azure テーブル ストレージ (Python Tools 2.1 for Visual Studio の使用方法)]: ../web-sites-python-ptvs-bottle-mongodb

<!--External Link references-->
[Python Tools for Visual Studio のドキュメント]: http://pytools.codeplex.com/documentation 
[Bottle のドキュメント]: http://bottlepy.org/docs/dev/index.html


<!--HONumber=42-->
[CO1]TM has "デプロイ" and "展開" for "deploy". I used "デプロイ" in the target segments. 
[CO2]unknown UI: APP FRAMEWORKS. translated to "APP FRAMEWORKS (アプリケーション フレームワーク)". please confirm.
[CO3]unknown UI: Generate requirements.txt. translated to "Generate requirements.txt (requirements.txt の生成)". Please confirm.
[CO4]unkown UI: Sync and Push. please confirm.