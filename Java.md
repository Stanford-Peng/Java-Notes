# Notes
### 8种基本类型的包装类和对象池

java中基本类型的包装类的大部分都实现了常量池技术，这些类是Byte, Short, Integer, Long, Character, Boolean, 
另外两种浮点数类型（Float、Double）的包装类则没有实现。另外Byte, Short, Integer, Long, Character
这5种整型的包装类也只在大于等于 **-128并且小于等于127** 时才使用常量池，**也即对象不负责创建和管理大于127的这些类的对象**。以下是一些对应的测试代码：



###  about protected method:
can be used in subclass in same or different packages.

for Object clone: it uses native methods:
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
```
————————————————
版权声明：本文为CSDN博主「耀凯考前突击大师」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/Yaokai_AssultMaster/article/details/79492190

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

### 差分数组 array of difference
https://www.jianshu.com/p/385b7185a94e

### 并查集 Union find
https://www.jianshu.com/p/def7767982ac

详细
https://zhuanlan.zhihu.com/p/93647900

### B+ B-树 用于硬盘数据库
https://zhuanlan.zhihu.com/p/54102723#:~:text=%E4%B8%80%E4%B8%AAm%E9%98%B6%E7%9A%84B%2B,%E5%B0%8F%E8%80%8C%E5%A4%A7%E9%A1%BA%E5%BA%8F%E9%93%BE%E6%8E%A5%E3%80%82

### skip list
https://lotabout.me/2018/skip-list/

### 红黑树
jianshu.com/p/86a1fd2d7406
https://www.jianshu.com/p/e136ec79235c
红黑树多用在内部排序，即全放在内存中的，STL的map和set的内部实现就是红黑树。
B+树多用于外存上时，B+也被成为一个磁盘友好的数据结构。
### 重写特例
Java中static方法不能被覆盖，因为方法覆盖是基于运行时动态绑定的，而static方法是编译时静态绑定的。static方法类的任何实例都不相关，所以概念上不适用。
Java中也不可以覆盖private的方法，因为private修饰的变量和方法只能在当前类中使用，如果是其他的类继承当前类是不能访问到private变量或方法的，当然也不能覆盖。
————————————————
版权声明：本文为CSDN博主「万河归海」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/JW614718/article/details/88358959

### jvm组成
https://blog.csdn.net/lzm1340458776/article/details/44153825
details about jdk 8
https://www.jianshu.com/p/b9a5cbef12f2
方法区是一个 JVM 规范，永久代与元空间都是其一种实现方式。在 JDK 1.8 之后，原来永久代的数据被分到了堆和元空间中。元空间存储类的元信息，静态变量和常量池等放入堆中。
### 关于程序计数器 The pc Register
https://www.zhihu.com/question/40598119/answer/87381512

### java面试题
https://www.jianshu.com/p/33aa25906f09

### memory vs cache vs hard disk
https://www.geeksforgeeks.org/difference-between-ram-and-cache/

### Data Collection Structure/Java容器

https://facingissuesonit.com/2019/10/15/java-collection-framework-hierarchy/

One More: LinkedHashMap

### JVM收集器
https://zhuanlan.zhihu.com/p/58896728


### concurrent collection
https://www.logicbig.com/tutorials/core-java-tutorial/java-collections/concurrent-collection-cheatsheet.html
https://www.baeldung.com/java-synchronized-collections

### concurrent
Callable vs Runable
都是可以实现线程的接口，callable有返回值可以使用futuretask实现

### Daemon thread/守护线程
https://blog.csdn.net/shimiso/article/details/8964414

### static interrupted vs isinterrupted

https://stackoverflow.com/questions/1904072/java-difference-in-usage-between-thread-interrupted-and-thread-isinterrupted#:~:text=interrupted()%20is%20static%20and,static%20method%20on%20an%20instance.&text=Another%20difference%20is%20that%20interrupted,status%20of%20the%20current%20thread.

### 线程的协调
当多个线程可以一起工作去解决某个问题时，如果某些部分必须在其它部分之前完成，那么就需要对线程进行协调。
join()

### wait() 和 sleep() 的区别

wait() 是 Object 的方法，而 sleep() 是 Thread 的静态方法；
wait() 会释放锁，sleep() 不会。

### await() signal() signalAll()
java.util.concurrent 类库中提供了 Condition 类来实现线程之间的协调，可以在 Condition 上调用 await() 方法使线程等待，其它线程调用 signal() 或 signalAll() 方法唤醒等待的线程。

相比于 wait() 这种等待方式，await() 可以指定等待的条件，因此更加灵活。

使用 Lock 来获取一个 Condition 对象。

### CountDownLatch CyclicBarrier Semaphore

```
public class CountdownLatchExample {

    public static void main(String[] args) throws InterruptedException {
        final int totalThread = 10;
        CountDownLatch countDownLatch = new CountDownLatch(totalThread);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalThread; i++) {
            executorService.execute(() -> {
                System.out.print("run..");
                countDownLatch.countDown();
            });
        }
        countDownLatch.await();
        System.out.println("end");
        executorService.shutdown();
    }
}
```

```
public class CyclicBarrierExample {

