# memcached勉強会

### 到達地点
* 参加者がmemcachedの概要について把握出来ている
* 参加者が以下の実装について把握している
	* セッション管理
	* メモリ管理
	* 排他制御
* 参加者がmemcachedをHack出来る

### memcachedの概要
[memcachedを知り尽くす](http://gihyo.jp/dev/feature/01/memcached/0001) がわかりやすい

### リポジトリ
現在は github で管理されている
[github.com/memcached/memcached](https://github.com/memcached/memcached)

### ビルド・動作手順
* Mac OS X

	```sh
	brew instal automake # aclocalをインストールするために必要
	git clone git@github.com:memcached/memcached.git
	cd memcached
	./autogen.sh # configure を生成
	./configure # Makefile を生成
	make # ビルド実行
	./memcached
	```
	
* Linux

	```sh
	brew instal automake # aclocalをインストールするために必要
	git clone git@github.com:memcached/memcached.git
	cd memcached
	./autogen.sh # configure を生成
	./configure # Makefile を生成
	make # ビルド実行
	./memcached
	```
 
### ソースコード
1. セッション管理

memcachedのセッション管理は、以下のところに大体記述されている
* memcached.h/c
* threads.h/c`c
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

この辺りの以下が参考になる
* server_socket
* event_handler


mainからは
server_socketsを起動していて、ここから各種コネクションイベントハンドリングを行う

3. メモリ管理

以下の記事が参考になる。
[memcachedを知り尽くす 第2回　memcachedのメモリストレージを理解する](http://gihyo.jp/dev/feature/01/memcached/0002) 


4. 排他制御

この辺りのドキュメントが非常に参考になる
https://github.com/memcached/memcached/blob/master/doc/threads.txt

https://github.com/memcached/memcached/blob/master/thread.c
thread周りの実装はこの辺
だいたい、セッション管理とメモリ管理で使っている。

