# Introduction #

Read GettingStarted for an overview of what dbdeploy does technically.  This page contains guidelines to help you get the right techniques and processes around dbdeploy.  These guidelines are based on over two years of use of dbdeploy on a mission critical project.

## Guidelines ##

  1. Treat all database code like any other source code - store it under version control
  1. Follow all the normal continuous integration rules: test before checkin, apply your change scripts to a database on a continuous integration server
  1. Once a delta script has been checked it don't modify it - if it's wrong or needs enhancing write a new change script that fixes it.
  1. Don't put any transaction handling in your change scripts. dbdeploy wraps your change script in a transaction.
  1. If your change script includes DDL (CREATE, ALTER etc) **only include one DDL statement in each script**.  You can break this rule if you must, but it makes recovery much more complicated as if there is an error applying the script you must manually undo the previous steps.  Multiple DML statements (UPDATE, INSERT etc) are ok as they are run transactionally.
  1. Make sure that **every** database modification is written as a delta script to be picked up by dbdeploy.  If you manually hack in changes you will then have problems keeping all your databases in sync causing errors later.
  1. Follow the naming convention for delta scripts. Script names must begin with a number that indicates the order in which it should be run (1.sql gets run first, then 2.sql and so on). It's strongly recommended to use the rest of the filename to describe what the script does, e.g. `001_create_test_table.sql`
  1. You can optionally add an undo section to your script. Write the script so it performs the do action first. Once all do actions have been scripted include the token –//@UNDO on a new line. Include the undo steps after this token.  NB: this feature isn't as useful as it sounds as in the author's experience many scripts are not easily undoable (DROP TABLE for example).  If you choose to use this feature make sure you have a comprehensive test process to prove the undo scripts actually work.

## Example ##

A developer needs to modify the database by adding a new table called foo.  First, they find the next available change script number.  You can just pick the next highest number after the list in source control if you are a small project with low concurrency.  We use a (physical) clipboard where developers sign up for numbers.

The clipboard shows the next number to use is 3.

Create a file called 3\_create\_foo\_table.sql:

```
CREATE TABLE FOO (
FOO_ID INTEGER NOT NULL
,FOO_VALUE VARCHAR(30)
)
;

ALTER TABLE FOO ADD CONSTRAINT PK_FOO PRIMARY KEY (FOO_ID)
;
```


Next day, the requirements change it becomes necessary to add a FOO\_DATE column to the FOO table.

If you remember nothing else from this page, remember this: **do not go back and edit 3\_create\_foo\_table.sql**.  Other developers, and perhaps staging environments or even production, will have got your change script 3 you committed yesterday. dbdeploy will have applied it. Therefore dbdeploy will now never re-apply change script 3 again to those environments.

In most circumstances the answer to amending what’s been done in a delta script is to just write another delta script.

So, write a new change script, 4\_add\_date\_to\_foo\_table.sql:

```
-- Story400 - late-breaking requirement, need FOO_DATE column.

ALTER TABLE FOO ADD (
FOO_DATE DATE NULL)
;
```

Many complain that this means there is no longer a single script that describes what the database looks like.  Use the tools provided with your database or one of many third party tools to do this.  [SchemaSpy](http://schemaspy.sourceforge.net/) is one such tool that is a great way of allowing people you explore your schema.

If you have no valuable data in your schema and can always drop and recreate on every release / deploy, then just maintain create scripts that you go back and edit.  dbdeploy probably won't do much for you.

## When can I edit a change script? ##

If you commit a change script that has syntax errors or doesn't work in some environments, then your only solution is to edit it.  When you do this, you have to manually fixup all environments to which it **has** been deployed.

Generally, this happens because of a failure to run the scripts before checkin, or poor synchronisation between environments.  Focus on putting processes in place to address these problems.