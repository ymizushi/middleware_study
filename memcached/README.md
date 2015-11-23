# memcached

1. memcachedの概要

2. セッション管理
memcachedのセッション管理は、以下のところに大体記述されている
    - emcached.h/c
    - threads.h/c
キモは
```c
/* event handling, network IO */
static void event_handler(const int fd, const short which, void *arg);
static void conn_close(conn *c);
static void conn_init(void);
static bool update_event(conn *c, const int new_flags);
static void complete_nread(conn *c);
static void process_command(conn *c, char *command);
static void write_and_free(conn *c, char *buf, int bytes);
static int ensure_iov_space(conn *c);
static int add_iov(conn *c, const void *buf, int len);
static int add_msghdr(conn *c);
static void write_bin_error(conn *c, protocol_binary_response_status err,
                            const char *errstr, int swallow);
static void conn_free(conn *c);
```
このへんの実装でござる

server_socket

event_handler


mainからは
server_socketsを起動していて、ここから各種コネクションイベントハンドリングを行う

3. メモリ管理
この辺りの記事が非常に参考になる
http://gihyo.jp/dev/feature/01/memcached/0002

4. 排他制御
この辺りのドキュメントが非常に参考になる
https://github.com/memcached/memcached/blob/master/doc/threads.txt

https://github.com/memcached/memcached/blob/master/thread.c
thread周りの実装はこの辺
だいたい、セッション管理とメモリ管理で使っている。

