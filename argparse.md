# argparse 

状态: 基本完成

# [参考](https://docs.python.org/zh-cn/3.7/howto/argparse.html)

```
import argparse
def get_parser():
	parser = argparse.ArgumentParser(description="Help XXX")
	parser.add_argument(***)
	return parser

if __name__ == "__main__":
	args = get_parser().parse_args()
```



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

# [进阶版本](https://docs.python.org/zh-cn/3.7/library/argparse.html#module-argparse) (skip)

ArgumentParser 对象通常关联一个单独的命令行参数到一个单独的被执行的动作。 `nargs` 命名参数关联不同数目的命令行参数到单一动作。支持的值有：

### N(一个整数) 

把n个参数合到一个列表里

```
parser.add_argument('--foo', nargs=2)
```

### `? `

如果可能的话，会从命令行中消耗一个参数，并产生一个单一项。如果当前没有命令行参数，则会产生 [default](https://docs.python.org/zh-cn/3.7/library/argparse.html#default) 值。注意，对于选项，有另外的用例 - 选项字符串出现但没有跟随命令行参数，则会产生 [const](https://docs.python.org/zh-cn/3.7/library/argparse.html#const) 值。一些说用用例:

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', nargs='?', const='c', default='d')
>>> parser.add_argument('bar', nargs='?', default='d')
>>> parser.parse_args(['XX', '--foo', 'YY'])
Namespace(bar='XX', foo='YY')
>>> parser.parse_args(['XX', '--foo'])
Namespace(bar='XX', foo='c')
>>> parser.parse_args([])
Namespace(bar='d', foo='d')
```

如果后面有参数, 就接受参数, 如果出现后面没参数就是const 如果没出现过, 那这个选项值就是default

`nargs='?'` 的一个更普遍用法是允许可选的输入或输出文件:

### `*`

`'*'`。所有当前命令行参数被聚集到一个列表中。注意通过 `nargs='*'` 来实现多个位置参数通常没有意义，但是多个选项是可能的。例如:

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', nargs='*')
>>> parser.add_argument('--bar', nargs='*')
>>> parser.add_argument('baz', nargs='*')
>>> parser.parse_args('a b --foo x y --bar 1 2'.split())
Namespace(bar=['1', '2'], baz=['a', 'b'], foo=['x', 'y'])
```

### `+`

`'+'`。和 `'*'` 类似，所有当前命令行参数被聚集到一个列表中。另外，当前没有至少一个命令行参数时会产生一个错误信息。例如:

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('foo', nargs='+')
>>> parser.parse_args(['a', 'b'])
Namespace(foo=['a', 'b'])
>>> parser.parse_args([])
usage: PROG [-h] foo [foo ...]
PROG: error: the following arguments are required: foo
```

### `argarse.REMAINDER`

`argarse.REMAINDER`。所有剩余的命令行参数被聚集到一个列表中。这通常在从一个命令行功能传递参数到另一个命令行功能中时有用:



如果不提供 `nargs` 命名参数，则消耗参数的数目将被 [action](https://docs.python.org/zh-cn/3.7/library/argparse.html#action) 决定。通常这意味着单一项目（非列表）消耗单一命令行参数。



# metavar

输出help时使用的...



当 [`ArgumentParser`](https://docs.python.org/zh-cn/3.7/library/argparse.html#argparse.ArgumentParser) 生成帮助消息时，它需要用某种方式来引用每个预期的参数。 默认情况下，ArgumentParser 对象使用 [dest](https://docs.python.org/zh-cn/3.7/library/argparse.html#dest) 值作为每个对象的 "name"。 默认情况下，对于位置参数动作，[dest](https://docs.python.org/zh-cn/3.7/library/argparse.html#dest) 值将被直接使用，而对于可选参数动作，[dest](https://docs.python.org/zh-cn/3.7/library/argparse.html#dest) 值将被转为大写形式。 因此，一个位置参数 `dest='bar'` 的引用形式将为 `bar`。 一个带有单独命令行参数的可选参数 `--foo` 的引用形式将为 `FOO`。 示例如下:

可以使用 `metavar` 来指定一个替代名称

请注意 `metavar` 仅改变 *显示的* 名称 -  [`parse_args()`](https://docs.python.org/zh-cn/3.7/library/argparse.html#argparse.ArgumentParser.parse_args) 对象的属性名称仍然会由 [dest](https://docs.python.org/zh-cn/3.7/library/argparse.html#dest) 值确定。

不同的 `nargs` 值可能导致 metavar 被多次使用。 提供一个元组给 `metavar` 即为每个参数指定不同的显示信息: