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

Устанавливаем пакеты *dplyr*, *stringr*, *lubridate*, *tidyr* для
последующей работы

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
library(lubridate)
```

    Warning: пакет 'lubridate' был собран под R версии 4.3.2


    Присоединяю пакет: 'lubridate'

    Следующие объекты скрыты от 'package:base':

        date, intersect, setdiff, union

``` r
library(tidyr)
```

    Warning: пакет 'tidyr' был собран под R версии 4.3.2

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
  mutate_at(to_convertDate, list(~as.POSIXct(., format = "%Y-%m-%d %H:%M:%S")))

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
  mutate_at(to_convertDate1, list(~as.POSIXct(., format = "%Y-%m-%d %H:%M:%S")))
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

-   Точки доступа

Небезопасные устройства и точки доступа

``` r
vulnerateP <- wireP %>%
  filter(Privacy == "OPN") %>%
  distinct(BSSID, ESSID) %>%
  head(10)
vulnerateP
```

                   BSSID         ESSID
    1  E8:28:C1:DC:B2:52 MIREA_HOTSPOT
    2  E8:28:C1:DC:B2:50  MIREA_GUESTS
    3  E8:28:C1:DC:B2:51              
    4  E8:28:C1:DC:FF:F2              
    5  00:25:00:FF:94:73              
    6  E8:28:C1:DD:04:52 MIREA_HOTSPOT
    7  E8:28:C1:DE:74:31              
    8  E8:28:C1:DE:74:32 MIREA_HOTSPOT
    9  E8:28:C1:DC:C8:32 MIREA_HOTSPOT
    10 E8:28:C1:DD:04:50  MIREA_GUESTS

Вывод всех идентификаторов сети в отдельный файл

``` r
write.table(vulnerateP[1], file = "vulnery.txt", sep = "\t", quote = FALSE, row.names = FALSE)
```

Проверка BSSID через https://www.wireshark.org/tools/oui-lookup.html:

1.  00:03:7A Taiyo Yuden Co., Ltd.
2.  00:03:7F Atheros Communications, Inc.
3.  00:25:00 Apple, Inc.
4.  00:26:99 Cisco Systems, Inc
5.  E0:D9:E3 Eltex Enterprise Ltd.
6.  E8:28:C1 Eltex Enterprise Ltd.

Сети и устройства, использующие протокол шифрования WPA3

``` r
WPA3 <- wireP %>%
  filter(str_detect(Privacy, "WPA3")) %>%
  select(BSSID, ESSID) %>%
  distinct()
WPA3
```

                  BSSID              ESSID
    1 26:20:53:0C:98:E8                   
    2 A2:FE:FF:B8:9B:C9         Christie’s
    3 96:FF:FC:91:EF:64                   
    4 CE:48:E7:86:4E:33 iPhone (Анастасия)
    5 8E:1F:94:96:DA:FD iPhone (Анастасия)
    6 BE:FD:EF:18:92:44            Димасик
    7 3A:DA:00:F9:0C:02  iPhone XS Max 🦊🐱🦊
    8 76:C5:A0:70:08:96                   

Вычисление длительности сессий каждого пользователя

``` r
session <- wireP %>%
  mutate(session = Last.time.seen - First.time.seen) %>%
  mutate(session = seconds_to_period(session)) %>%
  select(BSSID, ESSID, session) %>%
  arrange(desc(session)) %>%
  head(10)

session
```

                   BSSID         ESSID    session
    1  00:25:00:FF:94:73               2H 43M 15S
    2  E8:28:C1:DD:04:52 MIREA_HOTSPOT 2H 42M 56S
    3  E8:28:C1:DC:B2:52 MIREA_HOTSPOT 2H 42M 35S
    4  08:3A:2F:56:35:FE               2H 42M 26S
    5  6E:C7:EC:16:DA:1A          Cnet  2H 42M 9S
    6  E8:28:C1:DC:B2:50  MIREA_GUESTS  2H 42M 6S
    7  E8:28:C1:DC:B2:51                2H 42M 5S
    8  48:5B:39:F9:7A:48                2H 42M 5S
    9  E8:28:C1:DC:FF:F2                2H 42M 4S
    10 8E:55:4A:85:5B:01      Vladimir  2H 42M 3S

10 быстрых точек доступа

``` r
theFastest <- arrange(wireP, desc(Speed)) %>%
  select(BSSID, ESSID, Speed) %>%
  head(10)
