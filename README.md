# Stepwise-regression
> library(tidyverse)
> library(caret)
载入需要的程辑包：lattice

载入程辑包：‘caret’

The following object is masked from ‘package:purrr’:

    lift

Warning message:
程辑包‘caret’是用R版本4.1.3 来建造的 
> library(leaps)
Warning message:
程辑包‘leaps’是用R版本4.1.3 来建造的 
> library(leaps)
> library(dataset)
Error in library(dataset) : 不存在叫‘dataset’这个名字的程辑包
> library(datasets)
> data("swiss")
> sample_n(swiss, 3)
        Fertility Agriculture Examination Education Catholic
Boudry       70.4        38.4          26        12     5.62
Rolle        60.5        60.8          16        10     7.72
Yverdon      65.4        49.5          15         8     6.10
        Infant.Mortality
Boudry              20.3
Rolle               16.3
Yverdon             22.5
> library(MASS)

载入程辑包：‘MASS’

The following object is masked from ‘package:dplyr’:

    select

> full.model <- lm(Fertility ~., data = swiss)
> summary(full.model)

Call:
lm(formula = Fertility ~ ., data = swiss)

Residuals:
     Min       1Q   Median       3Q      Max 
-15.2743  -5.2617   0.5032   4.1198  15.3213 

Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
(Intercept)      66.91518   10.70604   6.250 1.91e-07 ***
Agriculture      -0.17211    0.07030  -2.448  0.01873 *  
Examination      -0.25801    0.25388  -1.016  0.31546    
Education        -0.87094    0.18303  -4.758 2.43e-05 ***
Catholic          0.10412    0.03526   2.953  0.00519 ** 
Infant.Mortality  1.07705    0.38172   2.822  0.00734 ** 
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 7.165 on 41 degrees of freedom
Multiple R-squared:  0.7067,    Adjusted R-squared:  0.671 
F-statistic: 19.76 on 5 and 41 DF,  p-value: 5.594e-10

> step.model <- stepAIC(full.model, direction = "both", 
+                       trace = FALSE)
> summary(step.model)

Call:
lm(formula = Fertility ~ Agriculture + Education + Catholic + 
    Infant.Mortality, data = swiss)

Residuals:
     Min       1Q   Median       3Q      Max 
-14.6765  -6.0522   0.7514   3.1664  16.1422 

Coefficients:
                 Estimate Std. Error t value Pr(>|t|)    
(Intercept)      62.10131    9.60489   6.466 8.49e-08 ***
Agriculture      -0.15462    0.06819  -2.267  0.02857 *  
Education        -0.98026    0.14814  -6.617 5.14e-08 ***
Catholic          0.12467    0.02889   4.315 9.50e-05 ***
Infant.Mortality  1.07844    0.38187   2.824  0.00722 ** 
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

Residual standard error: 7.168 on 42 degrees of freedom
Multiple R-squared:  0.6993,    Adjusted R-squared:  0.6707 
F-statistic: 24.42 on 4 and 42 DF,  p-value: 1.717e-10

> install.packages('leaps')
Warning: 正在使用‘leaps’这个程序包，因此不会被安装
> library(leaps)
> library(MASS)
> set.seed(123)
> #设置重复的k倍交叉验证
> train.control <- trainControl(method = "cv", number = 10)
> # 训练模型
> step.model <- train(Fertility ~., data = swiss,
+                     method = "leapBackward", 
+                     tuneGrid = data.frame(nvmax = 1:5),
+                     trControl = train.control
+                     )
> step.model$results
  nvmax     RMSE  Rsquared      MAE   RMSESD RsquaredSD    MAESD
1     1 9.227550 0.4366973 7.857870 1.777707  0.3218266 1.939873
2     2 8.710164 0.4781831 7.513855 1.660346  0.2653541 1.644553
3     3 8.085123 0.6207448 7.038078 2.616606  0.2398241 2.785069
4     4 7.376207 0.6699474 6.282399 2.646797  0.2724446 2.671684
5     5 7.424916 0.6922072 6.312180 2.891761  0.2722295 2.828701
> step.model$bestTune
  nvmax
4     4
> summary(step.model$finalModel)
Subset selection object
5 Variables  (and intercept)
                 Forced in Forced out
Agriculture          FALSE      FALSE
Examination          FALSE      FALSE
Education            FALSE      FALSE
Catholic             FALSE      FALSE
Infant.Mortality     FALSE      FALSE
1 subsets of each size up to 4
Selection Algorithm: backward
         Agriculture Examination Education Catholic Infant.Mortality
1  ( 1 ) " "         " "         "*"       " "      " "             
2  ( 1 ) " "         " "         "*"       "*"      " "             
3  ( 1 ) " "         " "         "*"       "*"      "*"             
4  ( 1 ) "*"         " "         "*"       "*"      "*"             
> coef(step.model$finalModel, 4)
     (Intercept)      Agriculture        Education         Catholic 
      62.1013116       -0.1546175       -0.9802638        0.1246664 
Infant.Mortality 
       1.0784422 
> lm(Fertility ~ Agriculture + Education + Catholic + Infant.Mortality, 
+    data = swiss)

Call:
lm(formula = Fertility ~ Agriculture + Education + Catholic + 
    Infant.Mortality, data = swiss)

Coefficients:
     (Intercept)       Agriculture         Education          Catholic  
         62.1013           -0.1546           -0.9803            0.1247  
Infant.Mortality  
          1.0784  
