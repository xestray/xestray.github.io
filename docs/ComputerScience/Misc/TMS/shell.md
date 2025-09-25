---
    comments: true
    statistics: true
---

# Shell

## 重定向

shell 中的程序通常会设计两个“流”：输入流和输出流。当程序试图从外界读取信息时，会从输入流中读取；当程序试图输出信息时，会将信息写入输出流中。一般来说，一个 shell 程序的输入流是键盘，输出流是屏幕，我们可以通过重定向来改变输入流和输出流。

基本的重定向符号有：

- `>`：将输出流重定向到指定文件中，会覆盖文件原有内容
- `>>`：将输出流重定向到指定文件中，会将内容追加到文件末尾
- `<`：将输入流重定向到指定文件中，将文件内容作为程序的输入
- `|`：管道符，将左侧程序的输出流作为右侧程序的输入流

    例如 `ls | grep txt` 会将 `ls` 命令的输出作为 `grep txt` 的输入，从而实现只显示当前目录下的 txt 文件。

    当我们使用管道（pipe）时，程序对于输入输出流的改变是无感的，即 `ls` 并不知道我们将它的输出流重定向到了 `grep`，`grep` 也并不知道它的输入流来自于 `ls`，它们只是忠实地完成属于自己的工作。

!!! tip
    在使用 `|` 管道符时，`sudo` 命令仅对于它所属的单个命令有效。例如 `sudo cat /etc/shadow | grep user` 中，`sudo` 只对 `cat /etc/shadow` 有效，而 `grep user` 并没有提升权限。

## Shell 脚本

### 变量

#### 定义与使用

在 bash 等 shell 中，变量的定义和使用方式如下：

```bash
foo=bar  # 定义变量 foo，值为 bar
echo $foo  # 使用变量 foo，输出 bar
```

需要注意的是在赋值时不能在等号两边使用空格隔开，如 `foo = bar`，否则解释器会认为我们在调用程序 `foo` 并传递了两个参数 `=` 和 `bar`。

#### 字符串变量

shell 中的字符串使用单引号（`'`）或双引号（`"`）括起来，这两者存在区别：使用单引号定义的字符串为原义字符串，其中的变量不会被解析，而使用双引号定义的字符串会对其中的变量进行解析。

```bash
foo=bar
echo '$foo'  # 输出 $foo
echo "$foo"  # 输出 bar
```

#### 命令替换

有时我们希望将某个命令的输出结果赋值给变量，这时可以使用命令替换（command substitution）。命令替换有两种语法形式：

```bash
var=$(command)  # 使用 $() 语法
var=`command`  # 使用反引号语法
```

我们还可以把命令替换与字符串变量、重定向等结合起来：

```bash
files=$(ls)  # 将 ls 命令的输出赋值给变量 files
echo "$files"  # 输出 files 变量的内容
```

```bash
echo <(cat file.txt)  # 将文件内容作为命令的输入
```

### 逻辑运算符

在 shell 中，我们直接使用全小写的 `true` 和 `false` 来表示布尔值 `真` 和 `假`。我们可以使用逻辑运算符来进行逻辑运算，需要注意这几个逻辑运算符同样会出现短路操作：

- `&&`：逻辑与（AND），第一个命令执行成功时，才会执行第二个命令
- `||`：逻辑或（OR），第一个命令执行失败时，才会执行第二个命令
- `;`：命令分隔符，依次执行多个命令，无论前一个命令是否成功，都会执行下一个命令

??? example

    - 第一个命令执行失败，第二个命令执行成功，输出为 `command2` 和 `0`：

        ```bash
        false || echo "command2"
        echo $?
        ```

    - 第一个命令执行成功，不执行第二个命令，输出为 `0`：

        ```bash
        true || echo "command2"
        echo $?
        ```

    - 第一个命令执行失败，不执行第二个命令，输出为 `1`：

        ```bash
        false && echo "command2"
        echo $?
        ```

### Shell 函数

shell 中函数的语法结构如下：

```bash
function_name() {
    # function body
}
```

例如

```bash
mcd () {
    mkdir -p "$1"
    cd "$1"
}
```
 
在这里我们使用了一个特殊变量 `$1`，它表示传递给函数的第一个参数，类似地 `$2` 表示第二个参数，依此类推。

