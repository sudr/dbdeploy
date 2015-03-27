# Introduction #

The command line interface is useful when ant is not available. Often, this is how dbdeploy is used when deploying to production databases.

As well as this command line interface, dbdeploy supports an [ant task](UsingTheAntInterface.md) and a [maven plugin](UsingTheMavenPlugin.md).

# Usage #

```
java -cp dbdeploy-cli-3.0-SNAPSHOT.jar com.dbdeploy.CommandLineTarget
```

(replace dbdeploy-cli-3.0-SNAPSHOT.jar with the version of the cli jar you are using. Note that the jar does not support `java -jar` syntax as to use it you need to specify a classpath including your database driver.)

This will display a usage statment:

```
usage: dbdeploy
 -D,--driver <arg>               database driver class
 -d,--dbms <arg>                 dbms type
    --delimiter <arg>            delimiter to separate sql statements
    --delimitertype <arg>        delimiter type to separate sql statements
                                 (row or normal)
 -e,--encoding <arg>             encoding for input and output files
                                 (default: UTF-8)
    --lineending <arg>           line ending to use when applying scripts
                                 direct to db (platform, cr, crlf, lf)
 -o,--outputfile <arg>           output file
 -P,--password <arg>             database password (use -P without a
                                 argument value to be prompted)
 -s,--scriptdirectory <arg>      directory containing change scripts
                                 (default: .)
 -t,--changeLogTableName <arg>   name of change log table to use (default:
                                 changelog)
    --templatedir <arg>          template directory
 -U,--userid <arg>               database user id
 -u,--url <arg>                  database url
```

The parameters match up with those on the ant interface, see UsingTheAntInterface for more information. Currently (3.0M3), the command line interface differs from the ant interface in the following ways:

  * the ant `dir` parameter is called `scriptdirectory` in the command line interface
  * `lastChangeToApply` and `undoOutputFile` are not supported.

[Issue 29](https://code.google.com/p/dbdeploy/issues/detail?id=29) when implemented will address these differences.

Here's an example:

```
java -cp mysql-connector-java-5.1.7-bin.jar:dbdeploy-cli-3.0-SNAPSHOT.jar \
   com.dbdeploy.CommandLineTarget \
   -U dbdeploy \
   -P dbdeploy \
   -D com.mysql.jdbc.Driver \
   -u jdbc:mysql://localhost/dbdeploy \
   -d mysql \
   -o consolidated_script.sql \
   -s dbdeploy-core/example_scripts/
```

This will examine the database to see what change scripts are applied, examine the script source directory to see what is available, then build a script consolidated\_script.sql with the changes to run against the database.

```
Changes currently applied to database:
  (none)
Scripts available:
  1, 2
To be applied:
  1, 2
```

See also the examples/cli-example.sh file for an example shell script.

Just like the ant interface, if you don't specify an output file it will run the scripts directly against the database.

### Specifying the database password ###

Including the database password on the command line can mean it is available to any user via `ps`. To avoid this, include `-P` or `--password` on the command line, you will then be prompted for the password from stdin. In scripts you probably want to pipe the password to dbdeploy:

```
echo password | java -cp mysql-connector-java-5.1.7-bin.jar:dbdeploy-cli-3.0-SNAPSHOT.jar \
   com.dbdeploy.CommandLineTarget \
   -U dbdeploy \
   -P \
   -D com.mysql.jdbc.Driver \
   -u jdbc:mysql://localhost/dbdeploy \
   -d mysql \
   -o consolidated_script.sql \
   -s dbdeploy-core/example_scripts/
```