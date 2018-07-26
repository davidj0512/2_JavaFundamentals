# Java Core, Fundamentals Notes

### 1. 编写完美equals方法的建议(CoreJava1-P169)：

> ##### 注意点：
> 
>> * Java语言规范要求equals方法具有这些特性： 自反性， 对称性， 传递性， 一致性， 以及对于任意非空引用x，x.equals(null)应该返回false
>> * equals方法其实是覆盖Object类的equals方法，方法签名为： `public boolean equals(Object obj)`, 注意参数只能是`Object`类型，因为父类Object类中的equals方法就是这样。 为了避免错误，要使用`@Override`对覆盖方法进行标记。
>
> ##### 写完美equals方法的建议： `public boolean equals(Object otherObject)`
> 
>> * 显示参数命名为otherObject，稍后需要将它转换成另一个叫做other的对象
>
>> * 检测this与otherObject是否引用同一个对象：  
> `if(this == otherObject) return true;`。   
> 这条语句只是一个优化。实际上，这是一种经常采用的形式。因为计算这个等式要比一个一个地比较类中的域所付出的代价要小的多。
>
>> * 检测otherObject是否为null，如果为null，返回false。这项检测是很必要的。  
>> `if(otherObject == null) return false;`
>
>> * 比较this与otherObject是否属于同一个类。  
> 如果equals的语义在每个子类中有所改变，就使用getClass检测：`if (getClass != otherObject.getClass()) return false;`；   
> 如果所有子类都拥有统一的语义，就使用instanceof检测：`if(!(otherObject instanceof ClassName)) return false;`    
> (也可说为：如果子类能够拥有自己的相等概念（即判断相等要用到子类中特有的域），则对称性需求强制超类equals方法中采用getClass进行检测； 如果由超类决定相等的概念（即判断相等的域都在超类中），那么超类equals方法中就可以使用instanceof进行检测。这样才可以满足对称性的特性，才可以在不同的子类对象之间进行相等的比较。)
>
>> * 将otherObject转换为相应类的类型变量：  
>> `ClassName other = (ClassName)otherObject;`
>
>> * 现在开始对所有需要比较的域进行比较。使用 == 比较基本类型域，使用equals比较对象域。如果所有的域都匹配，就返回true，否则放回false。  
> ```java  
> return field1 == other.field1  
>     && Objects.equals(field2, other.field2)  
>     && ...;  
> ```  
> **注意：** 1, 为了防备有的域可能为null的情况（如field2可能为null），最好使用`Objects.equals(a, b)`方法，如果两个参数都为null，则返回true；如果其中一个为null，则返回false；若两个参数都不为null，则调用a.equals(b)。  
> 2, 如果在子类中重新定义equals，就要在其中包含调用`super.equals(other)`
