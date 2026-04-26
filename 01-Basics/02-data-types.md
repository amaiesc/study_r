
# ชนิดของข้อมูลที่เราคีย์ใน R

ข้อมูลที่เราได้จาก database หรือการเก็บมาเองจะมีลำดับของมาตรส่วน หรือที่เรียกว่า Level of Measurements

<img src="https://raw.githubusercontent.com/amaiesc/study_r/master/docs/levels.png" alt="levels">

ใน R เราจะรู้จัก `level` เหล่านี้ได้ในอีกรูปแบบต่างออกไปคือ `Qualitative = Factor` ซึ่งส่วนใหญ่นักวิจัยหรือนักวิเคราะห์จะ `key` (ใส่ข้อมูลในโปรแกรม) ข้อมูลเหล่านี้เป็นตัวอักษร หรือ `charactor` และ `Quantitative = Numeric` ซึ่งเป็นเป็นตัวเลขที่มีทศนิยมหรือไม่มีก็ได้

หากย้อนกลับไปตอนเราสร้าง `vector`

``` r
vector1 <- c(1, 2, 3, 4, 5, 6)

vector2 <- c(0, 1, 2, 3, 4, 5)

vector3 <- c("a", "b", "c", "d", "e")

vector4 <- c(1.3, 1.2, 1.4, 1.5, 1.1)
```

แล้วเราเรียกชนิดและ class ของ vector นั้นๆ ขึ้นมา จะพบผลดังนี้


``` r
typeof(vector1)
```

    ## [1] "double"

``` r
typeof(vector3)
```

    ## [1] "character"

``` r
typeof(vector4)
```

    ## [1] "double"

``` r
class(vector1)
```

    ## [1] "numeric"

``` r
class(vector3)
```

    ## [1] "character"

``` r
class(vector4)
```

    ## [1] "numeric"

จะเห็นได้ว่า เมื่อใช้คำสั่ง `typeof()` และ `class()`ข้อมูลที่เป็นตัวเลขจะแสดงผลเป็น `double (เลขที่ไม่มีจุดทศนิยม)`  และ `numeric` ในขณะที่ ข้อมูลที่เป็นตัวอักษรจะแสดงผลออกมาเป็น
`character`

เพราะฉะนั้น การคีย์ข้อมูลใน R จะมี 2 ลักษณะคือเมื่อไหร่ก็ตามที่เราคีย์เป็นตัวเลข R จะจัดให้เป็น numeric และถ้าเราคีย์เป็นตัวอักษร R โดยการใส่เครื่องหมายคำพูด (quotation)
`"____"` จะจัดให้เป็น character หากต้องการเปลี่ยนลักษณะของข้อมูลจะสามารถทำได้หลาย (มากๆ) วิธีด้วยกัน เราชอบใช้ `as.________` ที่สุด (ง่ายดี)

## การเปลี่ยนชนิดข้อมูลใน R

`Demo` นี้เราจะใช้ `dataset` ชื่อว่า `coronavirus` ที่มาจาก `Package` ชื่อว่า `coronavirus`

เนื่องจากว่า R มีนักพัฒนาใจดีทั่วโลกร่วมกันเขียน package ออกมาหลากหลาย การเรียก package ออกมาเราจะต้องทำการติดตั้ง package นั้นเสียก่อน โดยใช้ คำสั่ง `install.packages("_____")` ต้องมีเครื่องหมาย Quatation ด้วยนะ และเรียก package นั้นออกมาด้วยคำสั่ง `library()`

เนื่องจากว่า เรามีตัวแปรที่เป็น**กลุ่ม** (เช่น เพศ ลำดับในองค์กร พื้นที่) และ**ข้อมูลต่อเนื่อง** (เช่น มาตรวัด น้ำหนัก อายุ ส่วนสูง) เราจึงต้องจัดชนิดของข้อมูลใน R ให้เหมาะสมด้วยการเขียนคำสั่ง เช่น


