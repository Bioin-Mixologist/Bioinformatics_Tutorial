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
