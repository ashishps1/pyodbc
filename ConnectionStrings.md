# Overview #
The most important thing to know is that pyodbc does not even look at the connection string -- it is passed directly to [SQLDriverConnect](http://msdn.microsoft.com/en-us/library/ms715433.aspx) unmodified.  Therefore any generic ODBC connection string documentation should be valid.  And anything ODBC supports, pyodbc supports, including DSN-less connections and FILEDSNs.

We'll collect useful information here, but connection strings are driver specific.


  * http://www.connectionstrings.com
  * [Important rules for connection strings](http://www.connectionstrings.com/default.aspx?article=what_rules_apply_to_connection_strings)

## DSN-less Connections ##

A DSN is basically a collection of settings for a connection.  Sometimes it is handy to eliminate centralized DSNs and provide the connection settings from your own configuration.  To do this, you simply need to pass all of the settings normally in a DSN to ODBC.  (Note that this works well on Windows.  Recent versions of unixODBC are supposed to support this.  I have not confirmed iODBC yet.)

You choose the driver using the DRIVER keyword.  You then pass all of the other settings, separated by semicolons.  The other setting keywords are driver-specific, but most drivers try to use the same set of keywords, so figuring out the right connection string is usually not too hard.

Here is an example for SQL Server 2000-2008:

> `DRIVER={SQL Server};SERVER=cloak;DATABASE=test;UID=user;PWD=password`

And Microsoft Access:

> `DRIVER={Microsoft Access Driver (*.mdb)};DBQ=c:\\dir\\file.mdb`

(Don't forget to escape your backslashes like I did above or use `r'...'`.)

## Security ##

This obviously differs by database, but most support a user-id and password.  Windows DSNs generally do not store the password, so you will have to do so in your application configuration.  (Since I have to do this anyway, this is why I generally opt for dsn-less connections.)  To provide the password for a DSN connection, append `PWD=xxx`:

> `DSN=myserver;PWD=xxx`

On Windows, some databases support Windows authentication / single sign-on.  This uses the currently running account to logon.  See your driver's documentation for this.  For Microsoft drivers, append `Trusted_Connection=yes`:

> `DSN=myserver;Trusted_Connection=yes`

**Important:** This is generally not a good idea for servers on Windows since they are usually running as a non-standard user.  I recommend using a real user-id and password for server configurations.

# Databases #

## SQL Server ##

### On Windows ###

There are actually two or three SQL Server drivers written and distrubuted by Microsoft: one referred to as "SQL Server" and the other as "SQL Native Client" and "SQL Server Native Client 10.0}".

  * `DRIVER={SQL Server};SERVER=cloak;DATABASE=test;UID=user;PWD=password`
  * `DRIVER={SQL Native Client};SERVER=dagger;DATABASE=test;UID=user;PWD=password`
  * `DRIVER={SQL Server Native Client 10.0};SERVER=dagger;DATABASE=test;UID=user;PWD=password`

The "SQL Server" one works for all versions of SQL Server, but only enables features and data types supported by SQL Server 2000, regardless of your actual server version.

For SQL Server 2005 installations, use "SQL Native Client" to enable 2005 features and types.  Note that this version is not provided in all SQL Server 2008 installations!

Finally, you need "SQL Server Native Client 10.0" for SQL Server 2008 features and types.

Adding the APP keyword allows you to set a name for your application which is very handy for the database administrator:

`APP=MyApp;DRIVER={SQL Server Native Client 10.0};SERVER=dagger;DATABASE=test;UID=user;PWD=password`

### Microsoft's ODBC driver for Linux ###

Microsoft has their own ODBC driver for Linux now.

As of late 2011, it appears to be targeted at RedHat-like distros, but it may be easy enough to move the files.  (They are 64-bit binaries.)  A 32-bit version is expected.

http://msdn.microsoft.com/en-us/library/hh598132(v=SQL.10).aspx

### FreeTDS ###

If you are having trouble, please test against Microsoft's driver (above) if possible.  It would be helpful to determine if problems are in pyodbc or the driver.

Reading VARCHAR fields limited to 255 characters: If you do not set the TDS protocol version it
will default to something old and limit your reads to 255 characters.  Use TDS\_Version=8.0.

You can use this in your connection string or in your /etc/odbc.ini or ~/.odbc.ini files:

<pre>
[freetdstest]<br>
<br>
Driver = /usr/lib64/libtdsodbc.so.0<br>
Description = test<br>
Server = 192.168.1.100<br>
Port   = 1433<br>
TDS_Version = 8.0<br>
</pre>

## MySQL ##

The official reference is here: http://dev.mysql.com/doc/refman/5.1/en/connector-odbc-configuration-connection-parameters.html

### Unicode ###

If you are using UTF8 in your database and are getting results like "\x0038", you probably need to add "CHARSET=UTF8" to your connection string.

### Errors on OS/X ###

Some MySQL ODBC drivers have the wrong socket path on OS/X, causing an error like `Can't connect to local MySQL server through socket /tmp/mysql.sock`.  To connect, determine the correct path and pass it to the driver using the 'socket' keyword.

Run `mysqladmin version` and look for the Unix socket entry:

```
Server version		5.0.67
Protocol version	10
Connection		Localhost via UNIX socket
UNIX socket		/var/lib/mysql/mysql.sock
```

Pass the socket path in the connection string:

cnxn = pyodbc.connect('DRIVER={MySQL};DATABASE=test;SOCKET=/var/lib/mysql/mysql.sock')

## Microsoft Excel ##

The Excel driver does not support transactions, so you will need to use the autocommit keyword:

```
cnxn = pyodbc.connect('DSN=test', autocommit=True)
```

# Operating Systems #

## Windows ##

Obviously Microsoft wrote the ODBC implementation for Windows.  It is now part of the base operating system and many drivers (well Microsoft drivers) are included automatically.  If you are using Windows, you'll find the ODBC support is very good, stable, and fast.

## Unix, Linux ##

There are two different ODBC implementations for Linux: [iODBC](http://www.iodbc.org) and [unixODBC](http://www.unixodbc.org).  If you are having trouble connecting, figure out which you have and include it in your web searches.

The unixODBC drivers do not allow multiple queries at the same time by default.  You must add Threading = 1 to your odbcinst.ini file to enable it.  (Make sure your driver allows it, however!)

http://lists.ibiblio.org/pipermail/freetds/2009q4/025439.html

```
[FreeTDS]
Driver = /path/to/our/libtdsodbc.so
Threading = 1
```

## Mac OS/X ##

[iODBC](http://www.iodbc.org) is apparently standard on Mac OS/X now, so you'll need to become familiar with how it works.

http://www.macsos.com.au/MacODBC/index.html