## 2.3.Linux Bash

文件 [bash_homework.zip](https://cloud.tsinghua.edu.cn/d/ad22768345664924b202/files/?p=%2FFiles%2FPART_I%2F2.linux%2Fbash_homework.zip)

```shell
#解压(默认保留压缩包)
unzip bash_homework.zip
#进入bash_homework目录
cd ./bash_homework
```

1.写出一个 bash 脚本，可以使它自动读取一个文件夹（例如bash_homework/）的内容，将该文件夹下文件的名字输出到 filename.txt, 子文件夹的名字输出到 dirname.txt 。

```shell
touch readname.sh	#创建.sh文件
chmod u+x readname.sh	#增加执行权限
vi readname.sh	#增加执行权限
```

```shell
#!bin/bash
echo "filenames are following:" > filename.txt
echo "dirnames are following:" > dirname.txt

for val in ./bash_homework/*
do
	if [ -f $val ];then
	echo "FILE: $val"
	echo "$val" | awk -F "/" '{print $3}' >> filename.txt
	elif [ -d $val ];then
	echo "DIR: $val"
	echo "$val" | awk -F "/" '{print $3}' >> dirname.txt
	else
	echo "unknown: $val"
	fi
done

exit 0
#遍历bash_homework文件夹
#[ -f ]判断是否为文件；[ -d ]判断是否为目录。分别输出到filename.txt和dirname.txt
```

```shell
bash readname.sh
```
