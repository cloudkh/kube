## Install

```
$ sudo yum install ansible
$ vi mykey.pem
Copy private key

$ chmod 400 mykey.pem
$ sudo vi /etc/ansible/hosts

[all:vars]
ansible_user=centos
ansible_ssh_private_key_file=/home/centos/belugarKey.pem

[admin]
172.31.17.210

[master]
172.31.21.245
172.31.31.93
172.31.22.70

[node]
172.31.20.221
172.31.20.109
172.31.30.242

$ ansible all -a "yum install -y docker" --sudo
$ ansible all -a "systemctl enable docker" --sudo
$ ansible all -a "systemctl start docker" --sudo
```



