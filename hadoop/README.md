# ��Kubernetes�а�װ����hadoop
����һ����ʾ��Kubernetes�а�װ����hadoop, hive, spark
## ׼������
1.����hadoop  

```bash
wget http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.7/hadoop-2.7.7.tar.gz
```
2.����hive  

```bash
wget http://mirror.bit.edu.cn/apache/hive-2.3.7/apache-hive-2.3.7-bin.tar.gz
```
3.����spark  

```bash
wget http://mirror.bit.edu.cn/apache/spark/spark-2.4.7/spark-2.4.7-bin-hadoop2.7.tgz
```
## ��������
ִ����������Docker���񣬲��ϴ�����˽����

```bash
docker build -t hadoop-spark
docker tag hadoop-spark 172.31.87.111:5000/hadoop-spark
docker push 172.31.87.111:5000/hadoop-spark
```
ע�⣺172.31.87.111:5000�Ǳ���˽����ַ:�˿ں�
## Kubernetes��װ����
1.��װmysql

```bash
kubectl create -f mysql.yaml
```
2.��װhadoop

```bash
kubectl create -f hadoop-hive.yaml
```
## ��ʼ��hive���ݿ�
### ��һ������ʱ������master�ڵ㣺  
1.����master�ڵ�����ƣ�

```bash
kubectl get po -n bigdata
```
2.����master�ڵ�Ĳ���ϵͳ��

```bash
kubectl exec -it hadoop-master-xxxxxx-xxxxx bash
```

### ����master�ڵ��ִ���������

```bash
schematool -dbType mysql -initSchema
```