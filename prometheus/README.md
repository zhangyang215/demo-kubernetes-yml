# Kubernetes��ϵͳ���Prometheus
����Kubernetes��ϵͳ���ϵͳPrometheus�İ�װ�������
## ��Kubernetes�����
ִ�нű���

```bash
cd <��װĿ¼>/prometheus
kubectl create -f ./yml/node-exporter.yaml
```
ִ�����Ժ���м�飺

```bash
$ kubectl get po -n kube-system |grep node-exporter
node-exporter-btclr                    1/1       Running   0          142d
node-exporter-rfxr9                    1/1       Running   0          142d
node-exporter-rw9qc                    1/1       Running   1          156d
```

## ��װ����Prometheus
���齫Prometheus������k8s֮�⣬������k8s������ʱ��Ȼ�������Լ��  
1.�޸������ļ�./server/prometheus.yml

```
global:
  scrape_interval:     15s
  evaluation_interval: 15s
rule_files:
  #����澯�Ĺ������ļ�
  - "rules/*.yaml"
scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets: ['localhost:9090']
      # ����k8s��ÿ���ڵ�node-exporter�ķ��ʵ�ַ
      - targets: ['172.31.87.109:31100','172.31.87.110:31100','172.31.87.111:31100']
        labels: 
          group: 'client-node-exporter'
      - targets: ['172.31.87.111:9091']
        labels:
          group: 'pushgateway'
alerting:
  alertmanagers:
    - static_configs:
      - targets: ['172.31.87.111:9003']
```
2.����Prometheus, pushgateway, alertmanager, grafana

```bash
docker run --name=prometheus -d -p 9090:9090 \
-v $PROMETHEUS_HOME/server/prometheus.yml:/etc/prometheus/prometheus.yml \
-v $PROMETHEUS_HOME/rules:/etc/prometheus/rules \
prom/prometheus \
--config.file=/etc/prometheus/prometheus.yml \
--web.enable-lifecycle

docker run -d -p 9091:9091 --name pushgateway prom/pushgateway
docker run -d -p 3000:3000 --name grafana grafana/grafana

docker run -d -p 9093:9093 --name alertmanager \
-v $PROMETHEUS_HOME/server/alertmanager.yml:/etc/alertmanager/alertmanager.yml \
prom/alertmanager
```
3.���ͨ��grafana����ϵͳ��أ�

```
http://localhost:3000/
```