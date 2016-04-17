Cursors represent a database cursor (and map to ODBC HSTMTs), which is used to manage the
context of a fetch operation.  Cursors created from the same connection are not isolated, i.e.,
any changes done to the database by a cursor are immediately visible by the other cursors.

# variables #

## description ##

This read-only attribute is a list of 7-item tuples, each containing (name, type\_code,
display\_size, internal\_size, precision, scale, null\_ok).  pyodbc only provides values for name,
type\_code, internal\_size, and null\_ok.  The other values are set to None.

This attribute will be None for operations that do not return rows or if one of the execute
methods has not been called.

The type\_code member is the class type used to create the Python objects when reading rows.
For example, a varchar column's type will be `str`.

## rowcount ##

The number of rows modified by the previous DDL statement.

This is -1 if no SQL has been executed or if the number of rows is unknown.  Note that it is not uncommon for databases
to report -1 after a select statement for performance reasons.  (The exact number may not be known before the first
records are returned to the application.)

# methods #

## execute ##

cursor.execute(sql, `*parameters`) --> Cursor

Prepares and executes SQL.  The optional parameters may be passed as a sequence, as specified by the DB API, or as individual values.

```
# standard
cursor.execute("select a from tbl where b=? and c=?", (x, y))

# pyodbc extension
cursor.execute("select a from tbl where b=? and c=?", x, y)
```

The return value is always the cursor itself:

```
for row in cursor.execute("select user_id, user_name from users"):
    print row.user_id, row.user_name

row  = cursor.execute("select * from tmp").fetchone()
rows = cursor.execute("select * from tmp").fetchall()

count = cursor.execute("update users set last_logon=? where user_id=?", now, user_id).rowcount
count = cursor.execute("delete from users where user_id=1").rowcount
```

As suggested in the DB API, the last prepared statement is kept and reused if you execute the same SQL again, makingexecuting the same SQL with different parameters will be more efficient.

## executemany ##

cursor.executemany(sql, seq\_of\_parameters) --> None

Executes the same SQL statement for each set of parameters.  `seq_of_parameters` is a
sequence of sequences.

```
params = [ ('A', 1), ('B', 2) ]
executemany("insert into t(name, id) values (?, ?)", params)
```

This will execute the SQL statement twice, once with ('A', 1) and once with ('B', 2).

## fetchone ##

cursor.fetchone() --> [Row](Rows.md) or None

Returns the next [row](Rows.md) or `None` when no more data is available.

A ProgrammingError exception is raised if no SQL has been executed or if it did not return a
result set (e.g. was not a SELECT statement).

```
cursor.execute("select user_name from users where user_id=?", userid)
row = cursor.fetchone()
if row:
    print row.user_name
```

## fetchall ##

cursor.fetchall() --> list of rows

Returns a list of all remaining [rows](Rows.md).

Since this reads all rows into memory, it should not be used if there are a lot of rows.
Consider iterating over the rows instead.  However, it is useful for freeing up a Cursor so you
can perform a second query before processing the resulting rows.

A ProgrammingError exception is raised if no SQL has been executed or if it did not return a
result set (e.g. was not a SELECT statement).

```
cursor.execute("select user_id, user_name from users where user_id < 100")
rows = cursor.fetchall()
for row in rows:
    print row.user_id, row.user_name
```

## fetchmany ##

cursor.fetchmany([size=cursor.arraysize]) --> list

Returns a list of remaining [rows](Rows.md), containing no more than `size` rows, used to process
results in chunks.  The list will be empty when there are no more rows.

The default for cursor.arraysize is 1 which is no different than calling fetchone().

A ProgrammingError exception is raised if no SQL has been executed or if it did not return a
result set (e.g. was not a SELECT statement).


## commit ##

cursor.commit() --> None

Commits pending transactions on the connection that created this cursor.

_This affects all cursors created by the same connection!_

This is no different than calling commit on the connection.  The benefit is that many uses can
now just use the cursor and not have to track the connection.

## rollback ##

cursor.rollback() --> None

Rolls back pending transactions on the connection that created this cursor.

_This affects all cursors created by the same connection!_


