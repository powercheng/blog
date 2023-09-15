---
layout: left-right-layout
title:  "rang sum query"
date:   2023-09-14 21:00:00
categories: jekyll update
---
Given an integer array nums, handle multiple queries of the following types:

Update the value of an element in nums.
Calculate the sum of the elements of nums between indices left and right inclusive where left <= right.

### method 1:
block process 
1. 设数组大小为n，我们将数组nums分成多个块，每个块大小size，最后一个块的大小为剩余的不超过size的元素数目，那么块的总数为 n/size 的上限，用一个数组sum保存每个块的元素和。
2. update方法，更新sum，其中对应的下标为 index/size。时间复杂度是O(1).
3. sumRange方法，加上前一部分块的内容，中间整体块，以及后一部分块的内容。时间复杂度是O(size+n/size)，size+n/size >=  $\sqrt{2}$
tree array
1. 树状数组
```

```