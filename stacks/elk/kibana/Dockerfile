# https://github.com/elastic/kibana-docker
#FROM docker.elastic.co/kibana/kibana:${ELK_VERSION}

FROM resin/rpi-raspbian:jessie

ARG ELK_VERSION

ENV PATH /opt/kibana/bin:$PATH


EXPOSE 5601

# add our user and group first to make sure their IDs get assigned consistently
RUN groupadd -r kibana && useradd -r -m -g kibana kibana

RUN apt-get update && apt-get install -y \
        ca-certificates \
        wget \
    --no-install-recommends

# grab gosu for easy step-down from root
ENV GOSU_VERSION 1.7
RUN set -x \
    && wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$(dpkg --print-architecture)" \
    && wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$(dpkg --print-architecture).asc" \
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
    && gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu \
    && rm -r "$GNUPGHOME" /usr/local/bin/gosu.asc \
    && chmod +x /usr/local/bin/gosu \
    && gosu nobody true

# grab tini for signal processing and zombie killing
ENV TINI_VERSION v0.10.0
RUN set -x \
    && wget -O /usr/local/bin/tini "https://github.com/ind3x/rpi-tini/releases/download/$TINI_VERSION/tini" \
    && chmod +x /usr/local/bin/tini \
    && tini -h

RUN set -x \
    && cd /opt \
    && wget https://nodejs.org/dist/v10.15.2/node-v10.15.2-linux-armv7l.tar.xz \
    && tar -xf node-v10.15.2-linux-armv7l.tar.xz \
    && mv node-v10.15.2-linux-armv7l node \
    && rm node-v10.15.2-linux-armv7l.tar.xz \
    && ln -sf /opt/node/bin/node /usr/local/bin/node \
    && ln -sf /opt/node/bin/npm /usr/local/bin/npm \
    && ln -sf /opt/node/bin/npx /usr/local/bin/npx

# install kibana
#RUN set -x \
#    && cd /tmp \
#    && wget "https://artifacts.elastic.co/downloads/kibana/kibana-${ELK_VERSION}-linux-x86_64.tar.gz" \
#    && tar -xvzf kibana-$ELK_VERSION-linux-x86_64.tar.gz \
#    && mv /tmp/kibana-$ELK_VERSION-linux-x86_64 /opt \
#    && rm kibana-$ELK_VERSION-linux-x86_64.tar.gz \
#    && ln -s /opt/kibana-$ELK_VERSION-linux-x86_64 /opt/kibana \
#    && chown -R kibana:kibana /opt/kibana \
#    && chmod o+w /opt/kibana/optimize/.babelcache.json \
##    && sed -ri "s!^(\#\s*)?(elasticsearch\.url:).*!\2 'http://elasticsearch:9200'!" /opt/kibana/config/kibana.yml \
##    && grep -q 'elasticsearch:9200' /opt/kibana/config/kibana.yml
#    #&& ln -sf /usr/local/bin/node /opt/kibana/node/bin/node \
#    #&& ln -sf /usr/local/bin/npm /opt/kibana/node/bin/npm
#    && ln -sf /opt/node/ /opt/kibana/node/

RUN cd /opt \
    && wget --quiet "https://github.com/elastic/kibana/archive/v${ELK_VERSION}.tar.gz" \
    && tar -zxf v${ELK_VERSION}.tar.gz

RUN cd /opt \
    rm v${ELK_VERSION}.tar.gz \
    && ln -s kibana-${ELK_VERSION} kibana \
    && ln -sf /opt/node /opt/kibana/node
RUN /opt/node/bin/yarn install \
    && chown -R kibana:kibana /opt/kibana \
    && chmod o+w /opt/kibana/optimize/.babelcache.json

COPY docker-entrypoint.sh /
RUN chmod +x /docker-entrypoint.sh

ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["kibana"]


