# การจัดการข้อมูลและวิเคราะห์ผล (Analysis)

ดาวน์โหลด `packages` ดังต่อไปนี้

``` r
library(mitools)
library(lme4)
library(lmerTest)
library(dplyr)
library(ggplot2)
library(sjPlot)
library(MASS)
library(rcompanion)
library(olsrr)
library(car)
library(rsq)
library(psych)
library(tidyverse)
library(MuMIn)
```

เรียกไฟล์ข้อมูลชื่อ `dat` และใส่ลงไปในชื่อ `data`

``` r
data <- read.table("dat.csv", sep=",", header=TRUE, na.strings="NA")
data[,"condition"] <- factor(data[,"condition"], 
                             labels = c("control", "fatigue", "no-fatigue"))
data[,"strategies"] <- factor(data[,"strategies"], 
labels = c("control", "Reappriasal", "Distraction", "Labeling", "Reappriasal.", "Distraction.", "Labeling."))
describe(data)
```

    ##             vars   n  mean    sd median trimmed   mad  min   max range  skew
    ## id             1 329 24.17 13.83  24.00   24.09 17.79 1.00 48.00 47.00  0.04
    ## block          2 329 22.55 13.12  23.00   22.54 16.31 1.00 45.00 44.00  0.00
    ## condition*     3 329  2.29  0.70   2.00    2.35  1.48 1.00  3.00  2.00 -0.46
    ## strategies*    4 329  4.00  2.00   4.00    4.00  2.97 1.00  7.00  6.00  0.00
    ## phasic         5 228  1.74  0.85   1.45    1.58  0.36 0.38  5.48  5.10  1.96
    ## Amplitude      6 223  1.77  0.81   1.48    1.61  0.32 0.70  5.44  4.74  2.21
    ## nSCR_r         7 218  1.30  1.33   1.00    1.12  1.48 0.00  5.00  5.00  0.82
    ## nSCR_z         8 227  2.04  1.10   2.00    1.95  1.48 0.00  5.00  5.00  0.53
    ## self.report    9 229  3.04  1.35   2.80    2.95  1.48 1.00  6.40  5.40  0.49
    ## order         10 235  1.96  1.40   2.00    1.95  1.48 0.00  4.00  4.00  0.05
    ##             kurtosis   se
    ## id             -1.19 0.76
    ## block          -1.18 0.72
    ## condition*     -0.91 0.04
    ## strategies*    -1.26 0.11
    ## phasic          3.99 0.06
    ## Amplitude       5.10 0.05
    ## nSCR_r         -0.45 0.09
    ## nSCR_z         -0.50 0.07
    ## self.report    -0.54 0.09
    ## order          -1.29 0.09

สร้างไฟล์ใหม่ของ `Amplitude` เพื่อใช้ในการ `transform`

``` r
data$amp <- data$Amplitude
```

### การหาค่ากลาง ค่าสุดโต่งและการแทนที่ข้อมูล

ใช้การ `aggregate()` ในการหาค่ากลางและค่า `quantile` ที่ `0.25, 0.75`
เพื่อใช้ในการแทนที่ (replace) ค่าสุดโต่ง

``` r
mean_amp <- aggregate(Amplitude ~ condition + strategies, data = data, 
                      FUN = function(x) c(M=mean(x), SD = sd(x), Q = quantile(x, probs = c(0.25, 0.75)), I = IQR(x)))
mean_amp
```

    ##    condition   strategies Amplitude.M Amplitude.SD Amplitude.Q.25%
    ## 1    control      control   1.5956578    0.5513869       1.3560000
    ## 2    fatigue  Reappriasal   1.7446667    0.8332435       1.3500000
    ## 3    fatigue  Distraction   1.9472727    1.0595585       1.3600000
    ## 4    fatigue     Labeling   2.0493103    1.0060387       1.3800000
    ## 5 no-fatigue Reappriasal.   1.5661538    0.5493420       1.2600000
    ## 6 no-fatigue Distraction.   1.6928571    0.6126134       1.3600000
    ## 7 no-fatigue    Labeling.   1.8534375    0.8535830       1.3750000
    ##   Amplitude.Q.75% Amplitude.I
    ## 1       1.7155000   0.3595000
    ## 2       1.6800000   0.3300000
    ## 3       1.8900000   0.5300000
    ## 4       2.1100000   0.7300000
    ## 5       1.6775000   0.4175000
    ## 6       1.8950000   0.5350000
    ## 7       2.0550000   0.6800000

