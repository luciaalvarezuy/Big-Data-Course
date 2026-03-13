**Preparar el entorno**

En la mayoría de los casos solo necesitas ejecutar estos dos comandos para trabajar con Hive.

*Paso 1. Arrancar el contenedor de Hive*

`sudo docker start myhiveserver`

*Paso 2. Conectarse a Hive con Beeline*
`sudo docker exec -it myhiveserver beeline -u 'jdbc:hive2://localhost:10000/'`

Si todo funciona correctamente, deberías ver algo como:

    Connected to: Apache Hive
    0: jdbc:hive2://localhost:10000>

Desde ese prompt ya puedes ejecutar consultas SQL en Hive.

⚠️ Nota: puede ser necesario ejecutar el segundo comando más de una vez. Esto ocurre porque Hive tarda unos segundos en iniciar dentro del contenedor después de arrancarlo.

------------------------------------------------------------------------

**Solución de problemas**

Los siguientes pasos solo son necesarios si los comandos anteriores fallan.

**Problema 1: el contenedor no arranca o está corrupto**

Si el contenedor myhiveserver no arranca correctamente, puedes eliminarlo y crear uno nuevo.

1. Ver el estado de los contenedores

`sudo docker ps -a`

Si aparece algo como:
    myhiveserver   Exited (...)

significa que el contenedor existe pero no está funcionando correctamente.

2. Eliminar el contenedor viejo

`sudo docker rm -f myhiveserver`

El parámetro -f fuerza el borrado incluso si el contenedor está en ejecución.

3. Crear un nuevo contenedor limpio

`sudo docker run -d -p 10000:10000 -p 10002:10002 --env SERVICE_NAME=hiveserver2 -v /home/project/data:/hive_custom_data --name myhiveserver apache/hive:4.0.0-alpha-1`

Este comando crea un nuevo contenedor de HiveServer2. Después puedes volver a conectarte con:

`sudo docker exec -it myhiveserver beeline -u 'jdbc:hive2://localhost:10000/'`

------------------------------------------------------------------------

**Problema 2: error de puerto ocupado**

A veces aparece este error:

    Bind for 0.0.0.0:10000 failed: port is already allocated

Qué significa: El puerto 10000 ya está siendo usado por otro proceso o contenedor.

1. Ver qué contenedores están activos

`sudo docker ps`

Esto mostrará qué contenedor está usando ese puerto.

2. Parar el contenedor que usa el puerto

`sudo docker stop $(sudo docker ps -q --filter "publish=10000")`

Esto detiene cualquier contenedor que esté usando el puerto 10000.

3. Borrar el contenedor viejo (opcional)

Si sabes el nombre del contenedor, puedes eliminarlo directamente:

`sudo docker rm -f myhiveserver`

4. Crear nuevamente el contenedor de Hive

`sudo docker run -d -p 10000:10000 -p 10002:10002 --env SERVICE_NAME=hiveserver2 -v /home/project/data:/hive_custom_data --name myhiveserver apache/hive:4.0.0-alpha-1`

------------------------------------------------------------------------

**Conectarse a Hive**

Una vez que el contenedor está funcionando, puedes conectarte con:

`sudo docker exec -it myhiveserver beeline -u 'jdbc:hive2://localhost:10000/'`

Deberías ver el prompt:

    jdbc:hive2://localhost:10000>

Desde aquí podrás ejecutar consultas SQL sobre Hive.

-----------------------------------------------------------------------
-----------------------------------------------------------------------

## Verificar la tabla cargada

Para confirmar que la tabla `opiniones` existe:

```sql
SHOW TABLES;
```

Si la tabla fue creada correctamente, aparecerá en el listado.

Si al ejecutar el comando `SHOW TABLES;` aparece una tabla vacía, como se muestra a continuación:

+-----------+

| tab_name  |

+-----------+

+-----------+

significa que no existe ninguna tabla creada en la base de datos actual.

En ese caso, debemos crear la tabla `opiniones` de forma manual e indicar el formato de los datos. 
Para hacerlo, ejecuta los siguientes comandos en Beeline:

```sql
CREATE TABLE opiniones (
    id INT,
    edad INT,
    sexo STRING,
    pais_donde_vive STRING,
    opinion_big_data STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ',';
```

Luego, carga el archivo CSV con los datos dentro de la tabla recién creada:

```sql
LOAD DATA INPATH '/hive_custom_data/BigData_Custom_Sample.csv' INTO TABLE opiniones;
```

Después de ejecutar estos comandos, puedes verificar que la tabla fue creada correctamente con:

```sql
SHOW TABLES;
SELECT * FROM opiniones LIMIT 10;
```



---

## Ejercicio 1 — Promedio de edad de los participantes

Calcular la edad promedio de las personas que completaron la encuesta.

```sql
SELECT AVG(edad) AS edad_promedio
FROM opiniones;
```

---


## Ejercicio 2 — Conteo por combinación de sexo y país

Muestra cuántas personas de cada sexo hay por país. Permite ver si hay diferencias en la participación por género según el país.