theFastest
```

                   BSSID              ESSID Speed
    1  26:20:53:0C:98:E8                      866
    2  96:FF:FC:91:EF:64                      866
    3  CE:48:E7:86:4E:33 iPhone (Анастасия)   866
    4  8E:1F:94:96:DA:FD iPhone (Анастасия)   866
    5  CA:66:3B:8F:56:DD                      858
    6  9A:75:A8:B9:04:1E                 KC   360
    7  4A:EC:1E:DB:BF:95     POCO X5 Pro 5G   360
    8  56:C5:2B:9F:84:90         OnePlus 6T   360
    9  E8:28:C1:DC:B2:41       MIREA_GUESTS   360
    10 E8:28:C1:DC:B2:40      MIREA_HOTSPOT   360

Запросы, отфильтрованные по частоте отправки Beacons в секунду

``` r
BPM <- wireP %>%
  mutate(session = difftime(Last.time.seen, First.time.seen, units = "mins")) %>%
  select(BSSID, ESSID, session, X..beacons) %>%
  filter(session != 0, X..beacons != 0) %>%
  mutate(BPM = round(session / X..beacons, 3)) %>%
  mutate(session = round(session, 3)) %>%
  arrange(BPM) %>%
  head(10)

BPM
```

                   BSSID                    ESSID      session X..beacons
    1  F2:30:AB:E9:03:ED          iPhone (Uliana)   0.117 mins          6
    2  B2:CF:C0:00:4A:60      Михаил's Galaxy M32   0.083 mins          4
    3  3A:DA:00:F9:0C:02        iPhone XS Max 🦊🐱🦊   0.150 mins          5
    4  02:BC:15:7E:D5:DC                  MT_FREE   0.033 mins          1
    5  00:3E:1A:5D:14:45                  MT_FREE   0.033 mins          1
    6  76:C5:A0:70:08:96                            0.033 mins          1
    7  D2:25:91:F6:6C:D8                     Саня   0.217 mins          5
    8  BE:F1:71:D6:10:D7             C322U21 0566 157.683 mins       1647
    9  00:03:7A:1A:03:56                  MT_FREE   0.100 mins          1
    10 38:1A:52:0D:84:D7 EBFCD57F-EE81fI_DL_1AO2T  71.983 mins        704
              BPM
    1  0.019 mins
    2  0.021 mins
    3  0.030 mins
    4  0.033 mins
    5  0.033 mins
    6  0.033 mins
    7  0.043 mins
    8  0.096 mins
    9  0.100 mins
    10 0.102 mins

-   Данные клиентов

Производители устройств

``` r
unique_rows <- unique(queries[6][queries[6] != "(not associated)" & queries[6] != "", ])
write.table(unique_rows, file = "vendors.txt", sep = "\t", quote = FALSE, row.names = FALSE)

# поиск производителей через https://www.wireshark.org/tools/oui-lookup.html
# записываем получившийся список в файл input.txt
# Считывание строк из файла

lines <- readLines("input.txt")
data <- strsplit(lines, " ", fixed = TRUE)

result <- data.frame(
  BSSID = sapply(data, function(x) x[1]),
  vendor = sapply(data, function(x) paste(x[-1], collapse = " "))
)

result
```

          BSSID                                              vendor
    1  00:03:7F                        Atheros Communications, Inc.
    2  00:0D:97                             Hitachi Energy USA Inc.
    3  00:23:EB                                  Cisco Systems, Inc
    4  00:25:00                                         Apple, Inc.
    5  00:26:99                                  Cisco Systems, Inc
    6  08:3A:2F Guangzhou Juan Intelligent Tech Joint Stock Co.,Ltd
    7  0C:80:63                       Tp-Link Technologies Co.,Ltd.
    8  DC:09:4C                         Huawei Technologies Co.,Ltd
    9  E0:D9:E3                               Eltex Enterprise Ltd.
    10 E8:28:C1                               Eltex Enterprise Ltd.

Устройства, не рандомизирующие свой MAC-адрес

``` r
notmac <- queries %>%
  filter(BSSID == "(not associated)") %>%
  select(Station.MAC, BSSID, Probed.ESSIDs) %>%
  head(10)

