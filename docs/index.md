---
title: "Einführung in die Datenvisualisierung mit R"
author: "Tillmann Schwörer"
date: "2021-05-03"
site: bookdown::bookdown_site
documentclass: book
output:
  bookdown::gitbook: 
    config:
      toc:
        collapse: section 
      df_print: paged
github-repo: tillschwoerer/idw-datenvisualisierung
---


# R und RStudio

An dieser Stelle stichpunktartig ein paar zentrale Informationen zum Arbeiten mit R und RStudio.

##

- **R** ist eine Software für statistische Berechnungen und Grafiken. 
- **RStudio** ist eine integrierte Entwicklungsumgebung (IDE) für R. Wir werden in diesem Kurs ausschließlich über RStudio mit R arbeiten (wobei dies bspw. auch über die Kommandozeile, Jupyter Notebooks, oder andere IDEs möglich ist)
- Die Funktionalität der Basisinstallation lässt sich über R Pakete in jede denkbare Richtung erweitern (Datenaufbereitung, Visualisierung, Maschinelles Lernen, ...). Pakete können von jedermann geschrieben werden und über Plattformen wie [CRAN](https://cran.r-project.org/web/views/) oder Github der Allgemeinheit zur Verfügung gestellt werden.
- Pakete müssen grundsätzlich nur 1 mal installiert werden, wobei ein gelegentliches Update Sinn macht. Jedes Mal, wenn die Funktionalität des Paketes in ein R Skript einebunden werden soll, muss das Paket über den `library` Befehl geladen werden.
- Dieser Kurs nutzt insbesondere das R Paket **tidyverse**. Genauer gesagt handelt es sich dabei um eine Sammlung mehrerer Pakete. Enthalten ist darin insbesondere das Paket **ggplot2**, welches wir für die Visualisierungen verwendet werden. An dieser Stelle auch der Hinweis, dass sich die Syntax des tidyverse teilweise deutlich von base R Syntax unterscheidet.
- Programmcode kann einerseits in **R Skripten** (Dateiendung: .R) und **R Markdown** Dateien (.Rmd) geschrieben werden. Erstere eignen sich insbesondere, wenn der Programmcode im Vordergrund steht. Letztere eignen sich insbesondere, wenn auch Erläuterungen und die Präsentation von Ergebnissen wichtig sind. 

## Datenmanipulation
Die wichtigsten Operationen zur Datenmanipulation werden nun ganz kurz angerissen:

Zu Beginn der R Dateien sind die benötigten Pakete einzubinden. 


```r
library(tidyverse)
```

```
## Warning: Paket 'tidyverse' wurde unter R Version 4.0.5 erstellt
```

```
## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --
```

```
## v ggplot2 3.3.3     v purrr   0.3.4
## v tibble  3.1.0     v dplyr   1.0.5
## v tidyr   1.1.3     v stringr 1.4.0
## v readr   1.4.0     v forcats 0.5.1
```

```
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

Die Funktion `read_rds` ist teil des Paketes **tidyverse**. Das heißt, sie können die Funktion nur verwenden, wenn sie vorher das Paket geladen haben. In den meisten Fällen werden wir Daten von Komma-separierte Datein (.csv) einlesen. Gelegentich werden wir auch Dateien mit dem R spezifischen Typ .rds einlesen.Der Pfeil Operator `<-` verwenden wir, um ein bestimmtes Object (hier: ein Datensatz) einem Namen (hier: df) zuzordnen. Nun taucht der Datensatz unter diesem Namen im **Environment** Tab von RStudio auf.


```r
df <- read_csv("data/wdi.csv")
```

```
## 
## -- Column specification --------------------------------------------------------
## cols(
##   .default = col_double(),
##   iso2c = col_character(),
##   land = col_character(),
##   kontinent = col_character(),
##   subregion = col_character()
## )
## i Use `spec()` for the full column specifications.
```

Einen schnellen Überblick über einen Datensatz können wir uns mithilfe der folgenden Funktionen verschaffen:


```r
head(df)     # Erste 6 Zeilen
```

```
## # A tibble: 6 x 23
##   iso2c land    kontinent subregion        jahr bevölkerung bevölkerung_weiblich
##   <chr> <chr>   <chr>     <chr>           <dbl>       <dbl>                <dbl>
## 1 AD    Andorra Europe    Southern Europe  1990       54509                   NA
## 2 AD    Andorra Europe    Southern Europe  1991       56671                   NA
## 3 AD    Andorra Europe    Southern Europe  1992       58888                   NA
## 4 AD    Andorra Europe    Southern Europe  1993       60971                   NA
## 5 AD    Andorra Europe    Southern Europe  1994       62677                   NA
## 6 AD    Andorra Europe    Southern Europe  1995       63850                   NA
## # ... with 16 more variables: bevölkerung_0_14 <dbl>, bevölkerung_65+ <dbl>,
## #   bevölkerung_land <dbl>, bevölkerung_stadt <dbl>,
## #   bevölkerung_unter$5.50 <dbl>, bruttosozialprodukt <dbl>, fläche <dbl>,
## #   lebenserwartung <dbl>, lebenserwartung_mann <dbl>,
## #   lebenserwartung_frau <dbl>, kindersterblichkeit <dbl>, geburtenrate <dbl>,
## #   co2_emissionen <dbl>, literacy_rate <dbl>, militärausgaben <dbl>,
## #   mordrate <dbl>
```

```r
glimpse(df)  # Kurzvorschau jeder Variable mit einigen Metadaten
```

```
## Rows: 6,177
## Columns: 23
## $ iso2c                    <chr> "AD", "AD", "AD", "AD", "AD", "AD", "AD", "AD~
## $ land                     <chr> "Andorra", "Andorra", "Andorra", "Andorra", "~
## $ kontinent                <chr> "Europe", "Europe", "Europe", "Europe", "Euro~
## $ subregion                <chr> "Southern Europe", "Southern Europe", "Southe~
## $ jahr                     <dbl> 1990, 1991, 1992, 1993, 1994, 1995, 1996, 199~
## $ bevölkerung              <dbl> 54509, 56671, 58888, 60971, 62677, 63850, 643~
## $ bevölkerung_weiblich     <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N~
## $ bevölkerung_0_14         <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N~
## $ `bevölkerung_65+`        <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N~
## $ bevölkerung_land         <dbl> 5.288, 5.470, 5.676, 5.889, 6.110, 6.339, 6.5~
## $ bevölkerung_stadt        <dbl> 94.712, 94.530, 94.324, 94.111, 93.890, 93.66~
## $ `bevölkerung_unter$5.50` <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N~
## $ bruttosozialprodukt      <dbl> 1029048482, 1106928583, 1210013652, 100702575~
## $ fläche                   <dbl> 470, 470, 470, 470, 470, 470, 470, 470, 470, ~
## $ lebenserwartung          <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N~
## $ lebenserwartung_mann     <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N~
## $ lebenserwartung_frau     <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N~
## $ kindersterblichkeit      <dbl> 9.1, 8.8, 8.6, 8.4, 8.2, 7.9, 7.7, 7.4, 7.1, ~
## $ geburtenrate             <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N~
## $ co2_emissionen           <dbl> 407.037, 407.037, 407.037, 410.704, 407.037, ~
## $ literacy_rate            <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N~
## $ militärausgaben          <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N~
## $ mordrate                 <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N~
```

Wenn wir genauere Information zu einer R Funktion oder einem Paket benötigen können wir die Hilfe verwenden, z.B. `help(head)`.

Wir verwenden die `filter` Funktion, um eine Teilmenge der Zeilen zu selektieren. Hier wählen wir alle Zeilen, für die Variable `jahr` den Wert 2018 annimmt und die Variale `bevölkerung` größer als 200 Millionen ist.


```r
filter(df, jahr==2018 & bevölkerung>200000000)
```

```
## # A tibble: 6 x 23
##   iso2c land      kontinent   subregion       jahr bevölkerung bevölkerung_weib~
##   <chr> <chr>     <chr>       <chr>          <dbl>       <dbl>             <dbl>
## 1 BR    Brazil    South Amer~ South America   2018   209469333              50.8
## 2 CN    China     Asia        Eastern Asia    2018  1392730000              48.7
## 3 ID    Indonesia Asia        South-Eastern~  2018   267663435              49.6
## 4 IN    India     Asia        Southern Asia   2018  1352617328              48.0
## 5 PK    Pakistan  Asia        Southern Asia   2018   212215030              48.5
## 6 US    United S~ North Amer~ Northern Amer~  2018   326687501              50.5
## # ... with 16 more variables: bevölkerung_0_14 <dbl>, bevölkerung_65+ <dbl>,
## #   bevölkerung_land <dbl>, bevölkerung_stadt <dbl>,
## #   bevölkerung_unter$5.50 <dbl>, bruttosozialprodukt <dbl>, fläche <dbl>,
## #   lebenserwartung <dbl>, lebenserwartung_mann <dbl>,
## #   lebenserwartung_frau <dbl>, kindersterblichkeit <dbl>, geburtenrate <dbl>,
## #   co2_emissionen <dbl>, literacy_rate <dbl>, militärausgaben <dbl>,
## #   mordrate <dbl>
```

Alternativ können wir dies unter Verwendung des sog. Pipe Operators (`%>%`) wie folgt schreiben. 


```r
df %>% filter(jahr==2018 & bevölkerung>200000000)
```

```
## # A tibble: 6 x 23
##   iso2c land      kontinent   subregion       jahr bevölkerung bevölkerung_weib~
##   <chr> <chr>     <chr>       <chr>          <dbl>       <dbl>             <dbl>
## 1 BR    Brazil    South Amer~ South America   2018   209469333              50.8
## 2 CN    China     Asia        Eastern Asia    2018  1392730000              48.7
## 3 ID    Indonesia Asia        South-Eastern~  2018   267663435              49.6
## 4 IN    India     Asia        Southern Asia   2018  1352617328              48.0
## 5 PK    Pakistan  Asia        Southern Asia   2018   212215030              48.5
## 6 US    United S~ North Amer~ Northern Amer~  2018   326687501              50.5
## # ... with 16 more variables: bevölkerung_0_14 <dbl>, bevölkerung_65+ <dbl>,
## #   bevölkerung_land <dbl>, bevölkerung_stadt <dbl>,
## #   bevölkerung_unter$5.50 <dbl>, bruttosozialprodukt <dbl>, fläche <dbl>,
## #   lebenserwartung <dbl>, lebenserwartung_mann <dbl>,
## #   lebenserwartung_frau <dbl>, kindersterblichkeit <dbl>, geburtenrate <dbl>,
## #   co2_emissionen <dbl>, literacy_rate <dbl>, militärausgaben <dbl>,
## #   mordrate <dbl>
```

Der Vorteil davon ist hier noch nicht ersichtlich, sondern erst, wenn wir mehrere Verarbeiungsschritte miteinander verketten, bspw. durch Selektion einzelner Spalten (`select`) und Sortierung (`arrange`). Der Vorteil besteht darin, dass wir nicht nach jeder Operation ein Zwischenergebnis speichern müssen. 


```r
df %>% 
  filter(jahr==2018 & bevölkerung>200000000) %>%   # Selektion einzelner Zeilen
  select(land, kontinent, bevölkerung) %>%         # Selektion einzelner Spalten
  arrange(-bevölkerung)                            # Sortieren anhand der Spalte Bevölkerung
```

```
## # A tibble: 6 x 3
##   land          kontinent     bevölkerung
##   <chr>         <chr>               <dbl>
## 1 China         Asia           1392730000
## 2 India         Asia           1352617328
## 3 United States North America   326687501
## 4 Indonesia     Asia            267663435
## 5 Pakistan      Asia            212215030
## 6 Brazil        South America   209469333
```

Mithilfe der Funktion `mutate` können wir neue Variablen erstellen, oder alte überschreiben.


```r
df %>%
  select(land, bevölkerung, bruttosozialprodukt) %>%
  mutate(pro_kopf_einkommen = bruttosozialprodukt / bevölkerung) %>%
  head(1)
```

```
## # A tibble: 1 x 4
##   land    bevölkerung bruttosozialprodukt pro_kopf_einkommen
##   <chr>         <dbl>               <dbl>              <dbl>
## 1 Andorra       54509         1029048482.             18879.
```
Mithilfe der Funktion `summarise` können wir unterschiedliche aggregierte Statistiken berechnen. Unter `help(summarise)` können sie wichtige Funktionen sehen, die zur Verfügung stehen. Zu beachten ist die Behandlung von fehlenden Werten (`NA`, not available). Um trotz vorhandener fehlender Werte ein Ergebnis angezeigt zu bekommen, muss die Option `na.rm` (remove NAs) auf TRUE gesetzt werden.


```r
df %>% summarise(welt_gesamtbevölkerung = sum(bevölkerung, na.rm=TRUE),
                 durchschnittliche_bevölkerung = mean(bevölkerung, na.rm=TRUE),
                 max_bevölkerung = max(bevölkerung, na.rm=TRUE))
```

```
## # A tibble: 1 x 3
##   welt_gesamtbevölkerung durchschnittliche_bevölkerung max_bevölkerung
##                    <dbl>                         <dbl>           <dbl>
## 1          185924258270.                     30187410.      1392730000
```

Häufig kombinieren wir `summarise` mit der Funktion `group_by`. Dann wird die Berechnung nicht für den gesamten Datensatz ausgeführt, sondern separat für jede durch `group_by` definierte Gruppe.


```r
df %>%
  group_by(kontinent) %>%
  summarise(sum(bevölkerung, na.rm=TRUE))
```

```
## # A tibble: 7 x 2
##   kontinent               `sum(bevölkerung, na.rm = TRUE)`
##   <chr>                                              <dbl>
## 1 Africa                                      26463948892 
## 2 Asia                                       112029446576.
## 3 Europe                                      21168009976 
## 4 North America                               14678984242 
## 5 Oceania                                       981192777 
## 6 Seven seas (open ocean)                        46714086 
## 7 South America                               10555961721
```
