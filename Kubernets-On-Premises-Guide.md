## 아주 유용한 블로그

[https://blog.inkubate.io/install-and-configure-a-multi-master-kubernetes-cluster-with-kubeadm/](https://blog.inkubate.io/install-and-configure-a-multi-master-kubernetes-cluster-with-kubeadm/)

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
          cat <<EOF > /etc/yum.repos.d/kubernetes.repo
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
        - sudo setenforce 0
        - sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
        - sudo systemctl enable kubelet
        - sudo systemctl start kubelet

    - name: setenforce
      become: true 
      shell:
        cmd: |
          cat <<EOF >  /etc/sysctl.d/k8s.conf
          net.bridge.bridge-nf-call-ip6tables = 1
          net.bridge.bridge-nf-call-iptables = 1
          EOF

    - name: systemctl
      become: true     
      command: "{{ item }}"
      with_items:
        - sysctl --system

$ ansible-playbook kubelet.yml
```

## swapoff

```
$ swapoff -a
$ sed -i '/ swap / s/^/#/' /etc/fstab
```

## all master

```
cat << EOF > /etc/systemd/system/kubelet.service.d/20-etcd-service-manager.conf
[Service]
ExecStart=
ExecStart=/usr/bin/kubelet --cgroup-driver=systemd --address=127.0.0.1 --pod-manifest-path=/etc/kubernetes/manifests --allow-privileged=true
Restart=always
EOF

systemctl daemon-reload
systemctl restart kubelet
```




1. TCP 로드밸런스 구성할것. 마스터 3개 노드의 6443 포트로.

```
Calico CDIR (사이더 블록)
192.168.0.0/16

$ kubectl version
쿠버네이츠 버젼 v1.11.2
```

## cgroup

```
# 도커 cgroup 확인
$ docker info | grep -i cgroup
$ vi /etc/default/kubelet
KUBELET_KUBEADM_EXTRA_ARGS=--cgroup-driver=systemd

$ vi /etc/sysconfig/kubelet
KUBELET_EXTRA_ARGS=--cgroup-driver=systemd



$ vi /etc/default/kubelet
$ 

# Reset
$ kubeadm reset


```

레퍼런스

[https://github.com/cookeem/kubeadm-ha](https://github.com/cookeem/kubeadm-ha)






