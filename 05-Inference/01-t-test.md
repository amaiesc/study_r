# การทดสอบ *t*-Test

การทดสอบนี้ แมวอาจผ่านตามาแล้วในเรื่องจำนวนการกินโดนัทระหว่างผู้หญิงกับผู้ชาย เพราะต้องใส่สถิติเปรียบเทียบคู่ แต่ทว่า t-test มีหลายแบบด้วยกัน แมวเลยจะแยกหัวข้อไว้ในสารบัญค่ะ

**ว่าแต่ว่า t-test คืออะไรก่อน?**

t-test เป็น test statistics ที่ถูกจัดอยู่ในกลุ่ม **Inferential statistical analysis** นั่นคือ เป็นการทดสอบโดยการนำผลวิเคราะห์จากกลุ่มตัวอย่างไปอธิบายประชากร ดังนั้นจะมีการควบคุมตัวแปร และความคลาดเคลื่อนที่อาจะเกิดขึ้น อาจจะเป็นในขั้นตอนวิจัย หรือในการวิเคราะห์ทางสถิติก็ได้

ในที่สุด บทเรียนนี้ เราก็จะได้ใช้ `package : psych` ในการวิเคราะห์กันเสียที สามารถดูวิธีการ install.package และ เรียก package ออกมาใช้ได้ที่ [install & call](https://amaiesc.github.io/study_r/install_pac_.html)


``` r
# ถ้า package ไหนยังไม่ได้ลงไว้ให้ใช้คำสั่ง
#install.package("ชื่อ package")
library(psych)
library(emmeans)
library(car)
```

    ## Loading required package: carData

    ## 
    ## Attaching package: 'car'

    ## The following object is masked from 'package:psych':
    ## 
    ##     logit


``` r
library(ggplot2)
```


    ## 
    ## Attaching package: 'ggplot2'

    ## The following objects are masked from 'package:psych':
    ## 
    ##     %+%, alpha


เริ่มสร้างข้อมูลโดนัท

``` r
set.seed(1990) #เซ็ทไว้สำหรับการทำซ้ำ
male_donut <- rnorm(40, mean = 6, sd = 1)
female_donut <- rnorm(70, mean = 8, sd = 1)

Gender <- c(rep("male", 40), c(rep("female", 70))) #แปลงไฟล์ ใส่เพศลงไป
male_donut <- as.data.frame(male_donut)
female_donut <- as.data.frame(female_donut)
colnames(female_donut) <- "donut" #ให้คอลลัมภ์จำนวนชิ้นชื่อว่า "donut"
colnames(male_donut) <- "donut"   #ให้คอลลัมภ์จำนวนชิ้นชื่อว่า "donut"
donut <- rbind(male_donut, female_donut) #rbind() ใช้รวบข้อมูลแถวเข้าด้วยกัน
data_donut <- cbind(Gender, donut) #cbind ใช้รวบคอลลัมภ์เข้าด้วยกัน
```

![donut](https://github.com/amaiesc/study_r/blob/master/docs/Male.png?raw=true)

> ผู้ชายกินโดนัทเฉลี่ย 6 ครั้งต่อสัปดาห์ ในกลุ่มตัวอย่างชาย 40 คน 
> ในขณะที่ผู้หญิงกินโดนัทเฉลี่ย 8 ครั้งต่อสัปดาห์ ในกลุ่มตัวอย่างผู้หญิง 70 คน
> 

ต่อไปนี้ เราจะใช้คำสั่ง `describe(`) จาก `psych` ในการหาสถิติเชิงพรรณา (descriptive statistics)

``` r
describe(data_donut)
```

    ##         vars   n mean   sd median trimmed  mad  min  max range  skew kurtosis
    ## Gender*    1 110 1.36 0.48   1.00    1.33 0.00 1.00  2.0  1.00  0.56    -1.70
    ## donut      2 110 7.00 1.38   7.07    7.01 1.61 3.67 10.4  6.72 -0.06    -0.66
    ##           se
    ## Gender* 0.05
    ## donut   0.13

Output นี้จะแสดงผลให้เห็นว่ามีตัวแปรอะไรบ้าง และค่าสถิติพรรณาแต่ละตัวเป็นอย่างไร ถ้าเปรียบเทียบกับ `summary()` จาก base R ก็ใช้อธิบายได้เหมือนกัน

``` r
summary(data_donut)
```
    ##     Gender              donut       
    ##  Length:110         Min.   : 3.674  
    ##  Class :character   1st Qu.: 5.898  
    ##  Mode  :character   Median : 7.073  
    ##                     Mean   : 7.002  
    ##                     3rd Qu.: 8.030  
    ##                     Max.   :10.398


ถ้าเราอยากทราบเจาะจงลงไปโดยเฉพาะในเรื่องตัวแปรจัดกลุ่มหรือ `factor` (ดู[ชนิดของข้อมูลใน R](https://amaiesc.github.io/study_r/type_of_.html) เราอาจจะเรียกคำสั่งอื่น ๆ ได้ ดังนี้

``` r
describe.by(donut, Gender, data = data_donut)
```

    ## 
    ##  Descriptive statistics by group 
    ## group: female
    ##    vars  n mean   sd median trimmed  mad  min  max range  skew kurtosis   se
    ## X1    1 70 7.75 1.03   7.72    7.76 0.91 4.72 10.4  5.67 -0.17      0.4 0.12
    ## ------------------------------------------------------------ 
    ## group: male
    ##    vars  n mean   sd median trimmed  mad  min  max range skew kurtosis   se
    ## X1    1 40  5.7 0.85    5.6    5.68 0.76 3.67 7.76  4.09  0.2     0.15 0.13



เห็น Output แบบนี้เราก็จะพอทราบว่ากลุ่ม male มีจำนวนเพียง 40 คน ในขณะที่ female มี 70 คน และมีค่าเฉลี่ยต่างกันออกไป

ก่อนที่เราจะวิเคราะห์สถิติ t-test เราควรมีความเข้าใจพื้นฐานในเรื่องสถิติที่ใช้เปรียบเทียบค่ากลางเสียก่อน

ไปอ่าน [concept](http://amaiesc.github.io/study_rp/CLT.html) กัน <3

__________
