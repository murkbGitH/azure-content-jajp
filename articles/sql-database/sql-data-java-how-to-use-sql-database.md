<properties 
	pageTitle="SQL Azure の使用方法 (Java) - Azure の機能ガイド" 
	description="Java コードから Azure SQL Database を使用する方法について説明します。" 
	services="sql-database" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="robmcm"/>

# Java での Azure SQL データベースの使用方法

次の手順では、Java で Azure SQL データベースを使用する方法を示しています。わかりやすく説明するために、コマンド ラインの例を示していますが、これと類似した手順を、内部設置型のホステッド Web アプリケーションや、Azure 内またはその他の実行環境内の Web アプリケーションにも適用できます。このガイドでは、[Azure 管理ポータル](https://windows.azure.com)からのサーバーとデータベースの作成について説明します。

## Azure SQL データベースとは

Azure SQL データベースは、Azure 用のリレーショナル データベース管理システムを提供し、SQL Server テクノロジを基盤としています。SQL データベース インスタンスを使用すると、リレーショナル データベース ソリューションを簡単に準備してクラウドにデプロイすることができ、分散したデータ センターを活用して、組み込みのデータ保護と自己復旧機能のメリットによるエンタープライズ クラスの可用性、拡張性、セキュリティを確保できます。



## 概念
Azure SQL Database は SQL Server テクノロジに基づいているため、Java から SQL Database へのアクセスは Java から SQL Server へのアクセスに非常に似ています。アプリケーションをローカルで開発した後 (SQL Server を使用)、接続文字列を変更するだけで SQL データベースに接続できます。SQL Server JDBC ドライバーをアプリケーションに使用できます。ただし、SQL データベースと SQL Server のいくつかの違いがアプリケーションに影響する場合があります。詳細については、「[SQL データベースのガイドラインと制限事項](http://msdn.microsoft.com/library/windowsazure/ff394102.aspx)」をご覧ください。

SQL データベースに関するその他のリソースについては、「[次のステップ][]」をご覧ください。

## 前提条件

次の前提条件は Java で SQL データベースを使用する場合に必要になります。

* A Java Developer Kit (JDK) v 1.6 以降。
* Azure サブスクリプション <http://www.microsoft.com/windowsazure/offers/> から入手できます。
* Eclipse を使用している場合は、Eclipse IDE for Java EE Developers Indigo 以降が必要です。<http://www.eclipse.org/downloads/> からダウンロードできます。また、Azure Plugin for Eclipse with Java (Microsoft Open Technologies 提供) も必要です。このプラグインのインストール時には Microsoft JDBC Driver 4.0 for SQL Server がインストール済みであることをご確認ください。詳細については、「[Installing the Azure Plugin for Eclipse with Java (by Microsoft Open Technologies) (Azure Plugin for Eclipse with Java (Microsoft Open Technologies 提供) のインストール)](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx)」をご覧ください。
* Eclipse を使用していない場合は、Microsoft JDBC Driver 4.0 for SQL Server が必要です。これは、<http://www.microsoft.com/download/details.aspx?id=11774> からダウンロードできます。

## Azure SQL データベースの作成

Java コードで Azure SQL データベースを使用するには、Azure SQL データベース サーバーの作成が必要になります。

1. [Azure 管理ポータル](https://manage.windowsazure.com) にログインします。
2. **[新規]** をクリックします。

    ![Create new SQL database][create_new]

3. **[SQL データベース]**、**[カスタム作成]** の順にクリックします。

    ![Create custom SQL database][create_new_sql_db]

4. **[データベースの設定]** ダイアログ ボックスで、データベース名を指定します。このガイドでは、データベース名として **gettingstarted** を使用します。
5. **[サーバー]** で、**[新しい SQL データベース サーバー]** を選択します。他のフィールドには既定値をそのまま使用します。

    ![SQL database settings][create_database_settings]

6. 次へ進む矢印をクリックします。	
7. **[サーバーの設定]** ダイアログ ボックスで、SQL データベース サーバーへのログイン名を入力します。このガイドでは、**MySQLAdmin** を使用しました。パスワードの指定と確認入力を行います。リージョンを指定し、**[Azure サービスにサーバーへのアクセスを許可します]** チェック ボックスがオンになっていることを確認します。

    ![SQL server settings][create_server_settings]

8. 完了ボタンをクリックします。

## SQL データベースの接続文字列の決定

1. [Azure 管理ポータル](https://manage.windowsazure.com) にログインします。
2. **[SQL データベース]** をクリックします。
3. 使用するデータベースをクリックします。
4. **[接続文字列の表示]** をクリックします。
5. **JDBC** 接続文字列の内容を強調表示します。

    ![Determine JDBC connection string][get_jdbc_connection_string]

6. 強調表示した **JDBC** 接続文字列の内容を右クリックし、**[コピー]** をクリックします。
7. これで、この値をコード ファイルに貼り付けて、次の形式の接続文字列を作成できるようになりました。 *your_server* (2 か所) を、前の手順でコピーしたテキストに置き換え、 *your_password* を、SQL Database アカウントの作成時に指定したパスワードの値に置き換えます。(さらに、**gettingstarted** と **MySQLAdmin** を使用しない場合は、**database=** と **user=** に設定された値もそれぞれ置き換える)。 

    String connectionString =
		"jdbc:sqlserver://*your_server*.database.windows.net:1433" + ";" +  
    	"database=gettingstarted" + ";" + 
    	"user=MySQLAdmin@*your_server*" + ";" +  
    	"password=*your_password*" + ";" +  
        "encrypt=true" + ";" +
        "hostNameInCertificate=*.int.mscds.com" + ";" +  
        "loginTimeout=30";

この文字列はこのガイドで後で実際に使用します。これで、接続文字列を決定する手順がわかりました。さらに、アプリケーションの要件によっては、**encrypt** と **hostNameInCertificate** 設定の使用が不要になったり、**loginTimeout** 設定の変更が必要になったりします。

## IP アドレスの特定の範囲へのアクセスを許可するには

1. [管理ポータル](https://manage.windowsazure.com)にログインします。
2. **[SQL データベース]** をクリックします。
3. **[サーバー]** をクリックします。
4. 使用するサーバーをクリックします。
5. **[管理]** をクリックします。
6. **[構成]** をクリックします。
7. **[使用できる IP アドレス]** で、新しい IP ルールの名前を入力します。IP アドレスの開始と終了の範囲を指定します。参考までに、ここでは現在のクライアント IP アドレスを示しています。次の例では、1 つのクライアント IP アドレスにアクセスを許可しています (IP アドレスは実際のものと異なる)。

    ![Allowed IP addresses dialog][allowed_ips_dialog]

8. 完了ボタンをクリックします。これで、指定した IP アドレスはデータベース サーバーへのアクセスが許可されます。

## Java で Azure SQL データベースを使用するには

1. Java プロジェクトを作成します。このチュートリアルでは、**HelloSQLAzure** という名前にします。
2. **HelloSQLAzure.java** という名前の Java クラス ファイルをプロジェクトに追加します。
3. **Microsoft JDBC Driver for SQL Server** をビルド パスに追加します。

   Eclipse を使用している場合:

    1. Eclipse の Project Explorer で、**HelloSQLAzure** プロジェクトを右クリックし、**[Properties]** をクリックします。
    2. **[Properties]** ダイアログ ボックスの左側のウィンドウで、**[Java Build Path]** をクリックします。
    3. **[Libraries]** タブをクリックし、**[Add Library]** をクリックします。
    4. **[Add Library]** ダイアログ ボックスで、**[Microsoft JDBC Driver 4.0 for SQL Server]** を選択し、**[Next]**、**[Finish]** の順にクリックします。
    5. **[OK]** をクリックして **[Properties]** ダイアログを閉じます。

    Eclipse を使用していない場合は、Microsoft JDBC Driver 4.0 for SQL Server JAR をクラス パスに追加します。関連情報については、「[JDBC ドライバーの使用](http://msdn.microsoft.com/library/ms378526.aspx)」をご覧ください。

4. **HelloSQLAzure.java** コード内で、次に示すような  `import` ステートメントを追加します。

        import java.sql.*;
        import com.microsoft.sqlserver.jdbc.*;

5. 接続文字列を指定します。たとえば次のようになります。前の手順と同様に、 *your_server* (2 か所)、 *your_user* と  *your_password* をSQL データベース サーバーに該当する値に置き換えます。

        String connectionString =
        	"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
        		"database=master" + ";" + 
        		"user=your_user@your_server" + ";" +  
        		"password=your_password";

これで、SQL データベース サーバーと通信するコードを追加する準備ができました。

## コードからの Azure SQL データベースとの通信

このトピックの残りでは、次の処理を実行する例を示します。

1. SQL データベース サーバーに接続する。
2. SQL ステートメントを定義する。たとえば、テーブルの作成や削除、行の挿入/選択/挿入/削除などのためです。
3. SQL ステートメントを実行する。**executeUpdate** または **executeQuery** の呼び出しを使用します。
4. クエリの結果を表示する (該当する場合)。

次のセクションは、順番に読み進むように構成 (サンプル提供) されています。最初のスニペットは完全なサンプルになっています。その他のスニペットは完全なサンプルのフレームワークの一部 (**import** ステートメント、**class** と **main** の宣言、エラーの処理、リソースの終了など) に依存するものとします。

## テーブルを作成するには

次のコードでは、**Person** という名前のテーブルを作成する方法を示しています。

	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
	public class HelloSQLAzure {
	
	    public static void main(String[] args) 
	    {
	
			// Connection string for your SQL Database server.
			// Change the values assigned to your_server, 
			// your_user@your_server,
			// and your_password.
			String connectionString = 
				"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
					"database=gettingstarted" + ";" + 
					"user=your_user@your_server" + ";" +  
					"password=your_password";
			
			// The types for the following variables are
			// defined in the java.sql library.
			Connection connection = null;  // For making the connection
			Statement statement = null;    // For the SQL statement
			ResultSet resultSet = null;    // For the result set, if applicable
			
			try
			{
			    // Ensure the SQL Server driver class is available.
			    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
			
			    // Establish the connection.
			    connection = DriverManager.getConnection(connectionString);
			
			    // Define the SQL string.
			    String sqlString = 
					"CREATE TABLE Person (" + 
			        	"[PersonID] [int] IDENTITY(1,1) NOT NULL," +
			            "[LastName] [nvarchar](50) NOT NULL," + 
			            "[FirstName] [nvarchar](50) NOT NULL)";
			
			    // Use the connection to create the SQL statement.
			    statement = connection.createStatement();
			
			    // Execute the statement.
			    statement.executeUpdate(sqlString);
			
			    // Provide a message when processing is complete.
			    System.out.println("Processing complete.");
			
			}
			// Exception handling
	        catch (ClassNotFoundException cnfe)  
	        {
	            
	            System.out.println("ClassNotFoundException " +
	                               cnfe.getMessage());
	        }
	        catch (Exception e)
	        {
	            System.out.println("Exception " + e.getMessage());
	            e.printStackTrace();
	        }
	        finally
	        {
	            try
	            {
	                // Close resources.
	                if (null != connection) connection.close();
	                if (null != statement) statement.close();
	                if (null != resultSet) resultSet.close();
	            }
	            catch (SQLException sqlException)
	            {
	                // No additional action if close() statements fail.
	            }
	        }
	        
	    }
	
	}
	

## テーブルにインデックスを作成するには

次のコードでは、**PersonID** 列を使用して、**Person** テーブルに **index1** という名前のインデックスを作成する方法を示しています。

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"CREATE CLUSTERED INDEX index1 " + "ON Person (PersonID)";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...



## 行を挿入するには

次のコードでは、**Person** テーブルに行を追加する方法を示しています。

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"SET IDENTITY_INSERT Person ON " + 
	        	"INSERT INTO Person " + 
	            "(PersonID, LastName, FirstName) " + 
	            "VALUES(1, 'Abercrombie', 'Kim')," + 
	            	  "(2, 'Goeschl', 'Gerhard')," + 
	                  "(3, 'Grachev', 'Nikolay')," + 
	                  "(4, 'Yee', 'Tai')," + 
	                  "(5, 'Wilson', 'Jim')";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 
## 行を取得するには

次のコードでは、**Person** テーブルから行を取得する方法を示しています。

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = "SELECT TOP 10 * FROM Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Loop through the results
	    while (resultSet.next())
	    {
	        // Print out the row data
	        System.out.println(
	        	"Person with ID " + 
	        	resultSet.getString("PersonID") + 
	        	" has name " +
	        	resultSet.getString("FirstName") + " " +
	       		resultSet.getString("LastName"));
	        }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 このコードでは、**Person** テーブルから先頭の 10 行を選択しました。すべての行が返されるようにする場合は、次のように SQL ステートメントを変更します。

	String sqlString = "SELECT * FROM Person";

 
## WHERE 句を使用して行を取得するには

句を使用して行を取得するには、前に示したコードで SQL ステートメントを変更して句を追加します。次の SQL 文では、**FirstName** 値が **Jim** に一致する行を取得するための句を追加しています。

	// Define the SQL string.
	String sqlString = "SELECT * FROM Person WHERE FirstName='Jim'";
	
WHERE 句は、行数の取得、行の更新、行の削除にも使用できます。

<h2><a id="to_retrieve_row_count"></a>行数を取得するには</h2>

次のコードでは、**Person** テーブルから行数を取得する方法を示しています。
 
	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	// Define the SQL string.
	    String sqlString = "SELECT COUNT (PersonID) FROM Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Print out the returned number of rows.
	    while (resultSet.next())
	    {
	        System.out.println("There were " + 
	                         resultSet.getInt(1) +
	                         " rows returned.");
	    }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## 行を更新するには

次のコードでは、行を更新する方法を示しています。この例では、**FirstName** 値が **Jim** である行はすべて、**LastName** 値が **Kim** に変更されます。

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"UPDATE Person " + "SET LastName = 'Kim' " + "WHERE FirstName='Jim'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	    
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}// Exception handling and resource closing not shown...

 

## 行を削除するには

次のコードでは、行を削除する方法を示しています。この例では、**FirstName** 値が **Jim** である行はすべて削除されます。

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"DELETE from Person " + 
				"WHERE FirstName='Jim'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...
	
 
## テーブルが存在するかどうかを調べるには

次のコードでは、テーブルが存在するかどうかを調べる方法を示しています。

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"IF EXISTS (SELECT 1 " +
	        	"FROM sysobjects " + 
	            "WHERE xtype='u' AND name='Person') " +
	            "SELECT 'Person table exists.'" +
	            "ELSE  " +
	            "SELECT 'Person table does not exist.'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Display the result.
	    while (resultSet.next())
	    {
	        System.out.println(resultSet.getString(1));
	    }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## インデックスを削除するには

次のコードでは、**Person** テーブルから **index1** という名前のインデックスを削除する方法を示しています。

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +
			"database=gettingstarted" + ";" +
			"user=your_user@your_server" + ";" +
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"DROP INDEX index1 " + 
	        	"ON Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 
## テーブルを削除するには

次のコードでは、**Person** という名前のテーブルを削除する方法を示しています。

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = "DROP TABLE Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## Azure デプロイ内での Java からの SQL データベースの使用

Azure 展開内の Java で SQL データベースを使用するには、前に示したように Microsoft JDBC Driver 4.0 for SQL Server をライブラリとしてクラス パスに追加する操作に加え、パッケージ化して展開に追加する必要があります。


**Eclipse を使用している場合の Microsoft JDBC Driver 4.0 SQL Server のパッケージ化**

1. Eclipse の Project Explorer で、プロジェクトを右クリックし、**[Properties]** をクリックします。
2. **[Properties]** ダイアログ ボックスの左側のウィンドウで、**[Deployment Assembly]**、**[Add]** の順にクリックします。
3. **[New Assembly Directive]** ダイアログ ボックスで、**[Java Build Path Entries]**、**[Next]** の順にクリックします。
4. **Microsoft JDBC Driver 4.0 SQL Server** を選択し、**[Finish]** をクリックします。
5. **[OK]** をクリックして **[Properties]** ダイアログを閉じます。
6. プロジェクトの WAR ファイルを approot フォルダーにエクスポートし、Azure プロジェクトを再ビルドします。その手順については、「[Creating a Hello World Application Using the Azure Plugin for Eclipse with Java (by Microsoft Open Technologies) (Azure Plugin for Eclipse with Java (Microsoft Open Technologies 提供) を使用した Hello World アプリケーションの作成)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)」で説明しています。そのトピックでは、コンピューティング エミュレーターと Azure 上でアプリケーションを実行する方法についても説明しています。

**Eclipse を使用していない場合の Microsoft JDBC Driver 4.0 SQL Server のパッケージ化**

* Microsoft JDBC Driver 4.0 SQL Server ライブラリが、Java アプリケーションと同じ Azure ロールにインクルードされ、アプリケーションのクラス パスに追加されていることを確認します。

## 次のステップ

Microsoft JDBC Driver for SQL Server の詳細については、「[JDBC ドライバーの概要](http://msdn.microsoft.com/library/ms378749.aspx)」をご覧ください。SQL データベースの詳細については、「[Azure SQL データベースの概要](http://msdn.microsoft.com/library/windowsazure/ee336241.aspx)」をご覧ください。

[概念]:#concepts
[前提条件]:#prerequisites
[Azure SQL データベースの作成]:#create_db
[SQL データベースの接続文字列の決定]:#determine_connection_string
[IP アドレスの特定の範囲へのアクセスを許可するには]:#specify_allowed_ips
[Java で Azure SQL データベースを使用するには]:#use_sql_azure_in_java
[コードからの Azure SQL データベースとの通信]:#communicate_from_code
[テーブルを作成するには]:#to_create_table
[テーブルにインデックスを作成するには]:#to_create_index
[行を挿入するには]:#to_insert_rows
[行を取得するには]:#to_retrieve_rows
[WHERE 句を使用して行を取得するには]:#to_retrieve_rows_using_where
[行数を取得するには]:#to_retrieve_row_count
[行を更新するには]:#to_update_rows
[行を削除するには]:#to_delete_rows
[テーブルが存在するかどうかを調べるには]:#to_check_table_existence
[インデックスを削除するには]:#to_drop_index
[テーブルを削除するには]:#to_drop_table
[Azure デプロイ内での Java からの SQL データベースの使用]:#using_in_azure
[次のステップ]:#nextsteps
[create_new]: ./media/sql-data-java-how-to-use-sql-database/WA_New.png
[create_new_sql_db]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_DB_Create.png
[create_database_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_1.png
[create_server_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_2.png
[get_jdbc_connection_string]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_JDBC_ConnectionString.png
[allowed_ips_dialog]: ./media/sql-data-java-how-to-use-sql-database/WA_Allowed_IPs.png

<!--HONumber=47-->
 