# make


`make` 是构建大型项目的首选方案。

<!--more-->

## Makefile

Makefile 基本格式:

```makefile
target ... : prerequisites ...
    command
    ...
```

- `target` - 目标，目标通常是文件名，Make 命令所要构建的对象。
- `prerequisites` - 前置条件，通常是一组文件名，之间用空格分隔。
- `command` - 命令，生成目标所需要执行的命令，由一行或多行的 shell 命令组成，是构建 `target` 的具体指令。

> **Makefile 中的命令必须以 `[tab]` 开头**。

### target

一个 target 构成一条规则。target 可以是一个文件名，也可以是多个文件名，之间用空格分隔。

运行 make 时没有指定目标，默认会执行 Makefile 文件的第一个目标。

#### 伪目标

当 target 是某个操作的名字时，被称为**伪目标**（phony target）。

```makefile
clean:
 rm -f apiserver
```

`clean` 就是伪目标，做一些清理操作。

```sh
make clean
```

如果当前目录中，存在一个文件叫做 `clean`，那么 `clean` 操作不会执行。因为 Make 任务 `clean` 已经存在，不需要重新构建。

这个时候可以声明 `clean` 是"伪目标"：

```makefile
.PHONY: clean
clean:
    rm -f apiserver
```

声明"伪目标"之后， make 就不会检查 `clean` 文件是否存在。

`.PHONY:` 后面可以声明一个或多个伪目标，之间用空格分隔。

### prerequisites

prerequisites 指定了 target 是否重新构建的判断标准：只要有一个前置文件不存在，或者有过更新，target 就需要重新构建。

```makefile
.PHONY: buildimg
buildimg: apisevergo
    cp apisevergo /apisevergo

apisevergo:
    ./build_binary.sh
```

上面的示例，`buildimg` 的前置条件是 `apisevergo`。如果 `apisevergo` 已经存在，那么 `make buildimg` 可以正常运行，
**否则必须再写一条规则，来生成 `apisevergo`**。

```sh
make buildimg
make buildimg
```

上面连续执行两次 `make buildimg`。第一次执行会先生成 `apisevergo`，然后再执行 `cp apisevergo /apisevergo`。
第二次执行，make 发现 apisevergo 存在而且没有变动，就不会执行任何操作。

### command

command 是构建 target 的具体指令，它的运行结果通常就是生成目标文件。

命令必须以 `[tab]` 开头，可以用内置变量 `.RECIPEPREFIX` 声明，来使用其他键。

```makefile
.RECIPEPREFIX = >
all:
> echo Hello, world
```

`.RECIPEPREFIX`指定了 `>` 替代 `tab` 键。

**每行命令在一个单独的 shell 中执行。也就是说 shell 之间没有继承关系**。

```makefile
var-lost:
    export foo=bar
    echo "foo=[$$foo]"
```

执行 `var-lost` 取不到 `foo` 的值。因为两行命令在两个不同的进程执行。有三种解决办法：

1. 将两行命令写在一行，中间用分号分隔。
2. 在换行符前加反斜杠转义。
3. 使用 `.ONESHELL`

```makefile
# 1
var-kept:
    export foo=bar; echo "foo=[$$foo]"

# 2
var-kept2:
    export foo=bar; \
    echo "foo=[$$foo]"

# 3
.ONESHELL:
var-kept3:
    export foo=bar;
    echo "foo=[$$foo]"
```

### 语法

`#` 表示注释。
`@` 放在行首，表示不打印此行。默认情况下会打印每条命令。

#### 通配符

通配符（wildcard）用来指定一组符合条件的文件名。与 Bash 一致，主要有星号 `*`、`?`、`[...]`。

```makefile
# *.log 表示所有后缀名为 log 的文件。
clean:
    rm -f *.log
```

#### 匹配符

可以对文件名，进行类似正则运算的匹配，主要用到的匹配符是 `%`。可以将大量同类型的文件，只用一条规则就完成构建。

```makefile
%.o: %.c

# 等同于
f1.o: f1.c
f2.o: f2.c
```

#### 变量和赋值符

```makefile
# 使用等号自定义变量
txt = Hello World
test:
    @echo $(txt) # 调用自定义变量需要放在 `$( )` 之中

test:
    @echo $$HOME # 调用 shell 变量时，变量前需要多加一个 `$`，这样才会对对 `$` 符号转义
```

**赋值**

```makefile
VARIABLE = value
# 在执行时扩展，允许递归扩展。

VARIABLE := value
# 在定义时扩展。

VARIABLE ?= value
# 只有在该变量为空时才设置值。

VARIABLE += value
# 将值追加到变量的尾端。
```

