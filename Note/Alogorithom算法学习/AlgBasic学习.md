# AlgBasic 学习笔记

> 2026-09-05 教学内容修订。保留 47 章、220 条学习条目，其中 17 条新手班补充仍位于对应章节开头；不重复追加已有题。

## 阅读与运行约定

- 题目按“题面、输入输出与约束、函数签名、具体示例、出处”组织。课程演示类和共享数据结构保留为练习条目，不虚构 LeetCode 题号。
- 思路解释如何从题意推导算法；代码注释解释变量含义、关键状态、分支原因与更新次序；题解给出正确性依据、分版本复杂度及边界反例。
- 正常解答和测试用水平分隔线隔开。课程里已有的暴力版本保留，便于比较；新增对数器注明固定随机种子与独立基准。随机对拍能发现反例，不能代替证明。
- 约束以当前 Java 实现为准。LeetCode 出处仅展示题号、题名和链接；返回值、稳定性或输入范围等实际差异另列在课程版本说明中。
- Java 代码保留课程包名与类名；图结构等共享类型需要和同章依赖一起编译。测试块粘回同一外层类，不是单独的 Java 文件。
- 本地以 Java 8 编译验证。Hash 示例依赖 `javax.xml.bind.DatatypeConverter`，较新 JDK（Java Development Kit，Java 开发工具包）运行时需补依赖或改写十六进制转换。
- 复杂度中的 N 通常为元素个数，树题的 H 为高度；金额、状态数、字符集、答案长度等额外参数在相应条目中另行说明。int/long 溢出风险与源码的输入修改行为必须结合边界说明阅读。

课程中采用相减的比较器还要求比较差值不溢出；扩展到完整int范围时应改用 `Integer.compare`，不能只保证最终答案不溢出。

## 来源与去重

- [体系学习班 algorithmbasic2020](https://github.com/algorithmzuo/algorithmbasic2020)：本地持久快照位于 `references/algorithmbasic2020/src`，生成不再依赖临时目录。
- [新手班 algorithm-primary](https://github.com/algorithmzuo/algorithm-primary)：本地参考位于 `算法和数据结构新手班代码`。

新手班未再次添加的重复主题或错误示范：

- 基础排序与对数器：Code03_Sort、Code04_SelectionSort、Code05_BubbleSort、Code06_InsertionSort、Code03_Comp。
- 随机函数内部重复：Code03_EqualProbabilityRandom（其内容已被 RandToRand 覆盖）。
- 二分与容器：四个二分文件、Code05_HashMapTreeMap。
- 基础链表结构：Code01_ReverseList、Code02_LinkedListToQueueAndStack、Code03_DoubleLinkedListToDeque。
- 位图错误示范：Code01_BitMap1（保留正确版本 BitMap2）。
- 比较器与遍历：ShowComparator、ShowComparator2、TraversalBinaryTree。
- 树与排序重复题：BalancedBinaryTree、IsBinarySearchTree、GetMax、MergeSort、PartitionAndQuickSort。

## 课程目录

- [第 1 课：复杂度、基础排序、对数器与二分查找](#course-01)
- [第 2 课：异或运算与位运算题目](#course-02)
- [第 3 课：链表、栈、队列与递归基础](#course-03)
- [第 4 课：归并排序及其扩展题](#course-04)
- [第 5 课：归并计数、荷兰国旗与快速排序](#course-05)
- [第 6 课：比较器、堆与堆排序](#course-06)
- [第 7 课：加强堆及其应用](#course-07)
- [第 8 课：前缀树、计数排序与基数排序](#course-08)
- [第 9 课：链表常见面试题](#course-09)
- [第 10 课：链表相交与二叉树遍历](#course-10)
- [第 11 课：二叉树的层序、序列化与结构题](#course-11)
- [第 12 课：二叉树递归套路（一）](#course-12)
- [第 13 课：二叉树递归套路（二）与贪心](#course-13)
- [第 14 课：贪心算法与并查集](#course-14)
- [第 15 课：并查集的岛屿问题](#course-15)
- [第 16 课：图结构与经典图算法](#course-16)
- [第 17 课：图算法补充与暴力递归](#course-17)
- [第 18 课：从暴力递归到动态规划（一）](#course-18)
- [第 19 课：从暴力递归到动态规划（二）](#course-19)
- [第 20 课：从暴力递归到动态规划（三）](#course-20)
- [第 21 课：从暴力递归到动态规划（四）](#course-21)
- [第 22 课：动态规划中的概率、最少硬币与整数拆分](#course-22)
- [第 23 课：背包变形与 N 皇后](#course-23)
- [第 24 课：滑动窗口及其应用](#course-24)
- [第 25 课：单调栈及其应用](#course-25)
- [第 26 课：子数组最小值与矩阵快速幂](#course-26)
- [第 27 课：KMP 字符串匹配算法](#course-27)
- [第 28 课：Manacher 回文算法](#course-28)
- [第 29 课：选择算法、Top K 与蓄水池抽样](#course-29)
- [第 30 课：Morris 遍历与二叉树最小深度](#course-30)
- [第 31 课：线段树及其应用](#course-31)
- [第 32 课：树状数组与 AC 自动机](#course-32)
- [第 33 课：哈希函数](#course-33)
- [第 34 课：资源限制类题目说明](#course-34)
- [第 35 课：AVL 平衡搜索树](#course-35)
- [第 36 课：SBT 与跳表](#course-36)
- [第 37 课：有序表的工程应用](#course-37)
- [第 38 课：根据数据量猜解法与打表技巧](#course-38)
- [第 39 课：分治、背包与组合计数](#course-39)
- [第 40 课：子数组技巧与矩阵打印](#course-40)
- [第 41 课：四边形不等式优化（一）](#course-41)
- [第 42 课：四边形不等式优化（二）](#course-42)
- [第 43 课：状态压缩动态规划](#course-43)
- [第 44 课：DC3 后缀数组算法](#course-44)
- [第 45 课：后缀数组的应用](#course-45)
- [第 46 课：区间动态规划与哈夫曼编码](#course-46)
- [第 47 课：高级动态规划与 Dinic 最大流](#course-47)

<a id="course-01"></a>

## 第 1 课：复杂度、基础排序、对数器与二分查找

### 1.1 阶乘累加和（新手班前置）

#### 题目

给定正整数 `N`，计算 `1! + 2! + ... + N!`；要求在遍历过程中复用前一个阶乘结果，避免重复计算。

**输入、输出与约束**

输入正整数 N，本实现限定 1≤N≤20；返回 long 类型的阶乘累加和。

**函数签名（课程入口）**

```java
public static long f1(int N);
public static long f2(int N);
```

**示例**

```text
输入：N=3
输出：9
```

解释：cur 依次为 1、2、6，ans 依次为 1、3、9。

**出处与版本差异**

- [课程源码：class01/Code02_SumOfFactorial.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class01/Code02_SumOfFactorial.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

直接做法分别计算 1! 到 N!，重复计算了大量前缀乘积。注意 i! = (i-1)! × i，因此 f2 只保留当前阶乘 cur 和累加和 ans。cur 从 1 开始，每轮先乘 i，再把得到的 i! 加入 ans。f1 和 factorial 保留为朴素对照。

#### 代码答案

```java
package class01;

public class Code02_SumOfFactorial {

	public static long f1(int N) {
		// ans 保存已经计算完的各个阶乘之和，尚未处理任何项时为 0。
		long ans = 0;
		for (int i = 1; i <= N; i++) {
			// 朴素版本重新计算 i!，因此相同的乘法前缀会执行多次。
			ans += factorial(i);
		}
		return ans;
	}

	public static long factorial(int N) {
		long ans = 1;
		for (int i = 1; i <= N; i++) {
			// 从 1 连乘到 N，循环结束时 ans=N!。
			ans *= i;
		}
		return ans;
	}

	public static long f2(int N) {
		// ans 保存已经计算完的各个阶乘之和，尚未处理任何项时为 0。
		long ans = 0;
		// cur 初始为 0!=1，用它逐步推出后续阶乘。
		long cur = 1;
		for (int i = 1; i <= N; i++) {
			// 把上一轮的 (i-1)! 乘以 i，得到本轮的 i!。
			cur = cur * i;
			// 将本轮刚算出的 i! 计入总和。
			ans += cur;
		}
		return ans;
	}

}
```

---

##### 新增对数器（固定输入或固定随机种子）

穷举1..20，逐项对照独立连乘与线性复用两个版本。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
    // long 可精确表示的范围很小，穷举全部合法 N，无须随机抽样。
    for (int n = 1; n <= 20; n++) {
        long expected = f1(n); // 每次重新连乘的朴素基准。
        long actual = f2(n);   // 复用前一阶乘的线性实现。
        if (actual != expected) throw new AssertionError("N=" + n);
    }
    System.out.println("PASS: SumOfFactorial, N=1..20");
}
```

#### 题解

**为什么正确**

进入第 i 轮前，cur=(i-1)!，ans=1!+…+(i-1)!；乘以 i 后 cur=i!，累加后 ans 恰好覆盖前 i 项。初始 cur=0!=1、ans=0 满足此条件；完成 N 轮便得到所求和。

**复杂度**

f1：时间 O(N²)，空间 O(1)；f2：时间 O(N)，空间 O(1)。

**边界与易错点**

顺序必须是先更新阶乘再累加。long 只能精确承接本题 N≤20 的结果；N≥21 会溢出，应改用 BigInteger。

### 1.2 前缀和区间查询（新手班前置）

#### 题目

给定一个不可变整数数组，需要多次查询闭区间 `[L, R]` 的元素累加和。设计预处理结构，使每次查询能够在 O(1) 时间完成。

**输入、输出与约束**

构造时输入非空 int 数组；查询满足 0≤L≤R<N，返回闭区间元素和。

**函数签名（课程入口）**

```java
public RangeSum2(int[] array); // 先构造查询对象
public int rangeSum(int L, int R); // 闭区间查询
```

**示例**

```text
输入：array=[-2,0,3,-5,2,-1], L=2, R=5
输出：-1
```

解释：preSum[5]=-3，preSum[1]=-2，两者相减为 -1。

**出处与版本差异**

- [课程源码：class02/Code01_PreSum.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class02/Code01_PreSum.java)。
- [LeetCode 原题 303. 区域和检索（数组不可变）（Range Sum Query - Immutable）](https://leetcode.com/problems/range-sum-query-immutable/)

课程版本说明：

- LeetCode 303：课程方法名为 rangeSum。

#### 思路

RangeSum1 每次逐项相加，Q 次查询最坏需要 O(QN)。RangeSum2 在构造时预先计算 preSum[i]=array[0]+…+array[i]。查询 [L,R] 时，用到 R 的总和减去 L 之前的总和；L=0 时没有需要扣除的前缀。

#### 代码答案

```java
package class02;

public class Code01_PreSum {

	public static class RangeSum1 {

		private int[] arr;

		public RangeSum1(int[] array) {
			// 朴素版本保存原数组引用；查询时读取当前数组内容。
			arr = array;
		}

		public int rangeSum(int L, int R) {
			int sum = 0;
			for (int i = L; i <= R; i++) {
				// 朴素查询把闭区间 [L,R] 中的每个元素各加一次。
				sum += arr[i];
			}
			return sum;
		}

	}

	public static class RangeSum2 {

		private int[] preSum;

		public RangeSum2(int[] array) {
			int N = array.length;
			preSum = new int[N];
			// 第一个前缀只包含 array[0]，也让后续递推有起点。
			preSum[0] = array[0];
			for (int i = 1; i < N; i++) {
				// 在前一个前缀之上加入 array[i]，得到 [0,i] 的总和。
				preSum[i] = preSum[i - 1] + array[i];
			}
		}

		public int rangeSum(int L, int R) {
			// L=0 时直接返回整个前缀，否则扣掉 [0,L-1]。
			return L == 0 ? preSum[R] : preSum[R] - preSum[L - 1];
		}

	}

}
```

---

##### 新增对数器（固定输入或固定随机种子）

固定种子生成含负数、零、重复值的非空数组，穷举每个数组的全部闭区间，以逐项相加作为基准；失败信息包含完整数组与区间。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
    java.util.Random random = new java.util.Random(20260905L);
    for (int round = 0; round < 2000; round++) {
        int[] arr = new int[1 + random.nextInt(30)];
        for (int i = 0; i < arr.length; i++) arr[i] = random.nextInt(201) - 100;
        RangeSum1 brute = new RangeSum1(arr); // 逐元素查询，与前缀相减独立。
        RangeSum2 fast = new RangeSum2(arr);
        // 穷举本数组所有合法区间，覆盖 L=0、单元素、整段和负和。
        for (int l = 0; l < arr.length; l++) {
            for (int r = l; r < arr.length; r++) {
                int expected = brute.rangeSum(l, r);
                int actual = fast.rangeSum(l, r);
                if (actual != expected) throw new AssertionError(
                    java.util.Arrays.toString(arr) + " L=" + l + " R=" + r
                    + " expected=" + expected + " actual=" + actual);
            }
        }
    }
    System.out.println("PASS: PreSum, seed=20260905");
}
```

#### 题解

**为什么正确**

preSum[R] 中同时包含 [0,L-1] 和 [L,R]。当 L>0，减去 preSum[L-1] 后，前一段每个元素恰好抵消一次，只留下查询区间。预处理递推每次接入一个新元素，因此所有前缀都正确。

**复杂度**

RangeSum1：构造 O(1)，单次查询 O(R-L+1)。RangeSum2：构造时间、额外空间 O(N)，单次查询 O(1)。

**边界与易错点**

L-1 不是 L；必须单独处理 L=0。源码构造器直接访问 array[0]，不接受空数组。原数组后续不能修改，否则两版本语义会分离；区间和必须在 int 范围内。

### 1.3 选择排序

#### 题目

给定一个整数数组 `arr`，请使用本节指定的排序算法将数组原地调整为非递减顺序。

**输入、输出与约束**

输入 int 数组，允许负数和重复值；原地升序排序，null 或长度不足 2 时直接返回。

**函数签名（课程入口）**

```java
public static void selectionSort(int[] arr);
```

**示例**

```text
输入：arr=[3,1,2]
输出：arr=[1,2,3]
```

解释：第一轮将 1 与 3 交换，第二轮将 2 放到下标 1。

**出处与版本差异**

- [课程源码：class01/Code01_SelectionSort.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class01/Code01_SelectionSort.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

将数组分成已排好序的 [0,i-1] 和未排序的 [i,N-1]。第 i 轮扫描未排序部分，记录最小值的位置 minIndex，扫描完成后才与 i 交换。每轮确定一个位置。

#### 代码答案

```java
package class01;

import java.util.Arrays;

public class Code01_SelectionSort {

	public static void selectionSort(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		// 0 ~ N-1  找到最小值，在哪，放到0位置上
		// 1 ~ n-1  找到最小值，在哪，放到1 位置上
		// 2 ~ n-1  找到最小值，在哪，放到2 位置上
		for (int i = 0; i < arr.length - 1; i++) {
			// 暂把未排序区间的第一个位置作为最小值候选。
			int minIndex = i;
			for (int j = i + 1; j < arr.length; j++) { // i ~ N-1 上找最小值的下标
				// 若发现更小元素，只更新候选下标，不立即交换。
				minIndex = arr[j] < arr[minIndex] ? j : minIndex;
			}
			// 整段扫描结束后，把本轮最小元素放到已排序前缀后面。
			swap(arr, i, minIndex);
		}
	}

	public static void swap(int[] arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static void comparator(int[] arr) {
		Arrays.sort(arr);
	}

	// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		// Math.random()   [0,1)  
		// Math.random() * N  [0,N)
		// (int)(Math.random() * N)  [0, N-1]
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			// [-? , +?]
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100;
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int[] arr1 = generateRandomArray(maxSize, maxValue);
			int[] arr2 = copyArray(arr1);
			selectionSort(arr1);
			comparator(arr2);
			if (!isEqual(arr1, arr2)) {
				succeed = false;
				printArray(arr1);
				printArray(arr2);
				break;
			}
		}
		System.out.println(succeed ? "Nice!" : "Fucking fucked!");

		int[] arr = generateRandomArray(maxSize, maxValue);
		printArray(arr);
		selectionSort(arr);
		printArray(arr);
	}
```

#### 题解

**为什么正确**

归纳假设前 i 个元素是整个数组最小的 i 个数且有序。本轮把剩余最小数放到 i，它不小于已排序前缀中的数，新的前缀仍有序。最后只剩一个元素时，它也已处于正确位置。

**复杂度**

各种输入下时间均为 O(N²)；额外空间 O(1)。

**边界与易错点**

必须比较 arr[j] 与 arr[minIndex]，而不是始终与 arr[i] 比较。交换放在内层循环结束后。远距离交换会改变相同值的相对顺序，所以不是稳定排序。

### 1.4 冒泡排序

#### 题目

给定一个整数数组 `arr`，请使用本节指定的排序算法将数组原地调整为非递减顺序。

**输入、输出与约束**

输入 int 数组，允许负数、重复值和空数组；原地升序排序。

**函数签名（课程入口）**

```java
public static void bubbleSort(int[] arr);
```

**示例**

```text
输入：arr=[3,2,1]
输出：arr=[1,2,3]
```

解释：第一轮变成 [2,1,3]，最大值 3 已经就位。

**出处与版本差异**

- [课程源码：class01/Code02_BubbleSort.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class01/Code02_BubbleSort.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

从左向右比较相邻两个元素；前者更大就交换。扫描到右边界 e 时，未排序区域的最大元素已经被连续推到 e。接着缩小 e，重复处理尚未排好的前缀。

#### 代码答案

```java
package class01;

import java.util.Arrays;

public class Code02_BubbleSort {

	public static void bubbleSort(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		// 0 ~ N-1
		// 0 ~ N-2
		// 0 ~ N-3
		// e 右侧已经排序，本轮把剩余最大值推到 e。
		for (int e = arr.length - 1; e > 0; e--) { // 0 ~ e
			for (int i = 0; i < e; i++) {
				// 这两个相邻元素逆序；只交换严格逆序的元素以保持稳定性。
				if (arr[i] > arr[i + 1]) {
					// 较大元素向右移动一步，并将继续与后面的元素比较。
					swap(arr, i, i + 1);
				}
			}
		}
	}

	// 交换arr的i和j位置上的值
	public static void swap(int[] arr, int i, int j) {
		arr[i] = arr[i] ^ arr[j];
		arr[j] = arr[i] ^ arr[j];
		arr[i] = arr[i] ^ arr[j];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static void comparator(int[] arr) {
		Arrays.sort(arr);
	}

	// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {		
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100;
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int[] arr1 = generateRandomArray(maxSize, maxValue);
			int[] arr2 = copyArray(arr1);
			bubbleSort(arr1);
			comparator(arr2);
			if (!isEqual(arr1, arr2)) {
				succeed = false;
				break;
			}
		}
		System.out.println(succeed ? "Nice!" : "Fucking fucked!");

		int[] arr = generateRandomArray(maxSize, maxValue);
		printArray(arr);
		bubbleSort(arr);
		printArray(arr);
	}
```

#### 题解

**为什么正确**

一次扫描中，每次比较后，较大者都会留在右侧继续参与下一次比较。因此到达 e 时，[0,e] 的最大值必在 e。逐轮固定最大值，就形成升序数组。

**复杂度**

源码没有提前终止标记，最好、平均和最坏时间均为 O(N²)；额外空间 O(1)。

**边界与易错点**

内层只比较到 i<e，否则 i+1 越界。只在严格大于时交换，可以保持相同元素的相对顺序。

### 1.5 插入排序

#### 题目

给定一个整数数组 `arr`，请使用本节指定的排序算法将数组原地调整为非递减顺序。

**输入、输出与约束**

输入任意 int 数组；原地升序排序，允许空数组和重复值。

**函数签名（课程入口）**

```java
public static void insertionSort(int[] arr);
```

**示例**

```text
输入：arr=[2,4,3,1]
输出：arr=[1,2,3,4]
```

解释：插入 3 时越过 4；插入 1 时依次越过 4、3、2。

**出处与版本差异**

- [课程源码：class01/Code03_InsertionSort.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class01/Code03_InsertionSort.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

把第一个元素视为有序前缀。每轮取紧邻前缀的下一个元素，向左逐个比较并交换，直到左边元素不大于它。此时它正好插入应处的位置，有序前缀扩大一格。

#### 代码答案

```java
package class01;

import java.util.Arrays;

public class Code03_InsertionSort {

	public static void insertionSort(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		// 不只1个数
		// [0,i-1] 已有序，本轮将 arr[i] 插入该前缀。
		for (int i = 1; i < arr.length; i++) { // 0 ~ i 做到有序
			// 当前元素不断向左移动，直到前驱不再更大或到达数组开头。
			for (int j = i - 1; j >= 0 && arr[j] > arr[j + 1]; j--) {
				// 消除当前元素与前驱的逆序，使当前元素左移一格。
				swap(arr, j, j + 1);
			}
		}
	}

	// i和j是一个位置的话，会出错
	public static void swap(int[] arr, int i, int j) {
		arr[i] = arr[i] ^ arr[j];
		arr[j] = arr[i] ^ arr[j];
		arr[i] = arr[i] ^ arr[j];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static void comparator(int[] arr) {
		Arrays.sort(arr);
	}

	// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		// Math.random() -> [0,1) 所有的小数，等概率返回一个
		// Math.random() * N -> [0,N) 所有小数，等概率返回一个
		// (int)(Math.random() * N) -> [0,N-1] 所有的整数，等概率返回一个
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())]; // 长度随机
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 100; // 随机数组的长度0～100
		int maxValue = 100;// 值：-100～100
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int[] arr = generateRandomArray(maxSize, maxValue);
			int[] arr1 = copyArray(arr);
			int[] arr2 = copyArray(arr);
			insertionSort(arr1);
			comparator(arr2);
			if (!isEqual(arr1, arr2)) {
				// 打印arr1
				// 打印arr2
				succeed = false;
				for (int j = 0; j < arr.length; j++) {
					System.out.print(arr[j] + " ");
				}
				System.out.println();
				break;
			}
		}
		System.out.println(succeed ? "Nice!" : "Fucking fucked!");

		int[] arr = generateRandomArray(maxSize, maxValue);
		printArray(arr);
		insertionSort(arr);
		printArray(arr);
	}
```

#### 题解

**为什么正确**

原前缀内部有序，只可能新元素与它前面的较大元素逆序。逐个越过这些元素后，左边都不大于新元素、右边都不小于它，旧前缀元素的相对次序也未改变。

**复杂度**

最好 O(N)，最坏及平均 O(N²)；额外空间 O(1)。

**边界与易错点**

先检查 j≥0 再访问 arr[j]，避免越界。相等时停止移动才能保持稳定。交换后需要继续检查更左边的位置。

### 1.6 二分查找：判断有序数组中是否存在目标值

#### 题目

给定一个按非递减顺序排列的整数数组 `sortedArr` 和目标值 `num`，判断数组中是否存在 `num`。

**输入、输出与约束**

输入非递减数组和整数目标；返回是否存在，空数组返回 false。

**函数签名（课程入口）**

```java
public static boolean exist(int[] sortedArr, int num);
```

**示例**

```text
输入：sortedArr=[1,3,3,7], num=3
输出：true
```

解释：任意一个值为 3 的位置都能证明目标存在。

**出处与版本差异**

- [课程源码：class01/Code04_BSExist.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class01/Code04_BSExist.java)。
- [LeetCode 对应题 704. 二分查找（Binary Search）](https://leetcode.com/problems/binary-search/)

课程版本说明：

- LeetCode 704：查找目标相同；课程返回是否存在，原题返回下标或 -1。

#### 思路

候选下标保留在闭区间 [L,R]。取中点 mid，与目标比较；相等即可返回。若中点值较小，有序性保证左半区间也不可能命中，令 L=mid+1；反之令 R=mid-1。

#### 代码答案

```java
package class01;

import java.util.Arrays;

public class Code04_BSExist {

	public static boolean exist(int[] sortedArr, int num) {
		if (sortedArr == null || sortedArr.length == 0) {
			return false;
		}
		int L = 0;
		int R = sortedArr.length - 1;
		int mid = 0;
		// L..R
		while (L < R) { // L..R 至少两个数的时候
			mid = L + ((R - L) >> 1);
			if (sortedArr[mid] == num) {
				return true;
			} else if (sortedArr[mid] > num) {
				// 中点及其右边都大于目标，候选区间结束于 mid-1。
				R = mid - 1;
			} else {
				// 中点及其左边都小于目标，候选区间从 mid+1 开始。
				L = mid + 1;
			}
		}
		// 循环保留了一个候选位置，最后单独确认它是否命中。
		return sortedArr[L] == num;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static boolean test(int[] sortedArr, int num) {
		for(int cur : sortedArr) {
			if(cur == num) {
				return true;
			}
		}
		return false;
	}
	
	
	// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 10;
		int maxValue = 100;
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int[] arr = generateRandomArray(maxSize, maxValue);
			Arrays.sort(arr);
			int value = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
			if (test(arr, value) != exist(arr, value)) {
				succeed = false;
				break;
			}
		}
		System.out.println(succeed ? "Nice!" : "Fucking fucked!");
	}
```

#### 题解

**为什么正确**

每次舍弃的区域都能由有序性证明不含目标，剩下的候选仍全部位于 [L,R]。区间至少缩短一半；源码收缩到 L=R 后还需检查这个最后候选。

**复杂度**

时间 O(log N)，额外空间 O(1)。

**边界与易错点**

输入必须有序。mid 用 L+((R-L)>>1) 避免 L+R 溢出；while(L<R) 退出后不能直接认为查找失败。课程返回 boolean，而 LeetCode 704 返回下标。

### 1.7 二分查找：大于等于目标值的最左位置

#### 题目

给定有序数组 `arr` 和目标值 `value`，返回第一个大于等于 `value` 的元素下标；不存在时返回 `-1`。

**输入、输出与约束**

输入非递减数组和目标 value；返回最左满足 arr[i]≥value 的下标，无解返回 -1。

**函数签名（课程入口）**

```java
public static int nearestIndex(int[] arr, int value);
```

**示例**

```text
输入：arr=[1,3,3,5], value=3
输出：1
```

解释：两个 3 都满足条件，返回第一个。

**出处与版本差异**

- [课程源码：class01/Code05_BSNearLeft.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class01/Code05_BSNearLeft.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

除候选区间 [L,R] 外，再维护当前找到的答案 index=-1。遇到 arr[mid]≥value，mid 是合法答案，但左边可能更早，因此记录 mid 后向左找；否则只能去右边找。

#### 代码答案

```java
package class01;

import java.util.Arrays;

public class Code05_BSNearLeft {

	// 在arr上，找满足>=value的最左位置
	public static int nearestIndex(int[] arr, int value) {
		int L = 0;
		int R = arr.length - 1;
		int index = -1; // 记录最左的对号
		while (L <= R) { // 至少一个数的时候
			int mid = L + ((R - L) >> 1);
			if (arr[mid] >= value) {
				// mid 已满足条件，先保存，再继续寻找更左的合法位置。
				index = mid;
				// 当前位置及右侧不会产生更靠左的答案。
				R = mid - 1;
			} else {
				// 中点值太小，有序性使左侧也全部不合格。
				L = mid + 1;
			}
		}
		return index;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int test(int[] arr, int value) {
		for (int i = 0; i < arr.length; i++) {
			if (arr[i] >= value) {
				return i;
			}
		}
		return -1;
	}

	// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}
	
	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 10;
		int maxValue = 100;
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int[] arr = generateRandomArray(maxSize, maxValue);
			Arrays.sort(arr);
			int value = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
			if (test(arr, value) != nearestIndex(arr, value)) {
				printArray(arr);
				System.out.println(value);
				System.out.println(test(arr, value));
				System.out.println(nearestIndex(arr, value));
				succeed = false;
				break;
			}
		}
		System.out.println(succeed ? "Nice!" : "Fucking fucked!");
	}
```

#### 题解

**为什么正确**

index 始终是已经检查过的合法位置中最靠左的一个。每次向左继续搜索只为寻找更优答案；区间耗尽后，再无未检查的下标能比 index 更小且满足条件。

**复杂度**

时间 O(log N)，额外空间 O(1)。

**边界与易错点**

满足条件后不能立即返回。条件是 ≥ 而不是 >；有重复值时更要继续向左。找不到时保持 -1。

### 1.8 二分查找：小于等于目标值的最右位置

#### 题目

给定有序数组 `arr` 和目标值 `value`，返回最后一个小于等于 `value` 的元素下标；不存在时返回 `-1`。

**输入、输出与约束**

输入非递减数组；返回最右满足 arr[i]≤value 的位置，无解返回 -1。

**函数签名（课程入口）**

```java
public static int nearestIndex(int[] arr, int value);
```

**示例**

```text
输入：arr=[1,3,3,5], value=3
输出：2
```

解释：返回最后一个 3 的下标。

**出处与版本差异**

- [课程源码：class01/Code05_BSNearRight.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class01/Code05_BSNearRight.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

保留候选区间和答案 index=-1。若 arr[mid]≤value，就先记录 mid，再搜索右半区间；否则搜索左半区间。与最左边界题的区别在于合法后继续向右。

#### 代码答案

```java
package class01;

import java.util.Arrays;

public class Code05_BSNearRight {

	// 在arr上，找满足<=value的最右位置
	public static int nearestIndex(int[] arr, int value) {
		int L = 0;
		int R = arr.length - 1;
		int index = -1; // 记录最右的对号
		while (L <= R) {
			int mid = L + ((R - L) >> 1);
			if (arr[mid] <= value) {
				// 记录已知最右候选，右侧仍可能存在更优答案。
				index = mid;
				// 满足条件后向右搜索，使答案尽可能靠后。
				L = mid + 1;
			} else {
				// 中点过大，它及右侧全部不满足 ≤value。
				R = mid - 1;
			}
		}
		return index;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int test(int[] arr, int value) {
		for (int i = arr.length - 1; i >= 0; i--) {
			if (arr[i] <= value) {
				return i;
			}
		}
		return -1;
	}

	// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 10;
		int maxValue = 100;
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int[] arr = generateRandomArray(maxSize, maxValue);
			Arrays.sort(arr);
			int value = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
			if (test(arr, value) != nearestIndex(arr, value)) {
				printArray(arr);
				System.out.println(value);
				System.out.println(test(arr, value));
				System.out.println(nearestIndex(arr, value));
				succeed = false;
				break;
			}
		}
		System.out.println(succeed ? "Nice!" : "Fucking fucked!");
	}
```

#### 题解

**为什么正确**

index 始终是已经发现的最右合法位置。合法位置右侧才可能存在更优解，不合法中点右侧则全部不合法，故两种收缩都不会排除正确答案。

**复杂度**

时间 O(log N)，额外空间 O(1)。

**边界与易错点**

合法条件使用 ≤；更新答案后仍要移动 L，防止停滞。所有元素都大于目标时返回 -1。

### 1.9 二分查找局部最小值

#### 题目

给定一个相邻元素不相等的整数数组，返回任意局部最小位置；端点只需要与唯一相邻元素比较。

**输入、输出与约束**

输入相邻值不同的数组；返回任意局部最小下标，空数组返回 -1。

**函数签名（课程入口）**

```java
public static int getLessIndex(int[] arr);
public static boolean isRight(int[] arr, int index);
```

**示例**

```text
输入：arr=[9,6,3,5,8]
输出：2
```

解释：3 同时小于左右相邻的 6 和 5。

**出处与版本差异**

- [课程源码：class01/Code06_BSAwesome.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class01/Code06_BSAwesome.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先检查两端是否为局部最小。排除端点后，区间两端形成向内下降的趋势。检查中点：若它大于左邻居，沿左侧下降方向找；否则若大于右邻居，就向右找；两边都更大时中点就是答案。

#### 代码答案

```java
package class01;

public class Code06_BSAwesome {

	// 课上的代码
	public static int getLessIndex(int[] arr) {
		if (arr == null || arr.length == 0) {
			return -1;
		}
		if (arr.length == 1 || arr[0] < arr[1]) {
			return 0;
		}
		if (arr[arr.length - 1] < arr[arr.length - 2]) {
			return arr.length - 1;
		}
		int left = 1;
		int right = arr.length - 2;
		int mid = 0;
		while (left < right) {
			mid = (left + right) / 2;
			// 左侧存在下降方向，选取仍保证含局部最小值的左半区间。
			if (arr[mid] > arr[mid - 1]) {
				right = mid - 1;
			// 左侧不再下降但右侧下降，转而保留右半区间。
			} else if (arr[mid] > arr[mid + 1]) {
				left = mid + 1;
			} else {
				// 中点不大于两个邻居；结合相邻不等，得到严格局部最小。
				return mid;
			}
		}
		return left;
	}

	// 验证得到的结果，是不是局部最小
	public static boolean isRight(int[] arr, int index) {
		if (arr.length <= 1) {
			return true;
		}
		if (index == 0) {
			return arr[index] < arr[index + 1];
		}
		if (index == arr.length - 1) {
			return arr[index] < arr[index - 1];
		}
		return arr[index] < arr[index - 1] && arr[index] < arr[index + 1];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	// 生成相邻不相等的数组
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) (Math.random() * maxSize) + 1];
		arr[0] = (int) (Math.random() * maxValue) - (int) (Math.random() * maxValue);
		for (int i = 1; i < arr.length; i++) {
			do {
				arr[i] = (int) (Math.random() * maxValue) - (int) (Math.random() * maxValue);
			} while (arr[i] == arr[i - 1]);
		}
		return arr;
	}

	// 为了测试
	public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 30;
		int maxValue = 100;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int[] arr = generateRandomArray(maxSize, maxValue);
			int ans = getLessIndex(arr);
			if (!isRight(arr, ans)) {
				System.out.println("出错了！");
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

沿一个下降方向前进，不可能一直下降又越过已经向内下降的边界而不转折。下降转为上升的位置必为局部最小。每轮选中的半区间保留了这种边界趋势，因此里面一定仍有解。

**复杂度**

时间 O(log N)，额外空间 O(1)。

**边界与易错点**

本题数组不要求整体有序，但要求相邻元素不相等；允许相等时，下降方向判断不再保证上述证明。端点只与一个邻居比较。


<a id="course-02"></a>

## 第 2 课：异或运算与位运算题目

### 2.1 打印整数的 32 位二进制表示（新手班前置）

#### 题目

给定一个 Java `int` 整数，按从最高位到最低位的顺序打印它的完整 32 位二进制补码表示。

**输入、输出与约束**

输入任意 Java int；打印恰好 32 个二进制位。

**函数签名（课程入口）**

```java
public static void print(int num);
```

**示例**

```text
输入：num=5
输出：00000000000000000000000000000101
```

解释：5 的第 2 位和第 0 位为 1，其余位补零。

**出处与版本差异**

- [课程源码：class01/Code01_PrintBinary.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class01/Code01_PrintBinary.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

int 固定有 32 位。从 i=31 到 0，用掩码 1<<i 单独保留第 i 位。按位与结果为 0 就输出 0，否则输出 1；高位先输出才能得到通常的二进制书写顺序。

#### 代码答案

```java
package class01;

public class Code01_PrintBinary {

	public static void print(int num) {
		// 先检查符号位，再依次检查低位，保证输出顺序从高到低。
		for (int i = 31; i >= 0; i--) {
			// 掩码只留下当前位；非零表示该位为 1，包括最高位为 1 的情形。
			System.out.print((num & (1 << i)) == 0 ? "0" : "1");
		}
		System.out.println();
	}

	public static void main(String[] args) {
		// 32位
//		int num = 4;
//
//		print(num);
//
//
//		int test = 1123123;
//		print(test);
//		print(test<<1);
//		print(test<<2);
//		print(test<<8);
//
//
//		int a = Integer.MAX_VALUE;
//		System.out.println(a);

//		print(-1);
//		int a = Integer.MIN_VALUE;
//		print(a);

//		int b = 123823138;
//		int c = ~b;
//		print(b);
//		print(c);

//		print(-5);

//		System.out.println(Integer.MIN_VALUE);
//		System.out.println(Integer.MAX_VALUE);

//		int a = 12319283;
//		int b = 3819283;
//		print(a);
//		print(b);
//		System.out.println("=============");
//		print(a | b);
//		print(a & b);
//		print(a ^ b);

//		int a = Integer.MIN_VALUE;
//		print(a);
//		print(a >> 1);
//		print(a >>> 1);
//
//		int c = Integer.MIN_VALUE;
//		int d = -c ;
//
//		print(c);
//		print(d);

	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：最高位为 1 时按位与结果可能是负数，所以应判断是否等于 0，不能判断是否大于 0。负数输出补码，不是在正数二进制前加负号。

本条未附独立随机对数器。

#### 题解

**为什么正确**

掩码中只有第 i 位为 1，按位与不会混入其他位置的信息。循环覆盖全部 32 个位置一次，因此输出完整的补码位模式，包括符号位和前导零。

**复杂度**

固定 32 次操作，时间和额外空间均为 O(1)。

**边界与易错点**

最高位为 1 时按位与结果可能是负数，所以应判断是否等于 0，不能判断是否大于 0。负数输出补码，不是在正数二进制前加负号。

### 2.2 从已有随机函数构造等概率随机函数（新手班前置）

#### 题目

只允许调用给定的随机函数，把它加工成另一个指定区间上的等概率随机函数；同时实现从有偏 0/1 随机源提取公平随机位。

**输入、输出与约束**

输入为独立可重复调用的随机源；输出给定目标区间上的等概率整数。

**函数签名（课程入口）**

```java
public static int a();
public static int b();
public static int c();
public int min();
public int max();
public static int rand01(RandomBox randomBox);
```

**示例**

```text
输入：原函数等概率返回 1..5；目标返回 1..7
输出：每个结果的理论概率均为 1/7
```

解释：每轮先产生 0..7，遇到 7 就重抽。

**出处与版本差异**

- [课程源码：class02/Code02_RandToRand.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class02/Code02_RandToRand.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

均匀 1..5 先丢弃 3，再把 1、2 映射为 0，4、5 映射为 1。拼接三个公平位可得到均匀 0..7；丢弃 7 后加 1 得到均匀 1..7。若原始 0/1 源有偏，则独立取两次，仅接受 01、10，分别输出 0、1。

#### 代码答案

```java
package class02;

public class Code02_RandToRand {

	// 此函数只能用，不能修改
	// 等概率返回1~5
	public static int f() {
		return (int) (Math.random() * 5) + 1;
	}

	// 等概率得到0和1
	public static int a() {
		int ans = 0;
		do {
			ans = f();
		// 拒绝中间值 3，剩余四个等概率结果可均分成两组。
		} while (ans == 3);
		// 把两个较小结果映射为 0，两个较大结果映射为 1。
		return ans < 3 ? 0 : 1;
	}

	// 等概率返回0~6
	public static int b() {
		int ans = 0;
		do {
			ans = (a() << 2) + (a() << 1) + a();
		// 拒绝第八个编码，使剩余七个编码概率相同。
		} while (ans == 7);
		return ans;
	}

	// 等概率返回1~7
	public static int c() {
		return b() + 1;
	}

	// 这个结构是唯一的随机机制
	// 你只能初始化并使用，不可修改
	public static class RandomBox {
		private final int min;
		private final int max;

		// 初始化时请一定不要让mi==ma
		public RandomBox(int mi, int ma) {
			min = mi;
			max = ma;
		}

		// 13 ~ 17
		// 13 + [0,4]
		public int random() {
			return min + (int) (Math.random() * (max - min + 1));
		}

		public int min() {
			return min;
		}

		public int max() {
			return max;
		}
	}

	// 利用条件RandomBox，如何等概率返回0和1
	public static int rand01(RandomBox randomBox) {
		int min = randomBox.min();
		int max = randomBox.max();
		// min ~ max
		int size = max - min + 1;
		// size是不是奇数，odd 奇数
		boolean odd = (size & 1) != 0;
		int mid = size / 2;
		int ans = 0;
		do {
			ans = randomBox.random() - min;
		} while (odd && ans == mid);
		return ans < mid ? 0 : 1;
	}

	// 给你一个RandomBox，这是唯一能借助的随机机制
	// 等概率返回from~to范围上任何一个数
	// 要求from<=to
	public static int random(RandomBox randomBox, int from, int to) {
		if (from == to) {
			return from;
		}
		// 3 ~ 9
		// 0 ~ 6
		// 0 ~ range
		int range = to - from;
		int num = 1;
		// 求0～range需要几个2进制位
		while ((1 << num) - 1 < range) {
			num++;
		}

		// 我们一共需要num位
		// 最终的累加和，首先+0位上是1还是0，1位上是1还是0，2位上是1还是0...
		int ans = 0;
		do {
			ans = 0;
			for (int i = 0; i < num; i++) {
				ans |= (rand01(randomBox) << i);
			}
		} while (ans > range);
		return ans + from;
	}

	// 返回[0,1)的一个小数
	// 任意的x，x属于[0,1)，[0,x)范围上的数出现概率由原来的x调整成x平方
	public static double xToXPower2() {
		return Math.min(Math.random(), Math.random());
	}

	// lib里的，不能改！
	public static int f1() {
		return (int) (Math.random() * 5) + 1;
	}

	// 随机机制，只能用f1，
	// 等概率返回0和1
	public static int f2() {
		int ans = 0;
		do {
			ans = f1();
		// 拒绝中间值 3，剩余四个等概率结果可均分成两组。
		} while (ans == 3);
		// 把两个较小结果映射为 0，两个较大结果映射为 1。
		return ans < 3 ? 0 : 1;
	}

	// 得到000 ~ 111 做到等概率 0 ~ 7等概率返回一个
	public static int f3() {
		return (f2() << 2) + (f2() << 1) + f2();
	}

	// 0 ~ 6等概率返回一个
	public static int f4() {
		int ans = 0;
		do {
			ans = f3();
		// 拒绝第八个编码，使剩余七个编码概率相同。
		} while (ans == 7);
		return ans;
	}

	public static int g() {
		return f4() + 1;
	}

	// 你只能知道，x会以固定概率返回0和1，但是x的内容，你看不到！
	public static int x() {
		return Math.random() < 0.84 ? 0 : 1;
	}

	// 等概率返回0和1
	public static int y() {
		int ans = 0;
		do {
			ans = x();
		// 只接受两次采样不同的情况，01 与 10 的概率恰好相等。
		} while (ans == x());
		return ans;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		System.out.println("测试开始");
		// Math.random() -> double -> [0,1)
		//

		int testTimes = 10000000;
		int count = 0;
		for (int i = 0; i < testTimes; i++) {
			if (Math.random() < 0.75) {
				count++;
			}
		}
		System.out.println((double) count / (double) testTimes);

		System.out.println("=========");

		// [0,1) -> [0,8)
		count = 0;
		for (int i = 0; i < testTimes; i++) {
			if (Math.random() * 8 < 5) {
				count++;
			}
		}
		System.out.println((double) count / (double) testTimes);
		System.out.println((double) 5 / (double) 8);

		int K = 9;
		// [0,K) -> [0,8]

		int[] counts = new int[9];
		for (int i = 0; i < testTimes; i++) {
			int ans = (int) (Math.random() * K); // [0,K-1]
			counts[ans]++;
		}
		for (int i = 0; i < K; i++) {
			System.out.println(i + "这个数，出现了 " + counts[i] + " 次");
		}

		System.out.println("=========");

		count = 0;
		double x = 0.17;
		for (int i = 0; i < testTimes; i++) {
			if (xToXPower2() < x) {
				count++;
			}
		}
		System.out.println((double) count / (double) testTimes);
		System.out.println((double) 1 - Math.pow((double) 1 - x, 2));

		System.out.println("==========");
		count = 0;
		for (int i = 0; i < testTimes; i++) {
			if (f2() == 0) {
				count++;
			}
		}
		System.out.println((double) count / (double) testTimes);

		System.out.println("==========");

		counts = new int[8];
		for (int i = 0; i < testTimes; i++) {
			int num = g();
			counts[num]++;
		}
		for (int i = 0; i < 8; i++) {
			System.out.println(i + "这个数，出现了 " + counts[i] + " 次");
		}

	}
```

#### 题解

**为什么正确**

条件在未被拒绝的样本上，四个原始结果仍等概率，故两组概率各半。独立公平位的每个编码概率都为 1/8；拒绝一个编码不改变其他编码的相对概率。有偏源中 P(01)=p(1-p)=P(10)，因此也能提取公平位。

**复杂度**

固定目标 1..7 的期望时间 O(1)；有偏源每对采样的接受概率为 2p(1-p)，p 接近 0 或 1 时重试很多。额外空间 O(1)。

**边界与易错点**

两次采样必须独立，且有偏源满足 0<p<1。不能用 %7 将 0..7 直接映射，否则一个结果会获得两倍概率。频率接近均匀只是实验现象，不是等概率证明。

### 2.3 位图的正确实现（新手班前置）

#### 题目

给定非负整数上界 `max`，实现位图集合，支持加入、删除和判断某个 `0..max` 范围内整数是否存在。

**输入、输出与约束**

构造给定非负上界 max；操作参数满足 0≤num≤max，查询返回 boolean。

**函数签名（课程入口）**

```java
public void add(int num);
public void delete(int num);
public boolean contains(int num);
```

**示例**

```text
输入：max=130；add(64)，add(129)，delete(64)，contains(129)
输出：true
```

解释：129 位于第 2 个桶的第 1 位，删除 64 不会影响它。

**出处与版本差异**

- [课程源码：class05/Code02_BitMap2.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class05/Code02_BitMap2.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

一个 long 可以保存 64 个整数的存在状态。整数 num 所在的桶为 num>>6，桶内偏移为 num&63。用 1L 左移偏移得到掩码；加入用按位或，删除用按位与掩码取反，查询检查按位与是否非零。

#### 代码答案

```java
package class05;

import java.util.HashSet;

public class Code02_BitMap2 {

	// 这个类的实现是正确的
	public static class BitMap {

		private long[] bits;

		public BitMap(int max) {
			// 每 64 个整数占一个桶，并额外保留容纳上界 max 的桶。
			bits = new long[(max + 64) >> 6];
		}

		public void add(int num) {
			// 使用 64 位掩码定位桶内的一位；L 后缀防止按 32 位规则移位。
			bits[num >> 6] |= (1L << (num & 63));
		}

		public void delete(int num) {
			// 使用 64 位掩码定位桶内的一位；L 后缀防止按 32 位规则移位。
			bits[num >> 6] &= ~(1L << (num & 63));
		}

		public boolean contains(int num) {
			// 使用 64 位掩码定位桶内的一位；L 后缀防止按 32 位规则移位。
			return (bits[num >> 6] & (1L << (num & 63))) != 0;
		}

	}

}
```

---

##### 新增对数器（固定输入或固定随机种子）

以boolean数组作独立集合基准，检查32位与64位边界，随机加删后核对整个值域，检测错误移位造成的串位。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
    int max = 130;
    BitMap bitmap = new BitMap(max);
    boolean[] expected = new boolean[max + 1];
    java.util.Random random = new java.util.Random(20260905L);
    // 覆盖 31/32 与 63/64 的移位边界，再做随机操作序列。
    int[] edges = {0, 31, 32, 63, 64, 65, 127, 128, 130};
    for (int value : edges) { bitmap.add(value); expected[value] = true; }
    for (int round = 0; round < 10000; round++) {
        int value = random.nextInt(max + 1);
        if (random.nextBoolean()) { bitmap.add(value); expected[value] = true; }
        else { bitmap.delete(value); expected[value] = false; }
        // 操作一个位后核对所有位，检测是否误伤同桶的其他整数。
        for (int n = 0; n <= max; n++) {
            if (bitmap.contains(n) != expected[n])
                throw new AssertionError("round=" + round + " value=" + value + " mismatch=" + n);
        }
    }
    System.out.println("PASS: BitMap2, seed=20260905");
}
```

#### 题解

**为什么正确**

非负整数唯一分解为 64×桶号+偏移，不同整数对应不同位。或运算只把目标位置 1；与反掩码只把目标位置 0，其他位不变，因此三种操作不会干扰其他整数。

**复杂度**

加入、删除、查询均 O(1)；使用 floor(max/64)+1 个 long。

**边界与易错点**

必须写 1L 而不是 1；后者先按 32 位 int 移位，偏移达到 32 后会回绕。数组长度需覆盖 max 本身，输入不能为负数或超过 max。

### 2.4 只用位运算实现加减乘除（新手班前置）

#### 题目

不使用 Java 的加、减、乘、除运算完成两个 32 位整数的四则运算；除法结果向零截断，并处理最小整数溢出边界。

**输入、输出与约束**

输入两个 int；除法要求 b≠0。禁用算术运算符的范围指核心四则运算实现；测试可用 Java 运算符作基准。

**函数签名（课程入口）**

```java
public static int add(int a, int b);
public static int negNum(int n);
public static int minus(int a, int b);
public static int multi(int a, int b);
public static boolean isNeg(int n);
public static int div(int a, int b);
```

**示例**

```text
输入：a=-7, b=3；divide(a,b)
输出：-2
```

解释：向零截断，余数为 -1。

**出处与版本差异**

- [课程源码：class05/Code03_BitAddMinusMultiDiv.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class05/Code03_BitAddMinusMultiDiv.java)。
- [LeetCode 对应题 29. 两数相除（Divide Two Integers）](https://leetcode.com/problems/divide-two-integers/)

课程版本说明：

- LeetCode 29：原题仅对应 divide 方法；本节还实现加、减、乘。

#### 思路

加法把 a^b 作为无进位和，把 (a&b)<<1 作为进位，重复直到进位为零。负数是 ~n+1，减法转成加相反数。乘法按乘数每一位是否为 1 累加被乘数的移位值。除法从高位往低位试减相应倍数，并把成功的位写入商。

#### 代码答案

```java
package class05;

// 测试链接：https://leetcode.com/problems/divide-two-integers
public class Code03_BitAddMinusMultiDiv {

	public static int add(int a, int b) {
		int sum = a;
		while (b != 0) {
			// 不同的位产生 1，相同的位产生 0，得到尚未包含进位的和。
			sum = a ^ b;
			// 两个 1 相加在更高一位产生进位，留到下一轮合并。
			b = (a & b) << 1;
			a = sum;
		}
		return sum;
	}

	public static int negNum(int n) {
		return add(~n, 1);
	}

	public static int minus(int a, int b) {
		return add(a, negNum(b));
	}

	public static int multi(int a, int b) {
		int res = 0;
		while (b != 0) {
			if ((b & 1) != 0) {
				res = add(res, a);
			}
			a <<= 1;
			// 无符号右移最终补零，负乘数也会在 32 轮内清空。
			b >>>= 1;
		}
		return res;
	}

	public static boolean isNeg(int n) {
		return n < 0;
	}

	public static int div(int a, int b) {
		int x = isNeg(a) ? negNum(a) : a;
		int y = isNeg(b) ? negNum(b) : b;
		int res = 0;
		for (int i = 30; i >= 0; i = minus(i, 1)) {
			if ((x >> i) >= y) {
				res |= (1 << i);
				x = minus(x, y << i);
			}
		}
		return isNeg(a) ^ isNeg(b) ? negNum(res) : res;
	}

	public static int divide(int a, int b) {
		// 最小整数没有可表示的正相反数，不能直接走普通取绝对值路径。
		if (a == Integer.MIN_VALUE && b == Integer.MIN_VALUE) {
			return 1;
		} else if (b == Integer.MIN_VALUE) {
			return 0;
		// 最小整数没有可表示的正相反数，不能直接走普通取绝对值路径。
		} else if (a == Integer.MIN_VALUE) {
			if (b == negNum(1)) {
				return Integer.MAX_VALUE;
			} else {
				int c = div(add(a, 1), b);
				return add(c, div(minus(a, multi(c, b)), b));
			}
		} else {
			return div(a, b);
		}
	}

}
```

---

##### 新增对数器（固定输入或固定随机种子）

边界值笛卡尔积加固定种子随机int；用Java内置四则运算对照，单独适配MIN_VALUE/-1的饱和规则，不测试题目排除的除数0。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
    java.util.Random random = new java.util.Random(20260905L);
    int[] edge = {Integer.MIN_VALUE, Integer.MAX_VALUE, -1, 0, 1, 2, -2};
    for (int round = 0; round < 20000 + edge.length * edge.length; round++) {
        int a = round < edge.length * edge.length ? edge[round / edge.length] : random.nextInt();
        int b = round < edge.length * edge.length ? edge[round % edge.length] : random.nextInt();
        // Java int 自身就是加减乘补码溢出语义的独立基准。
        if (add(a, b) != a + b || minus(a, b) != a - b || multi(a, b) != a * b)
            throw new AssertionError("arithmetic a=" + a + " b=" + b);
        if (b != 0) {
            int expected = a == Integer.MIN_VALUE && b == -1 ? Integer.MAX_VALUE : a / b;
            if (divide(a, b) != expected) throw new AssertionError("divide a=" + a + " b=" + b);
        }
    }
    System.out.println("PASS: BitAddMinusMultiDiv, seed=20260905");
}
```

#### 题解

**为什么正确**

每轮“无进位和＋进位”与原和模 2³² 同余，进位不断向高位移动而最终消失。乘法使用二进制展开恒等式。试除时始终保持原被除数=已经确定的商×除数+余量，按高位优先确定商，不会漏掉更大的可行倍数。

**复杂度**

以字长 W 计，加法最坏 O(W)，乘除含多次位加减，粗略上界 O(W²)；Java int 的 W=32，均为固定字长操作，额外空间 O(1)。

**边界与易错点**

除数必须非零。Integer.MIN_VALUE 取相反数仍溢出，源码 divide 为它单独分类，并将 MIN_VALUE/-1 饱和为 MAX_VALUE。乘法必须用无符号右移处理负乘数。普通加减乘保留 int 溢出语义。

### 2.5 不用额外变量交换两个数

#### 题目

给定整数数组和两个合法下标，在不申请额外数值变量的条件下交换两个位置的值。

**输入、输出与约束**

输入数组和两个有效且不同的下标；课程原交换函数要求 i≠j。

**函数签名（课程入口）**

```java
public static void swap (int[] arr, int i, int j);
```

**示例**

```text
输入：arr=[4,9], i=0, j=1
输出：arr=[9,4]
```

解释：4^9=13，再分别异或即可还原对方。

**出处与版本差异**

- [课程源码：class02/Code01_Swap.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class02/Code01_Swap.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

利用 x^x=0、x^0=x，连续执行 a=a^b、b=a^b、a=a^b。第一步保存混合信息，第二步恢复旧 a，第三步恢复旧 b。课程还演示交换同一个数组位置的失败情况。

#### 代码答案

```java
package class02;

public class Code01_Swap {

	public static void main(String[] args) {

		int a = 16;
		int b = 603;

		System.out.println(a);
		System.out.println(b);

		a = a ^ b;
		b = a ^ b;
		a = a ^ b;

		System.out.println(a);
		System.out.println(b);

		int[] arr = {3,1,100};

		int i = 0;
		int j = 0;

		// 当i≠j时，这个表达式第一次保存混合值，第三步恢复旧j；i=j则会直接清零。
		arr[i] = arr[i] ^ arr[j];
		// 当两位置独立时，用混合值消去旧j得到旧i；同一位置的失败示例已没有可恢复信息。
		arr[j] = arr[i] ^ arr[j];
		// 当i≠j时，这个表达式第一次保存混合值，第三步恢复旧j；i=j则会直接清零。
		arr[i] = arr[i] ^ arr[j];

		System.out.println(arr[i] + " , " + arr[j]);

		System.out.println(arr[0]);
		System.out.println(arr[2]);

		swap(arr, 0, 0);

		System.out.println(arr[0]);
		System.out.println(arr[2]);

	}

	public static void swap (int[] arr, int i, int j) {
		// arr[0] = arr[0] ^ arr[0];
		// 当i≠j时，这个表达式第一次保存混合值，第三步恢复旧j；i=j则会直接清零。
		arr[i]  = arr[i] ^ arr[j];
		// 当两位置独立时，用混合值消去旧j得到旧i；同一位置的失败示例已没有可恢复信息。
		arr[j]  = arr[i] ^ arr[j];
		// 当i≠j时，这个表达式第一次保存混合值，第三步恢复旧j；i=j则会直接清零。
		arr[i]  = arr[i] ^ arr[j];
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：i=j 时第一步就会把该位置清零，后面无法恢复，必须提前返回。工程代码通常使用临时变量，更清晰且不存在别名陷阱。

本条未附独立随机对数器。

#### 题解

**为什么正确**

设初值为 x、y：三步后的值依次为 (x^y,y)、(x^y,x)、(y,x)，故两个独立存储位置完成交换。

**复杂度**

时间 O(1)，额外空间 O(1)。

**边界与易错点**

i=j 时第一步就会把该位置清零，后面无法恢复，必须提前返回。工程代码通常使用临时变量，更清晰且不存在别名陷阱。

### 2.6 找出出现奇数次的数

#### 题目

给定整数数组，其中只有一个或两个数出现奇数次，其余数均出现偶数次，请找出出现奇数次的数。

**输入、输出与约束**

输入 int 数组，除指定的一种或两种值出现奇数次外，其余值均出现偶数次。

**函数签名（课程入口）**

```java
public static void printOddTimesNum1(int[] arr);
public static void printOddTimesNum2(int[] arr);
public static int bit1counts(int N);
```

**示例**

```text
输入：arr=[2,2,3,3,3,5]
输出：3 和 5
```

解释：整体异或为 6，取其最低位 1 可区分 3 与 5。

**出处与版本差异**

- [课程源码：class02/Code02_EvenTimesOddTimes.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class02/Code02_EvenTimesOddTimes.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

只有一个奇数次值时，将所有元素异或，偶数次出现会两两抵消。若有两个值 a、b，整体异或 eor=a^b 不为零；提取其最低位 1，把数组按该位分成两组，再对一组异或得到其中一个值。

#### 代码答案

```java
package class02;

public class Code02_EvenTimesOddTimes {

	// arr中，只有一种数，出现奇数次
	public static void printOddTimesNum1(int[] arr) {
		int eor = 0;
		for (int i = 0; i < arr.length; i++) {
			// 偶数次值两两抵消，最终只保留奇数次值的异或。
			eor ^= arr[i];
		}
		System.out.println(eor);
	}

	// arr中，有两种数，出现奇数次
	public static void printOddTimesNum2(int[] arr) {
		int eor = 0;
		for (int i = 0; i < arr.length; i++) {
			// 偶数次值两两抵消，最终只保留奇数次值的异或。
			eor ^= arr[i];
		}
		// a 和 b是两种数
		// eor != 0
		// eor最右侧的1，提取出来
		// eor :     00110010110111000
		// rightOne :00000000000001000
		// 提取 a 与 b 不同的一个二进制位，用它把两者分到不同组。
		int rightOne = eor & (-eor); // 提取出最右的1

		int onlyOne = 0; // eor'
		for (int i = 0 ; i < arr.length;i++) {
			//  arr[1] =  111100011110000
			// rightOne=  000000000010000
			if ((arr[i] & rightOne) != 0) {
				onlyOne ^= arr[i];
			}
		}
		// 整体异或再消掉已经求出的一个数，得到另一个。
		System.out.println(onlyOne + " " + (eor ^ onlyOne));
	}

	public static int bit1counts(int N) {
		int count = 0;

		//   011011010000
		//   000000010000     1

		//   011011000000
		//

		while(N != 0) {
			int rightOne = N & ((~N) + 1);
			count++;
			N ^= rightOne;
			// N -= rightOne
		}

		return count;

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int a = 5;
		int b = 7;

		a = a ^ b;
		b = a ^ b;
		a = a ^ b;

		System.out.println(a);
		System.out.println(b);

		int[] arr1 = { 3, 3, 2, 3, 1, 1, 1, 3, 1, 1, 1 };
		printOddTimesNum1(arr1);

		int[] arr2 = { 4, 3, 4, 2, 2, 2, 4, 1, 1, 1, 3, 3, 1, 1, 1, 4, 2, 2 };
		printOddTimesNum2(arr2);

	}
```

#### 题解

**为什么正确**

eor 的某一位为 1 说明 a、b 在该位不同，故必分属两组。任意其他值的相同副本必在同一组，仍出现偶数次并抵消。由一组答案 a 和 eor 可推出 b=eor^a。

**复杂度**

时间 O(N)，额外空间 O(1)。

**边界与易错点**

恰有一个或两个奇数次值是必要前提。最低位 1 可写 eor&(-eor)，不是 eor&1；输出两数的顺序不固定。

### 2.7 一个数出现 K 次、其他数出现 M 次

#### 题目

给定整数数组和正整数 `K`、`M`，恰有一种数出现 `K` 次，其余数都出现 `M` 次，且 `K < M`，返回出现 `K` 次的数。

**输入、输出与约束**

1≤K<M；恰有一种数出现 K 次，其余各出现 M 次，允许负数和零。

**函数签名（课程入口）**

```java
public static int onlyKTimes(int[] arr, int k, int m);
public static void mapCreater(HashMap<Integer, Integer> map);
public static int km(int[] arr, int k, int m);
```

**示例**

```text
输入：arr=[-2,-2,5,5,5], K=2, M=3
输出：-2
```

解释：5 的每个位贡献均被模 3 消掉。

**出处与版本差异**

- [课程源码：class02/Code03_KM.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class02/Code03_KM.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

对 32 个二进制位分别统计数组中该位为 1 的次数，再对 M 取余。出现 M 次的值贡献全部消失；剩余只能是 0 或 K。余数为 K 的位属于目标值，将这些位拼起来即可。

#### 代码答案

```java
package class02;

import java.util.HashMap;
import java.util.HashSet;

// 输入一定能够保证，数组中所有的数都出现了M次，只有一种数出现了K次
// 1 <= K < M
// 返回这种数
public class Code03_KM {

	public static int test(int[] arr, int k, int m) {
		HashMap<Integer, Integer> map = new HashMap<>();
		for (int num : arr) {
			if (map.containsKey(num)) {
				map.put(num, map.get(num) + 1);
			} else {
				map.put(num, 1);
			}
		}
		int ans = 0;
		for (int num : map.keySet()) {
			if (map.get(num) == k) {
				ans = num;
				break;
			}
		}
		return ans;
	}

	public static HashMap<Integer, Integer> map = new HashMap<>();

	// 请保证arr中，只有一种数出现了K次，其他数都出现了M次
	public static int onlyKTimes(int[] arr, int k, int m) {
		if (map.size() == 0) {
			mapCreater(map);
		}
		int[] t = new int[32];
		// t[0] 0位置的1出现了几个
		// t[i] i位置的1出现了几个
		for (int num : arr) {
			while (num != 0) {
				int rightOne = num & (-num);
				t[map.get(rightOne)]++;
				num ^= rightOne;
			}
		}
		int ans = 0;
		// 如果这个出现了K次的数，就是0
		// 那么下面代码中的 : ans |= (1 << i);
		// 就不会发生
		// 那么ans就会一直维持0，最后返回0，也是对的！
		for (int i = 0; i < 32; i++) {
			if (t[i] % m != 0) {
				// 该位余数为 K，说明目标值的第 i 位应设为 1。
				ans |= (1 << i);
			}
		}
		return ans;
	}

	public static void mapCreater(HashMap<Integer, Integer> map) {
		int value = 1;
		for (int i = 0; i < 32; i++) {
			map.put(value, i);
			value <<= 1;
		}
	}

	// 更简洁的写法
	public static int km(int[] arr, int k, int m) {
		int[] help = new int[32];
		for (int num : arr) {
			for (int i = 0; i < 32; i++) {
				help[i] += (num >> i) & 1;
			}
		}
		int ans = 0;
		for (int i = 0; i < 32; i++) {
			help[i] %= m;
			if (help[i] != 0) {
				ans |= 1 << i;
			}
		}
		return ans;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	public static int[] randomArray(int maxKinds, int range, int k, int m) {
		int ktimeNum = randomNumber(range);
		// 真命天子出现的次数
		int times = k;
		// 2
		int numKinds = (int) (Math.random() * maxKinds) + 2;
		// k * 1 + (numKinds - 1) * m
		int[] arr = new int[times + (numKinds - 1) * m];
		int index = 0;
		for (; index < times; index++) {
			arr[index] = ktimeNum;
		}
		numKinds--;
		HashSet<Integer> set = new HashSet<>();
		set.add(ktimeNum);
		while (numKinds != 0) {
			int curNum = 0;
			do {
				curNum = randomNumber(range);
			} while (set.contains(curNum));
			set.add(curNum);
			numKinds--;
			for (int i = 0; i < m; i++) {
				arr[index++] = curNum;
			}
		}
		// arr 填好了
		for (int i = 0; i < arr.length; i++) {
			// i 位置的数，我想随机和j位置的数做交换
			int j = (int) (Math.random() * arr.length);// 0 ~ N-1
			int tmp = arr[i];
			arr[i] = arr[j];
			arr[j] = tmp;
		}
		return arr;
	}

	// 为了测试
	// [-range, +range]
	public static int randomNumber(int range) {
		return (int) (Math.random() * (range + 1)) - (int) (Math.random() * (range + 1));
	}

	// 为了测试
	public static void main(String[] args) {
		int kinds = 5;
		int range = 30;
		int testTime = 100000;
		int max = 9;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int a = (int) (Math.random() * max) + 1; // a 1 ~ 9
			int b = (int) (Math.random() * max) + 1; // b 1 ~ 9
			int k = Math.min(a, b);
			int m = Math.max(a, b);
			// k < m
			if (k == m) {
				m++;
			}
			int[] arr = randomArray(kinds, range, k, m);
			int ans1 = test(arr, k, m);
			int ans2 = onlyKTimes(arr, k, m);
			int ans3 = km(arr, k, m);
			if (ans1 != ans2 || ans1 != ans3) {
				System.out.println(ans1);
				System.out.println(ans3);
				System.out.println("出错了！");
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

第 j 位总次数可写为 M×某整数+K×目标的第 j 位，因此模 M 的余数完全由目标位决定。32 个位置包括符号位，恢复所有位就恢复了完整 int。

**复杂度**

时间 O(32N)，固定字长下为 O(N)；额外空间为 32 个计数器，即 O(1)。

**边界与易错点**

目标可能为 0，所有余数为 0 并不自动证明输入合法，需数零的次数。若检查非法输入，任何非 0、非 K 的余数都应判错。


<a id="course-03"></a>

## 第 3 课：链表、栈、队列与递归基础

### 3.1 反转单链表和双向链表

#### 题目

给定单链表或双向链表的头节点，请原地反转链表并返回新的头节点。

**输入、输出与约束**

输入无环单链表或双向链表，允许空表；返回反转后的头，节点被原地复用。

**函数签名（课程入口）**

```java
public static Node reverseLinkedList(Node head);
public static DoubleNode reverseDoubleList(DoubleNode head);
```

**示例**

```text
输入：head=1→2→3
输出：3→2→1
```

解释：原头节点 1 最终成为尾节点，其 next 为 null。

**出处与版本差异**

- [课程源码：class03/Code01_ReverseList.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class03/Code01_ReverseList.java)。
- [LeetCode 对应题 206. 反转链表（Reverse Linked List）](https://leetcode.com/problems/reverse-linked-list/)

课程版本说明：

- LeetCode 206：单链表反转部分对应。

#### 思路

遍历时保留 pre、head、next 三个位置。先用 next 保存原后继，再把 head.next 指向 pre，最后让 pre 和 head 同时前进。双向链表还要把当前节点的 last 改为原 next。

#### 代码答案

```java
package class03;

import java.util.ArrayList;
import java.util.List;

public class Code01_ReverseList {

	public static class Node {
		public int value;
		public Node next;

		public Node(int data) {
			value = data;
		}
	}

	public static class DoubleNode {
		public int value;
		public DoubleNode last;
		public DoubleNode next;

		public DoubleNode(int data) {
			value = data;
		}
	}

	//  head
	//   a    ->   b    ->  c  ->  null
	//   c    ->   b    ->  a  ->  null
	public static Node reverseLinkedList(Node head) {
		Node pre = null;
		Node next = null;
		while (head != null) {
			// 先保存尚未处理的后缀，随后修改 next 指针才不会丢链。
			next = head.next;
			// 把当前节点接到已反转前缀的头部。
			head.next = pre;
			// 当前节点成为已反转部分的新头。
			pre = head;
			// 转去处理原链表中的下一个节点。
			head = next;
		}
		return pre;
	}

	public static DoubleNode reverseDoubleList(DoubleNode head) {
		DoubleNode pre = null;
		DoubleNode next = null;
		while (head != null) {
			// 先保存尚未处理的后缀，随后修改 next 指针才不会丢链。
			next = head.next;
			// 把当前节点接到已反转前缀的头部。
			head.next = pre;
			// 双向链表反转后，原后继变成当前节点的前驱。
			head.last = next;
			// 当前节点成为已反转部分的新头。
			pre = head;
			// 转去处理原链表中的下一个节点。
			head = next;
		}
		return pre;
	}

	public static Node testReverseLinkedList(Node head) {
		if (head == null) {
			return null;
		}
		ArrayList<Node> list = new ArrayList<>();
		while (head != null) {
			list.add(head);
			head = head.next;
		}
		list.get(0).next = null;
		int N = list.size();
		for (int i = 1; i < N; i++) {
			list.get(i).next = list.get(i - 1);
		}
		return list.get(N - 1);
	}

	public static DoubleNode testReverseDoubleList(DoubleNode head) {
		if (head == null) {
			return null;
		}
		ArrayList<DoubleNode> list = new ArrayList<>();
		while (head != null) {
			list.add(head);
			head = head.next;
		}
		list.get(0).next = null;
		DoubleNode pre = list.get(0);
		int N = list.size();
		for (int i = 1; i < N; i++) {
			DoubleNode cur = list.get(i);
			cur.last = null;
			cur.next = pre;
			pre.last = cur;
			pre = cur;
		}
		return list.get(N - 1);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static Node generateRandomLinkedList(int len, int value) {
		int size = (int) (Math.random() * (len + 1));
		if (size == 0) {
			return null;
		}
		size--;
		Node head = new Node((int) (Math.random() * (value + 1)));
		Node pre = head;
		while (size != 0) {
			Node cur = new Node((int) (Math.random() * (value + 1)));
			pre.next = cur;
			pre = cur;
			size--;
		}
		return head;
	}

	// for test
	public static DoubleNode generateRandomDoubleList(int len, int value) {
		int size = (int) (Math.random() * (len + 1));
		if (size == 0) {
			return null;
		}
		size--;
		DoubleNode head = new DoubleNode((int) (Math.random() * (value + 1)));
		DoubleNode pre = head;
		while (size != 0) {
			DoubleNode cur = new DoubleNode((int) (Math.random() * (value + 1)));
			pre.next = cur;
			cur.last = pre;
			pre = cur;
			size--;
		}
		return head;
	}

	// for test
	public static List<Integer> getLinkedListOriginOrder(Node head) {
		List<Integer> ans = new ArrayList<>();
		while (head != null) {
			ans.add(head.value);
			head = head.next;
		}
		return ans;
	}

	// for test
	public static boolean checkLinkedListReverse(List<Integer> origin, Node head) {
		for (int i = origin.size() - 1; i >= 0; i--) {
			if (!origin.get(i).equals(head.value)) {
				return false;
			}
			head = head.next;
		}
		return true;
	}

	// for test
	public static List<Integer> getDoubleListOriginOrder(DoubleNode head) {
		List<Integer> ans = new ArrayList<>();
		while (head != null) {
			ans.add(head.value);
			head = head.next;
		}
		return ans;
	}

	// for test
	public static boolean checkDoubleListReverse(List<Integer> origin, DoubleNode head) {
		DoubleNode end = null;
		for (int i = origin.size() - 1; i >= 0; i--) {
			if (!origin.get(i).equals(head.value)) {
				return false;
			}
			end = head;
			head = head.next;
		}
		for (int i = 0; i < origin.size(); i++) {
			if (!origin.get(i).equals(end.value)) {
				return false;
			}
			end = end.last;
		}
		return true;
	}

	// for test
	public static void main(String[] args) {
		int len = 50;
		int value = 100;
		int testTime = 100000;
		System.out.println("test begin!");
		for (int i = 0; i < testTime; i++) {
			Node node1 = generateRandomLinkedList(len, value);
			List<Integer> list1 = getLinkedListOriginOrder(node1);
			node1 = reverseLinkedList(node1);
			if (!checkLinkedListReverse(list1, node1)) {
				System.out.println("Oops1!");
			}

			Node node2 = generateRandomLinkedList(len, value);
			List<Integer> list2 = getLinkedListOriginOrder(node2);
			node2 = testReverseLinkedList(node2);
			if (!checkLinkedListReverse(list2, node2)) {
				System.out.println("Oops2!");
			}

			DoubleNode node3 = generateRandomDoubleList(len, value);
			List<Integer> list3 = getDoubleListOriginOrder(node3);
			node3 = reverseDoubleList(node3);
			if (!checkDoubleListReverse(list3, node3)) {
				System.out.println("Oops3!");
			}

			DoubleNode node4 = generateRandomDoubleList(len, value);
			List<Integer> list4 = getDoubleListOriginOrder(node4);
			node4 = testReverseDoubleList(node4);
			if (!checkDoubleListReverse(list4, node4)) {
				System.out.println("Oops4!");
			}

		}
		System.out.println("test finish!");

	}
```

#### 题解

**为什么正确**

每轮开始，pre 指向已反转前缀的头，head 指向未处理后缀。改链只影响当前节点，提前保存的 next 保证后缀不丢失；加入当前节点后，反转前缀扩大一个节点。head 为空时，pre 就是新头。

**复杂度**

时间 O(N)，额外空间 O(1)。

**边界与易错点**

先断链再保存 next 会丢失后续节点。双向链表的 next、last 都要更新；返回 pre，不能返回已经变成 null 的 head。

### 3.2 删除链表中的指定值

#### 题目

给定单链表头节点 `head` 和整数 `num`，删除链表中所有值等于 `num` 的节点并返回新头节点。

**输入、输出与约束**

输入无环单链表和待删除值；返回新头，空表和全部删除均返回 null。

**函数签名（课程入口）**

```java
public static Node removeValue(Node head, int num);
```

**示例**

```text
输入：head=2→2→1→2→3, num=2
输出：1→3
```

解释：头部两个 2 和中间的 2 都被移除。

**出处与版本差异**

- [课程源码：class03/Code02_DeleteGivenValue.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class03/Code02_DeleteGivenValue.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先持续跳过头部等于 num 的节点，找到第一个需要保留的节点作为新头。之后用 pre 指向最近保留节点、cur 扫描后续节点；遇到待删值就让 pre.next 跳过 cur，否则更新 pre。

#### 代码答案

```java
package class03;

public class Code02_DeleteGivenValue {

	public static class Node {
		public int value;
		public Node next;

		public Node(int data) {
			this.value = data;
		}
	}

	// head = removeValue(head, 2);
	public static Node removeValue(Node head, int num) {
		// head来到第一个不需要删的位置
		// 先处理没有前驱可用的头部删除情况。
		while (head != null) {
			if (head.value != num) {
				break;
			}
			head = head.next;
		}
		// 1 ) head == null
		// 2 ) head != null
		Node pre = head;
		Node cur = head;
		while (cur != null) {
			if (cur.value == num) {
				// 绕过 cur；pre 仍是最后保留节点，以支持连续删除。
				pre.next = cur.next;
			} else {
				// 当前节点需要保留，更新最后保留节点。
				pre = cur;
			}
			cur = cur.next;
		}
		return head;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：头部也可能连续删除，必须先更新 head。删除 cur 时不要推进 pre，否则会把已删除节点重新当成前驱。

本条未附独立随机对数器。

#### 题解

**为什么正确**

pre 之前的保留节点已经正确连接，cur 及后面尚待检查。删除分支不会改变 pre，因此连续多个待删节点也都能被跳过；保留分支把合法节点接入前缀，保持原有相对次序。

**复杂度**

时间 O(N)，额外空间 O(1)。

**边界与易错点**

头部也可能连续删除，必须先更新 head。删除 cur 时不要推进 pre，否则会把已删除节点重新当成前驱。

### 3.3 双端链表实现栈和队列

#### 题目

基于双端链表实现通用的栈和队列，要求正确维护头尾指针并支持常数时间入队、出队、压栈和弹栈。

**输入、输出与约束**

保存泛型元素；空结构取出返回 null，按方法返回值判断操作结果。

**函数签名（课程入口）**

```java
public void addFromHead(T value);
public void addFromBottom(T value);
public T popFromHead();
public T popFromBottom();
public boolean isEmpty();
public void push(T value);
```

**示例**

```text
输入：依次加入 1、2；取出一次
输出：栈得到 2，队列得到 1
```

解释：插入顺序相同，取出端不同导致顺序不同。

**出处与版本差异**

- [课程源码：class03/Code03_DoubleEndsQueueToStackAndQueue.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class03/Code03_DoubleEndsQueueToStackAndQueue.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先实现双端链表：head、tail 分别支持从两端加入或移除。栈从同一端加入和弹出；队列从一端加入、另一端取出。空结构插入首节点时，让 head 与 tail 同时指向它。

#### 代码答案

```java
package class03;

import java.util.LinkedList;
import java.util.Queue;
import java.util.Stack;

public class Code03_DoubleEndsQueueToStackAndQueue {

	public static class Node<T> {
		public T value;
		public Node<T> last;
		public Node<T> next;

		public Node(T data) {
			value = data;
		}
	}

	public static class DoubleEndsQueue<T> {
		public Node<T> head;
		public Node<T> tail;

		// 头插：新节点成为最早被头部弹出的元素。
		public void addFromHead(T value) {
			Node<T> cur = new Node<T>(value);
			if (head == null) {
				head = cur;
				tail = cur;
			} else {
				cur.next = head;
				head.last = cur;
				head = cur;
			}
		}

		// 尾插：更新尾节点及其与原尾之间的双向连接。
		public void addFromBottom(T value) {
			Node<T> cur = new Node<T>(value);
			if (head == null) {
				head = cur;
				tail = cur;
			} else {
				cur.last = tail;
				tail.next = cur;
				tail = cur;
			}
		}

		// 头部弹出：分别处理空表、唯一节点和多节点情况。
		public T popFromHead() {
			if (head == null) {
				return null;
			}
			Node<T> cur = head;
			// 两端相同表示只有一个节点，删除它后两端都要清空。
			if (head == tail) {
				head = null;
				tail = null;
			} else {
				head = head.next;
				cur.next = null;
				// 断开新头指向旧头的反向链接，维持头节点无前驱。
				head.last = null;
			}
			return cur.value;
		}

		// 尾部弹出与头部操作对称，同样要维护空表两端一致。
		public T popFromBottom() {
			if (head == null) {
				return null;
			}
			Node<T> cur = tail;
			// 两端相同表示只有一个节点，删除它后两端都要清空。
			if (head == tail) {
				head = null;
				tail = null;
			} else {
				tail = tail.last;
				// 断开新尾指向旧尾的链接，维持尾节点无后继。
				tail.next = null;
				cur.last = null;
			}
			return cur.value;
		}

		public boolean isEmpty() {
			return head == null;
		}

	}

	public static class MyStack<T> {
		private DoubleEndsQueue<T> queue;

		public MyStack() {
			queue = new DoubleEndsQueue<T>();
		}

		public void push(T value) {
			// 从头部插入；栈从同端弹出，队列从另一端弹出。
			queue.addFromHead(value);
		}

		public T pop() {
			// 同端先入后出，对应栈的弹出操作。
			return queue.popFromHead();
		}

		public boolean isEmpty() {
			return queue.isEmpty();
		}

	}

	public static class MyQueue<T> {
		private DoubleEndsQueue<T> queue;

		public MyQueue() {
			queue = new DoubleEndsQueue<T>();
		}

		public void push(T value) {
			// 从头部插入；栈从同端弹出，队列从另一端弹出。
			queue.addFromHead(value);
		}

		public T poll() {
			// 异端先入先出，对应队列的出队操作。
			return queue.popFromBottom();
		}

		public boolean isEmpty() {
			return queue.isEmpty();
		}

	}

	public static boolean isEqual(Integer o1, Integer o2) {
		if (o1 == null && o2 != null) {
			return false;
		}
		if (o1 != null && o2 == null) {
			return false;
		}
		if (o1 == null && o2 == null) {
			return true;
		}
		return o1.equals(o2);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int oneTestDataNum = 100;
		int value = 10000;
		int testTimes = 100000;
		for (int i = 0; i < testTimes; i++) {
			MyStack<Integer> myStack = new MyStack<>();
			MyQueue<Integer> myQueue = new MyQueue<>();
			Stack<Integer> stack = new Stack<>();
			Queue<Integer> queue = new LinkedList<>();
			for (int j = 0; j < oneTestDataNum; j++) {
				int nums = (int) (Math.random() * value);
				if (stack.isEmpty()) {
					myStack.push(nums);
					stack.push(nums);
				} else {
					if (Math.random() < 0.5) {
						myStack.push(nums);
						stack.push(nums);
					} else {
						if (!isEqual(myStack.pop(), stack.pop())) {
							System.out.println("oops!");
						}
					}
				}
				int numq = (int) (Math.random() * value);
				if (queue.isEmpty()) {
					myQueue.push(numq);
					queue.offer(numq);
				} else {
					if (Math.random() < 0.5) {
						myQueue.push(numq);
						queue.offer(numq);
					} else {
						if (!isEqual(myQueue.poll(), queue.poll())) {
							System.out.println("oops!");
						}
					}
				}
			}
		}
		System.out.println("finish!");
	}
```

#### 题解

**为什么正确**

双向链接让两端操作只改常数条指针。栈同端操作使最新节点最先离开；队列异端操作使最早插入的节点最先离开。保持空结构两端同时为空、单节点两端相同，即可覆盖退化边界。

**复杂度**

各次 push、pop、offer、poll 均为 O(1)，存放 N 个元素使用 O(N) 空间。

**边界与易错点**

移除唯一节点时必须同时清空 head 和 tail。移除头后清空新头的 last；移除尾后清空新尾的 next。

### 3.4 环形数组实现队列

#### 题目

使用固定长度数组实现循环队列，支持入队、出队和判空，并在下标到达数组末尾时回绕到开头。

**输入、输出与约束**

构造时容量必须大于 0；入队 int，出队返回队首，空队列出队或满队列入队会抛异常。

**函数签名（课程入口）**

```java
public void push(int value);
public int pop();
public boolean isEmpty();
```

**示例**

```text
输入：容量 2；push(1)，push(2)，pop()，push(3)，pop()
输出：两次 pop 分别得到 1、2
```

解释：第二次入队环绕到数组开头，但队列逻辑顺序未变。

**出处与版本差异**

- [课程源码：class03/Code04_RingArray.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class03/Code04_RingArray.java)。
- [LeetCode 对应题 622. 设计循环队列（Design Circular Queue）](https://leetcode.com/problems/design-circular-queue/)

课程版本说明：

- LeetCode 622：循环队列结构对应；课程满/空操作抛异常，原题使用布尔结果并有额外接口。

#### 思路

用 pushi 指向下次写入位置、polli 指向下次读取位置，size 表示当前元素数。每次读写后把对应指针加一，到数组末尾时回到 0。用 size 区分指针重合时的空和满。

#### 代码答案

```java
package class03;

public class Code04_RingArray {

	public static class MyQueue {
		private int[] arr;
		private int pushi;// end
		private int polli;// begin
		private int size;
		private final int limit;

		public MyQueue(int limit) {
			arr = new int[limit];
			pushi = 0;
			polli = 0;
			size = 0;
			this.limit = limit;
		}

		public void push(int value) {
			if (size == limit) {
				throw new RuntimeException("队列满了，不能再加了");
			}
			size++;
			// 写入环形队尾；size 已确保该位置不属于仍未出队的数据。
			arr[pushi] = value;
			// 写指针向后移动，到数组尾部后回绕。
			pushi = nextIndex(pushi);
		}

		public int pop() {
			if (size == 0) {
				throw new RuntimeException("队列空了，不能再拿了");
			}
			size--;
			// 读取当前逻辑队首，随后才移动读指针。
			int ans = arr[polli];
			polli = nextIndex(polli);
			return ans;
		}

		public boolean isEmpty() {
			return size == 0;
		}

		// 如果现在的下标是i，返回下一个位置
		private int nextIndex(int i) {
			return i < limit - 1 ? i + 1 : 0;
		}

	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：写入前检查 size==limit，读取前检查 size==0。源码以异常表示满或空，与 LeetCode 返回 false 的接口不同。

本条未附独立随机对数器。

#### 题解

**为什么正确**

逻辑队列从 polli 起包含 size 个连续的环形位置；入队写在逻辑队尾，出队读取逻辑队首，所以环绕不会改变先进先出顺序。

**复杂度**

单次入队、出队 O(1)，容量 C 的存储空间 O(C)。

**边界与易错点**

写入前检查 size==limit，读取前检查 size==0。源码以异常表示满或空，与 LeetCode 返回 false 的接口不同。

### 3.5 支持常数时间查询最小值的栈

#### 题目

设计一个栈，在支持普通压栈和弹栈的同时，能够在 O(1) 时间返回当前最小值。

**输入、输出与约束**

支持 int 的 push、pop、getmin；查询或弹出时要求栈非空。

**函数签名（课程入口）**

```java
public void push(int newNum);
public int pop();
public int getmin();
```

**示例**

```text
输入：push(2)，push(1)，push(1)，pop()，getmin()
输出：1
```

解释：只移除了一个 1，另一个仍是最小值。

**出处与版本差异**

- [课程源码：class03/Code05_GetMinStack.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class03/Code05_GetMinStack.java)。
- [LeetCode 原题 155. 最小栈（Min Stack）](https://leetcode.com/problems/min-stack/)

#### 思路

普通栈保存所有元素，辅助栈保存最小值信息。版本一仅在新元素不大于当前最小时压入辅助栈，弹出最小值时同步弹出；版本二每次都压入当前最小值，让两栈高度一致。

#### 代码答案

```java
package class03;

import java.util.Stack;

public class Code05_GetMinStack {

	public static class MyStack1 {
		private Stack<Integer> stackData;
		private Stack<Integer> stackMin;

		public MyStack1() {
			stackData = new Stack<Integer>();
			stackMin = new Stack<Integer>();
		}

		public void push(int newNum) {
			// 相同最小值也要保存，保证重复值能逐个正确退栈。
			if (stackMin.isEmpty() || newNum <= this.getmin()) {
				stackMin.push(newNum);
			}
			stackData.push(newNum);
		}

		public int pop() {
			if (stackData.isEmpty()) {
				throw new RuntimeException("Your stack is empty.");
			}
			int value = stackData.pop();
			if (value == getmin()) {
				stackMin.pop();
			}
			return value;
		}

		public int getmin() {
			if (stackMin.isEmpty()) {
				throw new RuntimeException("Your stack is empty.");
			}
			return stackMin.peek();
		}
	}

	public static class MyStack2 {
		private Stack<Integer> stackData;
		private Stack<Integer> stackMin;

		public MyStack2() {
			stackData = new Stack<Integer>();
			stackMin = new Stack<Integer>();
		}

		public void push(int newNum) {
			if (stackMin.isEmpty() || newNum < getmin()) {
				stackMin.push(newNum);
			} else {
				stackMin.push(stackMin.peek());
			}
			stackData.push(newNum);
		}

		public int pop() {
			if (stackData.isEmpty()) {
				throw new RuntimeException("Your stack is empty.");
			}
			stackMin.pop();
			return stackData.pop();
		}

		public int getmin() {
			if (stackMin.isEmpty()) {
				throw new RuntimeException("Your stack is empty.");
			}
			return stackMin.peek();
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		MyStack1 stack1 = new MyStack1();
		stack1.push(3);
		System.out.println(stack1.getmin());
		stack1.push(4);
		System.out.println(stack1.getmin());
		stack1.push(1);
		System.out.println(stack1.getmin());
		System.out.println(stack1.pop());
		System.out.println(stack1.getmin());

		System.out.println("=============");

		MyStack2 stack2 = new MyStack2();
		stack2.push(3);
		System.out.println(stack2.getmin());
		stack2.push(4);
		System.out.println(stack2.getmin());
		stack2.push(1);
		System.out.println(stack2.getmin());
		System.out.println(stack2.pop());
		System.out.println(stack2.getmin());
	}
```

#### 题解

**为什么正确**

版本一的辅助栈保留了每个仍有效的历史最小值；重复最小值也逐次入栈，所以只弹出一个不会丢失另一个。版本二在每一层记录该层以下所有元素的最小值，退栈即可恢复上一层答案。

**复杂度**

全部公开栈操作 O(1)，N 个元素额外空间 O(N)。

**边界与易错点**

相等最小值不能漏记。不要每次 getmin 时遍历数据栈，否则破坏常数时间要求。空栈查询与弹出按源码抛异常。

### 3.6 两个栈实现队列

#### 题目

只使用两个栈实现先进先出的队列，支持入队、出队和查看队首元素。

**输入、输出与约束**

提供 add、poll、peek；poll 删除队首，peek 只查看，空队列不可读。

**函数签名（课程入口）**

```java
public void add(int pushInt);
public int poll();
public int peek();
```

**示例**

```text
输入：add(1)，add(2)，poll()，add(3)，poll()
输出：1、2
```

解释：加入 3 时，尚未出队的 2 仍应优先。

**出处与版本差异**

- [课程源码：class03/Code06_TwoStacksImplementQueue.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class03/Code06_TwoStacksImplementQueue.java)。
- [LeetCode 原题 232. 用栈实现队列（Implement Queue using Stacks）](https://leetcode.com/problems/implement-queue-using-stacks/)

#### 思路

stackPush 接收新元素，stackPop 提供队首。只有 stackPop 为空时，才把 stackPush 全部倒入 stackPop；倒栈会把最早入队元素翻到顶部。若 stackPop 非空，先把其中旧元素消费完。

#### 代码答案

```java
package class03;

import java.util.Stack;

public class Code06_TwoStacksImplementQueue {

	public static class TwoStacksQueue {
		public Stack<Integer> stackPush;
		public Stack<Integer> stackPop;

		public TwoStacksQueue() {
			stackPush = new Stack<Integer>();
			stackPop = new Stack<Integer>();
		}

		// push栈向pop栈倒入数据
		private void pushToPop() {
			// 只有旧元素已经耗尽时，才允许把新段整体翻转到出队栈。
			if (stackPop.empty()) {
				while (!stackPush.empty()) {
					// 倒栈反转次序，使原先最早压入的元素最后到达栈顶。
					stackPop.push(stackPush.pop());
				}
			}
		}

		public void add(int pushInt) {
			stackPush.push(pushInt);
			pushToPop();
		}

		public int poll() {
			if (stackPop.empty() && stackPush.empty()) {
				throw new RuntimeException("Queue is empty!");
			}
			pushToPop();
			return stackPop.pop();
		}

		public int peek() {
			if (stackPop.empty() && stackPush.empty()) {
				throw new RuntimeException("Queue is empty!");
			}
			pushToPop();
			// 查看但不删除队首，不改变之后的出队顺序。
			return stackPop.peek();
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		TwoStacksQueue test = new TwoStacksQueue();
		test.add(1);
		test.add(2);
		test.add(3);
		System.out.println(test.peek());
		System.out.println(test.poll());
		System.out.println(test.peek());
		System.out.println(test.poll());
		System.out.println(test.peek());
		System.out.println(test.poll());
	}
```

#### 题解

**为什么正确**

stackPop 中从顶到底是尚未出队的旧元素顺序，stackPush 中从底到顶是新元素顺序。仅在旧段清空后倒入新段，才能保证所有更早入队元素先于后来的元素离开。

**复杂度**

单次倒栈 O(N)，每个元素只转移一次，因此每次操作均摊 O(1)；空间 O(N)。

**边界与易错点**

stackPop 非空时倒入新元素会把新元素盖到旧元素上，破坏顺序。倒栈必须一次倒完。空队列访问按源码抛异常。

### 3.7 两个队列实现栈

#### 题目

只使用两个队列实现后进先出的栈，支持压栈、弹栈、查看栈顶和判空。

**输入、输出与约束**

以两个队列实现栈；读取或弹出时要求非空。

**函数签名（课程入口）**

```java
public void push(T value);
public T poll();
public T peek();
public boolean isEmpty();
```

**示例**

```text
输入：push(1)，push(2)，peek()，poll()
输出：2、2
```

解释：peek 不删除 2，所以随后的 poll 仍得到它。

**出处与版本差异**

- [课程源码：class03/Code07_TwoQueueImplementStack.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class03/Code07_TwoQueueImplementStack.java)。
- [LeetCode 原题 225. 用队列实现栈（Implement Stack using Queues）](https://leetcode.com/problems/implement-stack-using-queues/)

#### 思路

新元素加入主队列尾部。弹栈时把前面的 size-1 个元素搬到辅助队列，剩下的最后入队元素就是栈顶；取出后交换两队列引用。peek 则在取得该元素后再加入辅助队列，保留数据。

#### 代码答案

```java
package class03;

import java.util.LinkedList;
import java.util.Queue;
import java.util.Stack;

public class Code07_TwoQueueImplementStack {

	public static class TwoQueueStack<T> {
		public Queue<T> queue;
		public Queue<T> help;

		public TwoQueueStack() {
			queue = new LinkedList<>();
			help = new LinkedList<>();
		}

		public void push(T value) {
			queue.offer(value);
		}

		public T poll() {
			// 把除最后一个元素之外的所有旧元素搬走，暴露栈顶。
			while (queue.size() > 1) {
				// 保持剩余元素的相对顺序，搬入辅助队列。
				help.offer(queue.poll());
			}
			T ans = queue.poll();
			// 两队列交换角色，下一轮继续使用保存剩余元素的队列。
			Queue<T> tmp = queue;
			queue = help;
			help = tmp;
			return ans;
		}

		public T peek() {
			// 把除最后一个元素之外的所有旧元素搬走，暴露栈顶。
			while (queue.size() > 1) {
				// 保持剩余元素的相对顺序，搬入辅助队列。
				help.offer(queue.poll());
			}
			T ans = queue.poll();
			help.offer(ans);
			// 两队列交换角色，下一轮继续使用保存剩余元素的队列。
			Queue<T> tmp = queue;
			queue = help;
			help = tmp;
			return ans;
		}

		public boolean isEmpty() {
			return queue.isEmpty();
		}

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		System.out.println("test begin");
		TwoQueueStack<Integer> myStack = new TwoQueueStack<>();
		Stack<Integer> test = new Stack<>();
		int testTime = 1000000;
		int max = 1000000;
		for (int i = 0; i < testTime; i++) {
			if (myStack.isEmpty()) {
				if (!test.isEmpty()) {
					System.out.println("Oops");
				}
				int num = (int) (Math.random() * max);
				myStack.push(num);
				test.push(num);
			} else {
				if (Math.random() < 0.25) {
					int num = (int) (Math.random() * max);
					myStack.push(num);
					test.push(num);
				} else if (Math.random() < 0.5) {
					if (!myStack.peek().equals(test.peek())) {
						System.out.println("Oops");
					}
				} else if (Math.random() < 0.75) {
					if (!myStack.poll().equals(test.pop())) {
						System.out.println("Oops");
					}
				} else {
					if (myStack.isEmpty() != test.isEmpty()) {
						System.out.println("Oops");
					}
				}
			}
		}

		System.out.println("test finish!");

	}
```

#### 题解

**为什么正确**

队列无法直接访问尾部，通过搬走前缀可让最后加入的元素来到队首。其余元素按原顺序进入辅助队列，交换引用后仍能用同样方式找到下一层栈顶。

**复杂度**

push 为 O(1)，pop/peek 最坏 O(N)，额外存储 O(N)。

**边界与易错点**

peek 不能丢掉被查看的最后一个元素。操作后要交换 queue 和 help，否则后续操作会在空队列上进行。

### 3.8 递归求数组最大值

#### 题目

给定整数数组，要求使用递归而不是显式循环返回数组最大值。

**输入、输出与约束**

输入非空 int 数组，返回其最大元素。

**函数签名（课程入口）**

```java
public static int getMax(int[] arr);
```

**示例**

```text
输入：arr=[-3,7,2,5]
输出：7
```

解释：左右半区间最大值分别为 7 和 5。

**出处与版本差异**

- [课程源码：class03/Code08_GetMax.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class03/Code08_GetMax.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

定义 process(arr,L,R) 返回闭区间最大值。单元素区间直接返回该元素；否则平分区间，分别求左右最大值，再取二者较大值。

#### 代码答案

```java
package class03;

public class Code08_GetMax {

	// 求arr中的最大值
	public static int getMax(int[] arr) {
		return process(arr, 0, arr.length - 1);
	}

	// arr[L..R]范围上求最大值  L ... R   N
	public static int process(int[] arr, int L, int R) {
		// arr[L..R]范围上只有一个数，直接返回，base case
		// 只剩一个元素时，最大值无需再比较。
		if (L == R) {
			return arr[L];
		}
		// L...R 不只一个数
		// mid = (L + R) / 2
		int mid = L + ((R - L) >> 1); // 中点   	1
		// 求左半闭区间最大值。
		int leftMax = process(arr, L, mid);
		// 求右半闭区间最大值，起点越过 mid。
		int rightMax = process(arr, mid + 1, R);
		// 两半覆盖整个区间，比较各自最大值即可合并。
		return Math.max(leftMax, rightMax);
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：必须保证数组非空。左区间为 [L,mid]，右区间为 [mid+1,R]，避免重复或漏掉中点。

本条未附独立随机对数器。

#### 题解

**为什么正确**

左右子区间不重叠且并集是原区间，原区间最大值必在其中一边。若两个递归结果正确，取较大值即为父区间答案；单元素提供归纳起点。

**复杂度**

T(N)=2T(N/2)+O(1)，时间 O(N)；递归深度及额外栈空间 O(log N)。

**边界与易错点**

必须保证数组非空。左区间为 [L,mid]，右区间为 [mid+1,R]，避免重复或漏掉中点。

### 3.9 哈希表与有序表的基本用法

#### 题目

演示 Java 哈希映射与有序映射的插入、查询、删除、键排序和边界键操作。

**输入、输出与约束**

本条为容器接口练习；输入键值操作序列，观察查询结果与判等语义。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：TreeMap 放入键 2、5、8；floorKey(6)，ceilingKey(6)
输出：5、8
```

解释：分别返回不大于 6 的最大键和不小于 6 的最小键。

**出处与版本差异**

- [课程源码：class03/HashMapAndSortedMap.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class03/HashMapAndSortedMap.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

HashMap 按键的 hashCode 与 equals 管理映射，不保证遍历顺序；TreeMap 根据比较器组织键，支持 firstKey、lastKey、floorKey、ceilingKey。课程通过不同对象是否被视为同一键展示两种判等方式。

#### 代码答案

```java
package class03;

import java.util.HashMap;
import java.util.HashSet;
import java.util.TreeMap;

public class HashMapAndSortedMap {

	public static class Node {
		public int value;

		public Node(int v) {
			value = v;
		}
	}

	public static class Zuo {
		public int value;

		public Zuo(int v) {
			value = v;
		}
	}

	public static void main(String[] args) {

		// 哈希映射侧重按键查询，不提供键的有序遍历保证。
		HashMap<Integer, String> test = new HashMap<>();
		Integer a = 19000000;
		Integer b = 19000000;
		System.out.println(a == b);

		test.put(a, "我是3");
		System.out.println(test.containsKey(b));

		Zuo z1 = new Zuo(1);
		Zuo z2 = new Zuo(1);
		// 哈希映射侧重按键查询，不提供键的有序遍历保证。
		HashMap<Zuo, String> test2 = new HashMap<>();
		test2.put(z1, "我是z1");
		System.out.println(test2.containsKey(z2));

		// UnSortedMap
		// 哈希映射侧重按键查询，不提供键的有序遍历保证。
		HashMap<Integer, String> map = new HashMap<>();
		map.put(1000000, "我是1000000");
		map.put(2, "我是2");
		map.put(3, "我是3");
		map.put(4, "我是4");
		map.put(5, "我是5");
		map.put(6, "我是6");
		map.put(1000000, "我是1000001");

		System.out.println(map.containsKey(1));
		System.out.println(map.containsKey(10));

		System.out.println(map.get(4));
		System.out.println(map.get(10));

		map.put(4, "他是4");
		System.out.println(map.get(4));

		map.remove(4);
		System.out.println(map.get(4));

		// key
		HashSet<String> set = new HashSet<>();
		set.add("abc");
		set.contains("abc");
		set.remove("abc");

		// 哈希表，增、删、改、查，在使用时，O（1）

		System.out.println("=====================");

		Integer c = 100000;
		Integer d = 100000;
		System.out.println(c.equals(d));

		Integer e = 127; // - 128 ~ 127
		Integer f = 127;
		System.out.println(e == f);

		// 哈希映射侧重按键查询，不提供键的有序遍历保证。
		HashMap<Node, String> map2 = new HashMap<>();
		Node node1 = new Node(1);
		Node node2 = node1;
		map2.put(node1, "我是node1");
		map2.put(node2, "我是node1");
		System.out.println(map2.size());

		System.out.println("======================");

		// TreeMap 有序表：接口名
		// 红黑树、avl、sb树、跳表
		// O(logN)
		System.out.println("有序表测试开始");
		// 有序映射按比较规则组织键，因此可以查询前驱和后继。
		TreeMap<Integer, String> treeMap = new TreeMap<>();

		treeMap.put(3, "我是3");
		treeMap.put(4, "我是4");
		treeMap.put(8, "我是8");
		treeMap.put(5, "我是5");
		treeMap.put(7, "我是7");
		treeMap.put(1, "我是1");
		treeMap.put(2, "我是2");

		System.out.println(treeMap.containsKey(1));
		System.out.println(treeMap.containsKey(10));

		System.out.println(treeMap.get(4));
		System.out.println(treeMap.get(10));

		treeMap.put(4, "他是4");
		System.out.println(treeMap.get(4));

		// treeMap.remove(4);
		System.out.println(treeMap.get(4));

		System.out.println("新鲜：");

		System.out.println(treeMap.firstKey());
		System.out.println(treeMap.lastKey());
		// <= 4
		System.out.println(treeMap.floorKey(4));
		// >= 4
		System.out.println(treeMap.ceilingKey(4));
		// O(logN)

	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：不要在插入后修改参与 hashCode、equals 或排序的键字段。== 比较对象身份，不等价于值相等。自定义 TreeMap 键必须可比较或提供比较器。

本条未附独立随机对数器。

#### 题解

**为什么正确**

哈希相同只是进入同一候选桶，仍需 equals 确认键相等；有序表则以 compare 返回 0 认定同一个键。因此键的判等约定决定插入是在新增条目还是覆盖旧值。

**复杂度**

HashMap 常规操作平均 O(1)，不能概括为任意输入最坏 O(1)；TreeMap 查询、插入、删除 O(log N)，空间均 O(N)。

**边界与易错点**

不要在插入后修改参与 hashCode、equals 或排序的键字段。== 比较对象身份，不等价于值相等。自定义 TreeMap 键必须可比较或提供比较器。


<a id="course-04"></a>

## 第 4 课：归并排序及其扩展题

### 4.1 归并排序

#### 题目

给定整数数组，使用归并排序将其按非递减顺序原地整理。

**输入、输出与约束**

输入 int 数组，允许空数组、负数和重复值；原地写回升序结果，但允许 O(N) 辅助空间。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：arr=[3,1,4,2]
输出：arr=[1,2,3,4]
```

解释：先分别得到 [1,3]、[2,4]，再按双指针合并。

**出处与版本差异**

- [课程源码：class04/Code01_MergeSort.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class04/Code01_MergeSort.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

递归把 [L,R] 分成两半，先让每一半有序，再用 p1、p2 比较各自最小的未处理元素，逐个写入辅助数组。非递归版本把有序块长度从 1 不断倍增，执行相同归并。

#### 代码答案

```java
package class04;

public class Code01_MergeSort {

	// 递归方法实现
	public static void mergeSort1(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		process(arr, 0, arr.length - 1);
	}

	// 请把arr[L..R]排有序
	// l...r N
	// T(N) = 2 * T(N / 2) + O(N)
	// O(N * logN)
	public static void process(int[] arr, int L, int R) {
		if (L == R) { // base case
			return;
		}
		int mid = L + ((R - L) >> 1);
		process(arr, L, mid);
		process(arr, mid + 1, R);
		merge(arr, L, mid, R);
	}

	public static void merge(int[] arr, int L, int M, int R) {
		int[] help = new int[R - L + 1];
		int i = 0;
		int p1 = L;
		int p2 = M + 1;
		while (p1 <= M && p2 <= R) {
			// 两边头部是各自剩余最小值；相等时优先取左边保持稳定。
			help[i++] = arr[p1] <= arr[p2] ? arr[p1++] : arr[p2++];
		}
		// 要么p1越界了，要么p2越界了
		// 右边已经耗尽，将左侧剩余有序元素追加到结果。
		while (p1 <= M) {
			help[i++] = arr[p1++];
		}
		while (p2 <= R) {
			help[i++] = arr[p2++];
		}
		for (i = 0; i < help.length; i++) {
			// 把本轮归并得到的区间覆盖回原数组对应位置。
			arr[L + i] = help[i];
		}
	}

	// 非递归方法实现
	public static void mergeSort2(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		int N = arr.length;
		// 步长
		int mergeSize = 1;
		while (mergeSize < N) { // log N
			// 当前左组的，第一个位置
			int L = 0;
			while (L < N) {
				if (mergeSize >= N - L) {
					break;
				}
				int M = L + mergeSize - 1;
				int R = M + Math.min(mergeSize, N - M - 1);
				merge(arr, L, M, R);
				L = R + 1;
			}
			// 防止溢出
			if (mergeSize > N / 2) {
				break;
			}
			mergeSize <<= 1;
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int[] arr1 = generateRandomArray(maxSize, maxValue);
			int[] arr2 = copyArray(arr1);
			mergeSort1(arr1);
			mergeSort2(arr2);
			if (!isEqual(arr1, arr2)) {
				System.out.println("出错了！");
				printArray(arr1);
				printArray(arr2);
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

两个子区间已经有序时，全局最小未输出元素只能位于两个指针之一。每次取较小值不会跳过更小元素；一边耗尽后，另一边剩余部分可直接接上。逐层归并便得到全局有序数组。

**复杂度**

两种归并排序时间 O(N log N)，辅助数组 O(N)；递归版另有 O(log N) 栈空间。

**边界与易错点**

相等时先取左边可保持稳定。归并必须将结果写回原 [L,R]。倍增块长度要防止移位溢出；尾部不足一个完整块时边界要截断。

### 4.2 数组小和问题

#### 题目

数组中每个元素左侧所有比它小的数之和称为该元素的小和，返回整个数组的小和。

**输入、输出与约束**

输入整数数组；返回每个位置左侧严格较小元素之和的总和，源码结果用 int，测试规模须避免溢出。

**函数签名（课程入口）**

```java
public static int smallSum(int[] arr);
```

**示例**

```text
输入：arr=[1,3,4,2,5]
输出：16
```

解释：各元素的小和分别为 0、1、4、1、10。

**出处与版本差异**

- [课程源码：class04/Code02_SmallSum.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class04/Code02_SmallSum.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

小和可改成逐个元素问：右边有多少个数比我大，我就贡献几次。归并时两半已排序，若 arr[p1]<arr[p2]，右侧从 p2 到 R 都更大，立即贡献 arr[p1]×(R-p2+1)，再推进左指针。

#### 代码答案

```java
package class04;

public class Code02_SmallSum {

	public static int smallSum(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		return process(arr, 0, arr.length - 1);
	}

	// arr[L..R]既要排好序，也要求小和返回
	// 所有merge时，产生的小和，累加
	// 左 排序   merge
	// 右 排序  merge
	// merge
	public static int process(int[] arr, int l, int r) {
		if (l == r) {
			return 0;
		}
		// l < r
		int mid = l + ((r - l) >> 1);
		return
				process(arr, l, mid)
				+
				process(arr, mid + 1, r)
				+
				merge(arr, l, mid, r);
	}

	public static int merge(int[] arr, int L, int m, int r) {
		int[] help = new int[r - L + 1];
		int i = 0;
		int p1 = L;
		int p2 = m + 1;
		int res = 0;
		while (p1 <= m && p2 <= r) {
			// 左值严格较小时，右侧从 p2 到 R 全部比它大，一次累加这些贡献。
			res += arr[p1] < arr[p2] ? (r - p2 + 1) * arr[p1] : 0;
			// 相等时先取右边，保留左值以便统计右侧后续严格更大的值。
			help[i++] = arr[p1] < arr[p2] ? arr[p1++] : arr[p2++];
		}
		while (p1 <= m) {
			help[i++] = arr[p1++];
		}
		while (p2 <= r) {
			help[i++] = arr[p2++];
		}
		for (i = 0; i < help.length; i++) {
			arr[L + i] = help[i];
		}
		return res;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int comparator(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		int res = 0;
		for (int i = 1; i < arr.length; i++) {
			for (int j = 0; j < i; j++) {
				res += arr[j] < arr[i] ? arr[j] : 0;
			}
		}
		return res;
	}

	// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100;
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int[] arr1 = generateRandomArray(maxSize, maxValue);
			int[] arr2 = copyArray(arr1);
			if (smallSum(arr1) != comparator(arr2)) {
				succeed = false;
				printArray(arr1);
				printArray(arr2);
				break;
			}
		}
		System.out.println(succeed ? "Nice!" : "Fucking fucked!");
	}
```

#### 题解

**为什么正确**

任一合法数对 (i,j) 在递归中恰好存在一层，使 i 在左半、j 在右半；它只在该层归并统计。左右内部的数对由子问题处理，跨区间数对由有序性成批统计，因此不重不漏。

**复杂度**

优化版时间 O(N log N)、空间 O(N)；双循环基准 O(N²)。

**边界与易错点**

必须严格小于；相等时先推进右指针，不能提前把左值结算掉。算法会排序原数组，对拍要使用副本。累加结果可能超 int。

### 4.3 数组逆序对数量

#### 题目

给定整数数组，返回满足 `i < j` 且 `arr[i] > arr[j]` 的逆序对数量。

**输入、输出与约束**

输入 int 数组；返回 i<j 且 arr[i]>arr[j] 的数对数量。

**函数签名（课程入口）**

```java
public static int reverPairNumber(int[] arr);
```

**示例**

```text
输入：arr=[3,1,2]
输出：2
```

解释：逆序对为 (3,1)、(3,2)。

**出处与版本差异**

- [课程源码：class04/Code03_ReversePair.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class04/Code03_ReversePair.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

左右部分先分别统计逆序对并排序。归并从两边末尾向前比较：若左侧当前值大于右侧当前值，右侧尚未归并的一整段都比它小，于是一次加入该段长度，再取出左值。

#### 代码答案

```java
package class04;

public class Code03_ReversePair {

	public static int reverPairNumber(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		return process(arr, 0, arr.length - 1);
	}

	// arr[L..R]既要排好序，也要求逆序对数量返回
	// 所有merge时，产生的逆序对数量，累加，返回
	// 左 排序 merge并产生逆序对数量
	// 右 排序 merge并产生逆序对数量
	public static int process(int[] arr, int l, int r) {
		if (l == r) {
			return 0;
		}
		// l < r
		int mid = l + ((r - l) >> 1);
		return process(arr, l, mid) + process(arr, mid + 1, r) + merge(arr, l, mid, r);
	}

	public static int merge(int[] arr, int L, int m, int r) {
		int[] help = new int[r - L + 1];
		int i = help.length - 1;
		// 从左半末尾取最大未处理值，以便成批统计右半较小值。
		int p1 = m;
		int p2 = r;
		int res = 0;
		while (p1 >= L && p2 > m) {
			// 左元素大于右侧当前最大值，右侧尚未合并部分全部与它形成逆序对。
			res += arr[p1] > arr[p2] ? (p2 - m) : 0;
			help[i--] = arr[p1] > arr[p2] ? arr[p1--] : arr[p2--];
		}
		while (p1 >= L) {
			help[i--] = arr[p1--];
		}
		while (p2 > m) {
			help[i--] = arr[p2--];
		}
		for (i = 0; i < help.length; i++) {
			arr[L + i] = help[i];
		}
		return res;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int comparator(int[] arr) {
		int ans = 0;
		for (int i = 0; i < arr.length; i++) {
			for (int j = i + 1; j < arr.length; j++) {
				if (arr[i] > arr[j]) {
					ans++;
				}
			}
		}
		return ans;
	}

	// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int[] arr1 = generateRandomArray(maxSize, maxValue);
			int[] arr2 = copyArray(arr1);
			if (reverPairNumber(arr1) != comparator(arr2)) {
				System.out.println("Oops!");
				printArray(arr1);
				printArray(arr2);
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

每个逆序对要么位于某一半，要么跨越两半，三者互斥。跨区间统计依靠已排序右半的单调性，成批计数与逐个比较等价，每个左元素只结算一次。

**复杂度**

时间 O(N log N)，辅助空间 O(N)；暴力 O(N²)。

**边界与易错点**

重复元素不构成逆序对，不能把 > 写成 ≥。统计后原数组会被排序；数量最高为 N(N-1)/2，扩大输入时应使用 long。

### 4.4 右侧两倍逆序对数量

#### 题目

给定整数数组，返回满足 `i < j` 且 `arr[i] > 2 × arr[j]` 的翻转对数量。

**输入、输出与约束**

输入 int 数组，可含负数；返回满足 i<j、arr[i]>2×arr[j] 的数对数，原数组会被排序。

**函数签名（课程入口）**

```java
public static int reversePairs(int[] arr);
```

**示例**

```text
输入：arr=[1,3,2,3,1]
输出：2
```

解释：两个值为 3 的位置分别与最后的 1 组成翻转对。

**出处与版本差异**

- [课程源码：class04/Code04_BiggerThanRightTwice.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class04/Code04_BiggerThanRightTwice.java)。
- [LeetCode 原题 493. 翻转对（Reverse Pairs）](https://leetcode.com/problems/reverse-pairs/)

#### 思路

将条件改写为对每个左元素统计右半有多少值满足左值>2×右值。两半排序后，用 windowR 从右半开头单向移动；对递增左值，满足条件的右侧前缀只会扩大。计数后再进行普通归并。

#### 代码答案

```java
package class04;

//  本题测试链接 : https://leetcode.com/problems/reverse-pairs/
public class Code04_BiggerThanRightTwice {

	public static int reversePairs(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		return process(arr, 0, arr.length - 1);
	}

	public static int process(int[] arr, int l, int r) {
		if (l == r) {
			return 0;
		}
		// l < r
		int mid = l + ((r - l) >> 1);
		return process(arr, l, mid) + process(arr, mid + 1, r) + merge(arr, l, mid, r);
	}

	public static int merge(int[] arr, int L, int m, int r) {
		// [L....M] [M+1....R]
		int ans = 0;
		// 目前囊括进来的数，是从[M+1, windowR)
		// 右窗口从右半开头起步，在扫描左半时只向右移动。
		int windowR = m + 1;
		for (int i = L; i <= m; i++) {
			// 把乘法提升到 long，避免 2×int 在比较前就发生溢出。
			while (windowR <= r && (long) arr[i] > (long) arr[windowR] * 2) {
				windowR++;
			}
			// 右半已越过的元素数，就是当前左元素产生的跨区间答案数。
			ans += windowR - m - 1;
		}
		int[] help = new int[r - L + 1];
		int i = 0;
		int p1 = L;
		int p2 = m + 1;
		while (p1 <= m && p2 <= r) {
			help[i++] = arr[p1] <= arr[p2] ? arr[p1++] : arr[p2++];
		}
		while (p1 <= m) {
			help[i++] = arr[p1++];
		}
		while (p2 <= r) {
			help[i++] = arr[p2++];
		}
		for (i = 0; i < help.length; i++) {
			arr[L + i] = help[i];
		}
		return ans;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int comparator(int[] arr) {
		int ans = 0;
		for (int i = 0; i < arr.length; i++) {
			for (int j = i + 1; j < arr.length; j++) {
				if (arr[i] > (arr[j] << 1)) {
					ans++;
				}
			}
		}
		return ans;
	}

	// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) ((maxValue + 1) * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int[] arr1 = generateRandomArray(maxSize, maxValue);
			int[] arr2 = copyArray(arr1);
			if (reversePairs(arr1) != comparator(arr2)) {
				System.out.println("Oops!");
				printArray(arr1);
				printArray(arr2);
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

固定左值时，右半满足条件的位置一定是连续前缀；左值增大不会使已经满足的右值失效，因此窗口无需回退。每个跨区间数对在其左右第一次分离的递归层被统计一次。

**复杂度**

时间 O(N log N)，额外空间 O(N)。

**边界与易错点**

比较时先转 long 再乘 2，否则极值会溢出。此条件不能直接套普通逆序对的归并分支，统计窗口和归并指针要分开。


<a id="course-05"></a>

## 第 5 课：归并计数、荷兰国旗与快速排序

### 5.1 区间和个数

#### 题目

给定整数数组 `nums` 和边界 `lower`、`upper`，返回累加和位于闭区间 `[lower, upper]` 的非空连续子数组数量。

**输入、输出与约束**

输入整数数组与 lower≤upper；返回和处于闭区间内的非空连续子数组数量。

**函数签名（课程入口）**

```java
public static int countRangeSum(int[] nums, int lower, int upper);
```

**示例**

```text
输入：nums=[-2,5,-1], lower=-2, upper=2
输出：3
```

解释：合法区间为 [-2]、[-1]、[-2,5,-1]。

**出处与版本差异**

- [课程源码：class05/Code01_CountOfRangeSum.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class05/Code01_CountOfRangeSum.java)。
- [LeetCode 原题 327. 区间和的个数（Count of Range Sum）](https://leetcode.com/problems/count-of-range-sum/)

#### 思路

子数组和写成两个前缀和之差。对当前右前缀 x，要找先前前缀落在 [x-upper,x-lower] 的数量。分治保持先前位置在左半、后续位置在右半；两半排序后用两个单调窗口维护该值域，再归并。

#### 代码答案

```java
package class05;

// 这道题直接在leetcode测评：
// https://leetcode.com/problems/count-of-range-sum/
public class Code01_CountOfRangeSum {

	public static int countRangeSum(int[] nums, int lower, int upper) {
		if (nums == null || nums.length == 0) {
			return 0;
		}
		long[] sum = new long[nums.length];
		sum[0] = nums[0];
		for (int i = 1; i < nums.length; i++) {
			sum[i] = sum[i - 1] + nums[i];
		}
		return process(sum, 0, sum.length - 1, lower, upper);
	}

	public static int process(long[] sum, int L, int R, int lower, int upper) {
		if (L == R) {
			return sum[L] >= lower && sum[L] <= upper ? 1 : 0;
		}
		int M = L + ((R - L) >> 1);
		return process(sum, L, M, lower, upper) + process(sum, M + 1, R, lower, upper)
				+ merge(sum, L, M, R, lower, upper);
	}

	public static int merge(long[] arr, int L, int M, int R, int lower, int upper) {
		int ans = 0;
		int windowL = L;
		int windowR = L;
		// [windowL, windowR)
		for (int i = M + 1; i <= R; i++) {
			// 当前前缀减 upper 得到允许的先前前缀最小值。
			long min = arr[i] - upper;
			// 当前前缀减 lower 得到允许的先前前缀最大值。
			long max = arr[i] - lower;
			while (windowR <= M && arr[windowR] <= max) {
				windowR++;
			}
			while (windowL <= M && arr[windowL] < min) {
				windowL++;
			}
			// 两个边界之间的左半前缀都能与当前右前缀组成合法子数组。
			ans += windowR - windowL;
		}
		long[] help = new long[R - L + 1];
		int i = 0;
		int p1 = L;
		int p2 = M + 1;
		while (p1 <= M && p2 <= R) {
			help[i++] = arr[p1] <= arr[p2] ? arr[p1++] : arr[p2++];
		}
		while (p1 <= M) {
			help[i++] = arr[p1++];
		}
		while (p2 <= R) {
			help[i++] = arr[p2++];
		}
		for (i = 0; i < help.length; i++) {
			arr[L + i] = help[i];
		}
		return ans;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：lower、upper 都包含端点，两窗口比较符不能相同。前缀和必须用 long。源码返回 int，超过其计数范围时需扩展返回类型。

本条未附独立随机对数器。

#### 题解

**为什么正确**

下标顺序由递归的左右划分保证，数值范围由双窗口保证。窗口下界排除 <x-upper 的值，上界排除 >x-lower 的值，二者之差正好覆盖闭区间。源码前缀数组不含显式 0，单元素递归负责统计从原数组起点开始的区间。

**复杂度**

时间 O(N log N)，空间 O(N)。

**边界与易错点**

lower、upper 都包含端点，两窗口比较符不能相同。前缀和必须用 long。源码返回 int，超过其计数范围时需扩展返回类型。

### 5.2 荷兰国旗划分与快速排序

#### 题目

给定整数数组，使用荷兰国旗划分和快速排序将其整理为非递减顺序。

**输入、输出与约束**

输入 int 数组；原地升序排序。partition 和 netherlandsFlag 是划分辅助方法，不独立完成全数组排序。

**函数签名（课程入口）**

```java
public static int[] netherlandsFlag(int[] arr, int L, int R);
public static void quickSort1(int[] arr);
public static void quickSort2(int[] arr);
public static void quickSort3(int[] arr);
```

**示例**

```text
输入：arr=[3,2,3,1], 基准=3
输出：划分后小于区含 1、2，等于区含两个 3；排序结果 [1,2,3,3]
```

解释：重复基准一次归位，不再反复递归。

**出处与版本差异**

- [课程源码：class05/Code02_PartitionAndQuickSort.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class05/Code02_PartitionAndQuickSort.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

以基准值为中心维护小于区、等于区、未检查区和大于区。当前值小于基准时扩大小于区；大于时与大于区前一格交换，但不推进检查指针；等于时直接前进。只对小于区和大于区继续排序。递归、显式栈和队列版本改变的是待处理区间的保存方式。

#### 代码答案

```java
package class05;

public class Code02_PartitionAndQuickSort {

	public static void swap(int[] arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}

	// arr[L..R]上，以arr[R]位置的数做划分值
	// <= X > X
	// <= X X
	public static int partition(int[] arr, int L, int R) {
		if (L > R) {
			return -1;
		}
		if (L == R) {
			return L;
		}
		int lessEqual = L - 1;
		int index = L;
		while (index < R) {
			if (arr[index] <= arr[R]) {
				swap(arr, index, ++lessEqual);
			}
			index++;
		}
		swap(arr, ++lessEqual, R);
		return lessEqual;
	}

	// arr[L...R] 玩荷兰国旗问题的划分，以arr[R]做划分值
	// <arr[R] ==arr[R] > arr[R]
	public static int[] netherlandsFlag(int[] arr, int L, int R) {
		if (L > R) { // L...R L>R
			return new int[] { -1, -1 };
		}
		if (L == R) {
			return new int[] { L, R };
		}
		int less = L - 1; // < 区 右边界
		int more = R; // > 区 左边界
		int index = L;
		while (index < more) { // 当前位置，不能和 >区的左边界撞上
			if (arr[index] == arr[R]) {
				index++;
			} else if (arr[index] < arr[R]) {
//				swap(arr, less + 1, index);
//				less++;
//				index++;
				// 当前较小值放到小于区末尾，新换来的值来自已检查等于区。
				swap(arr, index++, ++less);
			} else { // >
				// 把较大值送入右侧区；换来的值未知，index 暂不前进。
				swap(arr, index, --more);
			}
		}
		// 最后把末尾保存的基准放入等于区边界。
		swap(arr, more, R); // <[R]   =[R]   >[R]
		return new int[] { less + 1, more };
	}

	public static void quickSort1(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		process1(arr, 0, arr.length - 1);
	}

	public static void process1(int[] arr, int L, int R) {
		if (L >= R) {
			return;
		}
		// L..R partition arr[R] [ <=arr[R] arr[R] >arr[R] ]
		int M = partition(arr, L, R);
		process1(arr, L, M - 1);
		process1(arr, M + 1, R);
	}

	public static void quickSort2(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		process2(arr, 0, arr.length - 1);
	}

	// arr[L...R] 排有序，快排2.0方式
	public static void process2(int[] arr, int L, int R) {
		if (L >= R) {
			return;
		}
		// [ equalArea[0]  ,  equalArea[0]]
		// 记录整个等于区，只把两侧仍无序的区间交给后续排序。
		int[] equalArea = netherlandsFlag(arr, L, R);
		process2(arr, L, equalArea[0] - 1);
		process2(arr, equalArea[1] + 1, R);
	}

	public static void quickSort3(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		process3(arr, 0, arr.length - 1);
	}

	public static void process3(int[] arr, int L, int R) {
		if (L >= R) {
			return;
		}
		swap(arr, L + (int) (Math.random() * (R - L + 1)), R);
		// 记录整个等于区，只把两侧仍无序的区间交给后续排序。
		int[] equalArea = netherlandsFlag(arr, L, R);
		process3(arr, L, equalArea[0] - 1);
		process3(arr, equalArea[1] + 1, R);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100;
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int[] arr1 = generateRandomArray(maxSize, maxValue);
			int[] arr2 = copyArray(arr1);
			int[] arr3 = copyArray(arr1);
			quickSort1(arr1);
			quickSort2(arr2);
			quickSort3(arr3);
			if (!isEqual(arr1, arr2) || !isEqual(arr2, arr3)) {
				succeed = false;
				break;
			}
		}
		System.out.println(succeed ? "Nice!" : "Oops!");

	}
```

#### 题解

**为什么正确**

四区间划分在每次交换后保持成立，且未检查区必缩短。来自右端的交换值尚未分类，因此必须重新检查。划分结束时等于区已经处于最终位置，递归两侧即可得到整体有序。

**复杂度**

固定基准最坏 O(N²)；随机基准期望 O(N log N)，最坏仍为 O(N²)。递归栈期望 O(log N)、最坏 O(N)；非递归待处理区间空间最坏 O(N)。

**边界与易错点**

大于分支不能 index++。单元素和空区间直接结束。返回的是等于区左右边界，递归区间必须排除整段等于区。

### 5.3 快速排序的递归与非递归实现

#### 题目

给定整数数组，使用荷兰国旗划分和快速排序将其整理为非递减顺序。

**输入、输出与约束**

输入 int 数组；原地升序排序。partition 和 netherlandsFlag 是划分辅助方法，不独立完成全数组排序。

**函数签名（课程入口）**

```java
public static int[] netherlandsFlag(int[] arr, int L, int R);
public static void quickSort1(int[] arr);
public static void quickSort2(int[] arr);
public static void quickSort3(int[] arr);
```

**示例**

```text
输入：arr=[3,2,3,1], 基准=3
输出：划分后小于区含 1、2，等于区含两个 3；排序结果 [1,2,3,3]
```

解释：重复基准一次归位，不再反复递归。

**出处与版本差异**

- [课程源码：class05/Code03_QuickSortRecursiveAndUnrecursive.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class05/Code03_QuickSortRecursiveAndUnrecursive.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

以基准值为中心维护小于区、等于区、未检查区和大于区。当前值小于基准时扩大小于区；大于时与大于区前一格交换，但不推进检查指针；等于时直接前进。只对小于区和大于区继续排序。递归、显式栈和队列版本改变的是待处理区间的保存方式。

#### 代码答案

```java
package class05;

import java.util.LinkedList;
import java.util.Queue;
import java.util.Stack;

public class Code03_QuickSortRecursiveAndUnrecursive {

	// 荷兰国旗问题
	public static int[] netherlandsFlag(int[] arr, int L, int R) {
		if (L > R) {
			return new int[] { -1, -1 };
		}
		if (L == R) {
			return new int[] { L, R };
		}
		int less = L - 1;
		int more = R;
		int index = L;
		while (index < more) {
			if (arr[index] == arr[R]) {
				index++;
			} else if (arr[index] < arr[R]) {
				// 当前较小值放到小于区末尾，新换来的值来自已检查等于区。
				swap(arr, index++, ++less);
			} else {
				// 把较大值送入右侧区；换来的值未知，index 暂不前进。
				swap(arr, index, --more);
			}
		}
		// 最后把末尾保存的基准放入等于区边界。
		swap(arr, more, R);
		return new int[] { less + 1, more };
	}

	public static void swap(int[] arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}

	// 快排递归版本
	public static void quickSort1(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		process(arr, 0, arr.length - 1);
	}

	public static void process(int[] arr, int L, int R) {
		if (L >= R) {
			return;
		}
		swap(arr, L + (int) (Math.random() * (R - L + 1)), R);
		// 记录整个等于区，只把两侧仍无序的区间交给后续排序。
		int[] equalArea = netherlandsFlag(arr, L, R);
		process(arr, L, equalArea[0] - 1);
		process(arr, equalArea[1] + 1, R);
	}

	// 快排非递归版本需要的辅助类
	// 要处理的是什么范围上的排序
	public static class Op {
		public int l;
		public int r;

		public Op(int left, int right) {
			l = left;
			r = right;
		}
	}

	// 快排3.0 非递归版本 用栈来执行
	public static void quickSort2(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		int N = arr.length;
		swap(arr, (int) (Math.random() * N), N - 1);
		// 记录整个等于区，只把两侧仍无序的区间交给后续排序。
		int[] equalArea = netherlandsFlag(arr, 0, N - 1);
		int el = equalArea[0];
		int er = equalArea[1];
		Stack<Op> stack = new Stack<>();
		stack.push(new Op(0, el - 1));
		stack.push(new Op(er + 1, N - 1));
		while (!stack.isEmpty()) {
			Op op = stack.pop(); // op.l ... op.r
			if (op.l < op.r) {
				swap(arr, op.l + (int) (Math.random() * (op.r - op.l + 1)), op.r);
				equalArea = netherlandsFlag(arr, op.l, op.r);
				el = equalArea[0];
				er = equalArea[1];
				stack.push(new Op(op.l, el - 1));
				stack.push(new Op(er + 1, op.r));
			}
		}
	}

	// 快排3.0 非递归版本 用队列来执行
	public static void quickSort3(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		int N = arr.length;
		swap(arr, (int) (Math.random() * N), N - 1);
		// 记录整个等于区，只把两侧仍无序的区间交给后续排序。
		int[] equalArea = netherlandsFlag(arr, 0, N - 1);
		int el = equalArea[0];
		int er = equalArea[1];
		Queue<Op> queue = new LinkedList<>();
		queue.offer(new Op(0, el - 1));
		queue.offer(new Op(er + 1, N - 1));
		while (!queue.isEmpty()) {
			Op op = queue.poll();
			if (op.l < op.r) {
				swap(arr, op.l + (int) (Math.random() * (op.r - op.l + 1)), op.r);
				equalArea = netherlandsFlag(arr, op.l, op.r);
				el = equalArea[0];
				er = equalArea[1];
				queue.offer(new Op(op.l, el - 1));
				queue.offer(new Op(er + 1, op.r));
			}
		}
	}

	// 生成随机数组（用于测试）
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// 拷贝数组（用于测试）
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// 对比两个数组（用于测试）
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// 打印数组（用于测试）
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// 跑大样本随机测试（对数器）

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100;
		boolean succeed = true;
		System.out.println("test begin");
		for (int i = 0; i < testTime; i++) {
			int[] arr1 = generateRandomArray(maxSize, maxValue);
			int[] arr2 = copyArray(arr1);
			int[] arr3 = copyArray(arr1);
			quickSort1(arr1);
			quickSort2(arr2);
			quickSort3(arr3);
			if (!isEqual(arr1, arr2) || !isEqual(arr1, arr3)) {
				succeed = false;
				break;
			}
		}
		System.out.println("test end");
		System.out.println("测试" + testTime + "组是否全部通过：" + (succeed ? "是" : "否"));
	}
```

#### 题解

**为什么正确**

四区间划分在每次交换后保持成立，且未检查区必缩短。来自右端的交换值尚未分类，因此必须重新检查。划分结束时等于区已经处于最终位置，递归两侧即可得到整体有序。

**复杂度**

固定基准最坏 O(N²)；随机基准期望 O(N log N)，最坏仍为 O(N²)。递归栈期望 O(log N)、最坏 O(N)；非递归待处理区间空间最坏 O(N)。

**边界与易错点**

大于分支不能 index++。单元素和空区间直接结束。返回的是等于区左右边界，递归区间必须排除整段等于区。

### 5.4 双向链表快速排序

#### 题目

给定双向链表，使用快速排序思想重新连接节点并返回排序后的头节点。

**输入、输出与约束**

输入合法无环双向链表；返回排序后头节点，原节点原地重连。

**函数签名（课程入口）**

```java
public static Node quickSort(Node h);
```

**示例**

```text
输入：双向链表 3↔1↔2
输出：1↔2↔3
```

解释：每条 next 连接都必须存在对应的 last 反向连接。

**出处与版本差异**

- [课程源码：class05/Code04_DoubleLinkedListQuickSort.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class05/Code04_DoubleLinkedListQuickSort.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

在当前链表段随机选一个基准节点，逐个断开原链接并分配到小于、等于、大于三条子链表。递归排序较小和较大子链表，再按“小于→等于→大于”拼接，同时返回新头与新尾。

#### 代码答案

```java
package class05;

import java.util.ArrayList;
import java.util.Comparator;

// 双向链表的随机快速排序
// 课上没有讲，因为这是群里同学问的问题
// 作为补充放在这，有需要的同学可以看看
// 和课上讲的数组的经典快速排序在算法上没有区别
// 但是coding需要更小心
public class Code04_DoubleLinkedListQuickSort {

	public static class Node {
		public int value;
		public Node last;
		public Node next;

		public Node(int v) {
			value = v;
		}
	}

	public static Node quickSort(Node h) {
		if (h == null) {
			return null;
		}
		int N = 0;
		Node c = h;
		Node e = null;
		while (c != null) {
			N++;
			e = c;
			c = c.next;
		}
		return process(h, e, N).h;
	}

	public static class HeadTail {
		public Node h;
		public Node t;

		public HeadTail(Node head, Node tail) {
			h = head;
			t = tail;
		}
	}

	// L...R是一个双向链表的头和尾,
	// L的last指针指向null，R的next指针指向null
	// 也就是说L的左边没有，R的右边也没节点
	// 就是一个正常的双向链表，一共有N个节点
	// 将这一段用随机快排的方式排好序
	// 返回排好序之后的双向链表的头和尾(HeadTail)
	public static HeadTail process(Node L, Node R, int N) {
		if (L == null) {
			return null;
		}
		if (L == R) {
			return new HeadTail(L, R);
		}
		// L..R上不只一个节点
		// 随机得到一个随机下标
		int randomIndex = (int) (Math.random() * N);
		// 根据随机下标得到随机节点
		Node randomNode = L;
		while (randomIndex-- != 0) {
			randomNode = randomNode.next;
		}
		// 把随机节点从原来的环境里分离出来
		// 比如 a(L) -> b -> c -> d(R), 如果randomNode = c，那么调整之后
		// a(L) -> b -> d(R), c会被挖出来，randomNode = c
		if (randomNode == L || randomNode == R) {
			if (randomNode == L) {
				L = randomNode.next;
				// 双向链表还要清除原前驱，随后按所属分区尾插。
				L.last = null;
			} else {
				randomNode.last.next = null;
			}
		} else { // randomNode一定是中间的节点
			randomNode.last.next = randomNode.next;
			randomNode.next.last = randomNode.last;
		}
		randomNode.last = null;
		randomNode.next = null;
		Info info = partition(L, randomNode);
		// <randomNode的部分去排序
		HeadTail lht = process(info.lh, info.lt, info.ls);
		// >randomNode的部分去排序
		HeadTail rht = process(info.rh, info.rt, info.rs);
		// 左部分排好序、右部分排好序
		// 把它们串在一起
		if (lht != null) {
			lht.t.next = info.eh;
			info.eh.last = lht.t;
		}
		if (rht != null) {
			info.et.next = rht.h;
			rht.h.last = info.et;
		}
		// 返回排好序之后总的头和总的尾
		Node h = lht != null ? lht.h : info.eh;
		Node t = rht != null ? rht.t : info.et;
		return new HeadTail(h, t);
	}

	public static class Info {
		public Node lh;
		public Node lt;
		public int ls;
		public Node rh;
		public Node rt;
		public int rs;
		public Node eh;
		public Node et;

		public Info(Node lH, Node lT, int lS, Node rH, Node rT, int rS, Node eH, Node eT) {
			lh = lH;
			lt = lT;
			ls = lS;
			rh = rH;
			rt = rT;
			rs = rS;
			eh = eH;
			et = eT;
		}
	}

	// (L....一直到空)，是一个双向链表
	// pivot是一个不在(L....一直到空)的独立节点，它作为划分值
	// 根据荷兰国旗问题的划分方式，把(L....一直到空)划分成:
	// <pivot 、 =pivot 、 >pivot 三个部分，然后把pivot融进=pivot的部分
	// 比如 4(L)->6->7->1->5->0->9->null pivot=5(这个5和链表中的5，是不同的节点)
	// 调整完成后:
	// 4->1->0 小于的部分
	// 5->5 等于的部分
	// 6->7->9 大于的部分
	// 三个部分是断开的
	// 然后返回Info：
	// 小于部分的头、尾、节点个数 : lh,lt,ls
	// 大于部分的头、尾、节点个数 : rh,rt,rs
	// 等于部分的头、尾 : eh,et
	public static Info partition(Node L, Node pivot) {
		Node lh = null;
		Node lt = null;
		int ls = 0;
		Node rh = null;
		Node rt = null;
		int rs = 0;
		Node eh = pivot;
		Node et = pivot;
		Node tmp = null;
		while (L != null) {
			// 先保存未划分后缀，再把当前节点从原链断开。
			tmp = L.next;
			// 清除原后继，避免分区重连后残留跨分区旧链接。
			L.next = null;
			// 双向链表还要清除原前驱，随后按所属分区尾插。
			L.last = null;
			if (L.value < pivot.value) {
				ls++;
				if (lh == null) {
					lh = L;
					lt = L;
				} else {
					lt.next = L;
					L.last = lt;
					lt = L;
				}
			} else if (L.value > pivot.value) {
				rs++;
				if (rh == null) {
					rh = L;
					rt = L;
				} else {
					rt.next = L;
					L.last = rt;
					rt = L;
				}
			} else {
				et.next = L;
				L.last = et;
				et = L;
			}
			L = tmp;
		}
		return new Info(lh, lt, ls, rh, rt, rs, eh, et);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	public static class NodeComp implements Comparator<Node> {

		@Override
		public int compare(Node o1, Node o2) {
			return o1.value - o2.value;
		}

	}

	// 为了测试
	public static Node sort(Node head) {
		if (head == null) {
			return null;
		}
		ArrayList<Node> arr = new ArrayList<>();
		while (head != null) {
			arr.add(head);
			head = head.next;
		}
		arr.sort(new NodeComp());
		Node h = arr.get(0);
		h.last = null;
		Node p = h;
		for (int i = 1; i < arr.size(); i++) {
			Node c = arr.get(i);
			p.next = c;
			c.last = p;
			c.next = null;
			p = c;
		}
		return h;
	}

	// 为了测试
	public static Node generateRandomDoubleLinkedList(int n, int v) {
		if (n == 0) {
			return null;
		}
		Node[] arr = new Node[n];
		for (int i = 0; i < n; i++) {
			arr[i] = new Node((int) (Math.random() * v));
		}
		Node head = arr[0];
		Node pre = head;
		for (int i = 1; i < n; i++) {
			pre.next = arr[i];
			arr[i].last = pre;
			pre = arr[i];
		}
		return head;
	}

	// 为了测试
	public static Node cloneDoubleLinkedList(Node head) {
		if (head == null) {
			return null;
		}
		Node h = new Node(head.value);
		Node p = h;
		head = head.next;
		while (head != null) {
			Node c = new Node(head.value);
			p.next = c;
			c.last = p;
			p = c;
			head = head.next;
		}
		return h;
	}

	// 为了测试
	public static boolean equal(Node h1, Node h2) {
		return doubleLinkedListToString(h1).equals(doubleLinkedListToString(h2));
	}

	// 为了测试
	public static String doubleLinkedListToString(Node head) {
		Node cur = head;
		Node end = null;
		StringBuilder builder = new StringBuilder();
		while (cur != null) {
			builder.append(cur.value + " ");
			end = cur;
			cur = cur.next;
		}
		builder.append("| ");
		while (end != null) {
			builder.append(end.value + " ");
			end = end.last;
		}
		return builder.toString();
	}

	// 为了测试
	public static void main(String[] args) {
		int N = 500;
		int V = 500;
		int testTime = 10000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int size = (int) (Math.random() * N);
			Node head1 = generateRandomDoubleLinkedList(size, V);
			Node head2 = cloneDoubleLinkedList(head1);
			Node sort1 = quickSort(head1);
			Node sort2 = sort(head2);
			if (!equal(sort1, sort2)) {
				System.out.println("出错了!");
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

每个节点恰好被分到一个区域，值的区域次序保证跨区域有序。两侧递归保证区域内部有序，拼接不会改变节点数量。头尾同时返回，使父层能用常数次连接完成合并。

**复杂度**

随机选择下期望 O(N log N)，最坏 O(N²)；递归额外空间期望 O(log N)、最坏 O(N)。

**边界与易错点**

断开 next 前保存后继，拼接时同步设置 last。空子链表不能直接解引用其头尾；只重连节点，不创建重复节点。


<a id="course-06"></a>

## 第 6 课：比较器、堆与堆排序

### 6.1 Java 比较器与优先队列

#### 题目

展示如何为自定义对象定义比较规则，并在排序函数和优先队列中使用该比较器。

**输入、输出与约束**

本节输入学生等自定义对象，观察数组排序与优先队列的取出顺序。

**函数签名（课程入口）**

```java
public int compare(Student o1, Student o2);
public static void printStudents(Student[] students);
public int compare(Integer o1, Integer o2);
public int compare(Integer arg0, Integer arg1);
```

**示例**

```text
输入：按年龄升序排序：A=20、B=18
输出：B、A
```

解释：较年轻者比较结果更小，排在前面。

**出处与版本差异**

- [课程源码：class06/Code01_Comparator.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class06/Code01_Comparator.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

比较器返回负数表示第一个对象应排在前面，零表示同一排序位置，正数表示应排在后面。多字段排序先比较主字段，仅主字段相同才比较次字段；优先队列将比较意义上最小的元素置于堆顶。

#### 代码答案

```java
package class06;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Comparator;
import java.util.TreeMap;

public class Code01_Comparator {

	public static class Student {
		public String name;
		public int id;
		public int age;

		public Student(String name, int id, int age) {
			this.name = name;
			this.id = id;
			this.age = age;
		}
	}

	// 任何比较器：
	// compare方法里，遵循一个统一的规范：
	// 返回负数的时候，认为第一个参数应该排在前面
	// 返回正数的时候，认为第二个参数应该排在前面
	// 返回0的时候，认为无所谓谁放前面
	public static class IdShengAgeJiangOrder implements Comparator<Student> {

		// 根据id从小到大，但是如果id一样，按照年龄从大到小
		@Override
		// 返回值描述两个对象的先后关系；容器不会理解年龄、编号等业务含义。
		public int compare(Student o1, Student o2) {
			return o1.id != o2.id ? (o1.id - o2.id) : (o2.age - o1.age);
		}

	}

	public static class IdAscendingComparator implements Comparator<Student> {

		// 返回负数的时候，第一个参数排在前面
		// 返回正数的时候，第二个参数排在前面
		// 返回0的时候，谁在前面无所谓
		@Override
		// 返回值描述两个对象的先后关系；容器不会理解年龄、编号等业务含义。
		public int compare(Student o1, Student o2) {
			return o1.id - o2.id;
		}

	}

	public static class IdDescendingComparator implements Comparator<Student> {

		@Override
		// 返回值描述两个对象的先后关系；容器不会理解年龄、编号等业务含义。
		public int compare(Student o1, Student o2) {
			return o2.id - o1.id;
		}

	}

	// 先按照id排序，id小的，放前面；
	// id一样，age大的，前面；
	public static class IdInAgeDe implements Comparator<Student> {

		@Override
		// 返回值描述两个对象的先后关系；容器不会理解年龄、编号等业务含义。
		public int compare(Student o1, Student o2) {
			return o1.id != o2.id ? o1.id - o2.id : (o2.age - o1.age);
		}

	}

	public static void printStudents(Student[] students) {
		for (Student student : students) {
			System.out.println("Name : " + student.name + ", Id : " + student.id + ", Age : " + student.age);
		}
	}

	public static void printArray(Integer[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	public static class MyComp implements Comparator<Integer> {

		@Override
		// 返回值描述两个对象的先后关系；容器不会理解年龄、编号等业务含义。
		public int compare(Integer o1, Integer o2) {
			return o2 - o1;
		}

	}

	public static class AComp implements Comparator<Integer> {

		// 如果返回负数，认为第一个参数应该拍在前面
		// 如果返回正数，认为第二个参数应该拍在前面
		// 如果返回0，认为谁放前面都行
		@Override
		// 返回值描述两个对象的先后关系；容器不会理解年龄、编号等业务含义。
		public int compare(Integer arg0, Integer arg1) {

			return arg1 - arg0;

//			return 0;
		}

	}

	public static void main(String[] args) {

		Integer[] arr = { 5, 4, 3, 2, 7, 9, 1, 0 };

		Arrays.sort(arr, new AComp());

		for (int i = 0; i < arr.length; i++) {
			System.out.println(arr[i]);
		}

		System.out.println("===========================");

		Student student1 = new Student("A", 4, 40);
		Student student2 = new Student("B", 4, 21);
		Student student3 = new Student("C", 3, 12);
		Student student4 = new Student("D", 3, 62);
		Student student5 = new Student("E", 3, 42);
		// D E C A B

		Student[] students = new Student[] { student1, student2, student3, student4, student5 };
		System.out.println("第一条打印");

		Arrays.sort(students, new IdShengAgeJiangOrder());
		for (int i = 0; i < students.length; i++) {
			Student s = students[i];
			System.out.println(s.name + "," + s.id + "," + s.age);
		}

		System.out.println("第二条打印");
		ArrayList<Student> studentList = new ArrayList<>();
		studentList.add(student1);
		studentList.add(student2);
		studentList.add(student3);
		studentList.add(student4);
		studentList.add(student5);
		studentList.sort(new IdShengAgeJiangOrder());
		for (int i = 0; i < studentList.size(); i++) {
			Student s = studentList.get(i);
			System.out.println(s.name + "," + s.id + "," + s.age);
		}
		// N * logN
		System.out.println("第三条打印");
		student1 = new Student("A", 4, 40);
		student2 = new Student("B", 4, 21);
		student3 = new Student("C", 4, 12);
		student4 = new Student("D", 4, 62);
		student5 = new Student("E", 4, 42);
		TreeMap<Student, String> treeMap = new TreeMap<>((a, b) -> (a.id - b.id));
		treeMap.put(student1, "我是学生1，我的名字叫A");
		treeMap.put(student2, "我是学生2，我的名字叫B");
		treeMap.put(student3, "我是学生3，我的名字叫C");
		treeMap.put(student4, "我是学生4，我的名字叫D");
		treeMap.put(student5, "我是学生5，我的名字叫E");
		for (Student s : treeMap.keySet()) {
			System.out.println(s.name + "," + s.id + "," + s.age);
		}

	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：不要直接 a-b 比较任意 int，减法可能溢出；应使用 Integer.compare。加入有序容器后改变排序字段，需要移除重插或显式调整。

本条未附独立随机对数器。

#### 题解

**为什么正确**

排序和堆都只依赖同一比较关系。只要关系满足反对称与传递性，同一组对象才能形成一致顺序；按字段依次比较相当于建立字典序。

**复杂度**

单次定长字段比较 O(1)，数组排序 O(N log N)，优先队列插入、弹出 O(log N)。

**边界与易错点**

不要直接 a-b 比较任意 int，减法可能溢出；应使用 Integer.compare。加入有序容器后改变排序字段，需要移除重插或显式调整。

### 6.2 手写大根堆

#### 题目

实现固定容量的大根堆，支持插入元素和弹出当前最大值。

**输入、输出与约束**

构造正容量 C；支持 int 入堆和弹出最大值，满堆入队或空堆出队抛异常。

**函数签名（课程入口）**

```java
public boolean isEmpty();
public boolean isFull();
public void push(int value);
public int pop();
public int compare(Integer o1, Integer o2);
```

**示例**

```text
输入：push(3)，push(1)，push(5)，pop()
输出：5
```

解释：大根堆始终把当前最大元素放在根位置。

**出处与版本差异**

- [课程源码：class06/Code02_Heap.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class06/Code02_Heap.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

用数组表示完全二叉树，节点 i 的父亲为 (i-1)/2，孩子为 2i+1、2i+2。大根堆要求父值不小于孩子。插入末尾后向上交换；弹出堆顶时用末尾补位，再与较大孩子交换下沉。

#### 代码答案

```java
package class06;

import java.util.Comparator;
import java.util.PriorityQueue;

public class Code02_Heap {

	public static class MyMaxHeap {
		private int[] heap;
		private final int limit;
		private int heapSize;

		public MyMaxHeap(int limit) {
			heap = new int[limit];
			this.limit = limit;
			heapSize = 0;
		}

		public boolean isEmpty() {
			return heapSize == 0;
		}

		public boolean isFull() {
			return heapSize == limit;
		}

		public void push(int value) {
			if (heapSize == limit) {
				throw new RuntimeException("heap is full");
			}
			heap[heapSize] = value;
			// value heapSize
			// 新元素放在数组末尾，上浮修复通向根的路径。
			heapInsert(heap, heapSize++);
		}

		// 用户此时，让你返回最大值，并且在大根堆中，把最大值删掉
		// 剩下的数，依然保持大根堆组织
		public int pop() {
			int ans = heap[0];
			swap(heap, 0, --heapSize);
			heapify(heap, 0, heapSize);
			return ans;
		}

		// 新加进来的数，现在停在了index位置，请依次往上移动，
		// 移动到0位置，或者干不掉自己的父亲了，停！
		private void heapInsert(int[] arr, int index) {
			// [index] [index-1]/2
			// index == 0
			while (arr[index] > arr[(index - 1) / 2]) {
				swap(arr, index, (index - 1) / 2);
				index = (index - 1) / 2;
			}
		}

		// 从index位置，往下看，不断的下沉
		// 停：较大的孩子都不再比index位置的数大；已经没孩子了
		private void heapify(int[] arr, int index, int heapSize) {
			// 完全二叉树的左孩子下标，右孩子紧随其后。
			int left = index * 2 + 1;
			while (left < heapSize) { // 如果有左孩子，有没有右孩子，可能有可能没有！
				// 把较大孩子的下标，给largest
				// 先选两个孩子中较大的一个，才能同时满足两条父子堆序。
				int largest = left + 1 < heapSize && arr[left + 1] > arr[left] ? left + 1 : left;
				largest = arr[largest] > arr[index] ? largest : index;
				if (largest == index) {
					break;
				}
				// index和较大孩子，要互换
				swap(arr, largest, index);
				index = largest;
				left = index * 2 + 1;
			}
		}

		private void swap(int[] arr, int i, int j) {
			int tmp = arr[i];
			arr[i] = arr[j];
			arr[j] = tmp;
		}

	}

	public static class RightMaxHeap {
		private int[] arr;
		private final int limit;
		private int size;

		public RightMaxHeap(int limit) {
			arr = new int[limit];
			this.limit = limit;
			size = 0;
		}

		public boolean isEmpty() {
			return size == 0;
		}

		public boolean isFull() {
			return size == limit;
		}

		public void push(int value) {
			if (size == limit) {
				throw new RuntimeException("heap is full");
			}
			arr[size++] = value;
		}

		public int pop() {
			int maxIndex = 0;
			for (int i = 1; i < size; i++) {
				if (arr[i] > arr[maxIndex]) {
					maxIndex = i;
				}
			}
			int ans = arr[maxIndex];
			arr[maxIndex] = arr[--size];
			return ans;
		}

	}

	public static class MyComparator implements Comparator<Integer> {

		@Override
		public int compare(Integer o1, Integer o2) {
			return o2 - o1;
		}

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {

		// 小根堆
		PriorityQueue<Integer> heap = new PriorityQueue<>(new MyComparator());
		heap.add(5);
		heap.add(5);
		heap.add(5);
		heap.add(3);
		// 5 , 3
		System.out.println(heap.peek());
		heap.add(7);
		heap.add(0);
		heap.add(7);
		heap.add(0);
		heap.add(7);
		heap.add(0);
		System.out.println(heap.peek());
		while (!heap.isEmpty()) {
			System.out.println(heap.poll());
		}

		int value = 1000;
		int limit = 100;
		int testTimes = 1000000;
		for (int i = 0; i < testTimes; i++) {
			int curLimit = (int) (Math.random() * limit) + 1;
			MyMaxHeap my = new MyMaxHeap(curLimit);
			RightMaxHeap test = new RightMaxHeap(curLimit);
			int curOpTimes = (int) (Math.random() * limit);
			for (int j = 0; j < curOpTimes; j++) {
				if (my.isEmpty() != test.isEmpty()) {
					System.out.println("Oops!");
				}
				if (my.isFull() != test.isFull()) {
					System.out.println("Oops!");
				}
				if (my.isEmpty()) {
					int curValue = (int) (Math.random() * value);
					my.push(curValue);
					test.push(curValue);
				} else if (my.isFull()) {
					if (my.pop() != test.pop()) {
						System.out.println("Oops!");
					}
				} else {
					if (Math.random() < 0.5) {
						int curValue = (int) (Math.random() * value);
						my.push(curValue);
						test.push(curValue);
					} else {
						if (my.pop() != test.pop()) {
							System.out.println("Oops!");
						}
					}
				}
			}
		}
		System.out.println("finish!");

	}
```

#### 题解

**为什么正确**

插入只可能破坏新节点到根这一条路径上的堆序，上浮逐层修复。弹顶后只可能新根比孩子小，每次与较大孩子交换既修复父节点，也把唯一可能的违例下移。

**复杂度**

push、pop 为 O(log N)，查看满空 O(1)，数组空间 O(C)，C 为容量。

**边界与易错点**

选孩子必须选较大者。只在 heapSize 范围内判断孩子是否存在；物理数组末尾可能残留已经弹出的旧值。

### 6.3 堆排序

#### 题目

给定一个整数数组 `arr`，请使用本节指定的排序算法将数组原地调整为非递减顺序。

**输入、输出与约束**

输入 int 数组，原地升序排序，允许负数与重复值。

**函数签名（课程入口）**

```java
public static void heapSort(int[] arr);
```

**示例**

```text
输入：arr=[4,1,3,2]
输出：arr=[1,2,3,4]
```

解释：每次把当前最大值交换到尚未排序部分末尾。

**出处与版本差异**

- [课程源码：class06/Code03_HeapSort.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class06/Code03_HeapSort.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先把数组建成大根堆。每轮把堆顶最大值与堆的最后一个元素交换，使最大值固定在有序后缀；缩小堆大小后让新根下沉，重复直到堆只剩一个元素。

#### 代码答案

```java
package class06;

import java.util.Arrays;
import java.util.PriorityQueue;

public class Code03_HeapSort {

	// 堆排序额外空间复杂度O(1)
	public static void heapSort(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		// O(N*logN)
//		for (int i = 0; i < arr.length; i++) { // O(N)
//			heapInsert(arr, i); // O(logN)
//		}
		// O(N)
		for (int i = arr.length - 1; i >= 0; i--) {
			heapify(arr, i, arr.length);
		}
		int heapSize = arr.length;
		// 将当前最大值放到有序后缀，并从堆中排除该位置。
		swap(arr, 0, --heapSize);
		// O(N*logN)
		while (heapSize > 0) { // O(N)
			// 根被末尾元素替换，只需沿根到叶路径恢复堆序。
			heapify(arr, 0, heapSize); // O(logN)
			// 将当前最大值放到有序后缀，并从堆中排除该位置。
			swap(arr, 0, --heapSize); // O(1)
		}
	}

	// arr[index]刚来的数，往上
	public static void heapInsert(int[] arr, int index) {
		while (arr[index] > arr[(index - 1) / 2]) {
			swap(arr, index, (index - 1) / 2);
			index = (index - 1) / 2;
		}
	}

	// arr[index]位置的数，能否往下移动
	public static void heapify(int[] arr, int index, int heapSize) {
		int left = index * 2 + 1; // 左孩子的下标
		while (left < heapSize) { // 下方还有孩子的时候
			// 两个孩子中，谁的值大，把下标给largest
			// 1）只有左孩子，left -> largest
			// 2) 同时有左孩子和右孩子，右孩子的值<= 左孩子的值，left -> largest
			// 3) 同时有左孩子和右孩子并且右孩子的值> 左孩子的值， right -> largest
			int largest = left + 1 < heapSize && arr[left + 1] > arr[left] ? left + 1 : left;
			// 父和较大的孩子之间，谁的值大，把下标给largest
			largest = arr[largest] > arr[index] ? largest : index;
			if (largest == index) {
				break;
			}
			swap(arr, largest, index);
			index = largest;
			left = index * 2 + 1;
		}
	}

	public static void swap(int[] arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static void comparator(int[] arr) {
		Arrays.sort(arr);
	}

	// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {

		// 默认小根堆
		PriorityQueue<Integer> heap = new PriorityQueue<>();
		heap.add(6);
		heap.add(8);
		heap.add(0);
		heap.add(2);
		heap.add(9);
		heap.add(1);

		while (!heap.isEmpty()) {
			System.out.println(heap.poll());
		}

		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100;
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int[] arr1 = generateRandomArray(maxSize, maxValue);
			int[] arr2 = copyArray(arr1);
			heapSort(arr1);
			comparator(arr2);
			if (!isEqual(arr1, arr2)) {
				succeed = false;
				break;
			}
		}
		System.out.println(succeed ? "Nice!" : "Fucking fucked!");

		int[] arr = generateRandomArray(maxSize, maxValue);
		printArray(arr);
		heapSort(arr);
		printArray(arr);
	}
```

#### 题解

**为什么正确**

建堆保证每轮取出的根是未排序部分最大值。将它放在未排序部分末尾后，其最终位置确定；下沉恢复剩余元素的堆序，从而可以继续取下一个最大值。

**复杂度**

自底向上建堆 O(N)，逐个插入建堆 O(N log N)；完整排序均 O(N log N)，额外空间 O(1)。

**边界与易错点**

缩小 heapSize 后再下沉，不能把已就位的后缀当作孩子。堆排序不稳定。建堆 O(N) 来自各高度节点数的加权和，不是每节点都要下沉 log N 层。

### 6.4 排序几乎有序的数组

#### 题目

给定一个几乎有序的数组，每个元素距离其最终排序位置不超过 `k`，请高效完成排序。

**输入、输出与约束**

输入数组和 k≥0，保证存在升序排列使每个元素移动距离≤k；原地输出升序结果。

**函数签名（课程入口）**

```java
public static void sortedArrDistanceLessK(int[] arr, int k);
```

**示例**

```text
输入：arr=[2,1,3], k=1
输出：[1,2,3]
```

解释：位置 0 的最小值可从前两个元素中选出。

**出处与版本差异**

- [课程源码：class06/Code04_SortArrayDistanceLessK.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class06/Code04_SortArrayDistanceLessK.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

最终位置 0 的元素最多从原下标 k 移来，因此只需在最前面的 k+1 个候选中取最小值。用小根堆保存这些候选，写出一个最小值后，再加入一个后续元素。

#### 代码答案

```java
package class06;

import java.util.Arrays;
import java.util.PriorityQueue;

public class Code04_SortArrayDistanceLessK {

	public static void sortedArrDistanceLessK(int[] arr, int k) {
		if (k == 0) {
			return;
		}
		// 默认小根堆
		PriorityQueue<Integer> heap = new PriorityQueue<>();
		int index = 0;
		// 0...K-1
		for (; index <= Math.min(arr.length - 1, k - 1); index++) {
			// 把即将进入可选范围的新元素放入小根堆。
			heap.add(arr[index]);
		}
		int i = 0;
		for (; index < arr.length; i++, index++) {
			// 把即将进入可选范围的新元素放入小根堆。
			heap.add(arr[index]);
			// 候选已经覆盖当前位置所有可能来源，取最小者落位。
			arr[i] = heap.poll();
		}
		while (!heap.isEmpty()) {
			arr[i++] = heap.poll();
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static void comparator(int[] arr, int k) {
		Arrays.sort(arr);
	}

	// for test
	public static int[] randomArrayNoMoveMoreK(int maxSize, int maxValue, int K) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		// 先排个序
		Arrays.sort(arr);
		// 然后开始随意交换，但是保证每个数距离不超过K
		// swap[i] == true, 表示i位置已经参与过交换
		// swap[i] == false, 表示i位置没有参与过交换
		boolean[] isSwap = new boolean[arr.length];
		for (int i = 0; i < arr.length; i++) {
			int j = Math.min(i + (int) (Math.random() * (K + 1)), arr.length - 1);
			if (!isSwap[i] && !isSwap[j]) {
				isSwap[i] = true;
				isSwap[j] = true;
				int tmp = arr[i];
				arr[i] = arr[j];
				arr[j] = tmp;
			}
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {
		System.out.println("test begin");
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100;
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int k = (int) (Math.random() * maxSize) + 1;
			int[] arr = randomArrayNoMoveMoreK(maxSize, maxValue, k);
			int[] arr1 = copyArray(arr);
			int[] arr2 = copyArray(arr);
			sortedArrDistanceLessK(arr1, k);
			comparator(arr2, k);
			if (!isEqual(arr1, arr2)) {
				succeed = false;
				System.out.println("K : " + k);
				printArray(arr);
				printArray(arr1);
				printArray(arr2);
				break;
			}
		}
		System.out.println(succeed ? "Nice!" : "Fucking fucked!");
	}
```

#### 题解

**为什么正确**

在写出下标 i 时，所有可能应位于 i 的元素都已进入堆；更后面的元素距离 i 超过 k，不可能属于当前位置。故弹出堆最小值就是全局尚未输出的最小值。

**复杂度**

时间 O(N log(min(N,k+1)))，额外空间 O(min(N,k+1))；k=0 直接返回。

**边界与易错点**

候选窗口是 k+1 个元素。k 大于数组长度时要截断。若输入不满足位移限制，小堆不能保证最终排序正确。


<a id="course-07"></a>

## 第 7 课：加强堆及其应用

### 7.1 线段最大重合数量

#### 题目

给定若干开区间线段，返回同一位置最多被多少条线段覆盖。

**输入、输出与约束**

输入若干 start<end 的整数线段；返回正长度交集上最多重合条数。

**函数签名（课程入口）**

```java
public static int maxCover1(int[][] lines);
public static int maxCover2(int[][] m);
public int compare(Line o1, Line o2);
public static int maxCover3(int[][] m);
```

**示例**

```text
输入：lines=[[1,3],[2,4],[3,5]]
输出：2
```

解释：[1,3] 与 [3,5] 只接触端点，不能三条同时重合。

**出处与版本差异**

- [课程源码：class07/Code01_CoverMax.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class07/Code01_CoverMax.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

按线段起点升序处理，用小根堆维护仍与当前起点有严格重叠的线段终点。加入当前线段前，弹出所有终点≤当前起点的线段；加入后堆大小就是该位置右侧微小区间的重合数。

#### 代码答案

```java
package class07;

import java.util.Arrays;
import java.util.Comparator;
import java.util.PriorityQueue;

public class Code01_CoverMax {

	public static int maxCover1(int[][] lines) {
		int min = Integer.MAX_VALUE;
		int max = Integer.MIN_VALUE;
		for (int i = 0; i < lines.length; i++) {
			min = Math.min(min, lines[i][0]);
			max = Math.max(max, lines[i][1]);
		}
		int cover = 0;
		for (double p = min + 0.5; p < max; p += 1) {
			int cur = 0;
			for (int i = 0; i < lines.length; i++) {
				if (lines[i][0] < p && lines[i][1] > p) {
					cur++;
				}
			}
			cover = Math.max(cover, cur);
		}
		return cover;
	}

	public static int maxCover2(int[][] m) {
		Line[] lines = new Line[m.length];
		for (int i = 0; i < m.length; i++) {
			lines[i] = new Line(m[i][0], m[i][1]);
		}
		Arrays.sort(lines, new StartComparator());
		// 小根堆，每一条线段的结尾数值，使用默认的
		PriorityQueue<Integer> heap = new PriorityQueue<>();
		int max = 0;
		for (int i = 0; i < lines.length; i++) {
			// lines[i] -> cur 在黑盒中，把<=cur.start 东西都弹出
			while (!heap.isEmpty() && heap.peek() <= lines[i].start) {
				heap.poll();
			}
			heap.add(lines[i].end);
			// 此刻堆中所有线段共享当前起点右侧的一段区间。
			max = Math.max(max, heap.size());
		}
		return max;
	}

	public static class Line {
		public int start;
		public int end;

		public Line(int s, int e) {
			start = s;
			end = e;
		}
	}

	public static class EndComparator implements Comparator<Line> {

		@Override
		public int compare(Line o1, Line o2) {
			return o1.end - o2.end;
		}

	}

	// 和maxCover2过程是一样的
	// 只是代码更短
	// 不使用类定义的写法
	public static int maxCover3(int[][] m) {
		// m是二维数组，可以认为m内部是一个一个的一维数组
		// 每一个一维数组就是一个对象，也就是线段
		// 如下的code，就是根据每一个线段的开始位置排序
		// 比如, m = { {5,7}, {1,4}, {2,6} } 跑完如下的code之后变成：{ {1,4}, {2,6}, {5,7} }
		Arrays.sort(m, (a, b) -> (a[0] - b[0]));
		// 准备好小根堆，和课堂的说法一样
		PriorityQueue<Integer> heap = new PriorityQueue<>();
		int max = 0;
		for (int[] line : m) {
			while (!heap.isEmpty() && heap.peek() <= line[0]) {
				heap.poll();
			}
			heap.add(line[1]);
			// 此刻堆中所有线段共享当前起点右侧的一段区间。
			max = Math.max(max, heap.size());
		}
		return max;
	}

	public static class StartComparator implements Comparator<Line> {

		@Override
		public int compare(Line o1, Line o2) {
			return o1.start - o2.start;
		}

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[][] generateLines(int N, int L, int R) {
		int size = (int) (Math.random() * N) + 1;
		int[][] ans = new int[size][2];
		for (int i = 0; i < size; i++) {
			int a = L + (int) (Math.random() * (R - L + 1));
			int b = L + (int) (Math.random() * (R - L + 1));
			if (a == b) {
				b = a + 1;
			}
			ans[i][0] = Math.min(a, b);
			ans[i][1] = Math.max(a, b);
		}
		return ans;
	}

public static void main(String[] args) {

		Line l1 = new Line(4, 9);
		Line l2 = new Line(1, 4);
		Line l3 = new Line(7, 15);
		Line l4 = new Line(2, 4);
		Line l5 = new Line(4, 6);
		Line l6 = new Line(3, 7);

		// 底层堆结构，heap
		PriorityQueue<Line> heap = new PriorityQueue<>(new StartComparator());
		heap.add(l1);
		heap.add(l2);
		heap.add(l3);
		heap.add(l4);
		heap.add(l5);
		heap.add(l6);

		while (!heap.isEmpty()) {
			Line cur = heap.poll();
			System.out.println(cur.start + "," + cur.end);
		}

		System.out.println("test begin");
		int N = 100;
		int L = 0;
		int R = 200;
		int testTimes = 200000;
		for (int i = 0; i < testTimes; i++) {
			int[][] lines = generateLines(N, L, R);
			int ans1 = maxCover1(lines);
			int ans2 = maxCover2(lines);
			int ans3 = maxCover3(lines);
			if (ans1 != ans2 || ans1 != ans3) {
				System.out.println("Oops!");
			}
		}
		System.out.println("test end");
	}
```

#### 题解

**为什么正确**

一个最大重叠区间的左端可以取某条线段的起点。扫描到该起点时，之前开始且尚未结束的线段恰好保留在堆中，当前线段加入后便统计到这组重合。

**复杂度**

排序与堆操作共 O(N log N)，额外空间 O(N)。

**边界与易错点**

仅端点相接不算有正长度重叠，因此要弹出 end≤start。整数端点的暴力对照可枚举半整数位置，但不适用于任意实数端点。

### 7.2 实时获奖名单系统

#### 题目

给定用户购买或退货事件流以及获奖区容量 `K`，返回每次事件发生后的获奖用户集合。

**输入、输出与约束**

输入等长用户编号和买入/退货布尔事件数组，以及 K≥1；返回每次事件后的获奖用户集合，集合内输出顺序不作排名承诺。

**函数签名（课程入口）**

```java
public int compare(Customer o1, Customer o2);
public void operate(int time, int id, boolean buyOrRefund);
public List<Integer> getDaddies();
public static List<List<Integer>> topK(int[] arr, boolean[] op, int k);
public static List<List<Integer>> compare(int[] arr, boolean[] op, int k);
public static void move(ArrayList<Customer> cands, ArrayList<Customer> daddy, int k, int time);
```

**示例**

```text
输入：K=1；用户 1 买入，用户 2 买入，用户 2 再买入
输出：获奖集合依次为 {1}、{1}、{2}
```

解释：购买数相同不换人，用户 2 达到两次购买后才超过用户 1。

**出处与版本差异**

- [课程源码：class07/Code02_EveryStepShowBoss.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class07/Code02_EveryStepShowBoss.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

维护候选区和获奖区两个加强堆。候选区堆顶是购买数最多、进入时间最早者；获奖区堆顶是购买数最少、进入时间最早者。事件更新用户购买数后调整堆；获奖区不足 K 时补人，候选最佳严格多于获奖最差时交换。

#### 代码答案

```java
package class07;

import java.util.ArrayList;
import java.util.Comparator;
import java.util.HashMap;
import java.util.List;

public class Code02_EveryStepShowBoss {

	public static class Customer {
		public int id;
		public int buy;
		public int enterTime;

		public Customer(int v, int b, int o) {
			id = v;
			buy = b;
			enterTime = 0;
		}
	}

	public static class CandidateComparator implements Comparator<Customer> {

		@Override
		public int compare(Customer o1, Customer o2) {
			return o1.buy != o2.buy ? (o2.buy - o1.buy) : (o1.enterTime - o2.enterTime);
		}

	}

	public static class DaddyComparator implements Comparator<Customer> {

		@Override
		public int compare(Customer o1, Customer o2) {
			return o1.buy != o2.buy ? (o1.buy - o2.buy) : (o1.enterTime - o2.enterTime);
		}

	}

	public static class WhosYourDaddy {
		private HashMap<Integer, Customer> customers;
		private HeapGreater<Customer> candHeap;
		private HeapGreater<Customer> daddyHeap;
		private final int daddyLimit;

		public WhosYourDaddy(int limit) {
			customers = new HashMap<Integer, Customer>();
			candHeap = new HeapGreater<>(new CandidateComparator());
			daddyHeap = new HeapGreater<>(new DaddyComparator());
			daddyLimit = limit;
		}

		// 当前处理i号事件，arr[i] -> id,  buyOrRefund
		public void operate(int time, int id, boolean buyOrRefund) {
			// 不存在购买记录的用户退货无效，不能产生负购买数。
			if (!buyOrRefund && !customers.containsKey(id)) {
				return;
			}
			if (!customers.containsKey(id)) {
				customers.put(id, new Customer(id, 0, 0));
			}
			Customer c = customers.get(id);
			if (buyOrRefund) {
				c.buy++;
			} else {
				c.buy--;
			}
			if (c.buy == 0) {
				customers.remove(id);
			}
			if (!candHeap.contains(c) && !daddyHeap.contains(c)) {
				if (daddyHeap.size() < daddyLimit) {
					// 首次入区或发生换区时，以当前事件时间建立区内先后顺序。
					c.enterTime = time;
					daddyHeap.push(c);
				} else {
					// 首次入区或发生换区时，以当前事件时间建立区内先后顺序。
					c.enterTime = time;
					candHeap.push(c);
				}
			} else if (candHeap.contains(c)) {
				if (c.buy == 0) {
					candHeap.remove(c);
				} else {
					candHeap.resign(c);
				}
			} else {
				if (c.buy == 0) {
					daddyHeap.remove(c);
				} else {
					daddyHeap.resign(c);
				}
			}
			daddyMove(time);
		}

		public List<Integer> getDaddies() {
			List<Customer> customers = daddyHeap.getAllElements();
			List<Integer> ans = new ArrayList<>();
			for (Customer c : customers) {
				ans.add(c.id);
			}
			return ans;
		}

		private void daddyMove(int time) {
			if (candHeap.isEmpty()) {
				return;
			}
			if (daddyHeap.size() < daddyLimit) {
				Customer p = candHeap.pop();
				p.enterTime = time;
				daddyHeap.push(p);
			} else {
				if (candHeap.peek().buy > daddyHeap.peek().buy) {
					Customer oldDaddy = daddyHeap.pop();
					Customer newDaddy = candHeap.pop();
					oldDaddy.enterTime = time;
					newDaddy.enterTime = time;
					daddyHeap.push(newDaddy);
					candHeap.push(oldDaddy);
				}
			}
		}

	}

	public static List<List<Integer>> topK(int[] arr, boolean[] op, int k) {
		List<List<Integer>> ans = new ArrayList<>();
		WhosYourDaddy whoDaddies = new WhosYourDaddy(k);
		for (int i = 0; i < arr.length; i++) {
			whoDaddies.operate(i, arr[i], op[i]);
			ans.add(whoDaddies.getDaddies());
		}
		return ans;
	}

	// 干完所有的事，模拟，不优化
	public static List<List<Integer>> compare(int[] arr, boolean[] op, int k) {
		HashMap<Integer, Customer> map = new HashMap<>();
		ArrayList<Customer> cands = new ArrayList<>();
		ArrayList<Customer> daddy = new ArrayList<>();
		List<List<Integer>> ans = new ArrayList<>();
		for (int i = 0; i < arr.length; i++) {
			int id = arr[i];
			boolean buyOrRefund = op[i];
			if (!buyOrRefund && !map.containsKey(id)) {
				ans.add(getCurAns(daddy));
				continue;
			}
			// 没有发生：用户购买数为0并且又退货了
			// 用户之前购买数是0，此时买货事件
			// 用户之前购买数>0， 此时买货
			// 用户之前购买数>0, 此时退货
			if (!map.containsKey(id)) {
				map.put(id, new Customer(id, 0, 0));
			}
			// 买、卖
			Customer c = map.get(id);
			if (buyOrRefund) {
				c.buy++;
			} else {
				c.buy--;
			}
			if (c.buy == 0) {
				map.remove(id);
			}
			// c
			// 下面做
			if (!cands.contains(c) && !daddy.contains(c)) {
				if (daddy.size() < k) {
					c.enterTime = i;
					daddy.add(c);
				} else {
					c.enterTime = i;
					cands.add(c);
				}
			}
			cleanZeroBuy(cands);
			cleanZeroBuy(daddy);
			cands.sort(new CandidateComparator());
			daddy.sort(new DaddyComparator());
			move(cands, daddy, k, i);
			ans.add(getCurAns(daddy));
		}
		return ans;
	}

	public static void move(ArrayList<Customer> cands, ArrayList<Customer> daddy, int k, int time) {
		if (cands.isEmpty()) {
			return;
		}
		// 候选区不为空
		if (daddy.size() < k) {
			Customer c = cands.get(0);
			// 首次入区或发生换区时，以当前事件时间建立区内先后顺序。
			c.enterTime = time;
			daddy.add(c);
			cands.remove(0);
		} else { // 等奖区满了，候选区有东西
			if (cands.get(0).buy > daddy.get(0).buy) {
				Customer oldDaddy = daddy.get(0);
				daddy.remove(0);
				Customer newDaddy = cands.get(0);
				cands.remove(0);
				newDaddy.enterTime = time;
				oldDaddy.enterTime = time;
				daddy.add(newDaddy);
				cands.add(oldDaddy);
			}
		}
	}

	public static void cleanZeroBuy(ArrayList<Customer> arr) {
		List<Customer> noZero = new ArrayList<Customer>();
		for (Customer c : arr) {
			if (c.buy != 0) {
				noZero.add(c);
			}
		}
		arr.clear();
		for (Customer c : noZero) {
			arr.add(c);
		}
	}

	public static List<Integer> getCurAns(ArrayList<Customer> daddy) {
		List<Integer> ans = new ArrayList<>();
		for (Customer c : daddy) {
			ans.add(c.id);
		}
		return ans;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	public static class Data {
		public int[] arr;
		public boolean[] op;

		public Data(int[] a, boolean[] o) {
			arr = a;
			op = o;
		}
	}

	// 为了测试
	public static Data randomData(int maxValue, int maxLen) {
		int len = (int) (Math.random() * maxLen) + 1;
		int[] arr = new int[len];
		boolean[] op = new boolean[len];
		for (int i = 0; i < len; i++) {
			arr[i] = (int) (Math.random() * maxValue);
			op[i] = Math.random() < 0.5 ? true : false;
		}
		return new Data(arr, op);
	}

	// 为了测试
	public static boolean sameAnswer(List<List<Integer>> ans1, List<List<Integer>> ans2) {
		if (ans1.size() != ans2.size()) {
			return false;
		}
		for (int i = 0; i < ans1.size(); i++) {
			List<Integer> cur1 = ans1.get(i);
			List<Integer> cur2 = ans2.get(i);
			if (cur1.size() != cur2.size()) {
				return false;
			}
			cur1.sort((a, b) -> a - b);
			cur2.sort((a, b) -> a - b);
			for (int j = 0; j < cur1.size(); j++) {
				if (!cur1.get(j).equals(cur2.get(j))) {
					return false;
				}
			}
		}
		return true;
	}

public static void main(String[] args) {
		int maxValue = 10;
		int maxLen = 100;
		int maxK = 6;
		int testTimes = 100000;
		System.out.println("测试开始");
		for (int i = 0; i < testTimes; i++) {
			Data testData = randomData(maxValue, maxLen);
			int k = (int) (Math.random() * maxK) + 1;
			int[] arr = testData.arr;
			boolean[] op = testData.op;
			List<List<Integer>> ans1 = topK(arr, op, k);
			List<List<Integer>> ans2 = compare(arr, op, k);
			if (!sameAnswer(ans1, ans2)) {
				for (int j = 0; j < arr.length; j++) {
					System.out.println(arr[j] + " , " + op[j]);
				}
				System.out.println(k);
				System.out.println(ans1);
				System.out.println(ans2);
				System.out.println("出错了！");
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

每个活跃用户只属于一个区，且获奖区始终保留规则允许的前 K 人。一次事件只改变一个用户的购买数，修复该用户堆序并比较两个边界，即可恢复跨区优先关系。

**复杂度**

N 个事件、U 个活跃用户时，维护约 O(N log U)；输出每轮最多 K 个用户另需 O(NK)。空间 O(U)，不含历史答案。

**边界与易错点**

不存在的用户退货应忽略。购买数降为零时移除用户；换区时重置进入时间。两个区购买数相同不交换，区内时间仅用于各自排序。

### 7.3 加强堆的操作演示

#### 题目

构造若干对象并依次演示加强堆的插入、删除、重新调整、判空和批量取出操作。

**输入、输出与约束**

本条演示如何调用同章HeapGreater；正常代码中的main即为完整操作示例，需同时编译HeapGreater.java。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：按数值小根堆加入 3、1、2；删除对象 1；弹出
输出：2
```

解释：反向索引定位 1，补洞并调整后堆顶为 2。

**出处与版本差异**

- [课程源码：class07/Code03_ShowHeapGreater.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class07/Code03_ShowHeapGreater.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

第一段把Student对象放入按年龄排序的加强堆，修改某对象年龄后调用resign恢复位置。第二段把数组下标放入堆，比较器读取arr[i]决定优先级；重复值靠不同下标区分，修改arr[1]后调用resign(1)。

#### 代码答案

```java
package class07;

// 这个代码课上没有讲，但是非常容易理解
// 一定注意看main函数及其注释，用代码展示加强堆的用法
// 展示了加强堆怎么支持非基础类型和基础类型
public class Code03_ShowHeapGreater {

	// 看这个测试！
	// 每一句都看懂
	public static void main(String[] args) {
		// 展示非基础类型的用法
		Student s1 = new Student(17, "A同学");
		Student s2 = new Student(10, "B同学");
		Student s3 = new Student(29, "C同学");
		Student s4 = new Student(33, "D同学");
		Student s5 = new Student(54, "E同学");
		Student s6 = new Student(93, "F同学");
		// 生成一个加强堆
		// 排序策略是年龄小的放在堆顶，年龄小根堆
		HeapGreater<Student> heap1 = new HeapGreater<>((a, b) -> a.age - b.age);
		// 把所有学生加入堆
		heap1.push(s1);
		heap1.push(s2);
		heap1.push(s3);
		heap1.push(s4);
		heap1.push(s5);
		heap1.push(s6);
		// 加入之后
		// 可以把某个同学的年龄改了
		// 比如把s5，也就是E同学
		// 年龄从54改成了4
		s5.age = 4;
		// 此时堆被破坏了，因为你擅自改了一个同学的年龄
		// 只需要调用resign方法，就能让堆恢复成年龄小根堆
		// 而且复杂度是O(log N)，很快的
		// 系统提供的堆做不到的，加强堆可以
		// s5身份未变，但年龄改变了排序优先级，显式修复它在堆中的位置。
		heap1.resign(s5);
		// 依次弹出所有学生
		// 会发现从年龄小到年龄大依次弹出
		// 说明堆是正确的
		while (!heap1.isEmpty()) {
			Student cur = heap1.pop();
			System.out.println("年龄 : " + cur.age + " , 名字 : " + cur.name);
		}

		System.out.println("======================");

		// 现在展示非基础类型的加强堆用法
		int[] arr = { 3, 3, 2, 5, 3 };
		// arr[0] == 3
		// arr[1] == 3
		// arr[2] == 2
		// arr[3] == 5
		// arr[4] == 3
		// 每个位置的数字一定会自带一个下标，这是一定的!
		// 任何基础类型的元素，天生一定会自带一些类似下标的身份信息的！这是一定的！
		// 生成一个加强堆
		// 加强堆里只放下标即可，因为通过下标可以找到数字
		// 排序策略是 :
		// 数字小的下标，在堆顶
		HeapGreater<Integer> heap2 = new HeapGreater<>((i, j) -> arr[i] - arr[j]);

		// 把数组所有的下标加入堆
		// 就等于加入了所有数字
		heap2.push(0);
		heap2.push(1);
		heap2.push(2);
		heap2.push(3);
		heap2.push(4);

		// 加入之后
		// 可以把某个下标上的数字改了
		// arr[1]原来是3，现在变成了-9
		// 修改外部数组会改变比较器结果，却不会自动调整已有堆序。
		arr[1] = -9;
		// 此时堆被破坏了，因为你擅自改了一个下标的数字
		// 只需要调用resign方法，就能让堆恢复
		// 而且复杂度是O(log N)，很快的
		// 系统提供的堆做不到的，加强堆可以
		// 调用resign方法
		// 用唯一的数组下标定位改变的位置，重复数值不会相互混淆。
		heap2.resign(1);

		// 依次弹出所有下标
		// 会发现下标上的数字越小，下标越早弹出
		// 说明堆是正确的
		while (!heap2.isEmpty()) {
			int curIndex = heap2.pop();
			System.out.println("下标 : " + curIndex + " , 数字 :" + arr[curIndex]);
		}
	}

	// 一个自己定义的非基础类型
	public static class Student {
		public int age;
		public String name;

		public Student(int a, String n) {
			age = a;
			name = n;
		}
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：交换两位置后，两个反向索引都必须更新。同一个键或被 equals 视为同一对象不能作为两个独立条目插入；修改参与哈希判等的字段会使索引失效。

本条未附独立随机对数器。

#### 题解

**为什么正确**

反向索引用稳定身份找到被改动元素，再局部上浮和下沉修复顺序。值变化但对象引用或数组下标未变，因此仍能定位原堆中的同一个元素。

**复杂度**

单次push、pop、resign为O(log N)，本例构建并完整弹出N个元素共O(N log N)；空间O(N)。

**边界与易错点**

交换两位置后，两个反向索引都必须更新。同一个键或被 equals 视为同一对象不能作为两个独立条目插入；修改参与哈希判等的字段会使索引失效。

### 7.4 支持反向索引的加强堆

#### 题目

实现带反向索引表的加强堆，除普通堆操作外还要支持删除指定对象以及对象值改变后的重新调整。

**输入、输出与约束**

给定比较器，元素键在堆中唯一且哈希判等稳定；支持指定对象删除和优先级变更后的 resign。

**函数签名（课程入口）**

```java
public boolean isEmpty();
public int size();
public boolean contains(T obj);
public T peek();
public void push(T obj);
public T pop();
```

**示例**

```text
输入：按数值小根堆加入 3、1、2；删除对象 1；弹出
输出：2
```

解释：反向索引定位 1，补洞并调整后堆顶为 2。

**出处与版本差异**

- [课程源码：class07/HeapGreater.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class07/HeapGreater.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

普通堆只知道堆顶位置，加强堆增加“对象→数组下标”的反向索引。每次交换都同步更新索引。删除指定对象时用末尾对象补洞；对象优先级变化后，从其当前位置分别上浮和下沉，恢复堆序。演示类展示这些操作的调用顺序。

#### 代码答案

```java
package class07;

import java.util.ArrayList;
import java.util.Comparator;
import java.util.HashMap;
import java.util.List;

public class HeapGreater<T> {

	private ArrayList<T> heap;
	// 反向索引记录元素身份到堆下标的映射，定位不再需要扫描。
	private HashMap<T, Integer> indexMap;
	private int heapSize;
	private Comparator<? super T> comp;

	public HeapGreater(Comparator<? super T> c) {
		heap = new ArrayList<>();
		indexMap = new HashMap<>();
		heapSize = 0;
		comp = c;
	}

	public boolean isEmpty() {
		return heapSize == 0;
	}

	public int size() {
		return heapSize;
	}

	public boolean contains(T obj) {
		return indexMap.containsKey(obj);
	}

	public T peek() {
		return heap.get(0);
	}

	public void push(T obj) {
		heap.add(obj);
		// 新元素最初放在尾部，先登记位置再上浮。
		indexMap.put(obj, heapSize);
		heapInsert(heapSize++);
	}

	public T pop() {
		T ans = heap.get(0);
		// 弹出堆顶时先用末尾元素补位，避免数组中间删除。
		swap(0, heapSize - 1);
		// 被弹出元素不再属于堆，同步清除其位置登记。
		indexMap.remove(ans);
		heap.remove(--heapSize);
		// 末尾元素来到根后可能比孩子更差，向下修复堆序。
		heapify(0);
		return ans;
	}

	public void remove(T obj) {
		// 删除任意位置时，用最后一个元素填补空位。
		T replace = heap.get(heapSize - 1);
		int index = indexMap.get(obj);
		indexMap.remove(obj);
		heap.remove(--heapSize);
		// 若删除的本就是尾元素，无需补位，也不能再访问已经缩短的数组槽。
		if (obj != replace) {
			heap.set(index, replace);
			indexMap.put(replace, index);
			// 补位元素与父、孩子的关系都可能改变，执行双向修复。
			resign(replace);
		}
	}

	public void resign(T obj) {
		// 优先级可能提高，先尝试沿父链上浮。
		heapInsert(indexMap.get(obj));
		// 再以对象当前真实下标尝试下沉，覆盖优先级降低的情况。
		heapify(indexMap.get(obj));
	}

	// 请返回堆上的所有元素
	public List<T> getAllElements() {
		List<T> ans = new ArrayList<>();
		for (T c : heap) {
			ans.add(c);
		}
		return ans;
	}

	private void heapInsert(int index) {
		while (comp.compare(heap.get(index), heap.get((index - 1) / 2)) < 0) {
			swap(index, (index - 1) / 2);
			index = (index - 1) / 2;
		}
	}

	private void heapify(int index) {
		int left = index * 2 + 1;
		while (left < heapSize) {
			// 先在存在的两个孩子中选优先级更高者，避免只比较左孩子。
			int best = left + 1 < heapSize && comp.compare(heap.get(left + 1), heap.get(left)) < 0 ? (left + 1) : left;
			best = comp.compare(heap.get(best), heap.get(index)) < 0 ? best : index;
			// 父节点已经优于所有孩子，下沉到此结束。
			if (best == index) {
				break;
			}
			swap(best, index);
			index = best;
			left = index * 2 + 1;
		}
	}

	private void swap(int i, int j) {
		T o1 = heap.get(i);
		T o2 = heap.get(j);
		heap.set(i, o2);
		heap.set(j, o1);
		// 交换数组元素时同步交换反向索引，否则之后remove/resign会定位错误。
		indexMap.put(o2, i);
		// 交换对象位置后，同步记录第一个对象的新下标。
		indexMap.put(o1, j);
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：交换两位置后，两个反向索引都必须更新。同一个键或被 equals 视为同一对象不能作为两个独立条目插入；修改参与哈希判等的字段会使索引失效。

本条未附独立随机对数器。

#### 题解

**为什么正确**

索引表始终给出每个在堆对象的真实位置，因而定位不需遍历。一次值变化只可能破坏相邻父子关系，上浮或下沉能沿唯一受影响路径修复。删除补洞后执行同样的修复即可。

**复杂度**

contains 为平均 O(1)；push、pop、remove、resign 为 O(log N)；getAllElements 为 O(N)，空间 O(N)。

**边界与易错点**

交换两位置后，两个反向索引都必须更新。同一个键或被 equals 视为同一对象不能作为两个独立条目插入；修改参与哈希判等的字段会使索引失效。

### 7.5 源码勘误：值包装类与加强堆中的元素身份

#### 题目

课程保留了一个不推荐使用的值包装类。结合加强堆回答：数组中有重复值时，应怎样唯一标识每个待更新元素？

**输入、输出与约束**

本条为源码勘误说明；Inner<T> 仅保存一个 value，没有算法返回值。数组示例的堆操作在 ShowHeapGreater 中实现。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：arr=[3,3]，把下标0、1放入加强堆
输出：两个值相同的位置仍能分别定位与修改
```

解释：修改arr[1]后调用resign(1)，不会错误定位到下标0。

**出处与版本差异**

- [课程源码：class07/Inner.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class07/Inner.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

本文件只有一个泛型值包装类，作者明确标注它不是课上推荐方案。加强堆需要能唯一定位元素；对数组中的重复值，应把唯一的数组下标放进堆，再通过下标比较外部数组值，具体用法见同章 ShowHeapGreater。

#### 代码答案

```java
package class07;

// 课上讲的包一层，这是不对的！
// 这个文件是废物！留在这里是为了提醒同学们 :
// 看"Code03_ShowHeapGreater"，展示了用法
public class Inner<T> {
	// 只保存被包装的值；这个字段不提供加强堆所需的反向索引。
	public T value;

	public Inner(T v) {
		value = v;
	}
}
```

---

##### 边界核对

用题面示例核对接口，再检查：Inner 是顶层类的名字，不表示它是 Java 成员内部类。作者保留该文件作为说明，应学习 ShowHeapGreater 的下标身份方案，不把它当作额外算法题。

本条未附独立随机对数器。

#### 题解

**为什么正确**

数组下标唯一标识一个位置，因此两个值相等的元素仍有不同的反向索引键。单纯按数值作为键会覆盖重复元素的位置。包装对象也需要保持稳定身份，只有包装类本身并未完成堆的定位和更新协议。

**复杂度**

这个包装类构造和字段读写均 O(1)；它本身不提供任何堆操作。

**边界与易错点**

Inner 是顶层类的名字，不表示它是 Java 成员内部类。作者保留该文件作为说明，应学习 ShowHeapGreater 的下标身份方案，不把它当作额外算法题。


<a id="course-08"></a>

## 第 8 课：前缀树、计数排序与基数排序

### 8.1 前缀树

#### 题目

实现支持插入、删除、精确计数和前缀计数的前缀树；字符串由小写英文字母组成。

**输入、输出与约束**

输入小写英文单词和操作序列；按课程接口统计重复单词，数组实现须保证节点容量足够。

**函数签名（课程入口）**

```java
public void insert(String word);
public void erase(String word);
public int countWordsEqualTo(String word);
public int countWordsStartingWith(String pre);
```

**示例**

```text
输入：insert("app") 两次，insert("apple") 一次，erase("app") 一次
输出：精确 app=1；前缀 app=2
```

解释：剩余一次 app 和一次 apple 都经过 app 节点。

**出处与版本差异**

- [课程源码：class08/Code01_Trie.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class08/Code01_Trie.java)。
- [LeetCode 原题 1804. 实现 Trie II（Implement Trie II (Prefix Tree)）](https://leetcode.com/problems/implement-trie-ii-prefix-tree/)

#### 思路

沿字符逐层下降，每个节点 pass 记录有多少次插入经过这里，end 记录有多少单词恰好在这里结束。插入沿途增加 pass、终点增加 end；精确查询取 end，前缀查询取 pass。删除前先确认词存在，再沿路径减计数。两份源码分别采用对象节点和数组节点等表示方式。

#### 代码答案

```java
package class08;

public class Code01_Trie {

	// 测试链接 : https://leetcode.cn/problems/implement-trie-ii-prefix-tree/
	// 提交Trie类可以直接通过
	// 原来代码是对的，但是既然找到了直接测试的链接，那就直接测吧
	// 这个链接上要求实现的功能和课上讲的完全一样
	// 该前缀树的路用数组实现
	class Trie {

		class Node {
			public int pass;
			public int end;
			public Node[] nexts;

			public Node() {
				pass = 0;
				end = 0;
				nexts = new Node[26];
			}
		}

		private Node root;

		public Trie() {
			root = new Node();
		}

		public void insert(String word) {
			if (word == null) {
				return;
			}
			char[] str = word.toCharArray();
			Node node = root;
			// 一个新单词经过当前前缀，前缀计数加一。
			node.pass++;
			int path = 0;
			for (int i = 0; i < str.length; i++) { // 从左往右遍历字符
				path = str[i] - 'a'; // 由字符，对应成走向哪条路
				if (node.nexts[path] == null) {
					node.nexts[path] = new Node();
				}
				node = node.nexts[path];
				// 一个新单词经过当前前缀，前缀计数加一。
				node.pass++;
			}
			// 完整单词在此结束，终点次数加一，不能只记录是否存在。
			node.end++;
		}

		public void erase(String word) {
			if (countWordsEqualTo(word) != 0) {
				char[] chs = word.toCharArray();
				Node node = root;
				node.pass--;
				int path = 0;
				for (int i = 0; i < chs.length; i++) {
					path = chs[i] - 'a';
					// 该分支已无单词经过，可以剪掉整条后续路径。
					if (--node.nexts[path].pass == 0) {
						node.nexts[path] = null;
						return;
					}
					node = node.nexts[path];
				}
				node.end--;
			}
		}

		public int countWordsEqualTo(String word) {
			if (word == null) {
				return 0;
			}
			char[] chs = word.toCharArray();
			Node node = root;
			int index = 0;
			for (int i = 0; i < chs.length; i++) {
				index = chs[i] - 'a';
				if (node.nexts[index] == null) {
					return 0;
				}
				node = node.nexts[index];
			}
			return node.end;
		}

		public int countWordsStartingWith(String pre) {
			if (pre == null) {
				return 0;
			}
			char[] chs = pre.toCharArray();
			Node node = root;
			int index = 0;
			for (int i = 0; i < chs.length; i++) {
				index = chs[i] - 'a';
				if (node.nexts[index] == null) {
					return 0;
				}
				node = node.nexts[index];
			}
			return node.pass;
		}
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：end 是次数，不是布尔值；重复插入要重复删除。删除不存在的词不可减计数。数组池版本存在容量上限；课程实现针对 a..z，不能直接接受其他字符。

本条未附独立随机对数器。

#### 题解

**为什么正确**

一条根到节点的路径唯一表示一个前缀，所有拥有该前缀的单词都会且只会经过该节点。因此 pass 就是前缀出现次数，end 是完整单词次数。删除只减少该词路径上的计数，其他分支不受影响。

**复杂度**

单次插入、删除、查询 O(L)，L 为词长；节点空间与实际存储的不同前缀数量成正比，固定字母表下上界 O(所有插入词长之和)。

**边界与易错点**

end 是次数，不是布尔值；重复插入要重复删除。删除不存在的词不可减计数。数组池版本存在容量上限；课程实现针对 a..z，不能直接接受其他字符。

### 8.2 前缀树

#### 题目

实现支持插入、删除、精确计数和前缀计数的前缀树；字符串由小写英文字母组成。

**输入、输出与约束**

输入小写英文单词和操作序列；按课程接口统计重复单词，数组实现须保证节点容量足够。

**函数签名（课程入口）**

```java
public void insert(String word);
public void erase(String word);
public int countWordsEqualTo(String word);
public int countWordsStartingWith(String pre);
```

**示例**

```text
输入：insert("app") 两次，insert("apple") 一次，erase("app") 一次
输出：精确 app=1；前缀 app=2
```

解释：剩余一次 app 和一次 apple 都经过 app 节点。

**出处与版本差异**

- [课程源码：class08/Code02_Trie.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class08/Code02_Trie.java)。
- [LeetCode 原题 1804. 实现 Trie II（Implement Trie II (Prefix Tree)）](https://leetcode.com/problems/implement-trie-ii-prefix-tree/)

#### 思路

沿字符逐层下降，每个节点 pass 记录有多少次插入经过这里，end 记录有多少单词恰好在这里结束。插入沿途增加 pass、终点增加 end；精确查询取 end，前缀查询取 pass。删除前先确认词存在，再沿路径减计数。两份源码分别采用对象节点和数组节点等表示方式。

#### 代码答案

```java
package class08;

import java.util.HashMap;

public class Code02_Trie {

	// 测试链接 : https://leetcode.cn/problems/implement-trie-ii-prefix-tree/
	// 提交Trie类可以直接通过
	// 原来代码是对的，但是既然找到了直接测试的链接，那就直接测吧
	// 这个链接上要求实现的功能和课上讲的完全一样
	// 该前缀树的路用哈希表实现
	class Trie {

		class Node {
			public int pass;
			public int end;
			public HashMap<Integer, Node> nexts;

			public Node() {
				pass = 0;
				end = 0;
				nexts = new HashMap<>();
			}
		}

		private Node root;

		public Trie() {
			root = new Node();
		}

		public void insert(String word) {
			if (word == null) {
				return;
			}
			char[] chs = word.toCharArray();
			Node node = root;
			// 一个新单词经过当前前缀，前缀计数加一。
			node.pass++;
			int index = 0;
			for (int i = 0; i < chs.length; i++) {
				index = (int) chs[i];
				if (!node.nexts.containsKey(index)) {
					node.nexts.put(index, new Node());
				}
				node = node.nexts.get(index);
				// 一个新单词经过当前前缀，前缀计数加一。
				node.pass++;
			}
			// 完整单词在此结束，终点次数加一，不能只记录是否存在。
			node.end++;
		}

		public void erase(String word) {
			if (countWordsEqualTo(word) != 0) {
				char[] chs = word.toCharArray();
				Node node = root;
				node.pass--;
				int index = 0;
				for (int i = 0; i < chs.length; i++) {
					index = (int) chs[i];
					if (--node.nexts.get(index).pass == 0) {
						node.nexts.remove(index);
						return;
					}
					node = node.nexts.get(index);
				}
				node.end--;
			}
		}

		public int countWordsEqualTo(String word) {
			if (word == null) {
				return 0;
			}
			char[] chs = word.toCharArray();
			Node node = root;
			int index = 0;
			for (int i = 0; i < chs.length; i++) {
				index = (int) chs[i];
				if (!node.nexts.containsKey(index)) {
					return 0;
				}
				node = node.nexts.get(index);
			}
			return node.end;
		}

		public int countWordsStartingWith(String pre) {
			if (pre == null) {
				return 0;
			}
			char[] chs = pre.toCharArray();
			Node node = root;
			int index = 0;
			for (int i = 0; i < chs.length; i++) {
				index = (int) chs[i];
				if (!node.nexts.containsKey(index)) {
					return 0;
				}
				node = node.nexts.get(index);
			}
			return node.pass;
		}
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：end 是次数，不是布尔值；重复插入要重复删除。删除不存在的词不可减计数。数组池版本存在容量上限；课程实现针对 a..z，不能直接接受其他字符。

本条未附独立随机对数器。

#### 题解

**为什么正确**

一条根到节点的路径唯一表示一个前缀，所有拥有该前缀的单词都会且只会经过该节点。因此 pass 就是前缀出现次数，end 是完整单词次数。删除只减少该词路径上的计数，其他分支不受影响。

**复杂度**

单次插入、删除、查询 O(L)，L 为词长；节点空间与实际存储的不同前缀数量成正比，固定字母表下上界 O(所有插入词长之和)。

**边界与易错点**

end 是次数，不是布尔值；重复插入要重复删除。删除不存在的词不可减计数。数组池版本存在容量上限；课程实现针对 a..z，不能直接接受其他字符。

### 8.3 计数排序

#### 题目

给定一个整数数组 `arr`，请使用本节指定的排序算法将数组原地调整为非递减顺序。

**输入、输出与约束**

输入非负整数数组，最大值应足够小以分配桶数组；原地写回排序结果。

**函数签名（课程入口）**

```java
public static void countSort(int[] arr);
```

**示例**

```text
输入：arr=[2,0,2,1]
输出：[0,1,2,2]
```

解释：0、1、2 的计数分别为 1、1、2。

**出处与版本差异**

- [课程源码：class08/Code03_CountSort.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class08/Code03_CountSort.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先找最大值 max，创建 bucket[0..max]。扫描数组统计每个值出现多少次，再按值从小到大把对应次数写回数组。

#### 代码答案

```java
package class08;

import java.util.Arrays;

public class Code03_CountSort {

	// only for 0~200 value
	public static void countSort(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		int max = Integer.MIN_VALUE;
		for (int i = 0; i < arr.length; i++) {
			max = Math.max(max, arr[i]);
		}
		int[] bucket = new int[max + 1];
		for (int i = 0; i < arr.length; i++) {
			// 把数值当作桶下标，记录这个数需要在结果中出现几次。
			bucket[arr[i]]++;
		}
		int i = 0;
		for (int j = 0; j < bucket.length; j++) {
			// 按桶号递增展开计数，保证写回值非递减。
			while (bucket[j]-- > 0) {
				arr[i++] = j;
			}
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static void comparator(int[] arr) {
		Arrays.sort(arr);
	}

	// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 150;
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int[] arr1 = generateRandomArray(maxSize, maxValue);
			int[] arr2 = copyArray(arr1);
			countSort(arr1);
			comparator(arr2);
			if (!isEqual(arr1, arr2)) {
				succeed = false;
				printArray(arr1);
				printArray(arr2);
				break;
			}
		}
		System.out.println(succeed ? "Nice!" : "Fucking fucked!");

		int[] arr = generateRandomArray(maxSize, maxValue);
		printArray(arr);
		countSort(arr);
		printArray(arr);

	}
```

#### 题解

**为什么正确**

计数阶段保留了每个数的精确重数，写回阶段按照值顺序输出全部重数，故结果既有序又与原数组多重集合相同。

**复杂度**

时间 O(N+V)，额外空间 O(V)，V=max+1；不是无条件 O(N)。

**边界与易错点**

当前实现以值作数组下标，只接受非负数。值域很大时桶的开销可能远大于输入长度；不能只看元素个数选择计数排序。

### 8.4 基数排序

#### 题目

给定一个整数数组 `arr`，请使用本节指定的排序算法将数组原地调整为非递减顺序。

**输入、输出与约束**

输入非负 int 数组；原地写回升序结果，允许重复值和 0。

**函数签名（课程入口）**

```java
public static int maxbits(int[] arr);
public static int getDigit(int x, int d);
```

**示例**

```text
输入：arr=[21,13,11]
输出：[11,13,21]
```

解释：按个位稳定排序后为 [21,11,13]，再按十位排序。

**出处与版本差异**

- [课程源码：class08/Code04_RadixSort.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class08/Code04_RadixSort.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

从个位到最高位逐位排序。每轮统计当前位的 0..9 计数，累加成前缀位置，再从右向左扫描原数组把元素稳定放入辅助数组。低位的既有顺序因此在高位相等时得到保留。

#### 代码答案

```java
package class08;

import java.util.Arrays;

public class Code04_RadixSort {

	// only for no-negative value
	public static void radixSort(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		radixSort(arr, 0, arr.length - 1, maxbits(arr));
	}

	public static int maxbits(int[] arr) {
		int max = Integer.MIN_VALUE;
		for (int i = 0; i < arr.length; i++) {
			max = Math.max(max, arr[i]);
		}
		int res = 0;
		while (max != 0) {
			res++;
			max /= 10;
		}
		return res;
	}

	// arr[L..R]排序  ,  最大值的十进制位数digit
	public static void radixSort(int[] arr, int L, int R, int digit) {
		final int radix = 10;
		int i = 0, j = 0;
		// 有多少个数准备多少个辅助空间
		int[] help = new int[R - L + 1];
		for (int d = 1; d <= digit; d++) { // 有多少位就进出几次
			// 10个空间
		    // count[0] 当前位(d位)是0的数字有多少个
			// count[1] 当前位(d位)是(0和1)的数字有多少个
			// count[2] 当前位(d位)是(0、1和2)的数字有多少个
			// count[i] 当前位(d位)是(0~i)的数字有多少个
			int[] count = new int[radix]; // count[0..9]
			for (i = L; i <= R; i++) {
				// 103  1   3
				// 209  1   9
				j = getDigit(arr[i], d);
				count[j]++;
			}
			for (i = 1; i < radix; i++) {
				// 累计计数给出当前位≤i 的元素总数，即桶的结束位置。
				count[i] = count[i] + count[i - 1];
			}
			for (i = R; i >= L; i--) {
				j = getDigit(arr[i], d);
				// 从右向左放入对应桶末尾，同位元素保持原相对次序。
				help[count[j] - 1] = arr[i];
				// 该桶最后一个空位置已经使用，下一次向左一格。
				count[j]--;
			}
			for (i = L, j = 0; i <= R; i++, j++) {
				arr[i] = help[j];
			}
		}
	}

	public static int getDigit(int x, int d) {
		return ((x / ((int) Math.pow(10, d - 1))) % 10);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static void comparator(int[] arr) {
		Arrays.sort(arr);
	}

	// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100000;
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int[] arr1 = generateRandomArray(maxSize, maxValue);
			int[] arr2 = copyArray(arr1);
			radixSort(arr1);
			comparator(arr2);
			if (!isEqual(arr1, arr2)) {
				succeed = false;
				printArray(arr1);
				printArray(arr2);
				break;
			}
		}
		System.out.println(succeed ? "Nice!" : "Fucking fucked!");

		int[] arr = generateRandomArray(maxSize, maxValue);
		printArray(arr);
		radixSort(arr);
		printArray(arr);

	}
```

#### 题解

**为什么正确**

归纳地，处理完前 d 位后数组按最低 d 位有序。下一轮按第 d+1 位稳定分组，同组内保留低 d 位的顺序，从而按最低 d+1 位有序。处理完最高位即为数值升序。

**复杂度**

D 位十进制整数的时间 O(D(N+10))，辅助空间 O(N+10)。

**边界与易错点**

每一轮必须稳定，反向扫描配合递减累计计数。当前实现不支持负数；Java int 的最高十进制位提取应注意数值类型。

### 8.5 希尔排序

#### 题目

给定一个整数数组 `arr`，请使用本节指定的排序算法将数组原地调整为非递减顺序。

**输入、输出与约束**

输入任意 int 数组，原地升序排序。

**函数签名（课程入口）**

```java
public static void shellSort(int[] arr);
```

**示例**

```text
输入：arr=[5,1,4,2,3]
输出：[1,2,3,4,5]
```

解释：先对跨步子序列排序，再用 gap=1 完成整体排序。

**出处与版本差异**

- [课程源码：class08/Code05_ShellSort.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class08/Code05_ShellSort.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

选择逐渐缩小的 gap，把相差 gap 的元素视作同一子序列，在每个子序列内做插入排序。大步长先消除远距离逆序，最后 gap=1 使整个数组完成普通插入排序。

#### 代码答案

```java
package class08;

// 这是希尔排序，课上没有讲，因为比较简单，有兴趣的同学可以看一下
// 今天在面试场上也基本不怎么问了
// 这个排序就是调整步长的插入排序，也可以认为是插入排序的小改进版本
// 改变不了时间复杂度，只是优化了常数时间
// 我还写了和插入排序的性能对比
public class Code05_ShellSort {

	public static void shellSort(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		// 步长依次为5、2、1
		// 在插入排序中，一步一步往前交换，直到左边的数<=当前的数，停止
		// 在希尔排序中，如果步长依次为5、2、1
		// 那么来到每个数，每次跳5步往前交换，直到往前5步的数<=当前的数，停止
		// 然后再来到每个数，每次跳2步往前交换，直到往前2步的数<=当前的数，停止
		// 然后再来到每个数，每次跳1步往前交换，直到往前1步的数<=当前的数，停止
		// 当前源码固定使用5、2、1三个步长；最后一轮步长1保证全局有序。
		int[] step = { 5, 2, 1 };
		for (int s = 0; s < step.length; s++) {
			for (int i = step[s]; i < arr.length; i++) {
				// 沿相同步长分组向前插入，相邻交换在该组内消除逆序。
				for (int j = i - step[s]; j >= 0 && arr[j] > arr[j + step[s]]; j -= step[s]) {
					swap(arr, j, j + step[s]);
				}
			}
		}
	}

	public static void swap(int[] arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	public static void insertionSort(int[] arr) {
		if (arr == null || arr.length < 2) {
			return;
		}
		// 不只1个数
		for (int i = 1; i < arr.length; i++) { // 0 ~ i 做到有序
			for (int j = i - 1; j >= 0 && arr[j] > arr[j + 1]; j--) {
				swap(arr, j, j + 1);
			}
		}
	}

	// 为了测试
	public static int[] generateRandomArray(int len, int maxValue) {
		int[] arr = new int[len];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// 为了测试
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// 为了测试
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// 为了测试
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// 为了测试
	public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100;
		boolean succeed = true;
		for (int i = 0; i < testTime; i++) {
			int len = (int) (Math.random() * maxSize);
			int[] arr1 = generateRandomArray(len, maxValue);
			int[] arr2 = copyArray(arr1);
			shellSort(arr1);
			insertionSort(arr2);
			if (!isEqual(arr1, arr2)) {
				succeed = false;
				printArray(arr1);
				printArray(arr2);
				break;
			}
		}
		System.out.println(succeed ? "功能测试通过！" : "功能测试不通过！");
		int len = 100000;
		System.out.println("数据样本长度 : " + len);
		int[] arr1 = generateRandomArray(len, maxValue);
		int[] arr2 = copyArray(arr1);
		long start = 0;
		long end = 0;
		start = System.currentTimeMillis();
		shellSort(arr1);
		end = System.currentTimeMillis();
		System.out.println("希尔排序运行时间 : " + (end - start) + " 毫秒");
		start = System.currentTimeMillis();
		insertionSort(arr2);
		end = System.currentTimeMillis();
		System.out.println("插入排序运行时间 : " + (end - start) + " 毫秒");
	}
```

#### 题解

**为什么正确**

每轮结束后所有按 gap 划分的子序列都有序；最后一轮只有一个覆盖全数组的子序列，因而整体有序。之前轮次只影响效率，不改变最后一轮的正确性。

**复杂度**

源码包含不同步长方案，时间依赖步长及输入；保守最坏上界 O(N²)，不能统一声称 O(N log N)。额外空间 O(1)。

**边界与易错点**

必须包含 gap=1 的一轮。内层移动距离是 gap，不是 1；希尔排序不稳定。

<a id="course-09"></a>

## 第 9 课：链表常见面试题

### 9.1 合并两个有序链表（新手班前置）

#### 题目

给定两个按非递减顺序排列的单链表，将它们合并为一个新的非递减链表并返回头节点。

**输入、输出与约束**

输入两条互不相交的非递减单链表，可为空；原地合并并返回头。

**函数签名（课程入口）**

```java
public static ListNode mergeTwoLists(ListNode head1, ListNode head2);
```

**示例**

```text
输入：list1=1→3，list2=2→4
输出：1→2→3→4
```

解释：先确定头节点 1，再按 2、3、4 的顺序连接。

**出处与版本差异**

- [课程源码：class04/Code06_MergeTwoSortedLinkedList.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class04/Code06_MergeTwoSortedLinkedList.java)。
- [LeetCode 原题 21. 合并两个有序链表（Merge Two Sorted Lists）](https://leetcode.com/problems/merge-two-sorted-lists/)

#### 思路

先在两个非空表头中选较小者作为结果头。pre 指向已合并部分尾部，cur1、cur2 指向两表最小的未合并节点。每次连接较小者并推进对应指针；一边耗尽后直接连接另一边剩余整段。

#### 代码答案

```java
package class04;

// 测试链接：https://leetcode.com/problems/merge-two-sorted-lists
public class Code06_MergeTwoSortedLinkedList {

	// 不要提交这个类
	public static class ListNode {
		public int val;
		public ListNode next;
	}

	public static ListNode mergeTwoLists(ListNode head1, ListNode head2) {
		if (head1 == null || head2 == null) {
			return head1 == null ? head2 : head1;
		}
		// 选出全局最小节点作为新头，随后只维护尾部。
		ListNode head = head1.val <= head2.val ? head1 : head2;
		ListNode cur1 = head.next;
		ListNode cur2 = head == head1 ? head2 : head1;
		ListNode pre = head;
		while (cur1 != null && cur2 != null) {
			if (cur1.val <= cur2.val) {
				// 左表当前节点较小，把它连接到已合并尾部。
				pre.next = cur1;
				cur1 = cur1.next;
			} else {
				// 右表当前节点较小，把它连接到已合并尾部。
				pre.next = cur2;
				cur2 = cur2.next;
			}
			// 已合并区扩大一个节点，尾指针同步移动。
			pre = pre.next;
		}
		// 一表耗尽后，另一表的剩余有序后缀可直接接上。
		pre.next = cur1 != null ? cur1 : cur2;
		return head;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：这是复用原节点的合并，返回的新表头不意味着重新分配全部节点。两表应无环且不共享节点；连接后必须推进 pre，避免覆盖已有链段。

本条未附独立随机对数器。

#### 题解

**为什么正确**

两条链表各自有序，所有未合并节点的最小值必在两个当前节点之一。因此逐次选小值保持结果有序；每次只消费一个节点，尾部追加也不漏节点。

**复杂度**

时间 O(N+M)，额外空间 O(1)。

**边界与易错点**

这是复用原节点的合并，返回的新表头不意味着重新分配全部节点。两表应无环且不共享节点；连接后必须推进 pre，避免覆盖已有链段。

### 9.2 两数相加（链表表示）（新手班前置）

#### 题目

给定两个低位在前的非空链表，分别表示两个非负整数。逐位求和并返回同样表示的结果链表。本课程允许改写并复用输入节点。

**输入、输出与约束**

两表非空、无环、互不共享节点，各节点为 0..9，表示无前导零的非负整数；返回可复用输入节点的结果链表。

**函数签名（课程入口）**

```java
public static ListNode addTwoNumbers(ListNode head1, ListNode head2);
```

**示例**

```text
输入：head1=9→9，head2=1
输出：0→0→1
```

解释：99+1=100；结果仍按低位到高位保存。

**出处与版本差异**

- [课程源码：class04/Code05_AddTwoNumbers.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class04/Code05_AddTwoNumbers.java)。
- [LeetCode 原题 2. 两数相加（Add Two Numbers）](https://leetcode.com/problems/add-two-numbers/)

#### 思路

链表低位在前，因此从表头开始逐位计算。源码先找出较长链表 l，将结果原地写入它；两表都有节点时相加，短表耗尽后继续处理长表和进位。最后仍有进位才追加一个节点。

#### 代码答案

```java
package class04;

// 测试链接：https://leetcode.com/problems/add-two-numbers/
public class Code05_AddTwoNumbers {

	// 不要提交这个类
	public static class ListNode {
		public int val;
		public ListNode next;

		public ListNode(int val) {
			this.val = val;
		}

		public ListNode(int val, ListNode next) {
			this.val = val;
			this.next = next;
		}
	}

	public static ListNode addTwoNumbers(ListNode head1, ListNode head2) {
		int len1 = listLength(head1);
		int len2 = listLength(head2);
		// 选择较长链表承载结果，减少新节点分配。
		ListNode l = len1 >= len2 ? head1 : head2;
		ListNode s = l == head1 ? head2 : head1;
		ListNode curL = l;
		ListNode curS = s;
		ListNode last = curL;
		int carry = 0;
		int curNum = 0;
		while (curS != null) {
			// 把当前两位和低位传来的进位合并。
			curNum = curL.val + curS.val + carry;
			// 当前节点只保存个位部分，高位通过 carry 传递。
			curL.val = (curNum % 10);
			// 保存进位，下一对节点相加时继续使用。
			carry = curNum / 10;
			last = curL;
			curL = curL.next;
			curS = curS.next;
		}
		while (curL != null) {
			curNum = curL.val + carry;
			// 当前节点只保存个位部分，高位通过 carry 传递。
			curL.val = (curNum % 10);
			// 保存进位，下一对节点相加时继续使用。
			carry = curNum / 10;
			last = curL;
			curL = curL.next;
		}
		if (carry != 0) {
			// 所有原节点处理完仍有进位，需要扩展最高位。
			last.next = new ListNode(1);
		}
		return l;
	}

	// 求链表长度
	public static int listLength(ListNode head) {
		int len = 0;
		while (head != null) {
			len++;
			head = head.next;
		}
		return len;
	}

}
```

---

##### 新增对数器（固定输入或固定随机种子）

整数加法作独立基准，覆盖0、不同长度、连续进位；同时检查数字范围、链长度与复用较长输入链表的约定。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
    java.util.Random random = new java.util.Random(20260905L);
    int[] edge = {0, 1, 9, 99, 999, 999999};
    for (int round = 0; round < 5000; round++) {
        int a = round < edge.length * edge.length ? edge[round / edge.length] : random.nextInt(1000000);
        int b = round < edge.length * edge.length ? edge[round % edge.length] : random.nextInt(1000000);
        // 每轮重新建两条不相交链，避免原地改写污染下一轮基准。
        ListNode first = digitsForCheck(a), second = digitsForCheck(b);
        int n = listLength(first), m = listLength(second);
        ListNode result = addTwoNumbers(first, second);
        if (result != (n >= m ? first : second)) throw new AssertionError("result not reused");
        int actual = 0, place = 1, length = 0;
        for (ListNode p = result; p != null; p = p.next) {
            if (++length > Math.max(n, m) + 1) throw new AssertionError("cycle or extra nodes");
            if (p.val < 0 || p.val > 9) throw new AssertionError("invalid digit");
            actual += p.val * place;
            place *= 10;
        }
        if (actual != a + b) throw new AssertionError("a=" + a + " b=" + b + " actual=" + actual);
    }
    System.out.println("PASS: AddTwoNumbers, arithmetic and input-node reuse");
}

private static ListNode digitsForCheck(int value) {
    ListNode dummy = new ListNode(0), tail = dummy;
    do {
        tail.next = new ListNode(value % 10);
        tail = tail.next;
        value /= 10;
    } while (value != 0);
    return dummy.next;
}
```

#### 题解

**为什么正确**

每轮 curNum=当前两位+carry，curNum%10 是结果当前位，curNum/10 是传给高一位的进位。按十进制位权展开，这两部分之和与本轮总值相同。低位已经确定，后面的高位不会再改变它。

**复杂度**

时间 O(N+M)，额外空间 O(1)，至多新建一个进位节点。结果复用输入链表，不应把全部结果节点算作额外分配。

**边界与易错点**

允许修改输入是本实现的重要约定；要求保留原链表时应改成新建结果链表。短表耗尽不代表进位处理结束；[9,9]+[1] 必须得到 [0,0,1]。

### 9.3 K 个一组翻转链表（新手班前置）

#### 题目

给定单链表头节点和正整数 `k`，每 `k` 个节点为一组原地翻转；最后不足 `k` 个节点的部分保持原顺序。

**输入、输出与约束**

输入无环单链表与 k≥1；每个完整 k 节点分组原地翻转，返回新头。

**函数签名（课程入口）**

```java
public static ListNode reverseKGroup(ListNode head, int k);
```

**示例**

```text
输入：head=1→2→3→4→5, k=2
输出：2→1→4→3→5
```

解释：最后一组只有一个节点，不翻转。

**出处与版本差异**

- [课程源码：class04/Code04_ReverseNodesInKGroup.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class04/Code04_ReverseNodesInKGroup.java)。
- [LeetCode 原题 25. K 个一组翻转链表（Reverse Nodes in k-Group）](https://leetcode.com/problems/reverse-nodes-in-k-group/)

#### 思路

先从组起点向后数 k 个节点，取得组尾 end；不足 k 个就保留剩余链表。反转 [start,end] 时保存 end.next 作为下一组起点，再把上一组尾部连接到本组新头 end，原 start 则成为本组新尾。

#### 代码答案

```java
package class04;

// 测试链接：https://leetcode.com/problems/reverse-nodes-in-k-group/
public class Code04_ReverseNodesInKGroup {

	// 不要提交这个类
	public static class ListNode {
		public int val;
		public ListNode next;
	}

	public static ListNode reverseKGroup(ListNode head, int k) {
		ListNode start = head;
		// 先探测本组是否足够 k 个，不能边探测边改链。
		// 先探测本组是否足够 k 个，不能边探测边改链。
		ListNode end = getKGroupEnd(start, k);
		if (end == null) {
			return head;
		}
		// 第一组凑齐了！
		head = end;
		reverse(start, end);
		// 上一组的结尾节点
		// 原组起点已变成本组新尾，作为下次连接的前驱。
		ListNode lastEnd = start;
		while (lastEnd.next != null) {
			start = lastEnd.next;
			end = getKGroupEnd(start, k);
			if (end == null) {
				return head;
			}
			reverse(start, end);
			// 上组尾部连接本组反转后的新头。
			lastEnd.next = end;
			// 原组起点已变成本组新尾，作为下次连接的前驱。
			lastEnd = start;
		}
		return head;
	}

	public static ListNode getKGroupEnd(ListNode start, int k) {
		while (--k != 0 && start != null) {
			start = start.next;
		}
		return start;
	}

	public static void reverse(ListNode start, ListNode end) {
		// 把闭区间尾转换成循环停止位置，并保留下一组连接点。
		end = end.next;
		ListNode pre = null;
		ListNode cur = start;
		ListNode next = null;
		while (cur != end) {
			next = cur.next;
			cur.next = pre;
			pre = cur;
			cur = next;
		}
		start.next = end;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：先确认组长够 k 再修改指针。反转后组尾是原 start；下一组的起点要从保存的位置获取。k=1 不应改变链表。

本条未附独立随机对数器。

#### 题解

**为什么正确**

每组反转只改变组内指向，并以预存的下一组起点收尾，所以不会丢失后续节点。分组之间通过“前组尾→本组新头”连接；完整组逐个处理后，剩余不足 k 个的后缀保持原样。

**复杂度**

每节点参与一次计数和一次反转，时间 O(N)，额外空间 O(1)。

**边界与易错点**

先确认组长够 k 再修改指针。反转后组尾是原 start；下一组的起点要从保存的位置获取。k=1 不应改变链表。

### 9.4 合并 K 个升序链表（新手班前置）

#### 题目

给定由 `k` 个升序单链表组成的数组，把所有节点合并为一条升序链表并返回头节点。

**输入、输出与约束**

输入若干互不相交、无环的升序链表，允许空数组与空链表元素；返回合并后的头。

**函数签名（课程入口）**

```java
public static ListNode mergeKLists(ListNode[] lists);
```

**示例**

```text
输入：lists=[1→4,1→3,2→6]
输出：1→1→2→3→4→6
```

解释：每轮只比较三条链表当前头节点。

**出处与版本差异**

- [课程源码：class06/Code01_MergeKSortedLists.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class06/Code01_MergeKSortedLists.java)。
- [LeetCode 原题 23. 合并 K 个升序链表（Merge k Sorted Lists）](https://leetcode.com/problems/merge-k-sorted-lists/)

#### 思路

小根堆先放入每条非空链表的头。弹出最小节点接到结果尾部，然后只把该节点的后继入堆。堆中因此始终至多保留每条链表一个候选。

#### 代码答案

```java
package class06;

import java.util.Comparator;
import java.util.PriorityQueue;

// 测试链接：https://leetcode.com/problems/merge-k-sorted-lists/
public class Code01_MergeKSortedLists {

	public static class ListNode {
		public int val;
		public ListNode next;
	}

	public static class ListNodeComparator implements Comparator<ListNode> {

		@Override
		public int compare(ListNode o1, ListNode o2) {
			return o1.val - o2.val;
		}

	}

	public static ListNode mergeKLists(ListNode[] lists) {
		if (lists == null) {
			return null;
		}
		PriorityQueue<ListNode> heap = new PriorityQueue<>(new ListNodeComparator());
		for (int i = 0; i < lists.length; i++) {
			if (lists[i] != null) {
				heap.add(lists[i]);
			}
		}
		if (heap.isEmpty()) {
			return null;
		}
		ListNode head = heap.poll();
		ListNode pre = head;
		if (pre.next != null) {
			heap.add(pre.next);
		}
		while (!heap.isEmpty()) {
			ListNode cur = heap.poll();
			// 堆顶是所有尚未合并节点中的最小值，将其接在结果尾部。
			pre.next = cur;
			pre = cur;
			if (cur.next != null) {
				// 消费 cur 后，将同一链表新的最小未消费节点放回候选堆。
				heap.add(cur.next);
			}
		}
		return head;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：跳过空表头；不同链表应不共享节点。比较器用 Integer.compare 更安全，课程减法比较依赖节点值不使差值溢出的约束。

本条未附独立随机对数器。

#### 题解

**为什么正确**

每条链表最小的未消费节点一定是它当前候选。全体未消费节点的最小值也必在这些候选中，故堆顶就是下一个结果节点。后继入堆后同样的性质继续成立。

**复杂度**

总节点数 N、链表条数 K：时间 O(N log K)，堆空间 O(K)，K≤1 时退化为线性或常数连接。

**边界与易错点**

跳过空表头；不同链表应不共享节点。比较器用 Integer.compare 更安全，课程减法比较依赖节点值不使差值溢出的约束。

### 9.5 链表中点及其相邻位置

#### 题目

给定单链表头节点，根据所调用的方法返回中点、上中点、下中点或对应位置的前驱节点。

**输入、输出与约束**

输入无环单链表；midOrUpMidNode、midOrDownMidNode 等方法分别遵循名称中的中点约定。

**函数签名（课程入口）**

```java
public static Node midOrUpMidNode(Node head);
public static Node midOrDownMidNode(Node head);
public static Node midOrUpMidPreNode(Node head);
public static Node midOrDownMidPreNode(Node head);
```

**示例**

```text
输入：head=1→2→3→4
输出：上中点 2，下中点 3；对应前驱 1、2
```

解释：不能把四个方法视为同一个接口。

**出处与版本差异**

- [课程源码：class09/Code01_LinkedListMid.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class09/Code01_LinkedListMid.java)。
- [LeetCode 对应题 876. 链表的中间结点（Middle of the Linked List）](https://leetcode.com/problems/middle-of-the-linked-list/)

课程版本说明：

- LeetCode 876：偶数长度返回下中点的 midOrDownMidNode 对应；其余三个方法是变式。

#### 思路

快指针每轮走两步，慢指针每轮走一步。通过两者的起始位置与循环结束条件，分别停在上中点、下中点或它们的前驱。先用长度 1..5 的链表手算四个方法，确认方法之间的偏移。

#### 代码答案

```java
package class09;

import java.util.ArrayList;

public class Code01_LinkedListMid {

	public static class Node {
		public int value;
		public Node next;

		public Node(int v) {
			value = v;
		}
	}

	// head 头
	public static Node midOrUpMidNode(Node head) {
		if (head == null || head.next == null || head.next.next == null) {
			return head;
		}
		// 链表有3个点或以上
		Node slow = head.next;
		Node fast = head.next.next;
		while (fast.next != null && fast.next.next != null) {
			// 慢指针每轮移动一格，用快指针的到尾时机定位中部。
			slow = slow.next;
			// 快指针每轮移动两格；循环条件应先保证两步都能走。
			fast = fast.next.next;
		}
		return slow;
	}

	public static Node midOrDownMidNode(Node head) {
		if (head == null || head.next == null) {
			return head;
		}
		Node slow = head.next;
		Node fast = head.next;
		while (fast.next != null && fast.next.next != null) {
			// 慢指针每轮移动一格，用快指针的到尾时机定位中部。
			slow = slow.next;
			// 快指针每轮移动两格；循环条件应先保证两步都能走。
			fast = fast.next.next;
		}
		return slow;
	}

	public static Node midOrUpMidPreNode(Node head) {
		if (head == null || head.next == null || head.next.next == null) {
			return null;
		}
		Node slow = head;
		Node fast = head.next.next;
		while (fast.next != null && fast.next.next != null) {
			// 慢指针每轮移动一格，用快指针的到尾时机定位中部。
			slow = slow.next;
			// 快指针每轮移动两格；循环条件应先保证两步都能走。
			fast = fast.next.next;
		}
		return slow;
	}

	public static Node midOrDownMidPreNode(Node head) {
		if (head == null || head.next == null) {
			return null;
		}
		if (head.next.next == null) {
			return head;
		}
		Node slow = head;
		Node fast = head.next;
		while (fast.next != null && fast.next.next != null) {
			// 慢指针每轮移动一格，用快指针的到尾时机定位中部。
			slow = slow.next;
			// 快指针每轮移动两格；循环条件应先保证两步都能走。
			fast = fast.next.next;
		}
		return slow;
	}

	public static Node right1(Node head) {
		if (head == null) {
			return null;
		}
		Node cur = head;
		ArrayList<Node> arr = new ArrayList<>();
		while (cur != null) {
			arr.add(cur);
			cur = cur.next;
		}
		return arr.get((arr.size() - 1) / 2);
	}

	public static Node right2(Node head) {
		if (head == null) {
			return null;
		}
		Node cur = head;
		ArrayList<Node> arr = new ArrayList<>();
		while (cur != null) {
			arr.add(cur);
			cur = cur.next;
		}
		return arr.get(arr.size() / 2);
	}

	public static Node right3(Node head) {
		if (head == null || head.next == null || head.next.next == null) {
			return null;
		}
		Node cur = head;
		ArrayList<Node> arr = new ArrayList<>();
		while (cur != null) {
			arr.add(cur);
			cur = cur.next;
		}
		return arr.get((arr.size() - 3) / 2);
	}

	public static Node right4(Node head) {
		if (head == null || head.next == null) {
			return null;
		}
		Node cur = head;
		ArrayList<Node> arr = new ArrayList<>();
		while (cur != null) {
			arr.add(cur);
			cur = cur.next;
		}
		return arr.get((arr.size() - 2) / 2);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		Node test = null;
		test = new Node(0);
		test.next = new Node(1);
		test.next.next = new Node(2);
		test.next.next.next = new Node(3);
		test.next.next.next.next = new Node(4);
		test.next.next.next.next.next = new Node(5);
		test.next.next.next.next.next.next = new Node(6);
		test.next.next.next.next.next.next.next = new Node(7);
		test.next.next.next.next.next.next.next.next = new Node(8);

		Node ans1 = null;
		Node ans2 = null;

		ans1 = midOrUpMidNode(test);
		ans2 = right1(test);
		System.out.println(ans1 != null ? ans1.value : "无");
		System.out.println(ans2 != null ? ans2.value : "无");

		ans1 = midOrDownMidNode(test);
		ans2 = right2(test);
		System.out.println(ans1 != null ? ans1.value : "无");
		System.out.println(ans2 != null ? ans2.value : "无");

		ans1 = midOrUpMidPreNode(test);
		ans2 = right3(test);
		System.out.println(ans1 != null ? ans1.value : "无");
		System.out.println(ans2 != null ? ans2.value : "无");

		ans1 = midOrDownMidPreNode(test);
		ans2 = right4(test);
		System.out.println(ans1 != null ? ans1.value : "无");
		System.out.println(ans2 != null ? ans2.value : "无");

	}
```

#### 题解

**为什么正确**

每轮快慢位移比为 2:1，快指针接近尾部时慢指针到达中部。初始相差一格会决定偶数长度时落在两个中点中的哪一个；提前停止一轮则可获得中点前驱。

**复杂度**

每个方法 O(N) 时间、O(1) 额外空间。

**边界与易错点**

偶数长度不存在唯一中点，必须说清上中点还是下中点。短表的前驱可能不存在，返回 null；LeetCode 876 要求偶数时下中点。

### 9.6 判断链表是否为回文结构

#### 题目

给定单链表头节点，判断从头到尾的节点值序列是否构成回文。

**输入、输出与约束**

输入无环链表，空表和单节点表视为回文；常数空间版本返回前恢复输入。

**函数签名（课程入口）**

```java
public static boolean isPalindrome1(Node head);
public static boolean isPalindrome2(Node head);
public static boolean isPalindrome3(Node head);
public static void printLinkedList(Node node);
```

**示例**

```text
输入：head=1→2→3→2→1
输出：true
```

解释：两侧成对相等，中间的 3 独立。

**出处与版本差异**

- [课程源码：class09/Code02_IsPalindromeList.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class09/Code02_IsPalindromeList.java)。
- [LeetCode 原题 234. 回文链表（Palindrome Linked List）](https://leetcode.com/problems/palindrome-linked-list/)

#### 思路

基准实现把全部节点或后半段放入栈，反向弹出与前半段比较。常数空间版本用快慢指针定位中部，反转后半段后同步比较两侧，最后再次反转后半段并接回原结构。

#### 代码答案

```java
package class09;

import java.util.Stack;

public class Code02_IsPalindromeList {

	public static class Node {
		public int value;
		public Node next;

		public Node(int data) {
			this.value = data;
		}
	}

	// need n extra space
	public static boolean isPalindrome1(Node head) {
		Stack<Node> stack = new Stack<Node>();
		Node cur = head;
		while (cur != null) {
			stack.push(cur);
			cur = cur.next;
		}
		while (head != null) {
			if (head.value != stack.pop().value) {
				return false;
			}
			head = head.next;
		}
		return true;
	}

	// need n/2 extra space
	public static boolean isPalindrome2(Node head) {
		if (head == null || head.next == null) {
			return true;
		}
		Node right = head.next;
		Node cur = head;
		while (cur.next != null && cur.next.next != null) {
			right = right.next;
			cur = cur.next.next;
		}
		Stack<Node> stack = new Stack<Node>();
		while (right != null) {
			stack.push(right);
			right = right.next;
		}
		while (!stack.isEmpty()) {
			if (head.value != stack.pop().value) {
				return false;
			}
			head = head.next;
		}
		return true;
	}

	// need O(1) extra space
	public static boolean isPalindrome3(Node head) {
		if (head == null || head.next == null) {
			return true;
		}
		Node n1 = head;
		Node n2 = head;
		while (n2.next != null && n2.next.next != null) { // find mid node
			n1 = n1.next; // n1 -> mid
			n2 = n2.next.next; // n2 -> end
		}
		// n1 中点

		n2 = n1.next; // n2 -> right part first node
		// 先断开中点与后半段，后续反转不会形成环。
		n1.next = null; // mid.next -> null
		Node n3 = null;
		while (n2 != null) { // right part convert
			n3 = n2.next; // n3 -> save next node
			n2.next = n1; // next of right node convert
			n1 = n2; // n1 move
			n2 = n3; // n2 move
		}
		// 保存反转后半段的头，比较完成后还需要用它恢复链表。
		n3 = n1; // n3 -> save last node
		n2 = head;// n2 -> left first node
		boolean res = true;
		while (n1 != null && n2 != null) { // check palindrome
			if (n1.value != n2.value) {
				// 记录不相等结论，但继续走恢复流程，避免改变调用者的链表。
				res = false;
				break;
			}
			n1 = n1.next; // left to mid
			n2 = n2.next; // right to mid
		}
		n1 = n3.next;
		n3.next = null;
		while (n1 != null) { // recover list
			n2 = n1.next;
			n1.next = n3;
			// 保存反转后半段的头，比较完成后还需要用它恢复链表。
			n3 = n1;
			n1 = n2;
		}
		return res;
	}

	public static void printLinkedList(Node node) {
		System.out.print("Linked List: ");
		while (node != null) {
			System.out.print(node.value + " ");
			node = node.next;
		}
		System.out.println();
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {

		Node head = null;
		printLinkedList(head);
		System.out.print(isPalindrome1(head) + " | ");
		System.out.print(isPalindrome2(head) + " | ");
		System.out.println(isPalindrome3(head) + " | ");
		printLinkedList(head);
		System.out.println("=========================");

		head = new Node(1);
		printLinkedList(head);
		System.out.print(isPalindrome1(head) + " | ");
		System.out.print(isPalindrome2(head) + " | ");
		System.out.println(isPalindrome3(head) + " | ");
		printLinkedList(head);
		System.out.println("=========================");

		head = new Node(1);
		head.next = new Node(2);
		printLinkedList(head);
		System.out.print(isPalindrome1(head) + " | ");
		System.out.print(isPalindrome2(head) + " | ");
		System.out.println(isPalindrome3(head) + " | ");
		printLinkedList(head);
		System.out.println("=========================");

		head = new Node(1);
		head.next = new Node(1);
		printLinkedList(head);
		System.out.print(isPalindrome1(head) + " | ");
		System.out.print(isPalindrome2(head) + " | ");
		System.out.println(isPalindrome3(head) + " | ");
		printLinkedList(head);
		System.out.println("=========================");

		head = new Node(1);
		head.next = new Node(2);
		head.next.next = new Node(3);
		printLinkedList(head);
		System.out.print(isPalindrome1(head) + " | ");
		System.out.print(isPalindrome2(head) + " | ");
		System.out.println(isPalindrome3(head) + " | ");
		printLinkedList(head);
		System.out.println("=========================");

		head = new Node(1);
		head.next = new Node(2);
		head.next.next = new Node(1);
		printLinkedList(head);
		System.out.print(isPalindrome1(head) + " | ");
		System.out.print(isPalindrome2(head) + " | ");
		System.out.println(isPalindrome3(head) + " | ");
		printLinkedList(head);
		System.out.println("=========================");

		head = new Node(1);
		head.next = new Node(2);
		head.next.next = new Node(3);
		head.next.next.next = new Node(1);
		printLinkedList(head);
		System.out.print(isPalindrome1(head) + " | ");
		System.out.print(isPalindrome2(head) + " | ");
		System.out.println(isPalindrome3(head) + " | ");
		printLinkedList(head);
		System.out.println("=========================");

		head = new Node(1);
		head.next = new Node(2);
		head.next.next = new Node(2);
		head.next.next.next = new Node(1);
		printLinkedList(head);
		System.out.print(isPalindrome1(head) + " | ");
		System.out.print(isPalindrome2(head) + " | ");
		System.out.println(isPalindrome3(head) + " | ");
		printLinkedList(head);
		System.out.println("=========================");

		head = new Node(1);
		head.next = new Node(2);
		head.next.next = new Node(3);
		head.next.next.next = new Node(2);
		head.next.next.next.next = new Node(1);
		printLinkedList(head);
		System.out.print(isPalindrome1(head) + " | ");
		System.out.print(isPalindrome2(head) + " | ");
		System.out.println(isPalindrome3(head) + " | ");
		printLinkedList(head);
		System.out.println("=========================");

	}
```

#### 题解

**为什么正确**

回文等价于第 i 个值与倒数第 i 个值相等。栈弹出或后半段反转都让这两种位置可以同步访问；所有成对位置相等即为回文。恢复操作不影响判断结果，但保持调用者的输入结构。

**复杂度**

各版本时间 O(N)；栈版本空间 O(N)，原地反转版本额外空间 O(1)。

**边界与易错点**

发现不相等后仍需恢复链表，不能直接提前返回。奇数长度中间元素无需与其他节点配对。

### 9.7 按给定值划分链表

#### 题目

给定链表头节点和枢轴值 `pivot`，重新连接节点，使小于、等于、大于 `pivot` 的节点依次出现，并保持各区域内部的相对次序。

**输入、输出与约束**

输入无环链表和 pivot；返回三段划分后的头，要求稳定时使用 listPartition2。

**函数签名（课程入口）**

```java
public static Node listPartition1(Node head, int pivot);
public static void arrPartition(Node[] nodeArr, int pivot);
public static Node listPartition2(Node head, int pivot);
public static void printLinkedList(Node node);
```

**示例**

```text
输入：head=1→4→3→2→5→2, pivot=3
输出：稳定三段版：1→2→2→3→4→5
```

解释：小于、等于、大于区内各自保持原相对次序。

**出处与版本差异**

- [课程源码：class09/Code03_SmallerEqualBigger.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class09/Code03_SmallerEqualBigger.java)。
- [LeetCode 对应题 86. 分隔链表（Partition List）](https://leetcode.com/problems/partition-list/)

课程版本说明：

- LeetCode 86：相关变式：原题稳定分为小于 x 与不小于 x 两段，课程分为小于、等于、大于三段，输出顺序可能不同。

#### 思路

数组版把节点放入数组后做荷兰国旗划分，不保证稳定。链表版维护小于、等于、大于三个区域的头尾，按原顺序逐个追加节点，最后连接非空区域。

#### 代码答案

```java
package class09;

public class Code03_SmallerEqualBigger {

	public static class Node {
		public int value;
		public Node next;

		public Node(int data) {
			this.value = data;
		}
	}

	public static Node listPartition1(Node head, int pivot) {
		if (head == null) {
			return head;
		}
		Node cur = head;
		int i = 0;
		while (cur != null) {
			i++;
			cur = cur.next;
		}
		Node[] nodeArr = new Node[i];
		i = 0;
		cur = head;
		for (i = 0; i != nodeArr.length; i++) {
			nodeArr[i] = cur;
			cur = cur.next;
		}
		arrPartition(nodeArr, pivot);
		for (i = 1; i != nodeArr.length; i++) {
			nodeArr[i - 1].next = nodeArr[i];
		}
		nodeArr[i - 1].next = null;
		return nodeArr[0];
	}

	public static void arrPartition(Node[] nodeArr, int pivot) {
		int small = -1;
		int big = nodeArr.length;
		int index = 0;
		while (index != big) {
			if (nodeArr[index].value < pivot) {
				swap(nodeArr, ++small, index++);
			} else if (nodeArr[index].value == pivot) {
				index++;
			} else {
				swap(nodeArr, --big, index);
			}
		}
	}

	public static void swap(Node[] nodeArr, int a, int b) {
		Node tmp = nodeArr[a];
		nodeArr[a] = nodeArr[b];
		nodeArr[b] = tmp;
	}

	public static Node listPartition2(Node head, int pivot) {
		Node sH = null; // small head
		Node sT = null; // small tail
		Node eH = null; // equal head
		Node eT = null; // equal tail
		Node mH = null; // big head
		Node mT = null; // big tail
		Node next = null; // save next node
		// every node distributed to three lists
		while (head != null) {
			next = head.next;
			// 先让当前节点脱离旧链，防止旧后继把不同区域串在一起。
			head.next = null;
			if (head.value < pivot) {
				if (sH == null) {
					sH = head;
					sT = head;
				} else {
					// 在小于区尾部追加，保持小于区内的原顺序。
					sT.next = head;
					sT = head;
				}
			} else if (head.value == pivot) {
				if (eH == null) {
					eH = head;
					eT = head;
				} else {
					// 在等于区尾部追加，保留同值节点的相对次序。
					eT.next = head;
					eT = head;
				}
			} else {
				if (mH == null) {
					mH = head;
					mT = head;
				} else {
					mT.next = head;
					mT = head;
				}
			}
			head = next;
		}
		// 小于区域的尾巴，连等于区域的头，等于区域的尾巴连大于区域的头
		if (sT != null) { // 如果有小于区域
			sT.next = eH;
			eT = eT == null ? sT : eT; // 下一步，谁去连大于区域的头，谁就变成eT
		}
		// 下一步，一定是需要用eT 去接 大于区域的头
		// 有等于区域，eT -> 等于区域的尾结点
		// 无等于区域，eT -> 小于区域的尾结点
		// eT 尽量不为空的尾巴节点
		if (eT != null) { // 如果小于区域和等于区域，不是都没有
			eT.next = mH;
		}
		return sH != null ? sH : (eH != null ? eH : mH);
	}

	public static void printLinkedList(Node node) {
		System.out.print("Linked List: ");
		while (node != null) {
			System.out.print(node.value + " ");
			node = node.next;
		}
		System.out.println();
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		Node head1 = new Node(7);
		head1.next = new Node(9);
		head1.next.next = new Node(1);
		head1.next.next.next = new Node(8);
		head1.next.next.next.next = new Node(5);
		head1.next.next.next.next.next = new Node(2);
		head1.next.next.next.next.next.next = new Node(5);
		printLinkedList(head1);
		// head1 = listPartition1(head1, 4);
		head1 = listPartition2(head1, 5);
		printLinkedList(head1);

	}
```

#### 题解

**为什么正确**

每个节点按值只属于一个区域；尾插保留该区域内原先的顺序。三段按值域顺序拼接保证整体分区，且每个节点出现一次。

**复杂度**

时间 O(N)；数组版额外空间 O(N)，六指针链表版 O(1)。

**边界与易错点**

稳定性只由链表版保证。处理每个节点前先断开旧 next；中间区域可能为空，尾部最终要指向 null。本题三段划分与 LeetCode 86 的两段稳定划分并不完全等价。

### 9.8 复制带随机指针的链表

#### 题目

给定一条每个节点都含 `next` 与 `random` 指针的链表，构造其深拷贝；复制链表中的任何指针都不能指向原链表节点。

**输入、输出与约束**

next 构成无环链，random 指向链内节点或 null；返回完全独立的深拷贝。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：A.next=B，A.random=B，B.random=A
输出：A′.next=B′，A′.random=B′，B′.random=A′
```

解释：新旧节点身份不同，但连接关系一一对应。

**出处与版本差异**

- [课程源码：class09/Code04_CopyListWithRandom.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class09/Code04_CopyListWithRandom.java)。
- [LeetCode 原题 138. 随机链表的复制（Copy List with Random Pointer）](https://leetcode.com/problems/copy-list-with-random-pointer/)

#### 思路

映射版先建立“原节点→复制节点”，再按映射连接 next 与 random。常数辅助空间版把复制节点插到原节点后面，使原节点的 next 临时充当映射；填好 random 后，再把交错链拆成原链和复制链。

#### 代码答案

```java
package class09;

import java.util.HashMap;

// 测试链接 : https://leetcode.com/problems/copy-list-with-random-pointer/
public class Code04_CopyListWithRandom {

	public static class Node {
		int val;
		Node next;
		Node random;

		public Node(int val) {
			this.val = val;
			this.next = null;
			this.random = null;
		}
	}

	public static Node copyRandomList1(Node head) {
		// key 老节点
		// value 新节点
		HashMap<Node, Node> map = new HashMap<Node, Node>();
		Node cur = head;
		while (cur != null) {
			map.put(cur, new Node(cur.val));
			cur = cur.next;
		}
		cur = head;
		while (cur != null) {
			// cur 老
			// map.get(cur) 新
			// 新.next ->  cur.next克隆节点找到
			map.get(cur).next = map.get(cur.next);
			map.get(cur).random = map.get(cur.random);
			cur = cur.next;
		}
		return map.get(head);
	}

	public static Node copyRandomList2(Node head) {
		if (head == null) {
			return null;
		}
		Node cur = head;
		Node next = null;
		// 1 -> 2 -> 3 -> null
		// 1 -> 1' -> 2 -> 2' -> 3 -> 3'
		while (cur != null) {
			next = cur.next;
			cur.next = new Node(cur.val);
			cur.next.next = next;
			cur = next;
		}
		cur = head;
		Node copy = null;
		// 1 1' 2 2' 3 3'
		// 依次设置 1' 2' 3' random指针
		while (cur != null) {
			next = cur.next.next;
			copy = cur.next;
			// 交错结构把原 random 目标的 next 变成其副本，可直接映射。
			copy.random = cur.random != null ? cur.random.next : null;
			cur = next;
		}
		Node res = head.next;
		cur = head;
		// 老 新 混在一起，next方向上，random正确
		// next方向上，把新老链表分离
		while (cur != null) {
			next = cur.next.next;
			copy = cur.next;
			// 拆链时恢复原节点的 next，避免调用后原链仍夹着副本。
			cur.next = next;
			copy.next = next != null ? next.next : null;
			cur = next;
		}
		return res;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：random 可能为空，不能直接访问其 next。拆链必须同时恢复原链；不能只复制值而让新 random 继续引用原节点。

本条未附独立随机对数器。

#### 题解

**为什么正确**

交错结构中 x.next 总是 x 的副本，因此 x.random.next 就是 random 目标的副本。所有引用均转向副本后再拆链，复制图的结构和值与原图相同，且没有指针回到原节点。

**复杂度**

时间 O(N)；映射版辅助空间 O(N)，交错版除输出节点外额外空间 O(1)。输出节点本身为 O(N)。

**边界与易错点**

random 可能为空，不能直接访问其 next。拆链必须同时恢复原链；不能只复制值而让新 random 继续引用原节点。


<a id="course-10"></a>

## 第 10 课：链表相交与二叉树遍历

### 10.1 从前序与中序遍历序列构造二叉树（新手班前置）

#### 题目

给定一棵无重复值二叉树的前序和中序遍历数组，构造并返回这棵二叉树。

**输入、输出与约束**

输入等长且来自同一棵树的前序、中序数组，节点值互不重复；返回树根。

**函数签名（课程入口）**

```java
public static TreeNode buildTree1(int[] pre, int[] in);
public static TreeNode buildTree2(int[] pre, int[] in);
```

**示例**

```text
输入：pre=[3,9,20,15,7], in=[9,3,15,20,7]
输出：层序 [3,9,20,null,null,15,7]
```

解释：根 3 的中序左侧只有 9，因此左子树只占前序一个位置。

**出处与版本差异**

- [课程源码：class06/Code05_ConstructBinaryTreeFromPreorderAndInorderTraversal.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class06/Code05_ConstructBinaryTreeFromPreorderAndInorderTraversal.java)。
- [LeetCode 原题 105. 从前序与中序遍历序列构造二叉树（Construct Binary Tree from Preorder and Inorder Traversal）](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

#### 思路

前序区间第一个值是根；在对应中序区间找到它，其左侧长度就是左子树节点数。用该长度划分前序左右区间，并递归构造两棵子树。buildTree1 每次线性找根，buildTree2 预建 值到中序下标的映射。

#### 代码答案

```java
package class06;

import java.util.HashMap;

//测试链接：https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal
public class Code05_ConstructBinaryTreeFromPreorderAndInorderTraversal {

	public static class TreeNode {
		int val;
		TreeNode left;
		TreeNode right;

		TreeNode(int val) {
			this.val = val;
		}
	}

	public static TreeNode buildTree1(int[] pre, int[] in) {
		if (pre == null || in == null || pre.length != in.length) {
			return null;
		}
		return f(pre, 0, pre.length - 1, in, 0, in.length - 1);
	}

	// 有一棵树，先序结果是pre[L1...R1]，中序结果是in[L2...R2]
	// 请建出整棵树返回头节点
	public static TreeNode f(int[] pre, int L1, int R1, int[] in, int L2, int R2) {
		// 空前序区间对应空子树，返回 null。
		if (L1 > R1) {
			return null;
		}
		// 当前前序区间的首值一定是该子树根。
		TreeNode head = new TreeNode(pre[L1]);
		if (L1 == R1) {
			return head;
		}
		int find = L2;
		while (in[find] != pre[L1]) {
			find++;
		}
		head.left = f(pre, L1 + 1, L1 + find - L2, in, L2, find - 1);
		head.right = f(pre, L1 + find - L2 + 1, R1, in, find + 1, R2);
		return head;
	}

	public static TreeNode buildTree2(int[] pre, int[] in) {
		if (pre == null || in == null || pre.length != in.length) {
			return null;
		}
		HashMap<Integer, Integer> valueIndexMap = new HashMap<>();
		for (int i = 0; i < in.length; i++) {
			valueIndexMap.put(in[i], i);
		}
		return g(pre, 0, pre.length - 1, in, 0, in.length - 1, valueIndexMap);
	}

	// 有一棵树，先序结果是pre[L1...R1]，中序结果是in[L2...R2]
	// 请建出整棵树返回头节点
	public static TreeNode g(int[] pre, int L1, int R1, int[] in, int L2, int R2,
			HashMap<Integer, Integer> valueIndexMap) {
		// 空前序区间对应空子树，返回 null。
		if (L1 > R1) {
			return null;
		}
		// 当前前序区间的首值一定是该子树根。
		TreeNode head = new TreeNode(pre[L1]);
		if (L1 == R1) {
			return head;
		}
		// 用值唯一定位中序根，下标差决定左子树规模。
		int find = valueIndexMap.get(pre[L1]);
		head.left = g(pre, L1 + 1, L1 + find - L2, in, L2, find - 1, valueIndexMap);
		head.right = g(pre, L1 + find - L2 + 1, R1, in, find + 1, R2, valueIndexMap);
		return head;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：左子树长度为 find-L2，前序左段结束于 L1+find-L2。重复值或不相容的遍历序列不属于本实现合法输入，源码没有完整验证。

本条未附独立随机对数器。

#### 题解

**为什么正确**

节点值互不重复时，根在中序中的位置唯一，从而左右子树的节点集合和长度也唯一。前序保证根后依次为左、右子树，因此两种遍历对子问题的划分一致。空区间与单节点提供递归终点。

**复杂度**

buildTree1 最坏时间 O(N²)；buildTree2 平均时间 O(N)。两者输出 O(N) 节点，递归栈 O(H)，映射版另需 O(N) 哈希空间。

**边界与易错点**

左子树长度为 find-L2，前序左段结束于 L1+find-L2。重复值或不相容的遍历序列不属于本实现合法输入，源码没有完整验证。

### 10.2 两个链表的第一个相交节点

#### 题目

给定两条可能有环也可能无环的单链表，返回它们第一个相交节点；若不相交则返回 `null`。

**输入、输出与约束**

两条 next 链可有环；无交点返回 null，同环不同入口时接受源码返回的入环节点。

**函数签名（课程入口）**

```java
public static Node getIntersectNode(Node head1, Node head2);
public static Node getLoopNode(Node head);
public static Node noLoop(Node head1, Node head2);
public static Node bothLoop(Node head1, Node loop1, Node head2, Node loop2);
```

**示例**

```text
输入：A=1→共享节点7→8，B=2→3→共享节点7→8
输出：共享节点 7
```

解释：长表先走一步后同步移动，即在同一身份节点相遇。

**出处与版本差异**

- [课程源码：class10/Code01_FindFirstIntersectNode.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class10/Code01_FindFirstIntersectNode.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先用快慢指针判断两表是否有环并找到入环节点。都无环：比较尾节点并让长表先走长度差。只有一表有环：不可能相交。都有环：入口相同时，只在入口前对齐；入口不同时，沿一个环走一圈检查是否遇到另一个入口。

#### 代码答案

```java
package class10;

public class Code01_FindFirstIntersectNode {

	public static class Node {
		public int value;
		public Node next;

		public Node(int data) {
			this.value = data;
		}
	}

	public static Node getIntersectNode(Node head1, Node head2) {
		if (head1 == null || head2 == null) {
			return null;
		}
		Node loop1 = getLoopNode(head1);
		Node loop2 = getLoopNode(head2);
		// 两表都无环时，交点一旦出现就共享直到同一尾节点。
		if (loop1 == null && loop2 == null) {
			return noLoop(head1, head2);
		}
		// 两表都有环，需要进一步区分同入口与不同入口。
		if (loop1 != null && loop2 != null) {
			return bothLoop(head1, loop1, head2, loop2);
		}
		return null;
	}

	// 找到链表第一个入环节点，如果无环，返回null
	public static Node getLoopNode(Node head) {
		if (head == null || head.next == null || head.next.next == null) {
			return null;
		}
		// n1 慢  n2 快
		Node slow = head.next; // n1 -> slow
		Node fast = head.next.next; // n2 -> fast
		while (slow != fast) {
			if (fast.next == null || fast.next.next == null) {
				return null;
			}
			fast = fast.next.next;
			slow = slow.next;
		}
		// slow fast  相遇
		fast = head; // n2 -> walk again from head
		while (slow != fast) {
			slow = slow.next;
			fast = fast.next;
		}
		return slow;
	}

	// 如果两个链表都无环，返回第一个相交节点，如果不想交，返回null
	public static Node noLoop(Node head1, Node head2) {
		if (head1 == null || head2 == null) {
			return null;
		}
		Node cur1 = head1;
		Node cur2 = head2;
		int n = 0;
		while (cur1.next != null) {
			n++;
			cur1 = cur1.next;
		}
		while (cur2.next != null) {
			n--;
			cur2 = cur2.next;
		}
		if (cur1 != cur2) {
			return null;
		}
		// n  :  链表1长度减去链表2长度的值
		// 先让较长的无环前缀走掉长度差，使两者距终点相同。
		cur1 = n > 0 ? head1 : head2; // 谁长，谁的头变成cur1
		cur2 = cur1 == head1 ? head2 : head1; // 谁短，谁的头变成cur2
		n = Math.abs(n);
		while (n != 0) {
			n--;
			cur1 = cur1.next;
		}
		while (cur1 != cur2) {
			cur1 = cur1.next;
			cur2 = cur2.next;
		}
		return cur1;
	}

	// 两个有环链表，返回第一个相交节点，如果不想交返回null
	public static Node bothLoop(Node head1, Node loop1, Node head2, Node loop2) {
		Node cur1 = null;
		Node cur2 = null;
		if (loop1 == loop2) {
			cur1 = head1;
			cur2 = head2;
			int n = 0;
			while (cur1 != loop1) {
				n++;
				cur1 = cur1.next;
			}
			while (cur2 != loop2) {
				n--;
				cur2 = cur2.next;
			}
			// 先让较长的无环前缀走掉长度差，使两者距终点相同。
			cur1 = n > 0 ? head1 : head2;
			cur2 = cur1 == head1 ? head2 : head1;
			n = Math.abs(n);
			while (n != 0) {
				n--;
				cur1 = cur1.next;
			}
			while (cur1 != cur2) {
				cur1 = cur1.next;
				cur2 = cur2.next;
			}
			return cur1;
		} else {
			cur1 = loop1.next;
			while (cur1 != loop1) {
				if (cur1 == loop2) {
					return loop1;
				}
				cur1 = cur1.next;
			}
			return null;
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		// 1->2->3->4->5->6->7->null
		Node head1 = new Node(1);
		head1.next = new Node(2);
		head1.next.next = new Node(3);
		head1.next.next.next = new Node(4);
		head1.next.next.next.next = new Node(5);
		head1.next.next.next.next.next = new Node(6);
		head1.next.next.next.next.next.next = new Node(7);

		// 0->9->8->6->7->null
		Node head2 = new Node(0);
		head2.next = new Node(9);
		head2.next.next = new Node(8);
		head2.next.next.next = head1.next.next.next.next.next; // 8->6
		System.out.println(getIntersectNode(head1, head2).value);

		// 1->2->3->4->5->6->7->4...
		head1 = new Node(1);
		head1.next = new Node(2);
		head1.next.next = new Node(3);
		head1.next.next.next = new Node(4);
		head1.next.next.next.next = new Node(5);
		head1.next.next.next.next.next = new Node(6);
		head1.next.next.next.next.next.next = new Node(7);
		head1.next.next.next.next.next.next = head1.next.next.next; // 7->4

		// 0->9->8->2...
		head2 = new Node(0);
		head2.next = new Node(9);
		head2.next.next = new Node(8);
		head2.next.next.next = head1.next; // 8->2
		System.out.println(getIntersectNode(head1, head2).value);

		// 0->9->8->6->4->5->6..
		head2 = new Node(0);
		head2.next = new Node(9);
		head2.next.next = new Node(8);
		head2.next.next.next = head1.next.next.next.next.next; // 8->6
		System.out.println(getIntersectNode(head1, head2).value);

	}
```

#### 题解

**为什么正确**

单链表相交后 next 唯一，后续路径必完全共用，所以无环表尾节点相同是相交必要充分条件。一有环一无环若相交会共享环而矛盾；入口不同但同环时二者共享环，却不一定存在对双方都唯一的“首个”交点。

**复杂度**

时间 O(N+M)，额外空间 O(1)。

**边界与易错点**

比较节点身份而不是节点值。同环不同入口时，源码返回其中一个入口作为合法交点；题面不能要求不存在的唯一首交点。

### 10.3 二叉树递归遍历

#### 题目

输入合法无环二叉树，可为空；按选定方法打印遍历值。

**输入、输出与约束**

输入合法无环二叉树，可为空；按选定方法打印遍历值。

**函数签名（课程入口）**

```java
public static void pre(Node head);
public static void pos(Node head);
```

**示例**

```text
输入：root=[1,2,3]
输出：前序 1 2 3；中序 2 1 3；后序 2 3 1
```

解释：根节点输出分别放在左递归前、两次递归之间、右递归后。

**出处与版本差异**

- [课程源码：class10/Code02_RecursiveTraversalBT.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class10/Code02_RecursiveTraversalBT.java)。
- [LeetCode 对应题 144. 二叉树的前序遍历（Binary Tree Preorder Traversal）](https://leetcode.com/problems/binary-tree-preorder-traversal/)
- [LeetCode 对应题 94. 二叉树的中序遍历（Binary Tree Inorder Traversal）](https://leetcode.com/problems/binary-tree-inorder-traversal/)
- [LeetCode 对应题 145. 二叉树的后序遍历（Binary Tree Postorder Traversal）](https://leetcode.com/problems/binary-tree-postorder-traversal/)

课程版本说明：

- LeetCode 144：前序部分对应。
- LeetCode 94：中序部分对应。
- LeetCode 145：后序部分对应。

#### 思路

一个非空节点的递归流程依次为：到达自身、递归左树、递归右树。在递归左树前打印得到前序；在左右递归之间打印得到中序；在递归右树后打印得到后序。

#### 代码答案

```java
package class10;

public class Code02_RecursiveTraversalBT {

	public static class Node {
		public int value;
		public Node left;
		public Node right;

		public Node(int v) {
			value = v;
		}
	}

	public static void f(Node head) {
		if (head == null) {
			return;
		}
		// 1
		f(head.left);
		// 2
		f(head.right);
		// 3
	}

	// 先序打印所有节点
	public static void pre(Node head) {
		if (head == null) {
			return;
		}
		System.out.println(head.value);
		// 前序已先访问根，再完整访问左子树。
		pre(head.left);
		pre(head.right);
	}

	public static void in(Node head) {
		if (head == null) {
			return;
		}
		// 中序先完成左子树，返回后才访问当前根。
		in(head.left);
		System.out.println(head.value);
		in(head.right);
	}

	public static void pos(Node head) {
		if (head == null) {
			return;
		}
		pos(head.left);
		// 后序需等右子树也完成，才能输出当前根。
		pos(head.right);
		System.out.println(head.value);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		Node head = new Node(1);
		head.left = new Node(2);
		head.right = new Node(3);
		head.left.left = new Node(4);
		head.left.right = new Node(5);
		head.right.left = new Node(6);
		head.right.right = new Node(7);

		pre(head);
		System.out.println("========");
		in(head);
		System.out.println("========");
		pos(head);
		System.out.println("========");

	}
```

#### 题解

**为什么正确**

每次递归完整处理一棵子树。改变根节点输出相对于两次递归的位置，分别产生“根左右”“左根右”“左右根”，子树内部也遵守同一顺序。

**复杂度**

每种遍历 O(N) 时间，递归栈 O(H)，退化链 H=N。

**边界与易错点**

空节点直接返回。遍历顺序指输出时机，不是函数进入顺序；源码的三种方法分别打印，不返回列表。

### 10.4 二叉树非递归遍历

#### 题目

输入无环二叉树；输出次序与相应递归遍历一致。

**输入、输出与约束**

输入无环二叉树；输出次序与相应递归遍历一致。

**函数签名（课程入口）**

```java
public static void pre(Node head);
public static void pos1(Node head);
public static void pos2(Node h);
```

**示例**

```text
输入：root=[1,2,3]
输出：前序 1 2 3；中序 2 1 3；后序 2 3 1
```

解释：显式栈模拟对应递归尚未完成的工作。

**出处与版本差异**

- [课程源码：class10/Code03_UnRecursiveTraversalBT.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class10/Code03_UnRecursiveTraversalBT.java)。
- [LeetCode 对应题 144. 二叉树的前序遍历（Binary Tree Preorder Traversal）](https://leetcode.com/problems/binary-tree-preorder-traversal/)
- [LeetCode 对应题 94. 二叉树的中序遍历（Binary Tree Inorder Traversal）](https://leetcode.com/problems/binary-tree-inorder-traversal/)
- [LeetCode 对应题 145. 二叉树的后序遍历（Binary Tree Postorder Traversal）](https://leetcode.com/problems/binary-tree-postorder-traversal/)

课程版本说明：

- LeetCode 144：前序部分对应。
- LeetCode 94：中序部分对应。
- LeetCode 145：后序部分对应。

#### 思路

前序用栈弹出根后输出，先压右再压左。中序持续压入左链，无法再向左时弹出并输出，然后转右。后序可用两个栈将“根右左”逆序，或用一个栈与上次完成节点记录判断子树是否处理完。

#### 代码答案

```java
package class10;

import java.util.Stack;

public class Code03_UnRecursiveTraversalBT {

	public static class Node {
		public int value;
		public Node left;
		public Node right;

		public Node(int v) {
			value = v;
		}
	}

	public static void pre(Node head) {
		System.out.print("pre-order: ");
		if (head != null) {
			Stack<Node> stack = new Stack<Node>();
			stack.push(head);
			while (!stack.isEmpty()) {
				head = stack.pop();
				System.out.print(head.value + " ");
				if (head.right != null) {
					// 前序先压右、再压左，后进先出使左子树先访问。
					stack.push(head.right);
				}
				if (head.left != null) {
					stack.push(head.left);
				}
			}
		}
		System.out.println();
	}

	public static void in(Node cur) {
		System.out.print("in-order: ");
		if (cur != null) {
			Stack<Node> stack = new Stack<Node>();
			while (!stack.isEmpty() || cur != null) {
				if (cur != null) {
					stack.push(cur);
					// 中序暂存根后一路走左，先找到本子树最先访问的节点。
					cur = cur.left;
				} else {
					cur = stack.pop();
					System.out.print(cur.value + " ");
					// 根已输出，接下来用同一规则处理右子树。
					cur = cur.right;
				}
			}
		}
		System.out.println();
	}

	public static void pos1(Node head) {
		System.out.print("pos-order: ");
		if (head != null) {
			Stack<Node> s1 = new Stack<Node>();
			Stack<Node> s2 = new Stack<Node>();
			s1.push(head);
			while (!s1.isEmpty()) {
				head = s1.pop(); // 头 右 左
				// 第一栈按根右左发现节点，第二栈将该顺序整体反转成左右根。
				s2.push(head);
				if (head.left != null) {
					s1.push(head.left);
				}
				if (head.right != null) {
					s1.push(head.right);
				}
			}
			// 左 右 头
			while (!s2.isEmpty()) {
				System.out.print(s2.pop().value + " ");
			}
		}
		System.out.println();
	}

	public static void pos2(Node h) {
		System.out.print("pos-order: ");
		if (h != null) {
			Stack<Node> stack = new Stack<Node>();
			stack.push(h);
			Node c = null;
			while (!stack.isEmpty()) {
				// 单栈后序先观察根但不弹出，必须确认两个子树都处理完。
				c = stack.peek();
				// h记录最近完成的子树根；尚未从任何子树返回时先深入左树。
				if (c.left != null && h != c.left && h != c.right) {
					stack.push(c.left);
				// 左树已经结束而右树尚未完成，接着深入右树。
				} else if (c.right != null && h != c.right) {
					stack.push(c.right);
				} else {
					System.out.print(stack.pop().value + " ");
					// 根完成输出后登记为最近访问节点，帮助父节点判断回溯阶段。
					h = c;
				}
			}
		}
		System.out.println();
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		Node head = new Node(1);
		head.left = new Node(2);
		head.right = new Node(3);
		head.left.left = new Node(4);
		head.left.right = new Node(5);
		head.right.left = new Node(6);
		head.right.right = new Node(7);

		pre(head);
		System.out.println("========");
		in(head);
		System.out.println("========");
		pos1(head);
		System.out.println("========");
		pos2(head);
		System.out.println("========");
	}
```

#### 题解

**为什么正确**

栈的后进先出把压入次序翻转，故前序要先压右。中序栈保存尚未访问但左子树正在处理的祖先。后序只有左右子树都完成后才弹出父节点，与递归返回时机一致。

**复杂度**

时间 O(N)。前序与中序栈 O(H)，后序双栈版 O(N)，单栈版 O(H)。

**边界与易错点**

前序压栈顺序写反会变成根右左。后序单栈不能只看左右孩子是否为空，还要知道它们是否已经处理。


<a id="course-11"></a>

## 第 11 课：二叉树的层序、序列化与结构题

### 11.1 二叉树的自底向上层序遍历（新手班前置）

#### 题目

给定二叉树根节点，返回其节点值的自底向上层序遍历结果；每一层从左到右排列。

**输入、输出与约束**

输入二叉树，可为空；普通方法打印值，自底向上方法返回 List<List<Integer>>。

**函数签名（课程入口）**

```java
public List<List<Integer>> levelOrderBottom(TreeNode root);
```

**示例**

```text
输入：root=[3,9,20,null,null,15,7]
输出：普通层序：3 9 20 15 7；自底向上：[[15,7],[9,20],[3]]
```

解释：只反转层的顺序，15 仍在 7 前。

**出处与版本差异**

- [课程源码：class07/Code01_BinaryTreeLevelOrderTraversalII.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class07/Code01_BinaryTreeLevelOrderTraversalII.java)。
- [LeetCode 原题 107. 二叉树的层序遍历 II（Binary Tree Level Order Traversal II）](https://leetcode.com/problems/binary-tree-level-order-traversal-ii/)

#### 思路

队列先放根，取出一个节点后依次加入左、右孩子。普通版本逐个打印；自底向上版本先固定当前 queue.size()，只取这一层的节点形成列表，并把整层插到结果头部。

#### 代码答案

```java
package class07;

import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

// 测试链接：https://leetcode.com/problems/binary-tree-level-order-traversal-ii
public class Code01_BinaryTreeLevelOrderTraversalII {

	public static class TreeNode {
		public int val;
		public TreeNode left;
		public TreeNode right;

		TreeNode(int val) {
			this.val = val;
		}
	}

	public List<List<Integer>> levelOrderBottom(TreeNode root) {
		List<List<Integer>> ans = new LinkedList<>();
		if (root == null) {
			return ans;
		}
		Queue<TreeNode> queue = new LinkedList<>();
		queue.add(root);
		while (!queue.isEmpty()) {
			// 固定当前层节点数，后续入队的孩子归下一层。
			int size = queue.size();
			List<Integer> curAns = new LinkedList<>();
			for (int i = 0; i < size; i++) {
				TreeNode curNode = queue.poll();
				curAns.add(curNode.val);
				if (curNode.left != null) {
					queue.add(curNode.left);
				}
				if (curNode.right != null) {
					queue.add(curNode.right);
				}
			}
			ans.add(0, curAns);
		}
		return ans;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：层循环开始时保存 size，不能在循环条件中反复读变化中的 queue.size()。普通课程打印版与 LeetCode 102 的分层返回接口不同。

本条未附独立随机对数器。

#### 题解

**为什么正确**

队列中父节点先于孩子，同层左侧节点先于右侧节点。固定层大小使新加入的孩子不会混入当前层；把完整层倒序排列只改变层次次序，不颠倒层内顺序。

**复杂度**

时间 O(N)，队列空间 O(W)，W 为最大层宽；返回层列表另需 O(N) 输出空间。普通打印版不分配结果列表。

**边界与易错点**

层循环开始时保存 size，不能在循环条件中反复读变化中的 queue.size()。普通课程打印版与 LeetCode 102 的分层返回接口不同。

### 11.2 二叉树层序遍历

#### 题目

给定二叉树根节点，按题目要求输出或返回前序、中序、后序或层序遍历结果。

**输入、输出与约束**

输入二叉树根；逐个打印层序节点值，不返回按层分组的列表，空树不输出。

**函数签名（课程入口）**

```java
public static void level(Node head);
```

**示例**

```text
输入：root=[1,2,3,4,null,null,5]
输出：依次打印 1、2、3、4、5
```

解释：前三个节点分别来自前两层，最后输出第三层的4、5。

**出处与版本差异**

- [课程源码：class11/Code01_LevelTraversalBT.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class11/Code01_LevelTraversalBT.java)。
- [LeetCode 对应题 102. 二叉树的层序遍历（Binary Tree Level Order Traversal）](https://leetcode.com/problems/binary-tree-level-order-traversal/)

课程版本说明：

- LeetCode 102：遍历模型对应；课程逐个打印，不返回原题要求的分层列表。

#### 思路

用队列保存已发现但尚未输出的节点。根先入队，每次出队打印当前值，再把非空左、右孩子按顺序入队。该方法直接打印从上到下、同层从左到右的节点；不划分结果列表，也不倒序层次。

#### 代码答案

```java
package class11;

import java.util.LinkedList;
import java.util.Queue;

public class Code01_LevelTraversalBT {

	public static class Node {
		public int value;
		public Node left;
		public Node right;

		public Node(int v) {
			value = v;
		}
	}

	public static void level(Node head) {
		if (head == null) {
			return;
		}
		Queue<Node> queue = new LinkedList<>();
		// 根最先入队，作为第0层唯一待处理节点。
		queue.add(head);
		while (!queue.isEmpty()) {
			// 取出最早发现的节点，使浅层先于深层处理。
			Node cur = queue.poll();
			System.out.println(cur.value);
			if (cur.left != null) {
				// 左孩子先登记，同层保持从左到右。
				queue.add(cur.left);
			}
			if (cur.right != null) {
				// 右孩子跟在左孩子后，等更早入队节点处理完再访问。
				queue.add(cur.right);
			}
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		Node head = new Node(1);
		head.left = new Node(2);
		head.right = new Node(3);
		head.left.left = new Node(4);
		head.left.right = new Node(5);
		head.right.left = new Node(6);
		head.right.right = new Node(7);

		level(head);
		System.out.println("========");
	}
```

#### 题解

**为什么正确**

队列使更浅层节点先出队，同一父节点的左孩子比右孩子先入队。当前层所有节点先于下一层展开，因此输出恰好是自顶向下的层序。

**复杂度**

时间 O(N)，队列空间 O(W)，W 为最大层宽。

**边界与易错点**

此方法与自底向上层序的返回值不同，不需要头插层列表。空孩子不能入队。

### 11.3 二叉树序列化与反序列化

#### 题目

设计二叉树的序列化与反序列化方法，使任意合法二叉树转换为序列后都能无歧义地恢复原结构。

**输入、输出与约束**

输入合法二叉树或由对应序列化方法产生的完整标记序列；返回无歧义恢复的树。

**函数签名（课程入口）**

```java
public static Queue<String> preSerial(Node head);
public static void pres(Node head, Queue<String> ans);
public static Queue<String> inSerial(Node head);
public static void ins(Node head, Queue<String> ans);
public static Queue<String> posSerial(Node head);
public static void poss(Node head, Queue<String> ans);
```

**示例**

```text
输入：root=1，左孩子2，右孩子空
输出：前序序列 [1,2,null,null,null]
```

解释：两个连续 null 结束节点 2，最后一个 null 表示根的右树为空。

**出处与版本差异**

- [课程源码：class11/Code02_SerializeAndReconstructTree.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class11/Code02_SerializeAndReconstructTree.java)。
- [LeetCode 对应题 297. 二叉树的序列化与反序列化（Serialize and Deserialize Binary Tree）](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)

#### 思路

只保存遍历值无法辨认树形，因此连空孩子也写出标记。前序反序列化依次读取根、左树、右树；后序从末尾反读时顺序变为根、右树、左树；层序按队列逐个为父节点恢复两个孩子。

#### 代码答案

```java
package class11;

import java.util.LinkedList;
import java.util.Queue;
import java.util.Stack;

public class Code02_SerializeAndReconstructTree {
    /*
     * 二叉树可以通过先序、后序或者按层遍历的方式序列化和反序列化，
     * 以下代码全部实现了。
     * 但是，二叉树无法通过中序遍历的方式实现序列化和反序列化
     * 因为不同的两棵树，可能得到同样的中序序列，即便补了空位置也可能一样。
     * 比如如下两棵树
     *         __2
     *        /
     *       1
     *       和
     *       1__
     *          \
     *           2
     * 补足空位置的中序遍历结果都是{ null, 1, null, 2, null}
     *
     * */
	public static class Node {
		public int value;
		public Node left;
		public Node right;

		public Node(int data) {
			this.value = data;
		}
	}

	public static Queue<String> preSerial(Node head) {
		Queue<String> ans = new LinkedList<>();
		pres(head, ans);
		return ans;
	}

	public static void pres(Node head, Queue<String> ans) {
		if (head == null) {
			// 空指针也占一个序列位置，防止不同树形得到同样的值序列。
			ans.add(null);
		} else {
			ans.add(String.valueOf(head.value));
			pres(head.left, ans);
			pres(head.right, ans);
		}
	}

	public static Queue<String> inSerial(Node head) {
		Queue<String> ans = new LinkedList<>();
		ins(head, ans);
		return ans;
	}

	public static void ins(Node head, Queue<String> ans) {
		if (head == null) {
			// 空指针也占一个序列位置，防止不同树形得到同样的值序列。
			ans.add(null);
		} else {
			ins(head.left, ans);
			ans.add(String.valueOf(head.value));
			ins(head.right, ans);
		}
	}

	public static Queue<String> posSerial(Node head) {
		Queue<String> ans = new LinkedList<>();
		poss(head, ans);
		return ans;
	}

	public static void poss(Node head, Queue<String> ans) {
		if (head == null) {
			// 空指针也占一个序列位置，防止不同树形得到同样的值序列。
			ans.add(null);
		} else {
			poss(head.left, ans);
			poss(head.right, ans);
			ans.add(String.valueOf(head.value));
		}
	}

	public static Node buildByPreQueue(Queue<String> prelist) {
		if (prelist == null || prelist.size() == 0) {
			return null;
		}
		return preb(prelist);
	}

	public static Node preb(Queue<String> prelist) {
		// 读取当前子树根标记；读取顺序必须与前序编码完全一致。
		String value = prelist.poll();
		if (value == null) {
			return null;
		}
		Node head = new Node(Integer.valueOf(value));
		head.left = preb(prelist);
		head.right = preb(prelist);
		return head;
	}

	public static Node buildByPosQueue(Queue<String> poslist) {
		if (poslist == null || poslist.size() == 0) {
			return null;
		}
		// 左右中  ->  stack(中右左)
		Stack<String> stack = new Stack<>();
		while (!poslist.isEmpty()) {
			stack.push(poslist.poll());
		}
		return posb(stack);
	}

	public static Node posb(Stack<String> posstack) {
		String value = posstack.pop();
		if (value == null) {
			return null;
		}
		Node head = new Node(Integer.valueOf(value));
		// 后序从尾部反读为根右左，因此先恢复右子树。
		head.right = posb(posstack);
		head.left = posb(posstack);
		return head;
	}

	public static Queue<String> levelSerial(Node head) {
		Queue<String> ans = new LinkedList<>();
		if (head == null) {
			// 空指针也占一个序列位置，防止不同树形得到同样的值序列。
			ans.add(null);
		} else {
			ans.add(String.valueOf(head.value));
			Queue<Node> queue = new LinkedList<Node>();
			queue.add(head);
			while (!queue.isEmpty()) {
				head = queue.poll(); // head 父   子
				if (head.left != null) {
					ans.add(String.valueOf(head.left.value));
					queue.add(head.left);
				} else {
					// 空指针也占一个序列位置，防止不同树形得到同样的值序列。
					ans.add(null);
				}
				if (head.right != null) {
					ans.add(String.valueOf(head.right.value));
					queue.add(head.right);
				} else {
					// 空指针也占一个序列位置，防止不同树形得到同样的值序列。
					ans.add(null);
				}
			}
		}
		return ans;
	}

	public static Node buildByLevelQueue(Queue<String> levelList) {
		if (levelList == null || levelList.size() == 0) {
			return null;
		}
		Node head = generateNode(levelList.poll());
		Queue<Node> queue = new LinkedList<Node>();
		if (head != null) {
			queue.add(head);
		}
		Node node = null;
		while (!queue.isEmpty()) {
			node = queue.poll();
			node.left = generateNode(levelList.poll());
			node.right = generateNode(levelList.poll());
			if (node.left != null) {
				queue.add(node.left);
			}
			if (node.right != null) {
				queue.add(node.right);
			}
		}
		return head;
	}

	public static Node generateNode(String val) {
		if (val == null) {
			return null;
		}
		return new Node(Integer.valueOf(val));
	}

	public static void printInOrder(Node head, int height, String to, int len) {
		if (head == null) {
			return;
		}
		printInOrder(head.right, height + 1, "v", len);
		String val = to + head.value + to;
		int lenM = val.length();
		int lenL = (len - lenM) / 2;
		int lenR = len - lenM - lenL;
		val = getSpace(lenL) + val + getSpace(lenR);
		System.out.println(getSpace(height * len) + val);
		printInOrder(head.left, height + 1, "^", len);
	}

	public static String getSpace(int num) {
		String space = " ";
		StringBuffer buf = new StringBuffer("");
		for (int i = 0; i < num; i++) {
			buf.append(space);
		}
		return buf.toString();
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static Node generateRandomBST(int maxLevel, int maxValue) {
		return generate(1, maxLevel, maxValue);
	}

	// for test
	public static Node generate(int level, int maxLevel, int maxValue) {
		if (level > maxLevel || Math.random() < 0.5) {
			return null;
		}
		Node head = new Node((int) (Math.random() * maxValue));
		head.left = generate(level + 1, maxLevel, maxValue);
		head.right = generate(level + 1, maxLevel, maxValue);
		return head;
	}

	// for test
	public static boolean isSameValueStructure(Node head1, Node head2) {
		if (head1 == null && head2 != null) {
			return false;
		}
		if (head1 != null && head2 == null) {
			return false;
		}
		if (head1 == null && head2 == null) {
			return true;
		}
		if (head1.value != head2.value) {
			return false;
		}
		return isSameValueStructure(head1.left, head2.left) && isSameValueStructure(head1.right, head2.right);
	}

	// for test
	public static void printTree(Node head) {
		System.out.println("Binary Tree:");
		printInOrder(head, 0, "H", 17);
		System.out.println();
	}

public static void main(String[] args) {
		int maxLevel = 5;
		int maxValue = 100;
		int testTimes = 1000000;
		System.out.println("test begin");
		for (int i = 0; i < testTimes; i++) {
			Node head = generateRandomBST(maxLevel, maxValue);
			Queue<String> pre = preSerial(head);
			Queue<String> pos = posSerial(head);
			Queue<String> level = levelSerial(head);
			Node preBuild = buildByPreQueue(pre);
			Node posBuild = buildByPosQueue(pos);
			Node levelBuild = buildByLevelQueue(level);
			if (!isSameValueStructure(preBuild, posBuild) || !isSameValueStructure(posBuild, levelBuild)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("test finish!");
		
	}
```

#### 题解

**为什么正确**

空标记让读取者能知道某一子树何时结束。每个非空标记唯一启动一个节点，后面的标记按约定分别组成其两棵子树，递归消费边界确定，所以能唯一恢复原结构。

**复杂度**

序列化、反序列化时间 O(N)，序列空间 O(N)，递归栈 O(H) 或层序队列 O(W)。

**边界与易错点**

后序反读必须先建右树。只有中序值和空标记仍不足以唯一恢复树。null 标记与值字符串不能混淆；反序列化会消费传入队列。

### 11.4 N 叉树与二叉树互相编码

#### 题目

设计一种编码，把 N 叉树转换为二叉树，并能从该二叉树完整解码回原 N 叉树。

**输入、输出与约束**

输入有序孩子列表表示的 N 叉树或其合法二叉编码；返回可互相还原的结构。

**函数签名（课程入口）**

```java
public TreeNode encode(Node root);
public Node decode(TreeNode root);
public List<Node> de(TreeNode root);
```

**示例**

```text
输入：N 叉根 1 的孩子依次为 2、3、4
输出：二叉树 1.left=2，2.right=3，3.right=4
```

解释：节点 3 自己的孩子仍放在 3.left。

**出处与版本差异**

- [课程源码：class11/Code03_EncodeNaryTreeToBinaryTree.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class11/Code03_EncodeNaryTreeToBinaryTree.java)。
- [LeetCode 原题 431. 将 N 叉树编码为二叉树（Encode N-ary Tree to Binary Tree）](https://leetcode.com/problems/encode-n-ary-tree-to-binary-tree/)

#### 思路

使用左孩子、右兄弟表示：一个 N 叉节点的第一个孩子编码到二叉树 left，其余孩子依次沿 right 串成兄弟链。每个孩子自己的孩子仍递归编码到它的 left。解码时沿 right 收集兄弟，再递归解码各自 left。

#### 代码答案

```java
package class11;

import java.util.ArrayList;
import java.util.List;

// 本题测试链接：https://leetcode.com/problems/encode-n-ary-tree-to-binary-tree
public class Code03_EncodeNaryTreeToBinaryTree {

	// 提交时不要提交这个类
	public static class Node {
		public int val;
		public List<Node> children;

		public Node() {
		}

		public Node(int _val) {
			val = _val;
		}

		public Node(int _val, List<Node> _children) {
			val = _val;
			children = _children;
		}
	};

	// 提交时不要提交这个类
	public static class TreeNode {
		int val;
		TreeNode left;
		TreeNode right;

		TreeNode(int x) {
			val = x;
		}
	}

	// 只提交这个类即可
	class Codec {
		// Encodes an n-ary tree to a binary tree.
		public TreeNode encode(Node root) {
			if (root == null) {
				return null;
			}
			TreeNode head = new TreeNode(root.val);
			head.left = en(root.children);
			return head;
		}

		private TreeNode en(List<Node> children) {
			TreeNode head = null;
			TreeNode cur = null;
			for (Node child : children) {
				TreeNode tNode = new TreeNode(child.val);
				if (head == null) {
					head = tNode;
				} else {
					// 同一父节点的后续孩子编码成前一个孩子的右兄弟。
					cur.right = tNode;
				}
				cur = tNode;
				cur.left = en(child.children);
			}
			return head;
		}

		// Decodes your binary tree to an n-ary tree.
		public Node decode(TreeNode root) {
			if (root == null) {
				return null;
			}
			return new Node(root.val, de(root.left));
		}

		public List<Node> de(TreeNode root) {
			List<Node> children = new ArrayList<>();
			while (root != null) {
				Node cur = new Node(root.val, de(root.left));
				children.add(cur);
				root = root.right;
			}
			return children;
		}

	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：二叉树 right 表示兄弟，不是 N 叉树的某个独立右子树。空孩子列表应编码为 null；不能在解码时漏掉兄弟链末节点。

本条未附独立随机对数器。

#### 题解

**为什么正确**

每一组有序孩子列表与一条右兄弟链一一对应，第一个孩子的位置由 left 确定。递归编码保留每个节点的子列表，因此沿相反方向处理就恢复原树及孩子次序。

**复杂度**

编码、解码时间 O(N)，输出节点空间 O(N)；递归深度最坏 O(N)。

**边界与易错点**

二叉树 right 表示兄弟，不是 N 叉树的某个独立右子树。空孩子列表应编码为 null；不能在解码时漏掉兄弟链末节点。

### 11.5 直观打印二叉树

#### 题目

给定二叉树根节点，把树旋转 90 度后按层次和方向标记打印，使结构能够在控制台直观展示。

**输入、输出与约束**

输入二叉树；控制台输出横向结构，无统一数值返回值。

**函数签名（课程入口）**

```java
public static void printTree(Node head);
public static void printInOrder(Node head, int height, String to, int len);
public static String getSpace(int num);
```

**示例**

```text
输入：root=[2,1,3]
输出：3 位于 2 的右上方，1 位于 2 的右下方
```

解释：打印顺序是右、根、左。

**出处与版本差异**

- [课程源码：class11/Code04_PrintBinaryTree.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class11/Code04_PrintBinaryTree.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

把树想象为逆时针转到横向展示：先打印右子树，再打印当前根，最后打印左子树。用深度乘以固定列宽生成缩进，附加方向标记以区分来自父节点哪一侧。

#### 代码答案

```java
package class11;

public class Code04_PrintBinaryTree {

	public static class Node {
		public int value;
		public Node left;
		public Node right;

		public Node(int data) {
			this.value = data;
		}
	}

	public static void printTree(Node head) {
		System.out.println("Binary Tree:");
		printInOrder(head, 0, "H", 17);
		System.out.println();
	}

	public static void printInOrder(Node head, int height, String to, int len) {
		if (head == null) {
			return;
		}
		// 先打印右子树，使右分支显示在根上方。
		printInOrder(head.right, height + 1, "v", len);
		String val = to + head.value + to;
		int lenM = val.length();
		int lenL = (len - lenM) / 2;
		int lenR = len - lenM - lenL;
		val = getSpace(lenL) + val + getSpace(lenR);
		// 每深入一层增加一个固定宽度的水平缩进。
		System.out.println(getSpace(height * len) + val);
		// 左子树最后打印，显示在根下方。
		printInOrder(head.left, height + 1, "^", len);
	}

	public static String getSpace(int num) {
		String space = " ";
		StringBuffer buf = new StringBuffer("");
		for (int i = 0; i < num; i++) {
			buf.append(space);
		}
		return buf.toString();
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		Node head = new Node(1);
		head.left = new Node(-222222222);
		head.right = new Node(3);
		head.left.left = new Node(Integer.MIN_VALUE);
		head.right.left = new Node(55555555);
		head.right.right = new Node(66);
		head.left.left.right = new Node(777);
		printTree(head);

		head = new Node(1);
		head.left = new Node(2);
		head.right = new Node(3);
		head.left.left = new Node(4);
		head.right.left = new Node(5);
		head.right.right = new Node(6);
		head.left.left.right = new Node(7);
		printTree(head);

		head = new Node(1);
		head.left = new Node(1);
		head.right = new Node(1);
		head.left.left = new Node(1);
		head.right.left = new Node(1);
		head.right.right = new Node(1);
		head.left.left.right = new Node(1);
		printTree(head);

	}
```

#### 题解

**为什么正确**

递归始终让右子树位于根的上方、左子树位于根的下方；按深度缩进把亲子关系分到不同列，同一个节点只打印一次。

**复杂度**

节点访问 O(N)；实际字符输出约 O(NH×列宽)，递归栈 O(H)。不能忽略生成和打印缩进的成本。

**边界与易错点**

列宽要足以容纳数值及方向标记，否则多位数或负数会破坏对齐。该输出是结构示意，不是层序数组。

### 11.6 二叉树最大宽度

#### 题目

给定二叉树根节点，返回节点数最多的一层所包含的节点数量。

**输入、输出与约束**

输入二叉树，返回真实节点最多的一层的节点数，空树返回 0。

**函数签名（课程入口）**

```java
public static int maxWidthUseMap(Node head);
public static int maxWidthNoMap(Node head);
```

**示例**

```text
输入：root=[1,2,3,4,null,null,7]
输出：2
```

解释：最后一层只有 4、7 两个真实节点，中间空缺不计入宽度。

**出处与版本差异**

- [课程源码：class11/Code05_TreeMaxWidth.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class11/Code05_TreeMaxWidth.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

层序遍历累计当前层节点数。版本一记录每个节点的层号；版本二只保存当前层最后节点 curEnd，以及入队时不断更新的下一层末节点 nextEnd。取出 curEnd 时结算本层计数并清零。

#### 代码答案

```java
package class11;

import java.util.HashMap;
import java.util.LinkedList;
import java.util.Queue;

public class Code05_TreeMaxWidth {

	public static class Node {
		public int value;
		public Node left;
		public Node right;

		public Node(int data) {
			this.value = data;
		}
	}

	public static int maxWidthUseMap(Node head) {
		if (head == null) {
			return 0;
		}
		Queue<Node> queue = new LinkedList<>();
		queue.add(head);
		// key 在 哪一层，value
		HashMap<Node, Integer> levelMap = new HashMap<>();
		levelMap.put(head, 1);
		int curLevel = 1; // 当前你正在统计哪一层的宽度
		int curLevelNodes = 0; // 当前层curLevel层，宽度目前是多少
		int max = 0;
		while (!queue.isEmpty()) {
			Node cur = queue.poll();
			int curNodeLevel = levelMap.get(cur);
			if (cur.left != null) {
				levelMap.put(cur.left, curNodeLevel + 1);
				queue.add(cur.left);
			}
			if (cur.right != null) {
				levelMap.put(cur.right, curNodeLevel + 1);
				queue.add(cur.right);
			}
			if (curNodeLevel == curLevel) {
				curLevelNodes++;
			} else {
				max = Math.max(max, curLevelNodes);
				curLevel++;
				curLevelNodes = 1;
			}
		}
		max = Math.max(max, curLevelNodes);
		return max;
	}

	public static int maxWidthNoMap(Node head) {
		if (head == null) {
			return 0;
		}
		Queue<Node> queue = new LinkedList<>();
		queue.add(head);
		Node curEnd = head; // 当前层，最右节点是谁
		Node nextEnd = null; // 下一层，最右节点是谁
		int max = 0;
		int curLevelNodes = 0; // 当前层的节点数
		while (!queue.isEmpty()) {
			Node cur = queue.poll();
			if (cur.left != null) {
				queue.add(cur.left);
				// 记录目前最后入队的下一层节点，后续孩子还会覆盖它。
				nextEnd = cur.left;
			}
			if (cur.right != null) {
				queue.add(cur.right);
				// 右孩子更晚入队，因此成为更新后的下一层末节点。
				nextEnd = cur.right;
			}
			curLevelNodes++;
			// 当前层最后一个节点已出队，计数完整，可更新最大宽度。
			if (cur == curEnd) {
				max = Math.max(max, curLevelNodes);
				curLevelNodes = 0;
				curEnd = nextEnd;
			}
		}
		return max;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static Node generateRandomBST(int maxLevel, int maxValue) {
		return generate(1, maxLevel, maxValue);
	}

	// for test
	public static Node generate(int level, int maxLevel, int maxValue) {
		if (level > maxLevel || Math.random() < 0.5) {
			return null;
		}
		Node head = new Node((int) (Math.random() * maxValue));
		head.left = generate(level + 1, maxLevel, maxValue);
		head.right = generate(level + 1, maxLevel, maxValue);
		return head;
	}

public static void main(String[] args) {
		int maxLevel = 10;
		int maxValue = 100;
		int testTimes = 1000000;
		for (int i = 0; i < testTimes; i++) {
			Node head = generateRandomBST(maxLevel, maxValue);
			if (maxWidthUseMap(head) != maxWidthNoMap(head)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("finish!");

	}
```

#### 题解

**为什么正确**

队列保证同层节点连续出队，curEnd 恰好标记这段连续序列的终点。此前累加的 curLevelNodes 正好是该层真实节点数；对所有层取最大值即为答案。

**复杂度**

时间 O(N)，队列 O(W)；层号哈希版另需 O(N) 映射空间，末节点版除队列外 O(1)。

**边界与易错点**

这里统计真实节点数，不计算中间空位置，与 LeetCode 662 的宽度定义不同。最后一层也要结算。

### 11.7 含父指针二叉树的后继节点

#### 题目

给定一棵节点含父指针的二叉树和其中一个节点，返回其中序遍历序列里的后继节点。

**输入、输出与约束**

节点提供正确 parent 指针；返回中序后继节点身份，无后继返回 null。

**函数签名（课程入口）**

```java
public static Node getSuccessorNode(Node node);
public static Node getLeftMost(Node node);
```

**示例**

```text
输入：树根 2，左孩子1，右孩子3；查询节点1、3
输出：后继分别为 2、null
```

解释：1 从父节点左侧返回；3 已是中序最后节点。

**出处与版本差异**

- [课程源码：class11/Code06_SuccessorNode.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class11/Code06_SuccessorNode.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

有右子树时，中序后继是右子树最左节点。没有右子树时沿 parent 上行，跳过“当前节点是父亲右孩子”的关系，直到首次从某个父亲的左侧返回，该父亲就是后继。

#### 代码答案

```java
package class11;

public class Code06_SuccessorNode {

	public static class Node {
		public int value;
		public Node left;
		public Node right;
		public Node parent;

		public Node(int data) {
			this.value = data;
		}
	}

	public static Node getSuccessorNode(Node node) {
		if (node == null) {
			return node;
		}
		if (node.right != null) {
			// 右子树的中序首节点就是当前节点紧接着的后继。
			return getLeftMost(node.right);
		} else { // 无右子树
			Node parent = node.parent;
			// 持续跳过已经访问完根节点的祖先，直到从左侧返回。
			while (parent != null && parent.right == node) { // 当前节点是其父亲节点右孩子
				node = parent;
				parent = node.parent;
			}
			return parent;
		}
	}

	public static Node getLeftMost(Node node) {
		if (node == null) {
			return node;
		}
		while (node.left != null) {
			node = node.left;
		}
		return node;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		Node head = new Node(6);
		head.parent = null;
		head.left = new Node(3);
		head.left.parent = head;
		head.left.left = new Node(1);
		head.left.left.parent = head.left;
		head.left.left.right = new Node(2);
		head.left.left.right.parent = head.left.left;
		head.left.right = new Node(4);
		head.left.right.parent = head.left;
		head.left.right.right = new Node(5);
		head.left.right.right.parent = head.left.right;
		head.right = new Node(9);
		head.right.parent = head;
		head.right.left = new Node(8);
		head.right.left.parent = head.right;
		head.right.left.left = new Node(7);
		head.right.left.left.parent = head.right.left;
		head.right.right = new Node(10);
		head.right.right.parent = head.right;

		Node test = head.left.left;
		System.out.println(test.value + " next: " + getSuccessorNode(test).value);
		test = head.left.left.right;
		System.out.println(test.value + " next: " + getSuccessorNode(test).value);
		test = head.left;
		System.out.println(test.value + " next: " + getSuccessorNode(test).value);
		test = head.left.right;
		System.out.println(test.value + " next: " + getSuccessorNode(test).value);
		test = head.left.right.right;
		System.out.println(test.value + " next: " + getSuccessorNode(test).value);
		test = head;
		System.out.println(test.value + " next: " + getSuccessorNode(test).value);
		test = head.right.left.left;
		System.out.println(test.value + " next: " + getSuccessorNode(test).value);
		test = head.right.left;
		System.out.println(test.value + " next: " + getSuccessorNode(test).value);
		test = head.right;
		System.out.println(test.value + " next: " + getSuccessorNode(test).value);
		test = head.right.right; // 10's next is null
		System.out.println(test.value + " next: " + getSuccessorNode(test));
	}
```

#### 题解

**为什么正确**

中序顺序是左根右，因此右子树的首访问节点紧跟当前节点。若没有右树，已经完成的右分支及其祖先根都在当前节点之前；首次尚未访问根的祖先只能是从左分支返回的父节点。

**复杂度**

时间 O(H)，额外空间 O(1)。

**边界与易错点**

全树最后一个中序节点无后继，返回 null。必须按节点身份判断父子关系，不能根据节点数值大小推测。

### 11.8 折纸折痕问题

#### 题目

把纸条每次从下向上对折，给定折叠次数 `N`，按从上到下的顺序输出所有凹折痕和凸折痕。

**输入、输出与约束**

输入非负折叠次数 N，输出从上到下的折痕方向，N=0 无输出。

**函数签名（课程入口）**

```java
public static void printAllFolds(int N);
```

**示例**

```text
输入：N=2
输出：凹、凹、凸
```

解释：左子折痕、根折痕、右子折痕依次输出。

**出处与版本差异**

- [课程源码：class11/Code07_PaperFolding.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class11/Code07_PaperFolding.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

折痕形成隐式满二叉树：根为凹折痕，每个节点的左孩子为凹、右孩子为凸。折 N 次对应 N 层；按中序遍历输出，即得到纸条从上到下的折痕顺序，无需真的创建树。

#### 代码答案

```java
package class11;

public class Code07_PaperFolding {

	public static void printAllFolds(int N) {
		process(1, N, true);
		System.out.println();
	}

	// 当前你来了一个节点，脑海中想象的！
	// 这个节点在第i层，一共有N层，N固定不变的
	// 这个节点如果是凹的话，down = T
	// 这个节点如果是凸的话，down = F
	// 函数的功能：中序打印以你想象的节点为头的整棵树！
	public static void process(int i, int N, boolean down) {
		if (i > N) {
			return;
		}
		// 隐式树左孩子固定是凹，先输出它代表的更靠上折痕。
		process(i + 1, N, true);
		System.out.print(down ? "凹 " : "凸 ");
		// 右孩子固定是凸，在当前折痕后输出。
		process(i + 1, N, false);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int N = 4;
		printAllFolds(N);
	}
```

#### 题解

**为什么正确**

每次再折一次，会在已有折痕两侧分别加入凹、凸新折痕，正对应每个旧节点新增左右孩子。中序将新增左折痕、旧折痕、新增右折痕按纸条位置排列。

**复杂度**

输出 2^N-1 个折痕，时间 O(2^N)，递归栈 O(N)。

**边界与易错点**

输出规模本身就是指数，不能因为每节点处理一次就把复杂度写为 O(N)，此处 N 是折叠次数。根始终为凹。


<a id="course-12"></a>

## 第 12 课：二叉树递归套路（一）

### 12.1 判断两棵二叉树是否相同（新手班前置）

#### 题目

给定两棵二叉树的根节点，判断它们的结构是否相同，并且所有对应节点的值是否相等。

**输入、输出与约束**

输入两棵合法二叉树，允许空树；返回结构与值是否完全相同。

**函数签名（课程入口）**

```java
public static boolean isSameTree(TreeNode p, TreeNode q);
```

**示例**

```text
输入：p=[1,2]，q=[1,null,2]
输出：false
```

解释：节点值相同，但 2 分别是左、右孩子。

**出处与版本差异**

- [课程源码：class06/Code02_SameTree.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class06/Code02_SameTree.java)。
- [LeetCode 原题 100. 相同的树（Same Tree）](https://leetcode.com/problems/same-tree/)

#### 思路

同时检查两棵树的对应节点：一空一非空立即失败；都为空成功；都非空时要求值相同，并递归比较左对左、右对右。

#### 代码答案

```java
package class06;

// 测试链接：https://leetcode.com/problems/same-tree
public class Code02_SameTree {

	public static class TreeNode {
		public int val;
		public TreeNode left;
		public TreeNode right;
	}

	public static boolean isSameTree(TreeNode p, TreeNode q) {
		// 异或表示恰好一个节点为空，结构不同。
		if (p == null ^ q == null) {
			return false;
		}
		if (p == null && q == null) {
			return true;
		}
		// 都不为空
		// 根值相同还不够，还必须分别比较两对子树。
		return p.val == q.val && isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：不能只比较遍历值而忽略空节点；值相同不代表结构相同。都为空应返回 true。

本条未附独立随机对数器。

#### 题解

**为什么正确**

两树相同的定义恰好分解为根值相同及两对子树分别相同。空节点判断同时验证结构，值判断验证内容，递归不会把不同形状当作同一棵树。

**复杂度**

最坏时间 O(N+M)，递归栈 O(min(H1,H2))，在首次差异处可能提前返回。

**边界与易错点**

不能只比较遍历值而忽略空节点；值相同不代表结构相同。都为空应返回 true。

### 12.2 判断二叉树是否轴对称（新手班前置）

#### 题目

给定二叉树根节点，判断它的左子树和右子树是否关于根节点互为镜像。

**输入、输出与约束**

输入二叉树，返回关于根轴对称的布尔结果。

**函数签名（课程入口）**

```java
public static boolean isSymmetric(TreeNode root);
```

**示例**

```text
输入：root=[1,2,2,3,4,4,3]
输出：true
```

解释：左右子树的外侧 3 和内侧 4 分别相互对应。

**出处与版本差异**

- [课程源码：class06/Code03_SymmetricTree.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class06/Code03_SymmetricTree.java)。
- [LeetCode 原题 101. 对称二叉树（Symmetric Tree）](https://leetcode.com/problems/symmetric-tree/)

#### 思路

镜像比较与相同树不同：要求左边节点的左孩子对应右边节点的右孩子，左边节点的右孩子对应右边节点的左孩子。先检查空结构和值，再交叉递归。

#### 代码答案

```java
package class06;

// 测试链接：https://leetcode.com/problems/symmetric-tree
public class Code03_SymmetricTree {

	public static class TreeNode {
		public int val;
		public TreeNode left;
		public TreeNode right;
	}

	public static boolean isSymmetric(TreeNode root) {
		return isMirror(root, root);
	}

	public static boolean isMirror(TreeNode h1, TreeNode h2) {
		if (h1 == null ^ h2 == null) {
			return false;
		}
		if (h1 == null && h2 == null) {
			return true;
		}
		// 镜像反射交换左右，左侧的左孩子要对右侧的右孩子。
		// 继续核对另一组交叉孩子，缺一组都不能保证对称。
		return h1.val == h2.val && isMirror(h1.left, h2.right) && isMirror(h1.right, h2.left);
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：不能比较左对左、右对右，那判断的是相同而非镜像。空树视为对称；仅仅每层数值回文还不足以证明结构对称。

本条未附独立随机对数器。

#### 题解

**为什么正确**

轴对称反射会交换左右方向，而保持节点值。每一层都做交叉对应，才能让从根出发的一条路径与左右方向全反的路径一一匹配。

**复杂度**

时间 O(N)，递归栈 O(H)。

**边界与易错点**

不能比较左对左、右对右，那判断的是相同而非镜像。空树视为对称；仅仅每层数值回文还不足以证明结构对称。

### 12.3 二叉树的最大深度（新手班前置）

#### 题目

给定二叉树根节点，返回从根节点到最远叶子节点所经过的节点数。

**输入、输出与约束**

输入二叉树；空树返回 0，其余返回最大根到叶节点数。

**函数签名（课程入口）**

```java
public static int maxDepth(TreeNode root);
```

**示例**

```text
输入：root=[3,9,20,null,null,15,7]
输出：3
```

解释：根到 15 或 7 的路径都有三个节点。

**出处与版本差异**

- [课程源码：class06/Code04_MaximumDepthOfBinaryTree.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class06/Code04_MaximumDepthOfBinaryTree.java)。
- [LeetCode 原题 104. 二叉树的最大深度（Maximum Depth of Binary Tree）](https://leetcode.com/problems/maximum-depth-of-binary-tree/)

#### 思路

定义 maxDepth 返回从当前根到最远叶子的节点数。空树为 0；非空树分别求两棵子树深度，取较大值后加上当前根的一层。

#### 代码答案

```java
package class06;

// 测试链接：https://leetcode.com/problems/maximum-depth-of-binary-tree
public class Code04_MaximumDepthOfBinaryTree {

	public static class TreeNode {
		public int val;
		public TreeNode left;
		public TreeNode right;
	}

	// 以root为头的树，最大高度是多少返回！
	public static int maxDepth(TreeNode root) {
		if (root == null) {
			return 0;
		}
		// 选择较深子树，并计入当前根节点这一层。
		return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：本题按节点数计深度，叶子深度为 1；不要与按边数定义的高度混用。单边链的栈空间可达到 O(N)。

本条未附独立随机对数器。

#### 题解

**为什么正确**

从当前根出发的任何向下路径只能进入左树或右树；最远路径必来自两侧较深者。空树深度 0 使叶子自然得到 max(0,0)+1=1。

**复杂度**

时间 O(N)，递归栈 O(H)。

**边界与易错点**

本题按节点数计深度，叶子深度为 1；不要与按边数定义的高度混用。单边链的栈空间可达到 O(N)。

### 12.4 判断二叉树是否存在目标路径和（新手班前置）

#### 题目

给定二叉树根节点和目标和，判断是否存在一条从根到叶子的路径，使沿途节点值之和等于目标值。

**输入、输出与约束**

输入二叉树与目标 int 和，路径必须从根到叶；空树返回 false，路径计算须避免 int 溢出。

**函数签名（课程入口）**

```java
public static boolean hasPathSum(TreeNode root, int sum);
```

**示例**

```text
输入：root=[1,2,3], target=3
输出：true
```

解释：根到左叶子的路径 1+2=3。

**出处与版本差异**

- [课程源码：class07/Code03_PathSum.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class07/Code03_PathSum.java)。
- [LeetCode 原题 112. 路径总和（Path Sum）](https://leetcode.com/problems/path-sum/)

#### 思路

源码启用的版本维护 preSum，表示到当前节点父亲为止的路径和。到叶子时检查 preSum+x.val 是否等于目标；非叶节点则先把自身值累加，再递归孩子，用 isSum 记录是否找到。注释中另有“递减剩余和”的等价版本。

#### 代码答案

```java
package class07;

public class Code03_PathSum {

	// 测试链接：https://leetcode.com/problems/path-sum
	public static class TreeNode {
		public int val;
		public TreeNode left;
		public TreeNode right;

		TreeNode(int val) {
			this.val = val;
		}
	}

	// 清除上一轮非空树调用的结果，避免历史状态污染本次判断。
	public static boolean isSum = false;

	public static boolean hasPathSum(TreeNode root, int sum) {
		if (root == null) {
			return false;
		}
		// 清除上一轮非空树调用的结果，避免历史状态污染本次判断。
		isSum = false;
		process(root, 0, sum);
		return isSum;
	}

	public static void process(TreeNode x, int preSum, int sum) {
		if (x.left == null && x.right == null) {
			// 当前节点已是叶子，祖先和加自身才是一条完整路径和。
			if (x.val + preSum == sum) {
				isSum = true;
			}
			return;
		}
		// x是非叶节点
		// 传给孩子的前缀和必须包含当前节点。
		preSum += x.val;
		if (x.left != null) {
			process(x.left, preSum, sum);
		}
		if (x.right != null) {
			process(x.right, preSum, sum);
		}
	}

//	public static boolean hasPathSum(TreeNode root, int sum) {
//		if (root == null) {
//			return false;
//		}
//		return process(root, sum);
//	}
//
//	public static boolean process(TreeNode root, int rest) {
//		if (root.left == null && root.right == null) {
//			return root.val == rest;
//		}
//		boolean ans = root.left != null ? process(root.left, rest - root.val) : false;
//		ans |= root.right != null ? process(root.right, rest - root.val) : false;
//		return ans;
//	}

}
```

---

##### 新增对数器（固定输入或固定随机种子）

剩余和递归作独立基准，对同树连续查询31个目标，覆盖空树、负节点、零目标与多次调用状态残留。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
    java.util.Random random = new java.util.Random(20260905L);
    for (int round = 0; round < 2000; round++) {
        int n = random.nextInt(20);
        TreeNode[] nodes = new TreeNode[n];
        for (int i = 0; i < n; i++) nodes[i] = new TreeNode(random.nextInt(11) - 5);
        for (int i = 1; i < n; i++) {
            if (i % 2 == 1) nodes[(i - 1) / 2].left = nodes[i];
            else nodes[(i - 1) / 2].right = nodes[i];
        }
        TreeNode root = n == 0 ? null : nodes[0];
        // 同一棵树连续查询不同目标，检查静态结果标记能否被重置。
        for (int target = -15; target <= 15; target++) {
            boolean expected = remainingForCheck(root, target);
            if (hasPathSum(root, target) != expected)
                throw new AssertionError("round=" + round + " target=" + target);
        }
    }
    System.out.println("PASS: PathSum, repeated calls and negative values");
}

private static boolean remainingForCheck(TreeNode node, int rest) {
    if (node == null) return false;
    if (node.left == null && node.right == null) return node.val == rest;
    // 基准用剩余和返回布尔值，不复用被测版本的祖先前缀和与静态字段。
    return remainingForCheck(node.left, rest - node.val)
        || remainingForCheck(node.right, rest - node.val);
}
```

#### 题解

**为什么正确**

递归传参保证 preSum 精确等于当前路径上已经经过的祖先值之和。每条根到叶路径在对应叶子处检查一次，因此只有完整且达到目标的路径会置 isSum=true。

**复杂度**

时间 O(N)，递归栈 O(H)，标记额外空间 O(1)。

**边界与易错点**

只能在叶子判断，途中和相等不算。节点可为负数，不能在路径和超过目标时剪枝。静态 isSum 每次非空调用前要重置，不适合并发调用共享。

### 12.5 找出二叉树中所有目标路径和（新手班前置）

#### 题目

给定二叉树根节点和目标和，返回所有从根到叶子且节点值累加和等于目标值的路径。

**输入、输出与约束**

输入二叉树和目标和；返回全部根到叶路径值列表，空树返回空列表。

**函数签名（课程入口）**

```java
public static List<List<Integer>> pathSum(TreeNode root, int sum);
```

**示例**

```text
输入：root=[1,2,2], target=3
输出：[[1,2],[1,2]]
```

解释：两个不同叶子产生两条不同路径，即使值序列相同也保留两条。

**出处与版本差异**

- [课程源码：class07/Code04_PathSumII.java](https://github.com/algorithmzuo/algorithm-primary/blob/main/src/class07/Code04_PathSumII.java)。
- [LeetCode 原题 113. 路径总和 II（Path Sum II）](https://leetcode.com/problems/path-sum-ii/)

#### 思路

深度优先遍历同时维护祖先路径和及可变列表 path。加入当前节点后，若它是叶子且和等于目标，就复制 path 保存；处理完当前节点的所有子树后，删除 path 末尾元素，恢复进入本层之前的状态。

#### 代码答案

```java
package class07;

import java.util.ArrayList;
import java.util.List;

public class Code04_PathSumII {

	// 测试链接：https://leetcode.com/problems/path-sum-ii
	public static class TreeNode {
		public int val;
		public TreeNode left;
		public TreeNode right;

		TreeNode(int val) {
			this.val = val;
		}
	}

	public static List<List<Integer>> pathSum(TreeNode root, int sum) {
		List<List<Integer>> ans = new ArrayList<>();
		if (root == null) {
			return ans;
		}
		ArrayList<Integer> path = new ArrayList<>();
		process(root, path, 0, sum, ans);
		return ans;
	}

	public static void process(TreeNode x, List<Integer> path, int preSum, int sum, List<List<Integer>> ans) {
		if (x.left == null && x.right == null) {
			if (preSum + x.val == sum) {
				// 当前节点进入路径，直到本层结束前都应保留在 path 尾部。
				path.add(x.val);
				ans.add(copy(path));
				// 撤销当前节点，使调用者看到的 path 恢复为进入本层前的状态。
				path.remove(path.size() - 1);
			}
			return;
		}
		// x 非叶节点
		// 当前节点进入路径，直到本层结束前都应保留在 path 尾部。
		path.add(x.val);
		preSum += x.val;
		if (x.left != null) {
			process(x.left, path, preSum, sum, ans);
		}
		if (x.right != null) {
			process(x.right, path, preSum, sum, ans);
		}
		// 撤销当前节点，使调用者看到的 path 恢复为进入本层前的状态。
		path.remove(path.size() - 1);
	}

	public static List<Integer> copy(List<Integer> path) {
		List<Integer> ans = new ArrayList<>();
		for (Integer num : path) {
			ans.add(num);
		}
		return ans;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：不能直接 ans.add(path)，否则所有答案会共享同一个可变列表。每次 path.add 必须有对应删除，包括叶子分支。

本条未附独立随机对数器。

#### 题解

**为什么正确**

进入一个分支时，path 恰好表示根到该分支当前节点的路径。退出时撤销本层加入项，使兄弟分支不继承彼此节点。每个叶子对应唯一路径，复制快照保证后续回溯不改变已保存答案。

**复杂度**

遍历 O(N)，复制答案另需 O(S)，S 为所有输出路径长度总和；额外递归与当前路径 O(H)，输出 O(S)。

**边界与易错点**

不能直接 ans.add(path)，否则所有答案会共享同一个可变列表。每次 path.add 必须有对应删除，包括叶子分支。

### 12.6 判断完全二叉树

#### 题目

给定二叉树根节点，判断它是否为完全二叉树。

**输入、输出与约束**

输入二叉树；判断是否层序靠左填满，允许空树。第 12、13 课使用相同性质但保留各自课程接口。

**函数签名（课程入口）**

```java
public static boolean isCBT1(Node head);
public static boolean isCBT2(Node head);
```

**示例**

```text
输入：root=[1,2,3,4,5,6]
输出：true
```

解释：最后一层的 4、5、6 连续靠左，没有空位后再出现节点。

**出处与版本差异**

- [课程源码：class12/Code01_IsCBT.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class12/Code01_IsCBT.java)。
- [LeetCode 原题 958. 二叉树的完全性检验（Check Completeness of a Binary Tree）](https://leetcode.com/problems/check-completeness-of-a-binary-tree/)

#### 思路

层序版一旦遇到孩子不齐的节点，后续节点都必须为叶子，且任何节点都不能只有右孩子。递归版汇总 height、isFull、isCBT：两树同高时，左树须满、右树完全；左树高一层时，左树完全、右树须满；其他高度关系均不合法。

#### 代码答案

```java
package class12;

import java.util.LinkedList;

public class Code01_IsCBT {

	public static class Node {
		public int value;
		public Node left;
		public Node right;

		public Node(int data) {
			this.value = data;
		}
	}

	public static boolean isCBT1(Node head) {
		if (head == null) {
			return true;
		}
		LinkedList<Node> queue = new LinkedList<>();
		// 是否遇到过左右两个孩子不双全的节点
		// 尚未遇到孩子不全的节点；置 true 后，后续只能出现叶子。
		boolean leaf = false;
		Node l = null;
		Node r = null;
		queue.add(head);
		while (!queue.isEmpty()) {
			head = queue.poll();
			l = head.left;
			r = head.right;
			if (
			// 如果遇到了不双全的节点之后，又发现当前节点不是叶节点
			    (leaf && (l != null || r != null))
			    ||
			    // 没有左孩子却有右孩子会留下层序空缺，直接违反完全性。
			    (l == null && r != null)

			) {
				return false;
			}
			if (l != null) {
				queue.add(l);
			}
			if (r != null) {
				queue.add(r);
			}
			if (l == null || r == null) {
				// 首个孩子不双全节点出现后，后续所有节点都必须是叶子。
				leaf = true;
			}
		}
		return true;
	}

	public static boolean isCBT2(Node head) {
		if (head == null) {
			return true;
		}
		return process(head).isCBT;
	}

	// 对每一棵子树，是否是满二叉树、是否是完全二叉树、高度
	public static class Info {
		public boolean isFull;
		public boolean isCBT;
		public int height;

		public Info(boolean full, boolean cbt, int h) {
			isFull = full;
			isCBT = cbt;
			height = h;
		}
	}

	public static Info process(Node X) {
		if (X == null) {
			// 空树既满又完全，高度0，统一递归边界而不增加特判。
			return new Info(true, true, 0);
		}
		Info leftInfo = process(X.left);
		Info rightInfo = process(X.right);

		int height = Math.max(leftInfo.height, rightInfo.height) + 1;

		// 整棵满树要求左右都满且高度相等。
		boolean isFull = leftInfo.isFull
				&&
				rightInfo.isFull
				&& leftInfo.height == rightInfo.height;

		boolean isCBT = false;
		if (isFull) {
			isCBT = true;
		} else { // 以x为头整棵树，不满
			if (leftInfo.isCBT && rightInfo.isCBT) {

				if (leftInfo.isCBT
						&& rightInfo.isFull
						// 左侧可比右侧高1，但末层必须仍从左到右连续填充。
						&& leftInfo.height == rightInfo.height + 1) {
					isCBT = true;
				}
				if (leftInfo.isFull
						&&
						rightInfo.isFull
						// 左侧可比右侧高1，但末层必须仍从左到右连续填充。
						&& leftInfo.height == rightInfo.height + 1) {
					isCBT = true;
				}
				if (leftInfo.isFull
						&& rightInfo.isCBT && leftInfo.height == rightInfo.height) {
					isCBT = true;
				}

			}
		}
		// 父节点需要满性、完全性和高度三项，单个布尔值不足以判断拼接是否合法。
		return new Info(isFull, isCBT, height);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static Node generateRandomBST(int maxLevel, int maxValue) {
		return generate(1, maxLevel, maxValue);
	}

	// for test
	public static Node generate(int level, int maxLevel, int maxValue) {
		if (level > maxLevel || Math.random() < 0.5) {
			return null;
		}
		Node head = new Node((int) (Math.random() * maxValue));
		head.left = generate(level + 1, maxLevel, maxValue);
		head.right = generate(level + 1, maxLevel, maxValue);
		return head;
	}

public static void main(String[] args) {
		int maxLevel = 5;
		int maxValue = 100;
		int testTimes = 1000000;
		for (int i = 0; i < testTimes; i++) {
			Node head = generateRandomBST(maxLevel, maxValue);
			if (isCBT1(head) != isCBT2(head)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("finish!");
	}
```

#### 题解

**为什么正确**

完全树要求除最后一层外全满，最后一层从左到右连续。层序版的“遇缺后只能叶子”正好禁止空位后再出现节点。递归版则按最后一层尚在左树填充还是已经进入右树划分全部合法形态。

**复杂度**

时间 O(N)；层序队列 O(W)，递归栈 O(H)。

**边界与易错点**

完全树可以只有左孩子，但不能只有右孩子。“满”与“完全”不是同义词。空树在这两个布尔性质下均视为 true，高度为 0。

### 12.7 判断二叉搜索树

#### 题目

给定二叉树根节点，判断它是否满足二叉搜索树的严格有序性质。

**输入、输出与约束**

输入任意二叉树，采用严格左小右大规则，空树合法。

**函数签名（课程入口）**

```java
public static boolean isBST1(Node head);
public static boolean isBST2(Node head);
```

**示例**

```text
输入：root=[10,5,15,null,null,6,20]
输出：false
```

解释：6 位于根 10 的右子树，却小于 10；只比较直接孩子会漏检。

**出处与版本差异**

- [课程源码：class12/Code02_IsBST.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class12/Code02_IsBST.java)。
- [LeetCode 原题 98. 验证二叉搜索树（Validate Binary Search Tree）](https://leetcode.com/problems/validate-binary-search-tree/)

#### 思路

中序版先收集左根右序列，再检查是否严格递增。递归版让每棵子树返回 isBST、min、max：两侧各自合法，且左侧最大值<根值<右侧最小值时，整棵子树才合法。

#### 代码答案

```java
package class12;

import java.util.ArrayList;

public class Code02_IsBST {

	public static class Node {
		public int value;
		public Node left;
		public Node right;

		public Node(int data) {
			this.value = data;
		}
	}

	public static boolean isBST1(Node head) {
		if (head == null) {
			return true;
		}
		ArrayList<Node> arr = new ArrayList<>();
		// 基准版先收集中序序列，再检查全局严格递增，不只比较直接孩子。
		in(head, arr);
		for (int i = 1; i < arr.size(); i++) {
			// 重复值也违反本题严格搜索树定义。
			if (arr.get(i).value <= arr.get(i - 1).value) {
				return false;
			}
		}
		return true;
	}

	public static void in(Node head, ArrayList<Node> arr) {
		if (head == null) {
			return;
		}
		in(head.left, arr);
		arr.add(head);
		in(head.right, arr);
	}

	public static boolean isBST2(Node head) {
		if (head == null) {
			return true;
		}
		return process(head).isBST;
	}

	public static class Info {
		public boolean isBST;
		public int max;
		public int min;

		public Info(boolean i, int ma, int mi) {
			isBST = i;
			max = ma;
			min = mi;
		}

	}

	public static Info process(Node x) {
		if (x == null) {
			return null;
		}
		// 向左子树索取是否合法、全体最小值与最大值。
		Info leftInfo = process(x.left);
		// 右子树返回同样摘要，父节点无需再次遍历后代。
		Info rightInfo = process(x.right);
		int max = x.value;
		if (leftInfo != null) {
			max = Math.max(max, leftInfo.max);
		}
		if (rightInfo != null) {
			max = Math.max(max, rightInfo.max);
		}
		int min = x.value;
		if (leftInfo != null) {
			min = Math.min(min, leftInfo.min);
		}
		if (rightInfo != null) {
			min = Math.min(min, rightInfo.min);
		}
		boolean isBST = true;
		// 左子树自身不合法，加入父节点也无法补救。
		if (leftInfo != null && !leftInfo.isBST) {
			isBST = false;
		}
		// 右子树自身不合法，整棵子树直接不合法。
		if (rightInfo != null && !rightInfo.isBST) {
			isBST = false;
		}
		// 左侧任意后代达到或超过根值，都会破坏严格左小关系。
		if (leftInfo != null && leftInfo.max >= x.value) {
			isBST = false;
		}
		// 右侧最小值也必须严格大于根，否则整棵子树不合法。
		if (rightInfo != null && rightInfo.min <= x.value) {
			isBST = false;
		}
		// 把整体是否合法与完整值域一起交给上一层，范围不可只取左右根值。
		return new Info(isBST, max, min);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static Node generateRandomBST(int maxLevel, int maxValue) {
		return generate(1, maxLevel, maxValue);
	}

	// for test
	public static Node generate(int level, int maxLevel, int maxValue) {
		if (level > maxLevel || Math.random() < 0.5) {
			return null;
		}
		Node head = new Node((int) (Math.random() * maxValue));
		head.left = generate(level + 1, maxLevel, maxValue);
		head.right = generate(level + 1, maxLevel, maxValue);
		return head;
	}

public static void main(String[] args) {
		int maxLevel = 4;
		int maxValue = 100;
		int testTimes = 1000000;
		for (int i = 0; i < testTimes; i++) {
			Node head = generateRandomBST(maxLevel, maxValue);
			if (isBST1(head) != isBST2(head)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("finish!");
	}
```

#### 题解

**为什么正确**

二叉搜索树要求全部左侧后代都小于根、全部右侧后代都大于根。只比较孩子值不足以覆盖远处后代，而左右子树的 max、min 恰好概括全部范围；再结合子树自身合法，条件既必要又充分。

**复杂度**

两版时间 O(N)；中序数组版额外空间 O(N)，信息递归版 O(H)。

**边界与易错点**

不能写成 O(log N)：这是验证所有节点，不是沿一条路径查找。重复值在严格定义下不合法；空子树应跳过极值比较，避免哨兵与合法极值冲突。

### 12.8 判断平衡二叉树

#### 题目

给定二叉树根节点，判断任意节点的左右子树高度差是否都不超过 1。

**输入、输出与约束**

输入二叉树；每个节点的两棵子树高度差均≤1 才返回 true。

**函数签名（课程入口）**

```java
public static boolean isBalanced1(Node head);
public static boolean isBalanced2(Node head);
```

**示例**

```text
输入：root=1→左2→左3
输出：false
```

解释：根的左右高度分别为 2 和 0，差为 2。

**出处与版本差异**

- [课程源码：class12/Code03_IsBalanced.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class12/Code03_IsBalanced.java)。
- [LeetCode 原题 110. 平衡二叉树（Balanced Binary Tree）](https://leetcode.com/problems/balanced-binary-tree/)

#### 思路

后序递归返回每棵子树高度以及是否平衡。当前节点要求左右都平衡，并且高度差绝对值≤1；返回高度仍取两边最大值加一。另一版本通过可变布尔结果传播失败。

#### 代码答案

```java
package class12;

public class Code03_IsBalanced {

	public static class Node {
		public int value;
		public Node left;
		public Node right;

		public Node(int data) {
			this.value = data;
		}
	}

	public static boolean isBalanced1(Node head) {
		boolean[] ans = new boolean[1];
		ans[0] = true;
		process1(head, ans);
		return ans[0];
	}

	public static int process1(Node head, boolean[] ans) {
		if (!ans[0] || head == null) {
			return -1;
		}
		int leftHeight = process1(head.left, ans);
		int rightHeight = process1(head.right, ans);
		if (Math.abs(leftHeight - rightHeight) > 1) {
			ans[0] = false;
		}
		return Math.max(leftHeight, rightHeight) + 1;
	}

	public static boolean isBalanced2(Node head) {
		return process(head).isBalanced;
	}

	public static class Info{
		public boolean isBalanced;
		public int height;

		public Info(boolean i, int h) {
			isBalanced = i;
			height = h;
		}
	}

	public static Info process(Node x) {
		if(x == null) {
			return new Info(true, 0);
		}
		Info leftInfo = process(x.left);
		Info rightInfo = process(x.right);
		int height = Math.max(leftInfo.height, rightInfo.height)  + 1;
		boolean isBalanced = true;
		if(!leftInfo.isBalanced) {
			isBalanced = false;
		}
		if(!rightInfo.isBalanced) {
			isBalanced = false;
		}
		// 检查当前根本身的高度差，左右子树内部合法性还需另外合并。
		if(Math.abs(leftInfo.height - rightInfo.height) > 1) {
			isBalanced = false;
		}
		return new Info(isBalanced, height);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static Node generateRandomBST(int maxLevel, int maxValue) {
		return generate(1, maxLevel, maxValue);
	}

	// for test
	public static Node generate(int level, int maxLevel, int maxValue) {
		if (level > maxLevel || Math.random() < 0.5) {
			return null;
		}
		Node head = new Node((int) (Math.random() * maxValue));
		head.left = generate(level + 1, maxLevel, maxValue);
		head.right = generate(level + 1, maxLevel, maxValue);
		return head;
	}

public static void main(String[] args) {
		int maxLevel = 5;
		int maxValue = 100;
		int testTimes = 1000000;
		for (int i = 0; i < testTimes; i++) {
			Node head = generateRandomBST(maxLevel, maxValue);
			if (isBalanced1(head) != isBalanced2(head)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("finish!");
	}
```

#### 题解

**为什么正确**

一棵树平衡必须同时满足所有后代和当前根的高度约束。子树返回值验证了后代，当前高度差验证了根，这三个条件合起来覆盖整棵子树。

**复杂度**

时间 O(N)，递归栈 O(H)。

**边界与易错点**

不能只检查根的高度差。每节点仅求一次子树高度，若反复单独求高度会退化到 O(N²)。所有返回值必须采用一致的空树高度约定。

### 12.9 判断满二叉树

#### 题目

给定二叉树根节点，判断它是否为满二叉树。

**输入、输出与约束**

输入二叉树；判断是否每层全满，空树按课程约定合法。

**函数签名（课程入口）**

```java
public static boolean isFull1(Node head);
public static boolean isFull2(Node head);
```

**示例**

```text
输入：root=[1,2,3,4,5,6,7]
输出：true
```

解释：三层均填满，节点数 7=2³-1。

**出处与版本差异**

- [课程源码：class12/Code04_IsFull.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class12/Code04_IsFull.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

课程的“满二叉树”指每一层都满的 perfect tree。版本一统计高度 h 与节点数 n，判断 n=2^h-1；版本二要求左右子树都满且高度相同，递归返回高度与满树标志。

#### 代码答案

```java
package class12;

public class Code04_IsFull {

	public static class Node {
		public int value;
		public Node left;
		public Node right;

		public Node(int data) {
			this.value = data;
		}
	}

	// 第一种方法
	// 收集整棵树的高度h，和节点数n
	// 只有满二叉树满足 : 2 ^ h - 1 == n
	public static boolean isFull1(Node head) {
		if (head == null) {
			return true;
		}
		Info1 all = process1(head);
		// 将高度转成满树应有节点数；大高度需改用不会溢出的验证方式。
		return (1 << all.height) - 1 == all.nodes;
	}

	public static class Info1 {
		public int height;
		public int nodes;

		public Info1(int h, int n) {
			height = h;
			nodes = n;
		}
	}

	public static Info1 process1(Node head) {
		if (head == null) {
			return new Info1(0, 0);
		}
		Info1 leftInfo = process1(head.left);
		Info1 rightInfo = process1(head.right);
		int height = Math.max(leftInfo.height, rightInfo.height) + 1;
		int nodes = leftInfo.nodes + rightInfo.nodes + 1;
		return new Info1(height, nodes);
	}

	// 第二种方法
	// 收集子树是否是满二叉树
	// 收集子树的高度
	// 左树满 && 右树满 && 左右树高度一样 -> 整棵树是满的
	public static boolean isFull2(Node head) {
		if (head == null) {
			return true;
		}
		return process2(head).isFull;
	}

	public static class Info2 {
		public boolean isFull;
		public int height;

		public Info2(boolean f, int h) {
			isFull = f;
			height = h;
		}
	}

	public static Info2 process2(Node h) {
		if (h == null) {
			return new Info2(true, 0);
		}
		Info2 leftInfo = process2(h.left);
		Info2 rightInfo = process2(h.right);
		// 两侧自身先要每层全满，再要求高度一致才能拼成满树。
		boolean isFull = leftInfo.isFull && rightInfo.isFull && leftInfo.height == rightInfo.height;
		int height = Math.max(leftInfo.height, rightInfo.height) + 1;
		return new Info2(isFull, height);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static Node generateRandomBST(int maxLevel, int maxValue) {
		return generate(1, maxLevel, maxValue);
	}

	// for test
	public static Node generate(int level, int maxLevel, int maxValue) {
		if (level > maxLevel || Math.random() < 0.5) {
			return null;
		}
		Node head = new Node((int) (Math.random() * maxValue));
		head.left = generate(level + 1, maxLevel, maxValue);
		head.right = generate(level + 1, maxLevel, maxValue);
		return head;
	}

public static void main(String[] args) {
		int maxLevel = 5;
		int maxValue = 100;
		int testTimes = 1000000;
		System.out.println("测试开始");
		for (int i = 0; i < testTimes; i++) {
			Node head = generateRandomBST(maxLevel, maxValue);
			if (isFull1(head) != isFull2(head)) {
				System.out.println("出错了!");
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

高度为 h 的二叉树每层最多有 2^层号 个节点，只有所有层都达到上限时总数才为 2^h-1。递归条件等价：两个同高满子树加根恰好组成更高一层的满树。

**复杂度**

时间 O(N)，递归栈 O(H)。

**边界与易错点**

英文 full tree 有时只指每节点 0 或 2 个孩子，弱于本课程定义。1<<h 在 h≥31 时存在 int 移位或溢出问题，布尔结构版更稳妥。

### 12.10 最大二叉搜索子树的节点数

#### 题目

给定二叉树根节点，返回其中节点数最多的二叉搜索子树所包含的节点数。

**输入、输出与约束**

输入任意二叉树，采用严格搜索树规则；分别返回最大搜索子树节点数或根节点。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：root=[10,5,15,1,8,null,7]
输出：Size=3；Head=节点5
```

解释：以 5 为根的 [5,1,8] 合法，整树受右侧 7 破坏。

**出处与版本差异**

- [课程源码：class12/Code05_MaxSubBSTSize.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class12/Code05_MaxSubBSTSize.java)。
- [LeetCode 原题 333. 最大 BST 子树（Largest BST Subtree）](https://leetcode.com/problems/largest-bst-subtree/)

#### 思路

向子树索取范围最小值、最大值以及内部最大搜索子树的信息。若左右整棵子树都是搜索树且范围与根严格分开，当前整棵树可以合并为更大答案；否则答案只能完整落在左树或右树内，取较大者。Head 版本还携带答案根节点身份。

#### 代码答案

```java
package class12;

import java.util.ArrayList;

// 在线测试链接 : https://leetcode.com/problems/largest-bst-subtree
public class Code05_MaxSubBSTSize {

	// 提交时不要提交这个类
	public static class TreeNode {
		public int val;
		public TreeNode left;
		public TreeNode right;

		public TreeNode(int value) {
			val = value;
		}
	}

	// 提交如下的largestBSTSubtree方法，可以直接通过
	public static int largestBSTSubtree(TreeNode head) {
		if (head == null) {
			return 0;
		}
		return process(head).maxBSTSubtreeSize;
	}

	public static class Info {
		public int maxBSTSubtreeSize;
		public int allSize;
		public int max;
		public int min;

		public Info(int m, int a, int ma, int mi) {
			maxBSTSubtreeSize = m;
			allSize = a;
			max = ma;
			min = mi;
		}
	}

	public static Info process(TreeNode x) {
		if (x == null) {
			return null;
		}
		// 左侧返回整体范围和内部最佳搜索子树，两个信息不可混为一谈。
		Info leftInfo = process(x.left);
		Info rightInfo = process(x.right);
		int max = x.val;
		int min = x.val;
		int allSize = 1;
		if (leftInfo != null) {
			max = Math.max(leftInfo.max, max);
			min = Math.min(leftInfo.min, min);
			allSize += leftInfo.allSize;
		}
		if (rightInfo != null) {
			max = Math.max(rightInfo.max, max);
			min = Math.min(rightInfo.min, min);
			allSize += rightInfo.allSize;
		}
		int p1 = -1;
		if (leftInfo != null) {
			p1 = leftInfo.maxBSTSubtreeSize;
		}
		int p2 = -1;
		if (rightInfo != null) {
			p2 = rightInfo.maxBSTSubtreeSize;
		}
		int p3 = -1;
		boolean leftBST = leftInfo == null ? true : (leftInfo.maxBSTSubtreeSize == leftInfo.allSize);
		boolean rightBST = rightInfo == null ? true : (rightInfo.maxBSTSubtreeSize == rightInfo.allSize);
		if (leftBST && rightBST) {
			boolean leftMaxLessX = leftInfo == null ? true : (leftInfo.max < x.val);
			boolean rightMinMoreX = rightInfo == null ? true : (x.val < rightInfo.min);
			if (leftMaxLessX && rightMinMoreX) {
				int leftSize = leftInfo == null ? 0 : leftInfo.allSize;
				int rightSize = rightInfo == null ? 0 : rightInfo.allSize;
				p3 = leftSize + rightSize + 1;
			}
		}
		return new Info(Math.max(p1, Math.max(p2, p3)), allSize, max, min);
	}

	// 为了验证
	// 对数器方法
	public static int right(TreeNode head) {
		if (head == null) {
			return 0;
		}
		int h = getBSTSize(head);
		if (h != 0) {
			return h;
		}
		return Math.max(right(head.left), right(head.right));
	}

	// 为了验证
	// 对数器方法
	public static int getBSTSize(TreeNode head) {
		if (head == null) {
			return 0;
		}
		ArrayList<TreeNode> arr = new ArrayList<>();
		in(head, arr);
		for (int i = 1; i < arr.size(); i++) {
			if (arr.get(i).val <= arr.get(i - 1).val) {
				return 0;
			}
		}
		return arr.size();
	}

	// 为了验证
	// 对数器方法
	public static void in(TreeNode head, ArrayList<TreeNode> arr) {
		if (head == null) {
			return;
		}
		in(head.left, arr);
		arr.add(head);
		in(head.right, arr);
	}

	// 为了验证
	// 对数器方法
	public static TreeNode generateRandomBST(int maxLevel, int maxValue) {
		return generate(1, maxLevel, maxValue);
	}

	// 为了验证
	// 对数器方法
	public static TreeNode generate(int level, int maxLevel, int maxValue) {
		if (level > maxLevel || Math.random() < 0.5) {
			return null;
		}
		TreeNode head = new TreeNode((int) (Math.random() * maxValue));
		head.left = generate(level + 1, maxLevel, maxValue);
		head.right = generate(level + 1, maxLevel, maxValue);
		return head;
	}

	// 为了验证
	// 对数器方法

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int maxLevel = 4;
		int maxValue = 100;
		int testTimes = 1000000;
		System.out.println("测试开始");
		for (int i = 0; i < testTimes; i++) {
			TreeNode head = generateRandomBST(maxLevel, maxValue);
			if (largestBSTSubtree(head) != right(head)) {
				System.out.println("出错了！");
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

以某节点为根的子树必须包括它的全部后代，不能任意删节点。最优搜索子树若包含当前根，就只能是当前整棵树，需验证全部左右范围；若不包含当前根，它必完全位于一侧，故三个候选覆盖全部可能。

**复杂度**

信息递归版 O(N) 时间、O(H) 栈；逐子树重验的基准版最坏 O(N²)，临时中序数组可占 O(N)。

**边界与易错点**

“最大搜索子树”不同于可删节点的“最大搜索拓扑结构”。Head 版比较节点身份；同样大小时课程优先保留左侧答案。整棵子树的 min/max 不能只取最佳子树的范围。

### 12.11 二叉树节点间最大距离

#### 题目

给定二叉树根节点，返回任意两个节点之间路径包含的最大节点数。

**输入、输出与约束**

输入二叉树；返回任意两节点路径上的最大节点数，空树返回 0。

**函数签名（课程入口）**

```java
public static int maxDistance1(Node head);
public static ArrayList<Node> getPrelist(Node head);
public static void fillPrelist(Node head, ArrayList<Node> arr);
public static HashMap<Node, Node> getParentMap(Node head);
public static void fillParentMap(Node head, HashMap<Node, Node> parentMap);
public static int distance(HashMap<Node, Node> parentMap, Node o1, Node o2);
```

**示例**

```text
输入：root=[1,2,3,4,5]
输出：4
```

解释：路径 4→2→1→3 含 4 个节点、3 条边。

**出处与版本差异**

- [课程源码：class12/Code06_MaxDistance.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class12/Code06_MaxDistance.java)。
- [LeetCode 对应题 543. 二叉树的直径（Diameter of Binary Tree）](https://leetcode.com/problems/diameter-of-binary-tree/)

课程版本说明：

- LeetCode 543：距离按节点数计，LeetCode 按边数计。

#### 思路

后序返回子树高度 height 和内部最长路径 maxDistance。最长路径要么全在左侧、全在右侧，要么经过当前根；经过根的候选为左高度+右高度+1。取三者最大。

#### 代码答案

```java
package class12;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;

public class Code06_MaxDistance {

	public static class Node {
		public int value;
		public Node left;
		public Node right;

		public Node(int data) {
			this.value = data;
		}
	}

	public static int maxDistance1(Node head) {
		if (head == null) {
			return 0;
		}
		ArrayList<Node> arr = getPrelist(head);
		HashMap<Node, Node> parentMap = getParentMap(head);
		int max = 0;
		for (int i = 0; i < arr.size(); i++) {
			for (int j = i; j < arr.size(); j++) {
				max = Math.max(max, distance(parentMap, arr.get(i), arr.get(j)));
			}
		}
		return max;
	}

	public static ArrayList<Node> getPrelist(Node head) {
		ArrayList<Node> arr = new ArrayList<>();
		fillPrelist(head, arr);
		return arr;
	}

	public static void fillPrelist(Node head, ArrayList<Node> arr) {
		if (head == null) {
			return;
		}
		arr.add(head);
		fillPrelist(head.left, arr);
		fillPrelist(head.right, arr);
	}

	public static HashMap<Node, Node> getParentMap(Node head) {
		HashMap<Node, Node> map = new HashMap<>();
		map.put(head, null);
		fillParentMap(head, map);
		return map;
	}

	public static void fillParentMap(Node head, HashMap<Node, Node> parentMap) {
		if (head.left != null) {
			parentMap.put(head.left, head);
			fillParentMap(head.left, parentMap);
		}
		if (head.right != null) {
			parentMap.put(head.right, head);
			fillParentMap(head.right, parentMap);
		}
	}

	public static int distance(HashMap<Node, Node> parentMap, Node o1, Node o2) {
		HashSet<Node> o1Set = new HashSet<>();
		Node cur = o1;
		o1Set.add(cur);
		while (parentMap.get(cur) != null) {
			cur = parentMap.get(cur);
			o1Set.add(cur);
		}
		cur = o2;
		while (!o1Set.contains(cur)) {
			cur = parentMap.get(cur);
		}
		Node lowestAncestor = cur;
		cur = o1;
		int distance1 = 1;
		while (cur != lowestAncestor) {
			cur = parentMap.get(cur);
			distance1++;
		}
		cur = o2;
		int distance2 = 1;
		while (cur != lowestAncestor) {
			cur = parentMap.get(cur);
			distance2++;
		}
		return distance1 + distance2 - 1;
	}

//	public static int maxDistance2(Node head) {
//		return process(head).maxDistance;
//	}
//
//	public static class Info {
//		public int maxDistance;
//		public int height;
//
//		public Info(int dis, int h) {
//			maxDistance = dis;
//			height = h;
//		}
//	}
//
//	public static Info process(Node X) {
//		if (X == null) {
//			return new Info(0, 0);
//		}
//		Info leftInfo = process(X.left);
//		Info rightInfo = process(X.right);
//		int height = Math.max(leftInfo.height, rightInfo.height) + 1;
//		int maxDistance = Math.max(
//				Math.max(leftInfo.maxDistance, rightInfo.maxDistance),
//				leftInfo.height + rightInfo.height + 1);
//		return new Info(maxDistance, height);
//	}

	public static int maxDistance2(Node head) {
		return process(head).maxDistance;
	}

	public static class Info {
		public int maxDistance;
		public int height;

		public Info(int m, int h) {
			maxDistance = m;
			height = h;
		}

	}

	public static Info process(Node x) {
		if (x == null) {
			return new Info(0, 0);
		}
		Info leftInfo = process(x.left);
		Info rightInfo = process(x.right);
		int height = Math.max(leftInfo.height, rightInfo.height) + 1;
		int p1 = leftInfo.maxDistance;
		int p2 = rightInfo.maxDistance;
		// 这是经过当前根的最长路径，左右各取最长下降链并加根。
		int p3 = leftInfo.height + rightInfo.height + 1;
		int maxDistance = Math.max(Math.max(p1, p2), p3);
		return new Info(maxDistance, height);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static Node generateRandomBST(int maxLevel, int maxValue) {
		return generate(1, maxLevel, maxValue);
	}

	// for test
	public static Node generate(int level, int maxLevel, int maxValue) {
		if (level > maxLevel || Math.random() < 0.5) {
			return null;
		}
		Node head = new Node((int) (Math.random() * maxValue));
		head.left = generate(level + 1, maxLevel, maxValue);
		head.right = generate(level + 1, maxLevel, maxValue);
		return head;
	}

public static void main(String[] args) {
		int maxLevel = 4;
		int maxValue = 100;
		int testTimes = 1000000;
		for (int i = 0; i < testTimes; i++) {
			Node head = generateRandomBST(maxLevel, maxValue);
			if (maxDistance1(head) != maxDistance2(head)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("finish!");
	}
```

#### 题解

**为什么正确**

树中两点的简单路径唯一。不经过当前根时路径只能留在一侧；经过根时最多各取左右一条向下最长链，因此两边高度加根正好是最优跨根路径。

**复杂度**

递归版时间 O(N)、栈 O(H)；暴力枚举点对与找公共祖先的版本更慢，只适合小树对拍。

**边界与易错点**

课程按节点数计距离，LeetCode 543 按边数；非空树需减一才能转换。高度与路径长度都必须统一按节点数。


<a id="course-13"></a>

## 第 13 课：二叉树递归套路（二）与贪心

### 13.1 判断完全二叉树

#### 题目

给定二叉树根节点，判断它是否为完全二叉树。

**输入、输出与约束**

输入二叉树；判断是否层序靠左填满，允许空树。第 12、13 课使用相同性质但保留各自课程接口。

**函数签名（课程入口）**

```java
public static boolean isCompleteTree1(TreeNode head);
public static boolean isCompleteTree2(TreeNode head);
```

**示例**

```text
输入：root=[1,2,3,4,5,6]
输出：true
```

解释：最后一层的 4、5、6 连续靠左，没有空位后再出现节点。

**出处与版本差异**

- [课程源码：class13/Code01_IsCBT.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class13/Code01_IsCBT.java)。
- [LeetCode 原题 958. 二叉树的完全性检验（Check Completeness of a Binary Tree）](https://leetcode.com/problems/check-completeness-of-a-binary-tree/)

#### 思路

层序版一旦遇到孩子不齐的节点，后续节点都必须为叶子，且任何节点都不能只有右孩子。递归版汇总 height、isFull、isCBT：两树同高时，左树须满、右树完全；左树高一层时，左树完全、右树须满；其他高度关系均不合法。

#### 代码答案

```java
package class13;

import java.util.LinkedList;

// 测试链接 : https://leetcode.com/problems/check-completeness-of-a-binary-tree/

public class Code01_IsCBT {

	// 不要提交这个类
	public static class TreeNode {
		public int val;
		public TreeNode left;
		public TreeNode right;

		public TreeNode(int v) {
			val = v;
		}
	}

	public static boolean isCompleteTree1(TreeNode head) {
		if (head == null) {
			return true;
		}
		LinkedList<TreeNode> queue = new LinkedList<>();
		// 是否遇到过左右两个孩子不双全的节点
		// 尚未遇到孩子不全的节点；置 true 后，后续只能出现叶子。
		boolean leaf = false;
		TreeNode l = null;
		TreeNode r = null;
		queue.add(head);
		while (!queue.isEmpty()) {
			head = queue.poll();
			l = head.left;
			r = head.right;
			if (
			// 如果遇到了不双全的节点之后，又发现当前节点不是叶节点
			// 没有左孩子却有右孩子会留下层序空缺，直接违反完全性。
			(leaf && (l != null || r != null)) || (l == null && r != null)

			) {
				return false;
			}
			if (l != null) {
				queue.add(l);
			}
			if (r != null) {
				queue.add(r);
			}
			if (l == null || r == null) {
				// 首个孩子不双全节点出现后，后续所有节点都必须是叶子。
				leaf = true;
			}
		}
		return true;
	}

	public static boolean isCompleteTree2(TreeNode head) {
		return process(head).isCBT;
	}

	public static class Info {
		public boolean isFull;
		public boolean isCBT;
		public int height;

		public Info(boolean full, boolean cbt, int h) {
			isFull = full;
			isCBT = cbt;
			height = h;
		}
	}

	public static Info process(TreeNode x) {
		if (x == null) {
			// 空树既满又完全，高度0，统一递归边界而不增加特判。
			return new Info(true, true, 0);
		}
		Info leftInfo = process(x.left);
		Info rightInfo = process(x.right);
		int height = Math.max(leftInfo.height, rightInfo.height) + 1;
		// 整棵满树要求左右都满且高度相等。
		boolean isFull = leftInfo.isFull && rightInfo.isFull && leftInfo.height == rightInfo.height;
		boolean isCBT = false;
		if (leftInfo.isFull && rightInfo.isFull && leftInfo.height == rightInfo.height) {
			isCBT = true;
		// 左侧可比右侧高1，但末层必须仍从左到右连续填充。
		} else if (leftInfo.isCBT && rightInfo.isFull && leftInfo.height == rightInfo.height + 1) {
			isCBT = true;
		// 左侧可比右侧高1，但末层必须仍从左到右连续填充。
		} else if (leftInfo.isFull && rightInfo.isFull && leftInfo.height == rightInfo.height + 1) {
			isCBT = true;
		} else if (leftInfo.isFull && rightInfo.isCBT && leftInfo.height == rightInfo.height) {
			isCBT = true;
		}
		// 父节点需要满性、完全性和高度三项，单个布尔值不足以判断拼接是否合法。
		return new Info(isFull, isCBT, height);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static TreeNode generateRandomBST(int maxLevel, int maxValue) {
		return generate(1, maxLevel, maxValue);
	}

	// for test
	public static TreeNode generate(int level, int maxLevel, int maxValue) {
		if (level > maxLevel || Math.random() < 0.5) {
			return null;
		}
		TreeNode head = new TreeNode((int) (Math.random() * maxValue));
		head.left = generate(level + 1, maxLevel, maxValue);
		head.right = generate(level + 1, maxLevel, maxValue);
		return head;
	}

public static void main(String[] args) {
		int maxLevel = 5;
		int maxValue = 100;
		int testTimes = 1000000;
		for (int i = 0; i < testTimes; i++) {
			TreeNode head = generateRandomBST(maxLevel, maxValue);
			if (isCompleteTree1(head) != isCompleteTree2(head)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("finish!");
	}
```

#### 题解

**为什么正确**

完全树要求除最后一层外全满，最后一层从左到右连续。层序版的“遇缺后只能叶子”正好禁止空位后再出现节点。递归版则按最后一层尚在左树填充还是已经进入右树划分全部合法形态。

**复杂度**

时间 O(N)；层序队列 O(W)，递归栈 O(H)。

**边界与易错点**

完全树可以只有左孩子，但不能只有右孩子。“满”与“完全”不是同义词。空树在这两个布尔性质下均视为 true，高度为 0。

### 13.2 最大二叉搜索子树的头节点

#### 题目

给定二叉树根节点，返回其中节点数最多的二叉搜索子树的根节点。

**输入、输出与约束**

输入任意二叉树，采用严格搜索树规则；分别返回最大搜索子树节点数或根节点。

**函数签名（课程入口）**

```java
public static Node maxSubBSTHead1(Node head);
public static Node maxSubBSTHead2(Node head);
```

**示例**

```text
输入：root=[10,5,15,1,8,null,7]
输出：Size=3；Head=节点5
```

解释：以 5 为根的 [5,1,8] 合法，整树受右侧 7 破坏。

**出处与版本差异**

- [课程源码：class13/Code02_MaxSubBSTHead.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class13/Code02_MaxSubBSTHead.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

向子树索取范围最小值、最大值以及内部最大搜索子树的信息。若左右整棵子树都是搜索树且范围与根严格分开，当前整棵树可以合并为更大答案；否则答案只能完整落在左树或右树内，取较大者。Head 版本还携带答案根节点身份。

#### 代码答案

```java
package class13;

import java.util.ArrayList;

public class Code02_MaxSubBSTHead {

	public static class Node {
		public int value;
		public Node left;
		public Node right;

		public Node(int data) {
			this.value = data;
		}
	}

	public static int getBSTSize(Node head) {
		if (head == null) {
			return 0;
		}
		ArrayList<Node> arr = new ArrayList<>();
		in(head, arr);
		for (int i = 1; i < arr.size(); i++) {
			if (arr.get(i).value <= arr.get(i - 1).value) {
				return 0;
			}
		}
		return arr.size();
	}

	public static void in(Node head, ArrayList<Node> arr) {
		if (head == null) {
			return;
		}
		in(head.left, arr);
		arr.add(head);
		in(head.right, arr);
	}

	public static Node maxSubBSTHead1(Node head) {
		if (head == null) {
			return null;
		}
		// 基准版若整棵子树就是搜索树，显然无需再寻找更小的内部候选。
		if (getBSTSize(head) != 0) {
			return head;
		}
		// 整棵不合法时，最大合法子树必完全落在一侧。
		Node leftAns = maxSubBSTHead1(head.left);
		Node rightAns = maxSubBSTHead1(head.right);
		return getBSTSize(leftAns) >= getBSTSize(rightAns) ? leftAns : rightAns;
	}

	public static Node maxSubBSTHead2(Node head) {
		if (head == null) {
			return null;
		}
		return process(head).maxSubBSTHead;
	}

	// 每一棵子树
	public static class Info {
		public Node maxSubBSTHead;
		public int maxSubBSTSize;
		public int min;
		public int max;

		public Info(Node h, int size, int mi, int ma) {
			maxSubBSTHead = h;
			maxSubBSTSize = size;
			min = mi;
			max = ma;
		}
	}

	public static Info process(Node X) {
		if (X == null) {
			return null;
		}
		// 左侧返回整体范围和内部最佳搜索子树，两个信息不可混为一谈。
		Info leftInfo = process(X.left);
		Info rightInfo = process(X.right);
		int min = X.value;
		int max = X.value;
		Node maxSubBSTHead = null;
		int maxSubBSTSize = 0;
		if (leftInfo != null) {
			min = Math.min(min, leftInfo.min);
			max = Math.max(max, leftInfo.max);
			// 先把左侧最优子树作为候选，再比较右侧与整棵。
			maxSubBSTHead = leftInfo.maxSubBSTHead;
			maxSubBSTSize = leftInfo.maxSubBSTSize;
		}
		if (rightInfo != null) {
			min = Math.min(min, rightInfo.min);
			max = Math.max(max, rightInfo.max);
			// 右侧严格更大才替换，大小相同时保留已有左侧答案。
			if (rightInfo.maxSubBSTSize > maxSubBSTSize) {
				maxSubBSTHead = rightInfo.maxSubBSTHead;
				maxSubBSTSize = rightInfo.maxSubBSTSize;
			}
		}
		// 左侧最优子树的根必须就是左孩子，才能证明整个左子树合法。
		if ((leftInfo == null ? true : (leftInfo.maxSubBSTHead == X.left && leftInfo.max < X.value))
				// 同理要求整个右子树合法，而不是它内部存在某棵合法子树即可。
				&& (rightInfo == null ? true : (rightInfo.maxSubBSTHead == X.right && rightInfo.min > X.value))) {
			// 左右整树都可与当前根合法合并，最佳答案扩展为整棵当前子树。
			maxSubBSTHead = X;
			maxSubBSTSize = (leftInfo == null ? 0 : leftInfo.maxSubBSTSize)
					+ (rightInfo == null ? 0 : rightInfo.maxSubBSTSize) + 1;
		}
		// 返回最优根和大小，同时保留整棵当前子树值域供父节点合并。
		return new Info(maxSubBSTHead, maxSubBSTSize, min, max);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static Node generateRandomBST(int maxLevel, int maxValue) {
		return generate(1, maxLevel, maxValue);
	}

	// for test
	public static Node generate(int level, int maxLevel, int maxValue) {
		if (level > maxLevel || Math.random() < 0.5) {
			return null;
		}
		Node head = new Node((int) (Math.random() * maxValue));
		head.left = generate(level + 1, maxLevel, maxValue);
		head.right = generate(level + 1, maxLevel, maxValue);
		return head;
	}

public static void main(String[] args) {
		int maxLevel = 4;
		int maxValue = 100;
		int testTimes = 1000000;
		for (int i = 0; i < testTimes; i++) {
			Node head = generateRandomBST(maxLevel, maxValue);
			if (maxSubBSTHead1(head) != maxSubBSTHead2(head)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("finish!");
	}
```

#### 题解

**为什么正确**

以某节点为根的子树必须包括它的全部后代，不能任意删节点。最优搜索子树若包含当前根，就只能是当前整棵树，需验证全部左右范围；若不包含当前根，它必完全位于一侧，故三个候选覆盖全部可能。

**复杂度**

信息递归版 O(N) 时间、O(H) 栈；逐子树重验的基准版最坏 O(N²)，临时中序数组可占 O(N)。

**边界与易错点**

“最大搜索子树”不同于可删节点的“最大搜索拓扑结构”。Head 版比较节点身份；同样大小时课程优先保留左侧答案。整棵子树的 min/max 不能只取最佳子树的范围。

### 13.3 二叉树最低公共祖先

#### 题目

给定二叉树根节点和两个节点，返回二者的最低公共祖先。

**输入、输出与约束**

输入二叉树及两个节点引用；返回最低公共祖先，未同时找到时返回 null。

**函数签名（课程入口）**

```java
public static Node lowestAncestor1(Node head, Node o1, Node o2);
public static void fillParentMap(Node head, HashMap<Node, Node> parentMap);
public static Node lowestAncestor2(Node head, Node a, Node b);
```

**示例**

```text
输入：root=[3,5,1]；a=节点5，b=节点1
输出：节点3
```

解释：两个目标分别位于根两侧，首次在根处汇合。

**出处与版本差异**

- [课程源码：class13/Code03_lowestAncestor.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class13/Code03_lowestAncestor.java)。
- [LeetCode 原题 236. 二叉树的最近公共祖先（Lowest Common Ancestor of a Binary Tree）](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/)

#### 思路

基准版记录每节点父亲，用集合保存 a 的祖先，再沿 b 的父链向上找首次命中。递归版返回是否找到 a、是否找到 b 和已确定答案；若某侧已有答案直接继承，否则两目标首次在当前子树齐全时，当前根成为答案。

#### 代码答案

```java
package class13;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;

public class Code03_lowestAncestor {

	public static class Node {
		public int value;
		public Node left;
		public Node right;

		public Node(int data) {
			this.value = data;
		}
	}

	public static Node lowestAncestor1(Node head, Node o1, Node o2) {
		if (head == null) {
			return null;
		}
		// key的父节点是value
		HashMap<Node, Node> parentMap = new HashMap<>();
		parentMap.put(head, null);
		fillParentMap(head, parentMap);
		HashSet<Node> o1Set = new HashSet<>();
		Node cur = o1;
		o1Set.add(cur);
		while (parentMap.get(cur) != null) {
			cur = parentMap.get(cur);
			o1Set.add(cur);
		}
		cur = o2;
		while (!o1Set.contains(cur)) {
			cur = parentMap.get(cur);
		}
		return cur;
	}

	public static void fillParentMap(Node head, HashMap<Node, Node> parentMap) {
		if (head.left != null) {
			parentMap.put(head.left, head);
			fillParentMap(head.left, parentMap);
		}
		if (head.right != null) {
			parentMap.put(head.right, head);
			fillParentMap(head.right, parentMap);
		}
	}

	public static Node lowestAncestor2(Node head, Node a, Node b) {
		return process(head, a, b).ans;
	}

	public static class Info {
		public boolean findA;
		public boolean findB;
		public Node ans;

		public Info(boolean fA, boolean fB, Node an) {
			findA = fA;
			findB = fB;
			ans = an;
		}
	}

	public static Info process(Node x, Node a, Node b) {
		if (x == null) {
			return new Info(false, false, null);
		}
		Info leftInfo = process(x.left, a, b);
		Info rightInfo = process(x.right, a, b);
		// 目标可能等于当前根，也可能已在任一子树找到。
		boolean findA = (x == a) || leftInfo.findA || rightInfo.findA;
		// 独立传播另一个目标是否出现，不能只靠节点值比较。
		boolean findB = (x == b) || leftInfo.findB || rightInfo.findB;
		Node ans = null;
		if (leftInfo.ans != null) {
			ans = leftInfo.ans;
		} else if (rightInfo.ans != null) {
			ans = rightInfo.ans;
		} else {
			if (findA && findB) {
				// 左右没有更低答案而当前首次找齐两目标，所以当前根是最低公共祖先。
				ans = x;
			}
		}
		return new Info(findA, findB, ans);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static Node generateRandomBST(int maxLevel, int maxValue) {
		return generate(1, maxLevel, maxValue);
	}

	// for test
	public static Node generate(int level, int maxLevel, int maxValue) {
		if (level > maxLevel || Math.random() < 0.5) {
			return null;
		}
		Node head = new Node((int) (Math.random() * maxValue));
		head.left = generate(level + 1, maxLevel, maxValue);
		head.right = generate(level + 1, maxLevel, maxValue);
		return head;
	}

	// for test
	public static Node pickRandomOne(Node head) {
		if (head == null) {
			return null;
		}
		ArrayList<Node> arr = new ArrayList<>();
		fillPrelist(head, arr);
		int randomIndex = (int) (Math.random() * arr.size());
		return arr.get(randomIndex);
	}

	// for test
	public static void fillPrelist(Node head, ArrayList<Node> arr) {
		if (head == null) {
			return;
		}
		arr.add(head);
		fillPrelist(head.left, arr);
		fillPrelist(head.right, arr);
	}

public static void main(String[] args) {
		int maxLevel = 4;
		int maxValue = 100;
		int testTimes = 1000000;
		for (int i = 0; i < testTimes; i++) {
			Node head = generateRandomBST(maxLevel, maxValue);
			Node o1 = pickRandomOne(head);
			Node o2 = pickRandomOne(head);
			if (lowestAncestor1(head, o1, o2) != lowestAncestor2(head, o1, o2)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("finish!");
	}
```

#### 题解

**为什么正确**

最低公共祖先是第一棵同时包含两个目标的最小子树的根。后序先处理子树，若它们均未含齐两个目标而当前含齐，则当前根恰是最低位置，不可能还有更低候选。

**复杂度**

时间 O(N)；父映射版空间 O(N)，递归版 O(H)。

**边界与易错点**

目标可以就是当前根，也可以 a==b，需按节点身份判断。课程递归版可用找到标志处理目标缺失；在线评测通常保证两目标存在。

### 13.4 员工派对最大快乐值

#### 题目

公司组织关系是一棵多叉树；在直接上下级不能同时参加的条件下，选择参加宴会的员工，使快乐值总和最大。

**输入、输出与约束**

输入公司多叉树，每人有快乐值；返回满足直接上下级不能同时参加的最大总快乐值。

**函数签名（课程入口）**

```java
public static int maxHappy1(Employee boss);
public static int maxHappy2(Employee head);
```

**示例**

```text
输入：老板快乐10，两个直属下级快乐6、7
输出：13
```

解释：老板不来，两个下级都来优于只让老板来。

**出处与版本差异**

- [课程源码：class13/Code04_MaxHappy.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class13/Code04_MaxHappy.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

每个员工返回两种状态：yes 表示本人来时子树最高快乐值，no 表示本人不来时最高快乐值。本人来时所有直属下级必须不来；本人不来时每个直属下级可独立取来与不来的较优值。

#### 代码答案

```java
package class13;

import java.util.ArrayList;
import java.util.List;

public class Code04_MaxHappy {

	public static class Employee {
		public int happy;
		public List<Employee> nexts;

		public Employee(int h) {
			happy = h;
			nexts = new ArrayList<>();
		}

	}

	public static int maxHappy1(Employee boss) {
		if (boss == null) {
			return 0;
		}
		return process1(boss, false);
	}

	// 当前来到的节点叫cur，
	// up表示cur的上级是否来，
	// 该函数含义：
	// 如果up为true，表示在cur上级已经确定来，的情况下，cur整棵树能够提供最大的快乐值是多少？
	// 如果up为false，表示在cur上级已经确定不来，的情况下，cur整棵树能够提供最大的快乐值是多少？
	public static int process1(Employee cur, boolean up) {
		if (up) { // 如果cur的上级来的话，cur没得选，只能不来
			int ans = 0;
			for (Employee next : cur.nexts) {
				ans += process1(next, false);
			}
			return ans;
		} else { // 如果cur的上级不来的话，cur可以选，可以来也可以不来
			int p1 = cur.happy;
			int p2 = 0;
			for (Employee next : cur.nexts) {
				p1 += process1(next, true);
				p2 += process1(next, false);
			}
			return Math.max(p1, p2);
		}
	}

	public static int maxHappy2(Employee head) {
		Info allInfo = process(head);
		return Math.max(allInfo.no, allInfo.yes);
	}

	public static class Info {
		public int no;
		public int yes;

		public Info(int n, int y) {
			no = n;
			yes = y;
		}
	}

	public static Info process(Employee x) {
		if (x == null) {
			return new Info(0, 0);
		}
		int no = 0;
		int yes = x.happy;
		for (Employee next : x.nexts) {
			Info nextInfo = process(next);
			no += Math.max(nextInfo.no, nextInfo.yes);
			// 本人参加，直属下级必须选择不参加状态。
			yes += nextInfo.no;

		}
		return new Info(no, yes);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static Employee genarateBoss(int maxLevel, int maxNexts, int maxHappy) {
		if (Math.random() < 0.02) {
			return null;
		}
		Employee boss = new Employee((int) (Math.random() * (maxHappy + 1)));
		genarateNexts(boss, 1, maxLevel, maxNexts, maxHappy);
		return boss;
	}

	// for test
	public static void genarateNexts(Employee e, int level, int maxLevel, int maxNexts, int maxHappy) {
		if (level > maxLevel) {
			return;
		}
		int nextsSize = (int) (Math.random() * (maxNexts + 1));
		for (int i = 0; i < nextsSize; i++) {
			Employee next = new Employee((int) (Math.random() * (maxHappy + 1)));
			e.nexts.add(next);
			genarateNexts(next, level + 1, maxLevel, maxNexts, maxHappy);
		}
	}

public static void main(String[] args) {
		int maxLevel = 4;
		int maxNexts = 7;
		int maxHappy = 100;
		int testTimes = 100000;
		for (int i = 0; i < testTimes; i++) {
			Employee boss = genarateBoss(maxLevel, maxNexts, maxHappy);
			if (maxHappy1(boss) != maxHappy2(boss)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("finish!");
	}
```

#### 题解

**为什么正确**

唯一限制发生在直接上下级之间，因此固定当前员工是否参加后，各直属下级子树互不影响，可以分别优化再相加。两种状态穷尽当前员工所有选择。

**复杂度**

时间 O(N)，递归栈 O(H)。

**边界与易错点**

本人不来不意味着下级必须来，应取两种状态最大值。限制只在直接上下级，不是禁止所有祖孙同时参加。

### 13.5 拼接字符串的最小字典序

#### 题目

给定字符串数组，选择一种拼接顺序，使所有字符串拼接后的结果字典序最小。

**输入、输出与约束**

输入非 null 字符串数组及非 null 元素；每个元素使用一次，返回最小字典序拼接。

**函数签名（课程入口）**

```java
public static String lowestString1(String[] strs);
public static String[] removeIndexString(String[] arr, int index);
public int compare(String a, String b);
public static String lowestString2(String[] strs);
```

**示例**

```text
输入：strs=["b","ba"]
输出："bab"
```

解释：ba+b 比 b+ba 更小，所以 ba 应排在 b 前。

**出处与版本差异**

- [课程源码：class13/Code05_LowestLexicography.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class13/Code05_LowestLexicography.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

两个字符串 a、b 的先后不应按 a 与 b 本身比较，而应比较 a+b 和 b+a。按此拼接比较器排序后依次连接全部字符串。暴力版枚举全排列作为小规模基准。

#### 代码答案

```java
package class13;

import java.util.Arrays;
import java.util.Comparator;
import java.util.TreeSet;

public class Code05_LowestLexicography {

	public static String lowestString1(String[] strs) {
		if (strs == null || strs.length == 0) {
			return "";
		}
		TreeSet<String> ans = process(strs);
		return ans.size() == 0 ? "" : ans.first();
	}

	// strs中所有字符串全排列，返回所有可能的结果
	public static TreeSet<String> process(String[] strs) {
		TreeSet<String> ans = new TreeSet<>();
		if (strs.length == 0) {
			ans.add("");
			return ans;
		}
		for (int i = 0; i < strs.length; i++) {
			String first = strs[i];
			String[] nexts = removeIndexString(strs, i);
			TreeSet<String> next = process(nexts);
			for (String cur : next) {
				ans.add(first + cur);
			}
		}
		return ans;
	}

	// {"abc", "cks", "bct"}
	// 0 1 2
	// removeIndexString(arr , 1) -> {"abc", "bct"}
	public static String[] removeIndexString(String[] arr, int index) {
		int N = arr.length;
		String[] ans = new String[N - 1];
		int ansIndex = 0;
		for (int i = 0; i < N; i++) {
			if (i != index) {
				ans[ansIndex++] = arr[i];
			}
		}
		return ans;
	}

	public static class MyComparator implements Comparator<String> {
		@Override
		public int compare(String a, String b) {
			// 比较两种相邻拼接的整体字典序，才能决定正确先后。
			return (a + b).compareTo(b + a);
		}
	}

	public static String lowestString2(String[] strs) {
		if (strs == null || strs.length == 0) {
			return "";
		}
		// 消除拼接比较意义上的逆序，得到可直接连接的顺序。
		Arrays.sort(strs, new MyComparator());
		String res = "";
		for (int i = 0; i < strs.length; i++) {
			res += strs[i];
		}
		return res;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static String generateRandomString(int strLen) {
		char[] ans = new char[(int) (Math.random() * strLen) + 1];
		for (int i = 0; i < ans.length; i++) {
			int value = (int) (Math.random() * 5);
			ans[i] = (Math.random() <= 0.5) ? (char) (65 + value) : (char) (97 + value);
		}
		return String.valueOf(ans);
	}

	// for test
	public static String[] generateRandomStringArray(int arrLen, int strLen) {
		String[] ans = new String[(int) (Math.random() * arrLen) + 1];
		for (int i = 0; i < ans.length; i++) {
			ans[i] = generateRandomString(strLen);
		}
		return ans;
	}

	// for test
	public static String[] copyStringArray(String[] arr) {
		String[] ans = new String[arr.length];
		for (int i = 0; i < ans.length; i++) {
			ans[i] = String.valueOf(arr[i]);
		}
		return ans;
	}

public static void main(String[] args) {
		int arrLen = 6;
		int strLen = 5;
		int testTimes = 10000;
		System.out.println("test begin");
		for (int i = 0; i < testTimes; i++) {
			String[] arr1 = generateRandomStringArray(arrLen, strLen);
			String[] arr2 = copyStringArray(arr1);
			if (!lowestString1(arr1).equals(lowestString2(arr2))) {
				for (String str : arr1) {
					System.out.print(str + ",");
				}
				System.out.println();
				System.out.println("Oops!");
			}
		}
		System.out.println("finish!");
	}
```

#### 题解

**为什么正确**

若相邻字符串的顺序让 a+b>b+a，交换它们会严格改善整个拼接结果，而不改变前缀和后缀。排序结果没有这种相邻逆序；拼接比较关系满足可用于排序的传递性，因而可通过交换把最优序列整理为该顺序而不变差。

**复杂度**

比较排序约 O(N log N) 次比较，每次构造/比较拼接串需 O(L)，L 为参与比较字符串总长；最终输出另计总字符数。

**边界与易错点**

直接按字符串自然序排序会错，例如 b 与 ba。源码连续不可变字符串相加还可能重复复制输出，工程版本宜用 StringBuilder。


<a id="course-14"></a>

## 第 14 课：贪心算法与并查集

### 14.1 道路照明所需的最少灯数

#### 题目

道路字符串只含墙 `X` 和居民点 `.`；灯只能放在居民点并照亮自身及相邻位置，返回照亮所有居民点所需的最少灯数。

**输入、输出与约束**

字符串只含 X 和 .；灯放在 . 上并照亮自身及相邻位置，返回照亮所有 . 的最少灯数。

**函数签名（课程入口）**

```java
public static int minLight1(String road);
public static int minLight2(String road);
public static int minLight3(String road);
```

**示例**

```text
输入：road="...X.."
输出：2
```

解释：前段在中间放一盏，后段任一位置放一盏。

**出处与版本差异**

- [课程源码：class14/Code01_Light.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class14/Code01_Light.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

从最左侧尚未处理的位置 i 开始。墙可直接跳过；若 i 是居民点，必须安排一盏能照到它的灯。当 i+1 也是居民点时把灯放在 i+1，以覆盖尽可能靠右的位置；否则只能放在 i，然后跳过已覆盖范围。

#### 代码答案

```java
package class14;

import java.util.HashSet;

public class Code01_Light {

	public static int minLight1(String road) {
		if (road == null || road.length() == 0) {
			return 0;
		}
		return process(road.toCharArray(), 0, new HashSet<>());
	}

	// str[index....]位置，自由选择放灯还是不放灯
	// str[0..index-1]位置呢？已经做完决定了，那些放了灯的位置，存在lights里
	// 要求选出能照亮所有.的方案，并且在这些有效的方案中，返回最少需要几个灯
	public static int process(char[] str, int index, HashSet<Integer> lights) {
		if (index == str.length) { // 结束的时候
			for (int i = 0; i < str.length; i++) {
				if (str[i] != 'X') { // 当前位置是点的话
					if (!lights.contains(i - 1) && !lights.contains(i) && !lights.contains(i + 1)) {
						return Integer.MAX_VALUE;
					}
				}
			}
			return lights.size();
		} else { // str还没结束
			// i X .
			int no = process(str, index + 1, lights);
			int yes = Integer.MAX_VALUE;
			if (str[index] == '.') {
				lights.add(index);
				yes = process(str, index + 1, lights);
				lights.remove(index);
			}
			return Math.min(no, yes);
		}
	}

	public static int minLight2(String road) {
		char[] str = road.toCharArray();
		int i = 0;
		int light = 0;
		while (i < str.length) {
			if (str[i] == 'X') {
				i++;
			} else {
				// 当前最左未覆盖居民点必须由一盏新灯负责。
				light++;
				if (i + 1 == str.length) {
					break;
				} else { // 有i位置 i+ 1 X .
					if (str[i + 1] == 'X') {
						// 右侧是墙，当前点放灯即可，跳过墙后继续。
						i = i + 2;
					} else {
						// 灯放到 i+1 后覆盖 i、i+1、i+2，下次从 i+3 继续。
						i = i + 3;
					}
				}
			}
		}
		return light;
	}

	// 更简洁的解法
	// 两个X之间，数一下.的数量，然后除以3，向上取整
	// 把灯数累加
	public static int minLight3(String road) {
		char[] str = road.toCharArray();
		int cur = 0;
		int light = 0;
		for (char c : str) {
			if (c == 'X') {
				light += (cur + 2) / 3;
				cur = 0;
			} else {
				cur++;
			}
		}
		light += (cur + 2) / 3;
		return light;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static String randomString(int len) {
		char[] res = new char[(int) (Math.random() * len) + 1];
		for (int i = 0; i < res.length; i++) {
			res[i] = Math.random() < 0.5 ? 'X' : '.';
		}
		return String.valueOf(res);
	}

public static void main(String[] args) {
		int len = 20;
		int testTime = 100000;
		for (int i = 0; i < testTime; i++) {
			String test = randomString(len);
			int ans1 = minLight1(test);
			int ans2 = minLight2(test);
			int ans3 = minLight3(test);
			if (ans1 != ans2 || ans1 != ans3) {
				System.out.println("oops!");
			}
		}
		System.out.println("finish!");
	}
```

#### 题解

**为什么正确**

最左未覆盖居民点只能被附近灯照亮，把负责它的灯尽可能向右移动不会影响已经处理好的左侧，却能覆盖更多未来位置。因此总存在遵循这一选择的最优方案，可逐段贪心。

**复杂度**

贪心时间 O(N)、空间 O(1)；枚举放灯的基准指数时间。

**边界与易错点**

墙上不能放灯。居民点后若是墙，只跨过当前点及墙；若后面仍是居民点，可跨过三格。不要把灯的覆盖范围误当成只能照同一连续段。

### 14.2 切分金条的最小代价

#### 题目

给定若干目标长度，每次把一段金条切成两段的代价等于该段长度，返回完成全部切分的最小总代价。

**输入、输出与约束**

输入各段正长度；返回最小切分总代价，少于两段时为 0。

**函数签名（课程入口）**

```java
public static int lessMoney1(int[] arr);
public static int lessMoney2(int[] arr);
```

**示例**

```text
输入：arr=[10,20,30]
输出：90
```

解释：先合并 10+20 花 30，再合并 30+30 花 60。

**出处与版本差异**

- [课程源码：class14/Code02_LessMoneySplitGold.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class14/Code02_LessMoneySplitGold.java)。
- [LeetCode 对应题 1167. 连接木棍的最低费用（Minimum Cost to Connect Sticks）](https://leetcode.com/problems/minimum-cost-to-connect-sticks/)

#### 思路

把切分过程倒过来看：每次合并两段的代价是长度之和，总代价不变。用小根堆反复取最短两段合并，累加合并代价，并把新段放回堆。

#### 代码答案

```java
package class14;

import java.util.PriorityQueue;

public class Code02_LessMoneySplitGold {

	// 纯暴力！
	public static int lessMoney1(int[] arr) {
		if (arr == null || arr.length == 0) {
			return 0;
		}
		return process(arr, 0);
	}

	// 等待合并的数都在arr里，pre之前的合并行为产生了多少总代价
	// arr中只剩一个数字的时候，停止合并，返回最小的总代价
	public static int process(int[] arr, int pre) {
		if (arr.length == 1) {
			return pre;
		}
		int ans = Integer.MAX_VALUE;
		for (int i = 0; i < arr.length; i++) {
			for (int j = i + 1; j < arr.length; j++) {
				ans = Math.min(ans, process(copyAndMergeTwo(arr, i, j), pre + arr[i] + arr[j]));
			}
		}
		return ans;
	}

	public static int[] copyAndMergeTwo(int[] arr, int i, int j) {
		int[] ans = new int[arr.length - 1];
		int ansi = 0;
		for (int arri = 0; arri < arr.length; arri++) {
			if (arri != i && arri != j) {
				ans[ansi++] = arr[arri];
			}
		}
		ans[ansi] = arr[i] + arr[j];
		return ans;
	}

	public static int lessMoney2(int[] arr) {
		PriorityQueue<Integer> pQ = new PriorityQueue<>();
		for (int i = 0; i < arr.length; i++) {
			pQ.add(arr[i]);
		}
		int sum = 0;
		int cur = 0;
		while (pQ.size() > 1) {
			// 取当前最短两段组成兄弟节点，这是可交换证明保证的最优选择。
			cur = pQ.poll() + pQ.poll();
			// 本次合并费用需要加入总费用，后续还会再次承担这段长度。
			sum += cur;
			// 合并段作为一个新整体参与下一轮选择。
			pQ.add(cur);
		}
		return sum;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) (Math.random() * (maxValue + 1));
		}
		return arr;
	}

public static void main(String[] args) {
		int testTime = 100000;
		int maxSize = 6;
		int maxValue = 1000;
		for (int i = 0; i < testTime; i++) {
			int[] arr = generateRandomArray(maxSize, maxValue);
			if (lessMoney1(arr) != lessMoney2(arr)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("finish!");
	}
```

#### 题解

**为什么正确**

任意合并方案对应一棵二叉树，总代价等于各原长度乘叶子深度之和。可把最小的两个权重交换到最深的一对兄弟叶而不增大代价；合并它们后缩成较小同类问题，递归得到最优。

**复杂度**

时间 O(N log N)，空间 O(N)；暴力枚举合并顺序仅适合小 N。

**边界与易错点**

总费用需累加每次合并，不是只看最后总长度。大长度或大量片段可能使 int 溢出，扩展时使用 long。

### 14.3 最多可安排的会议数量

#### 题目

给定若干会议的开始和结束时间，同一会议室不能同时举行两场会议，返回最多能安排的会议数量。

**输入、输出与约束**

每场 0≤start<end；只用一间会议室，返回最多能完整参加的场数。

**函数签名（课程入口）**

```java
public static int bestArrange1(Program[] programs);
public static int bestArrange2(Program[] programs);
public int compare(Program o1, Program o2);
```

**示例**

```text
输入：meetings=[[1,3],[2,4],[3,5]]
输出：2
```

解释：选择 [1,3] 与 [3,5]。

**出处与版本差异**

- [课程源码：class14/Code03_BestArrange.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class14/Code03_BestArrange.java)。
- [LeetCode 对应题 435. 无重叠区间（Non-overlapping Intervals）](https://leetcode.com/problems/non-overlapping-intervals/)

课程版本说明：

- LeetCode 435：目标可通过总数减最少删除数互相转换。

#### 思路

按结束时间升序排列会议。从当前可用时间 timeLine 出发，只要会议开始时间不早于它就安排，并把 timeLine 更新为该会议结束时间。

#### 代码答案

```java
package class14;

import java.util.Arrays;
import java.util.Comparator;

public class Code03_BestArrange {

	public static class Program {
		public int start;
		public int end;

		public Program(int start, int end) {
			this.start = start;
			this.end = end;
		}
	}

	// 暴力！所有情况都尝试！
	public static int bestArrange1(Program[] programs) {
		if (programs == null || programs.length == 0) {
			return 0;
		}
		return process(programs, 0, 0);
	}

	// 还剩下的会议都放在programs里
	// done之前已经安排了多少会议的数量
	// timeLine目前来到的时间点是什么

	// 目前来到timeLine的时间点，已经安排了done多的会议，剩下的会议programs可以自由安排
	// 返回能安排的最多会议数量
	public static int process(Program[] programs, int done, int timeLine) {
		if (programs.length == 0) {
			return done;
		}
		// 还剩下会议
		int max = done;
		// 当前安排的会议是什么会，每一个都枚举
		for (int i = 0; i < programs.length; i++) {
			if (programs[i].start >= timeLine) {
				Program[] next = copyButExcept(programs, i);
				max = Math.max(max, process(next, done + 1, programs[i].end));
			}
		}
		return max;
	}

	public static Program[] copyButExcept(Program[] programs, int i) {
		Program[] ans = new Program[programs.length - 1];
		int index = 0;
		for (int k = 0; k < programs.length; k++) {
			if (k != i) {
				ans[index++] = programs[k];
			}
		}
		return ans;
	}

	// 会议的开始时间和结束时间，都是数值，不会 < 0
	public static int bestArrange2(Program[] programs) {
		Arrays.sort(programs, new ProgramComparator());
		int timeLine = 0;
		int result = 0;
		// 依次遍历每一个会议，结束时间早的会议先遍历
		for (int i = 0; i < programs.length; i++) {
			// 上一场结束后即可开始下一场，边界相等允许无缝衔接。
			if (timeLine <= programs[i].start) {
				result++;
				// 接受会议后，下次最早可用时间变为本场结束时刻。
				timeLine = programs[i].end;
			}
		}
		return result;
	}

	public static class ProgramComparator implements Comparator<Program> {

		@Override
		public int compare(Program o1, Program o2) {
			return o1.end - o2.end;
		}

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static Program[] generatePrograms(int programSize, int timeMax) {
		Program[] ans = new Program[(int) (Math.random() * (programSize + 1))];
		for (int i = 0; i < ans.length; i++) {
			int r1 = (int) (Math.random() * (timeMax + 1));
			int r2 = (int) (Math.random() * (timeMax + 1));
			if (r1 == r2) {
				ans[i] = new Program(r1, r1 + 1);
			} else {
				ans[i] = new Program(Math.min(r1, r2), Math.max(r1, r2));
			}
		}
		return ans;
	}

public static void main(String[] args) {
		int programSize = 12;
		int timeMax = 20;
		int timeTimes = 1000000;
		for (int i = 0; i < timeTimes; i++) {
			Program[] programs = generatePrograms(programSize, timeMax);
			if (bestArrange1(programs) != bestArrange2(programs)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("finish!");
	}
```

#### 题解

**为什么正确**

若最优方案第一场不是最早结束的可选会议，可用最早结束者替换它；替换后结束不更晚，不会挤掉后续会议。因此存在以贪心首场开始的最优方案，对剩余会议重复成立。

**复杂度**

时间 O(N log N)，扫描 O(N)；对象排序可能使用 O(N) 辅助空间，贪心状态 O(1)。

**边界与易错点**

比较的是结束时间，不是时长或开始时间。start==timeLine 可以紧接开会。课程从 timeLine=0 起步，输入时间应非负。

### 14.4 有限项目数下的最大资本

#### 题目

给定项目利润、启动资金、初始资本和最多可做项目数，每次只能选择当前资本可启动的项目，返回最终最大资本。

**输入、输出与约束**

项目只可执行一次，资本门槛和利润非负；返回至多 K 个项目后的最大资本，W 的累加需避免 int 溢出。

**函数签名（课程入口）**

```java
public static int findMaximizedCapital(int K, int W, int[] Profits, int[] Capital);
public int compare(Program o1, Program o2);
```

**示例**

```text
输入：K=2,W=0,profits=[1,2,3],capital=[0,1,1]
输出：4
```

解释：先做利润 1 的项目，再从新解锁项目中选择利润 3。

**出处与版本差异**

- [课程源码：class14/Code04_IPO.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class14/Code04_IPO.java)。
- [LeetCode 原题 502. IPO（IPO）](https://leetcode.com/problems/ipo/)

#### 思路

小根堆按启动资本保存尚未解锁项目，大根堆按利润保存当前可做项目。每一轮把资本要求≤当前 W 的项目全部转入利润堆，再选利润最大的做一次，增加 W；至多执行 K 轮。

#### 代码答案

```java
package class14;

import java.util.Comparator;
import java.util.PriorityQueue;

public class Code04_IPO {

	// 最多K个项目
	// W是初始资金
	// Profits[] Capital[] 一定等长
	// 返回最终最大的资金
	public static int findMaximizedCapital(int K, int W, int[] Profits, int[] Capital) {
		PriorityQueue<Program> minCostQ = new PriorityQueue<>(new MinCostComparator());
		PriorityQueue<Program> maxProfitQ = new PriorityQueue<>(new MaxProfitComparator());
		for (int i = 0; i < Profits.length; i++) {
			minCostQ.add(new Program(Profits[i], Capital[i]));
		}
		for (int i = 0; i < K; i++) {
			// 当前资金已达到门槛，项目从未解锁区转到可选区。
			while (!minCostQ.isEmpty() && minCostQ.peek().c <= W) {
				maxProfitQ.add(minCostQ.poll());
			}
			if (maxProfitQ.isEmpty()) {
				return W;
			}
			// 选择所有可行项目中利润最大的，以尽量扩大下一轮选择范围。
			W += maxProfitQ.poll().p;
		}
		return W;
	}

	public static class Program {
		public int p;
		public int c;

		public Program(int p, int c) {
			this.p = p;
			this.c = c;
		}
	}

	public static class MinCostComparator implements Comparator<Program> {

		@Override
		public int compare(Program o1, Program o2) {
			return o1.c - o2.c;
		}

	}

	public static class MaxProfitComparator implements Comparator<Program> {

		@Override
		public int compare(Program o1, Program o2) {
			return o2.p - o1.p;
		}

	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：每轮资本增加后要重新解锁项目。没有可做项目就提前结束。若允许负利润，“至多 K 个”应停止而非强做；当前题使用非负利润。

本条未附独立随机对数器。

#### 题解

**为什么正确**

利润非负且没有其他依赖时，选择当前最大利润使下一轮资本不小于选择任意其他可行项目，因而不会少解锁未来机会。以此逐轮替换可得到不劣于任意方案的贪心选择。

**复杂度**

N 个项目总进出堆常数次，时间 O(N log N+K log N)，空间 O(N)。

**边界与易错点**

每轮资本增加后要重新解锁项目。没有可做项目就提前结束。若允许负利润，“至多 K 个”应停止而非强做；当前题使用非负利润。

### 14.5 并查集实现

#### 题目

给定初始元素集合，构造并查集，支持查询两个元素是否同组、合并两组并查询集合数量。所有参与操作的元素必须已在构造时登记。

**输入、输出与约束**

对象版输入已登记对象，数组版输入已初始化编号；提供代表查询、合并和同集合判断。

**函数签名（课程入口）**

```java
public V findFather(V cur);
public boolean isSameSet(V a, V b);
public void union(V a, V b);
public int sets();
```

**示例**

```text
输入：元素1、2、3；union(1,2)，isSameSet(1,3)，union(2,3)
输出：false；合并后 1、3 同集合
```

解释：第二次合并通过代表根连接两个集合。

**出处与版本差异**

- [课程源码：class14/Code05_UnionFind.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class14/Code05_UnionFind.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

每个集合用一棵父指针树表示，根是代表。find 沿父链走到根，并让沿途节点直接指向根。union 先找两个根，不同才把小集合根挂到大集合根上，并更新新根大小。对象版和整数数组版遵循相同结构。

#### 代码答案

```java
package class14;

import java.util.HashMap;
import java.util.List;
import java.util.Stack;

// 课上讲的并查集实现
// 请务必看补充的Code06_UnionFind
// 那是数组实现的并查集，并且有测试链接
// 可以直接通过
// 这个文件的并查集是用map实现的
// 但是笔试或者平时用的并查集一律用数组实现
// 所以Code06_UnionFind更具实战意义
// 一定要看！
public class Code05_UnionFind {

	// 课上讲的时候
	// 包了一层
	// 其实不用包一层哦
	public static class UnionFind<V> {
		public HashMap<V, V> father;
		public HashMap<V, Integer> size;

		public UnionFind(List<V> values) {
			father = new HashMap<>();
			size = new HashMap<>();
			for (V cur : values) {
				father.put(cur, cur);
				size.put(cur, 1);
			}
		}

		// 给你一个节点，请你往上到不能再往上，把代表返回
		public V findFather(V cur) {
			Stack<V> path = new Stack<>();
			while (cur != father.get(cur)) {
				path.push(cur);
				cur = father.get(cur);
			}
			while (!path.isEmpty()) {
				// 将路径上每个节点直接接到代表节点，缩短后续查找路径。
				father.put(path.pop(), cur);
			}
			return cur;
		}

		public boolean isSameSet(V a, V b) {
			return findFather(a) == findFather(b);
		}

		public void union(V a, V b) {
			V aFather = findFather(a);
			V bFather = findFather(b);
			if (aFather != bFather) {
				int aSize = size.get(aFather);
				int bSize = size.get(bFather);
				if (aSize >= bSize) {
					// 把较小集合的代表接到较大集合，限制树高。
					father.put(bFather, aFather);
					size.put(aFather, aSize + bSize);
					// 被合并代表不再是独立集合，删除其大小记录。
					size.remove(bFather);
				} else {
					father.put(aFather, bFather);
					size.put(bFather, aSize + bSize);
					size.remove(aFather);
				}
			}
		}

		public int sets() {
			return size.size();
		}

	}
}
```

---

##### 边界核对

用题面示例核对接口，再检查：只有代表根的 size 有意义。两元素已经同根时不可再次相加大小。数组版初始化每组数据，编号范围必须合法。

本条未附独立随机对数器。

#### 题解

**为什么正确**

父链最终到同一根等价于属于同一集合。路径压缩只改到原来就能到达的根，不改变集合归属；合并仅连接两个不同根，恰把两个集合并成一个而不产生环。

**复杂度**

路径压缩加按大小合并，连续 M 次操作均摊 O(M α(N))，α 为反阿克曼函数；空间 O(N)。

**边界与易错点**

只有代表根的 size 有意义。两元素已经同根时不可再次相加大小。数组版初始化每组数据，编号范围必须合法。

### 14.6 并查集实现

#### 题目

给定初始元素集合，构造并查集，支持查询两个元素是否同组、合并两组并查询集合数量。所有参与操作的元素必须已在构造时登记。

**输入、输出与约束**

对象版输入已登记对象，数组版输入已初始化编号；提供代表查询、合并和同集合判断。

**函数签名（课程入口）**

```java
public static void init(int n);
public static int find(int i);
public static boolean isSameSet(int x, int y);
public static void union(int x, int y);
```

**示例**

```text
输入：元素1、2、3；union(1,2)，isSameSet(1,3)，union(2,3)
输出：false；合并后 1、3 同集合
```

解释：第二次合并通过代表根连接两个集合。

**出处与版本差异**

- [课程源码：class14/Code06_UnionFind.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class14/Code06_UnionFind.java)。
- [牛客网在线评测](https://www.nowcoder.com/questionTerminal/e7ed657974934a30b2010046536a5372)。

#### 思路

每个集合用一棵父指针树表示，根是代表。find 沿父链走到根，并让沿途节点直接指向根。union 先找两个根，不同才把小集合根挂到大集合根上，并更新新根大小。对象版和整数数组版遵循相同结构。

#### 代码答案

```java
package class14;

// 这个文件课上没有讲
// 原理和课上讲的完全一样
// 最大的区别就是这个文件实现的并查集是用数组结构，而不是map结构
// 请务必理解这个文件的实现，而且还提供了测试链接
// 提交如下的code，并把"Code06_UnionFind"这个类名改成"Main"
// 在测试链接里可以直接通过
// 请同学们务必参考如下代码中关于输入、输出的处理
// 这是输入输出处理效率很高的写法
// 测试链接 : https://www.nowcoder.com/questionTerminal/e7ed657974934a30b2010046536a5372
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.io.StreamTokenizer;

public class Code06_UnionFind {

	public static int MAXN = 1000001;

	public static int[] father = new int[MAXN];

	public static int[] size = new int[MAXN];

	public static int[] help = new int[MAXN];

	// 初始化并查集
	public static void init(int n) {
		for (int i = 0; i <= n; i++) {
			// 初始化时每个编号自成集合，父亲就是自己。
			father[i] = i;
			size[i] = 1;
		}
	}

	// 从i开始寻找集合代表点
	public static int find(int i) {
		int hi = 0;
		while (i != father[i]) {
			help[hi++] = i;
			i = father[i];
		}
		for (hi--; hi >= 0; hi--) {
			father[help[hi]] = i;
		}
		return i;
	}

	// 查询x和y是不是一个集合
	public static boolean isSameSet(int x, int y) {
		return find(x) == find(y);
	}

	// x所在的集合，和y所在的集合，合并成一个集合
	public static void union(int x, int y) {
		int fx = find(x);
		int fy = find(y);
		if (fx != fy) {
			if (size[fx] >= size[fy]) {
				size[fx] += size[fy];
				father[fy] = fx;
			} else {
				size[fy] += size[fx];
				father[fx] = fy;
			}
		}
	}

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StreamTokenizer in = new StreamTokenizer(br);
		PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
		while (in.nextToken() != StreamTokenizer.TT_EOF) {
			int n = (int) in.nval;
			init(n);
			in.nextToken();
			int m = (int) in.nval;
			for (int i = 0; i < m; i++) {
				in.nextToken();
				int op = (int) in.nval;
				in.nextToken();
				int x = (int) in.nval;
				in.nextToken();
				int y = (int) in.nval;
				if (op == 1) {
					out.println(isSameSet(x, y) ? "Yes" : "No");
					out.flush();
				} else {
					union(x, y);
				}
			}
		}
	}
}
```

---

##### 输入输出核对

先以本题示例核对结果，再重点覆盖：只有代表根的 size 有意义。两元素已经同根时不可再次相加大小。数组版初始化每组数据，编号范围必须合法。

该版本保留在线评测入口；未附独立随机对数器，不把编译通过当作正确性证明。

#### 题解

**为什么正确**

父链最终到同一根等价于属于同一集合。路径压缩只改到原来就能到达的根，不改变集合归属；合并仅连接两个不同根，恰把两个集合并成一个而不产生环。

**复杂度**

路径压缩加按大小合并，连续 M 次操作均摊 O(M α(N))，α 为反阿克曼函数；空间 O(N)。

**边界与易错点**

只有代表根的 size 有意义。两元素已经同根时不可再次相加大小。数组版初始化每组数据，编号范围必须合法。


<a id="course-15"></a>

## 第 15 课：并查集的岛屿问题

### 15.1 省份数量（朋友圈）

#### 题目

给定城市连接矩阵，直接或间接相连的城市属于同一省份，返回省份总数。

**输入、输出与约束**

输入 N×N 对称 0/1 连接矩阵；返回省份数量。

**函数签名（课程入口）**

```java
public static int findCircleNum(int[][] M);
public void union(int i, int j);
public int sets();
```

**示例**

```text
输入：matrix=[[1,1,0],[1,1,0],[0,0,1]]
输出：2
```

解释：城市 0、1 连通，城市 2 独立。

**出处与版本差异**

- [课程源码：class15/Code01_FriendCircles.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class15/Code01_FriendCircles.java)。
- [LeetCode 原题 547. 省份数量（Number of Provinces）](https://leetcode.com/problems/number-of-provinces/)

课程版本说明：

- LeetCode 547：课程源码使用旧题名 Friend Circles。

#### 思路

每个城市先自成集合。遍历连接矩阵上三角，遇到连接就合并两城市。并查集每成功合并两个不同集合，省份计数减一。

#### 代码答案

```java
package class15;

// 本题为leetcode原题
// 测试链接：https://leetcode.com/problems/friend-circles/
// 可以直接通过
public class Code01_FriendCircles {

	public static int findCircleNum(int[][] M) {
		int N = M.length;
		// {0} {1} {2} {N-1}
		UnionFind unionFind = new UnionFind(N);
		for (int i = 0; i < N; i++) {
			// 无向边在对称矩阵中出现两次，只扫描上三角即可。
			for (int j = i + 1; j < N; j++) {
				if (M[i][j] == 1) { // i和j互相认识
					unionFind.union(i, j);
				}
			}
		}
		return unionFind.sets();
	}

	public static class UnionFind {
		// parent[i] = k ： i的父亲是k
		private int[] parent;
		// size[i] = k ： 如果i是代表节点，size[i]才有意义，否则无意义
		// i所在的集合大小是多少
		private int[] size;
		// 辅助结构
		private int[] help;
		// 一共有多少个集合
		private int sets;

		public UnionFind(int N) {
			parent = new int[N];
			size = new int[N];
			help = new int[N];
			sets = N;
			for (int i = 0; i < N; i++) {
				parent[i] = i;
				size[i] = 1;
			}
		}

		// 从i开始一直往上，往上到不能再往上，代表节点，返回
		// 这个过程要做路径压缩
		private int find(int i) {
			int hi = 0;
			while (i != parent[i]) {
				help[hi++] = i;
				i = parent[i];
			}
			for (hi--; hi >= 0; hi--) {
				parent[help[hi]] = i;
			}
			return i;
		}

		public void union(int i, int j) {
			int f1 = find(i);
			int f2 = find(j);
			if (f1 != f2) {
				if (size[f1] >= size[f2]) {
					size[f1] += size[f2];
					parent[f2] = f1;
				} else {
					size[f2] += size[f1];
					parent[f1] = f2;
				}
				// 只有原本不同的两个代表被连接时，连通分量数才减一。
				sets--;
			}
		}

		public int sets() {
			return sets;
		}
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：无向连接矩阵对称，只需检查一半。对角线表示自连，不应重复减少集合数。

本条未附独立随机对数器。

#### 题解

**为什么正确**

直接连接边通过合并保证两端同集合，传递闭包由共享代表自然表达；没有连接路径的两组城市不会被合并，最终集合恰是所有连通分量。

**复杂度**

矩阵检查 O(N²)，并查集操作均摊接近常数，总计 O(N² α(N)) 上界；额外空间 O(N)。

**边界与易错点**

无向连接矩阵对称，只需检查一半。对角线表示自连，不应重复减少集合数。

### 15.2 岛屿数量

#### 题目

给定由陆地和水组成的二维网格，水平或竖直相邻的陆地属于同一岛屿，返回岛屿数量。

**输入、输出与约束**

输入非空矩形字符网格，每格为 0 或 1；返回四连通岛屿数。

**函数签名（课程入口）**

```java
public static int numIslands3(char[][] board);
public static void infect(char[][] board, int i, int j);
public static int numIslands1(char[][] board);
public Node<V> findFather(Node<V> cur);
public void union(V a, V b);
public int sets();
```

**示例**

```text
输入：grid=["110","010","001"]
输出：2
```

解释：左上三块陆地连通，右下角仅斜向接触。

**出处与版本差异**

- [课程源码：class15/Code02_NumberOfIslands.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class15/Code02_NumberOfIslands.java)。
- [LeetCode 原题 200. 岛屿数量（Number of Islands）](https://leetcode.com/problems/number-of-islands/)

#### 思路

感染版扫描到一个尚未处理的陆地就增加岛数，再递归把四方向连通陆地标记掉。并查集版仅为陆地建立集合，与相邻陆地合并，最终集合数就是岛数。

#### 代码答案

```java
package class15;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Stack;

// 本题为leetcode原题
// 测试链接：https://leetcode.com/problems/number-of-islands/
// 所有方法都可以直接通过
public class Code02_NumberOfIslands {

	public static int numIslands3(char[][] board) {
		int islands = 0;
		for (int i = 0; i < board.length; i++) {
			for (int j = 0; j < board[0].length; j++) {
				if (board[i][j] == '1') {
					islands++;
					infect(board, i, j);
				}
			}
		}
		return islands;
	}

	// 从(i,j)这个位置出发，把所有练成一片的'1'字符，变成0
	public static void infect(char[][] board, int i, int j) {
		if (i < 0 || i == board.length || j < 0 || j == board[0].length || board[i][j] != '1') {
			return;
		}
		// 访问后立即标记，防止邻居递归时再回到此节点。
		board[i][j] = 0;
		infect(board, i - 1, j);
		// 只扩展四个正交方向，斜对角不属于同岛。
		infect(board, i + 1, j);
		infect(board, i, j - 1);
		infect(board, i, j + 1);
	}

	public static int numIslands1(char[][] board) {
		int row = board.length;
		int col = board[0].length;
		Dot[][] dots = new Dot[row][col];
		List<Dot> dotList = new ArrayList<>();
		for (int i = 0; i < row; i++) {
			for (int j = 0; j < col; j++) {
				if (board[i][j] == '1') {
					dots[i][j] = new Dot();
					dotList.add(dots[i][j]);
				}
			}
		}
		UnionFind1<Dot> uf = new UnionFind1<>(dotList);
		for (int j = 1; j < col; j++) {
			// (0,j)  (0,0)跳过了  (0,1) (0,2) (0,3)
			if (board[0][j - 1] == '1' && board[0][j] == '1') {
				uf.union(dots[0][j - 1], dots[0][j]);
			}
		}
		for (int i = 1; i < row; i++) {
			if (board[i - 1][0] == '1' && board[i][0] == '1') {
				uf.union(dots[i - 1][0], dots[i][0]);
			}
		}
		for (int i = 1; i < row; i++) {
			for (int j = 1; j < col; j++) {
				if (board[i][j] == '1') {
					if (board[i][j - 1] == '1') {
						uf.union(dots[i][j - 1], dots[i][j]);
					}
					if (board[i - 1][j] == '1') {
						uf.union(dots[i - 1][j], dots[i][j]);
					}
				}
			}
		}
		return uf.sets();
	}

	public static class Dot {

	}

	public static class Node<V> {

		V value;

		public Node(V v) {
			value = v;
		}

	}

	public static class UnionFind1<V> {
		public HashMap<V, Node<V>> nodes;
		public HashMap<Node<V>, Node<V>> parents;
		public HashMap<Node<V>, Integer> sizeMap;

		public UnionFind1(List<V> values) {
			nodes = new HashMap<>();
			parents = new HashMap<>();
			sizeMap = new HashMap<>();
			for (V cur : values) {
				Node<V> node = new Node<>(cur);
				nodes.put(cur, node);
				parents.put(node, node);
				sizeMap.put(node, 1);
			}
		}

		public Node<V> findFather(Node<V> cur) {
			Stack<Node<V>> path = new Stack<>();
			while (cur != parents.get(cur)) {
				path.push(cur);
				cur = parents.get(cur);
			}
			while (!path.isEmpty()) {
				parents.put(path.pop(), cur);
			}
			return cur;
		}

		public void union(V a, V b) {
			Node<V> aHead = findFather(nodes.get(a));
			Node<V> bHead = findFather(nodes.get(b));
			if (aHead != bHead) {
				int aSetSize = sizeMap.get(aHead);
				int bSetSize = sizeMap.get(bHead);
				Node<V> big = aSetSize >= bSetSize ? aHead : bHead;
				Node<V> small = big == aHead ? bHead : aHead;
				parents.put(small, big);
				sizeMap.put(big, aSetSize + bSetSize);
				sizeMap.remove(small);
			}
		}

		public int sets() {
			return sizeMap.size();
		}

	}

	public static int numIslands2(char[][] board) {
		int row = board.length;
		int col = board[0].length;
		UnionFind2 uf = new UnionFind2(board);
		for (int j = 1; j < col; j++) {
			if (board[0][j - 1] == '1' && board[0][j] == '1') {
				uf.union(0, j - 1, 0, j);
			}
		}
		for (int i = 1; i < row; i++) {
			if (board[i - 1][0] == '1' && board[i][0] == '1') {
				uf.union(i - 1, 0, i, 0);
			}
		}
		for (int i = 1; i < row; i++) {
			for (int j = 1; j < col; j++) {
				if (board[i][j] == '1') {
					if (board[i][j - 1] == '1') {
						uf.union(i, j - 1, i, j);
					}
					if (board[i - 1][j] == '1') {
						uf.union(i - 1, j, i, j);
					}
				}
			}
		}
		return uf.sets();
	}

	public static class UnionFind2 {
		private int[] parent;
		private int[] size;
		private int[] help;
		private int col;
		private int sets;

		public UnionFind2(char[][] board) {
			col = board[0].length;
			sets = 0;
			int row = board.length;
			int len = row * col;
			parent = new int[len];
			size = new int[len];
			help = new int[len];
			for (int r = 0; r < row; r++) {
				for (int c = 0; c < col; c++) {
					if (board[r][c] == '1') {
						int i = index(r, c);
						parent[i] = i;
						size[i] = 1;
						sets++;
					}
				}
			}
		}

		// (r,c) -> i
		private int index(int r, int c) {
			return r * col + c;
		}

		// 原始位置 -> 下标
		private int find(int i) {
			int hi = 0;
			while (i != parent[i]) {
				help[hi++] = i;
				i = parent[i];
			}
			for (hi--; hi >= 0; hi--) {
				parent[help[hi]] = i;
			}
			return i;
		}

		public void union(int r1, int c1, int r2, int c2) {
			int i1 = index(r1, c1);
			int i2 = index(r2, c2);
			int f1 = find(i1);
			int f2 = find(i2);
			if (f1 != f2) {
				if (size[f1] >= size[f2]) {
					size[f1] += size[f2];
					parent[f2] = f1;
				} else {
					size[f2] += size[f1];
					parent[f1] = f2;
				}
				sets--;
			}
		}

		public int sets() {
			return sets;
		}

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	public static char[][] generateRandomMatrix(int row, int col) {
		char[][] board = new char[row][col];
		for (int i = 0; i < row; i++) {
			for (int j = 0; j < col; j++) {
				board[i][j] = Math.random() < 0.5 ? '1' : '0';
			}
		}
		return board;
	}

	// 为了测试
	public static char[][] copy(char[][] board) {
		int row = board.length;
		int col = board[0].length;
		char[][] ans = new char[row][col];
		for (int i = 0; i < row; i++) {
			for (int j = 0; j < col; j++) {
				ans[i][j] = board[i][j];
			}
		}
		return ans;
	}

	// 为了测试
	public static void main(String[] args) {
		int row = 0;
		int col = 0;
		char[][] board1 = null;
		char[][] board2 = null;
		char[][] board3 = null;
		long start = 0;
		long end = 0;

		row = 1000;
		col = 1000;
		board1 = generateRandomMatrix(row, col);
		board2 = copy(board1);
		board3 = copy(board1);

		System.out.println("感染方法、并查集(map实现)、并查集(数组实现)的运行结果和运行时间");
		System.out.println("随机生成的二维矩阵规模 : " + row + " * " + col);

		start = System.currentTimeMillis();
		System.out.println("感染方法的运行结果: " + numIslands3(board1));
		end = System.currentTimeMillis();
		System.out.println("感染方法的运行时间: " + (end - start) + " ms");

		start = System.currentTimeMillis();
		System.out.println("并查集(map实现)的运行结果: " + numIslands1(board2));
		end = System.currentTimeMillis();
		System.out.println("并查集(map实现)的运行时间: " + (end - start) + " ms");

		start = System.currentTimeMillis();
		System.out.println("并查集(数组实现)的运行结果: " + numIslands2(board3));
		end = System.currentTimeMillis();
		System.out.println("并查集(数组实现)的运行时间: " + (end - start) + " ms");

		System.out.println();

		row = 10000;
		col = 10000;
		board1 = generateRandomMatrix(row, col);
		board3 = copy(board1);
		System.out.println("感染方法、并查集(数组实现)的运行结果和运行时间");
		System.out.println("随机生成的二维矩阵规模 : " + row + " * " + col);

		start = System.currentTimeMillis();
		System.out.println("感染方法的运行结果: " + numIslands3(board1));
		end = System.currentTimeMillis();
		System.out.println("感染方法的运行时间: " + (end - start) + " ms");

		start = System.currentTimeMillis();
		System.out.println("并查集(数组实现)的运行结果: " + numIslands2(board3));
		end = System.currentTimeMillis();
		System.out.println("并查集(数组实现)的运行时间: " + (end - start) + " ms");

	}
```

#### 题解

**为什么正确**

感染一次恰好覆盖起点所在的连通分量，后续扫描不会重复计数。并查集只沿水平、竖直陆地边合并，所得分量与可达陆地集合完全相同。

**复杂度**

R×C 网格：感染时间 O(RC)，最坏递归空间 O(RC)；并查集时间 O(RC α(RC))，额外空间 O(RC)。

**边界与易错点**

感染会修改输入，版本对拍必须各用副本。斜向接触不连通；大连通块可能使递归栈过深。字符网格使用字符 1，不是整数 1。

### 15.3 动态岛屿数量

#### 题目

初始网格全部为水，按顺序把指定位置变为陆地，返回每次操作后的岛屿数量。

**输入、输出与约束**

给定正行列数和合法坐标序列，网格初始全水；返回每次操作后的岛数。

**函数签名（课程入口）**

```java
public static List<Integer> numIslands21(int m, int n, int[][] positions);
public int connect(int r, int c);
public static List<Integer> numIslands22(int m, int n, int[][] positions);
```

**示例**

```text
输入：m=2,n=2,positions=[[0,0],[1,1],[0,1],[0,1]]
输出：[1,2,1,1]
```

解释：第三步连接两个岛，第四步是重复加入。

**出处与版本差异**

- [课程源码：class15/Code03_NumberOfIslandsII.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class15/Code03_NumberOfIslandsII.java)。
- [LeetCode 原题 305. 岛屿数量 II（Number of Islands II）](https://leetcode.com/problems/number-of-islands-ii/)

#### 思路

开始没有陆地，集合数为零。新增陆地时先建立单节点集合并加一，再尝试与四个已经存在的陆地邻居合并；每次连接不同根才减一。稠密版用数组，稀疏版用坐标键映射。

#### 代码答案

```java
package class15;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

// 本题为leetcode原题
// 测试链接：https://leetcode.com/problems/number-of-islands-ii/
// 所有方法都可以直接通过
public class Code03_NumberOfIslandsII {

	public static List<Integer> numIslands21(int m, int n, int[][] positions) {
		UnionFind1 uf = new UnionFind1(m, n);
		List<Integer> ans = new ArrayList<>();
		for (int[] position : positions) {
			ans.add(uf.connect(position[0], position[1]));
		}
		return ans;
	}

	public static class UnionFind1 {
		private int[] parent;
		private int[] size;
		private int[] help;
		private final int row;
		private final int col;
		private int sets;

		public UnionFind1(int m, int n) {
			row = m;
			col = n;
			sets = 0;
			int len = row * col;
			parent = new int[len];
			size = new int[len];
			help = new int[len];
		}

		private int index(int r, int c) {
			return r * col + c;
		}

		private int find(int i) {
			int hi = 0;
			while (i != parent[i]) {
				help[hi++] = i;
				i = parent[i];
			}
			for (hi--; hi >= 0; hi--) {
				parent[help[hi]] = i;
			}
			return i;
		}

		private void union(int r1, int c1, int r2, int c2) {
			if (r1 < 0 || r1 == row || r2 < 0 || r2 == row || c1 < 0 || c1 == col || c2 < 0 || c2 == col) {
				return;
			}
			int i1 = index(r1, c1);
			int i2 = index(r2, c2);
			if (size[i1] == 0 || size[i2] == 0) {
				return;
			}
			int f1 = find(i1);
			int f2 = find(i2);
			if (f1 != f2) {
				if (size[f1] >= size[f2]) {
					size[f1] += size[f2];
					parent[f2] = f1;
				} else {
					size[f2] += size[f1];
					parent[f1] = f2;
				}
				sets--;
			}
		}

		public int connect(int r, int c) {
			int index = index(r, c);
			// size 为零表示该位置尚未激活，重复加入时跳过创建。
			if (size[index] == 0) {
				parent[index] = index;
				size[index] = 1;
				// 新陆地先作为一个独立岛，再通过邻接合并减少岛数。
				sets++;
				union(r - 1, c, r, c);
				union(r + 1, c, r, c);
				union(r, c - 1, r, c);
				union(r, c + 1, r, c);
			}
			return sets;
		}

	}

	// 课上讲的如果m*n比较大，会经历很重的初始化，而k比较小，怎么优化的方法
	public static List<Integer> numIslands22(int m, int n, int[][] positions) {
		UnionFind2 uf = new UnionFind2();
		List<Integer> ans = new ArrayList<>();
		for (int[] position : positions) {
			ans.add(uf.connect(position[0], position[1]));
		}
		return ans;
	}

	public static class UnionFind2 {
		private HashMap<String, String> parent;
		private HashMap<String, Integer> size;
		private ArrayList<String> help;
		private int sets;

		public UnionFind2() {
			parent = new HashMap<>();
			size = new HashMap<>();
			help = new ArrayList<>();
			sets = 0;
		}

		private String find(String cur) {
			while (!cur.equals(parent.get(cur))) {
				help.add(cur);
				cur = parent.get(cur);
			}
			for (String str : help) {
				parent.put(str, cur);
			}
			help.clear();
			return cur;
		}

		private void union(String s1, String s2) {
			if (parent.containsKey(s1) && parent.containsKey(s2)) {
				String f1 = find(s1);
				String f2 = find(s2);
				if (!f1.equals(f2)) {
					int size1 = size.get(f1);
					int size2 = size.get(f2);
					String big = size1 >= size2 ? f1 : f2;
					String small = big == f1 ? f2 : f1;
					parent.put(small, big);
					size.put(big, size1 + size2);
					sets--;
				}
			}
		}

		public int connect(int r, int c) {
			String key = String.valueOf(r) + "_" + String.valueOf(c);
			if (!parent.containsKey(key)) {
				parent.put(key, key);
				size.put(key, 1);
				// 新陆地先作为一个独立岛，再通过邻接合并减少岛数。
				sets++;
				String up = String.valueOf(r - 1) + "_" + String.valueOf(c);
				String down = String.valueOf(r + 1) + "_" + String.valueOf(c);
				String left = String.valueOf(r) + "_" + String.valueOf(c - 1);
				String right = String.valueOf(r) + "_" + String.valueOf(c + 1);
				union(up, key);
				union(down, key);
				union(left, key);
				union(right, key);
			}
			return sets;
		}

	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：同一位置重复增加不应改变答案。只合并已激活陆地，不能把默认父值当作已经存在的节点。

本条未附独立随机对数器。

#### 题解

**为什么正确**

新增位置只会创建一个新分量，并通过至多四条新边连接已有分量；所有可能的连通变化都来自这些边。重复邻居属于同一岛时，同根检查防止重复扣数。

**复杂度**

Q 次增加：稠密版初始化 O(RC)，维护 O(Q α(RC))，空间 O(RC)；稀疏版仅存已加入位置，平均维护 O(Q α(Q))，空间 O(Q)。

**边界与易错点**

同一位置重复增加不应改变答案。只合并已激活陆地，不能把默认父值当作已经存在的节点。


<a id="course-16"></a>

## 第 16 课：图结构与经典图算法

### 16.1 图的广度优先遍历

#### 题目

给定图中的起始节点，按广度优先或深度优先顺序访问所有从起点可达且尚未访问的节点。

**输入、输出与约束**

输入邻接表节点引用，可有环和重复可达路径；打印所有从起点可达节点，空起点不输出。

**函数签名（课程入口）**

```java
public static void bfs(Node start);
```

**示例**

```text
输入：边 A→B、A→C、B→D、C→D，从 A 开始
输出：A、B、C、D
```

解释：D 有两个来路，但只登记入队一次。

**出处与版本差异**

- [课程源码：class16/Code01_BFS.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Code01_BFS.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

BFS（Breadth-First Search，广度优先搜索）用队列维护已发现但尚未展开的节点。起点先登记 visited 再入队；每次出队访问，把尚未登记的邻居立即登记并加入队尾。

#### 代码答案

```java
package class16;

import java.util.HashSet;
import java.util.LinkedList;
import java.util.Queue;

public class Code01_BFS {

	// 从node出发，进行宽度优先遍历
	public static void bfs(Node start) {
		if (start == null) {
			return;
		}
		Queue<Node> queue = new LinkedList<>();
		HashSet<Node> set = new HashSet<>();
		queue.add(start);
		set.add(start);
		while (!queue.isEmpty()) {
			Node cur = queue.poll();
			System.out.println(cur.value);
			for (Node next : cur.nexts) {
				if (!set.contains(next)) {
					// 同一个邻居可能被多条边指向，登记后其他来路会跳过它。
					set.add(next);
					// 把新发现邻居放到队尾，待本层较早节点处理完后访问。
					queue.add(next);
				}
			}
		}
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：出队才登记会导致重复入队。这里只访问起点可达部分，不自动覆盖其他连通分量；邻居存储顺序影响同层输出顺序。

本条未附独立随机对数器。

#### 题解

**为什么正确**

队列使较早发现的节点先展开，因此无权图中按距离起点的边数逐层访问。节点在入队时便登记，同一节点即使由多个父节点到达也只入队一次。

**复杂度**

起点可达部分有 V 个点、E 条边时，时间 O(V+E)，空间 O(V)。

**边界与易错点**

出队才登记会导致重复入队。这里只访问起点可达部分，不自动覆盖其他连通分量；邻居存储顺序影响同层输出顺序。

### 16.2 图的深度优先遍历

#### 题目

给定图中的起始节点，按广度优先或深度优先顺序访问所有从起点可达且尚未访问的节点。

**输入、输出与约束**

输入邻接表起点；按课程栈模拟顺序打印可达节点，有环时使用 visited。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：边 A→B、A→C、B→D，从 A 开始，邻接按所列顺序
输出：A、B、D、C
```

解释：先深入 B 的子路径，再回到 A 处理 C。

**出处与版本差异**

- [课程源码：class16/Code02_DFS.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Code02_DFS.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

DFS（Depth-First Search，深度优先搜索）用栈保存回溯位置。弹出当前节点后，找到一个未访问邻居，就把当前节点重新压回，再压入该邻居并访问，随后停止本轮邻居扫描，以便优先深入。

#### 代码答案

```java
package class16;

import java.util.HashSet;
import java.util.Stack;

public class Code02_DFS {

	public static void dfs(Node node) {
		if (node == null) {
			return;
		}
		Stack<Node> stack = new Stack<>();
		HashSet<Node> set = new HashSet<>();
		stack.add(node);
		set.add(node);
		System.out.println(node.value);
		while (!stack.isEmpty()) {
			Node cur = stack.pop();
			for (Node next : cur.nexts) {
				if (!set.contains(next)) {
					// 保存父节点作为回溯点，深入后还需回来处理其他邻居。
					stack.push(cur);
					// 后压入邻居，下一轮优先展开这条更深路径。
					stack.push(next);
					set.add(next);
					System.out.println(next.value);
					// 一次只沿一条新边深入，剩余邻居留待回溯。
					break;
				}
			}
		}
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：深入一个邻居后应 break，否则变成另一种批量压栈顺序。回压父节点很重要，否则尚未探索的兄弟分支可能被遗漏。

本条未附独立随机对数器。

#### 题解

**为什么正确**

重新压入父节点保存了未来继续检查其他邻居的机会；邻居后压先出使搜索沿一条路径深入。visited 防止环和交叉边导致重复访问。

**复杂度**

标准保存邻接迭代位置的 DFS 为 O(V+E)。本课程栈版回到节点会从头重扫邻接表，时间可达 O(V+Σdeg(v)²)；空间 O(V)。

**边界与易错点**

深入一个邻居后应 break，否则变成另一种批量压栈顺序。回压父节点很重要，否则尚未探索的兄弟分支可能被遗漏。

### 16.3 拓扑排序：入度队列（对象版）

#### 题目

输入完整节点集合及有向边；返回或打印任一拓扑序，数组在线评测版以处理数量判断环。

**输入、输出与约束**

输入完整节点集合及有向边；返回或打印任一拓扑序，数组在线评测版以处理数量判断环。

**函数签名（课程入口）**

```java
public static ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph);
```

**示例**

```text
输入：边 1→2、1→3、2→4、3→4
输出：合法顺序 [1,2,3,4]，也可 [1,3,2,4]
```

解释：4 必须等两个前驱都出队后才能成为零入度。

**出处与版本差异**

- [课程源码：class16/Code03_TopologicalOrderBFS1.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Code03_TopologicalOrderBFS1.java)。
- [LintCode 在线评测](https://www.lintcode.com/problem/topological-sorting)。

#### 思路

统计每个节点入度，将所有零入度节点入队。出队节点可以放入结果，因为它所有前驱已被移除；随后把它的每条出边从图中概念性删除，令邻居入度减一，新变为零的节点入队。数组版使用固定容量数组保存队列和答案。

#### 代码答案

```java
package class16;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.LinkedList;
import java.util.Queue;

// OJ链接：https://www.lintcode.com/problem/topological-sorting
public class Code03_TopologicalOrderBFS1 {

	// 不要提交这个类
	public static class DirectedGraphNode {
		public int label;
		public ArrayList<DirectedGraphNode> neighbors;

		public DirectedGraphNode(int x) {
			label = x;
			neighbors = new ArrayList<DirectedGraphNode>();
		}
	}

	// 提交下面的
	public static ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph) {
		HashMap<DirectedGraphNode, Integer> indegreeMap = new HashMap<>();
		for (DirectedGraphNode cur : graph) {
			indegreeMap.put(cur, 0);
		}
		for (DirectedGraphNode cur : graph) {
			for (DirectedGraphNode next : cur.neighbors) {
				indegreeMap.put(next, indegreeMap.get(next) + 1);
			}
		}
		Queue<DirectedGraphNode> zeroQueue = new LinkedList<>();
		for (DirectedGraphNode cur : indegreeMap.keySet()) {
			if (indegreeMap.get(cur) == 0) {
				zeroQueue.add(cur);
			}
		}
		ArrayList<DirectedGraphNode> ans = new ArrayList<>();
		while (!zeroQueue.isEmpty()) {
			DirectedGraphNode cur = zeroQueue.poll();
			ans.add(cur);
			for (DirectedGraphNode next : cur.neighbors) {
				indegreeMap.put(next, indegreeMap.get(next) - 1);
				// 邻居所有前驱均已输出，可以进入候选队列。
				if (indegreeMap.get(next) == 0) {
					zeroQueue.offer(next);
				}
			}
		}
		return ans;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：结果数量不足 V 时表示有环。对象版本通常假设输入已是 DAG（Directed Acyclic Graph，有向无环图）；不要把部分结果误报完整拓扑序。副本入度避免破坏原图。

本条未附独立随机对数器。

#### 题解

**为什么正确**

任何未删除前驱都会贡献正入度，所以零入度节点不可能被尚未输出节点约束。依次选取它们保证每条边起点先于终点。若仍有节点但无零入度节点，剩余有向图存在环。

**复杂度**

时间 O(V+E)，辅助空间 O(V)，图存储另为 O(V+E)。

**边界与易错点**

结果数量不足 V 时表示有环。对象版本通常假设输入已是 DAG（Directed Acyclic Graph，有向无环图）；不要把部分结果误报完整拓扑序。副本入度避免破坏原图。

### 16.4 拓扑排序：入度队列（在线评测版）

#### 题目

输入完整节点集合及有向边；返回或打印任一拓扑序，数组在线评测版以处理数量判断环。

**输入、输出与约束**

输入完整节点集合及有向边；返回或打印任一拓扑序，数组在线评测版以处理数量判断环。

**函数签名（课程入口）**

```java
public static boolean topoSort(ArrayList<ArrayList<Integer>> graph);
```

**示例**

```text
输入：边 1→2、1→3、2→4、3→4
输出：合法顺序 [1,2,3,4]，也可 [1,3,2,4]
```

解释：4 必须等两个前驱都出队后才能成为零入度。

**出处与版本差异**

- [课程源码：class16/Code03_TopologicalOrderBFS2.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Code03_TopologicalOrderBFS2.java)。
- [牛客网在线评测](https://www.nowcoder.com/questionTerminal/88f7e156ca7d43a1a535f619cd3f495c)。

#### 思路

统计每个节点入度，将所有零入度节点入队。出队节点可以放入结果，因为它所有前驱已被移除；随后把它的每条出边从图中概念性删除，令邻居入度减一，新变为零的节点入队。数组版使用固定容量数组保存队列和答案。

#### 代码答案

```java
package class16;

// 课上没讲这个实现
// 因为是一样的，都是根据入度来求拓扑排序，只不过是牛客网的测试数据
// 测试链接 : https://www.nowcoder.com/questionTerminal/88f7e156ca7d43a1a535f619cd3f495c
// 请同学们务必参考如下代码中关于输入、输出的处理
// 这是输入输出处理效率很高的写法
// 提交以下所有代码，把主类名改成Main，可以直接通过
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.io.StreamTokenizer;
import java.util.ArrayList;
import java.util.Arrays;

public class Code03_TopologicalOrderBFS2 {

	public static int MAXN = 200001;

	public static int[] queue = new int[MAXN];

	public static int[] indegree = new int[MAXN];

	public static int[] ans = new int[MAXN];

	public static int n, m, from, to;

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StreamTokenizer in = new StreamTokenizer(br);
		PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
		while (in.nextToken() != StreamTokenizer.TT_EOF) {
			n = (int) in.nval;
			in.nextToken();
			m = (int) in.nval;
			ArrayList<ArrayList<Integer>> graph = new ArrayList<>();
			for (int i = 0; i <= n; i++) {
				graph.add(new ArrayList<>());
			}
			for (int i = 0; i < m; i++) {
				in.nextToken();
				from = (int) in.nval;
				in.nextToken();
				to = (int) in.nval;
				graph.get(from).add(to);
			}
			if (!topoSort(graph)) {
				out.println(-1);
			} else {
				for (int i = 0; i < n - 1; i++) {
					out.print(ans[i] + " ");
				}
				out.println(ans[n - 1]);
			}
			out.flush();
		}
	}

	// 有拓扑排序返回true
	// 没有拓扑排序返回false
	public static boolean topoSort(ArrayList<ArrayList<Integer>> graph) {
		Arrays.fill(indegree, 1, n + 1, 0);
		for (ArrayList<Integer> nexts : graph) {
			for (int next : nexts) {
				indegree[next]++;
			}
		}
		int l = 0;
		int r = 0;
		for (int i = 1; i <= n; i++) {
			if (indegree[i] == 0) {
				queue[r++] = i;
			}
		}
		int cnt = 0;
		while (l < r) {
			int cur = queue[l++];
			ans[cnt++] = cur;
			for (int next : graph.get(cur)) {
				// 先移除当前出边，再检查邻居是否刚刚解除全部前驱约束。
				if (--indegree[next] == 0) {
					queue[r++] = next;
				}
			}
		}
		// 输出数量等于节点总数才是完整拓扑序；不足时剩余部分有环。
		return cnt == n;
	}

}
```

---

##### 输入输出核对

先以本题示例核对结果，再重点覆盖：结果数量不足 V 时表示有环。对象版本通常假设输入已是 DAG（Directed Acyclic Graph，有向无环图）；不要把部分结果误报完整拓扑序。副本入度避免破坏原图。

该版本保留在线评测入口；未附独立随机对数器，不把编译通过当作正确性证明。

#### 题解

**为什么正确**

任何未删除前驱都会贡献正入度，所以零入度节点不可能被尚未输出节点约束。依次选取它们保证每条边起点先于终点。若仍有节点但无零入度节点，剩余有向图存在环。

**复杂度**

时间 O(V+E)，辅助空间 O(V)，图存储另为 O(V+E)。

**边界与易错点**

结果数量不足 V 时表示有环。对象版本通常假设输入已是 DAG（Directed Acyclic Graph，有向无环图）；不要把部分结果误报完整拓扑序。副本入度避免破坏原图。

### 16.5 拓扑排序：后续最长路径深度排序

#### 题目

输入完整 DAG 节点列表；返回按后续最长路径深度降序得到的拓扑序。

**输入、输出与约束**

输入完整 DAG 节点列表；返回按后续最长路径深度降序得到的拓扑序。

**函数签名（课程入口）**

```java
public int compare(Record o1, Record o2);
public static ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph);
```

**示例**

```text
输入：边 1→2、2→3、1→3
输出：深度分别 3、2、1；顺序 [1,2,3]
```

解释：每条边都从较大深度指向较小深度。

**出处与版本差异**

- [课程源码：class16/Code03_TopologicalOrderDFS1.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Code03_TopologicalOrderDFS1.java)。
- [LintCode 在线评测](https://www.lintcode.com/problem/topological-sorting)。

#### 思路

为每个节点计算从它出发的最长后续路径深度：叶子深度 1，其余为后继最大深度加一。用记忆化递归避免共享后继被重复计算，再按深度从大到小输出节点。

#### 代码答案

```java
package class16;

import java.util.ArrayList;
import java.util.Comparator;
import java.util.HashMap;

// OJ链接：https://www.lintcode.com/problem/topological-sorting
public class Code03_TopologicalOrderDFS1 {

	// 不要提交这个类
	public static class DirectedGraphNode {
		public int label;
		public ArrayList<DirectedGraphNode> neighbors;

		public DirectedGraphNode(int x) {
			label = x;
			neighbors = new ArrayList<DirectedGraphNode>();
		}
	}

	// 提交下面的
	public static class Record {
		public DirectedGraphNode node;
		public int deep;

		public Record(DirectedGraphNode n, int o) {
			node = n;
			deep = o;
		}
	}

	public static class MyComparator implements Comparator<Record> {

		@Override
		public int compare(Record o1, Record o2) {
			return o2.deep - o1.deep;
		}
	}

	public static ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph) {
		HashMap<DirectedGraphNode, Record> order = new HashMap<>();
		for (DirectedGraphNode cur : graph) {
			f(cur, order);
		}
		ArrayList<Record> recordArr = new ArrayList<>();
		for (Record r : order.values()) {
			recordArr.add(r);
		}
		recordArr.sort(new MyComparator());
		ArrayList<DirectedGraphNode> ans = new ArrayList<DirectedGraphNode>();
		for (Record r : recordArr) {
			ans.add(r.node);
		}
		return ans;
	}

	public static Record f(DirectedGraphNode cur, HashMap<DirectedGraphNode, Record> order) {
		if (order.containsKey(cur)) {
			return order.get(cur);
		}
		int follow = 0;
		for (DirectedGraphNode next : cur.neighbors) {
			// 当前深度取所有后继深度的最大值，不是求和。
			follow = Math.max(follow, f(next, order).deep);
		}
		// 加一计入当前节点，使每个前驱的深度严格大于其后继。
		Record ans = new Record(cur, follow + 1);
		order.put(cur, ans);
		return ans;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：必须输入无环图；当前记忆化只缓存已完成节点，不是环检测。这里是最长路径深度，不是可达节点总数。

本条未附独立随机对数器。

#### 题解

**为什么正确**

对任意边 u→v，都有 depth(u)≥depth(v)+1，因此 u 的深度严格更大。降序排列便保证每条边的起点先出现；同深度节点之间不可能有边，可以任意排列。

**复杂度**

深度计算 O(V+E)，排序 O(V log V)，总时间 O(V+E+V log V)；额外空间 O(V)。

**边界与易错点**

必须输入无环图；当前记忆化只缓存已完成节点，不是环检测。这里是最长路径深度，不是可达节点总数。

### 16.6 拓扑排序：后续路径点次排序

#### 题目

输入 DAG，点次总数必须在 long 范围内；返回合法拓扑序。

**输入、输出与约束**

输入 DAG，点次总数必须在 long 范围内；返回合法拓扑序。

**函数签名（课程入口）**

```java
public int compare(Record o1, Record o2);
public static ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph);
```

**示例**

```text
输入：边 A→B、A→C、B→D、C→D
输出：点次 D=1，B=C=2，A=5
```

解释：实际仅四个节点，D 通过两条路径在 A 的点次中计入两次。

**出处与版本差异**

- [课程源码：class16/Code03_TopologicalOrderDFS2.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Code03_TopologicalOrderDFS2.java)。
- [LintCode 在线评测](https://www.lintcode.com/problem/topological-sorting)。

#### 思路

定义 nodes(u)=1+Σnodes(v)，对 u 的每个后继 v 求和。这里同一个节点经不同路径可被多次计入，所以是点次而非去重可达节点数。记忆化算出每节点权重，再按权重降序排列。

#### 代码答案

```java
package class16;

import java.util.ArrayList;
import java.util.Comparator;
import java.util.HashMap;

// OJ链接：https://www.lintcode.com/problem/topological-sorting
public class Code03_TopologicalOrderDFS2 {

	// 不要提交这个类
	public static class DirectedGraphNode {
		public int label;
		public ArrayList<DirectedGraphNode> neighbors;

		public DirectedGraphNode(int x) {
			label = x;
			neighbors = new ArrayList<DirectedGraphNode>();
		}
	}

	// 提交下面的
	public static class Record {
		public DirectedGraphNode node;
		public long nodes;

		public Record(DirectedGraphNode n, long o) {
			node = n;
			nodes = o;
		}
	}

	public static class MyComparator implements Comparator<Record> {

		@Override
		public int compare(Record o1, Record o2) {
			return o1.nodes == o2.nodes ? 0 : (o1.nodes > o2.nodes ? -1 : 1);
		}
	}

	public static ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph) {
		HashMap<DirectedGraphNode, Record> order = new HashMap<>();
		for (DirectedGraphNode cur : graph) {
			f(cur, order);
		}
		ArrayList<Record> recordArr = new ArrayList<>();
		for (Record r : order.values()) {
			recordArr.add(r);
		}
		recordArr.sort(new MyComparator());
		ArrayList<DirectedGraphNode> ans = new ArrayList<DirectedGraphNode>();
		for (Record r : recordArr) {
			ans.add(r.node);
		}
		return ans;
	}

	// 当前来到cur点，请返回cur点所到之处，所有的点次！
	// 返回（cur，点次）
	// 缓存！！！！！order
	//  key : 某一个点的点次，之前算过了！
	//  value : 点次是多少
	public static Record f(DirectedGraphNode cur, HashMap<DirectedGraphNode, Record> order) {
		if (order.containsKey(cur)) {
			return order.get(cur);
		}
		// cur的点次之前没算过！
		long nodes = 0;
		for (DirectedGraphNode next : cur.neighbors) {
			// 每条后继路径贡献其点次，共享节点可通过不同路径重复贡献。
			nodes += f(next, order).nodes;
		}
		// 加上当前节点一次，确保当前权重大于任意单个后继。
		Record ans = new Record(cur, nodes + 1);
		order.put(cur, ans);
		return ans;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：共享后继允许在求和中重复贡献。long 溢出会破坏排序关系；本实现要求点次可表示，深度排序版没有这种指数计数风险。

本条未附独立随机对数器。

#### 题解

**为什么正确**

所有点次为正，对任何边 u→v，nodes(u) 至少为 1+nodes(v)，故严格更大。只需这一严格不等关系就能保证拓扑顺序，无需把点次解释成集合大小。

**复杂度**

图遍历 O(V+E)，排序 O(V log V)，额外空间 O(V)；点次可能指数增长，固定 long 运算仅在不溢出时成立。

**边界与易错点**

共享后继允许在求和中重复贡献。long 溢出会破坏排序关系；本实现要求点次可表示，深度排序版没有这种指数计数风险。

### 16.7 有向无环图的拓扑排序

#### 题目

输入完整节点集合及有向边；返回或打印任一拓扑序，数组在线评测版以处理数量判断环。

**输入、输出与约束**

输入完整节点集合及有向边；返回或打印任一拓扑序，数组在线评测版以处理数量判断环。

**函数签名（课程入口）**

```java
public static List<Node> sortedTopology(Graph graph);
```

**示例**

```text
输入：边 1→2、1→3、2→4、3→4
输出：合法顺序 [1,2,3,4]，也可 [1,3,2,4]
```

解释：4 必须等两个前驱都出队后才能成为零入度。

**出处与版本差异**

- [课程源码：class16/Code03_TopologySort.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Code03_TopologySort.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

统计每个节点入度，将所有零入度节点入队。出队节点可以放入结果，因为它所有前驱已被移除；随后把它的每条出边从图中概念性删除，令邻居入度减一，新变为零的节点入队。数组版使用固定容量数组保存队列和答案。

#### 代码答案

```java
package class16;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

public class Code03_TopologySort {

	// directed graph and no loop
	public static List<Node> sortedTopology(Graph graph) {
		// key 某个节点   value 剩余的入度
		HashMap<Node, Integer> inMap = new HashMap<>();
		// 只有剩余入度为0的点，才进入这个队列
		Queue<Node> zeroInQueue = new LinkedList<>();
		for (Node node : graph.nodes.values()) {
			inMap.put(node, node.in);
			if (node.in == 0) {
				zeroInQueue.add(node);
			}
		}
		List<Node> result = new ArrayList<>();
		while (!zeroInQueue.isEmpty()) {
			Node cur = zeroInQueue.poll();
			result.add(cur);
			for (Node next : cur.nexts) {
				// 移除当前节点的这条出边，邻居剩余前驱数减少一。
				inMap.put(next, inMap.get(next) - 1);
				// 所有前驱均已安排，邻居现在可以进入拓扑序。
				if (inMap.get(next) == 0) {
					zeroInQueue.add(next);
				}
			}
		}
		return result;
	}
}
```

---

##### 边界核对

用题面示例核对接口，再检查：结果数量不足 V 时表示有环。对象版本通常假设输入已是 DAG（Directed Acyclic Graph，有向无环图）；不要把部分结果误报完整拓扑序。副本入度避免破坏原图。

本条未附独立随机对数器。

#### 题解

**为什么正确**

任何未删除前驱都会贡献正入度，所以零入度节点不可能被尚未输出节点约束。依次选取它们保证每条边起点先于终点。若仍有节点但无零入度节点，剩余有向图存在环。

**复杂度**

时间 O(V+E)，辅助空间 O(V)，图存储另为 O(V+E)。

**边界与易错点**

结果数量不足 V 时表示有环。对象版本通常假设输入已是 DAG（Directed Acyclic Graph，有向无环图）；不要把部分结果误报完整拓扑序。副本入度避免破坏原图。

### 16.8 Kruskal 最小生成树

#### 题目

给定带权无向图，返回一组连接所有可达节点且总权重最小的边。

**输入、输出与约束**

输入带权无向图；对象版返回最小生成森林边集合，评测版按连通性输出总权或无解标记。

**函数签名（课程入口）**

```java
public void makeSets(Collection<Node> nodes);
public boolean isSameSet(Node a, Node b);
public void union(Node a, Node b);
public int compare(Edge o1, Edge o2);
public static Set<Edge> kruskalMST(Graph graph);
```

**示例**

```text
输入：无向边 (1,2,1)、(2,3,2)、(1,3,5)
输出：最小总权重 3
```

解释：先选权重 1、2，权重 5 的边会成环。

**出处与版本差异**

- [课程源码：class16/Code04_Kruskal.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Code04_Kruskal.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

按边权从小到大处理无向边，用并查集维护已选边形成的连通分量。若一条边两端属于不同集合，就选它并合并；若已同集合，加入只会形成环，应跳过。

#### 代码答案

```java
package class16;

import java.util.Collection;
import java.util.Comparator;
import java.util.HashMap;
import java.util.HashSet;
import java.util.PriorityQueue;
import java.util.Set;
import java.util.Stack;

//undirected graph only
public class Code04_Kruskal {

	// Union-Find Set
	public static class UnionFind {
		// key 某一个节点， value key节点往上的节点
		private HashMap<Node, Node> fatherMap;
		// key 某一个集合的代表节点, value key所在集合的节点个数
		private HashMap<Node, Integer> sizeMap;

		public UnionFind() {
			fatherMap = new HashMap<Node, Node>();
			sizeMap = new HashMap<Node, Integer>();
		}

		public void makeSets(Collection<Node> nodes) {
			fatherMap.clear();
			sizeMap.clear();
			for (Node node : nodes) {
				fatherMap.put(node, node);
				sizeMap.put(node, 1);
			}
		}

		private Node findFather(Node n) {
			Stack<Node> path = new Stack<>();
			while(n != fatherMap.get(n)) {
				path.add(n);
				n = fatherMap.get(n);
			}
			while(!path.isEmpty()) {
				fatherMap.put(path.pop(), n);
			}
			return n;
		}

		public boolean isSameSet(Node a, Node b) {
			return findFather(a) == findFather(b);
		}

		public void union(Node a, Node b) {
			if (a == null || b == null) {
				return;
			}
			Node aDai = findFather(a);
			Node bDai = findFather(b);
			if (aDai != bDai) {
				int aSetSize = sizeMap.get(aDai);
				int bSetSize = sizeMap.get(bDai);
				if (aSetSize <= bSetSize) {
					fatherMap.put(aDai, bDai);
					sizeMap.put(bDai, aSetSize + bSetSize);
					sizeMap.remove(aDai);
				} else {
					fatherMap.put(bDai, aDai);
					sizeMap.put(aDai, aSetSize + bSetSize);
					sizeMap.remove(bDai);
				}
			}
		}
	}

	public static class EdgeComparator implements Comparator<Edge> {

		@Override
		public int compare(Edge o1, Edge o2) {
			return o1.weight - o2.weight;
		}

	}

	public static Set<Edge> kruskalMST(Graph graph) {
		UnionFind unionFind = new UnionFind();
		unionFind.makeSets(graph.nodes.values());
		// 从小的边到大的边，依次弹出，小根堆！
		PriorityQueue<Edge> priorityQueue = new PriorityQueue<>(new EdgeComparator());
		for (Edge edge : graph.edges) { // M 条边
			priorityQueue.add(edge);  // O(logM)
		}
		Set<Edge> result = new HashSet<>();
		while (!priorityQueue.isEmpty()) { // M 条边
			Edge edge = priorityQueue.poll(); // O(logM)
			// 两端不同分量才可连边，同分量边会形成环。
			if (!unionFind.isSameSet(edge.from, edge.to)) { // O(1)
				// 当前最轻安全边加入答案，同时必须合并其两端分量。
				result.add(edge);
				unionFind.union(edge.from, edge.to);
			}
		}
		return result;
	}
}
```

---

##### 边界核对

用题面示例核对接口，再检查：无向边的表示必须一致，重边可保留。非连通图只能得到森林；在线评测版要求生成树时应检查选边数是否 V-1。负边权不妨碍最小生成树。

本条未附独立随机对数器。

#### 题解

**为什么正确**

当前连接两个分量的最轻候选边可按割性质加入某棵最小生成树；若其他最优树不用它，沿加入后形成的环删去一条不更轻的跨割边，总代价不增。反复选边得到最小生成森林。

**复杂度**

时间 O(E log E+E α(V))，空间 O(V+E) 包含排序或堆中边。

**边界与易错点**

无向边的表示必须一致，重边可保留。非连通图只能得到森林；在线评测版要求生成树时应检查选边数是否 V-1。负边权不妨碍最小生成树。

### 16.9 Kruskal 最小生成树（在线评测版）

#### 题目

给定连通的带权无向图，输出最小生成树总权重。支持多组数据，每组只处理本组输入的m条边。

**输入、输出与约束**

每组先读n、m，再读m行起点、终点、权重；1≤n≤10000，m≤100000，保证连通且int累计权重不溢出。

**函数签名（课程入口）**

```java
public static void build(int n);
public static boolean union(int i, int j);
```

**示例**

```text
输入：无向边 (1,2,1)、(2,3,2)、(1,3,5)
输出：最小总权重 3
```

解释：先选权重 1、2，权重 5 的边会成环。

**出处与版本差异**

- [课程源码：class16/Code04_KruskalNowCoder.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Code04_KruskalNowCoder.java)。
- [牛客网在线评测](https://www.nowcoder.com/questionTerminal/c23eab7bb39748b6b224a8a3afbe396b)。

#### 思路

按边权从小到大处理无向边，用并查集维护已选边形成的连通分量。若一条边两端属于不同集合，就选它并合并；若已同集合，加入只会形成环，应跳过。

#### 代码答案

```java
package class16;

// 课上没讲这个实现
// 因为是一样的，都是用Kruskal算法实现最小生成树，只不过是牛客网的测试数据
// 测试链接 : https://www.nowcoder.com/questionTerminal/c23eab7bb39748b6b224a8a3afbe396b
// 请同学们务必参考如下代码中关于输入、输出的处理
// 这是输入输出处理效率很高的写法
// 提交以下所有代码，把主类名改成Main，可以直接通过
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.io.StreamTokenizer;
import java.util.Arrays;

public class Code04_KruskalNowCoder {

	public static int MAXM = 100001;

	public static int[][] edges = new int[MAXM][3];

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StreamTokenizer in = new StreamTokenizer(br);
		PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
		while (in.nextToken() != StreamTokenizer.TT_EOF) {
			int n = (int) in.nval;
			in.nextToken();
			int m = (int) in.nval;
			for (int i = 0; i < m; i++) {
				in.nextToken();
				edges[i][0] = (int) in.nval;
				in.nextToken();
				edges[i][1] = (int) in.nval;
				in.nextToken();
				edges[i][2] = (int) in.nval;
			}
			// 仅把本组m条边按权重升序排列。
			Arrays.sort(edges, 0, m, (a, b) -> a[2] - b[2]);
			build(n);
			int ans = 0;
			// 只处理当前输入的有效边，不能遍历上组残留或未使用数组槽。
			for (int edgeIndex = 0; edgeIndex < m; edgeIndex++) {
				int[] edge = edges[edgeIndex];
				// 合并成功说明两端原属不同分量，这条边不会成环。
				if (union(edge[0], edge[1])) {
					// 只累加本轮实际被选入生成森林的边权。
					ans += edge[2];
				}
			}
			out.println(ans);
			out.flush();
		}
	}

	// 下面是并查集结构
	public static int MAXN = 10001;

	public static int[] father = new int[MAXN];

	public static int[] size = new int[MAXN];

	public static int[] help = new int[MAXN];

	public static void build(int n) {
		for (int i = 1; i <= n; i++) {
			father[i] = i;
			size[i] = 1;
		}
	}

	private static int find(int i) {
		int size = 0;
		while (i != father[i]) {
			help[size++] = i;
			i = father[i];
		}
		while (size > 0) {
			father[help[--size]] = i;
		}
		return i;
	}

	// 如果i和j，原本是一个集合，返回false
	// 如果i和j，不是一个集合，合并，然后返回true
	public static boolean union(int i, int j) {
		int fi = find(i);
		int fj = find(j);
		if (fi != fj) {
			if (size[fi] >= size[fj]) {
				father[fj] = fi;
				size[fi] += size[fj];
			} else {
				father[fi] = fj;
				size[fj] += size[fi];
			}
			return true;
		} else {
			return false;
		}
	}

}
```

---

##### 输入输出核对

先以本题示例核对结果，再重点覆盖：源码没有连通性判定，非连通图仅得森林权重。原文件遍历整个固定容量edges，跨组残留边可能污染结果；本笔记将遍历范围修正为当前m条，参考快照保持不动。

该版本保留在线评测入口；未附独立随机对数器，不把编译通过当作正确性证明。

多组输入回归用例（整体作为标准输入）：

```text
4 3
3 4 1
2 3 2
1 2 3
2 1
1 2 10
```

期望输出：

```text
6
10
```

原版第二组会误加上组残留边而输出12；修订版仅处理当前m条边，输出10。此用例已实际执行验证。

#### 题解

**为什么正确**

当前连接两个分量的最轻候选边可按割性质加入某棵最小生成树；若其他最优树不用它，沿加入后形成的环删去一条不更轻的跨割边，总代价不增。反复选边得到最小生成森林。

**复杂度**

时间 O(E log E+E α(V))，空间 O(V+E) 包含排序或堆中边。

**边界与易错点**

源码没有连通性判定，非连通图仅得森林权重。原文件遍历整个固定容量edges，跨组残留边可能污染结果；本笔记将遍历范围修正为当前m条，参考快照保持不动。

### 16.10 Prim 最小生成树

#### 题目

给定带权无向图，返回一组连接所有可达节点且总权重最小的边。

**输入、输出与约束**

输入带权无向图；对象版可返回森林。矩阵版和 PrimNowCoder 按连通图使用；后者只从1号点扩展，不检查全图连通性，非连通输入只得到1号点所在分量的权重。

**函数签名（课程入口）**

```java
public int compare(Edge o1, Edge o2);
public static Set<Edge> primMST(Graph graph);
public static int prim(int[][] graph);
```

**示例**

```text
输入：无向边 (1,2,1)、(1,3,4)、(2,3,2)，从1开始
输出：依次选 1—2、2—3，总权重3
```

解释：节点2加入后，新边比原先的1—3更便宜。

**出处与版本差异**

- [课程源码：class16/Code05_Prim.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Code05_Prim.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

从一个节点开始建立已访问集合，候选堆保存从已访问区域发出的边。弹出最轻边，若另一端未访问就接受并解锁它的出边，否则丢弃。邻接矩阵版每轮线性找最便宜的新节点，再更新剩余点的接入成本。

#### 代码答案

```java
package class16;

import java.util.Comparator;
import java.util.HashSet;
import java.util.PriorityQueue;
import java.util.Set;

// undirected graph only
public class Code05_Prim {

	public static class EdgeComparator implements Comparator<Edge> {

		@Override
		public int compare(Edge o1, Edge o2) {
			return o1.weight - o2.weight;
		}

	}

	public static Set<Edge> primMST(Graph graph) {
		// 解锁的边进入小根堆
		PriorityQueue<Edge> priorityQueue = new PriorityQueue<>(new EdgeComparator());

		// 哪些点被解锁出来了
		HashSet<Node> nodeSet = new HashSet<>();

		Set<Edge> result = new HashSet<>(); // 依次挑选的的边在result里

		for (Node node : graph.nodes.values()) { // 随便挑了一个点
			// node 是开始点
			if (!nodeSet.contains(node)) {
				nodeSet.add(node);
				for (Edge edge : node.edges) { // 由一个点，解锁所有相连的边
					priorityQueue.add(edge);
				}
				while (!priorityQueue.isEmpty()) {
					Edge edge = priorityQueue.poll(); // 弹出解锁的边中，最小的边
					Node toNode = edge.to; // 可能的一个新的点
					// 只有跨到未访问节点的边才有效，内部边出堆后直接丢弃。
					if (!nodeSet.contains(toNode)) { // 不含有的时候，就是新的点
						nodeSet.add(toNode);
						result.add(edge);
						for (Edge nextEdge : toNode.edges) {
							priorityQueue.add(nextEdge);
						}
					}
				}
			}
			// break;
		}
		return result;
	}

	// 请保证graph是连通图
	// graph[i][j]表示点i到点j的距离，如果是系统最大值代表无路
	// 返回值是最小连通图的路径之和
	public static int prim(int[][] graph) {
		int size = graph.length;
		int[] distances = new int[size];
		boolean[] visit = new boolean[size];
		visit[0] = true;
		for (int i = 0; i < size; i++) {
			distances[i] = graph[0][i];
		}
		int sum = 0;
		for (int i = 1; i < size; i++) {
			int minPath = Integer.MAX_VALUE;
			int minIndex = -1;
			for (int j = 0; j < size; j++) {
				if (!visit[j] && distances[j] < minPath) {
					minPath = distances[j];
					minIndex = j;
				}
			}
			if (minIndex == -1) {
				return sum;
			}
			visit[minIndex] = true;
			sum += minPath;
			for (int j = 0; j < size; j++) {
				if (!visit[j] && distances[j] > graph[minIndex][j]) {
					distances[j] = graph[minIndex][j];
				}
			}
		}
		return sum;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		System.out.println("hello world!");
	}
```

#### 题解

**为什么正确**

候选中的最轻跨割边连接已选集合与未选部分，依据割性质可安全加入最小生成树。接入新节点后重新维护边界，直到覆盖连通分量。

**复杂度**

边堆版 O(E log E)，矩阵版 O(V²)；空间分别与邻接表 O(V+E) 和矩阵 O(V²) 同阶。

**边界与易错点**

堆里允许出现已经失效的边，出堆时要检查终点。对象版外层遍历可处理森林，矩阵/评测版的连通约定需单独遵守。

### 16.11 Prim 最小生成树（在线评测版）

#### 题目

给定连通的带权无向图，节点编号为1..n，返回其最小生成树的总权重。本版本从1号节点扩展，调用方应保证全图连通。

**输入、输出与约束**

输入带权无向图；对象版可返回森林。矩阵版和 PrimNowCoder 按连通图使用；后者只从1号点扩展，不检查全图连通性，非连通输入只得到1号点所在分量的权重。

本题通过标准输入读取数据，`main` 是正式解答入口；输入布局见代码中的读取顺序。

**示例**

```text
输入：无向边 (1,2,1)、(1,3,4)、(2,3,2)，从1开始
输出：依次选 1—2、2—3，总权重3
```

解释：节点2加入后，新边比原先的1—3更便宜。

**出处与版本差异**

- [课程源码：class16/Code05_PrimNowCoder.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Code05_PrimNowCoder.java)。
- [牛客网在线评测](https://www.nowcoder.com/questionTerminal/c23eab7bb39748b6b224a8a3afbe396b)。

#### 思路

从一个节点开始建立已访问集合，候选堆保存从已访问区域发出的边。弹出最轻边，若另一端未访问就接受并解锁它的出边，否则丢弃。邻接矩阵版每轮线性找最便宜的新节点，再更新剩余点的接入成本。

#### 代码答案

```java
package class16;

// 课上没讲这个实现
// 因为是一样的，都是用Prim算法实现最小生成树，只不过是牛客网的测试数据
// 测试链接 : https://www.nowcoder.com/questionTerminal/c23eab7bb39748b6b224a8a3afbe396b
// 请同学们务必参考如下代码中关于输入、输出的处理
// 这是输入输出处理效率很高的写法
// 提交以下所有代码，把主类名改成Main，可以直接通过
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.io.StreamTokenizer;
import java.util.ArrayList;
import java.util.PriorityQueue;

public class Code05_PrimNowCoder {

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StreamTokenizer in = new StreamTokenizer(br);
		PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
		while (in.nextToken() != StreamTokenizer.TT_EOF) {
			ArrayList<ArrayList<int[]>> graph = new ArrayList<>();
			int n = (int) in.nval;
			for (int i = 0; i <= n; i++) {
				graph.add(new ArrayList<>());
			}
			in.nextToken();
			int m = (int) in.nval;
			for (int i = 0; i < m; i++) {
				in.nextToken();
				int A = (int) in.nval;
				in.nextToken();
				int B = (int) in.nval;
				in.nextToken();
				int cost = (int) in.nval;
				graph.get(A).add(new int[] { B, cost });
				// 无向边必须同时记录反向邻接，才能从任一端扩展。
				graph.get(B).add(new int[] { A, cost });
			}
			PriorityQueue<int[]> heap = new PriorityQueue<>((a, b) -> a[1] - b[1]);
			boolean[] visited = new boolean[n + 1];
			for (int[] edge : graph.get(1)) {
				heap.add(edge);
			}
			// 从1号点开始扩展，本版本要求输入图连通。
			visited[1] = true;
			int ans = 0;
			while (!heap.isEmpty()) {
				int[] edge = heap.poll();
				int next = edge[0];
				int cost = edge[1];
				// 堆里可能有已失效的内部边，只有接入新节点时才接受。
				if (!visited[next]) {
					visited[next] = true;
					// 把连接新节点的最小候选边计入生成树。
					ans += cost;
					for (int[] e : graph.get(next)) {
						heap.add(e);
					}
				}
			}
			out.println(ans);
			out.flush();
		}
	}

}
```

---

##### 输入输出核对

先以本题示例核对结果，再重点覆盖：堆里允许出现已经失效的边，出堆时要检查终点。对象版外层遍历可处理森林，矩阵/评测版的连通约定需单独遵守。

该版本保留在线评测入口；未附独立随机对数器，不把编译通过当作正确性证明。

#### 题解

**为什么正确**

候选中的最轻跨割边连接已选集合与未选部分，依据割性质可安全加入最小生成树。接入新节点后重新维护边界，直到覆盖连通分量。

**复杂度**

边堆版 O(E log E)，矩阵版 O(V²)；空间分别与邻接表 O(V+E) 和矩阵 O(V²) 同阶。

**边界与易错点**

堆里允许出现已经失效的边，出堆时要检查终点。对象版外层遍历可处理森林，矩阵/评测版的连通约定需单独遵守。

### 16.12 Dijkstra 单源最短路径

#### 题目

给定非负权图和源点，计算源点到其他节点的最短距离；网络延迟题返回信号到达全部节点的最短时间。

**输入、输出与约束**

输入非负权有向邻接图和源节点；返回所有可达节点的最短距离映射。

**函数签名（课程入口）**

```java
public static HashMap<Node, Integer> dijkstra1(Node from);
public static Node getMinDistanceAndUnselectedNode(HashMap<Node, Integer> distanceMap, HashSet<Node> touchedNodes);
public boolean isEmpty();
public void addOrUpdateOrIgnore(Node node, int distance);
public NodeRecord pop();
public static HashMap<Node, Integer> dijkstra2(Node head, int size);
```

**示例**

```text
输入：边 A→B 权2、A→C 权5、B→C 权1，从A出发
输出：A=0，B=2，C=3
```

解释：经 B 可将 C 的估计由 5 改进为 3。

**出处与版本差异**

- [课程源码：class16/Code06_Dijkstra.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Code06_Dijkstra.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

维护源点到各节点当前最短估计。反复选尚未确定且距离最小者，确定其距离，再用“当前距离+出边权重”松弛邻居。版本一线性扫描距离表选点；版本二用支持降键的堆与反向索引加速。

#### 代码答案

```java
package class16;

import java.util.HashMap;
import java.util.HashSet;
import java.util.Map.Entry;

// no negative weight
public class Code06_Dijkstra {

	public static HashMap<Node, Integer> dijkstra1(Node from) {
		HashMap<Node, Integer> distanceMap = new HashMap<>();
		// 源到自身距离为零，作为所有松弛的起点。
		distanceMap.put(from, 0);
		// 打过对号的点
		HashSet<Node> selectedNodes = new HashSet<>();
		Node minNode = getMinDistanceAndUnselectedNode(distanceMap, selectedNodes);
		while (minNode != null) {
			//  原始点  ->  minNode(跳转点)   最小距离distance
			int distance = distanceMap.get(minNode);
			for (Edge edge : minNode.edges) {
				Node toNode = edge.to;
				if (!distanceMap.containsKey(toNode)) {
					// 经过当前已确定节点，再走这条边，得到邻居的一条可行路径长度。
					distanceMap.put(toNode, distance + edge.weight);
				} else { // toNode
					// 经过当前已确定节点，再走这条边，得到邻居的一条可行路径长度。
					distanceMap.put(edge.to, Math.min(distanceMap.get(toNode), distance + edge.weight));
				}
			}
			// 非负权保证最小未确定估计已经最优，此后无需再修改。
			selectedNodes.add(minNode);
			minNode = getMinDistanceAndUnselectedNode(distanceMap, selectedNodes);
		}
		return distanceMap;
	}

	public static Node getMinDistanceAndUnselectedNode(HashMap<Node, Integer> distanceMap, HashSet<Node> touchedNodes) {
		Node minNode = null;
		int minDistance = Integer.MAX_VALUE;
		for (Entry<Node, Integer> entry : distanceMap.entrySet()) {
			Node node = entry.getKey();
			int distance = entry.getValue();
			if (!touchedNodes.contains(node) && distance < minDistance) {
				minNode = node;
				minDistance = distance;
			}
		}
		return minNode;
	}

	public static class NodeRecord {
		public Node node;
		public int distance;

		public NodeRecord(Node node, int distance) {
			this.node = node;
			this.distance = distance;
		}
	}

	public static class NodeHeap {
		private Node[] nodes; // 实际的堆结构
		// key 某一个node， value 上面堆中的位置
		private HashMap<Node, Integer> heapIndexMap;
		// key 某一个节点， value 从源节点出发到该节点的目前最小距离
		private HashMap<Node, Integer> distanceMap;
		private int size; // 堆上有多少个点

		public NodeHeap(int size) {
			nodes = new Node[size];
			heapIndexMap = new HashMap<>();
			distanceMap = new HashMap<>();
			size = 0;
		}

		public boolean isEmpty() {
			return size == 0;
		}

		// 有一个点叫node，现在发现了一个从源节点出发到达node的距离为distance
		// 判断要不要更新，如果需要的话，就更新
		public void addOrUpdateOrIgnore(Node node, int distance) {
			if (inHeap(node)) {
				distanceMap.put(node, Math.min(distanceMap.get(node), distance));
				insertHeapify(heapIndexMap.get(node));
			}
			if (!isEntered(node)) {
				nodes[size] = node;
				heapIndexMap.put(node, size);
				distanceMap.put(node, distance);
				insertHeapify(size++);
			}
		}

		public NodeRecord pop() {
			NodeRecord nodeRecord = new NodeRecord(nodes[0], distanceMap.get(nodes[0]));
			swap(0, size - 1);
			heapIndexMap.put(nodes[size - 1], -1);
			distanceMap.remove(nodes[size - 1]);
			// free C++同学还要把原本堆顶节点析构，对java同学不必
			nodes[size - 1] = null;
			heapify(0, --size);
			return nodeRecord;
		}

		private void insertHeapify(int index) {
			while (distanceMap.get(nodes[index]) < distanceMap.get(nodes[(index - 1) / 2])) {
				swap(index, (index - 1) / 2);
				index = (index - 1) / 2;
			}
		}

		private void heapify(int index, int size) {
			int left = index * 2 + 1;
			while (left < size) {
				int smallest = left + 1 < size && distanceMap.get(nodes[left + 1]) < distanceMap.get(nodes[left])
						? left + 1
						: left;
				smallest = distanceMap.get(nodes[smallest]) < distanceMap.get(nodes[index]) ? smallest : index;
				if (smallest == index) {
					break;
				}
				swap(smallest, index);
				index = smallest;
				left = index * 2 + 1;
			}
		}

		private boolean isEntered(Node node) {
			return heapIndexMap.containsKey(node);
		}

		private boolean inHeap(Node node) {
			return isEntered(node) && heapIndexMap.get(node) != -1;
		}

		private void swap(int index1, int index2) {
			heapIndexMap.put(nodes[index1], index2);
			heapIndexMap.put(nodes[index2], index1);
			Node tmp = nodes[index1];
			nodes[index1] = nodes[index2];
			nodes[index2] = tmp;
		}
	}

	// 改进后的dijkstra算法
	// 从head出发，所有head能到达的节点，生成到达每个节点的最小路径记录并返回
	public static HashMap<Node, Integer> dijkstra2(Node head, int size) {
		NodeHeap nodeHeap = new NodeHeap(size);
		nodeHeap.addOrUpdateOrIgnore(head, 0);
		HashMap<Node, Integer> result = new HashMap<>();
		while (!nodeHeap.isEmpty()) {
			NodeRecord record = nodeHeap.pop();
			Node cur = record.node;
			int distance = record.distance;
			for (Edge edge : cur.edges) {
				nodeHeap.addOrUpdateOrIgnore(edge.to, edge.weight + distance);
			}
			result.put(cur, distance);
		}
		return result;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：有负边时确定最短距离的证明失效。路径距离相加需避免 int 溢出；不可达节点在映射中缺席，不等价于距离零。已经出堆的确定节点不可再被降键。

本条未附独立随机对数器。

#### 题解

**为什么正确**

边权非负时，任何绕经尚未确定节点的路径，其到达边界时的距离都不会小于本轮最小估计，后续也不能通过负边变小。因此本轮最小者的估计已是最终最短距离，可永久确定。

**复杂度**

线性选点版 O(V²+E)；索引堆版 O((V+E) log V)，额外空间 O(V)，图存储另计。

**边界与易错点**

有负边时确定最短距离的证明失效。路径距离相加需避免 int 溢出；不可达节点在映射中缺席，不等价于距离零。已经出堆的确定节点不可再被降键。

### 16.13 网络延迟时间

#### 题目

输入 n 个编号为1..n的节点和非负权有向边；全部可达返回最晚最早到达时刻，否则 -1。

**输入、输出与约束**

输入 n 个编号为1..n的节点和非负权有向边；全部可达返回最晚最早到达时刻，否则 -1。

**函数签名（课程入口）**

```java
public static int networkDelayTime1(int[][] times, int n, int k);
public static int networkDelayTime2(int[][] times, int n, int k);
public void add(int cur, int delay);
public int[] poll();
public boolean isEmpty();
```

**示例**

```text
输入：times=[[2,1,1],[2,3,1],[3,4,1]], n=4, k=2
输出：2
```

解释：最慢的节点4经2→3→4，在时刻2收到。

**出处与版本差异**

- [课程源码：class16/Code06_NetworkDelayTime.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Code06_NetworkDelayTime.java)。
- [LeetCode 原题 743. 网络延迟时间（Network Delay Time）](https://leetcode.com/problems/network-delay-time/)

#### 思路

建立有向邻接表，从 k 运行非负权最短路径。普通优先队列版允许同节点多份候选，出堆时跳过已经确定节点；索引堆版更新现有候选距离。全部节点确定后取最大最短距离，否则返回 -1。

#### 代码答案

```java
package class16;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.PriorityQueue;

// 课上没有讲这个题，这是我给同学们找的练习题
// leetcode 743题，可以用这道题来练习Dijkstra算法
// 测试链接 : https://leetcode.com/problems/network-delay-time
public class Code06_NetworkDelayTime {

	// 方法一 : 普通堆 + 屏蔽已经计算过的点
	public static int networkDelayTime1(int[][] times, int n, int k) {
		ArrayList<ArrayList<int[]>> nexts = new ArrayList<>();
		for (int i = 0; i <= n; i++) {
			nexts.add(new ArrayList<>());
		}
		for (int[] delay : times) {
			nexts.get(delay[0]).add(new int[] { delay[1], delay[2] });
		}
		PriorityQueue<int[]> heap = new PriorityQueue<>((a, b) -> a[1] - b[1]);
		heap.add(new int[] { k, 0 });
		boolean[] used = new boolean[n + 1];
		int num = 0;
		int max = 0;
		while (!heap.isEmpty() && num < n) {
			int[] record = heap.poll();
			int cur = record[0];
			int delay = record[1];
			// 同一节点可能有多个堆候选，已经确定后其余候选都过期。
			if (used[cur]) {
				continue;
			}
			used[cur] = true;
			num++;
			max = Math.max(max, delay);
			for (int[] next : nexts.get(cur)) {
				if (!used[next[0]]) {
					heap.add(new int[] { next[0], delay + next[1] });
				}
			}
		}
		return num < n ? -1 : max;
	}

	// 方法二 : 加强堆的解法
	public static int networkDelayTime2(int[][] times, int n, int k) {
		ArrayList<ArrayList<int[]>> nexts = new ArrayList<>();
		for (int i = 0; i <= n; i++) {
			nexts.add(new ArrayList<>());
		}
		for (int[] delay : times) {
			nexts.get(delay[0]).add(new int[] { delay[1], delay[2] });
		}
		Heap heap = new Heap(n);
		heap.add(k, 0);
		int num = 0;
		int max = 0;
		while (!heap.isEmpty()) {
			int[] record = heap.poll();
			int cur = record[0];
			int delay = record[1];
			num++;
			max = Math.max(max, delay);
			for (int[] next : nexts.get(cur)) {
				heap.add(next[0], delay + next[1]);
			}
		}
		return num < n ? -1 : max;
	}

	// 加强堆
	public static class Heap {
		public boolean[] used;
		public int[][] heap;
		public int[] hIndex;
		public int size;

		public Heap(int n) {
			used = new boolean[n + 1];
			heap = new int[n + 1][2];
			hIndex = new int[n + 1];
			Arrays.fill(hIndex, -1);
			size = 0;
		}

		public void add(int cur, int delay) {
			// 同一节点可能有多个堆候选，已经确定后其余候选都过期。
			if (used[cur]) {
				return;
			}
			if (hIndex[cur] == -1) {
				heap[size][0] = cur;
				heap[size][1] = delay;
				hIndex[cur] = size;
				heapInsert(size++);
			} else {
				int hi = hIndex[cur];
				if (delay <= heap[hi][1]) {
					heap[hi][1] = delay;
					heapInsert(hi);
				}
			}
		}

		public int[] poll() {
			int[] ans = heap[0];
			swap(0, --size);
			heapify(0);
			used[ans[0]] = true;
			hIndex[ans[0]] = -1;
			return ans;
		}

		public boolean isEmpty() {
			return size == 0;
		}

		private void heapInsert(int i) {
			int parent = (i - 1) / 2;
			while (heap[i][1] < heap[parent][1]) {
				swap(i, parent);
				i = parent;
				parent = (i - 1) / 2;
			}
		}

		private void heapify(int i) {
			int l = (i * 2) + 1;
			while (l < size) {
				int smallest = l + 1 < size && heap[l + 1][1] < heap[l][1] ? (l + 1) : l;
				smallest = heap[smallest][1] < heap[i][1] ? smallest : i;
				if (smallest == i) {
					break;
				}
				swap(smallest, i);
				i = smallest;
				l = (i * 2) + 1;
			}
		}

		private void swap(int i, int j) {
			int[] o1 = heap[i];
			int[] o2 = heap[j];
			int o1hi = hIndex[o1[0]];
			int o2hi = hIndex[o2[0]];
			heap[i] = o2;
			heap[j] = o1;
			hIndex[o1[0]] = o2hi;
			hIndex[o2[0]] = o1hi;
		}

	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：点编号通常从 1 开始，数组须预留。部分可达时不能返回已访问最大距离，应返回 -1。最大距离和路径累加要与 int 范围相容。

本条未附独立随机对数器。

#### 题解

**为什么正确**

每个节点收到信号的最早时刻就是源到它的最短路径长度。网络全部收到的时刻必须不早于其中最慢者，且到最大最短距离时全部可达节点都已收到，因此答案是这些距离的最大值。

**复杂度**

重复候选堆版 O((V+E) log(E+1))，索引堆版 O((V+E) log V)；图和候选空间 O(V+E)。

**边界与易错点**

点编号通常从 1 开始，数组须预留。部分可达时不能返回已访问最大距离，应返回 -1。最大距离和路径累加要与 int 范围相容。

### 16.14 图的边结构

#### 题目

定义带权有向边结构，记录边权、起点和终点。

**输入、输出与约束**

给定权重和两个节点引用；得到一条边对象，邻接列表由建图函数维护。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：Edge(7,A,B)
输出：表示从A到B、代价7的一条有向边
```

解释：不自动建立 B→A。

**出处与版本差异**

- [课程源码：class16/Edge.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Edge.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

一条有向边由 from、to 和 weight 描述。无向图通常用方向相反的两条边编码，或在专用算法中显式按无向端点处理。对象边类本身不执行连通性或最短路计算。

#### 代码答案

```java
package class16;

public class Edge {
	public int weight;
	public Node from;
	public Node to;

	public Edge(int weight, Node from, Node to) {
		// 保存该边代价，最短路径会沿路径累加，生成树会按它排序。
		this.weight = weight;
		// 记录有向边起点，不自动生成反向边。
		this.from = from;
		// 记录可从起点直接到达的终点。
		this.to = to;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：不要把 from/to 写反；是否允许负权由使用它的算法决定，不能由边结构统一禁止。两条无向编码边不等价于两次独立选择的生成树边。

本条未附独立随机对数器。

#### 题解

**为什么正确**

边的端点固定了可行移动方向，weight 是使用该边的代价；图算法只有遵守相同的方向编码，才能让路径和邻接信息相符。

**复杂度**

每条边创建与字段访问 O(1)，E 条边占 O(E) 空间。

**边界与易错点**

不要把 from/to 写反；是否允许负权由使用它的算法决定，不能由边结构统一禁止。两条无向编码边不等价于两次独立选择的生成树边。

### 16.15 图结构

#### 题目

定义课程图算法共享的图结构，维护节点集合和边集合。

**输入、输出与约束**

图模型支撑类，保存节点表与边集合，不直接返回算法答案。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：节点编号1、2、3，只有边1→2
输出：nodes有3个节点，edges有1条边
```

解释：孤立节点3也必须登记，拓扑和连通判断才完整。

**出处与版本差异**

- [课程源码：class16/Graph.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Graph.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

Graph 将“编号→节点”的映射与全图边集合集中保存。编号映射保证建图时同一个编号复用同一个节点对象；节点内的邻接表提供局部访问，边集合提供 Kruskal 等算法的全边扫描。

#### 代码答案

```java
package class16;

import java.util.HashMap;
import java.util.HashSet;

public class Graph {
	public HashMap<Integer, Node> nodes;
	public HashSet<Edge> edges;

	public Graph() {
		// 用唯一编号定位并复用节点对象，包括需要手工登记的孤立节点。
		nodes = new HashMap<>();
		// 保存全图边对象，供需要全局排序或扫描的算法使用。
		edges = new HashSet<>();
	}
}
```

---

##### 边界核对

用题面示例核对接口，再检查：孤立节点不会自动由边列表出现，应单独加入 nodes。HashSet<Edge> 若未覆写相等判定，按对象身份而非端点权重去重。

本条未附独立随机对数器。

#### 题解

**为什么正确**

同编号节点复用避免把同一图顶点拆成多个对象。全局边集合与每节点出边表同步更新，才能让全图扫描和从节点出发的遍历看到同样的边。

**复杂度**

存储 V 个节点、E 条边需要 O(V+E) 空间；按编号查询平均 O(1)。

**边界与易错点**

孤立节点不会自动由边列表出现，应单独加入 nodes。HashSet<Edge> 若未覆写相等判定，按对象身份而非端点权重去重。

### 16.16 邻接矩阵生成图

#### 题目

给定每行格式为 `[权重, 起点, 终点]` 的二维数组，构造课程统一的图节点、边和邻接关系。

**输入、输出与约束**

输入每行恰有三个整数的有向边表，顺序为 [weight,from,to]；返回 Graph。

**函数签名（课程入口）**

```java
public static Graph createGraph(int[][] matrix);
```

**示例**

```text
输入：matrix=[[5,1,2],[7,2,3]]
输出：得到边1→2权5、2→3权7
```

解释：编号2只创建一次，被两条边共同引用。

**出处与版本差异**

- [课程源码：class16/GraphGenerator.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/GraphGenerator.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

逐行读取 [weight,from,to]。若编号尚未出现就创建节点；创建边对象后，同时加入起点邻居、起点出边、全图边集合，并增加起点出度和终点入度。

#### 代码答案

```java
package class16;

public class GraphGenerator {

	// matrix 所有的边
	// N*3 的矩阵
	// [weight, from节点上面的值，to节点上面的值]
	//
	// [ 5 , 0 , 7]
	// [ 3 , 0,  1]
	//
	public static Graph createGraph(int[][] matrix) {
		Graph graph = new Graph();
		for (int i = 0; i < matrix.length; i++) {
			 // 拿到每一条边， matrix[i]
			// 第0列是边权，先确认课程输入顺序避免错位建图。
			int weight = matrix[i][0];
			int from = matrix[i][1];
			int to = matrix[i][2];
			if (!graph.nodes.containsKey(from)) {
				graph.nodes.put(from, new Node(from));
			}
			if (!graph.nodes.containsKey(to)) {
				graph.nodes.put(to, new Node(to));
			}
			Node fromNode = graph.nodes.get(from);
			Node toNode = graph.nodes.get(to);
			Edge newEdge = new Edge(weight, fromNode, toNode);
			// 登记起点可直接到达的终点。
			fromNode.nexts.add(toNode);
			// 新增一条出边，起点出度加一。
			fromNode.out++;
			// 同一条边对终点贡献一个入度。
			toNode.in++;
			fromNode.edges.add(newEdge);
			graph.edges.add(newEdge);
		}
		return graph;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：课程每行顺序是权重、起点、终点，不是常见的起点、终点、权重。无向图应额外提供反向边；仅凭边列表无法发现孤立节点。

本条未附独立随机对数器。

#### 题解

**为什么正确**

每条输入边被完整写入图模型的所有相关字段，且编号映射复用端点对象，因此多条边能够正确在同一节点交汇，而不是形成互不相认的节点副本。

**复杂度**

期望建图时间 O(V+E)，空间 O(V+E)。

**边界与易错点**

课程每行顺序是权重、起点、终点，不是常见的起点、终点、权重。无向图应额外提供反向边；仅凭边列表无法发现孤立节点。

### 16.17 图的节点结构

#### 题目

定义图节点结构，记录节点值、入度、出度、邻接节点和相邻边。

**输入、输出与约束**

本条为图模型支撑类，不是独立在线评测题；由建图函数创建并维护。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：创建边 A→B 权重3
输出：A.out 加1，B.in 加1；A.nexts含B，A.edges含该边
```

解释：邻接信息和度数同步变化。

**出处与版本差异**

- [课程源码：class16/Node.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class16/Node.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

图节点保存 value、入度 in、出度 out、邻居 nexts 和出边 edges。nexts 适合无权遍历；edges 同时携带权重，供最短路与生成树使用。节点身份由对象引用区分。

#### 代码答案

```java
package class16;

import java.util.ArrayList;

// 点结构的描述
public class Node {
	public int value;
	public int in;
	public int out;
	public ArrayList<Node> nexts;
	public ArrayList<Edge> edges;

	public Node(int value) {
		this.value = value;
		in = 0;
		out = 0;
		// 保存出边指向的邻居，便于按点遍历。
		nexts = new ArrayList<>();
		// 单独保存有权出边，避免遍历时丢失权重。
		edges = new ArrayList<>();
	}
}
```

---

##### 边界核对

用题面示例核对接口，再检查：value 相同不自动意味着同一个节点对象。重复边会重复贡献度数；不要只更新列表而遗漏 in/out。

本条未附独立随机对数器。

#### 题解

**为什么正确**

若创建边 u→v 时同时更新 u.nexts、u.edges、u.out 和 v.in，节点的邻接与度数才能描述同一张图；依赖这些字段的算法才会得到一致结果。

**复杂度**

单节点固定字段 O(1)，邻居与出边列表总空间按全图为 O(V+E)。

**边界与易错点**

value 相同不自动意味着同一个节点对象。重复边会重复贡献度数；不要只更新列表而遗漏 in/out。


<a id="course-17"></a>

## 第 17 课：图算法补充与暴力递归

### 17.1 Dijkstra 单源最短路径

#### 题目

给定非负权图和源点，计算源点到其他节点的最短距离；网络延迟题返回信号到达全部节点的最短时间。

**输入、输出与约束**

输入非负权有向邻接图和源节点；返回所有可达节点的最短距离映射。

**函数签名（课程入口）**

```java
public static HashMap<Node, Integer> dijkstra1(Node from);
public static Node getMinDistanceAndUnselectedNode(HashMap<Node, Integer> distanceMap, HashSet<Node> touchedNodes);
public boolean isEmpty();
public void addOrUpdateOrIgnore(Node node, int distance);
public NodeRecord pop();
public static HashMap<Node, Integer> dijkstra2(Node head, int size);
```

**示例**

```text
输入：边 A→B 权2、A→C 权5、B→C 权1，从A出发
输出：A=0，B=2，C=3
```

解释：经 B 可将 C 的估计由 5 改进为 3。

**出处与版本差异**

- [课程源码：class17/Code01_Dijkstra.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class17/Code01_Dijkstra.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

维护源点到各节点当前最短估计。反复选尚未确定且距离最小者，确定其距离，再用“当前距离+出边权重”松弛邻居。版本一线性扫描距离表选点；版本二用支持降键的堆与反向索引加速。

#### 代码答案

```java
package class17;

import java.util.HashMap;
import java.util.HashSet;
import java.util.Map.Entry;

// no negative weight
public class Code01_Dijkstra {

	public static HashMap<Node, Integer> dijkstra1(Node from) {
		HashMap<Node, Integer> distanceMap = new HashMap<>();
		// 源到自身距离为零，作为所有松弛的起点。
		distanceMap.put(from, 0);
		// 打过对号的点
		HashSet<Node> selectedNodes = new HashSet<>();
		Node minNode = getMinDistanceAndUnselectedNode(distanceMap, selectedNodes);
		while (minNode != null) {
			// 原始点 -> minNode(跳转点) 最小距离distance
			int distance = distanceMap.get(minNode);
			for (Edge edge : minNode.edges) {
				Node toNode = edge.to;
				if (!distanceMap.containsKey(toNode)) {
					// 经过当前已确定节点，再走这条边，得到邻居的一条可行路径长度。
					distanceMap.put(toNode, distance + edge.weight);
				} else { // toNode
					// 经过当前已确定节点，再走这条边，得到邻居的一条可行路径长度。
					distanceMap.put(edge.to, Math.min(distanceMap.get(toNode), distance + edge.weight));
				}
			}
			// 非负权保证最小未确定估计已经最优，此后无需再修改。
			selectedNodes.add(minNode);
			minNode = getMinDistanceAndUnselectedNode(distanceMap, selectedNodes);
		}
		return distanceMap;
	}

	public static Node getMinDistanceAndUnselectedNode(HashMap<Node, Integer> distanceMap, HashSet<Node> touchedNodes) {
		Node minNode = null;
		int minDistance = Integer.MAX_VALUE;
		for (Entry<Node, Integer> entry : distanceMap.entrySet()) {
			Node node = entry.getKey();
			int distance = entry.getValue();
			if (!touchedNodes.contains(node) && distance < minDistance) {
				minNode = node;
				minDistance = distance;
			}
		}
		return minNode;
	}

	public static class NodeRecord {
		public Node node;
		public int distance;

		public NodeRecord(Node node, int distance) {
			this.node = node;
			this.distance = distance;
		}
	}

	public static class NodeHeap {
		// 堆！
		private Node[] nodes;
		// node -> 堆上的什么位置？

		private HashMap<Node, Integer> heapIndexMap;
		private HashMap<Node, Integer> distanceMap;
		private int size;

		public NodeHeap(int size) {
			nodes = new Node[size];
			heapIndexMap = new HashMap<>();
			distanceMap = new HashMap<>();
			size = 0;
		}

		public boolean isEmpty() {
			return size == 0;
		}

		// 有一个点叫node，现在发现了一个从源节点出发到达node的距离为distance
		// 判断要不要更新，如果需要的话，就更新
		public void addOrUpdateOrIgnore(Node node, int distance) {
			if (inHeap(node)) { // update
				distanceMap.put(node, Math.min(distanceMap.get(node), distance));
				insertHeapify(node, heapIndexMap.get(node));
			}
			if (!isEntered(node)) { // add
				nodes[size] = node;
				heapIndexMap.put(node, size);
				distanceMap.put(node, distance);
				insertHeapify(node, size++);
			}
			// ignore
		}

		public NodeRecord pop() {
			NodeRecord nodeRecord = new NodeRecord(nodes[0], distanceMap.get(nodes[0]));
			swap(0, size - 1); // 0 > size - 1    size - 1 > 0
			heapIndexMap.put(nodes[size - 1], -1);
			distanceMap.remove(nodes[size - 1]);
			// free C++同学还要把原本堆顶节点析构，对java同学不必
			nodes[size - 1] = null;
			heapify(0, --size);
			return nodeRecord;
		}

		private void insertHeapify(Node node, int index) {
			while (distanceMap.get(nodes[index]) < distanceMap.get(nodes[(index - 1) / 2])) {
				swap(index, (index - 1) / 2);
				index = (index - 1) / 2;
			}
		}

		private void heapify(int index, int size) {
			int left = index * 2 + 1;
			while (left < size) {
				int smallest = left + 1 < size && distanceMap.get(nodes[left + 1]) < distanceMap.get(nodes[left])
						? left + 1
						: left;
				smallest = distanceMap.get(nodes[smallest]) < distanceMap.get(nodes[index]) ? smallest : index;
				if (smallest == index) {
					break;
				}
				swap(smallest, index);
				index = smallest;
				left = index * 2 + 1;
			}
		}

		private boolean isEntered(Node node) {
			return heapIndexMap.containsKey(node);
		}

		private boolean inHeap(Node node) {
			return isEntered(node) && heapIndexMap.get(node) != -1;
		}

		private void swap(int index1, int index2) {
			heapIndexMap.put(nodes[index1], index2);
			heapIndexMap.put(nodes[index2], index1);
			Node tmp = nodes[index1];
			nodes[index1] = nodes[index2];
			nodes[index2] = tmp;
		}
	}

	// 改进后的dijkstra算法
	// 从head出发，所有head能到达的节点，生成到达每个节点的最小路径记录并返回
	public static HashMap<Node, Integer> dijkstra2(Node head, int size) {
		NodeHeap nodeHeap = new NodeHeap(size);
		nodeHeap.addOrUpdateOrIgnore(head, 0);
		HashMap<Node, Integer> result = new HashMap<>();
		while (!nodeHeap.isEmpty()) {
			NodeRecord record = nodeHeap.pop();
			Node cur = record.node;
			int distance = record.distance;
			for (Edge edge : cur.edges) {
				nodeHeap.addOrUpdateOrIgnore(edge.to, edge.weight + distance);
			}
			result.put(cur, distance);
		}
		return result;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：有负边时确定最短距离的证明失效。路径距离相加需避免 int 溢出；不可达节点在映射中缺席，不等价于距离零。已经出堆的确定节点不可再被降键。

本条未附独立随机对数器。

#### 题解

**为什么正确**

边权非负时，任何绕经尚未确定节点的路径，其到达边界时的距离都不会小于本轮最小估计，后续也不能通过负边变小。因此本轮最小者的估计已是最终最短距离，可永久确定。

**复杂度**

线性选点版 O(V²+E)；索引堆版 O((V+E) log V)，额外空间 O(V)，图存储另计。

**边界与易错点**

有负边时确定最短距离的证明失效。路径距离相加需避免 int 溢出；不可达节点在映射中缺席，不等价于距离零。已经出堆的确定节点不可再被降键。

### 17.2 汉诺塔

#### 题目

给定 `n` 个由小到大叠放的圆盘和三根柱子，每次只能移动一个圆盘且大盘不能压在小盘上，输出完整移动过程。

**输入、输出与约束**

输入正整数盘数 N；输出合法的最少搬运步骤。

**函数签名（课程入口）**

```java
public static void hanoi1(int n);
public static void hanoi2(int n);
public static void hanoi3(int N);
```

**示例**

```text
输入：n=2，从左移到右
输出：小盘左→中，大盘左→右，小盘中→右
```

解释：共3步，任一步都没有大盘压小盘。

**出处与版本差异**

- [课程源码：class17/Code02_Hanoi.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class17/Code02_Hanoi.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

移动 n 个盘从 from 到 to，借助 other：先把上面的 n-1 个移到 other，再把最大盘移到 to，最后把 n-1 个从 other 移到 to。具体六方向方法与通用三柱参数方法描述的是同一递归。

#### 代码答案

```java
package class17;

import java.util.HashSet;
import java.util.Stack;

public class Code02_Hanoi {

	public static void hanoi1(int n) {
		leftToRight(n);
	}

	// 请把1~N层圆盘 从左 -> 右
	public static void leftToRight(int n) {
		if (n == 1) { // base case
			System.out.println("Move 1 from left to right");
			return;
		}
		leftToMid(n - 1);
		System.out.println("Move " + n + " from left to right");
		midToRight(n - 1);
	}

	// 请把1~N层圆盘 从左 -> 中
	public static void leftToMid(int n) {
		if (n == 1) {
			System.out.println("Move 1 from left to mid");
			return;
		}
		leftToRight(n - 1);
		System.out.println("Move " + n + " from left to mid");
		rightToMid(n - 1);
	}

	public static void rightToMid(int n) {
		if (n == 1) {
			System.out.println("Move 1 from right to mid");
			return;
		}
		rightToLeft(n - 1);
		System.out.println("Move " + n + " from right to mid");
		leftToMid(n - 1);
	}

	public static void midToRight(int n) {
		if (n == 1) {
			System.out.println("Move 1 from mid to right");
			return;
		}
		midToLeft(n - 1);
		System.out.println("Move " + n + " from mid to right");
		leftToRight(n - 1);
	}

	public static void midToLeft(int n) {
		if (n == 1) {
			System.out.println("Move 1 from mid to left");
			return;
		}
		midToRight(n - 1);
		System.out.println("Move " + n + " from mid to left");
		rightToLeft(n - 1);
	}

	public static void rightToLeft(int n) {
		if (n == 1) {
			System.out.println("Move 1 from right to left");
			return;
		}
		rightToMid(n - 1);
		System.out.println("Move " + n + " from right to left");
		midToLeft(n - 1);
	}

	public static void hanoi2(int n) {
		if (n > 0) {
			func(n, "left", "right", "mid");
		}
	}

	public static void func(int N, String from, String to, String other) {
		if (N == 1) { // base
			System.out.println("Move 1 from " + from + " to " + to);
		} else {
			// 先腾空最大盘上方，把小盘整体搬到辅助柱。
			func(N - 1, from, other, to);
			System.out.println("Move " + N + " from " + from + " to " + to);
			// 最大盘已就位，再把小盘从辅助柱叠到目标柱。
			func(N - 1, other, to, from);
		}
	}

	public static class Record {
		public int level;
		public String from;
		public String to;
		public String other;

		public Record(int l, String f, String t, String o) {
			level = l;
			from = f;
			to = t;
			other = o;
		}
	}

	// 之前的迭代版本，很多同学表示看不懂
	// 所以我换了一个更容易理解的版本
	// 看注释吧！好懂！
	// 你把汉诺塔问题想象成二叉树
	// 比如当前还剩i层，其实打印这个过程就是：
	// 1) 去打印第一部分 -> 左子树
	// 2) 打印当前的动作 -> 当前节点
	// 3) 去打印第二部分 -> 右子树
	// 那么你只需要记录每一个任务 : 有没有加入过左子树的任务
	// 就可以完成迭代对递归的替代了
	public static void hanoi3(int N) {
		if (N < 1) {
			return;
		}
		// 每一个记录进栈
		Stack<Record> stack = new Stack<>();
		// 记录每一个记录有没有加入过左子树的任务
		HashSet<Record> finishLeft = new HashSet<>();
		// 初始的任务，认为是种子
		stack.add(new Record(N, "left", "right", "mid"));
		while (!stack.isEmpty()) {
			// 弹出当前任务
			Record cur = stack.pop();
			if (cur.level == 1) {
				// 如果层数只剩1了
				// 直接打印
				System.out.println("Move 1 from " + cur.from + " to " + cur.to);
			} else {
				// 如果不只1层
				if (!finishLeft.contains(cur)) {
					// 如果当前任务没有加入过左子树的任务
					// 现在就要加入了！
					// 把当前的任务重新压回去，因为还不到打印的时候
					// 再加入左子树任务！
					finishLeft.add(cur);
					stack.push(cur);
					stack.push(new Record(cur.level - 1, cur.from, cur.other, cur.to));
				} else {
					// 如果当前任务加入过左子树的任务
					// 说明此时已经是第二次弹出了！
					// 说明左子树的所有打印任务都完成了
					// 当前可以打印了！
					// 然后加入右子树的任务
					// 当前的任务可以永远的丢弃了！
					// 因为完成了左子树、打印了自己、加入了右子树
					// 再也不用回到这个任务了
					System.out.println("Move " + cur.level + " from " + cur.from + " to " + cur.to);
					stack.push(new Record(cur.level - 1, cur.other, cur.to, cur.from));
				}
			}
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int n = 3;
		hanoi1(n);
		System.out.println("============");
		hanoi2(n);
		System.out.println("============");
		hanoi3(n);
	}
```

#### 题解

**为什么正确**

最大盘要移动，所有更小盘必须先离开它且不能在目标柱上，因此先搬到辅助柱是必要的。最大盘就位后再搬小盘也是必要的，递归步骤既合法又给出最少移动数 T(n)=2T(n-1)+1。

**复杂度**

输出与时间均为 2^N-1，即 O(2^N)，递归栈 O(N)。

**边界与易错点**

第二次递归的柱子角色与第一次不同。N≤0 时不应进入缺少此边界的递归方法；不要把打印规模忽略成 O(N)。

### 17.3 打印字符串的全部子序列

#### 题目

给定字符串，返回它的全部子序列；去重版本中相同内容只保留一次。

**输入、输出与约束**

输入非 null 字符串；按方法返回全部下标选择结果或去重后的文本集合。

**函数签名（课程入口）**

```java
public static List<String> subs(String s);
public static List<String> subsNoRepeat(String s);
```

**示例**

```text
输入：str="aa"
输出：不去重：["","a","a","aa"]；去重：{"","a","aa"}
```

解释：两种选一个 a 的下标方案有相同文本。

**出处与版本差异**

- [课程源码：class17/Code03_PrintAllSubsquences.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class17/Code03_PrintAllSubsquences.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

递归状态由 index 和已经选择的 path 组成。对于当前位置字符，分别走“不选择”和“选择后追加”两个分支；index 到末尾时收集 path。需要按内容去重时，把终点结果放入集合。

#### 代码答案

```java
package class17;

import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;

public class Code03_PrintAllSubsquences {

	// s -> "abc" ->
	public static List<String> subs(String s) {
		char[] str = s.toCharArray();
		String path = "";
		List<String> ans = new ArrayList<>();
		process1(str, 0, ans, path);
		return ans;
	}

	// str 固定参数
	// 来到了str[index]字符，index是位置
	// str[0..index-1]已经走过了！之前的决定，都在path上
	// 之前的决定已经不能改变了，就是path
	// str[index....]还能决定，之前已经确定，而后面还能自由选择的话，
	// 把所有生成的子序列，放入到ans里去
	public static void process1(char[] str, int index, List<String> ans, String path) {
		if (index == str.length) {
			// 所有位置都决定完，此时 path 是一个完整子序列。
			ans.add(path);
			return;
		}
		// 没有要index位置的字符
		// 不选择当前字符，路径内容不变，只推进决策位置。
		process1(str, index + 1, ans, path);
		// 要了index位置的字符
		process1(str, index + 1, ans, path + String.valueOf(str[index]));
	}

	public static List<String> subsNoRepeat(String s) {
		char[] str = s.toCharArray();
		String path = "";
		HashSet<String> set = new HashSet<>();
		process2(str, 0, set, path);
		List<String> ans = new ArrayList<>();
		for (String cur : set) {
			ans.add(cur);
		}
		return ans;
	}

	public static void process2(char[] str, int index, HashSet<String> set, String path) {
		if (index == str.length) {
			set.add(path);
			return;
		}
		String no = path;
		process2(str, index + 1, set, no);
		// 选择当前字符并追加到路径尾，保持原先相对次序。
		String yes = path + String.valueOf(str[index]);
		process2(str, index + 1, set, yes);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		String test = "acccc";
		List<String> ans1 = subs(test);
		List<String> ans2 = subsNoRepeat(test);

		for (String str : ans1) {
			System.out.println(str);
		}
		System.out.println("=================");
		for (String str : ans2) {
			System.out.println(str);
		}
		System.out.println("=================");

	}
```

#### 题解

**为什么正确**

每个位置恰有选或不选两种选择，因此每个下标子集对应唯一递归路径。保持从左到右决策保证字符相对次序不变；相同字符导致不同下标子集产生相同文本，集合版再消去内容重复。

**复杂度**

N 个字符有 2^N 个下标子集；构造与保存字符串总成本可达 O(N2^N)，递归深度 O(N)，输出另计。

**边界与易错点**

子序列允许不连续，且通常包含空字符串。去重版去的是相同内容，不是禁止重复字符被同时选中。

### 17.4 打印字符串的全部排列

#### 题目

给定字符串，返回字符能够组成的全部排列；去重版本中相同排列只保留一次。

**输入、输出与约束**

输入非 null 字符串，去重实现须满足其字符表范围；返回字符全排列列表。

**函数签名（课程入口）**

```java
public static List<String> permutation1(String s);
public static List<String> permutation2(String s);
public static void g1(char[] str, int index, List<String> ans);
public static List<String> permutation3(String s);
public static void g2(char[] str, int index, List<String> ans);
```

**示例**

```text
输入：str="aba"
输出：去重结果 {"aab","aba","baa"}
```

解释：同层把第二个 a 再放到首位不会产生新结果。

**出处与版本差异**

- [课程源码：class17/Code04_PrintAllPermutations.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class17/Code04_PrintAllPermutations.java)。
- [LeetCode 对应题 46. 全排列（Permutations）](https://leetcode.com/problems/permutations/)

课程版本说明：

- LeetCode 46：排列模型对应；课程输入为字符串。

#### 思路

递归固定结果的第 index 位：枚举还未固定的字符，把候选交换到 index，再递归下一位，返回后交换回来。去重版在同一层记录已经放过的字符，跳过同值候选。

#### 代码答案

```java
package class17;

import java.util.ArrayList;
import java.util.List;

public class Code04_PrintAllPermutations {

	public static List<String> permutation1(String s) {
		List<String> ans = new ArrayList<>();
		if (s == null || s.length() == 0) {
			return ans;
		}
		char[] str = s.toCharArray();
		ArrayList<Character> rest = new ArrayList<Character>();
		for (char cha : str) {
			rest.add(cha);
		}
		String path = "";
		f(rest, path, ans);
		return ans;
	}

	public static void f(ArrayList<Character> rest, String path, List<String> ans) {
		if (rest.isEmpty()) {
			ans.add(path);
		} else {
			int N = rest.size();
			for (int i = 0; i < N; i++) {
				char cur = rest.get(i);
				rest.remove(i);
				f(rest, path + cur, ans);
				rest.add(i, cur);
			}
		}
	}

	public static List<String> permutation2(String s) {
		List<String> ans = new ArrayList<>();
		if (s == null || s.length() == 0) {
			return ans;
		}
		char[] str = s.toCharArray();
		g1(str, 0, ans);
		return ans;
	}

	public static void g1(char[] str, int index, List<String> ans) {
		if (index == str.length) {
			ans.add(String.valueOf(str));
		} else {
			for (int i = index; i < str.length; i++) {
				// 把候选字符放到当前待确定位置；递归返回后的同样交换负责恢复。
				swap(str, index, i);
				g1(str, index + 1, ans);
				// 把候选字符放到当前待确定位置；递归返回后的同样交换负责恢复。
				swap(str, index, i);
			}
		}
	}

	public static List<String> permutation3(String s) {
		List<String> ans = new ArrayList<>();
		if (s == null || s.length() == 0) {
			return ans;
		}
		char[] str = s.toCharArray();
		g2(str, 0, ans);
		return ans;
	}

	public static void g2(char[] str, int index, List<String> ans) {
		if (index == str.length) {
			ans.add(String.valueOf(str));
		} else {
			boolean[] visited = new boolean[256];
			for (int i = index; i < str.length; i++) {
				if (!visited[str[i]]) {
					// 本层已经用过这个字符，后续同值候选不再建立重复分支。
					visited[str[i]] = true;
					// 把候选字符放到当前待确定位置；递归返回后的同样交换负责恢复。
					swap(str, index, i);
					g2(str, index + 1, ans);
					// 把候选字符放到当前待确定位置；递归返回后的同样交换负责恢复。
					swap(str, index, i);
				}
			}
		}
	}

	public static void swap(char[] chs, int i, int j) {
		char tmp = chs[i];
		chs[i] = chs[j];
		chs[j] = tmp;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		String s = "acc";
		List<String> ans1 = permutation1(s);
		for (String str : ans1) {
			System.out.println(str);
		}
		System.out.println("=======");
		List<String> ans2 = permutation2(s);
		for (String str : ans2) {
			System.out.println(str);
		}
		System.out.println("=======");
		List<String> ans3 = permutation3(s);
		for (String str : ans3) {
			System.out.println(str);
		}

	}
```

#### 题解

**为什么正确**

每次固定一个位置，剩余位置递归排列，所有不同位置选择被完整枚举。交换恢复让兄弟分支看到相同原状态；同层重复值只能产生相同前缀及等价剩余字符集合，跳过不会丢失不同排列。

**复杂度**

N 个互异字符有 N! 个答案，构造输出时间 O(N·N!)，递归及工作数组 O(N)，输出 O(N·N!)。

**边界与易错点**

去重集合必须每层独立，不能所有层共用。源码某版本使用长度256的字符表，只适用于该字符范围；一般 Java char 应改集合或足够大数组。

### 17.5 只用递归逆序栈

#### 题目

只允许使用递归函数和栈本身的操作，把栈中元素次序完全逆转。

**输入、输出与约束**

输入栈对象，允许空栈；只用栈操作和递归原地逆序。

**函数签名（课程入口）**

```java
public static void reverse(Stack<Integer> stack);
```

**示例**

```text
输入：栈从底到顶 [1,2,3]
输出：从底到顶 [3,2,1]
```

解释：原底1在全部剩余元素反转后最后压到顶。

**出处与版本差异**

- [课程源码：class17/Code05_ReverseStackUsingRecursive.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class17/Code05_ReverseStackUsingRecursive.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

辅助函数 f 弹出并返回栈底元素，同时把它上方的元素按原顺序压回。reverse 每次取出栈底，递归反转剩余栈，再把原栈底压到顶部。

#### 代码答案

```java
package class17;

import java.util.Stack;

public class Code05_ReverseStackUsingRecursive {

	public static void reverse(Stack<Integer> stack) {
		if (stack.isEmpty()) {
			return;
		}
		int i = f(stack);
		reverse(stack);
		// 剩余栈已反转，原底元素现在应成为新栈顶。
		stack.push(i);
	}

	// 栈底元素移除掉
	// 上面的元素盖下来
	// 返回移除掉的栈底元素
	public static int f(Stack<Integer> stack) {
		int result = stack.pop();
		if (stack.isEmpty()) {
			return result;
		} else {
			// 递归取出底部，当前弹出的上层值留在本次调用栈中。
			int last = f(stack);
			// 底部已取走，把当前上层值压回，恢复其原相对次序。
			stack.push(result);
			return last;
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		Stack<Integer> test = new Stack<Integer>();
		test.push(1);
		test.push(2);
		test.push(3);
		test.push(4);
		test.push(5);
		reverse(test);
		while (!test.isEmpty()) {
			System.out.println(test.pop());
		}

	}
```

#### 题解

**为什么正确**

f 通过递归暂存上层元素，到最深处取得栈底，回溯时恢复上层顺序。reverse 先反转上层，再把原底放到顶，恰好完成整栈顺序翻转；空栈为归纳终点。

**复杂度**

取底一次 O(N)，总时间 O(N²)，递归栈 O(N)，未额外创建集合。

**边界与易错点**

“不用额外数据结构”不意味着额外空间 O(1)，递归栈仍占 O(N)。f 返回前必须压回非底部元素。

### 17.6 图的边结构

#### 题目

定义带权有向边结构，记录边权、起点和终点。

**输入、输出与约束**

给定权重和两个节点引用；得到一条边对象，邻接列表由建图函数维护。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：Edge(7,A,B)
输出：表示从A到B、代价7的一条有向边
```

解释：不自动建立 B→A。

**出处与版本差异**

- [课程源码：class17/Edge.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class17/Edge.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

一条有向边由 from、to 和 weight 描述。无向图通常用方向相反的两条边编码，或在专用算法中显式按无向端点处理。对象边类本身不执行连通性或最短路计算。

#### 代码答案

```java
package class17;

public class Edge {
	public int weight;
	public Node from;
	public Node to;

	public Edge(int weight, Node from, Node to) {
		// 保存该边代价，最短路径会沿路径累加，生成树会按它排序。
		this.weight = weight;
		// 记录有向边起点，不自动生成反向边。
		this.from = from;
		// 记录可从起点直接到达的终点。
		this.to = to;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：不要把 from/to 写反；是否允许负权由使用它的算法决定，不能由边结构统一禁止。两条无向编码边不等价于两次独立选择的生成树边。

本条未附独立随机对数器。

#### 题解

**为什么正确**

边的端点固定了可行移动方向，weight 是使用该边的代价；图算法只有遵守相同的方向编码，才能让路径和邻接信息相符。

**复杂度**

每条边创建与字段访问 O(1)，E 条边占 O(E) 空间。

**边界与易错点**

不要把 from/to 写反；是否允许负权由使用它的算法决定，不能由边结构统一禁止。两条无向编码边不等价于两次独立选择的生成树边。

### 17.7 图结构

#### 题目

定义课程图算法共享的图结构，维护节点集合和边集合。

**输入、输出与约束**

图模型支撑类，保存节点表与边集合，不直接返回算法答案。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：节点编号1、2、3，只有边1→2
输出：nodes有3个节点，edges有1条边
```

解释：孤立节点3也必须登记，拓扑和连通判断才完整。

**出处与版本差异**

- [课程源码：class17/Graph.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class17/Graph.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

Graph 将“编号→节点”的映射与全图边集合集中保存。编号映射保证建图时同一个编号复用同一个节点对象；节点内的邻接表提供局部访问，边集合提供 Kruskal 等算法的全边扫描。

#### 代码答案

```java
package class17;

import java.util.HashMap;
import java.util.HashSet;

public class Graph {
	public HashMap<Integer, Node> nodes;
	public HashSet<Edge> edges;

	public Graph() {
		// 用唯一编号定位并复用节点对象，包括需要手工登记的孤立节点。
		nodes = new HashMap<>();
		// 保存全图边对象，供需要全局排序或扫描的算法使用。
		edges = new HashSet<>();
	}
}
```

---

##### 边界核对

用题面示例核对接口，再检查：孤立节点不会自动由边列表出现，应单独加入 nodes。HashSet<Edge> 若未覆写相等判定，按对象身份而非端点权重去重。

本条未附独立随机对数器。

#### 题解

**为什么正确**

同编号节点复用避免把同一图顶点拆成多个对象。全局边集合与每节点出边表同步更新，才能让全图扫描和从节点出发的遍历看到同样的边。

**复杂度**

存储 V 个节点、E 条边需要 O(V+E) 空间；按编号查询平均 O(1)。

**边界与易错点**

孤立节点不会自动由边列表出现，应单独加入 nodes。HashSet<Edge> 若未覆写相等判定，按对象身份而非端点权重去重。

### 17.8 图的节点结构

#### 题目

定义图节点结构，记录节点值、入度、出度、邻接节点和相邻边。

**输入、输出与约束**

本条为图模型支撑类，不是独立在线评测题；由建图函数创建并维护。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：创建边 A→B 权重3
输出：A.out 加1，B.in 加1；A.nexts含B，A.edges含该边
```

解释：邻接信息和度数同步变化。

**出处与版本差异**

- [课程源码：class17/Node.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class17/Node.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

图节点保存 value、入度 in、出度 out、邻居 nexts 和出边 edges。nexts 适合无权遍历；edges 同时携带权重，供最短路与生成树使用。节点身份由对象引用区分。

#### 代码答案

```java
package class17;

import java.util.ArrayList;

// 点结构的描述
public class Node {
	public int value;
	public int in;
	public int out;
	public ArrayList<Node> nexts;
	public ArrayList<Edge> edges;

	public Node(int value) {
		this.value = value;
		in = 0;
		out = 0;
		// 保存出边指向的邻居，便于按点遍历。
		nexts = new ArrayList<>();
		// 单独保存有权出边，避免遍历时丢失权重。
		edges = new ArrayList<>();
	}
}
```

---

##### 边界核对

用题面示例核对接口，再检查：value 相同不自动意味着同一个节点对象。重复边会重复贡献度数；不要只更新列表而遗漏 in/out。

本条未附独立随机对数器。

#### 题解

**为什么正确**

若创建边 u→v 时同时更新 u.nexts、u.edges、u.out 和 v.in，节点的邻接与度数才能描述同一张图；依赖这些字段的算法才会得到一致结果。

**复杂度**

单节点固定字段 O(1)，邻居与出边列表总空间按全图为 O(V+E)。

**边界与易错点**

value 相同不自动意味着同一个节点对象。重复边会重复贡献度数；不要只更新列表而遗漏 in/out。


<a id="course-18"></a>

## 第 18 课：从暴力递归到动态规划（一）

### 18.1 机器人走路方法数

#### 题目

数轴上有 `N` 个位置，机器人从 `start` 出发恰走 `K` 步，返回到达 `aim` 的方法数。

**输入、输出与约束**

位置为1..N；每步移到相邻位置且不得越界，端点只能向内走；输入 start、aim、K，返回恰走K步的方法数。

**函数签名（课程入口）**

```java
public static int ways1(int N, int start, int aim, int K);
public static int ways2(int N, int start, int aim, int K);
public static int ways3(int N, int start, int aim, int K);
```

**示例**

```text
输入：N=3,start=2,aim=2,K=2
输出：2
```

解释：合法路径为 2→1→2 和 2→3→2。

**出处与版本差异**

- [课程源码：class18/Code01_RobotWalk.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class18/Code01_RobotWalk.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

1. 先决定递归状态：`process(cur, rest, aim, N)` 表示机器人当前在 `cur`，还必须走 `rest` 步，最终停在 `aim` 的方案数。状态不是“已经走了几步”，也不是最短距离。
2. `rest == 0` 时不能再移动；仅当 `cur == aim` 才贡献一条完整路径，否则贡献 0。还要走时，位置 1 只能到 2，位置 N 只能到 N-1，中间位置可向左右各走一步，两种选择的方法数相加。
3. 暴力树里会反复遇到相同的 `(cur, rest)`。例如从中间位置先左后右与先右后左，会回到相同位置和剩余步数。记忆化用 `-1` 表示“没算过”，而 0 是“算过但无路可走”。
4. 改成表格：`dp[cur][rest]` 与递归含义完全一致，唯一的零步初值是 `dp[aim][0]=1`。每一列依赖前一列，故按剩余步数从小到大填；最后返回 `dp[start][K]`。

#### 代码答案

```java
package class18;

public class Code01_RobotWalk {

	public static int ways1(int N, int start, int aim, int K) {
		if (N < 2 || start < 1 || start > N || aim < 1 || aim > N || K < 1) {
			return -1;
		}
		return process1(start, K, aim, N);
	}

	// 机器人当前来到的位置是cur，
	// 机器人还有rest步需要去走，
	// 最终的目标是aim，
	// 有哪些位置？1~N
	// 返回：机器人从cur出发，走过rest步之后，最终停在aim的方法数，是多少？
	public static int process1(int cur, int rest, int aim, int N) {
		if (rest == 0) { // 如果已经不需要走了，走完了！
			return cur == aim ? 1 : 0;
		}
		// (cur, rest)
		if (cur == 1) { // 1 -> 2
			return process1(2, rest - 1, aim, N);
		}
		// (cur, rest)
		if (cur == N) { // N-1 <- N
			return process1(N - 1, rest - 1, aim, N);
		}
		// (cur, rest)
		return process1(cur - 1, rest - 1, aim, N) + process1(cur + 1, rest - 1, aim, N);
	}

	public static int ways2(int N, int start, int aim, int K) {
		if (N < 2 || start < 1 || start > N || aim < 1 || aim > N || K < 1) {
			return -1;
		}
		int[][] dp = new int[N + 1][K + 1];
		for (int i = 0; i <= N; i++) {
			for (int j = 0; j <= K; j++) {
				dp[i][j] = -1;
			}
		}
		// dp就是缓存表
		// dp[cur][rest] == -1 -> process1(cur, rest)之前没算过！
		// dp[cur][rest] != -1 -> process1(cur, rest)之前算过！返回值，dp[cur][rest]
		// N+1 * K+1
		return process2(start, K, aim, N, dp);
	}

	// cur 范: 1 ~ N
	// rest 范：0 ~ K
	public static int process2(int cur, int rest, int aim, int N, int[][] dp) {
		if (dp[cur][rest] != -1) {
			return dp[cur][rest];
		}
		// 之前没算过！
		int ans = 0;
		if (rest == 0) {
			ans = cur == aim ? 1 : 0;
		} else if (cur == 1) {
			ans = process2(2, rest - 1, aim, N, dp);
		} else if (cur == N) {
			ans = process2(N - 1, rest - 1, aim, N, dp);
		} else {
			ans = process2(cur - 1, rest - 1, aim, N, dp) + process2(cur + 1, rest - 1, aim, N, dp);
		}
		dp[cur][rest] = ans;
		return ans;

	}

	public static int ways3(int N, int start, int aim, int K) {
		if (N < 2 || start < 1 || start > N || aim < 1 || aim > N || K < 1) {
			return -1;
		}
		int[][] dp = new int[N + 1][K + 1];
		// 剩余零步时只有已经位于 aim 才成功，其他位置保持默认0。
		dp[aim][0] = 1;
		// 当前列只依赖 rest-1 列，因此剩余步数从小到大计算。
		for (int rest = 1; rest <= K; rest++) {
			// 在左端点只有一步向右的选择，不存在位置0。
			dp[1][rest] = dp[2][rest - 1];
			for (int cur = 2; cur < N; cur++) {
				// 内部第一步向左或向右，两类路径互斥，方法数相加。
				dp[cur][rest] = dp[cur - 1][rest - 1] + dp[cur + 1][rest - 1];
			}
			// 在右端点只能向左，不能访问 N+1。
			dp[N][rest] = dp[N - 1][rest - 1];
		}
		// 查询从指定起点、恰好走指定步数到目标的状态。
		return dp[start][K];
	}

}
```

---

##### 新增对数器（固定输入或固定随机种子）

穷举小棋盘所有合法起终点及1..8步，逐状态对照暴力路径枚举、记忆化和表格法，覆盖端点、奇偶不可达与回到起点。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
    for (int n = 2; n <= 7; n++) {
        for (int start = 1; start <= n; start++) {
            for (int aim = 1; aim <= n; aim++) {
                for (int k = 1; k <= 8; k++) {
                    // 小步数暴力完整枚举路径，与记忆化、填表两版分别对照。
                    int brute = ways1(n, start, aim, k);
                    if (ways2(n, start, aim, k) != brute || ways3(n, start, aim, k) != brute)
                        throw new AssertionError("N=" + n + " start=" + start + " aim=" + aim + " K=" + k);
                }
            }
        }
    }
    System.out.println("PASS: RobotWalk, exhaustive small states");
}
```

#### 题解

**为什么正确**

任意合法路径的第一步必属于规定的一种方向，分支之间第一步不同，互不重复；第一步后剩余问题恰为相邻位置和 rest-1。因此内部状态为 f(cur-1,rest-1)+f(cur+1,rest-1)。所有依赖都在上一列，先算小 rest 就能正确填表。

**复杂度**

暴力时间上界 O(2^K)、栈 O(K)；记忆化和表格版 O(NK) 时间、O(NK) 空间，记忆化另有 O(K) 栈。

**边界与易错点**

课程入口要求 N≥2、K≥1，非法参数返回 -1；数学上 K=0 可定义，但原入口没有支持。不能在还有剩余步数时因已经到达 aim 就立即计数。结果用 int，需限制方案数不溢出。

### 18.2 纸牌博弈的获胜分数

#### 题目

两名玩家轮流从数组两端拿牌且都绝顶聪明，返回最终获胜者的分数。

**输入、输出与约束**

输入非负牌面数组，两名玩家每次从两端拿一张且都最优；返回胜者最终分数。

**函数签名（课程入口）**

```java
public static int win1(int[] arr);
public static int f1(int[] arr, int L, int R);
public static int g1(int[] arr, int L, int R);
public static int win2(int[] arr);
public static int f2(int[] arr, int L, int R, int[][] fmap, int[][] gmap);
public static int g2(int[] arr, int L, int R, int[][] fmap, int[][] gmap);
```

**示例**

```text
输入：arr=[1,100,2]
输出：100
```

解释：先手只能拿1或2，后手随后拿100而获胜。

**出处与版本差异**

- [课程源码：class18/Code02_CardsInLine.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class18/Code02_CardsInLine.java)。
- [LeetCode 对应题 486. 预测赢家（Predict the Winner）](https://leetcode.com/problems/predict-the-winner/)

#### 思路

用 f(L,R) 表示轮到我先拿时能取得的最佳分数，g(L,R) 表示轮到对手先拿时我最终能得到的分数。先手可拿左或右，取 max(arr[L]+g(L+1,R),arr[R]+g(L,R-1))；后手只能接受对手留下的较差局面，取 min(f(L+1,R),f(L,R-1))。

#### 代码答案

```java
package class18;

public class Code02_CardsInLine {

	// 根据规则，返回获胜者的分数
	public static int win1(int[] arr) {
		if (arr == null || arr.length == 0) {
			return 0;
		}
		int first = f1(arr, 0, arr.length - 1);
		int second = g1(arr, 0, arr.length - 1);
		// 输出获胜者分数，不是先手胜负布尔值。
		return Math.max(first, second);
	}

	// arr[L..R]，先手获得的最好分数返回
	public static int f1(int[] arr, int L, int R) {
		if (L == R) {
			return arr[L];
		}
		// 当前先手拿走左端后，在剩余区间轮到自己做后手。
		int p1 = arr[L] + g1(arr, L + 1, R);
		// 拿右端是另一个互斥选择，对应剩余区间[L,R-1]。
		int p2 = arr[R] + g1(arr, L, R - 1);
		return Math.max(p1, p2);
	}

	// // arr[L..R]，后手获得的最好分数返回
	public static int g1(int[] arr, int L, int R) {
		if (L == R) {
			return 0;
		}
		int p1 = f1(arr, L + 1, R); // 对手拿走了L位置的数
		int p2 = f1(arr, L, R - 1); // 对手拿走了R位置的数
		// 自己作为后手不能选对手拿哪边；对手最优会留下使自己得分更小的局面。
		return Math.min(p1, p2);
	}

	public static int win2(int[] arr) {
		if (arr == null || arr.length == 0) {
			return 0;
		}
		int N = arr.length;
		int[][] fmap = new int[N][N];
		int[][] gmap = new int[N][N];
		for (int i = 0; i < N; i++) {
			for (int j = 0; j < N; j++) {
				// 记忆化用-1标记未计算，避免和合法零分混淆。
				fmap[i][j] = -1;
				gmap[i][j] = -1;
			}
		}
		int first = f2(arr, 0, arr.length - 1, fmap, gmap);
		int second = g2(arr, 0, arr.length - 1, fmap, gmap);
		// 输出获胜者分数，不是先手胜负布尔值。
		return Math.max(first, second);
	}

	// arr[L..R]，先手获得的最好分数返回
	public static int f2(int[] arr, int L, int R, int[][] fmap, int[][] gmap) {
		// 同一区间的先手最优得分已经求过，直接复用。
		if (fmap[L][R] != -1) {
			return fmap[L][R];
		}
		int ans = 0;
		if (L == R) {
			ans = arr[L];
		} else {
			int p1 = arr[L] + g2(arr, L + 1, R, fmap, gmap);
			int p2 = arr[R] + g2(arr, L, R - 1, fmap, gmap);
			ans = Math.max(p1, p2);
		}
		fmap[L][R] = ans;
		return ans;
	}

	// // arr[L..R]，后手获得的最好分数返回
	public static int g2(int[] arr, int L, int R, int[][] fmap, int[][] gmap) {
		// 后手状态单独缓存，不能混用先手表。
		if (gmap[L][R] != -1) {
			return gmap[L][R];
		}
		int ans = 0;
		if (L != R) {
			int p1 = f2(arr, L + 1, R, fmap, gmap); // 对手拿走了L位置的数
			int p2 = f2(arr, L, R - 1, fmap, gmap); // 对手拿走了R位置的数
			ans = Math.min(p1, p2);
		}
		gmap[L][R] = ans;
		return ans;
	}

	public static int win3(int[] arr) {
		if (arr == null || arr.length == 0) {
			return 0;
		}
		int N = arr.length;
		int[][] fmap = new int[N][N];
		int[][] gmap = new int[N][N];
		for (int i = 0; i < N; i++) {
			// 单张牌归先手所有，后手对应表格默认0。
			fmap[i][i] = arr[i];
		}
		for (int startCol = 1; startCol < N; startCol++) {
			int L = 0;
			int R = startCol;
			while (R < N) {
				// 先手可以主动选择拿左或拿右，取较大得分。
				fmap[L][R] = Math.max(arr[L] + gmap[L + 1][R], arr[R] + gmap[L][R - 1]);
				// 后手面对对手的最佳选择，取两种剩余先手得分的较小值。
				gmap[L][R] = Math.min(fmap[L + 1][R], fmap[L][R - 1]);
				L++;
				R++;
			}
		}
		return Math.max(fmap[0][N - 1], gmap[0][N - 1]);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int[] arr = { 5, 7, 4, 5, 8, 1, 6, 0, 3, 4, 6, 1, 7 };
		System.out.println(win1(arr));
		System.out.println(win2(arr));
		System.out.println(win3(arr));

	}
```

#### 题解

**为什么正确**

每轮只有左右两个合法选择，拿牌后先后手互换。总分固定，对手最大化自己得分等价于最小化我能拿到的得分，因此后手转移使用 min。单张牌先手拿走、后手得零作为基例。

**复杂度**

暴力指数时间；记忆化与区间表格均 O(N²) 时间、O(N²) 空间。

**边界与易错点**

后手使用 min 而不是 max。课程返回胜者分数 max(f,g)，LeetCode 486 问的是先手是否不输，需要额外比较 f≥g。


<a id="course-19"></a>

## 第 19 课：从暴力递归到动态规划（二）

### 19.1 0-1 背包最大价值

#### 题目

给定每件物品的重量和价值以及背包容量，每件物品至多选择一次，返回不超重时可获得的最大价值。

**输入、输出与约束**

w、v 等长，重量为正、价值非负，bag≥0；每件至多选一次，返回最大价值。

**函数签名（课程入口）**

```java
public static int maxValue(int[] w, int[] v, int bag);
public static int dp(int[] w, int[] v, int bag);
```

**示例**

```text
输入：w=[2,3,4],v=[4,5,7],bag=5
输出：9
```

解释：选择重量2和3，价值4+5=9，优于只选重量4。

**出处与版本差异**

- [课程源码：class19/Code01_Knapsack.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class19/Code01_Knapsack.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

1. 定义 `process(index, rest)`：只考虑 `index..N-1` 的物品，剩余容量为 `rest` 时，最多还能得到多少价值。已经选过的价值不放进状态，而由调用者累加。
2. 当前物品只有“不选”和“选一次”两种选择。不选进入 `(index+1, rest)`；选则进入 `(index+1, rest-w[index])`，只有后续状态可行，才加上 `v[index]`。两分支都推进下标，所以同一件不会重复使用。
3. 剩余容量为负返回 `-1` 表示不可行；所有物品处理完且未超重返回 0。不能把不可行分支也当 0，否则超重方案会携带当前价值参与最大化。
4. 表格 `dp[index][rest]` 沿用相同含义；终止行全为 0，按下标从大到小填，每格从下一行转移。下方代码同时保留递归和二维表，便于逐项对应。

#### 代码答案

```java
package class19;

public class Code01_Knapsack {

	// 所有的货，重量和价值，都在w和v数组里
	// 为了方便，其中没有负数
	// bag背包容量，不能超过这个载重
	// 返回：不超重的情况下，能够得到的最大价值
	public static int maxValue(int[] w, int[] v, int bag) {
		if (w == null || v == null || w.length != v.length || w.length == 0) {
			return 0;
		}
		// 尝试函数！
		return process(w, v, 0, bag);
	}

	// index 0~N
	// rest 负~bag
	public static int process(int[] w, int[] v, int index, int rest) {
		// 容量为负表示当前选择非法，返回哨兵而不是可用价值。
		if (rest < 0) {
			return -1;
		}
		if (index == w.length) {
			return 0;
		}
		// 不选当前物品，剩余容量保持不变。
		int p1 = process(w, v, index + 1, rest);
		int p2 = 0;
		int next = process(w, v, index + 1, rest - w[index]);
		// 只有选择当前物品后仍合法，才可把当前价值加入子问题结果。
		if (next != -1) {
			p2 = v[index] + next;
		}
		return Math.max(p1, p2);
	}

	public static int dp(int[] w, int[] v, int bag) {
		if (w == null || v == null || w.length != v.length || w.length == 0) {
			return 0;
		}
		int N = w.length;
		int[][] dp = new int[N + 1][bag + 1];
		for (int index = N - 1; index >= 0; index--) {
			for (int rest = 0; rest <= bag; rest++) {
				int p1 = dp[index + 1][rest];
				int p2 = 0;
				int next = rest - w[index] < 0 ? -1 : dp[index + 1][rest - w[index]];
				// 只有选择当前物品后仍合法，才可把当前价值加入子问题结果。
				if (next != -1) {
					p2 = v[index] + next;
				}
				// 选与不选覆盖所有方案，保留价值较大者。
				dp[index][rest] = Math.max(p1, p2);
			}
		}
		return dp[0][bag];
	}

}
```

---

##### 新增对数器（固定输入或固定随机种子）

独立枚举物品下标子集作为基准，随机覆盖容量0、物品装不下、零价值和多种等价最优选择。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
    java.util.Random random = new java.util.Random(20260905L);
    for (int round = 0; round < 2000; round++) {
        int n = 1 + random.nextInt(10), bag = random.nextInt(25);
        int[] w = new int[n], v = new int[n];
        for (int i = 0; i < n; i++) { w[i] = 1 + random.nextInt(10); v[i] = random.nextInt(20); }
        // 按位枚举下标集合，基准不复用被测递归状态或转移。
        int expected = 0;
        for (int mask = 0; mask < (1 << n); mask++) {
            int weight = 0, value = 0;
            for (int i = 0; i < n; i++) if ((mask & (1 << i)) != 0) { weight += w[i]; value += v[i]; }
            if (weight <= bag) expected = Math.max(expected, value);
        }
        if (maxValue(w, v, bag) != expected || dp(w, v, bag) != expected)
            throw new AssertionError("w=" + java.util.Arrays.toString(w) + " v=" + java.util.Arrays.toString(v) + " bag=" + bag);
    }
    System.out.println("PASS: Knapsack, seed=20260905");
}
```

#### 题解

**为什么正确**

任意合法选择集合对当前物品只有选或不选两种情况，互斥且完备。选了它就扣除重量并禁止再次处理该物品；两种分支各取剩余最优值，再择优即为原状态最优。

**复杂度**

暴力 O(2^N)；二维表 O(NB) 时间、O(NB) 空间，B 为容量。

**边界与易错点**

rest<0 是非法分支，源码用 -1 标记，不能把非法返回值当成可加的正常收益。二维表依赖下一行，index 必须逆序；一维改写需容量逆序以防重复选物品。

### 19.2 数字字符串转字母的方法数

#### 题目

数字字符 `1` 到 `26` 可分别映射为字母，给定只含数字的字符串，返回合法解码方法数。

**输入、输出与约束**

输入仅含数字的字符串；1..26 对应字母，返回全部合法完整划分数。

**函数签名（课程入口）**

```java
public static int number(String str);
public static int dp1(String s);
public static int dp2(String s);
```

**示例**

```text
输入：str="111"
输出：3
```

解释：划分为 1|1|1、11|1、1|11。

**出处与版本差异**

- [课程源码：class19/Code02_ConvertToLetterString.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class19/Code02_ConvertToLetterString.java)。
- [LeetCode 原题 91. 解码方法（Decode Ways）](https://leetcode.com/problems/decode-ways/)

#### 思路

f(i) 表示从字符 i 到末尾的解码数。当前为0时不能独立解码；否则可取一位，再看相邻两位是否在10..26，合法时再加上取两位的方案。到字符串末尾返回1，表示前面的划分已经完整成功。

#### 代码答案

```java
package class19;

public class Code02_ConvertToLetterString {

	// str只含有数字字符0~9
	// 返回多少种转化方案
	public static int number(String str) {
		if (str == null || str.length() == 0) {
			return 0;
		}
		return process(str.toCharArray(), 0);
	}

	// str[0..i-1]转化无需过问
	// str[i.....]去转化，返回有多少种转化方法
	public static int process(char[] str, int i) {
		if (i == str.length) {
			return 1;
		}
		// i没到最后，说明有字符
		if (str[i] == '0') { // 之前的决定有问题
			return 0;
		}
		// str[i] != '0'
		// 可能性一，i单转
		int ways = process(str, i + 1);
		if (i + 1 < str.length && (str[i] - '0') * 10 + str[i + 1] - '0' < 27) {
			ways += process(str, i + 2);
		}
		return ways;
	}

	// 从右往左的动态规划
	// 就是上面方法的动态规划版本
	// dp[i]表示：str[i...]有多少种转化方式
	public static int dp1(String s) {
		if (s == null || s.length() == 0) {
			return 0;
		}
		char[] str = s.toCharArray();
		int N = str.length;
		int[] dp = new int[N + 1];
		// 已经消费完整字符串时有一种完成方案，供末尾状态累加。
		dp[N] = 1;
		for (int i = N - 1; i >= 0; i--) {
			if (str[i] != '0') {
				int ways = dp[i + 1];
				if (i + 1 < str.length && (str[i] - '0') * 10 + str[i + 1] - '0' < 27) {
					// 两位编码合法时，追加消费两位后的全部后缀方案。
					ways += dp[i + 2];
				}
				dp[i] = ways;
			}
		}
		return dp[0];
	}

	// 从左往右的动态规划
	// dp[i]表示：str[0...i]有多少种转化方式
	public static int dp2(String s) {
		if (s == null || s.length() == 0) {
			return 0;
		}
		char[] str = s.toCharArray();
		int N = str.length;
		if (str[0] == '0') {
			return 0;
		}
		int[] dp = new int[N];
		dp[0] = 1;
		for (int i = 1; i < N; i++) {
			if (str[i] == '0') {
				// 如果此时str[i]=='0'，那么他是一定要拉前一个字符(i-1的字符)一起拼的，
				// 那么就要求前一个字符，不能也是‘0’，否则拼不了。
				// 前一个字符不是‘0’就够了嘛？不够，还得要求拼完了要么是10，要么是20，如果更大的话，拼不了。
				// 这就够了嘛？还不够，你们拼完了，还得要求str[0...i-2]真的可以被分解！
				// 如果str[0...i-2]都不存在分解方案，那i和i-1拼成了也不行，因为之前的搞定不了。
				if (str[i - 1] == '0' || str[i - 1] > '2' || (i - 2 >= 0 && dp[i - 2] == 0)) {
					return 0;
				} else {
					dp[i] = i - 2 >= 0 ? dp[i - 2] : 1;
				}
			} else {
				dp[i] = dp[i - 1];
				if (str[i - 1] != '0' && (str[i - 1] - '0') * 10 + str[i] - '0' <= 26) {
					dp[i] += i - 2 >= 0 ? dp[i - 2] : 1;
				}
			}
		}
		return dp[N - 1];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	public static String randomString(int len) {
		char[] str = new char[len];
		for (int i = 0; i < len; i++) {
			str[i] = (char) ((int) (Math.random() * 10) + '0');
		}
		return String.valueOf(str);
	}

	// 为了测试
	public static void main(String[] args) {
		int N = 30;
		int testTime = 1000000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int len = (int) (Math.random() * N);
			String s = randomString(len);
			int ans0 = number(s);
			int ans1 = dp1(s);
			int ans2 = dp2(s);
			if (ans0 != ans1 || ans0 != ans2) {
				System.out.println(s);
				System.out.println(ans0);
				System.out.println(ans1);
				System.out.println(ans2);
				System.out.println("Oops!");
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

每种合法解码的第一段长度只能为1或2；两类划分的第一段不同，不会重计。末尾的1是空后缀的唯一完成方式，不是额外产生一个字母。

**复杂度**

暴力指数时间，动态规划 O(N) 时间、O(N) 空间，可进一步压为 O(1)。

**边界与易错点**

0 不能单独映射，06 也不是6。空后缀状态为1与公开入口对空字符串返回0是不同层面的约定。计数可能超 int。

### 19.3 贴纸拼词的最少张数

#### 题目

给定若干可重复使用的字母贴纸和目标字符串，返回拼出目标所需的最少贴纸数；无法完成时返回 `-1`。

**输入、输出与约束**

贴纸与目标只含小写字母，每种贴纸可重复用；返回最少张数或-1。

**函数签名（课程入口）**

```java
public static int minStickers1(String[] stickers, String target);
public static String minus(String s1, String s2);
public static int minStickers2(String[] stickers, String target);
public static int minStickers3(String[] stickers, String target);
```

**示例**

```text
输入：stickers=["ab","bc"], target="aabc"
输出：3
```

解释：两张ab提供两个a，再用一张bc补c。

**出处与版本差异**

- [课程源码：class19/Code03_StickersToSpellWord.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class19/Code03_StickersToSpellWord.java)。
- [LeetCode 原题 691. 贴纸拼词（Stickers to Spell Word）](https://leetcode.com/problems/stickers-to-spell-word/)

#### 思路

贴纸可以剪开重排，所以状态只需记录目标各字母剩余次数。每次选择一张贴纸，逐字母扣减并截到0，将剩余字符按字母顺序组成规范键。记忆化保存该键的最少贴纸数，并只尝试包含当前剩余首字母的贴纸。

#### 代码答案

```java
package class19;

import java.util.HashMap;

// 本题测试链接：https://leetcode.com/problems/stickers-to-spell-word
public class Code03_StickersToSpellWord {

	public static int minStickers1(String[] stickers, String target) {
		int ans = process1(stickers, target);
		return ans == Integer.MAX_VALUE ? -1 : ans;
	}

	// 所有贴纸stickers，每一种贴纸都有无穷张
	// target
	// 最少张数
	public static int process1(String[] stickers, String target) {
		if (target.length() == 0) {
			return 0;
		}
		int min = Integer.MAX_VALUE;
		for (String first : stickers) {
			String rest = minus(target, first);
			if (rest.length() != target.length()) {
				min = Math.min(min, process1(stickers, rest));
			}
		}
		return min + (min == Integer.MAX_VALUE ? 0 : 1);
	}

	public static String minus(String s1, String s2) {
		char[] str1 = s1.toCharArray();
		char[] str2 = s2.toCharArray();
		int[] count = new int[26];
		for (char cha : str1) {
			count[cha - 'a']++;
		}
		for (char cha : str2) {
			count[cha - 'a']--;
		}
		StringBuilder builder = new StringBuilder();
		for (int i = 0; i < 26; i++) {
			if (count[i] > 0) {
				for (int j = 0; j < count[i]; j++) {
					builder.append((char) (i + 'a'));
				}
			}
		}
		return builder.toString();
	}

	public static int minStickers2(String[] stickers, String target) {
		int N = stickers.length;
		// 关键优化(用词频表替代贴纸数组)
		int[][] counts = new int[N][26];
		for (int i = 0; i < N; i++) {
			char[] str = stickers[i].toCharArray();
			for (char cha : str) {
				counts[i][cha - 'a']++;
			}
		}
		int ans = process2(counts, target);
		return ans == Integer.MAX_VALUE ? -1 : ans;
	}

	// stickers[i] 数组，当初i号贴纸的字符统计 int[][] stickers -> 所有的贴纸
	// 每一种贴纸都有无穷张
	// 返回搞定target的最少张数
	// 最少张数
	public static int process2(int[][] stickers, String t) {
		if (t.length() == 0) {
			return 0;
		}
		// target做出词频统计
		// target  aabbc  2 2 1..
		//                0 1 2..
		char[] target = t.toCharArray();
		int[] tcounts = new int[26];
		for (char cha : target) {
			tcounts[cha - 'a']++;
		}
		int N = stickers.length;
		int min = Integer.MAX_VALUE;
		for (int i = 0; i < N; i++) {
			// 尝试第一张贴纸是谁
			int[] sticker = stickers[i];
			// 最关键的优化(重要的剪枝!这一步也是贪心!)
			// 必须覆盖一个当前需要的首字符，保证推进且不损失最优方案。
			if (sticker[target[0] - 'a'] > 0) {
				StringBuilder builder = new StringBuilder();
				for (int j = 0; j < 26; j++) {
					if (tcounts[j] > 0) {
						int nums = tcounts[j] - sticker[j];
						for (int k = 0; k < nums; k++) {
							builder.append((char) (j + 'a'));
						}
					}
				}
				String rest = builder.toString();
				min = Math.min(min, process2(stickers, rest));
			}
		}
		return min + (min == Integer.MAX_VALUE ? 0 : 1);
	}

	public static int minStickers3(String[] stickers, String target) {
		int N = stickers.length;
		int[][] counts = new int[N][26];
		for (int i = 0; i < N; i++) {
			char[] str = stickers[i].toCharArray();
			for (char cha : str) {
				counts[i][cha - 'a']++;
			}
		}
		HashMap<String, Integer> dp = new HashMap<>();
		// 空目标不需要贴纸，这是记忆化递归的终点。
		dp.put("", 0);
		int ans = process3(counts, target, dp);
		return ans == Integer.MAX_VALUE ? -1 : ans;
	}

	public static int process3(int[][] stickers, String t, HashMap<String, Integer> dp) {
		if (dp.containsKey(t)) {
			return dp.get(t);
		}
		char[] target = t.toCharArray();
		int[] tcounts = new int[26];
		for (char cha : target) {
			tcounts[cha - 'a']++;
		}
		int N = stickers.length;
		int min = Integer.MAX_VALUE;
		for (int i = 0; i < N; i++) {
			int[] sticker = stickers[i];
			// 必须覆盖一个当前需要的首字符，保证推进且不损失最优方案。
			if (sticker[target[0] - 'a'] > 0) {
				StringBuilder builder = new StringBuilder();
				for (int j = 0; j < 26; j++) {
					if (tcounts[j] > 0) {
						int nums = tcounts[j] - sticker[j];
						for (int k = 0; k < nums; k++) {
							builder.append((char) (j + 'a'));
						}
					}
				}
				String rest = builder.toString();
				min = Math.min(min, process3(stickers, rest, dp));
			}
		}
		int ans = min + (min == Integer.MAX_VALUE ? 0 : 1);
		dp.put(t, ans);
		return ans;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：每张贴纸只能扣除它实际拥有的字符次数，多余字符丢弃。选择后剩余状态必须缩小，否则递归会原地循环；无解返回-1而非0。

本条未附独立随机对数器。

#### 题解

**为什么正确**

贴纸使用顺序不影响最终字母多重集合，剩余计数相同的状态等价。任一可行方案都必须有一张贴纸覆盖目标首字母，把它提到第一步不会影响可行性，因此首字母剪枝不会漏最优解。

**复杂度**

设目标各字符次数为 c_j，可达计数状态数 S≤∏(c_j+1)；记忆化约 O(S·M·(26+L)) 时间，M 为贴纸数、L 为目标长，键与缓存空间 O(SL)。

**边界与易错点**

每张贴纸只能扣除它实际拥有的字符次数，多余字符丢弃。选择后剩余状态必须缩小，否则递归会原地循环；无解返回-1而非0。

### 19.4 最长公共子序列

#### 题目

给定两个字符串，返回二者最长公共子序列的长度；子序列不要求字符连续。

**输入、输出与约束**

输入两个字符串，返回最长公共子序列长度，任一为空返回0。

**函数签名（课程入口）**

```java
public static int longestCommonSubsequence1(String s1, String s2);
public static int longestCommonSubsequence2(String s1, String s2);
```

**示例**

```text
输入：text1="abcde", text2="ace"
输出：3
```

解释：公共子序列为ace，允许跳过b与d。

**出处与版本差异**

- [课程源码：class19/Code04_LongestCommonSubsequence.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class19/Code04_LongestCommonSubsequence.java)。
- [LeetCode 原题 1143. 最长公共子序列（Longest Common Subsequence）](https://leetcode.com/problems/longest-common-subsequence/)

#### 思路

dp[i][j] 表示两个字符串各自前缀 0..i、0..j 的最长公共子序列长度。末字符不等时，至少舍弃一侧末字符，取上方和左方最大值；末字符相等时还可在左上角结果后接这个字符，得到 dp[i-1][j-1]+1。

#### 代码答案

```java
package class19;

// 这个问题leetcode上可以直接测
// 链接：https://leetcode.com/problems/longest-common-subsequence/
public class Code04_LongestCommonSubsequence {

	public static int longestCommonSubsequence1(String s1, String s2) {
		if (s1 == null || s2 == null || s1.length() == 0 || s2.length() == 0) {
			return 0;
		}
		char[] str1 = s1.toCharArray();
		char[] str2 = s2.toCharArray();
		// 尝试
		return process1(str1, str2, str1.length - 1, str2.length - 1);
	}

	// str1[0...i]和str2[0...j]，这个范围上最长公共子序列长度是多少？
	// 可能性分类:
	// a) 最长公共子序列，一定不以str1[i]字符结尾、也一定不以str2[j]字符结尾
	// b) 最长公共子序列，可能以str1[i]字符结尾、但是一定不以str2[j]字符结尾
	// c) 最长公共子序列，一定不以str1[i]字符结尾、但是可能以str2[j]字符结尾
	// d) 最长公共子序列，必须以str1[i]字符结尾、也必须以str2[j]字符结尾
	// 注意：a)、b)、c)、d)并不是完全互斥的，他们可能会有重叠的情况
	// 但是可以肯定，答案不会超过这四种可能性的范围
	// 那么我们分别来看一下，这几种可能性怎么调用后续的递归。
	// a) 最长公共子序列，一定不以str1[i]字符结尾、也一定不以str2[j]字符结尾
	//    如果是这种情况，那么有没有str1[i]和str2[j]就根本不重要了，因为这两个字符一定没用啊
	//    所以砍掉这两个字符，最长公共子序列 = str1[0...i-1]与str2[0...j-1]的最长公共子序列长度(后续递归)
	// b) 最长公共子序列，可能以str1[i]字符结尾、但是一定不以str2[j]字符结尾
	//    如果是这种情况，那么我们可以确定str2[j]一定没有用，要砍掉；但是str1[i]可能有用，所以要保留
	//    所以，最长公共子序列 = str1[0...i]与str2[0...j-1]的最长公共子序列长度(后续递归)
	// c) 最长公共子序列，一定不以str1[i]字符结尾、但是可能以str2[j]字符结尾
	//    跟上面分析过程类似，最长公共子序列 = str1[0...i-1]与str2[0...j]的最长公共子序列长度(后续递归)
	// d) 最长公共子序列，必须以str1[i]字符结尾、也必须以str2[j]字符结尾
	//    同时可以看到，可能性d)存在的条件，一定是在str1[i] == str2[j]的情况下，才成立的
    //    所以，最长公共子序列总长度 = str1[0...i-1]与str2[0...j-1]的最长公共子序列长度(后续递归) + 1(共同的结尾)
	// 综上，四种情况已经穷尽了所有可能性。四种情况中取最大即可
	// 其中b)、c)一定参与最大值的比较，
	// 当str1[i] == str2[j]时，a)一定比d)小，所以d)参与
	// 当str1[i] != str2[j]时，d)压根不存在，所以a)参与
	// 但是再次注意了！
	// a)是：str1[0...i-1]与str2[0...j-1]的最长公共子序列长度
	// b)是：str1[0...i]与str2[0...j-1]的最长公共子序列长度
	// c)是：str1[0...i-1]与str2[0...j]的最长公共子序列长度
	// a)中str1的范围 < b)中str1的范围，a)中str2的范围 == b)中str2的范围
	// 所以a)不用求也知道，它比不过b)啊，因为有一个样本的范围比b)小啊！
	// a)中str1的范围 == c)中str1的范围，a)中str2的范围 < c)中str2的范围
	// 所以a)不用求也知道，它比不过c)啊，因为有一个样本的范围比c)小啊！
	// 至此，可以知道，a)就是个垃圾，有它没它，都不影响最大值的决策
	// 所以，当str1[i] == str2[j]时，b)、c)、d)中选出最大值
	// 当str1[i] != str2[j]时，b)、c)中选出最大值
	public static int process1(char[] str1, char[] str2, int i, int j) {
		if (i == 0 && j == 0) {
			// str1[0..0]和str2[0..0]，都只剩一个字符了
			// 那如果字符相等，公共子序列长度就是1，不相等就是0
			// 这显而易见
			return str1[i] == str2[j] ? 1 : 0;
		} else if (i == 0) {
			// 这里的情况为：
			// str1[0...0]和str2[0...j]，str1只剩1个字符了，但是str2不只一个字符
			// 因为str1只剩一个字符了，所以str1[0...0]和str2[0...j]公共子序列最多长度为1
			// 如果str1[0] == str2[j]，那么此时相等已经找到了！公共子序列长度就是1，也不可能更大了
			// 如果str1[0] != str2[j]，只是此时不相等而已，
			// 那么str2[0...j-1]上有没有字符等于str1[0]呢？不知道，所以递归继续找
			if (str1[i] == str2[j]) {
				return 1;
			} else {
				return process1(str1, str2, i, j - 1);
			}
		} else if (j == 0) {
			// 和上面的else if同理
			// str1[0...i]和str2[0...0]，str2只剩1个字符了，但是str1不只一个字符
			// 因为str2只剩一个字符了，所以str1[0...i]和str2[0...0]公共子序列最多长度为1
			// 如果str1[i] == str2[0]，那么此时相等已经找到了！公共子序列长度就是1，也不可能更大了
			// 如果str1[i] != str2[0]，只是此时不相等而已，
			// 那么str1[0...i-1]上有没有字符等于str2[0]呢？不知道，所以递归继续找
			if (str1[i] == str2[j]) {
				return 1;
			} else {
				return process1(str1, str2, i - 1, j);
			}
		} else { // i != 0 && j != 0
			// 这里的情况为：
			// str1[0...i]和str2[0...i]，str1和str2都不只一个字符
			// 看函数开始之前的注释部分
			// p1就是可能性c)
			int p1 = process1(str1, str2, i - 1, j);
			// p2就是可能性b)
			int p2 = process1(str1, str2, i, j - 1);
			// p3就是可能性d)，如果可能性d)存在，即str1[i] == str2[j]，那么p3就求出来，参与pk
			// 如果可能性d)不存在，即str1[i] != str2[j]，那么让p3等于0，然后去参与pk，反正不影响
			int p3 = str1[i] == str2[j] ? (1 + process1(str1, str2, i - 1, j - 1)) : 0;
			return Math.max(p1, Math.max(p2, p3));
		}
	}

	public static int longestCommonSubsequence2(String s1, String s2) {
		if (s1 == null || s2 == null || s1.length() == 0 || s2.length() == 0) {
			return 0;
		}
		char[] str1 = s1.toCharArray();
		char[] str2 = s2.toCharArray();
		int N = str1.length;
		int M = str2.length;
		int[][] dp = new int[N][M];
		dp[0][0] = str1[0] == str2[0] ? 1 : 0;
		for (int j = 1; j < M; j++) {
			dp[0][j] = str1[0] == str2[j] ? 1 : dp[0][j - 1];
		}
		for (int i = 1; i < N; i++) {
			dp[i][0] = str1[i] == str2[0] ? 1 : dp[i - 1][0];
		}
		for (int i = 1; i < N; i++) {
			for (int j = 1; j < M; j++) {
				int p1 = dp[i - 1][j];
				int p2 = dp[i][j - 1];
				// 末字符相等时，把它接在左上角公共子序列后面。
				int p3 = str1[i] == str2[j] ? (1 + dp[i - 1][j - 1]) : 0;
				dp[i][j] = Math.max(p1, Math.max(p2, p3));
			}
		}
		return dp[N - 1][M - 1];
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：子序列不要求连续，失配时不能把状态清零，那是最长公共子串的做法。首行首列要传播已经找到的匹配，不能只比较当前位置。

本条未附独立随机对数器。

#### 题解

**为什么正确**

一份公共子序列若不同时使用两末字符，就属于删除一侧末字符后的某个子问题；若同时使用，则两末字符必相等，删除这最后一个匹配后留下公共前缀子问题。分类覆盖所有答案。

**复杂度**

表格时间 O(NM)，空间 O(NM)；朴素递归有大量重复状态，指数时间。

**边界与易错点**

子序列不要求连续，失配时不能把状态清零，那是最长公共子串的做法。首行首列要传播已经找到的匹配，不能只比较当前位置。


<a id="course-20"></a>

## 第 20 课：从暴力递归到动态规划（三）

### 20.1 最长回文子序列

#### 题目

给定字符串，返回其中最长回文子序列的长度。

**输入、输出与约束**

输入字符串，返回最长回文子序列长度，空串为0。

**函数签名（课程入口）**

```java
public static int lpsl1(String s);
public static int lpsl2(String s);
public static int longestPalindromeSubseq1(String s);
public static int longestPalindromeSubseq2(String s);
```

**示例**

```text
输入：s="bbbab"
输出：4
```

解释：可选bbbb，跳过中间的a；最长连续回文却没有长度4。

**出处与版本差异**

- [课程源码：class20/Code01_PalindromeSubsequence.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class20/Code01_PalindromeSubsequence.java)。
- [LeetCode 原题 516. 最长回文子序列（Longest Palindromic Subsequence）](https://leetcode.com/problems/longest-palindromic-subsequence/)

#### 思路

1. 子序列允许跳过中间字符，因此不能套最长回文子串的中心扩展。定义 `f(L,R)` 为闭区间 `s[L..R]` 内最长回文子序列长度。
2. 一个字符时长度为 1；两个字符相同则为 2，不同则为 1。更大区间分别考虑舍弃左端、舍弃右端；当两端字符相等，还能把它们放在内层回文两侧，候选为 `2+f(L+1,R-1)`。
3. 为什么两端不同不能一起选？回文的首尾字符必须相同，所以至少舍弃一端。两端相同时，包裹内层回文给出合法候选，再与舍弃端点的结果取最大。
4. 动态规划表 `dp[L][R]` 就是该区间答案。依赖下方、左方和左下方的更短区间，因此 L 从大到小、R 从 L 右侧向右填，答案在 `dp[0][N-1]`。

#### 代码答案

```java
package class20;

// 测试链接：https://leetcode.com/problems/longest-palindromic-subsequence/
public class Code01_PalindromeSubsequence {

	public static int lpsl1(String s) {
		if (s == null || s.length() == 0) {
			return 0;
		}
		char[] str = s.toCharArray();
		return f(str, 0, str.length - 1);
	}

	// str[L..R]最长回文子序列长度返回
	// 闭区间[L,R]内最长回文子序列长度；不要求字符连续。
	public static int f(char[] str, int L, int R) {
		if (L == R) {
			return 1;
		}
		if (L == R - 1) {
			return str[L] == str[R] ? 2 : 1;
		}
		// 同时舍弃两端给出一个合法候选，较大区间还能考虑只舍弃一端。
		int p1 = f(str, L + 1, R - 1);
		// 舍弃右端，在剩余区间继续寻找回文子序列。
		int p2 = f(str, L, R - 1);
		// 舍弃左端，与舍弃右端共同覆盖两端不同的情况。
		int p3 = f(str, L + 1, R);
		int p4 = str[L] != str[R] ? 0 : (2 + f(str, L + 1, R - 1));
		return Math.max(Math.max(p1, p2), Math.max(p3, p4));
	}

	public static int lpsl2(String s) {
		if (s == null || s.length() == 0) {
			return 0;
		}
		char[] str = s.toCharArray();
		int N = str.length;
		int[][] dp = new int[N][N];
		// 最后一个单字符区间单独初始化，避免后续双字符循环漏掉它。
		dp[N - 1][N - 1] = 1;
		for (int i = 0; i < N - 1; i++) {
			// 每个单字符自身就是长度1的回文。
			dp[i][i] = 1;
			dp[i][i + 1] = str[i] == str[i + 1] ? 2 : 1;
		}
		// 左端点从右向左，保证下一行 L+1 的依赖已经计算。
		// 按左端点递减填表，确保依赖的下一行已经计算。
		for (int L = N - 3; L >= 0; L--) {
			// 当前行按右端点递增，确保更短区间已经就绪。
			for (int R = L + 2; R < N; R++) {
				// 先比较舍弃右端和舍弃左端的最优结果。
				dp[L][R] = Math.max(dp[L][R - 1], dp[L + 1][R]);
				if (str[L] == str[R]) {
					// 当两端相等时，同时选择它们包住中间的最优回文。
					dp[L][R] = Math.max(dp[L][R], 2 + dp[L + 1][R - 1]);
				}
			}
		}
		return dp[0][N - 1];
	}

	public static int longestPalindromeSubseq1(String s) {
		if (s == null || s.length() == 0) {
			return 0;
		}
		if (s.length() == 1) {
			return 1;
		}
		char[] str = s.toCharArray();
		char[] reverse = reverse(str);
		return longestCommonSubsequence(str, reverse);
	}

	public static char[] reverse(char[] str) {
		int N = str.length;
		char[] reverse = new char[str.length];
		for (int i = 0; i < str.length; i++) {
			reverse[--N] = str[i];
		}
		return reverse;
	}

	public static int longestCommonSubsequence(char[] str1, char[] str2) {
		int N = str1.length;
		int M = str2.length;
		int[][] dp = new int[N][M];
		dp[0][0] = str1[0] == str2[0] ? 1 : 0;
		for (int i = 1; i < N; i++) {
			dp[i][0] = str1[i] == str2[0] ? 1 : dp[i - 1][0];
		}
		for (int j = 1; j < M; j++) {
			dp[0][j] = str1[0] == str2[j] ? 1 : dp[0][j - 1];
		}
		for (int i = 1; i < N; i++) {
			for (int j = 1; j < M; j++) {
				dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
				if (str1[i] == str2[j]) {
					dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - 1] + 1);
				}
			}
		}
		return dp[N - 1][M - 1];
	}

	public static int longestPalindromeSubseq2(String s) {
		if (s == null || s.length() == 0) {
			return 0;
		}
		if (s.length() == 1) {
			return 1;
		}
		char[] str = s.toCharArray();
		int N = str.length;
		int[][] dp = new int[N][N];
		// 最后一个单字符区间单独初始化，避免后续双字符循环漏掉它。
		dp[N - 1][N - 1] = 1;
		for (int i = 0; i < N - 1; i++) {
			// 每个单字符自身就是长度1的回文。
			dp[i][i] = 1;
			dp[i][i + 1] = str[i] == str[i + 1] ? 2 : 1;
		}
		for (int i = N - 3; i >= 0; i--) {
			for (int j = i + 2; j < N; j++) {
				dp[i][j] = Math.max(dp[i][j - 1], dp[i + 1][j]);
				if (str[i] == str[j]) {
					dp[i][j] = Math.max(dp[i][j], dp[i + 1][j - 1] + 2);
				}
			}
		}
		return dp[0][N - 1];
	}

}
```

---

##### 新增对数器（固定输入或固定随机种子）

小字母表制造重复字符，枚举全部下标子集并直接验证回文，独立核对区间动态规划。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
    java.util.Random random = new java.util.Random(20260905L);
    for (int round = 0; round < 2000; round++) {
        int n = 1 + random.nextInt(10);
        StringBuilder text = new StringBuilder();
        for (int i = 0; i < n; i++) text.append((char) ('a' + random.nextInt(3)));
        String s = text.toString();
        int expected = 0;
        // 枚举所有子序列，再直接双指针判断回文，不复用区间递推。
        for (int mask = 0; mask < (1 << n); mask++) {
            StringBuilder sub = new StringBuilder();
            for (int i = 0; i < n; i++) if ((mask & (1 << i)) != 0) sub.append(s.charAt(i));
            boolean palindrome = true;
            for (int l = 0, r = sub.length() - 1; l < r; l++, r--)
                if (sub.charAt(l) != sub.charAt(r)) palindrome = false;
            if (palindrome) expected = Math.max(expected, sub.length());
        }
        if (lpsl2(s) != expected) throw new AssertionError("s=" + s + " expected=" + expected);
    }
    System.out.println("PASS: PalindromeSubsequence, seed=20260905");
}
```

#### 题解

**为什么正确**

最优回文若不同时使用两端，必包含于少一端的区间；若同时使用，两端必须相等，去掉它们后中间仍是回文子序列。因此三个候选覆盖最优解且每个候选都可构造合法回文。

**复杂度**

二维区间表和与逆串求公共子序列的实现均 O(N²) 时间、O(N²) 空间；朴素递归指数时间。

**边界与易错点**

不是二分查找，也不是求连续回文子串。dp[L+1][R] 依赖更大的 L，所以源码 L 从右向左；相等两端加2，不是加1。

### 20.2 象棋马跳到目标位置的方法数

#### 题目

在中国象棋棋盘上，马从原点出发恰走 `k` 步，返回到达目标坐标的方法数。

**输入、输出与约束**

马从(0,0)出发，目标位于10×9棋盘；返回恰跳K次的方法数，计数需在int范围内。

**函数签名（课程入口）**

```java
public static int jump(int a, int b, int k);
public static int dp(int a, int b, int k);
public static int pick(int[][][] dp, int x, int y, int rest);
public static int ways(int a, int b, int step);
public static int waysdp(int a, int b, int s);
public static int getValue(int[][][] dp, int i, int j, int step);
```

**示例**

```text
输入：目标(1,2)，K=1
输出：1
```

解释：从(0,0)只需一次合法马步。

**出处与版本差异**

- [课程源码：class20/Code02_HorseJump.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class20/Code02_HorseJump.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

棋盘为 x=0..9、y=0..8。递归 f(x,y,rest) 从当前位置枚举马的八种偏移；越界返回0，剩余零步时只看是否到达目标。表格版把目标位置的零步状态置1，按步数递增汇总八个邻居。

#### 代码答案

```java
package class20;

public class Code02_HorseJump {

	// 当前来到的位置是（x,y）
	// 还剩下rest步需要跳
	// 跳完rest步，正好跳到a，b的方法数是多少？
	// 10 * 9
	public static int jump(int a, int b, int k) {
		return process(0, 0, k, a, b);
	}

	public static int process(int x, int y, int rest, int a, int b) {
		if (x < 0 || x > 9 || y < 0 || y > 8) {
			// 此类辅助访问中的越界或不匹配状态不产生合法路径。
			return 0;
		}
		if (rest == 0) {
			return (x == a && y == b) ? 1 : 0;
		}
		int ways = process(x + 2, y + 1, rest - 1, a, b);
		ways += process(x + 1, y + 2, rest - 1, a, b);
		ways += process(x - 1, y + 2, rest - 1, a, b);
		ways += process(x - 2, y + 1, rest - 1, a, b);
		ways += process(x - 2, y - 1, rest - 1, a, b);
		ways += process(x - 1, y - 2, rest - 1, a, b);
		ways += process(x + 1, y - 2, rest - 1, a, b);
		ways += process(x + 2, y - 1, rest - 1, a, b);
		return ways;
	}

	public static int dp(int a, int b, int k) {
		int[][][] dp = new int[10][9][k + 1];
		// 倒向计数：零步时只有位于目标(a,b)才成功。
		dp[a][b][0] = 1;
		for (int rest = 1; rest <= k; rest++) {
			for (int x = 0; x < 10; x++) {
				for (int y = 0; y < 9; y++) {
					int ways = pick(dp, x + 2, y + 1, rest - 1);
					ways += pick(dp, x + 1, y + 2, rest - 1);
					ways += pick(dp, x - 1, y + 2, rest - 1);
					ways += pick(dp, x - 2, y + 1, rest - 1);
					ways += pick(dp, x - 2, y - 1, rest - 1);
					ways += pick(dp, x - 1, y - 2, rest - 1);
					ways += pick(dp, x + 1, y - 2, rest - 1);
					ways += pick(dp, x + 2, y - 1, rest - 1);
					dp[x][y][rest] = ways;
				}
			}
		}
		return dp[0][0][k];
	}

	public static int pick(int[][][] dp, int x, int y, int rest) {
		if (x < 0 || x > 9 || y < 0 || y > 8) {
			// 此类辅助访问中的越界或不匹配状态不产生合法路径。
			return 0;
		}
		return dp[x][y][rest];
	}

	public static int ways(int a, int b, int step) {
		return f(0, 0, step, a, b);
	}

	public static int f(int i, int j, int step, int a, int b) {
		if (i < 0 || i > 9 || j < 0 || j > 8) {
			// 此类辅助访问中的越界或不匹配状态不产生合法路径。
			return 0;
		}
		if (step == 0) {
			return (i == a && j == b) ? 1 : 0;
		}
		return f(i - 2, j + 1, step - 1, a, b) + f(i - 1, j + 2, step - 1, a, b) + f(i + 1, j + 2, step - 1, a, b)
				+ f(i + 2, j + 1, step - 1, a, b) + f(i + 2, j - 1, step - 1, a, b) + f(i + 1, j - 2, step - 1, a, b)
				+ f(i - 1, j - 2, step - 1, a, b) + f(i - 2, j - 1, step - 1, a, b);

	}

	public static int waysdp(int a, int b, int s) {
		int[][][] dp = new int[10][9][s + 1];
		// 倒向计数：零步时只有位于目标(a,b)才成功。
		dp[a][b][0] = 1;
		for (int step = 1; step <= s; step++) { // 按层来
			for (int i = 0; i < 10; i++) {
				for (int j = 0; j < 9; j++) {
					dp[i][j][step] = getValue(dp, i - 2, j + 1, step - 1) + getValue(dp, i - 1, j + 2, step - 1)
							+ getValue(dp, i + 1, j + 2, step - 1) + getValue(dp, i + 2, j + 1, step - 1)
							+ getValue(dp, i + 2, j - 1, step - 1) + getValue(dp, i + 1, j - 2, step - 1)
							+ getValue(dp, i - 1, j - 2, step - 1) + getValue(dp, i - 2, j - 1, step - 1);
				}
			}
		}
		return dp[0][0][s];
	}

	// 在dp表中，得到dp[i][j][step]的值，但如果(i，j)位置越界的话，返回0；
	public static int getValue(int[][][] dp, int i, int j, int step) {
		if (i < 0 || i > 9 || j < 0 || j > 8) {
			// 此类辅助访问中的越界或不匹配状态不产生合法路径。
			return 0;
		}
		return dp[i][j][step];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int x = 7;
		int y = 7;
		int step = 10;
		System.out.println(ways(x, y, step));
		System.out.println(dp(x, y, step));

		System.out.println(jump(x, y, step));
	}
```

#### 题解

**为什么正确**

每条路径的第一跳唯一属于八个方向之一，后续路径归于对应位置和少一步状态，互斥相加。越界跳法不合法而贡献0，零步基例只承认目标位置。

**复杂度**

暴力 O(8^K)，表格 O(10×9×K×8) 时间、O(10×9×K) 空间。

**边界与易错点**

横纵范围不要写反。本题空棋盘不考虑蹩马腿或其他棋子；恰好K步不是最多K步。

### 20.3 咖啡机排队与杯子清洗的最短时间

#### 题目

多台咖啡机制作一杯咖啡的时间不同；所有人喝完后杯子可机洗或自然挥发，返回全部杯子变干净的最早时间。

**输入、输出与约束**

机器耗时、洗杯耗时、自然干耗时为正，N为人数；喝完视为制作完成即刻，返回全部杯子干净的最早时刻。

**函数签名（课程入口）**

```java
public static int forceMake(int[] arr, int[] times, int kth, int[] drink, int n, int a, int b);
public static int forceWash(int[] drinks, int a, int b, int index, int washLine, int time);
public int compare(Machine o1, Machine o2);
public static int minTime1(int[] arr, int n, int a, int b);
public static int bestTime(int[] drinks, int wash, int air, int index, int free);
public static int minTime2(int[] arr, int n, int a, int b);
```

**示例**

```text
输入：一台机器耗时2，N=2，洗a=1，自然干b=5
输出：5
```

解释：两杯在2、4时刻完成，分别洗到3、5，总完成时刻5。

**出处与版本差异**

- [课程源码：class20/Code03_Coffee.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class20/Code03_Coffee.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先用堆按“机器空闲时刻+单杯耗时”分配每个人的最早咖啡完成时间，得到非递减 drinks。清洁状态 f(index,free) 表示从第index杯开始、洗杯机到free时刻空闲时的全部完成时间。当前杯可在 max(drinks[index],free)+a 洗完，或在 drinks[index]+b 自然干；分别与后续完成时间取max，再在两方案间取min。

#### 代码答案

```java
package class20;

import java.util.Arrays;
import java.util.Comparator;
import java.util.PriorityQueue;

// 题目
// 数组arr代表每一个咖啡机冲一杯咖啡的时间，每个咖啡机只能串行的制造咖啡。
// 现在有n个人需要喝咖啡，只能用咖啡机来制造咖啡。
// 认为每个人喝咖啡的时间非常短，冲好的时间即是喝完的时间。
// 每个人喝完之后咖啡杯可以选择洗或者自然挥发干净，只有一台洗咖啡杯的机器，只能串行的洗咖啡杯。
// 洗杯子的机器洗完一个杯子时间为a，任何一个杯子自然挥发干净的时间为b。
// 四个参数：arr, n, a, b
// 假设时间点从0开始，返回所有人喝完咖啡并洗完咖啡杯的全部过程结束后，至少来到什么时间点。
public class Code03_Coffee {

	// 验证的方法
	// 彻底的暴力
	// 很慢但是绝对正确
	public static int right(int[] arr, int n, int a, int b) {
		int[] times = new int[arr.length];
		int[] drink = new int[n];
		return forceMake(arr, times, 0, drink, n, a, b);
	}

	// 每个人暴力尝试用每一个咖啡机给自己做咖啡
	public static int forceMake(int[] arr, int[] times, int kth, int[] drink, int n, int a, int b) {
		if (kth == n) {
			int[] drinkSorted = Arrays.copyOf(drink, kth);
			Arrays.sort(drinkSorted);
			return forceWash(drinkSorted, a, b, 0, 0, 0);
		}
		int time = Integer.MAX_VALUE;
		for (int i = 0; i < arr.length; i++) {
			int work = arr[i];
			int pre = times[i];
			drink[kth] = pre + work;
			times[i] = pre + work;
			time = Math.min(time, forceMake(arr, times, kth + 1, drink, n, a, b));
			drink[kth] = 0;
			times[i] = pre;
		}
		return time;
	}

	public static int forceWash(int[] drinks, int a, int b, int index, int washLine, int time) {
		if (index == drinks.length) {
			return time;
		}
		// 选择一：当前index号咖啡杯，选择用洗咖啡机刷干净
		int wash = Math.max(drinks[index], washLine) + a;
		int ans1 = forceWash(drinks, a, b, index + 1, wash, Math.max(wash, time));

		// 选择二：当前index号咖啡杯，选择自然挥发
		int dry = drinks[index] + b;
		int ans2 = forceWash(drinks, a, b, index + 1, washLine, Math.max(dry, time));
		return Math.min(ans1, ans2);
	}

	// 以下为贪心+优良暴力
	public static class Machine {
		public int timePoint;
		public int workTime;

		public Machine(int t, int w) {
			timePoint = t;
			workTime = w;
		}
	}

	public static class MachineComparator implements Comparator<Machine> {

		@Override
		public int compare(Machine o1, Machine o2) {
			return (o1.timePoint + o1.workTime) - (o2.timePoint + o2.workTime);
		}

	}

	// 优良一点的暴力尝试的方法
	public static int minTime1(int[] arr, int n, int a, int b) {
		PriorityQueue<Machine> heap = new PriorityQueue<Machine>(new MachineComparator());
		for (int i = 0; i < arr.length; i++) {
			heap.add(new Machine(0, arr[i]));
		}
		int[] drinks = new int[n];
		for (int i = 0; i < n; i++) {
			Machine cur = heap.poll();
			cur.timePoint += cur.workTime;
			drinks[i] = cur.timePoint;
			heap.add(cur);
		}
		return bestTime(drinks, a, b, 0, 0);
	}

	// drinks 所有杯子可以开始洗的时间
	// wash 单杯洗干净的时间（串行）
	// air 挥发干净的时间(并行)
	// free 洗的机器什么时候可用
	// drinks[index.....]都变干净，最早的结束时间（返回）
	public static int bestTime(int[] drinks, int wash, int air, int index, int free) {
		if (index == drinks.length) {
			return 0;
		}
		// index号杯子 决定洗
		// 机器空闲且杯子已喝完才可开洗，起点取两个时刻最大值。
		int selfClean1 = Math.max(drinks[index], free) + wash;
		int restClean1 = bestTime(drinks, wash, air, index + 1, selfClean1);
		// 所有杯都干净要等当前杯与剩余杯中更晚完成的一方。
		int p1 = Math.max(selfClean1, restClean1);

		// index号杯子 决定挥发
		// 自然干从该杯喝完开始，不占用洗杯机。
		int selfClean2 = drinks[index] + air;
		int restClean2 = bestTime(drinks, wash, air, index + 1, free);
		int p2 = Math.max(selfClean2, restClean2);
		return Math.min(p1, p2);
	}

	// 贪心+优良尝试改成动态规划
	public static int minTime2(int[] arr, int n, int a, int b) {
		PriorityQueue<Machine> heap = new PriorityQueue<Machine>(new MachineComparator());
		for (int i = 0; i < arr.length; i++) {
			heap.add(new Machine(0, arr[i]));
		}
		int[] drinks = new int[n];
		for (int i = 0; i < n; i++) {
			Machine cur = heap.poll();
			cur.timePoint += cur.workTime;
			drinks[i] = cur.timePoint;
			heap.add(cur);
		}
		return bestTimeDp(drinks, a, b);
	}

	public static int bestTimeDp(int[] drinks, int wash, int air) {
		int N = drinks.length;
		int maxFree = 0;
		for (int i = 0; i < drinks.length; i++) {
			maxFree = Math.max(maxFree, drinks[i]) + wash;
		}
		int[][] dp = new int[N + 1][maxFree + 1];
		for (int index = N - 1; index >= 0; index--) {
			for (int free = 0; free <= maxFree; free++) {
				// 机器空闲且杯子已喝完才可开洗，起点取两个时刻最大值。
				int selfClean1 = Math.max(drinks[index], free) + wash;
				if (selfClean1 > maxFree) {
					break; // 因为后面的也都不用填了
				}
				// index号杯子 决定洗
				int restClean1 = dp[index + 1][selfClean1];
				// 所有杯都干净要等当前杯与剩余杯中更晚完成的一方。
				int p1 = Math.max(selfClean1, restClean1);
				// index号杯子 决定挥发
				// 自然干从该杯喝完开始，不占用洗杯机。
				int selfClean2 = drinks[index] + air;
				int restClean2 = dp[index + 1][free];
				int p2 = Math.max(selfClean2, restClean2);
				dp[index][free] = Math.min(p1, p2);
			}
		}
		return dp[0][0];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[] randomArray(int len, int max) {
		int[] arr = new int[len];
		for (int i = 0; i < len; i++) {
			arr[i] = (int) (Math.random() * max) + 1;
		}
		return arr;
	}

	// for test
	public static void printArray(int[] arr) {
		System.out.print("arr : ");
		for (int j = 0; j < arr.length; j++) {
			System.out.print(arr[j] + ", ");
		}
		System.out.println();
	}

public static void main(String[] args) {
		int len = 10;
		int max = 10;
		int testTime = 10;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int[] arr = randomArray(len, max);
			int n = (int) (Math.random() * 7) + 1;
			int a = (int) (Math.random() * 7) + 1;
			int b = (int) (Math.random() * 10) + 1;
			int ans1 = right(arr, n, a, b);
			int ans2 = minTime1(arr, n, a, b);
			int ans3 = minTime2(arr, n, a, b);
			if (ans1 != ans2 || ans2 != ans3) {
				printArray(arr);
				System.out.println("n : " + n);
				System.out.println("a : " + a);
				System.out.println("b : " + b);
				System.out.println(ans1 + " , " + ans2 + " , " + ans3);
				System.out.println("===============");
				break;
			}
		}
		System.out.println("测试结束");

	}
```

#### 题解

**为什么正确**

机器的下一次完成事件按最早时间取出，得到逐人最早完成序列。清洁中固定当前选择后，后续仅需知道洗杯机空闲时间；自然干不占机器。总完成时间是当前杯与后续杯完成时刻最大值，因此转移的内层max不可省略。

**复杂度**

制咖啡 O(N log M)，M为机器数。清洁暴力 O(2^N)；时间表版本 O(NT) 时间、O(NT) 空间，T为全部串行洗杯的时间上界，属伪多项式。

**边界与易错点**

洗杯只能串行，自然干可以并行。a≥b 时全部自然干不劣。状态是完成时刻，不是把所有耗时简单相加。


<a id="course-21"></a>

## 第 21 课：从暴力递归到动态规划（四）

### 21.1 矩阵最小路径和

#### 题目

给定非负整数矩阵，从左上角出发且每步只能向右或向下，返回到达右下角的最小路径和。

**输入、输出与约束**

输入非空矩形非负整数矩阵，只向右或下；返回左上到右下的最小和。

**函数签名（课程入口）**

```java
public static int minPathSum1(int[][] m);
public static int minPathSum2(int[][] m);
```

**示例**

```text
输入：matrix=[[1,3,1],[1,5,1],[4,2,1]]
输出：7
```

解释：沿顶行再向下，和为1+3+1+1+1。

**出处与版本差异**

- [课程源码：class21/Code01_MinPathSum.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class21/Code01_MinPathSum.java)。
- [LeetCode 原题 64. 最小路径和（Minimum Path Sum）](https://leetcode.com/problems/minimum-path-sum/)

#### 思路

1. 定义 `dp[i][j]` 为从左上角走到 `(i,j)` 的最小路径和，包含起点和当前格。它不是“从当前格走向终点”，因此边界和填表方向都从左上开始。
2. 到达内部格子的最后一步只能来自上方 `(i-1,j)` 或左方 `(i,j-1)`，所以取二者较小值，再加当前格。第一行只能从左来、第一列只能从上来，应单独累加初始化。
3. 二维实现完整保存所有格子的答案。压缩到一行时，进入当前列之前，`dp[j]` 尚未更新，表示上方；`dp[j-1]` 已更新，表示本行左方。因此必须从左往右更新。
4. 例如 `[[1,3],[2,1]]`：首行数组为 `[1,4]`；处理第二行先把首列变成 3，再把第二列变成 `min(4,3)+1=4`。这个“旧上方、新左方”的区别就是压缩空间后最容易写错的地方。

#### 代码答案

```java
package class21;

public class Code01_MinPathSum {

	public static int minPathSum1(int[][] m) {
		if (m == null || m.length == 0 || m[0] == null || m[0].length == 0) {
			return 0;
		}
		int row = m.length;
		int col = m[0].length;
		int[][] dp = new int[row][col];
		// 起点本身的数值也属于路径和。
		dp[0][0] = m[0][0];
		for (int i = 1; i < row; i++) {
			dp[i][0] = dp[i - 1][0] + m[i][0];
		}
		for (int j = 1; j < col; j++) {
			dp[0][j] = dp[0][j - 1] + m[0][j];
		}
		for (int i = 1; i < row; i++) {
			for (int j = 1; j < col; j++) {
				// 最后一步只能来自上或左，选择较小的前驱最优值再加当前格。
				dp[i][j] = Math.min(dp[i - 1][j], dp[i][j - 1]) + m[i][j];
			}
		}
		return dp[row - 1][col - 1];
	}

	public static int minPathSum2(int[][] m) {
		if (m == null || m.length == 0 || m[0] == null || m[0].length == 0) {
			return 0;
		}
		int row = m.length;
		int col = m[0].length;
		int[] dp = new int[col];
		dp[0] = m[0][0];
		for (int j = 1; j < col; j++) {
			dp[j] = dp[j - 1] + m[0][j];
		}
		for (int i = 1; i < row; i++) {
			dp[0] += m[i][0];
			for (int j = 1; j < col; j++) {
				// 一维数组中左值已是本行结果，当前槽仍保存上一行结果。
				dp[j] = Math.min(dp[j - 1], dp[j]) + m[i][j];
			}
		}
		return dp[col - 1];
	}

}
```

---

##### 新增对数器（固定输入或固定随机种子）

随机小矩阵完整枚举所有右下路径，分别核对二维表与一维压缩，包含单行、单列和零元素。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
    java.util.Random random = new java.util.Random(20260905L);
    for (int round = 0; round < 2000; round++) {
        int[][] matrix = new int[1 + random.nextInt(6)][1 + random.nextInt(6)];
        for (int[] row : matrix) for (int j = 0; j < row.length; j++) row[j] = random.nextInt(10);
        int expected = brutePath(matrix, 0, 0);
        if (minPathSum1(matrix) != expected || minPathSum2(matrix) != expected)
            throw new AssertionError(java.util.Arrays.deepToString(matrix));
    }
    System.out.println("PASS: MinPathSum, seed=20260905");
}

private static int brutePath(int[][] a, int row, int col) {
    // 完整枚举所有右/下路径，用小矩阵限制指数分支数量。
    if (row == a.length - 1 && col == a[0].length - 1) return a[row][col];
    int best = Integer.MAX_VALUE;
    if (row + 1 < a.length) best = Math.min(best, brutePath(a, row + 1, col));
    if (col + 1 < a[0].length) best = Math.min(best, brutePath(a, row, col + 1));
    return a[row][col] + best;
}
```

#### 题解

**为什么正确**

只许向右或下，因此抵达内部格的最后一步必来自左或上。取两种前驱最优路径中的较小者，并加本格值，既能构造合法路径，也不会遗漏更优路径。

**复杂度**

时间 O(RC)；二维版空间 O(RC)，一维版 O(C)。

**边界与易错点**

这是矩阵路径问题，与二叉树路径总和无关。一维更新从左到右，否则 dp[j-1] 仍是上一行。起点值必须计入，不能初始化为0。

### 21.2 每张货币不同的找零方法数

#### 题目

给定每张都视为不同个体的货币数组和目标金额，返回恰好组成目标金额的方法数。

**输入、输出与约束**

输入正面值纸币数组，各下标视为不同纸币，aim≥0；返回恰好组成目标的下标选择数。

**函数签名（课程入口）**

```java
public static int coinWays(int[] arr, int aim);
public static int dp(int[] arr, int aim);
```

**示例**

```text
输入：arr=[1,1,2], aim=2
输出：2
```

解释：选两张1，或选一张2；两张1各只能使用一次。

**出处与版本差异**

- [课程源码：class21/Code02_CoinsWayEveryPaperDifferent.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class21/Code02_CoinsWayEveryPaperDifferent.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

将每张纸币按下标视为不同个体。f(index,rest) 只处理后缀纸币，当前纸币可不选或选一次：f(i+1,rest)+f(i+1,rest-arr[i])。即使面值相同，不同下标的选择仍算不同方案。

#### 代码答案

```java
package class21;

public class Code02_CoinsWayEveryPaperDifferent {

	public static int coinWays(int[] arr, int aim) {
		return process(arr, 0, aim);
	}

	// arr[index....] 组成正好rest这么多的钱，有几种方法
	public static int process(int[] arr, int index, int rest) {
		if (rest < 0) {
			return 0;
		}
		if (index == arr.length) { // 没钱了！
			return rest == 0 ? 1 : 0;
		} else {
			// 选当前这一张，下一层直接越过它，不能重复使用。
			return process(arr, index + 1, rest) + process(arr, index + 1, rest - arr[index]);
		}
	}

	public static int dp(int[] arr, int aim) {
		if (aim == 0) {
			return 1;
		}
		int N = arr.length;
		int[][] dp = new int[N + 1][aim + 1];
		// 已经没有纸币且剩余为0时，空后续选择构成一个完整方案。
		dp[N][0] = 1;
		for (int index = N - 1; index >= 0; index--) {
			for (int rest = 0; rest <= aim; rest++) {
				dp[index][rest] = dp[index + 1][rest] + (rest - arr[index] >= 0 ? dp[index + 1][rest - arr[index]] : 0);
			}
		}
		return dp[0][aim];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	public static int[] randomArray(int maxLen, int maxValue) {
		int N = (int) (Math.random() * maxLen);
		int[] arr = new int[N];
		for (int i = 0; i < N; i++) {
			arr[i] = (int) (Math.random() * maxValue) + 1;
		}
		return arr;
	}

	// 为了测试
	public static void printArray(int[] arr) {
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// 为了测试
	public static void main(String[] args) {
		int maxLen = 20;
		int maxValue = 30;
		int testTime = 1000000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int[] arr = randomArray(maxLen, maxValue);
			int aim = (int) (Math.random() * maxValue);
			int ans1 = coinWays(arr, aim);
			int ans2 = dp(arr, aim);
			if (ans1 != ans2) {
				System.out.println("Oops!");
				printArray(arr);
				System.out.println(aim);
				System.out.println(ans1);
				System.out.println(ans2);
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

每个纸币下标只做一次二选一，方案按是否选该下标互斥分类。全部纸币处理完时，剩余金额为0才是一份恰好组合，返回1；其他剩余返回0。

**复杂度**

暴力 O(2^N)；动态规划 O(NA) 时间、O(NA) 空间，A为目标金额。

**边界与易错点**

不要把相同面值去重，那会改变方案定义。空选择是金额0的一种方案；数组为空时课程部分入口约定需与此数学基例区分。

### 21.3 每种面值无限张的找零方法数

#### 题目

给定互不相同的货币面值和目标金额，每种面值可使用任意张，返回组成目标金额的方法数。

**输入、输出与约束**

输入互异正面值及aim≥0，每种无限张；返回组合数。

**函数签名（课程入口）**

```java
public static int coinsWay(int[] arr, int aim);
public static int dp1(int[] arr, int aim);
public static int dp2(int[] arr, int aim);
```

**示例**

```text
输入：coins=[1,2], aim=4
输出：3
```

解释：方案为四个1、两个1加一个2、两个2，不计排列顺序。

**出处与版本差异**

- [课程源码：class21/Code03_CoinsWayNoLimit.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class21/Code03_CoinsWayNoLimit.java)。
- [LeetCode 原题 518. 零钱兑换 II（Coin Change II）](https://leetcode.com/problems/coin-change-ii/)

#### 思路

按面值种类递归，枚举当前面值使用0、1、2…张。去掉枚举后，dp[i][rest]=dp[i+1][rest]+dp[i][rest-coin]：第一项完全不用当前面值，第二项先用一张再允许继续使用本面值。

#### 代码答案

```java
package class21;

public class Code03_CoinsWayNoLimit {

	public static int coinsWay(int[] arr, int aim) {
		if (arr == null || arr.length == 0 || aim < 0) {
			return 0;
		}
		return process(arr, 0, aim);
	}

	// arr[index....] 所有的面值，每一个面值都可以任意选择张数，组成正好rest这么多钱，方法数多少？
	public static int process(int[] arr, int index, int rest) {
		if (index == arr.length) { // 没钱了
			return rest == 0 ? 1 : 0;
		}
		int ways = 0;
		for (int zhang = 0; zhang * arr[index] <= rest; zhang++) {
			ways += process(arr, index + 1, rest - (zhang * arr[index]));
		}
		return ways;
	}

	public static int dp1(int[] arr, int aim) {
		if (arr == null || arr.length == 0 || aim < 0) {
			return 0;
		}
		int N = arr.length;
		int[][] dp = new int[N + 1][aim + 1];
		dp[N][0] = 1;
		for (int index = N - 1; index >= 0; index--) {
			for (int rest = 0; rest <= aim; rest++) {
				int ways = 0;
				for (int zhang = 0; zhang * arr[index] <= rest; zhang++) {
					ways += dp[index + 1][rest - (zhang * arr[index])];
				}
				dp[index][rest] = ways;
			}
		}
		return dp[0][aim];
	}

	public static int dp2(int[] arr, int aim) {
		if (arr == null || arr.length == 0 || aim < 0) {
			return 0;
		}
		int N = arr.length;
		int[][] dp = new int[N + 1][aim + 1];
		dp[N][0] = 1;
		for (int index = N - 1; index >= 0; index--) {
			for (int rest = 0; rest <= aim; rest++) {
				// 先统计完全不用当前面值的全部方案。
				dp[index][rest] = dp[index + 1][rest];
				if (rest - arr[index] >= 0) {
					// 使用一张当前面值后还可继续使用它，所以依赖当前行。
					dp[index][rest] += dp[index][rest - arr[index]];
				}
			}
		}
		return dp[0][aim];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	public static int[] randomArray(int maxLen, int maxValue) {
		int N = (int) (Math.random() * maxLen);
		int[] arr = new int[N];
		boolean[] has = new boolean[maxValue + 1];
		for (int i = 0; i < N; i++) {
			do {
				arr[i] = (int) (Math.random() * maxValue) + 1;
			} while (has[arr[i]]);
			has[arr[i]] = true;
		}
		return arr;
	}

	// 为了测试
	public static void printArray(int[] arr) {
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// 为了测试
	public static void main(String[] args) {
		int maxLen = 10;
		int maxValue = 30;
		int testTime = 1000000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int[] arr = randomArray(maxLen, maxValue);
			int aim = (int) (Math.random() * maxValue);
			int ans1 = coinsWay(arr, aim);
			int ans2 = dp1(arr, aim);
			int ans3 = dp2(arr, aim);
			if (ans1 != ans2 || ans1 != ans3) {
				System.out.println("Oops!");
				printArray(arr);
				System.out.println(aim);
				System.out.println(ans1);
				System.out.println(ans2);
				System.out.println(ans3);
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

含至少一张当前面值的方案，去掉一张后，与金额rest-coin且仍允许本面值的方案一一对应；它们与完全不用本面值的方案互斥，因此可以相加而无需逐张枚举。

**复杂度**

枚举表最坏 O(NA²)，消枚举版本 O(NA) 时间、O(NA) 空间。

**边界与易错点**

面值必须互不重复且大于0。当前行依赖更小rest，因此rest递增；若改成下一行rest-coin，就变成每面值最多用一次。

### 21.4 同面值货币有限张的找零方法数

#### 题目

给定可能含重复面值且每张只能使用一次的货币数组，返回组成目标金额的方法数。

**输入、输出与约束**

输入正面值数组，相同面值不可区分、每张至多一次；返回目标金额组合数。

**函数签名（课程入口）**

```java
public static Info getInfo(int[] arr);
public static int coinsWay(int[] arr, int aim);
public static int dp1(int[] arr, int aim);
public static int dp2(int[] arr, int aim);
```

**示例**

```text
输入：arr=[1,1,1,2], aim=3
输出：2
```

解释：三张1，或一张1加一张2；三种选择哪张1不重复计数。

**出处与版本差异**

- [课程源码：class21/Code04_CoinsWaySameValueSamePapper.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class21/Code04_CoinsWaySameValueSamePapper.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先统计每种面值coin及张数z。状态按面值种类推进，枚举使用0..z张。优化时先像无限张一样加入当前行rest-coin，再减去使用超过z张的部分 dp[i+1][rest-(z+1)coin]。

#### 代码答案

```java
package class21;

import java.util.HashMap;
import java.util.Map.Entry;

public class Code04_CoinsWaySameValueSamePapper {

	public static class Info {
		public int[] coins;
		public int[] zhangs;

		public Info(int[] c, int[] z) {
			coins = c;
			zhangs = z;
		}
	}

	public static Info getInfo(int[] arr) {
		HashMap<Integer, Integer> counts = new HashMap<>();
		for (int value : arr) {
			if (!counts.containsKey(value)) {
				counts.put(value, 1);
			} else {
				counts.put(value, counts.get(value) + 1);
			}
		}
		int N = counts.size();
		int[] coins = new int[N];
		int[] zhangs = new int[N];
		int index = 0;
		for (Entry<Integer, Integer> entry : counts.entrySet()) {
			coins[index] = entry.getKey();
			zhangs[index++] = entry.getValue();
		}
		return new Info(coins, zhangs);
	}

	public static int coinsWay(int[] arr, int aim) {
		if (arr == null || arr.length == 0 || aim < 0) {
			return 0;
		}
		Info info = getInfo(arr);
		return process(info.coins, info.zhangs, 0, aim);
	}

	// coins 面值数组，正数且去重
	// zhangs 每种面值对应的张数
	public static int process(int[] coins, int[] zhangs, int index, int rest) {
		if (index == coins.length) {
			return rest == 0 ? 1 : 0;
		}
		int ways = 0;
		for (int zhang = 0; zhang * coins[index] <= rest && zhang <= zhangs[index]; zhang++) {
			ways += process(coins, zhangs, index + 1, rest - (zhang * coins[index]));
		}
		return ways;
	}

	public static int dp1(int[] arr, int aim) {
		if (arr == null || arr.length == 0 || aim < 0) {
			return 0;
		}
		Info info = getInfo(arr);
		int[] coins = info.coins;
		int[] zhangs = info.zhangs;
		int N = coins.length;
		int[][] dp = new int[N + 1][aim + 1];
		dp[N][0] = 1;
		for (int index = N - 1; index >= 0; index--) {
			for (int rest = 0; rest <= aim; rest++) {
				int ways = 0;
				for (int zhang = 0; zhang * coins[index] <= rest && zhang <= zhangs[index]; zhang++) {
					ways += dp[index + 1][rest - (zhang * coins[index])];
				}
				dp[index][rest] = ways;
			}
		}
		return dp[0][aim];
	}

	public static int dp2(int[] arr, int aim) {
		if (arr == null || arr.length == 0 || aim < 0) {
			return 0;
		}
		Info info = getInfo(arr);
		int[] coins = info.coins;
		int[] zhangs = info.zhangs;
		int N = coins.length;
		int[][] dp = new int[N + 1][aim + 1];
		dp[N][0] = 1;
		for (int index = N - 1; index >= 0; index--) {
			for (int rest = 0; rest <= aim; rest++) {
				dp[index][rest] = dp[index + 1][rest];
				if (rest - coins[index] >= 0) {
					dp[index][rest] += dp[index][rest - coins[index]];
				}
				// 定位刚好超出库存一张后的剩余金额，用于扣掉所有超额方案。
				if (rest - coins[index] * (zhangs[index] + 1) >= 0) {
					// 定位刚好超出库存一张后的剩余金额，用于扣掉所有超额方案。
					dp[index][rest] -= dp[index + 1][rest - coins[index] * (zhangs[index] + 1)];
				}
			}
		}
		return dp[0][aim];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	public static int[] randomArray(int maxLen, int maxValue) {
		int N = (int) (Math.random() * maxLen);
		int[] arr = new int[N];
		for (int i = 0; i < N; i++) {
			arr[i] = (int) (Math.random() * maxValue) + 1;
		}
		return arr;
	}

	// 为了测试
	public static void printArray(int[] arr) {
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// 为了测试
	public static void main(String[] args) {
		int maxLen = 10;
		int maxValue = 20;
		int testTime = 1000000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int[] arr = randomArray(maxLen, maxValue);
			int aim = (int) (Math.random() * maxValue);
			int ans1 = coinsWay(arr, aim);
			int ans2 = dp1(arr, aim);
			int ans3 = dp2(arr, aim);
			if (ans1 != ans2 || ans1 != ans3) {
				System.out.println("Oops!");
				printArray(arr);
				System.out.println(aim);
				System.out.println(ans1);
				System.out.println(ans2);
				System.out.println(ans3);
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

无限张递推覆盖了所有至少用一张的方案，但它错误包含了至少z+1张的方案。给这批方案统一取走z+1张后，剩余恰对应下一行的目标rest-(z+1)coin，所以减去这一项刚好删除全部超额情况。

**复杂度**

K种面值、目标A：枚举表 O(KA·最大张数)，优化表 O(KA) 时间、O(KA) 空间。

**边界与易错点**

同面值纸币不区分身份，需先合并频次。减项是z+1张而不是z张；两种前驱行号不能随意互换。

### 21.5 醉汉存活概率

#### 题目

醉汉位于 `N × M` 网格中，每一步等概率向上下左右移动，返回走 `K` 步后仍在网格内的概率。

**输入、输出与约束**

输入正行列数、合法起点与K≥0，每步四方向等概率；返回K步后存活概率。

**函数签名（课程入口）**

```java
public static double livePosibility1(int row, int col, int k, int N, int M);
public static double livePosibility2(int row, int col, int k, int N, int M);
public static long pick(long[][][] dp, int N, int M, int r, int c, int rest);
```

**示例**

```text
输入：棋盘2×2，起点(0,0)，K=1
输出：0.5
```

解释：四个方向只有向右和向下仍在棋盘内。

**出处与版本差异**

- [课程源码：class21/Code05_BobDie.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class21/Code05_BobDie.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先数生存路径，再除以总路径数。f(row,col,rest) 是从当前位置继续走rest步且始终不越界的路径条数；越界为0，合法位置剩余0步为1，其余状态把上下左右四个少一步状态相加。总随机方向序列为4^K。

#### 代码答案

```java
package class21;

public class Code05_BobDie {

	public static double livePosibility1(int row, int col, int k, int N, int M) {
		// 全部长度k方向序列共有4^k种，生存路径数除以它得到概率。
		return (double) process(row, col, k, N, M) / Math.pow(4, k);
	}

	// 目前在row，col位置，还有rest步要走，走完了如果还在棋盘中就获得1个生存点，返回总的生存点数
	public static long process(int row, int col, int rest, int N, int M) {
		if (row < 0 || row == N || col < 0 || col == M) {
			return 0;
		}
		// 还在棋盘中！
		if (rest == 0) {
			// 合法位置且剩余零步时，空后续路径是一条成功路径。
			return 1;
		}
		// 还在棋盘中！还有步数要走
		long up = process(row - 1, col, rest - 1, N, M);
		long down = process(row + 1, col, rest - 1, N, M);
		long left = process(row, col - 1, rest - 1, N, M);
		long right = process(row, col + 1, rest - 1, N, M);
		return up + down + left + right;
	}

	public static double livePosibility2(int row, int col, int k, int N, int M) {
		long[][][] dp = new long[N][M][k + 1];
		for (int i = 0; i < N; i++) {
			for (int j = 0; j < M; j++) {
				dp[i][j][0] = 1;
			}
		}
		for (int rest = 1; rest <= k; rest++) {
			for (int r = 0; r < N; r++) {
				for (int c = 0; c < M; c++) {
					dp[r][c][rest] = pick(dp, N, M, r - 1, c, rest - 1);
					dp[r][c][rest] += pick(dp, N, M, r + 1, c, rest - 1);
					dp[r][c][rest] += pick(dp, N, M, r, c - 1, rest - 1);
					dp[r][c][rest] += pick(dp, N, M, r, c + 1, rest - 1);
				}
			}
		}
		// 全部长度k方向序列共有4^k种，生存路径数除以它得到概率。
		return (double) dp[row][col][k] / Math.pow(4, k);
	}

	public static long pick(long[][][] dp, int N, int M, int r, int c, int rest) {
		if (r < 0 || r == N || c < 0 || c == M) {
			return 0;
		}
		return dp[r][c][rest];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		System.out.println(livePosibility1(6, 6, 10, 50, 50));
		System.out.println(livePosibility2(6, 6, 10, 50, 50));
	}
```

#### 题解

**为什么正确**

四个第一步方向互斥且各概率1/4，全部长度K方向序列等概率。越界路径一旦发生便失败，递归置0会永久排除它；生存计数除以4^K得到概率。

**复杂度**

暴力 O(4^K)，表格 O(RCK) 时间、O(RCK) 空间。

**边界与易错点**

不是只检查最后位置，途中出界后不能回来。路径计数用long仍可能溢出；K较大时可直接转移概率或使用更大整数。


<a id="course-22"></a>

## 第 22 课：动态规划中的概率、最少硬币与整数拆分

### 22.1 砍死怪兽的概率

#### 题目

怪兽有固定生命值，每次攻击造成区间内等概率随机伤害，返回限定攻击次数内击杀怪兽的概率。

**输入、输出与约束**

输入正生命N、正最大伤害M、正攻击次数K；每击独立均匀0..M，返回击杀概率。

**函数签名（课程入口）**

```java
public static double dp1(int N, int M, int K);
public static double dp2(int N, int M, int K);
```

**示例**

```text
输入：N=2,M=2,K=1
输出：1/3
```

解释：伤害0、1、2中只有2可以击杀。

**出处与版本差异**

- [课程源码：class22/Code01_KillMonster.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class22/Code01_KillMonster.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先统计K次攻击后生命值≤0的伤害序列数，再除以(M+1)^K。每次伤害为0..M。若已经死亡，剩余rest次攻击可任意取伤害，因此贡献(M+1)^rest。表格版进一步用相邻生命值状态的窗口差消掉伤害枚举。

#### 代码答案

```java
package class22;

public class Code01_KillMonster {

	public static double right(int N, int M, int K) {
		if (N < 1 || M < 1 || K < 1) {
			return 0;
		}
		long all = (long) Math.pow(M + 1, K);
		long kill = process(K, M, N);
		return (double) ((double) kill / (double) all);
	}

	// 怪兽还剩hp点血
	// 每次的伤害在[0~M]范围上
	// 还有times次可以砍
	// 返回砍死的情况数！
	public static long process(int times, int M, int hp) {
		if (times == 0) {
			return hp <= 0 ? 1 : 0;
		}
		// 怪兽已经死亡，剩余攻击的任意伤害序列均算成功。
		if (hp <= 0) {
			// 每次还有M+1种等概率伤害，剩余times次共有该数量的完成序列。
			return (long) Math.pow(M + 1, times);
		}
		long ways = 0;
		for (int i = 0; i <= M; i++) {
			ways += process(times - 1, M, hp - i);
		}
		return ways;
	}

	public static double dp1(int N, int M, int K) {
		if (N < 1 || M < 1 || K < 1) {
			return 0;
		}
		long all = (long) Math.pow(M + 1, K);
		long[][] dp = new long[K + 1][N + 1];
		dp[0][0] = 1;
		for (int times = 1; times <= K; times++) {
			// 每次还有M+1种等概率伤害，剩余times次共有该数量的完成序列。
			dp[times][0] = (long) Math.pow(M + 1, times);
			for (int hp = 1; hp <= N; hp++) {
				long ways = 0;
				for (int i = 0; i <= M; i++) {
					if (hp - i >= 0) {
						ways += dp[times - 1][hp - i];
					} else {
						ways += (long) Math.pow(M + 1, times - 1);
					}
				}
				dp[times][hp] = ways;
			}
		}
		long kill = dp[K][N];
		return (double) ((double) kill / (double) all);
	}

	public static double dp2(int N, int M, int K) {
		if (N < 1 || M < 1 || K < 1) {
			return 0;
		}
		long all = (long) Math.pow(M + 1, K);
		long[][] dp = new long[K + 1][N + 1];
		dp[0][0] = 1;
		for (int times = 1; times <= K; times++) {
			// 每次还有M+1种等概率伤害，剩余times次共有该数量的完成序列。
			dp[times][0] = (long) Math.pow(M + 1, times);
			for (int hp = 1; hp <= N; hp++) {
				dp[times][hp] = dp[times][hp - 1] + dp[times - 1][hp];
				if (hp - 1 - M >= 0) {
					dp[times][hp] -= dp[times - 1][hp - 1 - M];
				} else {
					dp[times][hp] -= Math.pow(M + 1, times - 1);
				}
			}
		}
		long kill = dp[K][N];
		return (double) ((double) kill / (double) all);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int NMax = 10;
		int MMax = 10;
		int KMax = 10;
		int testTime = 200;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int N = (int) (Math.random() * NMax);
			int M = (int) (Math.random() * MMax);
			int K = (int) (Math.random() * KMax);
			double ans1 = right(N, M, K);
			double ans2 = dp1(N, M, K);
			double ans3 = dp2(N, M, K);
			if (ans1 != ans2 || ans1 != ans3) {
				System.out.println("Oops!");
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

按第一击伤害划分全部序列，各分支互斥。已死状态并非只有一种后续：即使死亡后不影响结果，剩余每一击仍有M+1个可能，必须全部计入等概率样本空间。

**复杂度**

暴力 O((M+1)^K)；枚举表 O(KNM)，窗口差优化 O(KN) 时间、O(KN) 空间。

**边界与易错点**

生命恰好为0已经死亡。已死分支若返回1会少计后续序列。long与Math.pow转整数都受数值范围约束，大参数不能仅靠double转换保证精确计数。

### 22.2 无限张货币组成目标值的最少张数

#### 题目

互异正面值、每种无限张，aim≥0；返回最少张数，课程无解结果为Integer.MAX_VALUE。

**输入、输出与约束**

互异正面值、每种无限张，aim≥0；返回最少张数，课程无解结果为Integer.MAX_VALUE。

**函数签名（课程入口）**

```java
public static int minCoins(int[] arr, int aim);
public static int dp1(int[] arr, int aim);
public static int dp2(int[] arr, int aim);
```

**示例**

```text
输入：coins=[2,3], aim=7
输出：3
```

解释：2+2+3使用三张，无法用两张组成7。

**出处与版本差异**

- [课程源码：class22/Code02_MinCoinsNoLimit.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class22/Code02_MinCoinsNoLimit.java)。
- [LeetCode 对应题 322. 零钱兑换（Coin Change）](https://leetcode.com/problems/coin-change/)

课程版本说明：

- LeetCode 322：最少硬币模型对应；课程无解返回 Integer.MAX_VALUE，原题要求 -1。

#### 思路

状态f(i,rest)表示从第i种面值起凑够rest的最少张数。可不用当前面值，或用一张后继续使用同种面值；取 dp[i+1][rest] 与 1+dp[i][rest-coin] 的较小值。无解状态用最大整数哨兵。

#### 代码答案

```java
package class22;

public class Code02_MinCoinsNoLimit {

	public static int minCoins(int[] arr, int aim) {
		return process(arr, 0, aim);
	}

	// arr[index...]面值，每种面值张数自由选择，
	// 搞出rest正好这么多钱，返回最小张数
	// 拿Integer.MAX_VALUE标记怎么都搞定不了
	public static int process(int[] arr, int index, int rest) {
		if (index == arr.length) {
			return rest == 0 ? 0 : Integer.MAX_VALUE;
		} else {
			int ans = Integer.MAX_VALUE;
			for (int zhang = 0; zhang * arr[index] <= rest; zhang++) {
				int next = process(arr, index + 1, rest - zhang * arr[index]);
				if (next != Integer.MAX_VALUE) {
					ans = Math.min(ans, zhang + next);
				}
			}
			return ans;
		}
	}

	public static int dp1(int[] arr, int aim) {
		if (aim == 0) {
			return 0;
		}
		int N = arr.length;
		int[][] dp = new int[N + 1][aim + 1];
		dp[N][0] = 0;
		for (int j = 1; j <= aim; j++) {
			// 没有任何面值时，正金额不可达，用哨兵区分真正的0张方案。
			dp[N][j] = Integer.MAX_VALUE;
		}
		for (int index = N - 1; index >= 0; index--) {
			for (int rest = 0; rest <= aim; rest++) {
				int ans = Integer.MAX_VALUE;
				for (int zhang = 0; zhang * arr[index] <= rest; zhang++) {
					int next = dp[index + 1][rest - zhang * arr[index]];
					if (next != Integer.MAX_VALUE) {
						ans = Math.min(ans, zhang + next);
					}
				}
				dp[index][rest] = ans;
			}
		}
		return dp[0][aim];
	}

	public static int dp2(int[] arr, int aim) {
		if (aim == 0) {
			return 0;
		}
		int N = arr.length;
		int[][] dp = new int[N + 1][aim + 1];
		dp[N][0] = 0;
		for (int j = 1; j <= aim; j++) {
			// 没有任何面值时，正金额不可达，用哨兵区分真正的0张方案。
			dp[N][j] = Integer.MAX_VALUE;
		}
		for (int index = N - 1; index >= 0; index--) {
			for (int rest = 0; rest <= aim; rest++) {
				dp[index][rest] = dp[index + 1][rest];
				if (rest - arr[index] >= 0
						// 只有子金额可达，才允许给其最少张数加1。
						&& dp[index][rest - arr[index]] != Integer.MAX_VALUE) {
					dp[index][rest] = Math.min(dp[index][rest], dp[index][rest - arr[index]] + 1);
				}
			}
		}
		return dp[0][aim];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	public static int[] randomArray(int maxLen, int maxValue) {
		int N = (int) (Math.random() * maxLen);
		int[] arr = new int[N];
		boolean[] has = new boolean[maxValue + 1];
		for (int i = 0; i < N; i++) {
			do {
				arr[i] = (int) (Math.random() * maxValue) + 1;
			} while (has[arr[i]]);
			has[arr[i]] = true;
		}
		return arr;
	}

	// 为了测试
	public static void printArray(int[] arr) {
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// 为了测试
	public static void main(String[] args) {
		int maxLen = 20;
		int maxValue = 30;
		int testTime = 300000;
		System.out.println("功能测试开始");
		for (int i = 0; i < testTime; i++) {
			int N = (int) (Math.random() * maxLen);
			int[] arr = randomArray(N, maxValue);
			int aim = (int) (Math.random() * maxValue);
			int ans1 = minCoins(arr, aim);
			int ans2 = dp1(arr, aim);
			int ans3 = dp2(arr, aim);
			if (ans1 != ans2 || ans1 != ans3) {
				System.out.println("Oops!");
				printArray(arr);
				System.out.println(aim);
				System.out.println(ans1);
				System.out.println(ans2);
				break;
			}
		}
		System.out.println("功能测试结束");
	}
```

#### 题解

**为什么正确**

任何组合要么当前面值出现零次，要么至少一次；后一类删除一张后与rest-coin的同种类子问题对应。以较小张数合并得到最优。

**复杂度**

枚举表最坏 O(NA²)，优化表 O(NA) 时间、O(NA) 空间。

**边界与易错点**

无解哨兵不能直接加1，会溢出成为负数。课程返回 Integer.MAX_VALUE 表示无解，提交LeetCode322需转换为-1。

### 22.3 整数拆分方法数

#### 题目

给定正整数 `n`，把它拆成若干正整数之和且后一个数不小于前一个数，返回不同拆分方法数。

**输入、输出与约束**

输入正整数N；返回无序正整数拆分数，使用非递减序列作为唯一表示。

**函数签名（课程入口）**

```java
public static int ways(int n);
public static int dp1(int n);
public static int dp2(int n);
```

**示例**

```text
输入：N=4
输出：5
```

解释：1+1+1+1、1+1+2、1+3、2+2、4。

**出处与版本差异**

- [课程源码：class22/Code03_SplitNumber.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class22/Code03_SplitNumber.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

为避免把同一拆分的不同顺序重复计数，要求后续数字不小于前一个。f(pre,rest)表示用≥pre的数凑rest的方法数。枚举第一个数first后转到f(first,rest-first)；优化表拆成完全不用pre与至少用一个pre两类。

#### 代码答案

```java
package class22;

public class Code03_SplitNumber {

	// n为正数
	public static int ways(int n) {
		if (n < 0) {
			return 0;
		}
		if (n == 1) {
			return 1;
		}
		return process(1, n);
	}

	// 上一个拆出来的数是pre
	// 还剩rest需要去拆
	// 返回拆解的方法数
	public static int process(int pre, int rest) {
		if (rest == 0) {
			return 1;
		}
		if (pre > rest) {
			return 0;
		}
		int ways = 0;
		for (int first = pre; first <= rest; first++) {
			// 选定首数后，后续不得小于它，但允许再次使用相同数。
			ways += process(first, rest - first);
		}
		return ways;
	}

	public static int dp1(int n) {
		if (n < 0) {
			return 0;
		}
		if (n == 1) {
			return 1;
		}
		int[][] dp = new int[n + 1][n + 1];
		for (int pre = 1; pre <= n; pre++) {
			dp[pre][0] = 1;
			dp[pre][pre] = 1;
		}
		for (int pre = n - 1; pre >= 1; pre--) {
			for (int rest = pre + 1; rest <= n; rest++) {
				int ways = 0;
				for (int first = pre; first <= rest; first++) {
					ways += dp[first][rest - first];
				}
				dp[pre][rest] = ways;
			}
		}
		return dp[1][n];
	}

	public static int dp2(int n) {
		if (n < 0) {
			return 0;
		}
		if (n == 1) {
			return 1;
		}
		int[][] dp = new int[n + 1][n + 1];
		for (int pre = 1; pre <= n; pre++) {
			dp[pre][0] = 1;
			dp[pre][pre] = 1;
		}
		for (int pre = n - 1; pre >= 1; pre--) {
			for (int rest = pre + 1; rest <= n; rest++) {
				dp[pre][rest] = dp[pre + 1][rest];
				// 至少使用一次pre，去掉一次后仍允许继续使用pre。
				dp[pre][rest] += dp[pre][rest - pre];
			}
		}
		return dp[1][n];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int test = 39;
		System.out.println(ways(test));
		System.out.println(dp1(test));
		System.out.println(dp2(test));
	}
```

#### 题解

**为什么正确**

每个整数拆分排序后有唯一非递减表示，第一项及后缀因此构成唯一递归路径。优化式 dp[pre][rest]=dp[pre+1][rest]+dp[pre][rest-pre] 分别计数最小值大于pre和至少含一个pre的互斥方案。

**复杂度**

枚举表 O(N³)，消枚举表 O(N²) 时间、O(N²) 空间。

**边界与易错点**

rest=0 返回1表示已完成一个拆分；pre>rest时除rest=0外无解。递归选数后传first而不是first+1，因为相同数字可重复。


<a id="course-23"></a>

## 第 23 课：背包变形与 N 皇后

### 23.1 数组分成两组的最接近累加和

#### 题目

给定正整数数组，把元素分成两组，使两组累加和尽量接近，返回较小一组能够达到的最大累加和。

**输入、输出与约束**

输入正整数数组，分为两组使和尽量接近；返回较小组和，少于两个元素按课程返回0。

**函数签名（课程入口）**

```java
public static int dp(int[] arr);
```

**示例**

```text
输入：arr=[3,1,4,2,2]
输出：6
```

解释：可选4+2，其余也为6。

**出处与版本差异**

- [课程源码：class23/Code01_SplitSumClosed.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class23/Code01_SplitSumClosed.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

设总和S，要让两组接近，只需从数组中选一个和尽量大但≤floor(S/2)的子集。状态按物品下标与剩余容量做0-1背包，收益和重量都等于元素本身，返回较小组的最大和。

#### 代码答案

```java
package class23;

public class Code01_SplitSumClosed {

	public static int right(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		int sum = 0;
		for (int num : arr) {
			sum += num;
		}
		return process(arr, 0, sum / 2);
	}

	// arr[i...]可以自由选择，请返回累加和尽量接近rest，但不能超过rest的情况下，最接近的累加和是多少？
	public static int process(int[] arr, int i, int rest) {
		if (i == arr.length) {
			return 0;
		} else { // 还有数，arr[i]这个数
			// 可能性1，不使用arr[i]
			int p1 = process(arr, i + 1, rest);
			// 可能性2，要使用arr[i]
			int p2 = 0;
			if (arr[i] <= rest) {
				p2 = arr[i] + process(arr, i + 1, rest - arr[i]);
			}
			return Math.max(p1, p2);
		}
	}

	public static int dp(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		int sum = 0;
		for (int num : arr) {
			sum += num;
		}
		// 较小组不超过总和一半，把问题转换为容量上限。
		sum /= 2;
		int N = arr.length;
		int[][] dp = new int[N + 1][sum + 1];
		for (int i = N - 1; i >= 0; i--) {
			for (int rest = 0; rest <= sum; rest++) {
				// 可能性1，不使用arr[i]
				int p1 = dp[i + 1][rest];
				// 可能性2，要使用arr[i]
				int p2 = 0;
				if (arr[i] <= rest) {
					p2 = arr[i] + dp[i + 1][rest - arr[i]];
				}
				dp[i][rest] = Math.max(p1, p2);
			}
		}
		return dp[0][sum];
	}

	public static int[] randomArray(int len, int value) {
		int[] arr = new int[len];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) (Math.random() * value);
		}
		return arr;
	}

	public static void printArray(int[] arr) {
		for (int num : arr) {
			System.out.print(num + " ");
		}
		System.out.println();
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int maxLen = 20;
		int maxValue = 50;
		int testTime = 10000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int len = (int) (Math.random() * maxLen);
			int[] arr = randomArray(len, maxValue);
			int ans1 = right(arr);
			int ans2 = dp(arr);
			if (ans1 != ans2) {
				printArray(arr);
				System.out.println(ans1);
				System.out.println(ans2);
				System.out.println("Oops!");
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

任意二分都存在不超过S/2的较小一组，两组差为S-2×较小和；在上限内最大化较小和，就等价于最小化差值。每个元素选或不选覆盖所有分组。

**复杂度**

暴力 O(2^N)；表格 O(NS) 时间、O(NS) 空间，属于依赖总和的伪多项式算法。

**边界与易错点**

本题没有组人数相同要求，不要误套下一题的picks状态。元素为正使容量模型成立；结果是较小组的和，不是两组差。

### 23.2 两组数量接近时的最接近累加和

#### 题目

输入正整数数组，要求两组人数差≤1；返回满足人数限制时可达的最大较小组和。

**输入、输出与约束**

输入正整数数组，要求两组人数差≤1；返回满足人数限制时可达的最大较小组和。

**函数签名（课程入口）**

```java
public static int dp(int[] arr);
public static int dp2(int[] arr);
```

**示例**

```text
输入：arr=[1,2,100]
输出：3
```

解释：人数1与2；两元素组1+2=3是较小和组。

**出处与版本差异**

- [课程源码：class23/Code02_SplitSumClosedSizeHalf.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class23/Code02_SplitSumClosedSizeHalf.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

除和接近外，两组人数差必须≤1，因此在背包状态中增加picks，表示还需选多少个。只能在满足picks的情况下最大化不超过半总和的子集；奇数长度分别尝试选floor(N/2)与ceil(N/2)个。

#### 代码答案

```java
package class23;

public class Code02_SplitSumClosedSizeHalf {

	public static int right(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		int sum = 0;
		for (int num : arr) {
			sum += num;
		}
		if ((arr.length & 1) == 0) {
			return process(arr, 0, arr.length / 2, sum / 2);
		} else {
			return Math.max(process(arr, 0, arr.length / 2, sum / 2), process(arr, 0, arr.length / 2 + 1, sum / 2));
		}
	}

	// arr[i....]自由选择，挑选的个数一定要是picks个，累加和<=rest, 离rest最近的返回
	public static int process(int[] arr, int i, int picks, int rest) {
		if (i == arr.length) {
			// 元素耗尽后，只有选够指定人数才是合法分组。
			return picks == 0 ? 0 : -1;
		} else {
			int p1 = process(arr, i + 1, picks, rest);
			// 就是要使用arr[i]这个数
			int p2 = -1;
			int next = -1;
			if (arr[i] <= rest) {
				// 选了当前元素，后续需要的人数减少一，容量同时扣除。
				next = process(arr, i + 1, picks - 1, rest - arr[i]);
			}
			if (next != -1) {
				p2 = arr[i] + next;
			}
			return Math.max(p1, p2);
		}
	}

	public static int dp(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		int sum = 0;
		for (int num : arr) {
			sum += num;
		}
		sum /= 2;
		int N = arr.length;
		int M = (N + 1) / 2;
		int[][][] dp = new int[N + 1][M + 1][sum + 1];
		for (int i = 0; i <= N; i++) {
			for (int j = 0; j <= M; j++) {
				for (int k = 0; k <= sum; k++) {
					dp[i][j][k] = -1;
				}
			}
		}
		for (int rest = 0; rest <= sum; rest++) {
			dp[N][0][rest] = 0;
		}
		for (int i = N - 1; i >= 0; i--) {
			for (int picks = 0; picks <= M; picks++) {
				for (int rest = 0; rest <= sum; rest++) {
					int p1 = dp[i + 1][picks][rest];
					// 就是要使用arr[i]这个数
					int p2 = -1;
					int next = -1;
					// 选了当前元素，后续需要的人数减少一，容量同时扣除。
					if (picks - 1 >= 0 && arr[i] <= rest) {
						// 选了当前元素，后续需要的人数减少一，容量同时扣除。
						next = dp[i + 1][picks - 1][rest - arr[i]];
					}
					if (next != -1) {
						p2 = arr[i] + next;
					}
					dp[i][picks][rest] = Math.max(p1, p2);
				}
			}
		}
		if ((arr.length & 1) == 0) {
			return dp[0][arr.length / 2][sum];
		} else {
			return Math.max(dp[0][arr.length / 2][sum], dp[0][(arr.length / 2) + 1][sum]);
		}
	}

//	public static int right(int[] arr) {
//		if (arr == null || arr.length < 2) {
//			return 0;
//		}
//		int sum = 0;
//		for (int num : arr) {
//			sum += num;
//		}
//		return process(arr, 0, 0, sum >> 1);
//	}
//
//	public static int process(int[] arr, int i, int picks, int rest) {
//		if (i == arr.length) {
//			if ((arr.length & 1) == 0) {
//				return picks == (arr.length >> 1) ? 0 : -1;
//			} else {
//				return (picks == (arr.length >> 1) || picks == (arr.length >> 1) + 1) ? 0 : -1;
//			}
//		}
//		int p1 = process(arr, i + 1, picks, rest);
//		int p2 = -1;
//		int next2 = -1;
//		if (arr[i] <= rest) {
//			next2 = process(arr, i + 1, picks + 1, rest - arr[i]);
//		}
//		if (next2 != -1) {
//			p2 = arr[i] + next2;
//		}
//		return Math.max(p1, p2);
//	}
//
//	public static int dp1(int[] arr) {
//		if (arr == null || arr.length < 2) {
//			return 0;
//		}
//		int sum = 0;
//		for (int num : arr) {
//			sum += num;
//		}
//		sum >>= 1;
//		int N = arr.length;
//		int M = (arr.length + 1) >> 1;
//		int[][][] dp = new int[N + 1][M + 1][sum + 1];
//		for (int i = 0; i <= N; i++) {
//			for (int j = 0; j <= M; j++) {
//				for (int k = 0; k <= sum; k++) {
//					dp[i][j][k] = -1;
//				}
//			}
//		}
//		for (int k = 0; k <= sum; k++) {
//			dp[N][M][k] = 0;
//		}
//		if ((arr.length & 1) != 0) {
//			for (int k = 0; k <= sum; k++) {
//				dp[N][M - 1][k] = 0;
//			}
//		}
//		for (int i = N - 1; i >= 0; i--) {
//			for (int picks = 0; picks <= M; picks++) {
//				for (int rest = 0; rest <= sum; rest++) {
//					int p1 = dp[i + 1][picks][rest];
//					int p2 = -1;
//					int next2 = -1;
//					if (picks + 1 <= M && arr[i] <= rest) {
//						next2 = dp[i + 1][picks + 1][rest - arr[i]];
//					}
//					if (next2 != -1) {
//						p2 = arr[i] + next2;
//					}
//					dp[i][picks][rest] = Math.max(p1, p2);
//				}
//			}
//		}
//		return dp[0][0][sum];
//	}

	public static int dp2(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		int sum = 0;
		for (int num : arr) {
			sum += num;
		}
		sum >>= 1;
		int N = arr.length;
		int M = (arr.length + 1) >> 1;
		int[][][] dp = new int[N][M + 1][sum + 1];
		for (int i = 0; i < N; i++) {
			for (int j = 0; j <= M; j++) {
				for (int k = 0; k <= sum; k++) {
					dp[i][j][k] = Integer.MIN_VALUE;
				}
			}
		}
		for (int i = 0; i < N; i++) {
			for (int k = 0; k <= sum; k++) {
				dp[i][0][k] = 0;
			}
		}
		for (int k = 0; k <= sum; k++) {
			dp[0][1][k] = arr[0] <= k ? arr[0] : Integer.MIN_VALUE;
		}
		for (int i = 1; i < N; i++) {
			for (int j = 1; j <= Math.min(i + 1, M); j++) {
				for (int k = 0; k <= sum; k++) {
					dp[i][j][k] = dp[i - 1][j][k];
					if (k - arr[i] >= 0) {
						dp[i][j][k] = Math.max(dp[i][j][k], dp[i - 1][j - 1][k - arr[i]] + arr[i]);
					}
				}
			}
		}
		return Math.max(dp[N - 1][M][sum], dp[N - 1][N - M][sum]);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[] randomArray(int len, int value) {
		int[] arr = new int[len];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) (Math.random() * value);
		}
		return arr;
	}

	// for test
	public static void printArray(int[] arr) {
		for (int num : arr) {
			System.out.print(num + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {
		int maxLen = 10;
		int maxValue = 50;
		int testTime = 10000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int len = (int) (Math.random() * maxLen);
			int[] arr = randomArray(len, maxValue);
			int ans1 = right(arr);
			int ans2 = dp(arr);
			int ans3 = dp2(arr);
			if (ans1 != ans2 || ans1 != ans3) {
				printArray(arr);
				System.out.println(ans1);
				System.out.println(ans2);
				System.out.println(ans3);
				System.out.println("Oops!");
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

固定所选人数后，每个合法分组唯一落在相应的背包分支。偶数只能平分人数；奇数较小和的一组可能是人数较多也可能较少的一组，因此必须检查两种人数而取较大可行和。

**复杂度**

表格时间与空间 O(N²S)，S为总和；暴力指数时间。

**边界与易错点**

没有元素可选时，仅picks=0才合法，否则必须返回无解哨兵。不能只取人数少的组作为较小和组。

### 23.3 N 皇后问题

#### 题目

在 `N × N` 棋盘上放置 `N` 个皇后，使任意两个皇后不同行、不同列且不在同一条斜线上，返回合法方案数。

**输入、输出与约束**

输入N≥1，返回不同行列对角线冲突的方案数；位版仅支持其32位掩码范围，计数还须不溢出。

**函数签名（课程入口）**

```java
public static int num1(int n);
public static boolean isValid(int[] record, int i, int j);
public static int num2(int n);
```

**示例**

```text
输入：N=4
输出：2
```

解释：两组列号为[1,3,0,2]与[2,0,3,1]，列号从0开始。

**出处与版本差异**

- [课程源码：class23/Code03_NQueens.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class23/Code03_NQueens.java)。
- [LeetCode 对应题 52. N 皇后 II（N-Queens II）](https://leetcode.com/problems/n-queens-ii/)

课程版本说明：

- LeetCode 52：返回方案数量的实现对应。

#### 思路

逐行放皇后，每行只选一列。普通回溯用record记录前面各行列号，检查同列和对角线；位运算版用colLim、leftDiaLim、rightDiaLim记录当前行禁用列，取可用位逐个尝试，进入下一行时移动对角线掩码。

#### 代码答案

```java
package class23;

public class Code03_NQueens {

	public static int num1(int n) {
		if (n < 1) {
			return 0;
		}
		int[] record = new int[n];
		return process1(0, record, n);
	}

	// 当前来到i行，一共是0~N-1行
	// 在i行上放皇后，所有列都尝试
	// 必须要保证跟之前所有的皇后不打架
	// int[] record record[x] = y 之前的第x行的皇后，放在了y列上
	// 返回：不关心i以上发生了什么，i.... 后续有多少合法的方法数
	public static int process1(int i, int[] record, int n) {
		if (i == n) {
			return 1;
		}
		int res = 0;
		// i行的皇后，放哪一列呢？j列，
		for (int j = 0; j < n; j++) {
			if (isValid(record, i, j)) {
				record[i] = j;
				res += process1(i + 1, record, n);
			}
		}
		return res;
	}

	public static boolean isValid(int[] record, int i, int j) {
		// 0..i-1
		for (int k = 0; k < i; k++) {
			if (j == record[k] || Math.abs(record[k] - j) == Math.abs(i - k)) {
				return false;
			}
		}
		return true;
	}

	// 请不要超过32皇后问题
	public static int num2(int n) {
		if (n < 1 || n > 32) {
			return 0;
		}
		// 如果你是13皇后问题，limit 最右13个1，其他都是0
		int limit = n == 32 ? -1 : (1 << n) - 1;
		return process2(limit, 0, 0, 0);
	}

	// 7皇后问题
	// limit : 0....0 1 1 1 1 1 1 1
	// 之前皇后的列影响：colLim
	// 之前皇后的左下对角线影响：leftDiaLim
	// 之前皇后的右下对角线影响：rightDiaLim
	public static int process2(int limit, int colLim, int leftDiaLim, int rightDiaLim) {
		if (colLim == limit) {
			return 1;
		}
		// pos中所有是1的位置，是你可以去尝试皇后的位置
		// 合并三类禁位后取反，再与limit相交，只保留棋盘内可用列。
		int pos = limit & (~(colLim | leftDiaLim | rightDiaLim));
		int mostRightOne = 0;
		int res = 0;
		while (pos != 0) {
			// 提取一个可用列，当前递归分支把皇后放在这里。
			mostRightOne = pos & (~pos + 1);
			// 移除刚尝试的候选位，循环继续枚举其他列。
			pos = pos - mostRightOne;
			res += process2(limit, colLim | mostRightOne, (leftDiaLim | mostRightOne) << 1,
					(rightDiaLim | mostRightOne) >>> 1);
		}
		return res;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int n = 15;

		long start = System.currentTimeMillis();
		System.out.println(num2(n));
		long end = System.currentTimeMillis();
		System.out.println("cost time: " + (end - start) + "ms");

		start = System.currentTimeMillis();
		System.out.println(num1(n));
		end = System.currentTimeMillis();
		System.out.println("cost time: " + (end - start) + "ms");

	}
```

#### 题解

**为什么正确**

每行恰放一个已消除同行冲突；列相同或行差绝对值等于列差绝对值表示剩余两种冲突。每个合法棋盘有唯一的逐行列选择，枚举全部可用列既不漏解也不重计。

**复杂度**

回溯为指数搜索，常用粗界O(N!)个候选排列；逐列验证另有O(N)因子。位版每候选冲突检查O(1)，递归空间O(N)。

**边界与易错点**

对角线禁位每到下一行要分别左移与无符号右移。N=32时不能写(1<<32)-1，Java移位距离会取低5位。int答案在较大N也会溢出。


<a id="course-24"></a>

## 第 24 课：滑动窗口及其应用

### 24.1 滑动窗口最大值

#### 题目

输入int数组及合法窗口宽度w；返回各长度w连续窗口的最大值数组。

**输入、输出与约束**

输入int数组及合法窗口宽度w；返回各长度w连续窗口的最大值数组。

**函数签名（课程入口）**

```java
public static int[] getMaxWindow(int[] arr, int w);
```

**示例**

```text
输入：arr=[1,3,-1,-3,5], w=3
输出：[3,3,5]
```

解释：前三个窗口最大值依次为3、3、5。

**出处与版本差异**

- [课程源码：class24/Code01_SlidingWindowMaxArray.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class24/Code01_SlidingWindowMaxArray.java)。
- [LeetCode 原题 239. 滑动窗口最大值（Sliding Window Maximum）](https://leetcode.com/problems/sliding-window-maximum/)

#### 思路

双端队列保存候选下标，值从队首到队尾递减。新值进来时从尾部删除不大于它的旧值，再加入新下标；队首过期就弹出。窗口形成后，队首就是最大值位置。

#### 代码答案

```java
package class24;

import java.util.LinkedList;

public class Code01_SlidingWindowMaxArray {

	// 暴力的对数器方法
	public static int[] right(int[] arr, int w) {
		if (arr == null || w < 1 || arr.length < w) {
			return null;
		}
		int N = arr.length;
		int[] res = new int[N - w + 1];
		int index = 0;
		int L = 0;
		int R = w - 1;
		while (R < N) {
			int max = arr[L];
			for (int i = L + 1; i <= R; i++) {
				max = Math.max(max, arr[i]);

			}
			res[index++] = max;
			L++;
			R++;
		}
		return res;
	}

	public static int[] getMaxWindow(int[] arr, int w) {
		if (arr == null || w < 1 || arr.length < w) {
			return null;
		}
		// qmax 窗口最大值的更新结构
		// 放下标
		LinkedList<Integer> qmax = new LinkedList<Integer>();
		int[] res = new int[arr.length - w + 1];
		int index = 0;
		for (int R = 0; R < arr.length; R++) {
			// 新值不小于队尾且更晚过期，旧队尾永远不再是最佳候选。
			while (!qmax.isEmpty() && arr[qmax.peekLast()] <= arr[R]) {
				qmax.pollLast();
			}
			qmax.addLast(R);
			// 队首恰好离开当前长度w窗口，必须删除。
			if (qmax.peekFirst() == R - w) {
				qmax.pollFirst();
			}
			if (R >= w - 1) {
				// 队首既是候选最大值又在窗口内，写入本窗口答案。
				res[index++] = arr[qmax.peekFirst()];
			}
		}
		return res;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) (Math.random() * (maxValue + 1));
		}
		return arr;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

public static void main(String[] args) {
		int testTime = 100000;
		int maxSize = 100;
		int maxValue = 100;
		System.out.println("test begin");
		for (int i = 0; i < testTime; i++) {
			int[] arr = generateRandomArray(maxSize, maxValue);
			int w = (int) (Math.random() * (arr.length + 1));
			int[] ans1 = getMaxWindow(arr, w);
			int[] ans2 = right(arr, w);
			if (!isEqual(ans1, ans2)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("test finish");
	}
```

#### 题解

**为什么正确**

被新值淘汰的旧值既不更大又更早过期，在任何未来同时包含新旧值的窗口中都不会胜过新值。保留下来的队首最大且未过期，因此代表当前窗口最大值。

**复杂度**

每下标至多入队、出队一次，时间O(N)，队列空间O(W)，结果空间O(N-W+1)。

**边界与易错点**

队列存下标才能判断过期。等值时保留较新的更有用。必须等R≥W-1后才输出；合法窗口1≤W≤N。

### 24.2 最大值减最小值不超过限制的子数组数

#### 题目

给定整数数组和限制值 `num`，返回满足最大值减最小值不超过 `num` 的子数组数量。

**输入、输出与约束**

输入整数数组与非负极差上限；返回满足条件的非空子数组数。

**函数签名（课程入口）**

```java
public static int num(int[] arr, int sum);
```

**示例**

```text
输入：arr=[1,2,3], num=1
输出：5
```

解释：三个单元素区间及[1,2]、[2,3]合法。

**出处与版本差异**

- [课程源码：class24/Code02_AllLessNumSubArray.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class24/Code02_AllLessNumSubArray.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

固定左端L，让右端R尽量扩到max-min≤num，用两个单调队列分别维护最大、最小值。R停在第一个不合法位置时，以L开头的合法子数组有R-L个。再让L右移，R无需回退。

#### 代码答案

```java
package class24;

import java.util.LinkedList;

public class Code02_AllLessNumSubArray {

	// 暴力的对数器方法
	public static int right(int[] arr, int sum) {
		if (arr == null || arr.length == 0 || sum < 0) {
			return 0;
		}
		int N = arr.length;
		int count = 0;
		for (int L = 0; L < N; L++) {
			for (int R = L; R < N; R++) {
				int max = arr[L];
				int min = arr[L];
				for (int i = L + 1; i <= R; i++) {
					max = Math.max(max, arr[i]);
					min = Math.min(min, arr[i]);
				}
				if (max - min <= sum) {
					count++;
				}
			}
		}
		return count;
	}

	public static int num(int[] arr, int sum) {
		if (arr == null || arr.length == 0 || sum < 0) {
			return 0;
		}
		int N = arr.length;
		int count = 0;
		LinkedList<Integer> maxWindow = new LinkedList<>();
		LinkedList<Integer> minWindow = new LinkedList<>();
		int R = 0;
		for (int L = 0; L < N; L++) {
			while (R < N) {
				while (!maxWindow.isEmpty() && arr[maxWindow.peekLast()] <= arr[R]) {
					maxWindow.pollLast();
				}
				maxWindow.addLast(R);
				while (!minWindow.isEmpty() && arr[minWindow.peekLast()] >= arr[R]) {
					minWindow.pollLast();
				}
				minWindow.addLast(R);
				if (arr[maxWindow.peekFirst()] - arr[minWindow.peekFirst()] > sum) {
					break;
				} else {
					R++;
				}
			}
			// 右端可从L到R-1任选，共R-L个合法区间。
			count += R - L;
			if (maxWindow.peekFirst() == L) {
				maxWindow.pollFirst();
			}
			if (minWindow.peekFirst() == L) {
				minWindow.pollFirst();
			}
		}
		return count;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[] generateRandomArray(int maxLen, int maxValue) {
		int len = (int) (Math.random() * (maxLen + 1));
		int[] arr = new int[len];
		for (int i = 0; i < len; i++) {
			arr[i] = (int) (Math.random() * (maxValue + 1)) - (int) (Math.random() * (maxValue + 1));
		}
		return arr;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr != null) {
			for (int i = 0; i < arr.length; i++) {
				System.out.print(arr[i] + " ");
			}
			System.out.println();
		}
	}

public static void main(String[] args) {
		int maxLen = 100;
		int maxValue = 200;
		int testTime = 100000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int[] arr = generateRandomArray(maxLen, maxValue);
			int sum = (int) (Math.random() * (maxValue + 1));
			int ans1 = right(arr, sum);
			int ans2 = num(arr, sum);
			if (ans1 != ans2) {
				System.out.println("Oops!");
				printArray(arr);
				System.out.println(sum);
				System.out.println(ans1);
				System.out.println(ans2);
				break;
			}
		}
		System.out.println("测试结束");

	}
```

#### 题解

**为什么正确**

扩张区间只会让最大值不减、最小值不增，因此极差不减；一旦不合法，更长也不合法。缩小左端不会使极差增大，所以之前已经合法的右端不用重新检查，保证双指针单向。

**复杂度**

优化时间O(N)，双队列空间O(N)；暴力逐区间求极差可达O(N³)。

**边界与易错点**

R为首个不合法位置，计数是R-L不是R-L+1。num<0没有合法非空区间。极端int求max-min可能溢出，扩展输入需long差值。

### 24.3 加油站良好出发点

#### 题目

环形路线上每站有可加油量和驶往下一站的耗油量，返回能够绕行一周的起点编号。

**输入、输出与约束**

gas与cost等长且非负；返回能完成一圈的起点，无解返回-1。

**函数签名（课程入口）**

```java
public static int canCompleteCircuit(int[] gas, int[] cost);
public static boolean[] goodArray(int[] g, int[] c);
```

**示例**

```text
输入：gas=[1,2,3], cost=[2,2,2]
输出：起点2（从0编号）
```

解释：净油量[-1,0,1]，从最后一站出发的累计量为1、0、0。

**出处与版本差异**

- [课程源码：class24/Code03_GasStation.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class24/Code03_GasStation.java)。
- [LeetCode 原题 134. 加油站（Gas Station）](https://leetcode.com/problems/gas-station/)

#### 思路

将每站净油量写为gas[i]-cost[i]，复制成两圈并求前缀和。起点i能走完一圈，当且仅当后续N个前缀的最小值减去出发前前缀不小于0。用单调队列维护这个长度N窗口的最小前缀。

#### 代码答案

```java
package class24;

import java.util.LinkedList;

// 测试链接：https://leetcode.com/problems/gas-station
public class Code03_GasStation {

	// 这个方法的时间复杂度O(N)，额外空间复杂度O(N)
	public static int canCompleteCircuit(int[] gas, int[] cost) {
		boolean[] good = goodArray(gas, cost);
		for (int i = 0; i < gas.length; i++) {
			if (good[i]) {
				return i;
			}
		}
		return -1;
	}

	public static boolean[] goodArray(int[] g, int[] c) {
		int N = g.length;
		int M = N << 1;
		int[] arr = new int[M];
		for (int i = 0; i < N; i++) {
			arr[i] = g[i] - c[i];
			arr[i + N] = g[i] - c[i];
		}
		for (int i = 1; i < M; i++) {
			arr[i] += arr[i - 1];
		}
		// 举个例子说明一下
		// 比如纯能数组(也就是燃料 - 距离之后)的数组 :
		// 纯能数组 = 3, 2,-6, 2, 3,-4, 6
		// 数组下标 = 0  1  2  3  4  5  6
		// 客观上说:
		// 0位置不是良好出发点
		// 1位置不是良好出发点
		// 2位置不是良好出发点
		// 3位置是良好出发点
		// 4位置不是良好出发点
		// 5位置不是良好出发点
		// 6位置是良好出发点
		// 把数组增倍之后 :
		// arr   = 3, 2,-6, 2, 3,-4, 6, 3, 2,-6, 2, 3,-4, 6
		// 然后计算前缀和 :
		// arr   = 3, 5,-1, 1, 4, 0, 6, 9,11, 5, 7,10, 6,12
		// index = 0  1  2  3  4  5  6  7  8  9 10 11 12 13
		// 这些就是上面发生的过程
		// 接下来生成长度为N的窗口
		LinkedList<Integer> w = new LinkedList<>();
		for (int i = 0; i < N; i++) {
			while (!w.isEmpty() && arr[w.peekLast()] >= arr[i]) {
				w.pollLast();
			}
			w.addLast(i);
		}
		// 上面的过程，就是先遍历N个数，然后建立窗口
		// arr   =[3, 5,-1, 1, 4, 0, 6],9,11, 5, 7,10, 6,12
		// index = 0  1  2  3  4  5  6  7  8  9 10 11 12 13
		// w中的内容如下:
		// index:  2 5 6
		// value: -1 0 6
		// 左边是头，右边是尾，从左到右严格变大
		// 此时代表最原始的arr的这部分的数字:
		// 原始的值 = [3, 2,-6, 2, 3,-4, 6],3, 2,-6, 2, 3,-4, 6
		// 原始下标 =  0  1  2  3  4  5  6  0  1  2  3  4  5  6
		// 上面这个窗口中，累加和最薄弱的点，就是w中最左信息
		// 也就是会累加出，-1这个值，所以会走不下去。
		// 宣告了此时0位置不是良好出发点。
		// 接下来的代码，就是依次考察每个点是不是良好出发点。
		// 目前的信息是:
		// 计算的前缀和 :
		// arr   =[3, 5,-1, 1, 4, 0, 6],9,11, 5, 7,10, 6,12
		// index = 0  1  2  3  4  5  6  7  8  9 10 11 12 13
		// w中的内容如下:
		// index:  2 5 6
		// value: -1 0 6
		// 此时代表最原始的arr的这部分的数字:
		// 原始的值 = [3, 2,-6, 2, 3,-4, 6],3, 2,-6, 2, 3,-4, 6
		// 原始下标 =  0  1  2  3  4  5  6  0  1  2  3  4  5  6
		// 现在让窗口往下移动
		// 计算的前缀和 :
		// arr   = 3,[5,-1, 1, 4, 0, 6, 9],11, 5, 7,10, 6,12
		// index = 0  1  2  3  4  5  6  7   8  9 10 11 12 13
		// w中的内容如下:
		// index:  2 5 6 7
		// value: -1 0 6 9
		// 此时代表最原始的arr的这部分的数字:
		// 原始的值 =  3,[2,-6, 2, 3,-4, 6, 3],2,-6, 2, 3,-4, 6
		// 原始下标 =  0  1  2  3  4  5  6  0  1  2  3  4  5  6
		// 上面这个窗口中，累加和最薄弱的点，就是w中最左信息
		// 但是w最左的值是-1啊！而这个窗口中最薄弱的累加和是-4啊。
		// 对！所以最薄弱信息 = 窗口中的最左信息 - 窗口左侧刚出去的数(代码中的offset!)
		// 所以，最薄弱信息 = -1 - 0位置的3(窗口左侧刚出去的数) = -4
		// 看到了吗？最薄弱信息，依靠这种方式，加工出来了！
		// 宣告了此时1位置不是良好出发点。
		// 我们继续，让窗口往下移动
		// 计算的前缀和 :
		// arr   = 3, 5,[-1, 1, 4, 0, 6, 9,11], 5, 7,10, 6,12
		// index = 0  1   2  3  4  5  6  7  8   9 10 11 12 13
		// w中的内容如下:
		// index:  2  5  6  7  8
		// value: -1  0  6  9 11
		// 此时代表最原始的arr的这部分的数字:
		// 原始的值 =  3, 2,[-6, 2, 3,-4, 6, 3, 2],-6, 2, 3,-4, 6
		// 原始下标 =  0  1   2  3  4  5  6  0  1   2  3  4  5  6
		// 上面这个窗口中，累加和最薄弱的点，就是w中最左信息
		// 但是w最左的值是-1啊！而这个窗口中最薄弱的累加和是-6啊。
		// 对！所以最薄弱信息 = 窗口中的最左信息 - 窗口左侧刚出去的数(代码中的offset!)
		// 所以，最薄弱信息 = -1 - 1位置的5(窗口左侧刚出去的数) = -6
		// 看到了吗？最薄弱信息，依靠这种方式，加工出来了！
		// 宣告了此时2位置不是良好出发点。
		// 我们继续，让窗口往下移动
		// 计算的前缀和 :
		// arr   = 3, 5, -1,[1, 4, 0, 6, 9,11, 5], 7,10, 6,12
		// index = 0  1   2  3  4  5  6  7  8  9  10 11 12 13
		// w中的内容如下:
		// index:  5  9
		// value:  0  5
		// 没错，9位置的5进来，让6、7、8位置从w的尾部弹出了，
		// 同时原来在w中的2位置已经过期了，所以也弹出了，因为窗口左边界已经划过2位置了
		// 此时代表最原始的arr的这部分的数字:
		// 原始的值 =  3, 2, -6,[2, 3,-4, 6, 3, 2, -6],2, 3,-4, 6
		// 原始下标 =  0  1   2  3  4  5  6  0  1   2  3  4  5  6
		// 上面这个窗口中，累加和最薄弱的点，就是w中最左信息
		// 但是w最左的值是0啊！而这个窗口中最薄弱的累加和是1啊
		// 对！所以最薄弱信息 = 窗口中的最左信息 - 窗口左侧刚出去的数(代码中的offset!)
		// 所以，最薄弱信息 = 0 - 2位置的-1(窗口左侧刚出去的数) = 1
		// 看到了吗？最薄弱信息，依靠这种方式，加工出来了！
		// 宣告了此时3位置是良好出发点。
		// 往下同理
		boolean[] ans = new boolean[N];
		for (int offset = 0, i = 0, j = N; j < M; offset = arr[i++], j++) {
			// 窗口最小前缀扣掉出发前累计值，得到一圈内最少剩余油量。
			if (arr[w.peekFirst()] - offset >= 0) {
				ans[i] = true;
			}
			if (w.peekFirst() == i) {
				w.pollFirst();
			}
			while (!w.isEmpty() && arr[w.peekLast()] >= arr[j]) {
				w.pollLast();
			}
			w.addLast(j);
		}
		return ans;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：总净油量非负只说明至少存在可行起点，不代表任意起点可行。必须减出发前的前缀偏移；内部goodArray可找所有起点，入口通常返回第一个。

本条未附独立随机对数器。

#### 题解

**为什么正确**

经过任意前缀路段的剩余油量就是对应前缀差，整圈中途不缺油等价于所有这些差均非负，也等价于最小差非负。复制数组把环形一圈变成普通连续窗口。

**复杂度**

时间O(N)，额外空间O(N)。

**边界与易错点**

总净油量非负只说明至少存在可行起点，不代表任意起点可行。必须减出发前的前缀偏移；内部goodArray可找所有起点，入口通常返回第一个。

### 24.4 每张货币只能使用一次的最少张数

#### 题目

输入正面值纸币数组，每张至多一次；返回最少张数，无解按源码返回Integer.MAX_VALUE。

**输入、输出与约束**

输入正面值纸币数组，每张至多一次；返回最少张数，无解按源码返回Integer.MAX_VALUE。

**函数签名（课程入口）**

```java
public static int minCoins(int[] arr, int aim);
public static int dp1(int[] arr, int aim);
public static Info getInfo(int[] arr);
public static int dp2(int[] arr, int aim);
public static int dp3(int[] arr, int aim);
public static int compensate(int pre, int cur, int coin);
```

**示例**

```text
输入：arr=[1,1,3], aim=3
输出：1
```

解释：用一张3最优；不能无限使用只出现两次的1。

**出处与版本差异**

- [课程源码：class24/Code04_MinCoinsOnePaper.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class24/Code04_MinCoinsOnePaper.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

每张纸币只可使用一次，先用0-1背包得到最少张数。优化版把同面值合并为库存，按金额对面值的余数分组；同组金额每增加coin，可用张数增加一，用单调队列维护库存窗口中最优的“上一行张数-金额/coin”。

#### 代码答案

```java
package class24;

import java.util.HashMap;
import java.util.Map.Entry;
import java.util.LinkedList;

public class Code04_MinCoinsOnePaper {

	public static int minCoins(int[] arr, int aim) {
		return process(arr, 0, aim);
	}

	public static int process(int[] arr, int index, int rest) {
		if (rest < 0) {
			return Integer.MAX_VALUE;
		}
		if (index == arr.length) {
			return rest == 0 ? 0 : Integer.MAX_VALUE;
		} else {
			int p1 = process(arr, index + 1, rest);
			int p2 = process(arr, index + 1, rest - arr[index]);
			if (p2 != Integer.MAX_VALUE) {
				p2++;
			}
			return Math.min(p1, p2);
		}
	}

	// dp1时间复杂度为：O(arr长度 * aim)
	public static int dp1(int[] arr, int aim) {
		if (aim == 0) {
			return 0;
		}
		int N = arr.length;
		int[][] dp = new int[N + 1][aim + 1];
		dp[N][0] = 0;
		for (int j = 1; j <= aim; j++) {
			dp[N][j] = Integer.MAX_VALUE;
		}
		for (int index = N - 1; index >= 0; index--) {
			for (int rest = 0; rest <= aim; rest++) {
				int p1 = dp[index + 1][rest];
				int p2 = rest - arr[index] >= 0 ? dp[index + 1][rest - arr[index]] : Integer.MAX_VALUE;
				if (p2 != Integer.MAX_VALUE) {
					p2++;
				}
				dp[index][rest] = Math.min(p1, p2);
			}
		}
		return dp[0][aim];
	}

	public static class Info {
		public int[] coins;
		public int[] zhangs;

		public Info(int[] c, int[] z) {
			coins = c;
			zhangs = z;
		}
	}

	public static Info getInfo(int[] arr) {
		HashMap<Integer, Integer> counts = new HashMap<>();
		for (int value : arr) {
			if (!counts.containsKey(value)) {
				counts.put(value, 1);
			} else {
				counts.put(value, counts.get(value) + 1);
			}
		}
		int N = counts.size();
		int[] coins = new int[N];
		int[] zhangs = new int[N];
		int index = 0;
		for (Entry<Integer, Integer> entry : counts.entrySet()) {
			coins[index] = entry.getKey();
			zhangs[index++] = entry.getValue();
		}
		return new Info(coins, zhangs);
	}

	// dp2时间复杂度为：O(arr长度) + O(货币种数 * aim * 每种货币的平均张数)
	public static int dp2(int[] arr, int aim) {
		if (aim == 0) {
			return 0;
		}
		// 得到info时间复杂度O(arr长度)
		Info info = getInfo(arr);
		int[] coins = info.coins;
		int[] zhangs = info.zhangs;
		int N = coins.length;
		int[][] dp = new int[N + 1][aim + 1];
		dp[N][0] = 0;
		for (int j = 1; j <= aim; j++) {
			dp[N][j] = Integer.MAX_VALUE;
		}
		// 这三层for循环，时间复杂度为O(货币种数 * aim * 每种货币的平均张数)
		for (int index = N - 1; index >= 0; index--) {
			for (int rest = 0; rest <= aim; rest++) {
				dp[index][rest] = dp[index + 1][rest];
				for (int zhang = 1; zhang * coins[index] <= aim && zhang <= zhangs[index]; zhang++) {
					if (rest - zhang * coins[index] >= 0
							&& dp[index + 1][rest - zhang * coins[index]] != Integer.MAX_VALUE) {
						dp[index][rest] = Math.min(dp[index][rest], zhang + dp[index + 1][rest - zhang * coins[index]]);
					}
				}
			}
		}
		return dp[0][aim];
	}

	// dp3时间复杂度为：O(arr长度) + O(货币种数 * aim)
	// 优化需要用到窗口内最小值的更新结构
	public static int dp3(int[] arr, int aim) {
		if (aim == 0) {
			return 0;
		}
		// 得到info时间复杂度O(arr长度)
		Info info = getInfo(arr);
		int[] c = info.coins;
		int[] z = info.zhangs;
		int N = c.length;
		int[][] dp = new int[N + 1][aim + 1];
		dp[N][0] = 0;
		for (int j = 1; j <= aim; j++) {
			dp[N][j] = Integer.MAX_VALUE;
		}
		// 虽然是嵌套了很多循环，但是时间复杂度为O(货币种数 * aim)
		// 因为用了窗口内最小值的更新结构
		for (int i = N - 1; i >= 0; i--) {
			for (int mod = 0; mod < Math.min(aim + 1, c[i]); mod++) {
				// 当前面值 X
				// mod mod + x mod + 2*x mod + 3 * x
				LinkedList<Integer> w = new LinkedList<>();
				w.add(mod);
				dp[i][mod] = dp[i + 1][mod];
				for (int r = mod + c[i]; r <= aim; r += c[i]) {
					while (!w.isEmpty() && (dp[i + 1][w.peekLast()] == Integer.MAX_VALUE
							|| dp[i + 1][w.peekLast()] + compensate(w.peekLast(), r, c[i]) >= dp[i + 1][r])) {
						w.pollLast();
					}
					w.addLast(r);
					int overdue = r - c[i] * (z[i] + 1);
					// 删除库存窗口之外的最旧候选。
					if (w.peekFirst() == overdue) {
						w.pollFirst();
					}
					if (dp[i + 1][w.peekFirst()] == Integer.MAX_VALUE) {
						dp[i][r] = Integer.MAX_VALUE;
					} else {
						dp[i][r] = dp[i + 1][w.peekFirst()] + compensate(w.peekFirst(), r, c[i]);
					}
				}
			}
		}
		return dp[0][aim];
	}

	public static int compensate(int pre, int cur, int coin) {
		return (cur - pre) / coin;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	public static int[] randomArray(int N, int maxValue) {
		int[] arr = new int[N];
		for (int i = 0; i < N; i++) {
			arr[i] = (int) (Math.random() * maxValue) + 1;
		}
		return arr;
	}

	// 为了测试
	public static void printArray(int[] arr) {
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// 为了测试
	public static void main(String[] args) {
		int maxLen = 20;
		int maxValue = 30;
		int testTime = 300000;
		System.out.println("功能测试开始");
		for (int i = 0; i < testTime; i++) {
			int N = (int) (Math.random() * maxLen);
			int[] arr = randomArray(N, maxValue);
			int aim = (int) (Math.random() * maxValue);
			int ans1 = minCoins(arr, aim);
			int ans2 = dp1(arr, aim);
			int ans3 = dp2(arr, aim);
			int ans4 = dp3(arr, aim);
			if (ans1 != ans2 || ans3 != ans4 || ans1 != ans3) {
				System.out.println("Oops!");
				printArray(arr);
				System.out.println(aim);
				System.out.println(ans1);
				System.out.println(ans2);
				System.out.println(ans3);
				System.out.println(ans4);
				break;
			}
		}
		System.out.println("功能测试结束");

		System.out.println("==========");

		int aim = 0;
		int[] arr = null;
		long start;
		long end;
		int ans2;
		int ans3;

		System.out.println("性能测试开始");
		maxLen = 30000;
		maxValue = 20;
		aim = 60000;
		arr = randomArray(maxLen, maxValue);

		start = System.currentTimeMillis();
		ans2 = dp2(arr, aim);
		end = System.currentTimeMillis();
		System.out.println("dp2答案 : " + ans2 + ", dp2运行时间 : " + (end - start) + " ms");

		start = System.currentTimeMillis();
		ans3 = dp3(arr, aim);
		end = System.currentTimeMillis();
		System.out.println("dp3答案 : " + ans3 + ", dp3运行时间 : " + (end - start) + " ms");
		System.out.println("性能测试结束");

		System.out.println("===========");

		System.out.println("货币大量重复出现情况下，");
		System.out.println("大数据量测试dp3开始");
		maxLen = 20000000;
		aim = 10000;
		maxValue = 10000;
		arr = randomArray(maxLen, maxValue);
		start = System.currentTimeMillis();
		ans3 = dp3(arr, aim);
		end = System.currentTimeMillis();
		System.out.println("dp3运行时间 : " + (end - start) + " ms");
		System.out.println("大数据量测试dp3结束");

		System.out.println("===========");

		System.out.println("当货币很少出现重复，dp2比dp3有常数时间优势");
		System.out.println("当货币大量出现重复，dp3时间复杂度明显优于dp2");
		System.out.println("dp3的优化用到了窗口内最小值的更新结构");
	}
```

#### 题解

**为什么正确**

金额r使用k张当前面值的候选为dpNext[r-k·coin]+k。对同余金额重写后，当前金额项固定，需最小化候选旧状态的修正值；库存限定候选只来自最近z+1个位置，故可用滑动最小值队列。

**复杂度**

逐张背包O(NA)；库存单调队列O(KA)时间，K为面值种类数；源码二维表空间O(KA)。

**边界与易错点**

有限库存不能套无限张递推。无解MAX_VALUE不能参与直接加法或差值比较。队列同时处理过期与候选支配，缺任何一个都会错误。


<a id="course-25"></a>

## 第 25 课：单调栈及其应用

### 25.1 单调栈求左右最近较小位置

#### 题目

给定整数数组，为每个位置返回左侧和右侧距离最近且值严格更小的位置。

**输入、输出与约束**

输入整数数组；返回每个下标左右最近严格较小位置，无则-1。无重复专用方法要求值互异。

**函数签名（课程入口）**

```java
public static int[][] getNearLessNoRepeat(int[] arr);
public static int[][] getNearLess(int[] arr);
```

**示例**

```text
输入：arr=[3,1,3,3]
输出：[[-1,1],[-1,-1],[1,-1],[1,-1]]
```

解释：两个末尾3共享左侧较小位置1，相互不算严格较小。

**出处与版本差异**

- [课程源码：class25/Code01_MonotonousStack.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class25/Code01_MonotonousStack.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

栈中元素按值严格递增。新值较小时弹出更大元素：新下标就是弹出元素的右侧最近较小位置，弹出后栈顶是其左侧最近较小位置。允许重复值时，把相等值的下标放在同一组，整组共享边界。

#### 代码答案

```java
package class25;

import java.util.List;
import java.util.ArrayList;
import java.util.Stack;

public class Code01_MonotonousStack {

	// arr = [ 3, 1, 2, 3]
	//         0  1  2  3
	//  [
	//     0 : [-1,  1]
	//     1 : [-1, -1]
	//     2 : [ 1, -1]
	//     3 : [ 2, -1]
	//  ]
	public static int[][] getNearLessNoRepeat(int[] arr) {
		int[][] res = new int[arr.length][2];
		// 只存位置！
		Stack<Integer> stack = new Stack<>();
		for (int i = 0; i < arr.length; i++) { // 当遍历到i位置的数，arr[i]
			while (!stack.isEmpty() && arr[stack.peek()] > arr[i]) {
				int j = stack.pop();
				// 弹出后前一组末尾是左边最近严格较小位置，空栈表示不存在。
				int leftLessIndex = stack.isEmpty() ? -1 : stack.peek();
				res[j][0] = leftLessIndex;
				res[j][1] = i;
			}
			stack.push(i);
		}
		while (!stack.isEmpty()) {
			int j = stack.pop();
			// 弹出后前一组末尾是左边最近严格较小位置，空栈表示不存在。
			int leftLessIndex = stack.isEmpty() ? -1 : stack.peek();
			res[j][0] = leftLessIndex;
			res[j][1] = -1;
		}
		return res;
	}

	public static int[][] getNearLess(int[] arr) {
		int[][] res = new int[arr.length][2];
		Stack<List<Integer>> stack = new Stack<>();
		for (int i = 0; i < arr.length; i++) { // i -> arr[i] 进栈
			while (!stack.isEmpty() && arr[stack.peek().get(0)] > arr[i]) {
				// 相等值作为一组同时结算，组内节点不能互为严格较小边界。
				List<Integer> popIs = stack.pop();
				// 弹出后前一组末尾是左边最近严格较小位置，空栈表示不存在。
				int leftLessIndex = stack.isEmpty() ? -1 : stack.peek().get(stack.peek().size() - 1);
				for (Integer popi : popIs) {
					res[popi][0] = leftLessIndex;
					res[popi][1] = i;
				}
			}
			if (!stack.isEmpty() && arr[stack.peek().get(0)] == arr[i]) {
				stack.peek().add(Integer.valueOf(i));
			} else {
				ArrayList<Integer> list = new ArrayList<>();
				list.add(i);
				stack.push(list);
			}
		}
		while (!stack.isEmpty()) {
			// 相等值作为一组同时结算，组内节点不能互为严格较小边界。
			List<Integer> popIs = stack.pop();
			// 弹出后前一组末尾是左边最近严格较小位置，空栈表示不存在。
			int leftLessIndex = stack.isEmpty() ? -1 : stack.peek().get(stack.peek().size() - 1);
			for (Integer popi : popIs) {
				res[popi][0] = leftLessIndex;
				res[popi][1] = -1;
			}
		}
		return res;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[] getRandomArrayNoRepeat(int size) {
		int[] arr = new int[(int) (Math.random() * size) + 1];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = i;
		}
		for (int i = 0; i < arr.length; i++) {
			int swapIndex = (int) (Math.random() * arr.length);
			int tmp = arr[swapIndex];
			arr[swapIndex] = arr[i];
			arr[i] = tmp;
		}
		return arr;
	}

	// for test
	public static int[] getRandomArray(int size, int max) {
		int[] arr = new int[(int) (Math.random() * size) + 1];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) (Math.random() * max) - (int) (Math.random() * max);
		}
		return arr;
	}

	// for test
	public static int[][] rightWay(int[] arr) {
		int[][] res = new int[arr.length][2];
		for (int i = 0; i < arr.length; i++) {
			int leftLessIndex = -1;
			int rightLessIndex = -1;
			int cur = i - 1;
			while (cur >= 0) {
				if (arr[cur] < arr[i]) {
					leftLessIndex = cur;
					break;
				}
				cur--;
			}
			cur = i + 1;
			while (cur < arr.length) {
				if (arr[cur] < arr[i]) {
					rightLessIndex = cur;
					break;
				}
				cur++;
			}
			res[i][0] = leftLessIndex;
			res[i][1] = rightLessIndex;
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[][] res1, int[][] res2) {
		if (res1.length != res2.length) {
			return false;
		}
		for (int i = 0; i < res1.length; i++) {
			if (res1[i][0] != res2[i][0] || res1[i][1] != res2[i][1]) {
				return false;
			}
		}

		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

public static void main(String[] args) {
		int size = 10;
		int max = 20;
		int testTimes = 2000000;
		System.out.println("测试开始");
		for (int i = 0; i < testTimes; i++) {
			int[] arr1 = getRandomArrayNoRepeat(size);
			int[] arr2 = getRandomArray(size, max);
			if (!isEqual(getNearLessNoRepeat(arr1), rightWay(arr1))) {
				System.out.println("Oops!");
				printArray(arr1);
				break;
			}
			if (!isEqual(getNearLess(arr2), rightWay(arr2))) {
				System.out.println("Oops!");
				printArray(arr2);
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

一个下标留在栈中意味着此前尚未遇到右侧较小值，新值触发弹栈时便是第一个。栈内更近但更大的下标已被剔除，剩余前一组是左侧最近严格较小位置；同值组避免把相等误当较小。

**复杂度**

每下标入栈出栈一次，时间O(N)，空间O(N)。

**边界与易错点**

无重复专用版本不能拿来处理相等值。左边界取前一组的最后下标，才能保证最近。栈清空阶段右边界均为-1。

### 25.2 单调栈求左右最近较小位置（在线评测版）

#### 题目

输入整数数组；返回每个下标左右最近严格较小位置，无则-1。无重复专用方法要求值互异。

**输入、输出与约束**

输入整数数组；返回每个下标左右最近严格较小位置，无则-1。无重复专用方法要求值互异。

**函数签名（课程入口）**

```java
public static void getNearLess(int n);
```

**示例**

```text
输入：arr=[3,1,3,3]
输出：[[-1,1],[-1,-1],[1,-1],[1,-1]]
```

解释：两个末尾3共享左侧较小位置1，相互不算严格较小。

**出处与版本差异**

- [课程源码：class25/Code01_MonotonousStackForNowcoder.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class25/Code01_MonotonousStackForNowcoder.java)。
- [牛客网在线评测](https://www.nowcoder.com/practice/2a2c00e7a88a498693568cef63a4b7bb)。

#### 思路

数组版使用两个栈：stack1保存所有尚未结算下标，stack2只保存每个等值组的最后下标。遇到更小值时逐个弹出stack1；右边界就是当前i，左边界是stack2中前一个严格更小组的最后位置。同值组全部弹完后才删除它在stack2的代表。

#### 代码答案

```java
package class25;

// 测试链接 : https://www.nowcoder.com/practice/2a2c00e7a88a498693568cef63a4b7bb
// 如果在牛客上做题，可以用如下的方式来做
// 请同学们务必参考如下代码中关于输入、输出的处理
// 这是输入输出处理效率很高的写法
// 提交如下的代码，并把主类名改成"Main"
// 可以直接通过
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.io.StreamTokenizer;

public class Code01_MonotonousStackForNowcoder {

	public static int[] arr = new int[1000000];
	public static int[][] ans = new int[1000000][2];
	// stack1 : 相等值的位置也放
	// stack2 : 只放不相等值的最后一个位置
	// 比如 : arr = { 3, 3, 3, 4, 4, 6, 6, 6}
	//          位置  0  1  2  3  4  5  6  7
	// 如果位置依次压栈，
	// stack1中的记录是（位置） : 0 1 2 3 4 5 6 7
	// stack2中的记录是（位置） : 2 4 7
	public static int[] stack1 = new int[1000000];
	public static int[] stack2 = new int[1000000];

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StreamTokenizer in = new StreamTokenizer(br);
		PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
		while (in.nextToken() != StreamTokenizer.TT_EOF) {
			int n = (int) in.nval;
			for (int i = 0; i < n; i++) {
				in.nextToken();
				arr[i] = (int) in.nval;
			}
			getNearLess(n);
			for (int i = 0; i < n; i++) {
				out.println(ans[i][0] + " " + ans[i][1]);
			}
			out.flush();
		}
	}

	public static void getNearLess(int n) {
		int stackSize1 = 0;
		int stackSize2 = 0;
		for (int i = 0; i < n; i++) {
			while (stackSize1 > 0 && arr[stack1[stackSize1 - 1]] > arr[i]) {
				int curIndex = stack1[--stackSize1];
				// 当前等值组之前的组严格更小，取其最后位置作为最近左边界。
				int left = stackSize2 < 2 ? -1 : stack2[stackSize2 - 2];
				ans[curIndex][0] = left;
				// 当前i第一次以更小值弹出该位置，因此是最近右边界。
				ans[curIndex][1] = i;
				if (stackSize1 == 0 || arr[stack1[stackSize1 - 1]] != arr[curIndex]) {
					// 当前等值组已全部离开stack1，才能删除其代表。
					stackSize2--;
				}
			}
			if (stackSize1 != 0 && arr[stack1[stackSize1 - 1]] == arr[i]) {
				// 新位置与组顶相等，更新该组最右代表，不新增数值组。
				stack2[stackSize2 - 1] = i;
			} else {
				stack2[stackSize2++] = i;
			}
			stack1[stackSize1++] = i;
		}
		while (stackSize1 != 0) {
			int curIndex = stack1[--stackSize1];
			// 当前等值组之前的组严格更小，取其最后位置作为最近左边界。
			int left = stackSize2 < 2 ? -1 : stack2[stackSize2 - 2];
			ans[curIndex][0] = left;
			// 扫描结束仍未弹出的位置，右边不存在严格更小值。
			ans[curIndex][1] = -1;
			if (stackSize1 == 0 || arr[stack1[stackSize1 - 1]] != arr[curIndex]) {
				// 当前等值组已全部离开stack1，才能删除其代表。
				stackSize2--;
			}
		}
	}

}
```

---

##### 输入输出核对

先以本题示例核对结果，再重点覆盖：相等值不能成为严格更小的边界；stack2必须跳过整个同值组。两个栈都用当前调用的局部栈长初始化，数组容量限制N≤1,000,000。

该版本保留在线评测入口；未附独立随机对数器，不把编译通过当作正确性证明。

#### 题解

**为什么正确**

一个下标留在栈中意味着此前尚未遇到右侧较小值，新值触发弹栈时便是第一个。栈内更近但更大的下标已被剔除，剩余前一组是左侧最近严格较小位置；同值组避免把相等误当较小。

**复杂度**

每下标入栈出栈一次，时间O(N)，空间O(N)。

**边界与易错点**

相等值不能成为严格更小的边界；stack2必须跳过整个同值组。两个栈都用当前调用的局部栈长初始化，数组容量限制N≤1,000,000。

### 25.3 子数组累加和乘最小值的最大值

#### 题目

给定正整数数组，对每个非空子数组计算“元素和 × 最小值”，返回所有结果中的最大值。

**输入、输出与约束**

输入正整数数组；返回子数组和乘最小值的最大结果，在线评测方法另按1,000,000,007取模。

**函数签名（课程入口）**

```java
public static int max1(int[] arr);
public static int max2(int[] arr);
public static int[] gerenareRondomArray();
public static int maxSumMinProduct(int[] arr);
```

**示例**

```text
输入：arr=[1,2,3,2]
输出：14
```

解释：子数组[2,3,2]和为7、最小值2，乘积14。

**出处与版本差异**

- [课程源码：class25/Code02_AllTimesMinToMax.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class25/Code02_AllTimesMinToMax.java)。
- [LeetCode 原题 1856. 子数组最小乘积的最大值（Maximum Subarray Min-Product）](https://leetcode.com/problems/maximum-subarray-min-product/)

#### 思路

枚举哪个位置充当子数组最小值，先用单调栈找其左右更小边界。所有元素为正时，固定最小值后把区间扩到边界内最大范围会让区间和最大；用前缀和O(1)取得该范围和，再乘当前最小值。

#### 代码答案

```java
package class25;

import java.util.Stack;

public class Code02_AllTimesMinToMax {

	public static int max1(int[] arr) {
		int max = Integer.MIN_VALUE;
		for (int i = 0; i < arr.length; i++) {
			for (int j = i; j < arr.length; j++) {
				int minNum = Integer.MAX_VALUE;
				int sum = 0;
				for (int k = i; k <= j; k++) {
					sum += arr[k];
					minNum = Math.min(minNum, arr[k]);
				}
				max = Math.max(max, minNum * sum);
			}
		}
		return max;
	}

	public static int max2(int[] arr) {
		int size = arr.length;
		int[] sums = new int[size];
		sums[0] = arr[0];
		for (int i = 1; i < size; i++) {
			sums[i] = sums[i - 1] + arr[i];
		}
		int max = Integer.MIN_VALUE;
		Stack<Integer> stack = new Stack<Integer>();
		for (int i = 0; i < size; i++) {
			// 遇到不更大的值时结算旧最小值候选，并把等值归属交给后面的位置。
			while (!stack.isEmpty() && arr[stack.peek()] >= arr[i]) {
				int j = stack.pop();
				max = Math.max(max, (stack.isEmpty() ? sums[i - 1] : (sums[i - 1] - sums[stack.peek()])) * arr[j]);
			}
			stack.push(i);
		}
		while (!stack.isEmpty()) {
			int j = stack.pop();
			max = Math.max(max, (stack.isEmpty() ? sums[size - 1] : (sums[size - 1] - sums[stack.peek()])) * arr[j]);
		}
		return max;
	}

	public static int[] gerenareRondomArray() {
		int[] arr = new int[(int) (Math.random() * 20) + 10];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) (Math.random() * 101);
		}
		return arr;
	}

	// 本题可以在leetcode上找到原题
	// 测试链接 : https://leetcode.com/problems/maximum-subarray-min-product/
	// 注意测试题目数量大，要取模，但是思路和课上讲的是完全一样的
	// 注意溢出的处理即可，也就是用long类型来表示累加和
	// 还有优化就是，你可以用自己手写的数组栈，来替代系统实现的栈，也会快很多
	public static int maxSumMinProduct(int[] arr) {
		int size = arr.length;
		long[] sums = new long[size];
		sums[0] = arr[0];
		for (int i = 1; i < size; i++) {
			sums[i] = sums[i - 1] + arr[i];
		}
		long max = Long.MIN_VALUE;
		int[] stack = new int[size];
		int stackSize = 0;
		for (int i = 0; i < size; i++) {
			while (stackSize != 0 && arr[stack[stackSize - 1]] >= arr[i]) {
				int j = stack[--stackSize];
				max = Math.max(max,
						(stackSize == 0 ? sums[i - 1] : (sums[i - 1] - sums[stack[stackSize - 1]])) * arr[j]);
			}
			stack[stackSize++] = i;
		}
		while (stackSize != 0) {
			int j = stack[--stackSize];
			max = Math.max(max,
					(stackSize == 0 ? sums[size - 1] : (sums[size - 1] - sums[stack[stackSize - 1]])) * arr[j]);
		}
		return (int) (max % 1000000007);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int testTimes = 2000000;
		System.out.println("test begin");
		for (int i = 0; i < testTimes; i++) {
			int[] arr = gerenareRondomArray();
			if (max1(arr) != max2(arr)) {
				System.out.println("FUCK!");
				break;
			}
		}
		System.out.println("test finish");
	}
```

#### 题解

**为什么正确**

任一候选子数组都有一个最小值位置。固定它后，不越过更小元素的最大范围仍以它为最小值；正数扩展只增加和，所以该最宽范围不劣于任何较窄候选。枚举所有位置便覆盖最优答案。

**复杂度**

优化O(N)时间、O(N)空间；暴力三重循环O(N³)。

**边界与易错点**

正数条件是扩大区间正确的关键，允许负数时不能直接沿用。原课程整数版本和LeetCode取模版本的数值处理不同；应先用long比较真实乘积，再对最终最大值取模。

### 25.4 直方图最大矩形

#### 题目

给定柱状图各柱高度，每根柱宽度为 1，返回能够勾勒出的最大矩形面积。

**输入、输出与约束**

输入非负柱高数组，每柱宽1；返回最大矩形面积，结果须在返回类型范围内。

**函数签名（课程入口）**

```java
public static int largestRectangleArea1(int[] height);
public static int largestRectangleArea2(int[] height);
```

**示例**

```text
输入：heights=[2,1,5,6,2,3]
输出：10
```

解释：高度5、宽度2覆盖柱5和6，面积10。

**出处与版本差异**

- [课程源码：class25/Code03_LargestRectangleInHistogram.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class25/Code03_LargestRectangleInHistogram.java)。
- [LeetCode 原题 84. 柱状图中最大的矩形（Largest Rectangle in Histogram）](https://leetcode.com/problems/largest-rectangle-in-histogram/)

#### 思路

让每根柱子尝试作为矩形最低高度。单调递增栈保存尚未找到右边界的柱下标；遇到更低或等高柱时弹栈，用当前下标为右边界、弹出后栈顶为左边界，面积为高度×两边界间的宽度。

#### 代码答案

```java
package class25;

import java.util.Stack;

// 测试链接：https://leetcode.com/problems/largest-rectangle-in-histogram
public class Code03_LargestRectangleInHistogram {

	public static int largestRectangleArea1(int[] height) {
		if (height == null || height.length == 0) {
			return 0;
		}
		int maxArea = 0;
		Stack<Integer> stack = new Stack<Integer>();
		for (int i = 0; i < height.length; i++) {
			while (!stack.isEmpty() && height[i] <= height[stack.peek()]) {
				int j = stack.pop();
				// 弹出后剩余栈顶是该柱左侧限制边界，空栈用-1表示数组之前。
				int k = stack.isEmpty() ? -1 : stack.peek();
				// 两个边界本身不属于矩形，宽度为其距离减一。
				int curArea = (i - k - 1) * height[j];
				maxArea = Math.max(maxArea, curArea);
			}
			stack.push(i);
		}
		while (!stack.isEmpty()) {
			int j = stack.pop();
			// 弹出后剩余栈顶是该柱左侧限制边界，空栈用-1表示数组之前。
			int k = stack.isEmpty() ? -1 : stack.peek();
			int curArea = (height.length - k - 1) * height[j];
			maxArea = Math.max(maxArea, curArea);
		}
		return maxArea;
	}

	public static int largestRectangleArea2(int[] height) {
		if (height == null || height.length == 0) {
			return 0;
		}
		int N = height.length;
		int[] stack = new int[N];
		int si = -1;
		int maxArea = 0;
		for (int i = 0; i < height.length; i++) {
			while (si != -1 && height[i] <= height[stack[si]]) {
				int j = stack[si--];
				int k = si == -1 ? -1 : stack[si];
				// 两个边界本身不属于矩形，宽度为其距离减一。
				int curArea = (i - k - 1) * height[j];
				maxArea = Math.max(maxArea, curArea);
			}
			stack[++si] = i;
		}
		while (si != -1) {
			int j = stack[si--];
			int k = si == -1 ? -1 : stack[si];
			int curArea = (height.length - k - 1) * height[j];
			maxArea = Math.max(maxArea, curArea);
		}
		return maxArea;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：宽度是i-left-1，两边界不包含。结束时还要清栈，以N为右边界。相等柱可合并处理，但比较和边界归属要保持一致。

本条未附独立随机对数器。

#### 题解

**为什么正确**

弹出柱与两个边界之间没有更低柱，所以整段均能支撑该高度；跨过更低边界就不行。所有最优矩形都可把高度压到某根最低柱，因此枚举每根柱的最大支撑范围包含最优解。

**复杂度**

时间O(N)，栈空间O(N)。

**边界与易错点**

宽度是i-left-1，两边界不包含。结束时还要清栈，以N为右边界。相等柱可合并处理，但比较和边界归属要保持一致。

### 25.5 全 1 矩阵中的最大矩形

#### 题目

给定只含 `0` 和 `1` 的矩阵，返回只包含 `1` 的最大轴对齐矩形面积。

**输入、输出与约束**

输入矩形0/1字符网格；返回只含1的最大轴对齐矩形面积。

**函数签名（课程入口）**

```java
public static int maximalRectangle(char[][] map);
public static int maxRecFromBottom(int[] height);
```

**示例**

```text
输入：matrix=["11","11"]
输出：4
```

解释：第二行的直方图为[2,2]，得到面积4。

**出处与版本差异**

- [课程源码：class25/Code04_MaximalRectangle.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class25/Code04_MaximalRectangle.java)。
- [LeetCode 原题 85. 最大矩形（Maximal Rectangle）](https://leetcode.com/problems/maximal-rectangle/)

#### 思路

逐行作为矩形底边，height[j]记录该列到当前行为止连续1的个数；当前格为0则清零，为1则加一。每行的height形成直方图，调用单调栈求以这行为底的最大矩形。

#### 代码答案

```java
package class25;

import java.util.Stack;

// 测试链接：https://leetcode.com/problems/maximal-rectangle/
public class Code04_MaximalRectangle {

	public static int maximalRectangle(char[][] map) {
		if (map == null || map.length == 0 || map[0].length == 0) {
			return 0;
		}
		int maxArea = 0;
		int[] height = new int[map[0].length];
		for (int i = 0; i < map.length; i++) {
			for (int j = 0; j < map[0].length; j++) {
				// 当前列遇0中断连续高度，遇1延长以本行为底的直方图。
				height[j] = map[i][j] == '0' ? 0 : height[j] + 1;
			}
			// 把当前行作为底边，对连续1高度求直方图最大矩形。
			maxArea = Math.max(maxRecFromBottom(height), maxArea);
		}
		return maxArea;
	}

	// height是正方图数组
	public static int maxRecFromBottom(int[] height) {
		if (height == null || height.length == 0) {
			return 0;
		}
		int maxArea = 0;
		Stack<Integer> stack = new Stack<Integer>();
		for (int i = 0; i < height.length; i++) {
			while (!stack.isEmpty() && height[i] <= height[stack.peek()]) {
				int j = stack.pop();
				int k = stack.isEmpty() ? -1 : stack.peek();
				int curArea = (i - k - 1) * height[j];
				maxArea = Math.max(maxArea, curArea);
			}
			stack.push(i);
		}
		while (!stack.isEmpty()) {
			int j = stack.pop();
			int k = stack.isEmpty() ? -1 : stack.peek();
			int curArea = (height.length - k - 1) * height[j];
			maxArea = Math.max(maxArea, curArea);
		}
		return maxArea;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：每遇到0必须重置对应高度，不可继续累加。输入为字符矩阵时比较字符0而不是整数0。

本条未附独立随机对数器。

#### 题解

**为什么正确**

任一全1矩形都有某一行作为底边，它在每一列需要的高度不超过该行height。反之直方图中的合法矩形恰对应一个全1子矩阵，因此逐行取最大值涵盖所有二维矩形。

**复杂度**

R行C列，时间O(RC)，高度数组和单调栈空间O(C)。

**边界与易错点**

每遇到0必须重置对应高度，不可继续累加。输入为字符矩阵时比较字符0而不是整数0。

### 25.6 全 1 子矩形数量

#### 题目

给定二进制矩阵，返回元素全部为 `1` 的轴对齐子矩形数量。

**输入、输出与约束**

输入0/1整数矩阵；返回全部非空轴对齐全1子矩形数量。

**函数签名（课程入口）**

```java
public static int numSubmat(int[][] mat);
public static int countFromBottom(int[] height);
public static int num(int n);
```

**示例**

```text
输入：matrix=[[1,1],[1,1]]
输出：9
```

解释：四个1×1、两个1×2、两个2×1、一个2×2。

**出处与版本差异**

- [课程源码：class25/Code05_CountSubmatricesWithAllOnes.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class25/Code05_CountSubmatricesWithAllOnes.java)。
- [LeetCode 原题 1504. 统计全 1 子矩形（Count Submatrices With All Ones）](https://leetcode.com/problems/count-submatrices-with-all-ones/)

#### 思路

先逐行累积连续1高度，再统计该直方图以本行为底的全部矩形。对任意连续列区间，最小高度就是可选上边界数量。单调栈按高度层结算：宽度w的新增加高度层数h，对应 h×w(w+1)/2 个矩形。

#### 代码答案

```java
package class25;

// 测试链接：https://leetcode.com/problems/count-submatrices-with-all-ones
public class Code05_CountSubmatricesWithAllOnes {

	public static int numSubmat(int[][] mat) {
		if (mat == null || mat.length == 0 || mat[0].length == 0) {
			return 0;
		}
		int nums = 0;
		int[] height = new int[mat[0].length];
		for (int i = 0; i < mat.length; i++) {
			for (int j = 0; j < mat[0].length; j++) {
				height[j] = mat[i][j] == 0 ? 0 : height[j] + 1;
			}
			nums += countFromBottom(height);
		}
		return nums;

	}

	// 比如
	//              1
	//              1
	//              1         1
	//    1         1         1
	//    1         1         1
	//    1         1         1
	//
	//    2  ....   6   ....  9
	// 如上图，假设在6位置，1的高度为6
	// 在6位置的左边，离6位置最近、且小于高度6的位置是2，2位置的高度是3
	// 在6位置的右边，离6位置最近、且小于高度6的位置是9，9位置的高度是4
	// 此时我们求什么？
	// 1) 求在3~8范围上，必须以高度6作为高的矩形，有几个？
	// 2) 求在3~8范围上，必须以高度5作为高的矩形，有几个？
	// 也就是说，<=4的高度，一律不求
	// 那么，1) 求必须以位置6的高度6作为高的矩形，有几个？
	// 3..3  3..4  3..5  3..6  3..7  3..8
	// 4..4  4..5  4..6  4..7  4..8
	// 5..5  5..6  5..7  5..8
	// 6..6  6..7  6..8
	// 7..7  7..8
	// 8..8
	// 这么多！= 21 = (9 - 2 - 1) * (9 - 2) / 2
	// 这就是任何一个数字从栈里弹出的时候，计算矩形数量的方式
	public static int countFromBottom(int[] height) {
		if (height == null || height.length == 0) {
			return 0;
		}
		int nums = 0;
		int[] stack = new int[height.length];
		int si = -1;
		for (int i = 0; i < height.length; i++) {
			while (si != -1 && height[stack[si]] >= height[i]) {
				int cur = stack[si--];
				// 相等高度不应重复结算，只有真正降低才出现独立新增层。
				if (height[cur] > height[i]) {
					int left = si == -1 ? -1 : stack[si];
					int n = i - left - 1;
					int down = Math.max(left == -1 ? 0 : height[left], height[i]);
					// 新增高度层数乘覆盖宽度的连续列区间数，得到本次新增矩形数。
					nums += (height[cur] - down) * num(n);
				}

			}
			stack[++si] = i;
		}
		while (si != -1) {
			int cur = stack[si--];
			int left = si == -1  ? -1 : stack[si];
			int n = height.length - left - 1;
			int down = left == -1 ? 0 : height[left];
			// 新增高度层数乘覆盖宽度的连续列区间数，得到本次新增矩形数。
			nums += (height[cur] - down) * num(n);
		}
		return nums;
	}

	public static int num(int n) {
		return ((n * (1 + n)) >> 1);
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：不能把最大矩形面积相加当成矩形个数。等高弹栈时不重复结算同一高度层；三角数w(w+1)/2表示列区间个数。

本条未附独立随机对数器。

#### 题解

**为什么正确**

固定底行和左右列后，上边界可在最小连续高度范围内任选，每个选择形成唯一全1矩形。弹栈时只结算高于左右限制高度的新增层，低层留给更低柱统一统计，因此不重复覆盖。

**复杂度**

时间O(RC)，额外空间O(C)，答案可能需long扩展。

**边界与易错点**

不能把最大矩形面积相加当成矩形个数。等高弹栈时不重复结算同一高度层；三角数w(w+1)/2表示列区间个数。


<a id="course-26"></a>

## 第 26 课：子数组最小值与矩阵快速幂

### 26.1 子数组最小值之和

#### 题目

给定整数数组，求所有非空子数组最小值之和，并按题目要求取模。

**输入、输出与约束**

输入正整数数组；在线评测返回所有非空子数组最小值总和模1,000,000,007。

**函数签名（课程入口）**

```java
public static int subArrayMinSum1(int[] arr);
public static int subArrayMinSum2(int[] arr);
public static int sumSubarrayMins(int[] arr);
```

**示例**

```text
输入：arr=[2,2]
输出：6
```

解释：子数组为第一个[2]、第二个[2]、[2,2]，最小值总和2+2+2。

**出处与版本差异**

- [课程源码：class26/Code01_SumOfSubarrayMinimums.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class26/Code01_SumOfSubarrayMinimums.java)。
- [LeetCode 原题 907. 子数组的最小值之和（Sum of Subarray Minimums）](https://leetcode.com/problems/sum-of-subarray-minimums/)

#### 思路

1. 逐段找最小值会重复扫描。换成问每个位置 `i`：它作为某段指定最小值时，一共贡献多少次？答案是 `arr[i] × 可选左端点数 × 可选右端点数`。
2. 重复最小值必须明确归属。当前源码以左侧最近“小于等于”位置 L 和右侧最近“严格小于”位置 R 为界，因此包含相等最小值的区间归属于最左的那个位置。
3. 左端点可选 `L+1..i` 共 `i-L` 个，右端点可选 `i..R-1` 共 `R-i` 个，贡献为 `arr[i]*(i-L)*(R-i)`。单调栈在线性扫描中找出边界，每个下标只入栈、出栈一次。
4. 用 `[2,2]` 检查去重：第一个 2 负责 `[0,0]` 与 `[0,1]`，第二个只负责 `[1,1]`，总和 6。若左右都用严格边界会重复计数，若左右都用非严格边界会漏计。乘法在 long 中进行，最后按 1_000_000_007 取模。

#### 代码答案

```java
package class26;

// 测试链接：https://leetcode.com/problems/sum-of-subarray-minimums/
// subArrayMinSum1是暴力解
// subArrayMinSum2是最优解的思路
// sumSubarrayMins是最优解思路下的单调栈优化
// Leetcode上不要提交subArrayMinSum1、subArrayMinSum2方法，因为没有考虑取摸
// Leetcode上只提交sumSubarrayMins方法，时间复杂度O(N)，可以直接通过
public class Code01_SumOfSubarrayMinimums {

	public static int subArrayMinSum1(int[] arr) {
		int ans = 0;
		for (int i = 0; i < arr.length; i++) {
			for (int j = i; j < arr.length; j++) {
				int min = arr[i];
				for (int k = i + 1; k <= j; k++) {
					min = Math.min(min, arr[k]);
				}
				ans += min;
			}
		}
		return ans;
	}

	// 没有用单调栈
	public static int subArrayMinSum2(int[] arr) {
		// left[i] = x : arr[i]左边，离arr[i]最近，<=arr[i]，位置在x
		int[] left = leftNearLessEqual2(arr);
		// right[i] = y : arr[i]右边，离arr[i]最近，< arr[i],的数，位置在y
		int[] right = rightNearLess2(arr);
		int ans = 0;
		for (int i = 0; i < arr.length; i++) {
			int start = i - left[i];
			int end = right[i] - i;
			ans += start * end * arr[i];
		}
		return ans;
	}

	public static int[] leftNearLessEqual2(int[] arr) {
		int N = arr.length;
		int[] left = new int[N];
		for (int i = 0; i < N; i++) {
			int ans = -1;
			for (int j = i - 1; j >= 0; j--) {
				if (arr[j] <= arr[i]) {
					ans = j;
					break;
				}
			}
			left[i] = ans;
		}
		return left;
	}

	public static int[] rightNearLess2(int[] arr) {
		int N = arr.length;
		int[] right = new int[N];
		for (int i = 0; i < N; i++) {
			int ans = N;
			for (int j = i + 1; j < N; j++) {
				if (arr[i] > arr[j]) {
					ans = j;
					break;
				}
			}
			right[i] = ans;
		}
		return right;
	}

	public static int sumSubarrayMins(int[] arr) {
		int[] stack = new int[arr.length];
		int[] left = nearLessEqualLeft(arr, stack);
		int[] right = nearLessRight(arr, stack);
		long ans = 0;
		for (int i = 0; i < arr.length; i++) {
			// 起点可以选left[i]+1到i，共i-left[i]种，左边界本身不能跨过。
			long start = i - left[i];
			// 终点可以选i到right[i]-1，共right[i]-i种。
			long end = right[i] - i;
			// 起终点独立选择，每个拥有的子数组贡献一次arr[i]；使用long承接乘积。
			ans += start * end * (long) arr[i];
			ans %= 1000000007;
		}
		return (int) ans;
	}

	public static int[] nearLessEqualLeft(int[] arr, int[] stack) {
		int N = arr.length;
		int[] left = new int[N];
		int size = 0;
		for (int i = N - 1; i >= 0; i--) {
			// 左边界允许相等，把重复最小值区间归给最左出现者。
			while (size != 0 && arr[i] <= arr[stack[size - 1]]) {
				left[stack[--size]] = i;
			}
			stack[size++] = i;
		}
		while (size != 0) {
			left[stack[--size]] = -1;
		}
		return left;
	}

	public static int[] nearLessRight(int[] arr, int[] stack) {
		int N = arr.length;
		int[] right = new int[N];
		int size = 0;
		for (int i = 0; i < N; i++) {
			// 右边界必须严格更小，与左侧非严格规则配对去重。
			while (size != 0 && arr[stack[size - 1]] > arr[i]) {
				right[stack[--size]] = i;
			}
			stack[size++] = i;
		}
		while (size != 0) {
			right[stack[--size]] = N;
		}
		return right;
	}

	public static int[] randomArray(int len, int maxValue) {
		int[] ans = new int[len];
		for (int i = 0; i < len; i++) {
			ans[i] = (int) (Math.random() * maxValue) + 1;
		}
		return ans;
	}

	public static void printArray(int[] arr) {
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

}
```

---

##### 新增对数器（固定输入或固定随机种子）

逐区间维护最小值的O(N²)基准，随机小值域强调重复元素、平台和边界归属，避免原测试十万轮三重循环的高成本。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
    java.util.Random random = new java.util.Random(20260905L);
    for (int round = 0; round < 5000; round++) {
        int[] arr = new int[1 + random.nextInt(25)];
        for (int i = 0; i < arr.length; i++) arr[i] = 1 + random.nextInt(5);
        // 小值域产生大量相等最小值，专门检验左右边界的去重规则。
        long expected = 0;
        for (int l = 0; l < arr.length; l++) {
            int min = Integer.MAX_VALUE;
            for (int r = l; r < arr.length; r++) { min = Math.min(min, arr[r]); expected += min; }
        }
        int actual = sumSubarrayMins(arr);
        if (actual != expected % 1000000007L)
            throw new AssertionError(java.util.Arrays.toString(arr) + " expected=" + expected + " actual=" + actual);
    }
    System.out.println("PASS: SumOfSubarrayMinimums, seed=20260905");
}
```

#### 题解

**为什么正确**

边界内没有更小值，且不能跨过左边的相等值，因此每个子数组把最小值归给最左出现的那个位置，归属唯一。起点和终点可独立选择，乘法原理给出区间数量。以[2,2]为例，跨两元素区间归第一个2，第二个2的left被第一个2挡住。

**复杂度**

暴力subArrayMinSum1为O(N³)，逐位置扫描边界版O(N²)，单调栈版O(N)；优化额外空间O(N)。

**边界与易错点**

两边都严格会重复计数，两边都非严格会漏掉跨相等值区间。乘法先提升long再取模；前两个基准不取模，仅适合结果不大的对拍输入。

### 26.2 斐波那契及同类线性递推

#### 题目

实现斐波那契、台阶与母牛繁殖三个固定阶线性递推问题，比较暴力递归、线性迭代和矩阵快速幂。数值范围须保证源码 int 结果不溢出。

**输入、输出与约束**

按对应方法输入正整数n，返回相应固定阶递推值；必须限制结果在int范围内或扩展数值类型。

**函数签名（课程入口）**

```java
public static int f1(int n);
public static int f2(int n);
public static int f3(int n);
public static int[][] matrixPower(int[][] m, int p);
public static int[][] product(int[][] a, int[][] b);
public static int s1(int n);
```

**示例**

```text
输入：斐波那契n=6，F(1)=F(2)=1
输出：8
```

解释：序列1、1、2、3、5、8。

**出处与版本差异**

- [课程源码：class26/Code02_FibonacciProblem.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class26/Code02_FibonacciProblem.java)。
- [LeetCode 对应题 509. 斐波那契数（Fibonacci Number）](https://leetcode.com/problems/fibonacci-number/)

课程版本说明：

- LeetCode 509：斐波那契实现对应；本节还包含同类递推。

#### 思路

斐波那契满足F(n)=F(n-1)+F(n-2)，可用两个变量线性迭代；把状态[F(n),F(n-1)]写成固定2×2矩阵乘法，就能用二进制快速幂跳过大量重复步。课程还给出楼梯型递推和母牛繁殖等不同阶数的矩阵模型，每个模型需独立确定初值与转移矩阵。

#### 代码答案

```java
package class26;

public class Code02_FibonacciProblem {

	public static int f1(int n) {
		if (n < 1) {
			return 0;
		}
		if (n == 1 || n == 2) {
			return 1;
		}
		return f1(n - 1) + f1(n - 2);
	}

	public static int f2(int n) {
		if (n < 1) {
			return 0;
		}
		if (n == 1 || n == 2) {
			return 1;
		}
		int res = 1;
		int pre = 1;
		int tmp = 0;
		for (int i = 3; i <= n; i++) {
			tmp = res;
			res = res + pre;
			pre = tmp;
		}
		return res;
	}

	// O(logN)
	public static int f3(int n) {
		if (n < 1) {
			return 0;
		}
		if (n == 1 || n == 2) {
			return 1;
		}
		// [ 1 ,1 ]
		// [ 1, 0 ]
		int[][] base = {
				{ 1, 1 },
				{ 1, 0 }
				};
		int[][] res = matrixPower(base, n - 2);
		return res[0][0] + res[1][0];
	}

	public static int[][] matrixPower(int[][] m, int p) {
		int[][] res = new int[m.length][m[0].length];
		for (int i = 0; i < res.length; i++) {
			// 单位矩阵是矩阵乘法的单位元，用作尚未选取任何幂时的初值。
			res[i][i] = 1;
		}
		// res = 矩阵中的1
		int[][] t = m;// 矩阵1次方
		// 从低到高检查指数的二进制位，每轮处理一个幂位。
		for (; p != 0; p >>= 1) {
			// 当前指数位为1，把对应的2的幂次矩阵乘入答案。
			if ((p & 1) != 0) {
				// 累计选中的矩阵幂；乘法次序必须保持一致。
				res = product(res, t);
			}
			// 平方后从M的2^k次幂推进到2^(k+1)次幂。
			t = product(t, t);
		}
		return res;
	}

	// 两个矩阵乘完之后的结果返回
	public static int[][] product(int[][] a, int[][] b) {
		int n = a.length;
		int m = b[0].length;
		int k = a[0].length; // a的列数同时也是b的行数
		int[][] ans = new int[n][m];
		for(int i = 0 ; i < n; i++) {
			for(int j = 0 ; j < m;j++) {
				for(int c = 0; c < k; c++) {
					// 枚举中间维度c，累加从行i到列j的矩阵乘积项。
					ans[i][j] += a[i][c] * b[c][j];
				}
			}
		}
		return ans;
	}

	public static int s1(int n) {
		if (n < 1) {
			return 0;
		}
		if (n == 1 || n == 2) {
			return n;
		}
		return s1(n - 1) + s1(n - 2);
	}

	public static int s2(int n) {
		if (n < 1) {
			return 0;
		}
		if (n == 1 || n == 2) {
			return n;
		}
		int res = 2;
		int pre = 1;
		int tmp = 0;
		for (int i = 3; i <= n; i++) {
			tmp = res;
			res = res + pre;
			pre = tmp;
		}
		return res;
	}

	public static int s3(int n) {
		if (n < 1) {
			return 0;
		}
		if (n == 1 || n == 2) {
			return n;
		}
		int[][] base = { { 1, 1 }, { 1, 0 } };
		int[][] res = matrixPower(base, n - 2);
		return 2 * res[0][0] + res[1][0];
	}

	public static int c1(int n) {
		if (n < 1) {
			return 0;
		}
		if (n == 1 || n == 2 || n == 3) {
			return n;
		}
		return c1(n - 1) + c1(n - 3);
	}

	public static int c2(int n) {
		if (n < 1) {
			return 0;
		}
		if (n == 1 || n == 2 || n == 3) {
			return n;
		}
		int res = 3;
		int pre = 2;
		int prepre = 1;
		int tmp1 = 0;
		int tmp2 = 0;
		for (int i = 4; i <= n; i++) {
			tmp1 = res;
			tmp2 = pre;
			res = res + prepre;
			pre = tmp1;
			prepre = tmp2;
		}
		return res;
	}

	public static int c3(int n) {
		if (n < 1) {
			return 0;
		}
		if (n == 1 || n == 2 || n == 3) {
			return n;
		}
		int[][] base = {
				{ 1, 1, 0 },
				{ 0, 0, 1 },
				{ 1, 0, 0 } };
		int[][] res = matrixPower(base, n - 3);
		return 3 * res[0][0] + 2 * res[1][0] + res[2][0];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int n = 19;
		System.out.println(f1(n));
		System.out.println(f2(n));
		System.out.println(f3(n));
		System.out.println("===");

		System.out.println(s1(n));
		System.out.println(s2(n));
		System.out.println(s3(n));
		System.out.println("===");

		System.out.println(c1(n));
		System.out.println(c2(n));
		System.out.println(c3(n));
		System.out.println("===");

	}
```

#### 题解

**为什么正确**

转移矩阵的一次乘法精确执行一步线性递推，连续p步等价于矩阵p次幂。快速幂按p的二进制位分解乘积，并通过平方复用2、4、8…步转移，因此与逐步递推完全一致。

**复杂度**

暴力递归指数时间，迭代O(N)时间/O(1)空间；固定d阶矩阵快速幂O(d³ log N)时间、O(d²)空间。

**边界与易错点**

幂次数与所选初始状态相匹配，常见为n-2而不是n。单位矩阵是0次幂。原代码使用int，斐波那契从F(47)起溢出，快速幂不会解决数值范围问题。

### 26.3 0 左边必须有 1 的二进制字符串数量

#### 题目

给定字符串长度 `n`，返回满足每个字符 `0` 左侧紧邻位置必须为 `1` 的二进制字符串数量。

**输入、输出与约束**

输入n≥1；返回每个0的左邻字符都为1的长度n二进制字符串数。

**函数签名（课程入口）**

```java
public static int getNum1(int n);
public static int getNum2(int n);
public static int getNum3(int n);
public static int fi(int n);
public static int[][] matrixPower(int[][] m, int p);
public static int[][] product(int[][] a, int[][] b);
```

**示例**

```text
输入：n=3
输出：3
```

解释：合法字符串为111、110、101。

**出处与版本差异**

- [课程源码：class26/Code03_ZeroLeftOneStringNumber.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class26/Code03_ZeroLeftOneStringNumber.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

合法字符串首位必须为1。若以1后接另一1，可把首个1独立拿走；若接0，则前缀10必须整体拿走。因此长度n的方案数满足f(n)=f(n-1)+f(n-2)，初值f(1)=1、f(2)=2。可用递归、迭代或2阶矩阵快速幂。

#### 代码答案

```java
package class26;

public class Code03_ZeroLeftOneStringNumber {

	public static int getNum1(int n) {
		if (n < 1) {
			return 0;
		}
		return process(1, n);
	}

	public static int process(int i, int n) {
		if (i == n - 1) {
			return 2;
		}
		if (i == n) {
			return 1;
		}
		// 接一个1或一组10，分别消耗1或2个位置，方案互斥相加。
		return process(i + 1, n) + process(i + 2, n);
	}

	public static int getNum2(int n) {
		if (n < 1) {
			return 0;
		}
		if (n == 1) {
			return 1;
		}
		int pre = 1;
		int cur = 1;
		int tmp = 0;
		for (int i = 2; i < n + 1; i++) {
			tmp = cur;
			cur += pre;
			pre = tmp;
		}
		return cur;
	}

	public static int getNum3(int n) {
		if (n < 1) {
			return 0;
		}
		if (n == 1 || n == 2) {
			return n;
		}
		int[][] base = { { 1, 1 }, { 1, 0 } };
		int[][] res = matrixPower(base, n - 2);
		return 2 * res[0][0] + res[1][0];
	}

	public static int fi(int n) {
		if (n < 1) {
			return 0;
		}
		if (n == 1 || n == 2) {
			return 1;
		}
		int[][] base = { { 1, 1 },
				         { 1, 0 } };
		int[][] res = matrixPower(base, n - 2);
		return res[0][0] + res[1][0];
	}

	public static int[][] matrixPower(int[][] m, int p) {
		int[][] res = new int[m.length][m[0].length];
		for (int i = 0; i < res.length; i++) {
			// 单位矩阵是矩阵乘法的单位元，用作尚未选取任何幂时的初值。
			res[i][i] = 1;
		}
		int[][] tmp = m;
		// 从低到高检查指数的二进制位，每轮处理一个幂位。
		for (; p != 0; p >>= 1) {
			// 当前指数位为1，把对应的2的幂次矩阵乘入答案。
			if ((p & 1) != 0) {
				// 当前位需要该幂，将它乘入累计结果。
				res = product(res, tmp);
			}
			// 幂矩阵平方，对应下一位指数的权重。
			tmp = product(tmp, tmp);
		}
		return res;
	}

	// 两个矩阵乘完之后的结果返回
	public static int[][] product(int[][] a, int[][] b) {
		int n = a.length;
		int m = b[0].length;
		int k = a[0].length; // a的列数同时也是b的行数
		int[][] ans = new int[n][m];
		for(int i = 0 ; i < n; i++) {
			for(int j = 0 ; j < m;j++) {
				for(int c = 0; c < k; c++) {
					// 枚举中间维度c，累加从行i到列j的矩阵乘积项。
					ans[i][j] += a[i][c] * b[c][j];
				}
			}
		}
		return ans;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		for (int i = 0; i != 20; i++) {
			System.out.println(getNum1(i));
			System.out.println(getNum2(i));
			System.out.println(getNum3(i));
			System.out.println("===================");
		}

	}
```

#### 题解

**为什么正确**

合法字符串可唯一按开头1或10分解，去掉该前缀后剩余仍满足相同约束；两类首部不同、不重计，长度减少分别为1和2，所以递推成立。

**复杂度**

递归指数时间，迭代O(N)，固定矩阵快速幂O(log N)；后两者额外空间O(1)。

**边界与易错点**

要求0紧挨着的左边是1，不是只要前面某处出现过1。空字符串的公开返回约定以本实现n≥1为准；int方案数会随n增长溢出。


<a id="course-27"></a>

## 第 27 课：KMP 字符串匹配算法

### 27.1 KMP 字符串匹配

#### 题目

给定文本串和模式串，返回模式串第一次出现在文本串中的起始下标；不存在时返回 `-1`。

**输入、输出与约束**

输入文本与非空模式字符串；返回首次匹配起点，无匹配或课程非法输入返回-1。

**函数签名（课程入口）**

```java
public static int getIndexOf(String s1, String s2);
```

**示例**

```text
输入：text="ababc", pattern="abc"
输出：2
```

解释：匹配到前两个ab后失配，复用边界信息从下标2继续。

**出处与版本差异**

- [课程源码：class27/Code01_KMP.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class27/Code01_KMP.java)。
- [LeetCode 对应题 28. 找出字符串中第一个匹配项的下标（Find the Index of the First Occurrence in a String）](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/)

#### 思路

KMP（Knuth–Morris–Pratt）维护文本位置x和模式位置y；前y个字符已经匹配。失配时，next[y]给出已匹配前缀的最长真前后缀长度，把y退到该长度而不回退x；若y已经为0仍失配，才推进文本。

#### 代码答案

```java
package class27;

public class Code01_KMP {

	public static int getIndexOf(String s1, String s2) {
		if (s1 == null || s2 == null || s2.length() < 1 || s1.length() < s2.length()) {
			return -1;
		}
		char[] str1 = s1.toCharArray();
		char[] str2 = s2.toCharArray();
		int x = 0;
		int y = 0;
		// O(M) m <= n
		int[] next = getNextArray(str2);
		// O(N)
		while (x < str1.length && y < str2.length) {
			if (str1[x] == str2[y]) {
				x++;
				y++;
			} else if (next[y] == -1) { // y == 0
				x++;
			} else {
				// 只回退模式位置，已经相同的文本后缀作为新的匹配前缀复用。
				y = next[y];
			}
		}
		return y == str2.length ? x - y : -1;
	}

	public static int[] getNextArray(char[] str2) {
		if (str2.length == 1) {
			return new int[] { -1 };
		}
		int[] next = new int[str2.length];
		// 首字符失配没有可保留前缀，用-1标记必须推进文本。
		next[0] = -1;
		next[1] = 0;
		int i = 2; // 目前在哪个位置上求next数组的值
		int cn = 0; // 当前是哪个位置的值再和i-1位置的字符比较
		while (i < next.length) {
			if (str2[i - 1] == str2[cn]) { // 配成功的时候
				next[i++] = ++cn;
			} else if (cn > 0) {
				// 当前候选前后缀无法延长，尝试它自身的更短边界。
				cn = next[cn];
			} else {
				next[i++] = 0;
			}
		}
		return next;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static String getRandomString(int possibilities, int size) {
		char[] ans = new char[(int) (Math.random() * size) + 1];
		for (int i = 0; i < ans.length; i++) {
			ans[i] = (char) ((int) (Math.random() * possibilities) + 'a');
		}
		return String.valueOf(ans);
	}

public static void main(String[] args) {
		int possibilities = 5;
		int strSize = 20;
		int matchSize = 5;
		int testTimes = 5000000;
		System.out.println("test begin");
		for (int i = 0; i < testTimes; i++) {
			String str = getRandomString(possibilities, strSize);
			String match = getRandomString(possibilities, matchSize);
			if (getIndexOf(str, match) != str.indexOf(match)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("test finish");
	}
```

#### 题解

**为什么正确**

失配前的匹配区间同时是模式前缀。若保留其中一个后缀作为下一轮匹配前缀，该后缀必须等于模式的某个前缀；最长真前后缀是最大可保留部分，沿next链依次尝试就不会跳过可能起点。

**复杂度**

构造next O(M)，匹配O(N)，总时间O(N+M)，额外空间O(M)。

**边界与易错点**

课程next[0]=-1、next[1]=0，对应已匹配长度的约定；不要与前缀函数下标混用。空模式课程返回-1，与String.indexOf空串返回0不同，对拍需遵守入口约定。

### 27.2 判断一棵树是否包含另一棵完整子树

#### 题目

给定两棵二叉树 `root` 和 `subRoot`，判断 `subRoot` 是否与 `root` 的某棵子树结构和值完全相同。

**输入、输出与约束**

输入两棵二叉树；判断小树是否等于大树的某棵完整子树，课程空小树视为匹配。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：root=[3,4,5,1,2], subRoot=[4,1,2]
输出：true
```

解释：以节点4为根的完整子树与小树一致。

**出处与版本差异**

- [课程源码：class27/Code02_TreeEqual.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class27/Code02_TreeEqual.java)。
- [LeetCode 原题 572. 另一棵树的子树（Subtree of Another Tree）](https://leetcode.com/problems/subtree-of-another-tree/)

#### 思路

将两棵树做带空标记的前序序列化，然后用KMP查找小树序列是否连续出现在大树序列中。与只比较遍历值不同，null标记保留每个节点左右孩子是否存在。

#### 代码答案

```java
package class27;

import java.util.ArrayList;

public class Code02_TreeEqual {

	public static class TreeNode {
		public int val;
		public TreeNode left;
		public TreeNode right;

		public TreeNode(int v) {
			val = v;
		}
	}

	// 测试链接 : https://leetcode.cn/problems/subtree-of-another-tree/
	// 提交如下代码可以直接通过
	public static boolean isSubtree(TreeNode big, TreeNode small) {
		if (small == null) {
			return true;
		}
		if (big == null) {
			return false;
		}
		ArrayList<String> b = preSerial(big);
		ArrayList<String> s = preSerial(small);
		String[] str = new String[b.size()];
		for (int i = 0; i < str.length; i++) {
			str[i] = b.get(i);
		}

		String[] match = new String[s.size()];
		for (int i = 0; i < match.length; i++) {
			match[i] = s.get(i);
		}
		return getIndexOf(str, match) != -1;
	}

	public static ArrayList<String> preSerial(TreeNode head) {
		ArrayList<String> ans = new ArrayList<>();
		pres(head, ans);
		return ans;
	}

	public static void pres(TreeNode head, ArrayList<String> ans) {
		if (head == null) {
			// 空孩子也编码，区分仅值序列相同但树形不同的情况。
			ans.add(null);
		} else {
			ans.add(String.valueOf(head.val));
			pres(head.left, ans);
			pres(head.right, ans);
		}
	}

	public static int getIndexOf(String[] str1, String[] str2) {
		if (str1 == null || str2 == null || str1.length < 1 || str1.length < str2.length) {
			return -1;
		}
		int x = 0;
		int y = 0;
		int[] next = getNextArray(str2);
		while (x < str1.length && y < str2.length) {
			// 比较序列标记时同时支持节点值字符串与null标记。
			if (isEqual(str1[x], str2[y])) {
				x++;
				y++;
			} else if (next[y] == -1) {
				x++;
			} else {
				y = next[y];
			}
		}
		return y == str2.length ? x - y : -1;
	}

	public static int[] getNextArray(String[] ms) {
		if (ms.length == 1) {
			return new int[] { -1 };
		}
		int[] next = new int[ms.length];
		next[0] = -1;
		next[1] = 0;
		int i = 2;
		int cn = 0;
		while (i < next.length) {
			if (isEqual(ms[i - 1], ms[cn])) {
				next[i++] = ++cn;
			} else if (cn > 0) {
				cn = next[cn];
			} else {
				next[i++] = 0;
			}
		}
		return next;
	}

	public static boolean isEqual(String a, String b) {
		if (a == null && b == null) {
			return true;
		} else {
			if (a == null || b == null) {
				return false;
			} else {
				return a.equals(b);
			}
		}
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：这题匹配完整子树，不允许只取部分后代，标题中的“拓扑结构”容易误解。null要用安全比较；不应把整数直接拼成无分隔字符串。

本条未附独立随机对数器。

#### 题解

**为什么正确**

前序遍历使一棵子树的完整序列连续出现。带空标记的编码又能唯一决定树形，因此一段完整匹配既对应相同值，也对应相同结构，恰好是完整子树匹配。

**复杂度**

大树N、小树M，时间O(N+M)，序列与next空间O(N+M)，另有序列化递归栈。

**边界与易错点**

这题匹配完整子树，不允许只取部分后代，标题中的“拓扑结构”容易误解。null要用安全比较；不应把整数直接拼成无分隔字符串。

### 27.3 判断字符串是否互为旋转串

#### 题目

给定两个字符串，判断其中一个是否可以通过把另一个字符串的前缀移动到末尾得到。

**输入、输出与约束**

输入两个非空字符串，返回是否互为循环旋转；null与长度不等返回false。

**函数签名（课程入口）**

```java
public static boolean isRotation(String a, String b);
```

**示例**

```text
输入：a="abcd", b="cdab"
输出：true
```

解释：从a+a="abcdabcd"的下标2起可取到cdab。

**出处与版本差异**

- [课程源码：class27/Code03_IsRotation.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class27/Code03_IsRotation.java)。
- [LeetCode 原题 796. 旋转字符串（Rotate String）](https://leetcode.com/problems/rotate-string/)

#### 思路

先检查两串长度相等。把第一个串重复两遍得到a+a，再用KMP判断第二个串是否作为子串出现。切点前的前缀移到末尾，相当于从双倍串的某个中间位置开始读取原长度。

#### 代码答案

```java
package class27;

public class Code03_IsRotation {

	public static boolean isRotation(String a, String b) {
		if (a == null || b == null || a.length() != b.length()) {
			return false;
		}
		// 双倍串覆盖所有可能切点形成的长度不变旋转。
		String b2 = b + b;
		// 只需判断是否匹配，不要求返回旋转切点。
		return getIndexOf(b2, a) != -1;
	}

	// KMP Algorithm
	public static int getIndexOf(String s, String m) {
		if (s.length() < m.length()) {
			return -1;
		}
		char[] ss = s.toCharArray();
		char[] ms = m.toCharArray();
		int si = 0;
		int mi = 0;
		int[] next = getNextArray(ms);
		while (si < ss.length && mi < ms.length) {
			if (ss[si] == ms[mi]) {
				si++;
				mi++;
			} else if (next[mi] == -1) {
				si++;
			} else {
				mi = next[mi];
			}
		}
		return mi == ms.length ? si - mi : -1;
	}

	public static int[] getNextArray(char[] ms) {
		if (ms.length == 1) {
			return new int[] { -1 };
		}
		int[] next = new int[ms.length];
		next[0] = -1;
		next[1] = 0;
		int pos = 2;
		int cn = 0;
		while (pos < next.length) {
			if (ms[pos - 1] == ms[cn]) {
				next[pos++] = ++cn;
			} else if (cn > 0) {
				cn = next[cn];
			} else {
				next[pos++] = 0;
			}
		}
		return next;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		String str1 = "yunzuocheng";
		String str2 = "zuochengyun";
		System.out.println(isRotation(str1, str2));

	}
```

#### 题解

**为什么正确**

任意旋转都能在a+a中连续取到；反之长度相同的b若在双倍串中出现，其起点对原长度取模就是一个旋转切点，因此两方向等价。

**复杂度**

时间O(N)，双倍串与KMP辅助空间O(N)。

**边界与易错点**

不等长时立即失败。课程空字符串最终受KMP空模式约定影响，返回false；若采用数学上的空串自旋转约定，应单独处理。


<a id="course-28"></a>

## 第 28 课：Manacher 回文算法

### 28.1 最长回文子串长度

#### 题目

给定字符串，返回其最长回文子串的长度。

**输入、输出与约束**

输入字符串；返回最长连续回文子串长度，空串返回0。

**函数签名（课程入口）**

```java
public static int manacher(String s);
public static char[] manacherString(String str);
```

**示例**

```text
输入：s="abba"
输出：4
```

解释：转换串中心位于两个b之间，统一形式得到偶数长度回文。

**出处与版本差异**

- [课程源码：class28/Code01_Manacher.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class28/Code01_Manacher.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

在字符之间及两端加入分隔符，统一奇偶回文。pArr[i]表示含中心的回文半径，R是已知最右回文的右开边界，C是对应中心。i<R时从镜像2C-i继承不越过R的半径，再向外比较；若超过R则更新C、R。

#### 代码答案

```java
package class28;

public class Code01_Manacher {

	public static int manacher(String s) {
		if (s == null || s.length() == 0) {
			return 0;
		}
		// "12132" -> "#1#2#1#3#2#"
		char[] str = manacherString(s);
		// 回文半径的大小
		int[] pArr = new int[str.length];
		int C = -1;
		// 讲述中：R代表最右的扩成功的位置
		// coding：最右的扩成功位置的，再下一个位置
		int R = -1;
		int max = Integer.MIN_VALUE;
		for (int i = 0; i < str.length; i++) { // 0 1 2
			// R第一个违规的位置，i>= R
			// i位置扩出来的答案，i位置扩的区域，至少是多大。
			// 镜像已知半径最多复用到当前右边界，越界部分必须另行比较。
			pArr[i] = R > i ? Math.min(pArr[2 * C - i], R - i) : 1;
			while (i + pArr[i] < str.length && i - pArr[i] > -1) {
				if (str[i + pArr[i]] == str[i - pArr[i]])
					pArr[i]++;
				else {
					break;
				}
			}
			if (i + pArr[i] > R) {
				// 扩展后的回文右端更远，更新右开边界。
				R = i + pArr[i];
				C = i;
			}
			max = Math.max(max, pArr[i]);
		}
		// 转换串的含中心半径减1，正好对应原串的回文字符数。
		return max - 1;
	}

	public static char[] manacherString(String str) {
		char[] charArr = str.toCharArray();
		char[] res = new char[str.length() * 2 + 1];
		int index = 0;
		for (int i = 0; i != res.length; i++) {
			res[i] = (i & 1) == 0 ? '#' : charArr[index++];
		}
		return res;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int right(String s) {
		if (s == null || s.length() == 0) {
			return 0;
		}
		char[] str = manacherString(s);
		int max = 0;
		for (int i = 0; i < str.length; i++) {
			int L = i - 1;
			int R = i + 1;
			while (L >= 0 && R < str.length && str[L] == str[R]) {
				L--;
				R++;
			}
			max = Math.max(max, R - L - 1);
		}
		return max / 2;
	}

	// for test
	public static String getRandomString(int possibilities, int size) {
		char[] ans = new char[(int) (Math.random() * size) + 1];
		for (int i = 0; i < ans.length; i++) {
			ans[i] = (char) ((int) (Math.random() * possibilities) + 'a');
		}
		return String.valueOf(ans);
	}

public static void main(String[] args) {
		int possibilities = 5;
		int strSize = 20;
		int testTimes = 5000000;
		System.out.println("test begin");
		for (int i = 0; i < testTimes; i++) {
			String str = getRandomString(possibilities, strSize);
			if (manacher(str) != right(str)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("test finish");
	}
```

#### 题解

**为什么正确**

在已知大回文内，镜像位置与当前位置的对应字符相同，故镜像半径位于边界内的部分可直接复用。边界外没有已知信息，必须真实比较。每次成功越界扩展都会推动R，累计扩展次数线性。

**复杂度**

时间O(N)，半径与转换串空间O(N)。

**边界与易错点**

R为右开边界，所以镜像可继承长度最多R-i。课程最终原串回文长度是最大半径减1；最长子串长度与最长回文子序列完全不同。

### 28.2 末尾最少添加字符构成回文串

#### 题目

给定字符串，只允许在末尾追加字符，返回使整个字符串成为回文串所需追加的最短字符串。

**输入、输出与约束**

输入非空字符串；返回使其成为回文的最短末尾追加串。

**函数签名（课程入口）**

```java
public static String shortestEnd(String s);
public static char[] manacherString(String str);
```

**示例**

```text
输入：s="abcc"
输出："ba"
```

解释：最长回文后缀cc保持不动，反转前缀ab得到ba，整串为abccba。

**出处与版本差异**

- [课程源码：class28/Code02_AddShortestEnd.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class28/Code02_AddShortestEnd.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

只能在末尾加字符时，应保留最长回文后缀，将它前面未匹配的前缀反向追加。用Manacher扫描，第一次发现右边界达到转换串末尾的回文，其中心最靠左，因而是最长回文后缀。

#### 代码答案

```java
package class28;

public class Code02_AddShortestEnd {

	public static String shortestEnd(String s) {
		if (s == null || s.length() == 0) {
			return null;
		}
		char[] str = manacherString(s);
		int[] pArr = new int[str.length];
		int C = -1;
		int R = -1;
		int maxContainsEnd = -1;
		for (int i = 0; i != str.length; i++) {
			pArr[i] = R > i ? Math.min(pArr[2 * C - i], R - i) : 1;
			while (i + pArr[i] < str.length && i - pArr[i] > -1) {
				if (str[i + pArr[i]] == str[i - pArr[i]])
					pArr[i]++;
				else {
					break;
				}
			}
			if (i + pArr[i] > R) {
				R = i + pArr[i];
				C = i;
			}
			// 首次触及末尾的回文对应最靠左中心，也就是最长回文后缀。
			if (R == str.length) {
				maxContainsEnd = pArr[i];
				break;
			}
		}
		// 扣掉已成回文的后缀长度，只为剩余前缀分配反向追加空间。
		char[] res = new char[s.length() - maxContainsEnd + 1];
		for (int i = 0; i < res.length; i++) {
			// 跳过分隔符读取前缀字符，反向写到追加串。
			res[res.length - 1 - i] = str[i * 2 + 1];
		}
		return String.valueOf(res);
	}

	public static char[] manacherString(String str) {
		char[] charArr = str.toCharArray();
		char[] res = new char[str.length() * 2 + 1];
		int index = 0;
		for (int i = 0; i != res.length; i++) {
			res[i] = (i & 1) == 0 ? '#' : charArr[index++];
		}
		return res;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		String str1 = "abcd123321";
		System.out.println(shortestEnd(str1));
	}
```

#### 题解

**为什么正确**

原串中未被追加字符配对的剩余部分必须自身为回文，所以合法拼接一定保留一个回文后缀。后缀越长，需要镜像追加的前缀越短；反转前缀追加后左右对称，给出达到下界的构造。

**复杂度**

时间O(N)，额外空间O(N)，返回追加部分长度≤N-1。

**边界与易错点**

返回的是需要追加的字符串，不是完成后的整串。原串已经回文时返回空字符串；null或空输入源码返回null。


<a id="course-29"></a>

## 第 29 课：选择算法、Top K 与蓄水池抽样

### 29.1 数组中第 K 小的数

#### 题目

给定无序整数数组和 `k`，返回数组中第 `k` 小的元素。

**输入、输出与约束**

输入非空数组及1≤k≤N；返回第k小值。

**函数签名（课程入口）**

```java
public int compare(Integer o1, Integer o2);
public static int minKth1(int[] arr, int k);
public static int minKth2(int[] array, int k);
public static int minKth3(int[] array, int k);
public static int bfprt(int[] arr, int L, int R, int index);
public static int medianOfMedians(int[] arr, int L, int R);
```

**示例**

```text
输入：arr=[7,2,5,2,9], k=3
输出：5
```

解释：升序为[2,2,5,7,9]，重复值分别占秩。

**出处与版本差异**

- [课程源码：class29/Code01_FindMinKth.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class29/Code01_FindMinKth.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先可用大小k的大根堆保留最小k个，堆顶为第k小。快速选择把数组三向划分，只递归包含目标秩的一侧；随机基准取得期望线性时间。BFPRT（Blum–Floyd–Pratt–Rivest–Tarjan）每5个数取中位数，再递归取这些中位数的中位数作为基准。

#### 代码答案

```java
package class29;

import java.util.Comparator;
import java.util.PriorityQueue;

public class Code01_FindMinKth {

	public static class MaxHeapComparator implements Comparator<Integer> {

		@Override
		public int compare(Integer o1, Integer o2) {
			return o2 - o1;
		}

	}

	// 利用大根堆，时间复杂度O(N*logK)
	public static int minKth1(int[] arr, int k) {
		PriorityQueue<Integer> maxHeap = new PriorityQueue<>(new MaxHeapComparator());
		for (int i = 0; i < k; i++) {
			maxHeap.add(arr[i]);
		}
		for (int i = k; i < arr.length; i++) {
			if (arr[i] < maxHeap.peek()) {
				maxHeap.poll();
				maxHeap.add(arr[i]);
			}
		}
		return maxHeap.peek();
	}

	// 改写快排，时间复杂度O(N)
	// k >= 1
	public static int minKth2(int[] array, int k) {
		int[] arr = copyArray(array);
		return process2(arr, 0, arr.length - 1, k - 1);
	}

	public static int[] copyArray(int[] arr) {
		int[] ans = new int[arr.length];
		for (int i = 0; i != ans.length; i++) {
			ans[i] = arr[i];
		}
		return ans;
	}

	// arr 第k小的数
	// process2(arr, 0, N-1, k-1)
	// arr[L..R]  范围上，如果排序的话(不是真的去排序)，找位于index的数
	// index [L..R]
	public static int process2(int[] arr, int L, int R, int index) {
		if (L == R) { // L = =R ==INDEX
			return arr[L];
		}
		// 不止一个数  L +  [0, R -L]
		int pivot = arr[L + (int) (Math.random() * (R - L + 1))];
		// 划分返回等于基准的秩区间，目标落在其中即可停止。
		int[] range = partition(arr, L, R, pivot);
		if (index >= range[0] && index <= range[1]) {
			return arr[index];
		// 目标秩在等于区左侧，只递归较小区域。
		} else if (index < range[0]) {
			return process2(arr, L, range[0] - 1, index);
		} else {
			return process2(arr, range[1] + 1, R, index);
		}
	}

	public static int[] partition(int[] arr, int L, int R, int pivot) {
		int less = L - 1;
		int more = R + 1;
		int cur = L;
		while (cur < more) {
			if (arr[cur] < pivot) {
				swap(arr, ++less, cur++);
			} else if (arr[cur] > pivot) {
				swap(arr, cur, --more);
			} else {
				cur++;
			}
		}
		return new int[] { less + 1, more - 1 };
	}

	public static void swap(int[] arr, int i1, int i2) {
		int tmp = arr[i1];
		arr[i1] = arr[i2];
		arr[i2] = tmp;
	}

	// 利用bfprt算法，时间复杂度O(N)
	public static int minKth3(int[] array, int k) {
		int[] arr = copyArray(array);
		return bfprt(arr, 0, arr.length - 1, k - 1);
	}

	// arr[L..R]  如果排序的话，位于index位置的数，是什么，返回
	public static int bfprt(int[] arr, int L, int R, int index) {
		if (L == R) {
			return arr[L];
		}
		// L...R  每五个数一组
		// 每一个小组内部排好序
		// 小组的中位数组成新数组
		// 这个新数组的中位数返回
		// 用每五个一组的中位数构造有最坏分割保证的基准。
		int pivot = medianOfMedians(arr, L, R);
		// 划分返回等于基准的秩区间，目标落在其中即可停止。
		int[] range = partition(arr, L, R, pivot);
		if (index >= range[0] && index <= range[1]) {
			return arr[index];
		// 目标秩在等于区左侧，只递归较小区域。
		} else if (index < range[0]) {
			return bfprt(arr, L, range[0] - 1, index);
		} else {
			return bfprt(arr, range[1] + 1, R, index);
		}
	}

	// arr[L...R]  五个数一组
	// 每个小组内部排序
	// 每个小组中位数领出来，组成marr
	// marr中的中位数，返回
	// 用每五个一组的中位数构造有最坏分割保证的基准。
	public static int medianOfMedians(int[] arr, int L, int R) {
		int size = R - L + 1;
		int offset = size % 5 == 0 ? 0 : 1;
		int[] mArr = new int[size / 5 + offset];
		for (int team = 0; team < mArr.length; team++) {
			int teamFirst = L + team * 5;
			// L ... L + 4
			// L +5 ... L +9
			// L +10....L+14
			mArr[team] = getMedian(arr, teamFirst, Math.min(R, teamFirst + 4));
		}
		// marr中，找到中位数
		// marr(0, marr.len - 1,  mArr.length / 2 )
		return bfprt(mArr, 0, mArr.length - 1, mArr.length / 2);
	}

	public static int getMedian(int[] arr, int L, int R) {
		insertionSort(arr, L, R);
		return arr[(L + R) / 2];
	}

	public static void insertionSort(int[] arr, int L, int R) {
		for (int i = L + 1; i <= R; i++) {
			for (int j = i - 1; j >= L && arr[j] > arr[j + 1]; j--) {
				swap(arr, j, j + 1);
			}
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) (Math.random() * maxSize) + 1];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) (Math.random() * (maxValue + 1));
		}
		return arr;
	}

public static void main(String[] args) {
		int testTime = 1000000;
		int maxSize = 100;
		int maxValue = 100;
		System.out.println("test begin");
		for (int i = 0; i < testTime; i++) {
			int[] arr = generateRandomArray(maxSize, maxValue);
			int k = (int) (Math.random() * arr.length) + 1;
			int ans1 = minKth1(arr, k);
			int ans2 = minKth2(arr, k);
			int ans3 = minKth3(arr, k);
			if (ans1 != ans2 || ans2 != ans3) {
				System.out.println("Oops!");
			}
		}
		System.out.println("test finish");
	}
```

#### 题解

**为什么正确**

划分后小于区元素都排在等于区之前，目标秩因此能确定唯一需要继续的区域。五元组中位数保证基准两侧各有约3N/10个元素，最坏剩余规模至多约7N/10，结合中位数递归得到线性递推上界。

**复杂度**

堆版O(N log k)时间/O(k)空间；随机快选期望O(N)、最坏O(N²)；BFPRT最坏O(N)。复制数组和中位数辅助数组使源码整体额外空间可达O(N)。

**边界与易错点**

题目k从1计，内部目标下标为k-1。重复值需要整段等于区。最后不足5个也要取实际组中位数，不能访问不存在元素。

### 29.2 数组中最大的 K 个数

#### 题目

给定无序整数数组和 `k`，返回其中最大的 `k` 个元素。

**输入、输出与约束**

输入数组和k≥1，返回最大的min(k,N)个元素，重复值保留重数。

**函数签名（课程入口）**

```java
public static int[] maxTopK1(int[] arr, int k);
public static int[] maxTopK2(int[] arr, int k);
public static int[] maxTopK3(int[] arr, int k);
public static int minKth(int[] arr, int index);
```

**示例**

```text
输入：arr=[5,1,5,3], k=3
输出：[5,5,3]
```

解释：两个5都应保留，各代表一个输入元素。

**出处与版本差异**

- [课程源码：class29/Code02_MaxTopK.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class29/Code02_MaxTopK.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

课程比较三种方案：全排序后取尾部、建大根堆后弹k次、快速选择找第N-k小的阈值。阈值版先收集严格大于阈值的数，再用阈值补满k个，最后排序为降序。

#### 代码答案

```java
package class29;

import java.util.Arrays;

public class Code02_MaxTopK {

	// 时间复杂度O(N*logN)
	// 排序+收集
	public static int[] maxTopK1(int[] arr, int k) {
		if (arr == null || arr.length == 0) {
			return new int[0];
		}
		int N = arr.length;
		k = Math.min(N, k);
		Arrays.sort(arr);
		int[] ans = new int[k];
		for (int i = N - 1, j = 0; j < k; i--, j++) {
			ans[j] = arr[i];
		}
		return ans;
	}

	// 方法二，时间复杂度O(N + K*logN)
	// 解释：堆
	public static int[] maxTopK2(int[] arr, int k) {
		if (arr == null || arr.length == 0) {
			return new int[0];
		}
		int N = arr.length;
		k = Math.min(N, k);
		// 从底向上建堆，时间复杂度O(N)
		for (int i = N - 1; i >= 0; i--) {
			heapify(arr, i, N);
		}
		// 只把前K个数放在arr末尾，然后收集，O(K*logN)
		int heapSize = N;
		swap(arr, 0, --heapSize);
		int count = 1;
		while (heapSize > 0 && count < k) {
			heapify(arr, 0, heapSize);
			swap(arr, 0, --heapSize);
			count++;
		}
		int[] ans = new int[k];
		for (int i = N - 1, j = 0; j < k; i--, j++) {
			ans[j] = arr[i];
		}
		return ans;
	}

	public static void heapInsert(int[] arr, int index) {
		while (arr[index] > arr[(index - 1) / 2]) {
			swap(arr, index, (index - 1) / 2);
			index = (index - 1) / 2;
		}
	}

	public static void heapify(int[] arr, int index, int heapSize) {
		int left = index * 2 + 1;
		while (left < heapSize) {
			int largest = left + 1 < heapSize && arr[left + 1] > arr[left] ? left + 1 : left;
			largest = arr[largest] > arr[index] ? largest : index;
			if (largest == index) {
				break;
			}
			swap(arr, largest, index);
			index = largest;
			left = index * 2 + 1;
		}
	}

	public static void swap(int[] arr, int i, int j) {
		int tmp = arr[i];
		arr[i] = arr[j];
		arr[j] = tmp;
	}

	// 方法三，时间复杂度O(n + k * logk)
	public static int[] maxTopK3(int[] arr, int k) {
		if (arr == null || arr.length == 0) {
			return new int[0];
		}
		int N = arr.length;
		k = Math.min(N, k);
		// O(N)
		int num = minKth(arr, N - k);
		int[] ans = new int[k];
		int index = 0;
		for (int i = 0; i < N; i++) {
			// 严格超过阈值的元素必然属于最大k个，先全部收集。
			if (arr[i] > num) {
				ans[index++] = arr[i];
			}
		}
		for (; index < k; index++) {
			// 剩余名额由等于阈值的元素补齐，避免重复值导致长度不足。
			ans[index] = num;
		}
		// O(k*logk)
		Arrays.sort(ans);
		for (int L = 0, R = k - 1; L < R; L++, R--) {
			swap(ans, L, R);
		}
		return ans;
	}

	// 时间复杂度O(N)
	public static int minKth(int[] arr, int index) {
		int L = 0;
		int R = arr.length - 1;
		int pivot = 0;
		int[] range = null;
		while (L < R) {
			pivot = arr[L + (int) (Math.random() * (R - L + 1))];
			range = partition(arr, L, R, pivot);
			if (index < range[0]) {
				R = range[0] - 1;
			} else if (index > range[1]) {
				L = range[1] + 1;
			} else {
				return pivot;
			}
		}
		return arr[L];
	}

	public static int[] partition(int[] arr, int L, int R, int pivot) {
		int less = L - 1;
		int more = R + 1;
		int cur = L;
		while (cur < more) {
			if (arr[cur] < pivot) {
				swap(arr, ++less, cur++);
			} else if (arr[cur] > pivot) {
				swap(arr, cur, --more);
			} else {
				cur++;
			}
		}
		return new int[] { less + 1, more - 1 };
	}

	// 生成随机数组测试

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[] generateRandomArray(int maxSize, int maxValue) {
		int[] arr = new int[(int) ((maxSize + 1) * Math.random())];
		for (int i = 0; i < arr.length; i++) {
			// [-? , +?]
			arr[i] = (int) ((maxValue + 1) * Math.random()) - (int) (maxValue * Math.random());
		}
		return arr;
	}

	// for test
	public static int[] copyArray(int[] arr) {
		if (arr == null) {
			return null;
		}
		int[] res = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			res[i] = arr[i];
		}
		return res;
	}

	// for test
	public static boolean isEqual(int[] arr1, int[] arr2) {
		if ((arr1 == null && arr2 != null) || (arr1 != null && arr2 == null)) {
			return false;
		}
		if (arr1 == null && arr2 == null) {
			return true;
		}
		if (arr1.length != arr2.length) {
			return false;
		}
		for (int i = 0; i < arr1.length; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

	// for test
	public static void printArray(int[] arr) {
		if (arr == null) {
			return;
		}
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

public static void main(String[] args) {
		int testTime = 500000;
		int maxSize = 100;
		int maxValue = 100;
		boolean pass = true;
		System.out.println("测试开始，没有打印出错信息说明测试通过");
		for (int i = 0; i < testTime; i++) {
			int k = (int) (Math.random() * maxSize) + 1;
			int[] arr = generateRandomArray(maxSize, maxValue);

			int[] arr1 = copyArray(arr);
			int[] arr2 = copyArray(arr);
			int[] arr3 = copyArray(arr);

			int[] ans1 = maxTopK1(arr1, k);
			int[] ans2 = maxTopK2(arr2, k);
			int[] ans3 = maxTopK3(arr3, k);
			if (!isEqual(ans1, ans2) || !isEqual(ans1, ans3)) {
				pass = false;
				System.out.println("出错了！");
				printArray(ans1);
				printArray(ans2);
				printArray(ans3);
				break;
			}
		}
		System.out.println("测试结束了，测试了" + testTime + "组，是否所有测试用例都通过？" + (pass ? "是" : "否"));
	}
```

#### 题解

**为什么正确**

阈值将前N-k个较小元素与最大k个元素分开。大于阈值者必入选；等于阈值可能跨越边界，按需要数量补入，既不漏大值也不会超出k个。

**复杂度**

排序O(N log N)；建堆弹出O(N+k log N)；随机选择期望O(N+k log k)，最坏O(N²)。输出O(k)，快速选择会修改输入。

**边界与易错点**

不能只取严格大于阈值的数，否则有重复值时不足k个。输出顺序按课程为降序；k>N时课程截到N。

### 29.3 蓄水池抽样

#### 题目

数据以流的形式到达且总长度事先未知，请始终保留 `k` 个等概率样本。

**输入、输出与约束**

输入逐项数据流与正容量K；维护等概率不放回的位置样本。

**函数签名（课程入口）**

```java
public void add(int num);
public int[] choices();
```

**示例**

```text
输入：容量K=2，已扫描流[1,2,3,4]
输出：每个元素进入最终池的概率为1/2
```

解释：结果是一组随机的两个不同流位置，不能给唯一确定数组。

**出处与版本差异**

- [课程源码：class29/Code03_ReservoirSampling.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class29/Code03_ReservoirSampling.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

蓄水池先存前K个元素。扫描到第i个（从1计）时，以K/i概率接受它；接受后均匀替换池内一个位置。无须提前知道总流长度。

#### 代码答案

```java
package class29;

public class Code03_ReservoirSampling {

	public static class RandomBox {
		private int[] bag;
		private int N;
		private int count;

		public RandomBox(int capacity) {
			bag = new int[capacity];
			N = capacity;
			count = 0;
		}

		private int rand(int max) {
			return (int) (Math.random() * max) + 1;
		}

		public void add(int num) {
			count++;
			if (count <= N) {
				bag[count - 1] = num;
			} else {
				// 第count项以池容量N除以count的概率接受。
				if (rand(count) <= N) {
					// 接受后均匀替换任意一个池槽，保证旧样本被替换概率相同。
					bag[rand(N) - 1] = num;
				}
			}
		}

		public int[] choices() {
			int[] ans = new int[N];
			for (int i = 0; i < N; i++) {
				ans[i] = bag[i];
			}
			return ans;
		}

	}

	// 请等概率返回1~i中的一个数字
	public static int random(int i) {
		return (int) (Math.random() * i) + 1;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		System.out.println("hello");
		int test = 10000;
		int ballNum = 17;
		int[] count = new int[ballNum + 1];
		for (int i = 0; i < test; i++) {
			int[] bag = new int[10];
			int bagi = 0;
			for (int num = 1; num <= ballNum; num++) {
				if (num <= 10) {
					bag[bagi++] = num;
				} else { // num > 10
					if (random(num) <= 10) { // 一定要把num球入袋子
						bagi = (int) (Math.random() * 10);
						bag[bagi] = num;
					}
				}

			}
			for (int num : bag) {
				count[num]++;
			}
		}
		for (int i = 0; i <= ballNum; i++) {
			System.out.println(count[i]);
		}

		System.out.println("hello");
		int all = 100;
		int choose = 10;
		int testTimes = 50000;
		int[] counts = new int[all + 1];
		for (int i = 0; i < testTimes; i++) {
			RandomBox box = new RandomBox(choose);
			for (int num = 1; num <= all; num++) {
				box.add(num);
			}
			int[] ans = box.choices();
			for (int j = 0; j < ans.length; j++) {
				counts[ans[j]]++;
			}
		}

		for (int i = 0; i < counts.length; i++) {
			System.out.println(i + " times : " + counts[i]);
		}

	}
```

#### 题解

**为什么正确**

归纳假设旧元素留在池内概率K/(i-1)。新一步它被替换概率为(K/i)×(1/K)=1/i，因此保留概率变为K/(i-1)×(1-1/i)=K/i；新元素被接受概率也是K/i，所有已见元素等概率。

**复杂度**

扫描N项时间O(N)，池空间O(K)。

**边界与易错点**

接受概率与替换位置都要正确随机。流长不足K时只含实际见到的元素；本题保证等概率样本，不保证按原流顺序输出。统计实验只能发现偏差，不能替代归纳证明。


<a id="course-30"></a>

## 第 30 课：Morris 遍历与二叉树最小深度

### 30.1 Morris 二叉树遍历

#### 题目

给定二叉树根节点，要求仅使用 O(1) 额外空间完成前序、中序或后序遍历，并在结束后恢复树结构。

**输入、输出与约束**

输入可临时修改的二叉树；以常数额外空间完成遍历并恢复所有指针，运行时不要并发读取该树。

**函数签名（课程入口）**

```java
public static void morrisPre(Node head);
public static void morrisIn(Node head);
public static void morrisPos(Node head);
public static boolean isBST(Node head);
```

**示例**

```text
输入：root=[2,1,3]
输出：中序1、2、3，结束后节点1.right仍为null
```

解释：第一次到根2时借用1.right返回，第二次到根时拆除。

**出处与版本差异**

- [课程源码：class30/Code01_MorrisTraversal.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class30/Code01_MorrisTraversal.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

1. 普通递归需要栈记住“左子树访问完应回到哪个根”。Morris 用左子树最右节点原本为空的右指针，暂时连接回当前根，形成可拆除的回路。
2. 当前节点没有左子树时，直接向右走。存在左子树时寻找最右节点：若它的右指针为空，这是第一次来到当前根，建回路并向左；若它已指向当前根，这是第二次来到根，拆回路并向右。
3. 前序在第一次到达根时输出；中序在左子树处理完的第二次到达时输出。没有左子树的节点只到达一次，两种遍历都在这次输出。
4. 后序要等左右子树之后输出根：第二次到达时，倒转并打印左子树的右边界，然后立即还原；遍历结束再处理整棵树的右边界。无论是否找到目标，都必须走完必要的拆线过程，不能留下临时指针。

#### 代码答案

```java
package class30;

public class Code01_MorrisTraversal {

	public static class Node {
		public int value;
		Node left;
		Node right;

		public Node(int data) {
			this.value = data;
		}
	}

	public static void process(Node root) {
		if (root == null) {
			return;
		}
		// 1
		process(root.left);
		// 2
		process(root.right);
		// 3
	}

	public static void morris(Node head) {
		if (head == null) {
			return;
		}
		Node cur = head;
		Node mostRight = null;
		while (cur != null) {
			mostRight = cur.left;
			if (mostRight != null) {
				while (mostRight.right != null && mostRight.right != cur) {
					mostRight = mostRight.right;
				}
				if (mostRight.right == null) {
					// 借用左树最右节点的空右指针保存返回当前根的地址。
					mostRight.right = cur;
					cur = cur.left;
					continue;
				} else {
					// 第二次回到根说明左树处理完，拆除临时线索恢复原树。
					mostRight.right = null;
				}
			}
			cur = cur.right;
		}
	}

	public static void morrisPre(Node head) {
		if (head == null) {
			return;
		}
		Node cur = head;
		Node mostRight = null;
		while (cur != null) {
			mostRight = cur.left;
			if (mostRight != null) {
				while (mostRight.right != null && mostRight.right != cur) {
					mostRight = mostRight.right;
				}
				if (mostRight.right == null) {
					System.out.print(cur.value + " ");
					// 借用左树最右节点的空右指针保存返回当前根的地址。
					mostRight.right = cur;
					cur = cur.left;
					continue;
				} else {
					// 第二次回到根说明左树处理完，拆除临时线索恢复原树。
					mostRight.right = null;
				}
			} else {
				System.out.print(cur.value + " ");
			}
			cur = cur.right;
		}
		System.out.println();
	}

	public static void morrisIn(Node head) {
		if (head == null) {
			return;
		}
		Node cur = head;
		Node mostRight = null;
		while (cur != null) {
			mostRight = cur.left;
			if (mostRight != null) {
				while (mostRight.right != null && mostRight.right != cur) {
					mostRight = mostRight.right;
				}
				if (mostRight.right == null) {
					// 借用左树最右节点的空右指针保存返回当前根的地址。
					mostRight.right = cur;
					cur = cur.left;
					continue;
				} else {
					// 第二次回到根说明左树处理完，拆除临时线索恢复原树。
					mostRight.right = null;
				}
			}
			System.out.print(cur.value + " ");
			cur = cur.right;
		}
		System.out.println();
	}

	public static void morrisPos(Node head) {
		if (head == null) {
			return;
		}
		Node cur = head;
		Node mostRight = null;
		while (cur != null) {
			mostRight = cur.left;
			if (mostRight != null) {
				while (mostRight.right != null && mostRight.right != cur) {
					mostRight = mostRight.right;
				}
				if (mostRight.right == null) {
					// 借用左树最右节点的空右指针保存返回当前根的地址。
					mostRight.right = cur;
					cur = cur.left;
					continue;
				} else {
					// 第二次回到根说明左树处理完，拆除临时线索恢复原树。
					mostRight.right = null;
					// 后序第二次返回时逆序打印左树右边界，输出该子树尚未输出的节点。
					printEdge(cur.left);
				}
			}
			cur = cur.right;
		}
		// 整树最右边界没有外部回边触发，需在最后补输出。
		printEdge(head);
		System.out.println();
	}

	public static void printEdge(Node head) {
		Node tail = reverseEdge(head);
		Node cur = tail;
		while (cur != null) {
			System.out.print(cur.value + " ");
			cur = cur.right;
		}
		reverseEdge(tail);
	}

	public static Node reverseEdge(Node from) {
		Node pre = null;
		Node next = null;
		while (from != null) {
			next = from.right;
			from.right = pre;
			pre = from;
			from = next;
		}
		return pre;
	}

	public static void printInOrder(Node head, int height, String to, int len) {
		if (head == null) {
			return;
		}
		printInOrder(head.right, height + 1, "v", len);
		String val = to + head.value + to;
		int lenM = val.length();
		int lenL = (len - lenM) / 2;
		int lenR = len - lenM - lenL;
		val = getSpace(lenL) + val + getSpace(lenR);
		System.out.println(getSpace(height * len) + val);
		printInOrder(head.left, height + 1, "^", len);
	}

	public static String getSpace(int num) {
		String space = " ";
		StringBuffer buf = new StringBuffer("");
		for (int i = 0; i < num; i++) {
			buf.append(space);
		}
		return buf.toString();
	}

	public static boolean isBST(Node head) {
		if (head == null) {
			return true;
		}
		Node cur = head;
		Node mostRight = null;
		Integer pre = null;
		boolean ans = true;
		while (cur != null) {
			mostRight = cur.left;
			if (mostRight != null) {
				while (mostRight.right != null && mostRight.right != cur) {
					mostRight = mostRight.right;
				}
				if (mostRight.right == null) {
					// 借用左树最右节点的空右指针保存返回当前根的地址。
					mostRight.right = cur;
					cur = cur.left;
					continue;
				} else {
					// 第二次回到根说明左树处理完，拆除临时线索恢复原树。
					mostRight.right = null;
				}
			}
			if (pre != null && pre >= cur.value) {
				ans = false;
			}
			pre = cur.value;
			cur = cur.right;
		}
		return ans;
	}

}
```

---

##### 新增对数器（固定输入或固定随机种子）

独立递归上下界检查搜索树结果，并比较调用前后的每条左右指针。覆盖空树、重复值与左右单链；本对数器验证Morris的isBST分支及恢复，不把它称为三种打印序列的完整测试。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
    java.util.Random random = new java.util.Random(20260905L);
    for (int round = 0; round < 3000; round++) {
        int n = random.nextInt(31);
        Node[] nodes = new Node[n], left = new Node[n], right = new Node[n];
        for (int i = 0; i < n; i++) nodes[i] = new Node(random.nextInt(21) - 10);
        for (int i = 1; i < n; i++) {
            // 同时覆盖二叉形状、纯左链、纯右链，值允许重复。
            if (round % 3 == 0) {
                if (i % 2 == 1) nodes[(i - 1) / 2].left = nodes[i];
                else nodes[(i - 1) / 2].right = nodes[i];
            } else if (round % 3 == 1) nodes[i - 1].left = nodes[i];
            else nodes[i - 1].right = nodes[i];
        }
        for (int i = 0; i < n; i++) { left[i] = nodes[i].left; right[i] = nodes[i].right; }
        Node root = n == 0 ? null : nodes[0];
        boolean expected = bruteBSTForCheck(root, Long.MIN_VALUE, Long.MAX_VALUE);
        if (isBST(root) != expected) throw new AssertionError("BST round=" + round);
        // 逐条比较原指针身份，不只看遍历值，检测临时回路是否被拆除。
        for (int i = 0; i < n; i++)
            if (nodes[i].left != left[i] || nodes[i].right != right[i])
                throw new AssertionError("tree not restored: round=" + round + " node=" + i);
    }
    System.out.println("PASS: MorrisTraversal, BST result and pointer restoration");
}

private static boolean bruteBSTForCheck(Node node, long lo, long hi) {
    if (node == null) return true;
    return lo < node.value && node.value < hi
        && bruteBSTForCheck(node.left, lo, node.value)
        && bruteBSTForCheck(node.right, node.value, hi);
}
```

#### 题解

**为什么正确**

临时回边存放了递归本应保存的返回地址，而且只借用原来为空的右指针。每条回边在第二次到达时被识别并拆除，因此遍历结束恢复原树；右边界逆序打印后再反转回去，后序才同样保留结构。

**复杂度**

每条树边仅参与常数次扫描或反转，时间O(N)，Morris核心额外空间O(1)。源码另有递归演示process，它的O(H)栈不属于Morris实现。

**边界与易错点**

查找mostRight必须同时检查right!=null和right!=cur，防止沿临时回边死循环。不能中途直接返回而留下线索，判搜索树版本应记录失败后继续恢复。

### 30.2 二叉树最小深度

#### 题目

给定二叉树根节点，返回从根节点到最近叶子节点的节点数。

**输入、输出与约束**

输入二叉树；返回到最近真实叶子的节点数，空树为0。

**函数签名（课程入口）**

```java
public static int minDepth1(TreeNode head);
public static int minDepth2(TreeNode head);
```

**示例**

```text
输入：root=1→左2→左3
输出：3
```

解释：只有节点3是叶子，两个缺失右孩子都不能作为路径终点。

**出处与版本差异**

- [课程源码：class30/Code02_MinDepth.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class30/Code02_MinDepth.java)。
- [LeetCode 原题 111. 二叉树的最小深度（Minimum Depth of Binary Tree）](https://leetcode.com/problems/minimum-depth-of-binary-tree/)

#### 思路

递归版只在叶子返回1，非叶取存在的左右子树最小深度加一，缺失子树设为无限而不是0。Morris版在建线索时增加curLevel，拆线索时识别前驱是否原叶子并更新最小深度，再减去走过的右边界长度；整树最右叶子最后单独检查。

#### 代码答案

```java
package class30;

// 本题测试链接 : https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/
public class Code02_MinDepth {

	// 不提交这个类
	public static class TreeNode {
		public int val;
		public TreeNode left;
		public TreeNode right;

		public TreeNode(int x) {
			val = x;
		}
	}

	// 下面的方法是一般解
	public static int minDepth1(TreeNode head) {
		if (head == null) {
			return 0;
		}
		return p(head);
	}

	// 返回x为头的树，最小深度是多少
	public static int p(TreeNode x) {
		if (x.left == null && x.right == null) {
			return 1;
		}
		// 左右子树起码有一个不为空
		// 缺失子树不能提供根到叶路径，用无限值使它不参与最小值竞争。
		int leftH = Integer.MAX_VALUE;
		if (x.left != null) {
			leftH = p(x.left);
		}
		int rightH = Integer.MAX_VALUE;
		if (x.right != null) {
			rightH = p(x.right);
		}
		return 1 + Math.min(leftH, rightH);
	}

	// 下面的方法是morris遍历的解
	public static int minDepth2(TreeNode head) {
		if (head == null) {
			return 0;
		}
		TreeNode cur = head;
		TreeNode mostRight = null;
		int curLevel = 0;
		int minHeight = Integer.MAX_VALUE;
		while (cur != null) {
			mostRight = cur.left;
			if (mostRight != null) {
				int rightBoardSize = 1;
				while (mostRight.right != null && mostRight.right != cur) {
					rightBoardSize++;
					mostRight = mostRight.right;
				}
				if (mostRight.right == null) { // 第一次到达
					curLevel++;
					mostRight.right = cur;
					cur = cur.left;
					continue;
				} else { // 第二次到达
					if (mostRight.left == null) {
						minHeight = Math.min(minHeight, curLevel);
					}
					// 从前驱沿线索回到祖先后，扣掉已越过的右边界长度恢复深度。
					curLevel -= rightBoardSize;
					mostRight.right = null;
				}
			} else { // 只有一次到达
				curLevel++;
			}
			cur = cur.right;
		}
		int finalRight = 1;
		cur = head;
		while (cur.right != null) {
			finalRight++;
			cur = cur.right;
		}
		// 最后单独检查整树最右链终点是否是真叶子。
		if (cur.left == null && cur.right == null) {
			minHeight = Math.min(minHeight, finalRight);
		}
		return minHeight;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：只有左树时不能min(左深度,0)+1，否则误把根当最短路径终点。全树最右叶没有后继线索，漏掉最后检查会遗漏答案。

本条未附独立随机对数器。

#### 题解

**为什么正确**

最短根到叶路径只能终止于真实叶子，空孩子不是可选终点。Morris第二次返回时，前驱原right为空；再确认left也为空就识别出原叶子，此时累计层数给出它的真实深度，回退边界长度恢复祖先层数。

**复杂度**

两版时间O(N)；递归栈O(H)，Morris版额外空间O(1)。

**边界与易错点**

只有左树时不能min(左深度,0)+1，否则误把根当最短路径终点。全树最右叶没有后继线索，漏掉最后检查会遗漏答案。


<a id="course-31"></a>

## 第 31 课：线段树及其应用

### 31.1 支持区间更新与查询的线段树

#### 题目

设计线段树，支持区间加、区间赋值和区间累加和查询。

**输入、输出与约束**

输入非空整数数组；本课程树内部索引1..N，支持闭区间加、赋值与求和。

**函数签名（课程入口）**

```java
public void build(int l, int r, int rt);
public void update(int L, int R, int C, int l, int r, int rt);
public void add(int L, int R, int C, int l, int r, int rt);
public long query(int L, int R, int l, int r, int rt);
public void update(int L, int R, int C);
public void add(int L, int R, int C);
```

**示例**

```text
输入：初始[1,2,3]；[1,2]加2，再将[2,3]赋值5，查询[1,3]（下标从1）
输出：13
```

解释：数组先变[3,4,3]，再变[3,5,5]。

**出处与版本差异**

- [课程源码：class31/Code01_SegmentTree.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class31/Code01_SegmentTree.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

1. 每个节点代表一个区间，`sum[rt]` 保存整段和。查询或修改若完全覆盖当前段，直接使用整段信息；部分覆盖才下沉到左右孩子，返回时重新合并两边的和。
2. 区间加 x 时，长度为 len 的段和增加 `x*len`。把尚未传给孩子的增量记在 `lazy[rt]`，不必立刻逐个访问叶子。
3. 区间赋值 x 时，整段和变成 `x*len`，记录 `update=true` 和 `change=x`，同时清除旧加法标记，因为旧值及其增量都被覆盖了。
4. 下传必须先赋值、后加法。比如一段先被赋成 3，又整体加 2，孩子最终应得到 5；若把加法先传再被赋值覆盖，就会错误得到 3。部分递归前调用 pushDown，递归后调用 pushUp，分别保证孩子信息与父段和一致。

#### 代码答案

```java
package class31;

public class Code01_SegmentTree {

	public static class SegmentTree {
		// arr[]为原序列的信息从0开始，但在arr里是从1开始的
		// sum[]模拟线段树维护区间和
		// lazy[]为累加和懒惰标记
		// change[]为更新的值
		// update[]为更新慵懒标记
		private int MAXN;
		private int[] arr;
		private int[] sum;
		private int[] lazy;
		private int[] change;
		private boolean[] update;

		public SegmentTree(int[] origin) {
			MAXN = origin.length + 1;
			arr = new int[MAXN]; // arr[0] 不用 从1开始使用
			for (int i = 1; i < MAXN; i++) {
				arr[i] = origin[i - 1];
			}
			sum = new int[MAXN << 2]; // 用来支持脑补概念中，某一个范围的累加和信息
			lazy = new int[MAXN << 2]; // 用来支持脑补概念中，某一个范围沒有往下傳遞的纍加任務
			change = new int[MAXN << 2]; // 保存区间赋值任务的目标值
			update = new boolean[MAXN << 2]; // 标记当前节点是否有尚未下传的赋值任务
		}

		private void pushUp(int rt) {
			// 孩子状态更新后重新汇总父区间总和。
			sum[rt] = sum[rt << 1] + sum[rt << 1 | 1];
		}

		// 之前的，所有懒增加，和懒更新，从父范围，发给左右两个子范围
		// 分发策略是什么
		// ln表示左子树元素结点个数，rn表示右子树结点个数
		private void pushDown(int rt, int ln, int rn) {
			// 赋值标志独立于数值，赋值0也必须下发。
			if (update[rt]) {
				update[rt << 1] = true;
				update[rt << 1 | 1] = true;
				change[rt << 1] = change[rt];
				change[rt << 1 | 1] = change[rt];
				// 父区间赋值覆盖左孩子此前累计的加法。
				lazy[rt << 1] = 0;
				lazy[rt << 1 | 1] = 0;
				sum[rt << 1] = change[rt] * ln;
				sum[rt << 1 | 1] = change[rt] * rn;
				update[rt] = false;
			}
			if (lazy[rt] != 0) {
				lazy[rt << 1] += lazy[rt];
				sum[rt << 1] += lazy[rt] * ln;
				lazy[rt << 1 | 1] += lazy[rt];
				sum[rt << 1 | 1] += lazy[rt] * rn;
				lazy[rt] = 0;
			}
		}

		// 在初始化阶段，先把sum数组，填好
		// 在arr[l~r]范围上，去build，1~N，
		// rt : 这个范围在sum中的下标
		public void build(int l, int r, int rt) {
			if (l == r) {
				sum[rt] = arr[l];
				return;
			}
			int mid = (l + r) >> 1;
			build(l, mid, rt << 1);
			build(mid + 1, r, rt << 1 | 1);
			pushUp(rt);
		}

		// L~R  所有的值变成C
		// l~r  rt
		public void update(int L, int R, int C, int l, int r, int rt) {
			if (L <= l && r <= R) {
				update[rt] = true;
				change[rt] = C;
				sum[rt] = C * (r - l + 1);
				lazy[rt] = 0;
				return;
			}
			// 当前任务躲不掉，无法懒更新，要往下发
			int mid = (l + r) >> 1;
			pushDown(rt, mid - l + 1, r - mid);
			if (L <= mid) {
				update(L, R, C, l, mid, rt << 1);
			}
			if (R > mid) {
				update(L, R, C, mid + 1, r, rt << 1 | 1);
			}
			pushUp(rt);
		}

		// L~R, C 任务！
		// rt，l~r
		public void add(int L, int R, int C, int l, int r, int rt) {
			// 任务如果把此时的范围全包了！
			if (L <= l && r <= R) {
				// 整段每个元素都加C，区间和增加C乘元素个数。
				sum[rt] += C * (r - l + 1);
				lazy[rt] += C;
				return;
			}
			// 任务没有把你全包！
			// l  r  mid = (l+r)/2
			int mid = (l + r) >> 1;
			pushDown(rt, mid - l + 1, r - mid);
			// L~R
			if (L <= mid) {
				add(L, R, C, l, mid, rt << 1);
			}
			if (R > mid) {
				add(L, R, C, mid + 1, r, rt << 1 | 1);
			}
			pushUp(rt);
		}

		// 1~6 累加和是多少？ 1~8 rt
		public long query(int L, int R, int l, int r, int rt) {
			if (L <= l && r <= R) {
				return sum[rt];
			}
			int mid = (l + r) >> 1;
			pushDown(rt, mid - l + 1, r - mid);
			long ans = 0;
			if (L <= mid) {
				ans += query(L, R, l, mid, rt << 1);
			}
			if (R > mid) {
				ans += query(L, R, mid + 1, r, rt << 1 | 1);
			}
			return ans;
		}

	}

	public static class Right {
		public int[] arr;

		public Right(int[] origin) {
			arr = new int[origin.length + 1];
			for (int i = 0; i < origin.length; i++) {
				arr[i + 1] = origin[i];
			}
		}

		public void update(int L, int R, int C) {
			for (int i = L; i <= R; i++) {
				arr[i] = C;
			}
		}

		public void add(int L, int R, int C) {
			for (int i = L; i <= R; i++) {
				arr[i] += C;
			}
		}

		public long query(int L, int R) {
			long ans = 0;
			for (int i = L; i <= R; i++) {
				ans += arr[i];
			}
			return ans;
		}

	}

	public static int[] genarateRandomArray(int len, int max) {
		int size = (int) (Math.random() * len) + 1;
		int[] origin = new int[size];
		for (int i = 0; i < size; i++) {
			origin[i] = (int) (Math.random() * max) - (int) (Math.random() * max);
		}
		return origin;
	}

	public static boolean test() {
		int len = 100;
		int max = 1000;
		int testTimes = 5000;
		int addOrUpdateTimes = 1000;
		int queryTimes = 500;
		for (int i = 0; i < testTimes; i++) {
			int[] origin = genarateRandomArray(len, max);
			SegmentTree seg = new SegmentTree(origin);
			int S = 1;
			int N = origin.length;
			int root = 1;
			seg.build(S, N, root);
			Right rig = new Right(origin);
			for (int j = 0; j < addOrUpdateTimes; j++) {
				int num1 = (int) (Math.random() * N) + 1;
				int num2 = (int) (Math.random() * N) + 1;
				int L = Math.min(num1, num2);
				int R = Math.max(num1, num2);
				int C = (int) (Math.random() * max) - (int) (Math.random() * max);
				if (Math.random() < 0.5) {
					seg.add(L, R, C, S, N, root);
					rig.add(L, R, C);
				} else {
					seg.update(L, R, C, S, N, root);
					rig.update(L, R, C);
				}
			}
			for (int k = 0; k < queryTimes; k++) {
				int num1 = (int) (Math.random() * N) + 1;
				int num2 = (int) (Math.random() * N) + 1;
				int L = Math.min(num1, num2);
				int R = Math.max(num1, num2);
				long ans1 = seg.query(L, R, S, N, root);
				long ans2 = rig.query(L, R);
				if (ans1 != ans2) {
					return false;
				}
			}
		}
		return true;
	}

}
```

---

##### 新增对数器（固定输入或固定随机种子）

固定种子执行60000次交替赋值/累加，以逐元素数组作基准；强制穿插整段覆盖和部分查询，检查赋值覆盖旧加法、赋值后再加、负增量与单元素段。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
    java.util.Random random = new java.util.Random(20260905L);
    for (int round = 0; round < 300; round++) {
        int n = 1 + random.nextInt(30);
        int[] brute = new int[n];
        for (int i = 0; i < n; i++) brute[i] = random.nextInt(41) - 20;
        SegmentTree tree = new SegmentTree(brute.clone());
        tree.build(1, n, 1); // 树中下标1..n，对应基准数组0..n-1。
        for (int step = 0; step < 200; step++) {
            int l = 1 + random.nextInt(n), r = 1 + random.nextInt(n);
            if (l > r) { int t = l; l = r; r = t; }
            // 每10步强制整段覆盖，其余做部分区间，触发标记下传。
            if (step % 10 == 0) { l = 1; r = n; }
            int value = random.nextInt(41) - 20;
            if (step % 2 == 0) {
                tree.update(l, r, value, 1, n, 1);
                for (int i = l - 1; i < r; i++) brute[i] = value;
            } else {
                tree.add(l, r, value, 1, n, 1);
                for (int i = l - 1; i < r; i++) brute[i] += value;
            }
            // 逐元素基准不含懒标记；随机查询与全段查询分别对照。
            int ql = 1 + random.nextInt(n), qr = 1 + random.nextInt(n);
            if (ql > qr) { int t = ql; ql = qr; qr = t; }
            long expected = 0, total = 0;
            for (int i = 0; i < n; i++) {
                total += brute[i];
                if (i >= ql - 1 && i < qr) expected += brute[i];
            }
            if (tree.query(ql, qr, 1, n, 1) != expected || tree.query(1, n, 1, n, 1) != total)
                throw new AssertionError("round=" + round + " step=" + step + " L=" + ql + " R=" + qr);
        }
    }
    System.out.println("PASS: SegmentTree, 60000 mixed updates");
}
```

#### 题解

**为什么正确**

sum始终反映节点整段的真实总和，即使孩子尚未更新。下探前把延迟操作按原顺序作用到孩子即可恢复父子一致：先赋值并清除孩子旧加法，再传播父加法。回溯求和保持聚合正确。

**复杂度**

建树O(N)，单次连续区间更新或查询O(log N)，数组空间O(N)。

**边界与易错点**

区间赋值0也有效，不能用change!=0判断是否有赋值任务，必须有布尔标志。赋值会使此前加法失效，下发顺序不可颠倒；sum与增量乘区间长需防溢出。

### 31.2 下落方块的最高高度

#### 题目

若干正方形依次落到数轴上并停在地面或已有方块顶部，返回每次落下后的整体最高高度。

**输入、输出与约束**

输入整数左端点与正边长序列；返回每次落下后的全局最大高度。

**函数签名（课程入口）**

```java
public void update(int L, int R, int C, int l, int r, int rt);
public int query(int L, int R, int l, int r, int rt);
public HashMap<Integer, Integer> index(int[][] positions);
public List<Integer> fallingSquares(int[][] positions);
```

**示例**

```text
输入：positions=[[1,2],[2,3],[6,1]]
输出：[2,5,5]
```

解释：第二块与第一块重叠，顶高2+3=5；第三块独立落地。

**出处与版本差异**

- [课程源码：class31/Code02_FallingSquares.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class31/Code02_FallingSquares.java)。
- [LeetCode 原题 699. 掉落的方块（Falling Squares）](https://leetcode.com/problems/falling-squares/)

#### 思路

把每个方块水平范围映射为离散坐标区间。查询该区间当前最大高度，加方块边长得到落稳后顶高，再把整个覆盖区间赋值为这个顶高。每次输出迄今全局最高值。

#### 代码答案

```java
package class31;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.TreeSet;

public class Code02_FallingSquares {

	public static class SegmentTree {
		private int[] max;
		private int[] change;
		private boolean[] update;

		public SegmentTree(int size) {
			int N = size + 1;
			max = new int[N << 2];

			change = new int[N << 2];
			update = new boolean[N << 2];
		}

		private void pushUp(int rt) {
			max[rt] = Math.max(max[rt << 1], max[rt << 1 | 1]);
		}

		// ln表示左子树元素结点个数，rn表示右子树结点个数
		private void pushDown(int rt, int ln, int rn) {
			if (update[rt]) {
				update[rt << 1] = true;
				update[rt << 1 | 1] = true;
				change[rt << 1] = change[rt];
				change[rt << 1 | 1] = change[rt];
				max[rt << 1] = change[rt];
				max[rt << 1 | 1] = change[rt];
				update[rt] = false;
			}
		}

		public void update(int L, int R, int C, int l, int r, int rt) {
			if (L <= l && r <= R) {
				update[rt] = true;
				change[rt] = C;
				max[rt] = C;
				return;
			}
			int mid = (l + r) >> 1;
			pushDown(rt, mid - l + 1, r - mid);
			if (L <= mid) {
				update(L, R, C, l, mid, rt << 1);
			}
			if (R > mid) {
				update(L, R, C, mid + 1, r, rt << 1 | 1);
			}
			pushUp(rt);
		}

		public int query(int L, int R, int l, int r, int rt) {
			if (L <= l && r <= R) {
				return max[rt];
			}
			int mid = (l + r) >> 1;
			pushDown(rt, mid - l + 1, r - mid);
			int left = 0;
			int right = 0;
			if (L <= mid) {
				left = query(L, R, l, mid, rt << 1);
			}
			if (R > mid) {
				right = query(L, R, mid + 1, r, rt << 1 | 1);
			}
			return Math.max(left, right);
		}

	}

	public HashMap<Integer, Integer> index(int[][] positions) {
		TreeSet<Integer> pos = new TreeSet<>();
		for (int[] arr : positions) {
			pos.add(arr[0]);
			// 离散整数闭区间右端减1，避免仅边缘相接的方块被误当重叠。
			pos.add(arr[0] + arr[1] - 1);
		}
		HashMap<Integer, Integer> map = new HashMap<>();
		int count = 0;
		for (Integer index : pos) {
			map.put(index, ++count);
		}
		return map;
	}

	public List<Integer> fallingSquares(int[][] positions) {
		HashMap<Integer, Integer> map = index(positions);
		int N = map.size();
		SegmentTree segmentTree = new SegmentTree(N);
		int max = 0;
		List<Integer> res = new ArrayList<>();
		// 每落一个正方形，收集一下，所有东西组成的图像，最高高度是什么
		for (int[] arr : positions) {
			int L = map.get(arr[0]);
			int R = map.get(arr[0] + arr[1] - 1);
			// 查询投影范围内的最高支撑面，再加方块自身边长。
			int height = segmentTree.query(L, R, 1, N, 1) + arr[1];
			max = Math.max(max, height);
			res.add(max);
			// 方块覆盖整段的新表面应统一赋值为其顶面高度，不是给各点分别加高度。
			segmentTree.update(L, R, height, 1, N, 1);
		}
		return res;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：只接触边界不形成覆盖，整数坐标实现通常映射[left,left+size-1]。更新不是区间加；即使底面部分悬空，整个顶面高度也相同。

本条未附独立随机对数器。

#### 题解

**为什么正确**

方块底边必须不穿过已有任何物体，所以落稳底高是覆盖范围内最大已有高度。方块是刚性水平顶面，放下后整个范围顶高一致，因此更新是赋值而非逐点加高度。

**复杂度**

Q个方块：坐标离散化O(Q log Q)，每次查询和赋值O(log Q)，总O(Q log Q)，空间O(Q)。

**边界与易错点**

只接触边界不形成覆盖，整数坐标实现通常映射[left,left+size-1]。更新不是区间加；即使底面部分悬空，整个顶面高度也相同。


<a id="course-32"></a>

## 第 32 课：树状数组与 AC 自动机

### 32.1 一维树状数组

#### 题目

设计一维树状数组，支持单点增加和前缀累加和查询。

**输入、输出与约束**

构造N≥1；add下标1..N，sum下标0..N；返回前缀和。

**函数签名（课程入口）**

```java
public int sum(int index);
public void add(int index, int d);
```

**示例**

```text
输入：初始全0，add(2,5)，add(3,1)，sum(3)
输出：6
```

解释：前缀3拆为tree[3]管[3,3]、tree[2]管[1,2]。

**出处与版本差异**

- [课程源码：class32/Code01_IndexTree.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class32/Code01_IndexTree.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

树状数组tree[i]保存区间[i-lowbit(i)+1,i]的和，lowbit(i)=i&(-i)。查询前缀时累加tree[i]后减lowbit，跳到前一段；单点加时从原下标不断加lowbit，更新所有包含它的更大管理区间。

#### 代码答案

```java
package class32;

public class Code01_IndexTree {

	// 下标从1开始！
	public static class IndexTree {

		private int[] tree;
		private int N;

		// 0位置弃而不用！
		public IndexTree(int size) {
			N = size;
			tree = new int[N + 1];
		}

		// 1~index 累加和是多少？
		public int sum(int index) {
			int ret = 0;
			while (index > 0) {
				ret += tree[index];
				// 查询时去掉最后一个对齐块，剩下更短且不重叠的前缀。
				index -= index & -index;
			}
			return ret;
		}

		// index & -index : 提取出index最右侧的1出来
		// index :           0011001000
		// index & -index :  0000001000
		public void add(int index, int d) {
			while (index <= N) {
				// 当前管理区间包含被更新点，区间和同步增加d。
				tree[index] += d;
				// 更新时走向包含原位置的下一个更大管理区间。
				index += index & -index;
			}
		}
	}

	public static class Right {
		private int[] nums;
		private int N;

		public Right(int size) {
			N = size + 1;
			nums = new int[N + 1];
		}

		public int sum(int index) {
			int ret = 0;
			for (int i = 1; i <= index; i++) {
				ret += nums[i];
			}
			return ret;
		}

		public void add(int index, int d) {
			nums[index] += d;
		}

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int N = 100;
		int V = 100;
		int testTime = 2000000;
		IndexTree tree = new IndexTree(N);
		Right test = new Right(N);
		System.out.println("test begin");
		for (int i = 0; i < testTime; i++) {
			int index = (int) (Math.random() * N) + 1;
			if (Math.random() <= 0.5) {
				int add = (int) (Math.random() * V);
				tree.add(index, add);
				test.add(index, add);
			} else {
				if (tree.sum(index) != test.sum(index)) {
					System.out.println("Oops!");
				}
			}
		}
		System.out.println("test finish");
	}
```

#### 题解

**为什么正确**

查询每次去掉当前前缀的最后一个二进制对齐块，各块不重叠且最终覆盖1..i。更新沿父块链走过所有受该点影响的区间，所以以后每个前缀查询仍获得正确区间和。

**复杂度**

单点增加、前缀查询O(log N)，空间O(N)；区间和用sum(R)-sum(L-1)。

**边界与易错点**

更新下标必须≥1；i=0的lowbit为0，会死循环。add是增加delta，不是赋值value。sum(0)=0是合法空前缀。

### 32.2 二维树状数组

#### 题目

设计二维可变矩阵，支持单点更新以及任意轴对齐子矩形的元素和查询。

**输入、输出与约束**

输入非空矩阵，提供0基合法坐标的单点赋值和闭矩形查询。

**函数签名（课程入口）**

```java
public void update(int row, int col, int val);
public int sumRegion(int row1, int col1, int row2, int col2);
```

**示例**

```text
输入：matrix=[[1,2],[3,4]]，sumRegion(0,0,1,1)，update(0,1,5)，再次查询
输出：10、13
```

解释：更新增加了5-2=3，而非增加5。

**出处与版本差异**

- [课程源码：class32/Code02_IndexTree2D.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class32/Code02_IndexTree2D.java)。
- [LeetCode 原题 308. 二维区域和检索（可变）（Range Sum Query 2D - Mutable）](https://leetcode.com/problems/range-sum-query-2d-mutable/)

#### 思路

把一维树状数组扩展到行列两维，tree[i][j]管理两个lowbit区间的笛卡尔积。更新先求新值减旧值的差，再用两重向上lowbit循环增加所有覆盖块。矩形查询由四个二维前缀和做容斥。

#### 代码答案

```java
package class32;

// 测试链接：https://leetcode.com/problems/range-sum-query-2d-mutable
// 但这个题是付费题目
// 提交时把类名、构造函数名从Code02_IndexTree2D改成NumMatrix
public class Code02_IndexTree2D {
	private int[][] tree;
	private int[][] nums;
	private int N;
	private int M;

	public Code02_IndexTree2D(int[][] matrix) {
		if (matrix.length == 0 || matrix[0].length == 0) {
			return;
		}
		N = matrix.length;
		M = matrix[0].length;
		tree = new int[N + 1][M + 1];
		nums = new int[N][M];
		for (int i = 0; i < N; i++) {
			for (int j = 0; j < M; j++) {
				update(i, j, matrix[i][j]);
			}
		}
	}

	private int sum(int row, int col) {
		int sum = 0;
		for (int i = row + 1; i > 0; i -= i & (-i)) {
			for (int j = col + 1; j > 0; j -= j & (-j)) {
				sum += tree[i][j];
			}
		}
		return sum;
	}

	public void update(int row, int col, int val) {
		if (N == 0 || M == 0) {
			return;
		}
		// 外部是赋值接口，内部树状数组只累加新旧差值。
		int add = val - nums[row][col];
		// 保存本次新值，下一次赋值才能计算正确差量。
		nums[row][col] = val;
		for (int i = row + 1; i <= N; i += i & (-i)) {
			// 列方向同步向上更新包含该列的所有对齐块。
			for (int j = col + 1; j <= M; j += j & (-j)) {
				tree[i][j] += add;
			}
		}
	}

	public int sumRegion(int row1, int col1, int row2, int col2) {
		if (N == 0 || M == 0) {
			return 0;
		}
		return sum(row2, col2) + sum(row1 - 1, col1 - 1) - sum(row1 - 1, col2) - sum(row2, col1 - 1);
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：外部0基下标进入树状数组要加1。update是赋值，必须减去nums中旧值求delta；若直接加新值，反复更新会累错。

本条未附独立随机对数器。

#### 题解

**为什么正确**

二维前缀由互不相交的对齐行块和列块组合覆盖。矩形[ r1..r2 ]×[ c1..c2 ]从大前缀减去上方、左方后，左上重叠被减两次，要加回来一次。

**复杂度**

单次更新或查询O(log R·log C)，空间O(RC)；源码逐点初始化时间O(RC log R log C)。

**边界与易错点**

外部0基下标进入树状数组要加1。update是赋值，必须减去nums中旧值求delta；若直接加新值，反复更新会累错。

### 32.3 AC 自动机：出现过的模式条目数

#### 题目

小写非空模式与文章；先insert再build，单次查询返回至少出现一次的插入模式条目数。

**输入、输出与约束**

小写非空模式与文章；先insert再build，单次查询返回至少出现一次的插入模式条目数。

**函数签名（课程入口）**

```java
public void insert(String s);
public void build();
public int containNum(String content);
```

**示例**

```text
输入：模式["a","a","aa"]，文章"aaa"
输出：3
```

解释：a虽多次出现，仅按两次插入贡献2；aa贡献1。

**出处与版本差异**

- [课程源码：class32/Code03_AC1.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class32/Code03_AC1.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

AC（Aho–Corasick，多模式串匹配）自动机在前缀树上增加失配指针。先把所有模式串插入前缀树，再按层建立fail指针：失配时跳到当前已匹配串的最长可用后缀。扫描文章时按字符前进并沿fail链检查结尾。此版本把命中节点end改成-1，保证每个插入模式最多贡献一次，重复插入的同词按插入次数计。

#### 代码答案

```java
package class32;

import java.util.LinkedList;
import java.util.Queue;

public class Code03_AC1 {

	public static class Node {
		public int end; // 有多少个字符串以该节点结尾
		public Node fail;
		public Node[] nexts;

		public Node() {
			end = 0;
			fail = null;
			nexts = new Node[26];
		}
	}

	public static class ACAutomation {
		private Node root;

		public ACAutomation() {
			root = new Node();
		}

		// 你有多少个匹配串，就调用多少次insert
		public void insert(String s) {
			char[] str = s.toCharArray();
			Node cur = root;
			int index = 0;
			for (int i = 0; i < str.length; i++) {
				index = str[i] - 'a';
				if (cur.nexts[index] == null) {
					Node next = new Node();
					cur.nexts[index] = next;
				}
				cur = cur.nexts[index];
			}
			cur.end++;
		}

		public void build() {
			Queue<Node> queue = new LinkedList<>();
			queue.add(root);
			Node cur = null;
			Node cfail = null;
			while (!queue.isEmpty()) {
				cur = queue.poll(); // 父
				for (int i = 0; i < 26; i++) { // 下级所有的路
					if (cur.nexts[i] != null) { // 该路下有子节点
						// 暂设失败回到根，再沿父fail链寻找更长的可匹配后缀。
						cur.nexts[i].fail = root; // 初始时先设置一个值
						cfail = cur.fail;
						while (cfail != null) { // cur不是头节点
							if (cfail.nexts[i] != null) {
								cur.nexts[i].fail = cfail.nexts[i];
								break;
							}
							cfail = cfail.fail;
						}
						queue.add(cur.nexts[i]);
					}
				}
			}
		}

		public int containNum(String content) {
			char[] str = content.toCharArray();
			Node cur = root;
			Node follow = null;
			int index = 0;
			int ans = 0;
			for (int i = 0; i < str.length; i++) {
				index = str[i] - 'a';
				while (cur.nexts[index] == null && cur != root) {
					cur = cur.fail;
				}
				cur = cur.nexts[index] != null ? cur.nexts[index] : root;
				follow = cur;
				while (follow != root) {
					if (follow.end == -1) {
						break;
					}
					{ // 不同的需求，在这一段{ }之间修改
						// 该结尾对应的插入条目数计入答案，不按文章出现位置累计。
						ans += follow.end;
						// 破坏性去重标记，此节点以后再命中不重复贡献。
						follow.end = -1;
					} // 不同的需求，在这一段{ }之间修改
					follow = follow.fail;
				}
			}
			return ans;
		}

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		ACAutomation ac = new ACAutomation();
		ac.insert("dhe");
		ac.insert("he");
		ac.insert("c");
		ac.build();
		System.out.println(ac.containNum("cdhe"));
	}
```

#### 题解

**为什么正确**

任何以当前文章位置结尾的模式都必须是当前匹配路径的后缀，fail链枚举这些可用后缀。命中后破坏性标记阻止同一模式在文章多个位置重复贡献，因此它统计的是出现过的模式条目数而非全部出现次数。

**复杂度**

总模式长度P、最大词长L、文章长T：建fail的本实现逐链回退，保守上界O(PL)；查询状态匹配均摊O(T)，AC1会标记经过的节点，输出链总访问O(T+P)。空间O(P)。

**边界与易错点**

查询会破坏end计数，同一自动机不能直接用于第二篇文章并期待独立结果，需重建或重置。仅支持小写字母；不能把本方法答案说成所有匹配位置数量。

### 32.4 AC 自动机：出现过的不同模式串

#### 题目

输入小写模式集合与文章；单次查询返回出现过的不同模式字符串。

**输入、输出与约束**

输入小写模式集合与文章；单次查询返回出现过的不同模式字符串。

**函数签名（课程入口）**

```java
public void insert(String s);
public void build();
public List<String> containWords(String content);
```

**示例**

```text
输入：模式["he","she"]，文章"shehe"
输出：包含she、he，各一次
```

解释：第一次读完she时两模式都命中，后面的he不再重复加入。

**出处与版本差异**

- [课程源码：class32/Code04_AC2.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class32/Code04_AC2.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

建立前缀树和fail链后，逐字符扫描文章；沿fail链收集非空end字符串。endUse标记该模式是否已经加入答案，确保每个不同模式文本只返回一次。该版只标记终点，不像AC1标记所有经过节点。

#### 代码答案

```java
package class32;

import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Queue;

public class Code04_AC2 {

	// 前缀树的节点
	public static class Node {
		// 如果一个node，end为空，不是结尾
		// 如果end不为空，表示这个点是某个字符串的结尾，end的值就是这个字符串
		public String end;
		// 只有在上面的end变量不为空的时候，endUse才有意义
		// 表示，这个字符串之前有没有加入过答案
		public boolean endUse;
		public Node fail;
		public Node[] nexts;

		public Node() {
			endUse = false;
			end = null;
			fail = null;
			nexts = new Node[26];
		}
	}

	public static class ACAutomation {
		private Node root;

		public ACAutomation() {
			root = new Node();
		}

		public void insert(String s) {
			char[] str = s.toCharArray();
			Node cur = root;
			int index = 0;
			for (int i = 0; i < str.length; i++) {
				index = str[i] - 'a';
				if (cur.nexts[index] == null) {
					cur.nexts[index] = new Node();
				}
				cur = cur.nexts[index];
			}
			cur.end = s;
		}

		public void build() {
			Queue<Node> queue = new LinkedList<>();
			queue.add(root);
			Node cur = null;
			Node cfail = null;
			while (!queue.isEmpty()) {
				// 某个父亲，cur
				cur = queue.poll();
				for (int i = 0; i < 26; i++) { // 所有的路
					// cur -> 父亲  i号儿子，必须把i号儿子的fail指针设置好！
					if (cur.nexts[i] != null) { // 如果真的有i号儿子
						cur.nexts[i].fail = root;
						cfail = cur.fail;
						while (cfail != null) {
							if (cfail.nexts[i] != null) {
								cur.nexts[i].fail = cfail.nexts[i];
								break;
							}
							cfail = cfail.fail;
						}
						queue.add(cur.nexts[i]);
					}
				}
			}
		}

		// 大文章：content
		public List<String> containWords(String content) {
			char[] str = content.toCharArray();
			Node cur = root;
			Node follow = null;
			int index = 0;
			List<String> ans = new ArrayList<>();
			for (int i = 0; i < str.length; i++) {
				index = str[i] - 'a'; // 路
				// 如果当前字符在这条路上没配出来，就随着fail方向走向下条路径
				while (cur.nexts[index] == null && cur != root) {
					cur = cur.fail;
				}
				// 1) 现在来到的路径，是可以继续匹配的
				// 2) 现在来到的节点，就是前缀树的根节点
				cur = cur.nexts[index] != null ? cur.nexts[index] : root;
				follow = cur;
				while (follow != root) {
					// 该终点此前已处理，其更短后缀也已处理，可提前停止当前输出链。
					if (follow.endUse) {
						break;
					}
					// 不同的需求，在这一段之间修改
					if (follow.end != null) {
						// 当前fail节点是尚未输出的模式终点，记录这个完整词。
						ans.add(follow.end);
						// 标记已输出该词，后续重复出现不再加入。
						follow.endUse = true;
					}
					// 不同的需求，在这一段之间修改
					follow = follow.fail;
				}
			}
			return ans;
		}

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		ACAutomation ac = new ACAutomation();
		ac.insert("dhe");
		ac.insert("he");
		ac.insert("abcdheks");
		// 设置fail指针
		ac.build();

		List<String> contains = ac.containWords("abcdhekskdjfafhasldkflskdjhwqaeruv");
		for (String word : contains) {
			System.out.println(word);
		}
	}
```

#### 题解

**为什么正确**

fail链覆盖当前匹配串的所有可用模式后缀，所以全部以当前字符结尾的命中都能发现。终点记录完整模式文本，endUse使以后同词命中被忽略，从而得到出现过的不同模式集合。

**复杂度**

P为总模式长度、L为最长模式、T为文章长：本构建保守O(PL)，查询fail输出链可能重复扫非终点，保守O(TL)，空间O(P+输出总字符)。

**边界与易错点**

endUse会跨查询保留，复用自动机前需清理。相同词重复insert只保留一个end文本。返回列表顺序依扫描和fail顺序，不保证按插入顺序。


<a id="course-33"></a>

## 第 33 课：哈希函数

### 33.1 消息摘要哈希函数示例

#### 题目

给定消息摘要算法名称和输入字符串，计算并返回对应的十六进制哈希摘要。

**输入、输出与约束**

输入支持的摘要算法名和字符串；返回大写十六进制摘要，不是Java对象的int hashCode。

**函数签名（课程入口）**

```java
public String hashCode(String input);
```

**示例**

```text
输入：算法MD5，UTF-8文本"abc"
输出：900150983CD24FB0D6963F7D28E17F72
```

解释：相同字节重复计算得到相同32位十六进制摘要。

**出处与版本差异**

- [课程源码：class33/Hash.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class33/Hash.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

用MessageDigest将明确编码的文本字节映射为固定长度摘要，再把摘要字节转成十六进制以便显示。相同输入字节和同一摘要算法产生相同结果；课程借此观察输入微小变化对摘要的影响。

#### 代码答案

```java
package class33;

import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.security.Security;

// 需要自己找一下javax.xml.bind的jar，然后导入到项目
import javax.xml.bind.DatatypeConverter;

public class Hash {

	private MessageDigest hash;

	public Hash(String algorithm) {
		try {
			// 按算法名选择摘要实现，不同算法的输出长度和内容不同。
			hash = MessageDigest.getInstance(algorithm);
		} catch (NoSuchAlgorithmException e) {
			e.printStackTrace();
		}
	}

	public String hashCode(String input) {
		// 对输入字节完成一次摘要计算，输出原始摘要字节。
		return DatatypeConverter.printHexBinary(hash.digest(input.getBytes())).toUpperCase();
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		System.out.println("支持的算法 : ");
		for (String str : Security.getAlgorithms("MessageDigest")) {
			System.out.println(str);
		}
		System.out.println("=======");

		String algorithm = "MD5";
		Hash hash = new Hash(algorithm);

		String input1 = "zuochengyunzuochengyun1";
		String input2 = "zuochengyunzuochengyun2";
		String input3 = "zuochengyunzuochengyun3";
		String input4 = "zuochengyunzuochengyun4";
		String input5 = "zuochengyunzuochengyun5";
		System.out.println(hash.hashCode(input1));
		System.out.println(hash.hashCode(input2));
		System.out.println(hash.hashCode(input3));
		System.out.println(hash.hashCode(input4));
		System.out.println(hash.hashCode(input5));

	}
```

#### 题解

**为什么正确**

摘要计算对给定算法和输入字节是确定性的，因此可作为快速比较指纹；但输出长度有限、输入集合无限，必然存在碰撞，摘要相同不能构成内容相同的逻辑证明。

**复杂度**

输入B字节的摘要计算O(B)，输出长度由摘要算法固定；一次性getBytes会额外分配O(B)空间。

**边界与易错点**

应固定UTF-8，避免平台默认编码使结果不同。课程示例的MD5用于展示摘要性质，不应把它当作无碰撞编码。旧javax.xml.bind依赖在新版JDK中需替换或显式引入。


<a id="course-34"></a>

## 第 34 课：资源限制类题目说明

### 34.1 资源限制类题目说明

#### 题目

给定可能大于内存的整数文件及明确值域，讨论如何在内存限制下检测重复值。区分能够直接使用位图和必须外部分桶的情况。

**输入、输出与约束**

本条为资源约束方案题；具体输入格式、可用内存与输出需由具体面试题给定，不能伪装成统一函数提交题。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：值域0..15，输入[1,4,7,4]，可用16位
输出：发现重复值4
```

解释：第2次访问4时对应位已经为1。

**出处与版本差异**

- [课程源码：class34/ReadMe.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class34/ReadMe.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

资源限制题先量化输入规模、值域、内存和允许扫描次数，再选择位图、分桶、外部排序等方法。例如在0..U-1中判断是否重复，用U位记录出现情况；内存不足时，按高位把数据分桶写入外部存储，再逐桶检查。

#### 代码答案

```java
// 本章并无code，因为资源限制类题目输入需要的条件较多并且真的实现代码量巨大
// 面试中这类题目出现也就和面试官聊解法，不会有代码实现的要求
```

---

##### 边界核对

用题面示例核对接口，再检查：不能只按元素个数估计对象内存；位图按值域占空间。哈希分桶可能偏斜，不能无条件假设所有桶都均匀装得下。课程本章原文件仅说明没有统一实现。

本条未附独立随机对数器。

#### 题解

**为什么正确**

位图把每个合法值映射到唯一一位，重复到达已经置位的位置就是重复值。分桶函数使相同值必入同一桶，因此逐桶判重不会漏掉跨输入位置的重复；每桶必须能独立装入内存，过大桶继续细分。

**复杂度**

单次位图扫描O(N)时间、ceil(U/8)字节位存储；外部分桶增加写读成本，复杂度须按总数据量、桶数和扫描轮数分别计。

**边界与易错点**

不能只按元素个数估计对象内存；位图按值域占空间。哈希分桶可能偏斜，不能无条件假设所有桶都均匀装得下。课程本章原文件仅说明没有统一实现。


<a id="course-35"></a>

## 第 35 课：AVL 平衡搜索树

### 35.1 AVL 平衡搜索树实现有序表

#### 题目

实现一个键有序的映射结构，支持插入、删除、查询以及前驱、后继等有序操作。

**输入、输出与约束**

键可比较且不为null；支持有序映射、最小最大键与上下界查询。

**函数签名（课程入口）**

```java
public int size();
public boolean containsKey(K key);
public void put(K key, V value);
public void remove(K key);
public V get(K key);
public K firstKey();
```

**示例**

```text
输入：依次put键3、2、1
输出：右旋后根为2，左右孩子为1、3
```

解释：中序仍为1、2、3，但高度恢复平衡。

**出处与版本差异**

- [课程源码：class35/Code01_AVLTreeMap.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class35/Code01_AVLTreeMap.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

AVL树在搜索树有序性的基础上保存每节点高度。插入或删除后沿递归返回路径重算高度；左右高度差超过1时，根据较高子树的内外侧高度决定单旋或双旋，恢复局部平衡。

#### 代码答案

```java
package class35;

public class Code01_AVLTreeMap {

	public static class AVLNode<K extends Comparable<K>, V> {
		public K k;
		public V v;
		public AVLNode<K, V> l;
		public AVLNode<K, V> r;
		public int h;

		public AVLNode(K key, V value) {
			k = key;
			v = value;
			h = 1;
		}
	}

	public static class AVLTreeMap<K extends Comparable<K>, V> {
		private AVLNode<K, V> root;
		private int size;

		public AVLTreeMap() {
			root = null;
			size = 0;
		}

		private AVLNode<K, V> rightRotate(AVLNode<K, V> cur) {
			AVLNode<K, V> left = cur.l;
			cur.l = left.r;
			left.r = cur;
			// 结构修改后按左右孩子高度重算当前节点高度，供祖先判断平衡。
			cur.h = Math.max((cur.l != null ? cur.l.h : 0), (cur.r != null ? cur.r.h : 0)) + 1;
			left.h = Math.max((left.l != null ? left.l.h : 0), (left.r != null ? left.r.h : 0)) + 1;
			return left;
		}

		private AVLNode<K, V> leftRotate(AVLNode<K, V> cur) {
			AVLNode<K, V> right = cur.r;
			cur.r = right.l;
			right.l = cur;
			// 结构修改后按左右孩子高度重算当前节点高度，供祖先判断平衡。
			cur.h = Math.max((cur.l != null ? cur.l.h : 0), (cur.r != null ? cur.r.h : 0)) + 1;
			right.h = Math.max((right.l != null ? right.l.h : 0), (right.r != null ? right.r.h : 0)) + 1;
			return right;
		}

		private AVLNode<K, V> maintain(AVLNode<K, V> cur) {
			if (cur == null) {
				return null;
			}
			int leftHeight = cur.l != null ? cur.l.h : 0;
			int rightHeight = cur.r != null ? cur.r.h : 0;
			if (Math.abs(leftHeight - rightHeight) > 1) {
				if (leftHeight > rightHeight) {
					int leftLeftHeight = cur.l != null && cur.l.l != null ? cur.l.l.h : 0;
					int leftRightHeight = cur.l != null && cur.l.r != null ? cur.l.r.h : 0;
					if (leftLeftHeight >= leftRightHeight) {
						cur = rightRotate(cur);
					} else {
						// LR型先左旋左孩子，把折线变为外侧直线，再右旋当前根。
						cur.l = leftRotate(cur.l);
						cur = rightRotate(cur);
					}
				} else {
					int rightLeftHeight = cur.r != null && cur.r.l != null ? cur.r.l.h : 0;
					int rightRightHeight = cur.r != null && cur.r.r != null ? cur.r.r.h : 0;
					if (rightRightHeight >= rightLeftHeight) {
						cur = leftRotate(cur);
					} else {
						// RL型先右旋右孩子，再左旋当前根。
						cur.r = rightRotate(cur.r);
						cur = leftRotate(cur);
					}
				}
			}
			return cur;
		}

		private AVLNode<K, V> findLastIndex(K key) {
			AVLNode<K, V> pre = root;
			AVLNode<K, V> cur = root;
			while (cur != null) {
				pre = cur;
				if (key.compareTo(cur.k) == 0) {
					break;
				} else if (key.compareTo(cur.k) < 0) {
					cur = cur.l;
				} else {
					cur = cur.r;
				}
			}
			return pre;
		}

		private AVLNode<K, V> findLastNoSmallIndex(K key) {
			AVLNode<K, V> ans = null;
			AVLNode<K, V> cur = root;
			while (cur != null) {
				if (key.compareTo(cur.k) == 0) {
					ans = cur;
					break;
				} else if (key.compareTo(cur.k) < 0) {
					ans = cur;
					cur = cur.l;
				} else {
					cur = cur.r;
				}
			}
			return ans;
		}

		private AVLNode<K, V> findLastNoBigIndex(K key) {
			AVLNode<K, V> ans = null;
			AVLNode<K, V> cur = root;
			while (cur != null) {
				if (key.compareTo(cur.k) == 0) {
					ans = cur;
					break;
				} else if (key.compareTo(cur.k) < 0) {
					cur = cur.l;
				} else {
					ans = cur;
					cur = cur.r;
				}
			}
			return ans;
		}

		private AVLNode<K, V> add(AVLNode<K, V> cur, K key, V value) {
			if (cur == null) {
				return new AVLNode<K, V>(key, value);
			} else {
				if (key.compareTo(cur.k) < 0) {
					cur.l = add(cur.l, key, value);
				} else {
					cur.r = add(cur.r, key, value);
				}
				// 结构修改后按左右孩子高度重算当前节点高度，供祖先判断平衡。
				cur.h = Math.max(cur.l != null ? cur.l.h : 0, cur.r != null ? cur.r.h : 0) + 1;
				return maintain(cur);
			}
		}

		// 在cur这棵树上，删掉key所代表的节点
		// 返回cur这棵树的新头部
		private AVLNode<K, V> delete(AVLNode<K, V> cur, K key) {
			if (key.compareTo(cur.k) > 0) {
				cur.r = delete(cur.r, key);
			} else if (key.compareTo(cur.k) < 0) {
				cur.l = delete(cur.l, key);
			} else {
				if (cur.l == null && cur.r == null) {
					cur = null;
				} else if (cur.l == null && cur.r != null) {
					cur = cur.r;
				} else if (cur.l != null && cur.r == null) {
					cur = cur.l;
				} else {
					AVLNode<K, V> des = cur.r;
					while (des.l != null) {
						des = des.l;
					}
					cur.r = delete(cur.r, des.k);
					des.l = cur.l;
					des.r = cur.r;
					cur = des;
				}
			}
			if (cur != null) {
				// 结构修改后按左右孩子高度重算当前节点高度，供祖先判断平衡。
				cur.h = Math.max(cur.l != null ? cur.l.h : 0, cur.r != null ? cur.r.h : 0) + 1;
			}
			return maintain(cur);
		}

		public int size() {
			return size;
		}

		public boolean containsKey(K key) {
			if (key == null) {
				return false;
			}
			AVLNode<K, V> lastNode = findLastIndex(key);
			return lastNode != null && key.compareTo(lastNode.k) == 0 ? true : false;
		}

		public void put(K key, V value) {
			if (key == null) {
				return;
			}
			AVLNode<K, V> lastNode = findLastIndex(key);
			if (lastNode != null && key.compareTo(lastNode.k) == 0) {
				lastNode.v = value;
			} else {
				size++;
				root = add(root, key, value);
			}
		}

		public void remove(K key) {
			if (key == null) {
				return;
			}
			if (containsKey(key)) {
				size--;
				root = delete(root, key);
			}
		}

		public V get(K key) {
			if (key == null) {
				return null;
			}
			AVLNode<K, V> lastNode = findLastIndex(key);
			if (lastNode != null && key.compareTo(lastNode.k) == 0) {
				return lastNode.v;
			}
			return null;
		}

		public K firstKey() {
			if (root == null) {
				return null;
			}
			AVLNode<K, V> cur = root;
			while (cur.l != null) {
				cur = cur.l;
			}
			return cur.k;
		}

		public K lastKey() {
			if (root == null) {
				return null;
			}
			AVLNode<K, V> cur = root;
			while (cur.r != null) {
				cur = cur.r;
			}
			return cur.k;
		}

		public K floorKey(K key) {
			if (key == null) {
				return null;
			}
			AVLNode<K, V> lastNoBigNode = findLastNoBigIndex(key);
			return lastNoBigNode == null ? null : lastNoBigNode.k;
		}

		public K ceilingKey(K key) {
			if (key == null) {
				return null;
			}
			AVLNode<K, V> lastNoSmallNode = findLastNoSmallIndex(key);
			return lastNoSmallNode == null ? null : lastNoSmallNode.k;
		}

	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：旋转后先更新降下去的旧根高度，再更新新根。删除也可能连续触发多层修复；键相等应覆盖值而不是新插入重复节点。

本条未附独立随机对数器。

#### 题解

**为什么正确**

旋转仅改变局部父子关系，中序键顺序保持不变，因此不破坏搜索树性质。LL/RR单旋或LR/RL双旋把过高一侧向上提，恢复两边高度差；沿祖先继续修复使整树重新平衡。

**复杂度**

查询、插入、删除最坏O(log N)，空间O(N)，递归栈O(log N)。

**边界与易错点**

旋转后先更新降下去的旧根高度，再更新新根。删除也可能连续触发多层修复；键相等应覆盖值而不是新插入重复节点。


<a id="course-36"></a>

## 第 36 课：SBT 与跳表

### 36.1 SBT 实现有序表

#### 题目

实现一个键有序的映射结构，支持插入、删除、查询以及前驱、后继等有序操作。

**输入、输出与约束**

键可比较且唯一，支持插删查与按有序下标取键值；下标必须合法。

**函数签名（课程入口）**

```java
public int size();
public boolean containsKey(K key);
public void put(K key, V value);
public void remove(K key);
public K getIndexKey(int index);
public V getIndexValue(int index);
```

**示例**

```text
输入：键集合[2,5,8]，查询从0计的下标1
输出：键5
```

解释：左子树大小用于跳过排在前面的键。

**出处与版本差异**

- [课程源码：class36/Code01_SizeBalancedTreeMap.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class36/Code01_SizeBalancedTreeMap.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

SBT（Size Balanced Tree，大小平衡树）保存子树节点数size。检查某侧孙子子树规模是否超过另一侧整棵子树，分别按LL、LR、RR、RL旋转。按秩查询时，用左子树size判断目标在左边、当前根还是右边。

#### 代码答案

```java
package class36;

public class Code01_SizeBalancedTreeMap {

	public static class SBTNode<K extends Comparable<K>, V> {
		public K key;
		public V value;
		public SBTNode<K, V> l;
		public SBTNode<K, V> r;
		public int size; // 不同的key的数量

		public SBTNode(K key, V value) {
			this.key = key;
			this.value = value;
			size = 1;
		}
	}

	public static class SizeBalancedTreeMap<K extends Comparable<K>, V> {
		private SBTNode<K, V> root;

		private SBTNode<K, V> rightRotate(SBTNode<K, V> cur) {
			SBTNode<K, V> leftNode = cur.l;
			// 右旋先把左孩子原右子树接到当前根左侧，保持中序顺序。
			cur.l = leftNode.r;
			// 左孩子升为新根，旧根降为它的右孩子。
			leftNode.r = cur;
			// 旋转不改变这整棵子树的节点集合，新根继承旧总大小。
			leftNode.size = cur.size;
			// 旧根降下去后子树范围改变，重新加左右规模和自身。
			// 旧根的子树范围发生变化，要从新左右孩子重新计算大小。
			cur.size = (cur.l != null ? cur.l.size : 0) + (cur.r != null ? cur.r.size : 0) + 1;
			return leftNode;
		}

		private SBTNode<K, V> leftRotate(SBTNode<K, V> cur) {
			SBTNode<K, V> rightNode = cur.r;
			// 左旋将右孩子的左子树接到旧根右侧，不丢失中间键区间。
			cur.r = rightNode.l;
			// 原右孩子升根，旧根降为左孩子。
			rightNode.l = cur;
			rightNode.size = cur.size;
			// 旧根降下去后子树范围改变，重新加左右规模和自身。
			// 旧根的子树范围发生变化，要从新左右孩子重新计算大小。
			cur.size = (cur.l != null ? cur.l.size : 0) + (cur.r != null ? cur.r.size : 0) + 1;
			return rightNode;
		}

		private SBTNode<K, V> maintain(SBTNode<K, V> cur) {
			if (cur == null) {
				return null;
			}
			int leftSize = cur.l != null ? cur.l.size : 0;
			int leftLeftSize = cur.l != null && cur.l.l != null ? cur.l.l.size : 0;
			int leftRightSize = cur.l != null && cur.l.r != null ? cur.l.r.size : 0;
			int rightSize = cur.r != null ? cur.r.size : 0;
			int rightLeftSize = cur.r != null && cur.r.l != null ? cur.r.l.size : 0;
			int rightRightSize = cur.r != null && cur.r.r != null ? cur.r.r.size : 0;
			// 左左孙树大于右子树，右旋修复这一大小失衡。
			if (leftLeftSize > rightSize) {
				cur = rightRotate(cur);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			// 左右孙树过大，先对左孩子左旋，再对当前根右旋。
			} else if (leftRightSize > rightSize) {
				cur.l = leftRotate(cur.l);
				cur = rightRotate(cur);
				cur.l = maintain(cur.l);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			// 右右孙树过大，左旋修复。
			} else if (rightRightSize > leftSize) {
				cur = leftRotate(cur);
				cur.l = maintain(cur.l);
				cur = maintain(cur);
			// 右左孙树过大，需要先右后左的双旋。
			} else if (rightLeftSize > leftSize) {
				cur.r = rightRotate(cur.r);
				cur = leftRotate(cur);
				cur.l = maintain(cur.l);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			}
			return cur;
		}

		private SBTNode<K, V> findLastIndex(K key) {
			SBTNode<K, V> pre = root;
			SBTNode<K, V> cur = root;
			while (cur != null) {
				pre = cur;
				if (key.compareTo(cur.key) == 0) {
					break;
				} else if (key.compareTo(cur.key) < 0) {
					cur = cur.l;
				} else {
					cur = cur.r;
				}
			}
			return pre;
		}

		private SBTNode<K, V> findLastNoSmallIndex(K key) {
			SBTNode<K, V> ans = null;
			SBTNode<K, V> cur = root;
			while (cur != null) {
				if (key.compareTo(cur.key) == 0) {
					ans = cur;
					break;
				} else if (key.compareTo(cur.key) < 0) {
					ans = cur;
					cur = cur.l;
				} else {
					cur = cur.r;
				}
			}
			return ans;
		}

		private SBTNode<K, V> findLastNoBigIndex(K key) {
			SBTNode<K, V> ans = null;
			SBTNode<K, V> cur = root;
			while (cur != null) {
				if (key.compareTo(cur.key) == 0) {
					ans = cur;
					break;
				} else if (key.compareTo(cur.key) < 0) {
					cur = cur.l;
				} else {
					ans = cur;
					cur = cur.r;
				}
			}
			return ans;
		}

		// 现在，以cur为头的树上，新增，加(key, value)这样的记录
		// 加完之后，会对cur做检查，该调整调整
		// 返回，调整完之后，整棵树的新头部
		private SBTNode<K, V> add(SBTNode<K, V> cur, K key, V value) {
			if (cur == null) {
				return new SBTNode<K, V>(key, value);
			} else {
				// 插入路径上的每棵子树都会多一个节点，先递增计数。
				cur.size++;
				if (key.compareTo(cur.key) < 0) {
					cur.l = add(cur.l, key, value);
				} else {
					cur.r = add(cur.r, key, value);
				}
				// 插入后按子树大小恢复平衡，返回可能因旋转而改变的根。
				return maintain(cur);
			}
		}

		// 在cur这棵树上，删掉key所代表的节点
		// 返回cur这棵树的新头部
		private SBTNode<K, V> delete(SBTNode<K, V> cur, K key) {
			// 删除路径上的每棵子树都会少一个节点，同步减少计数。
			cur.size--;
			if (key.compareTo(cur.key) > 0) {
				cur.r = delete(cur.r, key);
			} else if (key.compareTo(cur.key) < 0) {
				cur.l = delete(cur.l, key);
			} else { // 当前要删掉cur
				if (cur.l == null && cur.r == null) {
					// free cur memory -> C++
					cur = null;
				} else if (cur.l == null && cur.r != null) {
					// free cur memory -> C++
					cur = cur.r;
				} else if (cur.l != null && cur.r == null) {
					// free cur memory -> C++
					cur = cur.l;
				} else { // 有左有右
					SBTNode<K, V> pre = null;
					SBTNode<K, V> des = cur.r;
					des.size--;
					// 两孩子删除时，寻找右子树最左的中序后继。
					while (des.l != null) {
						pre = des;
						des = des.l;
						des.size--;
					}
					if (pre != null) {
						// 后继没有左孩子，用它的右子树填补原位置。
						pre.l = des.r;
						des.r = cur.r;
					}
					// 后继接管被删根的左子树，继续保持键的中序有序性。
					des.l = cur.l;
					des.size = des.l.size + (des.r == null ? 0 : des.r.size) + 1;
					// free cur memory -> C++
					cur = des;
				}
			}
			// cur = maintain(cur);
			return cur;
		}

		private SBTNode<K, V> getIndex(SBTNode<K, V> cur, int kth) {
			// 当前根的子树内名次为左子树大小加1。
			if (kth == (cur.l != null ? cur.l.size : 0) + 1) {
				return cur;
			} else if (kth <= (cur.l != null ? cur.l.size : 0)) {
				return getIndex(cur.l, kth);
			} else {
				// 进入右子树时扣除左子树及当前根，把全局名次转为右树局部名次。
				return getIndex(cur.r, kth - (cur.l != null ? cur.l.size : 0) - 1);
			}
		}

		public int size() {
			return root == null ? 0 : root.size;
		}

		public boolean containsKey(K key) {
			if (key == null) {
				throw new RuntimeException("invalid parameter.");
			}
			SBTNode<K, V> lastNode = findLastIndex(key);
			return lastNode != null && key.compareTo(lastNode.key) == 0 ? true : false;
		}

		// （key，value） put -> 有序表 新增、改value
		public void put(K key, V value) {
			if (key == null) {
				throw new RuntimeException("invalid parameter.");
			}
			SBTNode<K, V> lastNode = findLastIndex(key);
			if (lastNode != null && key.compareTo(lastNode.key) == 0) {
				lastNode.value = value;
			} else {
				root = add(root, key, value);
			}
		}

		public void remove(K key) {
			if (key == null) {
				throw new RuntimeException("invalid parameter.");
			}
			if (containsKey(key)) {
				root = delete(root, key);
			}
		}

		public K getIndexKey(int index) {
			if (index < 0 || index >= this.size()) {
				throw new RuntimeException("invalid parameter.");
			}
			// 公开下标从0开始，内部第k小查询从1开始，入口统一转换。
			return getIndex(root, index + 1).key;
		}

		public V getIndexValue(int index) {
			if (index < 0 || index >= this.size()) {
				throw new RuntimeException("invalid parameter.");
			}
			return getIndex(root, index + 1).value;
		}

		public V get(K key) {
			if (key == null) {
				throw new RuntimeException("invalid parameter.");
			}
			SBTNode<K, V> lastNode = findLastIndex(key);
			if (lastNode != null && key.compareTo(lastNode.key) == 0) {
				return lastNode.value;
			} else {
				return null;
			}
		}

		public K firstKey() {
			if (root == null) {
				return null;
			}
			SBTNode<K, V> cur = root;
			while (cur.l != null) {
				cur = cur.l;
			}
			return cur.key;
		}

		public K lastKey() {
			if (root == null) {
				return null;
			}
			SBTNode<K, V> cur = root;
			while (cur.r != null) {
				cur = cur.r;
			}
			return cur.key;
		}

		public K floorKey(K key) {
			if (key == null) {
				throw new RuntimeException("invalid parameter.");
			}
			SBTNode<K, V> lastNoBigNode = findLastNoBigIndex(key);
			return lastNoBigNode == null ? null : lastNoBigNode.key;
		}

		public K ceilingKey(K key) {
			if (key == null) {
				throw new RuntimeException("invalid parameter.");
			}
			SBTNode<K, V> lastNoSmallNode = findLastNoSmallIndex(key);
			return lastNoSmallNode == null ? null : lastNoSmallNode.key;
		}

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static void printAll(SBTNode<String, Integer> head) {
		System.out.println("Binary Tree:");
		printInOrder(head, 0, "H", 17);
		System.out.println();
	}

	// for test
	public static void printInOrder(SBTNode<String, Integer> head, int height, String to, int len) {
		if (head == null) {
			return;
		}
		printInOrder(head.r, height + 1, "v", len);
		String val = to + "(" + head.key + "," + head.value + ")" + to;
		int lenM = val.length();
		int lenL = (len - lenM) / 2;
		int lenR = len - lenM - lenL;
		val = getSpace(lenL) + val + getSpace(lenR);
		System.out.println(getSpace(height * len) + val);
		printInOrder(head.l, height + 1, "^", len);
	}

	// for test
	public static String getSpace(int num) {
		String space = " ";
		StringBuffer buf = new StringBuffer("");
		for (int i = 0; i < num; i++) {
			buf.append(space);
		}
		return buf.toString();
	}

public static void main(String[] args) {
		SizeBalancedTreeMap<String, Integer> sbt = new SizeBalancedTreeMap<String, Integer>();
		sbt.put("d", 4);
		sbt.put("c", 3);
		sbt.put("a", 1);
		sbt.put("b", 2);
		// sbt.put("e", 5);
		sbt.put("g", 7);
		sbt.put("f", 6);
		sbt.put("h", 8);
		sbt.put("i", 9);
		sbt.put("a", 111);
		System.out.println(sbt.get("a"));
		sbt.put("a", 1);
		System.out.println(sbt.get("a"));
		for (int i = 0; i < sbt.size(); i++) {
			System.out.println(sbt.getIndexKey(i) + " , " + sbt.getIndexValue(i));
		}
		printAll(sbt.root);
		System.out.println(sbt.firstKey());
		System.out.println(sbt.lastKey());
		System.out.println(sbt.floorKey("g"));
		System.out.println(sbt.ceilingKey("g"));
		System.out.println(sbt.floorKey("e"));
		System.out.println(sbt.ceilingKey("e"));
		System.out.println(sbt.floorKey(""));
		System.out.println(sbt.ceilingKey(""));
		System.out.println(sbt.floorKey("j"));
		System.out.println(sbt.ceilingKey("j"));
		sbt.remove("d");
		printAll(sbt.root);
		sbt.remove("f");
		printAll(sbt.root);

	}
```

#### 题解

**为什么正确**

旋转保持中序顺序，size更新保持每棵子树真实节点数；按秩搜索时，左子树占据连续的前size(left)个秩，根占下一位，因此可精确减去已经跳过的节点数量。

**复杂度**

维护平衡时查询与插入O(log N)。本课程删除未调用maintain，任意删除序列后的严格最坏O(log N)不能仅凭插入维护推出；实际按当前树高H计操作O(H)。空间O(N)。

**边界与易错点**

size是节点个数，不是高度；重复键覆盖值不增加size。源码省略删除平衡维护，需要与完整SBT复杂度保证区分。

### 36.2 跳表实现有序表

#### 题目

实现一个键有序的映射结构，支持插入、删除、查询以及前驱、后继等有序操作。

**输入、输出与约束**

输入可比较非null键值操作；提供有序表和前驱后继查询。

**函数签名（课程入口）**

```java
public boolean isKeyLess(K otherKey);
public boolean isKeyEqual(K otherKey);
public boolean containsKey(K key);
public void put(K key, V value);
public V get(K key);
public void remove(K key);
```

**示例**

```text
输入：键集合[1,4,7]，floorKey(6)、ceilingKey(6)
输出：4、7
```

解释：从高层定位附近区间，再在底层确定紧邻边界。

**出处与版本差异**

- [课程源码：class36/Code02_SkipListMap.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class36/Code02_SkipListMap.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

底层链表保存全部有序键，高层以随机概率抽取节点建立快道。查找从最高层向右走到目标前驱，再下降一层继续；插入先随机决定高度，在涉及层接入新节点，删除则在每层跳过目标。

#### 代码答案

```java
package class36;

import java.util.ArrayList;

public class Code02_SkipListMap {

	// 跳表的节点定义
	public static class SkipListNode<K extends Comparable<K>, V> {
		public K key;
		public V val;
		public ArrayList<SkipListNode<K, V>> nextNodes;

		public SkipListNode(K k, V v) {
			key = k;
			val = v;
			nextNodes = new ArrayList<SkipListNode<K, V>>();
		}

		// 遍历的时候，如果是往右遍历到的null(next == null), 遍历结束
		// 头(null), 头节点的null，认为最小
		// node  -> 头，node(null, "")  node.isKeyLess(!null)  true
		// node里面的key是否比otherKey小，true，不是false
		public boolean isKeyLess(K otherKey) {
			//  otherKey == null -> false
			return otherKey != null && (key == null || key.compareTo(otherKey) < 0);
		}

		public boolean isKeyEqual(K otherKey) {
			return (key == null && otherKey == null)
					|| (key != null && otherKey != null && key.compareTo(otherKey) == 0);
		}

	}

	public static class SkipListMap<K extends Comparable<K>, V> {
		private static final double PROBABILITY = 0.5; // < 0.5 继续做，>=0.5 停
		private SkipListNode<K, V> head;
		private int size;
		private int maxLevel;

		public SkipListMap() {
			head = new SkipListNode<K, V>(null, null);
			head.nextNodes.add(null); // 0
			size = 0;
			maxLevel = 0;
		}

		// 从最高层开始，一路找下去，
		// 最终，找到第0层的<key的最右的节点
		private SkipListNode<K, V> mostRightLessNodeInTree(K key) {
			if (key == null) {
				return null;
			}
			int level = maxLevel;
			SkipListNode<K, V> cur = head;
			while (level >= 0) { // 从上层跳下层
				//  cur  level  -> level-1
				// 当前层已到目标之前最右位置，下降后在更细粒度链表继续定位。
				cur = mostRightLessNodeInLevel(key, cur, level--);
			}
			return cur;
		}

		// 在level层里，如何往右移动
		// 现在来到的节点是cur，来到了cur的level层，在level层上，找到<key最后一个节点并返回
		private SkipListNode<K, V> mostRightLessNodeInLevel(K key,
				SkipListNode<K, V> cur,
				int level) {
			SkipListNode<K, V> next = cur.nextNodes.get(level);
			while (next != null && next.isKeyLess(key)) {
				cur = next;
				next = cur.nextNodes.get(level);
			}
			return cur;
		}

		public boolean containsKey(K key) {
			if (key == null) {
				return false;
			}
			SkipListNode<K, V> less = mostRightLessNodeInTree(key);
			SkipListNode<K, V> next = less.nextNodes.get(0);
			return next != null && next.isKeyEqual(key);
		}

		// 新增、改value
		public void put(K key, V value) {
			if (key == null) {
				return;
			}
			// 0层上，最右一个，< key 的Node -> >key
			SkipListNode<K, V> less = mostRightLessNodeInTree(key);
			SkipListNode<K, V> find = less.nextNodes.get(0);
			if (find != null && find.isKeyEqual(key)) {
				find.val = value;
			} else { // find == null   8   7   9
				size++;
				int newNodeLevel = 0;
				// 每次独立决定是否再晋升一层，使高层节点数按比例递减。
				while (Math.random() < PROBABILITY) {
					newNodeLevel++;
				}
				// newNodeLevel
				while (newNodeLevel > maxLevel) {
					head.nextNodes.add(null);
					maxLevel++;
				}
				SkipListNode<K, V> newNode = new SkipListNode<K, V>(key, value);
				for (int i = 0; i <= newNodeLevel; i++) {
					newNode.nextNodes.add(null);
				}
				int level = maxLevel;
				SkipListNode<K, V> pre = head;
				while (level >= 0) {
					// level 层中，找到最右的 < key 的节点
					pre = mostRightLessNodeInLevel(key, pre, level);
					if (level <= newNodeLevel) {
						newNode.nextNodes.set(level, pre.nextNodes.get(level));
						pre.nextNodes.set(level, newNode);
					}
					// 当前层已到目标之前最右位置，下降后在更细粒度链表继续定位。
					level--;
				}
			}
		}

		public V get(K key) {
			if (key == null) {
				return null;
			}
			SkipListNode<K, V> less = mostRightLessNodeInTree(key);
			SkipListNode<K, V> next = less.nextNodes.get(0);
			return next != null && next.isKeyEqual(key) ? next.val : null;
		}

		public void remove(K key) {
			if (containsKey(key)) {
				size--;
				int level = maxLevel;
				SkipListNode<K, V> pre = head;
				while (level >= 0) {
					pre = mostRightLessNodeInLevel(key, pre, level);
					SkipListNode<K, V> next = pre.nextNodes.get(level);
					// 1）在这一层中，pre下一个就是key
					// 2）在这一层中，pre的下一个key是>要删除key
					if (next != null && next.isKeyEqual(key)) {
						// free delete node memory -> C++
						// level : pre -> next(key) -> ...
						pre.nextNodes.set(level, next.nextNodes.get(level));
					}
					// 在level层只有一个节点了，就是默认节点head
					if (level != 0 && pre == head && pre.nextNodes.get(level) == null) {
						head.nextNodes.remove(level);
						maxLevel--;
					}
					// 当前层已到目标之前最右位置，下降后在更细粒度链表继续定位。
					level--;
				}
			}
		}

		public K firstKey() {
			return head.nextNodes.get(0) != null ? head.nextNodes.get(0).key : null;
		}

		public K lastKey() {
			int level = maxLevel;
			SkipListNode<K, V> cur = head;
			while (level >= 0) {
				SkipListNode<K, V> next = cur.nextNodes.get(level);
				while (next != null) {
					cur = next;
					next = cur.nextNodes.get(level);
				}
				// 当前层已到目标之前最右位置，下降后在更细粒度链表继续定位。
				level--;
			}
			return cur.key;
		}

		public K ceilingKey(K key) {
			if (key == null) {
				return null;
			}
			SkipListNode<K, V> less = mostRightLessNodeInTree(key);
			SkipListNode<K, V> next = less.nextNodes.get(0);
			return next != null ? next.key : null;
		}

		public K floorKey(K key) {
			if (key == null) {
				return null;
			}
			SkipListNode<K, V> less = mostRightLessNodeInTree(key);
			SkipListNode<K, V> next = less.nextNodes.get(0);
			return next != null && next.isKeyEqual(key) ? next.key : less.key;
		}

		public int size() {
			return size;
		}

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static void printAll(SkipListMap<String, String> obj) {
		for (int i = obj.maxLevel; i >= 0; i--) {
			System.out.print("Level " + i + " : ");
			SkipListNode<String, String> cur = obj.head;
			while (cur.nextNodes.get(i) != null) {
				SkipListNode<String, String> next = cur.nextNodes.get(i);
				System.out.print("(" + next.key + " , " + next.val + ") ");
				cur = next;
			}
			System.out.println();
		}
	}

public static void main(String[] args) {
		SkipListMap<String, String> test = new SkipListMap<>();
		printAll(test);
		System.out.println("======================");
		test.put("A", "10");
		printAll(test);
		System.out.println("======================");
		test.remove("A");
		printAll(test);
		System.out.println("======================");
		test.put("E", "E");
		test.put("B", "B");
		test.put("A", "A");
		test.put("F", "F");
		test.put("C", "C");
		test.put("D", "D");
		printAll(test);
		System.out.println("======================");
		System.out.println(test.containsKey("B"));
		System.out.println(test.containsKey("Z"));
		System.out.println(test.firstKey());
		System.out.println(test.lastKey());
		System.out.println(test.floorKey("D"));
		System.out.println(test.ceilingKey("D"));
		System.out.println("======================");
		test.remove("D");
		printAll(test);
		System.out.println("======================");
		System.out.println(test.floorKey("D"));
		System.out.println(test.ceilingKey("D"));
		

	}
```

#### 题解

**为什么正确**

每层都是下一层的有序子序列，从不越过目标的前驱位置下降，不会错过底层目标。随机晋升让高层节点数量几何递减，平均缩短横向扫描距离。

**复杂度**

期望查询、插入、删除O(log N)，最坏O(N)；期望空间O(N)，随机最高层高度期望O(log N)。

**边界与易错点**

随机平衡是期望保证，不是确定性最坏保证。删除最高层最后节点后需收缩空层；一个节点只在其实际高度范围内拥有next指针。


<a id="course-37"></a>

## 第 37 课：有序表的工程应用

### 37.1 区间和个数（有序表实现）

#### 题目

给定整数数组 `nums` 和边界 `lower`、`upper`，返回累加和位于闭区间 `[lower, upper]` 的非空连续子数组数量。

**输入、输出与约束**

输入整数数组和lower≤upper；返回闭值域内的非空区间和数量。

**函数签名（课程入口）**

```java
public static int countRangeSum1(int[] nums, int lower, int upper);
public void add(long sum);
public long lessKeySize(long key);
public long moreKeySize(long key);
public static int countRangeSum2(int[] nums, int lower, int upper);
```

**示例**

```text
输入：nums=[0,0], lower=0, upper=0
输出：3
```

解释：两个单元素区间加整个区间；重复前缀0的次数不可去重。

**出处与版本差异**

- [课程源码：class37/Code01_CountofRangeSum.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class37/Code01_CountofRangeSum.java)。
- [LeetCode 原题 327. 区间和的个数（Count of Range Sum）](https://leetcode.com/problems/count-of-range-sum/)

课程版本说明：

- LeetCode 327：本节使用有序表。

#### 思路

与第5课同一计数问题，但本节有序表版按原下标扫描前缀和。当前sum需要先前前缀位于[sum-upper,sum-lower]，用支持重复计数的有序表查询两个秩之差，再插入sum。开头先插入前缀0。源码也保留归并版本作对照。

#### 代码答案

```java
package class37;

import java.util.HashSet;

public class Code01_CountofRangeSum {

	public static int countRangeSum1(int[] nums, int lower, int upper) {
		int n = nums.length;
		long[] sums = new long[n + 1];
		for (int i = 0; i < n; ++i)
			sums[i + 1] = sums[i] + nums[i];
		return countWhileMergeSort(sums, 0, n + 1, lower, upper);
	}

	private static int countWhileMergeSort(long[] sums, int start, int end, int lower, int upper) {
		if (end - start <= 1)
			return 0;
		int mid = (start + end) / 2;
		int count = countWhileMergeSort(sums, start, mid, lower, upper)
				+ countWhileMergeSort(sums, mid, end, lower, upper);
		int j = mid, k = mid, t = mid;
		long[] cache = new long[end - start];
		for (int i = start, r = 0; i < mid; ++i, ++r) {
			while (k < end && sums[k] - sums[i] < lower)
				k++;
			while (j < end && sums[j] - sums[i] <= upper)
				j++;
			while (t < end && sums[t] < sums[i])
				cache[r++] = sums[t++];
			cache[r] = sums[i];
			count += j - k;
		}
		System.arraycopy(cache, 0, sums, start, t - start);
		return count;
	}

	public static class SBTNode {
		public long key;
		public SBTNode l;
		public SBTNode r;
		public long size; // 不同key的size
		public long all; // 总的size

		public SBTNode(long k) {
			key = k;
			size = 1;
			all = 1;
		}
	}

	public static class SizeBalancedTreeSet {
		private SBTNode root;
		private HashSet<Long> set = new HashSet<>();

		private SBTNode rightRotate(SBTNode cur) {
			long same = cur.all - (cur.l != null ? cur.l.all : 0) - (cur.r != null ? cur.r.all : 0);
			SBTNode leftNode = cur.l;
			// 右旋先把左孩子原右子树接到当前根左侧，保持中序顺序。
			cur.l = leftNode.r;
			// 左孩子升为新根，旧根降为它的右孩子。
			leftNode.r = cur;
			// 旋转不改变这整棵子树的节点集合，新根继承旧总大小。
			leftNode.size = cur.size;
			// 旧根的子树范围发生变化，要从新左右孩子重新计算大小。
			cur.size = (cur.l != null ? cur.l.size : 0) + (cur.r != null ? cur.r.size : 0) + 1;
			// all modify
			leftNode.all = cur.all;
			cur.all = (cur.l != null ? cur.l.all : 0) + (cur.r != null ? cur.r.all : 0) + same;
			return leftNode;
		}

		private SBTNode leftRotate(SBTNode cur) {
			long same = cur.all - (cur.l != null ? cur.l.all : 0) - (cur.r != null ? cur.r.all : 0);
			SBTNode rightNode = cur.r;
			// 左旋将右孩子的左子树接到旧根右侧，不丢失中间键区间。
			cur.r = rightNode.l;
			// 原右孩子升根，旧根降为左孩子。
			rightNode.l = cur;
			rightNode.size = cur.size;
			// 旧根的子树范围发生变化，要从新左右孩子重新计算大小。
			cur.size = (cur.l != null ? cur.l.size : 0) + (cur.r != null ? cur.r.size : 0) + 1;
			// all modify
			rightNode.all = cur.all;
			cur.all = (cur.l != null ? cur.l.all : 0) + (cur.r != null ? cur.r.all : 0) + same;
			return rightNode;
		}

		private SBTNode maintain(SBTNode cur) {
			if (cur == null) {
				return null;
			}
			long leftSize = cur.l != null ? cur.l.size : 0;
			long leftLeftSize = cur.l != null && cur.l.l != null ? cur.l.l.size : 0;
			long leftRightSize = cur.l != null && cur.l.r != null ? cur.l.r.size : 0;
			long rightSize = cur.r != null ? cur.r.size : 0;
			long rightLeftSize = cur.r != null && cur.r.l != null ? cur.r.l.size : 0;
			long rightRightSize = cur.r != null && cur.r.r != null ? cur.r.r.size : 0;
			// 左左孙树大于右子树，右旋修复这一大小失衡。
			if (leftLeftSize > rightSize) {
				cur = rightRotate(cur);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			// 左右孙树过大，先对左孩子左旋，再对当前根右旋。
			} else if (leftRightSize > rightSize) {
				cur.l = leftRotate(cur.l);
				cur = rightRotate(cur);
				cur.l = maintain(cur.l);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			// 右右孙树过大，左旋修复。
			} else if (rightRightSize > leftSize) {
				cur = leftRotate(cur);
				cur.l = maintain(cur.l);
				cur = maintain(cur);
			// 右左孙树过大，需要先右后左的双旋。
			} else if (rightLeftSize > leftSize) {
				cur.r = rightRotate(cur.r);
				cur = leftRotate(cur);
				cur.l = maintain(cur.l);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			}
			return cur;
		}

		private SBTNode add(SBTNode cur, long key, boolean contains) {
			if (cur == null) {
				return new SBTNode(key);
			} else {
				// 每次出现前缀都增加重数，即使键值已经存在。
				cur.all++;
				if (key == cur.key) {
					return cur;
				} else { // 还在左滑或者右滑
					// 只有首次出现的新键才增加不同键节点规模size。
					if (!contains) {
						// 插入路径上的每棵子树都会多一个节点，先递增计数。
						cur.size++;
					}
					if (key < cur.key) {
						cur.l = add(cur.l, key, contains);
					} else {
						cur.r = add(cur.r, key, contains);
					}
					// 插入后按子树大小恢复平衡，返回可能因旋转而改变的根。
					return maintain(cur);
				}
			}
		}

		public void add(long sum) {
			boolean contains = set.contains(sum);
			root = add(root, sum, contains);
			set.add(sum);
		}

		public long lessKeySize(long key) {
			SBTNode cur = root;
			long ans = 0;
			while (cur != null) {
				if (key == cur.key) {
					return ans + (cur.l != null ? cur.l.all : 0);
				} else if (key < cur.key) {
					cur = cur.l;
				} else {
					ans += cur.all - (cur.r != null ? cur.r.all : 0);
					cur = cur.r;
				}
			}
			return ans;
		}

		// > 7 8...
		// <8 ...<=7
		public long moreKeySize(long key) {
			return root != null ? (root.all - lessKeySize(key + 1)) : 0;
		}

	}

	public static int countRangeSum2(int[] nums, int lower, int upper) {
		// 黑盒，加入数字（前缀和），不去重，可以接受重复数字
		// < num , 有几个数？
		SizeBalancedTreeSet treeSet = new SizeBalancedTreeSet();
		long sum = 0;
		int ans = 0;
		// 插入空前缀，使从原数组起点开始的子数组也有历史前缀可配对。
		treeSet.add(0);// 一个数都没有的时候，就已经有一个前缀和累加和为0，
		for (int i = 0; i < nums.length; i++) {
			sum += nums[i];
			// [sum - upper, sum - lower]
			// [10, 20] ?
			// < 10 ?  < 21 ?
			long a = treeSet.lessKeySize(sum - lower + 1);
			long b = treeSet.lessKeySize(sum - upper);
			ans += a - b;
			treeSet.add(sum);
		}
		return ans;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static void printArray(int[] arr) {
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static int[] generateArray(int len, int varible) {
		int[] arr = new int[len];
		for (int i = 0; i < arr.length; i++) {
			arr[i] = (int) (Math.random() * varible);
		}
		return arr;
	}

public static void main(String[] args) {
		int len = 200;
		int varible = 50;
		for (int i = 0; i < 10000; i++) {
			int[] test = generateArray(len, varible);
			int lower = (int) (Math.random() * varible) - (int) (Math.random() * varible);
			int upper = lower + (int) (Math.random() * varible);
			int ans1 = countRangeSum1(test, lower, upper);
			int ans2 = countRangeSum2(test, lower, upper);
			if (ans1 != ans2) {
				printArray(test);
				System.out.println(lower);
				System.out.println(upper);
				System.out.println(ans1);
				System.out.println(ans2);
			}
		}

	}
```

#### 题解

**为什么正确**

查询发生在插入当前sum之前，所以表内都是严格更早的前缀，天然满足非空子数组下标顺序。区间内每个历史前缀对应一个合法起点，即使值重复也必须按出现次数计入。

**复杂度**

归并版O(N log N)时间/O(N)空间；仅插入且维护平衡的有序表版O(N log N)时间、O(N)空间。

**边界与易错点**

size统计不同键数以平衡，all统计含重复的总次数以回答秩，两者不能混用。必须先查后插；忘记初始0会漏掉从下标0开始的子数组。

### 37.2 滑动窗口中位数

#### 题目

给定整数数组和窗口宽度 `k`，返回窗口每次移动后其中位数。

**输入、输出与约束**

输入int数组与1≤k≤N；输出每个窗口的double中位数。

**函数签名（课程入口）**

```java
public int size();
public boolean containsKey(K key);
public void add(K key);
public void remove(K key);
public K getIndexKey(int index);
public int compareTo(Node o);
```

**示例**

```text
输入：nums=[1,4,2,3], k=2
输出：[2.5,3.0,2.5]
```

解释：每个偶数窗口取两个值的平均。

**出处与版本差异**

- [课程源码：class37/Code02_SlidingWindowMedian.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class37/Code02_SlidingWindowMedian.java)。
- [LeetCode 原题 480. 滑动窗口中位数（Sliding Window Median）](https://leetcode.com/problems/sliding-window-median/)

#### 思路

把窗口元素编码为(value,index)复合键，先按值再按下标排序，以区分重复值。用带子树size的有序表支持按秩取数；奇数窗口取中间一个，偶数窗口取中间两个的平均。右移时加入新键、删除旧下标键。

#### 代码答案

```java
package class37;

public class Code02_SlidingWindowMedian {

	public static class SBTNode<K extends Comparable<K>> {
		public K key;
		public SBTNode<K> l;
		public SBTNode<K> r;
		public int size;

		public SBTNode(K k) {
			key = k;
			size = 1;
		}
	}

	public static class SizeBalancedTreeMap<K extends Comparable<K>> {
		private SBTNode<K> root;

		private SBTNode<K> rightRotate(SBTNode<K> cur) {
			SBTNode<K> leftNode = cur.l;
			// 右旋先把左孩子原右子树接到当前根左侧，保持中序顺序。
			cur.l = leftNode.r;
			// 左孩子升为新根，旧根降为它的右孩子。
			leftNode.r = cur;
			// 旋转不改变这整棵子树的节点集合，新根继承旧总大小。
			leftNode.size = cur.size;
			// 旧根的子树范围发生变化，要从新左右孩子重新计算大小。
			cur.size = (cur.l != null ? cur.l.size : 0) + (cur.r != null ? cur.r.size : 0) + 1;
			return leftNode;
		}

		private SBTNode<K> leftRotate(SBTNode<K> cur) {
			SBTNode<K> rightNode = cur.r;
			// 左旋将右孩子的左子树接到旧根右侧，不丢失中间键区间。
			cur.r = rightNode.l;
			// 原右孩子升根，旧根降为左孩子。
			rightNode.l = cur;
			rightNode.size = cur.size;
			// 旧根的子树范围发生变化，要从新左右孩子重新计算大小。
			cur.size = (cur.l != null ? cur.l.size : 0) + (cur.r != null ? cur.r.size : 0) + 1;
			return rightNode;
		}

		private SBTNode<K> maintain(SBTNode<K> cur) {
			if (cur == null) {
				return null;
			}
			int leftSize = cur.l != null ? cur.l.size : 0;
			int leftLeftSize = cur.l != null && cur.l.l != null ? cur.l.l.size : 0;
			int leftRightSize = cur.l != null && cur.l.r != null ? cur.l.r.size : 0;
			int rightSize = cur.r != null ? cur.r.size : 0;
			int rightLeftSize = cur.r != null && cur.r.l != null ? cur.r.l.size : 0;
			int rightRightSize = cur.r != null && cur.r.r != null ? cur.r.r.size : 0;
			// 左左孙树大于右子树，右旋修复这一大小失衡。
			if (leftLeftSize > rightSize) {
				cur = rightRotate(cur);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			// 左右孙树过大，先对左孩子左旋，再对当前根右旋。
			} else if (leftRightSize > rightSize) {
				cur.l = leftRotate(cur.l);
				cur = rightRotate(cur);
				cur.l = maintain(cur.l);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			// 右右孙树过大，左旋修复。
			} else if (rightRightSize > leftSize) {
				cur = leftRotate(cur);
				cur.l = maintain(cur.l);
				cur = maintain(cur);
			// 右左孙树过大，需要先右后左的双旋。
			} else if (rightLeftSize > leftSize) {
				cur.r = rightRotate(cur.r);
				cur = leftRotate(cur);
				cur.l = maintain(cur.l);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			}
			return cur;
		}

		private SBTNode<K> findLastIndex(K key) {
			SBTNode<K> pre = root;
			SBTNode<K> cur = root;
			while (cur != null) {
				pre = cur;
				if (key.compareTo(cur.key) == 0) {
					break;
				} else if (key.compareTo(cur.key) < 0) {
					cur = cur.l;
				} else {
					cur = cur.r;
				}
			}
			return pre;
		}

		private SBTNode<K> add(SBTNode<K> cur, K key) {
			if (cur == null) {
				return new SBTNode<K>(key);
			} else {
				// 插入路径上的每棵子树都会多一个节点，先递增计数。
				cur.size++;
				if (key.compareTo(cur.key) < 0) {
					cur.l = add(cur.l, key);
				} else {
					cur.r = add(cur.r, key);
				}
				// 插入后按子树大小恢复平衡，返回可能因旋转而改变的根。
				return maintain(cur);
			}
		}

		private SBTNode<K> delete(SBTNode<K> cur, K key) {
			// 删除路径上的每棵子树都会少一个节点，同步减少计数。
			cur.size--;
			if (key.compareTo(cur.key) > 0) {
				cur.r = delete(cur.r, key);
			} else if (key.compareTo(cur.key) < 0) {
				cur.l = delete(cur.l, key);
			} else {
				if (cur.l == null && cur.r == null) {
					// free cur memory -> C++
					cur = null;
				} else if (cur.l == null && cur.r != null) {
					// free cur memory -> C++
					cur = cur.r;
				} else if (cur.l != null && cur.r == null) {
					// free cur memory -> C++
					cur = cur.l;
				} else {
					SBTNode<K> pre = null;
					SBTNode<K> des = cur.r;
					des.size--;
					// 两孩子删除时，寻找右子树最左的中序后继。
					while (des.l != null) {
						pre = des;
						des = des.l;
						des.size--;
					}
					if (pre != null) {
						// 后继没有左孩子，用它的右子树填补原位置。
						pre.l = des.r;
						des.r = cur.r;
					}
					// 后继接管被删根的左子树，继续保持键的中序有序性。
					des.l = cur.l;
					des.size = des.l.size + (des.r == null ? 0 : des.r.size) + 1;
					// free cur memory -> C++
					cur = des;
				}
			}
			return cur;
		}

		private SBTNode<K> getIndex(SBTNode<K> cur, int kth) {
			// 当前根的子树内名次为左子树大小加1。
			if (kth == (cur.l != null ? cur.l.size : 0) + 1) {
				return cur;
			} else if (kth <= (cur.l != null ? cur.l.size : 0)) {
				return getIndex(cur.l, kth);
			} else {
				// 进入右子树时扣除左子树及当前根，把全局名次转为右树局部名次。
				return getIndex(cur.r, kth - (cur.l != null ? cur.l.size : 0) - 1);
			}
		}

		public int size() {
			return root == null ? 0 : root.size;
		}

		public boolean containsKey(K key) {
			if (key == null) {
				throw new RuntimeException("invalid parameter.");
			}
			SBTNode<K> lastNode = findLastIndex(key);
			return lastNode != null && key.compareTo(lastNode.key) == 0 ? true : false;
		}

		public void add(K key) {
			if (key == null) {
				throw new RuntimeException("invalid parameter.");
			}
			SBTNode<K> lastNode = findLastIndex(key);
			if (lastNode == null || key.compareTo(lastNode.key) != 0) {
				root = add(root, key);
			}
		}

		public void remove(K key) {
			if (key == null) {
				throw new RuntimeException("invalid parameter.");
			}
			if (containsKey(key)) {
				root = delete(root, key);
			}
		}

		public K getIndexKey(int index) {
			if (index < 0 || index >= this.size()) {
				throw new RuntimeException("invalid parameter.");
			}
			// 公开下标从0开始，内部第k小查询从1开始，入口统一转换。
			return getIndex(root, index + 1).key;
		}

	}

	public static class Node implements Comparable<Node> {
		public int index;
		public int value;

		public Node(int i, int v) {
			index = i;
			value = v;
		}

		@Override
		public int compareTo(Node o) {
			return value != o.value ? Integer.valueOf(value).compareTo(o.value)
					: Integer.valueOf(index).compareTo(o.index);
		}
	}

	public static double[] medianSlidingWindow(int[] nums, int k) {
		SizeBalancedTreeMap<Node> map = new SizeBalancedTreeMap<>();
		for (int i = 0; i < k - 1; i++) {
			// 把原下标纳入键，保证相等值也作为不同窗口元素保存。
			map.add(new Node(i, nums[i]));
		}
		double[] ans = new double[nums.length - k + 1];
		int index = 0;
		for (int i = k - 1; i < nums.length; i++) {
			// 把原下标纳入键，保证相等值也作为不同窗口元素保存。
			map.add(new Node(i, nums[i]));
			if (map.size() % 2 == 0) {
				Node upmid = map.getIndexKey(map.size() / 2 - 1);
				Node downmid = map.getIndexKey(map.size() / 2);
				ans[index++] = ((double) upmid.value + (double) downmid.value) / 2;
			} else {
				Node mid = map.getIndexKey(map.size() / 2);
				ans[index++] = (double) mid.value;
			}
			map.remove(new Node(i - k + 1, nums[i - k + 1]));
		}
		return ans;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：不能只用value作为键，否则重复值丢失且无法精确删除。偶数求平均先转long或double再相加，避免int溢出。

本条未附独立随机对数器。

#### 题解

**为什么正确**

复合键保留窗口每个位置的一份记录，值相同也不会覆盖。按秩查询得到排序后精确的中间位置，删除旧位置与加入新位置后集合恰等于下一窗口。

**复杂度**

若树持续保持平衡，O(N log K)时间、O(K)空间；本源码SBT删除省略maintain，严格最坏复杂度按实时树高H计O(NH)，不能自动宣称全部操作最坏对数。

**边界与易错点**

不能只用value作为键，否则重复值丢失且无法精确删除。偶数求平均先转long或double再相加，避免int溢出。

### 37.3 支持增删和按下标查询的动态数组

#### 题目

实现动态序列，支持按下标插入、删除和查询。课程用子树大小维护位置，并通过旋转加快查找；删除阶段的平衡维护限制见题解。

**输入、输出与约束**

动态序列支持0基下标插入、删除、查询；保持元素插入次序语义。

**函数签名（课程入口）**

```java
public void add(int index, V num);
public V get(int index);
public void remove(int index);
public int size();
```

**示例**

```text
输入：空序列add(0,10)，add(1,30)，add(1,20)，get(1)
输出：20
```

解释：插入后序列为[10,20,30]，值不参与排序。

**出处与版本差异**

- [课程源码：class37/Code03_AddRemoveGetIndexGreat.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class37/Code03_AddRemoveGetIndexGreat.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

把序列的相对位置编码到中序次序，不再用值排序。每节点记录子树size，插入下标index时与左子树大小比较，进入右侧时扣掉左子树和根占据的位置；查询、删除同理。旋转保留中序序列。

#### 代码答案

```java
package class37;

import java.util.ArrayList;

public class Code03_AddRemoveGetIndexGreat {

	public static class SBTNode<V> {
		public V value;
		public SBTNode<V> l;
		public SBTNode<V> r;
		public int size;

		public SBTNode(V v) {
			value = v;
			size = 1;
		}
	}

	public static class SbtList<V> {
		private SBTNode<V> root;

		private SBTNode<V> rightRotate(SBTNode<V> cur) {
			SBTNode<V> leftNode = cur.l;
			// 右旋先把左孩子原右子树接到当前根左侧，保持中序顺序。
			cur.l = leftNode.r;
			// 左孩子升为新根，旧根降为它的右孩子。
			leftNode.r = cur;
			// 旋转不改变这整棵子树的节点集合，新根继承旧总大小。
			leftNode.size = cur.size;
			// 旧根的子树范围发生变化，要从新左右孩子重新计算大小。
			cur.size = (cur.l != null ? cur.l.size : 0) + (cur.r != null ? cur.r.size : 0) + 1;
			return leftNode;
		}

		private SBTNode<V> leftRotate(SBTNode<V> cur) {
			SBTNode<V> rightNode = cur.r;
			// 左旋将右孩子的左子树接到旧根右侧，不丢失中间键区间。
			cur.r = rightNode.l;
			// 原右孩子升根，旧根降为左孩子。
			rightNode.l = cur;
			rightNode.size = cur.size;
			// 旧根的子树范围发生变化，要从新左右孩子重新计算大小。
			cur.size = (cur.l != null ? cur.l.size : 0) + (cur.r != null ? cur.r.size : 0) + 1;
			return rightNode;
		}

		private SBTNode<V> maintain(SBTNode<V> cur) {
			if (cur == null) {
				return null;
			}
			int leftSize = cur.l != null ? cur.l.size : 0;
			int leftLeftSize = cur.l != null && cur.l.l != null ? cur.l.l.size : 0;
			int leftRightSize = cur.l != null && cur.l.r != null ? cur.l.r.size : 0;
			int rightSize = cur.r != null ? cur.r.size : 0;
			int rightLeftSize = cur.r != null && cur.r.l != null ? cur.r.l.size : 0;
			int rightRightSize = cur.r != null && cur.r.r != null ? cur.r.r.size : 0;
			// 左左孙树大于右子树，右旋修复这一大小失衡。
			if (leftLeftSize > rightSize) {
				cur = rightRotate(cur);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			// 左右孙树过大，先对左孩子左旋，再对当前根右旋。
			} else if (leftRightSize > rightSize) {
				cur.l = leftRotate(cur.l);
				cur = rightRotate(cur);
				cur.l = maintain(cur.l);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			// 右右孙树过大，左旋修复。
			} else if (rightRightSize > leftSize) {
				cur = leftRotate(cur);
				cur.l = maintain(cur.l);
				cur = maintain(cur);
			// 右左孙树过大，需要先右后左的双旋。
			} else if (rightLeftSize > leftSize) {
				cur.r = rightRotate(cur.r);
				cur = leftRotate(cur);
				cur.l = maintain(cur.l);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			}
			return cur;
		}

		private SBTNode<V> add(SBTNode<V> root, int index, SBTNode<V> cur) {
			if (root == null) {
				return cur;
			}
			root.size++;
			int leftAndHeadSize = (root.l != null ? root.l.size : 0) + 1;
			if (index < leftAndHeadSize) {
				root.l = add(root.l, index, cur);
			} else {
				// 转入右子树时，目标下标要扣掉左子树和根所占的全部位置。
				root.r = add(root.r, index - leftAndHeadSize, cur);
			}
			root = maintain(root);
			return root;
		}

		private SBTNode<V> remove(SBTNode<V> root, int index) {
			root.size--;
			int rootIndex = root.l != null ? root.l.size : 0;
			if (index != rootIndex) {
				if (index < rootIndex) {
					root.l = remove(root.l, index);
				} else {
					root.r = remove(root.r, index - rootIndex - 1);
				}
				return root;
			}
			if (root.l == null && root.r == null) {
				return null;
			}
			if (root.l == null) {
				return root.r;
			}
			if (root.r == null) {
				return root.l;
			}
			SBTNode<V> pre = null;
			SBTNode<V> suc = root.r;
			suc.size--;
			while (suc.l != null) {
				pre = suc;
				suc = suc.l;
				suc.size--;
			}
			if (pre != null) {
				pre.l = suc.r;
				suc.r = root.r;
			}
			suc.l = root.l;
			suc.size = suc.l.size + (suc.r == null ? 0 : suc.r.size) + 1;
			return suc;
		}

		private SBTNode<V> get(SBTNode<V> root, int index) {
			int leftSize = root.l != null ? root.l.size : 0;
			if (index < leftSize) {
				return get(root.l, index);
			} else if (index == leftSize) {
				return root;
			} else {
				return get(root.r, index - leftSize - 1);
			}
		}

		public void add(int index, V num) {
			SBTNode<V> cur = new SBTNode<V>(num);
			if (root == null) {
				root = cur;
			} else {
				if (index <= root.size) {
					root = add(root, index, cur);
				}
			}
		}

		public V get(int index) {
			SBTNode<V> ans = get(root, index);
			return ans.value;
		}

		public void remove(int index) {
			if (index >= 0 && size() > index) {
				root = remove(root, index);
			}
		}

		public int size() {
			return root == null ? 0 : root.size;
		}

	}

	// 通过以下这个测试，
	// 可以很明显的看到LinkedList的插入、删除、get效率不如SbtList
	// LinkedList需要找到index所在的位置之后才能插入或者读取，时间复杂度O(N)
	// SbtList是平衡搜索二叉树，所以插入或者读取时间复杂度都是O(logN)

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		// 功能测试
		int test = 50000;
		int max = 1000000;
		boolean pass = true;
		ArrayList<Integer> list = new ArrayList<>();
		SbtList<Integer> sbtList = new SbtList<>();
		for (int i = 0; i < test; i++) {
			if (list.size() != sbtList.size()) {
				pass = false;
				break;
			}
			if (list.size() > 1 && Math.random() < 0.5) {
				int removeIndex = (int) (Math.random() * list.size());
				list.remove(removeIndex);
				sbtList.remove(removeIndex);
			} else {
				int randomIndex = (int) (Math.random() * (list.size() + 1));
				int randomValue = (int) (Math.random() * (max + 1));
				list.add(randomIndex, randomValue);
				sbtList.add(randomIndex, randomValue);
			}
		}
		for (int i = 0; i < list.size(); i++) {
			if (!list.get(i).equals(sbtList.get(i))) {
				pass = false;
				break;
			}
		}
		System.out.println("功能测试是否通过 : " + pass);

		// 性能测试
		test = 500000;
		list = new ArrayList<>();
		sbtList = new SbtList<>();
		long start = 0;
		long end = 0;

		start = System.currentTimeMillis();
		for (int i = 0; i < test; i++) {
			int randomIndex = (int) (Math.random() * (list.size() + 1));
			int randomValue = (int) (Math.random() * (max + 1));
			list.add(randomIndex, randomValue);
		}
		end = System.currentTimeMillis();
		System.out.println("ArrayList插入总时长(毫秒) ： " + (end - start));

		start = System.currentTimeMillis();
		for (int i = 0; i < test; i++) {
			int randomIndex = (int) (Math.random() * (i + 1));
			list.get(randomIndex);
		}
		end = System.currentTimeMillis();
		System.out.println("ArrayList读取总时长(毫秒) : " + (end - start));

		start = System.currentTimeMillis();
		for (int i = 0; i < test; i++) {
			int randomIndex = (int) (Math.random() * list.size());
			list.remove(randomIndex);
		}
		end = System.currentTimeMillis();
		System.out.println("ArrayList删除总时长(毫秒) : " + (end - start));

		start = System.currentTimeMillis();
		for (int i = 0; i < test; i++) {
			int randomIndex = (int) (Math.random() * (sbtList.size() + 1));
			int randomValue = (int) (Math.random() * (max + 1));
			sbtList.add(randomIndex, randomValue);
		}
		end = System.currentTimeMillis();
		System.out.println("SbtList插入总时长(毫秒) : " + (end - start));

		start = System.currentTimeMillis();
		for (int i = 0; i < test; i++) {
			int randomIndex = (int) (Math.random() * (i + 1));
			sbtList.get(randomIndex);
		}
		end = System.currentTimeMillis();
		System.out.println("SbtList读取总时长(毫秒) :  " + (end - start));

		start = System.currentTimeMillis();
		for (int i = 0; i < test; i++) {
			int randomIndex = (int) (Math.random() * sbtList.size());
			sbtList.remove(randomIndex);
		}
		end = System.currentTimeMillis();
		System.out.println("SbtList删除总时长(毫秒) :  " + (end - start));

	}
```

#### 题解

**为什么正确**

一个节点左边恰有size(left)个序列元素，所以中序秩等价于动态下标。旋转只重组树形而不改中序次序，故可在平衡树形的同时保持序列语义。

**复杂度**

平衡实现目标单次O(log N)、空间O(N)；课程删除部分未作完整平衡维护，严格最坏仍需按树高H分析为O(H)。

**边界与易错点**

插入允许index==size在末尾，查询删除只能index<size。插入相同数值完全合法，因为键是位置次序而非元素值。

### 37.4 根据身高重建队列

#### 题目

每个人由身高 `h` 和前方身高不低于 `h` 的人数 `k` 表示，重建满足全部约束的队列。

**输入、输出与约束**

每人(h,k)表示前面恰有k人身高≥h，保证存在合法队列；返回重建队列。

**函数签名（课程入口）**

```java
public static int[][] reconstructQueue1(int[][] people);
public static int[][] reconstructQueue2(int[][] people);
public int compare(Unit o1, Unit o2);
public void insert(int index, int value);
public int get(int index);
public LinkedList<Integer> allIndexes();
```

**示例**

```text
输入：people=[[7,0],[5,0],[7,1]]
输出：[[5,0],[7,0],[7,1]]
```

解释：先放两位7，再将5插到0，不改变7前面不矮者数量。

**出处与版本差异**

- [课程源码：class37/Code04_QueueReconstructionByHeight.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class37/Code04_QueueReconstructionByHeight.java)。
- [LeetCode 原题 406. 根据身高重建队列（Queue Reconstruction by Height）](https://leetcode.com/problems/queue-reconstruction-by-height/)

#### 思路

按身高降序，身高相同时按k升序处理。当前已经放入的所有人都不比新人矮，把新人插入当前序列下标k，便恰好有k个不矮者在前面。列表插入版与按下标平衡树版只在序列实现上不同。

#### 代码答案

```java
package class37;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Comparator;
import java.util.LinkedList;

// 本题测试链接：https://leetcode.com/problems/queue-reconstruction-by-height/
public class Code04_QueueReconstructionByHeight {

	public static int[][] reconstructQueue1(int[][] people) {
		int N = people.length;
		Unit[] units = new Unit[N];
		for (int i = 0; i < N; i++) {
			units[i] = new Unit(people[i][0], people[i][1]);
		}
		Arrays.sort(units, new UnitComparator());
		ArrayList<Unit> arrList = new ArrayList<>();
		for (Unit unit : units) {
			// 已放入者都不比当前矮，插到k位置恰使前方有k个合格者。
			arrList.add(unit.k, unit);
		}
		int[][] ans = new int[N][2];
		int index = 0;
		for (Unit unit : arrList) {
			ans[index][0] = unit.h;
			ans[index++][1] = unit.k;
		}
		return ans;
	}

	public static int[][] reconstructQueue2(int[][] people) {
		int N = people.length;
		Unit[] units = new Unit[N];
		for (int i = 0; i < N; i++) {
			units[i] = new Unit(people[i][0], people[i][1]);
		}
		Arrays.sort(units, new UnitComparator());
		SBTree tree = new SBTree();
		for (int i = 0; i < N; i++) {
			// 有序树按名次插入等价于列表插入，但避免线性搬移元素。
			tree.insert(units[i].k, i);
		}
		LinkedList<Integer> allIndexes = tree.allIndexes();
		int[][] ans = new int[N][2];
		int index = 0;
		for (Integer arri : allIndexes) {
			ans[index][0] = units[arri].h;
			ans[index++][1] = units[arri].k;
		}
		return ans;
	}

	public static class Unit {
		public int h;
		public int k;

		public Unit(int height, int greater) {
			h = height;
			k = greater;
		}
	}

	public static class UnitComparator implements Comparator<Unit> {

		@Override
		public int compare(Unit o1, Unit o2) {
			// 身高降序，同高k升序，确保插入当前人时约束可由已有队列直接表达。
			return o1.h != o2.h ? (o2.h - o1.h) : (o1.k - o2.k);
		}

	}

	public static class SBTNode {
		public int value;
		public SBTNode l;
		public SBTNode r;
		public int size;

		public SBTNode(int arrIndex) {
			value = arrIndex;
			size = 1;
		}
	}

	public static class SBTree {
		private SBTNode root;

		private SBTNode rightRotate(SBTNode cur) {
			SBTNode leftNode = cur.l;
			// 右旋先把左孩子原右子树接到当前根左侧，保持中序顺序。
			cur.l = leftNode.r;
			// 左孩子升为新根，旧根降为它的右孩子。
			leftNode.r = cur;
			// 旋转不改变这整棵子树的节点集合，新根继承旧总大小。
			leftNode.size = cur.size;
			// 旧根的子树范围发生变化，要从新左右孩子重新计算大小。
			cur.size = (cur.l != null ? cur.l.size : 0) + (cur.r != null ? cur.r.size : 0) + 1;
			return leftNode;
		}

		private SBTNode leftRotate(SBTNode cur) {
			SBTNode rightNode = cur.r;
			// 左旋将右孩子的左子树接到旧根右侧，不丢失中间键区间。
			cur.r = rightNode.l;
			// 原右孩子升根，旧根降为左孩子。
			rightNode.l = cur;
			rightNode.size = cur.size;
			// 旧根的子树范围发生变化，要从新左右孩子重新计算大小。
			cur.size = (cur.l != null ? cur.l.size : 0) + (cur.r != null ? cur.r.size : 0) + 1;
			return rightNode;
		}

		private SBTNode maintain(SBTNode cur) {
			if (cur == null) {
				return null;
			}
			int leftSize = cur.l != null ? cur.l.size : 0;
			int leftLeftSize = cur.l != null && cur.l.l != null ? cur.l.l.size : 0;
			int leftRightSize = cur.l != null && cur.l.r != null ? cur.l.r.size : 0;
			int rightSize = cur.r != null ? cur.r.size : 0;
			int rightLeftSize = cur.r != null && cur.r.l != null ? cur.r.l.size : 0;
			int rightRightSize = cur.r != null && cur.r.r != null ? cur.r.r.size : 0;
			// 左左孙树大于右子树，右旋修复这一大小失衡。
			if (leftLeftSize > rightSize) {
				cur = rightRotate(cur);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			// 左右孙树过大，先对左孩子左旋，再对当前根右旋。
			} else if (leftRightSize > rightSize) {
				cur.l = leftRotate(cur.l);
				cur = rightRotate(cur);
				cur.l = maintain(cur.l);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			// 右右孙树过大，左旋修复。
			} else if (rightRightSize > leftSize) {
				cur = leftRotate(cur);
				cur.l = maintain(cur.l);
				cur = maintain(cur);
			// 右左孙树过大，需要先右后左的双旋。
			} else if (rightLeftSize > leftSize) {
				cur.r = rightRotate(cur.r);
				cur = leftRotate(cur);
				cur.l = maintain(cur.l);
				cur.r = maintain(cur.r);
				cur = maintain(cur);
			}
			return cur;
		}

		private SBTNode insert(SBTNode root, int index, SBTNode cur) {
			if (root == null) {
				return cur;
			}
			root.size++;
			int leftAndHeadSize = (root.l != null ? root.l.size : 0) + 1;
			if (index < leftAndHeadSize) {
				root.l = insert(root.l, index, cur);
			} else {
				root.r = insert(root.r, index - leftAndHeadSize, cur);
			}
			root = maintain(root);
			return root;
		}

		private SBTNode get(SBTNode root, int index) {
			int leftSize = root.l != null ? root.l.size : 0;
			if (index < leftSize) {
				return get(root.l, index);
			} else if (index == leftSize) {
				return root;
			} else {
				return get(root.r, index - leftSize - 1);
			}
		}

		private void process(SBTNode head, LinkedList<Integer> indexes) {
			if (head == null) {
				return;
			}
			process(head.l, indexes);
			indexes.addLast(head.value);
			process(head.r, indexes);
		}

		public void insert(int index, int value) {
			SBTNode cur = new SBTNode(value);
			if (root == null) {
				root = cur;
			} else {
				if (index <= root.size) {
					root = insert(root, index, cur);
				}
			}
		}

		public int get(int index) {
			SBTNode ans = get(root, index);
			return ans.value;
		}

		public LinkedList<Integer> allIndexes() {
			LinkedList<Integer> indexes = new LinkedList<>();
			process(root, indexes);
			return indexes;
		}

	}

	// 通过以下这个测试，
	// 可以很明显的看到LinkedList的插入和get效率不如SBTree
	// LinkedList需要找到index所在的位置之后才能插入或者读取，时间复杂度O(N)
	// SBTree是平衡搜索二叉树，所以插入或者读取时间复杂度都是O(logN)

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		// 功能测试
		int test = 10000;
		int max = 1000000;
		boolean pass = true;
		LinkedList<Integer> list = new LinkedList<>();
		SBTree sbtree = new SBTree();
		for (int i = 0; i < test; i++) {
			int randomIndex = (int) (Math.random() * (i + 1));
			int randomValue = (int) (Math.random() * (max + 1));
			list.add(randomIndex, randomValue);
			sbtree.insert(randomIndex, randomValue);
		}
		for (int i = 0; i < test; i++) {
			if (list.get(i) != sbtree.get(i)) {
				pass = false;
				break;
			}
		}
		System.out.println("功能测试是否通过 : " + pass);

		// 性能测试
		test = 50000;
		list = new LinkedList<>();
		sbtree = new SBTree();
		long start = 0;
		long end = 0;

		start = System.currentTimeMillis();
		for (int i = 0; i < test; i++) {
			int randomIndex = (int) (Math.random() * (i + 1));
			int randomValue = (int) (Math.random() * (max + 1));
			list.add(randomIndex, randomValue);
		}
		end = System.currentTimeMillis();
		System.out.println("LinkedList插入总时长(毫秒) ： " + (end - start));

		start = System.currentTimeMillis();
		for (int i = 0; i < test; i++) {
			int randomIndex = (int) (Math.random() * (i + 1));
			list.get(randomIndex);
		}
		end = System.currentTimeMillis();
		System.out.println("LinkedList读取总时长(毫秒) : " + (end - start));

		start = System.currentTimeMillis();
		for (int i = 0; i < test; i++) {
			int randomIndex = (int) (Math.random() * (i + 1));
			int randomValue = (int) (Math.random() * (max + 1));
			sbtree.insert(randomIndex, randomValue);
		}
		end = System.currentTimeMillis();
		System.out.println("SBTree插入总时长(毫秒) : " + (end - start));

		start = System.currentTimeMillis();
		for (int i = 0; i < test; i++) {
			int randomIndex = (int) (Math.random() * (i + 1));
			sbtree.get(randomIndex);
		}
		end = System.currentTimeMillis();
		System.out.println("SBTree读取总时长(毫秒) :  " + (end - start));

	}
```

#### 题解

**为什么正确**

高个先放时，新人的k约束只由已经放入的人决定。后续更矮的人即使插在前面，也不会计入新人的不矮人数；同高按k升序使先前同高者的位置约束也保持成立。

**复杂度**

排序O(N log N)；数组列表插入最坏O(N²)，按秩平衡树插入O(N log N)，空间O(N)。

**边界与易错点**

同高时k必须升序；降序会出现插入位置越界或破坏已放者约束。输入必须可重建，不能把不合法k当成算法错误。

### 37.5 多种有序表的性能比较

#### 题目

在相同随机操作序列下比较多种有序表实现的运行时间，并校验它们的查询结果是否一致。

**输入、输出与约束**

配套验证程序，比较有序映射同一操作序列的语义与运行时间，不是独立算法提交题。

**函数签名（课程入口）**

```java
public static void performanceTest();
```

**示例**

```text
输入：put(2,"a")，put(1,"b")，remove(2)，floorKey(2)
输出：所有实现应返回1
```

解释：按同一操作前缀比较具体返回值，而非只观察耗时。

**出处与版本差异**

- [课程源码：class37/Compare.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class37/Compare.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

在相同键集合和同一操作序列上比较不同有序表的put、remove、get及边界查询结果，再分别计时。把正确性比较和性能采样分开，避免把输出或数据生成成本混入容器操作。

#### 代码答案

```java
package class37;

import java.util.TreeMap;

import class35.Code01_AVLTreeMap.AVLTreeMap;
import class36.Code01_SizeBalancedTreeMap.SizeBalancedTreeMap;
import class36.Code02_SkipListMap.SkipListMap;

// 本文件为avl、sbt、skiplist三种结构的测试文件
public class Compare {

	// 功能对拍：相同操作依次作用于标准TreeMap及三种课程有序表。
	public static void functionTest() {
		System.out.println("功能测试开始");
		TreeMap<Integer, Integer> treeMap = new TreeMap<>();
		AVLTreeMap<Integer, Integer> avl = new AVLTreeMap<>();
		SizeBalancedTreeMap<Integer, Integer> sbt = new SizeBalancedTreeMap<>();
		SkipListMap<Integer, Integer> skip = new SkipListMap<>();
		int maxK = 500;
		int maxV = 50000;
		int testTime = 1000000;
		for (int i = 0; i < testTime; i++) {
			int addK = (int) (Math.random() * maxK);
			int addV = (int) (Math.random() * maxV);
			// 先更新独立的标准容器，后面三个实现必须执行相同键值插入。
			treeMap.put(addK, addV);
			avl.put(addK, addV);
			sbt.put(addK, addV);
			skip.put(addK, addV);

			int removeK = (int) (Math.random() * maxK);
			// 随机删除可能命中或未命中，四种容器都应保持一致语义。
			treeMap.remove(removeK);
			avl.remove(removeK);
			sbt.remove(removeK);
			skip.remove(removeK);

			int querryK = (int) (Math.random() * maxK);
			if (treeMap.containsKey(querryK) != avl.containsKey(querryK)
					|| sbt.containsKey(querryK) != skip.containsKey(querryK)
					|| treeMap.containsKey(querryK) != sbt.containsKey(querryK)) {
				System.out.println("containsKey Oops");
				System.out.println(treeMap.containsKey(querryK));
				System.out.println(avl.containsKey(querryK));
				System.out.println(sbt.containsKey(querryK));
				System.out.println(skip.containsKey(querryK));
				break;
			}

			// 仅当键存在时继续比较读取结果，避免对null拆箱。
			if (treeMap.containsKey(querryK)) {
				int v1 = treeMap.get(querryK);
				int v2 = avl.get(querryK);
				int v3 = sbt.get(querryK);
				int v4 = skip.get(querryK);
				if (v1 != v2 || v3 != v4 || v1 != v3) {
					System.out.println("get Oops");
					System.out.println(treeMap.get(querryK));
					System.out.println(avl.get(querryK));
					System.out.println(sbt.get(querryK));
					System.out.println(skip.get(querryK));
					break;
				}
				// floorKey的基准是小于等于查询键的最大键，可能不存在而返回null。
				Integer f1 = treeMap.floorKey(querryK);
				Integer f2 = avl.floorKey(querryK);
				Integer f3 = sbt.floorKey(querryK);
				Integer f4 = skip.floorKey(querryK);
				if (f1 == null && (f2 != null || f3 != null || f4 != null)) {
					System.out.println("floorKey Oops");
					System.out.println(treeMap.floorKey(querryK));
					System.out.println(avl.floorKey(querryK));
					System.out.println(sbt.floorKey(querryK));
					System.out.println(skip.floorKey(querryK));
					break;
				}
				if (f1 != null && (f2 == null || f3 == null || f4 == null)) {
					System.out.println("floorKey Oops");
					System.out.println(treeMap.floorKey(querryK));
					System.out.println(avl.floorKey(querryK));
					System.out.println(sbt.floorKey(querryK));
					System.out.println(skip.floorKey(querryK));
					break;
				}
				if (f1 != null) {
					int ans1 = f1;
					int ans2 = f2;
					int ans3 = f3;
					int ans4 = f4;
					if (ans1 != ans2 || ans3 != ans4 || ans1 != ans3) {
						System.out.println("floorKey Oops");
						System.out.println(treeMap.floorKey(querryK));
						System.out.println(avl.floorKey(querryK));
						System.out.println(sbt.floorKey(querryK));
						System.out.println(skip.floorKey(querryK));
						break;
					}
				}
				f1 = treeMap.ceilingKey(querryK);
				f2 = avl.ceilingKey(querryK);
				f3 = sbt.ceilingKey(querryK);
				f4 = skip.ceilingKey(querryK);
				if (f1 == null && (f2 != null || f3 != null || f4 != null)) {
					System.out.println("ceilingKey Oops");
					System.out.println(treeMap.ceilingKey(querryK));
					System.out.println(avl.ceilingKey(querryK));
					System.out.println(sbt.ceilingKey(querryK));
					System.out.println(skip.ceilingKey(querryK));
					break;
				}
				if (f1 != null && (f2 == null || f3 == null || f4 == null)) {
					System.out.println("ceilingKey Oops");
					System.out.println(treeMap.ceilingKey(querryK));
					System.out.println(avl.ceilingKey(querryK));
					System.out.println(sbt.ceilingKey(querryK));
					System.out.println(skip.ceilingKey(querryK));
					break;
				}
				if (f1 != null) {
					int ans1 = f1;
					int ans2 = f2;
					int ans3 = f3;
					int ans4 = f4;
					if (ans1 != ans2 || ans3 != ans4 || ans1 != ans3) {
						System.out.println("ceilingKey Oops");
						System.out.println(treeMap.ceilingKey(querryK));
						System.out.println(avl.ceilingKey(querryK));
						System.out.println(sbt.ceilingKey(querryK));
						System.out.println(skip.ceilingKey(querryK));
						break;
					}
				}

			}

			Integer f1 = treeMap.firstKey();
			Integer f2 = avl.firstKey();
			Integer f3 = sbt.firstKey();
			Integer f4 = skip.firstKey();
			if (f1 == null && (f2 != null || f3 != null || f4 != null)) {
				System.out.println("firstKey Oops");
				System.out.println(treeMap.firstKey());
				System.out.println(avl.firstKey());
				System.out.println(sbt.firstKey());
				System.out.println(skip.firstKey());
				break;
			}
			if (f1 != null && (f2 == null || f3 == null || f4 == null)) {
				System.out.println("firstKey Oops");
				System.out.println(treeMap.firstKey());
				System.out.println(avl.firstKey());
				System.out.println(sbt.firstKey());
				System.out.println(skip.firstKey());
				break;
			}
			if (f1 != null) {
				int ans1 = f1;
				int ans2 = f2;
				int ans3 = f3;
				int ans4 = f4;
				if (ans1 != ans2 || ans3 != ans4 || ans1 != ans3) {
					System.out.println("firstKey Oops");
					System.out.println(treeMap.firstKey());
					System.out.println(avl.firstKey());
					System.out.println(sbt.firstKey());
					System.out.println(skip.firstKey());
					break;
				}
			}

			f1 = treeMap.lastKey();
			f2 = avl.lastKey();
			f3 = sbt.lastKey();
			f4 = skip.lastKey();
			if (f1 == null && (f2 != null || f3 != null || f4 != null)) {
				System.out.println("lastKey Oops");
				System.out.println(treeMap.lastKey());
				System.out.println(avl.lastKey());
				System.out.println(sbt.lastKey());
				System.out.println(skip.lastKey());
				break;
			}
			if (f1 != null && (f2 == null || f3 == null || f4 == null)) {
				System.out.println("firstKey Oops");
				System.out.println(treeMap.lastKey());
				System.out.println(avl.lastKey());
				System.out.println(sbt.lastKey());
				System.out.println(skip.lastKey());
				break;
			}
			if (f1 != null) {
				int ans1 = f1;
				int ans2 = f2;
				int ans3 = f3;
				int ans4 = f4;
				if (ans1 != ans2 || ans3 != ans4 || ans1 != ans3) {
					System.out.println("lastKey Oops");
					System.out.println(treeMap.lastKey());
					System.out.println(avl.lastKey());
					System.out.println(sbt.lastKey());
					System.out.println(skip.lastKey());
					break;
				}
			}
			if (treeMap.size() != avl.size() || sbt.size() != skip.size() || treeMap.size() != sbt.size()) {
				System.out.println("size Oops");
				System.out.println(treeMap.size());
				System.out.println(avl.size());
				System.out.println(sbt.size());
				System.out.println(skip.size());
				break;
			}
		}
		System.out.println("功能测试结束");
	}

	// 性能测量与正确性对拍分开；一次墙钟耗时不代表稳定复杂度或通用排名。
	public static void performanceTest() {
		System.out.println("性能测试开始");
		TreeMap<Integer, Integer> treeMap;
		AVLTreeMap<Integer, Integer> avl;
		SizeBalancedTreeMap<Integer, Integer> sbt;
		SkipListMap<Integer, Integer> skip;
		long start;
		long end;
		int max = 1000000;
		treeMap = new TreeMap<>();
		avl = new AVLTreeMap<>();
		sbt = new SizeBalancedTreeMap<>();
		skip = new SkipListMap<>();
		System.out.println("顺序递增加入测试，数据规模 : " + max);
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = 0; i < max; i++) {
			treeMap.put(i, i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("treeMap 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = 0; i < max; i++) {
			avl.put(i, i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("avl 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = 0; i < max; i++) {
			sbt.put(i, i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("sbt 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = 0; i < max; i++) {
			skip.put(i, i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("skip 运行时间 : " + (end - start) + "ms");

		System.out.println("顺序递增删除测试，数据规模 : " + max);
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = 0; i < max; i++) {
			treeMap.remove(i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("treeMap 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = 0; i < max; i++) {
			avl.remove(i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("avl 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = 0; i < max; i++) {
			sbt.remove(i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("sbt 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = 0; i < max; i++) {
			skip.remove(i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("skip 运行时间 : " + (end - start) + "ms");

		System.out.println("顺序递减加入测试，数据规模 : " + max);
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			treeMap.put(i, i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("treeMap 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			avl.put(i, i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("avl 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			sbt.put(i, i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("sbt 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			skip.put(i, i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("skip 运行时间 : " + (end - start) + "ms");

		System.out.println("顺序递减删除测试，数据规模 : " + max);
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			treeMap.remove(i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("treeMap 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			avl.remove(i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("avl 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			sbt.remove(i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("sbt 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			skip.remove(i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("skip 运行时间 : " + (end - start) + "ms");

		System.out.println("随机加入测试，数据规模 : " + max);
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = 0; i < max; i++) {
			treeMap.put((int) (Math.random() * i), i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("treeMap 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			avl.put((int) (Math.random() * i), i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("avl 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			sbt.put((int) (Math.random() * i), i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("sbt 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			skip.put((int) (Math.random() * i), i);
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("skip 运行时间 : " + (end - start) + "ms");

		System.out.println("随机删除测试，数据规模 : " + max);
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = 0; i < max; i++) {
			treeMap.remove((int) (Math.random() * i));
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("treeMap 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			avl.remove((int) (Math.random() * i));
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("avl 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			sbt.remove((int) (Math.random() * i));
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("sbt 运行时间 : " + (end - start) + "ms");

		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		start = System.currentTimeMillis();
		for (int i = max; i >= 0; i--) {
			skip.remove((int) (Math.random() * i));
		}
		// 记录当前阶段时间；JVM预热、随机数据和运行环境都会影响结果。
		end = System.currentTimeMillis();
		System.out.println("skip 运行时间 : " + (end - start) + "ms");

		System.out.println("性能测试结束");
	}

	public static void main(String[] args) {
		functionTest();
		System.out.println("======");
		performanceTest();
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：一次运行的墙钟时间受JIT（Just-In-Time，即时编译）、垃圾回收和预热影响。源码对拍和计时并不证明任意输入下的最坏复杂度。

本条未附独立随机对数器。

#### 题解

**为什么正确**

对同一序列逐步执行，任何实现若在某一步与标准TreeMap语义不一致，就能给出该操作前缀作为反例。仅比较最终大小不足以发现中途丢键或边界查询错误。

**复杂度**

设Q次操作，有序表理论O(Q log N)；测试程序本身还包括输入生成、遍历与输出，不能把总耗时直接当作某个单操作复杂度。

**边界与易错点**

一次运行的墙钟时间受JIT（Just-In-Time，即时编译）、垃圾回收和预热影响。源码对拍和计时并不证明任意输入下的最坏复杂度。


<a id="course-38"></a>

## 第 38 课：根据数据量猜解法与打表技巧

### 38.1 装苹果所需的最少袋子

#### 题目

只允许使用容量为 6 和 8 的袋子装完 `n` 个苹果，返回所需最少袋数；无法装完时返回 `-1`。

**输入、输出与约束**

输入非负整数苹果数，只用6或8容量且每袋装满；返回最少袋数，无解-1。

**函数签名（课程入口）**

```java
public static int minBags(int apple);
public static int minBagAwesome(int apple);
```

**示例**

```text
输入：N=24
输出：3
```

解释：三袋8恰装满，少于三袋最多装16。

**出处与版本差异**

- [课程源码：class38/Code01_AppleMinBags.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class38/Code01_AppleMinBags.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先尽量使用8个装的大袋，剩余若能被6整除就得到候选；否则逐次减少8袋数量。观察小输入后可证明：偶数N≥18时，最少袋数为(N-18)/8+3，较小值单独列表，奇数无解。

#### 代码答案

```java
package class38;

public class Code01_AppleMinBags {

	public static int minBags(int apple) {
		if (apple < 0) {
			return -1;
		}
		// 先尝试最多8个装的大袋，再逐个减少，寻找袋数最少的可行组合。
		int bag8 = (apple >> 3);
		int rest = apple - (bag8 << 3);
		while(bag8 >= 0) {
			// rest 个
			// 剩余可由6袋精确填满，无需留下未装满的袋子。
			// 余数能被6整除，当前大袋数可以组成合法方案。
			if(rest % 6 ==0) {
				return bag8 + (rest / 6);
			} else {
				bag8--;
				rest += 8;
			}
		}
		return -1;
	}

	public static int minBagAwesome(int apple) {
		if ((apple & 1) != 0) { // 如果是奇数，返回-1
			return -1;
		}
		if (apple < 18) {
			return apple == 0 ? 0 : (apple == 6 || apple == 8) ? 1
					: (apple == 12 || apple == 14 || apple == 16) ? 2 : -1;
		}
		// 已排除奇数及小规模缺口，剩余偶数可按每8个苹果多一袋的规律计算。
		return (apple - 18) / 8 + 3;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		for(int apple = 1; apple < 200;apple++) {
			System.out.println(apple + " : "+ minBags(apple));
		}

	}
```

#### 题解

**为什么正确**

固定总袋数b，容量为6b+2x，0≤x≤b，因此只可能覆盖[6b,8b]内的偶数。对足够大偶数，这些容量区间连续衔接；从最少可能袋数开始即可得到公式，18以下存在小规模缺口需单独处理。

**复杂度**

枚举袋数O(N)，公式O(1)，额外空间O(1)。

**边界与易错点**

n=0需要0袋。不能把偶数都视为可装，如2、4、10无解。整数除法公式仅在已处理奇偶与小值后使用。

### 38.2 先后手吃草博弈

#### 题目

两只动物轮流吃草，每次只能吃 1、4、16 等四次幂份，不能行动者输，判断先手还是后手获胜。

**输入、输出与约束**

输入非负草量n，双方每次吃不超过剩余的4的幂份，无合法动作者输。

**函数签名（课程入口）**

```java
public static String whoWin(int n);
public static String winner1(int n);
public static String winner2(int n);
```

**示例**

```text
输入：n=7
输出：后手胜
```

解释：7模5为2，先手吃1或4都把必胜状态留给对手。

**出处与版本差异**

- [课程源码：class38/Code02_EatGrass.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class38/Code02_EatGrass.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

定义当前剩余n份草的先手是否必胜。递归枚举吃1、4、16…份；只要能把对手送到必败状态就必胜。小表显示n模5为0或2时必败，可用模5余数直接判定。

#### 代码答案

```java
package class38;

public class Code02_EatGrass {

	// 如果n份草，最终先手赢，返回"先手"
	// 如果n份草，最终后手赢，返回"后手"
	public static String whoWin(int n) {
		if (n < 5) {
			return n == 0 || n == 2 ? "后手" : "先手";
		}
		// 进到这个过程里来，当前的先手，先选
		int want = 1;
		while (want <= n) {
			if (whoWin(n - want).equals("后手")) {
				return "先手";
			}
			if (want <= (n / 4)) {
				want *= 4;
			} else {
				break;
			}
		}
		return "后手";
	}

	public static String winner1(int n) {
		if (n < 5) {
			return (n == 0 || n == 2) ? "后手" : "先手";
		}
		int base = 1;
		while (base <= n) {
			if (winner1(n - base).equals("后手")) {
				return "先手";
			}
			if (base > n / 4) { // 防止base*4之后溢出
				break;
			}
			base *= 4;
		}
		return "后手";
	}

	public static String winner2(int n) {
		// 这两个余数组成已证明的必败状态集合。
		if (n % 5 == 0 || n % 5 == 2) {
			return "后手";
		} else {
			return "先手";
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		for (int i = 0; i <= 50; i++) {
			System.out.println(i + " : " + whoWin(i));
		}
	}
```

#### 题解

**为什么正确**

4的幂模5交替为1、4。余数0或2减去合法步长后只能到其余余数，无法转入同类必败集合；余数1、3、4在足够大时分别可减1或4到0或2，小值逐一验证，归纳完成。

**复杂度**

递归重复搜索指数增长；已证明规律的判定O(1)，空间O(1)。

**边界与易错点**

吃的数量是4的幂，不是任意4的倍数。循环扩大步长前检查溢出，0份时轮到的人无步可走而失败。

### 38.3 判断整数能否表示为连续正整数之和

#### 题目

给定正整数 `n`，判断它能否表示为至少两个连续正整数之和。

**输入、输出与约束**

输入正整数N；返回是否存在至少两个连续正整数之和等于N。

**函数签名（课程入口）**

```java
public static boolean isMSum1(int num);
public static boolean isMSum2(int num);
```

**示例**

```text
输入：N=15
输出：true
```

解释：7+8=15；N=16则无满足条件的表示。

**出处与版本差异**

- [课程源码：class38/Code03_MSumToN.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class38/Code03_MSumToN.java)。
- [LeetCode 对应题 829. 连续整数求和（Consecutive Numbers Sum）](https://leetcode.com/problems/consecutive-numbers-sum/)

课程版本说明：

- LeetCode 829：相关变式：原题统计全部表示数量并允许单项 N，课程只判断是否存在至少两项表示。

#### 思路

枚举版从每个正整数起点连续累加，检查能否以至少两项组成N。数学版利用结论：恰好2的幂不能由至少两个连续正整数表示，其他正整数都可以，用N&(N-1)检查是否只有一个置位。

#### 代码答案

```java
package class38;

public class Code03_MSumToN {

	public static boolean isMSum1(int num) {
		for (int start = 1; start <= num; start++) {
			int sum = start;
			for (int j = start + 1; j <= num; j++) {
				if (sum + j > num) {
					break;
				}
				if (sum + j == num) {
					return true;
				}
				sum += j;
			}
		}
		return false;
	}

	public static boolean isMSum2(int num) {
//
//		return num == (num & (~num + 1));
//
//		return num == (num & (-num));
//
//
		// 清掉最低置位后仍非零，说明不是2的幂，于是存在多项连续正整数表示。
		return (num & (num - 1)) != 0;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		for (int num = 1; num < 200; num++) {
			System.out.println(num + " : " + isMSum1(num));
		}
		System.out.println("test begin");
		for (int num = 1; num < 5000; num++) {
			if (isMSum1(num) != isMSum2(num)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("test end");

	}
```

#### 题解

**为什么正确**

长度k、首项a的连续和满足2N=k(2a+k-1)。若N为2的幂，两因子中所需的奇数因子只能为1，无法得到合法a≥1、k≥2。若N有大于1的奇因子，可构造奇数长度序列；当首项非正时改取互补偶数长度即可得到正首项。

**复杂度**

暴力枚举可达O(N²)，位判定O(1)时间和空间。

**边界与易错点**

题目只判断存在且至少两项；LeetCode829返回全部表示方法数并包含单项N，不能标为完整同题。N必须为正。

### 38.4 贿赂怪兽的最少花费

#### 题目

依次经过若干怪兽；能力不足时必须花钱贿赂并获得其能力，能力足够时可选择通过或贿赂，返回通过全部怪兽的最少花费。

**输入、输出与约束**

输入正能力需求d与贿赂成本p，初始能力0；贿赂才增加对应能力，返回最少总成本。

**函数签名（课程入口）**

```java
public static int minMoney2(int[] d, int[] p);
```

**示例**

```text
输入：d=[3,2,4], p=[5,1,10]
输出：6
```

解释：先花5得能力3，再花1得能力5，即可免费经过最后怪兽。

**出处与版本差异**

- [课程源码：class38/Code04_MoneyProblem.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class38/Code04_MoneyProblem.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

按顺序经过怪兽，贿赂第i个花p[i]并增加能力d[i]。能力不足必须贿赂，足够则可通过或仍贿赂。能力总和小时，状态按(index,ability)求最少钱；金钱总和小时，按(index,money)求最多能力，以-1表示该花费不可达。

#### 代码答案

```java
package class38;

public class Code04_MoneyProblem {

	// int[] d d[i]：i号怪兽的武力
	// int[] p p[i]：i号怪兽要求的钱
	// ability 当前你所具有的能力
	// index 来到了第index个怪兽的面前

	// 目前，你的能力是ability，你来到了index号怪兽的面前，如果要通过后续所有的怪兽，
	// 请返回需要花的最少钱数
	public static long process1(int[] d, int[] p, int ability, int index) {
		if (index == d.length) {
			return 0;
		}
		// 当前战力不足，唯一合法动作是贿赂该怪兽。
		if (ability < d[index]) {
			return p[index] + process1(d, p, ability + d[index], index + 1);
		} else { // ability >= d[index] 可以贿赂，也可以不贿赂
			return Math.min(

					p[index] + process1(d, p, ability + d[index], index + 1),

					0 + process1(d, p, ability, index + 1));
		}
	}

	public static long func1(int[] d, int[] p) {
		return process1(d, p, 0, 0);
	}

	// 从0....index号怪兽，花的钱，必须严格==money
	// 如果通过不了，返回-1
	// 如果可以通过，返回能通过情况下的最大能力值
	public static long process2(int[] d, int[] p, int index, int money) {
		if (index == -1) { // 一个怪兽也没遇到呢
			return money == 0 ? 0 : -1;
		}
		// index >= 0
		// 1) 不贿赂当前index号怪兽
		long preMaxAbility = process2(d, p, index - 1, money);
		long p1 = -1;
		if (preMaxAbility != -1 && preMaxAbility >= d[index]) {
			p1 = preMaxAbility;
		}
		// 2) 贿赂当前的怪兽 当前的钱 p[index]
		// 若本次付费，前面怪兽必须恰花剩余金额，从该前驱状态转移。
		long preMaxAbility2 = process2(d, p, index - 1, money - p[index]);
		long p2 = -1;
		if (preMaxAbility2 != -1) {
			p2 = d[index] + preMaxAbility2;
		}
		return Math.max(p1, p2);
	}

	public static int minMoney2(int[] d, int[] p) {
		int allMoney = 0;
		for (int i = 0; i < p.length; i++) {
			allMoney += p[i];
		}
		int N = d.length;
		for (int money = 0; money < allMoney; money++) {
			if (process2(d, p, N - 1, money) != -1) {
				return money;
			}
		}
		return allMoney;
	}

	public static long func2(int[] d, int[] p) {
		int sum = 0;
		for (int num : d) {
			sum += num;
		}
		long[][] dp = new long[d.length + 1][sum + 1];
		for (int cur = d.length - 1; cur >= 0; cur--) {
			for (int hp = 0; hp <= sum; hp++) {
				// 如果这种情况发生，那么这个hp必然是递归过程中不会出现的状态
				// 既然动态规划是尝试过程的优化，尝试过程碰不到的状态，不必计算
				if (hp + d[cur] > sum) {
					continue;
				}
				if (hp < d[cur]) {
					dp[cur][hp] = p[cur] + dp[cur + 1][hp + d[cur]];
				} else {
					dp[cur][hp] = Math.min(p[cur] + dp[cur + 1][hp + d[cur]], dp[cur + 1][hp]);
				}
			}
		}
		return dp[0][0];
	}

	public static long func3(int[] d, int[] p) {
		int sum = 0;
		for (int num : p) {
			sum += num;
		}
		// dp[i][j]含义：
		// 能经过0～i的怪兽，且花钱为j（花钱的严格等于j）时的武力值最大是多少？
		// 如果dp[i][j]==-1，表示经过0～i的怪兽，花钱为j是无法通过的，或者之前的钱怎么组合也得不到正好为j的钱数
		int[][] dp = new int[d.length][sum + 1];
		for (int i = 0; i < dp.length; i++) {
			for (int j = 0; j <= sum; j++) {
				dp[i][j] = -1;
			}
		}
		// 经过0～i的怪兽，花钱数一定为p[0]，达到武力值d[0]的地步。其他第0行的状态一律是无效的
		dp[0][p[0]] = d[0];
		for (int i = 1; i < d.length; i++) {
			for (int j = 0; j <= sum; j++) {
				// 可能性一，为当前怪兽花钱
				// 存在条件：
				// j - p[i]要不越界，并且在钱数为j - p[i]时，要能通过0～i-1的怪兽，并且钱数组合是有效的。
				if (j >= p[i] && dp[i - 1][j - p[i]] != -1) {
					dp[i][j] = dp[i - 1][j - p[i]] + d[i];
				}
				// 可能性二，不为当前怪兽花钱
				// 存在条件：
				// 0~i-1怪兽在花钱为j的情况下，能保证通过当前i位置的怪兽
				if (dp[i - 1][j] >= d[i]) {
					// 两种可能性中，选武力值最大的
					dp[i][j] = Math.max(dp[i][j], dp[i - 1][j]);
				}
			}
		}
		int ans = 0;
		// dp表最后一行上，dp[N-1][j]代表：
		// 能经过0～N-1的怪兽，且花钱为j（花钱的严格等于j）时的武力值最大是多少？
		// 那么最后一行上，最左侧的不为-1的列数(j)，就是答案
		for (int j = 0; j <= sum; j++) {
			if (dp[d.length - 1][j] != -1) {
				ans = j;
				break;
			}
		}
		return ans;
	}

	public static int[][] generateTwoRandomArray(int len, int value) {
		int size = (int) (Math.random() * len) + 1;
		int[][] arrs = new int[2][size];
		for (int i = 0; i < size; i++) {
			arrs[0][i] = (int) (Math.random() * value) + 1;
			arrs[1][i] = (int) (Math.random() * value) + 1;
		}
		return arrs;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int len = 10;
		int value = 20;
		int testTimes = 10000;
		for (int i = 0; i < testTimes; i++) {
			int[][] arrs = generateTwoRandomArray(len, value);
			int[] d = arrs[0];
			int[] p = arrs[1];
			long ans1 = func1(d, p);
			long ans2 = func2(d, p);
			long ans3 = func3(d, p);
			long ans4 = minMoney2(d,p);
			if (ans1 != ans2 || ans2 != ans3 || ans1 != ans4) {
				System.out.println("oops!");
			}
		}

	}
```

#### 题解

**为什么正确**

固定当前能力后，两种合法决策覆盖全部后续方案。反向状态中，相同已花金额只需保留最高能力，因为更强不会减少未来可选行动，因此低能力方案可被支配淘汰。

**复杂度**

能力维表O(NΣd)时间/空间；金钱维表O(NΣp)时间/空间，均为伪多项式。暴力指数时间。

**边界与易错点**

能力足够时仍可能值得提前贿赂以应对后面强敌，不能贪心跳过。不可达状态与能力0不同；d、p及总和应在分配和数值范围内。


<a id="course-39"></a>

## 第 39 课：分治、背包与组合计数

### 39.1 子序列累加和模 M 的最大值

#### 题目

给定非负整数数组和正整数 `m`，从任意子序列取数求和，返回累加和模 `m` 的最大值。

**输入、输出与约束**

输入非负整数数组及m≥1；允许空子集，返回子集和模m的最大值。

**函数签名（课程入口）**

```java
public static int max1(int[] arr, int m);
public static int max2(int[] arr, int m);
public static int max3(int[] arr, int m);
public static int max4(int[] arr, int m);
```

**示例**

```text
输入：arr=[3,5,9], m=7
输出：5
```

解释：单选5或选择3和9都得到余数5；穷举其余子集，余数均不超过5。

**出处与版本差异**

- [课程源码：class39/Code01_SubsquenceMaxModM.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class39/Code01_SubsquenceMaxModM.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

规模小可枚举全部子集。总和小时用按和可达表；模数m小时用余数可达表，选当前数后余数变为(old+value)%m。若N约40且m、总和都大，可折半枚举两侧余数，按有序集合查询最大不越过m-1的组合。

#### 代码答案

```java
package class39;

import java.util.HashSet;
import java.util.TreeSet;

// 给定一个非负数组arr，和一个正数m。 返回arr的所有子序列中累加和%m之后的最大值。
public class Code01_SubsquenceMaxModM {

	public static int max1(int[] arr, int m) {
		HashSet<Integer> set = new HashSet<>();
		process(arr, 0, 0, set);
		int max = 0;
		for (Integer sum : set) {
			max = Math.max(max, sum % m);
		}
		return max;
	}

	public static void process(int[] arr, int index, int sum, HashSet<Integer> set) {
		if (index == arr.length) {
			set.add(sum);
		} else {
			process(arr, index + 1, sum, set);
			process(arr, index + 1, sum + arr[index], set);
		}
	}

	public static int max2(int[] arr, int m) {
		int sum = 0;
		int N = arr.length;
		for (int i = 0; i < N; i++) {
			sum += arr[i];
		}
		boolean[][] dp = new boolean[N][sum + 1];
		for (int i = 0; i < N; i++) {
			dp[i][0] = true;
		}
		dp[0][arr[0]] = true;
		for (int i = 1; i < N; i++) {
			for (int j = 1; j <= sum; j++) {
				dp[i][j] = dp[i - 1][j];
				if (j - arr[i] >= 0) {
					dp[i][j] |= dp[i - 1][j - arr[i]];
				}
			}
		}
		int ans = 0;
		for (int j = 0; j <= sum; j++) {
			if (dp[N - 1][j]) {
				ans = Math.max(ans, j % m);
			}
		}
		return ans;
	}

	public static int max3(int[] arr, int m) {
		int N = arr.length;
		// 0...m-1
		boolean[][] dp = new boolean[N][m];
		for (int i = 0; i < N; i++) {
			dp[i][0] = true;
		}
		dp[0][arr[0] % m] = true;
		for (int i = 1; i < N; i++) {
			for (int j = 1; j < m; j++) {
				// dp[i][j] T or F
				dp[i][j] = dp[i - 1][j];
				int cur = arr[i] % m;
				if (cur <= j) {
					dp[i][j] |= dp[i - 1][j - cur];
				} else {
					dp[i][j] |= dp[i - 1][m + j - cur];
				}
			}
		}
		int ans = 0;
		for (int i = 0; i < m; i++) {
			if (dp[N - 1][i]) {
				ans = i;
			}
		}
		return ans;
	}

	// 如果arr的累加和很大，m也很大
	// 但是arr的长度相对不大
	public static int max4(int[] arr, int m) {
		if (arr.length == 1) {
			return arr[0] % m;
		}
		int mid = (arr.length - 1) / 2;
		TreeSet<Integer> sortSet1 = new TreeSet<>();
		process4(arr, 0, 0, mid, m, sortSet1);
		TreeSet<Integer> sortSet2 = new TreeSet<>();
		process4(arr, mid + 1, 0, arr.length - 1, m, sortSet2);
		int ans = 0;
		for (Integer leftMod : sortSet1) {
			// 给定左余数，找不使总余数跨过m的最大右余数。
			ans = Math.max(ans, leftMod + sortSet2.floor(m - 1 - leftMod));
		}
		return ans;
	}

	// 从index出发，最后有边界是end+1，arr[index...end]
	public static void process4(int[] arr, int index, int sum, int end, int m, TreeSet<Integer> sortSet) {
		if (index == end + 1) {
			sortSet.add(sum % m);
		} else {
			process4(arr, index + 1, sum, end, m, sortSet);
			process4(arr, index + 1, sum + arr[index], end, m, sortSet);
		}
	}

	public static int[] generateRandomArray(int len, int value) {
		int[] ans = new int[(int) (Math.random() * len) + 1];
		for (int i = 0; i < ans.length; i++) {
			ans[i] = (int) (Math.random() * value);
		}
		return ans;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int len = 10;
		int value = 100;
		int m = 76;
		int testTime = 500000;
		System.out.println("test begin");
		for (int i = 0; i < testTime; i++) {
			int[] arr = generateRandomArray(len, value);
			int ans1 = max1(arr, m);
			int ans2 = max2(arr, m);
			int ans3 = max3(arr, m);
			int ans4 = max4(arr, m);
			if (ans1 != ans2 || ans2 != ans3 || ans3 != ans4) {
				System.out.println("Oops!");
			}
		}
		System.out.println("test finish!");

	}
```

#### 题解

**为什么正确**

子集的未来作用只通过当前和或余数体现，故相同状态可以合并。折半后任一子集唯一分解为左右选择，枚举一侧并寻找另一侧最优补充就覆盖全部答案；两侧单独选的情况由空集余数0覆盖。

**复杂度**

按总和表O(NS)，按余数表O(Nm)；折半约O(N·2^(N/2))时间、O(2^(N/2))空间。

**边界与易错点**

输入非负使按和背包成立。取模时先求和可能溢出，应扩long或先模。空集贡献0，m=1答案必0。

### 39.2 零食背包的方法数

#### 题目

给定每袋零食的体积和背包容量，每袋至多选择一次，返回总体积不超过容量的选择方法数。

**输入、输出与约束**

输入非负体积数组和非负容量；每袋最多选一次，返回总体积不超容量的选择数。

**函数签名（课程入口）**

```java
public static int ways1(int[] arr, int w);
public static int ways2(int[] arr, int w);
public static int ways3(int[] arr, int w);
```

**示例**

```text
输入：arr=[1,2,3], bag=3
输出：5
```

解释：空集、{1}、{2}、{3}、{1,2}。

**出处与版本差异**

- [课程源码：class39/Code02_SnacksWays.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class39/Code02_SnacksWays.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

每袋零食有体积，目标是不超过容量而非恰好装满。递归按袋选或不选，所有袋处理完只要剩余容量非负就贡献1。表格可直接表示剩余容量下的方案数，或先算各精确体积方案再对0..W求和。

#### 代码答案

```java
package class39;

public class Code02_SnacksWays {

	public static int ways1(int[] arr, int w) {
		// arr[0...]
		return process(arr, 0, w);
	}

	// 从左往右的经典模型
	// 还剩的容量是rest，arr[index...]自由选择，
	// 返回选择方案
	// index ： 0～N
	// rest : 0~w
	public static int process(int[] arr, int index, int rest) {
		// 负容量说明已经超重，该分支不产生合法选择。
		if (rest < 0) { // 没有容量了
			// -1 无方案的意思
			return -1;
		}
		// rest>=0,
		if (index == arr.length) { // 无零食可选
			// 在所有袋已决定且容量未超限的递归终点，当前选择算一种方案。
			return 1;
		}
		// rest >=0
		// 有零食index
		// index号零食，要 or 不要
		// index, rest
		// (index+1, rest)
		// (index+1, rest-arr[i])
		int next1 = process(arr, index + 1, rest); // 不要
		int next2 = process(arr, index + 1, rest - arr[index]); // 要
		return next1 + (next2 == -1 ? 0 : next2);
	}

	public static int ways2(int[] arr, int w) {
		int N = arr.length;
		int[][] dp = new int[N + 1][w + 1];
		for (int j = 0; j <= w; j++) {
			dp[N][j] = 1;
		}
		for (int i = N - 1; i >= 0; i--) {
			for (int j = 0; j <= w; j++) {
				dp[i][j] = dp[i + 1][j] + ((j - arr[i] >= 0) ? dp[i + 1][j - arr[i]] : 0);
			}
		}
		return dp[0][w];
	}

	public static int ways3(int[] arr, int w) {
		int N = arr.length;
		int[][] dp = new int[N][w + 1];
		for (int i = 0; i < N; i++) {
			dp[i][0] = 1;
		}
		if (arr[0] <= w) {
			dp[0][arr[0]] = 1;
		}
		for (int i = 1; i < N; i++) {
			for (int j = 1; j <= w; j++) {
				dp[i][j] = dp[i - 1][j] + ((j - arr[i]) >= 0 ? dp[i - 1][j - arr[i]] : 0);
			}
		}
		int ans = 0;
		for (int j = 0; j <= w; j++) {
			ans += dp[N - 1][j];
		}
		return ans;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int[] arr = { 4, 3, 2, 9 };
		int w = 8;
		System.out.println(ways1(arr, w));
		System.out.println(ways2(arr, w));
		System.out.println(ways3(arr, w));

	}
```

#### 题解

**为什么正确**

每个袋下标对应独立二选一，所有选择组合唯一走到一个叶子；超容量分支不合法，其他叶子包括空选都算一个方案。精确体积状态之间互斥，所以可累加。

**复杂度**

暴力O(2^N)；容量表O(NW)时间、O(NW)空间。

**边界与易错点**

最后应统计≤W而不是只取等于W。零体积袋仍有选与不选两种下标方案，必须保留；总方案数可达2^N，int易溢出。

### 39.3 零食背包的大数据输入输出版本（一）

#### 题目

输入1≤N≤30左右的正体积零食与容量；在线评测main读取N、W、N个体积，输出long方案数。

**输入、输出与约束**

输入1≤N≤30左右的正体积零食与容量；在线评测main读取N、W、N个体积，输出long方案数。

**函数签名（课程入口）**

```java
public static long ways(int[] arr, int bag);
```

**示例**

```text
输入：arr=[1,2,3], bag=3
输出：5
```

解释：两个半区的组合最终覆盖空集及四个非空合法选择。

**出处与版本差异**

- [课程源码：class39/Code02_SnacksWaysMain1.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class39/Code02_SnacksWaysMain1.java)。
- [牛客网在线评测](https://www.nowcoder.com/questionTerminal/d94bb2fa461d42bcb4c0f2b94f5d4281)。

#### 思路

将袋子分两半，分别枚举各个非空选择的体积和与重数。右侧按体积建立累计方案数；对每个左和x，用有序表查右和≤W-x的总数，两侧重数相乘。另加仅选左、仅选右及全空方案。

#### 代码答案

```java
// 不要拷贝包信息的内容
package class39;

// 课堂版本
// 本文件是Code02_SnacksWays问题的牛客题目解答
// 但是用的分治的方法
// 这是牛客的测试链接：
// https://www.nowcoder.com/questionTerminal/d94bb2fa461d42bcb4c0f2b94f5d4281
// 请同学们务必参考如下代码中关于输入、输出的处理
// 这是输入输出处理效率很高的写法
// 提交如下的代码，并把主类名改成"Main"
// 可以直接通过
import java.util.Map.Entry;
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.io.StreamTokenizer;
import java.util.TreeMap;

public class Code02_SnacksWaysMain1 {

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StreamTokenizer in = new StreamTokenizer(br);
		PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
		while (in.nextToken() != StreamTokenizer.TT_EOF) {
			int n = (int) in.nval;
			in.nextToken();
			int bag = (int) in.nval;
			int[] arr = new int[n];
			for (int i = 0; i < n; i++) {
				in.nextToken();
				arr[i] = (int) in.nval;
			}
			long ways = ways(arr, bag);
			out.println(ways);
			out.flush();
		}
	}

	public static long ways(int[] arr, int bag) {
		if (arr == null || arr.length == 0) {
			return 0;
		}
		if (arr.length == 1) {
			return arr[0] <= bag ? 2 : 1;
		}
		int mid = (arr.length - 1) >> 1;
		TreeMap<Long, Long> lmap = new TreeMap<>();
		long ways = process(arr, 0, 0, mid, bag, lmap);
		TreeMap<Long, Long> rmap = new TreeMap<>();
		ways += process(arr, mid + 1, 0, arr.length - 1, bag, rmap);
		TreeMap<Long, Long> rpre = new TreeMap<>();
		long pre = 0;
		for (Entry<Long, Long> entry : rmap.entrySet()) {
			pre += entry.getValue();
			rpre.put(entry.getKey(), pre);
		}
		for (Entry<Long, Long> entry : lmap.entrySet()) {
			long lweight = entry.getKey();
			long lways = entry.getValue();
			Long floor = rpre.floorKey(bag - lweight);
			if (floor != null) {
				long rways = rpre.get(floor);
				// 左右选择互相独立，固定左和组与合法右和组的重数相乘。
				ways += lways * rways;
			}
		}
		// 前面只统计非空选择，在末尾补上唯一全空方案。
		return ways + 1;
	}

	// arr 30
	// func(arr, 0, 14, 0, bag, map)

	// func(arr, 15, 29, 0, bag, map)

	// 从index出发，到end结束
	// 之前的选择，已经形成的累加和sum
	// 零食[index....end]自由选择，出来的所有累加和，不能超过bag，每一种累加和对应的方法数，填在map里
	// 最后不能什么货都没选
	// [3,3,3,3] bag = 6
	// 0 1 2 3
	// - - - - 0 -> （0 : 1）
	// - - - $ 3 -> （0 : 1）(3, 1)
	// - - $ - 3 -> （0 : 1）(3, 2)
	public static long func(int[] arr, int index, int end, long sum, long bag, TreeMap<Long, Long> map) {
		if (sum > bag) {
			return 0;
		}
		// sum <= bag
		if (index > end) { // 所有商品自由选择完了！
			// sum
			if (sum != 0) {
				if (!map.containsKey(sum)) {
					map.put(sum, 1L);
				} else {
					map.put(sum, map.get(sum) + 1);
				}
				return 1;
			} else {
				return 0;
			}
		}
		// sum <= bag 并且 index <= end(还有货)
		// 1) 不要当前index位置的货
		long ways = func(arr, index + 1, end, sum, bag, map);

		// 2) 要当前index位置的货
		ways += func(arr, index + 1, end, sum + arr[index], bag, map);
		return ways;
	}

	public static long process(int[] arr, int index, long w, int end, int bag, TreeMap<Long, Long> map) {
		if (w > bag) {
			return 0;
		}
		if (index > end) {
			// 此版本把和0专门留作空集标记，所以体积必须为正。
			if (w != 0) {
				if (!map.containsKey(w)) {
					map.put(w, 1L);
				} else {
					map.put(w, map.get(w) + 1);
				}
				return 1;
			} else {
				return 0;
			}
		} else {
			long ways = process(arr, index + 1, w, end, bag, map);
			ways += process(arr, index + 1, w + arr[index], end, bag, map);
			return ways;
		}
	}

}
```

---

##### 输入输出核对

先以本题示例核对结果，再重点覆盖：该源码用sum!=0区分非空选择，故要求体积严格为正；零体积会被误当空集漏计。最终空选择只加一次。结果用long。

该版本保留在线评测入口；未附独立随机对数器，不把编译通过当作正确性证明。

#### 题解

**为什么正确**

任一非空选择唯一属于仅左、仅右、两侧都有三类。两侧都有时左右选择独立，乘法计数；按右前缀查询包含全部不超剩余容量的右选择，因此不重不漏。

**复杂度**

时间O(N·2^(N/2))量级，空间O(2^(N/2))，不依赖容量大小。

**边界与易错点**

该源码用sum!=0区分非空选择，故要求体积严格为正；零体积会被误当空集漏计。最终空选择只加一次。结果用long。

### 39.4 零食背包的大数据输入输出版本（二）

#### 题目

1≤N≤30，非负体积与容量；返回所有子集配对中体积不超容量的long计数。

**输入、输出与约束**

1≤N≤30，非负体积与容量；返回所有子集配对中体积不超容量的long计数。

**函数签名（课程入口）**

```java
public static long ways(long w);
public static int find(long num);
```

**示例**

```text
输入：arr=[0,1], bag=1
输出：4
```

解释：空选、选0、选1、两者都选；零体积下标仍区分。

**出处与版本差异**

- [课程源码：class39/Code02_SnacksWaysMain2.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class39/Code02_SnacksWaysMain2.java)。
- [牛客网在线评测](https://www.nowcoder.com/questionTerminal/d94bb2fa461d42bcb4c0f2b94f5d4281)。

#### 思路

折半枚举两边全部子集和，包括空集0，放入数组排序。相同左和合成一组重数，用二分找右和≤W-left的数量；二者相乘加入答案。

#### 代码答案

```java
// 不要拷贝包信息的内容
package class39;

// 优化版本
// 这是牛客的测试链接：
// https://www.nowcoder.com/questionTerminal/d94bb2fa461d42bcb4c0f2b94f5d4281
// 请同学们务必参考如下代码中关于输入、输出的处理
// 这是输入输出处理效率很高的写法
// 提交如下的代码，并把主类名改成"Main"
// 可以直接通过
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.io.StreamTokenizer;
import java.util.Arrays;

public class Code02_SnacksWaysMain2 {

	// 用来收集所有输入的数字
	public static long[] arr = new long[31];
	public static int size = 0;
	// 用来生成左部分可能的所有累加和
	public static long[] leftSum = new long[1 << 16];
	// 准备的数组可能用不完，左部分生成了多少累加和，用leftSize表示
	public static int leftSize = 0;
	// 用来生成右部分可能的所有累加和
	public static long[] rightSum = new long[1 << 16];
	// 准备的数组可能用不完，左部分生成了多少累加和，用leftSize表示
	public static int rightSize = 0;

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StreamTokenizer in = new StreamTokenizer(br);
		PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
		while (in.nextToken() != StreamTokenizer.TT_EOF) {
			size = (int) in.nval;
			in.nextToken();
			int bag = (int) in.nval;
			for (int i = 0; i < size; i++) {
				in.nextToken();
				arr[i] = (int) in.nval;
			}
			long ways = ways(bag);
			out.println(ways);
			out.flush();
		}
	}

	public static long ways(long w) {
		if (size == 0) {
			return 0;
		}
		if (size == 1) {
			return arr[0] <= w ? 2 : 1;
		}
		// 求中点
		int mid = size >> 1;
		// 生成左侧的累加和
		leftSize = 0;
		dfsLeft(0, mid + 1, 0L);
		// 生成右侧的累加和
		rightSize = 0;
		dfsRight(mid + 1, size, 0L);
		// 把左侧累加和排序
		Arrays.sort(leftSum, 0, leftSize);
		// 把右侧累加和排序
		Arrays.sort(rightSum, 0, rightSize);
		// 解释一下，接下来的流程。
		// 举个例子，比如：
		// 左侧累加和是:{0, 1, 1, 1, 2, 2, 3, 4, 4}
		// 右侧累加和是:{0, 1, 2, 3, 3, 3, 4, 4, 5}
		// w = 5
		// 左侧严格得到0的方法数：1
		// 右侧得到<=5的方法数（二分求出）：9
		// 1 * 9
		// 左侧严格得到1的方法数：3
		// 右侧得到<=4的方法数（二分求出）：8
		// 3 * 8
		// 左侧严格得到2的方法数：2
		// 右侧得到<=3的方法数（二分求出）：6
		// 2 * 6
		// 左侧严格得到3的方法数：1
		// 右侧得到<=2的方法数（二分求出）：3
		// 1 * 3
		// 左侧严格得到4的方法数：2
		// 右侧得到<=1的方法数（二分求出）：2
		// 2 * 2
		// 都累加起来
		// 其实和课上讲的一样！多看一下例子
		long ans = 0;
		long count = 1;
		for (int i = 1; i < leftSize; i++) {
			if (leftSum[i] != leftSum[i - 1]) {
				// 相同左和的count个方案，各能搭配相同数量的右子集。
				ans += count * (long) find(w - leftSum[i - 1]);
				count = 1;
			} else {
				count++;
			}
		}
		// 相同左和的count个方案，各能搭配相同数量的右子集。
		ans += count * (long) find(w - leftSum[leftSize - 1]);
		return ans;
	}

	// 生成左部分的累加和，每一个累加和出来，都记录
	public static void dfsLeft(int cur, int end, long sum) {
		if (cur == end) { // 已经终止位置了
			// 记录累加和
			// 保存每个下标子集的和，包括空集与重复和。
			leftSum[leftSize++] = sum;
		} else {
			// 可能性1，不要当前数
			dfsLeft(cur + 1, end, sum);
			// 可能性2，要当前数
			dfsLeft(cur + 1, end, sum + arr[cur]);
		}
	}

	// 生成右部分的累加和，每一个累加和出来，都记录
	public static void dfsRight(int cur, int end, long sum) {
		if (cur == end) { // 已经终止位置了
			// 记录累加和
			rightSum[rightSize++] = sum;
		} else {
			// 可能性1，不要当前数
			dfsRight(cur + 1, end, sum);
			// 可能性2，要当前数
			dfsRight(cur + 1, end, sum + arr[cur]);
		}
	}

	// <= num的数的个数，返回
	public static int find(long num) {
		int ans = -1;
		int l = 0;
		int r = rightSize - 1;
		int m = 0;
		while (l <= r) {
			m = (l + r) / 2;
			if (rightSum[m] <= num) {
				ans = m;
				l = m + 1;
			} else {
				r = m - 1;
			}
		}
		return ans + 1;
	}

}
```

---

##### 输入输出核对

先以本题示例核对结果，再重点覆盖：左右数组仅排序已使用范围，旧数据不能参与。两边已经包含空集，不能最后再加1。重复和不是重复方案，不能直接去重丢掉次数。

该版本保留在线评测入口；未附独立随机对数器，不把编译通过当作正确性证明。

#### 题解

**为什么正确**

每个全体下标子集唯一拆成左右子集。排序只调整枚举顺序而保留每个和的重数，二分取满足容量的右前缀大小，故计数等于全部合法配对数。

**复杂度**

时间O(N·2^(N/2))，空间O(2^(N/2))；数组容量限定N≤30。

**边界与易错点**

左右数组仅排序已使用范围，旧数据不能参与。两边已经包含空集，不能最后再加1。重复和不是重复方案，不能直接去重丢掉次数。

### 39.5 前缀合法的 0-1 序列数量（卡特兰数）

#### 题目

给定正整数 `N`，用恰好 `N` 个 0 和 `N` 个 1 组成序列，要求任意前缀中 0 的数量不少于 1，返回合法序列数量。

**输入、输出与约束**

输入非负N；用恰好N个0和N个1组成前缀始终0不少于1的序列，返回方案数。

**函数签名（课程入口）**

```java
public static long ways1(int N);
public static long ways2(int N);
```

**示例**

```text
输入：N=2
输出：2
```

解释：合法序列0011和0101。

**出处与版本差异**

- [课程源码：class39/Code03_10Ways.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class39/Code03_10Ways.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

把0看作左括号、1看作右括号；任意前缀0不少于1等价于括号串从不先关闭。合法序列数是卡特兰数C(2N,N)-C(2N,N-1)，也可枚举或按首次回到平衡点拆分。

#### 代码答案

```java
package class39;

import java.util.LinkedList;

public class Code03_10Ways {

	public static long ways1(int N) {
		int zero = N;
		int one = N;
		LinkedList<Integer> path = new LinkedList<>();
		LinkedList<LinkedList<Integer>> ans = new LinkedList<>();
		process(zero, one, path, ans);
		long count = 0;
		for (LinkedList<Integer> cur : ans) {
			int status = 0;
			for (Integer num : cur) {
				if (num == 0) {
					status++;
				} else {
					status--;
				}
				// 当前前缀的1已经多于0，此序列不合法，后面的字符无法修复前缀。
				if (status < 0) {
					break;
				}
			}
			if (status == 0) {
				count++;
			}
		}
		return count;
	}

	public static void process(int zero, int one, LinkedList<Integer> path, LinkedList<LinkedList<Integer>> ans) {
		if (zero == 0 && one == 0) {
			LinkedList<Integer> cur = new LinkedList<>();
			for (Integer num : path) {
				cur.add(num);
			}
			ans.add(cur);
		} else {
			if (zero == 0) {
				path.addLast(1);
				process(zero, one - 1, path, ans);
				path.removeLast();
			} else if (one == 0) {
				path.addLast(0);
				process(zero - 1, one, path, ans);
				path.removeLast();
			} else {
				path.addLast(1);
				process(zero, one - 1, path, ans);
				path.removeLast();
				path.addLast(0);
				process(zero - 1, one, path, ans);
				path.removeLast();
			}
		}
	}

	public static long ways2(int N) {
		if (N < 0) {
			return 0;
		}
		if (N < 2) {
			return 1;
		}
		long a = 1;
		long b = 1;
		long limit = N << 1;
		for (long i = 1; i <= limit; i++) {
			if (i <= N) {
				a *= i;
			} else {
				b *= i;
			}
		}
		// 先算中心二项式系数，再除N+1得到卡特兰数；中间乘积必须不溢出。
		return (b / a) / (N + 1);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		System.out.println("test begin");
		for (int i = 0; i < 10; i++) {
			long ans1 = ways1(i);
			long ans2 = ways2(i);
			if (ans1 != ans2) {
				System.out.println("Oops!");
			}
		}
		System.out.println("test finish");
	}
```

#### 题解

**为什么正确**

全部含N个0、N个1的序列共有C(2N,N)。对坏序列，翻转到第一次1比0多一的最短前缀，就得到含N+1个0、N-1个1的序列；反向翻转到第一次0比1多一的前缀可恢复原序列。这是双射，故坏序列数为C(2N,N-1)，相减得到卡特兰数C(2N,N)/(N+1)。

**复杂度**

枚举指数时间；组合公式乘除O(N)次整数操作，实际大整数成本随位数增长，固定long需避免中间乘积溢出。

**边界与易错点**

需要每个前缀合法，不是只看总0和1数量相等。N=0有一个空序列。除法前的中间乘积可能比最终答案更早溢出。

### 39.6 不同二叉树结构的数量

#### 题目

给定节点数 `n`，返回由 `n` 个不同键能够组成的不同二叉搜索树结构数量。

**输入、输出与约束**

输入非负节点数N；返回N个互异有序键的二叉搜索树结构数。

**函数签名（课程入口）**

```java
public static long num1(int N);
public static long num2(int N);
public static long gcd(long m, long n);
```

**示例**

```text
输入：N=3
输出：5
```

解释：根分别取1、2、3时，左右形状组合数为2、1、2。

**出处与版本差异**

- [课程源码：class39/Code04_DifferentBTNum.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class39/Code04_DifferentBTNum.java)。
- [LeetCode 对应题 96. 不同的二叉搜索树（Unique Binary Search Trees）](https://leetcode.com/problems/unique-binary-search-trees/)

#### 思路

固定N个不同有序键，根为第k个键时左边有k-1个、右边有N-k个，左右形状可独立组合。于是dp[N]=Σdp[L]·dp[N-1-L]，dp[0]=1；结果是卡特兰数。

#### 代码答案

```java
package class39;

public class Code04_DifferentBTNum {

//	k(0) = 1, k(1) = 1
//
//	k(n) = k(0) * k(n - 1) + k(1) * k(n - 2) + ... + k(n - 2) * k(1) + k(n - 1) * k(0)
//	或者
//	k(n) = c(2n, n) / (n + 1)
//	或者
//	k(n) = c(2n, n) - c(2n, n-1)

	public static long num1(int N) {
		if (N < 0) {
			return 0;
		}
		if (N < 2) {
			return 1;
		}
		long[] dp = new long[N + 1];
		// 空子树是一种可选形态，作为左右组合的乘法单位。
		dp[0] = 1;
		dp[1] = 1;
		for (int i = 2; i <= N; i++) {
			for (int leftSize = 0; leftSize < i; leftSize++) {
				dp[i] += dp[leftSize] * dp[i - 1 - leftSize];
			}
		}
		return dp[N];
	}

	public static long num2(int N) {
		if (N < 0) {
			return 0;
		}
		if (N < 2) {
			return 1;
		}
		long a = 1;
		long b = 1;
		for (int i = 1, j = N + 1; i <= N; i++, j++) {
			a *= i;
			b *= j;
			long gcd = gcd(a, b);
			a /= gcd;
			b /= gcd;
		}
		return (b / a) / (N + 1);
	}

	public static long gcd(long m, long n) {
		return n == 0 ? m : gcd(n, m % n);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		System.out.println("test begin");
		for (int i = 0; i < 15; i++) {
			long ans1 = num1(i);
			long ans2 = num2(i);
			if (ans1 != ans2) {
				System.out.println("Oops!");
			}
		}
		System.out.println("test finish");
	}
```

#### 题解

**为什么正确**

任一搜索树的根秩唯一决定左右键集合，左右子树形状又各自唯一。不同根秩或不同子树形状产生不同整树，按乘法和加法原理正好枚举所有结构。

**复杂度**

动态规划O(N²)时间、O(N)空间；组合公式O(N)次固定宽度算术，需满足long不溢出。

**边界与易错点**

空树必须计为一种，否则叶子无法由空左右树组合出来。键不同且有序范围固定，题目计结构，不是再乘N!种标签排列。

### 39.7 判断数组是否存在累加和为目标值的子序列

#### 题目

给定整数数组和目标值 `sum`，判断是否存在一个子序列的元素累加和恰好等于目标值。

**输入、输出与约束**

输入任意整数数组与目标和，允许空子集；返回是否存在恰好目标和的子集。

**函数签名（课程入口）**

```java
public static boolean isSum1(int[] arr, int sum);
public static boolean isSum2(int[] arr, int sum);
public static boolean isSum3(int[] arr, int sum);
public static boolean isSum4(int[] arr, int sum);
```

**示例**

```text
输入：arr=[3,-2,7], sum=1
输出：true
```

解释：选择3与-2。

**出处与版本差异**

- [课程源码：class39/IsSum.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class39/IsSum.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

有负数时不能只用0..target容量。可先统计最小可能和min（全部负数）及最大和max（全部正数），把可达和用偏移-min映射到数组；若数值范围太大但N不大，则折半枚举两边和，用集合查target-left。

#### 代码答案

```java
package class39;

import java.util.HashMap;
import java.util.HashSet;

// 这道题是一个小小的补充，课上没有讲
// 但是如果你听过体系学习班动态规划专题和本节课的话
// 这道题就是一道水题
public class IsSum {

	// arr中的值可能为正，可能为负，可能为0
	// 自由选择arr中的数字，能不能累加得到sum
	// 暴力递归方法
	public static boolean isSum1(int[] arr, int sum) {
		if (sum == 0) {
			return true;
		}
		if (arr == null || arr.length == 0) {
			return false;
		}
		return process1(arr, arr.length - 1, sum);
	}

	// 可以自由使用arr[0...i]上的数字，能不能累加得到sum
	public static boolean process1(int[] arr, int i, int sum) {
		if (sum == 0) {
			return true;
		}
		if (i == -1) {
			return false;
		}
		return process1(arr, i - 1, sum) || process1(arr, i - 1, sum - arr[i]);
	}

	// arr中的值可能为正，可能为负，可能为0
	// 自由选择arr中的数字，能不能累加得到sum
	// 记忆化搜索方法
	// 从暴力递归方法来，加了记忆化缓存，就是动态规划了
	public static boolean isSum2(int[] arr, int sum) {
		if (sum == 0) {
			return true;
		}
		if (arr == null || arr.length == 0) {
			return false;
		}
		return process2(arr, arr.length - 1, sum, new HashMap<>());
	}

	public static boolean process2(int[] arr, int i, int sum, HashMap<Integer, HashMap<Integer, Boolean>> dp) {
		if (dp.containsKey(i) && dp.get(i).containsKey(sum)) {
			return dp.get(i).get(sum);
		}
		boolean ans = false;
		if (sum == 0) {
			ans = true;
		} else if (i != -1) {
			ans = process2(arr, i - 1, sum, dp) || process2(arr, i - 1, sum - arr[i], dp);
		}
		if (!dp.containsKey(i)) {
			dp.put(i, new HashMap<>());
		}
		dp.get(i).put(sum, ans);
		return ans;
	}

	// arr中的值可能为正，可能为负，可能为0
	// 自由选择arr中的数字，能不能累加得到sum
	// 经典动态规划
	public static boolean isSum3(int[] arr, int sum) {
		if (sum == 0) {
			return true;
		}
		if (arr == null || arr.length == 0) {
			return false;
		}
		int min = 0;
		int max = 0;
		for (int num : arr) {
			min += num < 0 ? num : 0;
			max += num > 0 ? num : 0;
		}
		// 目标超出全部子集可能值域，不必分配表或继续搜索。
		if (sum < min || sum > max) {
			return false;
		}
		int N = arr.length;
		boolean[][] dp = new boolean[N][max - min + 1];
		dp[0][-min] = true;
		dp[0][arr[0] - min] = true;
		for (int i = 1; i < N; i++) {
			for (int j = min; j <= max; j++) {
				dp[i][j - min] = dp[i - 1][j - min];
				int next = j - min - arr[i];
				dp[i][j - min] |= (next >= 0 && next <= max - min && dp[i - 1][next]);
			}
		}
		return dp[N - 1][sum - min];
	}

	// arr中的值可能为正，可能为负，可能为0
	// 自由选择arr中的数字，能不能累加得到sum
	// 分治的方法
	// 如果arr中的数值特别大，动态规划方法依然会很慢
	// 此时如果arr的数字个数不算多(40以内)，哪怕其中的数值很大，分治的方法也将是最优解
	public static boolean isSum4(int[] arr, int sum) {
		if (sum == 0) {
			return true;
		}
		if (arr == null || arr.length == 0) {
			return false;
		}
		if (arr.length == 1) {
			return arr[0] == sum;
		}
		int N = arr.length;
		int mid = N >> 1;
		HashSet<Integer> leftSum = new HashSet<>();
		HashSet<Integer> rightSum = new HashSet<>();
		process4(arr, 0, mid, 0, leftSum);
		process4(arr, mid, N, 0, rightSum);
		for (int l : leftSum) {
			if (rightSum.contains(sum - l)) {
				return true;
			}
		}
		return false;
	}

	public static void process4(int[] arr, int i, int end, int pre, HashSet<Integer> ans) {
		if (i == end) {
			ans.add(pre);
		} else {
			process4(arr, i + 1, end, pre, ans);
			process4(arr, i + 1, end, pre + arr[i], ans);
		}
	}

	// 对数器验证所有方法

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	// 生成长度为len的随机数组
	// 值在[-max, max]上随机
	public static int[] randomArray(int len, int max) {
		int[] arr = new int[len];
		for (int i = 0; i < len; i++) {
			arr[i] = (int) (Math.random() * ((max << 1) + 1)) - max;
		}
		return arr;
	}

public static void main(String[] args) {
		int N = 20;
		int M = 100;
		int testTime = 100000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int size = (int) (Math.random() * (N + 1));
			int[] arr = randomArray(size, M);
			int sum = (int) (Math.random() * ((M << 1) + 1)) - M;
			boolean ans1 = isSum1(arr, sum);
			boolean ans2 = isSum2(arr, sum);
			boolean ans3 = isSum3(arr, sum);
			boolean ans4 = isSum4(arr, sum);
			if (ans1 ^ ans2 || ans3 ^ ans4 || ans1 ^ ans3) {
				System.out.println("出错了！");
				System.out.print("arr : ");
				for (int num : arr) {
					System.out.print(num + " ");
				}
				System.out.println();
				System.out.println("sum : " + sum);
				System.out.println("方法一答案 : " + ans1);
				System.out.println("方法二答案 : " + ans2);
				System.out.println("方法三答案 : " + ans3);
				System.out.println("方法四答案 : " + ans4);
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

任意子集和必在[min,max]内，偏移只是重编号，不改变可达关系。折半时全体子集唯一分解为左右选择，命中互补和当且仅当两者合成目标。

**复杂度**

值域表O(N(max-min+1))时间及相应表空间；折半O(2^(N/2))期望枚举查找时间、O(2^(N/2))空间。

**边界与易错点**

目标为0时空集立即合法。负数使“当前和超过目标就剪枝”不成立；范围差可能溢出或过大，应根据输入选择折半。


<a id="course-40"></a>

## 第 40 课：子数组技巧与矩阵打印

### 40.1 正数数组中累加和为 K 的最长子数组

#### 题目

给定正整数数组和目标值 `K`，返回累加和恰好为 `K` 的最长子数组长度。

**输入、输出与约束**

输入正整数数组与正K；返回和恰为K的最长非空连续子数组长度，无解0。

**函数签名（课程入口）**

```java
public static int getMaxLength(int[] arr, int K);
```

**示例**

```text
输入：arr=[1,2,1,1,1], K=3
输出：3
```

解释：末尾三个1和为3，长度超过前面的1+2。

**出处与版本差异**

- [课程源码：class40/Code01_LongestSumSubArrayLengthInPositiveArray.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class40/Code01_LongestSumSubArrayLengthInPositiveArray.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

正数数组中，窗口和小于K就向右扩张，大于K就移动左端缩小，等于K时记录长度再缩小左端继续。两个指针都只向右。

#### 代码答案

```java
package class40;

public class Code01_LongestSumSubArrayLengthInPositiveArray {

	public static int getMaxLength(int[] arr, int K) {
		if (arr == null || arr.length == 0 || K <= 0) {
			return 0;
		}
		int left = 0;
		int right = 0;
		int sum = arr[0];
		int len = 0;
		while (right < arr.length) {
			if (sum == K) {
				len = Math.max(len, right - left + 1);
				// 缩小左端使和下降；命中后也需前进，继续寻找可能更长的其他窗口。
				sum -= arr[left++];
			} else if (sum < K) {
				right++;
				if (right == arr.length) {
					break;
				}
				// 严格正数使扩张窗口一定增大和；和偏小时推进右端寻找目标。
				sum += arr[right];
			} else {
				// 缩小左端使和下降；命中后也需前进，继续寻找可能更长的其他窗口。
				sum -= arr[left++];
			}
		}
		return len;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int right(int[] arr, int K) {
		int max = 0;
		for (int i = 0; i < arr.length; i++) {
			for (int j = i; j < arr.length; j++) {
				if (valid(arr, i, j, K)) {
					max = Math.max(max, j - i + 1);
				}
			}
		}
		return max;
	}

	// for test
	public static boolean valid(int[] arr, int L, int R, int K) {
		int sum = 0;
		for (int i = L; i <= R; i++) {
			sum += arr[i];
		}
		return sum == K;
	}

	// for test
	public static int[] generatePositiveArray(int size, int value) {
		int[] ans = new int[size];
		for (int i = 0; i != size; i++) {
			ans[i] = (int) (Math.random() * value) + 1;
		}
		return ans;
	}

	// for test
	public static void printArray(int[] arr) {
		for (int i = 0; i != arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

public static void main(String[] args) {
		int len = 50;
		int value = 100;
		int testTime = 500000;
		System.out.println("test begin");
		for (int i = 0; i < testTime; i++) {
			int[] arr = generatePositiveArray(len, value);
			int K = (int) (Math.random() * value) + 1;
			int ans1 = getMaxLength(arr, K);
			int ans2 = right(arr, K);
			if (ans1 != ans2) {
				System.out.println("Oops!");
				printArray(arr);
				System.out.println("K : " + K);
				System.out.println(ans1);
				System.out.println(ans2);
				break;
			}
		}
		System.out.println("test end");
	}
```

#### 题解

**为什么正确**

扩张必增加和，缩小必减少和，因此和过大时任何更长右扩张都不可能命中；和过小时只有扩大才可能达到目标。被舍弃方向无法产生遗漏解，所有可行窗口边界沿单调轨迹被检查。

**复杂度**

时间O(N)，额外空间O(1)。

**边界与易错点**

严格正数是该移动策略依据；包含负数时必须换前缀和方法。源码K≤0直接返回0。记录长度要包含两端。

### 40.2 任意数组中累加和为 K 的最长子数组

#### 题目

给定可包含正数、负数和零的数组及目标值 `K`，返回累加和恰好为 `K` 的最长子数组长度。

**输入、输出与约束**

输入可含正负零的整数数组和K；返回和K的最长连续区间长度，无解0；前缀和需不溢出。

**函数签名（课程入口）**

```java
public static int maxLength(int[] arr, int k);
```

**示例**

```text
输入：arr=[1,-1,5,-2,3], K=3
输出：4
```

解释：[1,-1,5,-2]的和为3。

**出处与版本差异**

- [课程源码：class40/Code02_LongestSumSubArrayLength.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class40/Code02_LongestSumSubArrayLength.java)。
- [LeetCode 原题 325. 和等于 K 的最长子数组长度（Maximum Size Subarray Sum Equals k）](https://leetcode.com/problems/maximum-size-subarray-sum-equals-k/)

#### 思路

扫描前缀和sum，用哈希表只保存每个前缀值首次出现的位置。当前下标i要得到和K，需历史前缀sum-K；若存在，候选长度为i-最早位置。预置前缀0出现在-1。

#### 代码答案

```java
package class40;

import java.util.HashMap;

public class Code02_LongestSumSubArrayLength {

	public static int maxLength(int[] arr, int k) {
		if (arr == null || arr.length == 0) {
			return 0;
		}
		// key:前缀和
		// value : 0~value这个前缀和是最早出现key这个值的
		HashMap<Integer, Integer> map = new HashMap<Integer, Integer>();
		// 原数组开始前的空前缀和为0，使首元素起始区间也能通过差值找到。
		map.put(0, -1); // important
		int len = 0;
		int sum = 0;
		for (int i = 0; i < arr.length; i++) {
			sum += arr[i];
			if (map.containsKey(sum - k)) {
				// 历史前缀到当前前缀之差为k，两个下标之差就是区间长度。
				len = Math.max(i - map.get(sum - k), len);
			}
			// 仅在首次出现时登记，最早位置才能给出最长候选。
			if (!map.containsKey(sum)) {
				map.put(sum, i);
			}
		}
		return len;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int right(int[] arr, int K) {
		int max = 0;
		for (int i = 0; i < arr.length; i++) {
			for (int j = i; j < arr.length; j++) {
				if (valid(arr, i, j, K)) {
					max = Math.max(max, j - i + 1);
				}
			}
		}
		return max;
	}

	// for test
	public static boolean valid(int[] arr, int L, int R, int K) {
		int sum = 0;
		for (int i = L; i <= R; i++) {
			sum += arr[i];
		}
		return sum == K;
	}

	// for test
	public static int[] generateRandomArray(int size, int value) {
		int[] ans = new int[(int) (Math.random() * size) + 1];
		for (int i = 0; i < ans.length; i++) {
			ans[i] = (int) (Math.random() * value) - (int) (Math.random() * value);
		}
		return ans;
	}

	// for test
	public static void printArray(int[] arr) {
		for (int i = 0; i != arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

public static void main(String[] args) {
		int len = 50;
		int value = 100;
		int testTime = 500000;

		System.out.println("test begin");
		for (int i = 0; i < testTime; i++) {
			int[] arr = generateRandomArray(len, value);
			int K = (int) (Math.random() * value) - (int) (Math.random() * value);
			int ans1 = maxLength(arr, K);
			int ans2 = right(arr, K);
			if (ans1 != ans2) {
				System.out.println("Oops!");
				printArray(arr);
				System.out.println("K : " + K);
				System.out.println(ans1);
				System.out.println(ans2);
				break;
			}
		}
		System.out.println("test end");

	}
```

#### 题解

**为什么正确**

任一区间和为两个前缀之差。固定右端与目标K后，所需前缀值唯一；同值出现越早，区间越长，因此后来的同值位置都被最早位置支配，不需覆盖。

**复杂度**

期望时间O(N)，空间O(N)。

**边界与易错点**

必须预置0→-1以覆盖从首元素开始的区间。只保存首次位置，不能每次put覆盖。包含负数时不要用普通滑动窗口。

### 40.3 累加和小于等于 K 的最长子数组

#### 题目

给定整数数组和目标值 `K`，返回累加和小于等于 `K` 的最长子数组长度。

**输入、输出与约束**

输入任意整数数组和K；返回和≤K的最长非空子数组长度，无解0。

**函数签名（课程入口）**

```java
public static int maxLengthAwesome(int[] arr, int k);
public static int maxLength(int[] arr, int k);
public static int getLessIndex(int[] arr, int num);
```

**示例**

```text
输入：arr=[3,-2,-4,0,6], K=-2
输出：4
```

解释：前四项和为-3≤-2，加入6后不合法。

**出处与版本差异**

- [课程源码：class40/Code03_LongestLessSumSubArrayLength.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class40/Code03_LongestLessSumSubArrayLength.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

从右向左预处理minSums[i]：必须从i开始的最小和区块，以及它的末位置minSumEnds[i]。如果下一区块和为负就一起吸收，否则只取当前元素。扫描左端时，右端尽可能整块吸收这些最小和区间，达到无法继续才记录长度并右移左端。

#### 代码答案

```java
package class40;

public class Code03_LongestLessSumSubArrayLength {

	public static int maxLengthAwesome(int[] arr, int k) {
		if (arr == null || arr.length == 0) {
			return 0;
		}
		int[] minSums = new int[arr.length];
		int[] minSumEnds = new int[arr.length];
		minSums[arr.length - 1] = arr[arr.length - 1];
		minSumEnds[arr.length - 1] = arr.length - 1;
		for (int i = arr.length - 2; i >= 0; i--) {
			// 后一区块可降低当前起始区间的和，合并得到更有利的扩张块。
			if (minSums[i + 1] < 0) {
				minSums[i] = arr[i] + minSums[i + 1];
				minSumEnds[i] = minSumEnds[i + 1];
			} else {
				minSums[i] = arr[i];
				minSumEnds[i] = i;
			}
		}
		// 迟迟扩不进来那一块儿的开头位置
		int end = 0;
		int sum = 0;
		int ans = 0;
		for (int i = 0; i < arr.length; i++) {
			// while循环结束之后：
			// 1) 如果以i开头的情况下，累加和<=k的最长子数组是arr[i..end-1]，看看这个子数组长度能不能更新res；
			// 2) 如果以i开头的情况下，累加和<=k的最长子数组比arr[i..end-1]短，更新还是不更新res都不会影响最终结果；
			while (end < arr.length && sum + minSums[end] <= k) {
				sum += minSums[end];
				// 整个最小和区块都被吸收，右端直接跳到区块之后。
				end = minSumEnds[end] + 1;
			}
			ans = Math.max(ans, end - i);
			if (end > i) { // 还有窗口，哪怕窗口没有数字 [i~end) [4,4)
				sum -= arr[i];
			} else { // i == end,  即将 i++, i > end, 此时窗口概念维持不住了，所以end跟着i一起走
				end = i + 1;
			}
		}
		return ans;
	}

	public static int maxLength(int[] arr, int k) {
		int[] h = new int[arr.length + 1];
		int sum = 0;
		h[0] = sum;
		for (int i = 0; i != arr.length; i++) {
			sum += arr[i];
			h[i + 1] = Math.max(sum, h[i]);
		}
		sum = 0;
		int res = 0;
		int pre = 0;
		int len = 0;
		for (int i = 0; i != arr.length; i++) {
			sum += arr[i];
			pre = getLessIndex(h, sum - k);
			len = pre == -1 ? 0 : i - pre + 1;
			res = Math.max(res, len);
		}
		return res;
	}

	public static int getLessIndex(int[] arr, int num) {
		int low = 0;
		int high = arr.length - 1;
		int mid = 0;
		int res = -1;
		while (low <= high) {
			mid = (low + high) / 2;
			if (arr[mid] >= num) {
				res = mid;
				high = mid - 1;
			} else {
				low = mid + 1;
			}
		}
		return res;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[] generateRandomArray(int len, int maxValue) {
		int[] res = new int[len];
		for (int i = 0; i != res.length; i++) {
			res[i] = (int) (Math.random() * maxValue) - (maxValue / 3);
		}
		return res;
	}

public static void main(String[] args) {
		System.out.println("test begin");
		for (int i = 0; i < 10000000; i++) {
			int[] arr = generateRandomArray(10, 20);
			int k = (int) (Math.random() * 20) - 5;
			if (maxLengthAwesome(arr, k) != maxLength(arr, k)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("test finish");
	}
```

#### 题解

**为什么正确**

想从右端继续扩张时，以该位置开始的最小和区块是最有利的扩张；若连它都使总和>K，则任何从同一位置延伸的前缀都无法合法。若可吸收，就一步跳过整块，不会错过更长答案。

**复杂度**

预处理与扫描O(N)时间，额外空间O(N)。

**边界与易错点**

负数可使扩张后和降低，普通正数窗口不适用。minSums必须与minSumEnds同步。右端未领先左端时，要直接推进右端而非减掉不存在的窗口元素。

### 40.4 平均值小于等于 V 的最长子数组

#### 题目

给定整数数组和阈值 `v`，返回平均值小于等于 `v` 的最长子数组长度。

**输入、输出与约束**

输入整数数组和整数阈值v；返回平均值≤v的最长连续区间长度。

**函数签名（课程入口）**

```java
public static int ways1(int[] arr, int v);
public static int ways2(int[] arr, int v);
public static int ways3(int[] arr, int v);
public static int maxLengthAwesome(int[] arr, int k);
```

**示例**

```text
输入：arr=[2,1,3], v=2
输出：3
```

解释：总和6、长度3，平均值恰好为2；变换为[0,-1,1]总和0。

**出处与版本差异**

- [课程源码：class40/Code04_AvgLessEqualValueLongestSubarray.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class40/Code04_AvgLessEqualValueLongestSubarray.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

平均值≤v等价于区间元素和≤v×长度。将每个元素减v后，就变成变换数组的区间和≤0，接着用最小和区块扩张法求最长长度。基准版本可枚举区间直接核对平均值条件。

#### 代码答案

```java
package class40;

import java.util.TreeMap;

public class Code04_AvgLessEqualValueLongestSubarray {

	// 暴力解，时间复杂度O(N^3)，用于做对数器
	public static int ways1(int[] arr, int v) {
		int ans = 0;
		for (int L = 0; L < arr.length; L++) {
			for (int R = L; R < arr.length; R++) {
				int sum = 0;
				int k = R - L + 1;
				for (int i = L; i <= R; i++) {
					sum += arr[i];
				}
				double avg = (double) sum / (double) k;
				if (avg <= v) {
					ans = Math.max(ans, k);
				}
			}
		}
		return ans;
	}

	// 想实现的解法2，时间复杂度O(N*logN)
	// 题目要求平均值<=v的最长子数组。
	// 那么我们可以转化一下，
	// 比如数组如下：7，4，3，9，6
	// v=5
	// 我们可以让每个数字减去5，得到如下转化数组：
	// 2，-1，-2，4，1
	// 原数组平均值<=5的最长子数组，就等同于：
	// 累加和<=0的最长子数组。
	// 想通这个，就好办了。
	// 然后就是在转化数组里求：
	// 子数组必须以i结尾的情况下，累加和<=0的最长子数组。
	// 代码中的sum，就是每一步从原始数组的值先转化，然后把转化后的值累加起来的前缀和。
	// 比如，转化数组
	// 2，-1，-2，4，1
	// sum依次为 : 2、1、-1、3、4
	// 那么，比如当你来到一个位置i，
	// sum就是0...i的转化前缀和，假设是100
	// 那么就找>=100且距离100最近的转化前缀和最早出现在哪。
	public static int ways2(int[] arr, int v) {
		if (arr == null || arr.length == 0) {
			return 0;
		}
		for (int i = 0; i < arr.length; i++) {
			// 把平均值比较转成区间和≤0，避免每个区间都做除法。
			arr[i] -= v;
		}
		TreeMap<Integer, Integer> sortedMap = new TreeMap<>();
		sortedMap.put(0, -1);
		int sum = 0;
		int len = 0;
		for (int i = 0; i < arr.length; i++) {
			sum += arr[i];
			Integer ceiling = sortedMap.ceilingKey(sum);
			if (ceiling != null) {
				len = Math.max(len, i - sortedMap.get(ceiling));
			} else {
				sortedMap.put(sum, i);
			}
		}
		return len;
	}

	// 想实现的解法3，时间复杂度O(N)
	public static int ways3(int[] arr, int v) {
		if (arr == null || arr.length == 0) {
			return 0;
		}
		for (int i = 0; i < arr.length; i++) {
			// 把平均值比较转成区间和≤0，避免每个区间都做除法。
			arr[i] -= v;
		}
		// 变换后直接求累加和不超过0的最长区间。
		return maxLengthAwesome(arr, 0);
	}

	// 找到数组中累加和<=k的最长子数组
	public static int maxLengthAwesome(int[] arr, int k) {
		int N = arr.length;
		int[] sums = new int[N];
		int[] ends = new int[N];
		sums[N - 1] = arr[N - 1];
		ends[N - 1] = N - 1;
		for (int i = N - 2; i >= 0; i--) {
			if (sums[i + 1] < 0) {
				sums[i] = arr[i] + sums[i + 1];
				ends[i] = ends[i + 1];
			} else {
				sums[i] = arr[i];
				ends[i] = i;
			}
		}
		int end = 0;
		int sum = 0;
		int res = 0;
		for (int i = 0; i < N; i++) {
			while (end < N && sum + sums[end] <= k) {
				sum += sums[end];
				end = ends[end] + 1;
			}
			res = Math.max(res, end - i);
			if (end > i) {
				sum -= arr[i];
			} else {
				end = i + 1;
			}
		}
		return res;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 用于测试
	public static int[] randomArray(int maxLen, int maxValue) {
		int len = (int) (Math.random() * maxLen) + 1;
		int[] ans = new int[len];
		for (int i = 0; i < len; i++) {
			ans[i] = (int) (Math.random() * maxValue);
		}
		return ans;
	}

	// 用于测试
	public static int[] copyArray(int[] arr) {
		int[] ans = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			ans[i] = arr[i];
		}
		return ans;
	}

	// 用于测试
	public static void printArray(int[] arr) {
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// 用于测试
	public static void main(String[] args) {
		System.out.println("测试开始");
		int maxLen = 20;
		int maxValue = 100;
		int testTime = 500000;
		for (int i = 0; i < testTime; i++) {
			int[] arr = randomArray(maxLen, maxValue);
			int value = (int) (Math.random() * maxValue);
			int[] arr1 = copyArray(arr);
			int[] arr2 = copyArray(arr);
			int[] arr3 = copyArray(arr);
			int ans1 = ways1(arr1, value);
			int ans2 = ways2(arr2, value);
			int ans3 = ways3(arr3, value);
			if (ans1 != ans2 || ans1 != ans3) {
				System.out.println("测试出错！");
				System.out.print("测试数组：");
				printArray(arr);
				System.out.println("子数组平均值不小于 ：" + value);
				System.out.println("方法1得到的最大长度：" + ans1);
				System.out.println("方法2得到的最大长度：" + ans2);
				System.out.println("方法3得到的最大长度：" + ans3);
				System.out.println("=========================");
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

对长度len的区间，Σ(arr[i]-v)=Σarr[i]-v·len，因此不等式完全等价，不需要浮点除法。目标长度在变换前后不变，所以最长合法区间也不变。

**复杂度**

最优版O(N)时间、O(N)辅助空间；枚举版可达O(N³)，有序表方案O(N log N)。

**边界与易错点**

源码减v会修改数组，对拍要复制。用整数变换避开浮点误差，但int减法及累加也可能溢出。

### 40.5 螺旋打印矩阵

#### 题目

给定二维矩阵，按从外到内的顺时针螺旋顺序输出所有元素。

**输入、输出与约束**

输入非空矩形矩阵；按顺时针从外到内打印所有值。

**函数签名（课程入口）**

```java
public static void spiralOrderPrint(int[][] matrix);
public static void printEdge(int[][] m, int tR, int tC, int dR, int dC);
```

**示例**

```text
输入：matrix=[[1,2,3],[4,5,6]]
输出：1、2、3、6、5、4
```

解释：外圈一次输出完，内层为空。

**出处与版本差异**

- [课程源码：class40/Code05_PrintMatrixSpiralOrder.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class40/Code05_PrintMatrixSpiralOrder.java)。
- [LeetCode 原题 54. 螺旋矩阵（Spiral Matrix）](https://leetcode.com/problems/spiral-matrix/)

#### 思路

用左上角(tR,tC)与右下角(dR,dC)圈定当前外层。若只剩一行或一列，单独输出；否则按上、右、下、左四边依次走，并让每个角只由一条边负责。输出后四个边界各缩进一格。

#### 代码答案

```java
package class40;

public class Code05_PrintMatrixSpiralOrder {

	public static void spiralOrderPrint(int[][] matrix) {
		int tR = 0;
		int tC = 0;
		int dR = matrix.length - 1;
		int dC = matrix[0].length - 1;
		while (tR <= dR && tC <= dC) {
			// 当前外圈输出后，边界同时向内收缩。
			printEdge(matrix, tR++, tC++, dR--, dC--);
		}
	}

	public static void printEdge(int[][] m, int tR, int tC, int dR, int dC) {
		// 剩余区域只有一行，按列递增打印一次。
		if (tR == dR) {
			for (int i = tC; i <= dC; i++) {
				System.out.print(m[tR][i] + " ");
			}
		// 剩余区域只有一列，按行递增打印一次。
		} else if (tC == dC) {
			for (int i = tR; i <= dR; i++) {
				System.out.print(m[i][tC] + " ");
			}
		} else {
			int curC = tC;
			int curR = tR;
			while (curC != dC) {
				System.out.print(m[tR][curC] + " ");
				curC++;
			}
			while (curR != dR) {
				System.out.print(m[curR][dC] + " ");
				curR++;
			}
			while (curC != tC) {
				System.out.print(m[dR][curC] + " ");
				curC--;
			}
			while (curR != tR) {
				System.out.print(m[curR][tC] + " ");
				curR--;
			}
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int[][] matrix = { { 1, 2, 3, 4 }, { 5, 6, 7, 8 }, { 9, 10, 11, 12 },
				{ 13, 14, 15, 16 } };
		spiralOrderPrint(matrix);

	}
```

#### 题解

**为什么正确**

每轮输出矩形外边框，剩余未输出部分正好是缩小后的内矩形；各轮不相交且最终覆盖全部格子。退化行列的单独分支防止把同一条边往返打印两次。

**复杂度**

时间O(RC)，除输出外额外空间O(1)。

**边界与易错点**

四边端点的包含规则要一致；单行单列不能走一般四边逻辑。源码打印元素，不返回列表。

### 40.6 原地顺时针旋转矩阵

#### 题目

给定 `N × N` 方阵，在原矩阵上把图像顺时针旋转 90 度。

**输入、输出与约束**

输入N×N整数方阵，原地顺时针旋转90度。

**函数签名（课程入口）**

```java
public static void rotate(int[][] matrix);
public static void rotateEdge(int[][] m, int a, int b, int c, int d);
public static void printMatrix(int[][] matrix);
```

**示例**

```text
输入：matrix=[[1,2],[3,4]]
输出：[[3,1],[4,2]]
```

解释：旧左下3变成新左上，旧左上1变成新右上。

**出处与版本差异**

- [课程源码：class40/Code06_RotateMatrix.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class40/Code06_RotateMatrix.java)。
- [LeetCode 原题 48. 旋转图像（Rotate Image）](https://leetcode.com/problems/rotate-image/)

#### 思路

方阵逐圈处理，每圈一次选择四个对应位置：上边、右边、下边、左边。用临时变量保存上边值，再让左→上、下→左、右→下、旧上→右，完成顺时针四元循环。

#### 代码答案

```java
package class40;

public class Code06_RotateMatrix {

	public static void rotate(int[][] matrix) {
		int a = 0;
		int b = 0;
		int c = matrix.length - 1;
		int d = matrix[0].length - 1;
		while (a < c) {
			rotateEdge(matrix, a++, b++, c--, d--);
		}
	}

	public static void rotateEdge(int[][] m, int a, int b, int c, int d) {
		int tmp = 0;
		for (int i = 0; i < d - b; i++) {
			tmp = m[a][b + i];
			m[a][b + i] = m[c - i][b];
			m[c - i][b] = m[c][d - i];
			m[c][d - i] = m[a + i][d];
			// 最后把原上边值放到右边，闭合四位置循环。
			m[a + i][d] = tmp;
		}
	}

	public static void printMatrix(int[][] matrix) {
		for (int i = 0; i != matrix.length; i++) {
			for (int j = 0; j != matrix[0].length; j++) {
				System.out.print(matrix[i][j] + " ");
			}
			System.out.println();
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int[][] matrix = { { 1, 2, 3, 4 }, { 5, 6, 7, 8 }, { 9, 10, 11, 12 }, { 13, 14, 15, 16 } };
		printMatrix(matrix);
		rotate(matrix);
		System.out.println("=========");
		printMatrix(matrix);

	}
```

#### 题解

**为什么正确**

顺时针旋转坐标映射为(r,c)→(c,N-1-r)，反复四次回到原位。每个四元组恰属于一圈，圈内只枚举边长减一组，避免角点重复处理。

**复杂度**

时间O(N²)，额外空间O(1)。

**边界与易错点**

必须是方阵；普通R×C矩阵旋转后尺寸交换，不能在相同二维布局中直接套用。循环次数为边长减一，不能把四个角重复转动。

### 40.7 之字形打印矩阵

#### 题目

给定二维矩阵，沿对角线交替改变方向输出全部元素。

**输入、输出与约束**

输入非空矩形矩阵；按课程之字形方向依次打印各对角线。

**函数签名（课程入口）**

```java
public static void printMatrixZigZag(int[][] matrix);
public static void printLevel(int[][] m, int tR, int tC, int dR, int dC, boolean f);
```

**示例**

```text
输入：matrix=[[1,2,3],[4,5,6]]
输出：1、2、4、5、3、6
```

解释：对角线分别为[1]、[2,4]、[3,5]、[6]，交替反向。

**出处与版本差异**

- [课程源码：class40/Code07_ZigZagPrintMatrix.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class40/Code07_ZigZagPrintMatrix.java)。
- [LeetCode 对应题 498. 对角线遍历（Diagonal Traverse）](https://leetcode.com/problems/diagonal-traverse/)

#### 思路

维护同一条对角线的两个端点A、B。A先沿上边向右，触右边后向下；B先沿左边向下，触底边后向右。每次沿A、B之间的对角线打印，并翻转方向。

#### 代码答案

```java
package class40;

public class Code07_ZigZagPrintMatrix {

	public static void printMatrixZigZag(int[][] matrix) {
		int tR = 0;
		int tC = 0;
		int dR = 0;
		int dC = 0;
		int endR = matrix.length - 1;
		int endC = matrix[0].length - 1;
		boolean fromUp = false;
		while (tR != endR + 1) {
			// 按两个端点之间的同一行列和对角线输出。
			printLevel(matrix, tR, tC, dR, dC, fromUp);
			tR = tC == endC ? tR + 1 : tR;
			tC = tC == endC ? tC : tC + 1;
			dC = dR == endR ? dC + 1 : dC;
			dR = dR == endR ? dR : dR + 1;
			// 完成一条对角线后翻转方向，形成上下交替的之字形。
			fromUp = !fromUp;
		}
		System.out.println();
	}

	public static void printLevel(int[][] m, int tR, int tC, int dR, int dC, boolean f) {
		if (f) {
			while (tR != dR + 1) {
				System.out.print(m[tR++][tC--] + " ");
			}
		} else {
			while (dR != tR - 1) {
				System.out.print(m[dR--][dC++] + " ");
			}
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int[][] matrix = { { 1, 2, 3, 4 }, { 5, 6, 7, 8 }, { 9, 10, 11, 12 } };
		printMatrixZigZag(matrix);

	}
```

#### 题解

**为什么正确**

同一对角线上行列和相同，端点移动后该和值加一，依次覆盖所有对角线。每条对角线内部沿行列反向变化访问，端点包含一次，因此全部格子恰好输出一次。

**复杂度**

时间O(RC)，额外空间O(1)。

**边界与易错点**

更新端点时先后顺序不能让新行值影响旧边界判断。遍历次序以课程初始方向为准，不能随意等同任意对角线题的顺序。

### 40.8 打印嵌套星号图案

#### 题目

给定正整数 N，打印课程规定的 N×N 星号折线图案：每层由上边、右边、部分下边及内缩的左边组成，相邻层留一格空白。具体开口规则见 set 方法。

**输入、输出与约束**

输入N≥1；按课程set函数定义打印带间隔和缺口的嵌套星号图案。

**函数签名（课程入口）**

```java
public static void printStar(int N);
public static void set(char[][] m, int leftUp, int rightDown);
```

**示例**

```text
输入：N=3
输出：网格行依次为 "***"、"  *"、" **"
```

解释：实际控制台还在每个网格字符后打印一个空格。

**出处与版本差异**

- [课程源码：class40/Code08_PrintStar.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class40/Code08_PrintStar.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先分配N×N空格网格。每层按上边、右边、底边的一段、左侧内缩一列的一段画星号，再将边界各缩进2格，形成带缺口的嵌套折线。最后按行打印。

#### 代码答案

```java
package class40;

public class Code08_PrintStar {

	public static void printStar(int N) {
		int leftUp = 0;
		int rightDown = N - 1;
		char[][] m = new char[N][N];
		for (int i = 0; i < N; i++) {
			for (int j = 0; j < N; j++) {
				m[i][j] = ' ';
			}
		}
		while (leftUp <= rightDown) {
			set(m, leftUp, rightDown);
			// 向内跳两格，在相邻折线之间保留一格空白。
			leftUp += 2;
			rightDown -= 2;
		}
		for (int i = 0; i < N; i++) {
			for (int j = 0; j < N; j++) {
				System.out.print(m[i][j] + " ");
			}
			System.out.println();
		}
	}

	public static void set(char[][] m, int leftUp, int rightDown) {
		for (int col = leftUp; col <= rightDown; col++) {
			m[leftUp][col] = '*';
		}
		for (int row = leftUp + 1; row <= rightDown; row++) {
			m[row][rightDown] = '*';
		}
		for (int col = rightDown - 1; col > leftUp; col--) {
			m[rightDown][col] = '*';
		}
		for (int row = rightDown - 1; row > leftUp + 1; row--) {
			// 左侧回折边故意内缩一列，形成开口结构而非闭方框。
			m[row][leftUp + 1] = '*';
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		printStar(5);
	}
```

#### 题解

**为什么正确**

每一层只写当前边界规定的位置，边界缩进2留出空白间隔。四条边有意采用不同终点，构成课程图案的缺口与转折，而不是完整同心方框。

**复杂度**

初始化与输出O(N²)，网格空间O(N²)。

**边界与易错点**

原题描述“奇数规模同心方框”不准确，代码也能接受正偶数N；图案不是每圈四条完整闭边。保留空格才能观察正确形状。


<a id="course-41"></a>

## 第 41 课：四边形不等式优化（一）

### 41.1 数组整体的最优分割

#### 题目

给定非负数组，在某个位置把它分成非空左右两部分，最大化两部分累加和的较小值。

**输入、输出与约束**

输入非负数组，切为两段非空连续区间；返回较小区间和的最大值，长度<2返回0。

**函数签名（课程入口）**

```java
public static int bestSplit1(int[] arr);
public static int bestSplit2(int[] arr);
```

**示例**

```text
输入：arr=[1,2,3,4]
输出：4
```

解释：切在3之后，左右和6、4，较小为4。

**出处与版本差异**

- [课程源码：class41/Code01_BestSplitForAll.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class41/Code01_BestSplitForAll.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先求整个数组总和S，再从左到右枚举切点，累计左和L，右和直接为S-L。每个切点的得分是min(L,S-L)，取最大值。与双重循环基准相比，扫描复用了相邻切点的左和。

#### 代码答案

```java
package class41;

public class Code01_BestSplitForAll {

	public static int bestSplit1(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		int N = arr.length;
		int ans = 0;
		for (int s = 0; s < N - 1; s++) {
			int sumL = 0;
			for (int L = 0; L <= s; L++) {
				sumL += arr[L];
			}
			int sumR = 0;
			for (int R = s + 1; R < N; R++) {
				sumR += arr[R];
			}
			ans = Math.max(ans, Math.min(sumL, sumR));
		}
		return ans;
	}

	public static int bestSplit2(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		int N = arr.length;
		int sumAll = 0;
		for (int num : arr) {
			sumAll += num;
		}
		int ans = 0;
		int sumL = 0;
		// [0...s]  [s+1...N-1]
		for (int s = 0; s < N - 1; s++) {
			// 切点右移一格，只需把新进入左段的元素加入前缀和。
			sumL += arr[s];
			// 总和减左和直接得到右和，无需重新扫描。
			int sumR = sumAll - sumL;
			ans = Math.max(ans, Math.min(sumL, sumR));
		}
		return ans;
	}

	public static int[] randomArray(int len, int max) {
		int[] ans = new int[len];
		for (int i = 0; i < len; i++) {
			ans[i] = (int) (Math.random() * max);
		}
		return ans;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int N = 20;
		int max = 30;
		int testTime = 1000000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int len = (int) (Math.random() * N);
			int[] arr = randomArray(len, max);
			int ans1 = bestSplit1(arr);
			int ans2 = bestSplit2(arr);
			if (ans1 != ans2) {
				System.out.println(ans1);
				System.out.println(ans2);
				System.out.println("Oops!");
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

合法切点位于相邻元素之间，扫描逐个覆盖全部N-1个切点；两段和精确由前缀与总和之差获得，所以逐点最大化不会遗漏更好切分。

**复杂度**

最优O(N)时间、O(1)空间；基准逐切点重算O(N²)。

**边界与易错点**

两段必须非空，所以切点不能放在最后元素之后。返回较小段和的最大值，不是左右差或切点下标。

### 41.2 每个前缀的最优分割

#### 题目

对数组的每个前缀，分别计算把该前缀切成两部分时两部分较小累加和的最大值。

**输入、输出与约束**

输入非负数组；返回每个前缀的最佳两段较小和。

**函数签名（课程入口）**

```java
public static int[] bestSplit1(int[] arr);
public static int sum(int[] sum, int L, int R);
public static int[] bestSplit2(int[] arr);
public static int[] bestSplit3(int[] arr);
public static boolean isSameArray(int[] arr1, int[] arr2);
```

**示例**

```text
输入：arr=[1,2,3,4]
输出：[0,1,3,4]
```

解释：长度3前缀可分[1,2]与[3]；长度4最优较小和为4。

**出处与版本差异**

- [课程源码：class41/Code02_BestSplitForEveryPosition.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class41/Code02_BestSplitForEveryPosition.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

对每个前缀[0,R]求最佳切点。前缀和支持O(1)比较切点得分，best保存上个前缀的最佳位置；R增加后，只要best+1得分不小于best就向右移动。

#### 代码答案

```java
package class41;

public class Code02_BestSplitForEveryPosition {

	public static int[] bestSplit1(int[] arr) {
		if (arr == null || arr.length == 0) {
			return new int[0];
		}
		int N = arr.length;
		int[] ans = new int[N];
		ans[0] = 0;
		for (int range = 1; range < N; range++) {
			for (int s = 0; s < range; s++) {
				int sumL = 0;
				for (int L = 0; L <= s; L++) {
					sumL += arr[L];
				}
				int sumR = 0;
				for (int R = s + 1; R <= range; R++) {
					sumR += arr[R];
				}
				ans[range] = Math.max(ans[range], Math.min(sumL, sumR));
			}
		}
		return ans;
	}

	// 求原来的数组arr中，arr[L...R]的累加和
	public static int sum(int[] sum, int L, int R) {
		return sum[R + 1] - sum[L];
	}

	public static int[] bestSplit2(int[] arr) {
		if (arr == null || arr.length == 0) {
			return new int[0];
		}
		int N = arr.length;
		int[] ans = new int[N];
		ans[0] = 0;
		int[] sum = new int[N + 1];
		for (int i = 0; i < N; i++) {
			sum[i + 1] = sum[i] + arr[i];
		}
		for (int range = 1; range < N; range++) {
			for (int s = 0; s < range; s++) {
				int sumL = sum(sum, 0, s);
				int sumR = sum(sum, s + 1, range);
				ans[range] = Math.max(ans[range], Math.min(sumL, sumR));
			}
		}
		return ans;
	}

	public static int[] bestSplit3(int[] arr) {
		if (arr == null || arr.length == 0) {
			return new int[0];
		}
		int N = arr.length;
		int[] ans = new int[N];
		ans[0] = 0;
		// arr =   {5, 3, 1, 3}
		//          0  1  2  3
		// sum ={0, 5, 8, 9, 12}
		//       0  1  2  3   4
		// 0~2 ->  sum[3] - sum[0]
		// 1~3 ->  sum[4] - sum[1]
		int[] sum = new int[N + 1];
		for (int i = 0; i < N; i++) {
			sum[i + 1] = sum[i] + arr[i];
		}
		// 最优划分
		// 0~range-1上，最优划分是左部分[0~best]  右部分[best+1~range-1]
		int best = 0;
		for (int range = 1; range < N; range++) {
			while (best + 1 < range) {
				int before = Math.min(sum(sum, 0, best), sum(sum, best + 1, range));
				int after = Math.min(sum(sum, 0, best + 1), sum(sum, best + 2, range));
				// 注意，一定要是>=，只是>会出错
				// 课上会讲解
				// 并列最优时也向右移动，跨过零值平台保留最右最佳切点。
				if (after >= before) {
					// 最优切点随前缀延长只向右，总移动次数不超过N。
					best++;
				} else {
					break;
				}
			}
			ans[range] = Math.min(sum(sum, 0, best), sum(sum, best + 1, range));
		}
		return ans;
	}

	public static int[] randomArray(int len, int max) {
		int[] ans = new int[len];
		for (int i = 0; i < len; i++) {
			ans[i] = (int) (Math.random() * max);
		}
		return ans;
	}

	public static boolean isSameArray(int[] arr1, int[] arr2) {
		if (arr1.length != arr2.length) {
			return false;
		}
		int N = arr1.length;
		for (int i = 0; i < N; i++) {
			if (arr1[i] != arr2[i]) {
				return false;
			}
		}
		return true;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int N = 20;
		int max = 30;
		int testTime = 1000000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int len = (int) (Math.random() * N);
			int[] arr = randomArray(len, max);
			int[] ans1 = bestSplit1(arr);
			int[] ans2 = bestSplit2(arr);
			int[] ans3 = bestSplit3(arr);
			if (!isSameArray(ans1, ans2) || !isSameArray(ans1, ans3)) {
				System.out.println("Oops!");
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

元素非负时，切点右移使左和不减、右和不增，min值先升后降。延长前缀只给右侧增加非负量，最优平衡切点不会向左移动；选择最右并列最优点可保持单调推进。

**复杂度**

预处理与总指针移动O(N)时间，前缀与输出空间O(N)。

**边界与易错点**

比较要允许相等时向右走，零值可能形成平台。负数会破坏切点单调性。第一个前缀无法切成两段，答案0。

### 41.3 合并石子的最小代价

#### 题目

相邻石堆每次可合并为一堆，代价为两堆石子总数，返回合并成一堆的最小总代价。

**输入、输出与约束**

输入非负石堆重量，合并相邻两堆代价为总重量；返回合成一堆最小总代价。

**函数签名（课程入口）**

```java
public static int[] sum(int[] arr);
public static int w(int[] s, int l, int r);
public static int min1(int[] arr);
public static int min2(int[] arr);
public static int min3(int[] arr);
```

**示例**

```text
输入：arr=[3,2,4]
输出：14
```

解释：先合并3、2花5，再与4合并花9，总14。

**出处与版本差异**

- [课程源码：class41/Code03_StoneMerge.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class41/Code03_StoneMerge.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

dp[L][R]表示把相邻石堆L..R合成一堆的最小代价。最后一次合并必把某个切点两侧的两堆合并，因此枚举M，取dp[L][M]+dp[M+1][R]最小，再加原区间总石量。非负重量下可用最优切点的四边形不等式单调性缩小枚举。

#### 代码答案

```java
package class41;

// 四边形不等式：合并石子问题
public class Code03_StoneMerge {

	public static int[] sum(int[] arr) {
		int N = arr.length;
		int[] s = new int[N + 1];
		s[0] = 0;
		for (int i = 0; i < N; i++) {
			s[i + 1] = s[i] + arr[i];
		}
		return s;
	}

	public static int w(int[] s, int l, int r) {
		return s[r + 1] - s[l];
	}

	public static int min1(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		int N = arr.length;
		int[] s = sum(arr);
		return process1(0, N - 1, s);
	}

	public static int process1(int L, int R, int[] s) {
		if (L == R) {
			return 0;
		}
		int next = Integer.MAX_VALUE;
		for (int leftEnd = L; leftEnd < R; leftEnd++) {
			next = Math.min(next, process1(L, leftEnd, s) + process1(leftEnd + 1, R, s));
		}
		return next + w(s, L, R);
	}

	public static int min2(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		int N = arr.length;
		int[] s = sum(arr);
		int[][] dp = new int[N][N];
		// dp[i][i] = 0
		for (int L = N - 2; L >= 0; L--) {
			for (int R = L + 1; R < N; R++) {
				int next = Integer.MAX_VALUE;
				// dp(L..leftEnd)  + dp[leftEnd+1...R]  + 累加和[L...R]
				for (int leftEnd = L; leftEnd < R; leftEnd++) {
					next = Math.min(next, dp[L][leftEnd] + dp[leftEnd + 1][R]);
				}
				// 先取左右内部最优成本，再支付最后合并的整段重量。
				dp[L][R] = next + w(s, L, R);
			}
		}
		return dp[0][N - 1];
	}

	public static int min3(int[] arr) {
		if (arr == null || arr.length < 2) {
			return 0;
		}
		int N = arr.length;
		int[] s = sum(arr);
		int[][] dp = new int[N][N];
		int[][] best = new int[N][N];
		for (int i = 0; i < N - 1; i++) {
			best[i][i + 1] = i;
			dp[i][i + 1] = w(s, i, i + 1);
		}
		for (int L = N - 3; L >= 0; L--) {
			for (int R = L + 2; R < N; R++) {
				int next = Integer.MAX_VALUE;
				int choose = -1;
				// 较短右端区间的最优切点提供当前枚举下界。
				for (int leftEnd = best[L][R - 1]; leftEnd <= best[L + 1][R]; leftEnd++) {
					int cur = dp[L][leftEnd] + dp[leftEnd + 1][R];
					if (cur <= next) {
						next = cur;
						choose = leftEnd;
					}
				}
				best[L][R] = choose;
				// 先取左右内部最优成本，再支付最后合并的整段重量。
				dp[L][R] = next + w(s, L, R);
			}
		}
		return dp[0][N - 1];
	}

	public static int[] randomArray(int len, int maxValue) {
		int[] arr = new int[len];
		for (int i = 0; i < len; i++) {
			arr[i] = (int) (Math.random() * maxValue);
		}
		return arr;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int N = 15;
		int maxValue = 100;
		int testTime = 1000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int len = (int) (Math.random() * N);
			int[] arr = randomArray(len, maxValue);
			int ans1 = min1(arr);
			int ans2 = min2(arr);
			int ans3 = min3(arr);
			if (ans1 != ans2 || ans1 != ans3) {
				System.out.println("Oops!");
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

最后一刀之前左右区间各自必须已经最优合并，否则替换其内部方案就能改善总方案。所有最后切点覆盖全部合并树。非负区间和代价满足所需单调与四边形条件，最佳M落在best[L][R-1]与best[L+1][R]之间。

**复杂度**

普通区间表O(N³)时间、O(N²)空间；决策单调优化O(N²)时间与空间。

**边界与易错点**

只能合并相邻堆，不能直接用最短两堆的哈夫曼贪心。区间石量每层合并都会支付，需加在当前转移上。负重量下不能直接承诺优化仍正确。

### 41.4 分割数组后最小化最大分组和

#### 题目

把非负整数数组分成 `k` 个连续非空子数组，最小化各子数组累加和中的最大值。

**输入、输出与约束**

输入非负数组，分成恰k个非空连续组，返回最大组和的最小可能值。

**函数签名（课程入口）**

```java
public static int sum(int[] sum, int L, int R);
public static int splitArray1(int[] nums, int K);
public static int splitArray2(int[] nums, int K);
public static int splitArray3(int[] nums, int M);
public static int getNeedParts(int[] arr, long aim);
```

**示例**

```text
输入：nums=[7,2,5,10,8], k=2
输出：18
```

解释：分为[7,2,5]与[10,8]。

**出处与版本差异**

- [课程源码：class41/Code04_SplitArrayLargestSum.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class41/Code04_SplitArrayLargestSum.java)。
- [LeetCode 原题 410. 分割数组的最大值（Split Array Largest Sum）](https://leetcode.com/problems/split-array-largest-sum/)

#### 思路

动态规划dp[i][j]把前i项分成j组，枚举最后一组起点，取max(前面最优最大组和,最后一组和)的最小值。另一方案二分允许的最大组和limit，并贪心计算在该上限下至少需要几组；组数≤k说明上限可行。

#### 代码答案

```java
package class41;

// leetcode原题
// 测试链接：https://leetcode.com/problems/split-array-largest-sum/
public class Code04_SplitArrayLargestSum {

	// 求原数组arr[L...R]的累加和
	public static int sum(int[] sum, int L, int R) {
		return sum[R + 1] - sum[L];
	}

	// 不优化枚举的动态规划方法，O(N^2 * K)
	public static int splitArray1(int[] nums, int K) {
		int N = nums.length;
		int[] sum = new int[N + 1];
		for (int i = 0; i < N; i++) {
			sum[i + 1] = sum[i] + nums[i];
		}
		int[][] dp = new int[N][K + 1];
		for (int j = 1; j <= K; j++) {
			dp[0][j] = nums[0];
		}
		for (int i = 1; i < N; i++) {
			dp[i][1] = sum(sum, 0, i);
		}
		// 每一行从上往下
		// 每一列从左往右
		// 根本不去凑优化位置对儿！
		for (int i = 1; i < N; i++) {
			for (int j = 2; j <= K; j++) {
				int ans = Integer.MAX_VALUE;
				// 枚举是完全不优化的！
				for (int leftEnd = 0; leftEnd <= i; leftEnd++) {
					int leftCost = leftEnd == -1 ? 0 : dp[leftEnd][j - 1];
					int rightCost = leftEnd == i ? 0 : sum(sum, leftEnd + 1, i);
					int cur = Math.max(leftCost, rightCost);
					if (cur < ans) {
						ans = cur;
					}
				}
				dp[i][j] = ans;
			}
		}
		return dp[N - 1][K];
	}

	// 课上现场写的方法，用了枚举优化，O(N * K)
	public static int splitArray2(int[] nums, int K) {
		int N = nums.length;
		int[] sum = new int[N + 1];
		for (int i = 0; i < N; i++) {
			sum[i + 1] = sum[i] + nums[i];
		}
		int[][] dp = new int[N][K + 1];
		int[][] best = new int[N][K + 1];
		for (int j = 1; j <= K; j++) {
			dp[0][j] = nums[0];
			best[0][j] = -1;
		}
		for (int i = 1; i < N; i++) {
			dp[i][1] = sum(sum, 0, i);
			best[i][1] = -1;
		}
		// 从第2列开始，从左往右
		// 每一列，从下往上
		// 为什么这样的顺序？因为要去凑（左，下）优化位置对儿！
		for (int j = 2; j <= K; j++) {
			for (int i = N - 1; i >= 1; i--) {
				int down = best[i][j - 1];
				// 如果i==N-1，则不优化上限
				int up = i == N - 1 ? N - 1 : best[i + 1][j];
				int ans = Integer.MAX_VALUE;
				int bestChoose = -1;
				for (int leftEnd = down; leftEnd <= up; leftEnd++) {
					int leftCost = leftEnd == -1 ? 0 : dp[leftEnd][j - 1];
					int rightCost = leftEnd == i ? 0 : sum(sum, leftEnd + 1, i);
					int cur = Math.max(leftCost, rightCost);
					// 注意下面的if一定是 < 课上的错误就是此处！当时写的 <= ！
					// 也就是说，只有取得明显的好处才移动！
					// 举个例子来说明，比如[2,6,4,4]，3个画匠时候，如下两种方案都是最优:
					// (2,6) (4) 两个画匠负责 | (4) 最后一个画匠负责
					// (2,6) (4,4)两个画匠负责 | 最后一个画匠什么也不负责
					// 第一种方案划分为，[0~2] [3~3]
					// 第二种方案划分为，[0~3] [无]
					// 两种方案的答案都是8，但是划分点位置一定不要移动!
					// 只有明显取得好处时(<)，划分点位置才移动!
					// 也就是说后面的方案如果==前面的最优，不要移动！只有优于前面的最优，才移动
					// 比如上面的两个方案，如果你移动到了方案二，你会得到:
					// [2,6,4,4] 三个画匠时，最优为[0~3](前两个画家) [无](最后一个画家)，
					// 最优划分点为3位置(best[3][3])
					// 那么当4个画匠时，也就是求解dp[3][4]时
					// 因为best[3][3] = 3，这个值提供了dp[3][4]的下限
					// 而事实上dp[3][4]的最优划分为:
					// [0~2]（三个画家处理） [3~3] (一个画家处理)，此时最优解为6
					// 所以，你就得不到dp[3][4]的最优解了，因为划分点已经越过2了
					// 提供了对数器验证，你可以改成<=，对数器和leetcode都过不了
					// 这里是<，对数器和leetcode都能通过
					// 这里面会让同学们感到困惑的点：
					// 为啥==的时候，不移动，只有<的时候，才移动呢？例子懂了，但是道理何在？
					// 哈哈哈哈哈，看了邮局选址问题，你更懵，请看42节！
					if (cur < ans) {
						ans = cur;
						bestChoose = leftEnd;
					}
				}
				dp[i][j] = ans;
				best[i][j] = bestChoose;
			}
		}
		return dp[N - 1][K];
	}

	public static int splitArray3(int[] nums, int M) {
		long sum = 0;
		for (int i = 0; i < nums.length; i++) {
			sum += nums[i];
		}
		long l = 0;
		long r = sum;
		long ans = 0;
		while (l <= r) {
			long mid = (l + r) / 2;
			long cur = getNeedParts(nums, mid);
			// 限制mid下最少所需分组数不超过M，说明该上限可行，可继续压低。
			if (cur <= M) {
				ans = mid;
				r = mid - 1;
			} else {
				l = mid + 1;
			}
		}
		return (int) ans;
	}

	public static int getNeedParts(int[] arr, long aim) {
		for (int i = 0; i < arr.length; i++) {
			if (arr[i] > aim) {
				// 单个数已经超上限，任何分组都不可行。
				return Integer.MAX_VALUE;
			}
		}
		int parts = 1;
		int all = arr[0];
		for (int i = 1; i < arr.length; i++) {
			// 当前段再放入这个数就超上限，必须在它之前开启新段。
			if (all + arr[i] > aim) {
				parts++;
				all = arr[i];
			} else {
				all += arr[i];
			}
		}
		return parts;
	}

	public static int[] randomArray(int len, int maxValue) {
		int[] arr = new int[len];
		for (int i = 0; i < len; i++) {
			arr[i] = (int) (Math.random() * maxValue);
		}
		return arr;
	}

	public static void printArray(int[] arr) {
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int N = 100;
		int maxValue = 100;
		int testTime = 10000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int len = (int) (Math.random() * N) + 1;
			int M = (int) (Math.random() * N) + 1;
			int[] arr = randomArray(len, maxValue);
			int ans1 = splitArray1(arr, M);
			int ans2 = splitArray2(arr, M);
			int ans3 = splitArray3(arr, M);
			if (ans1 != ans2 || ans1 != ans3) {
				System.out.print("arr : ");
				printArray(arr);
				System.out.println("M : " + M);
				System.out.println("ans1 : " + ans1);
				System.out.println("ans2 : " + ans2);
				System.out.println("ans3 : " + ans3);
				System.out.println("Oops!");
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

元素非负时，limit越大，所需最少组数不会增加，可二分可行性边界。贪心尽量填满当前组使后续剩余更少，不会比提前断组需要更多组。最少组数≤k时，可拆分到恰k个非空组且不增加最大和。

**复杂度**

普通DP O(N²k)，决策优化版本更快但依赖非负条件；二分版O(N log S)时间、O(1)辅助空间，S为总和。

**边界与易错点**

1≤k≤N。二分下界可取最大单项，上界取总和并用long。负数时贪心最少组数和拆组可行性证明均不成立。


<a id="course-42"></a>

## 第 42 课：四边形不等式优化（二）

### 42.1 邮局选址问题

#### 题目

居民点位于一条数轴上，请建立指定数量的邮局，最小化每个居民到最近邮局的距离总和。

**输入、输出与约束**

输入非递减居民坐标和正邮局数；返回到最近邮局的绝对距离总和最小值。

**函数签名（课程入口）**

```java
public static int min1(int[] arr, int num);
public static int min2(int[] arr, int num);
```

**示例**

```text
输入：arr=[1,4,8], num=1
输出：7
```

解释：邮局放在中位数4，距离3+0+4=7。

**出处与版本差异**

- [课程源码：class42/Code01_PostOfficeProblem.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class42/Code01_PostOfficeProblem.java)。
- [LeetCode 对应题 1478. 安排邮筒（Allocate Mailboxes）](https://leetcode.com/problems/allocate-mailboxes/)

#### 思路

居民坐标有序时，一间邮局服务连续居民的最优位置在中位数。预处理w[L][R]为一间邮局服务该段的最小总距离，再按最后一间邮局服务的连续后缀做分组动态规划。优化版使用最优分割点单调性缩小搜索。

#### 代码答案

```java
package class42;

import java.util.Arrays;

public class Code01_PostOfficeProblem {

	public static int min1(int[] arr, int num) {
		if (arr == null || num < 1 || arr.length < num) {
			return 0;
		}
		int N = arr.length;
		int[][] w = new int[N + 1][N + 1];
		for (int L = 0; L < N; L++) {
			for (int R = L + 1; R < N; R++) {
				// 新增右端居民时，中位数递推可常数时间更新单邮局服务成本。
				w[L][R] = w[L][R - 1] + arr[R] - arr[(L + R) >> 1];
			}
		}
		int[][] dp = new int[N][num + 1];
		for (int i = 0; i < N; i++) {
			dp[i][1] = w[0][i];
		}
		for (int i = 1; i < N; i++) {
			for (int j = 2; j <= Math.min(i, num); j++) {
				int ans = Integer.MAX_VALUE;
				for (int k = 0; k <= i; k++) {
					ans = Math.min(ans, dp[k][j - 1] + w[k + 1][i]);
				}
				dp[i][j] = ans;
			}
		}
		return dp[N - 1][num];
	}

	public static int min2(int[] arr, int num) {
		if (arr == null || num < 1 || arr.length < num) {
			return 0;
		}
		int N = arr.length;
		int[][] w = new int[N + 1][N + 1];
		for (int L = 0; L < N; L++) {
			for (int R = L + 1; R < N; R++) {
				// 新增右端居民时，中位数递推可常数时间更新单邮局服务成本。
				w[L][R] = w[L][R - 1] + arr[R] - arr[(L + R) >> 1];
			}
		}
		int[][] dp = new int[N][num + 1];
		int[][] best = new int[N][num + 1];
		for (int i = 0; i < N; i++) {
			dp[i][1] = w[0][i];
			best[i][1] = -1;
		}
		for (int j = 2; j <= num; j++) {
			for (int i = N - 1; i >= j; i--) {
				int down = best[i][j - 1];
				int up = i == N - 1 ? N - 1 : best[i + 1][j];
				int ans = Integer.MAX_VALUE;
				int bestChoose = -1;
				for (int leftEnd = down; leftEnd <= up; leftEnd++) {
					int leftCost = leftEnd == -1 ? 0 : dp[leftEnd][j - 1];
					int rightCost = leftEnd == i ? 0 : w[leftEnd + 1][i];
					int cur = leftCost + rightCost;
					if (cur <= ans) {
						ans = cur;
						bestChoose = leftEnd;
					}
				}
				dp[i][j] = ans;
				best[i][j] = bestChoose;
			}
		}
		return dp[N - 1][num];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[] randomSortedArray(int len, int range) {
		int[] arr = new int[len];
		for (int i = 0; i != len; i++) {
			arr[i] = (int) (Math.random() * range);
		}
		Arrays.sort(arr);
		return arr;
	}

	// for test
	public static void printArray(int[] arr) {
		for (int i = 0; i != arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

	// for test
	public static void main(String[] args) {
		int N = 30;
		int maxValue = 100;
		int testTime = 10000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int len = (int) (Math.random() * N) + 1;
			int[] arr = randomSortedArray(len, maxValue);
			int num = (int) (Math.random() * N) + 1;
			int ans1 = min1(arr, num);
			int ans2 = min2(arr, num);
			if (ans1 != ans2) {
				printArray(arr);
				System.out.println(num);
				System.out.println(ans1);
				System.out.println(ans2);
				System.out.println("Oops!");
			}
		}
		System.out.println("测试结束");

	}
```

#### 题解

**为什么正确**

绝对距离和在中位数处最小，左右人数不平衡时向人数多的一侧移动不会变差。线上最近邮局的服务区可取连续区间，因此最优方案能拆成若干连续组；枚举最后组起点即覆盖全部方案。

**复杂度**

w预处理O(N²)时间与空间；普通分组DP O(kN²)，决策优化O(kN)，总还需计入w的O(N²)预处理。

**边界与易错点**

输入必须先排序，源码依赖已有序。邮局数≥居民数时答案0。不能将平均数用于绝对距离最小化，那是平方误差模型。

### 42.2 扔棋子测楼层问题

#### 题目

有若干枚相同棋子和一栋若干层的楼，求确定棋子恰好会摔碎的临界楼层所需的最少最坏尝试次数。

**输入、输出与约束**

存在单调临界楼层，低于它不碎、高于或达到阈值会碎；返回保证确定临界所需最少最坏投掷次数。

**函数签名（课程入口）**

```java
public static int superEggDrop1(int kChess, int nLevel);
public static int Process1(int rest, int k);
public static int superEggDrop2(int kChess, int nLevel);
public static int superEggDrop3(int kChess, int nLevel);
public static int superEggDrop4(int kChess, int nLevel);
public static int superEggDrop5(int kChess, int nLevel);
```

**示例**

```text
输入：楼层n=6，棋子k=2
输出：3
```

解释：3次、2棋子的覆盖数为6，2次只能覆盖3层。

**出处与版本差异**

- [课程源码：class42/Code02_ThrowChessPiecesProblem.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class42/Code02_ThrowChessPiecesProblem.java)。
- [LeetCode 原题 887. 鸡蛋掉落（Super Egg Drop）](https://leetcode.com/problems/super-egg-drop/)

#### 思路

普通状态以楼层数n、棋子数k表示最坏最少次数，试第x层后取碎与不碎两分支较大者加一。更紧凑的覆盖状态cover[k]表示已有t次机会、k枚棋子最多能区分的楼层数；再试一次可覆盖碎后下方、当前层、不碎后上方，递推为旧cover[k-1]+旧cover[k]+1。

#### 代码答案

```java
package class42;

// leetcode测试链接：https://leetcode.com/problems/super-egg-drop
// 方法1和方法2会超时
// 方法3勉强通过
// 方法4打败100%
// 方法5打败100%，方法5是在方法4的基础上做了进一步的常数优化
public class Code02_ThrowChessPiecesProblem {

	public static int superEggDrop1(int kChess, int nLevel) {
		if (nLevel < 1 || kChess < 1) {
			return 0;
		}
		return Process1(nLevel, kChess);
	}

	// rest还剩多少层楼需要去验证
	// k还有多少颗棋子能够使用
	// 一定要验证出最高的不会碎的楼层！但是每次都是坏运气。
	// 返回至少需要扔几次？
	public static int Process1(int rest, int k) {
		if (rest == 0) {
			return 0;
		}
		if (k == 1) {
			return rest;
		}
		int min = Integer.MAX_VALUE;
		for (int i = 1; i != rest + 1; i++) { // 第一次扔的时候，仍在了i层
			min = Math.min(min, Math.max(Process1(i - 1, k - 1), Process1(rest - i, k)));
		}
		return min + 1;
	}

	public static int superEggDrop2(int kChess, int nLevel) {
		if (nLevel < 1 || kChess < 1) {
			return 0;
		}
		if (kChess == 1) {
			return nLevel;
		}
		int[][] dp = new int[nLevel + 1][kChess + 1];
		for (int i = 1; i != dp.length; i++) {
			dp[i][1] = i;
		}
		for (int i = 1; i != dp.length; i++) {
			for (int j = 2; j != dp[0].length; j++) {
				int min = Integer.MAX_VALUE;
				for (int k = 1; k != i + 1; k++) {
					min = Math.min(min, Math.max(dp[k - 1][j - 1], dp[i - k][j]));
				}
				dp[i][j] = min + 1;
			}
		}
		return dp[nLevel][kChess];
	}

	public static int superEggDrop3(int kChess, int nLevel) {
		if (nLevel < 1 || kChess < 1) {
			return 0;
		}
		if (kChess == 1) {
			return nLevel;
		}
		int[][] dp = new int[nLevel + 1][kChess + 1];
		for (int i = 1; i != dp.length; i++) {
			dp[i][1] = i;
		}
		int[][] best = new int[nLevel + 1][kChess + 1];
		for (int i = 1; i != dp[0].length; i++) {
			dp[1][i] = 1;
			best[1][i] = 1;
		}
		for (int i = 2; i < nLevel + 1; i++) {
			for (int j = kChess; j > 1; j--) {
				int ans = Integer.MAX_VALUE;
				int bestChoose = -1;
				int down = best[i - 1][j];
				int up = j == kChess ? i : best[i][j + 1];
				for (int first = down; first <= up; first++) {
					int cur = Math.max(dp[first - 1][j - 1], dp[i - first][j]);
					if (cur <= ans) {
						ans = cur;
						bestChoose = first;
					}
				}
				dp[i][j] = ans + 1;
				best[i][j] = bestChoose;
			}
		}
		return dp[nLevel][kChess];
	}

	public static int superEggDrop4(int kChess, int nLevel) {
		if (nLevel < 1 || kChess < 1) {
			return 0;
		}
		int[] dp = new int[kChess];
		int res = 0;
		while (true) {
			res++;
			int previous = 0;
			for (int i = 0; i < dp.length; i++) {
				int tmp = dp[i];
				// 当前层连接碎后可覆盖区与不碎后可覆盖区，previous保存上一轮少一棋子的覆盖数。
				dp[i] = dp[i] + previous + 1;
				previous = tmp;
				// 已能区分全部待测楼层，当前试投次数就是最小答案。
				if (dp[i] >= nLevel) {
					return res;
				}
			}
		}
	}

	public static int superEggDrop5(int kChess, int nLevel) {
		if (nLevel < 1 || kChess < 1) {
			return 0;
		}
		int bsTimes = log2N(nLevel) + 1;
		if (kChess >= bsTimes) {
			return bsTimes;
		}
		int[] dp = new int[kChess];
		int res = 0;
		while (true) {
			res++;
			int previous = 0;
			for (int i = 0; i < dp.length; i++) {
				int tmp = dp[i];
				// 当前层连接碎后可覆盖区与不碎后可覆盖区，previous保存上一轮少一棋子的覆盖数。
				dp[i] = dp[i] + previous + 1;
				previous = tmp;
				// 已能区分全部待测楼层，当前试投次数就是最小答案。
				if (dp[i] >= nLevel) {
					return res;
				}
			}
		}
	}

	public static int log2N(int n) {
		int res = -1;
		while (n != 0) {
			res++;
			n >>>= 1;
		}
		return res;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int maxN = 500;
		int maxK = 30;
		int testTime = 1000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int N = (int) (Math.random() * maxN) + 1;
			int K = (int) (Math.random() * maxK) + 1;
			int ans2 = superEggDrop2(K, N);
			int ans3 = superEggDrop3(K, N);
			int ans4 = superEggDrop4(K, N);
			int ans5 = superEggDrop5(K, N);
			if (ans2 != ans3 || ans4 != ans5 || ans2 != ans4) {
				System.out.println("出错了!");
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

每次试投把未知临界点分成两个互斥区间。碎分支少一枚棋子，不碎分支保留棋子，两者都少一次机会；把各自可覆盖层数加当前试层得到新的完整可覆盖范围。首个cover[k]≥n的t即为最少最坏次数。

**复杂度**

朴素楼层DP O(kn²)；覆盖版O(kT)时间、O(k)空间，T为答案。常见k较大时T约log n，但k=1时T=n。

**边界与易错点**

覆盖版压一维时要使用上一轮的k和k-1，需逆序更新或暂存旧值。取min是在选试层，取max是在对抗最坏碎裂结果，不能互换。


<a id="course-43"></a>

## 第 43 课：状态压缩动态规划

### 43.1 累加数字先到目标值的先手胜负

#### 题目

两名玩家轮流从 `1..maxChoosableInteger` 中选择未使用数字累加，先使总和达到目标值者获胜，判断先手能否必胜。

**输入、输出与约束**

从1..M中不重复选数，两人共享累计和，先达到目标者胜；M须足够小以支持状态压缩。

**函数签名（课程入口）**

```java
public static boolean canIWin0(int choose, int total);
public static boolean canIWin1(int choose, int total);
public static boolean canIWin2(int choose, int total);
```

**示例**

```text
输入：max=10，desiredTotal=11
输出：false
```

解释：先手选x后，对手可选11-x直接达到目标。

**出处与版本差异**

- [课程源码：class43/Code01_CanIWin.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class43/Code01_CanIWin.java)。
- [LeetCode 原题 464. 我能赢吗（Can I Win）](https://leetcode.com/problems/can-i-win/)

#### 思路

用位掩码记录哪些数字已经用过，剩余目标rest由已选数字总和决定。枚举未用数字i：若i≥rest立即获胜；否则把i设为已用，若对手在剩余局面必败，当前也必胜。记忆化按掩码保存胜负。

#### 代码答案

```java
package class43;

// leetcode 464题
public class Code01_CanIWin {

	// 1~choose 拥有的数字
	// total 一开始的剩余
	// 返回先手会不会赢
	public static boolean canIWin0(int choose, int total) {
		if (total == 0) {
			return true;
		}
		if ((choose * (choose + 1) >> 1) < total) {
			return false;
		}
		int[] arr = new int[choose];
		for (int i = 0; i < choose; i++) {
			arr[i] = i + 1;
		}
		// arr[i] != -1 表示arr[i]这个数字还没被拿走
		// arr[i] == -1 表示arr[i]这个数字已经被拿走
		// 集合，arr，1~choose
		return process(arr, total);
	}

	// 当前轮到先手拿，
	// 先手只能选择在arr中还存在的数字，
	// 还剩rest这么值，
	// 返回先手会不会赢
	public static boolean process(int[] arr, int rest) {
		if (rest <= 0) {
			return false;
		}
		// 先手去尝试所有的情况
		for (int i = 0; i < arr.length; i++) {
			if (arr[i] != -1) {
				int cur = arr[i];
				arr[i] = -1;
				boolean next = process(arr, rest - cur);
				arr[i] = cur;
				if (!next) {
					return true;
				}
			}
		}
		return false;
	}

	// 这个是暴力尝试，思路是正确的，超时而已
	public static boolean canIWin1(int choose, int total) {
		if (total == 0) {
			return true;
		}
		if ((choose * (choose + 1) >> 1) < total) {
			return false;
		}
		return process1(choose, 0, total);
	}

	// 当前轮到先手拿，
	// 先手可以拿1~choose中的任何一个数字
	// status   i位如果为0，代表没拿，当前可以拿
	//          i位为1，代表已经拿过了，当前不能拿
	// 还剩rest这么值，
	// 返回先手会不会赢
	public static boolean process1(int choose, int status, int rest) {
		if (rest <= 0) {
			return false;
		}
		for (int i = 1; i <= choose; i++) {
			if (((1 << i) & status) == 0) { // i 这个数字，是此时先手的决定！
				if (!process1(choose, (status | (1 << i)), rest - i)) {
					return true;
				}
			}
		}
		return false;
	}

	// 暴力尝试改动态规划而已
	public static boolean canIWin2(int choose, int total) {
		if (total == 0) {
			return true;
		}
		if ((choose * (choose + 1) >> 1) < total) {
			return false;
		}
		int[] dp = new int[1 << (choose + 1)];
		// dp[status] == 1  true
		// dp[status] == -1  false
		// dp[status] == 0  process(status) 没算过！去算！
		return process2(choose, 0, total, dp);
	}

	// 为什么明明status和rest是两个可变参数，却只用status来代表状态(也就是dp)
	// 因为选了一批数字之后，得到的和一定是一样的，所以rest是由status决定的，所以rest不需要参与记忆化搜索
	public static boolean process2(int choose, int status, int rest, int[] dp) {
		if (dp[status] != 0) {
			return dp[status] == 1 ? true : false;
		}
		boolean ans = false;
		if (rest > 0) {
			for (int i = 1; i <= choose; i++) {
				if (((1 << i) & status) == 0) {
					// 把选择权交给对手后若其必败，则当前这一手能保证获胜。
					if (!process2(choose, (status | (1 << i)), rest - i, dp)) {
						ans = true;
						break;
					}
				}
			}
		}
		dp[status] = ans ? 1 : -1;
		return ans;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：1..M总和小于目标时谁都无法达到，应在递归前直接判false，不能套无合法步必败推导。目标≤0按题目约定先手已达标。

本条未附独立随机对数器。

#### 题解

**为什么正确**

有限无平局博弈中，存在一步通向对手必败态则当前必胜；若所有合法步都通向对手必胜态则当前必败。已用集合唯一决定累计和，因此无需把rest再加入缓存键。

**复杂度**

至多2^M个集合，每状态枚举M个数字，时间O(M2^M)，空间O(2^M)，递归栈O(M)。

**边界与易错点**

1..M总和小于目标时谁都无法达到，应在递归前直接判false，不能套无合法步必败推导。目标≤0按题目约定先手已达标。

### 43.2 旅行商问题

#### 题目

给定城市间距离矩阵，从指定城市出发访问每个城市恰好一次并回到起点，返回最短总路程。

**输入、输出与约束**

输入完整城市距离矩阵及对应方法的起点；访问每城一次后回到起点，返回最短总距离。

**函数签名（课程入口）**

```java
public static int t1(int[][] matrix);
public static int t2(int[][] matrix);
public static int f2(int[][] matrix, int cityStatus, int start);
public static int t3(int[][] matrix);
public static int f3(int[][] matrix, int cityStatus, int start, int[][] dp);
public static int t4(int[][] matrix);
```

**示例**

```text
输入：距离矩阵[[0,1,4],[1,0,2],[4,2,0]]，起点0
输出：7
```

解释：路线0→1→2→0，费用1+2+4=7。

**出处与版本差异**

- [课程源码：class43/Code02_TSP.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class43/Code02_TSP.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

TSP（Traveling Salesman Problem，旅行商问题）状态记录当前城市和还需访问的集合。枚举下一城市后删除它，最后只剩当前城市时直接返回回起点的距离。集合用位掩码表达，记忆化或表格避免重复计算相同子旅行。

#### 代码答案

```java
package class43;

import java.util.ArrayList;
import java.util.List;

public class Code02_TSP {

	public static int t1(int[][] matrix) {
		int N = matrix.length; // 0...N-1
		// set
		// set.get(i) != null i这座城市在集合里
		// set.get(i) == null i这座城市不在集合里
		List<Integer> set = new ArrayList<>();
		for (int i = 0; i < N; i++) {
			set.add(1);
		}
		return func1(matrix, set, 0);
	}

	// 任何两座城市之间的距离，可以在matrix里面拿到
	// set中表示着哪些城市的集合，
	// start这座城一定在set里，
	// 从start出发，要把set中所有的城市过一遍，最终回到0这座城市，最小距离是多少
	public static int func1(int[][] matrix, List<Integer> set, int start) {
		int cityNum = 0;
		for (int i = 0; i < set.size(); i++) {
			if (set.get(i) != null) {
				cityNum++;
			}
		}
		if (cityNum == 1) {
			return matrix[start][0];
		}
		// cityNum > 1  不只start这一座城
		set.set(start, null);
		int min = Integer.MAX_VALUE;
		for (int i = 0; i < set.size(); i++) {
			if (set.get(i) != null) {
				// start -> i i... -> 0
				int cur = matrix[start][i] + func1(matrix, set, i);
				min = Math.min(min, cur);
			}
		}
		set.set(start, 1);
		return min;
	}

	public static int t2(int[][] matrix) {
		int N = matrix.length; // 0...N-1
		// 7座城 1111111
		int allCity = (1 << N) - 1;
		return f2(matrix, allCity, 0);
	}

	// 任何两座城市之间的距离，可以在matrix里面拿到
	// set中表示着哪些城市的集合，
	// start这座城一定在set里，
	// 从start出发，要把set中所有的城市过一遍，最终回到0这座城市，最小距离是多少
	public static int f2(int[][] matrix, int cityStatus, int start) {
		// cityStatus == cityStatux & (~cityStaus + 1)

		if (cityStatus == (cityStatus & (~cityStatus + 1))) {
			return matrix[start][0];
		}

		// 把start位的1去掉，
		cityStatus &= (~(1 << start));
		int min = Integer.MAX_VALUE;
		// 枚举所有的城市
		for (int move = 0; move < matrix.length; move++) {
			if ((cityStatus & (1 << move)) != 0) {
				int cur = matrix[start][move] + f2(matrix, cityStatus, move);
				min = Math.min(min, cur);
			}
		}
		cityStatus |= (1 << start);
		return min;
	}

	public static int t3(int[][] matrix) {
		int N = matrix.length; // 0...N-1
		// 7座城 1111111
		int allCity = (1 << N) - 1;
		int[][] dp = new int[1 << N][N];
		for (int i = 0; i < (1 << N); i++) {
			for (int j = 0; j < N; j++) {
				dp[i][j] = -1;
			}
		}
		return f3(matrix, allCity, 0, dp);
	}

	// 任何两座城市之间的距离，可以在matrix里面拿到
	// set中表示着哪些城市的集合，
	// start这座城一定在set里，
	// 从start出发，要把set中所有的城市过一遍，最终回到0这座城市，最小距离是多少
	public static int f3(int[][] matrix, int cityStatus, int start, int[][] dp) {
		if (dp[cityStatus][start] != -1) {
			return dp[cityStatus][start];
		}
		if (cityStatus == (cityStatus & (~cityStatus + 1))) {
			dp[cityStatus][start] = matrix[start][0];
		} else {
			// 把start位的1去掉，
			cityStatus &= (~(1 << start));
			int min = Integer.MAX_VALUE;
			// 枚举所有的城市
			for (int move = 0; move < matrix.length; move++) {
				if (move != start && (cityStatus & (1 << move)) != 0) {
					int cur = matrix[start][move] + f3(matrix, cityStatus, move, dp);
					min = Math.min(min, cur);
				}
			}
			cityStatus |= (1 << start);
			dp[cityStatus][start] = min;
		}
		return dp[cityStatus][start];
	}

	public static int t4(int[][] matrix) {
		int N = matrix.length; // 0...N-1
		int statusNums = 1 << N;
		int[][] dp = new int[statusNums][N];

		for (int status = 0; status < statusNums; status++) {
			for (int start = 0; start < N; start++) {
				if ((status & (1 << start)) != 0) {
					// 集合只剩一个置位时，没有其他城市可访问，接下来直接回起点。
					if (status == (status & (~status + 1))) {
						dp[status][start] = matrix[start][0];
					} else {
						int min = Integer.MAX_VALUE;
						// start 城市在status里去掉之后，的状态
						int preStatus = status & (~(1 << start));
						// start -> i
						for (int i = 0; i < N; i++) {
							if ((preStatus & (1 << i)) != 0) {
								int cur = matrix[start][i] + dp[preStatus][i];
								min = Math.min(min, cur);
							}
						}
						dp[status][start] = min;
					}
				}
			}
		}
		return dp[statusNums - 1][0];
	}

	// matrix[i][j] -> i城市到j城市的距离
	public static int tsp1(int[][] matrix, int origin) {
		if (matrix == null || matrix.length < 2 || origin < 0 || origin >= matrix.length) {
			return 0;
		}
		// 要考虑的集合
		ArrayList<Integer> cities = new ArrayList<>();
		// cities[0] != null 表示0城在集合里
		// cities[i] != null 表示i城在集合里
		for (int i = 0; i < matrix.length; i++) {
			cities.add(1);
		}
		// null,1,1,1,1,1,1
		// origin城不参与集合
		cities.set(origin, null);
		return process(matrix, origin, cities, origin);
	}

	// matrix 所有距离，存在其中
	// origin 固定参数，唯一的目标
	// cities 要考虑的集合，一定不含有origin
	// 当前来到的城市是谁，cur
	public static int process(int[][] matrix, int aim, ArrayList<Integer> cities, int cur) {
		boolean hasCity = false; // 集团中还是否有城市
		int ans = Integer.MAX_VALUE;
		for (int i = 0; i < cities.size(); i++) {
			if (cities.get(i) != null) {
				hasCity = true;
				cities.set(i, null);
				// matrix[cur][i] + f(i, 集团(去掉i) )
				ans = Math.min(ans, matrix[cur][i] + process(matrix, aim, cities, i));
				cities.set(i, 1);
			}
		}
		return hasCity ? ans : matrix[cur][aim];
	}

	// cities 里，一定含有cur这座城
	// 解决的是，集合从cur出发，通过集合里所有的城市，最终来到aim，最短距离
	public static int process2(int[][] matrix, int aim, ArrayList<Integer> cities, int cur) {
		if (cities.size() == 1) {
			return matrix[cur][aim];
		}
		cities.set(cur, null);
		int ans = Integer.MAX_VALUE;
		for (int i = 0; i < cities.size(); i++) {
			if (cities.get(i) != null) {
				int dis = matrix[cur][i] + process2(matrix, aim, cities, i);
				ans = Math.min(ans, dis);
			}
		}
		cities.set(cur, 1);
		return ans;
	}

	public static int tsp2(int[][] matrix, int origin) {
		if (matrix == null || matrix.length < 2 || origin < 0 || origin >= matrix.length) {
			return 0;
		}
		int N = matrix.length - 1; // 除去origin之后是n-1个点
		int S = 1 << N; // 状态数量
		int[][] dp = new int[S][N];
		int icity = 0;
		int kcity = 0;
		for (int i = 0; i < N; i++) {
			icity = i < origin ? i : i + 1;
			// 00000000 i
			dp[0][i] = matrix[icity][origin];
		}
		for (int status = 1; status < S; status++) {
			// 尝试每一种状态 status = 0 0 1 0 0 0 0 0 0
			// 下标 8 7 6 5 4 3 2 1 0
			for (int i = 0; i < N; i++) {
				// i 枚举的出发城市
				dp[status][i] = Integer.MAX_VALUE;
				if ((1 << i & status) != 0) {
					// 如果i这座城是可以枚举的，i = 6 ， i对应的原始城的编号，icity
					icity = i < origin ? i : i + 1;
					for (int k = 0; k < N; k++) { // i 这一步连到的点，k
						if ((1 << k & status) != 0) { // i 这一步可以连到k
							kcity = k < origin ? k : k + 1; // k对应的原始城的编号，kcity
							dp[status][i] = Math.min(dp[status][i], dp[status ^ (1 << i)][k] + matrix[icity][kcity]);
						}
					}
				}
			}
		}
		int ans = Integer.MAX_VALUE;
		for (int i = 0; i < N; i++) {
			icity = i < origin ? i : i + 1;
			ans = Math.min(ans, dp[S - 1][i] + matrix[origin][icity]);
		}
		return ans;
	}

	public static int[][] generateGraph(int maxSize, int maxValue) {
		int len = (int) (Math.random() * maxSize) + 1;
		int[][] matrix = new int[len][len];
		for (int i = 0; i < len; i++) {
			for (int j = 0; j < len; j++) {
				matrix[i][j] = (int) (Math.random() * maxValue) + 1;
			}
		}
		for (int i = 0; i < len; i++) {
			matrix[i][i] = 0;
		}
		return matrix;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int len = 10;
		int value = 100;
		System.out.println("功能测试开始");
		for (int i = 0; i < 20000; i++) {
			int[][] matrix = generateGraph(len, value);
			int origin = (int) (Math.random() * matrix.length);
			int ans1 = t3(matrix);
			int ans2 = t4(matrix);
			int ans3 = tsp2(matrix, origin);
			if (ans1 != ans2 || ans1 != ans3) {
				System.out.println("fuck");
			}
		}
		System.out.println("功能测试结束");

		len = 22;
		System.out.println("性能测试开始，数据规模 : " + len);
		int[][] matrix = new int[len][len];
		for (int i = 0; i < len; i++) {
			for (int j = 0; j < len; j++) {
				matrix[i][j] = (int) (Math.random() * value) + 1;
			}
		}
		for (int i = 0; i < len; i++) {
			matrix[i][i] = 0;
		}
		long start;
		long end;
		start = System.currentTimeMillis();
		t4(matrix);
		end = System.currentTimeMillis();
		System.out.println("运行时间 : " + (end - start) + " 毫秒");
		System.out.println("性能测试结束");

	}
```

#### 题解

**为什么正确**

每条合法巡回路线从当前城市出发都有唯一下一个未访问城市；固定它后剩余正是规模更小的相同问题。各分支距离加当前边并取最小，基例补上回起点的边，保证不是只求开放路径。

**复杂度**

暴力约O(N!)；状态压缩O(N²2^N)时间、O(N2^N)空间。

**边界与易错点**

必须最后回起点。集合位表示已访问还是未访问要与方法一致；城市数不宜超过可分配2^N状态的规模。

### 43.3 铺砖问题

#### 题目

给定 `N × M` 棋盘和无限个 `1 × 2` 多米诺骨牌，返回铺满棋盘的方案数。

**输入、输出与约束**

正整数N、M，使用1×2骨牌无重叠无空洞铺满；在线评测版返回long，状态宽度须足够小。

**函数签名（课程入口）**

```java
public static int ways1(int N, int M);
public static int[] getOp(int[] pre);
public static int ways2(int N, int M);
public static int ways3(int N, int M);
public static int ways4(int N, int M);
```

**示例**

```text
输入：棋盘2×3
输出：3
```

解释：三个竖砖一种；另有两种由两块横砖与一块竖砖组合的铺法。

**出处与版本差异**

- [课程源码：class43/Code03_PavingTile.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class43/Code03_PavingTile.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

把较短边作为位掩码宽度，逐行处理。上一行掩码中的未填满位置需要本行竖砖承接，取反得到本行初始占用状态。扫描本行空格，可保留为向下一行伸出的竖砖，或在连续两个空格放横砖；完整行状态汇总到下一层。

#### 代码答案

```java
package class43;

public class Code03_PavingTile {

	/*
	 * 2*M铺地的问题非常简单，这个是解决N*M铺地的问题
	 */

	public static int ways1(int N, int M) {
		if (N < 1 || M < 1 || ((N * M) & 1) != 0) {
			return 0;
		}
		if (N == 1 || M == 1) {
			return 1;
		}
		int[] pre = new int[M]; // pre代表-1行的状况
		for (int i = 0; i < pre.length; i++) {
			pre[i] = 1;
		}
		return process(pre, 0, N);
	}

	// pre 表示level-1行的状态
	// level表示，正在level行做决定
	// N 表示一共有多少行 固定的
	// level-2行及其之上所有行，都摆满砖了
	// level做决定，让所有区域都满，方法数返回
	public static int process(int[] pre, int level, int N) {
		if (level == N) { // base case
			for (int i = 0; i < pre.length; i++) {
				if (pre[i] == 0) {
					return 0;
				}
			}
			return 1;
		}

		// 没到终止行，可以选择在当前的level行摆瓷砖
		int[] op = getOp(pre);
		return dfs(op, 0, level, N);
	}

	// op[i] == 0 可以考虑摆砖
	// op[i] == 1 只能竖着向上
	public static int dfs(int[] op, int col, int level, int N) {
		// 在列上自由发挥，玩深度优先遍历，当col来到终止列，i行的决定做完了
		// 轮到i+1行，做决定
		if (col == op.length) {
			return process(op, level + 1, N);
		}
		int ans = 0;
		// col位置不横摆
		ans += dfs(op, col + 1, level, N); // col位置上不摆横转
		// col位置横摆, 向右
		if (col + 1 < op.length && op[col] == 0 && op[col + 1] == 0) {
			op[col] = 1;
			op[col + 1] = 1;
			ans += dfs(op, col + 2, level, N);
			op[col] = 0;
			op[col + 1] = 0;
		}
		return ans;
	}

	public static int[] getOp(int[] pre) {
		int[] cur = new int[pre.length];
		for (int i = 0; i < pre.length; i++) {
			cur[i] = pre[i] ^ 1;
		}
		return cur;
	}

	// Min (N,M) 不超过 32
	public static int ways2(int N, int M) {
		if (N < 1 || M < 1 || ((N * M) & 1) != 0) {
			return 0;
		}
		if (N == 1 || M == 1) {
			return 1;
		}
		int max = Math.max(N, M);
		int min = Math.min(N, M);
		int pre = (1 << min) - 1;
		return process2(pre, 0, max, min);
	}

	// 上一行的状态，是pre，limit是用来对齐的，固定参数不用管
	// 当前来到i行，一共N行，返回填满的方法数
	public static int process2(int pre, int i, int N, int M) {
		if (i == N) { // base case
			return pre == ((1 << M) - 1) ? 1 : 0;
		}
		int op = ((~pre) & ((1 << M) - 1));
		return dfs2(op, M - 1, i, N, M);
	}

	public static int dfs2(int op, int col, int level, int N, int M) {
		if (col == -1) {
			return process2(op, level + 1, N, M);
		}
		int ans = 0;
		ans += dfs2(op, col - 1, level, N, M);
		if ((op & (1 << col)) == 0 && col - 1 >= 0 && (op & (1 << (col - 1))) == 0) {
			ans += dfs2((op | (3 << (col - 1))), col - 2, level, N, M);
		}
		return ans;
	}

	// 记忆化搜索的解
	// Min(N,M) 不超过 32
	public static int ways3(int N, int M) {
		if (N < 1 || M < 1 || ((N * M) & 1) != 0) {
			return 0;
		}
		if (N == 1 || M == 1) {
			return 1;
		}
		int max = Math.max(N, M);
		int min = Math.min(N, M);
		int pre = (1 << min) - 1;
		int[][] dp = new int[1 << min][max + 1];
		for (int i = 0; i < dp.length; i++) {
			for (int j = 0; j < dp[0].length; j++) {
				dp[i][j] = -1;
			}
		}
		return process3(pre, 0, max, min, dp);
	}

	public static int process3(int pre, int i, int N, int M, int[][] dp) {
		if (dp[pre][i] != -1) {
			return dp[pre][i];
		}
		int ans = 0;
		if (i == N) {
			ans = pre == ((1 << M) - 1) ? 1 : 0;
		} else {
			int op = ((~pre) & ((1 << M) - 1));
			ans = dfs3(op, M - 1, i, N, M, dp);
		}
		dp[pre][i] = ans;
		return ans;
	}

	public static int dfs3(int op, int col, int level, int N, int M, int[][] dp) {
		if (col == -1) {
			return process3(op, level + 1, N, M, dp);
		}
		int ans = 0;
		ans += dfs3(op, col - 1, level, N, M, dp);
		if (col > 0 && (op & (3 << (col - 1))) == 0) {
			ans += dfs3((op | (3 << (col - 1))), col - 2, level, N, M, dp);
		}
		return ans;
	}

	// 严格位置依赖的动态规划解
	public static int ways4(int N, int M) {
		if (N < 1 || M < 1 || ((N * M) & 1) != 0) {
			return 0;
		}
		if (N == 1 || M == 1) {
			return 1;
		}
		int big = N > M ? N : M;
		int small = big == N ? M : N;
		int sn = 1 << small;
		int limit = sn - 1;
		int[] dp = new int[sn];
		// 虚拟上一行已经全满，第一行没有上方竖砖占用。
		dp[limit] = 1;
		int[] cur = new int[sn];
		for (int level = 0; level < big; level++) {
			for (int status = 0; status < sn; status++) {
				if (dp[status] != 0) {
					// 上一行未填满的位由本行竖砖占据，取反后裁掉棋盘外高位。
					int op = (~status) & limit;
					dfs4(dp[status], op, 0, small - 1, cur);
				}
			}
			for (int i = 0; i < sn; i++) {
				dp[i] = 0;
			}
			int[] tmp = dp;
			dp = cur;
			cur = tmp;
		}
		return dp[limit];
	}

	public static void dfs4(int way, int op, int index, int end, int[] cur) {
		if (index == end) {
			cur[op] += way;
		} else {
			dfs4(way, op, index + 1, end, cur);
			if (((3 << index) & op) == 0) { // 11 << index 可以放砖
				// 相邻两个空格放一块横砖，把两个占用位同时置1。
				dfs4(way, op | (3 << index), index + 1, end, cur);
			}
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int N = 8;
		int M = 6;
		System.out.println(ways1(N, M));
		System.out.println(ways2(N, M));
		System.out.println(ways3(N, M));
		System.out.println(ways4(N, M));

		N = 10;
		M = 10;
		System.out.println("=========");
		System.out.println(ways3(N, M));
		System.out.println(ways4(N, M));
	}
```

#### 题解

**为什么正确**

砖只跨相邻两行，所以过去对未来的影响全部由当前边界占用情况决定。横放、竖放对第一个未覆盖格的选择互斥且完备；最终最后一行全满且无向外伸出需求时才算完整铺法。

**复杂度**

宽W=min(N,M)、高H=max(N,M)。行状态2^W，逐行枚举填充，保守时间O(H·W·4^W)；滚动行表O(2^W)空间，递归/记忆版本空间依状态层数变化。

**边界与易错点**

面积奇数直接0。掩码位1在当前课程行版表示已占满，与另一轮廓版约定不同。必须用较短边作宽度减小指数规模。

### 43.4 铺砖问题：按行状态压缩（在线评测版）

#### 题目

正整数N、M，使用1×2骨牌无重叠无空洞铺满；在线评测版返回long，状态宽度须足够小。

**输入、输出与约束**

正整数N、M，使用1×2骨牌无重叠无空洞铺满；在线评测版返回long，状态宽度须足够小。

**函数签名（课程入口）**

```java
public static long ways(int N, int M);
```

**示例**

```text
输入：棋盘2×3
输出：3
```

解释：三个竖砖一种；另有两种由两块横砖与一块竖砖组合的铺法。

**出处与版本差异**

- [课程源码：class43/Code03_PavingTile1.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class43/Code03_PavingTile1.java)。
- [课程源码标注的在线资料](http://poj.org/problem?id=2411)。

#### 思路

把较短边作为位掩码宽度，逐行处理。上一行掩码中的未填满位置需要本行竖砖承接，取反得到本行初始占用状态。扫描本行空格，可保留为向下一行伸出的竖砖，或在连续两个空格放横砖；完整行状态汇总到下一层。

#### 代码答案

```java
package class43;

// 找到了贴瓷砖问题在线测试
// 测试链接 : http://poj.org/problem?id=2411
// 注册一个北京大学评测平台的号
// 请同学们务必参考如下代码中关于输入、输出的处理
// 这是输入输出处理效率很高的写法
// 提交以下的code，提交时请把类名改成"Main"

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.io.StreamTokenizer;

// 本文件是状态压缩的动态规划版本，也就是课上讲的版本
public class Code03_PavingTile1 {

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StreamTokenizer in = new StreamTokenizer(br);
		PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
		while (in.nextToken() != StreamTokenizer.TT_EOF) {
			int n = (int) in.nval;
			in.nextToken();
			int m = (int) in.nval;
			if (n != 0 || m != 0) {
				long ans = ways(n, m);
				out.println(ans);
				out.flush();
			}
		}
	}

	// 状态压缩动态规划，最后一个版本
	// 其实其他版本也能通过
	public static long ways(int N, int M) {
		if (N < 1 || M < 1 || ((N * M) & 1) != 0) {
			return 0;
		}
		if (N == 1 || M == 1) {
			return 1;
		}
		int big = N > M ? N : M;
		int small = big == N ? M : N;
		int sn = 1 << small;
		int limit = sn - 1;
		long[] dp = new long[sn];
		// 虚拟上一行已经全满，第一行没有上方竖砖占用。
		dp[limit] = 1;
		long[] cur = new long[sn];
		for (int level = 0; level < big; level++) {
			for (int status = 0; status < sn; status++) {
				if (dp[status] != 0) {
					// 上一行未填满的位由本行竖砖占据，取反后裁掉棋盘外高位。
					int op = (~status) & limit;
					dfs(dp[status], op, 0, small - 1, cur);
				}
			}
			for (int i = 0; i < sn; i++) {
				dp[i] = 0;
			}
			long[] tmp = dp;
			dp = cur;
			cur = tmp;
		}
		return dp[limit];
	}

	public static void dfs(long way, int op, int index, int end, long[] cur) {
		if (index == end) {
			cur[op] += way;
		} else {
			dfs(way, op, index + 1, end, cur);
			if (((3 << index) & op) == 0) {
				// 相邻两个空格放一块横砖，把两个占用位同时置1。
				dfs(way, op | (3 << index), index + 1, end, cur);
			}
		}
	}

}
```

---

##### 输入输出核对

先以本题示例核对结果，再重点覆盖：面积奇数直接0。掩码位1在当前课程行版表示已占满，与另一轮廓版约定不同。必须用较短边作宽度减小指数规模。

该版本保留在线评测入口；未附独立随机对数器，不把编译通过当作正确性证明。

#### 题解

**为什么正确**

砖只跨相邻两行，所以过去对未来的影响全部由当前边界占用情况决定。横放、竖放对第一个未覆盖格的选择互斥且完备；最终最后一行全满且无向外伸出需求时才算完整铺法。

**复杂度**

宽W=min(N,M)、高H=max(N,M)。行状态2^W，逐行枚举填充，保守时间O(H·W·4^W)；滚动行表O(2^W)空间，递归/记忆版本空间依状态层数变化。

**边界与易错点**

面积奇数直接0。掩码位1在当前课程行版表示已占满，与另一轮廓版约定不同。必须用较短边作宽度减小指数规模。

### 43.5 铺砖问题：逐格轮廓状态压缩

#### 题目

输入1..11范围的行列数；返回铺满方案数，代码使用long。

**输入、输出与约束**

输入1..11范围的行列数；返回铺满方案数，代码使用long。

**函数签名（课程入口）**

```java
public static long ways(int n, int m);
```

**示例**

```text
输入：棋盘2×2
输出：2
```

解释：两块竖砖或两块横砖；跨出最后一行的状态不计。

**出处与版本差异**

- [课程源码：class43/Code03_PavingTile2.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class43/Code03_PavingTile2.java)。
- [课程源码标注的在线资料](http://poj.org/problem?id=2411)。

#### 思路

逐格扫描的轮廓状态(r,c,s)中，第c位表示当前格是否已被上方竖砖占据。已占据则清位并前进；未占据则可放竖砖，把该位留给下一行，或在右邻空闲时放横砖并前进两格。

#### 代码答案

```java
package class43;

// 找到了贴瓷砖问题在线测试
// 测试链接 : http://poj.org/problem?id=2411
// 注册一个北京大学评测平台的号
// 请同学们务必参考如下代码中关于输入、输出的处理
// 这是输入输出处理效率很高的写法
// 提交以下的code，提交时请把类名改成"Main"
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.io.StreamTokenizer;

// 本文件是轮廓线dp的版本，轮廓线dp的讲解在每周直播课 :
// 2022年9月第4周的课，看了就能懂
// 这是很难的一类题型，不做要求，大厂几乎不考
public class Code03_PavingTile2 {

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StreamTokenizer in = new StreamTokenizer(br);
		PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
		while (in.nextToken() != StreamTokenizer.TT_EOF) {
			int n = (int) in.nval;
			in.nextToken();
			int m = (int) in.nval;
			if (n != 0 || m != 0) {
				long ans = ways(n, m);
				out.println(ans);
				out.flush();
			}
		}
	}

	// 轮廓线dp的版本
	// 看课，上面说了看哪的
	public static int MAXN = 12;

	public static int MAXM = (1 << MAXN);

	public static long[][][] dp = new long[MAXN][MAXN][MAXM];

	public static long ways(int n, int m) {
		for (int i = 0; i <= n; i++) {
			for (int j = 0; j <= m; j++) {
				for (int k = 0; k <= (1 << m); k++) {
					dp[i][j][k] = -1;
				}
			}
		}
		return process(0, 0, 0, n, m);
	}

	public static long process(int r, int c, int s, int n, int m) {
		if (r == n) {
			// 全部格子扫描完后，不能仍有竖砖伸出到不存在的下一行。
			return s == 0 ? 1 : 0;
		}
		if (c == m) {
			return process(r + 1, 0, s, n, m);
		}
		if (dp[r][c][s] != -1) {
			return dp[r][c][s];
		}
		long ans;
		int cur = (s & (1 << c)) == 0 ? 0 : 1;
		if (cur == 1) {
			// 当前格已由上方砖覆盖，消费这一占用标记并继续。
			ans = process(r, c + 1, s ^ (1 << c), n, m);
		} else {
			// 在当前空格放竖砖，为下一行同列登记占用。
			long p1 = process(r, c + 1, s | (1 << c), n, m);
			long next = c + 1 < m && (s & (1 << (c + 1))) == 0 ? 0 : 1;
			long p2 = 0;
			if (next == 0) {
				p2 = process(r, c + 2, s, n, m);
			}
			ans = p1 + p2;
		}
		dp[r][c][s] = ans;
		return ans;
	}

}
```

---

##### 输入输出核对

先以本题示例核对结果，再重点覆盖：终点r==n不能无条件返回1，必须s==0。横放要求右邻存在且空闲。固定MAXN=12且初始化用≤边界，不能把12当可输入最大维度。

该版本保留在线评测入口；未附独立随机对数器，不把编译通过当作正确性证明。

#### 题解

**为什么正确**

按行扫描后，掩码精确保存跨越已处理与未处理边界的竖砖伸出部分。每一步都完整覆盖当前最早未处理格，且每块砖只有唯一摆放时机；扫描出棋盘时只有s=0才没有越界伸出的砖。

**复杂度**

O(NM2^M)时间与记忆空间，每状态至多两个转移；源码固定数组要求1≤N,M≤11。

**边界与易错点**

终点r==n不能无条件返回1，必须s==0。横放要求右邻存在且空闲。固定MAXN=12且初始化用≤边界，不能把12当可输入最大维度。


<a id="course-44"></a>

## 第 44 课：DC3 后缀数组算法

### 44.1 字典序最大的后缀

#### 题目

给定字符串 `s`，返回它的所有子串中字典序最大的子串。

**输入、输出与约束**

输入非空字符串；返回所有连续子串中字典序最大的一个。

**函数签名（课程入口）**

```java
public static String lastSubstring(String s);
```

**示例**

```text
输入：s="abab"
输出："bab"
```

解释：两个b开头后缀为bab与b，较长前缀一致的bab更大。

**出处与版本差异**

- [课程源码：class44/Code01_LastSubstringInLexicographicalOrder.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class44/Code01_LastSubstringInLexicographicalOrder.java)。
- [LeetCode 原题 1163. 按字典序排在最后的子串（Last Substring in Lexicographical Order）](https://leetcode.com/problems/last-substring-in-lexicographical-order/)

#### 思路

任意子串都是某个后缀的前缀，同起点的完整后缀不会比更短子串小，因此答案必为最大后缀。课程构造后缀数组，取排名最后的后缀起点并返回原串从该处到末尾。

#### 代码答案

```java
package class44;

// 测试链接: https://leetcode.com/problems/last-substring-in-lexicographical-order/
public class Code01_LastSubstringInLexicographicalOrder {

	public static String lastSubstring(String s) {
		if (s == null || s.length() == 0) {
			return s;
		}
		int N = s.length();
		char[] str = s.toCharArray();
		int min = Integer.MAX_VALUE;
		int max = Integer.MIN_VALUE;
		for (char cha : str) {
			min = Math.min(min, cha);
			max = Math.max(max, cha);
		}
		int[] arr = new int[N];
		for (int i = 0; i < N; i++) {
			// 真实字符映射为正整数，0留给后缀数组的末尾哨兵。
			arr[i] = str[i] - min + 1;
		}
		DC3 dc3 = new DC3(arr, max - min + 1);
		// 后缀数组最后一名是字典序最大后缀，取其起点到原串末尾。
		return s.substring(dc3.sa[N - 1]);
	}

	public static class DC3 {

		public int[] sa;

		public DC3(int[] nums, int max) {
			sa = sa(nums, max);
		}

		private int[] sa(int[] nums, int max) {
			int n = nums.length;
			// 尾部预留3个零哨兵，读取三元组时无需逐字符判断越界。
			int[] arr = new int[n + 3];
			for (int i = 0; i < n; i++) {
				arr[i] = nums[i];
			}
			return skew(arr, n, max);
		}

		private int[] skew(int[] nums, int n, int K) {
			// 按起点模3计数，采样组由余1与余2位置组成。
			int n0 = (n + 2) / 3, n1 = (n + 1) / 3, n2 = n / 3, n02 = n0 + n2;
			int[] s12 = new int[n02 + 3], sa12 = new int[n02 + 3];
			for (int i = 0, j = 0; i < n + (n0 - n1); ++i) {
				if (0 != i % 3) {
					// 收集采样后缀的起点，必要时包含用于对齐的虚拟后缀。
					s12[j++] = i;
				}
			}
			// 先按三元组第三关键字稳定排序。
			radixPass(nums, s12, sa12, 2, n02, K);
			// 再按第二关键字稳定排序，保留第三关键字内部顺序。
			radixPass(nums, sa12, s12, 1, n02, K);
			// 最后按第一关键字排序，得到完整三元组字典序。
			radixPass(nums, s12, sa12, 0, n02, K);
			int name = 0, c0 = -1, c1 = -1, c2 = -1;
			for (int i = 0; i < n02; ++i) {
				// 仅当三元组不同才分配新名字，相同三元组暂时同名。
				if (c0 != nums[sa12[i]] || c1 != nums[sa12[i] + 1] || c2 != nums[sa12[i] + 2]) {
					name++;
					c0 = nums[sa12[i]];
					c1 = nums[sa12[i] + 1];
					c2 = nums[sa12[i] + 2];
				}
				if (1 == sa12[i] % 3) {
					// 余1组名称放在压缩数组前段。
					s12[sa12[i] / 3] = name;
				} else {
					// 余2组名称放在压缩数组后段，分组偏移为n0。
					s12[sa12[i] / 3 + n0] = name;
				}
			}
			// 名字尚不唯一，单靠前三字符不能排完，递归比较后续名称序列。
			if (name < n02) {
				sa12 = skew(s12, n02, name);
				for (int i = 0; i < n02; i++) {
					// 把递归得到的排列反转成排名，排名从1开始给哨兵保留0。
					s12[sa12[i]] = i + 1;
				}
			} else {
				for (int i = 0; i < n02; i++) {
					// 名字已唯一时不必递归，直接按名称还原采样排列。
					sa12[s12[i] - 1] = i;
				}
			}
			int[] s0 = new int[n0], sa0 = new int[n0];
			for (int i = 0, j = 0; i < n02; i++) {
				if (sa12[i] < n0) {
					// 余0后缀的下一位属于余1组，先沿已知后缀排名组织它们。
					s0[j++] = 3 * sa12[i];
				}
			}
			// 余0组再按首字符稳定排序，首字符相同时沿用后续排名。
			radixPass(nums, s0, sa0, 0, n0, K);
			int[] sa = new int[n];
			// 双指针归并余0组与采样组；t跳过对齐用的虚拟后缀。
			for (int p = 0, t = n0 - n1, k = 0; k < n; k++) {
				// 把压缩数组位置还原为原串余1或余2后缀起点。
				int i = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
				int j = sa0[p];
				// 余1对余0比较首字符加后续排名；余2对余0比较两字符加后续排名。
				if (sa12[t] < n0 ? leq(nums[i], s12[sa12[t] + n0], nums[j], s12[j / 3])
						: leq(nums[i], nums[i + 1], s12[sa12[t] - n0 + 1], nums[j], nums[j + 1], s12[j / 3 + n0])) {
					sa[k] = i;
					t++;
					// 采样组已经耗尽，余0组剩余元素可直接追加。
					if (t == n02) {
						for (k++; p < n0; p++, k++) {
							sa[k] = sa0[p];
						}
					}
				} else {
					sa[k] = j;
					p++;
					// 余0组耗尽，依采样排名追加剩余后缀。
					if (p == n0) {
						for (k++; t < n02; t++, k++) {
							sa[k] = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
						}
					}
				}
			}
			return sa;
		}

		private void radixPass(int[] nums, int[] input, int[] output, int offset, int n, int k) {
			int[] cnt = new int[k + 1];
			for (int i = 0; i < n; ++i) {
				// 统计本轮关键字频次，排序对象是后缀起点而非原数组元素。
				cnt[nums[input[i] + offset]]++;
			}
			for (int i = 0, sum = 0; i < cnt.length; ++i) {
				int t = cnt[i];
				// 把频次转成每个桶的起始写入位置。
				cnt[i] = sum;
				sum += t;
			}
			for (int i = 0; i < n; ++i) {
				// 按输入顺序进入对应桶，并推进写指针，使该趟计数排序稳定。
				output[cnt[nums[input[i] + offset]]++] = input[i];
			}
		}

		private boolean leq(int a1, int a2, int b1, int b2) {
			return a1 < b1 || (a1 == b1 && a2 <= b2);
		}

		private boolean leq(int a1, int a2, int a3, int b1, int b2, int b3) {
			return a1 < b1 || (a1 == b1 && leq(a2, a3, b2, b3));
		}

	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：不要直接只取最大字符首次出现位置，还要比较其后续字符。后缀数组存的是起点，不是字符本身；空串需遵守入口处理。

本条未附独立随机对数器。

#### 题解

**为什么正确**

对任意候选子串，延长到相同起点的完整后缀只可能在已有前缀相同时变大或保持，所以至少有一个后缀不劣于它。后缀数组已经排列所有后缀，最大者就是整个子串集合的最大者。

**复杂度**

本课程DC3构造在紧凑整数字符编码下为O(N+Σ)时间与空间，Σ是计数桶值域；提取结果需O(答案长度)。

**边界与易错点**

不要直接只取最大字符首次出现位置，还要比较其后续字符。后缀数组存的是起点，不是字符本身；空串需遵守入口处理。

### 44.2 DC3 线性时间后缀数组

#### 题目

给定整数序列，在线性时间内构造后缀数组 `sa`、排名数组 `rank` 和高度数组 `height`。

**输入、输出与约束**

输入正整数编码数组及最大编码K，末尾补0哨兵；输出后缀数组、排名与相邻公共前缀数组。

本条为结构或接口练习，调用方式见下方类定义与操作示例。

**示例**

```text
输入：nums=[2,1,2]
输出：sa=[1,2,0]，rank=[2,0,1]，height=[0,0,1]
```

解释：后缀依次为[1,2]、[2]、[2,1,2]，最后两者公共前缀长度1。

**出处与版本差异**

- [课程源码：class44/DC3.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class44/DC3.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

DC3（Difference Cover modulo 3）把后缀起点按模3分组。先对余1、余2的后缀按前三个整数做三趟稳定基数排序，为相同三元组赋相同名字；名字不唯一时递归排序压缩串。再利用已知后缀排名排序余0组，最后用“1或2个原字符+已知排名”在线性归并中比较两组后缀。

#### 代码答案

```java
package class44;

public class DC3 {

	public int[] sa;

	public int[] rank;

	public int[] height;

	// 构造方法的约定:
	// 数组叫nums，如果你是字符串，请转成整型数组nums
	// 数组中，最小值>=1
	// 如果不满足，处理成满足的，也不会影响使用
	// max, nums里面最大值是多少
	public DC3(int[] nums, int max) {
		sa = sa(nums, max);
		rank = rank();
		height = height(nums);
	}

	private int[] sa(int[] nums, int max) {
		int n = nums.length;
		// 尾部预留3个零哨兵，读取三元组时无需逐字符判断越界。
		int[] arr = new int[n + 3];
		for (int i = 0; i < n; i++) {
			arr[i] = nums[i];
		}
		return skew(arr, n, max);
	}

	private int[] skew(int[] nums, int n, int K) {
		// 按起点模3计数，采样组由余1与余2位置组成。
		int n0 = (n + 2) / 3, n1 = (n + 1) / 3, n2 = n / 3, n02 = n0 + n2;
		int[] s12 = new int[n02 + 3], sa12 = new int[n02 + 3];
		for (int i = 0, j = 0; i < n + (n0 - n1); ++i) {
			if (0 != i % 3) {
				// 收集采样后缀的起点，必要时包含用于对齐的虚拟后缀。
				s12[j++] = i;
			}
		}
		// 先按三元组第三关键字稳定排序。
		radixPass(nums, s12, sa12, 2, n02, K);
		// 再按第二关键字稳定排序，保留第三关键字内部顺序。
		radixPass(nums, sa12, s12, 1, n02, K);
		// 最后按第一关键字排序，得到完整三元组字典序。
		radixPass(nums, s12, sa12, 0, n02, K);
		int name = 0, c0 = -1, c1 = -1, c2 = -1;
		for (int i = 0; i < n02; ++i) {
			// 仅当三元组不同才分配新名字，相同三元组暂时同名。
			if (c0 != nums[sa12[i]] || c1 != nums[sa12[i] + 1] || c2 != nums[sa12[i] + 2]) {
				name++;
				c0 = nums[sa12[i]];
				c1 = nums[sa12[i] + 1];
				c2 = nums[sa12[i] + 2];
			}
			if (1 == sa12[i] % 3) {
				// 余1组名称放在压缩数组前段。
				s12[sa12[i] / 3] = name;
			} else {
				// 余2组名称放在压缩数组后段，分组偏移为n0。
				s12[sa12[i] / 3 + n0] = name;
			}
		}
		// 名字尚不唯一，单靠前三字符不能排完，递归比较后续名称序列。
		if (name < n02) {
			sa12 = skew(s12, n02, name);
			for (int i = 0; i < n02; i++) {
				// 把递归得到的排列反转成排名，排名从1开始给哨兵保留0。
				s12[sa12[i]] = i + 1;
			}
		} else {
			for (int i = 0; i < n02; i++) {
				// 名字已唯一时不必递归，直接按名称还原采样排列。
				sa12[s12[i] - 1] = i;
			}
		}
		// 利用采样后缀排名，为余0的后缀建立排序位置。
		int[] s0 = new int[n0], sa0 = new int[n0];
		for (int i = 0, j = 0; i < n02; i++) {
			if (sa12[i] < n0) {
				// 余0后缀的下一位属于余1组，先沿已知后缀排名组织它们。
				s0[j++] = 3 * sa12[i];
			}
		}
		// 余0组再按首字符稳定排序，首字符相同时沿用后续排名。
		radixPass(nums, s0, sa0, 0, n0, K);
		int[] sa = new int[n];
		// 双指针归并余0组与采样组；t跳过对齐用的虚拟后缀。
		for (int p = 0, t = n0 - n1, k = 0; k < n; k++) {
			// 把压缩数组位置还原为原串余1或余2后缀起点。
			int i = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
			int j = sa0[p];
			// 余1对余0比较首字符加后续排名；余2对余0比较两字符加后续排名。
			if (sa12[t] < n0 ? leq(nums[i], s12[sa12[t] + n0], nums[j], s12[j / 3])
					: leq(nums[i], nums[i + 1], s12[sa12[t] - n0 + 1], nums[j], nums[j + 1], s12[j / 3 + n0])) {
				sa[k] = i;
				t++;
				// 采样组已经耗尽，余0组剩余元素可直接追加。
				if (t == n02) {
					for (k++; p < n0; p++, k++) {
						sa[k] = sa0[p];
					}
				}
			} else {
				sa[k] = j;
				p++;
				// 余0组耗尽，依采样排名追加剩余后缀。
				if (p == n0) {
					for (k++; t < n02; t++, k++) {
						sa[k] = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
					}
				}
			}
		}
		return sa;
	}

	private void radixPass(int[] nums, int[] input, int[] output, int offset, int n, int k) {
		int[] cnt = new int[k + 1];
		for (int i = 0; i < n; ++i) {
			// 统计本轮关键字频次，排序对象是后缀起点而非原数组元素。
			cnt[nums[input[i] + offset]]++;
		}
		for (int i = 0, sum = 0; i < cnt.length; ++i) {
			int t = cnt[i];
			// 把频次转成每个桶的起始写入位置。
			cnt[i] = sum;
			sum += t;
		}
		for (int i = 0; i < n; ++i) {
			// 按输入顺序进入对应桶，并推进写指针，使该趟计数排序稳定。
			output[cnt[nums[input[i] + offset]]++] = input[i];
		}
	}

	private boolean leq(int a1, int a2, int b1, int b2) {
		return a1 < b1 || (a1 == b1 && a2 <= b2);
	}

	private boolean leq(int a1, int a2, int a3, int b1, int b2, int b3) {
		return a1 < b1 || (a1 == b1 && leq(a2, a3, b2, b3));
	}

	private int[] rank() {
		int n = sa.length;
		int[] ans = new int[n];
		for (int i = 0; i < n; i++) {
			ans[sa[i]] = i;
		}
		return ans;
	}

	private int[] height(int[] s) {
		int n = s.length;
		int[] ans = new int[n];
		for (int i = 0, k = 0; i < n; ++i) {
			if (rank[i] != 0) {
				if (k > 0) {
					--k;
				}
				int j = sa[rank[i] - 1];
				while (i + k < n && j + k < n && s[i + k] == s[j + k]) {
					++k;
				}
				ans[rank[i]] = k;
			}
		}
		return ans;
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	public static int[] randomArray(int len, int maxValue) {
		int[] arr = new int[len];
		for (int i = 0; i < len; i++) {
			arr[i] = (int) (Math.random() * maxValue) + 1;
		}
		return arr;
	}

	// 为了测试
	public static void main(String[] args) {
		int len = 100000;
		int maxValue = 100;
		long start = System.currentTimeMillis();
		new DC3(randomArray(len, maxValue), maxValue);
		long end = System.currentTimeMillis();
		System.out.println("数据量 " + len + ", 运行时间 " + (end - start) + " ms");
	}
```

#### 题解

**为什么正确**

任意两类待比较后缀都可向后移至已排好的采样组，因此去掉至多两个开头字符后可用排名代表剩余整段。递归命名保留三元组字典序，稳定基数排序保持多关键字次序，最终归并使用的比较与完整后缀比较一致。

**复杂度**

紧凑整数值域Σ时，每层O(N+Σ)，递归规模约2N/3，总时间与空间O(N+Σ)。height的线性构造另O(N)；如果先用比较排序压缩任意整数，预处理另需O(N log N)。

**边界与易错点**

0留作末尾哨兵，真实字符需编码为正整数。sa、rank、height含义分别为第k小后缀起点、某起点排名、与前一名的最长公共前缀长度，不能互相当下标使用。


<a id="course-45"></a>

## 第 45 课：后缀数组的应用

### 45.1 插入字符串得到最大字典序

#### 题目

给定两个字符串，把第二个字符串整体插入第一个字符串的某个位置，返回能够得到的最大字典序结果。

**输入、输出与约束**

将s2整体插入s1任一字符间隙，可在两端；返回最大字典序结果，保留s1和s2内部次序。

**函数签名（课程入口）**

```java
public static String maxCombine(String s1, String s2);
public static int bestSplit(String s1, String s2, int first);
```

**示例**

```text
输入：s1="ab", s2="z"
输出："zab"
```

解释：在开头插入使第一个字符最大，优于azb与abz。

**出处与版本差异**

- [课程源码：class45/Code01_InsertS2MakeMostAlphabeticalOrder.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class45/Code01_InsertS2MakeMostAlphabeticalOrder.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

先将s1、分隔符、s2编码到同一个后缀数组。扫描s1，寻找第一个其后缀排名小于s2后缀的位置，这里开始才可能通过插入改善字典序。存在共同前缀时不能立即固定插点，bestSplit在这段可能并列的区间内比较插入后的前缀，选出最佳位置。

#### 代码答案

```java
package class45;

public class Code01_InsertS2MakeMostAlphabeticalOrder {

	// 暴力方法
	public static String right(String s1, String s2) {
		if (s1 == null || s1.length() == 0) {
			return s2;
		}
		if (s2 == null || s2.length() == 0) {
			return s1;
		}
		String p1 = s1 + s2;
		String p2 = s2 + s1;
		String ans = p1.compareTo(p2) > 0 ? p1 : p2;
		for (int end = 1; end < s1.length(); end++) {
			String cur = s1.substring(0, end) + s2 + s1.substring(end);
			if (cur.compareTo(ans) > 0) {
				ans = cur;
			}
		}
		return ans;
	}

	// 正式方法 O(N+M) + O(M^2)
	// N : s1长度
	// M : s2长度
	public static String maxCombine(String s1, String s2) {
		if (s1 == null || s1.length() == 0) {
			return s2;
		}
		if (s2 == null || s2.length() == 0) {
			return s1;
		}
		char[] str1 = s1.toCharArray();
		char[] str2 = s2.toCharArray();
		int N = str1.length;
		int M = str2.length;
		int min = str1[0];
		int max = str1[0];
		for (int i = 1; i < N; i++) {
			min = Math.min(min, str1[i]);
			max = Math.max(max, str1[i]);
		}
		for (int i = 0; i < M; i++) {
			min = Math.min(min, str2[i]);
			max = Math.max(max, str2[i]);
		}
		int[] all = new int[N + M + 1];
		int index = 0;
		for (int i = 0; i < N; i++) {
			all[index++] = str1[i] - min + 2;
		}
		// 用独立较小分隔符分开两串，真实字符统一平移到至少2。
		all[index++] = 1;
		for (int i = 0; i < M; i++) {
			all[index++] = str2[i] - min + 2;
		}
		DC3 dc3 = new DC3(all, max - min + 2);
		int[] rank = dc3.rank;
		int comp = N + 1;
		for (int i = 0; i < N; i++) {
			// 找到第一处s2后缀更大、可能通过插入提升字典序的位置。
			if (rank[i] < rank[comp]) {
				// 公共前缀内仍需精细比较候选插点，不能直接使用i。
				int best = bestSplit(s1, s2, i);
				return s1.substring(0, best) + s2 + s1.substring(best);
			}
		}
		return s1 + s2;
	}

	public static int bestSplit(String s1, String s2, int first) {
		int N = s1.length();
		int M = s2.length();
		int end = N;
		for (int i = first, j = 0; i < N && j < M; i++, j++) {
			if (s1.charAt(i) < s2.charAt(j)) {
				end = i;
				break;
			}
		}
		String bestPrefix = s2;
		int bestSplit = first;
		for (int i = first + 1, j = M - 1; i <= end; i++, j--) {
			String curPrefix = s1.substring(first, i) + s2.substring(0, j);
			if (curPrefix.compareTo(bestPrefix) >= 0) {
				bestPrefix = curPrefix;
				bestSplit = i;
			}
		}
		return bestSplit;
	}

	public static class DC3 {

		public int[] sa;

		public int[] rank;

		public DC3(int[] nums, int max) {
			sa = sa(nums, max);
			rank = rank();
		}

		private int[] sa(int[] nums, int max) {
			int n = nums.length;
			// 尾部预留3个零哨兵，读取三元组时无需逐字符判断越界。
			int[] arr = new int[n + 3];
			for (int i = 0; i < n; i++) {
				arr[i] = nums[i];
			}
			return skew(arr, n, max);
		}

		private int[] skew(int[] nums, int n, int K) {
			// 按起点模3计数，采样组由余1与余2位置组成。
			int n0 = (n + 2) / 3, n1 = (n + 1) / 3, n2 = n / 3, n02 = n0 + n2;
			int[] s12 = new int[n02 + 3], sa12 = new int[n02 + 3];
			for (int i = 0, j = 0; i < n + (n0 - n1); ++i) {
				if (0 != i % 3) {
					// 收集采样后缀的起点，必要时包含用于对齐的虚拟后缀。
					s12[j++] = i;
				}
			}
			// 先按三元组第三关键字稳定排序。
			radixPass(nums, s12, sa12, 2, n02, K);
			// 再按第二关键字稳定排序，保留第三关键字内部顺序。
			radixPass(nums, sa12, s12, 1, n02, K);
			// 最后按第一关键字排序，得到完整三元组字典序。
			radixPass(nums, s12, sa12, 0, n02, K);
			int name = 0, c0 = -1, c1 = -1, c2 = -1;
			for (int i = 0; i < n02; ++i) {
				// 仅当三元组不同才分配新名字，相同三元组暂时同名。
				if (c0 != nums[sa12[i]] || c1 != nums[sa12[i] + 1] || c2 != nums[sa12[i] + 2]) {
					name++;
					c0 = nums[sa12[i]];
					c1 = nums[sa12[i] + 1];
					c2 = nums[sa12[i] + 2];
				}
				if (1 == sa12[i] % 3) {
					// 余1组名称放在压缩数组前段。
					s12[sa12[i] / 3] = name;
				} else {
					// 余2组名称放在压缩数组后段，分组偏移为n0。
					s12[sa12[i] / 3 + n0] = name;
				}
			}
			// 名字尚不唯一，单靠前三字符不能排完，递归比较后续名称序列。
			if (name < n02) {
				sa12 = skew(s12, n02, name);
				for (int i = 0; i < n02; i++) {
					// 把递归得到的排列反转成排名，排名从1开始给哨兵保留0。
					s12[sa12[i]] = i + 1;
				}
			} else {
				for (int i = 0; i < n02; i++) {
					// 名字已唯一时不必递归，直接按名称还原采样排列。
					sa12[s12[i] - 1] = i;
				}
			}
			int[] s0 = new int[n0], sa0 = new int[n0];
			for (int i = 0, j = 0; i < n02; i++) {
				if (sa12[i] < n0) {
					// 余0后缀的下一位属于余1组，先沿已知后缀排名组织它们。
					s0[j++] = 3 * sa12[i];
				}
			}
			// 余0组再按首字符稳定排序，首字符相同时沿用后续排名。
			radixPass(nums, s0, sa0, 0, n0, K);
			int[] sa = new int[n];
			// 双指针归并余0组与采样组；t跳过对齐用的虚拟后缀。
			for (int p = 0, t = n0 - n1, k = 0; k < n; k++) {
				// 把压缩数组位置还原为原串余1或余2后缀起点。
				int i = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
				int j = sa0[p];
				// 余1对余0比较首字符加后续排名；余2对余0比较两字符加后续排名。
				if (sa12[t] < n0 ? leq(nums[i], s12[sa12[t] + n0], nums[j], s12[j / 3])
						: leq(nums[i], nums[i + 1], s12[sa12[t] - n0 + 1], nums[j], nums[j + 1], s12[j / 3 + n0])) {
					sa[k] = i;
					t++;
					// 采样组已经耗尽，余0组剩余元素可直接追加。
					if (t == n02) {
						for (k++; p < n0; p++, k++) {
							sa[k] = sa0[p];
						}
					}
				} else {
					sa[k] = j;
					p++;
					// 余0组耗尽，依采样排名追加剩余后缀。
					if (p == n0) {
						for (k++; t < n02; t++, k++) {
							sa[k] = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
						}
					}
				}
			}
			return sa;
		}

		private void radixPass(int[] nums, int[] input, int[] output, int offset, int n, int k) {
			int[] cnt = new int[k + 1];
			for (int i = 0; i < n; ++i) {
				// 统计本轮关键字频次，排序对象是后缀起点而非原数组元素。
				cnt[nums[input[i] + offset]]++;
			}
			for (int i = 0, sum = 0; i < cnt.length; ++i) {
				int t = cnt[i];
				// 把频次转成每个桶的起始写入位置。
				cnt[i] = sum;
				sum += t;
			}
			for (int i = 0; i < n; ++i) {
				// 按输入顺序进入对应桶，并推进写指针，使该趟计数排序稳定。
				output[cnt[nums[input[i] + offset]]++] = input[i];
			}
		}

		private boolean leq(int a1, int a2, int b1, int b2) {
			return a1 < b1 || (a1 == b1 && a2 <= b2);
		}

		private boolean leq(int a1, int a2, int a3, int b1, int b2, int b3) {
			return a1 < b1 || (a1 == b1 && leq(a2, a3, b2, b3));
		}

		private int[] rank() {
			int n = sa.length;
			int[] ans = new int[n];
			for (int i = 0; i < n; i++) {
				ans[sa[i]] = i;
			}
			return ans;
		}

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static String randomNumberString(int len, int range) {
		char[] str = new char[len];
		for (int i = 0; i < len; i++) {
			str[i] = (char) ((int) (Math.random() * range) + '0');
		}
		return String.valueOf(str);
	}

	// for test
	public static void main(String[] args) {
		int range = 10;
		int len = 50;
		int testTime = 100000;
		System.out.println("功能测试开始");
		for (int i = 0; i < testTime; i++) {
			int s1Len = (int) (Math.random() * len);
			int s2Len = (int) (Math.random() * len);
			String s1 = randomNumberString(s1Len, range);
			String s2 = randomNumberString(s2Len, range);
			String ans1 = right(s1, s2);
			String ans2 = maxCombine(s1, s2);
			if (!ans1.equals(ans2)) {
				System.out.println("Oops!");
				System.out.println(s1);
				System.out.println(s2);
				System.out.println(ans1);
				System.out.println(ans2);
				break;
			}
		}
		System.out.println("功能测试结束");

		System.out.println("==========");

		System.out.println("性能测试开始");
		int s1Len = 1000000;
		int s2Len = 500;
		String s1 = randomNumberString(s1Len, range);
		String s2 = randomNumberString(s2Len, range);
		long start = System.currentTimeMillis();
		maxCombine(s1, s2);
		long end = System.currentTimeMillis();
		System.out.println("运行时间 : " + (end - start) + " ms");
		System.out.println("性能测试结束");
	}
```

#### 题解

**为什么正确**

字典序由首个不同字符决定，之前s1后缀更大的位置提前插入s2不会改善答案。首次出现可改进处后，只有相等前缀导致多个插点仍需比较；bestSplit显式比较这段候选产生的同长度前缀，解决后缀排名不足以单独决定切点的问题。

**复杂度**

源码正式方法O(N+M+M²)时间，不是纯线性；O(N+M)后缀结构空间，比较过程中另有字符串分配。暴力枚举O(N(N+M))。

**边界与易错点**

拼接分隔符必须小于真实字符且不与之相同。不能看到较大s2后缀就直接在first处插入，公共前缀会影响最佳切点。

### 45.2 拼接两个数组得到最大数

#### 题目

给定两个数字数组和整数 `k`，保持各数组内部相对顺序，选出共 `k` 个数字组成字典序最大的结果。

**输入、输出与约束**

两个数字数组元素为0..9，0≤k≤N+M；返回最大字典序的长度k序列。

**函数签名（课程入口）**

```java
public static int[] maxNumber1(int[] nums1, int[] nums2, int k);
public static boolean preMoreThanLast(int[] nums1, int i, int[] nums2, int j);
public static int[] maxNumber2(int[] nums1, int[] nums2, int k);
public static boolean moreThan(int[] pre, int[] last);
public static int[][] getdp(int[] arr);
public static int[] maxPick(int[] arr, int[][] dp, int pick);
```

**示例**

```text
输入：nums1=[6,7], nums2=[6,0,4], k=5
输出：[6,7,6,0,4]
```

解释：两个首位均为6时，[6,7]后缀大于[6,0,4]，先取第一侧。

**出处与版本差异**

- [课程源码：class45/Code02_CreateMaximumNumber.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class45/Code02_CreateMaximumNumber.java)。
- [LeetCode 原题 321. 拼接最大数（Create Maximum Number）](https://leetcode.com/problems/create-maximum-number/)

#### 思路

枚举从第一个数组取i个、第二个取k-i个。各数组内保持相对次序选出该长度的最大子序列，再合并两条子序列：当前数字不同时选较大，相同时比较后续整段而不是任意挑一边。后缀数组版加速反复后缀比较。

#### 代码答案

```java
package class45;

// 测试链接: https://leetcode.com/problems/create-maximum-number/
public class Code02_CreateMaximumNumber {

	public static int[] maxNumber1(int[] nums1, int[] nums2, int k) {
		int len1 = nums1.length;
		int len2 = nums2.length;
		if (k < 0 || k > len1 + len2) {
			return null;
		}
		int[] res = new int[k];
		int[][] dp1 = getdp(nums1); // 生成dp1这个表，以后从nums1中，只要固定拿N个数，
		int[][] dp2 = getdp(nums2);
		// get1 从arr1里拿的数量
		// K - get1 从arr2里拿的数量
		// 枚举从第一数组取多少个；上下界保证两数组都取够且不越界。
		for (int get1 = Math.max(0, k - len2); get1 <= Math.min(k, len1); get1++) {
			// arr1 挑 get1个，怎么得到一个最优结果
			int[] pick1 = maxPick(nums1, dp1, get1);
			int[] pick2 = maxPick(nums2, dp2, k - get1);
			int[] merge = merge(pick1, pick2);
			// 比较完整候选串，保留所有取数分配中的最大结果。
			res = preMoreThanLast(res, 0, merge, 0) ? res : merge;
		}
		return res;
	}

	public static int[] merge(int[] nums1, int[] nums2) {
		int k = nums1.length + nums2.length;
		int[] ans = new int[k];
		for (int i = 0, j = 0, r = 0; r < k; ++r) {
			ans[r] = preMoreThanLast(nums1, i, nums2, j) ? nums1[i++] : nums2[j++];
		}
		return ans;
	}

	public static boolean preMoreThanLast(int[] nums1, int i, int[] nums2, int j) {
		// 两个候选头部相同不能立即决定，继续比较剩余后缀。
		while (i < nums1.length && j < nums2.length && nums1[i] == nums2[j]) {
			i++;
			j++;
		}
		return j == nums2.length || (i < nums1.length && nums1[i] > nums2[j]);
	}

	public static int[] maxNumber2(int[] nums1, int[] nums2, int k) {
		int len1 = nums1.length;
		int len2 = nums2.length;
		if (k < 0 || k > len1 + len2) {
			return null;
		}
		int[] res = new int[k];
		int[][] dp1 = getdp(nums1);
		int[][] dp2 = getdp(nums2);
		// 枚举从第一数组取多少个；上下界保证两数组都取够且不越界。
		for (int get1 = Math.max(0, k - len2); get1 <= Math.min(k, len1); get1++) {
			int[] pick1 = maxPick(nums1, dp1, get1);
			int[] pick2 = maxPick(nums2, dp2, k - get1);
			int[] merge = mergeBySuffixArray(pick1, pick2);
			res = moreThan(res, merge) ? res : merge;
		}
		return res;
	}

	public static boolean moreThan(int[] pre, int[] last) {
		int i = 0;
		int j = 0;
		while (i < pre.length && j < last.length && pre[i] == last[j]) {
			i++;
			j++;
		}
		return j == last.length || (i < pre.length && pre[i] > last[j]);
	}

	public static int[] mergeBySuffixArray(int[] nums1, int[] nums2) {
		int size1 = nums1.length;
		int size2 = nums2.length;
		int[] nums = new int[size1 + 1 + size2];
		for (int i = 0; i < size1; i++) {
			nums[i] = nums1[i] + 2;
		}
		nums[size1] = 1;
		for (int j = 0; j < size2; j++) {
			nums[j + size1 + 1] = nums2[j] + 2;
		}
		DC3 dc3 = new DC3(nums, 11);
		int[] rank = dc3.rank;
		int[] ans = new int[size1 + size2];
		int i = 0;
		int j = 0;
		int r = 0;
		while (i < size1 && j < size2) {
			ans[r++] = rank[i] > rank[j + size1 + 1] ? nums1[i++] : nums2[j++];
		}
		while (i < size1) {
			ans[r++] = nums1[i++];
		}
		while (j < size2) {
			ans[r++] = nums2[j++];
		}
		return ans;
	}

	public static class DC3 {

		public int[] sa;

		public int[] rank;

		public DC3(int[] nums, int max) {
			sa = sa(nums, max);
			rank = rank();
		}

		private int[] sa(int[] nums, int max) {
			int n = nums.length;
			// 尾部预留3个零哨兵，读取三元组时无需逐字符判断越界。
			int[] arr = new int[n + 3];
			for (int i = 0; i < n; i++) {
				arr[i] = nums[i];
			}
			return skew(arr, n, max);
		}

		private int[] skew(int[] nums, int n, int K) {
			// 按起点模3计数，采样组由余1与余2位置组成。
			int n0 = (n + 2) / 3, n1 = (n + 1) / 3, n2 = n / 3, n02 = n0 + n2;
			int[] s12 = new int[n02 + 3], sa12 = new int[n02 + 3];
			for (int i = 0, j = 0; i < n + (n0 - n1); ++i) {
				if (0 != i % 3) {
					// 收集采样后缀的起点，必要时包含用于对齐的虚拟后缀。
					s12[j++] = i;
				}
			}
			// 先按三元组第三关键字稳定排序。
			radixPass(nums, s12, sa12, 2, n02, K);
			// 再按第二关键字稳定排序，保留第三关键字内部顺序。
			radixPass(nums, sa12, s12, 1, n02, K);
			// 最后按第一关键字排序，得到完整三元组字典序。
			radixPass(nums, s12, sa12, 0, n02, K);
			int name = 0, c0 = -1, c1 = -1, c2 = -1;
			for (int i = 0; i < n02; ++i) {
				// 仅当三元组不同才分配新名字，相同三元组暂时同名。
				if (c0 != nums[sa12[i]] || c1 != nums[sa12[i] + 1] || c2 != nums[sa12[i] + 2]) {
					name++;
					c0 = nums[sa12[i]];
					c1 = nums[sa12[i] + 1];
					c2 = nums[sa12[i] + 2];
				}
				if (1 == sa12[i] % 3) {
					// 余1组名称放在压缩数组前段。
					s12[sa12[i] / 3] = name;
				} else {
					// 余2组名称放在压缩数组后段，分组偏移为n0。
					s12[sa12[i] / 3 + n0] = name;
				}
			}
			// 名字尚不唯一，单靠前三字符不能排完，递归比较后续名称序列。
			if (name < n02) {
				sa12 = skew(s12, n02, name);
				for (int i = 0; i < n02; i++) {
					// 把递归得到的排列反转成排名，排名从1开始给哨兵保留0。
					s12[sa12[i]] = i + 1;
				}
			} else {
				for (int i = 0; i < n02; i++) {
					// 名字已唯一时不必递归，直接按名称还原采样排列。
					sa12[s12[i] - 1] = i;
				}
			}
			int[] s0 = new int[n0], sa0 = new int[n0];
			for (int i = 0, j = 0; i < n02; i++) {
				if (sa12[i] < n0) {
					// 余0后缀的下一位属于余1组，先沿已知后缀排名组织它们。
					s0[j++] = 3 * sa12[i];
				}
			}
			// 余0组再按首字符稳定排序，首字符相同时沿用后续排名。
			radixPass(nums, s0, sa0, 0, n0, K);
			int[] sa = new int[n];
			// 双指针归并余0组与采样组；t跳过对齐用的虚拟后缀。
			for (int p = 0, t = n0 - n1, k = 0; k < n; k++) {
				// 把压缩数组位置还原为原串余1或余2后缀起点。
				int i = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
				int j = sa0[p];
				// 余1对余0比较首字符加后续排名；余2对余0比较两字符加后续排名。
				if (sa12[t] < n0 ? leq(nums[i], s12[sa12[t] + n0], nums[j], s12[j / 3])
						: leq(nums[i], nums[i + 1], s12[sa12[t] - n0 + 1], nums[j], nums[j + 1], s12[j / 3 + n0])) {
					sa[k] = i;
					t++;
					// 采样组已经耗尽，余0组剩余元素可直接追加。
					if (t == n02) {
						for (k++; p < n0; p++, k++) {
							sa[k] = sa0[p];
						}
					}
				} else {
					sa[k] = j;
					p++;
					// 余0组耗尽，依采样排名追加剩余后缀。
					if (p == n0) {
						for (k++; t < n02; t++, k++) {
							sa[k] = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
						}
					}
				}
			}
			return sa;
		}

		private void radixPass(int[] nums, int[] input, int[] output, int offset, int n, int k) {
			int[] cnt = new int[k + 1];
			for (int i = 0; i < n; ++i) {
				// 统计本轮关键字频次，排序对象是后缀起点而非原数组元素。
				cnt[nums[input[i] + offset]]++;
			}
			for (int i = 0, sum = 0; i < cnt.length; ++i) {
				int t = cnt[i];
				// 把频次转成每个桶的起始写入位置。
				cnt[i] = sum;
				sum += t;
			}
			for (int i = 0; i < n; ++i) {
				// 按输入顺序进入对应桶，并推进写指针，使该趟计数排序稳定。
				output[cnt[nums[input[i] + offset]]++] = input[i];
			}
		}

		private boolean leq(int a1, int a2, int b1, int b2) {
			return a1 < b1 || (a1 == b1 && a2 <= b2);
		}

		private boolean leq(int a1, int a2, int a3, int b1, int b2, int b3) {
			return a1 < b1 || (a1 == b1 && leq(a2, a3, b2, b3));
		}

		private int[] rank() {
			int n = sa.length;
			int[] ans = new int[n];
			for (int i = 0; i < n; i++) {
				ans[sa[i]] = i;
			}
			return ans;
		}

	}

	public static int[][] getdp(int[] arr) {
		int size = arr.length; // 0~N-1
		int pick = arr.length + 1; // 1 ~ N
		int[][] dp = new int[size][pick];
		// get 不从0开始，因为拿0个无意义
		for (int get = 1; get < pick; get++) { // 1 ~ N
			int maxIndex = size - get;
			// i~N-1
			for (int i = size - get; i >= 0; i--) {
				if (arr[i] >= arr[maxIndex]) {
					maxIndex = i;
				}
				dp[i][get] = maxIndex;
			}
		}
		return dp;
	}

	public static int[] maxPick(int[] arr, int[][] dp, int pick) {
		int[] res = new int[pick];
		for (int resIndex = 0, dpRow = 0; pick > 0; pick--, resIndex++) {
			res[resIndex] = arr[dp[dpRow][pick]];
			dpRow = dp[dpRow][pick] + 1;
		}
		return res;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：只能保持各自数组内部相对顺序，不能直接把全部数字排序。相等首数字需要比较整个剩余后缀。i范围同时满足0≤i≤N和0≤k-i≤M。

本条未附独立随机对数器。

#### 题解

**为什么正确**

固定两侧选取数量后，每侧使用字典序更小的子序列都不会改善最终最优交织，故可先分别取最大。合并时在两个可选后缀中选择字典序较大的首元素，首个能区分的后续位置决定贪心方向，保证当前前缀不劣。

**复杂度**

设可行分配数A≤min(N,M,k)+1。源码预处理选数位置表O(N²+M²)空间时间；朴素合并最坏每份O(k²)，DC3合并每份约O(k)，另需提取两子序列O(k)。

**边界与易错点**

只能保持各自数组内部相对顺序，不能直接把全部数字排序。相等首数字需要比较整个剩余后缀。i范围同时满足0≤i≤N和0≤k-i≤M。

### 45.3 用后缀数组求最长公共子串

#### 题目

给定两个字符串，返回二者最长公共子串的长度；公共子串中的字符必须连续。

**输入、输出与约束**

输入两个字符串；返回最长公共连续子串的长度，任一为空为0。

**函数签名（课程入口）**

```java
public static int lcs1(String s1, String s2);
public static int lcs2(String s1, String s2);
```

**示例**

```text
输入：s1="xabcd", s2="yabcz"
输出：3
```

解释：最长公共连续片段abc；d与z不同。

**出处与版本差异**

- [课程源码：class45/Code03_LongestCommonSubstringConquerByHeight.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class45/Code03_LongestCommonSubstringConquerByHeight.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

将两串用不出现的分隔符连接，构建后缀数组与height。只检查排序后相邻且来自不同原串的后缀，取它们最长公共前缀长度的最大值。基准版沿二维表或对角线计算连续匹配。

#### 代码答案

```java
package class45;

// 最长公共子串问题是面试常见题目之一
// 假设str1长度N，str2长度M
// 因为最优解的难度所限，一般在面试场上回答出O(N*M)的解法已经是比较优秀了
// 因为得到O(N*M)的解法，就已经需要用到动态规划了
// 但其实这个问题的最优解是O(N+M)，为了达到这个复杂度可是不容易
// 首先需要用到DC3算法得到后缀数组(sa)
// 进而用sa数组去生成height数组
// 而且在生成的时候，还有一个不回退的优化，都非常不容易理解
// 这就是后缀数组在面试算法中的地位 : 德高望重的噩梦
public class Code03_LongestCommonSubstringConquerByHeight {

	public static int lcs1(String s1, String s2) {
		if (s1 == null || s2 == null || s1.length() == 0 || s2.length() == 0) {
			return 0;
		}
		char[] str1 = s1.toCharArray();
		char[] str2 = s2.toCharArray();
		int row = 0;
		int col = str2.length - 1;
		int max = 0;
		while (row < str1.length) {
			int i = row;
			int j = col;
			int len = 0;
			while (i < str1.length && j < str2.length) {
				if (str1[i] != str2[j]) {
					len = 0;
				} else {
					len++;
				}
				if (len > max) {
					max = len;
				}
				i++;
				j++;
			}
			if (col > 0) {
				col--;
			} else {
				row++;
			}
		}
		return max;
	}

	public static int lcs2(String s1, String s2) {
		if (s1 == null || s2 == null || s1.length() == 0 || s2.length() == 0) {
			return 0;
		}
		char[] str1 = s1.toCharArray();
		char[] str2 = s2.toCharArray();
		int N = str1.length;
		int M = str2.length;
		int min = str1[0];
		int max = str1[0];
		for (int i = 1; i < N; i++) {
			min = Math.min(min, str1[i]);
			max = Math.max(max, str1[i]);
		}
		for (int i = 0; i < M; i++) {
			min = Math.min(min, str2[i]);
			max = Math.max(max, str2[i]);
		}
		int[] all = new int[N + M + 1];
		int index = 0;
		for (int i = 0; i < N; i++) {
			all[index++] = str1[i] - min + 2;
		}
		all[index++] = 1;
		for (int i = 0; i < M; i++) {
			all[index++] = str2[i] - min + 2;
		}
		DC3 dc3 = new DC3(all, max - min + 2);
		int n = all.length;
		int[] sa = dc3.sa;
		int[] height = dc3.height;
		int ans = 0;
		for (int i = 1; i < n; i++) {
			int Y = sa[i - 1];
			int X = sa[i];
			if (Math.min(X, Y) < N && Math.max(X, Y) > N) {
				// 当前排名后缀与前一名的最长公共前缀，仅异源相邻对可作为答案。
				ans = Math.max(ans, height[i]);
			}
		}
		return ans;
	}

	public static class DC3 {

		public int[] sa;

		public int[] rank;

		public int[] height;

		public DC3(int[] nums, int max) {
			sa = sa(nums, max);
			rank = rank();
			height = height(nums);
		}

		private int[] sa(int[] nums, int max) {
			int n = nums.length;
			// 尾部预留3个零哨兵，读取三元组时无需逐字符判断越界。
			int[] arr = new int[n + 3];
			for (int i = 0; i < n; i++) {
				arr[i] = nums[i];
			}
			return skew(arr, n, max);
		}

		private int[] skew(int[] nums, int n, int K) {
			// 按起点模3计数，采样组由余1与余2位置组成。
			int n0 = (n + 2) / 3, n1 = (n + 1) / 3, n2 = n / 3, n02 = n0 + n2;
			int[] s12 = new int[n02 + 3], sa12 = new int[n02 + 3];
			for (int i = 0, j = 0; i < n + (n0 - n1); ++i) {
				if (0 != i % 3) {
					// 收集采样后缀的起点，必要时包含用于对齐的虚拟后缀。
					s12[j++] = i;
				}
			}
			// 先按三元组第三关键字稳定排序。
			radixPass(nums, s12, sa12, 2, n02, K);
			// 再按第二关键字稳定排序，保留第三关键字内部顺序。
			radixPass(nums, sa12, s12, 1, n02, K);
			// 最后按第一关键字排序，得到完整三元组字典序。
			radixPass(nums, s12, sa12, 0, n02, K);
			int name = 0, c0 = -1, c1 = -1, c2 = -1;
			for (int i = 0; i < n02; ++i) {
				// 仅当三元组不同才分配新名字，相同三元组暂时同名。
				if (c0 != nums[sa12[i]] || c1 != nums[sa12[i] + 1] || c2 != nums[sa12[i] + 2]) {
					name++;
					c0 = nums[sa12[i]];
					c1 = nums[sa12[i] + 1];
					c2 = nums[sa12[i] + 2];
				}
				if (1 == sa12[i] % 3) {
					// 余1组名称放在压缩数组前段。
					s12[sa12[i] / 3] = name;
				} else {
					// 余2组名称放在压缩数组后段，分组偏移为n0。
					s12[sa12[i] / 3 + n0] = name;
				}
			}
			// 名字尚不唯一，单靠前三字符不能排完，递归比较后续名称序列。
			if (name < n02) {
				sa12 = skew(s12, n02, name);
				for (int i = 0; i < n02; i++) {
					// 把递归得到的排列反转成排名，排名从1开始给哨兵保留0。
					s12[sa12[i]] = i + 1;
				}
			} else {
				for (int i = 0; i < n02; i++) {
					// 名字已唯一时不必递归，直接按名称还原采样排列。
					sa12[s12[i] - 1] = i;
				}
			}
			int[] s0 = new int[n0], sa0 = new int[n0];
			for (int i = 0, j = 0; i < n02; i++) {
				if (sa12[i] < n0) {
					// 余0后缀的下一位属于余1组，先沿已知后缀排名组织它们。
					s0[j++] = 3 * sa12[i];
				}
			}
			// 余0组再按首字符稳定排序，首字符相同时沿用后续排名。
			radixPass(nums, s0, sa0, 0, n0, K);
			int[] sa = new int[n];
			// 双指针归并余0组与采样组；t跳过对齐用的虚拟后缀。
			for (int p = 0, t = n0 - n1, k = 0; k < n; k++) {
				// 把压缩数组位置还原为原串余1或余2后缀起点。
				int i = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
				int j = sa0[p];
				// 余1对余0比较首字符加后续排名；余2对余0比较两字符加后续排名。
				if (sa12[t] < n0 ? leq(nums[i], s12[sa12[t] + n0], nums[j], s12[j / 3])
						: leq(nums[i], nums[i + 1], s12[sa12[t] - n0 + 1], nums[j], nums[j + 1], s12[j / 3 + n0])) {
					sa[k] = i;
					t++;
					// 采样组已经耗尽，余0组剩余元素可直接追加。
					if (t == n02) {
						for (k++; p < n0; p++, k++) {
							sa[k] = sa0[p];
						}
					}
				} else {
					sa[k] = j;
					p++;
					// 余0组耗尽，依采样排名追加剩余后缀。
					if (p == n0) {
						for (k++; t < n02; t++, k++) {
							sa[k] = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
						}
					}
				}
			}
			return sa;
		}

		private void radixPass(int[] nums, int[] input, int[] output, int offset, int n, int k) {
			int[] cnt = new int[k + 1];
			for (int i = 0; i < n; ++i) {
				// 统计本轮关键字频次，排序对象是后缀起点而非原数组元素。
				cnt[nums[input[i] + offset]]++;
			}
			for (int i = 0, sum = 0; i < cnt.length; ++i) {
				int t = cnt[i];
				// 把频次转成每个桶的起始写入位置。
				cnt[i] = sum;
				sum += t;
			}
			for (int i = 0; i < n; ++i) {
				// 按输入顺序进入对应桶，并推进写指针，使该趟计数排序稳定。
				output[cnt[nums[input[i] + offset]]++] = input[i];
			}
		}

		private boolean leq(int a1, int a2, int b1, int b2) {
			return a1 < b1 || (a1 == b1 && a2 <= b2);
		}

		private boolean leq(int a1, int a2, int a3, int b1, int b2, int b3) {
			return a1 < b1 || (a1 == b1 && leq(a2, a3, b2, b3));
		}

		private int[] rank() {
			int n = sa.length;
			int[] ans = new int[n];
			for (int i = 0; i < n; i++) {
				ans[sa[i]] = i;
			}
			return ans;
		}

		private int[] height(int[] s) {
			int n = s.length;
			int[] ans = new int[n];
			// 依次求h[i] , k = 0
			for (int i = 0, k = 0; i < n; ++i) {
				if (rank[i] != 0) {
					if (k > 0) {
						--k;
					}
					int j = sa[rank[i] - 1];
					while (i + k < n && j + k < n && s[i + k] == s[j + k]) {
						++k;
					}
					// h[i] = k
					ans[rank[i]] = k;
				}
			}
			return ans;
		}

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static String randomNumberString(int len, int range) {
		char[] str = new char[len];
		for (int i = 0; i < len; i++) {
			str[i] = (char) ((int) (Math.random() * range) + 'a');
		}
		return String.valueOf(str);
	}

public static void main(String[] args) {
		int len = 30;
		int range = 5;
		int testTime = 100000;
		System.out.println("功能测试开始");
		for (int i = 0; i < testTime; i++) {
			int N1 = (int) (Math.random() * len);
			int N2 = (int) (Math.random() * len);
			String str1 = randomNumberString(N1, range);
			String str2 = randomNumberString(N2, range);
			int ans1 = lcs1(str1, str2);
			int ans2 = lcs2(str1, str2);
			if (ans1 != ans2) {
				System.out.println("Oops!");
			}
		}
		System.out.println("功能测试结束");
		System.out.println("==========");

		System.out.println("性能测试开始");
		len = 80000;
		range = 26;
		long start;
		long end;

		String str1 = randomNumberString(len, range);
		String str2 = randomNumberString(len, range);

		start = System.currentTimeMillis();
		int ans1 = lcs1(str1, str2);
		end = System.currentTimeMillis();
		System.out.println("方法1结果 : " + ans1 + " , 运行时间 : " + (end - start) + " ms");

		start = System.currentTimeMillis();
		int ans2 = lcs2(str1, str2);
		end = System.currentTimeMillis();
		System.out.println("方法2结果 : " + ans2 + " , 运行时间 : " + (end - start) + " ms");

		System.out.println("性能测试结束");

	}
```

#### 题解

**为什么正确**

任何共同子串都是某对异源后缀的公共前缀。共享某个前缀的后缀在排序中连续，这段中只要同时包含两种来源，必有一对相邻异源后缀，因此最大公共子串必能在这些相邻对中找到。

**复杂度**

DC3版在紧凑字符值域下O(N+M)时间、O(N+M)空间；动态规划/对角线基准O(NM)时间。

**边界与易错点**

要求连续，失配长度清零。跳过分隔符起点，比较后缀来源而不是起点大小本身。分隔符唯一可阻止公共前缀跨串边界。

### 45.4 最长重复子串

#### 题目

给定字符串 `s`，返回出现至少两次的最长子串长度，重复出现的位置可以重叠。

**输入、输出与约束**

输入字符串；返回出现至少两次且允许重叠的最长连续子串长度，无重复为0。

**函数签名（课程入口）**

```java
public static int longestRepeatingSubstring(String s);
```

**示例**

```text
输入：s="aaaa"
输出：3
```

解释：子串aaa从0、1开始出现，两次允许重叠。

**出处与版本差异**

- [课程源码：class45/Code04_LongestRepeatingSubstring.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class45/Code04_LongestRepeatingSubstring.java)。
- [LeetCode 原题 1062. 最长重复子串（Longest Repeating Substring）](https://leetcode.com/problems/longest-repeating-substring/)

#### 思路

将所有后缀排序并计算相邻公共前缀height。任意重复子串至少是两个不同起点后缀的公共前缀，取height最大值就得到最长重复子串长度。

#### 代码答案

```java
package class45;

// 一个非常经典的题
// 这道题课上没有讲
// 后缀数组的模版题
// 需要学会DC3算法生成后缀数组
// 需要学会课上讲的如何生成高度数组
// 时间复杂度O(N)，连官方题解都没有做到的时间复杂度，但这才是最优解
// 测试链接 : https://leetcode.cn/problems/longest-repeating-substring/
public class Code04_LongestRepeatingSubstring {

	public static int longestRepeatingSubstring(String s) {
		if (s == null || s.length() == 0) {
			return 0;
		}
		char[] str = s.toCharArray();
		int n = str.length;
		int min = str[0];
		int max = str[0];
		for (int i = 1; i < n; i++) {
			min = Math.min(min, str[i]);
			max = Math.max(max, str[i]);
		}
		int[] all = new int[n];
		for (int i = 0; i < n; i++) {
			all[i] = str[i] - min + 1;
		}
		DC3 dc3 = new DC3(all, max - min + 1);
		int ans = 0;
		for (int i = 1; i < n; i++) {
			// 相邻有序后缀的最长公共前缀最大值，就是允许重叠的最长重复子串长度。
			ans = Math.max(ans, dc3.height[i]);
		}
		return ans;
	}

	public static class DC3 {
		public int[] sa;
		public int[] rank;
		public int[] height;

		public DC3(int[] nums, int max) {
			sa = sa(nums, max);
			rank = rank();
			height = height(nums);
		}

		private int[] sa(int[] nums, int max) {
			int n = nums.length;
			// 尾部预留3个零哨兵，读取三元组时无需逐字符判断越界。
			int[] arr = new int[n + 3];
			for (int i = 0; i < n; i++) {
				arr[i] = nums[i];
			}
			return skew(arr, n, max);
		}

		private int[] skew(int[] nums, int n, int K) {
			// 按起点模3计数，采样组由余1与余2位置组成。
			int n0 = (n + 2) / 3, n1 = (n + 1) / 3, n2 = n / 3, n02 = n0 + n2;
			int[] s12 = new int[n02 + 3], sa12 = new int[n02 + 3];
			for (int i = 0, j = 0; i < n + (n0 - n1); ++i) {
				if (0 != i % 3) {
					// 收集采样后缀的起点，必要时包含用于对齐的虚拟后缀。
					s12[j++] = i;
				}
			}
			// 先按三元组第三关键字稳定排序。
			radixPass(nums, s12, sa12, 2, n02, K);
			// 再按第二关键字稳定排序，保留第三关键字内部顺序。
			radixPass(nums, sa12, s12, 1, n02, K);
			// 最后按第一关键字排序，得到完整三元组字典序。
			radixPass(nums, s12, sa12, 0, n02, K);
			int name = 0, c0 = -1, c1 = -1, c2 = -1;
			for (int i = 0; i < n02; ++i) {
				// 仅当三元组不同才分配新名字，相同三元组暂时同名。
				if (c0 != nums[sa12[i]] || c1 != nums[sa12[i] + 1] || c2 != nums[sa12[i] + 2]) {
					name++;
					c0 = nums[sa12[i]];
					c1 = nums[sa12[i] + 1];
					c2 = nums[sa12[i] + 2];
				}
				if (1 == sa12[i] % 3) {
					// 余1组名称放在压缩数组前段。
					s12[sa12[i] / 3] = name;
				} else {
					// 余2组名称放在压缩数组后段，分组偏移为n0。
					s12[sa12[i] / 3 + n0] = name;
				}
			}
			// 名字尚不唯一，单靠前三字符不能排完，递归比较后续名称序列。
			if (name < n02) {
				sa12 = skew(s12, n02, name);
				for (int i = 0; i < n02; i++) {
					// 把递归得到的排列反转成排名，排名从1开始给哨兵保留0。
					s12[sa12[i]] = i + 1;
				}
			} else {
				for (int i = 0; i < n02; i++) {
					// 名字已唯一时不必递归，直接按名称还原采样排列。
					sa12[s12[i] - 1] = i;
				}
			}
			int[] s0 = new int[n0], sa0 = new int[n0];
			for (int i = 0, j = 0; i < n02; i++) {
				if (sa12[i] < n0) {
					// 余0后缀的下一位属于余1组，先沿已知后缀排名组织它们。
					s0[j++] = 3 * sa12[i];
				}
			}
			// 余0组再按首字符稳定排序，首字符相同时沿用后续排名。
			radixPass(nums, s0, sa0, 0, n0, K);
			int[] sa = new int[n];
			// 双指针归并余0组与采样组；t跳过对齐用的虚拟后缀。
			for (int p = 0, t = n0 - n1, k = 0; k < n; k++) {
				// 把压缩数组位置还原为原串余1或余2后缀起点。
				int i = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
				int j = sa0[p];
				// 余1对余0比较首字符加后续排名；余2对余0比较两字符加后续排名。
				if (sa12[t] < n0 ? leq(nums[i], s12[sa12[t] + n0], nums[j], s12[j / 3])
						: leq(nums[i], nums[i + 1], s12[sa12[t] - n0 + 1], nums[j], nums[j + 1], s12[j / 3 + n0])) {
					sa[k] = i;
					t++;
					// 采样组已经耗尽，余0组剩余元素可直接追加。
					if (t == n02) {
						for (k++; p < n0; p++, k++) {
							sa[k] = sa0[p];
						}
					}
				} else {
					sa[k] = j;
					p++;
					// 余0组耗尽，依采样排名追加剩余后缀。
					if (p == n0) {
						for (k++; t < n02; t++, k++) {
							sa[k] = sa12[t] < n0 ? sa12[t] * 3 + 1 : (sa12[t] - n0) * 3 + 2;
						}
					}
				}
			}
			return sa;
		}

		private void radixPass(int[] nums, int[] input, int[] output, int offset, int n, int k) {
			int[] cnt = new int[k + 1];
			for (int i = 0; i < n; ++i) {
				// 统计本轮关键字频次，排序对象是后缀起点而非原数组元素。
				cnt[nums[input[i] + offset]]++;
			}
			for (int i = 0, sum = 0; i < cnt.length; ++i) {
				int t = cnt[i];
				// 把频次转成每个桶的起始写入位置。
				cnt[i] = sum;
				sum += t;
			}
			for (int i = 0; i < n; ++i) {
				// 按输入顺序进入对应桶，并推进写指针，使该趟计数排序稳定。
				output[cnt[nums[input[i] + offset]]++] = input[i];
			}
		}

		private boolean leq(int a1, int a2, int b1, int b2) {
			return a1 < b1 || (a1 == b1 && a2 <= b2);
		}

		private boolean leq(int a1, int a2, int a3, int b1, int b2, int b3) {
			return a1 < b1 || (a1 == b1 && leq(a2, a3, b2, b3));
		}

		private int[] rank() {
			int n = sa.length;
			int[] ans = new int[n];
			for (int i = 0; i < n; i++) {
				ans[sa[i]] = i;
			}
			return ans;
		}

		private int[] height(int[] s) {
			int n = s.length;
			int[] ans = new int[n];
			for (int i = 0, k = 0; i < n; ++i) {
				if (rank[i] != 0) {
					if (k > 0) {
						--k;
					}
					int j = sa[rank[i] - 1];
					while (i + k < n && j + k < n && s[i + k] == s[j + k]) {
						++k;
					}
					ans[rank[i]] = k;
				}
			}
			return ans;
		}

	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试, 不用提交
	public static String randomString(int n, int r) {
		char[] str = new char[n];
		for (int i = 0; i < n; i++) {
			str[i] = (char) ((int) (Math.random() * r) + 'a');
		}
		return String.valueOf(str);
	}

	// 为了测试, 不用提交
	public static void main(String[] args) {
		int n = 500000;
		int r = 3;
		long start = System.currentTimeMillis();
		longestRepeatingSubstring(randomString(n, r));
		long end = System.currentTimeMillis();
		System.out.println("字符长度为 " + n + ", 字符种类数为 " + r + " 时");
		System.out.println("求最长重复子串的运行时间 : " + (end - start) + " 毫秒");
	}
```

#### 题解

**为什么正确**

若两个非相邻后缀共享长度L前缀，它们之间的全部后缀也共享该前缀，所以至少有一对相邻后缀公共前缀≥L。因此只检查相邻项不会漏掉非相邻后缀带来的更长重复。

**复杂度**

当前后缀数组构造加height扫描，在紧凑字符编码下O(N)时间、O(N)空间。

**边界与易错点**

允许两次出现重叠，例如aaaa的aaa出现在0和1。若题目要求不重叠，仅取最大height不够，还需约束起点距离。


<a id="course-46"></a>

## 第 46 课：区间动态规划与哈夫曼编码

### 46.1 戳气球的最大得分

#### 题目

给定气球分值数组，每次戳破一个气球获得相邻分值乘积，返回戳破全部气球可获得的最大分数。

**输入、输出与约束**

输入非负气球数值，左右越界邻居视为1；返回最大总得分。

**函数签名（课程入口）**

```java
public static int maxCoins0(int[] arr);
public static int maxCoins1(int[] arr);
public static int maxCoins2(int[] arr);
```

**示例**

```text
输入：nums=[3,1,5,8]
输出：167
```

解释：可按1、5、3、8的顺序戳，收益依次15、120、24、8。

**出处与版本差异**

- [课程源码：class46/Code01_BurstBalloons.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class46/Code01_BurstBalloons.java)。
- [LeetCode 原题 312. 戳气球（Burst Balloons）](https://leetcode.com/problems/burst-balloons/)

#### 思路

两端补虚拟气球1。dp[L][R]表示戳完原区间L..R、但两侧边界气球仍保留时的最大分数。枚举最后戳破的气球i，左右两段先独立戳完，最后一击得分为arr[L-1]×arr[i]×arr[R+1]。

#### 代码答案

```java
package class46;

// 本题测试链接 : https://leetcode.com/problems/burst-balloons/
public class Code01_BurstBalloons {

	public static int maxCoins0(int[] arr) {
		// [3,2,1,3]
		// [1,3,2,1,3,1]
		int N = arr.length;
		int[] help = new int[N + 2];
		for (int i = 0; i < N; i++) {
			help[i + 1] = arr[i];
		}
		// 左虚拟边界统一处理最左气球无邻居的情况。
		help[0] = 1;
		help[N + 1] = 1;
		return func(help, 1, N);
	}

	// L-1位置，和R+1位置，永远不越界，并且，[L-1] 和 [R+1] 一定没爆呢！
	// 返回，arr[L...R]打爆所有气球，最大得分是什么
	public static int func(int[] arr, int L, int R) {
		if (L == R) {
			return arr[L - 1] * arr[L] * arr[R + 1];
		}
		// 尝试每一种情况，最后打爆的气球，是什么位置
		// L...R
		// L位置的气球，最后打爆
		int max = func(arr, L + 1, R) + arr[L - 1] * arr[L] * arr[R + 1];
		// R位置的气球，最后打爆
		max = Math.max(max, func(arr, L, R - 1) + arr[L - 1] * arr[R] * arr[R + 1]);
		// 尝试所有L...R，中间的位置，(L,R)
		for (int i = L + 1; i < R; i++) {
			// i位置的气球，最后打爆
			int left = func(arr, L, i - 1);
			int right = func(arr, i + 1, R);
			// i最后被戳时区间内部其他气球都消失，邻居正是区间外两边界。
			int last = arr[L - 1] * arr[i] * arr[R + 1];
			int cur = left + right + last;
			max = Math.max(max, cur);
		}
		return max;
	}

	public static int maxCoins1(int[] arr) {
		if (arr == null || arr.length == 0) {
			return 0;
		}
		if (arr.length == 1) {
			return arr[0];
		}
		int N = arr.length;
		int[] help = new int[N + 2];
		// 左虚拟边界统一处理最左气球无邻居的情况。
		help[0] = 1;
		help[N + 1] = 1;
		for (int i = 0; i < N; i++) {
			help[i + 1] = arr[i];
		}
		return process(help, 1, N);
	}

	// 打爆arr[L..R]范围上的所有气球，返回最大的分数
	// 假设arr[L-1]和arr[R+1]一定没有被打爆
	public static int process(int[] arr, int L, int R) {
		if (L == R) {// 如果arr[L..R]范围上只有一个气球，直接打爆即可
			return arr[L - 1] * arr[L] * arr[R + 1];
		}
		// 最后打爆arr[L]的方案，和最后打爆arr[R]的方案，先比较一下
		int max = Math.max(arr[L - 1] * arr[L] * arr[R + 1] + process(arr, L + 1, R),
				arr[L - 1] * arr[R] * arr[R + 1] + process(arr, L, R - 1));
		// 尝试中间位置的气球最后被打爆的每一种方案
		for (int i = L + 1; i < R; i++) {
			// i最后被戳时区间内部其他气球都消失，邻居正是区间外两边界。
			max = Math.max(max, arr[L - 1] * arr[i] * arr[R + 1] + process(arr, L, i - 1) + process(arr, i + 1, R));
		}
		return max;
	}

	public static int maxCoins2(int[] arr) {
		if (arr == null || arr.length == 0) {
			return 0;
		}
		if (arr.length == 1) {
			return arr[0];
		}
		int N = arr.length;
		int[] help = new int[N + 2];
		// 左虚拟边界统一处理最左气球无邻居的情况。
		help[0] = 1;
		help[N + 1] = 1;
		for (int i = 0; i < N; i++) {
			help[i + 1] = arr[i];
		}
		int[][] dp = new int[N + 2][N + 2];
		for (int i = 1; i <= N; i++) {
			dp[i][i] = help[i - 1] * help[i] * help[i + 1];
		}
		for (int L = N; L >= 1; L--) {
			for (int R = L + 1; R <= N; R++) {
				int ans = help[L - 1] * help[L] * help[R + 1] + dp[L + 1][R];
				ans = Math.max(ans, help[L - 1] * help[R] * help[R + 1] + dp[L][R - 1]);
				for (int i = L + 1; i < R; i++) {
					ans = Math.max(ans, help[L - 1] * help[i] * help[R + 1] + dp[L][i - 1] + dp[i + 1][R]);
				}
				dp[L][R] = ans;
			}
		}
		return dp[1][N];
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：最后收益用区间外边界，不是原i-1、i+1。虚拟1不参与待戳区间。乘积和总分需与int范围相容。

本条未附独立随机对数器。

#### 题解

**为什么正确**

若枚举先戳谁，邻居会随过程改变；枚举最后戳谁时，区间外两个边界一定仍在，因此最后收益固定，左右内部互不影响。每种顺序都有唯一最后气球，分类完整。

**复杂度**

动态规划O(N³)时间、O(N²)空间；无缓存递归指数增长。

**边界与易错点**

最后收益用区间外边界，不是原i-1、i+1。虚拟1不参与待戳区间。乘积和总分需与int范围相容。

### 46.2 移除盒子的最大得分

#### 题目

给定一排带颜色的盒子，每次删除连续同色的 `k` 个盒子得到 `k²` 分，返回删除全部盒子的最大得分。

**输入、输出与约束**

输入颜色序列；每次删除连续同色k个得k²，返回最大总分。

**函数签名（课程入口）**

```java
public static int removeBoxes1(int[] boxes);
public static int removeBoxes2(int[] boxes);
```

**示例**

```text
输入：boxes=[1,2,1]
输出：5
```

解释：先删2得1，再把两个1一起删得4。

**出处与版本差异**

- [课程源码：class46/Code02_RemoveBoxes.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class46/Code02_RemoveBoxes.java)。
- [LeetCode 原题 546. 移除盒子（Remove Boxes）](https://leetcode.com/problems/remove-boxes/)

#### 思路

定义f(L,R,K)：区间L..R左边已经接着K个与boxes[L]同色盒子时的最大分数。可立即把首色组一起删，得(K+1)²再处理后缀；也可先清空到某个后续同色位置之间的盒子，使该位置接上这K+1个，再递归。

#### 代码答案

```java
package class46;

// 本题测试链接 : https://leetcode.com/problems/remove-boxes/
public class Code02_RemoveBoxes {

	// arr[L...R]消除，而且前面跟着K个arr[L]这个数
	// 返回：所有东西都消掉，最大得分
	public static int func1(int[] arr, int L, int R, int K) {
		if (L > R) {
			return 0;
		}
		// 立即删除当前盒子与左侧已携带K个同色盒子的总收益。
		// 中间已清空时，把当前同色组数量传给后面的同色位置。
		int ans = func1(arr, L + 1, R, 0) + (K + 1) * (K + 1);

		// 前面的K个X，和arr[L]数，合在一起了，现在有K+1个arr[L]位置的数
		for (int i = L + 1; i <= R; i++) {
			if (arr[i] == arr[L]) {
				// 中间已清空时，把当前同色组数量传给后面的同色位置。
				ans = Math.max(ans, func1(arr, L + 1, i - 1, 0) + func1(arr, i, R, K + 1));
			}
		}
		return ans;
	}

	public static int removeBoxes1(int[] boxes) {
		int N = boxes.length;
		int[][][] dp = new int[N][N][N];
		int ans = process1(boxes, 0, N - 1, 0, dp);
		return ans;
	}

	public static int process1(int[] boxes, int L, int R, int K, int[][][] dp) {
		if (L > R) {
			return 0;
		}
		if (dp[L][R][K] > 0) {
			return dp[L][R][K];
		}
		// 立即删除当前盒子与左侧已携带K个同色盒子的总收益。
		// 中间已清空时，把当前同色组数量传给后面的同色位置。
		int ans = process1(boxes, L + 1, R, 0, dp) + (K + 1) * (K + 1);
		for (int i = L + 1; i <= R; i++) {
			if (boxes[i] == boxes[L]) {
				// 中间已清空时，把当前同色组数量传给后面的同色位置。
				ans = Math.max(ans, process1(boxes, L + 1, i - 1, 0, dp) + process1(boxes, i, R, K + 1, dp));
			}
		}
		dp[L][R][K] = ans;
		return ans;
	}

	public static int removeBoxes2(int[] boxes) {
		int N = boxes.length;
		int[][][] dp = new int[N][N][N];
		int ans = process2(boxes, 0, N - 1, 0, dp);
		return ans;
	}

	public static int process2(int[] boxes, int L, int R, int K, int[][][] dp) {
		if (L > R) {
			return 0;
		}
		if (dp[L][R][K] > 0) {
			return dp[L][R][K];
		}
		// 找到开头，
		// 1,1,1,1,1,5
		// 3 4 5 6 7 8
		//         !
		int last = L;
		while (last + 1 <= R && boxes[last + 1] == boxes[L]) {
			last++;
		}
		// K个1     (K + last - L) last
		int pre = K + last - L;
		int ans = (pre + 1) * (pre + 1) + process2(boxes, last + 1, R, 0, dp);
		for (int i = last + 2; i <= R; i++) {
			if (boxes[i] == boxes[L] && boxes[i - 1] != boxes[L]) {
				ans = Math.max(ans, process2(boxes, last + 1, i - 1, 0, dp) + process2(boxes, i, R, pre + 1, dp));
			}
		}
		dp[L][R][K] = ans;
		return ans;
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：K不是颜色编号，而是左侧已接上的同色数量。只做二维dp会丢掉未来合并收益。删除得分是数量平方，因此贪心先删最大现有段也未必最优。

本条未附独立随机对数器。

#### 题解

**为什么正确**

最优方案对首色组要么在未接触后续同色前删掉，要么等待与某个后续同色组合并。后一情况中间区间必须先清空，之后的收益只依赖同色数量K+1，故三维状态准确保留了区间外影响。

**复杂度**

记忆化状态O(N³)，每状态枚举后续合并位置O(N)，保守时间O(N⁴)、空间O(N³)；连续同色压缩可减少实际状态。

**边界与易错点**

K不是颜色编号，而是左侧已接上的同色数量。只做二维dp会丢掉未来合并收益。删除得分是数量平方，因此贪心先删最大现有段也未必最优。

### 46.3 删除相邻相同字符后的最短长度

#### 题目

给定字符串，可反复删除任意一段长度至少为 2 的连续相同字符，返回最终可能得到的最短长度。

**输入、输出与约束**

输入字符串；允许反复删除长度至少2的同字符连续段，返回可达到的最短剩余长度。

**函数签名（课程入口）**

```java
public static int restMin1(String s);
public static boolean canDelete(String s);
public static int restMin2(String s);
public static int restMin3(String s);
public static int dpProcess(char[] str, int L, int R, boolean has, int[][][] dp);
```

**示例**

```text
输入：s="abba"
输出：0
```

解释：先删bb，剩aa，再删aa。

**出处与版本差异**

- [课程源码：class46/Code03_DeleteAdjacentSameCharacter.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class46/Code03_DeleteAdjacentSameCharacter.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

状态f(L,R,has)表示处理区间且左侧是否已经有一个同字符可与首段相连。可直接删除长度≥2的首段再处理后缀；也可先把中间删空，让后续同字符接上首段。目标改为最小剩余字符数，而不是最大得分。

#### 代码答案

```java
package class46;

// 如果一个字符相邻的位置没有相同字符，那么这个位置的字符出现不能被消掉
// 比如:"ab"，其中a和b都不能被消掉
// 如果一个字符相邻的位置有相同字符，就可以一起消掉
// 比如:"abbbc"，中间一串的b是可以被消掉的，消除之后剩下"ac"
// 某些字符如果消掉了，剩下的字符认为重新靠在一起
// 给定一个字符串，你可以决定每一步消除的顺序，目标是请尽可能多的消掉字符，返回最少的剩余字符数量
// 比如："aacca", 如果先消掉最左侧的"aa"，那么将剩下"cca"，然后把"cc"消掉，剩下的"a"将无法再消除，返回1
// 但是如果先消掉中间的"cc"，那么将剩下"aaa"，最后都消掉就一个字符也不剩了，返回0，这才是最优解。
// 再比如："baaccabb"，
// 如果先消除最左侧的两个a，剩下"bccabb"，
// 如果再消除最左侧的两个c，剩下"babb"，
// 最后消除最右侧的两个b，剩下"ba"无法再消除，返回2
// 而最优策略是：
// 如果先消除中间的两个c，剩下"baaabb"，
// 如果再消除中间的三个a，剩下"bbb"，
// 最后消除三个b，不留下任何字符，返回0，这才是最优解
public class Code03_DeleteAdjacentSameCharacter {

	// 暴力解
	public static int restMin1(String s) {
		if (s == null) {
			return 0;
		}
		if (s.length() < 2) {
			return s.length();
		}
		int minLen = s.length();
		for (int L = 0; L < s.length(); L++) {
			for (int R = L + 1; R < s.length(); R++) {
				if (canDelete(s.substring(L, R + 1))) {
					minLen = Math.min(minLen, restMin1(s.substring(0, L) + s.substring(R + 1, s.length())));
				}
			}
		}
		return minLen;
	}

	public static boolean canDelete(String s) {
		char[] str = s.toCharArray();
		for (int i = 1; i < str.length; i++) {
			if (str[i - 1] != str[i]) {
				return false;
			}
		}
		return true;
	}

	// 优良尝试的暴力递归版本
	public static int restMin2(String s) {
		if (s == null) {
			return 0;
		}
		if (s.length() < 2) {
			return s.length();
		}
		char[] str = s.toCharArray();
		return process(str, 0, str.length - 1, false);
	}

	// str[L...R] 前面有没有跟着[L]字符，has T 有 F 无
	// L,R,has
	// 最少能剩多少字符，消不了
	public static int process(char[] str, int L, int R, boolean has) {
		if (L > R) {
			return 0;
		}
		if (L == R) {
			return has ? 0 : 1;
		}
		int index = L;
		// 把区间左侧携带的同色存在信息计入当前可删除组规模。
		int K = has ? 1 : 0;
		while (index <= R && str[index] == str[L]) {
			K++;
			index++;
		}
		// index表示，第一个不是[L]字符的位置
		int way1 = (K > 1 ? 0 : 1) + process(str, index, R, false);
		int way2 = Integer.MAX_VALUE;
		for (int split = index; split <= R; split++) {
			if (str[split] == str[L] && str[split] != str[split - 1]) {
				if (process(str, index, split - 1, false) == 0) {
					way2 = Math.min(way2, process(str, split, R, K != 0));
				}
			}
		}
		return Math.min(way1, way2);
	}

	// 优良尝试的动态规划版本
	public static int restMin3(String s) {
		if (s == null) {
			return 0;
		}
		if (s.length() < 2) {
			return s.length();
		}
		char[] str = s.toCharArray();
		int N = str.length;
		int[][][] dp = new int[N][N][2];
		for (int i = 0; i < N; i++) {
			for (int j = 0; j < N; j++) {
				for (int k = 0; k < 2; k++) {
					dp[i][j][k] = -1;
				}
			}
		}
		return dpProcess(str, 0, N - 1, false, dp);
	}

	public static int dpProcess(char[] str, int L, int R, boolean has, int[][][] dp) {
		if (L > R) {
			return 0;
		}
		// 把区间左侧携带的同色存在信息计入当前可删除组规模。
		int K = has ? 1 : 0;
		if (dp[L][R][K] != -1) {
			return dp[L][R][K];
		}
		int ans = 0;
		if (L == R) {
			ans = (K == 0 ? 1 : 0);
		} else {
			int index = L;
			int all = K;
			while (index <= R && str[index] == str[L]) {
				all++;
				index++;
			}
			int way1 = (all > 1 ? 0 : 1) + dpProcess(str, index, R, false, dp);
			int way2 = Integer.MAX_VALUE;
			for (int split = index; split <= R; split++) {
				if (str[split] == str[L] && str[split] != str[split - 1]) {
					if (dpProcess(str, index, split - 1, false, dp) == 0) {
						way2 = Math.min(way2, dpProcess(str, split, R, all > 0, dp));
					}
				}
			}
			ans = Math.min(way1, way2);
		}
		dp[L][R][K] = ans;
		return ans;
	}

	public static String randomString(int len, int variety) {
		char[] str = new char[len];
		for (int i = 0; i < len; i++) {
			str[i] = (char) ((int) (Math.random() * variety) + 'a');
		}
		return String.valueOf(str);
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int maxLen = 16;
		int variety = 3;
		int testTime = 100000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int len = (int) (Math.random() * maxLen);
			String str = randomString(len, variety);
			int ans1 = restMin1(str);
			int ans2 = restMin2(str);
			int ans3 = restMin3(str);
			if (ans1 != ans2 || ans1 != ans3) {
				System.out.println(str);
				System.out.println(ans1);
				System.out.println(ans2);
				System.out.println(ans3);
				System.out.println("出错了！");
				break;
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

首段要与后面的同字符合并，中间字符必须全部消失，这由子问题结果等于0精确检验。否则首段只能独立保留一个或删除整段。分类涵盖了首段第一次被处理的所有方式。

**复杂度**

带记忆的布尔携带状态有O(N²)个，每状态枚举分隔点O(N)，时间O(N³)、空间O(N²)；暴力枚举删除序列更高。

**边界与易错点**

只能删除连续至少两个相同字符，单字符不能直接删。中间区间“最少剩1”不等价于可删空，不能允许跨越它合并。

### 46.4 长度不超过 M 的最大子数组和

#### 题目

给定整数数组和正整数 `M`，返回长度不超过 `M` 的非空子数组最大累加和。

**输入、输出与约束**

输入整数数组及M≥1；返回长度1..M的非空子数组最大累加和。

**函数签名（课程入口）**

```java
public static int maxSum(int[] arr, int M);
```

**示例**

```text
输入：arr=[-2,3,-1,4], M=2
输出：4
```

解释：单选4或区间[-1,4]中，最大为4；长度3的[3,-1,4]虽和6但不合法。

**出处与版本差异**

- [课程源码：class46/Code04_MaxSumLengthNoMore.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class46/Code04_MaxSumLengthNoMore.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

用前缀和P把任意区间和写成P[R]-P[L]。固定右端时，合法左前缀下标范围为[R-M,R-1]，要最大化差就取其中最小前缀；或等价固定左前缀维护未来M个右前缀的最大值。课程使用单调队列滚动合法前缀窗口。

#### 代码答案

```java
package class46;

import java.util.LinkedList;

// 给定一个数组arr，和一个正数M
// 返回在子数组长度不大于M的情况下，最大的子数组累加和
public class Code04_MaxSumLengthNoMore {

	// O(N^2)的解法，暴力解，用作对数器
	public static int test(int[] arr, int M) {
		if (arr == null || arr.length == 0 || M < 1) {
			return 0;
		}
		int N = arr.length;
		int max = Integer.MIN_VALUE;
		for (int L = 0; L < N; L++) {
			int sum = 0;
			for (int R = L; R < N; R++) {
				if (R - L + 1 > M) {
					break;
				}
				sum += arr[R];
				max = Math.max(max, sum);
			}
		}
		return max;
	}

	// O(N)的解法，最优解
	public static int maxSum(int[] arr, int M) {
		if (arr == null || arr.length == 0 || M < 1) {
			return 0;
		}
		int N = arr.length;
		int[] sum = new int[N];
		sum[0] = arr[0];
		for (int i = 1; i < N; i++) {
			sum[i] = sum[i - 1] + arr[i];
		}
		LinkedList<Integer> qmax = new LinkedList<>();
		int i = 0;
		int end = Math.min(N, M);
		for (; i < end; i++) {
			while (!qmax.isEmpty() && sum[qmax.peekLast()] <= sum[i]) {
				qmax.pollLast();
			}
			qmax.add(i);
		}
		int max = sum[qmax.peekFirst()];
		int L = 0;
		for (; i < N; L++, i++) {
			// 该候选离开长度限制允许的范围，需从队首移除。
			if (qmax.peekFirst() == L) {
				qmax.pollFirst();
			}
			while (!qmax.isEmpty() && sum[qmax.peekLast()] <= sum[i]) {
				qmax.pollLast();
			}
			qmax.add(i);
			// 当前左前缀固定时，减去它并选合法右前缀窗口中的最大值。
			max = Math.max(max, sum[qmax.peekFirst()] - sum[L]);
		}
		for (; L < N - 1; L++) {
			// 该候选离开长度限制允许的范围，需从队首移除。
			if (qmax.peekFirst() == L) {
				qmax.pollFirst();
			}
			// 当前左前缀固定时，减去它并选合法右前缀窗口中的最大值。
			max = Math.max(max, sum[qmax.peekFirst()] - sum[L]);
		}
		return max;
	}

	// 用作测试
	public static int[] randomArray(int len, int max) {
		int[] arr = new int[len];
		for (int i = 0; i < len; i++) {
			arr[i] = (int) (Math.random() * max) - (int) (Math.random() * max);
		}
		return arr;
	}

	// 用作测试

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
public static void main(String[] args) {
		int maxN = 50;
		int maxValue = 100;
		int testTime = 1000000;
		System.out.println("测试开始");
		for (int i = 0; i < testTime; i++) {
			int N = (int) (Math.random() * maxN);
			int M = (int) (Math.random() * maxN);
			int[] arr = randomArray(N, maxValue);
			int ans1 = test(arr, M);
			int ans2 = maxSum(arr, M);
			if (ans1 != ans2) {
				System.out.println(ans1);
				System.out.println(ans2);
				System.out.println("Oops!");
			}
		}
		System.out.println("测试结束");
	}
```

#### 题解

**为什么正确**

长度约束精确等价于两个前缀下标距离在1..M。固定一侧后最优另一侧就是合法范围的极值，单调队列删除更差且更早失效的候选，保持窗口极值正确。

**复杂度**

时间O(N)，前缀数组与队列空间O(N)。

**边界与易错点**

要求非空，不能用初始答案0吞掉全负数组的正确负数答案。M大于N时截断，M≤0无合法长度。前缀下标与原数组下标需统一。

### 46.5 哈夫曼编码与解码

#### 题目

给定字符及其出现频率，构造无前缀冲突的哈夫曼编码，并支持字符串编码与解码。

**输入、输出与约束**

给定正频率字符表或原文；构造可逆无前缀码并支持字符串编码解码。

**函数签名（课程入口）**

```java
public static HashMap<Character, String> huffmanForm(HashMap<Character, Integer> countMap);
public static String huffmanEncode(String str, HashMap<Character, String> huffmanForm);
public static String huffmanDecode(String huffmanEncode, HashMap<Character, String> huffmanForm);
```

**示例**

```text
输入：频率A=5、B=2、C=1
输出：一种合法最优编码A=1、B=01、C=00，总位数11
```

解释：B与C先合并权重3，再与A合并；左右位选择可整体互换。

**出处与版本差异**

- [课程源码：class46/Code05_HuffmanTree.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class46/Code05_HuffmanTree.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

每个字符按频率建立叶子，小根堆反复取两个最轻节点合成父节点，再放回总权重；从根沿左0右1生成编码。解码从根按比特下降，到叶子输出字符并回根。

#### 代码答案

```java
package class46;

import java.util.Comparator;
import java.util.HashMap;
import java.util.Map.Entry;
import java.util.PriorityQueue;

// 本文件不牵扯任何byte类型的转化
// 怎么转byte自己来，我只负责huffman算法本身的正确实现
// 字符串为空的时候，自己处理边界吧
// 实现的代码通过了大样本随机测试的对数器
// 可以从main函数的内容开始看起
public class Code05_HuffmanTree {

	// 根据文章str, 生成词频统计表
	public static HashMap<Character, Integer> countMap(String str) {
		HashMap<Character, Integer> ans = new HashMap<>();
		char[] s = str.toCharArray();
		for (char cha : s) {
			if (!ans.containsKey(cha)) {
				ans.put(cha, 1);
			} else {
				ans.put(cha, ans.get(cha) + 1);
			}
		}
		return ans;
	}

	public static class Node {
		public int count;
		public Node left;
		public Node right;

		public Node(int c) {
			count = c;
		}
	}

	public static class NodeComp implements Comparator<Node> {

		@Override
		public int compare(Node o1, Node o2) {
			return o1.count - o2.count;
		}

	}

	// 根据由文章生成词频表countMap，生成哈夫曼编码表
	// key : 字符
	// value: 该字符编码后的二进制形式
	// 比如，频率表 A：60, B:45, C:13 D:69 E:14 F:5 G:3
	// A 10
	// B 01
	// C 0011
	// D 11
	// E 000
	// F 00101
	// G 00100
	public static HashMap<Character, String> huffmanForm(HashMap<Character, Integer> countMap) {
		HashMap<Character, String> ans = new HashMap<>();
		if (countMap.size() == 1) {
			for (char key : countMap.keySet()) {
				ans.put(key, "0");
			}
			return ans;
		}
		HashMap<Node, Character> nodes = new HashMap<>();
		PriorityQueue<Node> heap = new PriorityQueue<>(new NodeComp());
		for (Entry<Character, Integer> entry : countMap.entrySet()) {
			Node cur = new Node(entry.getValue());
			char cha = entry.getKey();
			nodes.put(cur, cha);
			heap.add(cur);
		}
		while (heap.size() != 1) {
			// 取当前频率最小的子树，与下一棵最小子树配成兄弟。
			Node a = heap.poll();
			Node b = heap.poll();
			// 合并节点权重等于两子树频率和，之后把它当作一个整体继续选择。
			Node h = new Node(a.count + b.count);
			h.left = a;
			h.right = b;
			heap.add(h);
		}
		Node head = heap.poll();
		fillForm(head, "", nodes, ans);
		return ans;
	}

	public static void fillForm(Node head, String pre, HashMap<Node, Character> nodes, HashMap<Character, String> ans) {
		if (nodes.containsKey(head)) {
			ans.put(nodes.get(head), pre);
		} else {
			// 沿左边追加0；只有到叶子时才记录完整码字。
			fillForm(head.left, pre + "0", nodes, ans);
			// 沿右边追加1，不同叶路径不可能互为前缀。
			fillForm(head.right, pre + "1", nodes, ans);
		}
	}

	// 原始字符串str，根据哈夫曼编码表，转译成哈夫曼编码返回
	public static String huffmanEncode(String str, HashMap<Character, String> huffmanForm) {
		char[] s = str.toCharArray();
		StringBuilder builder = new StringBuilder();
		for (char cha : s) {
			builder.append(huffmanForm.get(cha));
		}
		return builder.toString();
	}

	// 原始字符串的哈夫曼编码huffmanEncode，根据哈夫曼编码表，还原成原始字符串
	public static String huffmanDecode(String huffmanEncode, HashMap<Character, String> huffmanForm) {
		TrieNode root = createTrie(huffmanForm);
		TrieNode cur = root;
		char[] encode = huffmanEncode.toCharArray();
		StringBuilder builder = new StringBuilder();
		for (int i = 0; i < encode.length; i++) {
			int index = encode[i] == '0' ? 0 : 1;
			cur = cur.nexts[index];
			if (cur.nexts[0] == null && cur.nexts[1] == null) {
				builder.append(cur.value);
				cur = root;
			}
		}
		return builder.toString();
	}

	public static TrieNode createTrie(HashMap<Character, String> huffmanForm) {
		TrieNode root = new TrieNode();
		for (char key : huffmanForm.keySet()) {
			char[] path = huffmanForm.get(key).toCharArray();
			TrieNode cur = root;
			for (int i = 0; i < path.length; i++) {
				int index = path[i] == '0' ? 0 : 1;
				if (cur.nexts[index] == null) {
					cur.nexts[index] = new TrieNode();
				}
				cur = cur.nexts[index];
			}
			cur.value = key;
		}
		return root;
	}

	public static class TrieNode {
		public char value;
		public TrieNode[] nexts;

		public TrieNode() {
			value = 0;
			nexts = new TrieNode[2];
		}
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// 为了测试
	public static String randomNumberString(int len, int range) {
		char[] str = new char[len];
		for (int i = 0; i < len; i++) {
			str[i] = (char) ((int) (Math.random() * range) + 'a');
		}
		return String.valueOf(str);
	}

	// 为了测试
	public static void main(String[] args) {
		// 根据词频表生成哈夫曼编码表
		HashMap<Character, Integer> map = new HashMap<>();
		map.put('A', 60);
		map.put('B', 45);
		map.put('C', 13);
		map.put('D', 69);
		map.put('E', 14);
		map.put('F', 5);
		map.put('G', 3);
		HashMap<Character, String> huffmanForm = huffmanForm(map);
		for (Entry<Character, String> entry : huffmanForm.entrySet()) {
			System.out.println(entry.getKey() + " : " + entry.getValue());
		}
		System.out.println("====================");
		// str是原始字符串
		String str = "CBBBAABBACAABDDEFBA";
		System.out.println(str);
		// countMap是根据str建立的词频表
		HashMap<Character, Integer> countMap = countMap(str);
		// hf是根据countMap生成的哈夫曼编码表
		HashMap<Character, String> hf = huffmanForm(countMap);
		// huffmanEncode是原始字符串转译后的哈夫曼编码
		String huffmanEncode = huffmanEncode(str, hf);
		System.out.println(huffmanEncode);
		// huffmanDecode是哈夫曼编码还原成的原始字符串
		String huffmanDecode = huffmanDecode(huffmanEncode, hf);
		System.out.println(huffmanDecode);
		System.out.println("====================");
		System.out.println("大样本随机测试开始");
		// 字符串最大长度
		int len = 500;
		// 所含字符种类
		int range = 26;
		// 随机测试进行的次数
		int testTime = 100000;
		for (int i = 0; i < testTime; i++) {
			int N = (int) (Math.random() * len) + 1;
			String test = randomNumberString(N, range);
			HashMap<Character, Integer> counts = countMap(test);
			HashMap<Character, String> form = huffmanForm(counts);
			String encode = huffmanEncode(test, form);
			String decode = huffmanDecode(encode, form);
			if (!test.equals(decode)) {
				System.out.println(test);
				System.out.println(encode);
				System.out.println(decode);
				System.out.println("出错了!");
			}
		}
		System.out.println("大样本随机测试结束");
	}
```

#### 题解

**为什么正确**

叶子路径互不为前缀，因此编码无歧义。最小权重可通过交换放在最深的一对兄弟而不增加加权路径长，收缩这对兄弟后递归重复，得到最小总编码长度。

**复杂度**

K种字符建树O(K log K)，树空间O(K)；编码和解码时间与处理的字符数及输出比特数相加相关。

**边界与易错点**

仅一种字符时不能给空编码，课程为它指定一位。相同频率可产生不同但同样最优的码表，对拍应比较解码结果与总长度而非唯一比特串。


<a id="course-47"></a>

## 第 47 课：高级动态规划与 Dinic 最大流

### 47.1 奇怪打印机的最少打印次数

#### 题目

打印机每次可在任意连续区间打印同一个字符并覆盖原内容，返回打印目标字符串所需的最少次数。

**输入、输出与约束**

每轮任选连续区间覆盖同一字符；返回从空白打印目标字符串的最少轮数。

**函数签名（课程入口）**

```java
public static int strangePrinter1(String s);
public static int strangePrinter2(String s);
public static int strangePrinter3(String s);
```

**示例**

```text
输入：s="aba"
输出：2
```

解释：先打印aaa，再把中间覆盖成b。

**出处与版本差异**

- [课程源码：class47/Code01_StrangePrinter.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class47/Code01_StrangePrinter.java)。
- [LeetCode 原题 664. 奇怪的打印机（Strange Printer）](https://leetcode.com/problems/strange-printer/)

#### 思路

dp[L][R]表示打印目标区间最少次数。把首字符单独打印可得1+dp[L+1][R]；若后面某位置与首字符相同，可让它们在同一轮覆盖打印，再修补中间，合并两个区间转移时减掉重复计算的一次同色打印。

#### 代码答案

```java
package class47;

// 本题测试链接 : https://leetcode.com/problems/strange-printer/
public class Code01_StrangePrinter {

	public static int strangePrinter1(String s) {
		if (s == null || s.length() == 0) {
			return 0;
		}
		char[] str = s.toCharArray();
		return process1(str, 0, str.length - 1);
	}

	// 要想刷出str[L...R]的样子！
	// 返回最少的转数
	public static int process1(char[] str, int L, int R) {
		if (L == R) {
			return 1;
		}
		// L...R
		int ans = R - L + 1;
		for (int k = L + 1; k <= R; k++) {
			// L...k-1 k....R
			// 两段首字符相同，存在一次打印可共用，合并成本扣掉重复的一次。
			ans = Math.min(ans, process1(str, L, k - 1) + process1(str, k, R) - (str[L] == str[k] ? 1 : 0));
		}
		return ans;
	}

	public static int strangePrinter2(String s) {
		if (s == null || s.length() == 0) {
			return 0;
		}
		char[] str = s.toCharArray();
		int N = str.length;
		int[][] dp = new int[N][N];
		return process2(str, 0, N - 1, dp);
	}

	public static int process2(char[] str, int L, int R, int[][] dp) {
		if (dp[L][R] != 0) {
			return dp[L][R];
		}
		int ans = R - L + 1;
		if (L == R) {
			ans = 1;
		} else {
			for (int k = L + 1; k <= R; k++) {
				// 两段首字符相同，存在一次打印可共用，合并成本扣掉重复的一次。
				ans = Math.min(ans, process2(str, L, k - 1, dp) + process2(str, k, R, dp) - (str[L] == str[k] ? 1 : 0));
			}
		}
		dp[L][R] = ans;
		return ans;
	}

	public static int strangePrinter3(String s) {
		if (s == null || s.length() == 0) {
			return 0;
		}
		char[] str = s.toCharArray();
		int N = str.length;
		int[][] dp = new int[N][N];
		dp[N - 1][N - 1] = 1;
		for (int i = 0; i < N - 1; i++) {
			dp[i][i] = 1;
			dp[i][i + 1] = str[i] == str[i + 1] ? 1 : 2;
		}
		for (int L = N - 3; L >= 0; L--) {
			for (int R = L + 2; R < N; R++) {
				dp[L][R] = R - L + 1;
				for (int k = L + 1; k <= R; k++) {
					// 两段首字符相同，存在一次打印可共用，合并成本扣掉重复的一次。
					dp[L][R] = Math.min(dp[L][R], dp[L][k - 1] + dp[k][R] - (str[L] == str[k] ? 1 : 0));
				}
			}
		}
		return dp[0][N - 1];
	}

}
```

---

##### 边界核对

用题面示例核对接口，再检查：不能只数连续同色段，aba三段只需两次。相同字符合并要减去一次重复打印，但不是任意相邻子问题都可减一。

本条未附独立随机对数器。

#### 题解

**为什么正确**

打印允许覆盖，因此相隔同色字符可先由一次长区间打印连起来，中间不同字符后续重印。枚举同色合并位置覆盖首字符最后与哪一段共用一次打印的选择，其余各段由子问题最优完成。

**复杂度**

区间动态规划O(N³)时间、O(N²)空间；记忆化同阶。

**边界与易错点**

不能只数连续同色段，aba三段只需两次。相同字符合并要减去一次重复打印，但不是任意相邻子问题都可减一。

### 47.2 恢复数组的有效方案数

#### 题目

数组中部分位置为 0，需恢复为合法正整数，使每个位置满足题目规定的相邻大小关系，返回恢复方案数。

**输入、输出与约束**

3≤N≤10⁴，原非零值1..200；恢复0为1..200，满足两端不大于唯一邻居、中间不大于左右最大值，返回方案数。

**函数签名（课程入口）**

```java
public static int ways0(int[] arr);
public static boolean isValid(int[] arr);
public static int ways1(int[] arr);
public static int zuo(int[] arr, int i, int v, int s);
public static int ways2(int[] arr);
public static int ways3(int[] arr);
```

**示例**

```text
输入：arr=[6,0,9]
输出：1
```

解释：末端要求中间≥9，中间又不能大于max(6,9)=9，因此只能填9。

**出处与版本差异**

- [课程源码：class47/Code02_RestoreWays.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class47/Code02_RestoreWays.java)。
- 课程经典题或算法模板；未发现课程源码标注的 LeetCode 原题，且未建立可确认的一一对应关系。

#### 思路

状态f(i,v,s)固定当前位置为v，并记录它与右邻的大小关系s，求前缀0..i的恢复数。若当前≤右邻，当前的“不成为严格峰值”已满足，左值任意；若当前>右邻，左值必须≥当前。按左值与v的关系转入上一位置；值域1..200的区间求和可用前缀和加速。

#### 代码答案

```java
package class47;

// 整型数组arr长度为n(3 <= n <= 10^4)，最初每个数字是<=200的正数且满足如下条件：
// 1. 0位置的要求：arr[0]<=arr[1]
// 2. n-1位置的要求：arr[n-1]<=arr[n-2]
// 3. 中间i位置的要求：arr[i]<=max(arr[i-1],arr[i+1])
// 但是在arr有些数字丢失了，比如k位置的数字之前是正数，丢失之后k位置的数字为0
// 请你根据上述条件，计算可能有多少种不同的arr可以满足以上条件
// 比如 [6,0,9] 只有还原成 [6,9,9]满足全部三个条件，所以返回1种，即[6,9,9]达标
public class Code02_RestoreWays {

	public static int ways0(int[] arr) {
		return process0(arr, 0);
	}

	public static int process0(int[] arr, int index) {
		if (index == arr.length) {
			return isValid(arr) ? 1 : 0;
		} else {
			if (arr[index] != 0) {
				return process0(arr, index + 1);
			} else {
				int ways = 0;
				for (int v = 1; v < 201; v++) {
					arr[index] = v;
					ways += process0(arr, index + 1);
				}
				arr[index] = 0;
				return ways;
			}
		}
	}

	public static boolean isValid(int[] arr) {
		if (arr[0] > arr[1]) {
			return false;
		}
		if (arr[arr.length - 1] > arr[arr.length - 2]) {
			return false;
		}
		for (int i = 1; i < arr.length - 1; i++) {
			if (arr[i] > Math.max(arr[i - 1], arr[i + 1])) {
				return false;
			}
		}
		return true;
	}

	public static int ways1(int[] arr) {
		int N = arr.length;
		if (arr[N - 1] != 0) {
			return process1(arr, N - 1, arr[N - 1], 2);
		} else {
			int ways = 0;
			for (int v = 1; v < 201; v++) {
				ways += process1(arr, N - 1, v, 2);
			}
			return ways;
		}
	}

	// 如果i位置的数字变成了v,
	// 并且arr[i]和arr[i+1]的关系为s，
	// s==0，代表arr[i] < arr[i+1] 右大
	// s==1，代表arr[i] == arr[i+1] 右=当前
	// s==2，代表arr[i] > arr[i+1] 右小
	// 返回0...i范围上有多少种有效的转化方式？
	public static int process1(int[] arr, int i, int v, int s) {
		if (i == 0) { // 0...i 只剩一个数了，0...0
			return ((s == 0 || s == 1) && (arr[0] == 0 || v == arr[0])) ? 1 : 0;
		}
		// i > 0
		// 固定原值与尝试值不同，该状态不合法，不允许修改非零输入。
		if (arr[i] != 0 && v != arr[i]) {
			return 0;
		}
		// i>0 ，并且， i位置的数真的可以变成V，
		int ways = 0;
		// 右邻已不小于当前，当前峰值约束满足，左侧可取任意合法值。
		if (s == 0 || s == 1) { // [i] -> V <= [i+1]
			for (int pre = 1; pre < 201; pre++) {
				ways += process1(arr, i - 1, pre, pre < v ? 0 : (pre == v ? 1 : 2));
			}
		} else { // ? 当前 > 右 当前 <= max{左，右}
			// 右邻较小时，必须让左邻至少等于当前，避免形成严格峰值。
			for (int pre = v; pre < 201; pre++) {
				ways += process1(arr, i - 1, pre, pre == v ? 1 : 2);
			}
		}
		return ways;
	}

	public static int zuo(int[] arr, int i, int v, int s) {
		if (i == 0) { // 0...i 只剩一个数了，0...0
			return ((s == 0 || s == 1) && (arr[0] == 0 || v == arr[0])) ? 1 : 0;
		}
		// i > 0
		// 固定原值与尝试值不同，该状态不合法，不允许修改非零输入。
		if (arr[i] != 0 && v != arr[i]) {
			return 0;
		}
		// i>0 ，并且， i位置的数真的可以变成V，
		int ways = 0;
		// 右邻已不小于当前，当前峰值约束满足，左侧可取任意合法值。
		if (s == 0 || s == 1) { // [i] -> V <= [i+1]
			for (int pre = 1; pre < v; pre++) {
				ways += zuo(arr, i - 1, pre, 0);
			}
		}
		ways += zuo(arr, i - 1, v, 1);
		for (int pre = v + 1; pre < 201; pre++) {
			ways += zuo(arr, i - 1, pre, 2);
		}
		return ways;
	}

	public static int ways2(int[] arr) {
		int N = arr.length;
		int[][][] dp = new int[N][201][3];
		if (arr[0] != 0) {
			dp[0][arr[0]][0] = 1;
			dp[0][arr[0]][1] = 1;
		} else {
			for (int v = 1; v < 201; v++) {
				dp[0][v][0] = 1;
				dp[0][v][1] = 1;
			}
		}
		for (int i = 1; i < N; i++) {
			for (int v = 1; v < 201; v++) {
				for (int s = 0; s < 3; s++) {
					if (arr[i] == 0 || v == arr[i]) {
						// 右邻已不小于当前，当前峰值约束满足，左侧可取任意合法值。
						if (s == 0 || s == 1) {
							for (int pre = 1; pre < v; pre++) {
								dp[i][v][s] += dp[i - 1][pre][0];
							}
						}
						dp[i][v][s] += dp[i - 1][v][1];
						for (int pre = v + 1; pre < 201; pre++) {
							dp[i][v][s] += dp[i - 1][pre][2];
						}
					}
				}
			}
		}
		if (arr[N - 1] != 0) {
			return dp[N - 1][arr[N - 1]][2];
		} else {
			int ways = 0;
			for (int v = 1; v < 201; v++) {
				ways += dp[N - 1][v][2];
			}
			return ways;
		}
	}

	public static int ways3(int[] arr) {
		int N = arr.length;
		int[][][] dp = new int[N][201][3];
		if (arr[0] != 0) {
			dp[0][arr[0]][0] = 1;
			dp[0][arr[0]][1] = 1;
		} else {
			for (int v = 1; v < 201; v++) {
				dp[0][v][0] = 1;
				dp[0][v][1] = 1;
			}
		}
		int[][] presum = new int[201][3];
		for (int v = 1; v < 201; v++) {
			for (int s = 0; s < 3; s++) {
				presum[v][s] = presum[v - 1][s] + dp[0][v][s];
			}
		}
		for (int i = 1; i < N; i++) {
			for (int v = 1; v < 201; v++) {
				for (int s = 0; s < 3; s++) {
					if (arr[i] == 0 || v == arr[i]) {
						// 右邻已不小于当前，当前峰值约束满足，左侧可取任意合法值。
						if (s == 0 || s == 1) {
							dp[i][v][s] += sum(1, v - 1, 0, presum);
						}
						dp[i][v][s] += dp[i - 1][v][1];
						dp[i][v][s] += sum(v + 1, 200, 2, presum);
					}
				}
			}
			for (int v = 1; v < 201; v++) {
				for (int s = 0; s < 3; s++) {
					presum[v][s] = presum[v - 1][s] + dp[i][v][s];
				}
			}
		}
		if (arr[N - 1] != 0) {
			return dp[N - 1][arr[N - 1]][2];
		} else {
			return sum(1, 200, 2, presum);
		}
	}

	public static int sum(int begin, int end, int relation, int[][] presum) {
		return presum[end][relation] - presum[begin - 1][relation];
	}

}
```

---

##### 课程测试与演示

以下保留课程原有测试与辅助方法。含随机比较的部分用于对拍；仅打印示例或性能计时的部分不等于断言验证。

将下面的方法放回上方外层类的最后一个 `}` 之前，再运行该类的 `main`。不要把两段当作两个同名顶层类。

```java
// for test
	public static int[] generateRandomArray(int len) {
		int[] ans = new int[len];
		for (int i = 0; i < ans.length; i++) {
			if (Math.random() < 0.5) {
				ans[i] = 0;
			} else {
				ans[i] = (int) (Math.random() * 200) + 1;
			}
		}
		return ans;
	}

	// for test
	public static void printArray(int[] arr) {
		System.out.println("arr size : " + arr.length);
		for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
		System.out.println();
	}

public static void main(String[] args) {
		int len = 4;
		int testTime = 15;
		System.out.println("功能测试开始");
		for (int i = 0; i < testTime; i++) {
			int N = (int) (Math.random() * len) + 2;
			int[] arr = generateRandomArray(N);
			int ans0 = ways0(arr);
			int ans1 = ways1(arr);
			int ans2 = ways2(arr);
			int ans3 = ways3(arr);
			if (ans0 != ans1 || ans2 != ans3 || ans0 != ans2) {
				System.out.println("Oops!");
			}
		}
		System.out.println("功能测试结束");
		System.out.println("===========");
		int N = 100000;
		int[] arr = generateRandomArray(N);
		long begin = System.currentTimeMillis();
		ways3(arr);
		long end = System.currentTimeMillis();
		System.out.println("run time : " + (end - begin) + " ms");
	}
```

#### 题解

**为什么正确**

当前位置是否符合arr[i]≤max(左,右)，只需当前v、右侧关系和所选左值，不需保存更远右侧。左端基例要求arr[0]≤arr[1]；右端用s=2强制左邻≥末值，恰表达末端规则。每个零的赋值被唯一枚举。

**复杂度**

值域V=200，普通三维表O(NV²)时间、O(NV)空间；前缀和消枚举版O(NV)时间、O(NV)空间。暴力为V^零个数。

**边界与易错点**

非零原值不可改变。s=0、1、2分别表示当前小于、等于、大于右邻，不能反过来。n需≥3；结果可能非常大，课程int实现不承诺任意输入精确计数。

### 47.3 Dinic 最大流算法

#### 题目

给定有向容量网络、源点和汇点，返回从源点到汇点能够发送的最大流量。

**输入、输出与约束**

输入有向非负容量图、不同源点与汇点；返回最大可行流，原容量边以残量形式被更新。

**函数签名（课程入口）**

```java
public void addEdge(int u, int v, int r);
public int maxFlow(int s, int t);
```

**示例**

```text
输入：s→a容量3，a→t容量2，s→t容量1
输出：最大流3
```

解释：经a推2，直达推1；进入t的总容量也只有3。

**出处与版本差异**

- [课程源码：class47/Code03_DinicAlgorithm.java](https://github.com/algorithmzuo/algorithmbasic2020/blob/master/src/class47/Code03_DinicAlgorithm.java)。
- [课程源码标注的在线资料](https://lightoj.com/problem/internet-bandwidth)。

#### 思路

把每条容量边与初始容量0的反向边成对存储，维护残量网络。每轮BFS按残量正边建立从源的层次，再DFS只沿层号加一的边推流，直到该层次图形成阻塞流；当前弧索引避免反复检查已无用边。

#### 代码答案

```java
// 本题测试链接:
// https://lightoj.com/problem/internet-bandwidth
// 这是一道DinicAlgorithm算法的题
// 把如下代码粘贴进网页所提供的java编译器环境中
// 不需要修改任何内容可以直接通过
// 请看网页上的题目描述并结合main函数的写法去了解这个模板的用法
// 请同学们务必参考如下代码中关于输入、输出的处理
// 这是输入输出处理效率很高的写法

package class47;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.io.StreamTokenizer;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.LinkedList;

public class Code03_DinicAlgorithm {

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StreamTokenizer in = new StreamTokenizer(br);
		PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
		while (in.nextToken() != StreamTokenizer.TT_EOF) {
			int cases = (int) in.nval;
			for (int i = 1; i <= cases; i++) {
				in.nextToken();
				int n = (int) in.nval;
				in.nextToken();
				int s = (int) in.nval;
				in.nextToken();
				int t = (int) in.nval;
				in.nextToken();
				int m = (int) in.nval;
				Dinic dinic = new Dinic(n);
				for (int j = 0; j < m; j++) {
					in.nextToken();
					int from = (int) in.nval;
					in.nextToken();
					int to = (int) in.nval;
					in.nextToken();
					int weight = (int) in.nval;
					dinic.addEdge(from, to, weight);
					dinic.addEdge(to, from, weight);
				}
				int ans = dinic.maxFlow(s, t);
				out.println("Case " + i + ": " + ans);
				out.flush();
			}
		}
	}

	public static class Edge {
		public int from;
		public int to;
		public int available;

		public Edge(int a, int b, int c) {
			from = a;
			to = b;
			available = c;
		}
	}

	public static class Dinic {
		private int N;
		private ArrayList<ArrayList<Integer>> nexts;
		private ArrayList<Edge> edges;
		private int[] depth;
		private int[] cur;

		public Dinic(int nums) {
			N = nums + 1;
			nexts = new ArrayList<>();
			for (int i = 0; i <= N; i++) {
				nexts.add(new ArrayList<>());
			}
			edges = new ArrayList<>();
			depth = new int[N];
			cur = new int[N];
		}

		public void addEdge(int u, int v, int r) {
			int m = edges.size();
			edges.add(new Edge(u, v, r));
			nexts.get(u).add(m);
			edges.add(new Edge(v, u, 0));
			nexts.get(v).add(m + 1);
		}

		public int maxFlow(int s, int t) {
			int flow = 0;
			while (bfs(s, t)) {
				Arrays.fill(cur, 0);
				flow += dfs(s, t, Integer.MAX_VALUE);
				Arrays.fill(depth, 0);
			}
			return flow;
		}

		private boolean bfs(int s, int t) {
			LinkedList<Integer> queue = new LinkedList<>();
			queue.addFirst(s);
			boolean[] visited = new boolean[N];
			visited[s] = true;
			while (!queue.isEmpty()) {
				int u = queue.pollLast();
				for (int i = 0; i < nexts.get(u).size(); i++) {
					Edge e = edges.get(nexts.get(u).get(i));
					int v = e.to;
					if (!visited[v] && e.available > 0) {
						visited[v] = true;
						depth[v] = depth[u] + 1;
						if (v == t) {
							break;
						}
						queue.addFirst(v);
					}
				}
			}
			return visited[t];
		}

		// 当前来到了s点，s可变
		// 最终目标是t，t固定参数
		// r，收到的任务
		// 收集到的流，作为结果返回，ans <= r
		private int dfs(int s, int t, int r) {
			if (s == t || r == 0) {
				return r;
			}
			int f = 0;
			int flow = 0;
			// s点从哪条边开始试 -> cur[s]
			for (; cur[s] < nexts.get(s).size(); cur[s]++) {
				int ei = nexts.get(s).get(cur[s]);
				Edge e = edges.get(ei);
				Edge o = edges.get(ei ^ 1);
				if (depth[e.to] == depth[s] + 1 && (f = dfs(e.to, t, Math.min(e.available, r))) != 0) {
					// 正向边已使用f容量，剩余可用容量减少。
					e.available -= f;
					o.available += f;
					flow += f;
					r -= f;
					if (r <= 0) {
						break;
					}
				}
			}
			return flow;
		}
	}

}
```

---

##### 输入输出核对

先以本题示例核对结果，再重点覆盖：每条原边必须有配对反向边，并使用正确的i^1索引。只沿层次增加的边推流，正反残量同步修改。容量总和超int时需把流量字段整体升级long。

该版本保留在线评测入口；未附独立随机对数器，不把编译通过当作正确性证明。

#### 题解

**为什么正确**

沿路径增加流同时减少正向残量、增加反向残量，保持容量限制和中间节点流量守恒，反向边允许撤销旧选择。若残量网络已无源到汇路径，可达集合形成饱和割，当前流值等于该割容量，因此达到最大流。

**复杂度**

一般图经典上界O(V²E)，残量边与节点空间O(V+E)，DFS栈最坏O(V)。特殊网络可更快，但不能无条件套较强上界。

**边界与易错点**

每条原边必须有配对反向边，并使用正确的i^1索引。只沿层次增加的边推流，正反残量同步修改。容量总和超int时需把流量字段整体升级long。
