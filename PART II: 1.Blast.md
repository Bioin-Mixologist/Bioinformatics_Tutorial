## 1.Blast

对于序列MSTRSVSSSSYRRMFGGPGTASRPSSSRSYVTTSTRTYSLGSALRPSTSRSLYASSPGGVYATRSSAVRL:

1.请使用网页版的 blastp, 将上面的蛋白序列只与 mouse protein database 进行比对， 设置输出结果最多保留10个， E 值最大为 0.5。将操作过程和结果截图，并解释一下 E value和 P value 的实际意义。
![img](https://github.com/Bioin-Mixologist/Bioinformatics_Tutorial/blob/main/others/1.Blast_homework_F1.png)


E value: 在数据库随机搜索情况下，获得大于或等于当前比对分数的片段可能数目。

$$ E = kmne^{- \lambda S} $$

p value: 分值大于或等于当前分值的比对的随机发生概率。

$$ p = 1 - e^{- E} $$

E value 和 p value是反映比对显著性的两种不同方式。
当E<0.1时，E value ≈ p value 。
BLAST 的结果只列出 E value 。

|  E   |     p      |   E    |     p      |
| :--: | :--------: | :----: | :--------: |
|  10  | 0.99995460 |  0.1   | 0.09516258 |
|  5   | 0.99326205 |  0.05  | 0.04877058 |
|  2   | 0.86466472 | 0.001  | 0.00099950 |
|  1   | 0.63212056 | 0.0001 | 0.00010000 |

参考资料：

https://www.ncbi.nlm.nih.gov/BLAST/tutorial/Altschul-1.html#head4

http://abc.cbi.pku.edu.cn/talk/blast-kuang-c.pdf

http://abc.cbi.pku.edu.cn/talk/blast-jiang-s.pdf

http://abc.cbi.pku.edu.cn/talk/blast-kang-yj.pdf

https://yj-mo.github.io/2020/09/30/Homework2/

2.请使用 Bash 脚本编程：将上面的蛋白序列**随机打乱生成10个序列**， 然后对这10个序列**两两之间进行 blast 比对**，输出并解释结果。（请上传bash脚本，注意做好重要code的注释；同时上传一个结果文件用来示例程序输出的结果以及你对这些结果的解释。）

新建shuffle10.sh脚本，用于生成10个序列：

```bash
#!/bin/bash
# shuffle a sequence 10 times
# input sequence
s="MSTRSVSSSSYRRMFGGPGTASRPSSSRSYVTTSTRTYSLGSALRPSTSRSLYASSPGGVYATRSSAVRL"

# start index
si=0
L=${#s} # length of the sequence
# end index
ei=$(($L-1))
> shuffle10.txt

for j in {1..10};do
shuffled=""

	for i in `seq ${si} ${ei} | shuf`;do
  	# seq generate integers range from $si to $ei (include both $si and $ei)
  	# shuf command randomize the order of these integers
  	shuffled=${shuffled}${s:$i:1}
  	# ${s:$i:1}: variable expansion, take substring start from $i with length 1, this operator in bash use 0 based coordinate
  	# $shuffled${s:$i:1}: concatenate string $shuffled and string ${s:$i:1}
	done

	echo "original sequence: ${s}"
	echo "shuffled sequence${j}: ${shuffled}"
	echo ">sequence ${j}" >> shuffle10.txt
	echo "${shuffled}" >> shuffle10.txt
done
#通过两个for循环嵌套，生成10个序列。
#序列结果储存在shuffle10.txt文件中。
```

```shell
#运行脚本
bash shuffle10.sh
```

文件shuffle10.txt，用于建库(ShuffleGenome)。

```shell
#blast建库(蛋白)
makeblastdb -dbtype prot -in ./shuffle10.txt -out database/ShuffleGenome
```

取shuffle10.txt的前两行，新文件shuffle1.txt，和建库比对。

```shell
#取sequence 1(前2行)，建新文件shuffle1.txt
head -2 shuffle10.txt > s1.txt
#blastp比对
blastp -query shuffle1.txt -db database/ShuffleGenome -out output/blastp_shuffle1
```

结果文件解释：

```bash
Query= sequence 1 #输入的序列

Length=70 #输入序列长度

#比对到的库内序列依次排序
                                                                      Score     E
Sequences producing significant alignments:                          (Bits)  Value

seqGene 1                                                             123     2e-43
seqGene 5                                                             18.5    0.035
seqGene 7                                                             16.9    0.16 
seqGene 3                                                             15.0    0.79 
seqGene 2                                                             14.6    0.88 
seqGene 4                                                             13.9    2.1  

#与SeqGene 1比对的详细信息
>seqGene 1
Length=70

 Score = 123 bits (308),  Expect = 2e-43, Method: Compositional matrix adjust.
 Identities = 70/70 (100%), Positives = 70/70 (100%), Gaps = 0/70 (0%)

Query  1   RTGYVSASSMTYSTSAVSSMLSSGGSTVAPGSLPTPSRRYSSSGRYASGSSRRAPRTTSS  60
           RTGYVSASSMTYSTSAVSSMLSSGGSTVAPGSLPTPSRRYSSSGRYASGSSRRAPRTTSS
Sbjct  1   RTGYVSASSMTYSTSAVSSMLSSGGSTVAPGSLPTPSRRYSSSGRYASGSSRRAPRTTSS  60

Query  61  VLTRLYRFRS  70
           VLTRLYRFRS
Sbjct  61  VLTRLYRFRS  70


>seqGene 5
Length=70

 Score = 18.5 bits (36),  Expect = 0.035, Method: Compositional matrix adjust.
 Identities = 10/17 (59%), Positives = 15/17 (88%), Gaps = 2/17 (12%)
#identities一致性=完全一致的序列/比对上的序列；
#positives(类似于similarity相似性)

Query  42  SSGRYASGSSRRAPRTT  58
           SSGRYA+G+  R+PR++
Sbjct  51  SSGRYAAGT--RSPRSS  65
...
```
3.解释blast 中除了动态规划（dynamic programming）还利用了什么方法来提高速度，为什么可以提高速度。

使用启发式算法(Heuristic Algorithms)，降低计算量。先找到两条序列之间高度相似的小片(种子seed)；而后向两端延伸并比对。

参考资料：

https://www.jianshu.com/p/9273eb6e2572

https://www.bilibili.com/video/BV1DK411P776

https://www.bilibili.com/video/av10042290

4.我们常见的PAM250有两种（一种对称、一种不对称），请阅读一下 "Symmetry of the PAM matrices" @ wikipedia，再利用Google/wikipedia等工具查阅更多资料，然后总结和解释一下这两种（对称和不对称）PAM250不一样的原因及其在应用上的不同。

计算PAM1时，
如果认为氨基酸a和b之间的替换是**同等概率**的，那么得到的是对称的PAM矩阵；
如果认为a->b和b->a替换的概率是不同的，及两个氨基酸之间的**替换具有倾向性**，那么得到的矩阵是不对称的。
PAM1自乘250次后得到相应的非对称PAM250矩阵，代表蛋白序列的位点在自然选择压力下发生250次1%的突变概率。

PAM-1矩阵适于用来比较亲缘关系非常近的序列，PAM-1000矩阵可以用来比较亲缘性非常远的序列。实践中用的最多的且比较折衷的矩阵是PAM-250。
对称矩阵只需考虑半个矩阵分数，对蛋白序列每个位点的打分只需要简单相加对称矩阵中的分数，计算更加简单；
不对称矩阵存储了替换的偏向性信息，更准确反映每个氨基酸分子的突变趋向。

参考资料：

http://staff.ustc.edu.cn/~zhaowei/bioinformatics2016/3-2,%D0%F2%C1%D0%B1%C8%B6%D4.pdf

https://yj-mo.github.io/2020/09/30/Homework2/

https://cloud.tsinghua.edu.cn/d/7856522150094e49967d/files/?p=%2F2021%E7%A7%8B%2F2021%E7%A7%8B-%E8%AF%BE%E5%A0%82%E5%88%86%E4%BA%AB%E8%8A%82%E9%80%89%E6%B1%87%E6%80%BB.pptx
