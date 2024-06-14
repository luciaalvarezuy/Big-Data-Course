# Práctico Hadoop 

## Preparar el ambiente para trabajar.

Primero debemos instalar java para poder proceder con la instalación de hadoop para eso debemos abrir una nueva terminal y ejecutar:

`sudo apt update`

`sudo apt install openjdk-11-jdk -y`

recuerda que la contraseña de admin es **eurecat**

Ahora vamos a proceder a instalar hadoop para eso vamos a descargarlo y descomprimirlo  ejecutando estos comandos 

`wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz`

`tar xzf hadoop-3.3.6.tar.gz`

`sudo mv hadoop-3.3.6 /opt/hadoop`

Ahora debemos agregar las variables de hadoop al archivo de bash para esto debemos ejecutar.
Para esto debemos ejecutar 

`sudo nano .bashrc`

Vamos al final del archivo utilizando &darr; y pegamos este código al final 

```sh
  #Hadoop Related Options
  export HADOOP_HOME=/opt/hadoop
  export HADOOP_INSTALL=$HADOOP_HOME
  export HADOOP_MAPRED_HOME=$HADOOP_HOME
  export HADOOP_COMMON_HOME=$HADOOP_HOME
  export HADOOP_HDFS_HOME=$HADOOP_HOME
  export YARN_HOME=$HADOOP_HOME
  export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
  export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
```

Presionamos **ctrl + O** para salvar **enter** para confirmar y luego **ctrl + X** para cerrar el editor.

Luego ejecutamos el siguiente comando para actualizar la configuración

`source .bashrc`

Ahora si escribimos en la consola `hadoop` deberíamos recibir el siguiente error: `ERROR: JAVA_HOME is not set and could not be found.`

Esto se debe a que hadoop no sabe dónde tenemos instalado java dentro de nuestro sistema operativo por lo tanto vamos a solucionarlo.
Editamos la configuración de ambiente de hadoop utilizando:

`sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh`

Buscamos la línea donde dice:

```sh
  # The java implementation to use. By default, this environment
  # variable is REQUIRED on ALL platforms except OS X!
  # export JAVA_HOME=
```

y la reemplazamos por:

```sh
  # The java implementation to use. By default, this environment
  # variable is REQUIRED on ALL platforms except OS X!
  export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```

Presionamos **ctrl + O** para salvar **enter** para confirmar y luego **ctrl + X** para cerrar el editor.

Ahora si ejecutamos el comando `hadoop` tendríamos que ver todos los comandos que nos permite ejecutar hadoop.

## Ejemplo contar palabras repetidas.

Ahora que tenemos el ambiente instalado podemos utilizar hadoop. Primero vamos a crear una carpeta nueva utilizando:

`mkdir ejemplo_mapreduce`

y entramos a la misma utilizando 


`cd ejemplo_mapreduce`

Ahora vamos a crear un archivo de texto y agregar algunas palabras. En la consola ejecutamos:

`touch data.txt`

`nano data.txt`

Escribimos o pegamos algún texto de internet intentando que tenga palabras repetidas y luego guardamos el mismo utilizando los comandos que ya conocemos:

**ctrl + O** para salvar **enter** para confirmar y luego **ctrl + X** para cerrar el editor.

Ahora vamos a descargar un archivo jar que ejecutaremos utilizando hadoop para contar las palabras. Para esto ejecutamos: 

`wget https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-mapreduce-examples/3.3.6/hadoop-mapreduce-examples-3.3.6.jar` 

Para ver el contenido del documento .jar: 

`hadoop jar hadoop-mapreduce-examples-3.3.6.jar`

Y ahora ejecutamos:

`hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount data.txt output`

podemos consultar los resultados ejecutando:

`cat  output/part-r-00000`

También podemos ver esto en la interfaz gráfica utilizando.

`nautilus .`

Borrar la carpeta output si quieres ejecutar nuevamente el proceso.

Para salir del modo interfaz gráfica debemos ejecutar *Ctrl + C*

