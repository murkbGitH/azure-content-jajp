<properties 
	pageTitle="動的暗号化:.NET を使用してコンテンツ キー承認ポリシーを構成します。" 
	description="コンテンツ キー承認ポリシーを構成する方法について説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>



#動的暗号化:コンテンツ キー承認ポリシーを構成する 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)] 

この記事は、「[メディア サービスのビデオ オンデマンド ワークフロー](media-services-video-on-demand-workflow.md)」と「[メディア サービスのライブ ストリーミング ワークフロー](media-services-live-streaming-workflow.md)」シリーズの一部です。 シリーズの一部です。 

##概要

Microsoft Azure メディア サービスでは、高度暗号化標準 (AES) (128 ビット暗号化キーを使用) と PlayReady DRM を使用して動的に暗号化したコンテンツを配信できます。メディア サービスでは、承認されたクライアントにキーと PlayReady ライセンスを配信するためのサービスも提供しています。 

現時点では、HLS、MPEG DASH、スムーズ ストリーミングを暗号化できます。HDS 形式のストリーミングやプログレッシブ ダウンロードは暗号化できません。

メディア サービスでアセットを暗号化する場合は、[こちら](media-services-dotnet-create-contentkey.md)の説明に従って暗号化キー (**CommonEncryption** か **EnvelopeEncryption**) をアセットに関連付ける必要があります。また、このトピックでの説明に従って、キーの承認ポリシーを構成する必要があります。 

プレーヤーがストリームを要求すると、メディア サービスは指定されたキーを使用して、AES か PlayReady でコンテンツを動的に暗号化します。ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定した承認ポリシーを評価します。

メディア サービスでは、キーを要求するユーザーを承認する複数の方法がサポートされています。コンテンツ キー承認ポリシーには、**オープン制限**、**トークン制限**、または **IP 制限**のうち、1 つ以上の承認制限を指定できます。トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。Media Services では、**Simple Web Token** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) 形式と **JSON Web Token **(JWT) 形式のトークンがサポートされます。  

メディア サービスでは、Secure Token Services は提供されません。トークンを発行するには、カスタム STS を作成するか、Microsoft Azure ACS を活用できます。STS は、指定されたキーで署名されたトークンを作成し、トークン制限構成で指定したクレームを発行するよう構成する必要があります (この記事の説明を参照)。メディア サービスのキー配信サービスは、トークンが有効で、トークン内のクレームがコンテンツ キー向けに構成されたクレームと一致する場合、暗号化キーをクライアントに返します。

詳細については、次をご覧ください。 

