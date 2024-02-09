# Linux Kernel Coding Style

> https://www.kernel.org/doc/html/v6.8-rc2/process/coding-style.html

- Not read GNU coding standards.

## 1) Indentation

- Tabs are 8 characters, and thus indentations are also 8 characters.

- If you need more than 3 levels of indentation, you're screwed anyway, and should fix your program.

- The preferred way to ease multiple indentation levels in a switch statement is to align the `switch` and its subordinate `case` labels in the same column instead of `double-indenting` the `case` labels.

- Don't put multiple statements on a single line.

- Always uses braces for multiple statements.

- Don't put multiple assignments on a single line either.

- Outside of comments, documentation and except in Kconfig, spaces are never used for indentation.

- Don't leave whitespace at the end of lines.

    - `/\s\+$`

## 2) Breaking long lines and strings

- The preferred limit on the length of a single line is 80 columns.

- Descendants are always substantially shorter than the parent and are placed substantially to the right.

- A very commonly used style is to align descendants to a function open parenthesis.

## 3) Placing Braces and Spaces

- The preferred way, as shown to us by the prophets Kernighan and Ritchie, is to put the opening brace last on the line, and put the closing brace first.

- Functions have the opening brace at the beginning of the next line. K&R are **right**.

- The closing brace is empty on a line of its own, **except** in the cases where it is followed by a continuation of the same statement, it a `while` in a do-statement or an `else` in an if-statement. K&R.

- Do not unnecessarily use braces where a single statement will do.

- This does not apply if only one branch of a conditional statement is a single statement.

- Use braces when a loop contains more than a single simple statement:

```c
while (condition) {
	if (test)
		do_something();
}
```

### 3.1) Spaces

- Use a space after (most) keywords.

- The notable exceptions are sizeof, typeof, alignof, and \_\_attribute\_\_, which look somewhat like functions (and are usually used with parentheses in Linux, although they are not required in the language).

- Do not add spaces around (inside) parenthesized expressions.

- When declaring pointer data or a function that returns a pointer type, the preferred use of `*` is adjacent to the data name or function name and not adjacent to the type name.

- Use one space around (on each side of) most binary and ternary operators.

- but no space after unary operators

- no space before the postfix increment & decrement unary operators

- no space after the prefix increment & decrement unary operators

- and no space around the `.` and `->` structure member operators.

- Git will warn you about patches that introduce trailing whitespace, and can optionally strip the trailing whitespace for you.

## 4) Naming

- GLOBAL variables (to be used only if you **really** need them) need to have descriptive names, as do global functions.

- Mixed-case names are frowned upon.

- Encoding the type of a function into the name (so-called Hungarian notation) is asinine.

- LOCAL variable names should be short, and to the point. `i`, `tmp`

- For symbol names and documentation, avoid introducing new usage of 'master / slave' (or 'slave' independent of 'master') and 'blacklist / whitelist'.

- Recommended replacements for 'master / slave' are:

    - '{primary,main} / {secondary,replica,subordinate}' '{initiator,requester} / {target,responder}' '{controller,host} / {device,worker,proxy}' 'leader / follower' 'director / performer'

- Recommended replacements for 'blacklist/whitelist' are:

    - 'denylist / allowlist' 'blocklist / passlist'

## 5) Typedefs

- Typedefs are useful only for:

    1. Totally opaque objectes (where the typedef is actively used to **hide** what the object is).

        - opaque objects that you can only access using the proper accessor functions.

    1. Clear integer types, where the abstraction **helps** avoid confusion whether  it is `int` or `long`.

    1. When you usesparse to literally create a **new** type for type-checking.

    1. New types which are identical to standard C99 types, in certain exceptional circumstances.

        - The Linux-specific `u8/u16/u32/u64` types and their signed equivalents which are identical to standard types are permitted.

    1. Types safe for use in userspace.

- In general, a point, or a struct that has elements that can reasonably be directly accessed should **never** be a typedef.

## 6) Functions

- Functions should be short and sweet, and do just one thing.

- They should fit on one or two screenfuls of text (the ISO/ANSI screen size is 80x24, as we all know), and do one thing and do that well.

- The maximum length of a function is inversely proportional to the complexity and indentation level of that function.

- Local variables shouldn't exceed 5-10, or you're doing something srong.

- In source files, separate functions with one blank line.

- If the function is exported, the **EXPORT** macro for it should follow immediately after the closing function brace line.

### 6.1) Function prototypes

- In function prototypes, include parameter names with their data types.

- Do not use the `extern` keyword with function declarations as this makes lines longer and isn't strictly necessary.

