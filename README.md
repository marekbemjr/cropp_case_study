                                                              

# CHICAGO TAXI TRIPS 


Kontekst:

Taxicabs w Chicago, Illinois, są obsługiwane przez prywatne firmy i licencjonowane przez miasto. Istnieje około siedmiu tysięcy licencjonowanych taksówek 
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



- **Pierwszy przykład to sprawdzenie ilości przejazdów każdego dnia od 2018-01-01 do 2020-01-01. Mozemy zauważyć, że największa liczba
przejazdów odbyła się w 1 czerwca 2018. Pomiedzy 2018-2020 najwiecej przejazdów odbyło się własnie w roky 2018. Cały trend spatkowy pokazany 
w nastepnych przykładach pokazuje duże spadek zainteresowania przejazdami taksówkami w Chicago. Poniższy wyniki zapytania można oczywiście
wyeksportować do pliku .csv a nastepnie otworzyć w MS Excel. Utworzyć tabelę oraz tabele przestawną i stworzyć wykreś na którym 
dokładnie można zaobserwować problem podzielony na lata czy kwartały.  

```
SELECT
CAST(trip_start_timestamp as date) as trip_date,
count(*) as trip_count
FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips`
WHERE trip_start_timestamp between '2018-01-01' and '2020-01-01'
GROUP BY trip_date
ORDER BY trip_count DESC

```

- **Na podstawie tej funkcji możemy sprawdzić, który w którym miesiącu od 2014 do 2022 odbyło się najwiecej przejazdów. 
Okazuje się, ze miesiąc maj oraz marzec są najbardziej obleganymi jesl chodzi o przejazdy taksówkami.

```
SELECT
EXTRACT(month from trip_start_timestamp) as trip_month,
count(*) as trip_count
FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips`
GROUP BY trip_month
ORDER BY trip_count DESC;

```
## WYKRES 1 

![image](https://user-images.githubusercontent.com/110094376/196025173-bf9ce1e8-28de-4e1a-82ab-2e0f008d523d.png)


- **Zapytanie pokazuje duży trend spadkowy rok do roku (2014-2022) jesli chodzi o przejazdy taksówkami w Chicago. Wpływ na to najprawdopdobniej
miało prowadzenie UBERA oraz innych prywatnych przejazdów. 

```
SELECT
EXTRACT(year from trip_start_timestamp) as trip_year,
count(*) as trip_count
FROM `bigquery-public-data.chicago_taxi_trips.taxi_trips`
GROUP BY trip_year
ORDER BY trip_count DESC;
```

## WYKRES 2 

![image](https://user-images.githubusercontent.com/110094376/196025263-b624aa79-7146-49eb-9ab0-7003f13af0ea.png)
