# Práctico Hadoop – WordCount (Contar palabras)

## Objetivo

Aplicar el programa WordCount de Hadoop para comparar la frecuencia de palabras en distintos textos y comprender el funcionamiento del modelo MapReduce.

---

### Preparar el ambiente para trabajar

Asegúrate de tener Hadoop instalado y configurado correctamente.

Crea una carpeta de trabajo:

`mkdir ejemplo_mapreduce`

Si aparece este mensaje de error:
` mkdir: no se puede crear el directorio «ejemplo_mapreduce»: El archivo ya existe`

Significa que la carpeta ya existe. Podés resolverlo de dos formas:

#### Opción 1: Borrar y crear una nueva (elimina todo lo anterior)

` rm -r ejemplo_mapreduce`

`mkdir ejemplo_mapreduce`

`cd ejemplo_mapreduce`



#### Opción 2 — Si querés borrar todo y empezar desde cero

Desde tu carpeta base:

`cd ~`

Borrar completamente la carpeta (⚠️ esto elimina todo su contenido):

`rm -rf ejemplo_mapreduce`

Crear una nueva carpeta limpia:

`mkdir ejemplo_mapreduce`

`cd ejemplo_mapreduce`


---

### Crear los archivos de texto

Crea tres archivos dentro del directorio ejemplo_mapreduce:

`touch cuento.txt noticia.txt song.txt`

Edita cada archivo con nano y agrega un texto breve con palabras repetidas

`nano cuento.txt`

Ingresa el texto 

`nano noticia.txt`

Ingresa el texto

`nano song.txt`

Ingresa el texto 

Recuerda: Guarda los cambios con Ctrl + O, presiona Enter, y sal con Ctrl + X.

---

### Ejecutar el conteo de palabras

Para cada archivo, ejecuta el siguiente comando: **`hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount <archivo>.txt <output>`**  Ejemplo:

`hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount cuento.txt output_cuento`

`hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount noticia.txt output_noticia`

`hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount song.txt output_song`


### Si falta el archivo JAR

Si aparece este mensaje de error:

**`JAR does not exist or is not a normal file: /home/eurecat/ejemplo_mapreduce/hadoop-mapreduce-examples-3.3.6.jar`**

Significa que el archivo .jar no se encuentra descargado en tu carpeta. Verifica si existe:

`ls -l`

Si no está, descárgalo:

`wget https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-mapreduce-examples/3.3.6/hadoop-mapreduce-examples-3.3.6.jar`

Confirma la descarga:

`ls -l hadoop-mapreduce-examples-3.3.6.jar`

Ejecuta nuevamente:

`hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount cuento.txt output_cuento`

---

### Visualizar los resultados

Cada vez que Hadoop ejecuta un job, crea una carpeta de salida con los resultados. Para verlos, ejecuta:

`cat output_cuento/part-r-00000`

`cat output_noticia/part-r-00000`

`cat output_song/part-r-00000`

Si aparece este error:

**`cat: output_noticia`**: Es un directorio

**`cat: /part-r-00000`**: No existe el archivo o el directorio

Significa que escribiste mal la ruta. Debe ir todo junto:

`cat output_noticia/part-r-00000`

Recuerda que Hadoop guarda los resultados dentro de una carpeta, y dentro de esa carpeta se encuentra el archivo part-r-00000.

---

### Evaluar los resultados

Reflexiona sobre los resultados obtenidos:
- ¿Qué texto tiene más repeticiones de palabras?
- ¿Qué palabra aparece más veces?
- ¿Qué sucede si combinamos los tres textos en un solo archivo?

Para probarlo:

`cat cuento.txt noticia.txt song.txt > combinado.txt`

`hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount combinado.txt output_combinado`

`cat output_combinado/part-r-00000`

---
### Conclusión

Este ejercicio muestra cómo Hadoop divide, distribuye y combina resultados para procesar grandes volúmenes de texto de manera eficiente.

El ejemplo WordCount es la base para entender cómo MapReduce trabaja con pares clave–valor, y cómo una tarea simple (como contar palabras) se puede escalar a millones de registros distribuidos en distintos nodos.