``` r
Boxplot(Amplitude ~ strategies, data = data)
```

![](script_thesis_files/figure-markdown_github/unnamed-chunk-4-1.png)

    ##  [1] "50"  "239" "191" "128" "135" "142" "184" "198" "261" "31"  "87"  "101"
    ## [13] "108" "192" "213" "227" "151" "179" "284" "305" "117" "299" "244" "307"
    ## [25] "42"  "189"

เมื่อเรียกค่าสุดโต่งจาก `Boxplot()` ออกมาแล้ว
ให้ใช้สูตรการแทนที่ค่าสุดโต่งด้วยวิธี `inter-quantile range`
ทั้งขอบบนและล่างของข้อมูล

``` r
#g1

g1top <- 1.715 + 1.5*0.359

data[50, "Amplitude"] <- g1top
data[239, "Amplitude"] <- g1top

Boxplot(Amplitude ~ strategies, data = data)
```

![](script_thesis_files/figure-markdown_github/unnamed-chunk-5-1.png)

    ##  [1] "191" "128" "135" "142" "184" "198" "261" "31"  "87"  "101" "108" "192"
    ## [13] "213" "227" "151" "179" "284" "305" "117" "299" "244" "307" "42"  "189"

``` r
g2top <- 1.68 + 1.5*0.33

data[184, "Amplitude"] <- g2top
data[135, "Amplitude"] <- g2top
data[198, "Amplitude"] <- g2top
data[261, "Amplitude"] <- g2top
data[128, "Amplitude"] <- g2top
data[142, "Amplitude"] <- g2top


g2bot <- 1.35 - 1.5*0.33

data[191, "Amplitude"] <- g2bot
Boxplot(Amplitude ~ strategies, data = data)
```

![](script_thesis_files/figure-markdown_github/unnamed-chunk-5-2.png)

    ##  [1] "31"  "87"  "101" "108" "192" "213" "227" "151" "179" "284" "305" "117"
    ## [13] "299" "244" "307" "42"  "189"

``` r
g3top <- 1.89 + 1.5*0.53
g3top <- g3top - 0.01 

data[31, "Amplitude"] <- g3top
data[101, "Amplitude"] <- g3top
data[227, "Amplitude"] <- g3top
data[87, "Amplitude"] <- g3top
data[213, "Amplitude"] <- g3top
data[192, "Amplitude"] <- g3top
data[108, "Amplitude"] <- g3top

Boxplot(Amplitude ~ strategies, data = data)
```

![](script_thesis_files/figure-markdown_github/unnamed-chunk-5-3.png)

    ##  [1] "151" "179" "284" "305" "117" "299" "244" "307" "42"  "189"

``` r
g4top <- 2.11 + 1.5*0.73

data[284, "Amplitude"] <- g4top
data[179, "Amplitude"] <- g4top
data[305, "Amplitude"] <- g4top
data[151, "Amplitude"] <- g4top
Boxplot(Amplitude ~ strategies, data = data)
```

![](script_thesis_files/figure-markdown_github/unnamed-chunk-5-4.png)

    ## [1] "117" "299" "244" "307" "42"  "189"

``` r
g5top <- 1.678 + 1.5*0.418


data[117,"Amplitude"] <- g5top
data[299,"Amplitude"] <- g5top


Boxplot(Amplitude ~ strategies, data = data)
```

![](script_thesis_files/figure-markdown_github/unnamed-chunk-5-5.png)

    ## [1] "244" "307" "42"  "189"

``` r
g6top <- 1.895 + 1.5*0.535

data[244,"Amplitude"] <- g6top
data[307,"Amplitude"] <- g6top
Boxplot(Amplitude ~ strategies, data = data)
```

![](script_thesis_files/figure-markdown_github/unnamed-chunk-5-6.png)

    ## [1] "42"  "189"

``` r
g7top <- 2.055 + 1.5* 0.68

data[42,"Amplitude"] <- g7top
data[189,"Amplitude"] <- g7top

Boxplot(Amplitude ~ strategies, data = data)
```

![](script_thesis_files/figure-markdown_github/unnamed-chunk-5-7.png)

