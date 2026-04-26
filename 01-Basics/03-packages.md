## การ Install Package ใน R

### อะไรคือ package ใน R?

`package` คือชุดคำสั่งที่เป็น `functions` ในการวิเคราะห์หรือคำนวณสิ่งต่าง ๆ ซึ่ง `function` ใน `package` นั้นจะมากหรือน้อยหรือใช้ทำอะไรขึ้นอยู่กับผู้พัฒนา `package` นั้น ๆ

เนื่องจาก R มีผู้พัฒนาใจดีเยอะมาก และที่สำคัญมาจากหลากหลายสาขาทั่วทุกมุมโลก ดังนั้น การใช้ `package` ที่เหมาะสมกับการใช้งานของเราก็ถือว่าเป็น **booster** ให้กับชีวิต

เราสามารถเข้าไปดู `package` ทั้งหมดในโลกของ R program ได้ที่ 

>
> The Comprehensive R Archive Network
> `(CRAN; http://cran.r-project.org)`
> 

โดยปกติแล้วนักวิจัยทางจิตวิทยาจะสามารถเลือกใช้ได้หลากหลายขึ้นอยู่กับจุดประสงค์ของการใช้งานแต่แมวนับถือคุณ `Revelle, W (2021)` เป็นพิเศษเพราะศาสตร์ที่ใช้ในการวิเคราะห์ทางสถิติเหมือนกัน

โดยปกติแล้วเราสามารถดู `citation` ของ `package` นั้น ๆ ได้ที่

``` r
install.packages("psych") #มีเครื่องหมาย "___" ด้วยนะ
citation("psych")
```
    
    ##   Revelle, W. (2021) psych: Procedures for Personality and
    ##   Psychological Research, Northwestern University, Evanston, Illinois,
    ##   USA, https://CRAN.R-project.org/package=psych Version = 2.1.6,.


## วิธีการ install package

เราสามารถ `install package` โดยการใช้คำสั่ง `install.package("___")` \*อย่าลืม quotation และเรียกใช้งานได้โดยใช้คำสั่ง `library()`
ถ้าเรามีความสงสัยว่า package นี้ใช้งานและมี `function` อย่างไรบ้าง เราสามารถเขียน `library(help=____)`  หรือ `??___` เพื่อดูการใช้งานหรือจะไปที่ `Cran` ของ Packages นั้น ๆ ก็ได้ค่า

**Tip :** หากว่าเราลง package ไว้แล้ว แล้วเผลอจะใช้คำสั่งจาก package นั้นครั้งเดียว เราสามารถที่จะ skip การเรียกใช้ แล้วใช้คำสั่ง `package::______` แทน เช่น 

``` r
psych::describe()
```


สามารถอ่าน manual การใช้ package ได้ที่
[http://cran.r-project.org/doc/manuals/R-admin.html](http://cran.r-project.org/doc/manuals/R-admin.html)

_________________________________

_________________________________
