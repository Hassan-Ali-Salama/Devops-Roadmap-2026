# Inspecting Log Files in Linux

Hello,  
I'm Hassan Ali, a graduate in Computer Science and Artificial Intelligence from Benha University with a GPA of 3.47.

Today we will talk about how to view or print the content of files without opening a text editor, okay?

Now let's start with the `cat` command. It is the best command to start with in this topic because it is considered one of the first commands in Unix.

Try writing this line:

```bash
cat /var/log/dpkg.log

```

This will print the content of the file. But if the file has a huge content, this is not the best way to use `cat`.

There are commands more powerful than `cat`, like `more` or `less`.

Why? Because it is not good or comfortable to scroll up and down in your terminal buffer and keep moving your hand to scroll.

When you use `more` and `less`, you navigate to the next page by pressing **Space**, and go back by pressing **b**.

`more` and `less` are very similar, but `less` is used more with big files. Why? Because `less` doesn't load the whole file into RAM at once; it only reads and buffers the page you are viewing on demand, which is super fast and safe!

In `less`, you can search by typing `/word` and pressing Enter.

You can exit from `more` or `less` by pressing **q**.

**Examples:**

```bash
more /var/log/dpkg.log
less /var/log/dpkg.log

```

If you ask about this path or what it means, that is a good question! I appreciate it and I love people who ask about anything that looks new.

Okay, the answer to this question: `/var/log/dpkg.log` is the file where installation logs and package dependencies are stored. It is an important path!

---

And now, if we have a file with big content and we just want to print or see the first 5 lines, the first 6 lines, or the first N lines:

You will use the command:

```bash
head -n 5 /var/log/dpkg.log

```

You can change the number to any other value, and `-n` refers to the number of lines.

This is if you want to see lines from the beginning. Now, if you want to see lines from the end of the file, like printing the last 5 lines, in this case we will use `tail`:

```bash
tail -n 5 /var/log/dpkg.log

```

---

Now let's try some hands-on practice: reading live logs!

Open two terminals:

In the first terminal, we will read from the file `dpkg.log`.

Now, the first question: which command will we use?

To answer this question, we should ask another question: which task do you want to do by using the command?

- I want to read the file. Why? To see new logs in the file.
- How will the logs appear? They will appear as new lines at the end of the file.

Oh, okay! Now you know you need to read the end lines of the file. But if you use the command `tail -n`, it will print the end lines and exit, not live. And we need it live! I mean, when we install something, I want to see the new logs at the same time.

Okay, we will use the command `tail`, but don't use `-n`. We will use `-f`, and `f` here means **follow**:

```bash
tail -f /var/log/dpkg.log

```

_(Tip: In real DevOps work, you can also use `tail -F` with a capital `F`. It does the same follow action, but it keeps tracking the log even if the system rotates and re-creates the log file!)._

Now, I want you to ask yourself: what is the meaning of **Log Rotation**, and what is the difference between using `-f` and `-F`?

Okay, I will explain it!

When we use Nginx, it generates and saves many logs every moment. If the system stored everything in just one single file, the storage disk would fill up very fast!

Because of that, the system runs a process called **Rotation**, for example using a tool like `logrotate`. It creates a lifecycle for specific files, like `nginx.log`.

Now, here are the steps of this lifecycle:

1. **Archive:** It changes the file name from `nginx.log` to `nginx.log.1`.
2. **Start Active File:** It creates a fresh, new file named `nginx.log` to start writing incoming logs immediately.
3. **Compress:** It compresses older archived files to save free disk space. So over time, `nginx.log.1` is converted to `nginx.log.2.gz`.
4. **Cleanup and Remove:** The system decides how many archived files to keep (for example, the last 7 files). If an 8th file appears, it automatically deletes the oldest one.

**So, what is the difference between `-f` and `-F`?**

- `tail -f`: Follows by **File Descriptor**. If the file gets renamed during rotation, it stays attached to the old archived file.
- `tail -F`: Follows by **File Name** (with retry). It continuously monitors by filename, so when a new active file is created, it reconnects automatically without losing the stream!

---

Now that you know the command, run:

```bash
tail -f /var/log/dpkg.log

```

And in the second terminal, go and install anything, like:

```bash
sudo apt install nginx

```

Now go back to the first terminal and watch the new lines appearing so fast!

And if you want to end this, press **Ctrl + C**.

---

Now you have learned an important topic in DevOps: how to read real live logs.

I hope you are happy, understand everything, and see you later with a new concept!
