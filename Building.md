# Overview #

## Obtaining Source ##

The source for released versions are provided in zip files on the [Downloads](http://code.google.com/p/pyodbc/downloads/list) page.

If you are going to work on pyodbc (changes are welcome!), use the git repository on [github](http://github.com/mkleehammer/pyodbc).  (Even better, create a fork on github so I can easily pull changes from your fork.)

If you want an unreleased version from github but don't have git installed, choose the branch and commit you want and use the download button.  It will offer you a tar or zip file.

## Compiling ##

pyodbc is built using [distutils](http://docs.python.org/library/distutils.html), which comes with Python.  If you have the appropriate C++ compiler installed, run the following in the pyodbc source directory:
```
python setup.py build
```

To install after a successful build:
```
python setup.py install
```

## Version ##

pyodbc uses git tags in the format _major_._minor_._patch_ for versioning.  If the commit being built has a tag, such as "2.1.1", then that is used for the version.  If the commit is not tagged, then it is assumed that the commit is a beta for an upcoming release.  The patch number is incremented, such as "2.1.2", and the number of commits since the previous release is appended: 2.1.2-beta3.

If you are building from inside a git repository, the build script will automatically determine the correct version from the tags (using [git describe](http://www.kernel.org/pub/software/scm/git/docs/git-describe.html)).

If you downloaded the source as a zip from the [Downloads](http://code.google.com/p/pyodbc/downloads/list) page, the version will be in a PGK-INFO text file.  The build script will read the version from the text file.

If the version cannot be determined for some reason, you will see a warning about this and the script will build version 2.1.0.  Do not distribute this version since you won't be able to track its actual version!

# Operating Systems #

## Windows ##

On Windows, you will need the appropriate Microsoft Visual C++ compiler.  To build Python 2.4 or 2.5 versions, you will need the Visual Studio 2003 .NET compiler.  Unfortunately there is no free version of this.

For Python 2.6 and above, you can use the free [Visual C++ 2008 Express compiler](http://www.microsoft.com/express/vc).  (Do not use the 2010 version!  You need to use the version that your Python distribution was built with.)

You can create a Windows installer using:
```
python setup.py bdist_wininst
```

## Other ##

To build on other operating systems, use the gcc compiler.

On Linux, pyodbc is typically built using the unixODBC headers, so you will need unixODBC and its headers installed.  On a RedHat/CentOS/Fedora box, this means you would need to install unixODBC-devel:

```
yum install unixODBC-devel
```