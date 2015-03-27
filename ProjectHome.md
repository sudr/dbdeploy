Manages the deployment of numbered change scripts to a SQL database, using a simple table in the database to track applied changes.

Read the [getting started guide](GettingStarted.md) for an introduction to what dbdeploy can do.

More details can be found at http://www.dbdeploy.com.

# News #

## 16 Mar 2010: 3.0M3 released! ##

This release contains the following changes:

  * [issue 21](https://code.google.com/p/dbdeploy/issues/detail?id=21): explicit specification of character encoding via encoding= parameter. Note that this is a potential **breaking change** as dbdeploy now defaults to UTF-8 encoding instead of the java platform default. (with thanks to Kiril Raychev)
  * [issue 34](https://code.google.com/p/dbdeploy/issues/detail?id=34): implement a maven plugin. See UsingTheMavenPlugin for more information. (with thanks to Jim Bogan and abcoyle)
  * [issue 36](https://code.google.com/p/dbdeploy/issues/detail?id=36): script number > 999 fails due to comma in number for changelog insert statement. (with thanks to the many contributors who pointed out the fix to this schoolboy error :)
  * [issue 43](https://code.google.com/p/dbdeploy/issues/detail?id=43): invalid procedures and functions created in oracle due to windows delimiter / oracle driver issues (with thanks to trevor.urquhart and others on the mailing list)
  * [issue 47](https://code.google.com/p/dbdeploy/issues/detail?id=47): stream not closed in script mode (with thanks to Robert Longson)
  * [issue 51](https://code.google.com/p/dbdeploy/issues/detail?id=51): dbdeploy-core.jar should be included in distribution.zip
  * [issue 52](https://code.google.com/p/dbdeploy/issues/detail?id=52): improve error reporting when a change script fails
  * [issue 53](https://code.google.com/p/dbdeploy/issues/detail?id=53): avoid need to include password on command line
  * [issue 54](https://code.google.com/p/dbdeploy/issues/detail?id=54): support change numbers as long rather than int (with thanks to Jim Bogan)
  * [issue 55](https://code.google.com/p/dbdeploy/issues/detail?id=55): trimming change scripts can make e.g. views and SPs unreadable in vendor supplied tools (with thanks to Jim Bogan)

You can download this release by clicking the Download tab at the top of this page. In addition, for the first time, this release of dbdeploy is hosted on maven central (see http://repo1.maven.org/maven2/com/dbdeploy/).

Barring any showstoppers, the plan is to fix the remaining couple of minor issues labelled for Release 3.0 and release a final 3.0 release in the next couple of weeks. Please try this milestone out and feedback either in the issue tracker here or on the mailing list (http://groups.google.com/group/db-deploy-users).

If you're upgrading from 2.X you should read UpgradingFromVersionTwo because 3.0 contains **breaking changes** including a need to change your changelog table.

Thanks for all of your support, comments and contributions.