FROM ubuntu:14.04

# set locale
RUN locale-gen en_US.UTF-8
RUN update-locale LANG=en_US.UTF-8
ENV DEBIAN_FRONTEND noninteractive
ENV LC_ALL C
ENV LC_ALL en_US.UTF-8

ENV REDIS_VERSION=3.0.1

WORKDIR /data

RUN \
  apt-get update && \
  apt-get install -yq wget curl build-essential ruby && \
  rm -rf /var/lib/apt/lists/*

RUN \
  gem install redis

# make test には tcl が必要
RUN \
  wget http://download.redis.io/releases/redis-${REDIS_VERSION}.tar.gz && \
  tar zxvf redis-${REDIS_VERSION}.tar.gz && \
  cd redis-${REDIS_VERSION} && \
  make

COPY redis.conf /data/redis.conf

COPY create_cluster.sh /data/create_cluster.sh
RUN chmod +x create_cluster.sh

EXPOSE 6379
CMD /data/redis-${REDIS_VERSION}/src/redis-server /data/redis.conf
