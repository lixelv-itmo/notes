## Определение
AVL-сортировка ([англ.](https://ru.wikipedia.org/wiki/%D0%90%D0%BD%D0%B3%D0%BB%D0%B8%D0%B9%D1%81%D0%BA%D0%B8%D0%B9_%D1%8F%D0%B7%D1%8B%D0%BA "Английский язык") AVL sort) - это алгоритм устойчивой сортировки, основанный на структуре данных [[AVL дерево|AVL дерево]]. Алгоритм заключается в построении сбалансированного бинарного дерева поиска из элементов массива с последующим обходом дерева в симметричном порядке (in-order traversal) для получения отсортированной последовательности.

---
## Сложность и визуализация

- **Худшее время:** $O(n \log n)$
- **Лучшее время:**   $O(n \log n)$
- **Среднее время:**  $O(n \log n)$
- **Затраты памяти:** $O(n)$

---
## Реализация на C++
```cpp
void avl_sort(int arr[], int n) {
	AVLTree tree;
	
	for (int i = 0; i < n; i++) {
		tree.insert(arr[i]);
	}
	
	int index = 0;
	tree.inorder(arr, index);
}
```

---
## [Wiki](https://ru.wikipedia.org/wiki/АВЛ-дерево) [DSA](https://www.w3schools.com/dsa/dsa_data_avltrees.php)