<properties 
	pageTitle="SQL Database クライアント プログラムのエラー メッセージ"
	description="エラーごとに、数値形式の ID とテキスト形式のメッセージが記載されています。もっとわかりやすい独自のエラー メッセージがあれば必要に応じてそちらを相互参照してください。"
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="genemi"/>


# SQL Database クライアント プログラムのエラー メッセージ


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


このトピックでは、いくつかのカテゴリのエラー メッセージを取り上げます。大半は Azure SQL Database に固有のカテゴリで、Microsoft SQL Server には当てはまりません。


いずれのエラーも、そのメッセージをクライアント プログラムでカスタマイズし、ユーザーに表示することができます。


> [AZURE.TIP][*一時的な障害*](#bkmk_connection_errors)に関する次のセクションは特に重要です。



<a id="bkmk_connection_errors" name="bkmk_connection_errors">&nbsp;</a>


## 一時的な障害や接続喪失などの一時的なエラー

以下の表は、接続喪失エラーなど、インターネットで Azure SQL Database を操作しているときに発生する可能性のある一時的なエラーの一覧です。


### 最も一般的な一時的な障害


一時的な障害が現れるとき、一般的に、クライアント プログラムから次のエラー メッセージの 1 つが表示されます。

- サーバー <Azure_instance> 上のデータベース <db_name> は現在使用できません。後で接続を再試行してください。問題が解決しない場合は、<session_id> のセッション トレース ID を控えてカスタマー サポートに問い合わせてください。

- サーバー <Azure_instance> 上のデータベース <db_name> は現在使用できません。後で接続を再試行してください。問題が解決しない場合は、<session_id> のセッション トレース ID を控えてカスタマー サポートに問い合わせてください。(Microsoft SQL Server、エラー: 40613)

- 既存の接続はリモート ホストに強制的に切断されました。

- System.Data.Entity.Core.EntityCommandExecutionException: コマンド定義を実行中にエラーが発生しました。詳細については、内部例外を参照してください。 ---> System.Data.SqlClient.SqlException: サーバーから結果を受信しているときに、トランスポート レベルのエラーが発生しました。 (プロバイダー: セッション プロバイダー、エラー: 19 - 物理的な接続は使用できません)

一時的なエラーが発生すると通常、クライアント プログラムの*再試行ロジック*が設計に従って実行され、同じ操作がやり直されます。再試行ロジックのコード例については、以下のページを参照してください。


- [SQL Database のクライアント開発とクイック スタート コード サンプル](sql-database-develop-quick-start-client-code-samples.md)

- [SQL Database における接続エラーと一過性の障害から復旧するための対策](sql-database-connectivity-issues.md)


### 一時障害のエラー番号


| エラー番号 | 重大度 | 説明 |
| ---: | ---: | :--- |
| 4060 | 16 | ログインで要求されたデータベース "%.&#x2a;ls" を開くことができません。ログインに失敗しました。 |
|40197|17|要求の処理中にサービスでエラーが発生しました。もう一度実行してください。エラー コード %d。<br/><br/>ソフトウェアやハードウェアのアップグレード、ハードウェアの障害、その他フェールオーバーに関する問題によってサービスがダウンしたときに、このエラーが発生します。障害の種類や発生したフェールオーバーに関する詳細な情報は、エラー 40197 のメッセージに埋め込まれたエラー コード (%d) から得られます。エラー 40197 のメッセージに埋め込まれるエラー コードとしては、40020、40143、40166、40540 などがあります。<br/><br/>SQL Database サーバーに再接続すると自動的に、正常なデータベースのコピーに接続されます。アプリケーションでエラー 40197 をキャッチし、メッセージに埋め込まれているエラー コード (%d) をログに記録してトラブルシューティングに備えたうえで、リソースが復旧して接続が再度確立されるまで SQL Database への再接続を試みる必要があります。|
|40501|20|サービスは現在ビジー状態です。10 秒後に要求を再試行してください。インシデント ID: %ls。コード: %d.<br/><br/>*注記:* 一般的な情報は、「<br/>• [Azure SQL Database のリソース制限](sql-database-resource-limits.md)」を参照してください。
|40613|17|サーバー '%.&#x2a;ls' のデータベース '%.&#x2a;ls' は現在使用できません。後で接続を再試行してください。問題が解決しない場合は、'%.&#x2a;ls' のセッション トレース ID を控えてカスタマー サポートに問い合わせてください。|
|49918|16|要求を処理できません。要求を処理するためのリソースが不足しています。<br/><br/>サービスは現在ビジー状態です。後で要求を再試行してください。 |
|49919|16|要求を処理、作成、更新できません。サブスクリプション "%ld" に対して実行中の作成または更新の操作が多すぎます。<br/><br/>サービスは、サブスクリプションまたはサーバーに対する複数の作成要求または更新要求の処理でビジー状態です。現在、要求はリソースの最適化のためにブロックされています。クエリ [sys.dm\_operation\_stats](https://msdn.microsoft.com/library/dn270022.aspx) を実行して保留中の操作を確認します。保留中の作成要求または更新要求が完了するまで待つか、いずれかの保留中の要求を削除して後で要求を再試行します。 |
|49920|16|要求を処理できません。サブスクリプション "%ld" に対して実行中の操作が多すぎます。<br/><br/>サービスは、このサブスクリプションに対する複数の要求の処理でビジー状態です。現在、要求はリソースの最適化のためにブロックされています。[sys.dm\_operation\_stats](https://msdn.microsoft.com/library/dn270022.aspx) を実行して操作の状態を確認します。保留中の要求が完了するまで待つか、いずれかの保留中の要求を削除して後で要求を再試行します。 |

**注:** フェデレーション エラーである 10053 と 10054 も再試行ロジックに含めることを検討してください。


<a id="bkmk_b_database_copy_errors" name="bkmk_b_database_copy_errors">&nbsp;</a>

## データベース コピー エラー


以下の表は、Azure SQL Database でデータベースをコピーするときに発生する可能性のある各種エラーの一覧です。詳細については、「[Azure SQL Database のコピー](sql-database-copy.md)」を参照してください。


|エラー番号|重大度|説明|
|---:|---:|:---|
|40635|16|IP アドレス '%.&#x2a;ls' のクライアントは一時的に無効化されています。|
|40637|16|データベース コピーの作成は現在無効です。|
|40561|16|データベースのコピーに失敗しました。ソースまたはターゲットのデータベースが存在しません。|
|40562|16|データベースのコピーに失敗しました。ソース データベースは削除されました。|
|40563|16|データベースのコピーに失敗しました。ターゲット データベースは削除されました。|
|40564|16|データベースのコピーに失敗しました。内部エラーが発生しました。ターゲット データベースを削除してやり直してください。|
|40565|16|データベースのコピーに失敗しました。同じソースから複数のデータベース コピーを同時に実行することはできません。ターゲット データベースを削除してやり直してください。|
|40566|16|データベースのコピーに失敗しました。内部エラーが発生しました。ターゲット データベースを削除してやり直してください。|
|40567|16|データベースのコピーに失敗しました。内部エラーが発生しました。ターゲット データベースを削除してやり直してください。|
|40568|16|データベースのコピーに失敗しました。ソース データベースが使用できなくなりました。ターゲット データベースを削除してやり直してください。|
|40569|16|データベースのコピーに失敗しました。ターゲット データベースが使用できなくなりました。ターゲット データベースを削除してやり直してください。|
|40570|16|データベースのコピーに失敗しました。内部エラーが発生しました。ターゲット データベースを削除してやり直してください。|
|40571|16|データベースのコピーに失敗しました。内部エラーが発生しました。ターゲット データベースを削除してやり直してください。|


<a id="bkmk_c_resource_gov_errors" name="bkmk_c_resource_gov_errors">&nbsp;</a>

## リソース ガバナンス エラー


以下の表は、Azure SQL Database を使った作業中、リソースの過剰使用によって発生するエラーの一覧です。次に例を示します。


- トランザクションを長時間開いたままにした。
- トランザクションで保持しているロックが多すぎる。
- プログラムのメモリ使用量が多すぎる。
- プログラムで使用している `TempDb` の領域が多すぎる。


**ヒント:** 次のリンク先には、このセクションで取り上げた大半またはすべてのエラーに共通する情報が記載されています。


- [Azure SQL Database のリソース制限](sql-database-resource-limits.md)


|エラー番号|重大度|説明|
|---:|---:|:---|
|10928|20|リソース ID: %d。データベースの %s 制限の %d に達しました。詳細については、[http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) を参照してください。<br/><br/>リソース ID は、制限に達したリソースを示します。ワーカー スレッドの場合、リソース ID = 1 となります。セッションの場合、リソース ID = 2 となります。<br/><br/>*注:* このエラーの詳細および解決方法については、「<br/>• [Azure SQL Database のリソース制限](sql-database-resource-limits.md)」を参照してください。 |
|10929|20|リソース ID: %d。%S の最低限保証は %d、最大値は %d 、データベースの現在の使用状況は %d です。ただし、サーバーは現在ビジー状態であり、このデータベースの %d を超える要求をサポートできません。詳細については、[http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637) を参照してください。それ以外の場合は、後でもう一度お試しください。<br/><br/>リソース ID は、制限に達したリソースを示します。ワーカー スレッドの場合、リソース ID = 1 となります。セッションの場合、リソース ID = 2 となります。<br/><br/>*注:* このエラーの詳細および解決方法については、「<br/>• [Azure SQL Database のリソース制限](sql-database-resource-limits.md)」を参照してください。|
|40544|20|データベースのサイズ クォータに達しました。データをパーティション分割するか、データを削除するか、インデックスを削除してください。その他の解決方法についてはドキュメントを参照してください。|
|40549|16|トランザクションが長時間実行されているため、セッションを終了しました。トランザクションを短くしてください。|
|40550|16|取得したロックの数が多すぎるため、セッションを終了しました。1 つのトランザクションで読み取る行または変更する行の数を減らしてください。|
|40551|16|`TEMPDB` の使用領域が多すぎるため、セッションを終了しました。クエリを変更して一時テーブルの使用領域を減らしてください。<br/><br/>*ヒント:* 一時オブジェクトを使用している場合、セッションで不要となった一時オブジェクトを削除して `TEMPDB` データベースの領域を節約してください。|
|40552|16|トランザクション ログの使用領域が多すぎるため、セッションを終了しました。1 回のトランザクションで変更する行を減らしてください。<br/><br/>*ヒント:* `bcp.exe` ユーティリティまたは `System.Data.SqlClient.SqlBulkCopy` クラスを使用して一括挿入を実行する場合は、1 回のトランザクションでサーバーにコピーされる行数を `-b batchsize` (または `BatchSize`) オプションで制限してください。`ALTER INDEX` ステートメントでインデックスを再構築する場合は、`REBUILD WITH ONLINE = ON` オプションの使用を検討してください。|
|40553|16|メモリの使用量が多すぎるため、セッションを終了しました。クエリを変更して、処理する行を減らしてください。<br/><br/>*ヒント:* Transact-SQL コード内の `ORDER BY` 操作と `GROUP BY` 操作の数を減らすことで、クエリのメモリ要件を軽減することができます。|


リソースのガバナンスおよび関連するエラーの詳細については、以下のページを参照してください。


- [Azure SQL Database のリソース制限](sql-database-resource-limits.md)。


<a id="bkmk_d_federation_errors" name="bkmk_d_federation_errors">&nbsp;</a>

## フェデレーション エラー


以下の表は、フェデレーションの処理中に発生する可能性のあるエラーの一覧です。


> [AZURE.IMPORTANT]現在のフェデレーションの実装は、Web 階層および Business サービス階層と共に廃止される予定です。バージョン V12 の Azure SQL Database では、Web 階層と Business サービス階層がサポートされません。
> 
> Elastic Scale 機能は、シャーディング アプリケーションを最小限の手間で作成することを目的に設計されています。
> 
> Elastic Scale の詳細については、「[Azure SQL Database Elastic Scale のトピック](sql-database-elastic-scale-documentation-map.md)」をご覧ください。スケーラビリティ、柔軟性、パフォーマンスを最大限に高めるために、カスタム シャーディング ソリューションのデプロイを検討してください。カスタム シャーディングの詳細については、「[Elastic Database 機能の概要](sql-database-elastic-scale-introduction.md)」をご覧ください。


|エラー番号|重大度|説明|対応策|
|---:|---:|:---|:---|
|266|16|<statement> ステートメントは、複数のステートメントを含むトランザクション内では許可されません。|その接続で `@@trancount` が 0 であることを確認してから、ステートメントを実行してください。|
|2072|16|データベース '%.&#x2a;ls' が存在しません。|`sys.databases` でデータベースの状態を確認してから `USE FEDERATION` を実行してください。|
|2209|16|%s‘%ls’ 付近に構文エラーがあります。|`FEDERATED ON` を使用できるのは、フェデレーションのメンバーにテーブルを作成するときだけです。|
|2714|16|データベースに ‘%.&#x2a;ls’ という名前のオブジェクトが既に存在します。|フェデレーション名は既に存在します。|
|10054、10053|20|サーバーから結果を受信しているときに、トランスポート レベルのエラーが発生しました。確立された接続がホスト コンピューターのソウトウェアによって中止されました。|アプリケーションに再試行ロジックを実装してください。|
|40530|15|<statement> がバッチ内の唯一のステートメントである必要があります。|バッチ内に他のステートメントがないことを確認します。|
|40604|16|サーバーのクォータを超えるため、`CREATE DATABASE` できませんでした。|サーバーのデータベース カウント クォータを拡張します。|
|45000|16|<statement> 操作に失敗しました。指定されたフェデレーション名 <federation_name> は無効です。|Federation\_name がフェデレーション名の規則に準拠していないか、有効な識別子ではありません。|
|45001|16|<statement> 操作に失敗しました。指定されたフェデレーション名は存在しません。|フェデレーション名が存在しません。|
|45002|16|<statement> 操作に失敗しました。指定されたフェデレーション キー名 <distribution_name> は無効です。|フェデレーション キーが存在しないか、無効です。|
|45004|16|<statement> 操作に失敗しました。フェデレーション キー <distribution_name> とフェデレーション <federation_name> に対して指定された値が無効です。|`USE FEDERATION`: フェデレーション キーのデータ型のドメインに存在する境界値 (つまり NULL 以外の値) を使用してください。<br/><br/>`ALTER FEDERATION SPLIT`: まだ分割点になっていないフェデレーション キーのドメインに存在する有効な値を使用してください。<br/><br/>`ALTER FEDERATION DROP`: 既に分割点になっているフェデレーション キーのドメインに存在する有効な値を使用してください。|
|45005|16|フェデレーション <federation_name> とメンバー (ID: <member_id>) で別のフェデレーション操作が実行されている間は、<statement> を実行できません。|同時実行操作の終了を待ちます。|
|45006|16|<statement> 操作に失敗しました。フェデレーション テーブルを参照する参照テーブルの外部キー リレーションシップをフェデレーション メンバーに含めることはできません。|サポートされていません。|
|45007|16|<statement> 操作に失敗しました。フェデレーション テーブル間の外部キー リレーションシップは、フェデレーション キー列を含んでいる必要があります。|サポートされていません|
|45008|16|<statement> 操作に失敗しました。フェデレーション キーのデータ型が列のデータ型と一致しません|サポートされていません。|
|45009|16|<statement> 操作に失敗しました。フィルタリング接続では、この操作はサポートされません。|サポートされていません。|
|45010|16|<statement> 操作に失敗しました。フェデレーション キーを更新できません。|サポートされていません。|
|45011|16|<statement> 操作に失敗しました。フェデレーション キーのスキーマを更新できません。|サポートされていません。|
|45012|16|フェデレーション キーに指定された値が無効です。|その接続でアドレス指定される範囲内の値を指定する必要があります。<br/><br/>フィルタリングされている場合、指定されたフェデレーション キーの値となります。<br/><br/>フィルタリングされていない場合、フェデレーション メンバーがカバーする範囲となります。|
|45013|16|SID は既に別のユーザー名で存在します。|フェデレーション メンバーでは、ユーザーの SID が、フェデレーション ルートの同じユーザー アカウントの SID からコピーされます。特定の状況で、この SID が既に使用されている場合があります。|
|45014|16|%ls は、%ls ではサポートされません。|サポートされていない操作です。|
|45022|16|<statement> 操作に失敗しました。フェデレーション キー <distribution_name> とフェデレーション <federation_name> には、指定された境界値が既に存在します。|既に境界値になっている値を指定してください。|
|45023|16|<statement> 操作に失敗しました。フェデレーション キー <distribution_name> とフェデレーション <federation_name> には、指定された境界値が存在しません。|まだ境界値になっていない値を指定してください。|


<a id="bkmk_e_general_errors" name="bkmk_e_general_errors">&nbsp;</a>

## 一般エラー


以下の表は、上に挙げたいずれのカテゴリにも該当しない一般エラーの全一覧です。


|エラー番号|重大度|説明|
|---:|---:|:---|
|15006|16|<AdministratorLogin> は無効な文字を含んでいるため、有効な名前ではありません。|
|18452|14|ログインできませんでした。このログインは信頼されていないドメインからのログインなので、Windows 認証では使用できません。%.&#x2a;ls (Windows ログインは、このバージョンの SQL Server ではサポートされていません。)|
|18456|14|ユーザー '%.&#x2a;ls' はログインできませんでした。%.&#x2a;ls%.&#x2a;ls("ユーザー "%.&#x2a;ls" はログインできませんでした。パスワードを変更できませんでした。このバージョンの SQL Server ではログイン時にパスワードを変更することはできません。)|
|18470|14|ユーザー '%.&#x2a;ls' はログインできませんでした。理由: このアカウントは無効です。%.&#x2a;ls|
|40014|16|同じトランザクションで複数のデータベースを使用することはできません。|
|40054|16|クラスター化インデックスのないテーブルは、このバージョンの SQL Server ではサポートされていません。クラスター化インデックスを作成してからやり直してください。|
|40133|15|この操作は、このバージョンの SQL Server ではサポートされていません。|
|40506|16|指定された SID は、このバージョンの SQL Server では無効です。|
|40507|16|このバージョンの SQL Server では、'%.&#x2a;ls' の呼び出しでパラメーターを使用することはできません。|
|40508|16|USE ステートメントを使用してデータベースを切り替えることはできません。別のデータベースに接続するには新しい接続を使用してください。|
|40510|16|ステートメント '%.&#x2a;ls' は、このバージョンの SQL Server ではサポートされていません。|
|40511|16|組み込み関数 '%.&#x2a;ls' は、このバージョンの SQL Server ではサポートされていません。|
|40512|16|推奨されない機能 '%ls' は、このバージョンの SQL Server ではサポートされていません。|
|40513|16|サーバー変数 '%.&#x2a;ls' は、このバージョンの SQL Server ではサポートされていません。|
|40514|16|'%ls' は、このバージョンの SQL Server ではサポートされていません。|
|40515|16|このバージョンの SQL Server では、'%.&#x2a;ls' でデータベース名やサーバー名を参照することはできません。|
|40516|16|グローバル一時オブジェクトは、このバージョンの SQL Server ではサポートされていません。|
|40517|16|キーワードまたはステートメントのオプション '%.&#x2a;ls' は、このバージョンの SQL Server ではサポートされていません。|
|40518|16|DBCC コマンド '%.&#x2a;ls' は、このバージョンの SQL Server ではサポートされていません。|
|40520|16|セキュリティ保護可能なクラス '%S\_MSG' は、このバージョンの SQL Server ではサポートされていません。|
|40521|16|セキュリティ保護可能なクラス '%S\_MSG' は、このバージョンの SQL Server ではサーバー スコープでサポートされていません。|
|40522|16|データベース プリンシパル '%.&#x2a;ls' の型は、このバージョンの SQL Server ではサポートされていません。|
|40523|16|このバージョンの SQL Server では、ユーザー '%.&#x2a;ls' を暗黙的に作成することはできません。ユーザーを明示的に作成してから使用してください。|
|40524|16|データ型 '%.&#x2a;ls' は、このバージョンの SQL Server ではサポートされていません。|
|40525|16|WITH '%.ls' は、このバージョンの SQL Server ではサポートされていません。|
|40526|16|行セット プロバイダー '%.&#x2a;ls' は、このバージョンの SQL Server ではサポートされていません。|
|40527|16|リンク サーバーは、このバージョンの SQL Server ではサポートされていません。|
|40528|16|このバージョンの SQL Server では、ユーザーを資格情報、非対称キー、または Windows ログインにマップすることはできません。|
|40529|16|このバージョンの SQL Server では、組み込み関数 '%.&#x2a;ls' を権限借用コンテキストで使用することはできません。|
|40532|11|ログインにより要求されたサーバー "%.&#x2a;ls" を開けません。ログインに失敗しました。|
|40553|16|メモリの使用量が多すぎるため、セッションを終了しました。クエリを変更して、処理する行を減らしてください。<br/><br/>*注:* Transact-SQL コード内の `ORDER BY` 操作と `GROUP BY` 操作の数を減らすことで、クエリのメモリ要件を軽減することができます。|
|40604|16|サーバーのクォータを超えるため、CREATE/ALTER DATABASE できませんでした。|
|40606|16|データベースのアタッチは、このバージョンの SQL Server ではサポートされていません。|
|40607|16|Windows ログインは、このバージョンの SQL Server ではサポートされていません。|
|40611|16|サーバーで定義できるファイアウォール規則は最大 128 個です。|
|40614|16|ファイアウォールの規則の開始 IP アドレスに終了 IP アドレスを超える値を指定することはできません。|
|40615|16|ログインにより要求されたサーバー '{0}' を開けません。IP アドレス '{1}' のクライアントはこのサーバーへのアクセスが許可されていません。アクセスできるようにするには、SQL Database ポータルを使用するか、master データベースで run sp\_set\_firewall\_rule を実行して、この IP アドレスまたはアドレス範囲のファイアウォールの規則を作成します。この変更が有効になるまで最大で 5 分かかる場合があります。|
|40617|16|<rule name> で始まるファイアウォール規則の名前は長すぎます。最大長は 128 です。|
|40618|16|ファイアウォール規則の名前を空にすることはできません。|
|40620|16|ユーザー "%.&#x2a;ls" はログインできませんでした。パスワードを変更できませんでした。このバージョンの SQL Server ではログイン時にパスワードを変更することはできません。|
|40627|20|サーバー '{0}' およびデータベース '{1}' で操作を実行中です。数分待ってから、再試行してください。|
|40630|16|パスワードの検証に失敗しました。パスワードの文字数が不足していて、ポリシーで指定された基準を満たしていません。|
|40631|16|指定したパスワードが長すぎます。パスワードは 128 文字以下で指定してください。|
|40632|16|パスワードの検証に失敗しました。パスワードは、複雑さが不十分なため、ポリシーの要件を満たしていません。|
|40636|16|予約されているデータベース名 '%.&#x2a;ls' をこの操作で使用することはできません。|
|40638|16|サブスクリプション ID <subscription-id> は無効です。サブスクリプションが存在しません。|
|40639|16|要求がスキーマに準拠していません: <schema error>。|
|40640|20|サーバーで予期しない例外が発生しました。|
|40641|16|指定された場所が無効です。|
|40642|17|サーバーは現在ビジー状態です。後でもう一度やり直してください。|
|40643|16|指定された x-ms-version ヘッダー値が無効です。|
|40644|14|指定されたサブスクリプションへのアクセスの承認に失敗しました。|
|40645|16|サーバー名 <servername> を空や null にすることはできません。小文字アルファベットの 'a' ～ 'z'、数字の 0 ～ 9、およびハイフンでのみ構成されている必要があります。名前の先頭や末尾にハイフンを使用することはできません。|
|40646|16|サブスクリプションの ID を空にすることはできません。|
|40647|16|サブスクリプション subscription-id にサーバー servername がありません。|
|40648|17|多くの要求が行われています。後で再試行してください。|
|40649|16|無効なコンテンツ タイプが指定されました。サポートされているのは application/xml のみです。|
|40650|16|サブスクリプション <subscription-id> が存在しないか、操作の準備が整っていません。|
|40651|16|サブスクリプション <subscription-id> が無効であるため、サーバーを作成できませんでした。|
|40652|16|サーバーを移動または作成できません。サブスクリプション <subscription-id> がサーバーのクォータを超えています。|
|40671|17|ゲートウェイと管理サービスの間で通信エラーが発生しました。後で再試行してください。|
|45168|16|SQL Azure システムに負荷がかかっているため、サーバー 1 台あたりの同時実行 DB CRUD 操作 (データベースの作成など) に上限を設定しています。エラー メッセージに示されているサーバーは、最大同時接続数を超過しました。後でもう一度やり直してください。|
|45169|16|SQL Azure システムに負荷がかかっているため、サブスクリプション 1 つあたりの同時実行サーバー CRUD 操作 (サーバーの作成など) に上限を設定しています。エラー メッセージに示されているサブスクリプションは、最大同時接続数を超過したため、要求が拒否されました。後でもう一度やり直してください。|


## 関連リンク

- [Azure SQL Database の一般的な制限事項とガイドライン](sql-database-general-limitations.md)
- [Azure SQL Database のリソース制限](sql-database-resource-limits.md)

<!---HONumber=Nov15_HO2-->