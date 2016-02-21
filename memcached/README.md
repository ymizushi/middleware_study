# memcached勉強会

### 到達地点
* 参加者がmemcachedの概要について把握出来ている
* 参加者が以下の実装について把握している
	* メモリ管理
	* セッション管理
	* 排他制御
* 参加者がmemcachedをHack出来る

### memcachedの概要
[memcachedを知り尽くす](http://gihyo.jp/dev/feature/01/memcached/0001) がわかりやすい.

### リポジトリ

現在は [github.com/memcached/memcached](https://github.com/memcached/memcached) で管理されている.

### memcachedプロトコル仕様

プロトコル仕様は[protocol.txt](https://github.com/memcached/memcached/blob/master/doc/protocol.txt)から参照できる.


memcachedはクライアントとの通信において、TCPまたはUDP上でテキストベースとバイナリベースのプロトコルが利用できる.
セッション切断はクライアント側の責任になっている.
クライアント側でセッションを再利用したければクライアント側でセッションを自由に再利用できる.

#### 面白いと思った仕様

* キーは250文字までの制限がある
* キャッシュ時間は秒で指定するが、30日を超える秒数を指定すると、UNIX TIMEとしてキャッシュが設定される



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
	
* Linux(Kernel 2.4以降）

	```sh
	sudo yum install automake # ubuntu: sudo apt-get install build-essentials
	sudo yum install libtool
	git clone git@github.com:memcached/memcached.git
	cd memcached
	./autogen.sh # configure を生成
	./configure # Makefile を生成
	make # ビルド実行
	./memcached
	```
 
### ソースコード

1. メモリ管理

	memcachedのメモリ管理で特筆すべき機能は以下の２つ.
	* Slab allocator
	* LRU

	メモリ管理については以下の記事が参考になる
	
	[memcachedを知り尽くす 第2回　memcachedのメモリストレージを理解する](http://gihyo.jp/dev/feature/01/memcached/0002) 
	
	Version 1.4.25から 新しいLRUエンジンが搭載された
	* https://github.com/memcached/memcached/blob/master/doc/new_lru.txt

2. セッション管理

	memcachedのセッション管理は、以下のところに大体記述されている
	* memcached.h/c
	* threads.h/c
	
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
	
	この辺りの以下が参考になる
	* server_socket
	* event_handler
	
	
	mainからは
	server_socketsを起動していて、ここから各種コネクションイベントハンドリングを行う


3. 排他制御

	この辺りのドキュメントが非常に参考になる
	https://github.com/memcached/memcached/blob/master/doc/threads.txt
	
	https://github.com/memcached/memcached/blob/master/thread.c
	thread周りの実装はこの辺
	だいたい、セッション管理とメモリ管理で使っている。
	
