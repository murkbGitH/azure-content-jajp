﻿<properties urlDisplayName="Integrate an Azure Website with Azure CDN" pageTitle="Azure CDN と Azure Websites の統合" metaKeywords="Azure tutorial, Azure web app tutorial, ASP.NET, CDN, MVC, websites" description="A tutorial that teaches you how to deploy a website that serves content from an integrated Azure CDN endpoint" metaCanonical="" services="cdn,web-sites" documentationCenter=".NET" title="Integrate an Azure Website with Azure CDN" authors="cephalin" solutions="" manager="wpickett" editor="jimbe" />

<tags ms.service="cdn" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/02/2014" ms.author="cephalin" />

<a name="intro"></a>
# Azure CDN と Azure Websites の統合 #

Azure Websites は、お客様の近くのサーバー ノードから Web サイトのコンテンツをグローバルに提供することにより、従来のグローバル スケーリング機能に加えて、[Azure CDN](http://azure.microsoft.com/ja-jp/services/cdn/) と統合できます (現在のノードの場所を網羅した最新の一覧については、[ここ](http://msdn.microsoft.com/ja-jp/library/azure/gg680302.aspx)をクリックしてください)。この統合により、Azure Websites のパフォーマンスが大幅に増加し、世界中の web サイトのユーザー エクスペリエンスが大幅に向上します。 

Azure CDN と Azure Websites の統合には、次の利点があります。

- コンテンツの展開 (イメージ、スクリプト、およびスタイルシート) を、Azure Websites の[継続的な展開](http://azure.microsoft.com/ja-jp/documentation/articles/web-sites-publish-source-control/)プロセスの一部として統合
- Azure Websites の NuGet パッケージ (たとえば、jQuery バージョン、Bootstrap バージョン) を簡単にアップグレードできる 
- Web アプリケーションと CDN によって配信されるコンテンツを同じ Visual Studio インターフェイスから管理
- ASP.NET のバンドルと縮小を Azure CDN と統合できる。

## 学習内容 ##

このチュートリアルで学習する内容は次のとおりです。

-	[Azure CDN エンドポイントを Azure Websites と統合して、Azure CDN から Web ページの静的コンテンツを配信する](#deploy)
-	[Azure Websites の静的コンテンツのキャッシュ設定を構成する](#caching)
-	[Azure CDN を介してコントローラー アクションからコンテンツを配信する](#controller)
-	[Visual Studio のスクリプトのデバッグ エクスペリエンスを維持しながらバンドルされたコンテンツおよび縮小されたコンテンツを配信する](#bundling)
-	[Azure CDN がオフラインのときのスクリプトおよび CSS のフォールバックを構成する](#fallback) 

## 学習内容 ##

Visual Studio の既定の ASP.NET MVC テンプレートを使用して Azure Websites をデプロイし、統合された Azure CDN からコンテンツ (たとえば、画像、コントローラー アクションの結果、既定の JavaScript ファイルおよび CSS ファイル) を配信するコードを追加します。さらに、CDN がオフラインになった場合に提供されるバンドルのフォールバック メカニズムを構成するコードを作成します。

## 前提条件 ##

このチュートリアルの前提条件は次のとおりです。

-	アクティブな [Microsoft Azure アカウント](http://azure.microsoft.com/ja-jp/account/)
-	Visual Studio 2013 と [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>このチュートリアルを完了するには、Azure アカウントが必要です。</h5>
  <ul>
    <li><a href="http://azure.microsoft.com/ja-jp/pricing/free-trial/?WT.mc_id=A261C142F">無料で Azure アカウントを開く</a>ことができます - Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Websites など無料の Azure サービスをご利用になれます。</li>
    <li><a href="http://azure.microsoft.com/ja-jp/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">MSDN サブスクライバーの特典を有効にする</a>こともできます - MSDN サブスクリプションにより、有料の Azure のサービスを使用できるクレジットが毎月与えられます。</li>
  <ul>
</div>

<a name="deploy"></a>
## 統合された CDN エンドポイントで Azure Websites をデプロイする ##

このセクションでは、Visual Studio 2013 の既定の ASP.NET MVC アプリケーション テンプレートを Azure Websites にデプロイした後、これを新しい CDN エンドポイントと統合します。次の手順に従ってください。

1. Visual Studio 2013 で、**[ファイル]、[新規]、[プロジェクト]、[Web]、[ASP.NET Web アプリケーション]** に移動し、メニュー バーから新しい ASP.NET Web アプリケーションを作成します。クラウド サービスに名前を付けて、**[OK]** をクリックします。

	![](media/cdn-websites-with-cdn/1-new-project.png)

3. **MVC** を選択し、**[サブスクリプションの管理]** をクリックします。

	![](media/cdn-websites-with-cdn/2-webapp-template.png)

4. **[サインイン]** をクリックします。

	![](media/cdn-websites-with-cdn/3-manage-subscription.png)

6. サインイン ページで、Azure アカウントをアクティブ化する際に使用した Microsoft アカウントでサインインします。
7. サインインしたら、**[閉じる]** をクリックします。次に、**[OK]** をクリックして続行します。

	![](media/cdn-websites-with-cdn/4-signed-in.png)

8. Azure Websites が未作成の場合を想定して、Visual Studio には、Azure Websites 作成のヘルプ機能があります。**[Microsoft Azure Websites を構成する]** ダイアログ ボックスで、サイト名が一意になっていることを確認します。次に、**[OK]** をクリックします。

	![](media/cdn-websites-with-cdn/5-create-website.png)

9. ASP.NET アプリケーションが作成されたら、[**`<アプリケーション名>` をこのサイトに今すぐ発行する]** をクリックして、[Web 発行アクティビティ] ウィンドウでそのアプリケーションを Azure に発行します。**[発行]** をクリックしてプロセスを完了します。

	![](media/cdn-websites-with-cdn/6-publish-website.png)

	発行が完了すると、発行済みの Azure Websites がブラウザーに表示されます。 

1. CDN エンドポイントを作成するには、[Azure の管理ポータル](http://manage.windowsazure.com/)にログインします。 
2. **[新規]**、**[アプリケーション サービス]**、**[CDN]**、**[簡易作成]** の順にクリックします。**Http://*<サイト名>*azurewebsites.net/** を選択し、**[作成]** をクリックします。

	![](media/cdn-websites-with-cdn/7-create-cdn.png)

	>[WACOM.NOTE] CDN エンドポイントが作成されると、その URL と統合先のオリジン ドメインが Azure ポータルに表示されます。ただし、新しい CDN エンドポイントの構成がすべての CDN ノードの場所に完全に反映されるまで少し時間がかかる場合があります。 

3. Azure ポータルに戻り、**[CDN]** タブで、作成した CDN エンドポイントの名前をクリックします。

	![](media/cdn-websites-with-cdn/8-select-cdn.png)

3. **[クエリ文字列の有効化]** をクリックし、CDN キャッシュ内のクエリ文字列を有効にします。これを有効にすると、同一のリンクが異なるクエリ文字列でアクセスされた場合は別のエントリとしてキャッシュされます。

	![](media/cdn-websites-with-cdn/9-enable-query-string.png)

	>[WACOM.NOTE] チュートリアルのこのセクションでのクエリ文字列の有効化は必須ではありませんが、ここでの変更がすべての CDN ノードに反映されるまで時間がかかるため、できる限り早めに有効にしておくと便利です。また、クエリ文字列非対応コンテンツで CDN キャッシュが停滞するのを防ぐためでもあります (CDN コンテンツの更新については後で説明します)。

2. 次に、CDN エンドポイントのアドレスをクリックします。エンドポイントの準備が完了したら、Web サイトが表示されます。**HTTP 404 エラー**が表示された場合は、CDN エンドポイントの準備ができていません。CDN の構成がすべてのエッジ ノードに反映されるまで、最大で 1 時間待機する必要があります。 

	![](media/cdn-websites-with-cdn/11-access-success.png)

1. 次に、ASP.NET プロジェクトで **~/Content/bootstrap.css** ファイルにアクセスしてみます。ブラウザーのウィンドウで **http://*<cdnName>*.vo.msecnd.net/Content/bootstrap.css** に移動します。この設定では、次の URL を使用します。

		http://az673227.vo.msecnd.net/Content/bootstrap.css

	この URL は、CDN エンドポイントの次のオリジン URL に対応します。

		http://cdnwebapp.azurewebsites.net/Content/bootstrap.css

	**http://*<cdnName>*.vo.msecnd.net/Content/bootstrap.css** に移動すると、Azure Websites から bootstrap.css をダウンロードするよう求められます。 

	![](media/cdn-websites-with-cdn/12-file-access.png)

同様に、CDN エンドポイントから、**http://*<serviceName>*.cloudapp.net/** のパブリックにアクセスできる任意の URL にアクセスできます。次に例を示します。

-	/Script パスの .js ファイル
-	/Content パスの任意のコンテンツ ファイル
-	任意のコントローラー/アクション 
-	CDN エンドポイントでクエリ文字列が有効になっている場合、クエリ文字列を含む任意の URL
-	すべてのコンテンツがパブリックの場合、Azure Websites 全体

Azure CDN から Azure Websites 全体を配信することが常に (または一般的に) 最適とは限りません。いくつかの注意点があります。

-	Azure CDN ではプライベートなコンテンツを配信できないため、この方法ではサイト全体をパブリックにする必要があります。
-	予定されたメンテナンスであろうとユーザー エラーであろうと、CDN エンドポイントが何かの理由でオフラインになると、顧客をオリジン URL **http://*<sitename>*.azurewebsites.net/** にリダイレクトできる場合を除き、Azure Websites 全体がオフラインになります。 
-	カスタムの Cache-Control 設定 (「[Azure Websites の静的ファイルのキャッシュ オプションを構成する](#caching)」を参照してください) を使用した場合でも、CDN エンドポイントによって高度に動的なコンテンツのパフォーマンスが向上するわけではありません。上に示すように CDN エンドポイントからホーム ページを読み込もうとした場合、非常に単純な既定のホーム ページを初めて読み込むときに 5 秒以上かかります。このページに毎分更新する必要がある動的コンテンツが含まれていたとしたら、クライアント エクスペリエンスはどうなるでしょうか。動的コンテンツを CDN エンドポイントから配信するにはキャッシュの有効期限が短く設定されている必要があります。これは、CDN エンドポイントで頻繁にキャッシュ ミスが発生することになります。その結果、Azure Websites のパフォーマンスが低下し、CDN の目的が果たせなくなります。

また、Azure Websites で Azure CDN から配信するコンテンツをケースバイケースで決定する方法もあります。CDN エンドポイントから個々のコンテンツ ファイルにアクセスする方法については既に説明しました。CDN エンドポイントを介して特定のコントローラー アクションを配信する方法については、「[Azure CDN を介してコントローラー アクションからコンテンツを配信する](#controller)」で説明します。

<a name="caching"></a>
## Azure Websites の静的ファイルのキャッシュ オプションを構成する ##

Azure CDN 統合を Azure Websites に組み込むと、CDN エンドポイントで静的コンテンツをどのようにキャッシュするかを指定できます。そのためには、ASP.NET プロジェクト (例: *cdnwebapp*) から **Web.config** を開き `<system.webServer>` に `<staticContent>` 要素を追加します。次の XML では、3 日間で有効期限が切れるキャッシュを追加しています。  
<pre class="prettyprint">
<system.webServer>
  <mark><staticContent>
    <clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/>
  </staticContent></mark>
  ...
</system.webServer>
</pre>

この操作を実行すると、Azure Websites 内のすべての静的ファイルは、CDN キャッシュ内で同じ規則に従います。キャッシュ設定をより細かく制御するには、*Web.config* ファイルをフォルダーに追加し、そこに設定を追加します。たとえば、*Web.config* ファイルを *\Content* フォルダーに追加して、その内容を次の XML で置き換えます。

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

この設定は、*\Content* フォルダーのすべての静的ファイルを 15 日間キャッシュすることを指定しています。

`<clientCache>` 要素を構成する方法の詳細については、「[クライアント キャッシュ <clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache)」を参照してください。

「[Azure CDN を介してコントローラー アクションからコンテンツを配信する](#controller)」では、CDN キャッシュ内のコントローラー アクションの結果に対してキャッシュ設定を構成する方法についても説明します。

<a name="controller"></a>
## Azure CDN を介してコントローラー アクションからコンテンツを配信する ##

Azure CDN と Azure Websites を統合すると、Azure CDN を通じてコントローラー アクションからコンテンツを比較的簡単に配信できます。この場合も、CDN を通じて Azure Websites 全体を配信する場合は、すべてのコントローラー アクションが既に CDN から到達可能なため、前述の手順は不要です。ただし、「[統合された CDN エンドポイントで Azure Websites をデプロイする](#deploy)」で指摘したとおり、前述の手順の代わりに Azure CDN から配信するコントローラー アクションを選択できます。[Maarten Balliauw](https://twitter.com/maartenballiauw) 氏が、「[Reducing latency on the web with the Windows Azure CDN (Windows Azure CDN による Web の遅延時間の短縮)](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN)」の中でおもしろい MemeGenerator コントローラーを使用した方法を紹介しています。ここではその方法を再現します。

Azure Websites で次のような Chuck Norris の若いときの画像 ([Alan Light](http://www.flickr.com/photos/alan-light/218493788/) による撮影) に基づいてミームを生成するとします。

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

ここで使用する `Index` アクションは、顧客が画像内にジョークを指定することを許可し、顧客がアクションを送信するとミームを生成します。題材が Chuck Norris のため、このページは全世界で受け入れられると予想されます。これは、Azure CDN で半動的コンテンツを配信するよい例です。 

上記の手順を実行して、このコントローラー アクションを設定します。

1. *\Controllers* フォルダーに、*MemeGeneratorController.cs* という名前の新しい .cs ファイルを作成し、その内容を次のコードで置き換えます。強調表示されている箇所は、ファイル パスと CDN 名で置き換えてください。
	<pre class="prettyprint">
	using System;
	using System.Collections.Generic;
	using System.Diagnostics;
	using System.Drawing;
	using System.IO;
	using System.Net;
	using System.Web.Hosting;
	using System.Web.Mvc;
	using System.Web.UI;
	
	namespace cdnwebapp.Controllers
	{
	    public class MemeGeneratorController : Controller
	    {
	        static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

	        public ActionResult Index()
	        {
	            return View();
	        }
	
	        [HttpPost, ActionName(&quot;Index&quot;)]
        	public ActionResult Index_Post(string top, string bottom)
	        {
	            var identifier = Guid.NewGuid().ToString();
	            if (!Memes.ContainsKey(identifier))
	            {
	                Memes.Add(identifier, new Tuple<string, string>(top, bottom));
	            }
	
	            return Content(&quot;<a href=\&quot;&quot; + Url.Action(&quot;Show&quot;, new {id = identifier}) + &quot;\&quot;>here&#39;s your meme</a>&quot;);
	        }


	        [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Show(string id)
	        {
	            Tuple<string, string> data = null;
	            if (!Memes.TryGetValue(id, out data))
	            {
	                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
	            }
	
	            if (Debugger.IsAttached) // Preserve the debug experience
	            {
	                return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	            else // Get content from Azure CDN
	            {
	                return Redirect(string.Format(&quot;http://<mark><yourCDNName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	        }

	        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Generate(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;<mark>~/Content/chuck.bmp</mark>&quot;);
	            Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
	
	            using (Graphics graphics = Graphics.FromImage(bitmap))
	            {
	                SizeF size = new SizeF();
	                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
	                {
	                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
	                }
	                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
	                {
	                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
	                }
	            }
	
	            MemoryStream ms = new MemoryStream();
	            bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
	            return File(ms.ToArray(), &quot;image/png&quot;);
	        }
	
	        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
	        {
	            // Compute actual size, shrink if needed
	            while (true)
	            {
	                size = g.MeasureString(text, font);
	
	                // It fits, back out
	                if (size.Height < i.Height &&
	                     size.Width < i.Width) { return font; }
	
	                // Try a smaller font (90% of old size)
	                Font oldFont = font;
	                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
	                oldFont.Dispose();
	            }
	        }
	    }
	}
	</pre>

2. 既定の `Index()` アクション内で右クリックし、**[ビューの追加]** を選択します。

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  次の既定の設定をそのまま受け入れ、**[追加]** をクリックします。

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. 新しい *Views\MemeGenerator\Index.cshtml* を開き、その内容を、ジョークを送信するための次の単純な HTML で置き換えます。

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. もう一度 Azure Websites を発行して、ブラウザーで **http://*<serviceName>*.cloudapp.net/MemeGenerator/Index** に移動します。 

`/MemeGenerator/Index` にフォームの値を送信すると、`Index_Post` アクション メソッドにより、`Show` アクション メソッドへのリンクとそれぞれの入力の識別子が返されます。リンクをクリックすると、次のコードに到達します。  
<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple<string, string> data = null;
    if (!Memes.TryGetValue(id, out data))
    {
        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
    }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://<mark><yourCDNName></mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

ローカル デバッガーが接続されている場合は、ローカル リダイレクトにより通常のデバッグ エクスペリエンスが得られます。Azure Websites で実行されている場合は、次の場所にリダイレクトされます。

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

この URL は、CDN エンドポイントの次のオリジン URL に対応します。

	http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

URL の書き換え規則が適用された後、CDN エンドポイントにキャッシュされる実際のファイルは次のファイルになります。

	http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

次に、`Generate` メソッドの `OutputCacheAttribute` 属性を使用して、Azure CDN に有効な、アクション結果をキャッシュする方法を指定できます。次のコードでは、キャッシュの有効期限が 1 時間 (3,600 秒) に設定されています。

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

同様に、任意のキャッシュ オプションを使用して、Azure CDN を介して Azure Websites の任意のコントローラー アクションからコンテンツを配信できます。

次のセクションでは、バンドルされたスクリプト、縮小されたスクリプト、および CSS を Azure CDN 経由で提供する方法について説明します。 

<a name="bundling"></a>
## ASP.NET のバンドルと縮小を Azure CDN と統合できる。 ##

スクリプトや CSS スタイルシートはあまり頻繁に変更されないため、Azure CDN キャッシュの有力な候補になります。Azure CDN を介して Azure Websites 全体を提供するやり方は、バンドルや縮小を Azure CDN と統合する方法として最も簡単です。ただし、「[Azure CDN エンドポイントを Azure Websites と統合して、Azure CDN から Web ページの静的コンテンツを配信する](#deploy)」で説明した理由から、この方法を選択しない場合のために、ASP.NET のバンドルと縮小の開発者エクスペリエンスを維持しながらこれを実現する方法を紹介します。

-	優れたデバッグ モード エクスペリエンス
-	合理的なデプロイ
-	スクリプトや CSS のバージョンをアップグレードするためのクライアントの即時の更新
-	CDN エンドポイントのに障害に対するフォールバック メカニズム
-	最小限のコードの変更

「[Azure CDN エンドポイントを Azure Websites と統合して、Azure CDN から Web ページの静的コンテンツを配信する](#deploy)」で作成した ASP.NET プロジェクトで、*App_Start\BundleConfig.cs* を開き、`bundles.Add()` メソッドの呼び出しを確認します。

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

最初の `bundles.Add()` ステートメントにより、仮想ディレクトリ `~/bundles/jquery` にスクリプトのバンドルが追加されます。次に、*Views\Shared_Layout.cshtml* を開いて、スクリプト バンドル タグがどのようにレンダリングされているかを確認します。次の Razor コードが見つかります。

    @Scripts.Render("~/bundles/jquery")

Azure Websites でこの Razor コードが実行されると、次のようなスクリプト バンドルのための `<script>` タグがレンダリングされます。 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

ただし、`F5` キーを押してこのコードを Visual Studio 内で実行した場合は、バンドル内の各スクリプト ファイルが個別にレンダリングされます (上記のケースでは、バンドルに含まれているスクリプト ファイルは 1 つだけです)。

    <script src="/Scripts/jquery-1.10.2.js"></script>

これにより、JavaScript コードを開発環境でデバッグできる一方で、運用環境での同時クライアント接続数 (バンドル) を削減し、ファイルのダウンロード パフォーマンス (縮小) を高めることができます。これは、Azure CDN 統合によって得られる優れた機能です。加えて、レンダリングされたバンドルには自動生成されたバージョン文字列が既に含まれます。そこで、この機能を複製することで、NuGet を介して jQuery バージョンを更新するたびに、できる限り迅速にクライアント側にその更新が反映されるようにすることができます。

ASP.NET のバンドルおよび縮小を CDN エンドポイントと統合するには、次の手順に従います。

1. *App_Start\BundleConfig.cs* に戻り、CDN アドレスを指定する別の `Bundle コンストラクター`を使用するように [bundles.Add()](http://msdn.microsoft.com/ja-jp/library/jj646464.aspx) メソッドを変更します。そのためには、`RegisterBundles` メソッドの定義を次のコードで置き換えます。  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://<yourCDNName>.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jquery&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;<mark>, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)</mark>).Include(
	                &quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;<mark>, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)</mark>).Include(
	                &quot;~/Scripts/bootstrap.js&quot;,
	                &quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;<mark>, string.Format(cdnUrl, &quot;Content/css&quot;)</mark>).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}
	</pre>

	`<yourCDNName>` は Azure CDN の名前で置き換えてください。

	このコードでは、`bundles.UseCdn = true` を設定し、巧妙に作成された CDN URL を各バンドルに追加しています。たとえば、コードの最初のコンストラクター

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	は、次のコードと同じです。 

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	このコンストラクターは、ASP.NET のバンドルおよび縮小機能に対して、ローカルにデバッグされたときに個々のスクリプト ファイルをレンダリングする一方で、指定された CDN アドレスを使用して対象のスクリプトにアクセスすることを指定しています。ここで、この巧妙に作成された CDN URL の 2 つの重要な特性に注意してください。
	
	-	この CDN URL のオリジンは、`http://<yourSiteName>.azurewebsites.net/bundles/jquery?v=<W.X.Y.Z>` です。これは、実際には Web アプリケーションのスクリプト バンドルの仮想ディレクトリです。
	-	CDN コンストラクターを使用しているため、バンドルのための CDN スクリプト タグには、レンダリングされた URL 内の自動生成されたバージョン文字列が含まれません。Azure CDN でキャッシュ ミスを強制的に発生させるには、スクリプト バンドルを変更するたびに一意のバージョン文字列を手動で生成する必要があります。また、この一意のバージョン文字列は、バンドルがデプロイされた後の Azure CDN でのキャッシュ ヒットを最大化するために、デプロイの有効期間を通じて一定に保たれる必要があります。
	-	クエリ文字列 v=<W.X.Y.Z> は、ASP.NET プロジェクトの *Properties\AssemblyInfo.cs* から取得されます。Azure に発行するたびにアセンブリ バージョンを増分する操作を含むデプロイ ワークフローを導入できます。また、ワイルドカード文字 * を使って単にプロジェクトの *Properties\AssemblyInfo.cs* を変更して、ビルドするたびに自動的にバージョン文字列を増分するよう設定することもできます。次に例を示します。
	
			[assembly: AssemblyVersion("1.0.0.*")]
	
		デプロイの有効期間を通じて一意の文字列を生成する操作を合理化するための他の戦略も使用できます。

3. ASP.NET アプリケーションを再発行し、ホーム ページにアクセスします。
 
4. ページの HTML コードを表示します。Azure Websites に変更を再発行するたびに一意のバージョン文字列を付けて CDN URL がレンダリングされているのを確認できます。次に例を示します。  
	<pre class="prettyprint">
	...

    <link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/>

    <script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;>&lt;/script>

	...

    <script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;></script>

    <script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;></script>

	...</pre>

5. Visual Studio で、`F5` キーを押して ASP.NET アプリケーションをデバッグします。 

6. ページの HTML コードを表示します。Visual Studio での一貫したデバッグ エクスペリエンスが得られるように個別にレンダリングされた各スクリプト ファイルを確認できます。  
	<pre class="prettyprint">
	...
	
	    <link href=&quot;/Content/bootstrap.css&quot; rel=&quot;stylesheet&quot;/>
	<link href=&quot;/Content/site.css&quot; rel=&quot;stylesheet&quot;/>
	
	    <script src=&quot;/Scripts/modernizr-2.6.2.js&quot;></script>
	
	...
	
	    <script src=&quot;/Scripts/jquery-1.10.2.js&quot;></script>
	
	    <script src=&quot;/Scripts/bootstrap.js&quot;></script>
	<script src=&quot;/Scripts/respond.js&quot;></script>
	
	...    
	</pre>

<a name="fallback"></a>
## CDN URL のフォールバック メカニズム ##

なんらかの理由で Azure CDN エンドポイントに障害が発生した場合に備えて、JavaScript または Bootstrap を読み込むためのフォールバック オプションとして、オリジン Web サーバーにアクセスできるようにしておくと便利です。CDN が利用できないために Web サイトの画像が失われることは深刻な事態ですが、スクリプトやスタイルシートで提供される重要なページ機能が失われることは、さらに深刻な事態です。

[Bundle](http://msdn.microsoft.com/ja-jp/library/system.web.optimization.bundle.aspx) クラスには、CDN 障害に対するフォールバック メカニズムを構成するための [CdnFallbackExpression](http://msdn.microsoft.com/ja-jp/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) プロパティがあります。このプロパティを使用するには、次の手順に従います。

1. ASP.NET プロジェクトで、それぞれの *Bundle コンストラクター*に CDN URL を追加した [App_Start\BundleConfig.cs](http://msdn.microsoft.com/ja-jp/library/jj646464.aspx) を開き、次の強調表示された変更を加えて、既定のバンドルにフォールバック メカニズムを追加します。  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://cdnurl.vo.msecnd.net/.../{0}?&quot; + version;
	    bundles.UseCdn = true;
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;, string.Format(cdnUrl, &quot;bundles/jquery&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.jquery&quot; }</mark>
	                .Include(&quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;$.validator&quot; }</mark>
	            	.Include(&quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.Modernizr&quot; }</mark>
					.Include(&quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)) 	
					<mark>{ CdnFallbackExpression = &quot;$.fn.modal&quot; }</mark>
	        		.Include(
		              		&quot;~/Scripts/bootstrap.js&quot;,
		              		&quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;, string.Format(cdnUrl, &quot;Content/css&quot;)).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}</pre>

	`CdnFallbackExpression` が null でない場合は、HTML にスクリプトが挿入されます。このスクリプトは、バンドルが正常に読み込まれたかどうかをテストして、正常に読み込まれていない場合はオリジン Web サーバーのバンドルに直接アクセスします。このプロパティは、それぞれの CDN バンドルが適切に読み込まれたかどうかをテストする JavaScript 式に設定する必要があります。各バンドルをテストするために必要な式は、コンテンツによって異なります。上記の既定のバンドルに対して、次のように定義されています。
	
	-	`window.jquery` は jquery-{version}.js に定義されています。
	-	`$.validator` は jquery.validate.js に定義されています。
	-	`window.Modernizr` は modernizer-{version}.js に定義されています。
	-	`$.fn.modal` は bootstrap.js に定義されています。
	
	`~/Cointent/css` バンドルには CdnFallbackExpression を設定していません。これは、期待される [<link>](https://aspnetoptimization.codeplex.com/workitem/104) タグではなくフォールバック CSS の `<script>` タグが挿入されるという `System.Web.Optimization` のバグが存在するためです。
	
	ただし、[Ember Consulting Group](https://github.com/EmberConsultingGroup/StyleBundleFallback) から優れた[スタイル バンドルのフォールバック](https://github.com/EmberConsultingGroup)が提供されています。 

2. CSS の対処法を使用するには、ASP.NET プロジェクトの *App_Start* フォルダーに *StyleBundleExtensions.cs* という名前の新しい .cs ファイルを作成し、その内容を [GitHub に公開されているコード](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs)で置き換えます。 

4. *App_Start\StyleFundleExtensions.cs* で、名前空間の名前を ASP.NET アプリケーションの名前空間 (例: **cdnwebapp**) に変更します。 

3. `App_Start\BundleConfig.cs` に戻り、最後の `bundles.Add` ステートメントを、次の強調表示されたコードで置き換えます。  
	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	この新しいメソッドでは、同じアイデアを使用するスクリプトを HTML に挿入します。つまり、DOM を調べて CSS バンドルに定義されているクラス名、規則名、および規則値が一致するかどうかを確認し、一致しない場合はオリジン Web サーバーにフォールバックします。

4. もう一度 Azure Websites に発行し、ホーム ページにアクセスします。 
5. ページの HTML コードを表示します。次のようなスクリプトが挿入されていることを確認できます。    
	<pre class="prettyprint">...
	
		<link href=&quot;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/>
	<mark><script>(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i < len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf(&#39;http://az673227.vo.msecnd.net/Content/css?v=1.0.0.25474&#39;) !== -1) {
	                        var meta = document.createElement(&#39;meta&#39;);
	                        meta.className = &#39;sr-only&#39;;
	                        document.head.appendChild(meta);
	                        var value = window.getComputedStyle(meta).getPropertyValue(&#39;width&#39;);
	                        document.head.removeChild(meta);
	                        if (value !== &#39;1px&#39;) {
	                            document.write(&#39;<link href=&quot;/Content/css&quot; rel=&quot;stylesheet&quot; type=&quot;text/css&quot; />&#39;);
	                        }
	                    }
	                }
	                return true;
	            }())||document.write(&#39;<script src=&quot;/Content/css&quot;><\/script>&#39;);</script></mark>
	
	    <script src=&quot;http://az673227.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;></script>
	<mark><script>(window.Modernizr)||document.write(&#39;<script src=&quot;/bundles/modernizr&quot;><\/script>&#39;);</script></mark>
	
	...	
	
	    <script src=&quot;http://az673227.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;></script>
	<mark><script>(window.jquery)||document.write(&#39;<script src=&quot;/bundles/jquery&quot;><\/script>&#39;);</script></mark>
	
	    <script src=&quot;http://az673227.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;></script>
	<mark><script>($.fn.modal)||document.write(&#39;<script src=&quot;/bundles/bootstrap&quot;><\/script>&#39;);</script></mark>
	
	...
	</pre>

	CSS バンドルに対して挿入されたスクリプトには、次の行に `CdnFallbackExpression` プロパティのエラーの残りがまだ含まれている点に注意してください。

        }())||document.write('<script src="/Content/css"><\/script>');</script>

	ただし、(すぐ上の行の) || 式の最初の部分は常に true を返すため、document.write() 関数が実行されることはありません。

6. フォールバック スクリプトが動作するかどうかをテストするには、CDN エンドポイントのダッシュボードに戻り、**[エンドポイントを無効にする]** をクリックします。

	![](media/cdn-websites-with-cdn/13-test-fallback.png)

7. Azure Websites のブラウザー ウィンドウを更新します。これで、すべてのスクリプトとスタイルシートが正しく読み込まれていることを確認できます。

# 詳細 #
- [Azure コンテンツ配信ネットワーク (CDN) の概要](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Web アプリケーションで Azure CDN からコンテンツを配信する](http://azure.microsoft.com/ja-jp/Documentation/Articles/cdn-serve-content-from-cdn-in-your-web-application/)
- [クラウド サービスと Azure CDN との統合](http://azure.microsoft.com/ja-jp/documentation/articles/cdn-cloud-service-with-cdn/)
- [Bundling and Minification (バンドルおよび縮小)](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Azure 用 CDN の使用](http://azure.microsoft.com/ja-jp/documentation/articles/cdn-how-to-use/)