# Geodaten

Das Thema Visualisierung von **Geodaten** erschließen wir uns anhand von Daten der Weltbank aus der Reihe der **World Development Indicators**. Enthalten sind in den Daten alle Länder der Erde und ausgewählte Indikatoren seit 1990. Da die Zeitkomponente für das Verständnis nicht relevant ist, beschränken wir uns in der Folge auf das Jahr 2018 (für 2019 und 2020 fehlen noch viele Werte, daher wurden diese nicht berücksichtigt.)




```r
library(tidyverse)   
library(sf)          
theme_set(
  theme_bw() + 
  theme(panel.background = element_blank(), panel.spacing = unit(0, "lines"),
        plot.background = element_blank(),
        legend.justification = c(0, 0), legend.position = c(0, 0), legend.background = element_blank())
)
```

## Simple Features


```r
wdi <- read_csv("data/wdi.csv")        # Einlesen der World Development Indicators
wdi <- wdi %>% filter(jahr==2018)      # Einschränkung auf das aktuellste Jahr im 
geo <- read_rds("data/geo_wdi.rds")    # Einlesen der Geodaten

# Geodaten werden per Join zum Datensatz hinzugefügt
df <- wdi %>% left_join(geo, by = "iso2c") %>% st_as_sf()

glimpse(df)
```

```
## Rows: 213
## Columns: 24
## $ iso2c                    <chr> "AD", "AE", "AF", "AG", "AL", "AM", "AO", "AR~
## $ land                     <chr> "Andorra", "United Arab Emirates", "Afghanist~
## $ kontinent                <chr> "Europe", "Asia", "Asia", "North America", "E~
## $ subregion                <chr> "Southern Europe", "Western Asia", "Southern ~
## $ jahr                     <dbl> 2018, 2018, 2018, 2018, 2018, 2018, 2018, 201~
## $ bevölkerung              <dbl> 77006, 9630959, 37172386, 96286, 2866376, 295~
## $ bevölkerung_weiblich     <dbl> NA, 30.63669, 48.63585, 51.78850, 49.06309, 5~
## $ bevölkerung_0_14         <dbl> NA, 14.60351, 43.09018, 22.08109, 17.67287, 2~
## $ `bevölkerung_65+`        <dbl> NA, 1.085001, 2.584927, 8.799826, 13.744736, ~
## $ bevölkerung_land         <dbl> 11.938, 13.478, 74.505, 75.401, 39.681, 36.85~
## $ bevölkerung_stadt        <dbl> 88.062, 86.522, 25.495, 24.599, 60.319, 63.14~
## $ `bevölkerung_unter$5.50` <dbl> NA, 0.1, NA, NA, NA, 42.5, 88.5, 12.3, NA, 1.~
## $ bruttosozialprodukt      <dbl> 3.218316e+09, 4.222150e+11, 1.835388e+10, 1.6~
## $ fläche                   <dbl> 470.0, 98647.9, 652860.0, 440.0, 28750.0, 297~
## $ lebenserwartung          <dbl> NA, 77.81400, 64.48600, 76.88500, 78.45800, 7~
## $ lebenserwartung_mann     <dbl> NA, 77.133, 63.047, 75.721, 76.816, 71.215, 5~
## $ lebenserwartung_frau     <dbl> NA, 79.164, 66.026, 77.983, 80.167, 78.354, 6~
## $ kindersterblichkeit      <dbl> 2.9, 6.5, 48.0, 6.0, 8.5, 11.0, 51.9, 8.7, NA~
## $ geburtenrate             <dbl> NA, 1.413, 4.473, 1.994, 1.617, 1.755, 5.519,~
## $ co2_emissionen           <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N~
## $ literacy_rate            <dbl> NA, NA, 43.01972, NA, 98.14115, NA, NA, 99.00~
## $ militärausgaben          <dbl> NA, NA, 198074729, NA, 175886689, 608854650, ~
## $ mordrate                 <dbl> NA, NA, 6.6555612, NA, 2.2894924, 1.6939156, ~
## $ geometry                 <MULTIPOLYGON [°]> MULTIPOLYGON (((1.706055 42..., ~
```

