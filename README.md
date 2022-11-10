### PROYECTO INDIVIDUAL 3 - ANALISIS DE DATOS

## REPORTE DE CALIDAD Y DICCIONARIOS DE DATOS

# Objetivos

- Prepocesar los datos de los dataset provistos por la OACI para ingestarlos a una base de datos SQL.
- Cargar los datos desde la base de datos a la herramienta de visualización PowerBI.
- Investigar en profundidad los datos para encontrar patrones relacionados con la accidentalidad aérea.

# Archivos
- Preprocessing.ipynb: notebook con lectura, preprocesamiento y carga a base de datos de los dataset 'AccidentesAviones.csv' y 'cantidad_pasajeros.csv'
- Dashboard_oaci.pbix: archivo de power BI con el dashboard creado a partir de las tablas('accidentes' y 'passenger') cargadas de la base de datos 'oaci'.
- AccidentesAviones.csv: Dataset de accidentes provisto por la OACI
- cantidad_pasajeros.csv: Dataset de pasajeros transportados por pais y año obtenido del databank del banco mundial.
- col_country.csv: archivo auxiliar con la columna de paises creada usando el geolocalizador de geopy sobre la columna de lugar de accidente 'place', se utiliza para saltarse el paso de geolocalización ya que este es muy demorado.

# Definición de variables

- tabla accidentes
    - 'date': fecha del accidente.                              
    - 'hour': hora del accidente.
    - 'place': lugar del accidente.
    - 'country': pais del lugar del accidente.
    - 'operator': aerolinea operadora de la aeronave accidentada.
    - 'route': ruta o tipo de vuelo(ej: training) del accidente.
    - 'ac_type': tipo de la aeronave.
    - 'registration': código de registro del accidente.
    - 'all_aboard': cantidad total personas a bordo de la aeronave.
    - 'pass_aboard': cantidad de pasajeros a bordo de la aeronave.
    - 'crew_aboard': cantidad de tripulantes(personal de vuelo) de la aeronave.
    - 'total_fatalities': cantidad total de muertes de personas a bordo de la aeronave.
    - 'pass_fatalities':cantidad de muertes de pasajeros.
    - 'crew_fatalities':cantidad de muertes de tripulantes.
    - 'ground': cantidad de muertes de personas en tierra(no estaban a bordo).
    - 'summary': descripción de accidente.

- tabla passenger
    - 'country': pais
    - 'anio': año
    - 'passengers': cantidad de personas transportadas vía aérea.

# Calidad de los datos

- Cantidad de datos: 5008
- Cantidad de no nulos:   
    - date              5008    
    - hour              3498            
    - place             5003
    - country           4114         
    - operator          4998          
    - route             4246         
    - ac_type           4995       
    - registration      4736          
    - all_aboard        4991         
    - pass_aboard       4787          
    - crew_aboard       4789         
    - total_fatalities   5000         
    -  pass_fatalities   4773          
    - crew_fatalities   4773           
    - ground            4964         
    - summary           4949   

- ouliers: se encontraron un accidente con mas de 600 pasajeros abordo que correspondía a un accidente entre dos aviones grandes. En fallecidos en tierra se encontraron dos incidentes con mas de 2700 víctimas que correspondía a los atentados en new york en las torres gemelas en el 2001. debido a que que estos aoutliers si representaban la realidad no se realizó ninguna acción sobre estos.


 - Inconsistencias: 
    - operador: en operador de encontraron operadores con mas de un tipo de escritura ej: Military - Royal Air Force y Military -Royal Air Force. acción se unificaron los datos.
    - pass_aboard mayor que all_aboard: 4 filas, se aplicó all_aboard = pass_aboard + crew_aboard.
   
# Flujo de trabajo de preprocesamiento de datos.

- Carga de dataset de accidentes.
- Transformación de fecha y hora a formato datetime.
- Reasignación de nombres de columna.
- Conversión a mayúsculas de columnas de texto.
- Eliminación de columnas que no ofrecen información: 'flight_no', 'cn_ln'.
- Unificación de categorías escritas de varios modos: se definió un solo nombre para operadores escritos de diferentes maneras.
- Correción de fila con 'pass_aboard' > 'all_aboard'.
- Obtención del pais del lugar del accidente: con ayuda del geolocalizador de geopy se encontraron los paises donde ocurrieron el 82% de los accidentes, el 18% faltante se debe a nulos y a errores ortográficos que el geolocalizador es incapaz de reconocer(Ej: Minnisota en vez de Minnesota). es importante mencionar que debido a que este proceso toma alrededor de 45 min, se exportaron los resultados en el archivo 'cantidad_pasajeros.csv' para luego ser leidos y anexados al dataframe con el resto de datos de accidentes.
- Carga de dataset de pasajeros transportados por via aérea por año y pais, descargado del databank del banco mundial 
(https://datos.bancomundial.org/indicador/IS.AIR.PSGR?end=2020&name_desc=false&start=1970&view=chart&year=1974)
- Reorganización de la tabla de pasajeros para obtener solo tres columnas : country , anio, passengers, sin valores nulos.
- Cambios de nombre y formato de columnas de la tabla de pasajeros.
- Carga de dataframe a base de datos 'oaci' almacenada en el servidor local: se adicionan dos tablas 'accidentes' y 'passenger', utilizando la libreria sqlalchemy.


