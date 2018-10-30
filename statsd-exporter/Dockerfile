FROM prom/statsd-exporter:v0.4.0
ADD ./metrics/statsd_mapping.conf /tmp/statsd_mapping.conf
EXPOSE 9102
EXPOSE 9125/udp
ENTRYPOINT  [ "/bin/statsd_exporter", "-statsd.mapping-config=/tmp/statsd_mapping.conf" ]
