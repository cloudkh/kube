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

```
$ sudo vi kubelet.yml

---
- hosts: all
  remote_user: "{{ansible_user}}"
  tasks:
    - name: kubernetes repo
      become: true 
      shell:
        cmd: |
          sudo cat <<EOF > /etc/yum.repos.d/kubernetes.repo
          [kubernetes]
          name=Kubernetes
          baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
          enabled=1
          gpgcheck=1
          repo_gpgcheck=1
          gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
          exclude=kube*
          EOF

    - name: kubelet install
      command: "{{ item }}"
      with_items:
        - sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
        - sudo systemctl enable kubelet
        - sudo systemctl start kubelet

$ ansible-playbook kubelet.yml
```






