[py2exe](http://py2exe.org) is a very handy tool for creating executables from Python code. It even scans you program (using a modified version of modulefinder) to figure out which modules from the standard library to include. Unfortunately, it only scans Python code, not C++ code, so it cannot determine what code pyodbc imports. You'll need to tell py2exe to include decimal and datetime.

To do so, add includes to the py2exe options dictionary in your setup script like so:

```
 
setup(
  options = { "py2exe": { "includes": "decimal, datetime" } }
  ...
)
```

If you are trying to reduce your application size, you can leave these out if you don't actually read these data types from the database, but make **sure** that's the case.  If customers can add columns to your database, they may add these data types.