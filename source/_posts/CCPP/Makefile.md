---
title: Makefile
mathjs: true
date: 2021-08-25 19:29:37
tags:
- c/cpp
- makefile
---

# Make工作流程

1. 预编译
2. 汇编（c/cpp转汇编语言）
3. 编译（汇编转二进制）
4. 链接（库链接）

# 基本语法

```makefile
# define variable
TARGET = main

# name: dependence
${TARGET}: main.o hello.h

# reuse variable
TARGET := MAIN

# % is similar with *
${TARGET}: %.o

# pesudo label: command
clean:
	${RM} ${OBJS}
	@echo "success!" # @+command: dont display this command, only display the result of it
	-${RM} test # '-' means that the test isnt exist, but we need to continue progress and ignore this
# phony for distinguishing between pseduo label named clean and file named clean
.PHONY: clean *clean

```

