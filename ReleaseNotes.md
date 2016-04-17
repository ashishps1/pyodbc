## 3.0.7 - 2013-05-19 ##

Added context manager support to Cursor

Added padding for driver bugs writing an extra byte

Cursor.executemany now accepts an iterator or generator.

Compilation improvements for FreeBSD, Cygwin, and OS/X

Use SQL\_DATA\_AT\_EXEC instead of SQL\_DATA\_LEN\_AT\_EXEC when possible for driver compatibility.

Row objects can now be pickled.

# 3.0.6 - 2012-06-25 #

Added Cursor.commit() and Cursor.rollback(). It is now possible to use only a cursor in your code instead of keeping track of a connection and a cursor.

Added readonly keyword to connect. If set to True, SQLSetConnectAttr SQL\_ATTR\_ACCESS\_MODE is set to SQL\_MODE\_READ\_ONLY. This may provide better locking semantics or speed for some drivers.

Fixed an error reading SQL Server XML data types longer than 4K.

# 3.0.5 - 2012-02-23 #
Fixed "function sequence" errors caused by prepared SQL not being cleared ("unprepared") when a catalog function is executed.

# 3.0.4 - 2012-01-13 #

Fixed building on Python 2.5. Other versions are not affected.

# 3.0.3 - 2011-12-28 #

Update to build using gcc 4.6.2: A compiler warning was changed to an error in 4.6.2.

# 3.0.2 - 2011-12-26 #

This is the first official pyodbc release that supports both Python 2 and Python 3 (3.2+).  Merry Christmas!

Many updates were made for this version, particularly around Unicode support.  If you have outstanding issues from the 2.1.x line, please retest with this version.

If you are on Linux and connecting to SQL Server, you may also be interested in Microsoft's new Linux ODBC driver: http://www.microsoft.com/download/en/details.aspx?id=28160  Check your bitness - a Google search may be required ;)


# 2.1.12 - 2011-10-17 #

Prepared SQL not cleared out when Cursor.tables/columns etc. used.  Causes an error if SQL is executed with parameters, followed by Cursor.tables, followed by same SQL.  The cursor believes the SQL is still prepared and does not do so, causing a "function sequence" error or similar.

# 2.1.11 - 2011-09-13 #

