## 1.Blast

对于序列MSTRSVSSSSYRRMFGGPGTASRPSSSRSYVTTSTRTYSLGSALRPSTSRSLYASSPGGVYATRSSAVRL:

1.请使用网页版的 blastp, 将上面的蛋白序列只与 mouse protein database 进行比对， 设置输出结果最多保留10个， E 值最大为 0.5。将操作过程和结果截图，并解释一下 E value和 P value 的实际意义。
![img](https://github.com/Bioin-Mixologist/Bioinformatics_Tutorial/blob/main/others/1.Blast_homework1.png))
![img](../../Typora_Picture/2.png)
![img](../../Typora_Picture/result1.png)
![img](../../Typora_Picture/result2.png)
![img](../../Typora_Picture/result3.png)

>$$
E = kmne^{- \lambda S}
>$$
E value: 在数据库随机搜索情况下，获得大于或等于当前比对分数的片段可能数目。

>$$
p = 1 - e^{- E}
>$$
p value: 分值大于或等于当前分值的比对的随机发生概率。

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
