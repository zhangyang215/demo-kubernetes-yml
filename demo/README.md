# ΢����Demo��Kubernetes����
����Demo-service-xxxϵ�е�Kubernetes���𣬲���ʱִ�У�

```bash
kubectl delete -f .
kubectl create -f .
```

��ʾ������������Ŀ��

```bash
demo-parent             ��ʾ��������Ŀ�ĸ���Ŀ����������springboot, springcloud�����������Ŀ���maven���
demo-service-eureka     ΢����ע������eureka�ļ�Ⱥ����
demo-service-config     ΢������������config
demo-service-turbine    ��΢�����·������״�����turbine
demo-service-zuul       ��������zuul
demo-service-parent     ��ҵ��΢���������ݿ���ʣ��ĸ���Ŀ
demo-service-support    ��ҵ��΢���������ݿ���ʣ��ײ㼼�����
demo-service-customer   �û�����΢���������ݿ���ʣ�
demo-service-product    ��Ʒ����΢���������ݿ���ʣ�
demo-service-supplier   ��Ӧ�̹���΢���������ݿ���ʣ�
demo-service2-parent    ��ҵ��΢���������ݿ���ʣ��ĸ���Ŀ
demo-service2-support   ��ҵ��΢���������ݿ���ʣ��ײ㼼�����
demo-service2-customer  �û�����΢���������ݿ���ʣ�
demo-service2-product   ��Ʒ����΢���������ݿ���ʣ�
demo-service2-supplier  ��Ӧ�̹���΢���������ݿ���ʣ�
demo-service2-order     ��������΢���������ݿ���ʣ�
```