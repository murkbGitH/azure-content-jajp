<properties
	pageTitle="node.js を使用する Azure AD へのサインインおよびサインアウトの概要"
	description="サインインのために Azure AD と連携する node.js Express MVC Web アプリを構築する方法"
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="brandwe"/>

# Azure AD を使用した Web アプリのサインインおよびサインアウト


ここでは、Passport を使用して次のことを行います。

- Azure AD と v2.0 アプリ モデルを使用するアプリにユーザーをサインインします。
- ユーザーについての情報を表示します。
- ユーザーをアプリからサインアウトします。

**Passport** は Node.js 用の認証ミドルウェアです。Passport は、非常に柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Resitify Web アプリケーションに、支障をきたすことなくドロップされます。包括的な認証手法セットにより、ユーザー名とパスワードを使用する認証、Facebook、Twitter などをサポートします。Microsoft Azure Active Directory 用の戦略が開発されています。このモジュールをインストールし、Microsoft Azure Active Directory `passport-azure-ad` プラグインを追加します。

これを行うには、次の手順を実行する必要があります。

1. アプリを登録します。
2. Passport-azure-ad 戦略を使用するようにアプリを設定する
3. Passport を使用して、サインイン要求とサインアウト要求を Azure AD に発行する
4. ユーザーに関するデータを印刷する

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS) で管理されています。追加の参考資料として、[アプリのスケルトン (.zip) をダウンロード](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)したり、スケルトンを複製したりすることができます:

``git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git``

完成したアプリケーションは、このチュートリアルの終わりにも示しています。

## 1\.アプリを登録します
- Microsoft Azure 管理ポータルにサインインします。
- 左側のナビゲーションで **[Active Directory]** をクリックします。
- アプリケーションの登録先となるテナントを選択します。
- **[アプリケーション]** タブをクリックし、下部のドロアーで [追加] をクリックします。
- 画面の指示に従い、新しい **Web アプリケーションまたは WebAPI** を作成します。
    - アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
    -	**[サインオン URL]** は、アプリのベース URL です。スケルトンの既定値は、`http://localhost:3000/auth/openid/return`` です。
    - **[アプリケーション ID/URI]** は、アプリケーションの一意識別子です。形式は、`https://<tenant-domain>/<app-name>` (たとえば、`https://contoso.onmicrosoft.com/my-first-aad-app`) です。
- 登録が完了すると、AAD により、アプリケーションに一意のクライアント ID が割り当てられます。この値は次のセクションで必要になるので、[構成] タブからコピーします。

## 2\.ディレクトリに前提条件を追加する

コマンド ラインから、ディレクトリをルート フォルダーに移動し (まだルート フォルダーでない場合)、次のコマンドを実行します。

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- さらに、次のように `passport-azure-ad` も必要です。

- `npm install passport-azure-ad`

これにより、passport-azure-ad が依存するライブラリがインストールされます。

## 3\.passport-node-js 戦略を使用するようにアプリを設定する
ここでは、OpenID Connect 認証プロトコルを使用するように、Express ミドルウェアを構成します。Passport は、サインイン要求とサインアウト要求の発行、ユーザー セッションの管理、ユーザーに関する情報の取得などを行うために使用されます。

