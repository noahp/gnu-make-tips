class: left, middle

# GNU Make Tips

- [GNU Make Tips](#gnu-make-tips)
- [1. GNU Make](#1-gnu-make)
- [2. Invoking Make](#2-invoking-make)
- [3. 🧩 Anatomy of a Makefile](#3-%f0%9f%a7%a9-anatomy-of-a-makefile)
- [4. Variables](#4-variables)
  - [4.1 Environment variables](#41-environment-variables)
  - [4.2 Overriding variables](#42-overriding-variables)
  - [4.3 Target-specific variables](#43-target-specific-variables)
- [5. Conditionals](#5-conditionals)
- [6. Targets (Goals) and Prerequisites and Rules](#6-targets-goals-and-prerequisites-and-rules)
- [7. Functions](#7-functions)
- [8. Sub-make](#8-sub-make)
- [9. Phony targets](#9-phony-targets)
- [10. Implicit rules](#10-implicit-rules)
- [11. Metaprogramming](#11-metaprogramming)
- [References](#references)

<a class="github-fork-ribbon" href="https://github.com/noahp/gnu-make-tips" data-ribbon="Fork me on GitHub" title="Fork me on GitHub">Fork me on GitHub</a>

---
name: 1-gnu-make

# 1. GNU Make

GNU Make is used to run shell commands.

It is a software automation tool that helps executing repetitive tasks.

It is typically used to transform files into some other form, eg compiling
source code into programs or libraries.

It enables tracking "prerequisites" and running a hierarchy of commands to
produce "targets".

The GNU Make manual is very good:
https://www.gnu.org/software/make/manual/html_node/index.html

When searching for help on Make, using `gnu make xxx` as the search can be
helpful.

---
name: 2-invoking-make

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
name: 3-%f0%9f%a7%a9-anatomy-of-a-makefile

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
name: 4-variables

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
name: 41-environment-variables

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
name: 42-overriding-variables

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
name: 43-target-specific-variables

## 4.3 Target-specific variables

---
name: 5-conditionals

# 5. Conditionals

```makefile
FOO=yolo
ifeq ($(FOO),yolo)
$(info foo is yolo!)
else
$(info foo is not yolo :( )
endif

# testing if a variable is set; unset variables are empty
ifneq ($(FOO),)  # checking if FOO is blank
$(info FOO is unset)
endif

# "complex conditional"
ifeq ($(FOO),yolo)
$(info foo is yolo)
else ifeq ($(FOO), heyo)
$(info foo is heyo)
else
$(info foo is not yolo or heyo :( )
endif
```

---
name: 6-targets-goals-and-prerequisites-and-rules

# 6. Targets (Goals) and Prerequisites and Rules

---
name: 7-functions

# 7. Functions


---
name: 8-sub-make

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
name: 9-phony-targets

# 9. Phony targets


---
name: 10-implicit-rules

# 10. Implicit rules

Yr minimal makefile to build a c program:
```makefile
test: test.o
```

---
name: 11-metaprogramming

# 11. Metaprogramming

`Make`'s `eval` directive allows us to generate Make syntax at runtime:

```makefile
# generate rules for xml->json in some weird world
FILES = $(wildcard inputfile/*.xml)

# create a user-defined function that generates rules
define GENERATE_RULE =
$(eval
# prereq rule for creating output directory
$(1)_OUT_DIR = $(dir $(1))/$(1)_out
$(1)_OUT_DIR:
	mkdir -p $@

# rule that calls a script on the input file and produces $@ target
$(1)_OUT_DIR/$(1).json: $(1) | $(1)_OUT_DIR
	./convert-xml-to-json.sh $(1) $@
)

# add the target to the all rule
all: $(1)_OUT_DIR/$(1).json
endef
```

---

```makefile
# produce the rules
.PHONY: all
all:

$(foreach file,$(FILES),$(call GENERATE_RULE,$(file)))
```

---
name: references

# References

- Useful tutorial
 http://maemo.org/maemo_training_material/maemo4.x/html/maemo_Application_Development_Chinook/Chapter_02_GNU_Make_and_makefiles.html
- Nice pictures
 https://www.jfranken.de/homepages/johannes/vortraege/make.en.html
