## BootCamp IV proyecto final

### Realizado por Josep Cristobal. 
### Entrega el  19/03/2020 aplazada al día 02/04/2020.

En nuestro proyecto vamos a entregar un Mínimo Producto Viable (MVP).
Lo usaremos para probar rápidamente de manera cuantitativa y cualitativa la respuesta de nuestro cliente al proyecto y a la funcionalidad del mismo. 
Nos permitirá comprobar que efectivamente el producto resuelva una necesidad del cliente antes de tener que invertir demasiados recursos en su desarrollo.

## Escenario

Partimos  del supuesto que tenemos una empresa/consultoría que se dedica a gestionar y coordinar proyectos que se basan en el tratamiento y análisis de datos, pudiendo abarcar proyectos sencillos que mueven cantidades pequeñas de datos, como proyectos de gran envergadura con un manejo de gran cantidad de datos.
Hemos recibido un encargo, por parte de un grupo inversor (Capital Ganso, S.A.) que empieza a operar en nuestro pais. Nos ha pedido que le ayudemos en la tarea de toma de decisiones a la hora de invertir en la compra de inmuebles para dedicarlos al alquiler. Al ser recien llegados a nuestro mercado, quieren centrarse en Madrid y expandirse a otras ciudades en un futuro.

## El Proyecto

. El objetivo de este proyecto es montar un servicio para la empresa que hemos  mencionado que pretende adquirir viviendas, preferentemente pisos, para dedicarlos al alquiler vacacional. También quiere adquirir locales comerciales para alquilarlos, pero con contratos de más larga duración y siempre en zonas donde se pueda mover el turismo que suele alquilar pisos para sus estancias.

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

Añadimos acceso al proyecto de limpieza de datos 
[Limpieza adicional](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/CleanDataset.ipynb)

 
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

 * En este proyecto trabajaremos con ficheros csv para el análisis de datos. La información para generar estos ficheros la extraeremos de Hive y compondremos el csv mencionado.
 
 * Una vez con los datos ya preparados, antes de aplicar ningún algoritmo de IA, procedemos a representar gráficamente los datos obtenidos, para poder hacer un análisis de situación y visualizar el escenario.
 
 * Para ello hemos utilizado dos formas de representación, la primera basada en la herramienta de pago denominada Tableau y una segunda, totalmente gratuita, basada en javascript, librería D3, node y con la ventaja de que se puede visualizar a través de un navegador web sin la adquisición de ninguna licencia.

## 5. Tableau
[Proyecto de Tableau ](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/Tableau/README_Tableau.md)
 
## 6. D3
[Proyecto de D3](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/D3/README.md)
 
 * En este punto, ya podemos tener una idea clara de cuales son los precios medios que tienen los inmuebles en cada barrio de Madrid, tanto para el alquiler como para su compra. 
 
 ## 7. IA
 
 * Ahora queremos ir un poco más lejos y nos gustaría predecir los precios de alquiler que deberían tener los diferentes inmuebles que podamos encontrar en venta, teniendo en cuenta sus diferentes características y situación. Nuestro objetivo es precisar cuales de ellos saldrían más rentables para invertir y proceder a su compra.

[Proyecto Machine Learning](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/MachineLearning.md)

 * Es el momento de epezar generar nuestros algoritmos y realizar un estudio para determinar que tipo de tecnología IA deberemos aplicar para poder hacer unas predicciones de precios correctas para nuestro cliente.

[Notebook MachineLearning](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/MachineLearningPT_V4.ipynb)

 * Acceso a Deep Learning
 
 [Proyecto Deep Learning](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/DeepLearning/DeepLearning.md)
 
 [Notebook DeepLearning](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/DeepLearning/DeepLearningPT_Final.ipynb)
 
* Hemos aplicado dos tecnologías de IA para hacer una predicción de precios de alquiler de pisos de la zona de Madrid.
	- Aplicando técnicas de Machine Learnig hemos conseguido unos resultados no muy buenos. Aplicando SVM en regresión ha sido donde mejores resultados hemos obtenido, con un resultado de "Acc (TEST): 0.60"
	
	- Aplicando técnicas de Deep Learning hemos conseguidos unos resultados no muy buenos, pero mejores que los obtenidos con Machine Learning: "mean_absolute_error: 18.7502"
	
* A vistas de los resultados obtenidos, nos decantamos por Deep Learnig para hacer las predicciones de precios del alquiler de pisos según sus características, aplicando los algoritmos de los modelos obtenidos.

* Nuestros mejores modelos han sido guardados en formato .h5 para deep learning y en formato .sav usando joblib para machine learning. Estos modelos podrán ser cargados en cualquier momento y realizar las predicciones oportunas. Se han realizado pruebas de guardado, carga y predicción en el mismo NoteBook de de los proyectos de deep learnig y machine learning.

