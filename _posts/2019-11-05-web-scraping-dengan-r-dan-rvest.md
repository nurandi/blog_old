---
title: 'Web Scraping dengan R dan rvest'
author: "Nur Andi Setiabudi"
date: "Novemver 5, 2019"
layout: post
published: yes
tags:
- r
- scraping
- web scraping
- rvest
type: post
excerpt: Tutorial web scraping menggunakan R dan package rvest dengan contoh kasus mengekstrak dan menyimpan data top-scorers Liga Inggris dari laman BBC Sport ke dalam format terstruktur sehingga siap dianalisis lebih lanjut.
---
Di era digital sekarang ini, berbagai data dapat ditemukan dengan mudah di internet. Sebagai seorang data analis dan *scientist*, hal ini tentu menjadi kabar baik karena dapat meningkatkan kualitas model dan mempertajam hasil analisis. Tapi bagaimana jika data tersebut tersedia dalam *website* yang diperuntukan untuk dibaca melalui *browser*? Beberapa *website*  memang menyediakan fasilitas untuk menyimpan data (seperti Twitter melalui *API public*), tapi sayangnya sebagian besar tidak. Salah satu cara yang umum dilakukan adalah dengan membuka satu-per-satu halaman web lalu *"copy-paste"* data secara manual - tentunya akan menyita banyak waktu jika data sangat banyak. *Web scraping* adalah teknik yang lebih "smart" untuk otomatisasi proses *copy-paste* ini. Selain agar lebih efisien, tujuan utama dari *web scraping* sebenarnya adalah memanfaatkan struktur atau pola dari suatu laman web untuk mengekstrak dan menyimpan data dalam format yang diinginkan untuk dianalisis lebih lanjut.

