## Аналитика пассажиропотока транспортной компании

Основой для этого проекта послужило задание из программы переквалификации «Аналитик данных», изначально рассчитанное на групповое выполнение. Поскольку работа над заданием велась совместно с другими участниками, я считаю неэтичным публиковать общие результаты. Вместо этого я адаптировала концепцию проекта: проанализировала исходные требования, полностью переработала методику исследования и на их основе создала независимый аналитический кейс, отражающий мой персональный подход к решению задачи.

## Задание
Часть 1: Необходимо выбрать город для дальнейшего анализа, ответить на вопросы и проверить гипотезы:
1. Как изменялось количество пассажиров по годам?
2. Из каких городов приехало наибольшее количество пассажиров за последние 5 лет?
3. Существует ли сезонность в перевозках?
4. Какие перевозчики занимают наибольшую долю в 2018 году?
5. Проверьте гипотезу – «Чем выше средняя дальность перевозки, тем выше доход от услуг»
6. Какая доходность от услуг у различных типов вагонов в 2018?
7. Как менялся средний чек по месяцам за последние 2 года?

Часть 2: Создать 2 дашборда с визуализацией основных показателей.

## Инструменты
- Для анализа необходимо извлечь данные из SQL. 
- Для визуализации будет использован Power Bi.

## Реализация
Анализируемый город – Санкт-Петербург.    

<div align="center">
      
**Вопрос 1 - Как изменялось количество пассажиров по годам?**  
</div>

**Запрос SQL:**        
SELECT year, SUM(passangers) as pass_count      
FROM transportation         
WHERE arrival_id = 2       
GROUP BY year         
ORDER BY year      
**Результат запроса:**     
|year	|pass_count|
|------|--------|
|2009	|5449263|
|2010	|5482777|
|2011	|5767110|
|2012	|6035507|
|...|...|

**Визуализация**

<img src="https://github.com/user-attachments/assets/649ee4c6-7e8b-417b-b601-64e073f729dd" width="400"  />

**Ответ:** Количество пассажиров за рассматриваемый период росло.

<div align="center">

**Вопрос 2 - Из каких городов приехало наибольшее количество пассажиров за последние 5 лет?**
</div>

**Запрос SQL:**      
SELECT city_name, SUM(passangers) as 'sum_pass'       
FROM transportation          
INNER JOIN cities ON cities.city_id = transportation.departure_id         
WHERE arrival_id = 2 and year BETWEEN 2013 and 2018         
GROUP BY city_name       
ORDER BY sum_pass DESC      
**Результат запроса:**  
|city_name	|sum_pass|
|---|---|
|Москва	|24617069|
|Петрозаводск	|1634680|
|Тверь	|935233|
|Нижний |Новгород	|741130|
|...|...|

**Визуализация**

<img src="https://github.com/user-attachments/assets/485f8a30-2119-4299-bbf7-9dad11cf7e86" width="400"  />

**Ответ:** Наибольшее количество пассажиров приехало из Москвы.

<div align="center">

**Вопрос 3 - Существует ли сезонность в перевозках?**
</div>

**Запрос SQL:**          
SELECT month, SUM(passangers) as 'sum_pass'        
FROM transportation          
WHERE arrival_id = 2    
GROUP BY month       
ORDER BY sum_pass DESC             
Результат запроса:
|month	|sum_pass|
|---|---|
|Август	|7067213|
|Июль	|6827500|
|Июнь	|6355911|
|Май	|5454357|
|...|...|

**Визуализация**

<img src="https://github.com/user-attachments/assets/2aa36323-c4fe-4e23-ad0e-512749fcf4d3" width="400"  />

**Ответ:** Да, так как мы рассматривали данные за все года, то можно наглядно увидеть, что разница между сумма некоторых месяцев значительно больше, чего не может быть при отсутствии сезонности. 

<div align="center">

**Вопрос 4 - Какие перевозчики занимают наибольшую долю в 2018 году?**
</div>

**Запрос SQL:**   
SELECT carrier_name, SUM(passangers) as 'sum_pass',                          
SUM(passangers) * 100 / (SELECT SUM(passangers) FROM transportation WHERE arrival_id = 2 and year = 2018) as 'rate'      
FROM transportation       
INNER JOIN carriers ON carriers.carrier_id = transportation.carrier_id                          
WHERE arrival_id = 2 and year = 2018                 
GROUP BY carrier_name              
ORDER BY sum_pass DESC             
**Результат запроса:**     
|carrier_name	|sum_pass	|rate|
|---|----|---|
|Туры по Экспрессным Линиям	|4869623	|63|
|Поездки на Паровозах	|2604344	|33|
|Проход по Железным Дорогам	|132045	|1|
|Скорые Экспедиции по Железу	|73582	|0|
|...|...|...|