[JWT トークンの承認](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Azure Media Services OWIN MVC ベースのアプリを Azure Active Directory と統合し、JWT クレームに基づいてコンテンツ キーの配信を制限する](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[Azure ACS を使用してトークンを発行する](http://mingfeiy.com/acs-with-key-services).

###いくつかの考慮事項が適用されます。

- 動的パッケージ機能と動的な暗号化を使用するには、少なくとも 1 つのストリーミング占有ユニットがあることを確認する必要があります。詳細については、「[メディア サービスの規模の設定方法](media-services-manage-origins.md#scale_streaming_endpoints)」をご覧ください。 
- アセットには、一連のアダプティブ ビットレート MP4 や アダプティブ ビットレート スムーズ ストリーミング ファイルが含まれている必要があります。詳細については、「[アセットをエンコードする](media-services-encode-asset.md)」をご覧ください。  
- **AssetCreationOptions.StorageEncrypted** オプションを使用して、アセットをアップロードしてエンコードします。
- 複数のコンテンツ キーで同じポリシー構成を必要とする場合は、1 つの承認ポリシーを作成して、複数のコンテンツ キーに利用することを強くお勧めします。
- キー配信サービスでは、ContentKeyAuthorizationPolicy とそれに関連するオブジェクト (ポリシーのオプションと制限) を 15 分間キャッシュします。ContentKeyAuthorizationPolicy を作成して、"Token" 制限を使用するように指定した場合に、"Token" 制限をテストしてから、ポリシーを "Open" 制限に更新すると、ポリシーが "Open" バージョンのポリシーに切り替わるまで、約 15 分かかります。
- アセットの配信ポリシーを追加または更新する場合は、既存のロケーターを削除し (存在する場合)、新しいロケーターを作成する必要があります。


##AES-128 動的暗号化 

###オープン制限

オープン制限とは、キーを要求するすべてのユーザーに、システムがキーを提供することを意味します。この制限は、テストに便利です。

次の例では、オープン承認ポリシーを作成し、それをコンテンツ キーに追加します。
	
	static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	{
	    // Create ContentKeyAuthorizationPolicy with Open restrictions 
	    // and create authorization policy             
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("Open Authorization Policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
	        new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	        new ContentKeyAuthorizationPolicyRestriction
	        {
	            Name = "HLS Open Authorization Policy",
	            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
	            Requirements = null // no requirements needed for HLS
	        };
	
	    restrictions.Add(restriction);
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	        "policy", 
	        ContentKeyDeliveryType.BaselineHttp, 
	        restrictions, 
	        "");
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	}


###トークン制限

このセクションでは、コンテンツ キー承認ポリシーを作成し、それをコンテンツ キーに関連付ける方法について説明します。承認ポリシーには、ユーザーがキーを受け取ることを承認されているかどうかを判断するために、承認要求が満たす必要がある内容について記載されています (トークンの署名に使用されたキーが「検証キー」リストに含まれているなど)。

トークン制限オプションを構成するには、XML を使用してトークンの承認要件を記述する必要があります。トークン制限の構成 XML は、次の XML スキーマに準拠する必要があります。

####<a id="schema"></a>トークン制限スキーマ
	
	<?xml version="1.0" encoding="utf-8"?>
	<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="http://www.w3.org/2001/XMLSchema">
	  <xs:complexType name="TokenClaim">
	    <xs:sequence>
	      <xs:element name="ClaimType" nillable="true" type="xs:string" />
	      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	  <xs:complexType name="TokenRestrictionTemplate">
	    <xs:sequence>
	      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
	      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
	      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
	  <xs:complexType name="ArrayOfTokenVerificationKey">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
	  <xs:complexType name="TokenVerificationKey">
	    <xs:sequence />
	  </xs:complexType>
	  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
	  <xs:complexType name="ArrayOfTokenClaim">
	    <xs:sequence>
	      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
	    </xs:sequence>
	  </xs:complexType>
	  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
	  <xs:complexType name="SymmetricVerificationKey">
	    <xs:complexContent mixed="false">
	      <xs:extension base="tns:TokenVerificationKey">
	        <xs:sequence>
	          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
	        </xs:sequence>
	      </xs:extension>
	    </xs:complexContent>
	  </xs:complexType>
	  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
	</xs:schema>

**トークン**制限ポリシーを構成する際は、プライマリ**検証キー**、**発行者**、**対象ユーザー**の各パラメーターを指定する必要があります。**プライマリ検証キー**には、トークンの署名に使用されたキーが含まれ、**発行者**は、トークンを発行するセキュリティ トークン サービスです。**対象ユーザー** (**スコープ**とも呼ばれる) には、トークンの目的、またはトークンがアクセスを承認するリソースが記述されます。Media Services キー配信サービスでは、トークン内のこれらの値がテンプレート内の値と一致することが検証されます。 

**Media Services SDK for .NET** を使用する場合、**TokenRestrictionTemplate** クラスを使用して制限トークンを生成できます。
次の例では、トークン制限を含む承認ポリシーを作成します。この例では、クライアントが署名キー (VerificationKey)、トークン発行者、必要なクレームを含むトークンを提示する必要があります。
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions =
	            new List<ContentKeyAuthorizationPolicyRestriction>();
	
	    ContentKeyAuthorizationPolicyRestriction restriction =
	            new ContentKeyAuthorizationPolicyRestriction
	            {
	                Name = "Token Authorization Policy",
	                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	                Requirements = tokenTemplateString
	            };
	
	    restrictions.Add(restriction);
	
	    //You could have multiple options 
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create(
	            "Token option for HLS",
	            ContentKeyDeliveryType.BaselineHttp,
	            restrictions,
	            null  // no key delivery data is needed for HLS
	            );
	
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKey.AuthorizationPolicyId = policy.Id;
	    IContentKey updatedKey = contentKey.UpdateAsync().Result;
	    Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
	    template.Audience = _sampleAudience;
	    template.Issuer = _sampleIssuer;
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	}

####<a id="test"></a>テスト トークン

キー承認ポリシーに使用されたトークン制限に基づいてテスト トークンを取得するには、次の処理を行います。
	
	// Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
	// back into a TokenRestrictionTemplate class instance.
	TokenRestrictionTemplate tokenTemplate =
	    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
	
	// Generate a test token based on the the data in the given TokenRestrictionTemplate.
	// Note, you need to pass the key id Guid because we specified 
	// TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
	Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
	
	//The GenerateTestToken method returns the token without the word "Bearer" in front
	//so you have to add it in front of the token string. 
	string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
	Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
	Console.WriteLine();


##PlayReady 動的暗号化 

メディア サービスにより、ユーザーが保護されたコンテンツを再生する際に、PlayReady DRM ランタイムに適用される権限と制限を構成できます。 

PlayReady を使用してコンテンツを保護する場合、承認ポリシーの指定の 1 つとして、[PlayReady ライセンス テンプレート](https://msdn.microsoft.com/library/azure/dn783459.aspx)を定義する XML 文字列を指定する必要があります。Media Services SDK for .NET では、**PlayReadyLicenseResponseTemplate** クラスと **PlayReadyLicenseTemplate** クラスを利用して、PlayReady ライセンス テンプレートを定義できます。 

###オープン制限
	
オープン制限とは、キーを要求するすべてのユーザーに、システムがキーを提供することを意味します。この制限は、テストに便利です。

次の例では、オープン承認ポリシーを作成し、それをコンテンツ キーに追加します。

	static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	{
	
	    // Create ContentKeyAuthorizationPolicy with Open restrictions 
	    // and create authorization policy          
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	    {
	        new ContentKeyAuthorizationPolicyRestriction 
	        { 
	            Name = "Open", 
	            KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
	            Requirements = null
	        }
	    };
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	
	
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key.
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	}

###トークン制限

トークン制限オプションを構成するには、XML を使用してトークンの承認要件を記述する必要があります。トークン制限の構成 XML は、[この](#schema) 。
	
	public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	{
	    string tokenTemplateString = GenerateTokenRequirements();
	
	    IContentKeyAuthorizationPolicy policy = _context.
	                            ContentKeyAuthorizationPolicies.
	                            CreateAsync("HLS token restricted authorization policy").Result;
	
	    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
	    {
	        new ContentKeyAuthorizationPolicyRestriction 
	        { 
	            Name = "Token Authorization Policy", 
	            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	            Requirements = tokenTemplateString, 
	        }
	    };
	
	    // Configure PlayReady license template.
	    string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	    IContentKeyAuthorizationPolicyOption policyOption =
	        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
	            ContentKeyDeliveryType.PlayReadyLicense,
	                restrictions, newLicenseTemplate);
	
	    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                ContentKeyAuthorizationPolicies.
	                CreateAsync("Deliver Common Content Key with no restrictions").
	                Result;
	            
	    policy.Options.Add(policyOption);
	
	    // Add ContentKeyAutorizationPolicy to ContentKey
	    contentKeyAuthorizationPolicy.Options.Add(policyOption);
	
	    // Associate the content key authorization policy with the content key
	    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	    contentKey = contentKey.UpdateAsync().Result;
	
	    return tokenTemplateString;
	}
	
	static private string GenerateTokenRequirements()
	{
	
	    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	    template.PrimaryVerificationKey = new SymmetricVerificationKey();
	    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
	    template.Audience = _sampleAudience;
	    template.Issuer = _sampleIssuer;
	
	
	    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	    return TokenRestrictionTemplateSerializer.Serialize(template);
	} 
	
	static private string ConfigurePlayReadyLicenseTemplate()
	{
	    // The following code configures PlayReady License Template using .NET classes
	    // and returns the XML string.
	             
	    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();
	    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
	
	    responseTemplate.LicenseTemplates.Add(licenseTemplate);
	
	    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
	}

キー承認ポリシーに使用されたトークン制限に基づいてテスト トークンを取得するには、[こちら]の(#test) 。 

##<a id="types"></a>ContentKeyAuthorizationPolicy を定義するときに使用される種類

###<a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    public enum ContentKeyDeliveryType
    {
        None = 0,
        PlayReadyLicense = 1,
        BaselineHttp = 2,
    }

###<a id="TokenType"></a>TokenType

    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }



##次のステップ
これでコンテンツ キーの承認ポリシーを構成できました。次は、「[How to configure asset delivery policy (アセット配信ポリシーを構成する方法)](media-services-dotnet-configure-asset-delivery-policy.md)」 に進みます。


<!--HONumber=52--> 