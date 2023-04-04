# Manage Users in Bash

`users.txt`

```txt
Mary
Jim
Diego
Jason
Sherry
```

`create_users.sh`

```bash
#!/bin/bash
file="users.txt"
while read -r name; do
echo $name
useradd $name
echo "$name:bash" | chpasswd
usermod -aG sudo $name
done <$file
```

`sudo sh create_users.sh`

```bash
cat /etc/passwd
cat /etc/group | grep sudo
```

`delete_users.sh`

```bash
#!/bin/bash
file="users.txt"
while read -r name; do
echo $name
deluser --remove-home $name
done <$file
```

`sudo sh delete_users.sh`

```bash
cat /etc/passwd
cat /etc/group | grep sudo
```