# Introduction #

The maven plugin is designed for people who use [Apache Maven](http://maven.apache.org/) as a build tool.

As well as this maven plugin, dbdeploy supports an [ant task](UsingTheAntInterface.md) and a [command line interface](UsingTheCommandLineInterface.md).

# Usage #

The maven plugin was introduced in version 3.0M3. It is published to maven central.

Example pom.xml:

```

<project>

...

     <build>
        <plugins>
            <plugin>
                <groupId>com.dbdeploy</groupId>
                <artifactId>maven-dbdeploy-plugin</artifactId>
                <version>3.0M3</version>

                <configuration>
                    <scriptdirectory>.</scriptdirectory>
                    <driver>org.hsqldb.jdbcDriver</driver>
                    <url>jdbc:hsqldb:file:db/testdb;shutdown=true</url>
                    <userid>sa</userid>
                    <password></password>
                    <dbms>hsql</dbms>
                    <delimiter>;</delimiter>
                    <delimiterType>row</delimiterType>
                </configuration>

                <dependencies>
                    <dependency>
                        <groupId>hsqldb</groupId>
                        <artifactId>hsqldb</artifactId>
                        <version>1.8.0.7</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>

  ...

</project>

```

The parameters match up with those on the ant interface, see UsingTheAntInterface for more information. Currently (3.0M3), the maven parmaters differ from the ant interface in the following ways:

  * the ant `dir` parameter is called `scriptdirectory` in the maven plugin

The maven plugin supports the following goals:

  * db-scripts: executes dbdeploy in "output file" mode
  * update: executes dbdeploy in "direct to database" mode

So, you can use `mvn dbdepoy:update` in a directory with a configured pom to apply updates to the database. As with any plugin you can [configure a dbdeploy goal to execute automatically in any maven lifecycle phase](http://maven.apache.org/pom.html#Plugins). None of its goals bind to a lifecycle phase by default.

You can use `mvn help:describe -Dplugin=com.dbdeploy:maven-dbdeploy-plugin -Ddetail` to get full plugin documentation.


### Special note for the 3.0M3 release ###

Unfortunately the pom.xml in the examples directory of the distribution is incorrect, see [issue 56](https://code.google.com/p/dbdeploy/issues/detail?id=56) for details.

Specifically, you need to replace the version of 3.0-SNAPSHOT with 3.0M3  for it to work. Apologies.



