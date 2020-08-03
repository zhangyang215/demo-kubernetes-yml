FROM kibana:7.2.0
ADD logtrail-7.2.0-0.1.31.zip /tmp/
RUN ./bin/kibana-plugin install file:///tmp/logtrail-7.2.0-0.1.31.zip
COPY logtrail.json /usr/share/kibana/plugins/logtrail/