---
title: Redis CmakeList
layout: post
date: 2016-03-03 14:38:33  +0800
categories: Redis
---

具体路径自己调整～～
Redis版本 3.0.4
 
{% highlight shell %}

cmake_minimum_required(VERSION 3.0)
project(redis)

SET(LIBRARY_OUTPUT_PATH "${CMAKE_SOURCE_DIR}/build/lib")
SET(EXECUTABLE_OUTPUT_PATH "${CMAKE_SOURCE_DIR}/build/bin")

ADD_DEFINITIONS(-DUSING_LEVEL_DB)

INCLUDE_DIRECTORIES(
        "deps/jemalloc/include"
        "deps/hiredis"
        "deps/linenoise"
        "deps/lua/src"
        "src"
)

LINK_DIRECTORIES(
        "deps/hiredis"       #hiredis
        "deps/jemalloc/lib"  #jemalloc
        "deps/lua/src"       #lua
        "deps/leveldb"       #leveldb

)

SET(hiredis_SOURCE_FILES
        deps/hiredis/async.c
        deps/hiredis/dict.c
        deps/hiredis/hiredis.c
        deps/hiredis/net.c
        deps/hiredis/sds.c
        )

ADD_LIBRARY(lib_hiredis ${hiredis_SOURCE_FILES})
SET_TARGET_PROPERTIES(lib_hiredis PROPERTIES LINKER_LANGUAGE C)



SET(redis-benchmark_files
        src/redis-benchmark.c
        src/ae.c
        src/anet.c
        src/sds.c
        src/adlist.c
        src/zmalloc.c
        )

ADD_EXECUTABLE(redis-benchmark ${redis-benchmark_files})
TARGET_LINK_LIBRARIES(redis-benchmark hiredis jemalloc)

SET(redis-cli_files
        src/redis-cli.c
        src/anet.c
        src/sds.c
        src/adlist.c
        src/zmalloc.c
        src/release.c
        src/crc64.c
        src/ae.c
        deps/linenoise/linenoise.c
        )

ADD_EXECUTABLE(redis-cli ${redis-cli_files})
TARGET_LINK_LIBRARIES(redis-cli hiredis m)

SET(redis-check-dump_files
        src/redis-check-dump.c
        src/lzf_c.c
        src/lzf_d.c
        src/crc64.c
        )
ADD_EXECUTABLE(redis-check-dump ${redis-check-dump_files})
set_property(TARGET redis-check-dump PROPERTY C_STANDARD 99)

SET(redis-check-aof_files src/redis-check-aof.c)
ADD_EXECUTABLE(redis-check-aof ${redis-check-aof_files})
set_property(TARGET redis-check-aof  PROPERTY C_STANDARD 99)

SET(redis-server_files
        src/adlist.c
        src/ae.c
#        src/ae_epoll.c
        src/anet.c
        src/dict.c
        src/redis.c
        src/sds.c
        src/zmalloc.c
        src/lzf_c.c
        src/lzf_d.c
        src/pqsort.c
        src/zipmap.c
        src/sha1.c
        src/ziplist.c
        src/release.c
        src/networking.c
        src/util.c
        src/object.c
        src/db.c
        src/replication.c
        src/rdb.c
        src/t_string.c
        src/t_list.c
        src/t_set.c
        src/t_zset.c
        src/t_hash.c
        src/config.c
        src/aof.c
        src/pubsub.c
        src/multi.c
        src/debug.c
        src/sort.c
        src/intset.c
        src/syncio.c
        src/cluster.c
        src/crc16.c
        src/endianconv.c
        src/slowlog.c
        src/scripting.c
        src/bio.c
        src/rio.c
        src/rand.c
        src/memtest.c
        src/crc64.c
        src/bitops.c
        src/sentinel.c
        src/notify.c
        src/setproctitle.c
        src/blocked.c
        src/hyperloglog.c
        src/latency.c
        src/sparkline.c
        )
ADD_EXECUTABLE(redis-server ${redis-server_files})
TARGET_LINK_LIBRARIES(redis-server hiredis jemalloc lua m pthread leveldb)
set_property(TARGET redis-server PROPERTY C_STANDARD 99)



{% endhighlight %}
