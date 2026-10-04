# Linux Signals, Process Priority, and the /proc Filesystem

Hello everyone,  
I'm Hassan Ali, a graduate of Computer Science from Benha University with a grade of Very Good.

Today, I will talk about a very important topic when you want to terminate any task and improve CPU performance: we will talk about **Signals**, **Process Termination**, **Priority & Niceness**, and the **/proc** filesystem!

---

### 1. What Are Linux Signals?

Okay, let's start with Signals!  
Linux signals are asynchronous notifications sent by the kernel, or by a user through the terminal, to a process. They tell the process to do something, like stop gracefully, terminate immediately, or reload configuration files.

We have 3 famous signals: **SIGTERM**, **SIGKILL**, and **SIGHUP** (the prefix `SIG` refers to "Signal" in each one, okay?).

---

#### A. SIGTERM (Signal Terminate - Value: 15)

- First, **SIGTERM** means "Signal Terminate" and has the value **15**.
- It asks the process for a normal, graceful exit.
- It is sent to a process using the command: `kill -15 <PID>`  
  _(And it is also the default signal sent when you simply write `kill <PID>` without any number!)._
- This signal gives the process enough time to terminate cleanly.
- The process can respond to it, ignore it, or handle it programmatically using a signal handler.
- During this time, the process can complete active user requests, safely close its database connection pool, flush buffers and write data to disk, clean up memory, and then exit cleanly with an exit code.

We use this method when deploying updates to prevent users from seeing a `502 Bad Gateway` error or encountering corrupted data.

---

#### B. SIGKILL (Signal Kill - Value: 9)

- The next signal is **SIGKILL**, and it has the value **9** (which means "Signal Kill").
- It is sent to a process using: `kill -9 <PID>`
- This signal does not go to the process to ask it politely—no, it goes directly to the Linux kernel!
- It tells the kernel: remove this process from the CPU scheduler immediately, and wipe it from the CPU and memory right now!
- What can the process do about it? Nothing! It cannot be caught, blocked, or ignored.
- The kernel kills it instantly without giving it any chance to save open files or close active database connections.

We use this solution as a last resort for frozen or hung processes that refuse to respond to `SIGTERM`.

> **Important note:** When system memory runs out, the kernel invokes the **OOM Killer** (Out-Of-Memory Killer). The kernel forces a `SIGKILL` (9) onto the offending process, which results in the famous exit code **137**:
>
> > Exit Code 137 = 128 + 9 (SIGKILL)

---

#### C. SIGHUP (Signal Hangup - Value: 1)

- The next signal is **SIGHUP**, which has the value **1** (meaning "Signal Hangup").
- Nowadays, it is used to manage background services and web servers like Nginx, Apache, or Prometheus.
- It reloads configuration files without stopping or restarting the server.
- It is sent using: `kill -1 <PID>` or `kill -HUP <PID>`

**What happens when you send SIGHUP to Nginx?**  
Nginx checks the new configuration files to ensure there are no syntax errors, and then applies the new configuration smoothly without dropping active connections.  
This is the mechanism running behind the scenes when you run:

```bash
sudo systemctl reload nginx

```

---

### 2. Common Process Termination Commands

Let's review the commands:

```bash
# Graceful termination
kill -15 <PID>

# Forceful termination
kill -9 <PID>

# Kill all processes by name
killall -9 sleep

```

`killall` ends every process that has the name `sleep`.

#### Using `pkill -f`:

```bash
pkill -f 'my_app'

```

- `pkill` finds processes based on pattern matching.
- The `-f` flag stands for **Full Command-Line Matching**. It expands the search to match against the full argument list, not just the executable name.

**Here is why this is useful:**

If you have a Python application running like this:

`python3 -m my_app.server`

Running `killall my_app` will fail because the process name in the system is actually `python3`.

When you use `pkill -f 'my_app'`, it searches the entire command line, identifies your application, and kills it without killing any other Python apps running on the same server!

---

### 3. CPU Scheduling, Priority, and Niceness

Now let's see how process priority works in the CPU.

To understand this, we need to know how the Linux kernel scheduler handles hundreds of processes competing for the same hardware resources at the same time.

