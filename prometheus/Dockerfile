FROM prom/prometheus
FROM alpine:3.10.2
RUN apk add gettext


RUN apk update && \
    apk upgrade && \
    apk --no-cache add  iputils-ping && \
    apk --no-cache add telnet && \
    apk --no-cache add curl

COPY --from=0 /bin/prometheus /bin/prometheus

RUN mkdir -p prometheus /etc/prometheus && \
chown -R nobody:nogroup etc/prometheus /prometheus

COPY prometheus_config/prometheus.yml /etc/prometheus/prometheus.orig.yml

# Run envsubst before Prometheus.
RUN echo $'#!/bin/sh\n\
envsubst < /etc/prometheus/prometheus.orig.yml > /etc/prometheus/prometheus.yml && \
exec /bin/prometheus "$@"' \
> /etc/prometheus/entrypoint.sh

RUN chmod +x /etc/prometheus/entrypoint.sh
ENTRYPOINT ["/etc/prometheus/entrypoint.sh"]

CMD [ "--config.file=/etc/prometheus/prometheus.yml", \
"--storage.tsdb.path=/prometheus" ]
USER nobody
EXPOSE 9090
WORKDIR /prometheus
