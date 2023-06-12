# [shell scripts](https://github.com/wjwever/gitblog/issues/26)

* 统计itg超时数目
```
grep total_cost . -r | awk -F'total_cost:|us' '{if($2>100000)print $2}'
```

* 统计itg平均耗时
```
grep total_cost . -r | awk -F'total_cost:|us'  'BEGIN{cnt=0;sum=0}{cnt+=1;sum+=$2}END{print sum/cnt}'
cat log/itg-server2.log* | grep total_cost | awk -F'total_cost:' '{print $2}'| awk -F' us' 'BEGIN{sum=0} {if($1>40000) sum+=1} END{print sum,NR, sum/NR}'
```

* 找出文件（filename）中包含123或者包含abc的行
```
grep -E '123|abc' filename // 找出文件（filename）中包含123或者包含abc的行
egrep '123|abc' filename // 用egrep同样可以实现
awk '/123|abc/' filename // awk 的实现方式2、与操作
```


* 从itg log中抽出itg输入
```
grep INPUT log  -r | awk -F 'message:' '{print $2}'  | sed 's/.$//' > train.input
```

* sed 替换
```
sed -i "s/9231/$port/g" ./conf/$port.conf
```


* sed替换某几行的内容
```
qqq
123
ppp
123
sed -i '2,5s#123#456#' aa.txt
sed '2,3s/123/456/' a.txt
这里跟上一个相比，没有加g，结果只是匹配到了开头的“\s”字符
说明g进行了一个全局的搜索
不带g的话，只是匹配每行的第一个字段，后面的不管
```


* git 提交
```
git push origin HEAD:refs/for/wjw-xiaoduzaijia-sprint8.5
```


* 简单查看diff的文件
```
git diff --stat
```


* 在输出行中去除重复行
```
sort -u seq.txt
```


* bash c 风格循环
```
ans=0
# 注意，这里的 for 循环要有两层括号。
for ((i=1;i<=100;i++))
do
    let ans+=$i
done
echo $ans
```

* 查找文件text中第三行的内容
```
 sed -n '3p' text
```


* 查找文件text中第二行到第四行的内容
``` 
sed -n '2,4p' text
```


* 删除空行
```
cat 文件名 |sed ‘/^$/d'
```


* 找rank_word
```
grep OUTPUT . -r | awk -F 'rank_result:|\\]\\[rewrite_result' '{print $2}' > rank_word
```