**内置变量**
内置变量，如 `$(CC)` 指向当前使用的编译器，`$(MAKE)` 指向当前使用的 make 工具。
[内置变量](https://www.gnu.org/software/make/manual/html_node/Implicit-Variables.html)

**自动变量**
make 的自动变量与当前规则有关。

- `$@` - 指代当前构建的那个目标。比如，`make foo` 的 `$@` 就指代 `foo`。
- `$<` - 指代第一个前置条件。比如，`t: p1 p2`，那么 `$<` 就指代 `p1`。
- `$?` - 指代比目标更新的所有前置条件，之间以空格分隔。比如，`t: p1 p2`，`p2` 的时间戳比 `t` 新，`$?` 就指代 `p2`。
- `$^` - 指代所有前置条件，之间以空格分隔。比如，`t: p1 p2`，`$?` 就指代 `p1 p2`。
- `$*` - 指代匹配符 `%` 匹配的部分， 比如 `%` 匹配 `f1.txt` 中的 `f1`，`$*` 就表示 `f1`。
- `$(@D)` 和 `$(@F)` - 分别指向 `$@` 的目录名和文件名。比如，`$@` 是 `src/input.c`，那么 `$(@D)` 为 `src` ，`$(@F)` 的
值为 `input.c`。
- `$(<D)` 和 `$(<F)` - 分别指向 `$<` 的目录名和文件名。

**控制语句**
控制语句使用 Bash 语法。

```makefile
ifeq ($(CC),gcc)
  libs=$(libs_for_gcc)
else
  libs=$(normal_libs)
endif


LIST = one two three
all:
    for i in $(LIST); do \
        echo $$i; \
    done

# 等同于

all:
    for i in one two three; do \
        echo $i; \
    done
```

**函数**
函数格式：`$(function arguments)` 或者 `${function arguments}`。

常用内置函数：

```makefile
# shell 函数用来执行 shell 命令

srcfiles := $(shell echo src/{00..99}.txt)

# wildcard 函数替换 Bash 的通配符
srcfiles := $(wildcard src/*.txt)

# subst 函数用来文本替换
$(subst from,to,text)
$(subst ee,EE,feet on the street) # "feet on the street"替换成"fEEt on the strEEt"

# patsubst 函数用于模式匹配的替换
$(patsubst pattern,replacement,text)
$(patsubst %.c,%.o,x.c.c bar.c)    # 将文件名"x.c.c bar.c"，替换成"x.c.o bar.o"

# 替换后缀名
# 替换后缀名函数的写法是：变量名 + 冒号 + 后缀名替换规则
min: $(OUTPUT:.js=.min.js)         # 将变量 OUTPUT 中的后缀名 .js 全部替换成 .min.js 。
```

### include

`include` 可以引用其他 makefile。`include` 不能以 `[tab]` 开头。

makefile 入口文件，引用 build 目录下的 makefile。

```makefile
include build/Makefile
```

## 构建 Go 项目

```makefile
SHELL := /bin/bash
BASEDIR := $(shell pwd)
BACKENDDIR := $(BASEDIR)/backend
PROXY = http://web-proxy.net:8080
BINARY_NAME = installer
IMAGE_NAME = installer
VERSION = 0.1.2

.PHONY: all build image build-fips proxy module lint golint govet test benchmark clean help
all: proxy build

.ONESHELL:
build: module lint govet
 @echo "build binary: $(BINARY_NAME) .."
 @cd $(BACKENDDIR)
 @go build -o $(BINARY_NAME) ./app/main.go
 @echo "build $(BINARY_NAME) completed."

image: build-fips
 @echo "build image: $(IMAGE_NAME):$(VERSION) .."
 @docker build --rm --no-cache \
 --label com.sample.image-version=$(VERSION) \
 --label com.sample.image-name=$(IMAGE_NAME) \
 --label com.sample.itom.$(IMAGE_NAME)=$(VERSION) \
 --label vendor="Example plc" \
 -t localhost:5000/$(IMAGE_NAME):$(VERSION) $(BASEDIR)


build-fips:
 @echo "docker build binary: $(BINARY_NAME) .."
 @docker run --rm -v $(BACKENDDIR)/app:/backend/app \
 -v $(BACKENDDIR)/library:/backend/library \
 -v $(BACKENDDIR)/build:/backend/build \
 -v $(BACKENDDIR)/build/build.sh:/backend/build.sh \
 -w /backend \
 example.swinfra.net/golang-fips:1.0.0 \
 sh build.sh


proxy:
 @echo "set http proxy .."
 @export http_proxy=$(PROXY)
 @export https_proxy=$(PROXY)

.ONESHELL:
module:
 @echo "download dependencies .."
 @cd $(BACKENDDIR)
 @go mod tidy

lint:
 @echo "run go lint .."
 @golint $(BACKENDDIR)

golint:
 @echo "install golint .."
 @go get -u golang.org/x/lint/golint
 @go install golang.org/x/lint/golint

.ONESHELL:
govet:
 @echo "run go vet .."
 @cd $(BACKENDDIR)
 @go vet ./app/main.go

test:
 @echo "run go test .."

benchmark:
 @echo "run go test benchmark .."

clean:
 @echo "clean .."
 @rm -rf $(BACKENDDIR)/build/$(BINARY_NAME)
 @rm -rf $(BINARY_NAME)
 @docker system prune

.PHONY: show
show:
 @echo "$(BASEDIR)"

help:
 @echo "make              - compile the source code"
 @echo "make image        - build image, please make sure the docker is installed"
 @echo "make build-fips   - compile the source code with golang-fips image"
 @echo "make lint         - run go lint"
 @echo "make vet          - run go vet"
 @echo "make clean        - remove binary file and prune image"
```

`build.sh` 的内容：

```sh
#!/bin/sh

# Proxy
export http_proxy=http://web-proxy.net:8080
export https_proxy=http://web-proxy.net:8080

# Enable go module
export GO111MODULE=on

# Install tools
apk --update add gcc git musl-dev

# Download dependencies, remove unused dependencies
go mod tidy

# Fix clinet-go unsupport go.mod
go get k8s.io/client-go@kubernetes-1.15.0

# Fix github.com/ugorji/go import issue
go get github.com/ugorji/go@v1.1.2-0.20180831062425-e253f1f20942

# Build the renewCert
echo "Build apiservergo ..."
CGO_ENABLED=0 go build -a -o installer ./app/main.go

echo "Build apiservergo completed."
```

## 参考链接

- [Make 命令教](http://www.ruanyifeng.com/blog/2015/02/make.html)
- [使用 Make 构建网站](http://www.ruanyifeng.com/blog/2015/03/build-website-with-make.html)

