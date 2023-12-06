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

Устанавливаем пакеты *dplyr*, *stringr* для последующей работы

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

Небезопасные устройства и точки доступа

``` r
vulnerateP <- wireP %>%
  filter(Privacy == "OPN") %>%
  distinct(BSSID, ESSID)
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
    11 E8:28:C1:DD:04:51              
    12 E8:28:C1:DC:C8:30  MIREA_GUESTS
    13 E8:28:C1:DE:74:30              
    14 E0:D9:E3:48:FF:D2              
    15 E8:28:C1:DC:B2:41  MIREA_GUESTS
    16 E8:28:C1:DC:B2:40 MIREA_HOTSPOT
    17 00:26:99:F2:7A:E0              
    18 E8:28:C1:DC:B2:42              
    19 E8:28:C1:DD:04:40 MIREA_HOTSPOT
    20 E8:28:C1:DD:04:41  MIREA_GUESTS
    21 E8:28:C1:DE:47:D2 MIREA_HOTSPOT
    22 02:BC:15:7E:D5:DC       MT_FREE
    23 E8:28:C1:DC:C6:B1              
    24 E8:28:C1:DD:04:42              
    25 E8:28:C1:DC:C8:31              
    26 E8:28:C1:DE:47:D1              
    27 00:AB:0A:00:10:10              
    28 E8:28:C1:DC:C6:B0  MIREA_GUESTS
    29 E8:28:C1:DC:C6:B2              
    30 E8:28:C1:DC:BD:50  MIREA_GUESTS
    31 E8:28:C1:DC:0B:B2              
    32 E8:28:C1:DC:33:12              
    33 00:03:7A:1A:03:56       MT_FREE
    34 00:03:7F:12:34:56       MT_FREE
    35 00:3E:1A:5D:14:45       MT_FREE
    36 E0:D9:E3:49:00:B1              
    37 E8:28:C1:DC:BD:52 MIREA_HOTSPOT
    38 00:26:99:F2:7A:EF              
    39 02:67:F1:B0:6C:98       MT_FREE
    40 02:CF:8B:87:B4:F9       MT_FREE
    41 00:53:7A:99:98:56       MT_FREE
    42 E8:28:C1:DE:47:D0  MIREA_GUESTS

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
  mutate(duration = difftime(Last.time.seen, First.time.seen, units = "mins")) %>%
  select(BSSID, ESSID, duration) %>%
  arrange(desc(duration))

session <- mutate(session, duration = round(as.numeric(session$duration), 2))

session
```

                    BSSID                       ESSID duration
    1   00:25:00:FF:94:73                               163.25
    2   E8:28:C1:DD:04:52               MIREA_HOTSPOT   162.93
    3   E8:28:C1:DC:B2:52               MIREA_HOTSPOT   162.58
    4   08:3A:2F:56:35:FE                               162.43
    5   6E:C7:EC:16:DA:1A                        Cnet   162.15
    6   E8:28:C1:DC:B2:50                MIREA_GUESTS   162.10
    7   E8:28:C1:DC:B2:51                               162.08
    8   48:5B:39:F9:7A:48                               162.08
    9   E8:28:C1:DC:FF:F2                               162.07
    10  8E:55:4A:85:5B:01                    Vladimir   162.05
    11  00:26:99:BA:75:80                        GIVC   161.83
    12  00:26:99:F2:7A:E2                        GIVC   161.78
    13  1E:93:E3:1B:3C:F4                  Galaxy A71   160.55
    14  9A:75:A8:B9:04:1E                          KC   160.47
    15  0C:80:63:A9:6E:EE                               160.47
    16  00:23:EB:E3:81:F2                        GIVC   159.92
    17  9E:A3:A9:DB:7E:01                               159.25
    18  E8:28:C1:DC:C8:32               MIREA_HOTSPOT   159.25
    19  1C:7E:E5:8E:B7:DE                               158.73
    20  00:26:99:F2:7A:E1                         IKB   158.20
    21  BE:F1:71:D5:17:8B                C322U13 3965   157.78
    22  BE:F1:71:D6:10:D7                C322U21 0566   157.68
    23  9E:A3:A9:D6:28:3C                               157.52
    24  E8:28:C1:DD:04:40               MIREA_HOTSPOT   156.67
    25  E8:28:C1:DD:04:41                MIREA_GUESTS   156.67
    26  00:23:EB:E3:81:F1                         IKB   155.80
    27  00:23:EB:E3:81:FE                         IKB   155.08
    28  00:23:EB:E3:81:FD                        GIVC   155.08
    29  9E:A3:A9:BF:12:C0                               154.50
    30  E8:28:C1:DC:B2:40               MIREA_HOTSPOT   153.53
    31  AA:F4:3F:EE:49:0B            Redmi Note 8 Pro   150.75
    32  E8:28:C1:DE:47:D2               MIREA_HOTSPOT   150.68
    33  E8:28:C1:DD:04:50                MIREA_GUESTS   149.82
    34  14:EB:B6:6A:76:37             Gnezdo_lounge 2   148.58
    35  56:99:98:EE:5A:4E               tementy-phone   146.85
    36  E8:28:C1:DC:B2:42                               144.88
    37  38:1A:52:0D:90:A1    EBFCD597-EE81fI_DMN1AOe1   144.35
    38  0A:C5:E1:DB:17:7B               AndroidAP177B   143.47
    39  E8:28:C1:DC:C8:30                MIREA_GUESTS   140.75
    40  E8:28:C1:DC:C6:B1                               139.83
    41  E8:28:C1:DD:04:42                               138.63
    42  E8:28:C1:DC:B2:41                MIREA_GUESTS   138.45
    43  12:51:07:FF:29:D6        DESKTOP-KITJO8R 5262   124.72
    44  CE:B3:FF:84:45:FC                               121.18
    45  E8:28:C1:DC:C8:31                               119.98
    46  E8:28:C1:DC:C6:B2                               113.65
    47  4A:EC:1E:DB:BF:95              POCO X5 Pro 5G   110.97
    48  CA:66:3B:8F:56:DD                               106.68
    49  00:26:99:F2:7A:E0                               103.63
    50  E8:28:C1:DD:04:51                                94.05
    51  E0:D9:E3:48:FF:D2                                93.73
    52  00:AB:0A:00:10:10                                89.27
    53  E8:28:C1:DE:74:32               MIREA_HOTSPOT    86.50
    54  10:50:72:00:11:08              MGTS_GPON_B563    83.28
    55  EA:D8:D1:77:C8:08     DIRECT-08-HP M428fdw LJ    83.25
    56  D2:6D:52:61:51:5D                                77.27
    57  E0:D9:E3:49:04:52                                76.90
    58  7E:3A:10:A7:59:4E                    vivo Y21    76.85
    59  BE:F1:71:D5:0E:53                C322U06 9080    76.30
    60  A6:02:B9:73:83:18                C239U52 6706    76.28
    61  9A:9F:06:44:24:5B       Long Huong Galaxy M12    76.20
    62  E8:28:C1:DE:74:31                                73.88
    63  92:F5:7B:43:0B:69                    Redmi 12    73.20
    64  E8:28:C1:DC:3C:92                                72.18
    65  38:1A:52:0D:84:D7    EBFCD57F-EE81fI_DL_1AO2T    71.98
    66  38:1A:52:0D:90:5D    EBFCD615-EE81fI_DOL1AO8w    70.92
    67  A2:64:E8:97:58:EE                    Mi Phone    70.87
    68  A6:02:B9:73:81:47                C239U53 6056    70.40
    69  56:C5:2B:9F:84:90                  OnePlus 6T    69.55
    70  A6:02:B9:73:2F:76                C239U51 0701    69.07
    71  38:1A:52:0D:97:60    EBFCD593-EE81fI_DMJ1AOI4    68.10
    72  0A:24:D8:D9:24:70                   IgorKotya    67.85
    73  E8:28:C1:DC:C6:B0                MIREA_GUESTS    64.65
    74  8A:A3:03:73:52:08             Galaxy A30s5208    57.52
    75  5E:C7:C0:E4:D7:D4                   realme 10    54.42
    76  E8:28:C1:DC:54:72                                51.23
    77  4A:86:77:04:B7:28           iPhone (Искандер)    50.13
    78  B6:C4:55:B5:53:24                     Redmi 9    49.78
    79  E8:28:C1:DC:BD:50                MIREA_GUESTS    45.72
    80  76:70:AF:A4:D2:AF                        витя    45.55
    81  86:DF:BF:E4:2F:23        DESKTOP-Q7R0KRV 2433    44.80
    82  38:1A:52:0D:8F:EC    EBFCD6C2-EE81fI_DR21AOa0    43.92
    83  EA:7B:9B:D8:56:34                    POCO C40    37.35
    84  38:1A:52:0D:85:1D    EBFCD5C1-EE81fI_DN11AOcY    34.70
    85  00:26:CB:AA:62:71                         IKB    32.82
    86  96:FF:FC:91:EF:64                                32.13
    87  E8:28:C1:DC:33:12                                22.98
    88  E8:28:C1:DC:F0:90                                21.87
    89  3A:70:96:C6:30:2C           iPhone (Искандер)    21.67
    90  36:46:53:81:12:A0          GGWPRedmi Note 10S    20.80
    91  CE:C3:F7:A4:7E:B3                DIRECT-A6-HP    20.40
    92  26:20:53:0C:98:E8                                17.42
    93  92:12:38:E5:7E:1E                                14.47
    94  E8:28:C1:DC:33:10                                14.10
    95  E8:28:C1:DB:F5:F0                MIREA_GUESTS    14.03
    96  E8:28:C1:DC:0B:B0                                13.87
    97  E8:28:C1:DB:F5:F2                                13.03
    98  02:67:F1:B0:6C:98                     MT_FREE    10.85
    99  E8:28:C1:DE:74:30                                 8.47
    100 1E:C2:8E:D8:30:91                        Damy     8.30
    101 8E:1F:94:96:DA:FD          iPhone (Анастасия)     6.92
    102 E0:D9:E3:49:04:50                                 6.68
    103 CE:48:E7:86:4E:33          iPhone (Анастасия)     4.92
    104 00:26:99:BA:75:8F                                 4.80
    105 2A:E8:A2:02:01:73               Redmi Note 9S     3.67
    106 2E:FE:13:D0:96:51            Redmi Note 8 Pro     0.97
    107 9C:A5:13:28:D5:89              Galaxy M012063     0.72
    108 22:C9:7F:A9:BA:9C                                 0.68
    109 E8:28:C1:DC:54:B0                                 0.60
    110 D2:25:91:F6:6C:D8                        Саня     0.22
    111 3A:DA:00:F9:0C:02           iPhone XS Max 🦊🐱🦊     0.15
    112 E8:28:C1:DB:FC:F2                                 0.15
    113 DC:09:4C:32:34:9B              kak_vashi_dela     0.13
    114 F2:30:AB:E9:03:ED             iPhone (Uliana)     0.12
    115 E0:D9:E3:49:04:40                                 0.12
    116 00:03:7A:1A:03:56                     MT_FREE     0.10
    117 B2:CF:C0:00:4A:60         Михаил's Galaxy M32     0.08
    118 BE:FD:EF:18:92:44                     Димасик     0.07
    119 02:BC:15:7E:D5:DC                     MT_FREE     0.03
    120 00:23:EB:E3:49:31                                 0.03
    121 00:3E:1A:5D:14:45                     MT_FREE     0.03
    122 76:C5:A0:70:08:96                                 0.03
    123 82:CD:7D:04:17:3B                                 0.03
    124 E0:D9:E3:49:00:B0                                 0.02
    125 E8:28:C1:DC:54:B2                                 0.02
    126 C6:BC:37:7A:67:0D           Mura's Galaxy A52     0.00
    127 12:48:F9:CF:58:8E                                 0.00
    128 76:E4:ED:B0:5C:9A              Инет от Путина     0.00
    129 E0:D9:E3:48:FF:D0                                 0.00
    130 E2:37:BF:8F:6A:7B                                 0.00
    131 C2:B5:D7:7F:07:A8 DIRECT-a8-HP M227f LaserJet     0.00
    132 8A:4E:75:44:5A:F6                 quiksmobile     0.00
    133 00:03:7A:1A:18:56                                 0.00
    134 E8:28:C1:DE:47:D1                                 0.00
    135 A2:FE:FF:B8:9B:C9                  Christie’s     0.00
    136 00:09:9A:12:55:04                                 0.00
    137 E8:28:C1:DC:3A:B0                                 0.00
    138 E8:28:C1:DC:0B:B2                                 0.00
    139 E8:28:C1:DC:3C:80                                 0.00
    140 00:23:EB:E3:44:31                                 0.00
    141 A6:F7:05:31:E8:EE                                 0.00
    142 BA:2A:7A:DD:38:3E                Айфон (Oleg)     0.00
    143 12:54:1A:C6:FF:71                      Amuler     0.00
    144 76:5E:F3:F9:A5:1C                Redmi 9C NFC     0.00
    145 00:03:7F:12:34:56                     MT_FREE     0.00
    146 E8:28:C1:DC:03:30                                 0.00
    147 B2:1B:0C:67:0A:BD                                 0.00
    148 E0:D9:E3:49:00:B1                                 0.00
    149 E8:28:C1:DC:BD:52               MIREA_HOTSPOT     0.00
    150 E8:28:C1:DE:72:D0                                 0.00
    151 E0:D9:E3:49:04:41                                 0.00
    152 00:26:99:F1:1A:E1                         IKB     0.00
    153 00:23:EB:E3:44:32                                 0.00
    154 00:26:CB:AA:62:72                        GIVC     0.00
    155 E0:D9:E3:48:B4:D2                                 0.00
    156 AE:3E:7F:C8:BC:8E                    Igorek✨     0.00
    157 02:B3:45:5A:05:93                    HONOR 30     0.00
    158 00:00:00:00:00:00                                 0.00
    159 6A:B0:1A:C2:DF:49                                 0.00
    160 E8:28:C1:DC:3C:90                                 0.00
    161 30:B4:B8:11:C0:90                                 0.00
    162 00:26:99:F2:7A:EF                                 0.00
    163 02:CF:8B:87:B4:F9                     MT_FREE     0.00
    164 E8:28:C1:DC:03:32                                 0.00
    165 00:53:7A:99:98:56                     MT_FREE     0.00
    166 00:03:7F:10:17:56                                 0.00
    167 00:0D:97:6B:93:DF                                 0.00
    168 E8:28:C1:DE:47:D0                MIREA_GUESTS     0.00
    169       Station MAC                                   NA

## Оценка результата

Задание было выполнено

## Вывод

Были развиты практические навыки использования языка программирования R
для обработки данных
