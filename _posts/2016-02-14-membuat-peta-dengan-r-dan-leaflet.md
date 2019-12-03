---
layout: post
title: 'Membuat Peta dengan R dan Leaflet: Contoh Data Twitter'
date: 2016-02-14 12:02:43.000000000 +07:00
type: post
published: true
status: publish
tags:
- gis
- R
- twitter
- data-viz
author: Nur Andi Setiabudi
excerpt: Membuat peta dengan R dapat dilakukan dengan mudah menggunakan package leaflet.
  Leaflet memudahkan menghasilkan sebuah peta interaktif
redirect_from: 
  - 2016/02/14/membuat-peta-dengan-r-dan-leaflet/
  - /visualisasi/membuat-peta-dengan-r-dan-leaflet/
---
Beberapa waktu lalu ketika sedang mencari cara untuk membuat peta dengan
R, Google menunjukkan satu *package* yang sangat menarik -
[**leaflet**](https://rstudio.github.io/leaflet). *Package* yang
dikembangkan oleh RStudio ini merupakan *interface* untuk membuat peta
dengan memanfaatkan [Leaflet](http://leafletjs.com/), salah satu
*JavaScript library* untuk pemetaan yang sangat populer dan tentu saja
*open-source*.

Berbeda dengan *package* lain (seperti
[RgoogleMaps](https://cran.r-project.org/web/packages/RgoogleMaps/index.html),
[ggmap](https://cran.r-project.org/web/packages/ggmap/index.html) dan
[maps](https://cran.r-project.org/web/packages/maps/index.html)) yang
menghasilkan peta statis, **leaflet** memudahkan kita untuk membuat
[peta interaktif](http://leafletjs.com/index.html#features) yang lebih
hidup hanya dengan beberapa baris kode R, tanpa perlu mempunyai
pengetahuan tentang JavaScript. Tertarik untuk mencoba? Inilah hasil
"eksperimen" pertama saya dengan **R dan leaflet**.

## Persiapan

Sebenarnya dengan [*R*](https://www.r-project.org/) saja cukup,
namun saya merekomendasikan
[RStudio](https://www.rstudio.com/products/rstudio/).
[Instal](https://nurandi.net/r/menginstal-r-package/) *package
**leaflet*** dari CRAN:

```r
install.packages("leaflet")
```

Atau dari [GitHub](https://github.com/rstudio/leaflet), untuk versi
*development*

```r
devtools::install_github("rstudio/leaflet")
```
Lalu *load* ke dalam *R session*

```r
library(leaflet)
```

## Menandai suatu titik koordinat

OK! saatnya kita "bermain" dengan **leaflet**. Sebelum menggunakan data
Twitter, mari kita mulai dengan menandai suatu titik koordinat pada peta
dengan *icon marker*, misalnya untuk lokasi "Bundaran Hotel Indonesia"
yang menurut Google Maps berada pada koordinat [(-6.1949571,
106.8230631)](https://www.google.co.id/maps/place/Bundaran+Hotel+Indonesia/@-6.1949571,106.8230631).

```r
point <- "Bundaran Hotel Indonesia"
latitude <- -6.1949571
longitude <- 106.8230631

map <- leaflet() %>%
  addTiles() %>%
  addMarkers(lng=longitude, lat=latitude, popup=point)
map
```

<div class="scalable scalable-16x9">
  <div class="scalable-content">
    <iframe src="/src/leaflet-map-bundaran-HI.html"></iframe>
  </div>
</div>

Penjelasannya kurang lebih:

1.  `leaflet()` : menginisiasi *leaflet workspace*, seakan sebuah kanvas
    di mana peta akan digambar di atasnya.
2.  `addTiles()` : menambahkan peta dasar *base-map* (secara *default*
    menggunakan
    [OpenStreetMap](https://www.openstreetmap.org/#map=5/51.500/-0.100)).
3.  `addMarkers()` : menandai titik koordinat (`lng` untuk *longitude*
    dan `lat` untuk *latitude*) dengan *icon marker*. Opsi `popup`
    berguna untuk nenampilkan teks jika icon di-klik.

Kode di atas menggunakan *pipe operator* (`%>%`) dari *package*
[**magrittr**](https://github.com/smbache/magrittr), yang ekuivalen
dengan:

```r
map <- leaflet()
map <- addTiles(map)
map <- addMarkers(map, lng=longitude, lat=latitude, popup=point)
map
```

Memetakan data Twitter
----------------------

Kenapa data Twitter? Beberapa kali saya men-*download* data Twitter
melalui [search API](https://dev.twitter.com/rest/public/search) baik
menggunakan *package*
[**twitteR**](https://cran.r-project.org/web/packages/twitteR/index.html)
maupun *tools* lain. Salah satu hal menarik mengenai tweet-tweet
tersebut adalah adanya *geo-coordinate*, titik *latitude* dan
*longitute*, yang menandai lokasi di mana mereka di-*posting*. Memang,
informasi ini tidak tersedia untuk semua tweets, tapi cukup kaya untuk
dieksplorasi seperti yang akan kita lakukan sekarang.

Dimulai dengan [men-*download* tweets berdasarkan kata kunci tertentu
menggunakan
**twitteR**](https://nurandi.net/socmed/crawling-data-twitter-menggunakan-r/)
:

```r
library(twitteR)
setup_twitter_oauth(consumer_key = "XXX", 
                    consumer_secret = "XXX", 
                    access_token = "XXX", 
                    access_secret = "XXX")

tweetsList <- searchTwitteR("makan siang",
                            geocode = paste(latitude, longitude, "20km", sep = ","),
                            n = 1000)
```

Dengan fungsi di atas, kita berhasil mendapatkan 600-an tweets dengan
kata kunci *"makan siang"* yang berada dalam radius 10km seputar
Bundaran Hotel Indonesia, Jakarta.

Konversi `tweetList` menjadi *data frame*:

```r
tweets <- twListToDF(tweetsList)
```

Sebaiknya titik koordinat merupakan kolom numerik dan tidak ada data
yang *latitude/longitude*-nya kosong (`NA` atau `NULL`).

```r
tweets$latitude <- as.numeric(tweets$latitude)
tweets$longitude <- as.numeric(tweets$longitude)
tweets <- tweets[!is.na(tweets$latitude) & !is.na(tweets$longitude),]
```

Sekarang kita siap untuk *mapping* titik-titik koordinat data Twitter
tersebut dengan **leaflet**:

```r
tweetsmap <- leaflet(data = tweets) %>% 
  addTiles() %>%
  addMarkers(lng = ~longitude, lat = ~latitude, popup = ~screenName)
tweetsmap
```

<div class="scalable scalable-16x9">
  <div class="scalable-content">
    <iframe src="/src/leaflet-map-tweets.html"></iframe>
  </div>
</div>

***Nice!*** Seluruh titik koordinat sudah kita tampilkan dalam peta
hanya dengan empat baris kode R. Mudah, bukan?

Tentu saja kita bisa membuat tampilan peta tersebut menjadi lebih baik.
Misalnya, ada dua hal sederhana yang saya lakukan dalam "eksperimen"
ini. **Pertama**, menjadikan teks pada `popup` sebagai link untuk
membuka tweets pada laman Twitter dengan cara mengubahnya menjadi konten
HTML dengan format:

    "<a href="https://twitter.com/SCREENNAME/status/STATUSID" target="_blank">SCREENNAME</a>"

Berikut ini perintah untuk membuat konten HTML tersebut:

```r
link2tweet <- paste("https://twitter.com",
                    tweets$screenName,
                    "status",
                    tweets$id,
                    sep = "/")

popupLink <- paste0("<a href=\"",
                    link2tweet,
                    "\" target=\"_blank\">",
                    tweets$screenName,
                    "</a>")
```

**Kedua**, membuat *marker cluster* sehingga titik-titik pada peta tidak
terlalu padat. *Marker cluster* mengelompokkan beberapa titik yang
berdekatan dalam *cluster* dan menampilkan kembali seluruh titik apabila
peta diperbesar *(zoom-in)*. Untuk membuat *marker cluster* pada peta
baru kita buat tadi bisa dilakukan dengan terlebih dahulu menghapus
*markers* menggunakan fungsi `clearMarkers()`, lalu buat kembali dengan
fungsi `addMarkers()` yang ditambahkan dengan opsi
`markerClusterOptions()`.

```r
tweetsmap <- clearMarkers(tweetsmap) %>%
  addMarkers(popup = popupLink, clusterOptions = markerClusterOptions())
tweetsmap
```

**Note**: Jika *latitude* dan *longitude* tidak dinyatakan secara
eksplisit, `addMarker()` akan mencari kolom `lat/latitude` dan
`lon/lng/long/longitude` *(case-sensitive)* pada *data-frame*.

Ini adalah hasil akhir yang kita peroleh. *Klik pada titik cluster untuk
memperbesar peta!*

<div class="scalable scalable-16x9">
  <div class="scalable-content">
    <iframe src="/src/leaflet-map-tweets-cluster.html"></iframe>
  </div>
</div>

Kurang canggih? Masih ada fitur-fitur **leaflet** yang lain untuk kita
eksplorasi seperti mengganti *map tiles*, modifikasi *markers* dan *pop
ups*, menambahkan *polygons* dan *lines*, dan
[banyak](http://leafletjs.com/index.html#features)
[lagi](https://rstudio.github.io/leaflet/).
