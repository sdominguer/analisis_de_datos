# Taller Modelación Y Simulación 1
## Parte 1 
- ### ¿Cuál es el consumo, inyección y generación total de cada usuario?
#### Consumo
``` sql
select sum(value) as "PAGO TOTAL CONSUMO", id_user as "ID"
from consumption
group by id_user
order by sum(value);
```
|PAGO TOTAL CONSUMO | ID|
|-------------------|---| 
|62.185097581000065	|1281|
|322.28663868511984	|1310|
|325.5552420640003	|1285|
|386.3718154831197	|1492|
|387.8405311858003	|1286|
|814.5192051750003	|1294|
|1514.7299999330264	|1293|
|3033.6500000000156	|1290|
|8053.84800000002	|1239|
|16027.384999999973	|1257|

#### Inyección
``` sql
select sum(value) as "PAGO TOTAL INYECCIÓN", id_user as "ID"
from injection
group by id_user
order by sum(value);
```
|PAGO TOTAL INYECCIÓN |ID
|-------------------|---|
|3	|1310|
|4	|1492|
|56	|1293|
|171	|1290|

#### Generación
```sql
select sum(value) as "PAGO TOTAL GENERACIÓN", id_user as "ID"
from generation
group by id_user
order by sum(value);
```
|PAGO TOTAL GENERACIÓN |ID
|-------------------|---|
|519	|1309|
|645|	1493|
|2451	|1299|
|10598	|1298|

- ### ¿Cuál es la generación promedio mensual de cada usuario?
``` sql
select avg(value) as "GENERACION PROMEDIO MENSUAL", id_user as "ID"
from generation
group by id_user
order by avg(value);
```
|GENERACIÓN PROMEDIO MENSUAL| ID|
|-------------------|---|
|0.1458	|1309|
|0.1535	|1493|
|0.8431|	1299|
|1.8425	|1298|

- ### ¿Cuál es el promedio de los máximos mensuales de consumo de cada usuario?
``` sql
select avg(a.CONSUMO) as "MAXIMO", a.ID
from
(select id_user as "ID", max(value) as "CONSUMO", month(timestamp) as "MES"
from consumption
group by month(timestamp), id_user) as a
group by a.ID;
```

|MÁXIMO| ID|
|-------------------|---|
|6.87|	1290|
|0.3275471933333333	|1281|
|0.875074225|	1310|
|1.2421753|	1294|
|1.261489275	|1286|
|13.094249999999999	|1239|
|4.8225|	1293|
|21.1115	|1257|
|0.7409106425	|1285|
|0.6114518999999999|	1492|

- ### ¿Cuál es la ciudad con el mayor consumo?
``` sql
select max(a.CONSUMPTION) as "MAX CONSUMO", a.CITY
from
(select cities.city_name as "CITY", sum(consumption.value) as "CONSUMO"
from cities
inner join consumption on
cities.id_user=consumption.id_user
group by city_name
order by value) as a;
```

|MAX CONSUMO |PAÍS|
|-------------------|---|
|16415.22553118579|	New York|


## Parte 2
### Primero, importemos librerías y leamos los archivos csv 
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import rcParams
import seaborn as sns

cities=pd.read_csv(r"C:\Users\sarar\Downloads\cities.csv")
consumo=pd.read_csv(r"C:\Users\sarar\Downloads\consumption.csv")
generacion=pd.read_csv(r"C:\Users\sarar\Downloads\generation.csv")
inyeccion=pd.read_csv(r"C:\Users\sarar\Downloads\injection.csv")
```
- ### ¿Qué insights les dirías a los clientes sobre su consumo de energía?
#### Dimensiones de nuestro dataset
``` python
print(f"Dimensiones del dataset citie: {cities.shape}")
print(f"Dimensiones del dataset consumo: {consumo.shape}")
print(f"Dimensiones del dataset generacion: {generacion.shape}")
print(f"Dimensiones del dataset inyeccion: {inyeccion.shape}")
```
#### Salida:
```Dimensiones del dataset citie: (10, 3)
Dimensiones del dataset consumo: (26174, 3)
Dimensiones del dataset generacion: (16419, 3)
Dimensiones del dataset inyeccion: (15493, 3)```

#### Descripción de nuestro dataset (estadísticas, tendencias, resumenes)
``` python
print(consumo.describe())
print(generacion.describe())
print(inyeccion.describe())
``` 
#### Salida:
|      |     id_user  |       value|             
|------|--------------|------------|
|count | 26174.000000 | 26174.000000|
|mean  |  1302.380225 |     1.181645|
|std   |    66.251318 |     2.516065|
|min    | 1239.000000 |     0.000000|
|25%    | 1281.000000 |     0.078000|
|50%    | 1290.000000 |     0.220000|
|75%    | 1294.000000  |    0.690000|
|max   |  1492.000000  |   24.828000|

|      |     id_user  |       value|
|------|--------------|------------|
|count | 16419.000000 | 16419.000000|
|mean  |  1350.454534  |    0.896454|
|std   |    83.692953  |    1.473224|
|min   |  1298.000000   |   0.000000|
|25%   |  1298.000000  |    0.000000|
|50%   |  1299.000000  |    0.214355|
|75%  |   1493.000000  |    0.940000|
|max  |   1493.000000  |    7.140000|

|           |id_user |        value| 
|------|--------------|------------|
|count  |15493.000000 | 15493.000000|
|mean   | 1346.388950  |    0.325583|
|std    |   84.815757  |    0.819484|
|min   |  1290.000000  |    0.000000|
|25%   |  1290.000000  |    0.000000|
|50%   |  1293.000000  |    0.000000|
|75%   |  1492.000000  |    0.202000|
|max   |  1492.000000  |    6.480000|

#### Información más resumida de nuestro dataset
``` python
print(consumo.info())
print(generacion.info())
print(inyeccion.info())
```
#### Salida:
|#   |Column    | Non-Null Count | Dtype|
|--- | ------   |  --------------|  -----|
| 0  | id_user  |  26174 non-null|  int64|
| 1  | timestamp|  26174 non-null | object|
| 2  | value    |  26174 non-null|  float64|

|#   |Column    | Non-Null Count | Dtype|
|--- | ------   |  --------------|  -----|
| 0  | id_user  |  16419 non-null|  int64|
| 1  | timestamp | 16419 non-null | object|
| 2  | value    |  16419 non-null | float64|

| #  | Column   |  Non-Null Count | Dtype|
|--- | ------   |  -------------- | -----|
| 0  | id_user  |  15493 non-null | int64|
| 1  | timestamp | 15493 non-null | object|
| 2  | value    |  15493 non-null | float64|


#### Saber cuántos valores del mismo tipo hay 
``` python
print(cities.value_counts())
print(consumo.value_counts())
print(generacion.value_counts())
print(inyeccion.value_counts())
``` 
#### Salida: 
```
id_user  id_city  city_name
1239     12653    Seoul        1
1257     12651    New York     1
1281     12635    Prague       1
1285     12635    Prague       1
1286     12651    New York     1
1290     12612    Paris        1
1293     12612    Paris        1
1294     12635    Prague       1
1310     12635    Prague       1
1492     12635    Prague       1
------------------------------------
id_user  timestamp                   value   
1239     2022-03-01 00:00:00.000000  0.322000    1
1293     2022-06-04 12:00:00.000000  0.190000    1
         2022-06-04 22:00:00.000000  0.460000    1
                        ....                 
