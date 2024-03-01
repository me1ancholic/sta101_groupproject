---
title: "FLOE_notes"
author: "Christina Chen"
date: "2024-03-01"
output: 
  html_document: 
    keep_md: true
---
## Set Up




```r
library(tidyverse)
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.4     ✔ readr     2.1.5
## ✔ forcats   1.0.0     ✔ stringr   1.5.1
## ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
## ✔ purrr     1.0.2     
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

```r
library(devtools)
```

```
## Loading required package: usethis
```

```r
library(edgeR)
```

```
## Loading required package: limma
```


```r
sdiv <- read_tsv("../input/Christina_HiFi_Sdiv_Gene_Counts.tsv")
```

```
## New names:
## Rows: 40606 Columns: 219
## ── Column specification
## ──────────────────────────────────────────────────────── Delimiter: "\t" chr
## (1): ...1 dbl (218): a01, a02, a03, a04, a05, a06, a07, a08, a09, a10, a11,
## a13, a14, ...
## ℹ Use `spec()` to retrieve the full column specification for this data. ℹ
## Specify the column types or set `show_col_types = FALSE` to quiet this message.
## • `` -> `...1`
```

```r
sample <- read_csv("../input/Hydrothermal_Round_1_Sample_Descriptions.csv")
```

```
## Rows: 222 Columns: 6
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (5): sample.description, sample, population, condition, group
## dbl (1): replicate
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

## Environment Information

- sdiv = counts tsv file that was given
- sample = sample cvs file that was given
- dry_sample = sample csv with only dry condition, and without i02 and i03
- dry_sdiv = counts tsv with only dry condition
- dge_data = DGEList and calcNormFactors
- normal_dry_sdiv = cpm of dge_data

## Dry Samples


```r
dry_sample <- sample %>%
  filter(condition == "Dry") %>%
  filter(sample != "i02") %>%
  filter(sample != "i03") # had to remove these two samples because dry_sdiv did not have counts for these and it wouldn't let me move on with uneven number of sample sizes
```

- any sample from 01 to 05 was dry


```r
dry_sdiv <- sdiv %>%
  select(contains("01") | contains("02") | contains("03") | contains("04") | contains("05")) # would be curious to see how you would've done this part
```


```r
names(dry_sdiv)
```

```
##  [1] "a01" "b01" "c01" "d01" "e01" "f01" "g01" "h01" "i01" "j01" "k01" "l01"
## [13] "m01" "a02" "b02" "c02" "d02" "e02" "f02" "g02" "h02" "j02" "k02" "l02"
## [25] "m02" "a03" "b03" "c03" "d03" "e03" "f03" "g03" "j03" "k03" "l03" "m03"
## [37] "a04" "b04" "c04" "d04" "e04" "f04" "g04" "i04" "j04" "k04" "l04" "m04"
## [49] "a05" "b05" "c05" "d05" "e05" "f05" "g05" "i05" "j05" "k05" "l05" "m05"
```

```r
dry_sample$sample # is there a way to automatically check if these match? the way i made the dry_sdiv makes it harder to see if they are the same.
```

```
##  [1] "a01" "a02" "a03" "a04" "a05" "b01" "b02" "b03" "b04" "b05" "c01" "c02"
## [13] "c03" "c04" "c05" "d01" "d02" "d03" "d04" "d05" "e01" "e02" "e03" "e04"
## [25] "e05" "f01" "f02" "f03" "f04" "f05" "g01" "g02" "g03" "g04" "g05" "h01"
## [37] "h02" "i01" "i04" "i05" "j01" "j02" "j03" "j04" "j05" "k01" "k02" "k03"
## [49] "k04" "k05" "l01" "l02" "l03" "l04" "l05" "m01" "m02" "m03" "m04" "m05"
```

- at this point, there are 60 samples

## Normalizing and Plotting


```r
dge_data <- DGEList(counts = dry_sdiv, # lab says this needs to be a matrix, but this worked. also i'm not sure if that changes anything or what the difference would be
        group = dry_sample$group)

dge_data <- calcNormFactors(dge_data, method = "TMM")
dge_data$samples
```

