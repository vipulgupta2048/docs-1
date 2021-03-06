---
title: OWNER TO
summary: The OWNER TO subcommand changes the owner of an object.
toc: true
---

<span class="version-tag">New in v20.2</span>: `OWNER TO` is a subcommand of [`ALTER DATABASE`](alter-database.html), [`ALTER TABLE`](alter-table.html), [`ALTER SCHEMA`](alter-schema.html), and [`ALTER TYPE`](alter-type.html), and is used to change the owner of an object in a cluster.

{{site.data.alerts.callout_info}}
This page documents `ALTER DATABASE ... OWNER TO` and `ALTER TABLE ... OWNER TO`. For details on the `ALTER SCHEMA ... OWNER TO` and `ALTER TYPE ... OWNER TO`, see the [`ALTER SCHEMA`](alter-schema.html) and [`ALTER TYPE`](alter-type.html) pages.
{{site.data.alerts.end}}

## Required privileges

- To change the owner of a database, the user must be the current owner of the database, and a member of the new owner [role](authorization.html#roles). The user must also have the `CREATEDB` [privilege](authorization.html#assign-privileges).
- To change the owner of a table, the user must be the current owner of the table, and a member of the new owner [role](authorization.html#roles). The new owner role must also have the `CREATE` [privilege](authorization.html#assign-privileges) on the schema to which the table belongs.

## Syntax

### Databases

~~~
ALTER DATABASE <name> OWNER TO <newowner>
~~~

### Tables

~~~
ALTER TABLE <name> OWNER TO <newowner>
~~~

## Parameters

Parameter | Description
----------|------------
`name` | The name of the table or database.
`newowner` | The name of the new owner.

## Examples

{% include {{page.version.version}}/sql/movr-statements.md %}

### Change a database's owner

Suppose that you want to change the owner of the `movr` database to a new user named `max`.

{% include copy-clipboard.html %}
~~~ sql
> CREATE USER IF NOT EXISTS max WITH PASSWORD "roach";
~~~

To change the owner of a database, the current owner (in this case, `root`) must belong to the role of the new owner (in this case, `max`):

{% include copy-clipboard.html %}
~~~ sql
> GRANT max TO root;
~~~

{% include copy-clipboard.html %}
~~~ sql
> ALTER DATABASE movr OWNER TO max;
~~~

To verify that the owner is now `max`, query the [`pg_catalog.pg_database` and `pg_catalog.pg_roles` tables](sql-name-resolution.html#databases-with-special-names):

{% include copy-clipboard.html %}
~~~ sql
> SELECT rolname FROM pg_catalog.pg_database d JOIN pg_catalog.pg_roles r ON d.datdba = r.oid WHERE datname = 'movr';
~~~

~~~
  rolname
-----------
  max
(1 row)
~~~

### Change a table's owner

Suppose that you want to change the owner of the `rides` table to a new user named `max`.

{% include copy-clipboard.html %}
~~~ sql
> CREATE USER IF NOT EXISTS max WITH PASSWORD "roach";
~~~

To change the owner of a table, the current owner (in this case, `root`) must belong to the role of the new owner (in this case, `max`):

{% include copy-clipboard.html %}
~~~ sql
> GRANT max TO root;
~~~

{% include copy-clipboard.html %}
~~~ sql
> ALTER TABLE promo_codes OWNER TO max;
~~~

To verify that the owner is now `max`, query the [`pg_catalog.pg_tables` table](sql-name-resolution.html#databases-with-special-names):

{% include copy-clipboard.html %}
~~~ sql
> SELECT tableowner FROM pg_catalog.pg_tables WHERE tablename = 'promo_codes';
~~~

~~~
  tableowner
--------------
  max
(1 row)
~~~

## See also

- [`CREATE DATABASE`](create-database.html)
- [`SHOW DATABASES`](show-databases.html)
- [`CREATE TABLE`](create-table.html)
- [`SHOW TABLES`](show-tables.html)
- [Other SQL Statements](sql-statements.html)
