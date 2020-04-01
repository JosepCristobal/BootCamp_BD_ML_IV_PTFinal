## BootCamp IV proyecto final

### Realizado por Josep Cristobal. 
### Entrega el  19/03/2020 aplazada al día 02/04/2020.

En nuestro proyecto vamos a entregar un Mínimo Producto Viable (MVP).
Lo usaremos para probar rápidamente de manera cuantitativa y cualitativa la respuesta de nuestro cliente al proyecto y a la funcionalidad del mismo. 
Nos permitirá comprobar que efectivamente el producto resuelva una necesidad del cliente antes de tener que invertir demasiados recursos en su desarrollo.

## Situación

Partimos  del supuesto que tenemos una empresa/consultoría que se dedica a gestionar y coordinar proyectos que se basan en el tratamiento y análisis de datos, pudiendo abarcar proyectos pequeños que mueven cantidades pequeñas de datos, como proyectos de gran envergadura con un manejo de gran cantidad de datos.
Hemos recibido un encargo, por parte de un grupo inversor (Capital Ganso, S.A.) que empieza a operar en nuestro pais, que le ayudemos en la tarea de la toma de decisiones a la hora de invertir en la compra de inmuebles para dedicarlos al alquiler. Al ser recien llegados a nuestro mercado, quieren centrarse en Madrid y expandirse a otras ciudades en un futuro.

## El Proyecto

. El objetivo de este proyecto es montar un servicio para la empresa que hemos  mencionado y pretende adquirir viviendas, preferentemente pisos, para dedicarlos al alquiler vacacional. También quiere adquirir locales comerciales para alquilarlos, pero con contratos de más larga duración y siempre en zonas donde se pueda mover el turismo que suele alquilar pisos para sus estancias.

* A. La estrategia del proyecto es:
	* La adquisición de datos serán el resultado del scraping/crawling de la web de airbnb, habitaclia y/o milanuncios, siempre en formato .csv.
	* Vamos a informar, diariamente, a las 13:00 y a través del envío de un email, una selección de 10 locales y 10 pisos que estén en venta en las 2 zonas de mayor oferta/demanda de pisos de alquiler que tiene anunciados airbnb.
	* Como información adicional, adjuntaremos el precio medio por metro cuadrado de los pisos y de los locales, de las dos zonas seleccionadas.
* B. En la arquitectura 
	* Alojaremos nuestra infraestructura de Hadoop en Google Cloud.
	* Utilizaremos para guardar nuestos ficheros, Google Storage.
	* Crawlearemos con la herramienta Scrapy, desde un Colaboratory, las webs de milanuncios y habitaclia.
	* Para enviar los correos con la información diaria ya descrita, utilizaremos un servicio rest que nos facilitará nuestro proveedor de correo.
	* El servicio rest recibirá los datos en formato Json que habremos generado con Python. Una vez enviado el correo y habiendo recibido el ok o ko, utilizaremos MongoDB para almacenar los datos pertenecientes al envio del correo para una futura explotación de estos.
	* Para el envío de correos utilizaremos la plataforma de MailChimp.
	* La limpieza de ficheros la realizaremos con Trifacta, si son de gran tamaño. Para datasets pequeños, utilizaremos herramientas como el bloc de notas o similares.

* C. Operación
	* Obtención del dataset de airbnb.csv al inicio del proyecto y de forma periódica, cada 15 días, lo iremos actualizando.
	* Limpieza de dataset.
	* Staging del dataset, subiendolo a Google Cloud Storage.
	* Incorporamos los datos a Hive.
	* Crawlearemos cada dia, a las 12:30, las webs de milanuncios y habitaclia, para extraer los datos necesarios para nuestros cálculos.
	* Limpiamos los datasets de habitaclia.csv y milanuncios.csv generados con el crawler.
	* Subida de los datasets a Google Cloud Storage.
	* Incorporación de datos en Hive.
	* Procesamiento de datos a las 12:55, aplicaremos el proceso necesario para extraer los datos y aplicando algoritmos de IA determinaremos los 10 locales y 10 pisos que estén en venta en las 2 zonas de mayor oferta/demanda de pisos de alquiler, fundamentados en los datos de airbnb y que económicamente sean rentables.
	* Diariamente a las 13:00, envío de correo con los resultados obtenidos, a todas las cuentas suscritas.