The concept is called **Niceness**:

- When a process is "nice" to other processes, it yields its CPU time to them, meaning it receives a **lower priority**.
- When a process is "less nice" (or negative), it demands more CPU resources, meaning it receives a **higher priority**.

**Nice Values range from -20 to 19:**

- **19** --> Highest niceness = Lowest priority (meaning: let everyone else finish first, and run me only when the CPU is idle).
- **0** --> The default value for regular processes.
- **-20** --> Lowest niceness = Highest priority (the process gets scheduled before normal tasks). Setting a negative nice value requires root privileges so regular users cannot monopolize the CPU.

---

#### Commands for Priority:

To launch a process with a specific nice value:

```bash
nice -n 10 <command>

```

Here, `10` is the nice value. The goal is to prevent heavy batch jobs from consuming all CPU capacity and slowing down web servers or databases serving live users.

If a process is already running and you want to change its priority dynamically:

```bash
renice -n 5 -p <PID>

```

- `-n 5` specifies the new nice value.
- `-p` specifies the Process ID.

> **Security rule:** Normal users can increase the nice value (lowering their own priority). However, giving a process a negative nice value or modifying processes owned by other users requires `sudo`.

---

#### Understanding `NI` vs. `PR` in `top`:

When you inspect processes using `top`, you will see two columns: **NI** and **PR**:

- **NI (Nice Value):** The user-configurable value ranging between `-20` and `19`.
- **PR (Priority):** The internal priority mapped and calculated by the kernel to determine dynamic time-slices on the CPU.

For normal, non-real-time user processes, the formula is:

PR = 20 + NI

_(For example: if NI = 0, then PR = 20)._

---

#### Quick Hands-on Scenario:

First, start a low-priority command in the background:

```bash
nice -n 15 sleep 300 &

```

Next, check the `NI` and `PRI` values:

```bash
ps -o pid,comm,nice,pri -p $!

```

- `-o` means user-defined output format (display only the specified columns).
- `$!` returns the PID of the last background job started in this shell.

Now, adjust its priority while it is running:

```bash
renice -n 5 -p $!

```

You might ask me: _why didn't we need sudo here?_

I appreciate you noticing that! In many Linux systems, as long as you own the process and the nice value remains within the unprivileged range, you can lower the nice value toward `0`. However, setting any negative nice value (like `-1` or `-10`) strictly requires `sudo` privileges!

---

### 4. What Is the `/proc` Pseudo-Filesystem?

Now let's move to our final topic: what is `/proc`, what does it mean, and what does it do?

In Linux, `/proc` is a **pseudo-filesystem**. It does not exist on your physical hard drive; it resides entirely in RAM and is generated on the fly by the Linux kernel. It acts as an interactive real-time window into the kernel and running processes.

When you read files like `/proc/meminfo`, the kernel generates that data dynamically from memory and presents it as plain text.

The `/proc` directory contains numbered folders corresponding to every running process (named after their **PID**), along with key system status files.

---

#### Let's See Some Useful Commands:

Inspect the status of your current shell process:

```bash
cd /proc/$$ && cat status

```

- `$$` is an environment variable holding the PID of your current terminal shell.
- `cat status` prints the runtime kernel state for this process (extracted from its `task_struct`, which we explained earlier in our SUID topic!). You will see fields like `Name`, `State`, `PPID`, `UID`, and `VmRSS` (which represents the actual physical RAM currently used by the process).

Check CPU hardware details:

```bash
cat /proc/cpuinfo

```

This extracts real-time CPU information directly from the kernel, including processor architecture, CPU model name, clock speed, cache size, physical cores, and virtual threads.

Check detailed memory statistics:

```bash
cat /proc/meminfo

```

This shows memory details beyond what standard tools display:

- **MemTotal:** Total usable RAM available to the operating system.
- **MemFree:** Memory completely unused by the system.
- **MemAvailable:** An estimate of how much memory is available for starting new applications without swapping or invoking the OOM Killer.
- **Buffers & Cached:** Storage space used by the kernel to speed up disk reads and writes.

---

We have finished our topic for today! I hope you understood every concept clearly, and see you in the next topic!
