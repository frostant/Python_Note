# Detectron2 Note

[TOC]

# import multiprocessing as mp #ship

```
mp.set_start_method("spawn",force=True)
# 
```



# import argparse

## [参考](https://docs.python.org/zh-cn/3.7/howto/argparse.html)

```
parser = argparse.ArgumentParser() # 初始化
args=parse_args() # args就是我们得到的参数
# 因此简写成 args = get_parser().parse_args() 其中get_parser()是一个解析参数的函数
!python test.py --help # 查看命令行参数
parser.add_argument("arg", help="help info",type=int) #名字, 帮助, 类型
# 如果名字前面没有-- 那么就是位置参数,按照位置顺序读入
parser.add_argument("--kexuanarg", helo="XXX")
if args.kexuanarg:
	print("有参数")
!python test.py --kexuanarg 1
# 有--是可选参数, 调用就把--去掉就可以了
parser.add_argument("--verbose", help="XXX",action="store_true")
# 表示我们只存储true和false 不需要参数
!python test.py --verbose

parser.add_argument("-v","--verbose", help="XXX",action="store_true")
# 短选项, 使用-v就可以了, 效果好像是一样的
!python test.py -v
# 可选参数的位置是没有影响的
parser.add_argument("-v","--verbose", type=int, choices=[0,1,2],help="XX")
# 可以使用 choices=[] 来限制这个参数的取值

parser.add_argument("-v", "--verbose", action="count", help="XXX")
# 使用action="count" 记录出现这个参数多少次 
!python test.py -vv #出现2次
!python test.py --verbose --verbose #出现2次
# 注意, 如果没有输入, 则他是nonetype 而不是0 所以用default
parser.add_argument("-v","--verbose", action="count",default=0,help="XX")

group = parser.add_mutually_exclusive_group()
# 互斥的参数选项, 即parser和group只能出现一种
parser = argparse.ArgumentParser(description="calculate X to the power of Y") #告诉使用者这个函数是干嘛的

```

### [进阶版本](https://docs.python.org/zh-cn/3.7/library/argparse.html#module-argparse) (skip)

