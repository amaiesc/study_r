# อะไรคือ Central limit Theorem; CLT

จากความเข้าใจเรื่อง Normal Distribution เราจะเข้าใจถึงคอนเซ็ป Central Limited Theorem (CLT) นั่นก็คือ

> ไม่ว่าข้อมูลจากกลุ่มประชากรจะเป็นเช่นใด เมื่อมีข้อมูลจากกลุ่มตัวอย่างมากพอ โค้งของข้อมูลจะเข้าสู่ศูนย์กลาง หรือที่เรียกว่า Normal Distribution 
> ซึ่ง Concept ดังกล่าวนี้ คุณ Abraham de Moivre (1733) เป็นผู้เสนอไว้ และถูกนำมาเป็นทฤษฎีหลักของนักสถิติกลุ่มความน่าจะเป็น อย่างคุณ George Polya ที่บรรญัติให้มีชื่อว่า Central Limited Theorem (CLT)

## แล้ว CLT เกี่ยวอะไรกับค่าเฉลี่ย (mean)

ก่อนอื่นเลย เราต้องเข้าใจที่มาที่ไปของโค้งปกติ (bell curve) เราจะมาสร้าง data เล่นกันค่า

``` r
bell_curve <-  c(1.1, 1.5,
                 2.1, 2.3, 2.4, 2.7, 
                 3.1, 3.3, 3.5, 3.5, 3.5, 3.9,
                 4.1, 4.3, 4.5, 4.6,
                 5.1, 5.9)

left_skewed <- c(1.1, 1.3, 1.5, 1.5, 1.5, 1.9,
                 2.1, 2.3, 2.5, 2.7, 2.9,
                 3.1, 3.5, 3.7,
                 4.1, 4.5,
                 5.1, 5.9)

right_skewed <- c(1.1, 1.9,
                  2.1, 2.5,
                  3.1, 3.3, 3.3,
                  4.1, 4.3, 4.5, 4.8, 4.9,
                  5.1, 5.3, 5.5, 5.5, 5.5, 5.9)

    curved <- data.frame(bell_curve, left_skewed, right_skewed)
```

**Note** แมวทำไว้ให้สนุก ๆ ในความเป็นจริงลูกแมวสามารถที่จะพิมพ์เรียงติดกันเลยได้นะคะ

#### คำนวณค่า Mean Median และ Mode

##### Bell Curve

``` r
# หาค่ากลาง
#ขอตัวช่วยจาก package
#install.packages("modeest")
library(modeest)

mean(bell_curve)

    ## [1] 3.411111

median(bell_curve)

    ## [1] 3.5

mlv(bell_curve, method = "mfv")

    ## [1] 3.5
```


##### Left Skewed

``` r
mean(left_skewed)

    ## [1] 2.844444

median(left_skewed)

    ## [1] 2.6

mlv(left_skewed, method = "mfv")

    ## [1] 1.5
```


##### Right Skewed

``` r
    mean(right_skewed)

    ## [1] 4.038889

    median(right_skewed)

    ## [1] 4.4

    mlv(right_skewed, method = "mfv")

    ## [1] 5.5
```

พอได้ค่าทางสถิติแล้วเราจะมา plot density graph กัน

``` r
library(ggplot2)
bell_curve <- ggplot(curved, aes(x = bell_curve)) +
                      geom_density(fill = "gray", alpha = 0.5) +
                      geom_vline(xintercept = 3.41111, color = "red") +
                      geom_vline(xintercept = 3.5, color = "blue") +
                      geom_vline(xintercept = 3.5, color = "pink") +
                      theme_classic()

left_skewed <- ggplot(curved, aes(x = left_skewed)) +
                      geom_density(fill = "gray", alpha = 0.5) +
                      geom_vline(xintercept = 2.84444, color = "red") +
                      geom_vline(xintercept = 2.6, color = "blue") +
                      geom_vline(xintercept = 1.5, color = "pink") +
                      theme_classic()

right_skewed <- ggplot(curved, aes(x = left_skewed)) +
                      geom_density(fill = "gray", alpha = 0.5) +
                      geom_vline(xintercept = 4.038889, color = "red") +
                      geom_vline(xintercept = 4.4, color = "blue") +
                      geom_vline(xintercept = 5.5, color = "pink") +
                      theme_classic()
```


โชว์กราฟใช้คำสั่ง `gridextra::grid.arrange()`

``` r
    gridExtra::grid.arrange(bell_curve, left_skewed, right_skewed)
```

![](docs/CLT_files/figure-markdown_strict/unnamed-chunk-6-1.png)

**Note that, Red = Mean, Blue = Median, Pink = mode**

ในการเปรียบเทียบข้อมูลทั้งสามข้อมูลจะพบว่า bell curve จะมีค่ากลางอยู่ใกล้กันมากที่สุด

กราฟ Bell curve ขึ้นอยู่กับปัจจัย 2 อย่าง คือ 
    1. โอกาสที่จะเกิด bell curve และ 
    2. ค่าความคลาดเคลื่อนของข้อมูล (SD) 
จากที่เราเห็นกราฟ เราพอจะอนุมานได้ว่า การใช้ค่า mean ข้อมูลจากกลุ่มตัวอย่างที่มีลักษณะเป็น bell curve จะเหมาะสมที่สุด (อิงอยู่บน CLT)

ดังนั้น สถิติอ้างอิง หรือ referential statistics จึงใช้ค่า Mean ในการวิเคราะห์ผล


_____________________
อ่านเพิ่มเติมได้ใน Howell, D. C. (2012). Statistical methods for psychology. Cengage Learning.
