FROM alpine:3.12

ARG DOCKER_VERSION=19.03.12

RUN apk --update add bash curl tzdata \
  && cd /tmp/ \
#  && curl -sSL -O https://get.docker.com/builds/Linux/armf/docker-${DOCKER_VERSION}.tgz \
  && curl -sSL -O https://download.docker.com/linux/static/stable/armhf/docker-${DOCKER_VERSION}.tgz \
  && tar zxf docker-${DOCKER_VERSION}.tgz \
  && mkdir -p /usr/local/bin/ \
  && mv /tmp/docker/docker /usr/local/bin/ \
  && chmod +x /usr/local/bin/docker \
  && apk del curl \
  && rm -rf /tmp/* \
  && rm -rf /var/cache/apk/*

ADD https://raw.githubusercontent.com/spotify/docker-gc/master/docker-gc /usr/bin/docker-gc
COPY build/default-docker-gc-exclude /etc/docker-gc-exclude
COPY build/executed-by-cron.sh /executed-by-cron.sh
COPY build/generate-crontab.sh /generate-crontab.sh

RUN chmod 0755 /usr/bin/docker-gc \
  && chmod 0755 /generate-crontab.sh \
  && chmod 0755 /executed-by-cron.sh \
  && chmod 0644 /etc/docker-gc-exclude 

CMD /generate-crontab.sh > /var/log/cron.log 2>&1 \
  && crontab crontab.tmp \
  && /usr/sbin/crond \
  && tail -f /var/log/cron.log
