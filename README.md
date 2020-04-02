# BootCamp_BD_ML_IV_PTFinal
Práctica final BootCamp Big Data and Machine Learning IV

## Intrucciones para la puesta en marcha del proyecto

* Descargar fichero airbnb-listings.csv del siguinete enlace y copiarlo en la carpeta ./Data

[Airbnb-listings](https://drive.google.com/file/d/10JJ6swnWr1qlut7nq84xwR3iraHsxUg3/view?usp=sharing)

* En segundo lugar, deberemos ejecutar el notebook que se encuentra en la raiz de proyecto, denominado CleanDataset.ipynb. Este notebook lo que nos aporta es una lipieza importante sobre el csv original que hemos descargado en el punto anterior. De esta proceso, obtendremos otro csv denominado airbnb_clean.csv que es el que utilizaremos a partir de ahora para nuestro estudio.

* Este fichero csv que hemos generado, lo deberemos copiar en diferentes directorios y en algunos casos renombrarlo, según el subproyecto. Esto se ha hecho de esta forma porque este MVP lo hemos desarrollado generalmente en local y no hemos llegado a hacer el despliegue y test en Google cloud.
  - Copiar y renombrar ./Data/airbnb_clean.csv a ./D3/Data/airbnb.csv
  - Copiar y renombrar ./Data/airbnb_clean.csv a ./Tableau/airbnb-listings Madrid.csv
  - Para el proyecto de Machine Learning, solo debemos ejecutar el notebook en el directorio donde está y conservar la jerarquía de directorios del proyecto, tal y como está.
  - Para el proyecto de Deep Learnig. deberemos ejecutarlo preferiblemente en Google Colab y en la casilla de carga de fichero, al pulsar, nos aparecerá un cuadro de diálogo donde deberemos escoger el fichero situado en la carpeta ./Data/airbnb_clean.csv .
