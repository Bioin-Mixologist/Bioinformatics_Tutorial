## 3.1 GO

1.从 [wt.light.vs.dark.all.txt](https://cloud.tsinghua.edu.cn/d/ad22768345664924b202/files/?p=%2FFiles%2FPART_II%2F3.1.go_kegg%2Fwt.light.vs.dark.all.txt&dl=1) (这是我们在“差异表达”一节获得的野生型的结果)中选取显著上调的(FDR<0.05, logFC>1)基因进行GO分析。

```
# 拟南芥的基因表达数据
# baseMean:平均表达量; log2FoldChange:treatment相对于control的log2 fold change; lfcSE:估计出的log2FoldChange的标准差; stat:假设检验用到的统计量; pvalue:p值; padj:经过multiple test correction的p值(FDR:经过multiple test correction的p值)
baseMean	log2FoldChange	lfcSE	stat	pvalue	padj
AT1G01010	1476.80527373285	0.538173075027527	0.298814087682993	1.80102979481499	0.0716981839388965	0.335782524781388
AT1G01020	3019.5129325948	-0.174118337842184	0.103783067173717	-1.67771431876007	0.0934028827847086	0.387762556901047
AT1G03987	5.37709076465172	0.045879334283834	1.15431567028854	0.0397459165328369	0.968295694478582	NA
AT1G01030	308.949858726666	-0.0193715307173973	0.150518114554995	-0.128698999284365	0.897595827280559	0.973890283662992
...
```

文件中的 padj 相当于 FDR，不用单独计算。
使用R处理。先根据 LogFC > 1(**上调**) 且 adjust.p-value < 0.05(**显著**)筛选出符合的基因，再使用**clusterProfiler**包enrichGO()进行GO富集，最后绘图。

```R
################################################################################
library(clusterProfiler)
library(org.At.tair.db)
#### 1) read data ##############################################################
expr.filepath <- "./wt.light.vs.dark.all.txt"

expr <- read.table(expr.filepath, header = TRUE, sep = '\t')

sum(is.na(expr))
# delete lows of NA
expr <- na.omit(expr)
# choose upgrade gene significantly (LogFC > 1 & adjust.p-value < 0.05)
expr.filtered <- expr[expr$log2FoldChange > 1 & expr$padj < 0.05, ]
genes <- rownames(expr.filtered)

#### 2) GO enrichment ##########################################################
ego <- enrichGO(gene = genes,
                OrgDb = org.At.tair.db,
                keyType = "TAIR",
                ont = "ALL",
                pAdjustMethod = "BH",
                pvalueCutoff  = 0.05,
                qvalueCutoff  = 0.2,
                readable = FALSE,
                pool = FALSE)

head(ego)

################################################################################
library(ggplot2)
#### 3) dotplot ################################################################
dotplot(ego, split="ONTOLOGY", showCategory=5, label_format=50)+
  facet_grid(ONTOLOGY~., scale = "free")+
  theme(panel.grid = element_blank())+
  theme(axis.title = element_text(size = 12, color = 'black'),
        axis.text.y = element_text(size = 12),
        legend.title = element_text(size = 12))+
  scale_color_gradient(low = "#C82423", high = "#0000CD")

#### END #######################################################################
```

![img](https://github.com/Bioin-Mixologist/Bioinformatics_Tutorial/blob/main/others/3.1GO_enrichment.png)

2.请问上面的例子中， Fold Enrichment和P value是如何计算的? 请写出公式，并解释原理。此外，在定义显著富集的 GO terms 时为什么一般不是参考P value的大小，而是要计算一个 FDR来做为参考？
$$
\text{Fold Enrichment} = \frac{\text{Gene Ratio}}{\text{Background Ratio}}
$$

$$
p = 1 - \displaystyle\sum_{i = 0}^{k-1}\frac{{M \choose i}{{N-M} \choose {n-i}}} {{N \choose n}}
$$

> k是自定义基因列表中注释到目标基因集的基因数目，n是自定义基因列表的基因总数目，M是背景分布中被注释到目标基因集的基因数目，N是背景分布的基因总数。

$$
\begin{array}{lcc}
\text{ } & \text{User Genes} & \text{Genome} \\
\hline
\text{In Pathway} & k & M \\
\text{Not in Pathway} & n-k & N-M \\
\hline
\text{ } & n & N \\
\end{array}
$$

本质是**列联表检验**问题，卡方检验(大样本趋向于无穷)。当理论频数小于5或加和样本总数小于40时，应该使用Fisher精确检验。
Fisher精确检验本质上是**超几何分布检验**，转变为抽球问题。在N个基因中，有M个在通路中；随机抽取n个基因，观察到在通路中的基因个数为k个，出现该结果(或更罕见情况)的概率为P value。

P value需要矫正。**因为在多次(成千上万次)检验过程中，不可避免地出现第1类错误**。因此通过对P value进行放大，更加严格地筛选检验结果。
目前常用BH矫正方法：对每个p value做矫正，转换为q value；q value = p value * n / rank，其中rank是指p value从小到大排序后的次序。q value有时也被称为FDR(Fale Discover Rate)，经过BH矫正后的q value，若选择α为q value的阈值，那么FDR不会超过α。

参考资料：
http://www.pantherdb.org/tips/tips_geneListAnalysis.jsp
https://www.jianshu.com/p/949626b18e69
https://zhuanlan.zhihu.com/p/51546651
https://zhuanlan.zhihu.com/p/50863682
https://www.biostars.org/p/220465/
https://yulab-smu.top/biomedical-knowledge-mining-book/enrichment-overview.html
https://ke.qq.com/course/395709

