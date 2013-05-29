mapsql
======

A tool for running SQL statements against multiple tables

It only really makes sense to use non-SELECT statements; the
tool does not return results.


Operation
---------

First, a list of tables to be affected is built. By default, this
is all tables and views in the database, unless restricted by the '-t'
option.

A dictionary of key value pairs is assembled from the command line
you specify. The keys may be used in your SQL statement template;
the values are substituted for the keys just before the statement
is executed for each table. A special key, `table', is set to the
name of each table in turn, whereas the other key value pairs
remain constant.

This allows you to specify statements such as:

  ALTER TABLE %(table) ADD COLUMN %(my_col) INTEGER;

And then wrap this in a bash script thus:

  for column in age1 age2 age3
  do
    mapsql 'ALTER TABLE %(table) ADD COLUMN %(my_col) INTEGER;' my_col=$column
  done

which if run against table1 and table2 will yield the following six statements:

  ALTER TABLE table1 ADD COLUMN age1 INTEGER;
  ALTER TABLE table2 ADD COLUMN age1 INTEGER;
  ALTER TABLE table1 ADD COLUMN age2 INTEGER;
  ALTER TABLE table2 ADD COLUMN age2 INTEGER;
  ALTER TABLE table1 ADD COLUMN age3 INTEGER;
  ALTER TABLE table2 ADD COLUMN age3 INTEGER;

The format for the SQL statement template is normal SQL; to use a
variable from the dictionary, put its name in parentheses and
precede it with a % sign.

The constant values in the dictionary should be in a set of words
after the SQL statement, in the form key1=value1 key2=value2 .

It is advisable to quote MySQL table names lest they contain
characters significant to SQL. Note that the two ways of
doing this: ` and " are both significant to bash and must
be handled with care.


Options
-------

The '-t' option allows you to specify part of a WHERE-clause
to limit which tables will get used. The argument to '-t' is
appended to "WHERE table_name", so you can do (e.g.,):

 mapsql ... -t "LIKE '%_image'"

or

 mapsql ... -t "IN (SELECT tbl from my_list_of_table_names)"

The '-d' option lets you set the name of the database to work on; this
will generally limit the tables to which your SQL statements will
be applied.

The '--host' option allows you to override the default database (upp-dun-db2)

The '-p' option allows you to override the default password

The '-u' option allows you to set the user

The '-v' option prints the substantive SQL statements the tool generates

The '-n' option triggers a dry-run; no actual changes are made to the
database.

In combination, '-v' and '-n' will safely show what would happen, without
doing anything.