``` r
#install.packages("coronavirus")
library(coronavirus)
head(coronavirus)
```

    ##         date province country     lat      long      type cases   uid iso2 iso3
    ## 1 2020-01-22  Alberta  Canada 53.9333 -116.5765 confirmed     0 12401   CA  CAN
    ## 2 2020-01-23  Alberta  Canada 53.9333 -116.5765 confirmed     0 12401   CA  CAN
    ## 3 2020-01-24  Alberta  Canada 53.9333 -116.5765 confirmed     0 12401   CA  CAN
    ## 4 2020-01-25  Alberta  Canada 53.9333 -116.5765 confirmed     0 12401   CA  CAN
    ## 5 2020-01-26  Alberta  Canada 53.9333 -116.5765 confirmed     0 12401   CA  CAN
    ## 6 2020-01-27  Alberta  Canada 53.9333 -116.5765 confirmed     0 12401   CA  CAN
    ##   code3    combined_key population continent_name continent_code
    ## 1   124 Alberta, Canada    4413146  North America             NA
    ## 2   124 Alberta, Canada    4413146  North America             NA
    ## 3   124 Alberta, Canada    4413146  North America             NA
    ## 4   124 Alberta, Canada    4413146  North America             NA
    ## 5   124 Alberta, Canada    4413146  North America             NA
    ## 6   124 Alberta, Canada    4413146  North America             NA


``` r
typeof(coronavirus)
```

    ## [1] "list"

``` r
class(coronavirus)
```

    ## [1] "data.frame"

`dataset` นี้มีลักษณะเป็น `list` ซ้อนกันอยู่ โดยมีข้อมูลเป็น `data.frame` ใน `dataset` นี้ มีตัวแปรแบบกลุ่มที่เห็นได้ชัดคือ `country` และ `province` ในขณะที่ตัวแปรแบบต่อเนื่องคือ จำนวน `case` `population` (ที่เหลือเดี๋ยวสนุกแล้วกลับมาเล่นได้นะ)

เราจะเรียกใช้เพียง country, province, case, population เท่านั้น วิธีการด้านล่างจะเรียกว่าวิธีการ `subset` ซึ่งจะมีการพูดถึงในส่วนถัดไป


- subset ให้เหลือแค่ country, province, case, population
- 
``` r
Data <- coronavirus[c("date", "province", "country", "cases", "population")]
head(Data, 3)
```

    ##         date province country cases population
    ## 1 2020-01-22  Alberta  Canada     0    4413146
    ## 2 2020-01-23  Alberta  Canada     0    4413146
    ## 3 2020-01-24  Alberta  Canada     0    4413146

การที่เราจะดูลักษณะ (structure) ของข้อมูลในแต่ละ column เราสามารถเรียกคำสั่ง `str()` ออกมาให้เราดูโดยรวมได้

``` r
str(Data)
```

    ## 'data.frame':    518682 obs. of  5 variables:
    ##  $ date      : Date, format: "2020-01-22" "2020-01-23" ...
    ##  $ province  : chr  "Alberta" "Alberta" "Alberta" "Alberta" ...
    ##  $ country   : chr  "Canada" "Canada" "Canada" "Canada" ...
    ##  $ cases     : int  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ population: num  4413146 4413146 4413146 4413146 4413146 ...

จะเห็นได้ว่า ข้อมูลที่เรา `subset` มาแล้วมีจำนวน `observation = 518682` และมีตัวแปร `5` ตัว คือ date `(Date)`, province `(chr)`, country `(chr)`, cases `(int)`, population `(num)`

ถ้าเราอยากเปลี่ยนชนิดของตัวแปร เช่น เปลี่ยน province ให้เป็น factor เราสามารถทำได้โดยการ เรียกชื่อ `file` และ ใช้ `$` (เรียกว่า dolla sign) และชื่อ `column` ที่มีตัวแปรนั้นอยู่ เช่น `as.factor(data$columnx)`เมื่อแปลงเป็น `factor` เรียบร้อยแล้ว เราสามารถเรียกดูชนิดได้โดยใช้คำสั่ง `is.factor()`

- ลองเปลี่ยนคอลลัมภ์ province ให้เป็น `factor`
- 
``` r
Data$province <- as.factor(Data$province)
is.factor(Data$province)
```

    ## [1] TRUE

ถ้าเราต้องการเปลี่ยนเป็นชนิดอื่น ๆ ก็ใช้คำสั่ง `as.____()` R จะมี `dropdown` ลงมาให้ว่ามีตัวเลือกอะไรให้บ้าง เช่น `as.numeric()`

_____________________________

