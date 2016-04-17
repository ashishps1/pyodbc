# Calling #

The Python DB API specifies that Cursor objects should have a callproc method for calling stored procedures.  This hasn't been implemented yet in pyodbc because there is no way for it to know which parameters are input and which are output.

Since there won't be a way anytime soon, I'll probably implement callproc but make all of the parameters input-only.

To call a stored procedure right now, pass the call to the execute method using either a format your database recognizes or using the [ODBC call escape format](http://msdn.microsoft.com/en-us/library/ms131685.aspx).  (The ODBC driver will then reformat the call for you to match the given database.)

For SQL Server you would use something like this:
```
# SQL Server format
cursor.execute("exec sp_dosomething(123, 'abc')")

# ODBC format
cursor.execute("{call sp_dosomething(123, 'abc')}")
```

# Results #

Since we can't use output parameters at this point, you'll need to return results in a result set.  Usually this means just ending your stored procedure with a SELECT statement.

# Tips #

Some databases will return results for everything you do, including the row counts of any inserts or deletes.  If you have these, you can "step over" them using Cursor.nextset().

If you are using SQL Server, you can use SET NOCOUNT ON to disable this.  (Much cleaner)