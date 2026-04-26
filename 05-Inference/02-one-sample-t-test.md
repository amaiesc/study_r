# การเปรียบเทียบค่าเฉลี่ยกลุ่มเมื่อรู้ค่าเฉลี่ยของกลุ่มประชากร

กลับมาที่ t-test ของเรา หลังจากที่ไปมึนอยู่กับ [Central Limit Teorem](https://amaiesc.github.io/study_r/CLT.html)

ในบทนี้ เราจะสร้างชุดข้อมูลง่าย ๆ ในการเปรียบกลุ่มประชากร

สมมติว่า

> แมวทั้งโลกมี 6,000,000 ตัว และนอนวันละ 21 ชั่วโมง
> เราไปเก็บข้อมูลชั่วโมงที่แมวนอนแถวซอยบ้านเรามา 30 ตัว

``` r
set.seed(1990)
cat_alley <- rnorm(n = 30, mean = 16.78, sd = 3.4)
cat_alley <- as.data.frame(cat_alley)
summary(cat_alley)
```

    ##    cat_alley    
    ##  Min.   :10.91  
    ##  1st Qu.:14.14  
    ##  Median :15.31  
    ##  Mean   :15.97  
    ##  3rd Qu.:17.54  
    ##  Max.   :22.77


เราลองมา plot graph เล่น ๆ ดู

``` r
library(ggplot2)
ggplot(cat_alley, aes(y = cat_alley)) +
      geom_boxplot()
```

![](docs/one_t_files/figure-markdown_strict/unnamed-chunk-2-1.png)

``` r
ggplot(cat_alley, aes(x = cat_alley)) +
      geom_density()
```

![](docs/one_t_files/figure-markdown_strict/unnamed-chunk-2-2.png)

จะพบว่าข้อมูล cat\_alley ที่เก็บมามีความเป็น Normal Distribution

จากโจทย์
> แมวทั่วโลกมีค่าเฉลี่ยนอนวันละ 21 ชั่ว (μ)

## การวิเคราะห์ผลสถิติ t แบบ one-sample

สมมติฐานว่างของเราในที่นี้คือ

    Ho : μ = 21 
    or
    Ho : μ - 21 = 0

เมื่อทดสอบด้วยสถิติ t จะพบว่า

``` r
t.test(cat_alley, mu = 21)
```

    ## 
    ##  One Sample t-test
    ## 
    ## data:  cat_alley
    ## t = -9.7915, df = 29, p-value = 1.062e-10
    ## alternative hypothesis: true mean is not equal to 21
    ## 95 percent confidence interval:
    ##  14.92281 17.02292
    ## sample estimates:
    ## mean of x 
    ##  15.97286

ผลจากการวิเคราะห์ t.test() พบว่า
แมวในซอยบ้านเรามีชั่วโมงนอนเฉลี่ยแตกต่างจากแมวทั่วโลกอย่างมีนัยสำคัญทางสถิติค่ะ

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

tbc
