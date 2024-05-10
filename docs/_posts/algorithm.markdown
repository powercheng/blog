### 321 create maximum number
1. 最开始的想法，每一轮找到最佳值，在两个数组允许范围内，找到最大值。但是如果有两个相同值，就不知道哪个是最优解，必须比较两个子问题。
2. 这个解法会超时，当k非常大的时候，进行优化，当选择只有一个数组时候，单独处理，仍然超时
3. 查看官方的方法，首先可以知道从一个数组找到k值create maximum number
4. 从第一个数组最少取Math.max(0, k-n)个，最多取Math.min(len, k)个。这是一个for循环，第一个数组取i个值，第二个数组取k-i个值。
5. 获取的两个sequence进行合并，合并后的值判断是否为最大值

```
    public int[] maxNumber(int[] nums1, int[] nums2, int k) {
        int m = nums1.length, n = nums2.length;
        int[] maxSubsequence = new int[k];
        int start = Math.max(0, k - n), end = Math.min(k, m);
        for (int i = start; i <= end; i++) {
            int[] subsequence1 = maxSubsequence(nums1, i);
            int[] subsequence2 = maxSubsequence(nums2, k - i);
            int[] curMaxSubsequence = merge(subsequence1, subsequence2);
            if (compare(curMaxSubsequence, 0, maxSubsequence, 0) > 0) {
                System.arraycopy(curMaxSubsequence, 0, maxSubsequence, 0, k);
            }
        }
        return maxSubsequence;

    }

        // union and find
        public void union(int p, int q) {
            int rootP = find(p);
            int rootQ = find(q);
            if (rootP == rootQ) return;
            parent[rootQ] = rootP;
            count--;

        }

        public int find(int x) {
            while (parent[x] != x) {
                parent[x] = parent[parent[x]];  // compression
                x = parent[x];
            }
            return parent[x];
        }

```
### Segment tree
also known as a statistic tree, is a tree data structure used for storing information about intervals, or segments. 
It allows querying which of the stored segments contain a given point.
叶子节点是数据，父节点统计区间，或者极值

### AVL tree

```
class AVLTree{
    class AVLNode {
        int val;
        int count;
        int duplicate;
        int leftBound;
        int rightBound;
        int height;
        AVLNode left;
        AVLNode right;
        public AVLNode(int x) {
            this.val = x;
            this.count = 1;
            this.duplicate = 1;
            this.leftBound = x;
            this.rightBound = x;
        }

    }
    AVLNode root;
    public int getHeight(AVLNode node) {return node == null ? -1 : node.height;}
    public int getCount(AVLNode node) {return node == null ? 0 : node.count;}
    public void setHeight(AVLNode node) {
        node.height = Math.max(getHeight(node.left), getHeight(node.right))+1;
        node.count = node.duplicate + getCount(node.left) + getCount(node.right);
        node.leftBound = node.left == null ? node.val: node.left.leftBound;
        node.rightBound = node.right == null ? node.val: node.right.rightBound;
    }
    public int getBalance(AVLNode node) {
        if (node == null) return 0;
        return getHeight(node.left) - getHeight(node.right);
    }

    public void insert(int val) {
        root = insert(root, val);
    }
    public AVLNode insert(AVLNode node, int val) {
        if (node == null) return new AVLNode(val);
        if (node.val < val) {
            node.right = insert(node.right, val);
        } else if (node.val > val) {
            node.left = insert(node.left, val);
        } else {
            node.duplicate++;
            node.count++;
            return node;
        }
        setHeight(node);
        node = rotate(node);
        return node;
    }
    public AVLNode rotate(AVLNode node) {
        int balance = getBalance(node);
        if (balance > 1) {
            if (getBalance(node.left) < 0) leftRotate(node.left);
            return rightRotate(node);
        } else if (balance < -1) {
            if (getBalance(node.right) > 0) rightRotate(node.right);
            return leftRotate(node);
        } else {
            return node;
        }
    }

    public AVLNode leftRotate(AVLNode node) {
        AVLNode rightChild = node.right;
        AVLNode grandChild = rightChild.left;
        rightChild.left = node;
        node.right = grandChild;
        setHeight(node);
        setHeight(rightChild);
        return rightChild;
    }

    public AVLNode rightRotate(AVLNode node) {
        AVLNode leftChild = node.left;
        AVLNode grandChild = leftChild.right;
        leftChild.right = node;
        node.left = grandChild;
        setHeight(node);
        setHeight(leftChild);
        return leftChild;
    }

    public int searchRange(int low, int high) {
        return searchRange(root, low, high);
    }

    public int searchRange(AVLNode node, int left, int right) {

        if (node == null) return 0;
        int x = node.count;
        if (left > node.rightBound || right < node.leftBound) return 0;
        if (left <= node.leftBound && right >= node.rightBound) return node.count;
        int res = 0;
        int val = node.val;
        if (left > val) return searchRange(node.right, left, right);
        if (right < val) return searchRange(node.left, left, right);
        return searchRange(node.left, left, right) + node.duplicate + searchRange(node.right, left, right);
    }

}
```
### 3. Longest Substring Without Repeating Characters    left-right pointer
左右两个节点，判断右节点值是否在范围内，更新范围
```
int left = 0, right = 1;
Arrays.fill(map, -1);
map[chars[left]] = 0;
if (map[chars[right]] >= left) left = map[chars[right]] + 1;
```
### 4. find the median of two sorted array               recursion shrink the range
更宽泛的题目就是，查找kth值。线性算法是最简单的，但是要求log
每次比较两个数组 k/2位置处的值，讲小于部分的范围去除掉
### 5. find the longest palindromic substring            manacher(arm[]数组)
1. brute force: search all substring(i,j)
2. dynamic programming, the substring len from 1 to n, N square time complexity
3. manacher algorithm  加入#，arm[],center+right+mirror,先取可以确定到的最大范围（math.min）然后在对称更新。
```java
class Solution {
    public String longestPalindrome(String s) {
        StringBuilder sb = new StringBuilder("#");
        for (int i = 0; i < s.length(); i++) {
            sb.append(s.charAt(i));
            sb.append("#");
        }
        int[] arm = new int[sb.length()];
        int right = 2, center = 1;
        for (int i = 2; i < sb.length(); i++) {
            int mirror = 2*center-i;
            if (right > i) {
                arm[i] = Math.min(arm[mirror], right-i);
            }
            while (i-arm[i]-1>=0 && i+arm[i]+1 < sb.length() && sb.charAt(i-arm[i]-1) == sb.charAt(i+arm[i]+1)) {
                arm[i]++;
            }
            if (right < i+arm[i]) {
                center = i;
                right = i+arm[i];
            }
        }
    }
}
```
### 3Sum closest
1. Arrays.sort(nums);
2. Arrays.binarySearch(nums, j+1, sz, complement); // if not found, return - (insertion) - 1
   返回值为负数，～idx为数组插入位置，原本插入位置的值向后移动
3. 

### 214. shortest palindrome
you can convert s to a palindrome by adding characters in front of it.
换一句就是找到最长前缀palindrome，也可以使用manacher


### 测试一些内容
### 冲突内容3
### 增加内容
### 增加内容3
### 增加内容6
