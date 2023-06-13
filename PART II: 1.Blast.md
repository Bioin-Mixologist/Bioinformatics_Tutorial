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
echo "#shuffle 10 sequences" > shuffle10.txt

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
