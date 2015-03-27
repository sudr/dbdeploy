# Introduction #

## What? ##

dbdeploy is a Database Change Management tool. It’s for developers or
DBAs who want to evolve their database design - or refactor their
database - in a simple, controlled, flexible and frequent manner.

## Why? ##

The recurring problem with database development is that at some point
you’ll need to upgrade an existing database and preserve its content.
In development environments it’s often possible (even desirable) to
blow away the database and rebuild from scratch as often as the code
is rebuilt but this approach cannot be taken forward into more
controlled environments such as QA, UAT and Production.

## How? ##

Drawing from our experiences, we’ve found that one of the easiest ways
to allow people to change the database is by using version-controlled
SQL delta scripts. We’ve also found it beneficial to ensure that the
scripts used to build development environments are the exact same used
in QA, UAT and production. Maintaining and making use of these deltas can
quickly become a significant overhead - dbdeploy aims to address this.

# Try it #

dbdeploy requires java 1.5 or higher.

  1. From [the downloads page](http://code.google.com/p/dbdeploy/downloads/list) download the distribution zip file.
  1. Unzip this somewhere.
  1. Open a command prompt in the example subdirectory of the distribution
  1. Make sure you have [ant](http://ant.apache.org/) installed and on your path
  1. run `ant`
  1. you should see something like:

```
Buildfile: build.xml

drop-and-create-database:
    [mkdir] Created dir: /tmp/dbdeploy/dbdeploy-3.0-SNAPSHOT/example/db

create-changelog-table:
      [sql] Executing resource: /tmp/dbdeploy/dbdeploy-3.0-SNAPSHOT/scripts/createSchemaVersionTable.hsql.sql
      [sql] 2 of 2 SQL statements executed successfully

clean:

update-database:
 [dbdeploy] dbdeploy 3.0M2
 [dbdeploy] Reading change scripts from directory /tmp/dbdeploy-3.0M2/example...
 [dbdeploy] Changes currently applied to database:
 [dbdeploy]   (none)
 [dbdeploy] Scripts available:
 [dbdeploy]   1, 2
 [dbdeploy] To be applied:
 [dbdeploy]   1, 2
 [dbdeploy] Applying #1: 001_create_table.sql...
 [dbdeploy] Applying #2: 002_insert_data.sql...

default:

```

# What's going on here? #

## Understanding the output ##

The example, for simplicity, uses a local file version of [hsqldb](http://hsqldb.org/) that is included in the distribution.  Many other databases are supported by dbdeploy including Oracle, MySql and Microsoft SQL Server.

```
drop-and-create-database:
    [mkdir] Created dir: /tmp/dbdeploy/dbdeploy-3.0-SNAPSHOT/example/db
```

This makes sure the example always starts with a clean database by deleting and recreating the directory.

```
create-changelog-table:
      [sql] Executing resource: /tmp/dbdeploy/dbdeploy-3.0-SNAPSHOT/scripts/createSchemaVersionTable.hsql.sql
      [sql] 2 of 2 SQL statements executed successfully
```

dbdeploy uses a table in your database called `changelog` to track which delta scripts have been successfully applied.  This target runs the script provided in the distribution to create this table.  You will need to do this by hand on any database you want to start using dbdeploy.

```
update-database:
 [dbdeploy] dbdeploy 3.0-SNAPSHOT
 [dbdeploy] Reading change scripts from directory /tmp/dbdeploy/dbdeploy-3.0-SNAPSHOT/example...
 [dbdeploy] Changes currently applied to database:
 [dbdeploy]   (none)
 [dbdeploy] Scripts available:
 [dbdeploy]   1, 2
 [dbdeploy] To be applied:
 [dbdeploy]   1, 2
 [dbdeploy] Applying #1: 001_create_table.sql...
 [dbdeploy] Applying #2: 002_insert_data.sql...
```

This is dbdeploy actually doing its work.  It:

  * Read the entries from the changelog table to which scripts had currently been applied: `Changes currently applied to database: (none)`
  * Scanned the script directory for sql scripts, found the two provided  (`001_create_table.sql` and `002_insert_data.sql`), and parsed the file names to discover their numbers: `Scripts available: 1, 2`
  * Worked out what needed applying: `To be applied: 1, 2`
  * Applied those changes to the database

The bit of ant that made this happen was:

```
    <taskdef name="dbdeploy" classname="com.dbdeploy.AntTarget" classpathref="dbdeploy.classpath"/>

        <dbdeploy driver="${db.driver}" url="${db.url}"
                  userid="sa"
                  password=""
                  dir="."
                />
```

Take a look at the source scripts to see what was included in the scripts.

In versions of dbdeploy prior to 3.0M2, you had to write out a generated script file to a file and then execute that with the database vendor's tool or by using ant's sql task.  You don't need to do this any more, though if you need to do this see GeneratingAndCustomisingScripts.  By default dbdeploy will split your files on ";" to work out which separate jdbc statements to execute; you can use the delimiter and delimitertype parameters to change this - they work just like they do for the [ant sql task](http://ant.apache.org/manual/CoreTasks/sql.html).

(For those who used earlier versions, if you don't specify an output file, dbdeploy will apply the changes for you directly.  Note that when dbdeploy applies changes, it uses standard sql and jdbc so you do not need to specify a database syntax.)


## Run dbdeploy again ##

Run just dbdeploy again, without clearing down the database:

```
$ ant update-database
Buildfile: build.xml

update-database:
 [dbdeploy] dbdeploy 3.0M2
 [dbdeploy] Reading change scripts from directory /tmp/dbdeploy-3.0M2/example...
 [dbdeploy] Changes currently applied to database:
 [dbdeploy]   1, 2
 [dbdeploy] Scripts available:
 [dbdeploy]   1, 2
 [dbdeploy] To be applied:
 [dbdeploy]   (none)

BUILD SUCCESSFUL
Total time: 2 seconds
$ 
```

dbdeploy detects that scripts 1 and 2 have already been applied, so generates a empty script.

## Create a new change script ##

Create a file `003_more_data.sql` with the following content:

```
INSERT INTO Test VALUES (8);
```

Then:
```
$ ant update-database
Buildfile: build.xml

update-database:
 [dbdeploy] dbdeploy 3.0-SNAPSHOT
 [dbdeploy] Reading change scripts from directory /tmp/dbdeploy/dbdeploy-3.0-SNAPSHOT/example...
 [dbdeploy] Changes currently applied to database:
 [dbdeploy]   1, 2 
 [dbdeploy] Scripts available:
 [dbdeploy]   1..3
 [dbdeploy] To be applied:
 [dbdeploy]   3
 [dbdeploy] Applying #3: 003_more_data.sql...

BUILD SUCCESSFUL
Total time: 1 second
$
```

As you see, just the new change script is applied.

# What next? #

  * Study the example ant, shell, maven and sql scripts - we've tried to provide as much information in those as possible so they should be enough to get you started.
  * See GuidelinesForUsingDbdeploy for more information about using dbdeploy in real environments.
  * See http://www.dbdeploy.com for more information about the background to dbdeploy