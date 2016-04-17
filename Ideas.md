# Performance #

## SQLNumResultCols On Demand ##

Currently, SQLNumResultCols and SQLNumResultCols are both called after every execute.  However, the actual rowcount is very seldom used, so we could call it the first time Cursor.rowcount is accessed.

We'll probably need to call SQLNumResultCols to find out if there are results to fetch, though we could try to eliminate that if we see that the SQL starts with INSERT or UPDATE.