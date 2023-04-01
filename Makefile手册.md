# Makefile手册
**【参考】：https://makefiletutorial.com/**

**Makefile目标**：仅编译修改后，需要编译的文件。

## Makefile语法
- Makefile有众多规则，一条规则如下：

		targets: prerequisites
			command
			command
			command

	- `targets`为文件名，由空格分隔开。
	- `command`为一系列步骤，通常是用来实现生成`target(s)`。**他们需要以`Tab`开头，而不是空格**。
	- `prerequisites`（前提条件）同样是一系列由空格分隔开的文件名。在运行用于生成`target`的`command`之前，这些文件要求必须已经存在。这些文件也成为依赖（dependencies）。

## Makefile精髓

- 小例子：

		blah: blah.c
			cc blah.c -o blah
	- 因为`blah.c`是`blah`的先决条件。所以判断是否需要重新编译的基准为：**`blash.c`的最后修改时间在编译生产`blash`后是否修改，若修改，则需要重新编译。**

## 一个小例子

	blah: blah.o
		cc blah.o -o blah # Runs third
	
	blah.o: blah.c
		cc -c blah.c -o blah.o # Runs second
	
	# Typically blah.c would already exist, but I want to limit any additional required files
	blah.c:
		echo "int main() { return 0; }" > blah.c # Runs first

- **`blah`会在以下几个步骤生成**：
	- Make会选择`blah`作为目标，因为第一个目标为默认目标
	- `blah`依赖于`blah.o`, 所以make会搜索**blah.o**文件作为目标
	- `blah.o`依赖于`blah.c`, 所以make会搜索目标文件`blah.c`
	- `blah.c`没有依赖, 所以`echo`命令运行
	- `cc -c`命令随后运行, 因为`blah.o` 的依赖已经完成
	- ......

## 变量

- 你可以使用`:=`或者`=`来使用，变量仅能为字符串。单引号和双引号对于make来说，只是简单的字符，make会原样接收。

		files := file1 file2
		some_file: $(files)
			echo "Look at this variable: " $(files)
			touch some_file
		
		file1:
			touch file1
		file2:
			touch file2
		
		clean:
			rm -f file1 file2 some_file

- **引用变量**：使用`${} `或` $()`

		x := dude
		
		all:
			echo $(x)
			echo ${x}
		
			# Bad practice, but works
			echo $x 

## 目标文件

当一条规则有多个目标文件是，以下命令会为每个目标进行运行。`$@`是一个包含目标文件名的自动变量（automatic variable ）：

	all: f1.o f2.o
	
	f1.o f2.o:
		echo $@
	# Equivalent to:
	# f1.o:
	#	 echo f1.o
	# f2.o:
	#	 echo f2.o

## 自动变量和通配符

- **`*`通配符**：`*`和`%`在Make里均称为通配符。`*`在文件系统中搜索匹配的文件名。

		# Print out file information about every .c file
		print: $(wildcard *.c)
			ls -la  $?

	- `*`可以用在目标文件，依赖文件或者`wildcard`函数中。
	- 危险：**`*`不能够直接在变量定义中使用**
	- 危险：当`*`不匹配任何文件时，它会留下来（除非在`wildcard`函数中使用）
			
			thing_wrong := *.o # Don't do this! '*' will not get expanded
			thing_right := $(wildcard *.o)
			
			all: one two three four
			
			# Fails, because $(thing_wrong) is the string "*.o"
			one: $(thing_wrong)
			
			# Stays as *.o if there are no files that match this pattern :(
			two: *.o 
			
			# Works as you would expect! In this case, it does nothing.
			three: $(thing_right)
			
			# Same as rule three
			four: $(wildcard *.o)

- **%通配符**：
	- 在匹配（matching）模式下，它可以匹配字符串中的一个或者多个字符
	- 在替代（replacing）模式下，会提取匹配的词干，并在字符串中替代这一部分

- **自动变量**：

		hey: one two
			# Outputs "hey", since this is the target name
			echo $@
		
			# Outputs all prerequisites newer than the target
			echo $?
		
			# Outputs all prerequisites
			echo $^
		
			touch hey
		
		one:
			touch one
		
		two:
			touch two
		
		clean:
			rm -f hey one two

## 花哨的规则：

### 隐性规则：

- 一系列隐性规则：
	- 编译一个C程序：`n.o`通过命令形式`$(CC) -c $(CPPFLAGS) $(CFLAGS) $^ -o $@`自动由`n.c`生成
	- 编译一个C++程序：`n.o`通过命令形式`$(CXX) -c $(CPPFLAGS) $(CXXFLAGS) $^ -o $@`自动由`n.cpp`或`n.cc`生成
	- 链接目标文件：`n`自动由`n.o`通过运行**$(CC) $(LDFLAGS) $^ $(LOADLIBES) $(LDLIBS) -o $@**
- 隐性规则中所使用的重要的变量：
	- `cc`：用于编译C代码的程序；默认为cc
	- `CXX`:用于编译C++代码的程序；默认为g++
	- `CFLAGS`：给C编译器的参数
	- `CXXFLAGS`：给C++编译器的参数
	- `CPPFLAGS`：给C预处理器的参数
	- `LDFLAGS`：当需要当编译器应该调用链接器时，要给编译器的额外参数

			CC = gcc # Flag for implicit rules
			CFLAGS = -g # Flag for implicit rules. Turn on debug info
			
			# Implicit rule #1: blah is built via the C linker implicit rule
			# Implicit rule #2: blah.o is built via the C compilation implicit rule, because blah.c exists
			blah: blah.o
			
			blah.c:
				echo "int main() { return 0; }" > blah.c
			
			clean:
				rm -f blah*

### 静态模式规则

