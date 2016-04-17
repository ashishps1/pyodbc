# Read Only #

You can read Excel spreadsheets pretty easily using the Microsoft ODBC driver.  See the [ConnectionStrings](ConnectionStrings.md) document for connection string examples.

Microsoft's documentation says that inserts should be possible, but I have never been able to make them work.  If you can provide a working example, please do!

# Data Types #

The excel driver uses the majority data type in first 8 rows to determine the type of each column.  So if you have 5 numbers and 3 text values in a column, the column will be considered a float column, but the 3 text values will be returned as NULL!