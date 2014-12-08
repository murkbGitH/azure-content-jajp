<properties title="Azure 上で CoreOS を使用する方法" pageTitle="Azure 上で CoreOS を使用する方法" description="CoreOS、Azure 上で CoreOS 仮想マシンを作成する方法、およびその基本的な使用方法について説明します。" metaKeywords="linux, virtual machines, vm, azure, CoreOS, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/27/2014" ms.author="rasquill" />

<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->
<!--Properties section (above): this is required in all topics. Please fill it out! See http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20properties%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20values.aspx for details. -->
<!-- Tags section (above): this is required in all topics. Please fill it out! See http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20tags%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20for%20reporting.aspx for details. -->
<!--The next line, with one pound sign at the beginning, is the page title-->

# Windows Azure 上で CoreOS を使用する方法

このトピックでは、CoreOS を理解するためのクイック スタートとして [CoreOS][CoreOS] について説明し、Azure 上に 3 台の CoreOS 仮想マシンで構成されるクラスターを作成する方法を示します。ここでは、CoreOS デプロイメントのごく基本的な要素と、[CoreOS と Azure に関するページ][CoreOS と Azure に関するページ]、[Tim Park による CoreOS のチュートリアル][Tim Park による CoreOS のチュートリアル]、および [Patrick Chanezon によるチュートリアル][Patrick Chanezon によるチュートリアル]の例を使用して、CoreOS デプロイメントの基本構造を理解し、3 台の仮想マシンから構成されるクラスターを正常に実行するための極限まで抑えた最小要件について説明します。

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

このトピックで取り上げる内容は次のとおりです。

-   [CoreOS、クラスターと Linux コンテナー][CoreOS、クラスターと Linux コンテナー]
-   [セキュリティに関する考慮事項][セキュリティに関する考慮事項]
-   [Azure 上で CoreOS を使用する方法][Azure 上で CoreOS を使用する方法]
-   [次のステップ][次のステップ]

## <span id="intro"></span>CoreOS、クラスターと Linux コンテナー</a>

CoreOS は、Linux の軽量バージョンです。唯一のパッケージ化のメカニズムとして [Docker][Docker] コンテナーなどの Linux コンテナーを使用する、きわめて大規模になる可能性がある VM のクラスターの迅速な作成をサポートします。CoreOS は、以下をサポートすることを目的としています。

-   きわめて高度な自動化
-   より容易で一貫性のあるアプリケーションのデプロイメント
-   アプリケーション レベルおよびシステム レベルでの拡張性

大まかに言えば、以下の CoreOS の機能がこれらの目的を支援します。

1.  1 つのパッケージ システム: CoreOS では、高速化、均一性、および容易なデプロイメントを実現するため、Linux コンテナーで実行される Linux コンテナー イメージのみを実行します。
2.  アトミックに実行されるオペレーティング システムの更新。そのため、オペレーティング システムは単一のエンティティとして更新され、容易に既知の状態にロールバックできます。
3.  動的 VM およびクラスター通信と管理用の組み込みの [etcd][etcd] および [fleet][fleet] デーモン (サービス)

以上は、CoreOS とその機能の一般的な説明です。CoreOS の詳細については、「[CoreOS Overview][CoreOS Overview]」を参照してください。

## <span id="security"></span>セキュリティに関する考慮事項</a>

現在、CoreOS では、SSH でクラスターと通信できるユーザーには、そのクラスターを管理するためのアクセス許可が与えられていることを前提としています。そのため、CoreOS クラスターは、変更しなくてもテスト環境および開発環境として優れています。ただし、運用環境では、より強力なセキュリティ対策を実施する必要があります。

## <span id="usingcoreos"></span>Azure 上で CoreOS を使用する方法</a>

このセクションでは、[Azure クロス プラットフォーム インターフェイス (xplat-cli)][Azure クロス プラットフォーム インターフェイス (xplat-cli)] を使用して 3 台の CoreOS 仮想マシンを含む Azure Cloud Service を作成する方法について説明します。基本的な手順は次のとおりです。

1.  SSH 証明書とキーを作成し、CoreOS 仮想マシンとのコミュニケーションを保護する
2.  相互に通信するために、使用するクラスターの etcd ID を取得する
3.  cloud-config ファイルを [YAML][YAML] 形式で作成する
4.  xplat-cli を使用して、新しい Azure Cloud Service と 3 台の CoreOS VM を作成する
5.  Azure VM で CoreOS クラスターをテストする
6.  localhost で CoreOS クラスターをテストする

