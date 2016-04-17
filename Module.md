# Variables #

## version ##

The module version string in the format major.minor.revision

## apilevel ##

The string constant '2.0' indicating this module supports DB API level 2.0.

## lowercase ##

A Boolean that controls whether column names in result rows are lowercased.  This can be
changed any time and affects queries executed after the change.  The default is False.  This
can be useful when database columns have inconsistent capitalization.

## pooling ##

A Boolean indicating whether connection pooling is enabled.  This is a global (HENV) setting,
so it can only be modified before the first connection is made.  The default is True, which
enables ODBC connection pooling.

## threadsafety ##

The integer 1, indicating that threads may share the module but not connections.  Note that
connections and cursors may be used by different threads, just not at the same time.

## qmark ##

The string constant 'qmark' to indicate parameters are identified using question marks.

# Functions #

## connect ##

connect(`*`connectionstring, `**`kwargs) --> Connection

Creates and returns a new connection to the database.

The optional connection string can be passed as a string or Unicode object (`connectionstring`), as keywords (`kwargs`), or both.

```
# a string
cnxn = connect('driver={SQL Server};server=localhost;database=test;uid=me;pwd=me2')
```

```
# keywords
cnxn = connect(driver='{SQL Server}', server='localhost', database='test', uid='me', pwd='me2')
```

```
# both
cnxn = connect('driver={SQL Server};server=localhost;database=test', uid='me', pwd='me2')
```

The DB API recommends some keywords that are not usually used in ODBC connection strings, so they are converted:

| **from** | **converted to** |
|:---------|:-----------------|
| host     | server           |
| user     | uid              |
| password | pwd              |

Some keywords are used by pyodbc and are not passed to the odbc driver:

| **keyword** | **description** | **default** |
|:------------|:----------------|:------------|
| autocommit  | If False, Connection.commit must be called; otherwise each statement is automatically commited | False       |
| ansi        | If True, the driver does not support Unicode and SQLDriverConnectA should be used | False       |
| unicode\_results | If True, strings returned in result sets are always Unicode (2.1.5+) | False       |
| readonly    | If True, the connection is set to readonly  | False       |

The ansi keyword should only be used to work around driver bugs.  pyodbc will determine if the
Unicode connection function (SQLDriverConnectW) exists and always attempt to call it.  If the
driver returns IM001 indicating it does not support the Unicode version, the ANSI version is
tried (SQLDriverConnectA).  Any other SQLSTATE is turned into an exception.  Setting ansi to
true skips the Unicode attempt and only connects using the ANSI version.  This is useful for
drivers that return the wrong SQLSTATE (or if pyodbc is out of date and should support other
SQLSTATEs).

For help on connection strings, see [ConnectionStrings](ConnectionStrings.md)

## dataSources ##

dataSources() -> { DSN : Description }

Returns a dictionary mapping available DSNs to their descriptions.