# Notes
##### 8种基本类型的包装类和对象池

java中基本类型的包装类的大部分都实现了常量池技术，这些类是Byte, Short, Integer, Long, Character, Boolean, 
另外两种浮点数类型（Float、Double）的包装类则没有实现。另外Byte, Short, Integer, Long, Character
这5种整型的包装类也只在大于等于 **-128并且小于等于127** 时才使用常量池，**也即对象不负责创建和管理大于127的这些类的对象**。以下是一些对应的测试代码：
