Лабораторная работа №3. Основы обработки данных с помощью R
================

## Цель работы

1.  Закрепить практические навыки использования языка программирования R
    для обработки данных
2.  Закрепить знания основных функций обработки данных экосистемы
    tidyverse языка R
3.  Развить пркатические навыки использования функций обработки данных
    пакета dplyr – функции select(), filter(), mutate(), arrange(),
    group_by()

## Исходные данные

-   Windows 10
-   RStudio

## Ход работы

Устанавливаем пакеты *dplyr* и *nycflights13*

``` r
library(dplyr)
```

    Warning: пакет 'dplyr' был собран под R версии 4.3.2


    Присоединяю пакет: 'dplyr'

    Следующие объекты скрыты от 'package:stats':

        filter, lag

    Следующие объекты скрыты от 'package:base':

        intersect, setdiff, setequal, union

``` r
library(nycflights13)
```

    Warning: пакет 'nycflights13' был собран под R версии 4.3.2

Сколько встроенных в пакет nycflights13 датафреймов?

``` r
frame <- data(package = "nycflights13")
nrow(frame$results)
```

    [1] 5

Сколько строк в каждом датафрейме?

``` r
dataframes <- list(
  flights = flights,
  weather = weather,
  planes = planes,
  airports = airports,
  airlines = airlines
                  )

for (frame in names(dataframes)) {
  cat(paste("Количество строк в", frame, ":", nrow(dataframes[[frame]])), "\n")}
```

    Количество строк в flights : 336776 
    Количество строк в weather : 26115 
    Количество строк в planes : 3322 
    Количество строк в airports : 1458 
    Количество строк в airlines : 16 

Сколько столбцов в каждом датафрейме?

``` r
for (frame in names(dataframes)) {
  cat(paste("Количество столбцов в", frame, ":", ncol(dataframes[[frame]])), "\n")
}
```

    Количество столбцов в flights : 19 
    Количество столбцов в weather : 15 
    Количество столбцов в planes : 9 
    Количество столбцов в airports : 8 
    Количество столбцов в airlines : 2 

Как просмотреть примерный вид датафрейма?

``` r
glimpse(flights)
```

    Rows: 336,776
    Columns: 19
    $ year           <int> 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2…
    $ month          <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1…
    $ day            <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1…
    $ dep_time       <int> 517, 533, 542, 544, 554, 554, 555, 557, 557, 558, 558, …
    $ sched_dep_time <int> 515, 529, 540, 545, 600, 558, 600, 600, 600, 600, 600, …
    $ dep_delay      <dbl> 2, 4, 2, -1, -6, -4, -5, -3, -3, -2, -2, -2, -2, -2, -1…
    $ arr_time       <int> 830, 850, 923, 1004, 812, 740, 913, 709, 838, 753, 849,…
    $ sched_arr_time <int> 819, 830, 850, 1022, 837, 728, 854, 723, 846, 745, 851,…
    $ arr_delay      <dbl> 11, 20, 33, -18, -25, 12, 19, -14, -8, 8, -2, -3, 7, -1…
    $ carrier        <chr> "UA", "UA", "AA", "B6", "DL", "UA", "B6", "EV", "B6", "…
    $ flight         <int> 1545, 1714, 1141, 725, 461, 1696, 507, 5708, 79, 301, 4…
    $ tailnum        <chr> "N14228", "N24211", "N619AA", "N804JB", "N668DN", "N394…
    $ origin         <chr> "EWR", "LGA", "JFK", "JFK", "LGA", "EWR", "EWR", "LGA",…
    $ dest           <chr> "IAH", "IAH", "MIA", "BQN", "ATL", "ORD", "FLL", "IAD",…
    $ air_time       <dbl> 227, 227, 160, 183, 116, 150, 158, 53, 140, 138, 149, 1…
    $ distance       <dbl> 1400, 1416, 1089, 1576, 762, 719, 1065, 229, 944, 733, …
    $ hour           <dbl> 5, 5, 5, 5, 6, 5, 6, 6, 6, 6, 6, 6, 6, 6, 6, 5, 6, 6, 6…
    $ minute         <dbl> 15, 29, 40, 45, 0, 58, 0, 0, 0, 0, 0, 0, 0, 0, 0, 59, 0…
    $ time_hour      <dttm> 2013-01-01 05:00:00, 2013-01-01 05:00:00, 2013-01-01 0…

Сколько компаний-перевозчиков (carrier) учитывают эти наборы данных
(представлено в наборах данных)?

``` r
num_carriers <- n_distinct(flights$carrier)
num_carriers
```

    [1] 16

Сколько рейсов принял аэропорт John F Kennedy Intl в мае?

``` r
may <- flights %>%
  filter(month == 5, dest == "JFK")
num_may <- nrow(may)
num_may
```

    [1] 0

Какой самый северный аэропорт?

``` r
northernmost_airport <- airports %>%
  arrange(desc(lat)) %>% # Сортируем по убыванию широты
  head(1) # Выбираем самый северный аэропорт
northernmost_airport
```

    # A tibble: 1 × 8
      faa   name                      lat   lon   alt    tz dst   tzone
      <chr> <chr>                   <dbl> <dbl> <dbl> <dbl> <chr> <chr>
    1 EEN   Dillant Hopkins Airport  72.3  42.9   149    -5 A     <NA> 

Какой аэропорт самый высокогорный (находится выше всех над уровнем
моря)?

``` r
highest_airport <- airports %>%
  arrange(desc(alt)) %>%
  head(1)
highest_airport
```

    # A tibble: 1 × 8
      faa   name        lat   lon   alt    tz dst   tzone         
      <chr> <chr>     <dbl> <dbl> <dbl> <dbl> <chr> <chr>         
    1 TEX   Telluride  38.0 -108.  9078    -7 A     America/Denver

Какие бортовые номера у самых старых самолетов?

``` r
old <- planes %>%
  filter(year < 1970) %>%
  arrange(year) %>%
  distinct(tailnum)

old
```

    # A tibble: 8 × 1
      tailnum
      <chr>  
    1 N381AA 
    2 N201AA 
    3 N567AA 
    4 N378AA 
    5 N575AA 
    6 N14629 
    7 N615AA 
    8 N425AA 

Какая средняя температура воздуха была в сентябре в аэропорту John F
Kennedy Intl (в градусах Цельсия).

``` r
temp <- weather %>%
  filter(month == 9, origin == "JFK")
temp <- mean(temp$temp)

tempC = (5/9) * (temp - 32)
tempC
```

    [1] 19.38764

Самолеты какой авиакомпании совершили больше всего вылетов в июне?

``` r
mostFlights <- flights %>%
  filter(month == 6) %>%
  count(carrier) %>%
  arrange(desc(n)) %>%
  head(1)
mostFlights
```

    # A tibble: 1 × 2
      carrier     n
      <chr>   <int>
    1 UA       4975

Самолеты какой авиакомпании задерживались чаще других в 2013 году?

``` r
mostDelay <- flights %>%
  filter(dep_delay > 0, year == 2013) %>%
  count(carrier) %>%
  arrange(desc(n)) %>%
  head(1)

mostDelay
```

    # A tibble: 1 × 2
      carrier     n
      <chr>   <int>
    1 UA      27261

## Оценка результата

Задание было выполнено

## Вывод

Были развиты практические навыки использования языка программирования R
для обработки данных