**Визуализация**

<img src="https://github.com/user-attachments/assets/feea6785-f1ce-498f-ae2b-8e102ac2aa83" width="200"  />

**Ответ:** Основными перевозчиками в 2018 году являются:       
•	Туры по Экспрессным Линиям – 63%       
•	Поездки на Паровозах – 33%          
•	Проход по Железным Дорогам – 1%            

<div align="center">

**Вопрос 5 - Проверьте гипотезу – «Чем выше средняя дальность перевозки, тем выше доход от услуг»**
</div>

**Запрос SQL:**   
SELECT city_name,                  
SUM(passanger_turnover)  as 'sum_turover',              
SUM(passangers)  as 'sum_pass',                       
SUM(passanger_turnover) / SUM(passangers) as 'average_dist',              
SUM(service_revenue) / SUM(passangers) as 'service_prof'                     
FROM transportation                
INNER JOIN cities ON cities.city_id = transportation.departure_id                   
WHERE arrival_id = 2               
GROUP BY city_name              
ORDER BY service_prof DESC             
**Результат запроса:**      
|city_name	|sum_turover	|sum_pass|	average_dist|	service_prof|
|----|----|-----|-----|-----|
|Пенза	|204825909	|138912	|1474	|224|
|Сочи	|486482448	|198791	|2447	|211|
|Рязань	|350540594	|402955	|869	|197|
|Москва	|24401648815	|37489882	|650	|189|
|...|...|...|...|...|

**Визуализация**

<img src="https://github.com/user-attachments/assets/ee6120db-887f-473c-b8fd-0c1ddbd3993a" width="400"  />

**Ответ:** Гипотеза неверна, так как она опровергнута в ходе анализа данных. На графике наглядно можно увидеть 10 городов с самой большой дальностью, однако средняя доходность не имеет прямой взаимосвязи с дальностью.

<div align="center">

**Вопрос 6 - Какая доходность от услуг у различных типов вагонов в 2018?**
</div>

**Запрос SQL:**  
SELECT type_name,         
SUM(passangers)  as 'sum_pass',                 
SUM(service_revenue) / SUM(passangers) as 'service_prof'              
FROM transportation               
INNER JOIN types ON types.type_id = transportation.type_id                       
WHERE arrival_id = 2 and year = 2018              
GROUP BY type_name              
ORDER BY service_prof DESC                 
**Результат запроса:**            
|type_name	|sum_pass	|service_prof|
|---|---|---|
|Мягкий	|14490	|2153|
|Люкс	|124470	|1123|
|СВ	|3053761	|324|
|Купе	|2149290	|290|
|...|...|...|

**Ответ:**
Доходность от услуг составила:     
•	Для тарифа «Мягкий»	2153               
•	Для тарифа «Люкс»	1123             
•	Для тарифа «СВ»		324             
•	Для тарифа «Купе»	290             
•	Для тарифа «Плацкарт»	132          

<div align="center">

**Вопрос 7 - Как менялся средний чек по месяцам за последние 2 года?**
</div>
       
**Запрос SQL:**   
SELECT year, month, 
SUM(transportation_revenue+commercial_fee+service_revenue) / SUM(passangers) as 'total_revenue',               
SUM(passangers) as 'total_pass',    
SUM(transportation_revenue+commercial_fee+service_revenue) / SUM(passangers) as 'average_check'              
FROM transportation                      
WHERE arrival_id = 2 and year BETWEEN 2017 and 2018                  
GROUP BY year, month, month_number              
ORDER BY year, month_number         
**Результат запроса:**
|year	|month	|total_revenue	|total_pass	average_check|
|---|---|---|---|
|2017	|Январь	|2522	|502924	|2522|
|2017	|Февраль	|2597	|393240	|2597|
|2017	|Март	|2480	|442201	|2480|
|2017	|Апрель	|2655	|490751	|2655|
|...|...|...|...|...|

**Ответ:** За последние 2 года средний чек вырос. Однако, в летний период 2018 года сумма была меньше значения 2017 года. 




















