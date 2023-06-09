## 2.2.Practice Guide

文件 [1.gtf](https://cloud.tsinghua.edu.cn/d/ad22768345664924b202/files/?p=%2FFiles%2FPART_I%2F2.linux%2F1.gtf.gz)

1.列出1.gtf文件中 **XI** 号染色体上的后 10 个 **CDS** （按照每个CDS终止位置的基因组坐标进行sort）。

```shell
# grep 过滤开头的#行
# awk 筛选符合CDS,XI染色体的行
# sort 根据基因终止位置(第5列)数值大小排序(默认升序)
# tail 取末尾10行
grep -v '^#' 1.gtf | awk '$3=="CDS" && $1=="XI" { print $0 }' | sort -nk5 | tail -10
```

2.统计 **IV** 号染色体上各类 **feature**（1.gtf文件的第3列，有些注释文件中还应同时考虑第2列）的数目，并按**升序**排列。

```shell
# grep 过滤开头的#行
# awk 筛选符合IV染色体的行，输出feature(第3列)
# sort 排序(为uniq计数做准备)
# uniq --count 计算重复出现次数
# sort 根据数值排序(默认升序)
grep -v '^#' 1.gtf | awk '$1=="IV" {print $3}' | sort | uniq -c | sort

#    853 start_codon
#    853 stop_codon
#    886 gene
#    886 transcript
#    895 CDS
#    933 exon
```

3.寻找**不在 IV **号染色体上的所有**负链**上的基因中**最长的2条** CDS 序列，输出他们的**长度**。

```shell
# grep 过滤开头的#行
# awk 筛选除IV染色体，基因，负链(第7列为"-")的行，输出长度(终止位置-起始位置+1)
# sort 根据数值-r排序(降序)
# head 取前2行
grep -v '^#' 1.gtf | awk '$1!="IV" && $3=="gene" && $7=="-" {print $5-$4+1}' | sort -n -r | head -2

# 14733
# 12279
```

4.寻找 **XV** 号染色体上长度**最长的5条**基因，并输出**基因 id** 及对应的**长度**。

```shell
# grep 过滤开头的#行
# awk 筛选XV染色体，基因。基因ID(第10列)由";"分隔，删除两侧"""引号。输出基因ID和长度。
# sort 根据长度-r排序(降序)
# head 取前5行
grep -v '^#' 1.gtf | awk '$1=="XV" && $3=="gene" {split($10,x,";");name=x[1];gsub("\"","",name);print name,$5-$4+1}' | sort -n -r -k2 | head -5

# YOL081W 9240
# YOR396W 5391
# YOR343W-B 5314
# YOR192C-B 5314
# YOR142W-B 5269
```

5.统计1.gtf **列数**。

```shell
# awk 强制以tab为分隔符。输出每行的NF(字段数，列数)
# awk 每行累加。输出最后一次结果。
awk -F "\t" '{print NF}' 1.gtf | awk '{sum+=$1;} END {print sum;}'

# 380228
```
