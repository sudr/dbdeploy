# Introduction #

Version 3.0 of dbdeploy changes the scripts generated to improve behaviour in failure conditions.  See [issue 12](https://code.google.com/p/dbdeploy/issues/detail?id=12) for more information as to why this change was made.

For recovery to work automatically, you need to ensure that change scripts:

  1. Do not include any transaction handling - dbdeploy wraps the script in a transaction for you
  1. Only contain one DDL statement (CREATE TABLE, ALTER etc) if your database cannot process DDL transactionally (most can't)

The second requirement may seem excessive.  However, if provides a great failure recovery process: if the script fails, just fix the problem and re-run dbdeploy.  No further manual hacking is required.


In addition, the changelog table has been simplified to remove the deltaSet concept - if you need this feature, specify a different changelog table instead.  See [issue 19](https://code.google.com/p/dbdeploy/issues/detail?id=19) for a discussion.

As a result, when you upgrade to 3.0 you will need to make a change to your changelog table.

# Details #

The changelog table used to look like:

```
CREATE TABLE changelog (
  change_number INTEGER NOT NULL,
  delta_set VARCHAR(10) NOT NULL,
  start_dt TIMESTAMP NOT NULL,
  complete_dt TIMESTAMP NULL,
  applied_by VARCHAR(100) NOT NULL,
  description VARCHAR(500) NOT NULL
);

ALTER TABLE changelog ADD CONSTRAINT Pkchangelog PRIMARY KEY (change_number, delta_set)
```

Changelog no longer has a start\_dt or delta\_set.  You may, if you wish, mark complete\_dt as not null, because dbdeploy will only insert into this table when the change script has been fully applied.

So it now should look like:

```

CREATE TABLE changelog (
  change_number INTEGER NOT NULL,
  complete_dt TIMESTAMP NOT NULL,
  applied_by VARCHAR(100) NOT NULL,
  description VARCHAR(500) NOT NULL
);

ALTER TABLE changelog ADD CONSTRAINT Pkchangelog PRIMARY KEY (change_number)
```