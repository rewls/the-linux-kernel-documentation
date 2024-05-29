# HOWTO do Linux kernel development

- This is the be-all, end-all document on this topic.

- It contains instructions on how to become a Linux kernel developer and how to learn to work with Linux kernel development community.

## Introduction

- The kernel is written mostly in C, with some architecture-dependent parts written in assembly.

- A good understanding of C is required for kernel development.

- Assembly is not required unless you plan to do low-level development for that architecture.

- The following books are good for reference:

    - "The C Programming Language" by Kernighan and Ritchie [Prentice Hall]

    - "Practical C Programming" by Steve Oualline [O'Reilly]

    - "C: A Reference Manual" by Harbison an dSteele [Prentice Hall]

- This kernel is written using GNU C and the GNU toolchain.

- While it adheres to the ISO C11 standard, it uses a number of extensions that are not featured in the standard.

- The kernel is a freestanding C environment, with no reliance on the standard C library, so some portions of the C standard are not supported.

- It can sometimes be difficult to understand the assumptions the kernel has on the toolchain and the extensions that it uses.

- Please check the gcc info pages (*info gcc*) for some information on them.

## Legal issues

- The Linux kernel source code is released under the GPL.

- Please see the file COPYING in the main directory of the source tree.

- The Linux kernel licensing rules and how to use SPDX identifiers in source code are described in Documentation/process/license-rules.rst.

- For common questions and answers about the GPL, please see:

    - https://www.gnu.org/licenses/gpl-faq.html

## Documentation

- Here is a list of files that are in the kernel source tree that are required reading:

    - Documentation/admin-guide/README.rst

        - This file gives a short background on the Linux kernel and describes what is necessary to do to configure and build the kernel.

        - People who are new to the kernel should start here.

    - Documentation/process/changes.rst

        - This file gives a list of the minimum levels of various software packages that are necessary to build and run the kernel successfully.

    - Documentation/process/coding-style.rst

    - Documentation/process/submitting-patches.rst

        - Other excellent descriptions of how to create patches properly are:

            - “The Perfect Patch”, https://www.ozlabs.org/~akpm/stuff/tpp.txt

            - “Linux kernel patch submission format”, https://web.archive.org/web/20180829112450/http://linux.yyz.us/patch-format.html 

    - Documentation/process/stable-api-nonsense.rst

        - This document is crucial for understanding the Linux development philosophy and is very important for people moving to Linux from development on other Operating Systems.

    - Documentation/process/security-bugs.rst

    - Documentation/process/management-style.rst

        - This is important reading for anyone new to kernel development (or anyone simply curious about it), as it resolves a lot of common misconceptions and confusion about the unique behavior of kernel maintainers.

    - Documentation/process/stable-kernel-rules.rst

    - Documentation/process/kernel-docs.rst

    - Documentation/process/applying-patches.rst

- The kernel also has a large number of documents that can be automatically generated from the source code itself or from ReStructuredText markups (ReST), like this one.

- This includes a full description of the in-kernel API, and rules on how to handle locking properly.

- All such documents can be generated as PDF or HTML by running:

    ```shell
    make pdfdocs
    make htmldocs
    ```

- The documents that uses ReST markup will be generated at Documentation/output. They can also be generated on LaTeX and ePub formats with:

    ```shell
    make latexdocs
    make epubdocs
    ```

## Becoming A Kernel Developer

- If you do not know anything about Linux kernel development, you should look at the Linux KernelNewbies project:

    - https://kernelnewbies.org

- If you do not know where you want to start, but you want to look for some task to start doing to join into the kernel development community, go to the Linux Kernel Janitor’s project:

    - https://kernelnewbies.org/KernelJanitors

- One tool that is particularly recommended is the Linux Cross-Reference project, which is able to present source code in a self-referential, indexed webpage format.

- An excellent up-to-date repository of the kernel code may be found at:

    - https://elixir.bootlin.com/

## The development process

- Linux kernel development process currently consists of a few different main kernel “branches” and lots of different subsystem-specific kernel branches. These different branches are:

    - Linus’s mainline tree

    - Various stable trees with multiple major numbers

    - Subsystem-specific trees

    - linux-next integration testing tree

### Mainline tree¶

- The mainline tree is maintained by Linus Torvalds, and can be found at https://kernel.org or in the repo.

- Its development process is as follows:

    - As soon as a new kernel is released a two week window is open, during this period of time maintainers can submit big diffs to Linus, usually the patches that have already been included in the linux-next for a few weeks.

    - After two weeks a -rc1 kernel is released and the focus is on making the new kernel as rock solid as possible. Most of the patches at this point should fix a regression.

    - A new -rc is released whenever Linus deems the current git tree to be in a reasonably sane state adequate for testing.

        - The goal is to release a new -rc kernel every week.

    - Process continues until the kernel is considered “ready”, the process should last around 6 weeks.

### Various stable trees with multiple major numbers¶

- Kernels with 3-part versions are -stable kernels.

- They contain relatively small and critical fixes for security problems or significant regressions discovered in a given major mainline release.

- Each release in a major stable series increments the third part of the version number, keeping the first two parts the same.

### Subsystem-specific trees

- The maintainers of the various kernel subsystems --- and also many kernel subsystem developers --- expose their current state of development in source repositories.

- Addresses of these subsystem repositories are listed in the MAINTAINERS file.

- Many of them can be browsed at https://git.kernel.org/.

### linux-next integration testing tree

- Before updates from subsystem trees are merged into the mainline tree, they need to be integration-tested.

## Bug Reporting

- The file ‘Reporting issues’ in the main kernel source directory describes how to report a possible kernel bug, and details what kind of information is needed by the kernel developers to help track down the problem.

## Managing bug reports

- To work on already reported bug reports, find a subsystem you are interested in.

- Check the MAINTAINERS file where bugs for that subsystem get reported to; often it will be a mailing list, rarely a bugtracker. 

- Search the archives of said place for recent reports and help where you see fit.

- You may also want to check https://bugzilla.kernel.org for bug reports; only a handful of kernel subsystems use it actively for reporting or tracking, nevertheless bugs for the whole kernel get filed there.

## Mailing lists

- Details on how to subscribe and unsubscribe from the list can be found at:

    - http://vger.kernel.org/vger-lists.html#linux-kernel

- There are archives of the mailing list on the web in many different places.

- For example:

    - https://lore.kernel.org/lkml/

- Most of the individual kernel subsystems also have their own separate mailing list where they do their development efforts.

- See the MAINTAINERS file for a list of what these lists are for the different groups.

- Many of the lists are hosted on kernel.org.

- Though a bit cheesy, the following URL has some simple guidelines for interacting with the list (or any list):

    - http://www.albion.com/netiquette/

- Don’t remove anybody from the CC: list without a good reason, or don’t reply only to the list address.

- Get used to receiving the mail twice, one from the sender and the one from the list, and don’t try to tune that by adding fancy mail-headers, people will not like it.

- Remember to keep the context and the attribution of your replies intact, keep the “John Kernelhacker wrote ...:” lines at the top of your reply, and add your statements between the individual quoted sections instead of writing at the top of the mail.

- If you add patches to your mail, make sure they are plain readable text as stated in Documentation/process/submitting-patches.rst.

- Make sure you use a mail program that does not mangle spaces and tab characters.

## Working with the community

- If there are no responses to your posting, wait a few days and try again, sometimes things get lost in the huge volume.

- What should you not do?

    - expect your patch to be accepted without question

    - become defensive

    - ignore comments

    - resubmit the patch without making any of the requested changes

## Break up your changes

- The Linux kernel community does not gladly accept large chunks of code dropped on it all at once.

- Your proposal should also be introduced very early in the development process, so that you can receive feedback on what you are doing.

## Justify your change

- Along with breaking up your patches, it is very important for you to let the Linux community know why they should add this change.

## Document your change

- When sending in your patches, pay special attention to what you say in the text in your email.

- This information will become the ChangeLog information for the patch, and will be preserved for everyone to see for all time.

- It should describe the patch completely, containing:

    - why the change is necessary

    - the overall design approach in the patch

    - implementation details

    - testing results

- For more details on what this should all look like, please see the ChangeLog section of the document:

    - “The Perfect Patch”, https://www.ozlabs.org/~akpm/stuff/tpp.txt
