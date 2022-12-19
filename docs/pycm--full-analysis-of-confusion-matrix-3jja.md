# PyCM:混淆矩阵的全面分析

> 原文：<https://dev.to/sepandhaghighi/pycm--full-analysis-of-confusion-matrix-3jja>

[![PyCM Logo](../Images/21c99033983ad3608f9073809992aa00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mcmkEkjW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sepandhaghighi/pycm/raw/master/Otherfiles/logo.png) 
PyCM 是用 Python 编写的多类混淆矩阵库，既支持输入数据向量，也支持直接矩阵，是支持大多数类和整体统计参数的后分类模型评估的合适工具。PyCM 是混淆矩阵的瑞士军刀，主要面向需要大量预测模型指标和大量分类器精确评估的数据科学家。

[![PyCM Block Diagram](../Images/5853bc1fff8baf21abaceb9d7d0f951f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gsgdxaaP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sepandhaghighi/pycm/raw/master/Otherfiles/block_diagram.jpg)

### 从矢量

```
>>> from pycm import *
>>> y_actu = [2, 0, 2, 2, 0, 1, 1, 2, 2, 0, 1, 2] # or y_actu = numpy.array([2, 0, 2, 2, 0, 1, 1, 2, 2, 0, 1, 2])
>>> y_pred = [0, 0, 2, 1, 0, 2, 1, 0, 2, 0, 2, 2] # or y_pred = numpy.array([0, 0, 2, 1, 0, 2, 1, 0, 2, 0, 2, 2])
>>> cm = ConfusionMatrix(actual_vector=y_actu, predict_vector=y_pred) # Create CM From Data
>>> cm.classes
[0, 1, 2]
>>> cm.table
{0: {0: 3, 1: 0, 2: 0}, 1: {0: 0, 1: 1, 2: 2}, 2: {0: 2, 1: 1, 2: 3}}
>>> print(cm)
Predict          0    1    2    
Actual
0                3    0    0    
1                0    1    2    
2                2    1    3    

Overall Statistics : 

95% CI                                                           (0.30439,0.86228)
AUNP                                                             0.66667
AUNU                                                             0.69444
Bennett_S                                                        0.375
CBA                                                              0.47778
Chi-Squared                                                      6.6
Chi-Squared DF                                                   4
Conditional Entropy                                              0.95915
Cramer_V                                                         0.5244
Cross Entropy                                                    1.59352
Gwet_AC1                                                         0.38931
Hamming Loss                                                     0.41667
Joint Entropy                                                    2.45915
KL Divergence                                                    0.09352
Kappa                                                            0.35484
Kappa 95% CI                                                     (-0.07708,0.78675)
Kappa No Prevalence                                              0.16667
Kappa Standard Error                                             0.22036
Kappa Unbiased                                                   0.34426
Lambda A                                                         0.16667
Lambda B                                                         0.42857
Mutual Information                                               0.52421
NIR                                                              0.5
Overall_ACC                                                      0.58333
Overall_CEN                                                      0.46381
Overall_J                                                        (1.225,0.40833)
Overall_MCC                                                      0.36667
Overall_MCEN                                                     0.51894
Overall_RACC                                                     0.35417
Overall_RACCU                                                    0.36458
P-Value                                                          0.38721
PPV_Macro                                                        0.56667
PPV_Micro                                                        0.58333
Phi-Squared                                                      0.55
RR                                                               4.0
Reference Entropy                                                1.5
Response Entropy                                                 1.48336
Scott_PI                                                         0.34426
Standard Error                                                   0.14232
Strength_Of_Agreement(Altman)                                    Fair
Strength_Of_Agreement(Cicchetti)                                 Poor
Strength_Of_Agreement(Fleiss)                                    Poor
Strength_Of_Agreement(Landis and Koch)                           Fair
TPR_Macro                                                        0.61111
TPR_Micro                                                        0.58333
Zero-one Loss                                                    5

Class Statistics :

Classes                                                          0                       1                       2                       
ACC(Accuracy)                                                    0.83333                 0.75                    0.58333                 
AUC(Area under the roc curve)                                    0.88889                 0.61111                 0.58333                 
BM(Informedness or bookmaker informedness)                       0.77778                 0.22222                 0.16667                 
CEN(Confusion entropy)                                           0.25                    0.49658                 0.60442                 
DOR(Diagnostic odds ratio)                                       None                    4.0                     2.0                     
ERR(Error rate)                                                  0.16667                 0.25                    0.41667                 
F0.5(F0.5 score)                                                 0.65217                 0.45455                 0.57692                 
F1(F1 score - harmonic mean of precision and sensitivity)        0.75                    0.4                     0.54545                 
F2(F2 score)                                                     0.88235                 0.35714                 0.51724                 
FDR(False discovery rate)                                        0.4                     0.5                     0.4                     
FN(False negative/miss/type 2 error)                             0                       2                       3                       
FNR(Miss rate or false negative rate)                            0.0                     0.66667                 0.5                     
FOR(False omission rate)                                         0.0                     0.2                     0.42857                 
FP(False positive/type 1 error/false alarm)                      2                       1                       2                       
FPR(Fall-out or false positive rate)                             0.22222                 0.11111                 0.33333                 
G(G-measure geometric mean of precision and sensitivity)         0.7746                  0.40825                 0.54772                 
IS(Information score)                                            1.26303                 1.0                     0.26303                 
J(Jaccard index)                                                 0.6                     0.25                    0.375                   
LR+(Positive likelihood ratio)                                   4.5                     3.0                     1.5                     
LR-(Negative likelihood ratio)                                   0.0                     0.75                    0.75                    
MCC(Matthews correlation coefficient)                            0.68313                 0.2582                  0.16903                 
MCEN(Modified confusion entropy)                                 0.26439                 0.5                     0.6875                  
MK(Markedness)                                                   0.6                     0.3                     0.17143                 
N(Condition negative)                                            9                       9                       6                       
NPV(Negative predictive value)                                   1.0                     0.8                     0.57143                 
P(Condition positive or support)                                 3                       3                       6                       
POP(Population)                                                  12                      12                      12                      
PPV(Precision or positive predictive value)                      0.6                     0.5                     0.6                     
PRE(Prevalence)                                                  0.25                    0.25                    0.5                     
RACC(Random accuracy)                                            0.10417                 0.04167                 0.20833                 
RACCU(Random accuracy unbiased)                                  0.11111                 0.0434                  0.21007                 
TN(True negative/correct rejection)                              7                       8                       4                       
TNR(Specificity or true negative rate)                           0.77778                 0.88889                 0.66667                 
TON(Test outcome negative)                                       7                       10                      7                       
TOP(Test outcome positive)                                       5                       2                       5                       
TP(True positive/hit)                                            3                       1                       3                       
TPR(Sensitivity, recall, hit rate, or true positive rate)        1.0                     0.33333                 0.5                     
dInd(Distance index)                                             0.22222                 0.67586                 0.60093                 
sInd(Similarity index)                                           0.84287                 0.52209                 0.57508                 

>>> cm.matrix()
Predict          0        1        2        
Actual
0                3        0        0        
1                0        1        2        
2                2        1        3        

>>> cm.normalized_matrix()
Predict          0              1              2              
Actual
0                1.0            0.0            0.0            
1                0.0            0.33333        0.66667        
2                0.33333        0.16667        0.5            

>>> cm.matrix(one_vs_all=True,class_name=0)   # One-Vs-All, new in version 1.4
Predict          0    ~    
Actual
0                3    0    
~                2    7 
```

### 直接 CM

```
>>> from pycm import *
>>> cm2 = ConfusionMatrix(matrix={"Class1": {"Class1": 1, "Class2":2}, "Class2": {"Class1": 0, "Class2": 5}}) # Create CM Directly
>>> cm2
pycm.ConfusionMatrix(classes: ['Class1', 'Class2'])
>>> print(cm2)
Predict          Class1    Class2    
Actual
Class1           1         2         
Class2           0         5         

Overall Statistics : 

95% CI                                                           (0.44994,1.05006)
AUNP                                                             0.66667
AUNU                                                             0.66667
Bennett_S                                                        0.5
CBA                                                              0.52381
Chi-Squared                                                      1.90476
Chi-Squared DF                                                   1
Conditional Entropy                                              0.34436
Cramer_V                                                         0.48795
Cross Entropy                                                    1.2454
Gwet_AC1                                                         0.6
Hamming Loss                                                     0.25
Joint Entropy                                                    1.29879
KL Divergence                                                    0.29097
Kappa                                                            0.38462
Kappa 95% CI                                                     (-0.354,1.12323)
Kappa No Prevalence                                              0.5
Kappa Standard Error                                             0.37684
Kappa Unbiased                                                   0.33333
Lambda A                                                         0.33333
Lambda B                                                         0.0
Mutual Information                                               0.1992
NIR                                                              0.625
Overall_ACC                                                      0.75
Overall_CEN                                                      0.44812
Overall_J                                                        (1.04762,0.52381)
Overall_MCC                                                      0.48795
Overall_MCEN                                                     0.29904
Overall_RACC                                                     0.59375
Overall_RACCU                                                    0.625
P-Value                                                          0.36974
PPV_Macro                                                        0.85714
PPV_Micro                                                        0.75
Phi-Squared                                                      0.2381
RR                                                               4.0
Reference Entropy                                                0.95443
Response Entropy                                                 0.54356
Scott_PI                                                         0.33333
Standard Error                                                   0.15309
Strength_Of_Agreement(Altman)                                    Fair
Strength_Of_Agreement(Cicchetti)                                 Poor
Strength_Of_Agreement(Fleiss)                                    Poor
Strength_Of_Agreement(Landis and Koch)                           Fair
TPR_Macro                                                        0.66667
TPR_Micro                                                        0.75
Zero-one Loss                                                    2

Class Statistics :

Classes                                                          Class1                  Class2                  
ACC(Accuracy)                                                    0.75                    0.75                    
AUC(Area under the roc curve)                                    0.66667                 0.66667                 
BM(Informedness or bookmaker informedness)                       0.33333                 0.33333                 
CEN(Confusion entropy)                                           0.5                     0.43083                 
DOR(Diagnostic odds ratio)                                       None                    None                    
ERR(Error rate)                                                  0.25                    0.25                    
F0.5(F0.5 score)                                                 0.71429                 0.75758                 
F1(F1 score - harmonic mean of precision and sensitivity)        0.5                     0.83333                 
F2(F2 score)                                                     0.38462                 0.92593                 
FDR(False discovery rate)                                        0.0                     0.28571                 
FN(False negative/miss/type 2 error)                             2                       0                       
FNR(Miss rate or false negative rate)                            0.66667                 0.0                     
FOR(False omission rate)                                         0.28571                 0.0                     
FP(False positive/type 1 error/false alarm)                      0                       2                       
FPR(Fall-out or false positive rate)                             0.0                     0.66667                 
G(G-measure geometric mean of precision and sensitivity)         0.57735                 0.84515                 
IS(Information score)                                            1.41504                 0.19265                 
J(Jaccard index)                                                 0.33333                 0.71429                 
LR+(Positive likelihood ratio)                                   None                    1.5                     
LR-(Negative likelihood ratio)                                   0.66667                 0.0                     
MCC(Matthews correlation coefficient)                            0.48795                 0.48795                 
MCEN(Modified confusion entropy)                                 0.38998                 0.51639                 
MK(Markedness)                                                   0.71429                 0.71429                 
N(Condition negative)                                            5                       3                       
NPV(Negative predictive value)                                   0.71429                 1.0                     
P(Condition positive or support)                                 3                       5                       
POP(Population)                                                  8                       8                       
PPV(Precision or positive predictive value)                      1.0                     0.71429                 
PRE(Prevalence)                                                  0.375                   0.625                   
RACC(Random accuracy)                                            0.04688                 0.54688                 
RACCU(Random accuracy unbiased)                                  0.0625                  0.5625                  
TN(True negative/correct rejection)                              5                       1                       
TNR(Specificity or true negative rate)                           1.0                     0.33333                 
TON(Test outcome negative)                                       7                       1                       
TOP(Test outcome positive)                                       1                       7                       
TP(True positive/hit)                                            1                       5                       
TPR(Sensitivity, recall, hit rate, or true positive rate)        0.33333                 1.0                     
dInd(Distance index)                                             0.66667                 0.66667                 
sInd(Similarity index)                                           0.5286                  0.5286                  

>>> cm3 = ConfusionMatrix(matrix={"Class1": {"Class1": 1, "Class2":0}, "Class2": {"Class1": 2, "Class2": 5}},transpose=True) # Transpose Matrix 
>>> cm3.matrix()
Predict          Class1    Class2    
Actual
Class1           1         2         
Class2           0         5 
```

[Github 回购](https://github.com/sepandhaghighi/pycm)
[网页](http://pycm.shaghighi.ir/)
[文档](http://pycm.shaghighi.ir/doc/)