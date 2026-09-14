# Essential Linux File and Directory Commands

**Hello,**

I'm Hassan Ali, a graduate from the Faculty of Computer Science, Benha University, with a GPA of 3.47.

Now I will explain the most common commands that help us to list, create, copy, move, or delete files/directories.

---

### 1. Listing Files: `ls`

First, when we want to show the content of any directory, we use the command `ls`.

But we will see it oriented in a row style and just see the names of files/directories. So if we want to see more information like file type, permissions, or which user created it, and see all files in a column style, we use the command:

```bash
ls -l

```

And here `l` refers to long, and I forgot to tell you that `ls` refers to list.

And if we need a more human-readable format, we use:

```bash
ls -lh

```

This command makes the file size more clear for humans.

---

### 2. Creating Files and Folders: `mkdir` & `touch`

Now after we know how to list or see files, the time has come to see how to create a file or folder.

Let's first clarify that when I say folder, it means directory, and the previous statement is true. Okay, let's go back to creation commands.

When we want to create a folder, we use the command `mkdir foldername`. If we want to create many folders at the same time and in one command, we write:

```bash
mkdir folder1 folder2

```

We write the folders' names and separate between them by a space (not a comma). This command is for folders. If we want to create files, we don't use the same command; we use the command:

```bash
touch filename

```

And the same concept applies to files if we want to create many files at the same time and in one command.

Now what if we try to create files or folders with the same name created previously? Okay:

If we run the command `mkdir test`, this will create a folder with the name `test`. Try now to run the command again, the command will fail, and an error message will tell us: `file exists`.

Now you will ask me: why does it say `file exists` and not `folder exists`? And I appreciate that you noticed that! Okay, in the Linux filesystem, **everything is a file**, but there are files that contain other files; in this case, we call them folders or directories.

Let's go back to see what happens when we use the same command with `touch`:

If we run the command `touch test.txt`, it will create `test.txt`. And if we run the command again, it will update the **timestamp**, and you will see the timestamp when you run the command `ls -l` for more information.

Now if we want to create recursive directories, we use the command `mkdir -p` like that:

```bash
mkdir -p project/linux/{usr,src,var}

```

This will create a folder named `project` that contains a folder named `linux`, and the folder `linux` will contain `usr`, `src`, and `var` folders.

---

### 3. Copying Files and Folders: `cp`

Now let's go to the copy concept. It's easy because you just use the command:

```bash
cp file_you_want_to_copy destination

```

When I say destination, I mean you have many options, like just writing another name, or writing the path where you want to put it with the same name, or you can write another name:

```bash
cp test.txt copy_test.txt
# or
cp test.txt project/
# or
cp test.txt project/copy_test.txt

```

_(Note: Everything we said about copying files applies to folders, but with folders you must add `-r` for recursive: `cp -r folder1 folder2`)._

---

### 4. Moving or Renaming: `mv`

Now let's go to a new concept: move or rename.

Move or rename is the same concept, because renaming is moving a file to the same location but with a new name, that's it!

We will use the command `mv`. Its style is very similar to the command `cp`:

```bash
mv test.txt copy_test.txt
# or
mv test.txt project/
# or
mv test.txt project/copy_test.txt

```

And the same thing we applied to files will apply to folders.

---

### 5. Removing Files and Folders: `rm` & `rmdir`

Let's go to the final concept: remove.

When we want to remove any file, whether empty or not, we just use the command:

```bash
rm filename

```

The same thing applies to an empty folder using `rmdir`, but if you try it on a folder that is not empty, it will fail because you must remove everything in the folder and then remove the folder.

But it's very difficult to remove all these folders and files manually. You can use a command, but it's so dangerous! The command is:

```bash
rm -rf directory_name/

```

`r` refers to recursive, and `f` refers to force.

> **Warning:** I don't advise you to use this command as a superuser / root user, because you can damage the system. And you must know that any file or folder you remove, you cannot restore it again.

---

### Conclusion

Now we know everything about how to list, create, copy, move, and delete concepts and commands.

I hope you understood everything and don't face any problems.

See you soon in another concept, and have a good day!