1285     2022-06-03 08:00:00.000000  0.041748    1
         2022-06-03 07:00:00.000000  0.176025    1
------------------------------------
id_user  timestamp                   value
1298     2022-01-01 05:00:00.000000  0.000    2
         2022-04-16 07:00:00.000000  0.190    2
         2022-04-14 13:00:00.000000  4.560    2
                     ...
1309     2022-02-04 19:00:00.000000  0.001    1
         2022-02-04 20:00:00.000000  0.000    1
         2022-02-04 21:00:00.000000  0.000    1
         2022-02-04 22:00:00.000000  0.000    1
------------------------------------
id_user  timestamp                   value
1293     2022-04-14 23:00:00.000000  0.00     2
1290     2022-01-01 00:00:00.000000  0.00     1
                    ...
1293     2022-02-06 11:00:00.000000  0.40     1
         2022-02-06 12:00:00.000000  0.71     1
         2022-02-06 13:00:00.000000  1.96     1
```

#### Graficando los datos anteriores
``` python
consumo.id_user.value_counts().plot(kind="bar", color="pink")
plt.title("Value of users consumption")
plt.xlabel('User ID') 
plt.ylabel('Consumption')
plt.show()

generacion.id_user.value_counts().plot(kind="bar", color="pink")
plt.title("Value of users generation")
plt.xlabel('User ID') 
plt.ylabel('Generation')
plt.show()

inyeccion.id_user.value_counts().plot(kind="bar", color="pink")
plt.title("Value of users inyection")
plt.xlabel('User ID') 
plt.ylabel('Inyection')
plt.show()
``` 

#### Salida:
![img](https://user-images.githubusercontent.com/88257827/191152264-9908a832-4048-4402-9b1c-49cd5227a7f2.png)
![Figure_2](https://user-images.githubusercontent.com/88257827/193512521-6b570eae-9ad8-4da2-8c55-1278dec0f043.png)
![Figure_3](https://user-images.githubusercontent.com/88257827/193512650-e4bfb2d4-04fa-4d1f-94bb-b479ca747a90.png)

#### Por último, calculemos la oblicuidad (asimetría) y curtosis de nuestra línea
``` python
print(f"Skewness: {consumo['value'].skew()}")
print(f"Kurtosis: {consumo['value'].kurt()}")

print(f"Skewness: {generacion['value'].skew()}")
print(f"Kurtosis: {generacion['value'].kurt()}")

print(f"Skewness: {inyeccion['value'].skew()}")
print(f"Kurtosis: {inyeccion['value'].kurt()}")
``` 
#### Salida:
```
Skewness: 3.550503501918619
Kurtosis: 14.713459148756845

Skewness: 2.084752545724789
Kurtosis: 3.7189331017390366

Skewness: 3.5732681373874864
Kurtosis: 14.010812558407391
```

### Conclusión: 
- La mayoría de usuarios tiene un consumo total mayor a 3000.
- En horas de la noche hay mayor consumo, mientras que en horas de la madrugada en muchos casos es de 0.
- Aunque parezca raro, también hay consumo de energía en el día, quizás no sea por iluminación sino por aparatos electrónicos conectados.
- No hay una desviación estándar muy alta entre el total de consumo de los usuarios, por lo que los valores están cerca a la media, lo que inddica que los usuarios están consumiendo cantidades de energía similares.

- ### ¿Qué acciones pueden tomar los clientes para reducir su consumo de energía?
   - Dormirse más temprano para aprovechar más la luz natural.
   - Utilizar electrodomésticos y equipos electrónicos de consumo eficiente.
   - Apagar las luces cuando no estén haciendo uso de ellas y desconectar aparatos electrónicos si no se están usando.
   - Desconectar dispositivos electrónicos cuando ya no necesiten usarlos.

- ### ¿Qué insights le darías a los comercializadores de energía respectivos?
   - Que su mayor ganancia está en la ciudad de Nueva York.
   - Que en países con estaciones puede haber menos consumo energético, debido a que oscurece mucho mas tarde en algunos casos y habrá más aprovechamiento de luz        natural.
