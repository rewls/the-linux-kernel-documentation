# Configfs - Userspace-driven Kernel Object Configuration

## What is configfs?

- configfs is a ram-based filesystem that provides the converse of sysfs's functionality.

- Where sysfs is a filesystem-based view of kernel objects, configfs is a filesystem-based manager of kernel objects, or config_items.

- With sysfs, an object is created in kernel(for example, when a device is discovered) and it is registered with sysfs.

- Its attributes then appear in sysfs, allowing userspace to read the attributes via readdir(3)/read(2).

- It may allow some attributes to be modified via write(2).

- The important point is that the object is created and destroyed in kernel, the kernel controls the lifecycle of the sysfs representations, and sysfs is merely a window on all this.

- A configfs config_item is created via explicit userspace operation: mkdir(2).

- It is destroyed via rmdir(2).

- The attributes appear at mkdir(2) time, and can be read or  modified via read(2) and write(2).

- As with sysfs, readdir(3) queries the list of items and/or attributes.

- symlink(2) can be used to group items together.

- Unlike sysfs, the lifetime of the representation is completely driven by userspace.

- The kernel modules backing the items must respond to this.

- Both sysfs and configfs can and should exist together on the same system.

- One is not a replacement for the other.

## Using configfs

- configfs can be compiled as a module or into the kernel.

- You can access it by doing:

    ```sh
    mount -t configfs none /config
    ```

- The configfs tree will be empty unless client modules are also loaded.

- These are modules that register their item types with configfs as subsystems.

- Once a client subsystem is loaded, it will appear as a subdirectory under /config.

- Like sysfs, the configfs tree is always there, whether mounted on /config or not.

- As item is created via mkdir(2).

- The item's attributes will also appear at this time.

- readdir(3) can determine what the attributes are, read(2) can query their default values, and write(2) can store new values.

- Don't mix more than one attribute in one attribute file.

- There are two types of configfs attributes:

    - Normal attributes, which similar to sysfs attributes, are small ASCII text files, with a maximum size of one page (PAGE_SIZE, 4096 on i386).

        - Preferably only one value per file should be used, and the same caveats from sysfs apply.

        - Configfs expects write(2) to store the entire buffer at once.

        - When writing to normal configfs attributes, userspace processes should first read the entire file, modify the portions they wish to change, and then write the entire buffer back.

    - Binary attributes, which are somewhat similar to sysfs binary attributes, but with a few slight changes to semantics.

        - The PAGE_SIZE limitation does not apply, but the whol binary item must fit in single kernel vmalloc'ed buffer.

        - The write(2) calls from user space are buffered, and the attributes' write_bin_attribute method will be invoked on the final close, therefore it is imperative for user-space to check the return code of close(2) in order to verify that the operation finished successfully.

        - To avoid a malicious user OOMing the kernel, there's a per-binary attribute maximum buffer value.

- When an item needs to be destroyed, remove it with rmdir(2).

- An item cannot be destroyed if any other item has a link to it.

- Links can be removed via unlink(2).