``` r
mean_amp_cut <- aggregate(Amplitude ~ condition + strategies, data = data, 
                          FUN = function(x) c(M=mean(x), SD = sd(x), Q = quantile(x, probs = c(0.25, 0.75)), I = IQR(x)))
mean_amp_cut
```

    ##    condition   strategies Amplitude.M Amplitude.SD Amplitude.Q.25%
    ## 1    control      control   1.5285578    0.3191266       1.3560000
    ## 2    fatigue  Reappriasal   1.5421667    0.3822995       1.3500000
    ## 3    fatigue  Distraction   1.7183333    0.5635513       1.3600000
    ## 4    fatigue     Labeling   1.9244828    0.7013919       1.3800000
    ## 5 no-fatigue Reappriasal.   1.4953846    0.3260672       1.2600000
    ## 6 no-fatigue Distraction.   1.6387500    0.4568357       1.3600000
    ## 7 no-fatigue    Labeling.   1.7693750    0.6037340       1.3750000
    ##   Amplitude.Q.75% Amplitude.I
    ## 1       1.7155000   0.3595000
    ## 2       1.6800000   0.3300000
    ## 3       1.8900000   0.5300000
    ## 4       2.1100000   0.7300000
    ## 5       1.6775000   0.4175000
    ## 6       1.8950000   0.5350000
    ## 7       2.0550000   0.6800000

จากนั้นจัดการค่าสุดโต่งในตัวแปร `self-reported negative emotions`

``` r
mean_self <- aggregate(self.report ~ condition + strategies, data = data, 
                       FUN = function(x) c(M=mean(x), SD = sd(x), Q = quantile(x, probs = c(0.25, 0.75)), I = IQR(x)))
mean_self
```

    ##    condition   strategies self.report.M self.report.SD self.report.Q.25%
    ## 1    control      control     4.1391304      1.1804854         3.4000000
    ## 2    fatigue  Reappriasal     2.6000000      1.1710801         1.6000000
    ## 3    fatigue  Distraction     2.6914286      1.3384300         1.6000000
    ## 4    fatigue     Labeling     3.1937500      1.2876429         2.1000000
    ## 5 no-fatigue Reappriasal.     2.1862069      0.8975061         1.4000000
    ## 6 no-fatigue Distraction.     2.5310345      1.1405210         1.4000000
    ## 7 no-fatigue    Labeling.     3.3448276      1.2905110         2.4000000
    ##   self.report.Q.75% self.report.I
    ## 1         4.9500000     1.5500000
    ## 2         3.4000000     1.8000000
    ## 3         3.7000000     2.1000000
    ## 4         4.2000000     2.1000000
    ## 5         2.8000000     1.4000000
    ## 6         3.2000000     1.8000000
    ## 7         4.0000000     1.6000000

``` r
Boxplot(self.report ~ strategies, data = data)
```

![](script_thesis_files/figure-markdown_github/unnamed-chunk-6-1.png)

### จัดการค่าสูญหายและแนวโน้มเข้าสู่ศูนย์กลาง

เมื่อจัดการกับค่าสุดโต่งแล้ว
ขั้นตอนต่อไปคือการจัดการกับค่าสูญหายและความเป็นโค้งปกติของข้อมูล
ซึ่งข้อมูลที่มาจาก physiological measurement จะมีแนวโน้มเบ้ไปทางขวา

``` r
hist(data$Amplitude)
```

![](script_thesis_files/figure-markdown_github/unnamed-chunk-7-1.png)

``` r
#tranform Amplitude by Turkey Method
Amp_t <-
  transformTukey(data$Amplitude,
                 plotit=FALSE)
```

    ## 
    ##     lambda      W Shapiro.p.value
    ## 366 -0.875 0.9809        0.004103
    ## 
    ## if (lambda >  0){TRANS = x ^ lambda} 
    ## if (lambda == 0){TRANS = log(x)} 
    ## if (lambda <  0){TRANS = -1 * x ^ lambda}

``` r
plotNormalHistogram(Amp_t)
```

![](script_thesis_files/figure-markdown_github/unnamed-chunk-7-2.png)

``` r
qqnorm(Amp_t,
       ylab="tukey")
qqline(Amp_t,
       col="red")
```

![](script_thesis_files/figure-markdown_github/unnamed-chunk-7-3.png)

``` r
data$Amp_t <- Amp_t
```

### การวิเคราะห์สถิติด้วยวิธีการทำ Linear-Mixed Modelling