- When writing function prototypes, please keep the [order of elements regular](https://lore.kernel.org/mm-commits/CAHk-=wiOCLRny5aifWNhr621kYrJwhfURsa0vFPeUEm8mF0ufg@mail.gmail.com/).

```c
__init void * __must_check action(enum magic value, size_t size, u8 count,
				  char *fmt, ...) __printf(4, 5) __malloc;
```

- The preferred order of elements for a function prototype is:

    - storage class (below, `static __always_inline`, noting that `__always_inline` is technically an attribute but is treated like `inline`)

    - storage class attributes (here, `__init` -- i.e. section declarations, but also things like `__cold`)

    - return type

    - return type attributes (here, `__must_check`)

    - function name

    - function parameters

    - function parameter attributes (here, `__printf(4, 5)`)

    - function behavior attributes (here, `__malloc`)

- Note that for a function **definition**, the compiler does not allow function parameter attributes after the function parameters.

- In these cases, they should go after the storage class attributes:

```c
static __always_inline __init __printf(4, 5) void * __must_check action(
	enum magic value, u8 count, char *fmt, ...) __malloc
{
	...
}
```

## 7) Centralized exiting of functions

- The goto statement comes in handy when a function exits from multiple locations and some common work such as cleanup has to be done.

- Choose label names which say what the goto does or why the goto exists. `out_free_buffer:`

- The rationale for using gotos is:

    - unconditional statements are easier to understanad and follow

    - nesting is reduced

    - errors by not updating individual exit points when making modifications are prevented

    - saves the compiler work to optimize redundant code away

- A common type of bug to be aware of is `one err bugs` which look like this:

```c
err:
	kfree(foo->bar);
	kfree(foo);
	return ret;
```

-  The bug in this code is that on some exit paths `foo` is NULL.

```c
err_free_bar:
	kfree(foo->bar);
err_free_foo:
	kfree(foo);
	return ret;
```

- Ideally you should simulate errors to test all exit paths.

## 8) Commenting

- NEVER try to explain HOW your code works in a comment.

- Also, try to avoid putting comments inside a function body.

- You can make small comments to note or warn about something particularly clever (or ugly), but try to avoid excess.

- Instead, put the comments at the head of the function, telling people what it does, and possibly WHY it does it.

- When commenting the kernel API functions, please use the kernel-doc format.

- The preferred style for long (multi-line) comments is:

```c
/*
 * This is the preferred style for multi-line
 * comments in the Linux kernel source code.
 * Please use it consistently.
 *
 * Description:  A column of asterisks on the left side,
 * with beginning and ending almost-blank lines.
 */
```

- For files in net/ and drivers/net/ the preferred style for long (multi-line) comments is a little different.

```c
/* The preferred comment style for files in net/ and drivers/net
 * looks like this.
 *
 * It is nearly the same as the generally preferred comment style,
 * but there is no initial almost-blank line.
 */
```

- It's also important to comment data, whether they are basic types or derived types.

- To this end, use just one data declaration per line (no commas for multiple data declarations).

## 9) You've made a mess of it

- Use `indent -kr -i8` (stands for K&R, 8 character indents`), or use `scripts/Lindent`, which indents in the latest style.

- `indent` has a lot of options, and especially when it comes to comment reformatting you may want to take a look at the man page.

- Note that you can also use the clang-format tool to help you with these rules, to quickly re-format parts of your code automatically, and to review full files in order to spot coding style mistakes, typos and possible improvements. It is also handy for sorting #includes, for aligning variables/macros, for reflowing text and other similar tasks.

- Some basic editor settings, such as indentation and line endings, will be set automatically if you are using an editor that is compatible with EditorConfig.

## 10) Kconfig configuration files

- Lines under a `config` definition are indented with one tab, while help text is indented an additional two spaces

- Seriously dangerous features (such as write support for certain filesystems) should advertise this prominently in their prompt string:

```
config ADFS_FS_RW
      bool "ADFS write support (DANGEROUS)"
      depends on ADFS_FS
      ...
```

## 11) Data structures

- Data structures that have visibility outside the single-threaded environment they are created and destroyed in should always have reference counts.

- Reference counting means that you can avoid locking, and allows multiple users to have access to the data structure in parallel - and not having to worry about the structure suddenly going away from under them just because they slept or did something else for a while.

- Note that locking is **not** a replacement for reference counting. Locking is used to keep data structures coherent, while reference counting is a memory management technique.

- Many data structures can indeed have two levels of reference counting, when there are users of different `classes`.

- The subclass count counts the number of subclass users, and decrements the global count just once when the subclass count goes to zero.

## 12) Macros, Enums and RTL

- Names of macros defining constants and labels in enums are capitalized.

- CAPITALIZED macro names are appreciated but macros resembling functions may be named in lower case.

- Generally, inline functions are preferable to macros resembling functions.

- Macros with multiple statements should be enclosed in a do - while block

- Things to avoid when using macros:

    1. macros that affect control flow

    2. macros that depend on having a local variable with a magic name

    3. macros with arguments that are used as l-values

    4. forgetting about precedence

    5. namespace collisions when defining local variables in macros resembling functions

- The cpp manual deals with macros exhaustively.

- The gcc internals manual also covers RTL which is used frequently with assembly language in the kernel.

## 13) Printing kernel messages

- Make the messages concise, clear, and unambiguous.

- Kernel messages do not have to be terminated with a period.

- Printing numbers in parentheses (%d) adds no value and should be avoided.

- There are a number of driver model diagnostic macros in <linux/dev_printk.h> which you should use to make sure messages are matched to the right device and driver, and are tagged with the right level

## 14) Allocating memory

- The kernel provides the following general purpose memory allocators: `kmalloc()`, `kzalloc()`, `kmalloc_array()`, `kcalloc()`, `vmalloc()`, and `vzalloc()`.

- The preferred form for passing a size of a struct is the following:

```c
p = kmalloc(sizeof(*p), ...);
```

- Casting the return value which is a void pointer is redundant.

- The preferred form for allocating an array is the following:

```c
p = kmalloc_array(n, sizeof(...), ...);
```

- The preferred form for allocating a zeroed array is the following:

```c
p = kcalloc(n, sizeof(...), ...);
```

- Both forms check for overflow on the allocation size n * sizeof(...), and return NULL if that occurred.

- These generic allocation functions all emit a stack dump on failure when used without __GFP_NOWARN so there is no use in emitting an additional failure message when NULL is returned.

## 15) The inline disease

- Abundant use of the inline keyword leads to a much bigger kernel, which in turn slows the system as a whole down, due to a bigger icache footprint for the CPU and simply because there is less memory available for the pagecache.

- A reasonable rule of thumb is to not put inline at functions that have more than 3 lines of code in them.

- An exception to this rule are the cases where a parameter is known to be a compiletime constant, and as a result of this constantness you know the compiler will be able to optimize most of your function away at compile time. 

- gcc is capable of inlining functions that are static and used only once automatically without help.

## 16) Function return values and names

- If the name of a function is an action or an imperative command, the function should return an error-code integer.  If the name is a predicate, the function should return a "succeeded" boolean.

- Generally functions whose return value is the actual result of a computation indicate failure by returning some out-of-range result. NULL or ERR_PTR

## 17) Using bool

- The Linux kernel bool type is an alias for the C99 _Bool type.

- When working with bool values the true and false definitions should be used instead of 1 and 0.

- bool function return types and stack variables are always fine to use whenever appropriate.

- Do not use bool if cache line layout or size of the value matters, as its size and alignment varies based on the compiled architecture.

- If a structure has many true/false values, consider consolidating them into a bitfield with 1 bit members, or using an appropriate fixed width type, such as u8. Similarly for function arguments.

## 18) Don't re-invent the kernel macros

- The header file include/linux/kernel.h contains a number of macros that you should use, rather than explicitly coding some variant of them yourself.

## 19) Editor modelines and other cruft

- Do not include configuration information embedded in source files.

## 20) Inline assembly

- Don't use inline assembly gratuitously when C can do the job.

- Consider writing simple helper functions that wrap common bits of inline assembly, rather than repeatedly writing them with slight variations.

- Large, non-trivial assembly functions should go in .S files, with corresponding C prototypes defined in C header files.

-  You may need to mark your asm statement as volatile, to prevent GCC from removing it if GCC doesn't notice any side effects.

- When writing a single inline assembly statement containing multiple instructions, put each instruction on a separate line in a separate quoted string, and end each string except the last with \n\t to properly indent the next instruction in the assembly output.

## 21) Conditional Compilation

- Wherever possible, don't use preprocessor conditionals (#if, #ifdef) in .c files; doing so makes code harder to read and logic harder to follow.

-  Instead, use such conditionals in a header file defining functions for use in those .c files, providing no-op stub versions in the #else case, and then call those functions unconditionally from .c files.

- Prefer to compile out entire functions, rather than portions of functions or portions of expressions.

- Rather than putting an ifdef in an expression, factor out part or all of the expression into a separate helper function and apply the conditional to that function.

- If you have a function or variable which may potentially go unused in a particular configuration, and the compiler would warn about its definition going unused, mark the definition as __maybe_unused rather than wrapping it in a preprocessor conditional.

- Within code, where possible, use the IS_ENABLED macro to convert a Kconfig symbol into a C boolean expression, and use it in a normal C conditional.

- You still have to use an #ifdef if the code inside the block references symbols that will not exist if the condition is not met.

- At the end of any non-trivial #if or #ifdef block (more than a few lines), place a comment after the #endif on the same line, noting the conditional expression used.
