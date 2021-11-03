---
title: "Einführung in die Datenvisualisierung mit R"
subtitle: "IDW Workshop"
author: "Prof. Dr. Tillmann Schwörer, FH Kiel, Studiengang Data Science"
date: "2021-11-03"
site: bookdown::bookdown_site
documentclass: book
cover-image: "img/FH-Kiel-Logo.png"
github-repo: tillschwoerer/idw-datenvisualisierung
output:
  bookdown::gitbook: 
    config:
      toc:
        collapse: section 
---


```r
knitr::opts_chunk$set(warning=FALSE, message=FALSE)
```


# Einleitung

An dieser Stelle stichpunktartig ein paar zentrale Informationen zur Motivation dieses Buches, sowie einige Starthilfen zum Arbeiten mit R.

## Dieses Buch
Ein Bild sagt mehr als 1000 Worte", sagt das Sprichwort. Ganz in diesem Sinne sind Visualisierungen eine wirkungsvolle Technik, um komplexe Zusammenhänge in Daten zu analysieren und für andere greifbar zu machen.

Was benötigen wir für derart wirkungsvolle Visualisierungen? (1) Interessante und komplexe Daten, (2) Leitlinien/Reflektion, was eine **gute Grafik** ausmacht, (3) Kenntnisse, wie wir Visualisierungen (mithilfe der Programmiersprache R) umsetzen können.

Der Fokus dieses Buches liegt auf letzterem Punkt. Es demonstriert, wie mithilfe des R Paketes ggplot2 unterschiedlichste Arten von Daten und Fragestellungen visuell dargestellt werden können.