Pueden seguir cambiando el texto y ejecutando `hadoop jar hadoop-mapreduce-examples-3.3.6.jar wordcount data.txt output` para testear diferentes resultados.

Recuerda ingresar nuevamente:  `cat output/part-r-00000`

# Configurar Nodos de Clúster en Hadoop Dockerizado

En la sección anterior aprendimos a instalar hadoop en un ordenar manualmente pero para manejar un clúster o funciones más complejas deberíamos de meternos en configuraciones que no resultan de gran valor en este curso ya que serían muy distintas en ambientes reales. Es por esto que en esta sección se va a utilizar docker para desplegar contenedores pre-configurados y así simplificar el mismo.

*Docker es una plataforma que te permite empaquetar aplicaciones y sus dependencias en "contenedores" aislados. Estos contenedores funcionan como cajas de herramientas virtuales que incluyen todo lo necesario para que la aplicación se ejecute correctamente, desde el código hasta el sistema operativo.*

## Preparar el ambiente para trabajar.

Primero debemos instalar docker y git para poder proceder con la creación del clúster de hadoop, para eso debemos **abrir una nueva terminal** y ejecutar:

`sudo snap install docker`

`sudo apt install git -y`

recuerda que la contraseña de admin es **eurecat**

Ahora vamos a descargar la imagen de docker correspondiente:

`git clone https://github.com/luciaalvarezuy/Docker_hadoop`
 
procedemos a entrar a la carpeta que acabamos de clonar utilizando:

`cd Docker_hadoop`

y ya podemos iniciar las imágenes de docker ejecutando

`sudo docker-compose up -d`

Podemos ver los contenedores desplegados utilizando 

`sudo docker ps`

Ahora vamos al navegador y escribimos `http://localhost:9870` donde podemos ver el explorador web de Hadoop.

## Crear un archivo en HDFS.

Ahora podemos ejecutar la terminal **dentro** de uno de los contenedores que hemos creado utilizando el comando:

`sudo docker exec -it namenode bash`

dentro de esta terminal crearemos una carpeta llamada user/root/input utilizando el comando 

`hdfs dfs -mkdir -p /user/root/input`

copiamos todos los archivos xml de configuración de Hadoop al directorio de que creamos:

`hdfs dfs -put $HADOOP_HOME/etc/hadoop/*.xml /user/root/input`

ahora podemos descargar un archivo desde git al directorio:

`curl https://raw.githubusercontent.com/ibm-developer-skills-network/ooxwv-docker_hadoop/master/SampleMapReduce.txt --output data.txt`

y copiamos el archivo al directorio utilizando:

`hdfs dfs -put data.txt /user/root/`

## Ver el archivo en HDFS.

volvemos al navegador, a la ruta `http://localhost:9870`

vamos a Utilities &rarr; Broswe the file system y luego a la ruta user/root como se muestra en las imágenes:

![hadoop1](https://github.com/luciaalvarezuy/Clase_practica_hadoop/raw/main/hadoop1.png)


![01_exercise](https://github.com/luciaalvarezuy/Big-Data-Course/blob/main/Hadoop/01_exercise/hadoop2.png)

Ten en cuenta que el tamaño del bloque es de 128 MB aunque el tamaño del archivo sea en realidad mucho menor. Esto se debe a que el tamaño de bloque predeterminado utilizado por HDFS es de 128 MB.

Puedes hacer clic en el archivo para ver en qué bloque se encuentra. Esto te dará información sobre el archivo en términos de número de bytes, ID de bloque, etc.

![hadoop3](https://github.com/luciaalvarezuy/Big-Data-Course/blob/main/Hadoop/01_exercise/hadoop3.png)

para salir de la terminal dentro del contenedor simplemente podemos ejecutar: `exit`

# Configurar Hive and Bee

## Obtención de la información para trabajar

Antes de comenzar vamos a descargar un archivo con datos con el que trabajaremos. Para esto **abrimos una nueva terminal** y vamos a crear un nuevo directorio utilizando el comando:

`sudo mkdir /home/project` 

`sudo mkdir /home/project/data`

y nos movemos al directorio utilizando el comando:

`cd /home/project/data`

ahora procedemos a descargar el archivo desde github con el comando:

`sudo wget https://raw.githubusercontent.com/luciaalvarezuy/Big_data_test/main/BigData_Custom_Sample.csv`

podemos ver este archivo en vs code utilizando el comando `code .`

## Ejecución de la imagen Hive en docker  

Ahora que tenemos la información podemos descargar la imagen de docker de apache/hive, para eso **abrimos una nueva terminal** y ejecutamos: 

`sudo docker pull apache/hive:4.0.0-alpha-1`

Ahora que tenemos la imagen descargada podemos chequearlo utilizando `sudo docker images`

Para ejecutar la misma podemos ejecutar:

`sudo docker run -d -p 10000:10000 -p 10002:10002 --env SERVICE_NAME=hiveserver2 -v /home/project/data:/hive_custom_data --name myhiveserver apache/hive:4.0.0-alpha-1`

Como puedes observar tenemos varias configuraciones procedemos a explicarlas:

- `-p 10000:10000 -p 10002:10002`: Se mapean los puertos del contenedor de docker con las de nuestro ordenador o vm. Es decir, el puerto 10000 del contenedor corresponde con el 10000 de nuestro ordenador y lo mismo para el 10002.
- `--env SERVICE_NAME=hiveserver2`Simplemente seteamos la variable de entorno SERVICE_NAME
- `--name myhiveserver`  Nombraremos la instancia del servidor como "myhiveserver"
- `-v /home/project/data:/hive_custom_data` Definimos el mapeo de volúmenes entre el contenedor y el ordenador. Al igual que con los puertos en este caso estamos diciendo que los archivos que agreguemos en la carpeta */home/project/data* de nuestro ordenador se replicarán en la carpeta */hive_custom_data* del contenedor (nótese que en */home/project/data* fue donde agregamos nuestro archivo csv con el que vamos a trabajar)

Para ver las imágenes desde las cuales estamos trabajando `sudo docker ps`

Ahora vamos al navegador y escribimos `http://localhost:10002` donde podemos ver el explorador web de hive.

## Crear tablas, agregar y ver datos en Hive

**Abrimos una nueva terminal** y ejecutamos: 

Con Hive funcionando, que te permite acceder a beeline. Beeline es una interfaz de línea de comandos SQL donde puedes crear, modificar, eliminar tablas y acceder a los datos dentro de ellas.

`sudo docker exec -it myhiveserver beeline -u 'jdbc:hive2://localhost:10000/'`

Tendríamos que ver este resultado:

![hive1](https://github.com/luciaalvarezuy/Big-Data-Course/blob/main/Hadoop/01_exercise/hive1.png)

Ya dentro de la consola vamos a crear una tabla y a cargarle la información que descargamos en el archivo csv. Para crear la tabla podemos ejecutar el siguiente comando:

`create table Opiniones(id int, edad int, sexo string, pais_donde_vive string, opinion_big_data string)  row format delimited fields terminated by ',' ;`

Para ver si la tabla se creo correctamente podemos utilizar el siguiente comando 

`show tables;`

Si todo es correcto tendríamos que ver el siguiente resultado:

![hive2](https://github.com/luciaalvarezuy/Big-Data-Course/blob/main/Hadoop/01_exercise/hive2.png)

Ahora podemos cargar la que descargamos en el primer paso en la tabla utilizando el siguiente comando:

`LOAD DATA INPATH '/hive_custom_data/BigData_Custom_Sample.csv' INTO TABLE opiniones;`

Podemos ver los datos cargado ejecutando:

`SELECT * FROM opiniones;`

Para salir de la terminal de beehive podemos utilizar **ctrl + D**

Internamente, Hive utiliza MapReduce para procesar y analizar datos. Cuando ejecutas una consulta de Hive, esta genera trabajos de MapReduce que se ejecutan en el clúster de Hadoop.
