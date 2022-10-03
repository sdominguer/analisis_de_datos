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
print(f"Dimensiones del dataset consumo: {consumo.shape}")
```
#### Salida:
```Dimensiones del dataset consumo: (26174, 3)```

#### Descripción de nuestro dataset (estadísticas, tendencias, resumenes)
``` python
print(consumo.describe())
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

#### Información más resumida de nuestro dataset
``` python
print(consumo.info())
```
#### Salida:
|#   |Column    | Non-Null Count | Dtype|
|--- | ------   |  --------------|  -----|
| 0  | id_user  |  26174 non-null|  int64|
| 1  | timestamp|  26174 non-null | object|
| 2  | value    |  26174 non-null|  float64|

#### Saber cuántos valores del mismo tipo hay 
``` python
print(cities.value_counts())
``` 
#### Salida: 
```
0
```

#### Graficando los datos anteriores
``` python
consumo.id_user.value_counts().plot(kind="bar", color="pink")
plt.title("Value of users consumption")
plt.xlabel('User ID') 
plt.ylabel('Consumption')
plt.show()
``` 

#### Salida:
![img](https://user-images.githubusercontent.com/88257827/191152264-9908a832-4048-4402-9b1c-49cd5227a7f2.png)

#### Por último, calculemos la oblicuidad (asimetría) y curtosis de nuestra línea
``` python
print(f"Skewness: {consumo['value'].skew()}")
print(f"Kurtosis: {consumo['value'].kurt()}")
``` 
#### Salida:
```
Skewness: 3.550503501918619
Kurtosis: 14.713459148756845
```

### Conclusión: 
- La mayoría de usuarios tiene un consumo total mayor a 3000
- 

- ###  ¿Qué acciones pueden tomar los clientes para reducir su consumo de energía?
   - Dormirse más temprano para aprovechar más la luz natural.
   - Utilizar electrodomésticos y equipos electrónicos de consumo eficiente.
   - Apagar las luces cuando no estén haciendo uso de ellas y desconectar aparatos electrónicos si no se están usando.
   - 

- ¿Qué insights le darías a los comercializadores de energía respectivos?

