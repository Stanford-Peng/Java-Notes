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