Es handelt sich nicht um einen gewöhnlichen R Datensatz (data frame), sondern um einen sogenannten **Simple Feature** Datensatz. Das besondere daran ist die Variable `geometry`, welche die Koordinaten (Längengrad und Breitengrad) der Ländergrenzen enthält.

Analog zum bisherigen Vorgehen, benötigen wir für die Visualisierung einer Weltkarte nur 3 Dinge: (1) den (Simple Feature) Datensatz, (2) eine Ästhetikzuordnung (die Variable `geometry` wird der Ästhetik **geometry** zugeordet, und (3) den passenden Objecttyp `geom_sf`. 


```r
ggplot(data = df, mapping = aes(geometry = geometry)) + geom_sf()
```

<img src="02-geodaten_files/figure-html/unnamed-chunk-3-1.png" width="672" />

Tatsächlich können wir hier die Ästhetikzuordnung sogar weglassen, die Spezifikation des Datensatzes und `geom_sf` ist hier sogar ausreichend.


```r
ggplot(df) + geom_sf()
```

<img src="02-geodaten_files/figure-html/unnamed-chunk-4-1.png" width="672" />

**Was bedeutet der Begriff feature?** Ein Feature kann grundsätzlich alles mögliche sein (Land, Fluss, Bushaltestelle), in unserem Fall ist es ein Land. Features können durch unterschiedliche Geometrien repräsentiert werden: eine Bushaltestelle durch einen **Punkt**, ein Fluss durch eine **Linie**, ein Land durch ein **Polygon** oder **Multipolygon**. Beispielsweise kann das "Vereinigte Königreich" nur durch einen Multipolygon repräsentiert werden, da es aus zwei Inseln (jeweils Polygone) besteht.


```r
uk <- df %>% filter(land == "United Kingdom")
ggplot(uk) + geom_sf()
```

<img src="02-geodaten_files/figure-html/unnamed-chunk-5-1.png" width="672" />

```r
uk[["geometry"]][[1]][[1]]        # Dies sind die Koordinaten von Irland
```

```
## [[1]]
##            [,1]     [,2]
##  [1,] -1.065576 50.69023
##  [2,] -1.149365 50.65571
##  [3,] -1.175830 50.61523
##  [4,] -1.196094 50.59922
##  [5,] -1.251465 50.58882
##  [6,] -1.306299 50.58853
##  [7,] -1.515332 50.66978
##  [8,] -1.563428 50.66611
##  [9,] -1.515674 50.70332
## [10,] -1.385840 50.73354
## [11,] -1.312793 50.77349
## [12,] -1.144238 50.73472
## [13,] -1.065576 50.69023
```

## Ästhetiken

Die wichtigste Ästhetik ist bei Karten in der Regel die Füllfarbe (`fill`). Wie bisher können sie sie entweder die Füllfarbe entweder auf eine bestimmte Ausprägung (bspw. "red") fixieren, oder eine Variable des Datensatzes der Füllfarbe zuordnen (*aesthetic mapping*). Zu beachten: Die Farbe der Landesgrenzen können über `color` Ästhetik beeinflusst werden. 


```r
map <- df %>% ggplot()                   
map + geom_sf(aes(fill=kontinent))                    # nominal-skalierte Variable
```

<img src="02-geodaten_files/figure-html/unnamed-chunk-6-1.png" width="672" />

```r
map + geom_sf(aes(fill=lebenserwartung))              # kardinal-skalierte Variable
```

<img src="02-geodaten_files/figure-html/unnamed-chunk-6-2.png" width="672" />

```r
map + geom_sf(aes(fill=lebenserwartung), color = NA)  # fest definierte Länderfarbe (hier unsichtbar)
```

<img src="02-geodaten_files/figure-html/unnamed-chunk-6-3.png" width="672" />

## Schichten
Wie bisher können Sie auch hier über zusätzliche `geom_[typ]` Funktionen weitere Schichten definieren. Dies ist auch hier insbesondere für Hervorhebungen hilfreich.


```r
auswahl <- df %>% filter(land %in% c("Germany", "Brazil", "China", "South Africa")) 

map + 
  geom_sf(aes(fill=kontinent)) +
  geom_sf_label(data = auswahl, aes(label=land)) 
```

<img src="02-geodaten_files/figure-html/unnamed-chunk-7-1.png" width="672" />

## Aggregation
In manchen Situationen möchten wir die Daten durch eine Aggregation verdichten, bspw. um die durchschnittliche Lebenserwartung je Kontinent zu berechnen. 



```r
kontinente <- df %>% 
  group_by(kontinent) %>%
  summarise(lebenserwartung = mean(lebenserwartung, na.rm=TRUE)) 
kontinente
```

```
## Simple feature collection with 7 features and 2 fields
## geometry type:  MULTIPOLYGON
## dimension:      XY
## bbox:           xmin: -180 ymin: -55.8917 xmax: 180 ymax: 83.59961
## CRS:            +proj=longlat +datum=WGS84 +no_defs +ellps=WGS84 +towgs84=0,0,0
## # A tibble: 7 x 3
##   kontinent       lebenserwartung                                       geometry
##   <chr>                     <dbl>                             <MULTIPOLYGON [°]>
## 1 Africa                     63.4 (((-2.938306 5.124495, -2.94834 5.118848, -3.~
## 2 Asia                       74.3 (((122.9489 -10.90928, 122.8559 -10.90967, 12~
## 3 Europe                     79.3 (((55.79736 -21.33936, 55.65615 -21.36904, 55~
## 4 North America              76.0 (((-78.89834 8.274268, -78.91812 8.231934, -7~
## 5 Oceania                    73.4 (((158.8788 -54.70977, 158.8452 -54.74922, 15~
## 6 Seven seas (op~            75.3 (((57.65127 -20.48486, 57.5248 -20.51318, 57.~
## 7 South America              74.9 (((-67.5752 -55.88965, -67.61143 -55.8917, -6~
```

Die gute Nachricht: bei der Aggregation wird auch die `geometry` Variable so aktualisiert, dass diese nun die Umrisse der jeweiligen Kontinente repräsentiert. 


```r
kontinente %>% ggplot() + geom_sf(aes(fill = lebenserwartung), color = NA)
```

<img src="02-geodaten_files/figure-html/unnamed-chunk-9-1.png" width="672" />

## Koordinatensystem                         

Bisher haben wir das Thema **Koordinatensystem** nicht explizit angesprochen. Im Kontext von Karten ist es aber besonders relevant, da unsere 3-dimensionale Erde nicht akurat in 2 Dimensionen dargestellt werden kann. Es stehen uns nun unterschiedlich Projektionen zu Verfügung, die jeweils vorgeben, wo die Längen- und Breitengrade in den zwei Dimensionn verortet werden.


Betrachten wir nun folgende Basiskarte:


```r
(basiskarte <- df %>% 
  ggplot() + 
  geom_sf(aes(fill = kontinent), color = NA) +
  scale_fill_brewer(palette = "Set1"))
```

<img src="02-geodaten_files/figure-html/unnamed-chunk-10-1.png" width="672" />

Die **Mercator Projection** stellt Formen und Winkel korrekt dar, aber in Richtung der Pole wird die Größe der Länder künstlich aufgebläht. 


```r
basiskarte + coord_sf(crs = st_crs("+proj=merc")) 
```

<img src="02-geodaten_files/figure-html/unnamed-chunk-11-1.png" width="672" />

Hingegen werden bei der **Lambert Azimuthal Equal Area** (LAEA) Projektion die Ländergrößen korrekt dargestellt, wobei aber eine Verzerrung der Formen und Winkel in Kauf genommen wird. Auch der Perspektivwechsel auf einen anderen Mittelpunkt (hier Lhasa, Tibet) kann einen Mehrwert bzgl. der Interpretation schaffen. 


```r
basiskarte + coord_sf(crs = st_crs("+proj=laea + lat_0=29.6490404 + lon_0=91.0052352"))
```

<img src="02-geodaten_files/figure-html/unnamed-chunk-12-1.png" width="672" />