    public static void main(String[] args) {
        final int totalThread = 10;
        CyclicBarrier cyclicBarrier = new CyclicBarrier(totalThread);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalThread; i++) {
            executorService.execute(() -> {
                System.out.print("before..");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException | BrokenBarrierException e) {
                    e.printStackTrace();
                }
                System.out.print("after..");
            });
        }
        executorService.shutdown();
    }
}

```

```
public class SemaphoreExample {

    public static void main(String[] args) {
        final int clientCount = 3;
        final int totalRequestCount = 10;
        Semaphore semaphore = new Semaphore(clientCount);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalRequestCount; i++) {
            executorService.execute(()->{
                try {
                    semaphore.acquire();
                    System.out.print(semaphore.availablePermits() + " ");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();
                }
            });
        }
        executorService.shutdown();
    }
}


```

### ReentrantLock
Java 提供了两种锁机制来控制多个线程对共享资源的互斥访问，第一个是 JVM 实现的 synchronized，而另一个是 JDK 实现的 ReentrantLock
```
public class LockExample {

    private Lock lock = new ReentrantLock();

    public void func() {
        lock.lock();
        try {
            for (int i = 0; i < 10; i++) {
                System.out.print(i + " ");
            }
        } finally {
            lock.unlock(); // 确保释放锁，从而避免发生死锁。
        }
    }
}
public static void main(String[] args) {
    LockExample lockExample = new LockExample();
    ExecutorService executorService = Executors.newCachedThreadPool();
    executorService.execute(() -> lockExample.func());
    executorService.execute(() -> lockExample.func());
}


```

### 锁消除

锁消除是指对于被检测出不可能存在竞争的共享数据的锁进行消除。

锁消除主要是通过逃逸分析来支持，如果堆上的共享数据不可能逃逸出去被其它线程访问到，那么就可以把它们当成私有数据对待，也就可以将它们的锁进行消除。

对于一些看起来没有加锁的代码，其实隐式的加了很多锁。例如下面的字符串拼接代码就隐式加了锁：

```
public static String concatString(String s1, String s2, String s3) {
    return s1 + s2 + s3;
}```
String 是一个不可变的类，编译器会对 String 的拼接自动优化。在 JDK 1.5 之前，会转化为 StringBuffer 对象的连续 append() 操作：

```
public static String concatString(String s1, String s2, String s3) {
    StringBuffer sb = new StringBuffer();
    sb.append(s1);
    sb.append(s2);
    sb.append(s3);
    return sb.toString();
}```
每个 append() 方法中都有一个同步块。虚拟机观察变量 sb，很快就会发现它的动态作用域被限制在 concatString() 方法内部。也就是说，sb 的所有引用永远不会逃逸到 concatString() 方法之外，其他线程无法访问到它，因此可以进行消除。

### 锁粗化

如果一系列的连续操作都对同一个对象反复加锁和解锁，频繁的加锁操作就会导致性能损耗。

上一节的示例代码中连续的 append() 方法就属于这类情况。如果虚拟机探测到由这样的一串零碎的操作都对同一个对象加锁，将会把加锁的范围扩展（粗化）到整个操作序列的外部。对于上一节的示例代码就是扩展到第一个 append() 操作之前直至最后一个 append() 操作之后，这样只需要加锁一次就可以了

### 轻量级锁🔒
https://zhuanlan.zhihu.com/p/141554048
作者：EnjoyMoving
链接：https://www.zhihu.com/question/53826114/answer/236363126
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

https://blog.csdn.net/choukekai/article/details/63688332
偏向所锁，轻量级锁都是乐观锁，重量级锁是悲观锁。     一个对象刚开始实例化的时候，没有任何线程来访问它的时候。它是可偏向的，意味着，它现在认为只可能有一个线程来访问它，所以当第一个线程来访问它的时候，它会偏向这个线程，此时，对象持有偏向锁。偏向第一个线程，这个线程在修改对象头成为偏向锁的时候使用CAS操作，并将对象头中的ThreadID改成自己的ID，之后再次访问这个对象时，只需要对比ID，不需要再使用CAS在进行操作。一旦有第二个线程访问这个对象，因为偏向锁不会主动释放，所以第二个线程可以看到对象时偏向状态，这时表明在这个对象上已经存在竞争了，检查原来持有该对象锁的线程是否依然存活，如果挂了，则可以将对象变为无锁状态，然后重新偏向新的线程，如果原来的线程依然存活，则马上执行那个线程的操作栈，检查该对象的使用情况，如果仍然需要持有偏向锁，则偏向锁升级为轻量级锁，（偏向锁就是这个时候升级为轻量级锁的）。如果不存在使用了，则可以将对象回复成无锁状态，然后重新偏向。       轻量级锁认为竞争存在，但是竞争的程度很轻，一般两个线程对于同一个锁的操作都会错开，或者说稍微等待一下（自旋），另一个线程就会释放锁。 但是当自旋超过一定的次数，或者一个线程在持有锁，一个在自旋，又有第三个来访时，轻量级锁膨胀为重量级锁，重量级锁使除了拥有锁的线程以外的线程都阻塞，防止CPU空转。