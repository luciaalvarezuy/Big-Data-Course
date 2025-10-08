# Práctico Hadoop – WordCount (Conteo de palabras)

## Objetivo

Aplicar el programa WordCount de Hadoop para comparar la frecuencia de palabras en distintos textos y comprender el funcionamiento del modelo MapReduce.

### Preparar el ambiente para trabajar

Asegúrate de tener Hadoop instalado y configurado correctamente.

Crea una carpeta de trabajo:

`mkdir ejemplo_mapreduce`

Si aparece este mensaje de error:
` mkdir: no se puede crear el directorio «ejemplo_mapreduce»: El archivo ya existe`

Significa que la carpeta ya existe. Podés resolverlo de dos formas:

#### Opción 1: Entrar en la carpeta existente

` cd ejemplo_mapreduce`

`ls -l `

#### Opción 2: Borrar y crear una nueva (⚠️ elimina todo lo anterior)

` rm -r ejemplo_mapreduce`

`mkdir ejemplo_mapreduce`

`cd ejemplo_mapreduce`

Luego creamos un archivo de texto de prueba:

`nano data.txt`

### Crear los archivos de texto

Crea tres archivos dentro del directorio ejemplo_mapreduce:

`touch cuento.txt noticia.txt song.txt`

Edita cada archivo con nano y agrega un texto breve con palabras repetidas

`cuento.txt`

Ingresa el texto 

`noticia.txt`
Ingresa el texto 

`song.txt`
Ingresa el texto 

` `
` `

