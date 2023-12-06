Лабораторная работа №5. Исследование информации о состоянии беспроводных
сетей
================

## Цель работы

1.  Получить знания о методах исследования радиоэлектронной обстановки.
2.  Составить представление о механизмах работы Wi-Fi сетей на канальном
    и сетевом уровне модели OSI.
3.  Зекрепить практические навыки использования языка программирования R
    для обработки данных
4.  Закрепить знания основных функций обработки данных экосистемы
    tidyverse языка R

## Исходные данные

-   Windows 10
-   RStudio

## Ход работы

Устанавливаем пакеты *dplyr* для последующей работы

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
library(stringr)
```

Загрузка и прочтение файла

``` r
data1 <- read.csv("mir.csv-01.csv")
wireP <- data1[1:169, ]
data2 <- read.csv("mir.csv-01.csv", skip = 169)
queries <- data2[1:nrow(data1), ]
```

Удаление лишних пробелов из всех ячеек

``` r
wireP <- wireP %>%
  mutate_all(~str_squish(.))

queries <- queries %>%
  mutate_all(~str_squish(.))
```

Изменение формата столбцов

``` r
to_convertDate <- c('First.time.seen', 'Last.time.seen')
to_convertInt <- c('channel', 'Speed', 'Power', 'X..beacons', 'X..IV', 'ID.length')

wireP <- wireP %>%
  mutate_at(to_convertDate, funs(as.POSIXct(., format = "%Y-%m-%d %H:%M:%S")))
```

    Warning: `funs()` was deprecated in dplyr 0.8.0.
    ℹ Please use a list of either functions or lambdas:

    # Simple named list: list(mean = mean, median = median)

    # Auto named with `tibble::lst()`: tibble::lst(mean, median)

    # Using lambdas list(~ mean(., trim = .2), ~ median(., na.rm = TRUE))

``` r
wireP <- wireP %>%
  mutate_at(to_convertInt, as.integer)
```

    Warning: There were 2 warnings in `mutate()`.
    The first warning was:
    ℹ In argument: `channel = .Primitive("as.integer")(channel)`.
    Caused by warning:
    ! в результате преобразования созданы NA
    ℹ Run `dplyr::last_dplyr_warnings()` to see the 1 remaining warning.

``` r
to_convertDate1 <- c('First.time.seen', 'Last.time.seen')
to_convertInt1 <- c('Power', 'X..packets')

queries <- queries %>%
  mutate_at(to_convertDate1, funs(as.POSIXct(., format = "%Y-%m-%d %H:%M:%S")))
```

    Warning: `funs()` was deprecated in dplyr 0.8.0.
    ℹ Please use a list of either functions or lambdas:

    # Simple named list: list(mean = mean, median = median)

    # Auto named with `tibble::lst()`: tibble::lst(mean, median)

    # Using lambdas list(~ mean(., trim = .2), ~ median(., na.rm = TRUE))

``` r
queries <- queries %>%
  mutate_at(to_convertInt1, as.integer)
```

    Warning: There were 2 warnings in `mutate()`.
    The first warning was:
    ℹ In argument: `Power = .Primitive("as.integer")(Power)`.
    Caused by warning:
    ! в результате преобразования созданы NA
    ℹ Run `dplyr::last_dplyr_warnings()` to see the 1 remaining warning.

Просмотр получившихся таблиц

``` r
wireP %>% glimpse()
```

    Rows: 169
    Columns: 15
    $ BSSID           <chr> "BE:F1:71:D5:17:8B", "6E:C7:EC:16:DA:1A", "9A:75:A8:B9…
    $ First.time.seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13:03, 2023-07-28 …
    $ Last.time.seen  <dttm> 2023-07-28 11:50:50, 2023-07-28 11:55:12, 2023-07-28 …
    $ channel         <int> 1, 1, 1, 7, 6, 6, 11, 11, 11, 1, 6, 14, 11, 11, 6, 6, …
    $ Speed           <int> 195, 130, 360, 360, 130, 130, 195, 130, 130, 195, 180,…
    $ Privacy         <chr> "WPA2", "WPA2", "WPA2", "WPA2", "WPA2", "OPN", "WPA2",…
    $ Cipher          <chr> "CCMP", "CCMP", "CCMP", "CCMP", "CCMP", "", "CCMP", "C…
    $ Authentication  <chr> "PSK", "PSK", "PSK", "PSK", "PSK", "", "PSK", "PSK", "…
    $ Power           <int> -30, -30, -68, -37, -57, -63, -27, -38, -38, -66, -42,…
    $ X..beacons      <int> 846, 750, 694, 510, 647, 251, 1647, 1251, 704, 617, 13…
    $ X..IV           <int> 504, 116, 26, 21, 6, 3430, 80, 11, 0, 0, 86, 0, 0, 0, …
    $ LAN.IP          <chr> "0. 0. 0. 0", "0. 0. 0. 0", "0. 0. 0. 0", "0. 0. 0. 0"…
    $ ID.length       <int> 12, 4, 2, 14, 25, 13, 12, 13, 24, 12, 10, 0, 24, 24, 1…
    $ ESSID           <chr> "C322U13 3965", "Cnet", "KC", "POCO X5 Pro 5G", "", "M…
    $ Key             <chr> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA…

``` r
queries %>% glimpse()
```

    Rows: 12,249
    Columns: 7
    $ Station.MAC     <chr> "CA:66:3B:8F:56:DD", "96:35:2D:3D:85:E6", "5C:3A:45:9E…
    $ First.time.seen <dttm> 2023-07-28 09:13:03, 2023-07-28 09:13:03, 2023-07-28 …
    $ Last.time.seen  <dttm> 2023-07-28 10:59:44, 2023-07-28 09:13:03, 2023-07-28 …
    $ Power           <int> -33, -65, -39, -61, -53, -43, -31, NA, -71, -74, -65, …
    $ X..packets      <int> 858, 4, 432, 958, 1, 344, 163, NA, 3, 115, 437, 265, 7…
    $ BSSID           <chr> "BE:F1:71:D5:17:8B", "(not associated)", "BE:F1:71:D6:…
    $ Probed.ESSIDs   <chr> "C322U13 3965", "IT2 Wireless", "C322U21 0566", "C322U…

## Оценка результата

Задание было выполнено

## Вывод

Были развиты практические навыки использования языка программирования R
для обработки данных
