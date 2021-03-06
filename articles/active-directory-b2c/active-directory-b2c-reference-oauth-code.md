<properties
	pageTitle="Azure AD B2C プレビュー | Microsoft Azure"
	description="Azure AD で導入された OpenID Connect 認証プロトコルを利用し、Web アプリケーションを構築します。"
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Azure AD B2C プレビュー: OAuth 2.0 認証コード フロー

デバイスにインストールされているアプリに、Web API など、保護されているリソースにアクセスする権利を与えるために OAuth 2.0 認証コード付与を利用できます。Azure AD B2C で導入された OAuth 2.0 を利用することで、サインアップ、サインイン、その他の ID 管理タスクをモバイル アプリとデスクトップ アプリに追加できます。本ガイドでは、オープンソース ライブラリを利用せず、HTTP メッセージを送受信する方法について説明します。本ガイドは言語非依存です。

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

OAuth 2.0 承認コード フローは、[OAuth 2.0 仕様のセクション 4.1](http://tools.ietf.org/html/rfc6749) で規定されています。[Web アプリ](active-directory-b2c-apps.md#web-apps)や[ネイティブにインストールされるアプリ](active-directory-b2c-apps.md#mobile-and-native-apps)を含め、大半のアプリ タイプで認証と承認を行う際にこのフローを利用できます。アプリは、このフローによって安全に **access\_tokens** を取得し、[認証サーバー](active-directory-b2c-reference-protocols.md#the-basics)で保護されているリソースにアクセスできます。本ガイドでは特に、OAuth 2.0 認証コード フローの「**パブリック クライアント**」について説明します。パブリック クライアントとは、秘密のパスワードの整合性を守る目的で信頼できないクライアント アプリケーションのことです。モバイル アプリ、デスクトップ アプリ、デバイスで実行され、access\_tokens の取得が必要な大半のアプリが該当します。Azure AD B2C を利用し、Web アプリに ID 管理を追加する場合、OAuth 2.0 ではなく、[OpenID Connect](active-directory-b2c-reference-oidc.md) をご利用ください。

Azure AD B2C は、単純な認証と権限付与以上のことができるように標準の OAuth 2.0 プロトコルを拡張したものです。[**ポリシー パラメーター**](active-directory-b2c-reference-poliices.md)を導入しており、このパラメーターにより、OAuth 2.0 を利用し、サインアップ、サインイン、プロファイル管理などのユーザー操作をアプリに追加できます。ここでは、OAuth 2.0 とポリシーを利用し、ネイティブ アプリケーションに各種のユーザー操作を導入し、Web API にアクセスするための access\_tokens を取得する方法について説明します。

下の HTTP 要求例では、サンプル B2C ディレクトリの **fabrikamb2c.onmicrosoft.com**、サンプル アプリケーション、ポリシーを利用します。これらの値を利用し、要求を自由にお試しいただけます。あるいは、独自の値で置換できます。[独自の B2C ディレクトリ、アプリケーション、ポリシーの取得方法](#use-your-own-b2c-directory)について学習してください。

## 1\.承認コードを取得する
承認コード フローは、クライアントがユーザーを `/authorize` エンドポイントにリダイレクトさせることから始まります。これはフローの対話部分であり、ユーザーが実際に操作します。この要求では、クライアントはユーザーから取得する必要があるアクセス許可を `scope` パラメーターで示し、実行するポリシーを `p` パラメーターで示します。3 つの例を以下に示します (読みやすいように改行してあります)。それぞれ異なるポリシーが使用されています。

#### サインイン ポリシーを使用する

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### サインアップ ポリシーを使用する

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### プロファイル ポリシー編集を使用する

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | 必須 | [Azure ポータル](https://portal.azure.com)からアプリに割り当てられたアプリケーション ID。 |
| response\_type | 必須 | 承認コード フローでは `code` を指定する必要があります。 |
| redirect\_uri | 必須 | アプリ の redirect\_uri。アプリは、この URI で認証応答を送受信することができます。ポータルで登録したいずれかの redirect\_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope | 必須 | スコープのスペース区切りリスト。1 つのスコープ値は、要求されている両方のアクセス許可を Azure AD を示します。`openid` スコープは、ユーザーをサインインさせ、**id\_tokens** の形式でユーザーに関するデータを取得するためのアクセス許可を示します (これについては後に詳しく説明します)。`offline_access` 範囲は、リソースに長期アクセスするためにアプリは **refresh\_token** を必要とすることを示します。 |
| response\_mode | 推奨 | 結果として得られた authorization\_code をアプリに返す際に使用するメソッドを指定します。'query'、'form\_post'、'fragment' のいずれかを指定できます。
| state | 推奨 | 要求に含まれ、かつトークンの応答として返される値。任意の文字列を指定することができます。クロスサイト リクエスト フォージェリ攻撃を防ぐために通常、ランダムに生成された一意の値が使用されます。この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページや実行中のポリシーなど) に関する情報をエンコードする目的にも使用されます。 |
| p | 必須 | 実行するポリシーを示します。B2C ディレクトリで作成されたポリシーの名前であり、その値は必ず「b2c\_1\_」で始まります。ポリシーに関する詳細は、[ここ](active-directory-b2c-reference-policies.md)にあります。 |
| prompt | 省略可能 | ユーザーとの必要な対話の種類を指定します。現時点で有効な値は 'login' のみです。この場合ユーザーは要求時に、その資格情報を入力する必要があります。シングル サインオンは作用しません。 |

この時点で、ユーザーにポリシーのワークフローの完了が求められます。たとえば、ユーザー名とパスワードを入力したり、ソーシャル ID でサインインしたり、ディレクトリにサインアップしたりする必要があります。他にも、ポリシーの定義に基づき、多数の手順があります。ユーザーがポリシーを完了すると、Azure AD は `response_mode` パラメーターに指定されたメソッドを使い、指定された `redirect_uri` でアプリに応答を返します。実行されたポリシーに関係なく、上記のすべての例で応答はまったく同じになります。

`response_mode=query` を使用した場合の正常な応答は次のようになります。

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| code | アプリが要求した authorization\_code。アプリは承認コードを使用して、対象リソースのアクセス トークンを要求します。承認コードは有効期間が非常に短く、通常 10 分後には期限切れとなります。 |
| state | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

アプリ側でエラーを適切に処理できるよう、`redirect_uri` にはエラー応答も送信されます。

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| state | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |


## 2\.トークンを取得する
authorization\_code を取得できたので、`POST` 要求を `/token` エンドポイントに送信し、トークンの `code` を任意のリソースに適用できます。Azure AD B2C プレビューでは、トークンを要求できる唯一のリソースはアプリの独自のバックエンド Web API です。トークンの要求には一般的にスコープ `openid` が使用されます。

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob"
}
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | --------------------- |
| p | 必須 | 認証コードの取得に使用されたポリシー。この要求に別のポリシーを使用することはできません。**このパラメーターはクエリ文字列に追加されることに注意してください。**POST 本文ではありません。 |
| client\_id | 必須 | [Azure ポータル](https://portal.azure.com)からアプリに割り当てられたアプリケーション ID。 |
| grant\_type | 必須 | 承認コード フローでは `authorization_code` を指定する必要があります。 |
| scope | 必須 | スコープのスペース区切りリスト。1 つのスコープ値は、要求されている両方のアクセス許可を Azure AD を示します。`openid` スコープは、ユーザーをサインインさせ、**id\_tokens** の形式でユーザーに関するデータを取得するためのアクセス許可を示します。クライアントと同じアプリケーション ID で表され、アプリの独自のバックエンド Web API にトークンを届けるために利用できます。`offline_access` 範囲は、リソースに長期アクセスするためにアプリは **refresh\_token** を必要とすることを示します。 |
| code | 必須 | フローの最初の段階で取得した authorization\_code。 |
| redirect\_uri | 必須 | authorization\_code を受け取った、アプリケーションの redirect\_uri。 |

正常なトークン応答は次のようになります。

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| not\_before | トークンが有効と見なされる時間 (エポック時間)。 |
| token\_type | トークン タイプ値を指定します。Azure AD でサポートされるのは Bearer タイプのみです。 |
| id\_token | 要求した署名付きの JWT トークン。 |
| scope | トークンの有効スコープ。後の利用のためにトークンのキャッシュに利用できます。 |
| id\_token\_expires\_in | id\_token の有効期間 (秒)。 |
| profile\_info | base-64 エンコードの JSON 文字列。ネイティブ アプリケーションに表示するユーザーに関する役に立つ情報が含まれる場合があります。その厳密なコンテンツは、ポリシーで構成したアプリケーション要求に基づきます。 |
| refresh\_token | OAuth 2.0 更新トークン。現在のトークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のトークンを取得します。Refresh\_token は有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。詳細については、[B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。 |
| refresh\_token\_expires\_in | 更新トークンの最大有効期間 (秒)。ただし、更新トークンは任意の時点で無効になる場合があります。 |

> [AZURE.NOTE]この時点で access\_token の場所がわからなければ、次の点を考慮してください。`openid` スコープを要求すると、Azure AD は応答で JWT `id_token` を発行します。この `id_token` は厳密には OAuth 2.0 access\_token ではなく、クライアントと同じ client\_id で表され、アプリの独自のバックエンド サービスと通信するときなどに利用できます。`id_token` は署名付きの JWT ベアラー トークンであり、HTTP 認証ヘッダーでリソースに送信し、要求の認証に利用できます。違いは、`id_token` には、特定のクライアント アプリケーションに与えられるアクセスをスコープ ダウンするメカニズムがないことにあります。ただし、(現行の Azure AD B2C プレビューでそうであるように) クライアント アプリケーションがバックエンド サービスと通信できる唯一のクライアントの場合、そのようなスコープ メカニズムは必要ありません。Azure AD B2C プレビューで追加のファースト パーティ リソースとサード パーティ リソースと通信する機能がクライアントに与えられるとき、access\_tokens が導入されます。ただし、そのときであっても、アプリの独自のバックエンド サーバスには `id_tokens` で通信することが推奨されます。Azure AD B2C プレビューで構築できるアプリケーションの種類については、[この記事](active-directory-b2c-apps.md)を参照してください。

エラー応答は次のようになります。

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

## 3\.トークンを使用する
`id_token` を無事取得したら、そのトークンを `Authorization` ヘッダーに追加することによって、バックエンド Web API への要求に使用することができます。

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## 4\.トークンを更新する
Id\_tokens は有効期間が短く、期限が切れた後もリソースにアクセスし続けるためにはトークンを更新する必要があります。アクセス トークンを更新するには、もう一度 `POST` 要求を `/token` エンドポイントに送信します。このとき、`code` の代わりに `refresh_token` を指定します。

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob"
}
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | -------- |
| p | 必須 | 元の更新トークンの取得に使用されたポリシー。この要求に別のポリシーを使用することはできません。**このパラメーターはクエリ文字列に追加されることに注意してください。**POST 本文ではありません。 |
| client\_id | 必須 | [Azure ポータル](https://portal.azure.com)からアプリに割り当てられたアプリケーション ID。 |
| grant\_type | 必須 | この段階の承認コード フローでは `refresh_token` を指定する必要があります。 |
| scope | 必須 | スコープのスペース区切りリスト。1 つのスコープ値は、要求されている両方のアクセス許可を Azure AD を示します。`openid` スコープは、ユーザーをサインインさせ、**id\_tokens** の形式でユーザーに関するデータを取得するためのアクセス許可を示します。クライアントと同じアプリケーション ID で表され、アプリの独自のバックエンド Web API にトークンを届けるために利用できます。`offline_access` 範囲は、リソースに長期アクセスするためにアプリは **refresh\_token** を必要とすることを示します。 |
| redirect\_uri | 必須 | authorization\_code を受け取った、アプリケーションの redirect\_uri。 |
| refresh\_token | 必須 | フローの第 2 段階で取得した元の refresh\_token。 |

正常なトークン応答は次のようになります。

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| not\_before | トークンが有効と見なされる時間 (エポック時間)。 |
| token\_type | トークン タイプ値を指定します。Azure AD でサポートされるのは Bearer タイプのみです。 |
| id\_token | 要求した署名付きの JWT トークン。 |
| scope | トークンの有効スコープ。後の利用のためにトークンのキャッシュに利用できます。 |
| id\_token\_expires\_in | id\_token の有効期間 (秒)。 |
| profile\_info | base-64 エンコードの JSON 文字列。ネイティブ アプリケーションに表示するユーザーに関する役に立つ情報が含まれる場合があります。その厳密なコンテンツは、ポリシーで構成したアプリケーション要求に基づきます。 |
| refresh\_token | OAuth 2.0 更新トークン。現在のトークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のトークンを取得します。Refresh\_token は有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。詳細については、[B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。 |
| refresh\_token\_expires\_in | 更新トークンの最大有効期間 (秒)。ただし、更新トークンは任意の時点で無効になる場合があります。 |

エラー応答は次のようになります。

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |


<!-- 

Here is the entire flow for a native  app; each request is detailed in the sections below:

![OAuth Auth Code Flow](./media/active-directory-b2c-reference-oauth-code/convergence_scenarios_native.png) 

-->

## 独自の B2C ディレクトリを使用する

これらの要求を自分で試す場合、最初に次の 3 つの手順を実行し、上記のサンプルの値を独自の値に置換する必要があります。

- [B2C ディレクトリを作成し](active-directory-b2c-get-started.md)、要求でディレクトリの名前を使用します。
- [アプリケーションを作成し](active-directory-b2c-app-registration.md)、アプリケーション ID と redirect\_uri を取得します。アプリに**ネイティブ クライアント**を追加します。
- [ポリシーを作成し](active-directory-b2c-reference-policies.md)、ポリシー名を取得します。

<!---HONumber=Oct15_HO3-->