-	最初に、プロジェクトのルートにある `config.js` ファイルを開いて、`exports.creds` セクションでアプリの構成値を入力します。
    -	`clientID:` は、登録ポータルでアプリに割り当てられた**アプリケーション ID** です。
    -	`returnURL` は、ポータルで入力した**リダイレクト URI** です。
    - `clientSecret` は、ポータルで生成したシークレットです。
    - `realm` は、ポータルで入力した、ルートがない場合の**リダイレクト URI** (例: http//localhost:3000) です。
    - `issuer` は、https://sts.windows.net/96702724-991f-4576-bc90-be9862749ac5/ のように、**アプリケーション ID** を追加する前の部分になります。

- 次に、プロジェクトのルートにある `app.js` ファイルを開き、次の呼び出しを追加して、`passport-azure-ad` に付属する `OIDStrategy` 戦略を呼び出します。


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;
```

- その後、今参照した戦略を使用してログイン要求を処理します。

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 

//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('We received profile of: ', profile);
    log.info('Example: Email address we received was: ', profile._json.upn);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile._json.upn, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport は、すべての戦略ライターが従うすべての戦略 (Twitter や Facebook など) に対して類似するパターンを使用します。戦略を調べると、それは、パラメーターとして token と done を持つ function() が渡されることがわかります。戦略は、その処理をすべて終えると、必ず戻ってきます。戻ったら、再度要求しなくてもいいように、ユーザーを保存し、トークンを隠します。


> [AZURE.IMPORTANT]上記のコードでは、サーバーに認証を求めたすべてのユーザーを受け入れています。これは、自動登録と呼ばれます。運用サーバーでは、指定された登録プロセスを先に実行していないユーザーにはアクセスを許可しないように設定できます。これは、Facebook への登録は許可するが、その後で追加情報の入力を求めるコンシューマー アプリで通常見られるパターンです。これがサンプル アプリケーションでなければ、返されるトークン オブジェクトから電子メールを抽出した後、追加情報の入力を要求できます。これはテスト サーバーなので、単純にユーザーをメモリ内データベースに追加します。

- 次に、Passport で必要な、ログオンしているユーザーの追跡を可能にするメソッドを追加します。これには、ユーザーの情報のシリアル化と逆シリアル化が含まれます。

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.preferred_username);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    if (user._json.upn === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

- 次に、express エンジンを読み込むコードを追加します。ここでは、Express が提供する既定の /views と /routes のパターンを使用します。

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- 最後に、実際のログイン要求を `passport-azure-ad` エンジンに渡す POST ルートを追加します。

```JavaScript

// Our POST routes (Section 3)

// POST /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.post('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});
```

## 4\.Passport を使用してサインイン要求とサインアウト要求を Azure AD に発行する

OpenID Connect 認証プロトコルを使用して v2.0 エンドポイントと通信するように、アプリが適切に構成されました。認証メッセージの作成、Azure AD からのトークンの検証、ユーザー セッションの維持という細かな処理は、すべて `passport-azure-ad` が行います。残っているのは、サインインとサインアウトを行う方法をユーザーに提示することと、ログインしているユーザーに関する追加情報を収集することです。

- まず、既定のメソッド、login メソッド、account メソッド、および logout メソッドを `app.js` ファイルに追加します。

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login', 
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-	これらについて詳しく説明しましょう。
    -	`/` ルートは、index.ejs ビューにリダイレクトして、要求内のユーザーを渡します (存在する場合)。
    - `/account` ルートは、***ユーザーが認証されていることを確認した後*** (次で実装します)、ユーザーに関する追加情報を取得できるように、要求内のユーザーを渡します。
    - `/login` ルートは、`passport-azuread` から azuread-openidconnect 認証子を呼び出します。これが失敗した場合は、ユーザーはもう一度 /login にリダイレクトされます。
    - `/logout` は、Cookie をクリアする logout.ejs (およびルート) を呼び出した後、ユーザーを index.ejs に返します。


- `app.js` の最後の部分に、上記の `/account` で使用される EnsureAuthenticated メソッドを追加します。

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)
//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}
```

- 最後に、`app.js` でサーバー自体を実際に作成します。

```JavaScript

app.listen(3000);

```


## 5\.Web サイトにユーザーを表示する express のビューとルートを作成する

`app.js` が完成しました。これで必要なのは、取得した情報をユーザーに表示するルートとビューを追加し、作成した `/logout` ルートと `/login` ルートを処理することだけです。

- ルート ディレクトリの下に `/routes/index.js` ルートを作成します。

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- ルート ディレクトリの下に `/routes/user.js` ルートを作成します。

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

これらは、要求 (存在する場合はユーザーも) をビューに渡すだけの単純なルートです。

- ルート ディレクトリの下に `/views/index.ejs` ビューを作成します。これは login メソッドと logout メソッドを呼び出して、アカウント情報を取得できるようにする単純なページです。条件付きの `if (!user)` を使用できることに注目してください。要求でユーザーが渡されることは、ログインしているユーザーが存在していることの証拠です。

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
	<h2>Hello, <%= user._json.given_name %>.</h2>
	<a href="/account">Account Info</a></br>
	<a href="/logout">Log Out</a>
<% } %>

```

- ルート ディレクトリの下に `/views/account.ejs` ビューを作成し、`passport-azuread` によってユーザー要求内に配置された追加情報を表示できるようにします。

```Javascript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<a href="/logout">Log Out</a>
<% } %>
```

- 最後に、レイアウトを追加して、見やすくします。ルート ディレクトリの下に '/views/layout.ejs' ビューを作成します。

```JavaScript

<!DOCTYPE html>
<html>
	<head>
		<title>Passport-OpenID Example</title>
	</head>
	<body>
		<% if (!user) { %>
			<p>
			<a href="/">Home</a> | 
			<a href="/login">Log In</a>
			</p>
		<% } else { %>
			<p>
			<a href="/">Home</a> | 
			<a href="/account">Account</a> | 
			<a href="/logout">Log Out</a>
			</p>
		<% } %>
		<%- body %>
	</body>
</html>
```

最後に、アプリを構築して実行します。

`node app.js` を実行し、`http://localhost:3000` に移動します。


Microsoft の個人または職場/学校アカウントのいずれかでサインインすると、ユーザーの ID が /account リストにどのように反映されるかがわかります。これで、Web アプリが業界標準のプロトコルで保護され、個人および職場/学校アカウントの両方でユーザーを認証できるようになりました。

参照用の完全なサンプル (構成値を除く) が、[.zip としてこちらで提供されています](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip)。または、次のように GitHub から複製できます。

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```


これ以降は、さらに高度なトピックに進むことができます。次のチュートリアルを試してみてください。

[Azure AD を使用することによる Web API の保護](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=August15_HO9-->