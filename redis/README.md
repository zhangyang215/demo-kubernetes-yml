# ��Kubernetes�а�װredis
����һ����ʾ��Kubernetes�а�װ����redis
## ��������redis
ִ������ű���

```bash
kubectl create -f redis.yaml
```
����ɹ��󣬿���ͨ��redis-0.redis����k8s��Ⱥ�ڲ���΢����ķ��ʡ�

## ��Ⱥ����redis
### 1.k8s��Ⱥ����
ִ������ű���

```bash
kubectl create -f redis-cluster.yaml
```
ִ�гɹ�����k8s������6���ڵ��redis�������ǻ�δ������Ⱥ��
### 2.����redis�ͻ���
ִ�������docker���񣬲��ϴ�����˽����

```bash
docker build -t redis-tools ./redis-tools/.
docker tag redis-tools 172.31.87.111:5000/redis-tools
docker push 172.31.87.111:5000/redis-tools
```
ִ��k8s���

```bash
kubectl run redis-tools --image=172.31.87.111:5000/redis-tools -it --restart=Never
```
��redis-tools��ִ���������redis���ڵ㼯Ⱥ��

```
redis-trib.py create \
  `dig +short redis-cluster-0.redis-cluster.default.svc.cluster.local`:6379 \
  `dig +short redis-cluster-1.redis-cluster.default.svc.cluster.local`:6379 \
  `dig +short redis-cluster-2.redis-cluster.default.svc.cluster.local`:6379
```
Ϊÿ��redis���ڵ����ôӽڵ㣺

```
redis-trib.py replicate \
  --master-addr `dig +short redis-cluster-0.redis-cluster.default.svc.cluster.local`:6379 \
  --slave-addr `dig +short redis-cluster-3.redis-cluster.default.svc.cluster.local`:6379

redis-trib.py replicate \
  --master-addr `dig +short redis-cluster-1.redis-cluster.default.svc.cluster.local`:6379 \
  --slave-addr `dig +short redis-cluster-4.redis-cluster.default.svc.cluster.local`:6379

redis-trib.py replicate \
  --master-addr `dig +short redis-cluster-2.redis-cluster.default.svc.cluster.local`:6379 \
  --slave-addr `dig +short redis-cluster-5.redis-cluster.default.svc.cluster.local`:6379
```
����redis��Ⱥ�е�ĳ���ڵ�Ŀͻ��ˣ�

```
redis-cli -h `dig +short redis-cluster-0.redis-cluster.default.svc.cluster.local`
```
���redis��Ⱥ״̬��

```
> cluster nodes
```
ÿһ��master���ڵ㶼������һ����Ӧ��slave�ӽڵ㣬��ô��ͳɹ���