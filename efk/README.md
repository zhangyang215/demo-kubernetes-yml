# Kubernetes��־���EFK
����Kubernetes��־���EFK�İ�װ�ű����ڲ���ʱִ�У�

```bash
kubectl delete -f .
kubectl create -f .
```

���ϣ����kibana��ʹ�����logtrail����������kibana����

```bash
cd kibana
docker build -t kibana .
```
�������Ժ󣬽�kibana-deployment.yaml�еľ����Ϊ�þ���

```
...
    spec:
      containers:
      - name: kibana-logging
        image: kibana #kibana:7.2.0
        resources:
...
```