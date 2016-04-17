Exceptions are raised when ODBC errors are detected.  The exception classes specified in the
[DB API specification](http://www.python.org/peps/pep-0249.html) are used.

  * DatabaseError
  * DataError
  * OperationalError
  * IntegrityError
  * InternalError
  * ProgrammingError

When an error occurs, the type of exception raised is based on the SQLSTATE.

| **SQLSTATE**        | **Exception Class** |
|:--------------------|:--------------------|
| 0A000               | NotSupportedError   |
| 22xxx               | DataError           |
| 23xxx 40002         | IntegrityError      |
| 24xxx 25xxx 42xxx   | ProgrammingError    |
| All Others          | DatabaseError       |

A primary key error (attempting to insert a value when the key already exists) will raise an
IntegrityError.