```
##           group lib.size norm.factors
## a01   CAAN1_Dry   414337    1.6576297
## b01   CAAN1_Dry  3678831    1.0232881
## c01   CAAN1_Dry  2148405    1.0176648
## d01   CAAN1_Dry  2894076    1.0318467
## e01   CAAN1_Dry  8511382    0.9508347
## f01   CAAN2_Dry  3717251    1.1065346
## g01   CAAN2_Dry  1043110    0.9477438
## h01   CAAN2_Dry  1840824    0.9164089
## i01   CAAN2_Dry  3264302    0.9932367
## j01   CAAN2_Dry  3738417    0.9742469
## k01   CACO1_Dry   547990    1.1331898
## l01   CACO1_Dry  2458703    1.0585747
## m01   CACO1_Dry  4125635    0.9607162
## a02   CACO1_Dry  3010342    1.0782837
## b02   CACO1_Dry   888854    0.8706118
## c02   CAIN2_Dry  1491540    0.8561183
## d02   CAIN2_Dry  2610341    1.0158520
## e02   CAIN2_Dry  3254810    1.0625630
## f02   CAIN2_Dry  1542299    1.0424603
## g02   CAIN2_Dry  2996736    1.0518349
## h02   STBR3_Dry  3992350    0.8861696
## j02   STBR3_Dry  4133133    0.9622010
## k02   STBR3_Dry  3384724    0.8599002
## l02   STBR3_Dry  4110017    0.9374288
## m02   STBR3_Dry  4946171    0.8860812
## a03    STDI_Dry  3093716    1.0963170
## b03    STDI_Dry  3241674    1.0213877
## c03    STDI_Dry  3133741    0.8993295
## d03    STDI_Dry  2129184    1.0167198
## e03    STDI_Dry  1093086    1.0783989
## f03   STDR2_Dry  3538647    1.0790704
## g03   STDR2_Dry  2450815    1.0772416
## j03   STDR2_Dry   241077    1.2753674
## k03   STDR2_Dry   145749    1.0632025
## l03   STDR2_Dry  3714969    1.0552601
## m03   STGL1_Dry  5128236    1.0306867
## a04   STGL1_Dry  3582953    1.0148673
## b04   STGL2_Dry  1463895    0.8605475
## c04   STGL2_Dry  3055852    0.9570860
## d04   STGL2_Dry  1410603    1.0028832
## e04   STGL3_Dry  4492498    1.0815980
## f04   STGL3_Dry  6241727    0.9488698
## g04   STGL3_Dry  2573660    1.0222853
## i04   STGL3_Dry 13255244    0.9292557
## j04   STGL3_Dry  5072899    0.9123171
## k04    STIN_Dry  3704704    0.9888395
## l04    STIN_Dry  1932311    1.0776873
## m04    STIN_Dry  4092463    1.0521879
## a05    STIN_Dry  4333614    1.0305187
## b05    STIN_Dry  3031351    1.0154895
## c05   STPO1_Dry  1457501    0.9392542
## d05   STPO1_Dry  4239714    0.8587838
## e05   STPO1_Dry  2440839    1.0577307
## f05   STPO1_Dry  5551013    0.9330058
## g05   STPO1_Dry  3477269    0.9091938
## i05 STTO-BH_Dry  4051667    0.9821192
## j05 STTO-BH_Dry  4006397    0.9407703
## k05 STTO-BH_Dry  3862125    0.9514423
## l05 STTO-BH_Dry  3585541    0.9567871
## m05 STTO-BH_Dry  5284882    0.9433388
```


```r
plotMDS(dge_data, method = "bcv") # I'm not sure if this was done correctly, most of then are clustered together by condition. (I assume, since it's hard to see with all the over lapping.) But there are a few that seem really spread out, like the j, b, g, m, e, h. Are those the ones that need to be removed? or do I need to plot CPM data to figure out which ones to remove?)
```

```
## Note: the bcv method is now scheduled to be removed in a future release of edgeR.
```

![](FLOE_notes_files/figure-html/plotting dge_data-1.png)<!-- -->


```r
normal_dry_sdiv <- cpm(dge_data, log = T)
head(normal_dry_sdiv)
```

