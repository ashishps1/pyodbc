# pyodbc 3.x for Python 3 #

**This is preliminary and subject to change**

## Parameters ##

The following table describes how Python objects passed to Cursor.execute as parameters are
formatted and passed to the driver/database.

| **Python Data Type** | **Description** | **ODBC Data Type** |
|:---------------------|:----------------|:-------------------|
| None                 | varies          | varies (1)         |
| str                  | UCS-2 encoded Unicode | SQL\_WVARCHAR or SQL\_WLONGVARCHAR (2) |
| bytes, bytearray     | binary          | SQL\_VARBINARY or SQL\_LONGVARBINARY (2) |
| bool                 | bit             | BIT                |
| datetime.date        | date            | SQL\_TYPE\_DATE    |
| datetime.time        | time            | SQL\_TYPE\_TIME    |
| datetime.datetime    | timestamp       | SQL\_TIMESTAMP     |
| long                 | bigint          | SQL\_BIGINT        |
| float                | double          | SQL\_DOUBLE        |
| decimal              | numeric         | SQL\_NUMERIC       |

  1. If the driver supports it, SQLDescribeParam is used to determine the appropriate type.  If not supported, SQL\_VARCHAR is used.
  1. SQLGetTypeInfo is used to determine when the LONG types are used.  If not supported by the driver, VARCHAR and WVARCHAR will be 255 and BINARY will be 510.

## Results ##

The following table describes how database results are converted to Python objects.

| **Description**         | **ODBC Data Type**                       | **Python data type** |
|:------------------------|:-----------------------------------------|:---------------------|
| NULL                    | _any_                                    | None                 |
| UCS-2 encoded Unicode   | SQL\_WCHAR, SQL\_WVARCHAR                | str                  |
| ASCII                   | SQL\_CHAR, SQL\_VARCHAR                  | str                  |
| GUID                    | SQL\_GUID                                | str                  |
| XML                     | SQL\_XML                                 | str                  |
| binary                  | SQL\_BINARY && SQL\_VARBINARY            | bytes                |
| decimal, numeric        | SQL\_DECIMAL, SQL\_DECIMAL               | decimal.Decimal      |
| bit                     | SQL\_BIT                                 | bool                 |
| integers                | SQL\_TINYINT, SQL\_SMALLINT, SQL\_INTEGER, SQL\_BIGINT | long                 |
| floating point          | SQL\_REAL, SQL\_FLOAT, SQL\_DOUBLE       | float                |
| time                    | SQL\_TYPE\_TIME                          | datetime.time        |
| SQL Server time         | SS\_TIME2                                | datetime.time        |
| date                    | SQL\_TYPE\_DATE                          | datetime.date        |
| timestamp               | SQL\_TIMESTAMP                           | datetime.datetime    |

# pyodbc 3.x for Python 2 #

**This is preliminary and subject to change**

## Parameters ##

| **Python Data Type** | **Description**         | **ODBC data type** |
|:---------------------|:------------------------|:-------------------|
| None                 | varies                  | varies (1)         |
| unicode              | UCS-2 encoded Unicode   | SQL\_WVARCHAR or SQL\_WLONGVARCHAR (2) |
| str                  | ASCII                   | SQL\_VARCHAR or SQL\_LONGVARCHAR (2)   |
| bytearray (3)        | binary                  | SQL\_VARBINARY or SQL\_LONGVARBINARY (2) |
| buffer               | binary                  | SQL\_VARBINARY or SQL\_LONGVARBINARY (2) |
| bool                 | bit                     | BIT                |
| datetime.date        | date                    | SQL\_TYPE\_DATE    |
| datetime.time        | time                    | SQL\_TYPE\_TIME    |
| datetime.datetime    | timestamp               | SQL\_TIMESTAMP     |
| int                  | integer                 | SQL\_INTEGER       |
| long                 | bigint                  | SQL\_BIGINT        |
| float                | double                  | SQL\_DOUBLE        |
| decimal              | numeric                 | SQL\_NUMERIC       |

  1. If the driver supports it, SQLDescribeParam is used to determine the appropriate type.  If not supported, SQL\_VARCHAR is used.
  1. SQLGetTypeInfo is used to determine when the LONG types are used.  If not supported by the driver, VARCHAR and WVARCHAR will be 255 and BINARY will be 510.
  1. Introduced in Python 2.6

## Results ##

The following table describes how database results are converted to Python objects.

| **Description**         | **ODBC Data Type**                       | **Python data type** |
|:------------------------|:-----------------------------------------|:---------------------|
| NULL                    | _any_                                    | None                 |
| UCS-2 encoded Unicode   | SQL\_WCHAR, SQL\_WVARCHAR                | unicode              |
| ASCII                   | SQL\_CHAR, SQL\_VARCHAR                  | str                  |
| GUID                    | SQL\_GUID                                | str                  |
| XML                     | SQL\_XML                                 | str                  |
| binary                  | SQL\_BINARY, SQL\_VARBINARY              | bytearray (Python 2.6+) or buffer(str) (Python 2.4 & 2.5)|
| decimal, numeric        | SQL\_DECIMAL, SQL\_DECIMAL               | decimal.Decimal      |
| bit                     | SQL\_BIT                                 | bool                 |
| integers                | SQL\_TINYINT, SQL\_SMALLINT, SQL\_INTEGER, SQL\_BIGINT | long                 |
| floating point          | SQL\_REAL, SQL\_FLOAT, SQL\_DOUBLE       | float                |
| time                    | SQL\_TYPE\_TIME                          | datetime.time        |
| SQL Server time         | SS\_TIME2                                | datetime.time        |
| date                    | SQL\_TYPE\_DATE                          | datetime.date        |
| timestamp               | SQL\_TIMESTAMP                           | datetime.datetime    |

# pyodbc 2.1 #

The following table shows the ODBC data types supported and the Python type used to represent
values.  None is always used for NULL values.

| **ODBC**                            | **Python** |
|:------------------------------------|:-----------|
| char varchar longvarchar GUID       | string     |
| wchar wvarchar wlongvarchar         | unicode    |
| smallint integer tinyint            | int        |
| bigint                              | long       |
| decimal numeric                     | decimal    |
| real float double                   | double     |
| date                                | datetime.date |
| time                                | datetime.time |
| timestamp                           | datetime.datetime |
| bit                                 | bool       |
| binary varbinary longvarbinary      | buffer     |
| SQL Server XML type                 | unicode    |