## 1. Diagrama de flujo de datos y herramientas utilizadas.
![insertar imagen Diagrama Proyecto](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/images/DiagramaBDA.png)

	
## 2. Creamos un scraper en Google Collaboratory a partir de un crawler con scrapy y descargamos los datos a un archivo estructurado.

Se han invertido muchísimas horas en intentarlo y al final los resultados no han sido los esperados. Dejo esta parte para el final. No quiero encallarme en este punto y no poder entregar el resto de práctica.
	
## 2.1 Descarga de dataset de Airbnb.
* Procedemos a la descarga del dataset de airbnb.csv con datos estructurados.
* Aplicamos filtros de limpieza de datos para obtener un dataset más fiable y que no nos provoque problemas. Para ello hemos abierto el bloc de notas para hacer una primera inspección y posteriormente hemos utilizado Trifacta Wrangler para limpiar los datos y evitar inconsistencias, definiendo reglas para ello.

![insertar imagen Trifacta](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/images/Trifacta01.png)

## 3. Montar infraestructura de Cluster para el proyecto.
Vamos a utilizar Google Cloud Platform para montar un cluster de 3 contenedores configurados para alojar Hadoop y sus complementos.
Nos apoyaremos, a nivel de almacenamiento, con Google Cloud Storage.
Procedemos al montaje y detallamos los pasos con las siguientes imágenes y comentarios.

* Creamos el cluster en Google Cloud Plataform

![insertar imagen GCP](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/images/CP02.png)

![insertar imagen GCP](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/images/CP04.png)

* En el cluster hemos creado una máquina máster y dos esclavas. Para poder acceder a la master por SSH, podemos abrir un terminal directo de Google para trabajar bajo el entorno de navegador.

![insertar imagen GCP](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/images/CP05.png)

* Una vez montado el cluster, Google nos da unas máquinas preparadas y con todos los servicios que necesitamos par nuestra infraestructura.

## 4. Storage y proceso.

* A continuación, vamos a revisar si Google nos ha montado una unidad de almacenamiento llamada Google Storage y vamos a configurarla.

* El Google Storage lo utilizaremos como lugar de almacenamiento de nuestros datasets y otros ficheros. Con ello evitamos utilizar el HDFS de forma directa (internamente se utiliza) siendo su rendimiento parecido o igual. Otra gran ventaja es, en el momento que destruyamos nuestro cluster, esta unidad no se borra y esto es muy útil si queremos volver a crear otro cluster o acceder a sus datos.

![insertar imagen GCP](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/images/CP06.png)

* Ahora dentro de Google Storage, vamos a crear los directorios que utilizaremos para recoger datasets y depositar resultados. 

* Los directorios a crear, son los que se detallan en el Diagrama, en el área de G. Storage.

![insertar imagen GCP](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/images/CP07.png)

* Una vez montada la infraestructura de Cloud, procederemos a subir los Datasets/ficheros que previamente hemos obtenido por diferentes medios y que hemos limpiado con bloc de notas y/o Trifacta. Cada uno subirá a su carpeta correspondiente, como hemos indicado en el punto anterior, haciéndolo de forma automática (Staging). Utilizaremos un jar que habremos desarrollado con Spark/Scala utilizando y lo programaremos en un job de Hadoop. Utilizaríamos Straming para procesar los diferentes ficheros y posiblemente utilizaríamos Kafka para casos muy concretos, pero no en este proyecto.

Para este desarrollo, lo moveremos todo, de forma manual a los diferentes repositorios de ficheros/datos

* Cambiar siguiente imagen a proyecto de Spark/Scala.

![insertar imagen GCP](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/images/CP08.png)

* En este punto ya tenemos nuestros datos subidos al cluster para poder ser procesados.

