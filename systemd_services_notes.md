# Linux Init Systems (systemd), Daemons, journalctl, and Custom Services

Hello everyone,  
I'm Hassan Ali, a Computer Science graduate from Benha University with a grade of Very Good.

Today we will talk about an important topic: the init system (systemd), Daemons, modern log monitoring with `journalctl`, and in the end we will create a custom systemd service!

---

### 1. What Is the Init System?

First, what is an init system?  
When you click the power button to start your machine, a boot process lifecycle occurs like this:

1. **Hardware / BIOS / UEFI:** Checks the hardware devices and loads the bootloader (like GRUB).
2. **Kernel:** Loads into memory and initializes the CPU, disks, and network devices.
3. **User Space:** When the kernel finishes preparing the hardware, it searches the disk for the very first program in user space to execute.
4. **Init System:** This first program is called the **init system**.

`init` stands for **Initialization**. It is launched by the kernel to start all other system components, launch services, configure networks, and prepare user sessions.

> **Notice:** The init system always gets **PID 1** (remember PID 1 from our previous lesson!).

In modern Linux distributions, PID 1 has become **systemd**.  
You might ask me: _why isn't the kernel PID 1? Why is systemd PID 1?_  
Because every process in Linux needs a parent! The kernel does not run as a normal process in user space; instead, the kernel executes systemd directly and gives it PID 1.

---

### 2. Responsibilities of systemd as PID 1

1. **Parallel Booting:** systemd starts services in parallel based on sockets and dependency mapping, which makes system startup very fast.
2. **Reaping Zombie / Orphan Processes:** If a parent process dies and leaves child processes running, systemd steps in to become the new parent (adopt them) to clean them up and prevent dead processes (zombies) from accumulating in the system.
3. **Lifecycle Monitoring & Self-Healing:** If a web service or database crashes abruptly, systemd can automatically restart it in a fraction of a second.

---

### 3. What Are Daemons?

In any operating system, there are services that run in the background without a graphical interface or terminal, like network services. These programs are called **Daemons**.

So, a **Daemon** means any program or service running silently in the background all the time, without needing an interactive terminal, to provide services to the system and users.

Daemons often have a specific naming pattern: their names usually end with the letter **d**, such as:

- **sshd:** SSH Daemon — the service that listens for and accepts incoming SSH connections on port 22.
- **systemd-journald:** The service responsible for collecting and writing system logs.
- **crond / cron:** The service that executes scheduled background tasks.
- **dockerd:** The service that manages Docker containers on your server.

> **Notice:** A daemon starts automatically with systemd (at boot time) or on demand. It stays alive continuously, and you control it through systemd using the `systemctl` command.

Whenever we have an application that must stay running all the time and boot automatically, we set it up as a daemon.

---

### 4. Hands-on: Applying the Concept with Nginx

Now it's time to apply what we learned!  
We will test this on **Nginx**, an application responsible for web serving and reverse proxying.

#### First Command: Installing Nginx

```bash
sudo apt install -y nginx

```

Let's explain it:

- `sudo`: Used because you need root privileges, as the installer writes files to protected system directories like `/etc/nginx`, `/usr/sbin`, and `/lib/systemd/system`.
- `apt`: The package manager responsible for installing software (similar to setup executables in Windows).
- `-y`: Stands for "yes", meaning answer "yes" automatically to any installation prompt.

**What happens in the background?**

The system downloads and installs the Nginx files, and automatically creates a service unit file named `nginx.service` inside systemd directories.

---

#### Second Command: Checking Service Status

```bash
sudo systemctl status nginx

```

This tells systemd to show us the status of the Nginx service.

You will see output containing:

- **Loaded:** Shows the service file path and whether it loaded successfully or has an error.
- **Active:** Shows the current operational state:
- `active (running)` in green: the service is working properly.
- `inactive (dead)`: the service is currently stopped.
- `failed` in red: the service crashed or failed during start.

- **Main PID:** The Process ID of Nginx in memory.
- **Tasks / Memory:** Current memory consumption and the number of child tasks/threads spawned.
- **Recent Logs:** At the bottom, you will see recent log messages related to this service, pulled directly from `journalctl`.

---

#### Controlling the Service Lifecycle

- **Stop the service:**

```bash
sudo systemctl stop nginx

```

systemd sends the `SIGTERM` (15) signal to Nginx so it can gracefully close active web connections, flush data, and release port 80. The state changes from active to `inactive (dead)`.

- **Start the service:**

```bash
sudo systemctl start nginx

```

systemd executes the start command, allocates ports, CPU, and memory, and changes the state to `active (running)`.

- **Restart the service:**

```bash
sudo systemctl restart nginx

```

Executes a stop followed by a start (Kill & Re-spawn). This terminates current user connections and gives the process a new PID.

- **Reload the configuration:**

```bash
sudo systemctl reload nginx

```

In production environments, when modifying configuration files, we use `reload` instead of `restart`. It sends the `SIGHUP` signal to reload settings without dropping user connections.

---

#### Boot Management: Enable vs. Disable

To make the service start automatically at boot time:

```bash
sudo systemctl enable nginx

```

**What happens in the background?**

This command does not start Nginx immediately. Instead, it creates a **symbolic link (symlink)** pointing from:

