## Install VirtualBox
```bash
sudo yum update
sudo yum install –y patch gcc kernel-headers kernel-devel make perl wget
sudo reboot
# download virtualbox repostory
sudo wget http://download.virtualbox.org/virtualbox/rpm/el/virtualbox.repo -P /etc/yum.repos.d
sudo yum install VirtualBox-6.1 -y
sudo systemctl status vboxdrv
```

To clean up virtualbox
```bash
sudo systemctl stop vboxdrv
sudo rpm -qa | grep VirtualBox-5.1
sudo yum remove packageNameFromTheList
```
## Install Vagrant and Git
Install git
```
sudo yum install –y git
```
Look for appropriate package from [Vagrant download page](https://www.vagrantup.com/downloads), For my example, I used the one for CentOS
```bash
# download and save under /tmp/
wget https://releases.hashicorp.com/vagrant/2.2.19/vagrant_2.2.19_x86_64.rpm -P /tmp
# insall vagrant
rpm -i /tmp/vagrant_2.2.19_x86_64.rpm
vagrant --version 
# OUTPUT: Vagrant 2.2.19
```