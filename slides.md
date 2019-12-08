class: left, middle

# GNU Make Tips

- [GNU Make Tips](#GNU-Make-Tips)
- [1. GNU Make](#1-GNU-Make)
- [2. Invoking Make](#2-Invoking-Make)
- [3. 🧩 Anatomy of a Makefile](#3-%F0%9F%A7%A9-Anatomy-of-a-Makefile)
- [4. Variables](#4-Variables)
	- [4.1 Environment variables](#41-Environment-variables)
	- [4.2 Overriding variables](#42-Overriding-variables)
	- [4.3 Target-specific variables](#43-Target-specific-variables)
- [5. Conditionals](#5-Conditionals)
- [6. Targets (Goals) and Prerequisites and Rules](#6-Targets-Goals-and-Prerequisites-and-Rules)
- [7. Functions](#7-Functions)
- [8. Sub-make](#8-Sub-make)
- [9. Phony targets](#9-Phony-targets)
- [X. Implicit rules](#X-Implicit-rules)
- [References](#References)

<a class="github-fork-ribbon" href="https://github.com/noahp/gnu-make-tips" data-ribbon="Fork me on GitHub" title="Fork me on GitHub">Fork me on GitHub</a>

---
name: 1-GNU-Make

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
name: 2-Invoking-Make

# 2. Invoking Make

- Running `make` will load a file named `Makefile` from the current directory,
  and attempt to update the default **goal** (more on goals later).
  > *the default is to try `GNUmakefile`, `makefile`, and `Makefile`, in that
  > order*

- You can specify another file with the `-f/--file` arg: `make -f foo.mk`

- You can specify any number of *goals* by listing them as positional arguments:

  ```bash
  # typical goals
  make clean all
  ```

- You can run make in another directory with the `-C` arg:

  ```bash
  make -C some/sub/directory
  ```

  Make will run as if it first `cd`'d to that directory.

  *Fun fact- `git` also can be run with `-C` for the same effect!*

---
name: 3-%F0%9F%A7%A9-Anatomy-of-a-Makefile

# 3. 🧩 Anatomy of a Makefile


```makefile
# Comments are prefixed with the '#' symbol

# A make variable "FOO" assignment
FOO = "hello there!"

# A rule creating "test.txt" using the $(FOO) variable
test.txt:
	echo $(FOO) > test.txt
```

---
name: 4-Variables

# 4. Variables

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

---
name: 41-Environment-variables

## 4.1 Environment variables

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
name: 42-Overriding-variables

## 4.2 Overriding variables

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
name: 43-Target-specific-variables

## 4.3 Target-specific variables

---
name: 5-Conditionals

# 5. Conditionals

---
name: 6-Targets-Goals-and-Prerequisites-and-Rules

# 6. Targets (Goals) and Prerequisites and Rules

---
name: 7-Functions

# 7. Functions


---
name: 8-Sub-make

# 8. Sub-make

Invoking Make from a Makefile should be done with the `$(MAKE)` variable:

```makefile
somelib.a:
	$(MAKE) -C path/to/somelib/directory
```

This is often used when building external libraries.

It's also used heavily in Kconfig builds.

Note that this approach has some pitfalls<sup>[0]</sup>:

- recusive invocation can result in slow builds
- tracking prerequisites can be tricky; often you will see `.PHONY` used

<br/>
[0] - http://aegis.sourceforge.net/auug97.pdf

---
name: 9-Phony-targets

# 9. Phony targets


---
name: X-Implicit-rules

# X. Implicit rules

Yr minimal makefile to build a c program:
```makefile
test: test.o
```

---
name: References

# References

- Useful tutorial
 http://maemo.org/maemo_training_material/maemo4.x/html/maemo_Application_Development_Chinook/Chapter_02_GNU_Make_and_makefiles.html
- Nice pictures
 https://www.jfranken.de/homepages/johannes/vortraege/make.en.html
