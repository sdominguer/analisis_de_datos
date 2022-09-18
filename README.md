# Parte 1
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

```sql
select sum(value) as "PAGO TOTAL GENERACIÓN", id_user as "ID"
from generation
group by id_user
order by sum(value);
```

- #### ¿Cuál es la generación promedio mensual de cada usuario?
``` sql
select avg(value) as "GENERACION PROMEDIO MENSUAL", id_user as "ID"
from generation
group by id_user
order by avg(value);
```

- #### ¿Cuál es el promedio de los máximos mensuales de consumo de cada usuario?
``` sql
select avg(a.CONSUMO) as "MAXIMO", a.ID
from
(select id_user as "ID", max(value) as "CONSUMO", month(timestamp) as "MES"
from consumption
group by month(timestamp), id_user) as a
group by a.ID;
```

- #### ¿Cuál es la ciudad con el mayor consumo?
``` sql
select max(a.CONSUMPTION) as "MAX CONSUMPTION", a.CITY
from
(select cities.city_name as "CITY", sum(consumption.value) as "CONSUMPTION"
from cities
inner join consumption on
cities.id_user=consumption.id_user
group by city_name
order by value) as a;
```
