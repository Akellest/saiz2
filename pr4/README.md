Лабораторная работа №4. Исследование метаданных DNS трафика
================

## Цель работы

1.  Закрепить практические навыки использования языка программирования R
    для обработки данных
2.  Закрепить знания основных функций обработки данных экосистемы
    tidyverse языка R
3.  Закрепить навыки исследования метаданных DNS трафика

## Исходные данные

-   Windows 10
-   RStudio

## Ход работы

Устанавливаем пакеты *dplyr*, *tidyr* и *tidyverse* для последующей
работы

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
library(tidyr)
```

    Warning: пакет 'tidyr' был собран под R версии 4.3.2

``` r
library(tidyverse)
```

    Warning: пакет 'tidyverse' был собран под R версии 4.3.2

    Warning: пакет 'ggplot2' был собран под R версии 4.3.2

    Warning: пакет 'tibble' был собран под R версии 4.3.2

    Warning: пакет 'readr' был собран под R версии 4.3.2

    Warning: пакет 'purrr' был собран под R версии 4.3.2

    Warning: пакет 'forcats' был собран под R версии 4.3.2

    Warning: пакет 'lubridate' был собран под R версии 4.3.2

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ forcats   1.0.0     ✔ readr     2.1.4
    ✔ ggplot2   3.4.4     ✔ stringr   1.5.0
    ✔ lubridate 1.9.3     ✔ tibble    3.2.1
    ✔ purrr     1.0.2     

    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

Встраиваем названия столбцов из header.csv в dng.log

``` r
data <- read.csv("dns.log", sep="\t", header=FALSE)
header <- read.csv("header.csv")
colnames(data) <- header[,1]
```

### Сколько участников информационного обмена в сети Доброй Организации?

``` r
result <- data %>%
  select(id1_orig, id3_host) %>%
  gather(key = "id", value = "IP") %>%
  count(IP, sort = TRUE) %>%
  head(10)
result
```

                    IP      n
    1    192.168.207.4 266627
    2    10.10.117.210  75943
    3  192.168.202.255  68720
    4   192.168.202.93  26522
    5     172.19.1.100  25481
    6  192.168.202.103  18121
    7   192.168.202.76  16978
    8   192.168.202.97  16176
    9  192.168.202.141  14976
    10 192.168.202.110  14493

### Какое соотношение участников обмена внутри сети и участников обращений к внешним ресурсам?

``` r
kResponse <- data %>%
  filter(data$QR == TRUE) %>%
  summarise(k = n())
kResponse/nrow(data)
```

                k
    1 0.002647598

### Найдите топ-10 участников сети, проявляющих наибольшую сетевую активность

``` r
result <- data %>%
  select(id1_orig, id3_host) %>%
  gather(key = "id", value = "IP") %>%
  count(IP, sort = TRUE) %>%
  head(10)
result
```

                    IP      n
    1    192.168.207.4 266627
    2    10.10.117.210  75943
    3  192.168.202.255  68720
    4   192.168.202.93  26522
    5     172.19.1.100  25481
    6  192.168.202.103  18121
    7   192.168.202.76  16978
    8   192.168.202.97  16176
    9  192.168.202.141  14976
    10 192.168.202.110  14493

### Найдите топ-10 доменов, к которым обращаются пользователи сети и соответственное количество обращений

``` r
domain_counts <- data %>%
  group_by(query) %>%
  summarise(count = n()) %>%
  arrange(desc(count)) %>%
  head(10)
domain_counts
```

    # A tibble: 10 × 2
       query                                                                   count
       <chr>                                                                   <int>
     1 "teredo.ipv6.microsoft.com"                                             39273
     2 "tools.google.com"                                                      14057
     3 "www.apple.com"                                                         13390
     4 "time.apple.com"                                                        13109
     5 "safebrowsing.clients.google.com"                                       11658
     6 "*\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x… 10401
     7 "WPAD"                                                                   9134
     8 "44.206.168.192.in-addr.arpa"                                            7248
     9 "HPE8AA67"                                                               6929
    10 "ISATAP"                                                                 6569

### Опеределите базовые статистические характеристики (функция summary()) интервала времени между последовательным обращениями к топ-10 доменам

## Оценка результата

Задание было выполнено

## Вывод

Были развиты практические навыки использования языка программирования R
для обработки данных
