# Introduction #

By default dbdeploy will apply changes directly to the database.  This is the recommended way of using dbdeploy.

However, you can get dbdeploy to generate scripts that you then separately apply to the database.  You might want to do this because:

  * you or your dba wants to review the changes before they get applied
  * you want to use database features not available to a jdbc connection
  * your scripts are too complicated to split into statements using dbdeploy's deliberately simple splitting options (delimiter and delimitertype)
  * you want to use undo scripts (this is currently only supported using script generation - see [issue 33](https://code.google.com/p/dbdeploy/issues/detail?id=33))

# Enabling Script Generation #

To generate scripts, specify an "outputfile" or an "undoOutputfile".  If you do this you then need to specify the database type using the "dbms" paramater:

| ora | Oracle |
|:----|:-------|
| syb-ase | Sybase ASE |
| hsql | Hypersonic SQL |
| mssql | MS SQL Server |
| mysql | MySQL |
| db2 | DB2 |
| pgsql | Postgres |

> Note: you don't need to specify a dbms type when dbdeploy is applying changes directly: it uses standard sql and jdbc and _should_ therefore work with any database.  [Raise a bug](http://code.google.com/p/dbdeploy/issues/list) if it doesn't.

# Customising Script Generation #

dbdeploy uses simple [freemarker](http://freemarker.org/) templates to generate the scripts.  You can see the ones provided at http://code.google.com/p/dbdeploy/source/browse/#svn/trunk/dbdeploy-core/src/main/resources.

You can also supply your own.  Probably the easiest thing to do is take one of the included ones and change it.  Use the "templatedir" parameter to point to a directory containing your modified templates. dbdeploy will look for files called `{dbms}_apply.ftl` and `{dbms}_undo.ftl` in that directory, where `{dbms}` is what you specified for the dbms parameter.

If you do add support for another mainstream database, or if you have to correct the included scripts, please tell us about it either by [raising an issue](http://code.google.com/p/dbdeploy/issues/list) or [posting to the users list](http://groups.google.com/group/db-deploy-users).