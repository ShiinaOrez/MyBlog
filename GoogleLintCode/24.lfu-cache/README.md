## Description

LFU (Least Frequently Used) is a famous cache eviction algorithm.

For a cache with capacity k, if the cache is full and need to evict a key in it, the key with the lease frequently used will be kicked out.

Implement `set` and `get` method for LFU cache.

## Solution

LFU缓存策略, Least Frequently Used, 最少频次使用.

实现方法为主从链表+哈希表. 主链表记录访问次数, 从链表记录时间先后. 哈希表记录(key, (value, 主链表ptr, 从链表ptr))

当一个key被使用的时候, 对应的从哈希表中查找到值, 从主从链表中进行位置调整.
