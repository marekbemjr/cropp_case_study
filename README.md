                                                              

# CHICAGO TAXI TRIPS 

Żródło:
https://console.cloud.google.com/marketplace/product/city-of-chicago-public-data/chicago-taxi-trips


Kontekst:
Taksówki w Chicago, Illinois, są obsługiwane przez prywatne firmy i licencjonowane przez miasto. Istnieje około siedmiu tysięcy licencjonowanych taksówek 
działających w granicach miasta. Licencje uzyskuje się poprzez zakup lub dzierżawę "znaku" taksówkowego, który następnie umieszcza się na prawej górnej masce samochodu. 
Źródło: https://en.wikipedia.org/wiki/Taxicabs_of_the_United_States#Chicago

Treść:
Ten zbiór danych zawiera przejazdy taksówkowe od 2013 roku do chwili obecnej, zgłoszone do miasta Chicago.
Aby chronić prywatność, ale umożliwić analizy zbiorcze, Taxi ID jest spójne dla każdego numeru "znaku" taksówki, ale nie pokazuje numeru. 
Czasy są zaokrąglone do najbliższych 15 minut. Ze względu na proces raportowania danych, nie wszystkie podróże są raportowane.
Tutaj: http://digital.cityofchicago.org/index.php/chicago-taxi-data-released, można uzyskać więcej informacji o tym zbiorze danych i jak został on stworzony.
Zbiór danych nie obejmuje danych z firm TZW: ridesharingowych, takich jak Uber i Lyft. sĄ to tylko publiczne taksówki. Trzeba zaznaczyć, że jest to ogromny zbiór danych 
zawierający ponad 200 milionów przejazdów.


Tableau Dashboard https://public.tableau.com/views/Cropp/DashboardCROPPCasestudy_1?:language=en-US&:display_count=n&:origin=viz_share_link



**Pierwszy przykład to sprawdzenie ilości przejazdów każdego dnia od 2018-01-01 do 2020-01-01. Mozemy zauważyć, że największa liczba
przejazdów odbyła się w 1 czerwca 2018. Pomiedzy 2018-2020 najwiecej przejazdów odbyło się własnie w roky 2018. Cały trend spatkowy pokazany 
w nastepnych przykładach pokazuje duże spadek zainteresowania przejazdami taksówkami w Chicago. Poniższy wyniki zapytania można oczywiście
wyeksportować do pliku .csv a nastepnie otworzyć w MS Excel. Utworzyć tabelę oraz tabele przestawną i stworzyć wykreś na którym 
dokładnie można zaobserwować problem podzielony na lata czy kwartały.**  

```
SELECT
CAST(trip_start_timestamp as date) as trip_date,
count(*) as trip_count
FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips`
WHERE trip_start_timestamp between '2018-01-01' and '2020-01-01'
GROUP BY trip_date
ORDER BY trip_count DESC

```



## WYKRES 1 

![image](https://user-images.githubusercontent.com/110094376/196025173-bf9ce1e8-28de-4e1a-82ab-2e0f008d523d.png)

**Na podstawie tej funkcji możemy sprawdzić, który w którym miesiącu od 2014 do 2022 odbyło się najwiecej przejazdów. 
Okazuje się, ze miesiąc maj oraz marzec są najbardziej obleganymi jesl chodzi o przejazdy taksówkami.**

```
SELECT
EXTRACT(month from trip_start_timestamp) as trip_month,
count(*) as trip_count
FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips`
GROUP BY trip_month
ORDER BY trip_count DESC;

```


## WYKRES 2 

