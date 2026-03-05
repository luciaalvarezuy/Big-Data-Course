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

Para hacerlo en una misma linea de código: 

`rm -rf output_cuento output_noticia output_song && hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount cuento.txt output_cuento && hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount noticia.txt output_noticia && hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount song.txt output_song`

```diff
### Si falta el archivo JAR

Si aparece este mensaje de error:

**`JAR does not exist or is not a normal file: /home/eurecat/ejemplo_mapreduce/hadoop-mapreduce-examples-3.3.6.jar`**

Significa que el archivo .jar no se encuentra en la ruta actual. Verifica si existe:

`ls -l`

Si no está, descárgalo:

`wget https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-mapreduce-examples/3.3.6/hadoop-mapreduce-examples-3.3.6.jar`

Confirma la descarga:

`ls -l hadoop-mapreduce-examples-3.3.6.jar`

Ejecuta nuevamente:

`hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount cuento.txt output_cuento`
```
---

### Visualizar los resultados

Cada vez que Hadoop ejecuta un job, crea una carpeta de salida con los resultados. Para verlos, ejecuta:

`cat output_cuento/part-r-00000`

`cat output_noticia/part-r-00000`

`cat output_song/part-r-00000`


Si lo quieres hacer en una línea de código:

`cat output_cuento/part-r-00000; cat output_noticia/part-r-00000; cat output_song/part-r-00000`

``` diff
Si aparece este error:

**`cat: output_noticia`**: Es un directorio

**`cat: /part-r-00000`**: No existe el archivo o el directorio

Significa que escribiste mal la ruta. Debe ir todo junto:

`cat output_noticia/part-r-00000`

Recuerda que Hadoop guarda los resultados dentro de una carpeta, y dentro de esa carpeta se encuentra el archivo part-r-00000.
```

### Evaluar los resultados

Reflexiona sobre los resultados obtenidos:
- ¿Qué texto tiene más repeticiones de palabras?
- ¿Qué palabra aparece más veces?
- ¿Qué sucede si combinamos los tres textos en un solo archivo?

1. Combinar los archivos

El siguiente comando une el contenido de los tres archivos (cuento.txt, noticia.txt y song.txt) en un único archivo llamado combinado.txt.

`cat cuento.txt noticia.txt song.txt > combinado.txt`

2. Ejecutar WordCount sobre el archivo combinado

Ahora ejecutamos nuevamente el programa WordCount de Hadoop, pero utilizando el archivo combinado como entrada.

`hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount combinado.txt output_combinado`

Este comando generará una nueva carpeta llamada output_combinado, donde Hadoop almacenará los resultados del conteo de palabras.

3. Visualizar los resultados

Para ver el resultado del análisis, ejecutamos:

`cat output_combinado/part-r-00000`

Este archivo contiene todas las palabras encontradas en los textos y la cantidad de veces que aparecen.



### Para profundizar un poco más

###### 1. Ordenar los resultados por frecuencia

De menor a mayor (orden ascendente)

El archivo generado por Hadoop contiene dos columnas:

- la palabra

- la cantidad de veces que aparece

Podemos ordenar los resultados para ver cuáles son las palabras más frecuentes. Para hacerlo, utilizamos el comando sort.

El siguiente comando ordena los resultados de mayor a menor según la frecuencia de aparición (segunda columna):

`sort -k2 -n output_combinado/part-r-00000`

De mayor a menor:

`sort -k2 -nr output_combinado/part-r-00000`

###### 2. Mostrar solo las palabras más frecuentes

Si queremos visualizar únicamente las palabras con mayor número de repeticiones, podemos combinar sort con el comando head.

El siguiente comando muestra las 2 palabras más repetidas:

`sort -k2 -nr output_combinado/part-r-00000 | head -2`

###### 3. Guardar el resultado en un archivo
Si queremos guardar el resultado del análisis en un archivo en lugar de mostrarlo solamente en pantalla, podemos utilizar el operador >.

El siguiente comando ordena las palabras por frecuencia de mayor a menor, muestra las 2 palabras más repetidas, y guarda el resultado en un archivo llamado resultado_top.txt

`sort -k2 -nr output_combinado/part-r-00000 | head -2 > resultado_top.txt`

Podemos visualizar su contenido utilizando:

`cat resultado_top.txt`

###### 4. Cambiar el nombre del archivo generado
Si queremos cambiar el nombre del archivo que acabamos de crear, podemos utilizar el comando mv.

Por ejemplo, para cambiar el nombre de resultado_top.txt a palabras_mas_frecuentes.txt, ejecutamos:
`mv resultado_top.txt palabras_mas_frecuentes.txt`

El comando mv se utiliza para mover o renombrar archivos dentro del sistema. Después de ejecutar el comando, el archivo tendrá el nuevo nombre: palabras_mas_frecuentes.txt

---
### Conclusión

Este ejercicio muestra cómo Hadoop divide, distribuye y combina resultados para procesar grandes volúmenes de texto de manera eficiente.

El ejemplo WordCount es la base para entender cómo MapReduce trabaja con pares clave–valor, y cómo una tarea simple (como contar palabras) se puede escalar a millones de registros distribuidos en distintos nodos.

