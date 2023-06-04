## 2.1.Basic Command

文件 [test_command.gtf](https://cloud.tsinghua.edu.cn/d/ad22768345664924b202/files/?p=%2FFiles%2FPART_I%2F2.linux%2Ftest_command.gtf), [test.gtf](https://cloud.tsinghua.edu.cn/d/ad22768345664924b202/files/?p=%2FFiles%2FPART_I%2F2.linux%2Ftest.gtf)

```shell
wget #直接下载的是网页，需要解析后的连接
#文件通过浏览器下载
```

1.对于示例文件（test_command.gtf），尝试使用相关命令或命令组合分别统计文件的**行数**以及**字符数**。

```shell
#使用wc命令
#查看行数
wc --lines test_command.gtf
#查看字符数
wc --words test_command.gtf
```

2.利用 grep 等命令尝试筛选并输出示例文件中以 chr_ 起始，并且基因id为 YDL248W 的行。

```shell
#使用grep命令
grep "^chr_" test_command.gtf | grep "YDL248W"
#或者
grep "^chr_" test_command.gtf | grep "gene_id \"YDL248W\""

# "^"表示匹配行首
# "|"为管道符
# "\"标记转义字符，"\""表示引号
```

3.利用 sed 等命令将示例文件中的 chr_ 替换为 chromosome_ 并输出每行的第1，3，4，5列。（无需改动原文件，只输出结果）

```shell
#使用sed命令
cat test_command.gtf | sed 's/chr_/chromosome_/g' | cut -f 1,3,4,5
```

4.通过man命令以及更多的资料学习简单的 awk 命令，尝试互换示例文件的第2列和第3列，并且对输出结果利用 sort 命令依照第4和第5列数字大小排序，将最终结果输出到result.gtf文件中。

```shell
#使用man命令查询awk
man awk
#按q退出

#使用awk,sort命令
awk '{temp=$2; $2=$3; $3=temp; print}' test_command.gtf | sort -nk4 -nk5 > result.gtf
```

5.更改示例文件的权限，使得文件所有者及所在用户组用户**可读、写、执行**而其他用户只**可读**，展示权限修改前后的权限变化。

```shell
#查看文件信息
ls -hl
# -rw-rw-r-- 1 jack jack  636  1月 1 12:00 test_command.gtf

#(所有者权限)rwx (同组权限)rwx (其他用户权限)r--
# 4+2+1=7,4+2+1=7,4+0+0=4
# 774
#更改权限为774
chmod 774 test_command.gtf

#查看更改后的文件信息
ls -hl
# -rwxrwxr-- 1 jack jack  636  1月 1 12:00 test_command.gtf
```
