# LRU缓存

## 问题
设计一种键、值对的容器数据结构，支持 $get$ 和 $set$ 操作：

* $get(key)$ - 如果缓存中存在 $key$ ，则返回其对应值，否则返回 $-1$

* $set(key, value)$ - 如果 $key$ 已存在则更新其对应的值，否则加入新的键值对 $(key, value)$ ，如果容器已达到最大容量则踢出上次访问距离最远的键值对


## 分析
由于需要刻画访问顺序，并且需要快速将容器中某个元素移到头部，故可以使用键、值对的双向链表`std::list`来实现。

进一步，可以使用一个`std::unordered_map`来维护 $key$ 到链表节点（键值对）的迭代器的哈希。


## 代码
```cpp
class LRUcache {
   public:
    LRUcache(int capacity) { this->capacity = capacity; }
    int Get(int key) {
        if (cache_map.find(key) == cache_map.end()) return -1;
        cache_list.splice(cache_list.begin(), cache_list, cache_map[key]);
        cache_map[key] = cache_list.begin();
        return cache_map[key]->v;
    }
    void Set(int key, int value) {
        if (cache_map.find(key) == cache_map.end()) {
            if (cache_list.size() == capacity) {
                cache_map.erase(cache_list.back().k);
                cache_list.pop_back();
            }
            cache_list.push_front(CacheNode(key, value));
            cache_map[key] = cache_list.begin();
        } else {
            cache_map[key]->v = value;
            cache_list.splice(cache_list.begin(), cache_list, cache_map[key]);
            cache_map[key] = cache_list.begin();
        }
    }

   private:
    struct CacheNode {
        int k, v;
        CacheNode() {}
        CacheNode(int _k, int _v) : k(_k), v(_v) {}
    };
    int capacity;
    list<CacheNode> cache_list;
    unordered_map<int, list<CacheNode>::iterator> cache_map;
};
```

* $CacheNode$ - 键值对（链表节点）

* $cache\_list$ - 内部链表

* $cache\_map$ - 从 $key$ 到键值对地址的哈希
