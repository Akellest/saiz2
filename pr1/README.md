Лабораторная работа №1
================

## Цель работы

## Задание

## Исходные данные

-   Windows 10
-   RStudio

## Ход работы

1.  Устанавливаем swirl
2.  Выполняем команду swirl::swirl() в консоли
3.  Следуем пути R Programming: The basics of programming in R -\> R
    Programming
4.  Проходим 4 первых урока

### 1. Basic Building Blocks

In its simplest form, R can be used as an interactive calculator. Type
5 + 7 and press Enter

``` r
5+7
```

    [1] 12

To assign the result of 5 + 7 to a new variable called x, you type x \<-
5 + 7. This can be read as ‘x gets 5 plus 7’. Give it a try now.

``` r
x <- 5+7
```

To view the contents of the variable x, just type x and press Enter. Try
it now.

``` r
x
```

    [1] 12

Now, store the result of x - 3 in a new variable called y.

``` r
y <- x - 3
```

What is the value of y? Type y to find out.

``` r
y
```

    [1] 9

The easiest way to create a vector is with the c() function, which
stands for ‘concatenate’ or ‘combine’. To create a vector containing the
numbers 1.1, 9, and 3.14, type c(1.1, 9, 3.14). Try it now and store the
result in a variable called z.

``` r
z <- c(1.1, 9, 3.14)
```

Anytime you have questions about a particular function, you can access
R’s built-in help files via the `?` command. For example, if you want
more information on the c() function, type ?c without the parentheses
that normally follow a function name. Give it a try.

``` r
?c
```

    запускаю httpd сервер помощи... готово

Type z to view its contents. Notice that there are no commas separating
the values in the output.

``` r
z
```

    [1] 1.10 9.00 3.14

You can combine vectors to make a new vector. Create a new vector that
contains z, 555, then z again in that order. Don’t assign this vector to
a new variable, so that we can just see the result immediately.

``` r
c(z, 555, z)
```

    [1]   1.10   9.00   3.14 555.00   1.10   9.00   3.14

Numeric vectors can be used in arithmetic expressions. Type the
following to see what happens: z \* 2 + 100.

``` r
z*2+100
```

    [1] 102.20 118.00 106.28

Take the square root of z - 1 and assign it to a new variable called
my_sqrt.

``` r
my_sqrt <- sqrt(z-1)
```

Before we view the contents of the my_sqrt variable, what do you think
it contains? 2: a vector of length 3 Print the contents of my_sqrt.

``` r
my_sqrt
```

    [1] 0.3162278 2.8284271 1.4628739

Now, create a new variable called my_div that gets the value of z
divided by my_sqrt.

``` r
my_div <- z/my_sqrt
```

Which statement do you think is true? 2: The first element of my_div is
equal to the first element of z divided by the first element of my_sqrt,
and so on… Go ahead and print the contents of my_div.

``` r
my_div
```

    [1] 3.478505 3.181981 2.146460

To see another example of how this vector ‘recycling’ works, try adding
c(1, 2, 3, 4) and c(0, 10). Don’t worry about saving the result in a new
variable.

``` r
c(1, 2, 3, 4) + c(0, 10)
```

    [1]  1 12  3 14

Try c(1, 2, 3, 4) + c(0, 10, 100) for an example.

``` r
c(1, 2, 3, 4) + c(0, 10, 100)
```

    Warning in c(1, 2, 3, 4) + c(0, 10, 100): длина большего объекта не является
    произведением длины меньшего объекта

    [1]   1  12 103   4

In many programming environments, the up arrow will cycle through
previous commands. Try hitting the up arrow on your keyboard until you
get to this command (z \* 2 + 100), then change 100 to 1000 and hit
Enter. If the up arrow doesn’t work for you, just type the corrected
command.

``` r
z*2+1000
```

    [1] 1002.20 1018.00 1006.28

You can type the first two letters of the variable name, then hit the
Tab key (possibly more than once). Most programming environments will
provide a list of variables that you’ve created that begin with ‘my’.
This is called auto-completion and can be quite handy when you have many
variables in your workspace. Give it a try. (If auto-completion doesn’t
work for you, just type my_div and press Enter.)

``` r
my_div
```

    [1] 3.478505 3.181981 2.146460

### 1. Basic Building Blocks

Determine which directory your R session is using as its current working
directory using getwd().

``` r
getwd()
```

    [1] "C:/Users/alexk/OneDrive/Documents"

List all the objects in your local workspace using ls().

``` r
ls()
```

    [1] "my_div"  "my_sqrt" "x"       "y"       "z"      

Assign 9 to x using x \<- 9.

``` r
x <- 9
```

Now take a look at objects that are in your workspace using ls().

``` r
ls()
```

    [1] "my_div"  "my_sqrt" "x"       "y"       "z"      

List all the files in your working directory using list.files() or
dir().

