Android SDK の開発が継続中であるため、Eclipse にインストールされている Android SDK のバージョンが、コードのバージョンと一致しない可能性があります。このチュートリアルで参照している Android SDK は、記事の執筆時点で最新のバージョンであるバージョン 21 です。SDK の新しいリリースが登場するにつれてバージョン番号が大きくなる可能性があるので、入手可能な最新バージョンを使用することをお勧めします。

バージョンの不一致の場合に見られる現象は、次の 2 つです。

1. Eclipse コンソールの下部にあるウィンドウに注目します。"**ターゲット 'android-n' を解決できません**" という形式のエラー メッセージが表示される場合があります。

2. `import` ステートメントに基づく解決が必要なコード内の標準の Android オブジェクトによって、エラー メッセージが生成される場合があります。

このいずれかが発生した場合、Eclipse にインストールされている Android SDK のバージョンが、ダウンロードしたプロジェクトの SDK ターゲットと一致していない可能性があります。バージョンを確認するには、次の変更を加えます。


1. Eclipse で **[Window]** をクリックし、**[Android SDK Manager]** をクリックします。SDK プラットフォームの最新バージョンをまだインストールしていない場合は、これをクリックしてインストールします。バージョン番号をメモしておきます。

2. プロジェクト ファイル **AndroidManifest.xml** を開きます。**uses-sdk** 要素の中で、**targetSdkVersion** が、インストール済みの最新のバージョンに設定されていることを確認します。**uses-sdk** タグは、次のようになります。
 
	 	    <uses-sdk
	 	        android:minSdkVersion="8"
	 	        android:targetSdkVersion="21" />
	
3. Eclipse Package Explorer でプロジェクト ノードを右クリックし、**[Properties]** を右クリックして、左の列から **[Android]** を選択します。**[Project Build Target]** が、**[targetSdkVersion]** と同じ SDK バージョンに設定されていることを確認します。

<!---HONumber=August15_HO6-->