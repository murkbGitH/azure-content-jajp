## 四角形のデータセットの構造定義を指定する
データセット JSON の structure セクションは、(行と列がある) 四角形のテーブルの**省略可能**なセクションです。テーブルの列のコレクションが含まれています。structure セクションは、型変換のために型情報を提供したり、列マッピングを実行したりするために使用されます。次のセクションでは、これらの機能の詳細について説明します。

各列には次のプロパティが含まれます。

| プロパティ | 説明 | 必須 |
| -------- | ----------- | -------- |
| name | 列の名前です。 | あり |
| type | 列のデータ型です。型情報を指定する必要がある場合の詳細については、以下の型変換のセクションを参照してください。 | いいえ |
| culture | 型を指定するときに使用される .NET ベースのカルチャ。.NET 型の Datetime または Datetimeoffset です。既定値は “ja-jp” です。 | いいえ |
| BlobSink の format | 型を指定するときに使用される書式指定文字列。.NET 型の Datetime または Datetimeoffset です。 | いいえ |

次に、userid、name、および lastlogindate という 3 つの列があるテーブルの structure セクション JSON の例を示します。

    "structure": 
    [
        { "name": "userid"},
        { "name": "name"},
        { "name": "lastlogindate"}
    ],

"structure" 情報を含める場合と、**structure** セクションに含める内容については、次のガイドラインに従ってください。

1.	データ自体と共にデータ スキーマと型情報を格納する**構造化データ ソースの場合** (SQL Server、Oracle、Azure テーブルなどのソース)、"structure" セクションを指定する必要があるのは、特定のソース列をシンク内の特定の列とマップし、それらの列名が同じではない場合のみです (詳細については、後述する列マッピングのセクションを参照してください)。 

	前述のように、"structure" セクションの型情報は省略可能です。構造化ソースの場合、型情報はデータ ストアのデータセット定義の一部として既に使用可能なので、"structure" セクションを含める場合に型情報を含めないでください。
2. **読み取りデータ ソースのスキーマの場合 (具体的には Azure BLOB)**、データと共にスキーマや型情報を保存せずに、データを保存することができます。このような種類のデータ ソースでは、次の 2 つの場合に "structure" を含める必要があります。
	1. 列マッピングを実行する場合。
	2. データセットがコピー アクティビティのソースの場合、"structure" で型情報を提供できます。Data Factory ではシンクのネイティブ型への変換にその型情報を使用します。詳細については、[Azure BLOB との間のデータの移動](../articles/data-factory/data-factory-azure-blob-connector.md)の記事を参照してください。

### サポートされる .NET ベースの型 
Data Factory は、Azure BLOB などの読み取りデータ ソースでスキーマに "structure" で型情報を提供する場合、次の CLS 準拠の .NET ベースの型値をサポートしています。

- Int16
- Int32 
- Int64
- Single
- Double
- 小数点
- Byte
- ブール値
- String 
- Guid
- Datetime
- Datetimeoffset
- Timespan 

Datetime と Datetimeoffset の場合、必要に応じて “culture” と “format” 文字列を指定して、カスタムの Datetime 文字列の解析に利用することができます。型変換については、以下のサンプルを参照してください。

<!---HONumber=August15_HO6-->