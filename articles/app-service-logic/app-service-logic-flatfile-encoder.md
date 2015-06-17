<properties 
   pageTitle="BizTalk Flat File Encoder" 
   description="BizTalk Flat File Encoder" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="rajram"/>

# BizTalk Flat File Encoder

BizTalk Flat File Encode Decode コネクタは、アプリでのフラット ファイル データ (excel、csv など) と XML データの相互運用を支援します。フラット ファイル インスタンスを XML に、またはその逆方向に変換できます。

##BizTalk Flat File Encoder の使用
1. BizTalk Flat File Encoder を使用するには、まず、BizTalk Flat File Encoder API アプリのインスタンスを作成する必要があります。これは、ロジック アプリを作成するときにインラインで、または Azure Marketplace から BizTalk Flat File Encoder API アプリを選択することによって、行うことができます。

###BizTalk Flat File Encoder の構成
BizTalk Flat File Encoder は、構成の一部としてスキーマを受け取ります。ユーザーは、Azure ポータルから直接 API アプリを起動して、またはデザイナー画面で API アプリをダブルクリックして、API アプリ構成ブレードを起動できます。

![BizTalk Flat File Encoder の構成][1]

API アプリ ブレードでは、*[スキーマ]* 部分をクリックしてスキーマを構成できます。

![BizTalk Flat File Encoder のスキーマ部分][2]

ユーザーは、ディスクからスキーマをアップロードするか、フラット ファイル インスタンスまたは JSON インスタンスからスキーマを生成することができます。

![BizTalk Flat File Encoder のスキーマ部分][3]


###デザイン画面での BizTalk Flat File Encoder の使用
構成が済むと、ユーザーは *->* をクリックしてアクションの一覧からアクションを選択できます。

![BizTalk Flat File Encoder のアクション一覧][4]

####フラット ファイルを XML に

![BizTalk Flat File Encoder のアクション一覧][5]

<table>
	<tr>
		<th>パラメーター</th>
		<th>型</th>
		<th>パラメーターの説明</th>
	</tr>
	<tr>
		<td>フラット ファイル</td>
		<td>string</td>
		<td>入力フラット ファイルの内容</td>
	</tr>
	<tr>
		<td>スキーマ名</td>
		<td>string</td>
		<td>入力フラット ファイルを表すスキーマの名前</td>
	</tr>
	<tr>
		<td>ルート名</td>
		<td>string</td>
		<td>フラット ファイル スキーマのルート ノード名</td>
	</tr>
</table>


アクションは文字列として出力を返します (出力 XML)。出力 XML には、入力フラット ファイルの内容の XML 表現が含まれています。

####XML をフラット ファイルに

![BizTalk Flat File Encoder のアクション一覧][6]

<table>
	<tr>
		<th>パラメーター</th>
		<th>型</th>
		<th>パラメーターの説明</th>
	</tr>
	<tr>
		<td>入力 XML</td>
		<td>string</td>
		<td>入力 XML の内容</td>
	</tr>
</table>

アクションは文字列として出力を返します (フラット ファイル)。出力には、入力 XML の内容のフラット ファイル表現が含まれています。

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG

<!---HONumber=58--> 