```
##          a01        b01        c01        d01        e01        f01        g01
## 1 -0.7030824  2.8052083  4.5813942  4.0893979  4.7905074  3.2644660  6.7253770
## 2 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824
## 3 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824
## 4 -0.7030824  1.4538440  1.5366327  1.1944660  0.4389810 -0.7030824 -0.7030824
## 5 -0.7030824  1.8222352 -0.7030824 -0.7030824  0.6805727  1.9163847  5.4295651
## 6 -0.7030824  2.3589926 -0.7030824 -0.7030824  3.4091190 -0.7030824 -0.7030824
##          h01        i01        j01        k01        l01        m01        a02
## 1  3.7714155  4.0307050  3.7272862  4.0633220 -0.7030824  3.1189891  3.9735864
## 2 -0.7030824 -0.7030824  1.3426699 -0.7030824 -0.7030824 -0.7030824 -0.7030824
## 3 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824
## 4  1.5779253 -0.1160849  0.2183289 -0.7030824 -0.7030824 -0.7030824 -0.7030824
## 5 -0.7030824  1.8869623 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824
## 6 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824  1.7610872 -0.7030824
##          b02        c02        d02        e02        f02        g02        h02
## 1  4.2254039 -0.7030824  3.9015487  5.2446687  3.6359564  4.0423662  4.4840454
## 2 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824
## 3 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824
## 4  0.9309283 -0.7030824 -0.7030824  2.0299920  1.6332682 -0.7030824  0.5481587
## 5  1.6775065 -0.7030824 -0.7030824  3.9137629  0.3059485  4.9721244 -0.7030824
## 6 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.1023573 -0.7030824
##          j02        k02        l02        m02        a03        b03        c03
## 1  2.7867107  3.5371500  3.4589299  3.0257089  3.3289890  3.5593812  3.8882432
## 2 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824
## 3 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824
## 4 -0.7030824 -0.7030824 -0.1946146 -0.7030824 -0.7030824  2.6673673 -0.7030824
## 5 -0.7030824 -0.7030824 -0.7030824  0.8115564 -0.1374954 -0.7030824 -0.7030824
## 6 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824  4.4991173
##          d03        e03        f03        g03        j03        k03        l03
## 1  4.3881614  3.6428058  4.4306296  3.9786594  1.9510996 -0.7030824  2.3801162
## 2 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824
## 3 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824
## 4 -0.7030824  0.5485255 -0.7030824  2.5648760 -0.7030824  2.8211999 -0.7030824
## 5 -0.7030824  3.1854821  1.6929268  3.2786901  4.5472826 -0.7030824 -0.7030824
## 6 -0.7030824  4.2688503 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824
##          m03        a04        b04        c04        d04        e04         f04
## 1  3.5578558  3.4315752  3.0963165  4.5348427  4.1926835  4.5646144  2.74258333
## 2 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.2862012 -0.70308242
## 3 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.70308242
## 4  0.2410311 -0.7030824 -0.7030824 -0.7030824  1.0200675  0.3006027  1.20333144
## 5 -0.7030824  0.2194370  4.5135439 -0.7030824  0.4017791  3.2423990 -0.07104486
## 6 -0.7030824  1.4927974 -0.7030824  0.9868931 -0.7030824  2.0401423  0.70250517
##          g04        i04         j04        k04        l04        m04
## 1  2.2615758  4.6973302  2.78247803  4.1098309  3.7114590  2.7338492
## 2 -0.7030824 -0.7030824 -0.70308242 -0.7030824 -0.7030824 -0.7030824
## 3 -0.7030824 -0.7030824 -0.70308242 -0.7030824 -0.7030824 -0.7030824
## 4 -0.7030824 -0.7030824  0.06543631 -0.7030824 -0.7030824 -0.7030824
## 5  3.5405987  1.7195637  2.87035963 -0.7030824 -0.7030824  0.1093227
## 6 -0.7030824 -0.7030824 -0.70308242 -0.7030824  1.0390564  1.5541344
##           a05        b05        c05        d05        e05        f05        g05
## 1  3.81244418  3.5830542  3.8573193  1.4913174  4.2041099  3.0258218  5.4473017
## 2 -0.70308242 -0.7030824 -0.7030824 -0.7030824  1.3510214  0.2552056 -0.7030824
## 3 -0.70308242 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.7030824
## 4  0.08691812  0.9363445 -0.7030824 -0.7030824  0.0022950  1.4532098  3.2908778
## 5  0.96921860 -0.7030824  0.4273240 -0.1698939  2.6835310  1.3477128  4.2922296
## 6  0.59449067 -0.7030824 -0.7030824 -0.7030824  1.5549622  0.6596240 -0.7030824
##          i05        j05        k05        l05         m05
## 1  3.9250463  4.3769989  3.8299718  3.8969944  2.63937410
## 2 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.70308242
## 3 -0.7030824 -0.7030824 -0.7030824 -0.7030824 -0.70308242
## 4  0.4522518 -0.7030824  2.6620177 -0.7030824  0.50242712
## 5 -0.7030824  1.4524788 -0.7030824 -0.7030824  0.02208593
## 6  1.0854856  1.3053657 -0.7030824 -0.7030824  0.50242712
```
