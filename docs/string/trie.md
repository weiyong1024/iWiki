# 字典树


## 问题
给定字符集，设计一种数据结构，实现单词集合，支持以下三种操作：

* 加入/Add - 将单词加入集合

* 删除/Remove - 将单词从集合中去除

* 查找/Find - 判断目标单词是否在集合中


## 代码
下面的字典树，支持由 $26$ 个小写英文字母构成的单词的增、删、查操作。对于其他字符集，只需改变类中从字符到其在字符集中的序号的映射即可。
```cpp
class Trie {
   public:
    Trie() {}
    virtual ~Trie() { RemoveTrie(root); }
    void Add(string word) {
        TrieNode *cur = root;
        for (int i = 0; i < word.size(); i++) {
            if (cur->next[word[i] - 'a'] != nullptr)
                cur = cur->next[word[i] - 'a'];
            else {
                TrieNode *tmp = new TrieNode(false);
                cur->next[word[i] - 'a'] = tmp;
                cur = tmp;
            }
            if (i == word.size() - 1) cur->isword = true;
        }
    }
    void Remove(string word) {
        TrieNode *cur = root;
        for (int i = 0; i < word.size(); i++) {
            if (cur->next[word[i] - 'a'] == nullptr) {
                cout << "\"" << word << "\" "
                     << "was not in Trie." << endl;
                return;
            }
            cur = cur->next[word[i] - 'a'];
        }
        cur->isword = false;
    }
    bool Find(string word) {
        TrieNode *cur = root;
        for (int i = 0; i < word.size(); i++) {
            if (cur->next[word[i] - 'a'] == nullptr) return false;
            cur = cur->next[word[i] - 'a'];
        }
        return cur->isword;
    }

   private:
    static const int alphabat_size = 26;
    struct TrieNode {
        bool isword;
        TrieNode *next[alphabat_size];
        TrieNode() {}
        TrieNode(bool _isword) : isword(_isword) {
            for (int i = 0; i < alphabat_size; i++) next[i] = nullptr;
        }
    };
    TrieNode *root = new TrieNode(false);
    void RemoveTrie(TrieNode *cur) {
        for (int i = 0; i < alphabat_size; i++)
            if (cur->next[i] != nullptr) RemoveTrie(cur->next[i]);
        delete cur;
    }
};
```

需要注意

* `Trie`类中用链式结构在内存中维护一棵树，析构函数中要递归删除。


## 算法
字典树是一棵度数等于字符表大小的多叉树，增、删、查的复杂度都是单词长度 $l$ 的线性函数，即 $O(l)$ 。


## 应用
* 从字典树的的根节点DFS可以得到所有单词的字典序排序。
