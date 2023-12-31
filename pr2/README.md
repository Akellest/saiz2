Лабораторная работа №2. Основы обработки данных с помощью R
================

## Цель работы

1.  Развить практические навыки использования языка программирования R
    для обработки данных
2.  Закрепить знания базовых типов данных языка R
3.  Развить пркатические навыки использования функций обработки данных
    пакета dplyr – функции select(), filter(), mutate(), arrange(),
    group_by()

## Исходные данные

-   Windows 10
-   RStudio

## Ход работы

``` r
library(dplyr)
```

    Warning: пакет 'dplyr' был собран под R версии 4.3.2


    Присоединяю пакет: 'dplyr'

    Следующие объекты скрыты от 'package:stats':

        filter, lag

    Следующие объекты скрыты от 'package:base':

        intersect, setdiff, setequal, union

### Сколько строк в датафрейме

``` r
data(starwars)
num_rows <- nrow(starwars)
num_rows
```

    [1] 87

### Сколько столбцов в датафрейме

``` r
starwars %>% ncol()
```

    [1] 14

### Примерный вид датафрейма

``` r
starwars %>% glimpse()
```

    Rows: 87
    Columns: 14
    $ name       <chr> "Luke Skywalker", "C-3PO", "R2-D2", "Darth Vader", "Leia Or…
    $ height     <int> 172, 167, 96, 202, 150, 178, 165, 97, 183, 182, 188, 180, 2…
    $ mass       <dbl> 77.0, 75.0, 32.0, 136.0, 49.0, 120.0, 75.0, 32.0, 84.0, 77.…
    $ hair_color <chr> "blond", NA, NA, "none", "brown", "brown, grey", "brown", N…
    $ skin_color <chr> "fair", "gold", "white, blue", "white", "light", "light", "…
    $ eye_color  <chr> "blue", "yellow", "red", "yellow", "brown", "blue", "blue",…
    $ birth_year <dbl> 19.0, 112.0, 33.0, 41.9, 19.0, 52.0, 47.0, NA, 24.0, 57.0, …
    $ sex        <chr> "male", "none", "none", "male", "female", "male", "female",…
    $ gender     <chr> "masculine", "masculine", "masculine", "masculine", "femini…
    $ homeworld  <chr> "Tatooine", "Tatooine", "Naboo", "Tatooine", "Alderaan", "T…
    $ species    <chr> "Human", "Droid", "Droid", "Human", "Human", "Human", "Huma…
    $ films      <list> <"The Empire Strikes Back", "Revenge of the Sith", "Return…
    $ vehicles   <list> <"Snowspeeder", "Imperial Speeder Bike">, <>, <>, <>, "Imp…
    $ starships  <list> <"X-wing", "Imperial shuttle">, <>, <>, "TIE Advanced x1",…

### Сколько уникальных рас персонажей представлено

``` r
unique_species <- starwars %>%
  select(species) %>%
  distinct()
nrow(unique_species)
```

    [1] 38

### Самый высокий персонаж

``` r
tallest_character <- starwars %>%
  filter(height == max(height, na.rm = TRUE))
names_only <- tallest_character %>%
  select(name)
as.character(unlist(names_only))[1]
```

    [1] "Yarael Poof"

### Персонажи ниже 170

``` r
name_height <- starwars %>%
  filter(height < 170) %>%
  select(name, height)

print(name_height)
```

    # A tibble: 23 × 2
       name                  height
       <chr>                  <int>
     1 C-3PO                    167
     2 R2-D2                     96
     3 Leia Organa              150
     4 Beru Whitesun lars       165
     5 R5-D4                     97
     6 Yoda                      66
     7 Mon Mothma               150
     8 Wicket Systri Warrick     88
     9 Nien Nunb                160
    10 Watto                    137
    # ℹ 13 more rows

### ИМТ для всех персонажей

``` r
starwars_with_bmi <- starwars %>%
  mutate(BMI = mass / (height/100)^2)
mass_height <- starwars_with_bmi %>%
  select(name, mass, height)
print(mass_height)
```

    # A tibble: 87 × 3
       name                mass height
       <chr>              <dbl>  <int>
     1 Luke Skywalker        77    172
     2 C-3PO                 75    167
     3 R2-D2                 32     96
     4 Darth Vader          136    202
     5 Leia Organa           49    150
     6 Owen Lars            120    178
     7 Beru Whitesun lars    75    165
     8 R5-D4                 32     97
     9 Biggs Darklighter     84    183
    10 Obi-Wan Kenobi        77    182
    # ℹ 77 more rows

### 10 самых “вытянутых” персонажей

``` r
stretched10 <- starwars %>%
  mutate(stretchiness = mass / height) %>%
  arrange(desc(stretchiness)) %>%
  head(10)
mass_height <- stretched10 %>%
  select(name, mass, height)
print(mass_height)
```

    # A tibble: 10 × 3
       name                   mass height
       <chr>                 <dbl>  <int>
     1 Jabba Desilijic Tiure  1358    175
     2 Grievous                159    216
     3 IG-88                   140    200
     4 Owen Lars               120    178
     5 Darth Vader             136    202
     6 Jek Tono Porkins        110    180
     7 Bossk                   113    190
     8 Tarfful                 136    234
     9 Dexter Jettster         102    198
    10 Chewbacca               112    228

### Средний возраст персонажей каждой расы

``` r
grouped_data <- starwars %>%
  group_by(species) %>%
  summarize(mean_age = mean(birth_year, na.rm = TRUE)) %>%
  filter(!is.nan(mean_age))

grouped_data
```

    # A tibble: 16 × 2
       species        mean_age
       <chr>             <dbl>
     1 Cerean             92  
     2 Droid              53.3
     3 Ewok                8  
     4 Gungan             52  
     5 Human              53.4
     6 Hutt              600  
     7 Kel Dor            22  
     8 Mirialan           49  
     9 Mon Calamari       41  
    10 Rodian             44  
    11 Trandoshan         53  
    12 Twi'lek            48  
    13 Wookiee           200  
    14 Yoda's species    896  
    15 Zabrak             54  
    16 <NA>               62  

### Найти самый распространенный цвет глаз персонажей

``` r
most_common_eye_color <- starwars %>%
  group_by(eye_color) %>%
  summarise(count = n()) %>%
  arrange(desc(count)) %>%
  head(1)
print(most_common_eye_color)
```

    # A tibble: 1 × 2
      eye_color count
      <chr>     <int>
    1 brown        21

### Средняя длина имени в каждой расе

``` r
avg_name_length_by_species <- starwars %>%
  group_by(species) %>%
  summarise(mean_name_length = mean(nchar(name)))
print(avg_name_length_by_species)
```

    # A tibble: 38 × 2
       species   mean_name_length
       <chr>                <dbl>
     1 Aleena               13   
     2 Besalisk             15   
     3 Cerean               12   
     4 Chagrian             10   
     5 Clawdite             10   
     6 Droid                 4.83
     7 Dug                   7   
     8 Ewok                 21   
     9 Geonosian            17   
    10 Gungan               11.7 
    # ℹ 28 more rows

## Оценка результата

Задание было выполнено, изучен пакет *dplyr*

## Вывод

Были развиты практические навыки использования языка программирования R
для обработки данных