## skip ##

cursor.skip(count) --> None

Skips the next _count_ records by calling SQLFetchScroll with SQL\_FETCH\_NEXT.

For convenience, skip(0) is accepted and will do nothing.


## nextset ##

cursor.nextset() --> True or None

This method will make the cursor skip to the next available set, discarding any remaining rows from the current set.

If there are no more sets, the method returns None. Otherwise, it returns a true value and subsequent calls to the fetch methods will return rows from the next result set.

This method is primarily used if you have stored procedures that return multiple results.

## close() ##

Closes the cursor.  A ProgrammingError exception will be raised if any operation is attempted with the cursor.

Cursors are closed automatically when they are deleted, so calling this is not usually
necessary when using CPython.

## setinputsizes, setoutputsize ##

These are optional in the API and are not supported.

## callproc(procname[,parameters]) ##

This is not yet supported since there is no way for pyodbc to determine which parameters are input, output, or both.

You will need to call stored procedures using execute().  You can use your database's format or the ODBC escape format.

## tables ##

cursor.tables(table=None, catalog=None, schema=None, tableType=None) --> Cursor

Creates a result set of tables in the database that match the given criteria.

The table, catalog, and schema interpret the '_' and '%' characters as wildcards.  The escape
character is driver specific, so use Connection.searchescape._

