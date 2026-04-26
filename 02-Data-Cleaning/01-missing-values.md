# การจัดการข้อมูลที่ศูนย์หาย (missing value analysis)

เราจะใช้ข้อมูล `coronavirus` จาก package `coronavirus`

    library(coronavirus)
    data("coronavirus")
    head(coronavirus)

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

จากการดูข้อมูลคร่าวๆ ค่าสูญหาย `(NA)` ดันไปอยู่ในจังหวัด `(province)`
ไม่เป็นไรนะ สมมติว่าเราสร้าง `missing value` เอง

เราจะใช้การ `subset` ข้อมูลที่ต้องการเพียง `date` `country` และจำนวน
`case`

    coronavirus <- coronavirus[, c("date", "country", "cases")]
    head(coronavirus[coronavirus$cases == 0,]) #เดี๋ยวเราจะให้จำนวนเคสที่เป็น 0 เป็นข้อมูลสูญหาย

    ##         date country cases
    ## 1 2020-01-22  Canada     0
    ## 2 2020-01-23  Canada     0
    ## 3 2020-01-24  Canada     0
    ## 4 2020-01-25  Canada     0
    ## 5 2020-01-26  Canada     0
    ## 6 2020-01-27  Canada     0

    coronavirus$cases[coronavirus$cases == 0] <- NA #ใส่ข้อมูลสูญหายด้วย NA

    #which(!complete.cases(coronavirus$case), arr.ind = TRUE) #เรียกดูสักหน่อย

จะดูข้อมูลทั่วโลกก็อาจจะเยอะไป เราจะ `subset` ให้เหลือแค่ `UK` และ
`Thailand` เพื่อความสะดวก ทีนี้ เรามาดู `pattern`
ของข้อมูลสูญหายที่เราใส่เข้าไป

    Covid_th_UK <- coronavirus[coronavirus$country == "United Kingdom" | coronavirus$country == "Thailand", ] #subset
    head(Covid_th_UK)

    ##           date        country cases
    ## 632 2020-01-22 United Kingdom    NA
    ## 633 2020-01-23 United Kingdom    NA
    ## 634 2020-01-24 United Kingdom    NA
    ## 635 2020-01-25 United Kingdom    NA
    ## 636 2020-01-26 United Kingdom    NA
    ## 637 2020-01-27 United Kingdom    NA

เมื่อได้ data ที่ทำการ subset ให้เหลือแค่ประเทศ `UK` และ Thailand `แล้ว`
เราจะใช้คำสั่ง `md.pattern()`จาก package `mice` ในการดู pattern
ของค่าสูญหาย

    library(mice)

    ## 
    ## Attaching package: 'mice'

    ## The following object is masked from 'package:stats':
    ## 
    ##     filter

    ## The following objects are masked from 'package:base':
    ## 
    ##     cbind, rbind

    md.pattern(Covid_th_UK)

![](missing_files/figure-markdown_strict/unnamed-chunk-4-1.png)

    ##       date country cases      
    ## 5652     1       1     1     0
    ## 18957    1       1     0     1
    ##          0       0 18957 18957

เนื่องจากสูญหาย ที่เราใส่ไปมีโอกาสที่จะเป็นแบบ intentionally missed ถ้า
dataset ของเรามีขนาดใหญ่พอ เราสามารถที่จะเรียกคำสั่ง `na.omit()` สำหรับ
dataset นั้นได้เลย

    Covid_th_UK_omit <- na.omit(Covid_th_UK)
    md.pattern(Covid_th_UK_omit)

    ##  /\     /\
    ## {  `---'  }
    ## {  O   O  }
    ## ==>  V <==  No need for mice. This data set is completely observed.
    ##  \  \|/  /
    ##   `-----'

![](missing_files/figure-markdown_strict/unnamed-chunk-5-1.png)

    ##      date country cases  
    ## 5652    1       1     1 0
    ##         0       0     0 0

แมวบอกเราว่าไม่มี missing value แล้ววววว~

อย่างไรก็ตาม หาก dataset มีขนาดเล็ก
โดยเฉพาะกับจากการเก็บข้อมูลแบบทดลองหรือ survey เล็ก ๆ
เราอาจจะไม่สามารถตัดค่าสูญหายไปได้
ในกรณีนี้เราสามารถที่จะดูสัดส่วนของข้อมูลที่ศูนย์หายและแทนค่าข้อมูลที่ศูนย์หายนั้น
ๆ โดยวิธีการต่างๆ (เดี๋ยวมาต่อนะ)
