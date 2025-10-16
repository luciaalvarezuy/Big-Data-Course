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
| pais_donde_vive | STRING | País de residencia |
| opinion_big_data | STRING | Opinión textual sobre Big Data |

---

## Preparar el entorno

Si aún no tienes Hive en ejecución, abre una terminal y crea el contenedor con la imagen oficial de Apache Hive.

`sudo docker run -d -p 10000:10000 -p 10002:10002 --env SERVICE_NAME=hiveserver2 -v /home/project/data:/hive_custom_data --name myhiveserver apache/hive:4.0.0-alpha-1`

Para verificar que el contenedor está activo:

`sudo docker ps`

Deberías ver un contenedor con el nombre **myhiveserver** y el puerto 10000 en ejecución.

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

`SHOW TABLES;`

Si la tabla fue creada correctamente, aparecerá en el listado.

Para ver las primeras 10 filas de la tabla:

`SELECT * FROM opiniones LIMIT 10;`

---

## Ejercicio 1 — Promedio de edad de los participantes

Calcular la edad promedio de las personas que completaron la encuesta.

`Pensar en el código`


---


## Ejercicio 2 — Conteo por combinación de sexo y país

Muestra cuántas personas de cada sexo hay por país. Permite ver si hay diferencias en la participación por género según el país.

`Pensar en el código`


---


## Ejercicio 3 — Distribución por país

Contar cuántas opiniones hay por país y ordenarlas de mayor a menor.

`Pensar en el código`

---

## Ejercicio 4 — Filtrar por condición

Mostrar solo las opiniones de personas mayores de 30 años.

`Pensar en el código`

---
## Ejercicio 4 — Conteos rápidos de valores distintos
Etiquetas de sexo distintas:  

`Pensar en el código`

---
## Ejercicio 5 — Crear tabla con opiniones de un país específico

Crear una tabla nueva llamada opiniones_espana que contenga solo los registros de personas que viven en España.

`Pensar en el código`

Verificar que la tabla se haya creado correctamente:

`Pensar en el código`


Contar cuántos registros tiene la nueva tabla:

`Pensar en el código`


Visualizar los primeros registros para comprobar que los datos sean correctos:

`Pensar en el código`


---
