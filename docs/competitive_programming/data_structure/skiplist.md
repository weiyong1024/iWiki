# 跳表

## 问题

实现一种集合数据结构，支持如下操作：  
1. 插入 / Insert  
2. 删除 / Delete  
3. 查找 / Search  
4. 按序遍历

显然， **平衡树**（如红黑树）类的数据结构组织数据可以打满足上述要求，增、删、查的时间复杂度都是 $O(\log n)$，而顺序迭代额复杂度为 $O(1)$ 。除此之外还有什么思路？

## 跳表

通过在有序链表上增加若干级索引层实现上述需求。

具体地，在有序链表上增加给定层数上限的索引，将部分位于低层的节点按照概率上移形成对下级的索引，由于随机性跳表的最终形态往往是不唯一的。

## C++实现

实现成员为`int`型的跳表`SkipList`类：
```cpp
class Node {
   public:
    int key;
    // Array to hold pointers to node of different level
    Node **forward;
    Node(int, int);
};

Node::Node(int key, int level) {
    this->key = key;
    // Allocate memory to forward
    forward = new Node *[level + 1];
    // Fill forward array with 0(NULL)
    memset(forward, 0, sizeof(Node *) * (level + 1));
};

class SkipList {
   public:
    SkipList(int, float);
    friend ostream &operator<<(ostream &out, const SkipList &src);

    void Insert(int);
    void Delete(int);
    bool Search(int);

   private:
    int RandomLevel();
    Node *CreateNode(int, int);

    // Maximum level for this skip list
    int MAXLVL;
    // P is the fraction of the nodes with level
    // i pointers also having level i+1 pointers
    float P;
    // current level of skip list
    int level;
    // pointer to header node
    Node *header;
};

SkipList::SkipList(int MAXLVL, float P) {
    this->MAXLVL = MAXLVL;
    this->P = P;
    level = 0;
    // create header node and initialize key to -1
    header = new Node(-1, MAXLVL);
};

int SkipList::RandomLevel() {
    float r = (float)rand() / RAND_MAX;
    int lvl = 0;
    while (r < P && lvl < MAXLVL) {
        lvl++;
        r = (float)rand() / RAND_MAX;
    }
    return lvl;
};

Node *SkipList::CreateNode(int key, int level) {
    Node *n = new Node(key, level);
    return n;
};

void SkipList::Insert(int key) {
    Node *current = header;

    // create update array and initialize it
    Node *update[MAXLVL + 1];
    memset(update, 0, sizeof(Node *) * (MAXLVL + 1));

    /*    start from highest level of skip list
        move the current pointer forward while key
        is greater than key of node next to current
        Otherwise inserted current in update and
        move one level down and continue search
    */
    for (int i = level; i >= 0; i--) {
        while (current->forward[i] != NULL && current->forward[i]->key < key)
            current = current->forward[i];
        update[i] = current;
    }

    /* reached level 0 and forward pointer to
       right, which is desired position to
       insert key.
    */
    current = current->forward[0];

    /* if current is NULL that means we have reached
       to end of the level or current's key is not equal
       to key to insert that means we have to insert
       node between update[0] and current node */
    if (current == NULL || current->key != key) {
        // Generate a random level for node
        int rlevel = RandomLevel();

        /* If random level is greater than list's current
           level (node with highest level inserted in
           list so far), initialize update value with pointer
           to header for further use */
        if (rlevel > level) {
            for (int i = level + 1; i < rlevel + 1; i++) update[i] = header;

            // Update the list current level
            level = rlevel;
        }

        // create new node with random level generated
        Node *n = CreateNode(key, rlevel);

        // insert node by rearranging pointers
        for (int i = 0; i <= rlevel; i++) {
            n->forward[i] = update[i]->forward[i];
            update[i]->forward[i] = n;
        }
        // cout << "Successfully Inserted key " << key << "\n";
    }
};

void SkipList::Delete(int key) {
    Node *current = header;

    // create update array and initialize it
    Node *update[MAXLVL + 1];
    memset(update, 0, sizeof(Node *) * (MAXLVL + 1));

    /*    start from highest level of skip list
        move the current pointer forward while key
        is greater than key of node next to current
        Otherwise inserted current in update and
        move one level down and continue search
    */
    for (int i = level; i >= 0; i--) {
        while (current->forward[i] != NULL && current->forward[i]->key < key)
            current = current->forward[i];
        update[i] = current;
    }

    /* reached level 0 and forward pointer to
       right, which is possibly our desired node.*/
    current = current->forward[0];

    // If current node is target node
    if (current != NULL and current->key == key) {
        /* start from lowest level and rearrange
           pointers just like we do in singly linked list
           to remove target node */
        for (int i = 0; i <= level; i++) {
            /* If at level i, next node is not target
               node, break the loop, no need to move
              further level */
            if (update[i]->forward[i] != current) break;

            update[i]->forward[i] = current->forward[i];
        }

        // Remove levels having no elements
        while (level > 0 && header->forward[level] == 0) level--;
        // cout << "Successfully deleted key " << key << "\n";
    }
};

// Search for element in skip list
bool SkipList::Search(int key) {
    Node *current = header;

    /*    start from highest level of skip list
        move the current pointer forward while key
        is greater than key of node next to current
        Otherwise inserted current in update and
        move one level down and continue search
    */
    for (int i = level; i >= 0; i--) {
        while (current->forward[i] && current->forward[i]->key < key)
            current = current->forward[i];
    }

    /* reached level 0 and advance pointer to
       right, which is possibly our desired node*/
    current = current->forward[0];

    // If current node have key equal to
    // search key, we have found our target node
    if (current and current->key == key)
        return true;
    else
        return false;
};

// Display SkipList by overriding '<<' operator
ostream &operator<<(ostream &out, const SkipList &src) {
    cout << "\n*****Skip List*****"
         << "\n";
    for (int i = 0; i <= src.level; i++) {
        Node *node = src.header->forward[i];
        cout << "Level " << i << ": ";
        while (node != NULL) {
            cout << node->key << " ";
            node = node->forward[i];
        }
        cout << "\n";
    }
    return out;
}
```

测试代码：
```cpp
int main() {
    // Seed random number generator
    srand((unsigned)time(0));

    // create SkipList object with MAXLVL and P
    SkipList lst(3, 0.5);

    lst.Insert(3);
    lst.Insert(6);
    lst.Insert(7);
    lst.Insert(9);
    lst.Insert(12);
    lst.Insert(19);
    lst.Insert(17);
    lst.Insert(26);
    lst.Insert(21);
    lst.Insert(25);
    cout << lst << endl;

    // Search for node 19
    cout << "search 19: ";
    cout << lst.Search(19) << endl;

    // Delete node 19
    lst.Delete(19);
    cout << lst << endl;

    // Search for node 19
    cout << "search 19: ";
    cout << lst.Search(19) << endl;
}
```

输出：
```cpp
*****Skip List*****
Level 0: 3 6 7 9 12 17 19 21 25 26 
Level 1: 3 7 9 19 21 
Level 2: 19 

search 19: 1

*****Skip List*****
Level 0: 3 6 7 9 12 17 21 25 26 
Level 1: 3 7 9 21 

search 19: 0
```