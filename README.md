# Taller Modelación Y Simulación 1
### Parte 1
- #### ¿Cuál es el consumo, inyección y generación total de cada usuario?
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

- #### ¿Cuál es la generación promedio mensual de cada usuario?
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

- #### ¿Cuál es el promedio de los máximos mensuales de consumo de cada usuario?
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

- #### ¿Cuál es la ciudad con el mayor consumo?
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
