# Shell实用脚本

## 利用enconv编码转换
```
sudo apt-get install enca
enca filename # 查看编码
find . > 2.txt # 先将find结果重定向到文本，直接重定向到enconv失败了，，，主要是shell玩的不是很溜
echo 2.txt > enconv -L zh_CN -x UTF-8 # 编码	转换
```

## 复制所有文件到指定文件夹find
```
find data/blog -name "*.md.txt" | xargs -i cp {} blogall
```

## 批量重命名
```
find ./ -name "*.md.txt" | awk -F "." '{print $2}' | xargs -i -t mv ./{}.md.txt  ./{}.md
```
