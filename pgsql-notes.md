Official Docs
=============
* <http://www.postgresql.org/docs/current/static/index.html>
* <http://www.postgresql.org/docs/devel/static/index.html>

Non-official Docs
=================
*   Total security in a PostgreSQL database
    <http://www.ibm.com/developerworks/library/os-postgresecurity/index.html>
*   <https://wiki.archlinux.org/index.php/PostgreSQL>
*   <http://www.yolinux.com/TUTORIALS/LinuxTutorialPostgreSQL.html>

command         | action
-------         | ------
\l              | list databases
\c <db name>    | connect to database
\d              | list tables in database
\d <table-name> | discribe table

- [reverse limit](https://stackoverflow.com/questions/11200808/sql-limit-but-from-the-end)
> SELECT * FROM (SELECT * FROM "SPY" ORDER BY datetime DESC LIMIT 10) X ORDER BY datetime ASC;

- list all dates
> select distinct date(datetime) from "SPY";

*****

<http://www.postgresonline.com/special_feature.php?sf_name=postgresql83_psql_cheatsheet&outputformat=html>
```
This is psql 8.3.5, the PostgreSQL interactive terminal.
Usage: psql [OPTIONS]... [DBNAME [USERNAME]]

General options:

  -c COMMAND
  -d, --dbname=NAME
  -f, --file=FILENAME
  --help
  -l, --list
  -v NAME=VALUE
  --version
  -X

  run only single command (SQL or internal) and exit
  specify database name to connect to (default: "logged in username here")
  execute commands from file, then exit
  show this help, then exit
  list available databases, then exit
  set psql variable NAME to VALUE
  output version information, then exit
  do not read startup file (~/.psqlrc)
```

*****

interactive console
-------------------
```
TYPE:  \copyright                                        for distribution terms
       \h for help with SQL commands                     for help with SQL commands
       \? for help with psql commands                    for help with psql commands
       \g or terminate with semicolon to execute query   or terminate with semicolon to execute query
       \q to quit                                        to quit

GENERAL:
  \c[onnect] [DBNAME|- USER|- HOST|- PORT|-]             connect to new database
  \cd [DIR]                                              change the current working directory
  \encoding [ENCODING]                                   show or set client encoding
  \h [NAME]                                              help on syntax of SQL commands, * for all commands
  \set [NAME [VALUE]]                                    set internal variable, or list all if no parameters
  \timing                                                toggle timing of commands (currently off)
  \unset NAME                                            unset (delete) internal variable
  \prompt [TEXT] NAME                                    prompt user to set internal variable
  \! [COMMAND]                                           execute command in shell or start interactive shell

QUERY BUFFER:
  \e [FILE]                                              edit the query buffer (or file) with external editor
  \g [FILE]                                              send query buffer to server (and results to file or |pipe)
  \p                                                     show the contents of the query buffer
  \r                                                     reset (clear) the query buffer
  \w FILE                                                write query buffer to file

INPUT/OUTPUT:
  \echo [STRING]                                         write string to standard output
  \i FILE                                                execute commands from file
  \o [FILE]                                              send all query results to file or |pipe
  \qecho [STRING]                                        write string to query output stream (see \o)

INFORMATIONAL:
  \d [NAME]                                              describe table, index, sequence, or view
  \d{t|i|s|v|S} [PATTERN] (add "+" for more detail)      list tables/indexes/sequences/views/system tables
  \da [PATTERN]                                          list aggregate functions
  \db [PATTERN]                                          list tablespaces (add "+" for more detail)
  \dc [PATTERN]                                          list conversions
  \dC                                                    list casts
  \dd [PATTERN]                                          show comment for object
  \dD [PATTERN]                                          list domains
  \df [PATTERN]                                          list functions (add "+" for more detail)
  \dF [PATTERN]                                          list text search configurations (add "+" for more detail)
  \dFd [PATTERN]                                         list text search dictionaries (add "+" for more detail)
  \dFt [PATTERN]                                         list text search templates
  \dFp [PATTERN]                                         list text search parsers (add "+" for more detail)
  \dg [PATTERN]                                          list groups
  \dn [PATTERN]                                          list schemas (add "+" for more detail)
  \do [NAME]                                             list operators
  \dl                                                    list large objects, same as \lo_list
  \dp [PATTERN]                                          list table, view, and sequence access privileges
  \dT [PATTERN]                                          list data types (add "+" for more detail)
  \du [PATTERN]                                          list users
  \l                                                     list all databases (add "+" for more detail)
  \z [PATTERN]                                           list table, view, and sequence access privileges (same as \dp)

FORMATTING
  \a                                                     toggle between unaligned and aligned output mode
  \C [STRING]                                            set table title, or unset if none
  \f [STRING]                                            show or set field separator for unaligned query output
  \H                                                     toggle HTML output mode (currently off)
  \pset NAME [VALUE]                                     set table output option
                                                           (NAME := {format|border|expanded|fieldsep|footer|null|numericlocale|recordsep
                                                           |tuples_only|title|tableattr|pager})
  \t                                                     show only rows (currently off)
  \T [STRING]                                            set HTML <table> tag attributes, or unset if none
  \x                                                     toggle expanded output (currently off)

COPY, LARGE OBJECT
  \copy ...                                              perform SQL COPY with data stream to the client host
  \lo_export LOBOID FILE                                 LOBOID FILE
  \lo_import FILE [COMMENT]                              FILE [COMMENT]
  \lo_list
  \lo_unlink LOBOID                                      large object operations
```

connection options:
-------------------
flag                | meaning
----                | -------
-h, --host=HOSTNAME | database server host or socket directory
-p, --port=PORT     | database server port number
-U, --username=NAME | connect as specified database user
-W, --password      | force password prompt (should happen automatically)
-e, --exit-on-error | exit on error, default is to continue
-d DBNAME           | some database


psql automated shell examples
-----------------------------

- restore whole server
> psql --host=localhost --username=someuser -f /path/to/pgdumpall.sql

- Run an sql batch script against a database
> psql -h localhost -U someuser -d somedb -f /path/to/somefile.sql

- Run an sql batch script against a database and send output to file
> psql -h localhost -U someuser -d somedb -f /path/to/scriptfile.sql  -o /path/to/outputfile.txt

- Run a single statement against a db
> psql -U postgres -d pagila -c "CREATE TABLE test(some_id serial PRIMARY KEY, some_text text);"

- Output data in html format
> psql -h someserver -p 5432 -U someuser -d somedb -H -c "SELECT * FROM sometable" -o mydata.html


psql interactive mode
---------------------

- Launch Interactive session
> psql -h localhost -U postgres -d somedb

- View help for SELECT * LIMIT
> \h  SELECT * LIMIT

- List all tables in db with descriptions
> \dt+

- List all tables in db with s in the name
> \dt *s*

- Cancel out of MORE screen
> :q

*****

<http://www.davidpashley.com/articles/postgresql-user-administration/>

##User Administration

####Adding Users

- Synopsis

    - <http://www.postgresql.org/docs/current/static/sql-createuser.html>

    ```
    CREATE USER name [ [ WITH ] option [ ... ] ]

    where option can be:

          SUPERUSER | NOSUPERUSER
        | CREATEDB | NOCREATEDB
        | CREATEROLE | NOCREATEROLE
        | CREATEUSER | NOCREATEUSER
        | INHERIT | NOINHERIT
        | LOGIN | NOLOGIN
        | REPLICATION | NOREPLICATION
        | CONNECTION LIMIT connlimit
        | [ ENCRYPTED | UNENCRYPTED ] PASSWORD 'password'
        | VALID UNTIL 'timestamp'
        | IN ROLE role_name [, ...]
        | IN GROUP role_name [, ...]
        | ROLE role_name [, ...]
        | ADMIN role_name [, ...]
        | USER role_name [, ...]
        | SYSID uid
    ```

- Example: simplest form (no password, no privileges)
    > CREATE USER user;

- More Examples
    > CREATE USER alice WITH ENCRYPTED PASSWORD 'pAssw0rd';
    > CREATE USER dumbot WITH NOLOGIN ENCRYPTED PASSWORD 'dumbbbb';
    > CREATE USER bob VALID UNTIL 'Jan 31 2030';


####Listing Users

> SELECT * FROM pg_shadow;


####Altering Users

- Synopsis

    ```
    ALTER USER name [ [ WITH ] 
        [ ENCRYPTED | UNENCRYPTED ] PASSWORD 'password' 
        | CREATEDB | NOCREATEDB
        | CREATEUSER | NOCREATEUSER 
        | VALID UNTIL 'abstime' ]
    ```

- Examples

    1.  Say we wanted to allow alice to create databases:
        > ALTER USER alice CREATEDB;

    2.  You can also rename users using:
        > ALTER USER name RENAME TO newname;

    3.  To rename bob to colin we could use:
        > ALTER USER bob RENAME TO colin;


####Changing a User Password

- Examples
    > ALTER USER colin WITH PASSWORD 'letmein';
    > ALTER USER de WITH ENCRYPTED PASSWORD 'tingting';

####Show All Users

- Within psql shell
    > SELECT * FROM pg_user;
    > SELECT * FROM pg_shadow;

####Removing Users

- At command line shell
    >  $ dropuser alice

- Within psql shell
    > DROP USER colin;


####[Create Database](http://www.postgresql.org/docs/current/static/sql-createdatabase.html)

- Synopsis

    ```
    CREATE DATABASE name
        [ [ WITH ] [ OWNER [=] user_name ]
            [ TEMPLATE [=] template ]
            [ ENCODING [=] encoding ]
            [ LC_COLLATE [=] lc_collate ]
            [ LC_CTYPE [=] lc_ctype ]
            [ TABLESPACE [=] tablespace_name ]
            [ CONNECTION LIMIT [=] connlimit ] ]
    ```

- Examples

1.  To create a new database:
    > CREATE DATABASE lusiadas;

2.  To create a database sales owned by user salesapp with a default tablespace of salesspace:
    > CREATE DATABASE sales OWNER salesapp TABLESPACE salesspace;

3.  To create a database music which supports the ISO-8859-1 character set:
    > CREATE DATABASE music ENCODING 'LATIN1' TEMPLATE template0;

