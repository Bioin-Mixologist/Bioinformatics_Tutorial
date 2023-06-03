## 3.1.R Basics

**iris**是R语言自带的一个数据集，它默认会作为一个数据框加载到R环境中，请对iris数据做如下分析：

1.**iris数据集**有几**列**？每列的**数据类型**是什么?

```R
colnames(iris) #输出列名
ncol(iris) #输出列数

#依次输出列名、数据类型
for (n in colnames(iris)) {
  print(n)
  print(class(iris[[n]]))
}

#故，有5列，Sepal.Length:"numeric"(数值型); Sepal.Width:"numeric"(数值型), 
#Petal.Length:"numeric"(数值型), Petal.Width:"numeric"(数值型),Species:"factor"(因子型)
```

2.按Species列将数据分成3组，分别计算Sepal.Length的**均值**和**标准差**，保存为一个csv文件，提供代码和csv文件的内容。

```R
#计算均值和标准差。按Species分组，计算Sepal.Length列。
iris.mean <- aggregate(Sepal.Length ~ Species, data=iris, FUN=mean)
iris.var <- aggregate(Sepal.Length ~ Species, data=iris, FUN=var)

#保存.csv文件
write.csv(iris.mean,"mean.csv",quote=F)
write.csv(iris.var,"var.csv",quote=F)
```

3.对不同Species的Sepal.Width进行**One way ANOVA分析**，提供代码和输出的结果。

```R
summary(aov(Sepal.Width ~ Species,data=iris))

#              Df Sum Sq Mean Sq F value Pr(>F)    
# Species       2  11.35   5.672   49.16 <2e-16 ***
# Residuals   147  16.96   0.115                   
# ---
# Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```
