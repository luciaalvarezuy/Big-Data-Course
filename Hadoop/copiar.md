Ver el estado de los contenedores:

sudo docker ps -a 


Si les aparece "Exited" en mihyserver tienen que ejecutar: 

parar:


sudo docker stop myhiveserver


remove:

sudo docker rm myhiveserver


iniciarlo:

sudo docker run -d -p 10000:10000 -p 10002:10002 --env SERVICE_NAME=hiveserver2 -v /home/project/data:/hive_custom_data --name myhiveserver apache/hive:4.0.0-alpha-1


ejecutamos


sudo docker run -d \
-p 10000:10000 -p 10002:10002 \
--env SERVICE_NAME=hiveserver2 \
-v /home/project/data:/hive_custom_data \
--name myhiveserver \
apache/hive:4.0.0-alpha-1