```sql
SELECT pais_donde_vive, sexo, COUNT(*) AS cantidad 
FROM opiniones 
GROUP BY pais_donde_vive, sexo 
ORDER BY pais_donde_vive, cantidad DESC;
```

Esto ordena primero por el nombre del país (alfabéticamente) y, dentro de cada país, por la cantidad en orden descendente. Es decir, te muestra primero Argentina, luego Chile, luego España, etc., y dentro de cada país, el sexo con más respuestas arriba. Si querés ver los países con más respuestas totales arriba
Entonces necesitás ordenar primero por la cantidad (para que los países más grandes aparezcan primero).

Tu consulta debería quedar así:

```sql
SELECT pais_donde_vive, sexo, COUNT(*) AS cantidad
FROM opiniones
GROUP BY pais_donde_vive, sexo
ORDER BY cantidad DESC, pais_donde_vive; 
```

Ahora, el primer criterio de orden es la cantidad (de mayor a menor).
Si dos países tienen la misma cantidad, se ordenan alfabéticamente por país.
---

## Ejercicio 3 — Distribución por país

Contar cuántas opiniones hay por país y ordenarlas de mayor a menor.

```sql
SELECT pais_donde_vive, COUNT(*) AS cantidad
FROM opiniones
GROUP BY pais_donde_vive
ORDER BY cantidad DESC;
```
---

## Ejercicio 4 — Filtrar por condición

Mostrar solo las opiniones de personas mayores de 30 años.

```sql
SELECT * FROM opiniones 
WHERE edad > 30;
```

---
## Ejercicio 5 — Conteos rápidos de valores distintos
Etiquetas de sexo distintas:

```sql
SELECT COUNT(DISTINCT sexo) AS etiquetas_sexo
FROM opiniones;
```

---
## Ejercicio 6 — Crear tabla con opiniones de un país específico

Crear una tabla nueva llamada opiniones_uruguay que contenga solo los registros de personas que viven en Uruguay.

```sql
CREATE TABLE opiniones_uruguay AS SELECT id, edad, sexo, pais_donde_vive, opinion_big_data
FROM opiniones
WHERE pais_donde_vive = 'Uruguay'; 
```

Verificar que la tabla se haya creado correctamente:

```sql
SHOW TABLES; 
```

Contar cuántos registros tiene la nueva tabla:

```sql
SELECT COUNT(*)
FROM opiniones_uruguay; 
```

Visualizar los primeros registros para comprobar que los datos sean correctos:

```sql
SELECT *
FROM opiniones_uruguay
LIMIT 10; 
```



---

---

### Recomendación

Antes de comenzar cada práctica, es recomendable limpiar el entorno de Docker para evitar errores con contenedores anteriores.
Esto garantiza que todos trabajen con un entorno limpio y sin conflictos residuales.

Pasos sugeridos:

`sudo docker ps -a`  

`sudo docker rm -f myhiveserver`

De esta forma, se aseguran de empezar desde cero, evitando problemas con contenedores previos o servicios que quedaron activos.

---

Antes de crear un nuevo contenedor, verificá siempre si el puerto 10000 está ocupado (es el que utiliza HiveServer2 para conectarse con Beeline):

`sudo lsof -i :10000`

Si aparece un proceso activo, significa que el puerto está en uso —probablemente por otro contenedor de Hive—.
En ese caso, podés:

- Detener el proceso o contenedor que lo está usando, o

- Usar otro puerto distinto (por ejemplo, 10001) para evitar conflictos.

---

Nota importante sobre la carga de datos en Hive

Un error muy común al comenzar con Hive es que, al cargar un archivo CSV, la primera fila (los encabezados) se interpreta como si fuera una fila de datos. Esto sucede porque Hive, por defecto, no sabe que el archivo tiene cabecera.

Por eso, después de crear y cargar la tabla, siempre conviene ejecutar un SELECT * LIMIT 10 para verificar que los datos se hayan cargado correctamente.

Si observás valores como NULL o encabezados (sexo, pais_donde_vive, etc.) en la primera fila, significa que la cabecera se cargó como registro.

Para evitarlo, podés agregar esta propiedad al crear la tabla:
`TBLPROPERTIES ("skip.header.line.count"="1");`

Esto le indica a Hive que ignore la primera línea del CSV y cargue solo los datos reales.

Así, te asegurás de tener una tabla limpia y correctamente interpretada desde el inicio.

Ejemplo de cómo cargar los datos 

```sql
CREATE TABLE opiniones_segunda_carga (
    id INT,
    edad INT,
    sexo STRING,
    pais_donde_vive STRING,
    opinion_big_data STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
TBLPROPERTIES ("skip.header.line.count"="1");
```

```sql
LOAD DATA INPATH '/hive_custom_data/BigData_Custom_Sample.csv' INTO TABLE opiniones_segunda_carga;
```


Ver todos los registros
```sql
SELECT *
FROM opiniones_segunda_carga;
```

