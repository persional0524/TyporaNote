示例:
假设 nowcoder.txt 内容如下：

```shell
#include <iostream>
using namespace std;
int main()
{
    int a = 10;
    int b = 100;
    cout << "a + b:" << a + b << endl;
    return 0;
}
```

你的脚本应当输出：

```shell
9
```



```shell
# wc命令
cat nowcoder.txt | wc -l
wc -l < nowcoder.txt
wc -l nowcoder.txt | gawk '{print $1}'
# grep命令
grep -n "" nowcoder.txt | tail -n1 | gawk -F: '{print $1}'
# sed命令
sed -n '$=' nowcoder.txt
# gawk命令
gawk '{print NR}' nowcoder.txt | tail -n1
gawk 'END{print NR}' nowcoder.txt

line=0
while read p
do
    ((line++))
done < ./nowcoder.txt
echo $line
```

### 2.经常查看日志的时候，会从文件的末尾往前查看，于是请你写一个 bash脚本以输出一个文本文件 nowcoder.txt中的最后5行,事例同第一题

```shell
tail -n5 ./nowcoder.txt

cat ./nowcoder.txt|tail -5
```

### 3.写一个 bash脚本以输出数字 0 到 500 中 7 的倍数(0 7 14 21...)的命令

```shell
seq 0 500 |awk '$0%7==0 {print$0}'

for i in {0..500}
do 
    if [[ i%7 -eq 0 ]];then
        echo $i
    fi
done


for i in {0..500..7}
do
        echo $i
done
```

### 4. 写一个 bash脚本以输出一个文本文件 nowcoder.txt 中第5行的内容。

```shell
sed -n '5p' nowcoder.txt

awk '{if(NR==5){print $0}}' nowcoder.txt

awk 'NR==5' nowcoder.txt

i=0
while read line;
do
    if [ $i -eq 4 ]
    then
        echo $line
        exit 0
    fi
    i=$[$i + 1]
done

line=1
while read value
do
    if [ $line -eq 5 ]
    then
          echo $value;
    fi
        line=$((line+1));
done < nowcoder.txt

sed -n '5,5p'
sed -n '5p'
sed -n '5{p;q}'

awk 'NR==5'
awk '{NR==5{print $0}}' 
awk '{if(NR==5){print $0}}' 

head -5 | tail -1

```

### 5. 写一个 bash脚本以输出一个文本文件 nowcoder.txt中空行的行号,可能连续,从1开始

示例:
假设 nowcoder.txt 内容如下：

```shell
a
b

c

d

e


f
```

你的脚本应当输出：
3
5
7
9
10

```shell
awk '/^$/{print NR}' ./nowcoder.txt
awk '{if($0 == ""){print NR}}' ./nowcoder_1.txt

# = 打印当前读取的数据行数
sed -n '/^$/=' nowcoder_1.txt

grep -n "^$" nowcoder_1.txt|awk -F : '{print $1}'

i=1
while read line
do
    if [ -z $line ];then
        echo $i
    fi
    let i=i+1
done<./nowcoder.txt
```

### 6.写一个 bash脚本以去掉一个文本文件 nowcoder.txt中的空行

示例:
假设 nowcoder.txt 内容如下：

```shell
abc

567


aaa
bbb



ccc
```

你的脚本应当输出：
abc
567
aaa
bbb
ccc

```shell
sed '/^$/d' ./nowcoder.txt 

while read line
do 
    if [ ! -z $line ];then
     echo $line
    fi
done <./nowcoder.txt
```

### 7.写一个 bash脚本以统计一个文本文件 nowcoder.txt中字母数小于8的单词。

示例:
假设 nowcoder.txt 内容如下：
how they are implemented and applied in computer 

你的脚本应当输出：
how
they
are

and

applied

in

说明:
不要担心你输出的空格以及换行的问题

