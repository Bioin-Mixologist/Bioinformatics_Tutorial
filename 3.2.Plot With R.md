## 3.2.Plot with R

1.基于ggplot2,用**violin plot**对iris数据集中不同Species的Sepal.Length长度分布进行可视化，并进行如下设置。

画violin plot时可参考 2a)，对作图进行配置还可以参考本节的 1d) 和7)。 提交脚本和结果。
把 **图片标题** 设为"Sepal Length Distribution"，加粗居中(可使用labs函数和theme函数)
把 **y轴范围** 设为0.5到7之间(可使用scale_y_continuous函数)
三个Species的对应的 **填充颜色** 分别设为#C44E52, #55A868和#4C72B0(可使用scale_fill_manual函数)

```R
library(ggplot2)

data(iris)
head(iris)

ggplot(iris, aes(x=Species, y=Sepal.Length, fill=Species)) + #指定按"Species"填充
  geom_violin(trim=FALSE) +
  scale_y_continuous(limits = c(0.5,7))+ #y轴范围0.5到7
  scale_fill_manual(values = c("#C44E52", "#55A868", "#4C72B0"))+ #指定填充颜色
  theme_bw()+
  theme(legend.position = "middle",
        panel.grid = element_blank(),
        panel.border = element_blank(),
        axis.line = element_line(size = 0.5, colour = "black"),
        plot.title = element_text(hjust = 0.5, size = 24, face = "bold") #设置标题格式，居中加粗
        )+
  labs(title="Sepal Length Distribution", x="Species", y = "Sepal.Length") #标题内容，xy轴内容
```
