# Understanding Linux Environment Variables, Aliases, and $PATH

Hello everybody,  
I'm Hassan Ali, a graduate in Computer Science from Benha University with a grade of Very Good.

Today we will talk about a simple topic, but it is very important to know: **Environment Variables**—what they mean, why they are important, and how to use them!

---

### 1. What Does an Environment Variable Mean?

First, what does an environment variable mean?  
They are variables stored in memory that the operating system and running programs use to know essential information, such as:

- Who the current user is.
- Where your home directory is located.
- Where executable programs are located and ready to run.

When calling any variable in the command line (shell), we prepend `$` before its name so the shell reads it and replaces it with its actual value.

The concept of environment variables is based on a **Key:Value** pair:

- **Key:** What you write and reference in the shell/command line.
- **Value:** The real value or path that gets evaluated or executed.

---

### 2. Aliases vs. Export

#### What is an Alias?

Imagine you have a long command like:

```bash
ls -lah

```

This command shows all file details (including hidden files) in a human-readable format. But you don't want to type this long command every time—you just want to type `ll`.

So, you make an alias where the key is `ll` and the value is `ls -lah`. Whenever you type `ll`, the shell will automatically execute `ls -lah`.

This is called an **alias**:

```bash
alias ll="ls -lah"

```

_(Notice: Make sure the alias name was not used before to avoid conflicts with existing system commands!)._

#### What is Export?

`export` uses the same key-value idea, but when you reference it in the shell, you must write `$` before the variable name, as mentioned earlier.

For example, if you write:

```bash
export DEVOPS_TOOL="Docker"

```

Now in your shell, if you type `$DEVOPS_TOOL` or `echo $DEVOPS_TOOL`, the shell will replace it with `Docker`.

> **The difference between alias and export:**
> An **alias** replaces an entire command shortcut directly without `$`, while an **exported environment variable** holds data/values that you retrieve using the `$` prefix.

---

### 3. Inspecting Environment Variables

Let's look at some important inspection commands:

#### `printenv`

This command prints all environment variables available in the current shell directly to the screen:

```bash
printenv

```

If you want to search for a specific variable (like `USER`), you can pipe it to grep:

```bash
printenv | grep USER

```

#### Common Standard Variables:

- **`echo $USER`**
  `echo` means print. `$USER` stores the username of the account you are currently logged in with.
- **`echo $HOME`**
  `$HOME` stores the absolute path of the current user's home directory (e.g., `/home/hassanali`).
- **`echo $PATH`**
  `$PATH` stores a list of directory paths where executable programs live.

---

### 4. Deep Dive: How Does `$PATH` Work?

When you type `ls`, `docker`, or `python3` and press Enter:

1. **How does the system know where the executable file lives on disk?**
2. The system searches through the directory paths defined in `$PATH` in order from **left to right**, separated by a colon (`:`).
3. In the first path where it finds the executable file, it will run it immediately. If it checks every path and does not find the binary, you will see the famous error:
   `Command not found`

**Example `$PATH` format:**

```text
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

```

It is very important to understand this: whenever you install a tool like `terraform` or `kubectl` and see `command not found`, you know immediately that its executable directory was not added to `$PATH`!

---

### 5. Local (Shell) Variables vs. Exported Variables

If you define a variable directly in your current shell:

```bash
APP_ENV=development

```

_(Notice: Never write spaces before or after the `=` sign!)._

When you write it this way, you create a **local shell variable**. It has a restricted scope:

- If you close the terminal and open it again, the variable is deleted.
- Any child process (sub-process) cannot see it; only the current parent shell can access it.

#### Making it Available to Sub-Processes:

A sub-process is any program launched from your current shell (which makes the shell its parent).

To pass the variable down to child processes, you must `export` it:

```bash
export APP_ENV=development

```

---

### 6. Making Variables and Aliases Persistent

Even with `export`, if you close the terminal, the variable will disappear because it only lived in memory.

To make it persistent forever across all terminal sessions, we must add it to the file:

```bash
~/.bashrc

```

The file `~/.bashrc` is a startup script that runs automatically every time you open a new Bash terminal. Linux reads this file line by line and executes all commands, aliases, and variables inside it automatically!

#### Steps to add it:

1. Open the file using a text editor (like `nano`):

```bash
nano ~/.bashrc

```

2. Scroll to the very last line and append:

```bash
export DEVOPS_TOOL="Docker"
alias ll="ls -lah"

```

3. Save and exit: press **Ctrl + O**, then hit **Enter**, then press **Ctrl + X**.

#### Applying the Changes Immediately:

The file is saved, but you will notice nothing has changed yet in your current window!

You don't need to close and reopen your terminal. Simply reload the file into memory using:

```bash
source ~/.bashrc

```

This command forces your active shell to reread the file, execute its contents into memory right now, and apply all changes immediately!

---

Now we have finished today's lesson! I hope you understood everything, and see you next time!
