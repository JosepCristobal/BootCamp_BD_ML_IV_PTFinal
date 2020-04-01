## Data Visualización (Tableau)


[Volver principal](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/BootCamp%20IV%20proyecto%20final.md)

## Descripción y objetivos de nuestro proyecto.

Una Empresa que gestiona un fondo de capital riesgo está interesada en adquirir inmuebles, en la zona de Madrid centro. Inicialmente quiere dedicarlos al alquiler.
Nos han encargado un estudio para determinar cual puede ser la mejor zona/barrio para invertir y si les es más rentable el alquilarlos como vivienda o bien como apartementos/casas turísticas. 

Nuestro objetivo es el de comparar los precios de venta y los diferentes tipos de alquiler en la zona de Madrid centro.

Los KPIs en los que nos vamos a basar para hacer el estudio, serán los precios por metro cuadrado / mes, tanto de compra, como de alquiler.


## Ejecución del proyecto

En nuestro proyecto hemos utilizado Tableau para diseñar nuestro Dashboard.

En primer lugar creamos y definimos nuestra fuente de datos.

1. 	Hemos utilizado como base, el fichero airbnb_clean.csv, copiado al directorio de ./Tableau y renombrado a Airbnb-listings Madrid.csv
	Le hemos cambiado la configuración regional a Inglés(Reino Unido) para que nos respete los separadores de miles y decimales de nuestro fichero.
	
2. A continuación, hemos subido el fichero de Nveces_alquilado.csv y hemos creado una relación normal con el fichero anterior. Este fichero es ficticio y lo hemos utilizado para este estudio y crear una variable más que nos puede ayudar en nuestras decisiones.

3. A través de la web de Idealista, hemos adquirido los datos referentes a los precios de venta y alquiler de inmuebles, en el centro de Madrid y clasificados por zonas. Se han generado dos ficheros csv nuevos, preciosAlquiler.csv y precioVenta.csv.

4. Se han creado las relaciones correspondientes de estos dos ficheros con el de airbnb-listings Madrid.csv.

5. Una vez vinculada la información de los cuatro ficheros, hemos procedido ha ocultar campos innecesarios y a repasar la tipología de los campos clave para el proyecto.

6. En la fuente de datos, para optimizar las consultas y ajustarnos a nuestros objetivos, hemos creado tres filtros, de esta forma la cantidad de datos en su representación, será la menor y más optimizada posible.

7. Los filtros creados, en base a que sólo queremos comprar inmuebles, son:
	* 	Pies cuadrados. intervalo de 220 a 3000.
	*  Tipos de propiedad, solo pisos, casas, ...
	*  Ciudad, solo Madrdid.

8. Hemos creado varios campos calculados para poder representar los datos.

	*  En nuestro proyecto vamos a trabajar con la medida de metros cuadrados. Los datos nos vienen en feet (pies) y los hemos transformado a metros, aplicando el factor de conversión de 1 pie2 = 0,092903 m2. El campo calculado es "Metros2"
	*  Los precios del alquiler por metro 2, se suelen calcular cogiendo como referencia el precio del inmueble aplicandole una rentabilidad del 4% y amortizado a 25 años o 300 meses. Con esta formula, nosotros hemos hecho un campo calculado, denominado "AlquilerRecomendado".
	*  Los precios de Airbnb vinen calculados por día, hemos creado un campo nuevo denominado "MediaM2_Airbnb_Mes", para tener el precio por mes ya calculado.

9. Una vez preparada toda la fuente de datos, se empieza a desarrollar las diferentes hojas para hacer la composición de Dasboard final.
	* Se ha creado 4 paneles para representar todos los KPIs necesarios para poder tomar la mejor decisión a la hora de adquirir los inmuebles.
	* En el primer panel podemos ver y comparar el precio recomendado de alquiler (calculado a través del precio de venta) y el precio real de alquiler, según datos de idealista y clasificado por zonas. Tambien añadimos el total de viviendas y las veces que han sido alquiladas.
	* Cualquier pulsación en este panel, hará de filtro para los demás.
	* En el panel "Compara alquiler y venta, podremos encontrar los tres precios de alquiler por mes más el precio de venta por m2.
	* En el panel situado en la parte superior derecha, encontraremo el total de viviendas clasificadas por zona y tipo
	* En la parte inferior, tenemos una representación de datos situados en un mapa. Hemos introducido una ventana emergente que nos muestra los datos en formato texto y hemos incrurstado un diagrama de anillo.


## Conclusión final
Esperamos que nuestro cliente le sea más fácil la toma de decisiones a través de la información facilitada y representada en este estuduio.
	
 
[Volver principal](https://github.com/JosepCristobal/BootCamp_BD_ML_IV_PTFinal/blob/master/BootCamp%20IV%20proyecto%20final.md)
