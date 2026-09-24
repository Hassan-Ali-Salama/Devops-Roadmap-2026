# Linux Users and Groups Management

Hello,  
I'm Hassan Ali, a graduate in Computer Science and Artificial Intelligence from Benha University.

Today we will talk about an important topic: users in Unix, how to add a user, create a password, and navigate between user accounts.

---

Let's first start with what a user means to the system. In any system, a user is just a number, and we map it to a name to make it easy to switch or log in as that user.

If we want to see the users we have on the system, just run `cat /etc/passwd`. You will see many lines; each line represents a user row.  
In one row, we see 7 values separated by colons like this:  
`root:x:0:0:root:/root:/bin/bash`

Let's explain what each value means in order:

1. **root** -> Means the user (login) name.
2. **x** -> The user's encrypted password (an `x` indicates it is stored securely in `/etc/shadow`).
3. **0** -> The User ID (UID) -> The user's representation to the kernel (UID 0 is reserved for root).
4. **0** -> The Group ID (GID) -> Primary group for the user.
5. **root** -> The user's real name (or account description).
6. **/root** -> The user's home directory.
7. **/bin/bash** -> The user's shell (the program that runs when the user logs in).

---

After we know what each line and value means, now let's understand groups!

The idea of groups is to give permissions quickly and organize users into categories.  
Now, if we want to know the groups in the system, just run `cat /etc/group`.  
You will see lines like the users file, but with 4 values like this:  
`root:x:0:`

1. **root** -> The group name.
2. **x** -> The group password placeholder.
3. **0** -> The Group ID (GID).
4. _(empty here)_ -> This field is empty for root, but in many groups it contains a list of secondary users that belong to this group.

---

Now after we know users and groups, it's time to know how to create a user or group.

If we want to create a user, we use the command:

```bash
sudo useradd -m -s /bin/bash user_name

```

You might ask many questions about this command, so I will explain everything about it:

1. We use **`sudo`** because this command must be run by a superuser, and `sudo` gives us this permission.
2. We use **`-m`** (refers to `--create-home`). It forces the system to create a home directory for the user and copies configuration files like `.bashrc` and `.profile` from `/etc/skel`. Without this option, it will create a user without a home directory, so the user will not have a location to save their settings.
3. We use **`-s`** (refers to `--shell`). This defines which shell the user will use when logging in. Here we use `/bin/bash`. Other people might use `/bin/sh` which is the basic default system shell.

After adding the user, we need to set a password for this user, so we will use the command:

```bash
sudo passwd user_name

```

---

After this, if we want to create a new group (like `engineers`), okay, we will use the command:

```bash
sudo groupadd engineers

```

And if we want to append the new user to this group, we will use:

```bash
sudo usermod -aG engineers user_name

```

So you will ask me: what is the meaning of `-aG` and why do we use it?

Good question!

- **`-a`** refers to `--append`: it means add the user to the new group and don't remove them from other groups.
- **`-G`** means make this group a secondary group.
  _(And if you use a lowercase `-g`, it will change the primary group for the user)._

If you want to give `sudo` permissions to a user, just use the exact same command and change the group name to `sudo`:

```bash
sudo usermod -aG sudo user_name

```

---

Now, how can we check the user, its UID, GID, and all the groups it belongs to?

We use the command:

```bash
id user_name

```

This command will print the UID, the primary GID, and all secondary groups for that user so you can verify everything is set up correctly.

---

If you want to navigate/switch to that user, just use:

```bash
su - user_name

```

It will ask you for the password if there is one, and then you will enter.

If you want to return, just write:

```bash
exit

```

---

Now if you want to remove a user and keep their files, use the command:

```bash
sudo userdel devops_user

```

If you want to delete their files too, use:

```bash
sudo userdel -r devops_user

```

_(Here `-r` refers to remove, and it will remove `/home/user_name` and user mail files)._

To remove a group, use:

```bash
sudo groupdel engineers

```

To remove a user from a group without removing the group itself, use:

```bash
sudo gpasswd -d user_name engineers

```

_(Here `-d` refers to delete)._

---

I hope you enjoyed this topic and that you can now create and switch between users, create groups, add users to groups, and remove users or groups safely.

See you soon in another topic, and have a good day!
