# Special Permissions in Linux: SUID, SGID, and Sticky Bit

Hello everyone,  
I'm Hassan Ali, a graduate in Computer Science and Artificial Intelligence from Benha University with a grade of Very Good.

Today we will continue with a new topic: High permissions / Special permissions (SUID, SGID, and Sticky Bit).

Okay, you will ask me: what is the difference between the permissions we mentioned in the previous topic yesterday and this topic?  
Standard permissions like read, write, and execute apply only to performing those specific actions.  
However, when a process needs to access a sensitive file owned by root—for example, `/etc/shadow`, which is used when running the `passwd` command to change your password—that file has sensitive permissions and only root can access it.  
Now, you want to change your password, but if you don't have permission, you can't run this command. At the same time, we cannot simply give everyone full access like `777` because it's a sensitive file.  
So, we need a command or a mechanism that gives you temporary root access only while running the command, and drops it again right after!  
This way, when the process runs, the system sees root running it, even though it's actually HassanAli! I know it might not sound simple and you might ask what this means, but just keep reading and I will not let you leave without understanding it!

---

### Let's Go Deep: SUID (Set User ID)

SUID refers to **Set User ID**. It's not a standard permission like read or write; it acts like a special instruction or order for the system.  
It means whenever anyone tries to run the program, the system will not execute it with the permissions of whoever pressed Enter—no! It opens and runs with the **file owner's permissions** for the entire period the program is open.

You will ask me: what is the difference between execute (`x`) and SUID (`s`)?

- **Execute (`x`):** Is the permission to run the file itself.
- **SUID (`s`):** Is the permission of the program in memory (the process) during runtime—meaning what sensitive external files it can access across the system!

---

#### Let's See This Scenario:

Root creates a program named `show_secret` and gives it permission `775`:

- **Owner:** `root` has `7` (read, write, execute)
- **Group:** `engineers` has `7` (read, write, execute)
- **Others:** has `5` (read, execute)
- Me (HassanAli) is a member in the group `engineers`.
- The job of the program is to read a private and sensitive file named `/root/secret.txt`, and this file has permission `600` (meaning only root can access it).

**What happens if we run the program without SUID?**

- **If run as root:** The program will run with root permissions, go read `/root/secret.txt`, the system will allow it because root is executing it, and the content will be printed successfully.
- **If run as HassanAli:** Do you have access to run the program? Yes, because my group has access. But once the program opens in memory (process), the system attributes the operation to HassanAli. When the program tries to read `/root/secret.txt`, Linux checks who is trying to read the secret file. It sees HassanAli and asks: does HassanAli have root permissions on this file? No! The final result will show the message: `Permission denied`.

**Now, what happens if we run the program with SUID?**

- **If run as HassanAli:** Because the file has SUID enabled, Linux ignores who pressed Enter and gives the process the identity of **root**, so the program succeeds!

---

#### How Does the System Know the Real User in Logs?

You will ask me: how does the system know the real user in logs? Can anyone do malicious things hidden behind the root identity?  
Okay, good question! I really appreciate whoever asked this question!  
In the Linux kernel, the system does not save just one ID for each process in memory; it stores two types of IDs inside the process structure (`task_struct`):

1. **Real UID (RUID):** The ID of who actually pressed Enter to run the program (e.g., HassanAli with UID `1000`).
2. **Effective UID (EUID):** The ID that the system checks when trying to read or execute sensitive operations (here it will be `0` for root because SUID is used).

---

### Commands for SUID:

I think you understand SUID now, so let's look at the commands!  
First, the numeric value for SUID is **4000**, and it is added before the standard permission numbers.  
This means if the permission is `755`, when you add SUID it becomes `4755`.  
If you want to add it symbolically, use `u+s`:

```bash
chmod 4755 file
# or
chmod u+s file

```

When you run `ls -l`, you will see the `x` (execute) in the user section change to `s`, which means SUID is active:

```text
-rwsr-xr-x 1 root root ... file

```

There is also a famous command:

```bash
find /usr/bin -perm -4000 -ls

```

- `find /usr/bin`: Searches inside the folder containing system programs.
- `-perm -4000`: Searches specifically for files that have SUID active.
- `-ls`: Displays the results in detailed format like `ls -l`.

> **Most important thing:** Never give SUID to any file unless it's strictly necessary, because it can be very dangerous. Hackers can use it for **Privilege Escalation** to compromise the server!

---

### SGID (Set Group ID)

SGID means **Set Group ID**, and it is used so that any file created inside a folder inherits the group owner of that folder.

Let's see this scenario to understand:

We have a shared folder named `shared_folder` managed by the group `engineers`. In the team, there are two users: HassanAli and Ahmed, both in the group `engineers`.

- **Without SGID:**
  If Hassan creates a new file inside the folder named `script.sh`, by default Linux assigns his primary group to it (`hassanali:hassanali`). When Ahmed goes to edit the file, he will be surprised that the file does not belong to group `engineers` and he cannot edit it!
- **With SGID:**
  When Hassan creates a file, it will automatically inherit the group of the folder by force!

The numeric value of SGID is **2000**.

Commands:

```bash
chmod g+s shared_folder
# or
chmod 2775 shared_folder

```

When you run `ls -ld`, you will see `x` change to `s` in the group section like this:

```text
drwxrwsr-x 2 hassanali engineers 4096 Sep 18 shared_folder

```

---

### Sticky Bit

Now for the final concept: **Sticky Bit** (numeric value **1000**). In simple terms, it provides deletion security in Linux.

In shared folders, anyone in the same group could normally delete or rename files even if they don't own them.

To solve this and ensure that no one can delete or rename a file except its actual owner or root, we use the **Sticky Bit**!

Commands:

```bash
chmod +t shared_folder
# or
chmod 1777 shared_folder

```

When you run `ls -ld`, you will see the execute permission change to `t` in the others section:

```text
drwxrwxrwt 15 root root 4096 Sep 18 10:00 shared_folder

```

---

### Notice: Lowercase vs. Uppercase (`s/S` and `t/T`)

- If you see **`T`** (capital), it means the Sticky Bit is active, but execute (`x`) permission is **missing**.
- If you see **`t`** (lowercase), it means the Sticky Bit is active and execute (`x`) permission **is present**.
- The same rule applies to **`S`** and **`s`** in SUID and SGID!

---

Finally, we have finished this lesson! It might seem simple at first, but when you focus on the topic, you discover very important information you might not have known before.

I hope you understood everything, and see you in the next topic!
