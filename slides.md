class: left, middle

# GNU Make Tips

1. [GNU Make](#1--make)
2. [Anatomy of a Makefile](#2--anatomy)
3. [Variables](#3--variables)
4. [Conditionals](#4--conditionals)
5. [Targets and Rules](#5--targets-and-rules)
6. [Functions](#6--functions)
100. [References](#reference)

<a class="github-fork-ribbon" href="https://github.com/noahp/gnu-make-tips" data-ribbon="Fork me on GitHub" title="Fork me on GitHub">Fork me on GitHub</a>

---
name: 1--make

# 1. GNU Make

GNU Make is used to run shell commands.

It is a software automation tool that helps executing repetitive tasks.

It is typically used to transform files into some other form, eg compiling
source code into programs or libraries.

It enables tracking "prerequisites" and running a hierarchy of commands to
produce "targets".

The GNU Make manual is very good:
https://www.gnu.org/software/make/manual/html_node/index.html

---
name: 2--anatomy

# 2. 🧩 Anatomy of a Makefile

```makefile
# Comments are prefixed with the '#' symbol

# A make variable "FOO" assignment
FOO = "hello there!"

# A rule creating "test.txt" using the $(FOO) variable
test.txt:
	echo $(FOO) > test.txt
```

---
name: 3--variables

# 3. Variables

Variables that are not set will evaluate to an empty string.

Variable assignment comes in 2 **flavors**:

1. *recursive expansion*

 ```makefile
 FOO = 1
 BAR = foo=$(FOO)
 FOO = 2
 $(info BAR=$(BAR))
 # prints BAR=foo=2
 ```

2. *simple expansion*

 ```makefile
 FOO = 1
 BAR := foo=$(FOO)
 FOO = 2
 $(info BAR=$(BAR))
 # prints BAR=foo=1
 ```

## 3.1 Environment variables

```makefile
# all environment variables are available
$(info YOLO variable = $(YOLO))
```

```bash
$ YOLO="hello there!" make
YOLO variable = hello there!
make: *** No targets.  Stop.
```

---

## 3.2 Overriding variables

```makefile
# the value passed in the make command will override
# any value set elsewhere
YOLO = "not overridden"
$(info $(YOLO))
```

```bash
$ YOLO="environment set" \
    make -f examples/4.overriding-variables.mk YOLO='overridden!!'
overridden!!
make: *** No targets.  Stop.
```

---
name: 4--conditionals

# 4. Conditionals

---
name: 5--targets-and-rules

# 5. Targets and Rules

---
name: 6--functions

# 6. Functions

---
name: reference

# References

- Useful tutorial
 http://maemo.org/maemo_training_material/maemo4.x/html/maemo_Application_Development_Chinook/Chapter_02_GNU_Make_and_makefiles.html
- Nice pictures
 https://www.jfranken.de/homepages/johannes/vortraege/make.en.html
