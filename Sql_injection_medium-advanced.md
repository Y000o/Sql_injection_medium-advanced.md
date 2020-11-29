
# INYECCIÓN SQLi - Medio - Avanzado 
## TEMAS

* [Antes de comenzar](#Antes-de-comenzar)
* [Inyecciónes sqli en diferentes bases de datos](#Inyecciónes-sqli-en-diferentes-bases-de-datos)
    * [MySQL](#MySQL)
      * [Bases de datos por defecto](#Bases-de-datos-por-defecto)
      * [Version de base de datos](#Version-de-base-de-datos)
      * [Credenciales por defecto](#Credenciales-por-defecto)
      * [Base de datos actual](#Base-de-datos-actual)
      * [Server Hostname ](#Server-Hostname)
    * [MySSQL](#MySSQL)
      * [Bases de datos por defecto](#Bases-de-datos-por-defecto)
      * [Version de base de datos](#Version-de-base-de-datos)
      * [Credenciales por defecto](#Credenciales-por-defecto)
      * [Base de datos actual](#Base-de-datos-actual)
      * [Server Hostname ](#Server-Hostname)
    * [Oracle](#Oracle)
      * [Bases de datos por defecto](#Bases-de-datos-por-defecto)
      * [Version de base de datos](#Version-de-base-de-datos)
      * [Credenciales por defecto](#Credenciales-por-defecto)
      * [Base de datos actual](#Base-de-datos-actual)
      * [Server Hostname ](#Server-Hostname)
* [Tipos de payloads](#Tipos-de-payloads)
  * [Boolean-based blind](#Boolean-based-blind)
  * [Error-based](#Error-based)
  * [Union query-based](#Union-query-based)
  * [Stacked queries](#Stacked-queries)
  * [Time-based blind](#Time-based-blind)
  * [Inline queries](#Inline-queries)
   
      
## Antes de comenzar

Este escrito esta pensado como una continuación directa a mi anterior lectura de inyecciones sqli en donde se explican de manera un poco más general las bases de esta vulnerabilidad , por lo que si no estas al tanto de ella te recomiendo que primero le des un repaso antes de empezar esta lectura, ya que este escrito será un poco más avanzado.


## Inyecciónes sqli en diferentes bases de datos

Vamos a empezar aclarando que una inyección sql no es igual en todas las bases de datos, si bien pueden ser un poco parecidas no son del todo igual, esto se debe a que cada base de datos tiene su propia sintaxis. 

Para dar un ejemplo de esto voy a tomar 3 bases de datos diferentes y trataré de mostrar las diferencias por separado, las bases de datos que vamos a ver son:

```
MySQL
MSSQL
ORACLE
```

La informacion que vamos a recopilar será la siguiente:

```

Bases de datos por defecto.
Conocer la version de la base de datos.
Bases de datos que contienen las credenciales por defecto.
Conocer la base de datos en la que estamos.
Conocer el Hostname del servidor.
Tablas y columnas.
Conocer los privilegios.

```

### MySQL

#### Bases de datos por defecto

```
mysql 
information_schema
```
#### Version de base de datos

```
VERSION()
@@VERSION
@@GLOBAL.VERSION
```

#### Credenciales por defecto

En este caso la base de datos de MySQL tiene por defecto las credenciales en una tabla llamada: 

```
mysql.user
```

Para ser mas exactos en las columnas: `user y password`

#### Base de datos actual

```
database()
schema()
```

#### Server Hostname 

```
@@HOSTNAME
```

### MySSQL

#### Bases de datos por defecto.

```
Model
Msdb
Tempdb
Northwind
Information_schema
```
#### Version de base de datos

```
@@VERSION
```

#### Credenciales por defecto

En este caso la base de datos de MySSQL tiene por defecto las credenciales en 2 tablas llamadas: 

```
master..syslogins
master..sysprocesses
```

Para ser mas exactos en las columnas: `name y loginame`

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

#### Bases de datos por defecto.

```
System
Sysaux
```
#### Version de base de datos

```
VERSION
```

#### Credenciales por defecto

En este caso la base de datos de MySQL tiene por defecto las credenciales en una tabla llamada: 

```
all_users
sys.user$
```

Para ser mas exactos en las columnas: 

```
username

name y password
```

#### Base de datos actual

```
global_name
name
instance_name
SYS.DATABASE_NAME
```

#### Server Hostname 

```
host_name
UTL_INADDR.get_host_name
```

### Tipos de payloads

#### Boolean-based blind

Boolean-based blind significa que la vulnerabilidad se basa en valores booleanos (true or false) y se dice que es ciega por que no muetra alguna señal de que hay un error en la página.

#### Error-based

Error-based significa que la inyeccion se basa en los mensajes de error que el servidor responde y asi conocer un poco mas sobre la estructura de la base de datos que se esta usando.

#### Union query-based

Union query-based significa que estamos aprovechando que el operador UNION de sql puede ser usado, gracias a esto podemos combinar declaraciones que serán visualizadas como parte de la respuesta del servidor.

#### Stacked queries

Stacked queries significa que la vulneravilidad se basa en añadir mas consultas sql en serie, de esta manera mandar una consulta normal y al mismo tiempo mandar la consulta del atacante, todo esto solo dividiendo las consultas con un ";".

#### Time-based blind

Time-based blind es cuando la vulnerabilidad se basa en el tiempo, lo que significa que un atacante enviará una consulta sql obligano a la base de datos a esperar una cierta cantidad de tiempo, si el tiempo de respuesta del servidor es el mismo tiempo que el atacante declaró en la sentencia, esto significa que es vulnerable.

#### Inline queries

Inline queries consiste en unir una query sql dentro de otra y asi sucesivamente para ver si la sentencia es ejecutada.