### 通信用に公開キーと秘密キーを作成する

「[Azure 上の Linux における SSH の使用方法][Azure 上の Linux における SSH の使用方法]」の指示に従って SSH 用の公開キーと秘密キーのペアを作成します (基本的な手順は、以下の指示に記載されています)。これらのキーを使用してクラスターの VM に接続し、VM が動作中で互いに通信できることを確認します。

> [WACOM.NOTE] このトピックでは、わかりやすくするために、これらのキーがなく、**`myPrivateKey.pem`** および **`myCert.pem`** ファイルの作成を必要とすることを前提にしています。既に公開キーと秘密キーのペアを **`~/.ssh/id_rsa`** に保存している場合は、「`openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem`」と入力するだけで Azure にアップロードが必要な .pem ファイルを取得できます。

1.  作業ディレクトリで、「`openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem`」と入力し、秘密キーと秘密キーに関連付けられた X.509 証明書を作成します。

2.  秘密キーの所有者がファイルを読み取る、または書き込むことができるかどうかをアサートするには、「`chmod 600 myPrivateKey.key`」と入力します。

これで、作業ディレクトリに、**`myPrivateKey.key`** と **`myCert.pem`** ファイルの両方が作成されます。

### 使用するクラスターの etcd ID を取得する

CoreOS の **etcd** デーモンでは、クラスターのすべてのノードを自動的に照会するのにデーモン検出 ID が必要です。検出 ID を取得して、**etcdid** ファイルに保存するには、次を入力します。

    curl https://discovery.etcd.io/new | grep ^http.* > etcdid

### cloud-config ファイルを作成する

引き続き同じ作業ディレクトリで、任意のテキスト エディターを使用して次のテキストを含むファイルを作成し、**`cloud-config.yaml`** として保存します(任意のファイル名で保存できますが、次の手順で VM を作成するときに、**azure create vm** コマンドの **--custom-data** オプションでこのファイル名を参照する必要があります)。

> [WACOM.NOTE] 「`cat etcdid`」と入力して、上で作成した `etcdid` ファイルから etcd 検出 ID を取得し、次の **cloud-config.yaml** ファイルの **`<token>`** を、`etcdid` ファイルから生成された番号で置き換えます。最後にクラスターを検証できない場合は、この手順を行っていない可能性があります。

    #cloud-config

    coreos:
      etcd:
        # generate a new token for each unique cluster from https://discovery.etcd.io/new
        discovery: https://discovery.etcd.io/<token>
        # deployments across multiple cloud services will need to use $public_ipv4
        addr: $private_ipv4:4001
        peer-addr: $private_ipv4:7001
      units:
        - name: etcd.service
          command: start
        - name: fleet.service
          command: start

cloud-config ファイルの詳細については、CoreOS ドキュメントの「[Using Cloud-Config][Using Cloud-Config]」を参照してください。

### xplat-cli を使用して、新しい CoreOS VM を作成する

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1.  [Azure クロス プラットフォーム インターフェイス (xplat-cli)][Azure クロス プラットフォーム インターフェイス (xplat-cli)] をまだインストールしていない場合はインストールし、仕事上の ID または学校の ID を使用してログインするか、publishsettings ファイルをダウンロードして、アカウントにインポートします。
2.  CoreOS イメージを探します。現在は、**`2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0`** というイメージだけありますが、「`azure vm image list | grep .*CoreOS.*`」と入力するといつでも使用可能なイメージを探すことができ、次のような結果が 1 つ以上表示されます。
    `data:    2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0              Public    Linux`
3.  「
    `azure service create <cloud-service-name>`」と入力し、基本的なクラスター用にクラウド サービスを作成します。ここで、*cloud-service-name* には、CoreOS クラウド サービスの名前を指定します。このサンプルでは、**`coreos-cluster`** という名前を使用します。この名前を再利用して、クラウド サービス内に CoreOS VM インスタンスを作成する場合に選択する必要があります。

注: [新しいポータル][新しいポータル]でここまでの作業を確認すると、次の画像で示すように、クラウド サービスの名前がリソース グループとドメインの両方になっていることがわかります。

![][0]

1.  クラウド サービスに接続し、**azure vm create** コマンドを使用して、新しい CoreOS VM インスタンスを作成します。**--ssh-cert** オプションで X.509 証明書の場所を渡します。次を入力して、最初の VM イメージを作成し、次のように必ず **coreos-cluster** を作成したクラウド サービスの名前に置き換えます。

