# Linux Filesystem Hierarchy & Basic Navigation

**Hello,**

I am Hassan Ali, a graduate of the Faculty of Computers and Artificial Intelligence at Benha University with a GPA of 3.47. Today, I will explain the **Linux Filesystem Hierarchy**.

The Linux filesystem is designed as an inverted tree. At the very top, we have the root directory (`/`), from which all other subdirectories branch out.

When you run `ls -la /`, you can view all files and directories located at the root level. Below is an overview of what each key subdirectory contains:

---

### Main Subdirectories Overview

1. **/bin** $\rightarrow$ Contains essential user binaries (executable programs) required for basic system operations and recovery, along with some core command scripts.
2. **/boot** $\rightarrow$ Stores static boot loader files, the Linux kernel, and initial RAM disk files that are loaded first when the system starts.
3. **/dev** $\rightarrow$ Houses device nodes representing hardware and virtual I/O files.
4. **/etc** $\rightarrow$ Contains system-wide configuration files (such as network settings, date, time, and system configurations).
5. **/home** $\rightarrow$ Holds personal user directories for individual files and settings.
6. **/lib** $\rightarrow$ Contains essential shared library files and kernel modules required by applications to run correctly.
7. **/sbin** $\rightarrow$ Similar to `/bin`, but reserved for system administration binaries used by the root/admin user.
8. **/tmp** $\rightarrow$ Used for temporary files, which are wiped when the system reboots.
9. **/var** $\rightarrow$ Contains variable data files (such as logs and spool files).
10. **/usr** $\rightarrow$ This is not for individual users, but rather the largest subdirectory after root. It contains a secondary hierarchy of files similar to root (such as `/usr/bin` or `/usr/lib`).

- _You might ask:_ Why aren't these files placed directly in `/`?
- _Answer:_ To keep the filesystem organized rather than storing everything in one massive bulk.
- _What is the difference between `/var/tmp` and `/tmp`?_ Files in `/var/tmp` are **not** wiped when the system boots.

11. **/opt** $\rightarrow$ Contains optional third-party application packages.

---

### Absolute vs. Relative Paths

Now, let's look at the difference between absolute and relative paths. A path generally points to a location in the filesystem:

- **Absolute Path:** The full system path starting from the root directory down to the target file (e.g., `/usr/bin`).
- **Relative Path:** A path defined relative to your current working directory (it can start directly with a directory name, `.`, or `..`). For example, if your current directory is `/usr/lib`, typing `../bin` points directly to `/usr/bin`.

---

### Basic Navigation Commands

First, we need to know where we are or what our current path is, so we use the **`pwd`** command.

- **`cd <path>`**: Navigate to any specified path.
- **`cd -`**: Go back to the previous path.
- **`cd /`**: Go directly to the root directory.
- **`cd ~`**: Go to the current user's home directory.

---

### Conclusion

Finally, we now know many things about the filesystem, the difference between absolute and relative paths, and how to navigate around.

I hope you find this explanation beneficial. Have a good day!
