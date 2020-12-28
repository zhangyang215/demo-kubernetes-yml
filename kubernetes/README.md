# Kubernetes�İ�װ����
����һ��Kubernetes�İ�װ������̣��汾��1.11.0

## ׼������
д��hosts

```bash
echo "10.211.55.18    k8s-master-1
10.211.55.19    k8s-node-1
10.211.55.20    k8s-node-2" >> /etc/hosts
```
�رշ���ǽ

```bash
systemctl stop firewalld
systemctl disable firewalld
```
����SELINUX

```bash
setenforce 0
vi /etc/selinux/config 
#SELINUX=disabled
```
����ipvs��kube-proxy����ipvs��ǰ��������Ҫ���������ں�ģ�飺

```bash
cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF
chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep -e ip_vs -e nf_conntrack_ipv4
```

�ر�swap

```bash
swapoff �Ca
```
�޸� /etc/fstab �ļ���ע�͵� SWAP ���Զ����أ�ʹ��free -mȷ��swap�Ѿ��رա� swappiness�����������޸�/etc/sysctl.d/k8s.conf�������һ�У�
vm.swappiness=0
ִ����Ч��

```bash
sysctl -p /etc/sysctl.d/k8s.conf
```

## ��װDocker (���нڵ㶼Ҫִ��)
1.���linux�ں˰汾

```bash
uname -r
```
2.ʹ�� root Ȩ�޵�¼ Centos��ȷ�� yum �����µ����£������ܻᱨ��˵û�ж���yum�ֿ�

```bash
yum update
```
3.��װ��Ҫ�������������yum-util�ṩyum-config-manager����

```bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```
4.����docker��yumԴ

```bash
$ yum-config-manager \
--add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
5.�鿴��docker�汾

```bash
$ yum list docker-ce --showduplicates | sort -r
```
6.��װdocker

```bash
$ yum install docker-ce-17.12.0.ce
```

## ʹ��kubeadm����Kubernetes
### 1.��Ӱ����Դ(���нڵ㶼ִ��)
�����ǣ�

```bash
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
```
�ָ�Ϊ�����Դ��

```bash
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```
��װ��Ҫ�����

```bash
yum -y install epel-release
yum clean all
yum makecache fast
yum -y install kubelet-1.11.7-0.x86_64 kubeadm-1.11.7-0.x86_64 kubectl-1.11.7-0.x86_64 kubernetes-cni
systemctl enable kubelet && systemctl start kubelet
```
��������k8s���������������  
ע�⣺��װk8s���������ǲ���ֱ�Ӵ�google��վ���أ���˲������ַ�ʽ

```bash
cd <��װĿ¼>/kubernetes
chmod -R 777 ./xxx.sh
./xxx.sh
```

### 2.ʹ��kubeadm init��ʼ����Ⱥ����master��ִ�У�
����k8s���ڵ�

```bash
kubeadm init --kubernetes-version=v1.11.0 --pod-network-cidr=10.244.0.0/16
```

��ס����̨��������һ�仰�����ƣ�

```bash
kubeadm join 172.19.1.109:6443 --token 8b1jm7.6951eviubal4x39q --discovery-token-ca-cert-hash sha256:6412
50cfbffae1e857a26d83c1e79f3d4c7d2759359e868406375784be1c1626
```
ÿ��ִ��init��token����һ�������������ڵ�������ڵ������  

���Ҫͣ��k8s��Ⱥ��ÿ���ڵ�ִ�У�

```bash
kubeadm reset
```

���������Ժ�ִ��һ�����

```bash
mkdir -p ~/.kube
cp -i /etc/kubernetes/admin.conf ~/.kube/config
chown $(id -u):$(id -g) ~/.kube/config
```

���� kubectl ��֤��Ϣ

```bash
export KUBECONFIG=/etc/kubernetes/admin.conf
echo "export KUBECONFIG=/etc/kubernetes/admin.conf" >> ~/.bash_profile
```
### 3.��װ Flannel ���磨��master��ִ�У�

```bash
mkdir -p /etc/cni/net.d/

cat <<EOF> /etc/cni/net.d/10-flannel.conf
{
��name��: ��cbr0��,
��type��: ��flannel��,
��delegate��: {
��isDefaultGateway��: true
}
}
EOF

mkdir /usr/share/oci-umount/oci-umount.d -p

mkdir /run/flannel/

cat <<EOF> /run/flannel/subnet.env
FLANNEL_NETWORK=10.244.0.0/16
FLANNEL_SUBNET=10.244.1.0/24
FLANNEL_MTU=1450
FLANNEL_IPMASQ=true
EOF
```
ִ�нű���

```bash
kubectl create -f ./flannel.yml
```
�������״̬��ִ�У�

```bash
kubectl -n kube-system get pods
```
�õ������

```
NAME                                   READY     STATUS    RESTARTS   AGE
coredns-78fcdf6894-ddb54               1/1       Running   0          1h
coredns-78fcdf6894-qqqxf               1/1       Running   0          1h
etcd-172-19-1-109                      1/1       Running   0          1h
kube-apiserver-172-19-1-109            1/1       Running   0          1h
kube-controller-manager-172-19-1-109   1/1       Running   0          1h
kube-flannel-ds-zq9ns                  1/1       Running   0          50m
kube-proxy-hs89s                       1/1       Running   0          1h
kube-scheduler-172-19-1-109            1/1       Running   0          1h
```
������нڵ㶼����Running״̬�����ڵ㰲װ�ɹ�

## ����worker�ڵ�(���дӽڵ�ִ��)
������ÿ���ӽڵ���ִ��join������

```bash
kubeadm join 172.19.1.109:6443 --token 8b1jm7.6951eviubal4x39q --discovery-token-ca-cert-hash sha256:641250cfbffae1e857a26d83c1e79f3d4c7d2759359e868406375784be1c1626
```

ע�⣺ÿ�����ڵ�reset�Ժ󣬻�����һ���µ�token�����ÿ���ӽڵ���Ҫ��ִ��kubeadm reset��Ȼ����ִ��join������  

�ص�master�ڵ�ִ�У�

```bash
kubectl get nodes
```
�õ������

```
NAME        STATUS   ROLES    AGE   VERSION
k8s-master-1   Ready    master   10d   v1.11.7
k8s-node-1    Ready    <none>   10d   v1.11.7
k8s-node-2    Ready    <none>   10d   v1.11.7
```
�������Ready״̬������k8s��Ⱥ��װ�ɹ��������NotReady״̬����鼯Ⱥ��װ���̣���������Flannel��������δ��ȷ��װ��

## ��װDashboard
ִ�����

```bash
cd dashboard
kubectl  -n kube-system create -f .
```
