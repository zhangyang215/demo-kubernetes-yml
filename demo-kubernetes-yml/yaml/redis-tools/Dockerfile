FROM python:2.7
#ADD sources.list /etc/apt/
RUN apt-get update &&\
    apt-get install -y redis-tools dnsutils
RUN pip install redis-trib
CMD ["bash"]