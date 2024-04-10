# Instalando Hadoop en Windows 10/11


Para instalar hadoop necesitamos las siguientes herramientas:


1. Java JDK
2. 7zip o WinRAR para descomprimir Haddop
3. Terminal de windows o Powershell

## Paso 1 - Descargar y descomprimir Hadoop

Descargar [Hadoop](https://dlcdn.apache.org/hadoop/common/hadoop-3.3.5/). Crear una carpeta en la raiz de tu disco C:\ o D:\ que nos permita localizar facilmente el contenido de Hadoop. Descomprimir en este directorio el contenido de Hadoop. En el video creamos el directorio **Hadoop** y dentro colocamos el contenido del comprimido de hadoop.

## Paso 2 - Instalar Java 8

Debido a que Hadoop necesita de Java 8, vamos a instalar Java en la raiz, con el fin de acceder facilmente a la ruta de sus binarios. En el video se instalo en `C:\Java` y la ruta que vamos a copiar sera `C:\Java\jdk1.8.0_211`, es posible que cambie pero es para que se de una idea.


## Paso 3 - Modificar **hadoop-env.cmd**

Este archivo se localiza en el directorio de hadoop, en el directorio **etc/hadoop**. Lo editamos con el bloc de notas o visual studio code. Vamos a buscar la linea 25 o la que haga referencia a la siguiente:

```cmd
set JAVA_HOME=%JAVA_HOME%
```

Y vamos a quitar %JAVA_HOME% y sustituir por la ruta del jdk de Java del paso 2, quedando:

```cmd
set JAVA_HOME=C:\Java\jdk1.8.0_211
@rem %JAVA_HOME%
```

## Paso 4 - Agregar las variables de entorno

En este paso agregaremos la variable **HADOOP_HOME** y las rutas a la variable **Path**, donde agregaremos bin y sbin.

## Paso 5 - Abrir una terminal de windows

En este paso, solo probaremos que tengamos acceso a los comandos de Hadoop. Abrimos la terminal y escribimos:

```shell
hadoop -version
```

Si todo ha salido bien, debemos ver una salida similar a la siguiente:

```shell
java version "1.8.0_211"
Java(TM) SE Runtime Environment (build 1.8.0_211-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.211-b12, mixed mode)
```

## Paso 6 - Configurar el core-site.xml

Este archivo se encuentra en el directorio `etc/hadoop` , lo editamos y agregamos lo siguiente:

```xml
<configuration>
  <property>
    <name>fs.default.name</name>
    <value>hdfs://0.0.0.0:19000</value>
  </property>
</configuration>
```

En el gist tambien se encuentra dicho archivo para que puedas guiarte :D.

## Paso 7 - Configurar hdfs-site.xml

Este archivo se encuentra en el directorio `etc/hadoop` , lo editamos y agregamos lo siguiente:

```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <!-- <value>file:///DIRECTORY 1 HERE</value> -->
    <value>file:///C:/Hadoop/hadoop-3.3.5/data/dfs/namespace_logs</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <!-- <value>file:///DIRECTORY 2 HERE</value> -->
    <value>file:///C:/Hadoop/hadoop-3.3.5/data/dfs/data</value>
  </property>
</configuration>
```

Se deben de crear los directorios: `data/dfs/namespace_logs` & `data/dfs/data` dentro del directorio de Hadoop.

## Paso 8 - Configurar mapred-site.xml

Este archivo se encuentra en el directorio `etc/hadoop` , lo editamos y agregamos lo siguiente:

```xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
  <property> 
    <name>mapreduce.application.classpath</name>
    <value>%HADOOP_HOME%/share/hadoop/mapreduce/*,%HADOOP_HOME%/share/hadoop/mapreduce/lib/*,%HADOOP_HOME%/share/hadoop/common/*,%HADOOP_HOME%/share/hadoop/common/lib/*,%HADOOP_HOME%/share/hadoop/yarn/*,%HADOOP_HOME%/share/hadoop/yarn/lib/*,%HADOOP_HOME%/share/hadoop/hdfs/*,%HADOOP_HOME%/share/hadoop/hdfs/lib/*</value>
  </property>
</configuration>
```

## Paso 9 - Configurar yarn-site.xml

Este archivo se encuentra en el directorio `etc/hadoop` , lo editamos y agregamos lo siguiente:

```xml
<configuration>
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>localhost</value>
  </property>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
    <name>yarn.nodemanager.env-whitelist</name>
    <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
  </property>
</configuration>
```

## Paso 10 - Sustituir los archivos del directorio bin

Debemos descargar los archivos del repositorio (https://github.com/marcoscastanedac/Hadoop/tree/main/winutils), buscamos la versión de Hadoop, en este caso la 3.3.5. Sustituimos el contenido de los archivos descargados por los de la carpeta de `hadoop/bin`, nos preguntara si deseamos reemplazarlos, le damos que si.

## Paso 11 - Formatear el directorio para HDFS

Abrimos una terminal y ejecutar:

```shell
hdfs namenode -format
```

Como es la primera vez nos preguntara si queremos formatear el directorio, escribimos `Y` y le damos Enter.

## Paso 12 - Inicializar los demons de dfs y yarn

Vamos a escribir en la terminal lo siguiente:

```shell
start-dfs
```

Ahora ejecutamos lo siguiente:

```shell
start-yarn
```

Esto levantara el servidor web que nos brinda información del HDFS y YARN.  Para acceder a estos sitios, abrimos un navegador y accedemos a las siguientes URL:

**HDFS Namenode UI info**

[http://localhost:9870/dfshealth.html#tab-overview](http://localhost:9870/dfshealth.html#tab-overview)

**HDFS Datanode UI info**

[http://localhost:9864/datanode.html](http://localhost:9864/datanode.html)

**YARN resource manager UI**

[http://localhost:8088](http://localhost:8088)


Para detener los servicios, debemos ejecutar lo siguiente:

```shell
stop-yarn
stop-dfs
```
