# docker_redis_cluster

redis3から入ったclusterの動作確認用

# USAGE

1. ビルドと起動

          docker-compose up

2. commanderに入ってcluster確認

          $ docker ps
          CONTAINER ID        IMAGE                    COMMAND                CREATED             STATUS              PORTS               NAMES
          712069042d5d        redis_commander:latest   "./create_cluster.sh   11 hours ago        Up 11 hours         6379/tcp            redis_commander_1
          eea3f595225a        redis_redisc1:latest     "/bin/sh -c '/data/r   11 hours ago        Up 11 hours         6379/tcp            redis_redisc1_1
          22e6a86e228c        redis_redisc2:latest     "/bin/sh -c '/data/r   11 hours ago        Up 11 hours         6379/tcp            redis_redisc2_1
          5b28a08ec628        redis_redisa2:latest     "/bin/sh -c '/data/r   11 hours ago        Up 11 hours         6379/tcp            redis_redisa2_1
          1b7835827fea        redis_redisa1:latest     "/bin/sh -c '/data/r   11 hours ago        Up 11 hours         6379/tcp            redis_redisa1_1
          529b160126e3        redis_redisb1:latest     "/bin/sh -c '/data/r   11 hours ago        Up 11 hours         6379/tcp            redis_redisb1_1
          76236e3306a8        redis_redisb2:latest     "/bin/sh -c '/data/r   11 hours ago        Up 11 hours         6379/tcp            redis_redisb2_1


          $ docker exec -it 712069042d5d bash
          root@712069042d5d:/data# env | egrep 'REDIS(A1|A2|B1|B2|C1|C2)_PORT_6379_TCP_ADDR' | sort
          REDISA1_PORT_6379_TCP_ADDR=172.17.0.172
          REDISA2_PORT_6379_TCP_ADDR=172.17.0.173
          REDISB1_PORT_6379_TCP_ADDR=172.17.0.171
          REDISB2_PORT_6379_TCP_ADDR=172.17.0.170
          REDISC1_PORT_6379_TCP_ADDR=172.17.0.175
          REDISC2_PORT_6379_TCP_ADDR=172.17.0.174

          root@712069042d5d:/data# env | egrep 'REDIS(A1|A2|B1|B2|C1|C2)_PORT_6379_TCP_PORT' | sort
          REDISA1_PORT_6379_TCP_PORT=6379
          REDISA2_PORT_6379_TCP_PORT=6379
          REDISB1_PORT_6379_TCP_PORT=6379
          REDISB2_PORT_6379_TCP_PORT=6379
          REDISC1_PORT_6379_TCP_PORT=6379
          REDISC2_PORT_6379_TCP_PORT=6379

          root@712069042d5d:/data# ./redis-3.0.1/src/redis-cli -h ${REDISA1_PORT_6379_TCP_ADDR} -p ${REDISA1_PORT_6379_TCP_PORT}
          172.17.0.172:6379> cluster info
          cluster_state:ok
          cluster_slots_assigned:16384
          cluster_slots_ok:16384
          cluster_slots_pfail:0
          cluster_slots_fail:0
          cluster_known_nodes:6
          cluster_size:3
          cluster_current_epoch:6
          cluster_my_epoch:1
          cluster_stats_messages_sent:180362
          cluster_stats_messages_received:180362
          172.17.0.172:6379> cluster nodes
          196cae3b33d0bdd3ad21e923acc2cfef097f9eb0 172.17.0.173:6379 slave 0b27e9eb1704b6567b6ed343f21e391efbc4bdff 0 1431612246233 4 connected
          ee64d2b0a05b1b2ed5505f0c7217cf2798141977 172.17.0.170:6379 slave 778f64a2786677f0817e73e6d5e913209fb8ca15 0 1431612247735 5 connected
          1700db892d810e678124931263d439b9af359645 172.17.0.175:6379 master - 0 1431612246734 3 connected 10923-16383
          778f64a2786677f0817e73e6d5e913209fb8ca15 172.17.0.171:6379 master - 0 1431612247234 2 connected 5461-10922
          0b27e9eb1704b6567b6ed343f21e391efbc4bdff 172.17.0.172:6379 myself,master - 0 0 1 connected 0-5460
          39b60daed57ca13abb165ede4fa97a35612510bc 172.17.0.174:6379 slave 1700db892d810e678124931263d439b9af359645 0 1431612245733 6 connected
          172.17.0.172:6379> cluster slots
          1) 1) (integer) 10923
             2) (integer) 16383
             3) 1) "172.17.0.175"
                2) (integer) 6379
             4) 1) "172.17.0.174"
                2) (integer) 6379
          2) 1) (integer) 5461
             2) (integer) 10922
             3) 1) "172.17.0.171"
                2) (integer) 6379
             4) 1) "172.17.0.170"
                2) (integer) 6379
          3) 1) (integer) 0
             2) (integer) 5460
             3) 1) "172.17.0.172"
                2) (integer) 6379
             4) 1) "172.17.0.173"
                2) (integer) 6379
          172.17.0.172:6379>
