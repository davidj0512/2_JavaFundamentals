# Java Core, Fundamentals Notes

### 1. 编写完美equals方法的建议(CoreJava1-P169)：

> ##### 注意点：
>> * Java语言规范要求equals方法具有这些特性： 自反性， 对称性， 传递性， 一致性， 以及对于任意非空引用x，x.equals(null)应该返回false
>> * equals方法其实是覆盖Object类的equals方法，方法签名为： `public boolean equals(Object obj)`, 注意参数只能是`Object`类型，因为父类Object类中的equals方法就是这样。 为了避免错误，要使用`@Override`对覆盖方法进行标记。
> ##### 写完美equals方法的建议： `public boolean equals(Object otherObject)`
>> * 显示参数命名为otherObject，稍后需要将它转换成另一个叫做other的对象
>> * 检测this与otherObject是否引用同一个对象：

        if(this == otherObject) return true;


>> 这条语句知识一个优化。实际上，这是一种经常采用的形式。因为计算这个等式要比一个一个地比较类中的域所付出的代价要小的多。
