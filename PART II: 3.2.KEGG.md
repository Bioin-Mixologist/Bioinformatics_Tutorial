## 3.2. KEGG

1.请用 KEGG enrichment 分析上一章(GO enrichment analysis)中的基因集，比较两章的结果，总结两种方法得到的生物学意义有哪些异同。

```R
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

#### 2) KEGG enrichment ##########################################################
ekegg <- enrichKEGG(gene = genes,
                    organism = "ath",
                    keyType = "kegg",
                    pAdjustMethod = "BH",
                    pvalueCutoff  = 0.05,
                    qvalueCutoff  = 0.2,
                    use_internal_data = FALSE)
head(ekegg)

################################################################################
library(ggplot2)
#### 3) dotplot ################################################################
dotplot(ekegg, showCategory=10, label_format=50)+
  theme(panel.grid = element_blank())+
  theme(axis.title = element_text(size = 12, color = 'black'),
        axis.text.y = element_text(size = 12),
        legend.title = element_text(size = 12))+
  scale_color_gradient(low = "#C82423", high = "#0000CD")

#### END #######################################################################
```
![img](https://github.com/Bioin-Mixologist/Bioinformatics_Tutorial/blob/main/others/3.1KEGG_enrichment.png)
