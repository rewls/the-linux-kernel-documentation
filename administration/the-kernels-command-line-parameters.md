# The kernel's command-line parameters

- The following is a consolidated list of the kernel parameters as implemented by the __setup(), early_param(), core_param() and module_param() macros and sorted into English Dictionary order (defined as ignoring all punctuation and sorting digits before letters in a case insensitive manner), and with descriptions where known.

<br>

- The kernel parses parameters from the kernel command line up to "`--`"; if it doesn’t recognize a parameter and it doesn’t contain a ‘.’, the parameter gets passed to init: parameters with ‘=’ go into init’s environment, others are passed as command line arguments to init.

- Everything after “--” is passed as an argument to init.

<br>

- Module parameters can be specified in two ways: via the kernel command line with a module name prefix, or via modprobe, e.g.:

    ```
    (kernel command line) usbcore.blinkenlights=1
    (modprobe command line) modprobe usbcore blinkenlights=1
    ```

- Parameters for modules which are built into the kernel need to be specified on the kernel command line.

- modprobe looks through the kernel command line (/proc/cmdline) and collects module parameters when it loads a module, so the kernel command line can be used for loadable modules too.

<br>

- Hyphens (dashes) and underscores are equivalent in parameter names, so:

    ```
    log_buf_len=1M print-fatal-signals=1
    ```

- can also be entered as:

    ```
    log-buf-len=1M print_fatal_signals=1
    ```

- Double-quotes can be used to protect spaces in values, e.g.:

    ```
    param="spaces in here"
    ```

- This document may not be entirely up to date and comprehensive.

- The command "modinfo -p ${modulename}" shows a current list of all parameters of a loadable module.

- Loadable modules, after being loaded into the running kernel, also reveal their parameters in /sys/module/${modulename}/parameters/.

- Some of these parameters may be changed at runtime by the command `echo -n ${value} > /sys/module/${modulename}/parameters/${parm}`.

- The parameters listed below are only valid if certain kernel build options were enabled and if respective hardware is present.

- This list should be kept in alphabetical order.

- The text in square brackets at the beginning of each description states the restrictions within which a parameter is applicable:

    ```
    EARLY   Parameter processed too early to be embedded in initrd.
    ```

- In addition, the following text indicates that the option:

    ```
    KNL     Is a kernel start-up parameter.
    ```

- Note that ALL kernel parameters listed below are CASE SENSITIVE, and that a trailing = on the name of any parameter states that that parameter will be entered as an environment variable, whereas its absence indicates that it will appear as a kernel argument readable via /proc/cmdline by programs running once the system is up.

<br>

- The number of kernel parameters is not limited, but the length of the complete command line (parameters including spaces etc.) is limited to a fixed number of characters.

- This limit depends on the architecture and is between 256 and 4096 characters.

- It is defined in the file ./include/uapi/asm-generic/setup.h as COMMAND_LINE_SIZE.

<br>

- Finally, the [KMG] suffix is commonly described after a number of kernel parameter values.

- These 'K', 'M', and 'G' letters represent the _binary_ multipliers 'Kilo', 'Mega', and 'Giga', equaling 2^10, 2^20, and 2^30 bytes respectively.

- Such letter suffixes can also be entirely omitted:

#### `console=`

- [KNL]

- Output console device and options.

- `tty<n>`: Use the virtual console device `<n>`.

- `ttyS<n>[,options]` `ttyUSB0[,options]`:

    - Use the specified serial port.

    - The options are of the form "bbbbpnf", where "bbbb" is the baud rate, "p" is parity ("n", "o", or "e"), "n" is number of bits, and "f" is flow control ("r" for RTS or omit it).

    - Default is "9600n8".

    <br>

    - See Documentation/admin-guide/serial-console.rst for more information.

    - See Documentation/networking/netconsole.rst for an alternative.

> ##### `earlycon=`

- [KNL,EARLY]

- Output early console device and options.

- When used with no options, the early console is determined by stdout-path property in device tree's chosen node or the ACPI SPCR table if supported by the platform.

- `pl011,<addr>` `pl011,mmio32,<addr>`

    - Start an early, polled-mode console on a pl011 serial port at the specified address.

    - The pl011 serial port must already be setup and configured.

    - Options are not yet supported.

    - If 'mmio32' is specified, then only the driver will use only 32-bit accessors to read/write the device registers.

> ##### `initcall_blacklist=`:

- [KNL]

- Do not execute a comma-separated list of initcall functions.

- Useful for debugging built-in modules and initcalls.

> ##### `root=`

- [KNL]

- Root filesystem

- Usually this is a block device specifier of some kind, see the early_lookup_bdev comment in block/early-lookup.c for details.

> ##### `rootwait`

- [KNL]

- Wait (indefinitely) for root device to show up.

- Useful for devices that are detected asynchronously (e.g. USB and MMC devices).

> ##### `rw`

- [KNL]

- Mount root device read-write on boot