notmac
```

             Station.MAC            BSSID Probed.ESSIDs
    1  96:35:2D:3D:85:E6 (not associated)  IT2 Wireless
    2  5E:8E:A6:5E:34:81 (not associated)              
    3  10:51:07:CB:33:E7 (not associated)              
    4  BC:F1:71:D4:DB:04 (not associated)              
    5  A6:EC:3C:AB:BA:10 (not associated)              
    6  9E:01:46:3E:EF:4E (not associated)              
    7  00:95:69:E7:7F:35 (not associated)   nvripcsuite
    8  00:95:69:E7:7C:ED (not associated)   nvripcsuite
    9  14:13:33:59:9F:AB (not associated)              
    10 10:51:07:FE:77:BB (not associated)              

Кластеризовать запросы от устройств к точкам доступа по их именам.
Определить время появления устройства в зоне радиовидимости и время
выхода его из нее

``` r
cluster <- queries %>%
  filter(!is.na(Probed.ESSIDs) & Probed.ESSIDs != "") %>%
  group_by(Station.MAC, Probed.ESSIDs, .add = TRUE) %>%
  summarize(First_time_seen = min(First.time.seen),
            Last_time_seen = max(Last.time.seen),
            .groups = 'drop') %>%
  head(10)

cluster
```

    # A tibble: 10 × 4
       Station.MAC       Probed.ESSIDs       First_time_seen     Last_time_seen     
       <chr>             <chr>               <dttm>              <dttm>             
     1 00:90:4C:E6:54:54 Redmi               2023-07-28 09:16:59 2023-07-28 10:21:15
     2 00:95:69:E7:7C:ED nvripcsuite         2023-07-28 09:13:11 2023-07-28 11:56:13
     3 00:95:69:E7:7D:21 nvripcsuite         2023-07-28 09:13:15 2023-07-28 11:56:17
     4 00:95:69:E7:7F:35 nvripcsuite         2023-07-28 09:13:11 2023-07-28 11:56:07
     5 00:F4:8D:F7:C5:19 Redmi 12            2023-07-28 10:45:04 2023-07-28 11:43:26
     6 02:00:00:00:00:00 xt3 w64dtgv5cfrxht… 2023-07-28 09:54:40 2023-07-28 11:55:36
     7 02:06:2B:A5:0C:31 Avenue611           2023-07-28 09:55:12 2023-07-28 09:55:12
     8 02:1D:0F:A4:94:74 iPhone (Дима )      2023-07-28 09:57:08 2023-07-28 09:57:08
     9 02:32:DC:56:5C:82 Timo Resort         2023-07-28 10:58:23 2023-07-28 10:58:24
    10 02:35:E9:C2:44:5F iPhone (Максим)     2023-07-28 10:00:55 2023-07-28 10:00:55

Оценить стабильность уровня сигнала внури кластера во времени. Выявить
наиболее стабильный кластер

``` r
stability_summary <- queries %>%
  group_by(Station.MAC, Probed.ESSIDs) %>%
  summarize(
    Mean_Power = mean(Power, na.rm = TRUE),
    .groups = 'drop'
  ) %>%
  filter(!is.na(Mean_Power)) %>%
  arrange(Mean_Power) %>%
  head(10)

stability_summary
```

    # A tibble: 10 × 3
       Station.MAC       Probed.ESSIDs     Mean_Power
       <chr>             <chr>                  <dbl>
     1 8A:45:77:F9:7F:F4 "iPhone (Дима )"         -89
     2 02:35:E9:C2:44:5F "iPhone (Максим)"        -88
     3 46:EA:D3:C0:50:1E ""                       -88
     4 C2:5B:0B:02:39:B1 "Galaxy A31CAED"         -88
     5 C4:D0:E3:5B:B4:6D ""                       -88
     6 0A:3B:54:B4:EF:1F ""                       -87
     7 4A:40:CA:E8:1A:08 ""                       -87
     8 50:8F:4C:71:25:96 ""                       -87
     9 92:5B:0A:A2:D6:1B ""                       -87
    10 AA:59:C8:6F:F3:2C ""                       -87

``` r
indexMin <- which.min(queries$Power)
theStablest <- queries[indexMin, ] %>%
  select(Station.MAC, First.time.seen, Last.time.seen, Power, BSSID, Probed.ESSIDs)
theStablest
```

               Station.MAC     First.time.seen      Last.time.seen Power
    3585 8A:45:77:F9:7F:F4 2023-07-28 10:00:55 2023-07-28 10:00:55   -89
                    BSSID  Probed.ESSIDs
    3585 (not associated) iPhone (Дима )

## Оценка результата

Задание было выполнено

## Вывод

Были развиты практические навыки использования языка программирования R
для обработки данных
