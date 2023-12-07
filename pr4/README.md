# Лабораторная работа №4. Исследование метаданных DNS трафика

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

Устанавливаем пакеты *dplyr*, *tidyr*, *httr* и *tidyverse* для
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
library(tidyr)
library(tidyverse)
```

    ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ✔ forcats   1.0.0     ✔ readr     2.1.4
    ✔ ggplot2   3.4.4     ✔ stringr   1.5.1
    ✔ lubridate 1.9.3     ✔ tibble    3.2.1
    ✔ purrr     1.0.2     

    ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ✖ dplyr::filter() masks stats::filter()
    ✖ dplyr::lag()    masks stats::lag()
    ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(httr)
```

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

``` r
# данные только для 10 популярных доменов
topQuery <- data %>%
  filter(query %in% domain_counts$query)
# интервалы между последовательными обращениями
intervals <- topQuery %>%
  arrange(query, ts) %>%
  group_by(query) %>%
  mutate(time_interval = ts - lag(ts))

summary(intervals$time_interval)
```

        Min.  1st Qu.   Median     Mean  3rd Qu.     Max.     NA's 
        0.00     0.00     0.75     8.76     1.74 52723.50       10 

### Часто вредоносное программное обеспечение использует DNS канал в качестве канала управления, периодически отправляя запросы на подконтрольный злоумышленникам DNS сервер. По периодическим запросам на один и тот же домен можно выявить скрытый DNS канал. Есть ли такие IP адреса в исследуемом датасете?

``` r
dns_queries <- data %>%
  filter(!data$QR)

repeated_ips <- dns_queries %>%
  group_by(id3_host) %>%
  summarise(times = n()) %>%
  filter(times > 1) %>%
  arrange(desc(times)) %>%
  distinct() %>%
  filter(times > 1000)
  
repeated_ips
```

    # A tibble: 13 × 2
       id3_host         times
       <chr>            <int>
     1 192.168.207.4   266542
     2 192.168.202.255  68720
     3 172.19.1.100     25481
     4 ff02::1:3        14411
     5 8.26.56.26        5974
     6 156.154.70.22     5821
     7 172.16.42.255     4962
     8 68.87.64.150      4838
     9 68.87.75.198      4171
    10 ff02::fb          2894
    11 192.168.206.44    1841
    12 192.168.204.255   1434
    13 192.168.202.110   1121

### Определите местоположение (страну, город) и организацию-провайдера для топ-10 доменов. Для этого можно использовать сторонние сервисы, например https://v4.ifconfig.co/

``` r
domainV <- c(domain_counts$query)
top_domains <- domainV

# ищем через nslookup доменные записи из нашего вектора
top_dns <- c("teredo.ipv6.microsoft.com",
             "64.233.165.138",
             "95.101.144.198",
             "17.253.38.125",
             "64.233.162.101",
             "*\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00",
             "WPAD",
             "44.206.168.192.in-addr.arpa",
             "HPE8AA67",
             "ISATAP")
```

``` r
get_location_info <- function(domain) {
  url <- paste0("https://ipinfo.io/", domain, "/json")
  response <- tryCatch(GET(url), error = function(e) e)
  
  if (!inherits(response, "error") && http_type(response) == "application/json") {
    content <- content(response, as = "parsed")
    if (!is.null(content$country) && !is.null(content$city) && !is.null(content$org)) {
      return(data.frame(domain = domain, country = content$country, city = content$city, org = content$org))
    } else {
      return(data.frame(domain = domain, country = NA, city = NA, org = NA))
    }
  } else {
    return(data.frame(domain = domain, country = NA, city = NA, org = NA))
  }
}

location_info <- lapply(top_dns, get_location_info)
location_df <- bind_rows(location_info)
location_df
```

                                                                        domain
    1                                                teredo.ipv6.microsoft.com
    2                                                           64.233.165.138
    3                                                           95.101.144.198
    4                                                            17.253.38.125
    5                                                           64.233.162.101
    6  *\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00
    7                                                                     WPAD
    8                                              44.206.168.192.in-addr.arpa
    9                                                                 HPE8AA67
    10                                                                  ISATAP
       country      city                               org
    1     <NA>      <NA>                              <NA>
    2       FI    Hamina                AS15169 Google LLC
    3       SE Stockholm AS16625 Akamai Technologies, Inc.
    4       SE     Kista                 AS6185 Apple Inc.
    5       FI    Hamina                AS15169 Google LLC
    6       US   Ashburn          AS14618 Amazon.com, Inc.
    7     <NA>      <NA>                              <NA>
    8     <NA>      <NA>                              <NA>
    9     <NA>      <NA>                              <NA>
    10    <NA>      <NA>                              <NA>

## Оценка результата

Задание было выполнено

## Вывод

Были развиты практические навыки использования языка программирования R
для обработки данных