```shell
awk '
{for(i=1;i<=NF;i++){
if(length($i)<8){
print $i}}}
' ./nowcoder.txt


cat ./nowcoder.txt|awk '
{
for(i=1;i<=NF;i++){
    if (length($i)<8)
    print $i
}
}
'

for i in `cat nowcoder.txt`
do
    if [ $(echo $i|wc -L) -lt 8 ];then
        echo $i
    fi

done

```

### 8.假设 nowcoder.txt 内容如下：

```shell
root         2  0.0  0.0      0     0 ?        S    9月25   0:00 [kthreadd]
root         4  0.0  0.0      0     0 ?        I<   9月25   0:00 [kworker/0:0H]
web       1638  1.8  1.8 6311352 612400 ?      Sl   10月16  21:52 test
web       1639  2.0  1.8 6311352 612401 ?      Sl   10月16  21:52 test
tangmiao-pc       5336   0.0  1.4  9100240 238544   ??  S     3:09下午   0:31.70 /Applications
```

以上内容是通过ps aux | grep -v 'RSS TTY' 命令输出到nowcoder.txt文件下面的
请你写一个脚本计算一下所有进程占用内存大小的和:

```shell
s=0
while read l
do
    a=(${l[@]})
    s=$((s+a[5]))
done<./nowcoder_8.txt
echo $s

awk 'BEGIN{sum = 0};{(sum = sum + $6)} ; END{print sum}' nowcoder.txt

awk '{sum=sum+$6} END{print sum}'

awk '{
count += $6
}
END {print count
}' nowcoder.txt
```

### 9. 写一个 bash脚本以统计一个文本文件 nowcoder.txt 中每个单词出现的个数。

为了简单起见，你可以假设：
nowcoder.txt只包括小写字母和空格。
每个单词只由小写字母组成。
单词间由一个或多个空格字符分隔。

示例:
假设 nowcoder.txt 内容如下：
welcome nowcoder
welcome to nowcoder
nowcoder
你的脚本应当输出（以词频升序排列）：
to 1 
welcome 2 
nowcoder 3 

说明:
不要担心个数相同的单词的排序问题，每个单词出现的个数都是唯一的。

```shell
1、首先cat命令查看words.txt
2、tr -s ' ' '\n'将空格都替换为换行 实现分词
3、sort排序 将分好的词按照顺序排序
4、uniq -c 统计重复次数（此步骤与上一步息息相关，-c原理是字符串相同则加一，如果不进行先排序的话将无法统计数目）
5、sort -r 将数目倒序排列
6、awk '{print $2,$1}' 将词频和词语调换位置打印出来


cat ./nowcoder.txt|tr -s ' ' '\n'|sort|uniq -c|sort -n|awk '{print $2,$1}'

cat ./nowcoder.txt|xargs -n 1|sort|uniq -c|sort -n|awk '{print $2" "$1}'

awk '{
 for(i=1;i<=NF;i++){
   row[$i] = row[$i] + 1;
 }
}END{
 for(k in row){
    print k " " row[k]
 }
}
' ./nowcoder.txt |sort -n -k2# n：按数值排序，r：倒序，k：按第2列排序

awk '{
 for(i=1;i<=NF;i++){
   row[$i] += 1;
 }
}END{
 for(k in row){
    print k " " row[k]
 }
}
' ./nowcoder.txt |sort -n -k2

awk '{
    for(i=1;i<=NF;i++){
        row[$i] +=1;
    }
}END{
    for(k in row){
        print k,row[k]
    }
}' ./nowcoder.txt|sort -n -k2

 #!/bin/bash
# 声明 map 类型
declare -A map
while read line
do
  arr=($line)
  for i in ${arr[@]}
  do
    if [ -z ${map[$i]} ]; then
       map[$i]=1
    else 
       ((map[$i]++))
    fi
  done
done < nowcoder.txt

declare -a mm
for val in ${map[@]}
do
  # ${#mm[@]} 代表数组元素的个数
  mm[${#mm[@]}]=${val}
done

for ((i=0;i<${#mm[@]};i++)) 
do 
  for((j=$i+1;j<${#mm[@]};j++))
  do
     if [ ${mm[$i]} -gt ${mm[$j]} ];then
        qq=${mm[$i]}
        mm[$i]=${mm[$j]}
        mm[$j]=$qq
     fi
   done
done

for ((k=0;k<${#mm[@]};k++))
do
  # ${!map[@]} 列出所有的 key
  for key in ${!map[@]}
  do
     if [ ${map[$key]} -eq ${mm[$k]} ]; then
        echo $key ${map[$key]}
     fi
  done
done


awk '{for(i=1;i<=NF;i++)uid[$i]++}END{for( item in uid) {print item,uid[item]}}' nowcoder.txt
```

