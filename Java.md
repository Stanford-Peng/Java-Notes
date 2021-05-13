# Notes
### 8种基本类型的包装类和对象池

java中基本类型的包装类的大部分都实现了常量池技术，这些类是Byte, Short, Integer, Long, Character, Boolean, 
另外两种浮点数类型（Float、Double）的包装类则没有实现。另外Byte, Short, Integer, Long, Character
这5种整型的包装类也只在大于等于 **-128并且小于等于127** 时才使用常量池，**也即对象不负责创建和管理大于127的这些类的对象**。以下是一些对应的测试代码：



###  about protected method:
When a subclasss is not in the same package of super class, it, in class body, can directly call the protected method from parent class but cannot call the method using dot notation after an instance of both subclass unless subclass has overwritten(using super) the method itself. Superclass instance method is also forbidden to call.

### Weak Reference
https://www.cnblogs.com/absfree/p/5555687.html

强引用（Strong Reference）：通常我们通过new来创建一个新对象时返回的引用就是一个强引用，若一个对象通过一系列强引用可到达，它就是强可达的(strongly reachable)，那么它就不被回收

弱引用对象的存在不会阻止它所指向的对象变被垃圾回收器回收。弱引用最常见的用途是实现规范映射(canonicalizing mappings，比如哈希表）。

假设垃圾收集器在某个时间点决定一个对象是弱可达的(weakly reachable)（也就是说当前指向它的全都是弱引用），这时垃圾收集器会清除所有指向该对象的弱引用，然后垃圾收集器会把这个弱可达对象标记为可终结(finalizable)的，这样它们随后就会被回收。与此同时或稍后，垃圾收集器会把那些刚清除的弱引用放入创建弱引用对象时所登记到的引用队列(Reference Queue)中。

软引用（Soft Reference）：软引用和弱引用的区别在于，若一个对象是弱引用可达，无论当前内存是否充足它都会被回收，而软引用可达的对象在内存不充足时才会被回收，因此软引用要比弱引用“强”一些

虚引用（Phantom Reference）：虚引用是Java中最弱的引用，那么它弱到什么程度呢？它是如此脆弱以至于我们通过虚引用甚至无法获取到被引用的对象，虚引用存在的唯一作用就是当它指向的对象被回收后，虚引用本身会被加入到引用队列中，用作记录它指向的对象已被销毁。

### About Array Comparison
No deep comparison
https://www.geeksforgeeks.org/compare-two-arrays-java/

### functional interface
https://www.baeldung.com/java-8-functional-interfaces

### Binary indexed tree vs segment tree
https://blog.csdn.net/Yaokai_AssultMaster/article/details/79492190
```
public class BinaryIndexedTree {
	private int[] bitArr;

	// O(nlogn) initialization
//	public BinaryIndexedTree(int[] list) {
//		this.bitArr = new int[list.length + 1];
//		for (int i = 0; i < list.length; i++) {
//			this.update(i, list[i]);
//		}
//	}
	
	public BinaryIndexedTree(int[] list) {
		// O(n) initialization
		this.bitArr = new int[list.length + 1];
		for (int i = 0; i < list.length; i++) {
			this.bitArr[i + 1] = list[i];
		}
		
		for (int i = 1; i < this.bitArr.length; i++) {
			int j = i + (i & -i);
			if (j < this.bitArr.length) {
				this.bitArr[j] += this.bitArr[i];
			}
		}
	}
	
	/**
	 * Add `delta` to elements in `idx` of original array
	 * @param idx index of the element in original array that is going to be updated
	 * @param delta number that will be added to the original element.
	 */
	public void update(int idx, int delta) {
		idx += 1;
		while (idx < this.bitArr.length) {
			this.bitArr[idx] += delta;
			idx = idx + (idx & -idx);
		}
	}
	
	/**
	 * Get the sum of elements in the original array up to index `idx`
	 * @param idx index of the last element that should be summed. 
	 * @return sum of elements from index 0 to `idx`.
	 */
	public int prefixSum(int idx) {
		idx += 1;
		int result = 0;
		while (idx > 0) {
			result += this.bitArr[idx];
			idx = idx - (idx & -idx);
		}
		
		return result;
	}
	
	/**
	 * Get the range sum of elements from original array from index `from_idx` to `to_idx`
	 * @param from_idx start index of element in original array
	 * @param to_idx end index of element in original array
	 * @return range sum of elements from index `from_idx` to `to_idx`
	 */
	public int rangeSum(int from_idx, int to_idx) {
		return prefixSum(to_idx) - prefixSum(from_idx - 1);
	}
}
————————————————
版权声明：本文为CSDN博主「耀凯考前突击大师」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/Yaokai_AssultMaster/article/details/79492190
```
https://zhuanlan.zhihu.com/p/34150142
```
class SegmentTree {

    private static class TreeNode {
        int start, end, sum;
        TreeNode left, right;

        TreeNode(int start, int end) {
            this.start = start;
            this.end = end;
        }
    }

    private TreeNode buildTree(int[] nums, int start, int end) {
        if (start > end) return null;
        TreeNode cur = new TreeNode(start, end);
        if (start == end) cur.sum = nums[start];
        else {
            int mid = start + (end - start) / 2;
            cur.left = buildTree(nums, start, mid);
            cur.right = buildTree(nums, mid + 1, end);
            cur.sum = cur.left.sum + cur.right.sum;
        }
        return cur;
    }

    private void updateTree(TreeNode node, int i, int val) {
        if (node.start == node.end) {
            node.sum = val;
        } else {
            int mid = node.start + (node.end - node.start) / 2;
            if (i <= mid) updateTree(node.left, i, val);
            else updateTree(node.right, i, val);
            node.sum = node.left.sum + node.right.sum;
        }
    }

    private int queryTree(TreeNode node, int i, int j) {
        if (node.start == i && node.end == j) return node.sum;
        else {
            int mid = node.start + (node.end - node.start) / 2;
            if (j <= mid) {
                return queryTree(node.left, i, j);
            } else if (i >= (mid + 1)) {
                return queryTree(node.right, i, j);
            } else {
                return queryTree(node.left, i, mid) + queryTree(node.right, mid + 1, j);
            }
        }
    }

    private TreeNode root;

    SegmentTree(int[] nums) {
        root = buildTree(nums, 0, nums.length - 1);
    }

    public void update(int i, int val) {
        updateTree(root, i, val);
    }

    public int sumRange(int i, int j) {
        return queryTree(root, i, j);
    }
}
```