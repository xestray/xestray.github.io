---
    comments: true
    statistics: true
---

# Makefile 杂记

- GNU make 官方文档：[GNU make](https://www.gnu.org/software/make/manual/make.html)

## 基础

### makefile 规则

- makefile 的文件名默认为 `Makefile` 或 `makefile`，如果文件名不是这两者，那么需要使用 `make -f filename` 来指定文件名。
- makefile 由一系列规则组成，每个规则由三部分组成：目标、依赖和命令。大体结构如下：

    ```makefile
    # 使用 # 号来书写注释
    # 目标: 依赖
    #     命令
    target: dependency1 dependency2 ...
        command1
        command2
        ...
    ```

    - 依赖可以是另一条规则的目标名，如果依赖是目标名，那么 make 会先查看这个依赖是否存在或是否需要更新，如果不存在或需要更新，那么会先执行这个依赖的规则。

- **命令必须以 `Tab` 开头，不能用空格代替**。

!!! example

    假设我们已经有了 `a.txt` `b.txt` 和 `c.txt` 三个文件，makefile 内容如下：

    ```makefile
    x.txt: m.txt c.txt
        cat m.txt c.txt > x.txt

    m.txt: a.txt b.txt
        cat a.txt b.txt > m.txt
    ```

    - 当这个 makefile 被执行时，默认将第一条规则作为整个 makefile 的总目标，即生成 `x.txt` 文件
    - `x.txt` 依赖于 `m.txt`，所以会先执行第二条规则来创建/更新 `m.txt` 文件

**make 通过文件的创建和修改时间来判断是否应该更新一个目标文件。**

还是上面那个文本文件的例子，

- 当我们修改 `c.txt` 时，会使 `c.txt` 的时间戳比 `x.txt` 更晚
    - makefile 意识到 `x.txt` 需要被重新生成，因此执行 `cat m.txt c.txt > x.txt` 来生成新的 `x.txt`
    - 而由于 `m.txt` 与 `c.txt` 无关，所以 `c.txt` 的修改不会影响 `m.txt`
- 当我们修改 `a.txt` 时，`a.txt` 的时间戳比 `m.txt` 更晚
    - makefile 首先查看 `x.txt` 的依赖中是否需要更新
    - 它发现 `c.txt` 不需要更新，但 `m.txt` 的依赖 `a.txt` 已经被更新了，所以 `m.txt` 也需要被更新
    - 因此 makefile 会先执行 `cat a.txt b.txt > m.txt` 来更新 `m.txt`，然后再执行 `cat m.txt c.txt > x.txt` 来更新 `x.txt`

### 伪目标

伪目标（Phony Target）是一种特殊的目标，它不是一个文件，而是一个标签。伪目标的作用执行一些特殊的操作，例如清理工程、生成文档等。

当我们想要删除已经生成的文件时，可以使用伪目标。

```makefile
.PHONY: clean
clean:
    rm -f m.txt x.txt
```

通常来说，`clean`、`install`、`uninstall` 等都是约定俗成的伪目标名称，这时候也可以不使用 `.PHONY` 来声明伪目标（除非这个目录下真的有一个文件叫做 `clean`）。

### 其他

- 控制打印 `@`：在命令前加上 `@` 可以阻止 make 打印命令本身，只打印命令的输出。

    ```makefile
    target:
        @echo "Hello, world!"
    ```

- 忽略错误：在命令前加上 `-` 可以忽略命令执行的错误。

    - 当我们执行的命令遇到错误时会让 makefile 停止执行，但有时候我们希望即使命令执行失败也继续执行后续命令，这时候可以使用 `-` 来忽略错误。

    ```makefile
    target:
        -rm -f zzzs.txt
        echo 'ok'
    ```

## 变量

### 给变量赋值

- `=`：递归赋值，在使用变量时才会递归地展开变量

    === "example 1"

        ```makefile 
        foo = $(bar)
        bar = $(ugh)
        ugh = Huh?

        all:;echo $(foo)
        # 打印的结果为 Huh?，$(foo)展开得到$(bar)，
        # $(bar)展开得到$(ugh)，$(ugh)展开得到Huh?
        # 最终$(foo)展开得到Huh?g
        ```

    === "example 2"

        ```makefile
        VAR1 = foo
        VAR2 = $(VAR1)   # VAR2 的值是未展开的 $(VAR1)
        VAR1 = bar

        all:
            @echo $(VAR2)  # 输出 "bar"（使用时的 VAR1 已是 bar）
        ```

- `:=`：简单赋值，在赋值时就展开变量

    ```makefile
    VAR1 := foo
    VAR2 := $(VAR1)   # VAR2 的值是已展开的 foo
    VAR1 := bar

    all:
        @echo $(VAR2)  # 输出 "foo"（VAR2 值不会因为 VAR1 的改变而改变）
    ```

- `+=`：追加赋值，用于向已经定义的变量添加文本

    ```makefile
    VAR1 = foo
    VAR1 += bar
    all:
        @echo $(VAR1)  # 输出 "foo bar"
    ```

- `?=`：条件赋值，只有当变量未定义时才会赋值

    ```makefile
    VAR1 ?= aaa
    VAR1 ?= bbb
    all:
        @echo $(VAR1)  # 输出 "aaa"
    ```

!!! note "未定义的变量"
    当一个变量未定义时，make 会将其视为空字符串。

    ```makefile
    all:
        @echo $(VAR1)  # 输出 ""
    ```

### 使用变量

使用变量时用 `${}` 或 `$()`。

```makefile
# 简单赋值
CC = gcc
CFLAGS := -Wall -g

hello: hello.c
    ${CC} $(CFLAGS) -o hello hello.c
```

### 自动变量

- `$@`：表示规则中的目标文件名
- `$<`：表示规则中的第一个依赖文件名
- `$^`：表示规则中的所有依赖文件名
- `$?`：表示规则中所有比目标文件更新的依赖文件名

### 模式规则

```makefile
%.o: %.c
    ${CC} $(CFLAGS) -c $< -o $@
```

这是一个通用的规则（模式匹配规则），% 表示匹配任意非空字符。这个规则的意思是：`%.o` 会匹配上所有以 `.o` 结尾的文件；`%.c` 中的 `%` 会被替换成目标 `.o` 文件名中的前缀。

## 函数

### 常用函数

- `$(subst from,to,text)`：将 text 中的 from 替换为 to

    ```makefile
    VAR := aabbcc
    VAR := $(subst a,b,$(VAR))
    all:
        @echo $(VAR)  # 输出 "bbbbcc"
    ```

- `$(patsubst pattern,replacement,text)`：将 text 中的符合 pattern 的部分替换为 replacement

    ```makefile
    VAR := a.c b.c c.c
    VAR := $(patsubst %.c,%.o,$(VAR))
    all:
        @echo $(VAR)  # 输出 "a.o b.o c.o"
    ```

- `$(strip string)`：去掉 string 中的前后空格

    ```makefile
    VAR :=   a b c   d e f   

    all:
        @echo $(strip $(VAR))  # 输出 "a b c   d e f"
    ```

- `$(findstring find,in)`：在 in 中查找 find，如果找到返回 find，否则返回空

    ```makefile
    VAR := $(findstring a,bcde)
    all:
        @echo $(VAR)  # 输出 ""
    ```

- `$(filter pattern...,text)`：从 text 中过滤出符合 pattern 的部分

    ```makefile
    VAR := a.c b.c c.h d.o
    VAR := $(filter %.c %.o,$(VAR))
    all:
        @echo $(VAR)  # 输出 "a.c b.c d.o"
    ```

- `$(foreach var,list,text)`：遍历 list 中的每个元素，将元素赋值给变量 var，然后展开 text

    ```makefile
    VAR := $(foreach n,1 2 3, $(n).c)
    all:
        @echo $(VAR)  # 输出 "1.c 2.c 3.c"
    ```

- `$(wildcard pattern)`：展开通配符

    ```makefile
    # 展开当前目录下所有的 .c 文件
    VAR := $(wildcard *.c)
    all:
        @echo $(VAR) # 输出 "a.c b.c c.c" 等
    ```
    
- `$(shell command)`：执行 shell 命令

    ```makefile
    VAR := $(shell echo "Hello, world!")
    all:
        @echo $(VAR)  # 输出 "Hello, world!"
    ```

### 其他函数

- `$(if condition,then-part[,else-part])`：条件判断，如果第一个参数为非空字符串，则展开第二个参数，否则展开第三个参数

    ```makefile
    VAR := $(if 1, yes, no)
    all:
        @echo $(VAR)  # 输出 "yes"
    ```

- `$(or condition1,condition2,...)`：逻辑或

    ```makefile
    VAR := $(or 0, 1)
    all:
        @echo $(VAR)  # 输出 "1"
    ```

- `$(and condition1,condition2,...)`：逻辑与

    ```makefile
    VAR := $(and 1, 1)
    all:
        @echo $(VAR)  # 输出 "1"
    ```

!!! note "一个简单的 C 程序 makefile 通用模板"

    ```makefile
    ROOT := $(shell pwd) # 获取当前目录

    # 工作子目录
    SUBDIR := $(ROOT)
    SUBDIR += $(ROOT)/func

    TARGET := main
    OUTPUT := ./output

    INCS := $(foreach dir,$(SUBDIR),-I$(dir)) # 添加头文件路径
    SRCS := $(foreach dir,$(SUBDIR),$(wildcard $(dir)/*.c)) # 获取所有的 .c 源文件
    OBJS := $(patsubst $(ROOT)/%.c,$(OUTPUT)/%.o,$(SRCS)) # 要生成所有的 .o 文件
    DEPS := $(patsubst %.o,%.d,$(OBJS)) # 生成所有的 .d 文件（存有依赖关系的文件）

    $(TARGET) : $(OBJS)
            @echo linking...
            @gcc $(OBJS) -o $@
            @echo complete!

    $(OUTPUT)/%.o : %.c
            @echo compile $<...
            @mkdir -p $(dir $@)
            @gcc -MMD -MP -c $(INCS) $< -o $@

    .PHONY : clean

    clean:
            @echo try to clean...
            @rm -r $(OUTPUT)
            @echo complete!

    -include $(DEPS)
    ```
