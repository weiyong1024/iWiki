# LRU缓存

## 问题
设计一种键、值对的容器数据结构，支持 $get$ 和 $set$ 操作：

* $Get(key)$ - 如果缓存中存在 $key$ ，则返回其对应值，否则返回 $-1$

* $Set(key, value)$ - 如果 $key$ 已存在则更新其对应的值，否则加入新的键值对 $(key, value)$ ，如果容器已达到最大容量则踢出上次访问距离最远的键值对


## 分析
由于需要刻画访问顺序，并且需要快速将容器中某个元素移到头部，故可以使用键、值对的双向链表`std::list`来实现。

进一步，可以使用一个`std::unordered_map`来维护 $key$ 到链表节点（键值对）的迭代器的哈希。


## 代码
```cpp
template <typename T>
class LRUCache {
 public:
  LRUCache(int _capacity, T _find_failure)
      : capacity(_capacity), find_failure(_find_failure) {}

  T Get(int key) {
    if (cache_map.find(key) == cache_map.end()) {
      return this->find_failure;
    }
    cache_list.splice(cache_list.begin(), cache_list, cache_map[key]);
    return cache_map[key]->value;
  }

  void Set(int key, T value) {
    if (cache_map.find(key) == cache_map.end()) {
      if (cache_list.size() == capacity) {
        cache_map.erase(cache_list.back().key);
        cache_list.pop_back();
      }
      cache_list.push_front(CacheNode(key, value));
      cache_map[key] = cache_list.begin();
    } else {
      cache_map[key]->value = value;
      cache_list.splice(cache_list.begin(), cache_list, cache_map[key]);
      cache_map[key] = cache_list.begin();
    }
  }

 private:
  struct CacheNode {
    int key;
    T value;
    CacheNode() {}
    CacheNode(int _key, int _value) : key(_key), value(_value) {}
  };

  int capacity;
  T find_failure;
  list<CacheNode> cache_list;
  unordered_map<int, typename list<CacheNode>::iterator> cache_map;
};
```

* $CacheNode$ - 键值对（链表节点）

* $cache\_list$ - 内部链表

* $cache\_map$ - 从 $key$ 到链表 $cache\_list$ 迭代器的映射

## 测试

测试代码：
```cpp
using ll = long long;

int main() {
  LRUCache<ll> lru_cache = LRUCache<ll>(5, -1l);

  for (int i = 0; i < 5; i++) {
    lru_cache.Set(i, 1l * i);
    cout << "lru_cache.Set(" << i << ", " << i << ")" << endl;
  }

  cout << "--------------------" << endl;
  for (int i = 0; i < 5; i++) {
    ll value = lru_cache.Get(i);
    cout << "lru_cache.Get(" << i << "): " << value << endl;
  }

  cout << "--------------------" << endl;
  for (int i = 2; i < 7; i++) {
    lru_cache.Set(i, 1l * i);
    cout << "lru_cache.Set(" << i << ", " << i << ")" << endl;
  }

  cout << "--------------------" << endl;
  for (int i = 0; i < 7; i++) {
    ll value = lru_cache.Get(i);
    cout << "lru_cache.Get(" << i << "): " << value << endl;
  }

  return 0;
}
```

输出：
```
lru_cache.Set(0, 0)
lru_cache.Set(1, 1)
lru_cache.Set(2, 2)
lru_cache.Set(3, 3)
lru_cache.Set(4, 4)
--------------------
lru_cache.Get(0): 0
lru_cache.Get(1): 1
lru_cache.Get(2): 2
lru_cache.Get(3): 3
lru_cache.Get(4): 4
--------------------
lru_cache.Set(2, 2)
lru_cache.Set(3, 3)
lru_cache.Set(4, 4)
lru_cache.Set(5, 5)
lru_cache.Set(6, 6)
--------------------
lru_cache.Get(0): -1
lru_cache.Get(1): -1
lru_cache.Get(2): 2
lru_cache.Get(3): 3
lru_cache.Get(4): 4
lru_cache.Get(5): 5
lru_cache.Get(6): 6
```
## 典型适用场景

如微信表情包列表等满足用户习惯性输入的场景。
