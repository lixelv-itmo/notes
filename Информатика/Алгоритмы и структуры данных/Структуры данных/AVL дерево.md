## Определение
--- start-multi-column: ExampleRegion1
```column-settings
number of columns: 2
Border: false
Shadow: false
```

AVL бинарное дерево поиска ([англ.](https://ru.wikipedia.org/wiki/%D0%90%D0%BD%D0%B3%D0%BB%D0%B8%D0%B9%D1%81%D0%BA%D0%B8%D0%B9_%D1%8F%D0%B7%D1%8B%D0%BA "Английский язык") AVL BST) - это сбалансированное [[бинарное дерево поиска]], где все операции вставки и удаления гарантированно работают за $O(\log n)$. Основная идея в хранении высоты каждого поддерева и если высота правого ребенка - высота левого (баланс) больше 1 или меньше -1, то происходит ребалансировка с помощью правых и левых поворотов.

--- end-column ---

![](avl_tree.png)
--- end-multi-column
## Реализация на C++
```cpp
#include <iostream>

template <typename T>
struct Node {
  T val;
  Node *left = nullptr;
  Node *right = nullptr;
  int height = 1;

  Node(T v) : val(v) {}
};

template <typename T, bool (*Comp)(const T &, const T &)>
class AVLAbstract {
public:
  AVLAbstract() { root = nullptr; }

  ~AVLAbstract() { clear(root); }

  void insert(T val) { root = insertNode(root, val); }
  T *search(T val) { return searchNode(root, val); }
  bool remove(T val) {
    bool removed = false;
    root = removeNode(root, val, removed);
    return removed;
  }

  void print() {
    print(root, 0);
    std::cout << std::endl;
  }

private:
  Node<T> *root;

  int compare(T a, T b) { return Comp(a, b) ? -1 : (Comp(b, a) ? 1 : 0); }

  int getHeight(Node<T> *node) { return node ? node->height : 0; }

  int getBalance(Node<T> *node) {
    if (!node)
      return 0;
    return getHeight(node->left) - getHeight(node->right);
  }

  void updateHeight(Node<T> *node) {
    if (!node)
      return;
    int hl = getHeight(node->left);
    int hr = getHeight(node->right);
    node->height = std::max(hl, hr) + 1;
  }

  Node<T> *rotateRight(Node<T> *y) {
    Node<T> *x = y->left;
    Node<T> *T2 = x->right;

    x->right = y;
    y->left = T2;

    updateHeight(y);
    updateHeight(x);

    return x;
  }

  Node<T> *rotateLeft(Node<T> *x) {
    Node<T> *y = x->right;
    Node<T> *T2 = y->left;

    y->left = x;
    x->right = T2;

    updateHeight(x);
    updateHeight(y);

    return y;
  }

  Node<T> *balanceNode(Node<T> *node) {
    if (!node)
      return node;

    updateHeight(node);
    int balance = getBalance(node);

    // LL
    if (balance > 1 && getBalance(node->left) >= 0) {
      return rotateRight(node);
    }

    // LR
    if (balance > 1 && getBalance(node->left) < 0) {
      node->left = rotateLeft(node->left);
      return rotateRight(node);
    }

    // RR
    if (balance < -1 && getBalance(node->right) <= 0) {
      return rotateLeft(node);
    }

    // RL
    if (balance < -1 && getBalance(node->right) > 0) {
      node->right = rotateRight(node->right);
      return rotateLeft(node);
    }

    return node;
  }

  Node<T> *insertNode(Node<T> *node, T val) {
    if (!node) {
      return new Node<T>(val);
    }

    int r = compare(val, node->val);
    if (r < 0) {
      node->left = insertNode(node->left, val);
    } else if (r > 0) {
      node->right = insertNode(node->right, val);
    } else {
      return node;
    }

    return balanceNode(node);
  }

  T *searchNode(Node<T> *node, T val) {
    if (!node)
      return nullptr;

    int r = compare(val, node->val);
    if (r < 0)
      return searchNode(node->left, val);
    if (r > 0)
      return searchNode(node->right, val);
    return &(node->val);
  }

  Node<T> *findMin(Node<T> *node) {
    while (node && node->left) {
      node = node->left;
    }
    return node;
  }

  Node<T> *removeNode(Node<T> *node, T val, bool &removed) {
    if (!node) {
      removed = false;
      return nullptr;
    }

    int r = compare(val, node->val);
    if (r < 0) {
      node->left = removeNode(node->left, val, removed);
    } else if (r > 0) {
      node->right = removeNode(node->right, val, removed);
    } else {
      removed = true;
      if (!node->left && !node->right) {
        delete node;
        return nullptr;
      } else if (!node->left) {
        Node<T> *temp = node->right;
        delete node;
        return temp;
      } else if (!node->right) {
        Node<T> *temp = node->left;
        delete node;
        return temp;
      } else {
        Node<T> *successor = findMin(node->right);
        node->val = successor->val;
        node->right = removeNode(node->right, successor->val, removed);
      }
    }

    return balanceNode(node);
  }

  void print(Node<T> *node, int depth) {
    if (node == nullptr) {
      return;
    }

    print(node->right, depth + 1);

    for (int i = 0; i < depth; i++) {
      std::cout << "   ";
    }
    std::cout << node->val << std::endl;

    print(node->left, depth + 1);
  };

  void clear(Node<T> *node) {
    if (!node)
      return;
    clear(node->left);
    clear(node->right);
    delete node;
  }
};

template <typename T> bool lessCompare(const T &a, const T &b) { return a < b; }
template <typename T> using AVLTree = AVLAbstract<T, lessCompare<T>>;
```