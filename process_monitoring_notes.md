# Linux Process Management and Resource Monitoring Guide

Hello Everyone,  
I'm Hassan Ali, a graduate from Computer Science and Artificial Intelligence of Benha University with a grade of Very Good.

Today we will talk about processes: what a process means, how to see it, how to sort it, and everything related to it!

---

First, what does a process mean?  
A process is a program running and allocating CPU and memory in RAM. In the kernel, it creates a `task_struct` to follow and control the process (we mentioned `task_struct` when we explained SUID!).

You will ask: what is the difference between a program and a process?  
Okay! A program is just code, a script, or a binary file that is not running. When it is executed and running, its name changes to a **process**.

How does the system see a process?  
The system sees a process as a number—it does not see the name of the process, no! It is just a number, okay?  
The name of this number is **PID (Process Identifier)**. It is a unique number, and every process has a different number; you can't see two running processes having the same number at the same time.  
The first process that starts in the system always has **PID 1**, like `systemd` (or init).

And there is another number associated with the process: **PPID (Parent Process Identifier)**.  
In the Linux system, no process is created by itself out of nowhere; it must have another process that ran it.  
So, PPID refers to the PID of the process that launched it.

Like when you open a terminal: it might have PID `1050`.  
If you write inside that terminal the command `sleep 300`:  
The command `sleep` will have a new PID, for example `2040`,  
and its PPID will be `1050`!

Okay!

---

Now let's see some commands:

`echo $$`  
Prints the PID of your current terminal shell.

If we want to see PID and PPID, we use the command `ps -ef`.  
You will see columns like:

- **UID:** The user who runs it.
- **PID:** The Process ID.
- **PPID:** The Parent Process ID.
- **CMD:** The name of the command or program.
- And you will see a column named **TIME**: this does not mean uptime or elapsed time; it means **CPU Execution Time**!

- **ps** refers to: Process Status
- **-e** refers to: Every process
- **-f** refers to: Full-format listing

If you want a specific process for an app like Python, use this command:

```bash
ps -ef | grep python

```

We will explain the pipe `|` and `grep` in the next topic, but for now I want you to remember this style!

If we want to see it as a tree structure starting from the first process running, use the command:

```bash
pstree -p

```

---

If you want a full report for all processes across the whole system and all users, showing CPU and memory usage, use the command:

```bash
ps aux

```

- **a** refers to: all processes, not just for the current user, but for all users.
- **u** refers to: user-oriented format—showing user details like CPU% or MEM%.
- **x** refers to: processes without a TTY—processes not related to a specific terminal session (like background services and daemons).

In `ps aux`, when you run it, you will notice a column named **STAT**.

It contains characters like `S`, `Z`, `R`, `+`, `s`, `l`.

Let's understand what each character refers to:

- **S** means: Interruptible sleep → means the process is sleeping and waiting for an update or event (like clicking on the keyboard, as an example).
- **Z** means: Zombie → the process has finished or died, but its entry is not removed from the process table yet because the parent hasn't read its exit status.
- **R** means: Running / Runnable → the process is running right now or waiting in the CPU queue.

And the other characters are modifiers, like:

- **+** means: Foreground Process Group → means the process is controlling the terminal, and you can't type in the terminal until it finishes.
- **s** means: Session leader → means the process is the leader of the session (like your main bash shell).
- **l** means: Multi-threaded (using cloned threads sharing memory).
  _(Note: Real-time processes can also lock memory pages into RAM using memory locking to prevent swapping, but the character `l` in the STAT column specifically marks a multi-threaded process)._

---

Now if we want to monitor resources live, we will use the tool `top`.

`top` comes by default with Linux; you don't need to install it.

Just write `top` to run it.

After running it:

- If you want to sort by memory usage, press **Shift + M**
- If you want to sort by CPU usage, press **Shift + P**
- Press **1** to show what each CPU core is using
- Press **k** and then write the PID to terminate a process
- Press **q** to exit

There is also a tool called `htop`: it is similar to `top`, but more colorful and interactive, and you need to install it to use it.

---

The last thing is **job control**: it is a great feature in the shell that allows the user to control and run processes in the background or foreground.

- In the **foreground**, the process controls the terminal and prevents the terminal from accepting keyboard input until the process ends.
- In the **background**, the process runs in the back without blocking anything.

To try this, do this command:

```bash
sleep 300 &

```

`sleep 300` → means just wait for 300 seconds.

And `&` → tells the shell to run it in the background.

The output will be like this:

```text
[1] 3421

```

- `[1]` → means the Job ID for the shell.
- `3421` → refers to the PID.

If you write `jobs`, it will return:

```text
[1]+  Running                 sleep 300 &

```

It shows whether the process is running or stopped.

- If you want to change it to the foreground, write:

```bash
fg %1

```

- If you want to stop (pause) it, press:
  `Ctrl + Z`
- If you want to resume it in the background, write:

```bash
bg %1

```

---

Today's topic is finished! I hope you understood everything, and see you soon in a new topic!
