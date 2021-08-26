# Shell 脚本代码记录

## 获取链接文件的实际文件路径地址
~~~shell
链接文件可使用 ln -s <源文件路径> <链接文件路径>
file = "链接文件路径"

real_file="$file"
# need this for relative symlinks
while [ -h "$real_file" ] ; do
  ls=`ls -ld "$real_file"`
  link=`expr "$ls" : '.*-> \(.*\)$'`
  if expr "$link" : '/.*' > /dev/null; then
    real_file="$link"
  else
    real_file="`dirname "$PRG"`/$link"
  fi
done
~~~