# Procesamiento de Datos de Uber

Este repositorio contiene un script de Python que se utiliza para procesar datos de Uber. El script realiza diversas operaciones para limpiar y transformar los datos antes de cargarlos en una tabla de hechos y dimensiones. A continuación, se describe el proceso paso a paso:

## 1. Cargar los datos

El código utiliza la biblioteca pandas para cargar los datos desde un archivo CSV llamado "uber_data.csv" en un DataFrame de pandas llamado "df".

```python
import pandas as pd
df = pd.read_csv("./data/uber_data.csv")
```

## 2. Limpieza de datos
Se realizan varias operaciones de limpieza de datos, que incluyen:

Conversión de las columnas de fecha y hora a objetos de tipo datetime.
Eliminación de duplicados en el DataFrame.
Asignación de un identificador único a cada viaje.
```python
# Conversión de fechas y horas
df['tpep_pickup_datetime'] = pd.to_datetime(df['tpep_pickup_datetime'])
df['tpep_dropoff_datetime'] = pd.to_datetime(df['tpep_dropoff_datetime'])

# Eliminación de duplicados
df = df drop_duplicates().reset_index(drop=True)

# Asignación de un identificador único a cada viaje
df['trip_id'] = df.index
```


## 3. Creación de dimensiones
Se crean dimensiones para las siguientes variables:

- Fecha y hora de recogida y entrega.
- Número de pasajeros.
- Distancia del viaje.
- Tipo de tarifa.
- Tipo de pago.
- Ubicación de recogida.
- Ubicación de entrega.
- Estas dimensiones se almacenan en DataFrames separados.


## 4. Tabla de hechos
Se crea una tabla de hechos que combina todas las dimensiones y las métricas relevantes del viaje, como el monto total del viaje, propina, impuestos, etc.
```python
fact_table = df.merge(passenger_count_dim, left_on='trip_id', right_on='passenger_count_id') \
             .merge(trip_distance_dim, left_on='trip_id', right_on='trip_distance_id') \
             .merge(rate_code_dim, left_on='trip_id', right_on='rate_code_id') \
             .merge(pickup_location_dim, left_on='trip_id', right_on='pickup_location_id') \
             .merge(dropoff_location_dim, left_on='trip_id', right_on='dropoff_location_id')\
             .merge(datetime_dim, left_on='trip_id', right_on='datetime_id') \
             .merge(payment_type_dim, left_on='trip_id', right_on='payment_type_id') \
             [['trip_id','VendorID', 'datetime_id', 'passenger_count_id',
               'trip_distance_id', 'rate_code_id', 'store_and_fwd_flag', 'pickup_location_id', 'dropoff_location_id',
               'payment_type_id', 'fare_amount', 'extra', 'mta_tax', 'tip_amount', 'tolls_amount',
               'improvement_surcharge', 'total_amount']]
```
Este DataFrame representa la tabla de hechos que puede ser utilizada para análisis posteriores.

## 5. Dimensiones
Las dimensiones creadas anteriormente también se pueden utilizar para realizar análisis específicos en función de diferentes atributos, como la fecha y hora, el número de pasajeros, la distancia del viaje, etc.


