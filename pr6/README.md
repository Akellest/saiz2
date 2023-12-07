# Лабораторная работа №6. Исследование вредоносной активности в домене
Windows

## Цель работы

1.  Закрепить навыки исследования данных журнала Windows Active
    Directory
2.  Изучить структуру журнала системы Windows Active Directory
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


    Attaching package: 'dplyr'

    The following objects are masked from 'package:stats':

        filter, lag

    The following objects are masked from 'package:base':

        intersect, setdiff, setequal, union

``` r
library(stringr)
library(lubridate)
```


    Attaching package: 'lubridate'

    The following objects are masked from 'package:base':

        date, intersect, setdiff, union

``` r
library(tidyr)
library(jsonlite)
```

Загрузка и прочтение файла

``` r
url = "https://storage.yandexcloud.net/iamcth-data/dataset.tar.gz"
download.file(url, "dataset.tar.gz", mode="wb")
untar("dataset.tar.gz")
```

    Warning in untar("dataset.tar.gz"): '/usr/bin/tar -xf 'dataset.tar.gz''
    returned error code 2

``` r
#data <- jsonlite::stream_in(file("caldera_attack_evals_round1_day1_2019-10-20201108.json"))
```

## Оценка результата

Задание было выполнено

## Вывод

Были развиты практические навыки использования языка программирования R
для обработки данных