#### ตัวแปร Amplitude

``` r
m.amp <- lmer(Amp_t ~ strategies + (1 |id),  data = data, REML=FALSE)
```

    ## boundary (singular) fit: see ?isSingular

``` r
summ.amp <- summary(m.amp)

``` r
coef(summ.amp)
```

    ##                            Estimate Std. Error  df     t value     Pr(>|t|)
    ## (Intercept)            -0.714778459 0.02244378 223 -31.8475115 6.258507e-85
    ## strategiesReappriasal  -0.003811934 0.03548673 223  -0.1074186 9.145535e-01
    ## strategiesDistraction   0.041003671 0.03450534 223   1.1883283 2.359681e-01
    ## strategiesLabeling      0.099121635 0.03585195 223   2.7647486 6.173452e-03
    ## strategiesReappriasal. -0.012964641 0.03708843 223  -0.3495602 7.269986e-01
    ## strategiesDistraction.  0.031311889 0.03623918 223   0.8640341 3.884975e-01
    ## strategiesLabeling.     0.059757124 0.03481499 223   1.7164192 8.747398e-02

``` r
confint(m.amp, level = 0.95)
```

    ## Computing profile confidence intervals ...

    ##                               2.5 %      97.5 %
    ## .sig01                  0.000000000  0.04337213
    ## .sigma                  0.137493050  0.16568921
    ## (Intercept)            -0.758957564 -0.67059935
    ## strategiesReappriasal  -0.073665233  0.06604136
    ## strategiesDistraction  -0.026917826  0.10892517
    ## strategiesLabeling      0.028549416  0.16969386
    ## strategiesReappriasal. -0.085970786  0.06004150
    ## strategiesDistraction. -0.040022563  0.10264634
    ## strategiesLabeling.    -0.008773911  0.12828816

``` r
performance::r2(m.amp)
```

    ## Warning: Can't compute random effect variances. Some variance components equal zero. Your
    ##   model may suffer from singulariy.
    ##   Solution: Respecify random structure! You may also decrease the 'tolerance'
    ##   level to enforce the calculation of random effect variances.

    ## Random effect variances not available. Returned R2 does not account for random effects.

    ## # R2 for Mixed Models
    ## 
    ##   Conditional R2: NA
    ##      Marginal R2: 0.055

``` r
psi_a <- coef(summ.amp)[,1]
vpsi_a <- vcov(summ.amp)


cmat <-  cbind(c(1,  -1/6, -1/6, -1/6, -1/6, -1/6, -1/6),
               c(0,   1/3,  1/3,  1/3, -1/3, -1/3, -1/3),
               c(0,   1/2,  -1/2,   0, 1/2,  -1/2,    0),
               c(0,   1/2,    0, -1/2,  1/2,    0, -1/2),
               c(0,     0,  1/2, -1/2,    0,  1/2, -1/2),
               c(0,   1/6,  -1/6,   0,  1/6, -1/6,    0),
               c(0,   1/6,    0, -1/6, -1/6,    0,  1/6),
               c(0,     0,  1/6, -1/6,    0, -1/6,  1/6))