[Issue 192](http://code.google.com/p/pyodbc/issues/detail?id=192): Source distribution was missing a file (pyodbcconf).

# 2.1.10 - 2011-09-12 #

[Issue 185](http://code.google.com/p/pyodbc/issues/detail?id=185): Important memory leak fix.  Another patch from l..@deller.id.au.  Thanks again!

[Issue 199](http://code.google.com/p/pyodbc/issues/detail?id=199): Fixed support for Python `with` statement.

# 2.1.9 - 2011-07-28 #

Important fix for failed LONGVARCHAR/LONGWVARCHAR insert/update fix.  Most drivers were working
around an incorrect length that was being passed by pyodbc (such as SQL Server), but some
drivers correctly raised an error.

[Issue 182](http://code.google.com/p/pyodbc/issues/detail?id=182): Installer updated for proper Vista/Windows 7 UAC support.  Most installations seemed to work
with 2.1.8, but some crashed.

Added ''pyodbc.drivers()'' function for Windows builds.

[Issue 121](http://code.google.com/p/pyodbc/issues/detail?id=121): Update with large
parameter + no rows updated = error.  Issue and patch (!) from l...@deller.id.au.  Thanks.

[Issue 188](http://code.google.com/p/pyodbc/issues/detail?id=188): FreeTDS crash when fetching
NVARCHAR(MAX) data over 511 bytes.

[Issue 129](http://code.google.com/p/pyodbc/issues/detail?id=129): ("select ?", None) fails

Some build fixes for Solaris and for Python 2.4.  Eliminated many compiler warnings.  Added
SQLite tests.

# 2.1.8 - 2010-09-06 #

A major release for Mac OS/X, Unicode, and 64-bit issues, plus a few suggestions.  Thanks for the reports and suggestions.

Unicode handling has been completely reworked, fixing a number of issues on platforms where the size of a SQLWCHAR did not match the size of a Python Unicode character.  This includes Mac OS/X and most 64-bit Linux distributions.

[Issue 100](http://code.google.com/p/pyodbc/issues/detail?id=100): Added context manager support so connections can now be used with Python's `with` statement.

[Issue 101](http://code.google.com/p/pyodbc/issues/detail?id=101): Added `timeout` keyword parameter to the connection.

Users can now read custom SQL types.  A user function can be attached to a connection via the new Connection.add\_output\_converter for a SQL type (an integer defined by ODBC).  When a column of this type is read, the user function will be called to convert the value to a Python object.  Connection.clear\_output\_converters can be called to remove all output converters from a connection.

[Issue 84](http://code.google.com/p/pyodbc/issues/detail?id=84): Data truncation was silently ignored.  Any data that required SQLPutData could fail without an exception.

Related to this, the maximum varchar, wvarchar, and binary sizes are retrieved during the connect and these are used to decide when to write a parameter as a single value and when to use SQLPutData.  This should improve performance for larger inserts.  For example, SQL Server string inserts between 501 and 4000 bytes should now be better.

[Issue 91](http://code.google.com/p/pyodbc/issues/detail?id=91): Decimal parameter handling was completely reworked, which also fixed an error if the size of a decimal exactly matched the database's maximum.

# 2.1.7 - 2010-02-19 #

Thanks for the many reports, suggestions, and especially patches.

[Issue 80](http://code.google.com/p/pyodbc/issues/detail?id=80): setup.py uses setuptools if available to allow building eggs

[Issue 82](http://code.google.com/p/pyodbc/issues/detail?id=82): Occassional crash if the cursor was freed after the connection.

Fix: Writing NULL to binary columns only worked for first binary column in SQL statement.

[Issue 67](http://code.google.com/p/pyodbc/issues/detail?id=67): More cursor.description elements are populated.

[Issue 57](http://code.google.com/p/pyodbc/issues/detail?id=57): pyodbc Row objects can be passed as parameters to execute.

# 2.1.6 - 2009-05-24 #

<font color='red'>Important Fix:</font> Connections with autocommit turned on would not actually be closed!  (By default, connections are opened with autocommit turned off; you must set autocommit to True to see this bug.)

Applied the [patch from john.chandler.lists (Issue 48)](http://code.google.com/p/pyodbc/issues/detail?id=48&can=1) to add support for the SQL Server XML data type.  (The patch included unit tests too.  Very nice.)

# 2.1.5 - 2009-04-19 #

I'm trying to verify as many fixes as possible before releasing, but it should be soon.  In particular, I need some Mac OS/X testing.

## New timeout attribute ##

Applied the excellent [patch from patrik.simons (Issue 47)](http://code.google.com/p/pyodbc/issues/detail?id=47) which adds a timeout attribute to the connection.  The timeout is applied to all subsequently created cursors.

## Fixed Cursor.execute return value ##

<font color='red'><b>WARNING:</b> This is not backwards compatible!</font>

The return value from Cursor.execute stated that the return value was always the cursor itself, but there were 2 cases where the rowcount would be returned instead.  This has been corrected -- the Cursor is always returned now.

If you have code that expected the return value to be an integer row count, you'll need to update your code from:

```
# WRONG
if cursor.execute("delete from tbl where x=1"):
    # do something if deleted
```

to

```
# RIGHT
if cursor.execute("delete from tbl where x=1").rowcount:
    # do something if deleted
```

This is probably rare.  The return for SELECT statements was always the cursor, so there is no change there.

## [Issue 36](http://code.google.com/p/pyodbc/issues/detail?id=36): Option to force string results to Unicode ##

Included a fantastic patch from patrik.simons that adds a unicode\_results Boolean keyword to connect which causes all result strings to be Unicode:

```
cnxn = pyodbc.connect(cnxnstring, unicode_results=True)
```


## Other Changes ##

  * [Issue 35](http://code.google.com/p/pyodbc/issues/detail?id=35): A patch to enable building pyodbc with mingw32 (gcc on Windows).
  * [Issue 39](http://code.google.com/p/pyodbc/issues/detail?id=39): Fixed cursor.skip (would only skip 1 in past)
  * [Issue 40](http://code.google.com/p/pyodbc/issues/detail?id=40): Fixed the "finally pops bad exception" problem in Python 2.4 (finally)
  * [Issue 22](http://code.google.com/p/pyodbc/issues/detail?id=22): Added README.rst to source distribution.  (Lost when renamed from README.txt to README.rst for [github](http://github.com/mkleehammer/pyodbc).)
  * Added Excel unit tests
  * Fixed Access unit tests so they run under Python 2.4
  * [Issue 45](http://code.google.com/p/pyodbc/issues/detail?id=45): Crash when passing sets to executemany.

# 2.1.4 #

[Issue 19](http://code.google.com/p/pyodbc/issues/detail?id=19): New code did not build on 64-bit Linux due to incorrect parameter definitions (SQLINTEGER instead of SQLLEN, etc.)

Because this is a 64-bit only fix, the 2.1.3 Windows installers can still be used.

Thanks to axel.b.k...@web.de for the patch!

# 2.1.3 #

[Issue 18](http://code.google.com/p/pyodbc/issues/detail?id=18): Added optional keyword support to pyodbc.connect to better match the DB API.  See the documentation for connect on the [Module](Module.md) page.

[Issue 14](http://code.google.com/p/pyodbc/issues/detail?id=14): Reading decimals failed if the locale used anything other than a period as the decimal point.

[Issue 11](http://code.google.com/p/pyodbc/issues/detail?id=11): GIL now released around all ODBC calls to free up other threads.

[Issue 16](http://code.google.com/p/pyodbc/issues/detail?id=16): Added [Cursor.skip](Cursor.md) method.

[Issue 17](http://code.google.com/p/pyodbc/issues/detail?id=17): Implemented phase 1 of the performance enhancements.