* Una vez tenemos nuestros datos subidos, procederemos a hacer un estudio de los datos que nos son necesarios y haremos una limpieza general de datos e inconsistencias. Este proceso lo realizaremos con Python a través de notebooks.

* Inicialmente empezamos a tratar el fichero de airbnb completo de 247 MB y desistimos después de tres jornadas intentando hacer una importación decente para este proyecto, al final nos hemos decidido por el fichero reducido de 60,4 MB.

Añadir link al proyecto de limpieza de datos 
![insertar imagen GCP]()

 
 ## 4. Hive
 
 * Por último, vamos a utilizar Hive como base para almacenar y analizar los datos del proyecto.
 
 * Seguimos trabajando con el cluster de Google cloud que tenemos creado. Hive es una de las piezas que viene ya instalada por defecto y solo tenemos que empezar a utilizarla.
 
 * En primer lugar, abrimos la ventana del CLI que nos facilita Google en su insfraestructura.
 
![insertar imagen GCP](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/images/CP05.png)

 * A continuación, utilizaremos el Beeline para ejecutar los comandos de creación, carga y consulta de datos.

![insertar imagen GCP](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/images/CP16.png)

![insertar imagen GCP](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/images/CP17.png)

 * Una vez creada la tabla, insertamos los datos que obtendremos del dataset de airbnb, que se encuentra en Google Storage de nuestra infraestructura.
 
 * Una vez ejecutado el proceso, verificaremos que los datos hayan sido cargados correctamente.
 
 ![insertar imagen GCP](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/images/CP18.png)
 
 * Ya tenemos todos los datos disponibles para realizar consultas y cálculos.

 * Una vez con los datos ya preparados, antes de aplicar ningún algoritmo de IA, procedemos a representar gráficamente los datos obtenidos, para poder hacer un análisis de situación y situarnos en el escenario.
 
 * Para ello hemos utilizado dos formas de representación, la primera basada en la herramienta de pago denominada Tableau y una segunda, totalmente gratuita, basada en javascript, librería D3, node y con la ventaja de que se puede visualizar a través de un navegador web sin la adquisición de ninguna licencia.

## 5. Tableau
[Proyecto de Tableau ](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/Tableau/README_Tableau.md)
 
## 6. D3
[Proyecto de D3](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/D3/README.md)
 
 * En este punto, ya podemos tener una idea clara de cuales son los precios medios que tienen los inmuebles en cada barrio de Madrid, tanto para el alquiler como para su compra. 
 
 ## 7. IA
 
 * Ahora queremos ir un poco más lejos y nos gustaría predecir los precios de alquiler que deberían tener los diferentes inmuebles que podamos encontrar en venta, teniendo en cuenta sus diferentes características y situación. Nuestro objetivo es precisar cuales de ellos saldrían más rentables para invertir y proceder a su compra.

![Proyecto Machine Learning](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/MachineLearning.md)

 * Es el momento de epezar generar nuestros algoritmos y realizar un estudio para determinar que tipo de tecnología IA deberemos aplicar para poder hacer unas predicciones de precios correctas para nuestro cliente.

![Notebook MachineLearning](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/MachineLearningPT_V4.ipynb)


![insertar proyecto de ML DL MLP]()


* Con todo ello ya hemos conseguido obtener los datos deseados para ser procesados y enviados por correo a nuestro cliente.

* Para el envío de correos, utilizaremos la plataforma de MailChimp.

* Según recibamos la respuesta del api de MailChimp, guardaremos un registro/objeto en una BBDD Mongo para su posterior explotación.

* Escogemos MailChimp por que nos permite interactuar con su api de forma muy comoda. Es un sistema que lleva tiempo funcionando, es fiable y nos aporta información del estado de los correos.

 * Una vez generados nuestros modelos, los aplicaremos a los nuevos datos que vayamos incorporando diariamente. El resultado lo dejaremos en Google Storage en la carpeta /output y quedará todo listo para generar el correo electrónico y enviarlo a nuestros clientes con el resultado.
 
 
 
 



