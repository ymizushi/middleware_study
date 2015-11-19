# memcached

1. memcachedの概要

2. セッション管理



3. メモリ管理
この辺りの記事が非常に参考になる
http://gihyo.jp/dev/feature/01/memcached/0002

4. 排他制御
この辺りのドキュメントが非常に参考になる
https://github.com/memcached/memcached/blob/master/doc/threads.txt

https://github.com/memcached/memcached/blob/master/thread.c
thread周りの実装はこの辺
だいたい、セッション管理とメモリ管理で使っている。