### 10.给定一个 nowcoder.txt文件，其中有3列信息，如下实例，编写一个sheel脚本来检查文件第二列是否有重复，且有几个重复，并提取出重复的行的第二列信息：

实例：
20201001 python 99
20201002 go 80
20201002 c++ 88
20201003 php 77
20201001 go 88
20201005 shell 89
20201006 java 70
20201008 c 100
20201007 java 88
20201006 go 97

结果：
2 java
3 go

```shell
awk '{print $2}' nowcoder_10.txt|awk '{for(i=1;i<=NF;i++){row[$i] += 1;}}END{for(i in row){if(row[i]>1)print row[i]" "i}}'
#|sort -r


awk '{row[$2]++}END{for(i in row){if(row[i]>1)print row[i],i}}' nowcoder.txt
```

### 11.写一个 bash脚本来转置文本文件nowcoder.txt中的文件内容。

为了简单起见，你可以假设：
你可以假设每行列数相同，并且每个字段由空格分隔

示例:
假设 nowcoder.txt 内容如下：
job salary
c++ 13
java 14
php 12

你的脚本应当输出（以词频升序排列）：
job c++ java php
salary 13 14 12

```shell
awk '{
    for(i=1;i<=NF;i++){
      if(NR==1){
        row[i] = $i;
      }else{
        row[i] = row[i]" "$i;
      }
    }
}END{
  for(i=1;i<=NF;i++){
    print row[i]
  }
}
' ./nowcoder.txt
```

### 12.写一个 bash脚本以统计一个文本文件 nowcoder.txt中每一行出现的1,2,3,4,5数字个数并且要计算一下整个文档中一共出现了几个1,2,3,4,5数字数字总数。

示例:
假设 nowcoder.txt 内容如下：
a12b8
10ccc
2521abc
9asf
你的脚本应当输出：
line1 number: 2
line2 number: 1
line3 number: 4
line4 number: 0
sum is 7

说明:
不要担心你输出的空格以及换行的问题

