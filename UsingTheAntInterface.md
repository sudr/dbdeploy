In the supplied distribution is an example ant file, examples/build.xml.  You can probably just [get started using that](GettingStarted.md).

As well as an ant task, dbdeploy supports a [command line interface](UsingTheCommandLineInterface.md) and a [maven plugin](UsingTheMavenPlugin.md).

## Classpath and taskdef ##

You need to declare the dbdeploy task to ant. The dbdeploy task needs to talk to your database, so the task classpath should also include the database drivers, e.g.

```
    <path id="hsql.classpath">
        <fileset dir="lib">
            <include name="hsqldb*.jar"/>
        </fileset>
    </path>

   <path id="dbdeploy.classpath">
        <!-- include the dbdeploy-ant jar -->
        <fileset dir="lib">
            <include name="dbdeploy-ant-*.jar"/>
        </fileset>

        <!-- the dbdeploy task also needs the database driver jar on the classpath -->
        <path refid="hsql.classpath" />

    </path>

```

Then, you can declare the dbdeploy task:

```
    <taskdef name="dbdeploy" classname="com.dbdeploy.AntTarget" classpathref="dbdeploy.classpath"/>
```

## Invoking dbdeploy ##

Next include a call to the dbdeploy task in the relevant database target, for example:

```
    <dbdeploy
        driver="org.hsqldb.jdbcDriver"
        url="jdbc:hsqldb:hsql://localhost/xdb"
        userid="sa"
        password="s3cur1ty"
        dir="db\deltas"
    />
```

There are two different modes in which you can use dbdeploy:

  1. dbdeploy parses your change scripts and applies them directly to the database via a jdbc connection (recommended)
  1. dbdeploy generates a combined output script which you then use a database vendor supplied tool to apply

It's recommended that you usually choose the first "direct to db" mode. Use the "output script" mode only if that doesn't work for you, or if you or your dba want to manually review the generated script before application.

dbdeploy works in "direct to db" mode by default. If you specify an "outputfile" paramater, it will switch to "output script" mode.

The following parameters apply regardless of mode:

|Attribute | Description | Required |
|:---------|:------------|:---------|
|driver | Specifies the jdbc driver | Yes |
|url |Specifies the url of the database that the deltas are to be applied to. |Yes|
|userid |The ID of a dbms user who has permissions to select from the schema version table. |Yes|
|password |The password of the dbms user who has permissions to select from the schema version table. |Yes|
|dir |Full or relative path to the directory containing the delta scripts. |Yes|
|changeLogTableName |The name of the changelog table to use.  Useful if you need to separate DDL and DML when deploying to replicated environments. If not supplied defaults to "changelog"|No|
|lastChangeToApply |The highest numbered delta script to apply. |No|
|undoOutputfile |The name of the undo script that dbdeploy will output. Include a full or relative path. |No|
|encoding | The [character encoding](http://download.oracle.com/javase/6/docs/api/java/nio/charset/Charset.html) used for the input sql files and, if specified, all output files. Defaults to UTF-8 on all platforms.|No|

The following parameters only apply in "direct to db" mode:

|Attribute | Description | Required |
|:---------|:------------|:---------|
|delimiter|Delimiter to use to separate scripts into statements. Default `;`|No|
|delimitertype|either `normal`: split on delimiter wherever it occurs or `row` only split on delimiter if it features on a line by itself. Default `normal`.|No|
|lineending | How to separate lines in sql statements issued via jdbc. The default `platform` uses the appropriate line ending for your platform and is normally satisfactory. However a bug in some oracle drivers mean that the Windows default of CRLF may not always work. See [issue 43](https://code.google.com/p/dbdeploy/issues/detail?id=43), use this parameter if you hit this issue. Supports `platform`, `cr`, `lf`, `crlf`.|No|

Note that delimiter and delimitertype are intentionally the same as the same parameters provided by the [ant sql task](http://ant.apache.org/manual/CoreTasks/sql.html).

The following parameters only apply in "output script" mode:

|Attribute | Description | Required |
|:---------|:------------|:---------|
|outputfile |The name of the script that dbdeploy will output. Include a full or relative path.|No|
|dbms |The target dbms. (In "direct to db" mode, all dbdeploy-generated commands are database agnostic, so this parameter is not required.) |No|
|templatedir| Directory to read customised template scripts from| No|

See GeneratingAndCustomisingScripts for more information on which databases are supported by default and how to customise the generated scripts.
