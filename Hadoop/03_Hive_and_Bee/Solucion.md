# Práctico Hive — Análisis de Opiniones

## Objetivo

Aplicar los conceptos aprendidos sobre **Hive** para realizar consultas básicas sobre una tabla cargada desde un archivo CSV.

---

## Contexto

Ya tenemos cargado el archivo `BigData_Custom_Sample.csv` en la tabla `opiniones`.  
La tabla contiene las siguientes columnas:

| Columna | Tipo | Descripción |
|----------|------|-------------|
| id | INT | Identificador único de cada registro |
| edad | INT | Edad de la persona |
| sexo | STRING | Sexo reportado (F o M) |
| pais_donde_vive | STRING | País de residencia |``
| opinion_big_data | STRING | Opinión textual sobre Big Data |

---

## Preparar el entorno

Si quieren intentar arrancar el contenedor (sin crearlo desde cero) pueden realizar estos pasos
1. Arrancar el contenedor con esto:

`sudo docker start myhiveserver`

2. Conectarse a Beeline así:

`sudo docker exec -it myhiveserver beeline -u 'jdbc:hive2://localhost:10000/'`

Comentario del segundo paso: puede ser necesario intentarlo más de una vez. Esto pasa porque el servidor de Hive tarda unos segundos en iniciar dentro del contenedor después de que lo encendemos.

---
---

Si quieren crear un nuevo contenedor pueden:

### Eliminar o renombrar el contenedor viejo

Si queremos **borrarlo por completo**, ejecutamos:

`sudo docker rm -f myhiveserver`

> El parámetro `-f` fuerza el borrado incluso si el contenedor está en ejecución.

Si preferimos **mantenerlo pero cambiarle el nombre**, podemos usar:

`sudo docker rename myhiveserver myhiveserver_old`

---

### Crear un nuevo contenedor limpio

Una vez borrado o renombrado el anterior, ya podemos ejecutar el comando original sin conflicto:

`sudo docker run -d -p 10000:10000 -p 10002:10002 --env SERVICE_NAME=hiveserver2 -v /home/project/data:/hive_custom_data --name myhiveserver apache/hive:4.0.0-alpha-1`

---

## Error común: "Bind for 0.0.0.0:10000 failed: port is already allocated"

**Qué significa el error y cómo identificarlo**

> `Bind for 0.0.0.0:10000 failed: port is already allocated`

Este mensaje indica que el **puerto 10000 del sistema anfitrión ya está ocupado**.  
Normalmente lo está usando **otro contenedor de Hive** o un proceso que no se cerró correctamente.

Cuando ejecutamos el comando con el parámetro:

`-p 10000:10000`

...Docker intenta **reservar ese puerto** para exponerlo fuera del contenedor, pero **no puede hacerlo porque ya está en uso**.

---

### **1. Ver los contenedores activos**

Para ver todos los contenedores en ejecución y comprobar cuál está usando el puerto 10000:

```bash
sudo docker ps
```

Esto mostrará una lista con el nombre, ID y puertos usados por cada contenedor.

---

### **2. Parar el contenedor que usa el puerto 10000**

Usa este comando para **detener automáticamente el contenedor que publica el puerto 10000**:

```bash
sudo docker stop $(sudo docker ps -q --filter "publish=10000")
```

---

### **3. Borrar el contenedor antiguo (por nombre)**

Si conocés el nombre del contenedor (por ejemplo, `myhiveserver`), podés eliminarlo con:

```bash
sudo docker rm -f myhiveserver
```

Esto detiene y borra el contenedor, liberando el puerto 10000 para volver a usarlo.

---

### **4. Crear nuevamente el contenedor limpio de Hive**

Una vez liberado el puerto, ejecutá nuevamente el comando original para crear un contenedor nuevo:

```bash
sudo docker run -d -p 10000:10000 -p 10002:10002 --env SERVICE_NAME=hiveserver2 -v /home/project/data:/hive_custom_data --name myhiveserver apache/hive:4.0.0-alpha-1
```

---


## Conectarse a Hive

Hive se ejecuta dentro del contenedor.  
Para conectarte, utiliza el comando:

`sudo docker exec -it myhiveserver beeline -u 'jdbc:hive2://localhost:10000/'`

Una vez dentro, deberías ver el prompt:

`jdbc:hive2://localhost:10000>`

Desde aquí podrás ejecutar tus consultas SQL.

---

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

