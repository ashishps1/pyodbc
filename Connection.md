Connection objects manage connections to the database.

Each manages a single ODBC HDBC.

There is no constructor; Connections can only be created by the [module's connect function](Module.md).

# variables #

## autocommit ##

True if the connection is in autocommit mode; False otherwise.  Changing this value updates the
ODBC autocommit setting.

## searchescape ##

The ODBC search pattern escape character, as returned by SQLGetInfo(SQL\_SEARCH\_PATTERN\_ESCAPE),
used to escape special characters such as '%' and '_'.  These are driver specific._

## timeout ##

An optional integer query timeout, in seconds.  Use zero, the default, to disable.

The timeout is applied to all cursors created by the connection, so it cannot be changed for a given connection.

If a query timeout occurs, the database should raise an OperationalError with SQLSTATE HYT00 or HYT01.

Note: This attribute only affects queries.  To set the timeout for the actual connection process, use the timeout keyword of the [pyodbc.connect](Module.md) function.

# methods #

## cursor ##

cnxn.cursor() --> Cursor

Returns a new Cursor Object using the connection.

pyodbc supports multiple cursors per connection but your database may not.

## commit ##

cnxn.commit()

Commits any pending transaction to the database.

Pending transactions are automatically rolled back when a connection is closed, so be sure to
call this.

## rollback ##

cnxn.rollback()

Causes the the database to roll back to the start of any pending transaction.

You can call this even if no work has been performed on the cursor, allowing it to be used in
finally statements, etc.

## close ##

cnxn.close()

Closes the connection.  Connections are automatically closed when they are deleted, but you
should call this if the connection is referenced in more than one place.

The connection will be unusable from this point forward and a ProgrammingError will be raised
if any operation is attempted with the connection.  The same applies to all cursor objects
trying to use the connection.

Note that closing a connection without committing the changes first will cause an implicit
rollback to be performed.

## getinfo ##

getinfo(type) --> str | int | bool

Returns general information about the driver and data source associated with a connection by calling
[SQLGetInfo](http://msdn.microsoft.com/en-us/library/ms711681%28VS.85%29.aspx) and returning its
results.  See Microsoft's SQLGetInfo documentation for the types of information.

The supported types and data type of the return values are:

| SQL\_EXPRESSIONS\_IN\_ORDERBY      | True or False |
|:-----------------------------------|:--------------|
| SQL\_FILE\_USAGE                   | number        |
| SQL\_FORWARD\_ONLY\_CURSOR\_ATTRIBUTES1 | number        |
| SQL\_FORWARD\_ONLY\_CURSOR\_ATTRIBUTES2 | number        |
| SQL\_GETDATA\_EXTENSIONS           | number        |
| SQL\_GROUP\_BY                     | number        |
| SQL\_IDENTIFIER\_CASE              | number        |
| SQL\_IDENTIFIER\_QUOTE\_CHAR       | string        |
| SQL\_INDEX\_KEYWORDS               | number        |
| SQL\_INFO\_SCHEMA\_VIEWS           | number        |
| SQL\_INSERT\_STATEMENT             | number        |
| SQL\_INTEGRITY                     | True or False |
| SQL\_KEYSET\_CURSOR\_ATTRIBUTES1   | number        |
| SQL\_KEYSET\_CURSOR\_ATTRIBUTES2   | number        |
| SQL\_KEYWORDS                      | string        |
| SQL\_LIKE\_ESCAPE\_CLAUSE          | True or False |
| SQL\_MAX\_ASYNC\_CONCURRENT\_STATEMENTS | number        |
| SQL\_MAX\_BINARY\_LITERAL\_LEN      | number        |
| SQL\_MAX\_CATALOG\_NAME\_LEN        | number        |
| SQL\_MAX\_CHAR\_LITERAL\_LEN        | number        |
| SQL\_MAX\_COLUMNS\_IN\_GROUP\_BY     | number        |
| SQL\_MAX\_COLUMNS\_IN\_INDEX        | number        |
| SQL\_MAX\_COLUMNS\_IN\_ORDER\_BY     | number        |
| SQL\_MAX\_COLUMNS\_IN\_SELECT       | number        |
| SQL\_MAX\_COLUMNS\_IN\_TABLE        | number        |
| SQL\_MAX\_COLUMN\_NAME\_LEN         | number        |
| SQL\_MAX\_CONCURRENT\_ACTIVITIES   | number        |
| SQL\_MAX\_CURSOR\_NAME\_LEN         | number        |
| SQL\_MAX\_DRIVER\_CONNECTIONS      | number        |
| SQL\_MAX\_IDENTIFIER\_LEN          | number        |
| SQL\_MAX\_INDEX\_SIZE              | number        |
| SQL\_MAX\_PROCEDURE\_NAME\_LEN      | number        |
| SQL\_MAX\_ROW\_SIZE                | number        |
| SQL\_MAX\_ROW\_SIZE\_INCLUDES\_LONG  | True or False |
| SQL\_MAX\_SCHEMA\_NAME\_LEN         | number        |
| SQL\_MAX\_STATEMENT\_LEN           | number        |
| SQL\_MAX\_TABLES\_IN\_SELECT        | number        |
| SQL\_MAX\_TABLE\_NAME\_LEN          | number        |
| SQL\_MAX\_USER\_NAME\_LEN           | number        |
| SQL\_MULTIPLE\_ACTIVE\_TXN         | True or False |
| SQL\_MULT\_RESULT\_SETS            | True or False |
| SQL\_NEED\_LONG\_DATA\_LEN          | True or False |
| SQL\_NON\_NULLABLE\_COLUMNS        | number        |
| SQL\_NULL\_COLLATION               | number        |
| SQL\_NUMERIC\_FUNCTIONS            | number        |
| SQL\_ODBC\_INTERFACE\_CONFORMANCE  | number        |
| SQL\_ODBC\_VER                     | string        |
| SQL\_OJ\_CAPABILITIES              | number        |
| SQL\_ORDER\_BY\_COLUMNS\_IN\_SELECT  | True or False |
| SQL\_PARAM\_ARRAY\_ROW\_COUNTS      | number        |
| SQL\_PARAM\_ARRAY\_SELECTS         | number        |
| SQL\_PROCEDURES                    | True or False |
| SQL\_PROCEDURE\_TERM               | string        |
| SQL\_QUOTED\_IDENTIFIER\_CASE      | number        |
| SQL\_ROW\_UPDATES                  | True or False |
| SQL\_SCHEMA\_TERM                  | string        |
| SQL\_SCHEMA\_USAGE                 | number        |
| SQL\_SCROLL\_OPTIONS               | number        |
| SQL\_SEARCH\_PATTERN\_ESCAPE       | string        |
| SQL\_SERVER\_NAME                  | string        |
| SQL\_SPECIAL\_CHARACTERS           | string        |
| SQL\_SQL92\_DATETIME\_FUNCTIONS    | number        |
| SQL\_SQL92\_FOREIGN\_KEY\_DELETE\_RULE | number        |
| SQL\_SQL92\_FOREIGN\_KEY\_UPDATE\_RULE | number        |
| SQL\_SQL92\_GRANT                  | number        |
| SQL\_SQL92\_NUMERIC\_VALUE\_FUNCTIONS | number        |
| SQL\_SQL92\_PREDICATES             | number        |
| SQL\_SQL92\_RELATIONAL\_JOIN\_OPERATORS | number        |
| SQL\_SQL92\_REVOKE                 | number        |
| SQL\_SQL92\_ROW\_VALUE\_CONSTRUCTOR | number        |
| SQL\_SQL92\_STRING\_FUNCTIONS      | number        |
| SQL\_SQL92\_VALUE\_EXPRESSIONS     | number        |
| SQL\_SQL\_CONFORMANCE              | number        |
| SQL\_STANDARD\_CLI\_CONFORMANCE    | number        |
| SQL\_STATIC\_CURSOR\_ATTRIBUTES1   | number        |
| SQL\_STATIC\_CURSOR\_ATTRIBUTES2   | number        |
| SQL\_STRING\_FUNCTIONS             | number        |
| SQL\_SUBQUERIES                    | number        |
| SQL\_SYSTEM\_FUNCTIONS             | number        |
| SQL\_TABLE\_TERM                   | string        |
| SQL\_TIMEDATE\_ADD\_INTERVALS      | number        |
| SQL\_TIMEDATE\_DIFF\_INTERVALS     | number        |
| SQL\_TIMEDATE\_FUNCTIONS           | number        |
| SQL\_TXN\_CAPABLE                  | number        |
| SQL\_TXN\_ISOLATION\_OPTION        | number        |
| SQL\_UNION                         | number        |
| SQL\_USER\_NAME                    | string        |
| SQL\_XOPEN\_CLI\_YEAR              | string        |

## execute ##

execute(sql, `*`params) --> Cursor

Create a new Cursor object, call its execute method, and returns it.  See
Cursor.execute for more details.

This is a convenience method that is not part of the DB API.  Since a new
Cursor is allocated by each call, this should not be used if more than one SQL
statement needs to be executed.

## add\_output\_converter ##

add\_output\_converter(sqltype, func)

Register an output converter function that will be called whenever a value with
the given SQL type is read from the database.

sqltype
> The integer SQL type value to convert, which can be one of the defined
> standard constants (e.g. pyodbc.SQL\_VARCHAR) or a database-specific value
> (e.g. -151 for the SQL Server 2008 geometry data type).

func
> The converter function which will be called with a single parameter, the
> value, and should return the converted value.  If the value is NULL, the
> parameter will be None.  Otherwise it will be a Python string.

## clear\_output\_converters ##

clear\_output\_converters()

Remove all output converter functions.