* Con todo ello ya hemos conseguido obtener los datos deseados para procesarlos y calcular que 10 mejores inmuebles podemos recomendar a nuestro cliente.

* Para hacer el cáculo utilizaremos la formula de idealista referente al precio del alquiler de los pisos.

	- Los precios del alquiler por metro2, se suelen calcular cogiendo como referencia el precio del inmueble aplicandole una rentabilidad del 4% y amortizado a 25 años o 300 meses. Con esta formula, nosotros deberemos hacer un campo calculado, denominado "AlquilerRecomendado" en nuestro dataset. ([PrecioVenta/M2]/300) Aqui obtendremos el precio del alquiler por metro cuadrado recomendado, según el precio de la vivienda a adquirir, por mes.
	- Los precios de Airbnb vinen calculados por día y nuestro algoritmo de deep learning nos dará su predicción por dia, por este motivo hemos creado un nuevo cálculo para tener el precio por mes. ([Price]/round([Metros2])) * 30. Con este cálculo obtenemos el precio del alquiler mensual, basándonos en las predicciones que nos ha dado nuestro algoritmo. El precio será el resultado de aplicar nuestro modelo a la vivienda que está en venta y lo dividimos por los metros cuadrados de la misma. 
	- Con los datos obtenidos ya sabemos el precio del alquiler mensual recomendado y el precio mensual según nuestro algoritmo.
	- Ahora solo nos queda calcular la diferencia entre alquiler predecible menos alquiler recomendado. Haremos una lista ordenada descendentemente y seleccionaremos los 10 primeros para recomendar a nuestros clientes.
	
* Ahora, con todos los cálculos realizados, ya sabemos que diez inmuebles podremos recomendar para su compra, a nuestro cliente Capital Ganso, S.A.

* Los precios que se han calculado son precios por metro cuadrado. Para hacer la presentación a nuestro cliente, multiplicaremos los precios calculados por el total de metros cuadrados de cada vivienda.

* Ahora, con todos los cálculos realizados, ya sabemos que diez inmuebles podremos recomendar para su compra, a nuestro cliente Capital Ganso, S.A.

* Prepararemos un documento en formato HTML donde relacionaremos los 10 inmuebles más rentables para su alquiler, maquetándolo de forma que podamos mostrar toda la información más relevante del inmueble, incluidas fotos si las tenemos, y detallando el precio de alquiler recomendado, el precio de alquiler predictivo, el precio de compra del inmueble y el precio del alquiler predictivo multiplicado por 300 meses. De esta forma podrá verse la diferencia del precio de compra comparado con el precio que obtendremos del alquiler durante estos 300 meses. Somos optimistas y presuponemos que el inmueble va a estar siempre alquilado.

* Con todo ello ya hemos conseguido obtener un documento maquetado de forma elegante y fácil de entender y procedemos a enviarlo por correo a toda la lista de distribución que nos ha facilitado nuestro cliente.

* Para el envío de correos, utilizaremos la plataforma de MailChimp. Utilizaremos su api.

* Según recibamos la respuesta del api de MailChimp, guardaremos un registro/objeto en una BBDD Mongo para su posterior explotación.

* Escogemos MailChimp por que nos permite interactuar con su api de forma muy comoda. Es un sistema que lleva tiempo funcionando, es fiable y nos aporta información del estado de los correos.

 * Una vez generados nuestros modelos, los aplicaremos a los nuevos datos que vayamos incorporando diariamente. El resultado lo dejaremos en Google Storage en la carpeta /output y quedará todo listo para generar el correo electrónico y enviarlo a nuestros clientes con el resultado.
 
 Esperamos que con este MVP, a falta de automatizar todos los procesos y de dasarrollar la parte final de envío de los correos, nuestro cliente Capital Ganso, S.A., le queden cubiertas sus espectativas y nos compre el proyecto.
 
 
 PD. Gracias por las dos clases extras que se hicieron de arquitectura, pero no he llegado a tiempo de poder incorporar lo aprendido en estas dos jornadas.
 
 Hasta aquí he podido llegar con mi práctica, no he podido avanzar, pulir y perfeccionar más mi proyecto.
 Me hubiera gustado llegar más lejos, y la verdad es que en estos 9 meses he aprendido mucho de lo que no sabía y tengo unas puertas abiertas y una base para profundizar en muchas áreas del mundo de los datos.
 
 Mi agradecimiento a mi familia que ha aguantado conmigo la carga personal en horas y esfuerzos que supone hacer este Bootcamp.
 
 A todos mis compañeros de curso, con ellos, con su ayuda moral y de colaboración he conseguido llegar hasta aquí.
 
Y por último, mi agradecimiento a todos los profesores y responsables de este Bootcamp que se han esforzado para que todo saliera bien y su conocimiento llegara a nosotros. Nos han ayudado a entrar en un mundo nuevo, en un mundo desconocido para mi hace 9 meses.

Gracias a tod@s
 
 
 
 