Diese Demonstration erfolgt anhand von drei verschiedenen Datensätzen: (1) Daten zu Gebrauchtwagen, die über Ebay-Kleinanzeigen verkauft wurden. Die hier verwendeten Daten stellen einen Ausschnitt aus einem noch größeren Datensatz dar, der über die Plattform Kaggle erhältlich ist. (2) Ausgewählte Informationen der [World Development Indicators](https://databank.worldbank.org/source/world-development-indicators) von der Weltbank. (3) Daten zu Luft-Schadstoffmessungen an ausgewählten Standorten in Deutschland seit 2006. 


## R und RStudio
Dieses Buch ist keine Einführung in die Programmiersprache R. Daher an dieser Stelle nur ein sehr kondensierte Zusammenfassung wesentlicher Aspekte.

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

Anschließend lesen wir die Daten ein.


```r
df <- read_csv("data/gebrauchtwagen.csv")
```

Die Funktion `read_csv` ist teil des Paketes **tidyverse**. Das heißt, sie können die Funktion nur verwenden, wenn sie vorher das Paket geladen haben. In den meisten Fällen werden wir Daten von Komma-separierte Datein (.csv) einlesen. Gelegentich werden wir auch Dateien mit dem R spezifischen Typ .rds einlesen.Der Pfeil Operator `<-` verwenden wir, um ein bestimmtes Object (hier: ein Datensatz) einem Namen (hier: df) zuzuweisen. Nun erscheint der Datensatz unter diesem Namen im **Environment** Tab von RStudio

Einen schnellen Überblick über einen Datensatz können wir uns mithilfe der folgenden Funktionen verschaffen:


```r
glimpse(df)  # Kurzvorschau jeder Variable mit einigen Metadaten
```

```
## Rows: 20,000
## Columns: 13
## $ name        <chr> "smart_forTwo_Softtouch__passion_Inspektion_fuer_687_Euro_~
## $ preis       <dbl> 5299, 1100, 8199, 8999, 3600, 2490, 15500, 2700, 6200, 279~
## $ alter       <dbl> 7, 16, 14, 8, 13, 11, 7, 12, 8, 8, 22, 8, 9, 15, 16, 18, 1~
## $ kilometer   <dbl> 30000, 125000, 150000, 150000, 150000, 150000, 150000, 900~
## $ hersteller  <chr> "smart", "renault", "audi", "mercedes_benz", "bmw", "peuge~
## $ modell      <chr> "fortwo", "clio", "a4", "c_klasse", "3er", "1_reihe", "5er~
## $ fahrzeugtyp <chr> "kleinwagen", "kleinwagen", "kombi", "limousine", "limousi~
## $ getriebe    <chr> "automatik", "manuell", "automatik", "automatik", "manuell~
## $ ps          <dbl> 71, 75, 131, 136, 116, 68, 177, 69, 125, 67, 115, 121, 129~
## $ kraftstoff  <chr> "benzin", "benzin", "benzin", "diesel", "benzin", "diesel"~
## $ schaden     <chr> "nein", "nein", "nein", "nein", "nein", "nein", "nein", "j~
## $ plz         <dbl> 35315, 36137, 85057, 10627, 65934, 14793, 28816, 30880, 71~
## $ bundesland  <chr> "Hessen", "Hessen", "Bayern", "Berlin", "Hessen", "Branden~
```

```r
head(df)     # Erste 6 Zeilen
```

```
## # A tibble: 6 x 13
##   name        preis alter kilometer hersteller modell fahrzeugtyp getriebe    ps
##   <chr>       <dbl> <dbl>     <dbl> <chr>      <chr>  <chr>       <chr>    <dbl>
## 1 smart_forT~  5299     7     30000 smart      fortwo kleinwagen  automat~    71
## 2 Renault_Cl~  1100    16    125000 renault    clio   kleinwagen  manuell     75
## 3 Audi_A4_Av~  8199    14    150000 audi       a4     kombi       automat~   131
## 4 Mercedes_B~  8999     8    150000 mercedes_~ c_kla~ limousine   automat~   136
## 5 BMW_316_i_~  3600    13    150000 bmw        3er    limousine   manuell    116
## 6 verk._eine~  2490    11    150000 peugeot    1_rei~ kleinwagen  manuell     68
## # ... with 4 more variables: kraftstoff <chr>, schaden <chr>, plz <dbl>,
## #   bundesland <chr>
```

Wenn wir genauere Information zu einer R Funktion oder einem Paket benötigen können wir die Hilfe verwenden, z.B. `help(head)`.

Wir verwenden die `filter` Funktion, um alle diejenigen Autos (Zeilen des Datensatzes) zu selektieren, die eine oder mehrere Bedingungen erfüllen. Hier wählen wir alle Autos des Herstellers Porsche mit mehr als 280 PS.

### Filter


```r
filter(df, hersteller=='porsche' & ps>280)
```

```
## # A tibble: 5 x 13
##   name        preis alter kilometer hersteller modell fahrzeugtyp getriebe    ps
##   <chr>       <dbl> <dbl>     <dbl> <chr>      <chr>  <chr>       <chr>    <dbl>
## 1 Porsche_Ca~ 15500     8    150000 porsche    cayen~ suv         automat~   290
## 2 Porsche_99~ 25996    16    125000 porsche    911    cabrio      automat~   300
## 3 Porsche_Ca~ 18888     9    100000 porsche    cayen~ suv         automat~   290
## 4 Porsche_Bo~ 26900     8     80000 porsche    boxst~ cabrio      manuell    295
## 5 Porsche_996 29996    15    125000 porsche    911    cabrio      automat~   300
## # ... with 4 more variables: kraftstoff <chr>, schaden <chr>, plz <dbl>,
## #   bundesland <chr>
```

Alternativ können wir dies unter Verwendung des sog. Pipe Operators (`%>%`) wie folgt schreiben. 


```r
df %>% filter(hersteller=='porsche' & ps>280)
```

```
## # A tibble: 5 x 13
##   name        preis alter kilometer hersteller modell fahrzeugtyp getriebe    ps
##   <chr>       <dbl> <dbl>     <dbl> <chr>      <chr>  <chr>       <chr>    <dbl>
## 1 Porsche_Ca~ 15500     8    150000 porsche    cayen~ suv         automat~   290
## 2 Porsche_99~ 25996    16    125000 porsche    911    cabrio      automat~   300
## 3 Porsche_Ca~ 18888     9    100000 porsche    cayen~ suv         automat~   290
## 4 Porsche_Bo~ 26900     8     80000 porsche    boxst~ cabrio      manuell    295
## 5 Porsche_996 29996    15    125000 porsche    911    cabrio      automat~   300
## # ... with 4 more variables: kraftstoff <chr>, schaden <chr>, plz <dbl>,
## #   bundesland <chr>
```

### Select und Arrange
Der Vorteil des Pipe Operators wird ersichtlich, wenn wir mehrere Verarbeiungsschritte miteinander verketten, bspw. durch Selektion einzelner Spalten (`select`) und Sortierung (`arrange`). Der Vorteil besteht darin, dass wir nicht nach jeder Operation ein Zwischenergebnis speichern müssen. 


```r
df %>% 
  filter(hersteller=='porsche' & ps>280) %>%   # Selektion einzelner Zeilen
  select(hersteller, ps, alter, preis) %>%     # Selektion einzelner Spalten
  arrange(-preis)                              # Sortieren anhand der Spalte Bevölkerung
```

```
## # A tibble: 5 x 4
##   hersteller    ps alter preis
##   <chr>      <dbl> <dbl> <dbl>
## 1 porsche      300    15 29996
## 2 porsche      295     8 26900
## 3 porsche      300    16 25996
## 4 porsche      290     9 18888
## 5 porsche      290     8 15500
```

### Mutate

Mithilfe der Funktion `mutate` können wir neue Variablen erstellen, oder alte überschreiben.


```r
df %>%
  select(hersteller, modell, preis) %>%
  mutate(preis_in_1000 = preis / 1000) %>%
  head(3)
```

```
## # A tibble: 3 x 4
##   hersteller modell preis preis_in_1000
##   <chr>      <chr>  <dbl>         <dbl>
## 1 smart      fortwo  5299          5.30
## 2 renault    clio    1100          1.1 
## 3 audi       a4      8199          8.20
```
Mithilfe der Funktion `summarise` können wir unterschiedliche aggregierte Statistiken berechnen. Unter `help(summarise)` können sie wichtige Funktionen sehen, die zur Verfügung stehen. Zu beachten ist die Behandlung von fehlenden Werten (`NA`, not available). Um trotz vorhandener fehlender Werte ein Ergebnis angezeigt zu bekommen, muss die Option `na.rm` (remove NAs) auf TRUE gesetzt werden.

### Summarise


```r
df %>% summarise(anzahl_fahrzeuge = n(),
                 durchschnittliche_kilometer = mean(kilometer, na.rm=TRUE),
                 gesamt_kilometer = sum(kilometer, na.rm=TRUE))
```

```
## # A tibble: 1 x 3
##   anzahl_fahrzeuge durchschnittliche_kilometer gesamt_kilometer
##              <int>                       <dbl>            <dbl>
## 1            20000                     124122.       2482430000
```

Häufig kombinieren wir `summarise` mit der Funktion `group_by`. Dann wird die Berechnung nicht für den gesamten Datensatz ausgeführt, sondern separat für jede durch `group_by` definierte Gruppe.

### Group by


```r
df %>%
  group_by(schaden) %>%
  summarise(durchschnitts_preis = mean(preis, na.rm=TRUE))
```

```
## # A tibble: 2 x 2
##   schaden durchschnitts_preis
##   <chr>                 <dbl>
## 1 ja                    2173.
## 2 nein                  6164.
```
