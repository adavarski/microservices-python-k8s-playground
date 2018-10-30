FROM prom/prometheus
ADD ./metrics/prometheus.yml /tmp/prometheus.yml
EXPOSE 9090
ENTRYPOINT  [ "/bin/prometheus", "--config.file=/tmp/prometheus.yml", "--web.listen-address=0.0.0.0:9090" ]