Melalui artikel ini, saya akan membahas bagaimana melakukan *scraping* halaman *web* menggunakan R dan *package* **rvest**. Agar lebih menarik, saya menggunakan contoh *real* berupa data *top-scorers* Liga Inggris dari halaman [BBC Sport](https://www.bbc.com/sport/football/premier-league/top-scorers).


{% include base_path %}
{% capture fig_img %}
![BBC EPL Top Scorers]({{ base_path }}/img/blog/2019-11-05-web-scraping-dengan-r-dan-rvest/bbc-epl-top-scorers.png)
{% endcapture %}

<figure>
{{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
<span class="caption" markdown="1">Top Scorers Liga Inggris. Diakses dari [BBC Sport](https://www.bbc.com/sport/football/premier-league/top-scorers) pada 5 November 2019</span>
</figure> 

**Catatan**: *Web scraping* merupakan salah satu teknik yang sangat bermanfaat untuk mendapatkan data dari internet, tetapi dapat menyebabkan masalah bagi beberapa *website*. Untuk itu, proses *web scraping* harus dilakukan dengan penuh pertimbangan dan etika. Sebelum melakukan *web scraping* sebaiknya periksa dulu apakah website mempunyai file **robots.txt**? Jika iya, lihat apakah diizinkan untuk melakukan *scraping* pada halaman yang kita inginkan. File robots.txt dapat diakses di **nama-domain/robots.txt**, misalnya [https://www.bbc.com/robots.txt](https://www.bbc.com/robots.txt).

# Package rvest

*Package* [**rvest**](https://rvest.tidyverse.org/) ditulis oleh Hadley Wickham dari RStudio. *Package* ini mempunyai fungsi yang serupa dengan *library* **beautiful soup** pada Python, yaitu untuk *web scraping*. Langkah pertama, tentu saja, install dan *load package* **rvest**.


{% highlight r %}
install.packages("rvest")
library(rvest)
{% endhighlight %}

# Inspect and scrape 

Proses *scraping* kita mulai dengan membaca file HTML dari halaman website dengan menggunakan fungsi `read_html`.


{% highlight r %}
url <- "https://www.bbc.com/sport/football/premier-league/top-scorers"
html <- read_html(url)
{% endhighlight %}

Selanjutnya, melalui browser kita identifikasi "letak" dari data yang akan kita ekstrak. Pada contoh ini, kita akan ekstrak list *top scorers* termasuk detailnya meliputi:

* player name
* team
* total goal scored
* minutes played
* minutes per goal
* total assists
* total shots on goal target
* total shots
* percentage on target

Caranya, buka URL *top-scorers* melalui browser (misal pada contoh ini saya menggunakan Google Chrome). Klik kanan, klik **Inspect** lalu klik tab **Element**. Klik icon tanda panah di bagian kiri-atas, lalu arahkan kursor pada teks/element yang akan kita ekstrak. Sebagai contoh untuk mengekstrak nama pemain, bisa arahkan kursor pada salah satu nama pemain.

{% capture fig_img %}
![Inspect element]({{ base_path }}/img/blog/2019-11-05-web-scraping-dengan-r-dan-rvest/inspect-element.png)
{% endcapture %}

<figure>
{{ fig_img | markdownify | remove: "<p>" | remove: "</p>" }}
<span class="caption" markdown="1">Inspect element pada Google Chrome. Dapat ditampilkan dengan cara klik kanan lalu klik *Inspect*</span>
</figure> 

Secara otomatis Chrome akan meng-*highlight* baris *element* HTML di mana teks itu berada, yaitu:

```
<h2 class="top-player-stats__name gel-double-pica">Sadio Mané</h2>
```
Yang kita perlukan adalah *CSS selectors* atau XPath dari *element* tersebut. *CSS selector* biasanya ditandai dengan *class*, *id*, dan sebagainya ([referensi](https://www.w3schools.com/cssref/css_selectors.asp)). *CSS selector* untuk nama pemain adalah `top-player-stats__name`. Karena merupakan *class*, tambahkan titik sebelum huruf pertama, sehingga menjadi `.top-player-stats__name`. 

Kembali ke R. Setelah mengetahui *selector*-nya, kita bisa mengekstrak informasi nama pemain dengan fungsi `html_nodes` (jika hanya satu pemain saja bisa menggunakan `html_node`) dan `html_text` untuk mengambil teksnya. Parameter `trim = TRUE` berguna untuk menghilangkan *space* sebelum/setelah teks.


{% highlight r %}
html %>% 
    html_nodes(".top-player-stats__name") %>% 
    html_text(trim = TRUE)
{% endhighlight %}



{% highlight text %}
##  [1] "Jamie Vardy"               "Sergio Agüero"            
##  [3] "Tammy Abraham"             "Pierre-Emerick Aubameyang"
##  [5] "Raheem Sterling"           "Sadio Mané"               
##  [7] "Teemu Pukki"               "Harry Kane"               
##  [9] "Mohamed Salah"             "Marcus Rashford"          
## [11] "Callum Wilson"             "Christian Pulisic"        
## [13] "Bernardo Silva"            "Raúl Jiménez"             
## [15] "Danny Ings"                "Sébastien Haller"         
## [17] "Wesley"                    "Mason Mount"              
## [19] "Chris Wood"                "Neal Maupay"              
## [21] "Jordan Ayew"               "Ashley Barnes"            
## [23] "David Silva"               "Lys Mousset"              
## [25] "Firmino"
{% endhighlight %}

Dengan cara seperti di atas, kita bisa mengekstrak nama team dengan *selector* `.top-player-stats__team`


{% highlight r %}
html %>% 
    html_nodes(".top-player-stats__team") %>% 
    html_text(trim = TRUE)
{% endhighlight %}



{% highlight text %}
##  [1] "Leicester"      "Man City"       "Chelsea"        "Arsenal"       
##  [5] "Man City"       "Liverpool"      "Norwich"        "Tottenham"     
##  [9] "Liverpool"      "Man Utd"        "Bournemouth"    "Chelsea"       
## [13] "Man City"       "Wolves"         "Southampton"    "West Ham"      
## [17] "Aston Villa"    "Chelsea"        "Burnley"        "Brighton"      
## [21] "Crystal Palace" "Burnley"        "Man City"       "Sheff Utd"     
## [25] "Liverpool"
{% endhighlight %}

# Membuat fungsi scraping

Karena proses ekstraksi data merupakan pengulangan (dengan mengganti *CSS selector*), sangat disarankan untuk dibuat sebagai fungsi.


{% highlight r %}
scrape <- function(selector, html){
    html %>% 
        html_nodes(selector) %>% 
        html_text(trim = TRUE)
}
{% endhighlight %}

Sehingga untuk mendapatkan *total goals scored* cukup dengan perintah


{% highlight r %}
scrape(".top-player-stats__goals-scored-number", html)
{% endhighlight %}



{% highlight text %}
##  [1] "10" "9"  "9"  "8"  "7"  "6"  "6"  "6"  "5"  "5"  "5"  "4"  "4"  "4" 
## [15] "4"  "4"  "4"  "4"  "4"  "4"  "4"  "4"  "3"  "3"  "3"
{% endhighlight %}

Selanjutnya kita iterasi fungsi `scrape` untuk seluruh *selector*. Iterasi bisa menggunakan fungsi `sapply`/`lapply`.


{% highlight r %}
selector <- c(".top-player-stats__name",
              ".top-player-stats__team",
              ".top-player-stats__goals-scored-number",
              ".top-player-stats__mins-played",
              ".top-player-stats__mins-per-goal",
              ".top-player-stats__assists",
              ".shots-on-goal-total",
              ".shots-total",
              ".percentage-goals-on-target")

top_scorers <- sapply(selector, scrape, html)
str(top_scorers)
{% endhighlight %}



{% highlight text %}
##  chr [1:25, 1:9] "Jamie Vardy" "Sergio Agüero" "Tammy Abraham" ...
##  - attr(*, "dimnames")=List of 2
##   ..$ : NULL
##   ..$ : chr [1:9] ".top-player-stats__name" ".top-player-stats__team" ".top-player-stats__goals-scored-number" ".top-player-stats__mins-played" ...
{% endhighlight %}

Clean up! Proses *scraping* sudah selesai dan hasilnya disimpan dalam matriks `top-scorers`. Matriks tersebut dapat kita ubah menjadi *data frame* dan beri nama yang tepat untuk setiap kolom.


{% highlight r %}
top_scorers <- as.data.frame(top_scorers)

col_names <- c("name",
               "team",
               "total_goal",
               "mins_played",
               "mins_per_goal",
               "assists",
               "shots_on_goal",
               "shots_total",
               "percent_on_target")

names(top_scorers) <- col_names
top_scorers
{% endhighlight %}



{% highlight text %}
##                         name           team total_goal     mins_played
## 1                Jamie Vardy      Leicester         10 990 mins played
## 2              Sergio Agüero       Man City          9 664 mins played
## 3              Tammy Abraham        Chelsea          9 824 mins played
## 4  Pierre-Emerick Aubameyang        Arsenal          8 990 mins played
## 5            Raheem Sterling       Man City          7 882 mins played
## 6                 Sadio Mané      Liverpool          6 878 mins played
## 7                Teemu Pukki        Norwich          6 977 mins played
## 8                 Harry Kane      Tottenham          6 895 mins played
## 9              Mohamed Salah      Liverpool          5 857 mins played
## 10           Marcus Rashford        Man Utd          5 950 mins played
## 11             Callum Wilson    Bournemouth          5 952 mins played
## 12         Christian Pulisic        Chelsea          4 486 mins played
## 13            Bernardo Silva       Man City          4 638 mins played
## 14              Raúl Jiménez         Wolves          4 945 mins played
## 15                Danny Ings    Southampton          4 665 mins played
## 16          Sébastien Haller       West Ham          4 900 mins played
## 17                    Wesley    Aston Villa          4 930 mins played
## 18               Mason Mount        Chelsea          4 953 mins played
## 19                Chris Wood        Burnley          4 757 mins played
## 20               Neal Maupay       Brighton          4 785 mins played
## 21               Jordan Ayew Crystal Palace          4 793 mins played
## 22             Ashley Barnes        Burnley          4 803 mins played
## 23               David Silva       Man City          3 700 mins played
## 24               Lys Mousset      Sheff Utd          3 263 mins played
## 25                   Firmino      Liverpool          3 924 mins played
##        mins_per_goal   assists shots_on_goal shots_total percent_on_target
## 1   99 mins per goal 1 Assists            14          21               67%
## 2   74 mins per goal 2 Assists            10          27               37%
## 3   92 mins per goal 2 Assists            19          29               66%
## 4  124 mins per goal 0 Assists            13          21               62%
## 5  126 mins per goal 1 Assists            11          20               55%
## 6  146 mins per goal 2 Assists            13          26               50%
## 7  163 mins per goal 2 Assists            16          24               67%
## 8  149 mins per goal 1 Assists            10          21               48%
## 9  171 mins per goal 3 Assists            17          26               65%
## 10 190 mins per goal 3 Assists            12          23               52%
## 11 190 mins per goal 0 Assists            11          16               69%
## 12 122 mins per goal 2 Assists             9          10               90%
## 13 160 mins per goal 2 Assists             9          13               69%
## 14 236 mins per goal 2 Assists            12          22               55%
## 15 166 mins per goal 1 Assists             9          19               47%
## 16 225 mins per goal 1 Assists            12          18               67%
## 17 233 mins per goal 1 Assists            12          16               75%
## 18 238 mins per goal 1 Assists            13          21               62%
## 19 189 mins per goal 0 Assists             9          16               56%
## 20 196 mins per goal 0 Assists            11          25               44%
## 21 198 mins per goal 0 Assists             9          14               64%
## 22 201 mins per goal 0 Assists            10          27               37%
## 23 233 mins per goal 4 Assists             6          14               43%
## 24  88 mins per goal 3 Assists             3           6               50%
## 25 308 mins per goal 3 Assists            13          20               65%
{% endhighlight %}

# Last but not least...

Tutorial ini hanya menggunakan tiga fungsi **rvest**. Tentu saja, ada fungsi-fungsi lain yang bisa digunakan sesuai keperluan, di antaranya:

* `html_attr` dan `html_httrs` : untuk ekstrak atribut, teks dan tag
* `html_table` : untuk parsing tabel HTML menjadi data frame
* `html_form` : parsing form
* Dan lain-lain.

Sebagai catatan tambahan terkait dengan *web scraping*:

* *Scraping* dengan **rvest** hanya bisa digunakan untuk *web statis* (artinya konten/data yang akan diekstrak berada dalam file HTML). Untuk web dinamis di mana data di-*generate* oleh **JavaScript**, misalnya, tidak cukup menggunakan **rvest**.
* *Scraping* hanya efektif apabila HTML terstruktur dengan baik. Bisa dibayangkan jika nama-nama pemain pada contoh di atas berada pada class yang berbeda?
* Jika tersedia, gunakan API *(applicaton program interface)* dan hindari *scraping*.
* Seperti telah saya sampaikan sebelumnya, lakukan *scraping* dengan penuh "etika". Jangan sampai membebani website.

Selamat menambang data! Semoga bermanfaat.








