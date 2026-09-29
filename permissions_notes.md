# Linux File Permissions and Ownership

Hello Everyone,  
I'm Hassan Ali, a graduate in Computer Science and Artificial Intelligence from Benha University with a GPA of 3.47.  
Today we will talk about an important topic in security: **Permissions in Linux**.

First, we must know and remember this rule:

> **"Least privilege = More security"**

When you set permissions, always remember these words!

---

Let's start with: what does permission mean?  
It determines what a user can do on files or folders, like read, write, or execute.  
We refer to it by symbols:

- **r** means read
- **w** means write
- **x** means execute (which means run)
- **-** means nothing (no access)

When we run `ls -l`, it displays these permissions.  
We can also refer to read, write, and execute by numbers:

- **Read** is `4` ($2^2$)
- **Write** is `2` ($2^1$)
- **Execute** is `1` ($2^0$)
- **-** or nothing means `0`

---

Permissions are divided into 3 types:

1. **User (`u`)**
2. **Group (`g`)**
3. **Others (`o`)** (and "others" means anyone outside the user and the group)

This means:

- If I see a file and I'm not the owner and not in the group, the system will apply the permissions of **Others** to me.
- If I am in the group that the file belongs to, it will apply the permissions of the **Group** to me.

---

### How to Calculate Permissions:

If we want:

- `rwx` = $4 + 2 + 1 = 7$ $\rightarrow$ means full access
- `rw-` = $4 + 2 + 0 = 6$ $\rightarrow$ means read and write without execute
- `r-x` = $4 + 0 + 1 = 5$ $\rightarrow$ means read and execute without write
- `r--` = $4 + 0 + 0 = 4$ $\rightarrow$ means read only
- `---` = $0 + 0 + 0 = 0$ $\rightarrow$ does not have any access

---

Now let's go back to the command `ls -l` to display permissions and see how it appears:

```text
-rwxr-xr--

```

Let's explain this line in order:

- The first `-` means the type, like a regular file. If it is a folder, we will see `d`, which means Directory.
- After that, we divide the line into three terms: the `rwx` term, the `r-x` term, and the `r--` term.

Let's break them down:

- **First term (`rwx`)**: means User (owner), and here it has full access ($7$).
- **Second term (`r-x`)**: means Group, and here it has read and execute only ($5$).
- **Third term (`r--`)**: means Others, and here it has read only ($4$).

Now, I want to test something: if we want to convert this line to numbers, how will it appear?

It will be **`754`**: full access for user, read and execute for group, and read only for others.

---

Now, if we want to change permissions on a file or folder, what is the command?

We will use **`chmod`** (which means "change mode").

Here are the most common commands used in DevOps:

- `chmod 755 script.sh`
  This is the most familiar command for executable scripts and directories:
- Owner: read, write, execute.
- Group & Others: read, execute.

- `chmod 644 config.conf`
  This is used for most config files or text files:
- Owner: read, write.
- Group & Others: read only.

- `chmod 600 id_ed25519`
  You must use this command for SSH keys and secret data, because Linux will refuse to use an SSH key if anyone else can read it besides the owner:
- Owner: read, write.
- Group & Others: no access at all.

- `chmod 777 folder`
  **Don't use this command in production or on servers!** Because it cancels all security:
- Owner, group, and others will have complete access.

---

Now, if you notice: when we change permissions in octal numbers, we must provide all three digits. If you want to change only "others" to read-only without updating user and group, you can't just write `chmod 4` (because Linux will interpret it as `004` and strip permissions from the owner and group!).

In this case, octal mode forces you to rewrite everything. But there is another method called **Symbolic Mode** that gives you the flexibility to change only what you need.

_(Notice: the previous numeric method is called Octal mode)._

You remember when we divided permissions into three categories: User, Group, and Other? And beside each one we have a symbol: `u` for user, `g` for group, and `o` for others.

This is what we will use!

Now let's see the command:

```bash
chmod u+x,g-w,o-rwx demo_file

```

- Here, `u+x` adds execute permission to the user.
- We use `,` to separate between user, group, and others.
- `g-w` removes write permission from the group.
- `o-rwx` removes all permissions from others.

`+` means add, and `-` means remove.

We use symbolic mode whenever we want to make a quick, specific change without rewriting all permissions!

---

Now let's talk about changing the owner or group of a file: what must we do?

We will use the command **`sudo chown`** (which means "change owner"). We use `sudo` because this command requires root/administrative permissions:

```bash
sudo chown devops_user:engineers demo_file

```

- `devops_user` is the new owner.
- `engineers` is the new group.
- We separate them with `:`.

If we want to change only the group, just write:

```bash
sudo chown :engineers demo_file

```

_(Or you can use `sudo chgrp engineers demo_file` without writing `:`)._

If we want to change only the user, write:

```bash
sudo chown devops_user demo_file

```

_(Notice we don't write `:` here)._

When you run `ls -l demo_file` before the change, you will see:

```text
-rw-r--r-- 1 root root 0 Sep 17 demo_file

```

And after the change, you will see:

```text
-rw-r--r-- 1 devops_user engineers 0 Sep 17 demo_file

```

And if we want to change the user and group for a folder and every file inside it recursively, we use `-R`:

```bash
sudo chown -R devops_user:engineers my_project/

```

---

Now let's talk about default permissions: what if I want newly created files or folders to use permissions I decided beforehand, instead of the system defaults?

This is called **`umask`** (user mask). It acts like a filter that masks out permissions from the default system base permissions.

- The default base permission is **`777`** for folders.
- The default base permission is **`666`** for files (files never get execute permission by default for safety).

Here, `umask` removes specific bits:

$$\text{Base Permission} - \text{umask value} = \text{Final Permission}$$

For example, the standard default value of `umask` is usually **`022`**:

- For a new folder: $777 - 022 = \mathbf{755}$
- For a new file: $666 - 022 = \mathbf{644}$

Useful commands:

```bash
umask        # Shows the current value of umask
umask 077    # Changes umask so newly created files and folders are private to the owner only

```

---

After this lesson, I think you might want to take a rest and apply these commands manually on your terminal to remember them!

I hope you understood everything, and see you in a new topic!