Each row has the following columns.  See the
[SQLTables](http://msdn.microsoft.com/en-us/library/ms711831.aspx)
documentation for more information.

  1. table\_cat: The catalog name.
  1. table\_schem: The schema name.
  1. table\_name: The table name.
  1. table\_type: One of TABLE, VIEW, SYSTEM TABLE, GLOBAL TEMPORARY, LOCAL TEMPORARY, ALIAS, SYNONYM, or a data source-specific type name.
  1. remarks: A description of the table.

```
for row in cursor.tables():
    print row.table_name

# Does table 'x' exist?
if cursor.tables(table='x').fetchone():
   print 'yes it does'
```

## columns ##

cursor.columns(table=None, catalog=None, schema=None, column=None) --> Cursor

Creates a result set of column information in the specified tables using the
[SQLColumns](http://msdn.microsoft.com/en-us/library/ms711683%28VS.85%29.aspx) function.

Each row has the following columns:

  1. table\_cat
  1. table\_schem
  1. table\_name
  1. column\_name
  1. data\_type
  1. type\_name
  1. column\_size
  1. buffer\_length
  1. decimal\_digits
  1. num\_prec\_radix
  1. nullable
  1. remarks
  1. column\_def
  1. sql\_data\_type
  1. sql\_datetime\_sub
  1. char\_octet\_length
  1. ordinal\_position
  1. is\_nullable: One of SQL\_NULLABLE, SQL\_NO\_NULLS, SQL\_NULLS\_UNKNOWN.

```
# columns in table x
for row in cursor.columns(table='x'):
    print row.column_name
```

## statistics ##

cursor.statistics(table, catalog=None, schema=None, unique=False, quick=True) --> Cursor

Creates a result set of statistics about a single table and the indexes associated with the
table by executing [SQLStatistics](http://msdn.microsoft.com/en-us/library/ms711022%28VS.85%29.aspx).

If `code` unique is `True` only unique indexes are returned; if `False` all indexes
are returned.

If `quick` is `True`, CARDINALITY and PAGES are returned only if they are readily
available.  Otherwise NULL is returned on those colummns.

Each row has the following columns:

  1. table\_cat
  1. table\_schem
  1. table\_name
  1. non\_unique
  1. index\_qualifier
  1. index\_name
  1. type
  1. ordinal\_position
  1. column\_name
  1. asc\_or\_desc
  1. cardinality
  1. pages
  1. filter\_condition

## rowIdColumns ##

cursor.rowIdColumns(table, catalog=None, schema=None, nullable=True) --> Cursor

Executes [SQLSpecialColumns](http://msdn.microsoft.com/en-us/library/ms714602%28VS.85%29.aspx)
with SQL\_BEST\_ROWID which creates a result set of columns that uniquely identify a row.

Each row has the following columns.

  1. scope: One of SQL\_SCOPE\_CURROW, SQL\_SCOPE\_TRANSACTION, or SQL\_SCOPE\_SESSION
  1. column\_name
  1. data\_type: The ODBC SQL data type constant (e.g. SQL\_CHAR)
  1. type\_name
  1. column\_size
  1. buffer\_length
  1. decimal\_digits
  1. pseudo\_column: One of SQL\_PC\_UNKNOWN, SQL\_PC\_NOT\_PSEUDO, SQL\_PC\_PSEUDO

## rowVerColumns ##

cursor.rowVerColumns(table, catalog=None, schema=None, nullable=True) --> Cursor

Executes [SQLSpecialColumns](http://msdn.microsoft.com/en-us/library/ms714602%28VS.85%29.aspx)
with SQL\_ROWVER which creates a result set of columns that are automatically updated when any
value in the row is updated.  Returns the Cursor object.  Each row has the following columns.

  1. scope: One of SQL\_SCOPE\_CURROW, SQL\_SCOPE\_TRANSACTION, or SQL\_SCOPE\_SESSION
  1. column\_name
  1. data\_type: The ODBC SQL data type constant (e.g. SQL\_CHAR)
  1. type\_name
  1. column\_size
  1. buffer\_length
  1. decimal\_digits
  1. pseudo\_column: One of SQL\_PC\_UNKNOWN, SQL\_PC\_NOT\_PSEUDO, SQL\_PC\_PSEUDO

## primaryKeys ##

primaryKeys(table, catalog=None, schema=None) --> Cursor

Creates a result set of column names that make up the primary key for a table by executing the
[SQLPrimaryKeys](http://msdn.microsoft.com/en-us/library/ms711005%28VS.85%29.aspx) function.

Each row has the following columns:

  1. table\_cat
  1. table\_schem
  1. table\_name
  1. column\_name
  1. key\_seq
  1. pk\_name

## foreignKeys ##

cursor.foreignKeys(table=None, catalog=None, schema=None, foreignTable=None, foreignCatalog=None, foreignSchema=None) --> Cursor

Executes the [SQLForeignKeys](http://msdn.microsoft.com/en-us/library/ms709315%28VS.85%29.aspx)
function and creates a result set of column names that are foreign keys in the specified table
(columns in the specified table that refer to primary keys in other tables) or foreign keys in
other tables that refer to the primary key in the specified table.

Each row has the following columns:

  1. pktable\_cat
  1. pktable\_schem
  1. pktable\_name
  1. pkcolumn\_name
  1. fktable\_cat
  1. fktable\_schem
  1. fktable\_name
  1. fkcolumn\_name
  1. key\_seq
  1. update\_rule
  1. delete\_rule
  1. fk\_name
  1. pk\_name
  1. deferrability

## procedures ##

cursor.procedures(procedure=None, catalog=None, schema=None) --> Cursor

Executes [SQLProcedures](http://msdn.microsoft.com/en-us/library/ms715368%28VS.85%29.aspx)
and creates a result set of information about the procedures in the data source.  Each row has
the following columns:

  1. procedure\_cat
  1. procedure\_schem
  1. procedure\_name
  1. num\_input\_params
  1. num\_output\_params
  1. num\_result\_sets
  1. remarks
  1. procedure\_type

## getTypeInfo ##

cursor.getTypeInfo(sqlType=None) --> Cursor

Executes [SQLGetTypeInfo](http://msdn.microsoft.com/en-us/library/ms714632%28VS.85%29.aspx)
a creates a result set with information about the specified data type or all data types
supported by the ODBC driver if not specified.  Each row has the following columns:

  1. type\_name
  1. data\_type
  1. column\_size
  1. literal\_prefix
  1. literal\_suffix
  1. create\_params
  1. nullable
  1. case\_sensitive
  1. searchable
  1. unsigned\_attribute
  1. fixed\_prec\_scale
  1. auto\_unique\_value
  1. local\_type\_name
  1. minimum\_scale
  1. maximum\_scale
  1. sql\_data\_type
  1. sql\_datetime\_sub
  1. num\_prec\_radix
  1. interval\_precision