`/lib/systemd/system/nginx.service`

to:

`/etc/systemd/system/multi-user.target.wants/nginx.service`

During boot time, when systemd reaches the `multi-user.target` state, it checks that folder, sees the symlink, and automatically launches Nginx!

To stop the service from starting at boot:

```bash
sudo systemctl disable nginx

```

This simply deletes the symlink, so the service will not start automatically on the next reboot.

---

### 5. What Is a Symlink (Symbolic Link)?

After this explanation, you might ask: _what does a symlink mean?_

A **symlink** stands for **symbolic link** (also known as a soft link).

It is simply a shortcut pointing to an original target file or directory. Just like in Windows when you create a desktop shortcut pointing to an application!

**Why use symlinks?**

You don't need to duplicate large files into every directory; you just create a small pointer that references the original path. This saves disk storage and avoids file duplication.

To create a symlink manually:

```bash
ln -s <original_path> <shortcut_name_or_path>

```

To verify whether a file is a symlink:

```bash
ls -l

```

Example output:

`lrwxrwxrwx 1 hassanali hassanali 25 Oct 9 21:30 quick_config.conf -> /var/www/my_website/...`

**You will notice:**

1. The very first character in the permission string is `l` (not `-` for regular files or `d` for directories).
2. An arrow `->` points to the original target path.

> **Question:** What happens if you delete the original file without deleting the symlink?
> The shortcut remains, but it turns into a **broken link**, which often appears highlighted in red in your terminal.

---

### 6. Modern Log Monitoring with `journalctl`

Now let's move to a very important tool: `journalctl`.

`journalctl` is the query utility for `systemd-journald`. It represents a modern alternative to using `tail -f` across traditional plain log files.

systemd logs every event in a centralized, indexed binary database. `journalctl` lets you filter, read, and search these logs without opening files manually.

#### Real-time Monitoring:

```bash
journalctl -u nginx -f

```

- `-u`: Stands for unit, specifying that we only want to read logs for `nginx.service`. This isolates Nginx logs from the rest of the system.
- `-f`: Stands for follow (similar to `tail -f`), keeping the terminal open to display incoming log lines in real time.

#### Filtering by Time:

```bash
# Logs from the past hour
journalctl -u nginx --since "1 hour ago"

# Logs within a specific date and time range
journalctl -u nginx --since "2026-10-09 20:00:00" --until "2026-10-09 21:00:00"

# Logs from today only
journalctl -u nginx --since today

```

#### Essential Troubleshooting Flags:

- **Filter by priority (errors only):**

```bash
journalctl -u nginx -p err

```

- **Filter by boot session (logs since last reboot):**

```bash
journalctl -u nginx -b

```

- **View the most recent lines (e.g., last 30 lines):**

```bash
journalctl -u nginx -n 30

```

---

### 7. Creating a Custom systemd Service

Before creating our service, remember the **Filesystem Hierarchy Standard (FHS)** and what `/etc` is for: `/etc` stores system configuration files.

In systemd, there are 3 important directory locations:

1. `/lib/systemd/system/`: Default service unit files provided by installed packages (like Nginx via apt).
2. `/run/systemd/system/`: Dynamic, runtime units created in memory while the system is running.
3. `/etc/systemd/system/`: The administrator's directory for custom services and configuration overrides.

---

#### Step 1: Create the Unit File

Create a file ending with `.service` inside `/etc/systemd/system/`:

```bash
sudo nano /etc/systemd/system/dummy.service

```

Add the following content:

```ini
[Unit]
Description=My Dummy Test Service
After=network.target

[Service]
Type=simple
ExecStart=/bin/bash -c "while true; do echo 'Dummy Service is alive' >> /tmp/dummy.log; sleep 5; done"
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target

```

#### Understanding the Configuration:

- `[Unit]`: Defines metadata and dependency relationships.
- `Description`: A descriptive label shown when running `systemctl status`.
- `After=network.target`: Tells systemd to delay starting this service until network capabilities are initialized.

- `[Service]`: Controls execution and runtime behavior.
- `Type=simple`: The process declared in `ExecStart` is the main process of this service.
- `ExecStart`: The exact command executed by systemd. Here, a bash loop appends a log entry every 5 seconds.
- `Restart=always`: Configures self-healing; if the process crashes or gets killed, systemd automatically restarts it.
- `RestartSec=3`: Waits 3 seconds before attempting a restart.

- `[Install]`: Defines installation targets for boot configuration.
- `WantedBy=multi-user.target`: Connects this unit to the standard multi-user runlevel when you execute `systemctl enable`.

---

#### Step 2: Reload the Daemon

After saving the file, systemd does not detect it yet because it reads dependency trees into memory only during boot time.

Instead of rebooting the machine, run:

```bash
sudo systemctl daemon-reload

```

This forces PID 1 to rescan `/etc/systemd/system/` and load new or modified unit files immediately!

---

#### Step 3: Run and Test the Service

- **Start the service:**

```bash
sudo systemctl start dummy.service

```

- **Check its status:**

```bash
sudo systemctl status dummy.service

```

- **Stream its logs in real time:**

```bash
journalctl -u dummy.service -f

```

Now you know how init systems work, how daemons function in the background, how to query logs using `journalctl`, and how to build your own custom systemd service with self-healing capabilities!
