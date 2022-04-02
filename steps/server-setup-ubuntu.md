## setup servers
On ALL nodes: 
```bash
# update /etc/hostname, master-1,2; worker-1,2; loadbalancer
# Update /etc/hosts about other hosts
cat >> /etc/hosts <<EOF
<private ip>  master-1
<private ip>  master-2
<private ip> worker-1
<private ip> worker-2
<private ip>  lb
EOF
# update dns
    #!/bin/bash
    sed -i -e 's/#DNS=/DNS=8.8.8.8/' /etc/systemd/resolved.conf && \
    service systemd-resolved restart
# restart machine
reboot
```
    
## Create new sudo user <kube> without password on Ubuntu
```bash
# repeat on both master nodes, worker nodes and loadbalancer
adduser kube --disabled-password
adduser kube sudo
# add below line to bottom of file /etc/sudoers
kube ALL=(ALL) NOPASSWD: ALL
# switch user
su - kube
# add public key to allow ssh into
cat >> ~/.ssh/authorized_keys <<EOF
ecdsa-sha2-nistp521 AAAAE2...Lk8g==
EOF
```

## provision scripts into the machines
```bash
scp -i <private_key> cert_verify.sh kube@master-1:~
scp -i <private_key> cert_verify.sh kube@master-2:~
scp -i <private_key> install-docker-2.sh allow-bridge-nf-traffic.sh cert_verify.sh kube@worker-1:~
scp -i <private_key> install-docker-2.sh allow-bridge-nf-traffic.sh cert_verify.sh kube@worker-2:~
# exec scripts on worker nodes
bash install-docker-2.sh
bash allow-bridge-nf-traffic.sh
```