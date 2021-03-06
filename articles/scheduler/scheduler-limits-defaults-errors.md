<properties
 pageTitle="Scheduler の制限、既定値、エラー コード"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="07/28/2015"
 ms.author="krisragh"/>

# Scheduler の制限、既定値、エラー コード

## Scheduler のクォータ、制限、既定値、調整

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## x-ms-request-id ヘッダー

Scheduler サービスに対するすべての要求は、**x-ms-request-id** という名前の応答ヘッダーを返します。このヘッダーには、要求を一意に識別する非透過の値が含まれています。

要求の形式が正しいにもかかわらず要求が常に失敗する場合は、この値を使用して Microsoft にエラーを報告することができます。このとき、x-ms-request-id の値、要求が行われたおおよその時間、サブスクリプション、クラウド サービス、ジョブ コレクション、ジョブの識別子のほかに、要求で試みた操作の種類もレポートに含めてください。

## Scheduler の状態コードとエラー コード

Azure Scheduler REST API は、標準の HTTP 状態コードに加えて、拡張エラー コードとエラー メッセージを返します。拡張コードは、標準 HTTP 状態コードを置き換えるものではなく、標準 HTTP 状態コードと組み合わせて使用できる実用的な追加情報を提供します。

たとえば、HTTP 404 エラーはさまざまな原因で発生するため、拡張メッセージに追加情報を含めることで問題の解決に役立ちます。REST API から返される標準 HTTP コードの詳細については、「[サービス管理のステータス コードとエラー コード](https://msdn.microsoft.com/library/windowsazure/ee460801.aspx)」を参照してください。サービス管理 API の REST API 操作は、[HTTP/1.1 の状態コードの定義](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)に関するページに示されている標準の HTTP 状態コードを返します。次の表に、サービスから返される可能性のある一般的なエラーについて説明します。

|エラー コード|HTTP 状態コード|ユーザー メッセージ|
|----|----|----|
|MissingOrIncorrectVersionHeader|正しくない要求 (400)|バージョン管理ヘッダーが指定されていないか、正しく指定されていませんでした。|
|InvalidXmlRequest|正しくない要求 (400)|要求本文の XML が無効または正しく指定されていませんでした。|
|MissingOrInvalidRequiredQueryParameter|正しくない要求 (400)|この要求に対して必要なクエリ パラメーターが指定されていないか、正しく指定されていませんでした。|
|InvalidHttpVerb|正しくない要求 (400)|指定した HTTP 動詞はサーバーで認識されなかったか、このリソースに対して有効ではありません。|
|AuthenticationFailed|禁止 (403)|サーバーは要求を認証できませんでした。証明書が有効で、このサブスクリプションと関連付けられていることを確認してください。|
|ResourceNotFound|見つかりません (404)|指定したリソースは存在しません。|
|InternalError|内部サーバー エラー (500)|サーバーで内部エラーが発生しました。要求を再試行してください。|
|OperationTimedOut|内部サーバー エラー (500)|許可された時間内に操作を完了できません。|
|ServerBusy|サービスを利用できません (503)|サーバー (または内部コンポーネント) は現在要求の受信に使用できません。要求を再試行してください。|
|SubscriptionDisabled|禁止 (403)|サブスクリプションは無効の状態にあります。|
|BadRequest|正しくない要求 (400)|パラメーターが正しくありません。|
|ConflictError|競合 (409)|操作の完了を妨げる競合が発生しました。|
|TemporaryRedirect|一時リダイレクト (307)|指定したオブジェクトが見つかりません。オブジェクトの新しい場所の一時的 URI は応答の Location フィールドから取得できます。この新しい URI で元の要求を繰り返すことができます。|

API 操作は、管理サービスで定義されている追加のエラー情報も返す可能性があります。この追加のエラー情報は、応答本文で返されます。エラー応答の本文は、次に示す基本形式に従います。

		<?xml version="1.0" encoding="utf-8"?>  
		<Error>  
			<Code>string-code</Code>  
			<Message>detailed-error-message</Message>  
		</Error>  

## 関連項目

 [Scheduler Concepts, Terminology, and Entity Hierarchy (Scheduler の概念、用語集、エンティティ階層構造)](scheduler-concepts-terms.md)

 [管理ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)

 [Plans and Billing in Azure Scheduler (Azure Scheduler のプランと課金)](scheduler-plans-billing.md)

 [How to Build Complex Schedules and Advanced Recurrence with Azure Scheduler (Azure Scheduler で複雑なスケジュールと高度な定期実行を構築する方法)](scheduler-advanced-complexity.md)

 [Scheduler REST API リファレンス](https://msdn.microsoft.com/library/dn528946)

 [Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Scheduler 送信認証](scheduler-outbound-authentication.md)

<!---HONumber=Oct15_HO3-->