<!-- -->

    azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location='West US' 2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0 core

1.  手順 4. のコマンドを繰り返し、2 番目のノードを作成します。その際、**--vm-name** 値は **node-2** に、**--ssh** ポートの値は 2022 に置き換えます。

2.  手順 4. のコマンドを繰り返し、3 番目のノードを作成します。その際、**--vm-name** 値は **node-3** に、**--ssh** ポートの値は 3022 に置き換えます。

次の画像では、新しいポータルに CoreOS クラスターが表示されている状態を示します。

![][1]

### Azure VM で CoreOS クラスターをテストする

クラスターをテストするには、作業ディレクトリで次を入力し、**ssh** により **node-1** に接続して、秘密キーを渡します。

`ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key`

接続されたら、「`sudo fleetctl list-machines`」と入力して、クラスターにより、既にクラスター内のすべての VM が識別されたかどうかを確認します。次のような応答を受け取ります。

    core@node-1 ~ $ sudo fleetctl list-machines
    MACHINE     IP      METADATA
    442e6cfb... 100.71.168.115  -
    a05e2d7c... 100.71.168.87   -
    f7de6717... 100.71.188.96   -

### localhost で CoreOS クラスターをテストする

最後に、**fleet** をインストールして、ローカル Linux クライアントで CoreOS クラスターをテストします。**fleet** には **Go 言語**が必要です。よって、まず次を入力して Go 言語のインストールが必要になる場合があります。

`sudo apt-get install golang`

次を入力して、github から **fleet** リポジトリを複製します。

`git clone https://github.com/coreos/fleet.git`

次を入力して **fleet** をビルドします。

`./build`

最後に、次のように **fleet** を使いやすいように配置します (**sudo** 使用する必要があるかどうかは、構成によって変わります)。

`cp bin/fleetctl /usr/local/bin`

次を入力して、作業ディレクトリで **fleet** が `myPrivateKey.key` にアクセスできることを確認します。

`ssh-add ./myPrivateKey.key`

> [WACOM.NOTE] **~/.ss h/id\_rsa** キーを既に使用している場合は、`ssh-add ~/.ssh/id_rsa` を使用してそのキーを追加します。

これで、**node-1** で使用したのと同じ **fleetctl** コマンドを使用してリモートでテストできます。ただし、以下のようにリモート引数をいくつか渡します。

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

以下とまったく同じ結果が得られます。

    MACHINE     IP      METADATA
    442e6cfb... 100.71.168.115  -
    a05e2d7c... 100.71.168.87   -
    f7de6717... 100.71.188.96   -

## 次のステップ

これで、3 ノード構成の CoreOS クラスターが Azure 上で実行されています。ここからは、「[Tim Park's CoreOS Tutorial (Tim Park による CoreOS のチュートリアル)][Tim Park による CoreOS のチュートリアル]」、「[Patrick Chanezon's CoreOS Tutorial (Patrick Chanezon によるチュートリアル)][Patrick Chanezon によるチュートリアル]」、[Docker][Docker] のドキュメント、および「[CoreOS Overview][CoreOS Overview]」を参照しながら、より複雑なクラスターを作成する方法と、Docker を使用してより興味深いアプリケーションを作成する方法を確認できます。

<!--Anchors-->
<!--Image references-->
<!--Link references-->

  [CoreOS]: https://coreos.com/
  [CoreOS と Azure に関するページ]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
  [Tim Park による CoreOS のチュートリアル]: https://github.com/timfpark/coreos-azure
  [Patrick Chanezon によるチュートリアル]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
  [CoreOS、クラスターと Linux コンテナー]: #intro
  [セキュリティに関する考慮事項]: #security
  [Azure 上で CoreOS を使用する方法]: #usingcoreos
  [次のステップ]: #next-steps
  [Docker]: http://docker.io
  [etcd]: https://github.com/coreos/etcd
  [fleet]: https://github.com/coreos/fleet
  [CoreOS Overview]: https://coreos.com/using-coreos/
  [Azure クロス プラットフォーム インターフェイス (xplat-cli)]: ../xplat-cli/
  [YAML]: http://yaml.org/
  [Azure 上の Linux における SSH の使用方法]: http://azure.microsoft.com/ja-jp/documentation/articles/virtual-machines-linux-use-ssh-key/
  [Using Cloud-Config]: https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/
  [新しいポータル]: https://portal.azure.com
  [0]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
  [1]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png