est_a <- t(cmat) %*% psi_a
se_a <- sqrt(diag(t(cmat) %*% vpsi_a %*% cmat))
zval_a <- est_a/se_a
pval_a <- round(pnorm(-abs(zval_a)) * 2, 6)
Amp_out <- cbind(est_a, se_a, zval_a, pval_a)
Amp_out
```

    ##                          se_a                     
    ## [1,] -0.750514750 0.046293147 -16.2122215 0.000000
    ## [2,]  0.019403000 0.022641809   0.8569545 0.391470
    ## [3,] -0.044546068 0.027945767  -1.5940184 0.110932
    ## [4,] -0.087827667 0.027916900  -3.1460394 0.001655
    ## [5,] -0.043281600 0.027324212  -1.5840017 0.113193
    ## [6,] -0.014848689 0.009315256  -1.5940184 0.110932
    ## [7,] -0.005035301 0.009305633  -0.5411024 0.588437
    ## [8,] -0.004945455 0.009108071  -0.5429750 0.587147

``` r
performance::r2(m.amp)
```

    ## Warning: Can't compute random effect variances. Some variance components equal zero. Your
    ##   model may suffer from singulariy.
    ##   Solution: Respecify random structure! You may also decrease the 'tolerance'
    ##   level to enforce the calculation of random effect variances.

    ## Random effect variances not available. Returned R2 does not account for random effects.

    ## # R2 for Mixed Models
    ## 
    ##   Conditional R2: NA
    ##      Marginal R2: 0.055

``` r
r.squaredGLMM(m.amp)
```

    ## Warning: 'r.squaredGLMM' now calculates a revised statistic. See the help page.

    ##             R2m        R2c
    ## [1,] 0.05513824 0.05513824

#### ตัวแปร Self-reported

``` r
m.self <- lmer(self.report ~ strategies + (1 |id),  data = data, REML=FALSE)
summ.self <- summary(m.self)
tab_model(m.self)
```

``` r
coef(summ.self)
```

    ##                          Estimate Std. Error       df   t value     Pr(>|t|)
    ## (Intercept)             4.1391304  0.1740757 126.0065 23.777761 2.059150e-48
    ## strategiesReappriasal  -1.6131283  0.2111083 186.6339 -7.641235 1.093314e-12
    ## strategiesDistraction  -1.4494522  0.1981145 185.1053 -7.316235 7.498341e-12
    ## strategiesLabeling     -0.8848156  0.2040933 185.8259 -4.335349 2.381913e-05
    ## strategiesReappriasal. -2.0069251  0.2111220 186.6564 -9.505998 1.003648e-17
    ## strategiesDistraction. -1.6005855  0.2111062 186.6273 -7.581897 1.552230e-12
    ## strategiesLabeling.    -0.7246227  0.2111067 186.6306 -3.432495 7.365563e-04

``` r
confint(m.self, level = 0.95)
```

    ## Computing profile confidence intervals ...

    ##                             2.5 %     97.5 %
    ## .sig01                  0.6158197  1.0316833
    ## .sigma                  0.7908161  0.9708918
    ## (Intercept)             3.7952897  4.4829712
    ## strategiesReappriasal  -2.0287458 -1.1967626
    ## strategiesDistraction  -1.8397556 -1.0591147
    ## strategiesLabeling     -1.2872989 -0.4829809
    ## strategiesReappriasal. -2.4226165 -1.5906493
    ## strategiesDistraction. -2.0165285 -1.1847292
    ## strategiesLabeling.    -1.1409823 -0.3090074

``` r
psi_s <- coef(summ.self)[,1]
vpsi_s <- vcov(summ.self)

cmat <-  cbind(c(1,  -1/6, -1/6, -1/6, -1/6, -1/6, -1/6),
               c(0,   1/3,  1/3,  1/3, -1/3, -1/3, -1/3),
               c(0,   1/2,    0, -1/2,  1/2,    0, -1/2),
               c(0,     0,  1/2, -1/2,    0,  1/2, -1/2),
               c(0,   1/6,    0, -1/6, -1/6,    0,  1/6),
               c(0,     0,  1/6, -1/6,    0, -1/6,  1/6))


est_s <- t(cmat) %*% psi_s
se_s <- sqrt(diag(t(cmat) %*% vpsi_s %*% cmat))
zval_s <- est_s/se_s
pval_s <- round(pnorm(-abs(zval_s)) * 2, 6)
self.report_out <- cbind(est_s, se_s, zval_s, pval_s)
self.report_out
```

    ##                        se_s                    
    ## [1,]  5.51905200 0.29020738 19.0176145 0.000000
    ## [2,]  0.12824575 0.13307213  0.9637310 0.335181
    ## [3,] -1.00530758 0.16565913 -6.0685310 0.000000
    ## [4,] -0.72029970 0.16201826 -4.4457933 0.000009
    ## [5,]  0.09233163 0.05555686  1.6619304 0.096527
    ## [6,]  0.05188771 0.05385049  0.9635513 0.335271

``` r
tab_model(m.self)
```

``` r
anova(m.self)
```

    ## Type III Analysis of Variance Table with Satterthwaite's method
    ##            Sum Sq Mean Sq NumDF  DenDF F value    Pr(>F)    
    ## strategies 102.03  17.006     6 188.15  22.304 < 2.2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

``` r
r.squaredGLMM(m.self)
```

    ##            R2m       R2c
    ## [1,] 0.2470145 0.5881336

การเปรียบเทียบโดยไม่ใช่กลุ่มควบคุม next \>\> :)
