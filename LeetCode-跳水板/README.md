## 跳水板

#### DFS

注：该题目来自[Leetcode](https://leetcode-cn.com/problems/diving-board-lcci/)

这道题是这样折腾的（详细的题目和例子可以[点击这里](https://leetcode-cn.com/problems/diving-board-lcci/)）
> 你正在使用一堆木板建造跳水板。有两种类型的木板，其中长度较短的木板长度为shorter，长度较长的木板长度为longer。你必须正好使用k块木板。编写一个方法，生成跳水板所有可能的长度。

> 返回的长度需要从小到大排列。

大家应该都能想到最简单的暴力法: **枚举所有的排列组合，并求组合中所有数的和**

```java
// 特判以及入口
private int[] help1(int shorter, int longer, int k) {
    List<Integer> res = new ArrayList<>();
    Set<Integer> set = new HashSet<>();
    dfs(shorter, longer, k, res, set, 0);
    return res.stream().mapToInt(Integer::valueOf).toArray();
}

private void dfs(int shorter, int longer, int k, List<Integer> res, int sum) {
    if (k == 0) {
        res.add(sum);
        return;
    }
		
  // 这两行代码调换位置会使结果为降序（从大往小枚举）
    dfs(shorter, longer, k - 1, res, sum + shorter);
    dfs(shorter, longer, k - 1, res, sum + longer);
}
```
运行看结果：

```java
输入：shorter 1 ; longer 2; k 5;
输出：[5,6,6,7,6,7,7,8,6,7,7,8,7,8,8,9,6,7,7,8,7,8,8,9,7,8,8,9,8,9,9,10]
预期：[5,6,7,8,9,10]
```
啊呀妈呀！？ 这啥玩意？？ （内心有点波动）

缓过神来定睛一看，哦**(上升音调)**~ 原来是有很多重复元素在里面。我们先来探究下为什么会有这么多的重复元素， 这是该算法的解空间（太懒了，只画了k=3的情况）

![](https://github.com/xiefans/algorithm-doc/raw/master/Screenshots/p1.png)

大家可以看到从根节点到叶子节点的路径就是一种组合

1-2-1和2-1-1虽然排列不同，最后的和却是一样的我们先不考虑那么多，直接想办法去重（特判和上面一样，这里就不再写了）。

```java
private void dfs(int shorter, int longer, int k, List<Integer> res, Set<Integer> set, int sum) {
    if (k == 0) {
        if (!set.contains(sum)) {
            res.add(sum);
            set.add(sum);
        }
        return;
    }

    dfs(shorter, longer, k - 1, res, set, sum + shorter);
    dfs(shorter, longer, k - 1, res, set, sum + longer);
}
```

利用HashSet去重，跑一下瞧瞧

```java
输入：shorter 1 ; longer 2; k 5;
输出：[5,6,7,8,9,10]
预期：[5,6,7,8,9,10]
```

哇，测试用例通过了。普天同庆~

提交到LeetCode之后发现time limit。因为是dfs，所以第一时间想到的是用**剪枝**来提高效率。

我思考的剪枝纬度是两个数出现的次数，因为只要两个数不相等，出现的次数不同，那么和一定不同。那么我只需要枚举其中一个数出现的次数，这样直接用迭代即可

#### 迭代

```java
private int[] help2(int shorter, int longer, int k) {
    int[] res = new int[k + 1];
    for (int i = 0; i <= k; i ++) {
   		 res[i] = i * longer + (k - i) * shorter;
    }
    return res;
}
```

从小到大的去枚举大的数，就可以得到升序的结果。提交通过

```
执行结果：通过
执行用时：2 ms, 在所有 Java 提交中击败了96.14%的用户
内存消耗：47.6 MB, 在所有 Java 提交中击败了100.00%的用户
```