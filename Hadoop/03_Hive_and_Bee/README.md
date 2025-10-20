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

Si aún no tienes Hive en ejecución, abre una terminal y crea el contenedor con la imagen oficial de Apache Hive.

`sudo docker run -d -p 10000:10000 -p 10002:10002 --env SERVICE_NAME=hiveserver2 -v /home/project/data:/hive_custom_data --name myhiveserver apache/hive:4.0.0-alpha-1`


---
---

## Error común: "The container name '/myhiveserver' is already in use"

Este error aparece cuando intentamos crear un contenedor con Docker que tiene el **mismo nombre** que otro contenedor ya existente.  
Por ejemplo:

`docker: Error response from daemon: Conflict. The container name "/myhiveserver" is already in use by container ...`

Esto significa que el contenedor llamado **myhiveserver** ya fue creado antes y Docker no permite duplicar nombres.

---

### Verificar los contenedores existentes

Podemos listar todos los contenedores (activos e inactivos) con:

`sudo docker ps -a`

En la última columna (`NAMES`) verás si existe uno con el nombre **myhiveserver**.

---

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
## Ejercicio 5 — Conteos rápidos de valores distintos
Etiquetas de sexo distintas:

`Pensar en el código`

---
## Ejercicio 6 — Crear tabla con opiniones de un país específico

Crear una tabla nueva llamada opiniones_uruguay que contenga solo los registros de personas que viven en Uruguay.

`Pensar en el código`

Verificar que la tabla se haya creado correctamente:

`Pensar en el código`

Contar cuántos registros tiene la nueva tabla:

`Pensar en el código`

Visualizar los primeros registros para comprobar que los datos sean correctos:

`Pensar en el código`


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
