## Change hostname and reboot server
```bash
# edit server hostname, replace $hostname with actual name
echo $hostname > /etc/hostname
# add hostname to localhost in /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 $hostname
# edit the file /etc/environment and add the following lines
# with this, you will not encounter warning
# "warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory"
# when you ssh into the machine
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
# set system timezone
timedatectl set-timezone 'Asia/Singapore'
```

## Create new sudo user without password on CentOS
```bash
# replace $user with actual user
sudo adduser $user --disabled-password
sudo passwd $user $user_password
sudo usermod -aG wheel $user
# assume new user and test sudo commands
su - $user
sudo ls -la /root
```

## Delete default sudo user if any
```bash
# list all sudo users
sudo lid -g wheel
sudo userdel -r <username>

# Create ssh folder and change permission
mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys
chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys
```

## Setup SSH Key
```bash
# on local machine, replace $key_file with actual filename
ssh-keygen -t ecdsa -b 521 -f ~/.ssh/$key_file
scp ~/.ssh/$key_file user@remote_host:~/.ssh/authorized_keys
```
add a new entry in `/etc/hosts`, replace $hostname, $ip_address
```
$ip_address $hostname
```
add following section to ~/.ssh/config, replace $hostname, $key_file, $user
```
Host $hostname
    HostName $hostname
    IdentityFile ~/.ssh/$key_file
    User $user
```
Now, you should be able to login without password. Simply type: 
```
ssh $hostname
```

**Optional** To disable ssh login with password for all users except `root` user

comment off or remove the line below in `/etc/ssh/sshd_config`
```bash
# PasswordAuthentication yes
```
add following in `/etc/ssh/sshd_config` to allow only `root` user to authenticate with password
```
Match User !root
    PasswordAuthentication no
```

**Optional** To configer sudo without password for `<username>`
```bash
# assume root user
su -
# backup old file
cp /etc/sudoers /etc/sudoers.old
# open the Sudoers File
visudo
# add this line at the end of the file, so that other directive will not overwrite it
<username> ALL=(ALL) NOPASSWD: ALL
```