``` r
dir()
```

     [1] "1.html"                         "1.qmd"                         
     [3] "1.rmarkdown"                    "1_files"                       
     [5] "Assassin's Creed IV Black Flag" "Assassin's Creed Odyssey"      
     [7] "Audacity"                       "Avalanche Studios"             
     [9] "Battlefield 1"                  "Battlefield 2042"              
    [11] "Battlefield V"                  "BioWare"                       
    [13] "BlackSquad"                     "Custom Office Templates"       
    [15] "DeadIslandDE"                   "desktop.ini"                   
    [17] "EA Games"                       "FormatFactory"                 
    [19] "IISExpress"                     "Larian Studios"                
    [21] "My Cheat Tables"                "My Games"                      
    [23] "Need for Speed(TM) Payback"     "NFS SHIFT"                     
    [25] "Project CARS"                   "README.md"                     
    [27] "Rockstar Games"                 "Telltale Games"                
    [29] "testdir"                        "The Mercury man"               
    [31] "The Witcher 3"                  "Visual Studio 2022"            
    [33] "Warm Lamp Games"                "WB Games"                      
    [35] "wmd_symbol_cache"              

As we go through this lesson, you should be examining the help page for
each new function. Check out the help page for list.files with the
command ?list.files.

``` r
?list.files
```

Use the args() function to determine the arguments to list.files().

``` r
args(list.files)
```

    function (path = ".", pattern = NULL, all.files = FALSE, full.names = FALSE, 
        recursive = FALSE, ignore.case = FALSE, include.dirs = FALSE, 
        no.. = FALSE) 
    NULL

Assign the value of the current working directory to a variable called
“old.dir”.

``` r
old.dir <- getwd()
```

Use dir.create() to create a directory in the current working directory
called “testdir”.

``` r
dir.create("testdir")
```

    Warning in dir.create("testdir"): 'testdir' уже существует

Set your working directory to “testdir” with the setwd() command.

``` r
setwd("testdir")
```

Create a file in your working directory called “mytest.R” using the
file.create() function.

``` r
file.create("mytest.R")
```

    [1] TRUE

This should be the only file in this newly created directory. Let’s
check this by listing all the files in the current directory.

``` r
list.files()
```

     [1] "1.html"                         "1.qmd"                         
     [3] "1.rmarkdown"                    "1_files"                       
     [5] "Assassin's Creed IV Black Flag" "Assassin's Creed Odyssey"      
     [7] "Audacity"                       "Avalanche Studios"             
     [9] "Battlefield 1"                  "Battlefield 2042"              
    [11] "Battlefield V"                  "BioWare"                       
    [13] "BlackSquad"                     "Custom Office Templates"       
    [15] "DeadIslandDE"                   "desktop.ini"                   
    [17] "EA Games"                       "FormatFactory"                 
    [19] "IISExpress"                     "Larian Studios"                
    [21] "My Cheat Tables"                "My Games"                      
    [23] "mytest.R"                       "Need for Speed(TM) Payback"    
    [25] "NFS SHIFT"                      "Project CARS"                  
    [27] "README.md"                      "Rockstar Games"                
    [29] "Telltale Games"                 "testdir"                       
    [31] "The Mercury man"                "The Witcher 3"                 
    [33] "Visual Studio 2022"             "Warm Lamp Games"               
    [35] "WB Games"                       "wmd_symbol_cache"              

Check to see if “mytest.R” exists in the working directory using the
file.exists() function.

``` r
file.exists("mytest.R")
```

    [1] TRUE

Access information about the file “mytest.R” by using file.info().

``` r
file.info("mytest.R")
```

             size isdir mode               mtime               ctime
    mytest.R    0 FALSE  666 2023-11-08 14:02:57 2023-11-08 14:02:57
                           atime exe
    mytest.R 2023-11-08 14:02:57  no

Change the name of the file “mytest.R” to “mytest2.R” by using
file.rename().

``` r
file.rename("mytest.R", "mytest2.R")
```

    [1] TRUE

Make a copy of “mytest2.R” called “mytest3.R” using file.copy().

``` r
file.copy("mytest2.R", "mytest3.R")
```

    [1] TRUE

Provide the relative path to the file “mytest3.R” by using file.path().

``` r
file.path("mytest3.R")
```

    [1] "mytest3.R"

You can use file.path to construct file and directory paths that are
independent of the operating system your R code is running on. Pass
‘folder1’ and ‘folder2’ as arguments to file.path to make a
platform-independent pathname.

``` r
file.path("folder1", "folder2")
```

    [1] "folder1/folder2"

Take a look at the documentation for dir.create by entering ?dir.create
. Notice the ‘recursive’ argument. In order to create nested
directories, ‘recursive’ must be set to TRUE.

``` r
?dir.create
```

Create a directory in the current working directory called “testdir2”
and a subdirectory for it called “testdir3”, all in one command by using
dir.create() and file.path().

``` r
dir.create(file.path('testdir2', 'testdir3'), recursive = TRUE)
```

Go back to your original working directory using setwd(). (Recall that
we created the variable old.dir with the full path for the orginal
working directory at the start of these questions.)

``` r
setwd(old.dir)
```
