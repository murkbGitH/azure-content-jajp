## コピー中の再現性

他のデータ ストアから Azure SQL/SQL Server にデータをコピーする場合、意図しない結果を回避するために、再現性の維持を考慮する必要があります。

Azure SQL Database にデータをコピーすると、コピー アクティビティの既定では、シンク テーブルにデータ セットが付加されます。たとえば、2 つのレコードを含む CSV (コンマ区切り値データ) ファイル ソースのデータを Azure SQL/SQL Server データベースにコピーすると、テーブルは次のようになります。
	
	ID	Product		Quantity	ModifiedDate
	...	...			...			...
	6	Flat Washer	3			2015-05-01 00:00:00
	7 	Down Tube	2			2015-05-01 00:00:00

たとえば、ソース ファイルにエラーが見つかり、ソース ファイルで Down Tube の数を 2 から 4 に更新したとします。その期間のデータ スライスを再実行すると、Azure SQL/SQL Server データベースに 2 つの新しいレコードが付加されます。以下の例では、テーブルには主キーの制約がある列がないと想定しています。
	
	ID	Product		Quantity	ModifiedDate
	...	...			...			...
	6	Flat Washer	3			2015-05-01 00:00:00
	7 	Down Tube	2			2015-05-01 00:00:00
	6	Flat Washer	3			2015-05-01 00:00:00
	7 	Down Tube	4			2015-05-01 00:00:00

この問題を回避するために、以下に示す 2 つのメカニズムのいずれかを使用して、UPSERT セマンティクスを指定する必要があります。

> [AZURE.NOTE]Azure Data Factory でも、再試行ポリシーで指定された回数、スライスを自動的に再実行することができます。

### メカニズム 1

スライスの実行時にまずクリーンアップ アクションを実行するには、**sqlWriterCleanupScript** プロパティを利用できます。

	"sink":  
	{ 
	  "type": "SqlSink", 
	  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate = \'{0:yyyy-MM-dd HH:mm}\'', SliceStart)"
	}

クリーンアップ スクリプトは、指定したスライスのコピー中に最初に実行されます。このとき、SQL テーブルからそのスライスに対応するデータが削除されます。次に、コピー アクティビティで、SQL テーブルにデータが挿入されます。

次にスライスを実行すると、目的の数に更新されます。
	
	ID	Product		Quantity	ModifiedDate
	...	...			...			...
	6	Flat Washer	3			2015-05-01 00:00:00
	7 	Down Tube	4			2015-05-01 00:00:00

たとえば、Flat Washer レコードを元の csv から削除するとします。次にスライスを再実行すると、以下の結果が生成されます。
	
	ID	Product		Quantity	ModifiedDate
	...	...			...			...
	8 	Down Tube	4			2015-05-01 00:00:00

新しい処理は何も実行されていません。コピー アクティビティでクリーンアップ スクリプトが実行され、そのスライスに対応するデータが削除されました。次に、csv から入力が読み込まれ (今度は 1 レコードのみが含まれる csv です)、テーブルに挿入されます。

### メカニズム 2

再現性を実現するためのもう 1 つのメカニズムは、対象のテーブルに専用の列 (**sliceIdentifierColumnName**) を用意する方法です。この列は、Azure Data Factory がソースと対象の同期状態を保つために使用されます。この手法は、対象の SQL テーブル スキーマを変更または定義する際に柔軟性がある場合に機能します。

この列は、再現性のために Azure Data Factory で使用されます。このプロセスで、Azure Data Factory はテーブルのスキーマを変更しません。この手法を使用する方法:

1.	対象 SQL テーブルで binary (32) 型の列を定義します。この列に制約は設定しないでください。この例では、この列に 'ColumnForADFuseOnly' という名前を付けます。
2.	コピー アクティビティで、次のように使用します。

		"sink":  
		{ 
		  "type": "SqlSink", 
		  "sliceIdentifierColumnName": "ColumnForADFuseOnly"
		}

ソースと対象の同期状態を維持するために必要なので、Azure Data Factory でこの列が設定されます。この列の値は、この状況以外でユーザーから使用されることはありません。

メカニズム 1 と同様に、まず指定したスライスのデータがコピー アクティビティで対象 SQL テーブルからクリーンアップされます。通常、次にコピー アクティビティが実行され、そのスライスのソースのデータが対象に挿入されます。

<!---HONumber=August15_HO6-->