```shell
#/bin/bash
linecount=0
sum=0
count=0

while read line
do
    for (( i=0; i<${#line};i++ ))
    do
        if [[  ${line:$i:1} =~ [1-5] ]]
        then
            count=$(($count+1))
        fi
    done
    linecount=$(($linecount+1))
    echo "line$linecount number:$count"
    sum=$(($sum+$count))
    count=0
    
done<nowcoder.txt
echo "sum is $sum"
#####################################
coutline=0
count=0
sum=0
while read line
do
    for((i=0;i<${#line};i++));do
        if [[ ${line:$i:1} =~ [1-5] ]];then
            let count=count+1
        fi
    done
    let coutline=coutline+1
    echo "line${coutline} number: ${count}"
    let sum=sum+count
    count=0
done<./nowcoder.txt
echo "sum is ${sum}"
#####################################
cnt=1
sum=0
while read line
do
    r=`echo $line|grep -oE "[12345]"|wc -l`
    echo "line${cnt} number: ${r}"
    $((cnt++))
    $((sum+=$r))
done
echo "sum is ${sum}"
<./nowcoder.txt
#####################################
#!/bin/bash
declare -A m

line=1
while read l
do
  len=${#l}
  for((i=0;i<$len;i++))
  do
    if [[ ${l:$i:1} =~ [1-5] ]];then
      ((m[$line]++))
    fi
  done
  
  if [[ -z ${m[$line]} ]];then
    m[$line]=0
  fi

  ((line++))
done < nowcoder.txt

sum=0
for i in ${!m[@]}
do
  echo "line${i} number: ${m[$i]}"
  sum=$[$sum+${m[$i]}]
done

echo "sum is $sum"
#####################################
awk '{
    gsub(/[^1-5]/,"",$0);
    print "line"NR" number: "length($0);
    sum+=length($0);
} END{print "sum is "sum}'
#####################################

awk '{
    gsub(/[^1-5]/,"",$0);
    print "line"NR" number: "length($0);
    sum+=length($0);
} END{ print "sum is "sum }' nowcoder.txt
#####################################
cnt=1
sum=0
while read line
do
    r=`echo $line | grep -oE "[12345]" | wc -l`
    echo "line${cnt} number: ${r}"
    let "cnt++"
    let "sum+=$r"
done 
echo "sum is ${sum}" < nowcoder.txt 
```

### 13.写一个 bash脚本以实现一个需求，去掉输入中含有this的语句，把不含this的语句输出

示例:
假设输入如下：
that is your bag
is this your bag?
to the degree or extent indicated.
there was a court case resulting from this incident
welcome to nowcoder


你的脚本获取以上输入应当输出：
to the degree or extent indicated.
welcome to nowcoder

说明:
你可以不用在意输出的格式，包括空格和换行

```shell
grep -v 'this' ./nowcoder.txt
#####################################
sed '/this/d' ./nowcoder.txt
#####################################
awk '$0!~/this/ {print $0}'
#####################################
#!/bin/bash
while read line
do
    flag=0
    for i in $line
    do
        if [[ $i = "this" ]];then
            flag=1
            break
        fi
    done
    if [[ $flag -eq 0 ]];then
        echo $line
    fi
done < nowcoder.txt
```

####  正则运算符

| 运算符 | 描述                             |
| ------ | -------------------------------- |
| ~ !~   | 匹配正则表达式和不匹配正则表达式 |

### 14.写一个bash脚本以实现一个需求，求输入的一个的数组的平均值

第1行为输入的数组长度N
第2~N行为数组的元素，如以下为:
数组长度为4，数组元素为1 2 9 8
示例:
4
1
2
9
8

那么平均值为:5.000(保留小数点后面3位)
你的脚本获取以上输入应当输出：
5.000

```shell
linecnt=0
while read line
do
    if [[ $linecnt -eq 0 ]];then
           a=$line
    else
           let b=b+$line
    fi
let linecnt=linecnt+1
done<./nowcoder.txt
echo "scale=3;$b/$a"|bc
#####################################
awk '{
    if (NR==1) total=$1;
        else sum+=$1
}
    END{
        printf("%.3f",sum/total);
    }
    ' $1
#####################################
awk '{if(NR==1) total=$1; else{sum+=$1} } END {printf ("%.3f" , sum / total)}' $1
```

### 15.写一个 bash脚本以实现一个需求，去掉输入中的含有B和b的单词

示例:
假设输入如下：
big
nowcoder
Betty
basic
test


你的脚本获取以上输入应当输出：
nowcoder test

说明:
你可以不用在意输出的格式，空格和换行都行

```shell
grep -v -E 'b|B' nowcoder.txt
grep -iv "b"
grep -v '[bB]' 

cat nowcoder.txt | grep -v -E 'b|B' 
cat nowcoder.txt|grep -vi "b"

awk '$0!~/b|B/ {print $0}' nowcoder.txt
awk '!/[bB]/'


sed '/[Bb]/d'
sed '/b\|B/d'

```