![image](https://user-images.githubusercontent.com/110094376/196025263-b624aa79-7146-49eb-9ab0-7003f13af0ea.png)


**Zapytanie pokazuje duży trend spadkowy rok do roku (2014-2022) jesli chodzi o przejazdy taksówkami w Chicago. Wpływ na to najprawdopdobniej
miało prowadzenie UBERA oraz innych prywatnych przejazdów.**

```
SELECT
EXTRACT(year from trip_start_timestamp) as trip_year,
count(*) as trip_count
FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips`
GROUP BY trip_year
ORDER BY trip_count DESC;
```



## WYKRES 3 

![image](https://user-images.githubusercontent.com/110094376/196025437-fb48836a-7177-43c8-94f0-a9ba6f727e8f.png)


**Przy dużej liczbie firm taxi w Chicago tutaj możemy zobaczyć 50 najpopularniejszych. Taxi Affiliation Services ma bardzo dużo przewagę 
nad kolejną.** 

```
SELECT company, count (*) as rides_total
FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips`
WHERE company is NOT NULL
GROUP BY company 
ORDER BY count(*) DESC
LIMIT 50
```


## WYKRES 4 

![image](https://user-images.githubusercontent.com/110094376/196025488-a29c47f3-451b-4028-95b4-ce2a3d1b3603.png)



**Najabrdziej popularną metodą płatności jest cały czas gotówka** -

```
SELECT payment_type, count(*) as payment
FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips`
GROUP BY payment_type
ORDER BY count(*) DESC
```


## WYKRES 5 

![image](https://user-images.githubusercontent.com/110094376/196025637-0015c822-e20f-4dd2-a7dd-5e165b4155b0.png)

**Tutaj możemy zaobserwować ile średnio taksówka przebywa mil podczas kursu. Ilość danych zostałą przefiltrowana w Tableau. 
Co ciekawe w tym zestawieniu nie ma zadnej z firm z pierwszej 5 najpopularniejszych firm.**

```
SELECT company, ROUND(AVG(trip_miles),2) AS Avg_ride_miles
FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips`
WHERE trip_miles >=1
GROUP BY company 
ORDER BY Avg_ride_miles DESC;

```


## WYKRES 6 

![image](https://user-images.githubusercontent.com/110094376/196025718-f5554149-e00a-4ee1-b16a-e9d8689cbceb.png)

**Najchętniej wybierany godziny na przejazd taksówką w Chicago to godziy popołudniowe 17-18.**

```
WITH subquery AS (
SELECT
EXTRACT(HOUR FROM trip_start_timestamp) AS hour
FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips`)
SELECT
Hour,
COUNT(Hour) AS count
FROM
subquery
GROUP BY
Hour
ORDER BY
count DESC
```

## WYKRES 7 

![image](https://user-images.githubusercontent.com/110094376/196025795-d3ace3dd-af72-4c32-b3d8-ed87f074d60a.png)

**Średnia mil przejechana przez taksówki w konkretnej godzinie w ciagu dnia. Tutaj widać, że najdłuższe kursu odbywają się nad ranem.**

```
SELECT
  EXTRACT(HOUR FROM trip_start_timestamp) AS Hour,
  AVG(trip_miles) AS miles
FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips`
GROUP BY
  Hour
ORDER BY
  miles DESC;
```

## WYKRES 8 

![image](https://user-images.githubusercontent.com/110094376/196025839-98c835f2-eafc-4f04-b139-17b4eb50c557.png)

**Dzięki tej funkcji chciałem pokazać srednią mil/h podczas jedno kursu w ciągu konkretnej godziny w ciągu dnia. Przez dane z tabeli oraz ogranizcania związane z 
darmową wersją BQ udało się policzyć mil/minute. Ale dalej możemy zauważyć, że w godzinach porannych zarówną odbywają się najdłuższe kursu jak i samochody jadą z większą prędkością (mniejsze korki).**

```
SELECT
EXTRACT(HOUR FROM trip_start_timestamp) AS hour,
AVG((trip_miles/TIMESTAMP_DIFF(trip_end_timestamp,trip_start_timestamp, MINUTE))) AS miles_per_minute
FROM
  `bigquery-public-data.chicago_taxi_trips.taxi_trips`
WHERE
  trip_miles > 0
  AND fare/trip_miles BETWEEN 2
  AND 10
  AND trip_end_timestamp > trip_start_timestamp
GROUP BY hour
ORDER BY miles_per_minute DESC
```