!!! note "特殊变量"
    - `$0`：脚本的名称
    - `$#`：传递给脚本或函数的参数个数
    - `$1`、`$2`、...：传递给脚本或函数的第 1、2、... 个参数
    - `$@`：传递给脚本或函数的所有参数，作为一个整体
    - `$*`：传递给脚本或函数的所有参数，作为一个字符串
    - `$$`：当前脚本的进程 ID (PID)
    - `$?`：上一个命令的退出状态，0 表示成功，非 0 表示失败
    - `$_`：上一个命令的最后一个参数
    - `!!`：上一个命令

我们在使用 shell 脚本时，通常会看到 `#!/bin/bash` 这样的开头，这被称为 shebang，用于指定脚本的解释器。在下面这个例子中，`/bin/zsh` 是 zsh 解释器的路径。

```bash
#!/bin/zsh
echo "Starting program at $(date)"

echo "Running Program $0 with $# arguments with pid $$"

for file in "$@"; do
    # 将标准输出和标准错误都重定向到 /dev/null，相当于屏蔽掉所有输出
    grep foobar "$file" > /dev/null 2> /dev/null 

    if [[ "$?" -ne 0 ]]; then
    echo "File $file does not have any foobar,add one"
    echo "#foobar">>"$file"
    fi
done # end for
```

### 通配符

在 shell 中，通配符（wildcards）用于匹配文件名或路径名。常用的通配符有：

- `*`：匹配任意数量的任意字符（包括零个字符）
- `?`：匹配任意单个字符
- `[...]`：匹配括号内的任意单个字符，例如 `[abc]` 匹配 `a`、`b` 或 `c`
- `{...}`：匹配括号内的任意字符串，例如 `{jpg,png}` 匹配 `jpg` 或 `png`
    - 当我们使用多个 `{}` 时，会进行笛卡尔积匹配，例如 `file{1,2}.{jpg,png}` 会匹配 `file1.jpg`、`file1.png`、`file2.jpg` 和 `file2.png`

!!! tip "shell 函数和脚本的区别"
    - 函数只能与 shell 使用相同的语言，脚本可以使用任意语言。因此在脚本中包含 shebang 是很重要的。

        例如我们可以写一个 Python 脚本 `script.py`：

        ```python
        #!/usr/local/bin/python
        import sys
        for arg in reversed(sys.argv[1:]):
            print(arg)
        ```

        脚本第一行的 shebang 指定了解释器路径，这样我们就可以直接运行脚本，而不需要每次都输入 `python script.py`：

        ```bash
        ./script.py arg1 arg2 arg3
        ```

    - 函数仅在定义时被加载，而脚本在每次运行时都会被单独加载。因此函数的加载比脚本略快，但每次我们修改海曙定义后，都需要重新加载一次 `source func.sh`。
    - 函数会在当前的 shell 环境中执行，脚本会在单独的进程中执行。因此，函数可以对环境变量进行更改，比如改变当前工作目录，脚本则不行。

## Shell 工具

> 这里只简要记录一下一些常见且好用的 shell 工具

- `tldr`： 一个简化版的 `man`，提供常用命令的简要说明和示例，适合快速查阅。

    例如 `tldr tar` 会显示 `tar` 命令的常用选项和示例。

- `find`： 用于在文件系统中搜索文件和目录，支持多种搜索条件。

    ```bash
    # 查找所有名称为 src 的文件夹
    find . -name src -type d
    # 查找所有文件夹路径中包含 test 的 python 文件
    find . -path '*/test/*.py' -type f
    # 查找前一天修改的所有文件
    find . -mtime -1
    # 查找所有大小在 500k 至 10M 的 tar.gz 文件
    find . -size +500k -size -10M -name '*.tar.gz'
    ```

    我们也可以直接对查找到的文件进行操作：

    ```bash
    # 删除全部扩展名为.tmp 的文件
    find . -name '*.tmp' -exec rm {} \;
    # 查找全部的 PNG 文件并将其转换为 JPG
    find . -name '*.png' -exec magick {} {}.jpg \;
    ```

- `autojump`： 一个基于频率和最近使用的目录跳转工具，类似 `cd`，但更智能。

    `j proj` 会跳转到最近访问过的包含 `proj` 的目录。
    