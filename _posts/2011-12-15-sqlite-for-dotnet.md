---
id: 173
title: '.NET 4.0中使用sqlite'
date: '2011-12-15T18:54:37+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=173'
permalink: /2011/12/sqlite-for-dotnet
categories:
    - 'net&amp;c#'
---

<http://www.csharpcity.com/sqlite-ado-net-c-4-0/>

Getting SQLite to run (the ADO.NET version) under C# 4.0 is a bit tricky. You can follow the steps below to get it to work.

First, download SQLite (the ADO.NET version) from [here](http://sourceforge.net/projects/sqlite-dotnet2/). This is the sourceforge page for the official project. Install it.

Second, download SQLiteAdmin, a free, awesome GUI tool for SQLite. You can get it [here](http://sqliteadmin.orbmu2k.de/).

Next, create a database using SQLiteAdmin. Add a single table with a single row, so that you can test that things work.

Then, launch up Visual Studio 2010 and create a new project. Add a reference to the System.Data.SQLite.dll file (located in SQLite.NETbin). I recommend copying the DLL into an “extlib” folder in your project and referencing that.

Finally, run a query and see how things go! Here’s a quick method I hacked together: public static DataTable ExecuteQuery(string sql) { // Validate SQL if (string.IsNullOrWhiteSpace(sql)) { return null; } else { if (!sql.EndsWith(“;”)) { sql += “;”; } SQLiteConnection connection = new SQLiteConnection(“Data Source=blah.db”); connection.Open(); SQLiteCommand cmd = new SQLiteCommand(connection); cmd.CommandText = sql; DataTable dt = new DataTable(); SQLiteDataReader reader = cmd.ExecuteReader(); dt.Load(reader); reader.Close(); connection.Close(); return dt; } }

The only wierd thing is that the function returns a DataTable, which essentially makes it easy for you to iterate.

Then, call this function, and trace out the results:DataTable table = ExecuteQuery(“SELECT \* FROM someTable”);foreach (DataRow row in table.Rows) { Debug.Trace(“Record: id=” + row\[“id”\] + ” name=” + row\[“name”\]);}

But wait! You get this annoying and un-googlable error:

Mixed mode assembly is built against version ‘v2.0.50727’ of the runtime and cannot be loaded in the 4.0 runtime without additional configuration information.

Ouch! It seems like you’re running a .NET 2.0 library, which is ok, but it has unmanaged code in it, which may or may not be ok; we need to tell our project explicitly that we can run it.

Right-click on your project, and add a new file; select “Application Config File” (depicted below):

<figure class="wp-block-image">![](http://hiphotos.baidu.com/handboy/pic/item/f93adb63f6246b600dddd63eebf81a4c500fa258.jpg)</figure>Paste the following XML inside the configuration tag:&lt;startup useLegacyV2RuntimeActivationPolicy=”true”&gt; &lt;supportedRuntime version=”v4.0″/&gt; &lt;/startup&gt;

And viola! The exception should disappear. You should see something like (assuming you inserted two names):Record: id=1 Name=CSharpCityRecord: id=2 Name=Google

That wasn’t so hard, now was it?

===

using System.Configuration;  
using System.Data;  
using System.Data.SQLite;  
  
namespace SQLliteTest  
{  
 public class SqliteHelper  
 {  
 /// &lt;summary&gt;  
 /// 获得连接对象  
 /// &lt;/summary&gt;  
 /// &lt;returns&gt;&lt;/returns&gt;  
 public static SQLiteConnection GetSQLiteConnection()  
 {  
 return  
 new SQLiteConnection(ConfigurationManager.ConnectionStrings\[“db”\].ConnectionString);  
 }  
 private static void PrepareCommand(SQLiteCommand cmd, SQLiteConnection conn, string cmdText, params SQLiteParameter\[\] commandParameters)  
 {  
 if (conn.State != ConnectionState.Open)  
 {  
 conn.Open();  
 }  
 cmd.Parameters.Clear();  
 cmd.Connection = conn;  
 cmd.CommandText = cmdText;  
 cmd.CommandType = CommandType.Text;  
 cmd.CommandTimeout = 30;  
 if (commandParameters != null)  
 {  
 cmd.Parameters.AddRange(commandParameters);  
 }  
 }  
  
 public static DataSet ExecuteDataset(string cmdText, params SQLiteParameter\[\] commandParameters)  
 {  
 var ds = new DataSet();  
 var command = new SQLiteCommand();  
 using (SQLiteConnection connection = GetSQLiteConnection())  
 {  
 PrepareCommand(command, connection, cmdText, commandParameters);  
 var da = new SQLiteDataAdapter(command);  
 da.Fill(ds);  
 }  
 return ds;  
 }  
  
 public static DataRow ExecuteDataRow(string cmdText, params SQLiteParameter\[\] commandParameters)  
 {  
 DataSet ds = ExecuteDataset(cmdText, commandParameters);  
 if (ds != null &amp;&amp; ds.Tables.Count &gt; 0 &amp;&amp; ds.Tables\[0\].Rows.Count &gt; 0)  
 return ds.Tables\[0\].Rows\[0\];  
 return null;  
 }  
  
 /// &lt;summary&gt;  
 /// 返回受影响的行数  
 /// &lt;/summary&gt;  
 /// &lt;param name=”cmdText”&gt;a&lt;/param&gt;  
 /// &lt;param name=”commandParameters”&gt;&lt;/param&gt;  
 /// &lt;returns&gt;&lt;/returns&gt;  
 public static int ExecuteNonQuery(string cmdText, params SQLiteParameter\[\] commandParameters)  
 {  
 var command = new SQLiteCommand();  
 using (SQLiteConnection connection = GetSQLiteConnection())  
 {  
 PrepareCommand(command, connection, cmdText, commandParameters);  
  
 return command.ExecuteNonQuery();  
 }  
 }  
 /// &lt;summary&gt;  
 /// 返回SqlDataReader对象  
 /// &lt;/summary&gt;  
 /// &lt;param name=”cmdText”&gt;&lt;/param&gt;  
 /// &lt;param name=”commandParameters”&gt;传入的参数&lt;/param&gt;  
 /// &lt;returns&gt;&lt;/returns&gt;  
 public static SQLiteDataReader ExecuteReader(string cmdText, params SQLiteParameter\[\] commandParameters)  
 {  
 var command = new SQLiteCommand();  
 SQLiteConnection connection = GetSQLiteConnection();  
 try  
 {  
 PrepareCommand(command, connection, cmdText, commandParameters);  
 SQLiteDataReader reader = command.ExecuteReader(CommandBehavior.CloseConnection);  
 return reader;  
 }  
 catch  
 {  
 connection.Close();  
 throw;  
 }  
 }  
  
  
 /// &lt;summary&gt;  
 /// 返回结果集中的第一行第一列，忽略其他行或列  
 /// &lt;/summary&gt;  
 /// &lt;param name=”cmdText”&gt;&lt;/param&gt;  
 /// &lt;param name=”commandParameters”&gt;传入的参数&lt;/param&gt;  
 /// &lt;returns&gt;&lt;/returns&gt;  
 public static object ExecuteScalar(string cmdText, params SQLiteParameter\[\] commandParameters)  
 {  
 var cmd = new SQLiteCommand();  
 using (SQLiteConnection connection = GetSQLiteConnection())  
 {  
 PrepareCommand(cmd, connection, cmdText, commandParameters);  
 return cmd.ExecuteScalar();  
 }  
 }  
  
  
 /// &lt;summary&gt;  
 /// 分页  
 /// &lt;/summary&gt;  
 /// &lt;param name=”recordCount”&gt;&lt;/param&gt;  
 /// &lt;param name=”pageIndex”&gt;&lt;/param&gt;  
 /// &lt;param name=”pageSize”&gt;&lt;/param&gt;  
 /// &lt;param name=”cmdText”&gt;&lt;/param&gt;  
 /// &lt;param name=”countText”&gt;&lt;/param&gt;  
 /// &lt;param name=”commandParameters”&gt;&lt;/param&gt;  
 /// &lt;returns&gt;&lt;/returns&gt;  
 public static DataSet ExecutePager(ref int recordCount, int pageIndex, int pageSize, string cmdText,  
 string countText, params SQLiteParameter\[\] commandParameters)  
 {  
 if (recordCount &lt; 0)  
 recordCount = int.Parse(ExecuteScalar(countText, commandParameters).ToString());  
 var ds = new DataSet();  
 var command = new SQLiteCommand();  
 using (SQLiteConnection connection = GetSQLiteConnection())  
 {  
 PrepareCommand(command, connection, cmdText, commandParameters);  
 var da = new SQLiteDataAdapter(command);  
 da.Fill(ds, (pageIndex – 1) \* pageSize, pageSize, “result”);  
 }  
 return ds;  
 }  
 }  
}