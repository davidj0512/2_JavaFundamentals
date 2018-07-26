# 1. equals方法(CoreJava1-P166)：

### 注意点：
> 
> * Java语言规范要求equals方法具有这些特性： 自反性， 对称性， 传递性， 一致性， 以及对于任意非空引用x，x.equals(null)应该返回false
> 
> * equals方法其实是覆盖Object类的equals方法，方法签名为： `public boolean equals(Object obj)`, 注意参数只能是`Object`类型，因为父类Object类中的equals方法就是这样。 为了避免错误，要使用`@Override`对覆盖方法进行标记。
>
### 写完美equals方法的建议： `public boolean equals(Object otherObject)`
> 
> * 显示参数命名为otherObject，稍后需要将它转换成另一个叫做other的对象
>
> * 检测this与otherObject是否引用同一个对象：  
> `if(this == otherObject) return true;`。   
> 这条语句只是一个优化。实际上，这是一种经常采用的形式。因为计算这个等式要比一个一个地比较类中的域所付出的代价要小的多。
>
> * 检测otherObject是否为null，如果为null，返回false。这项检测是很必要的。  
> `if(otherObject == null) return false;`
>
> * 比较this与otherObject是否属于同一个类。  
> 如果equals的语义在每个子类中有所改变，就使用getClass检测：`if (getClass != otherObject.getClass()) return false;`；   
> 如果所有子类都拥有统一的语义，就使用instanceof检测：`if(!(otherObject instanceof ClassName)) return false;`    
> (也可说为：如果子类能够拥有自己的相等概念（即判断相等要用到子类中特有的域），则对称性需求强制超类equals方法中采用getClass进行检测； 如果由超类决定相等的概念（即判断相等的域都在超类中），那么超类equals方法中就可以使用instanceof进行检测。这样才可以满足对称性的特性，才可以在不同的子类对象之间进行相等的比较。)
>
> * 将otherObject转换为相应类的类型变量：  
> `ClassName other = (ClassName)otherObject;`
>
> * 现在开始对所有需要比较的域进行比较。使用 == 比较基本类型域，使用equals比较对象域。如果所有的域都匹配，就返回true，否则放回false。  
> 
> ```java  
> return field1 == other.field1  
>     && Objects.equals(field2, other.field2)  
>     && ...;  
> ```  
>
> **注意：** 1, 为了防备有的域可能为null的情况（如field2可能为null），最好使用`Objects.equals(a, b)`方法，如果两个参数都为null，则返回true；如果其中一个为null，则返回false；若两个参数都不为null，则调用a.equals(b)。  
> 2, 对于数组类型的域，可以使用静态的`Arrays.equals()`方法检测相应的数组元素是否相等。
> 3, 如果在子类中重新定义equals，就要在其中包含对超类equals的调用`super.equals(other)`  

### equals方法示例：

>
>     ```java  
>     public class Employee{
>         ...
>         public boolean equals(Object otherObject) {
>             // a quick test to see if the objects are identical
>             if (this == otherObject) return true;
> 
>             // must return false if the explicit parameter is null
>             if (otherObject == null) return false;
> 
>             // if the classes don't match, they can't be equal
>             if (getClass() != otherObject.getClass()) return false;
> 
>             // now we know otherObject is a non-null Employee
>             Employee other = (Employee) otherObject;
> 
>             // test whether the fields have identical values
>             return Objects.equals(name, other.name)  
>                         && salary == other.salary  
>                         && Objects.equals(hireDay, other.hireDay);
>         }
>     }  
>     ```
>

# 2. hashCode方法(CoreJava1-P170)：  

### 注意点：

* 如果重新定义equals方法，就必须重新定义hashCode方法，以便用户可以将对象插入到散列表中。否则该类就无法与基于散列的集合类一起工作，比如HashMap和HashSet。  

* Equals与hashCode的定义必须一致：
    * 如果两个对象equals结果为真，那么他们的hashCode结果必须一致。
    * 如果两个对象equals结果为假，那么他们的hashCode结果不一定不同。但是，不同的hashCode结果有利于提高散列表性能。

* hashCode方法应该返回一个整形数值（也可以是负数），并合理的组合实例域的散列码，以便能够让各个不同的对象产生的散列码更加均匀。  

### hashCode最佳实践（参考《Effective java》 05）：

理想情况下，散列函数应该把集合中不相等的实例均匀地分布到所有可能的散列值上。虽然很难做到，但下面的方案不失为一种良好的解决方法。

> * 1- 把某个非零的常数值，比如17，保存在变量int result中

> * 2- 对于对象中每个关键域f（关键域指equals方法比较时用到的域），完成以下步骤：
> 
>> * a. 为该域计算int类型的散列码c：
>>
>>> * i. 如果该域是boolean类型，则计算f ? 1:0。
>>> * ii. 如果该域是byte、char、short或者int类型，则计算(int) f。
>>> * iii. 如果该域是long类型，则计算(int) (f ^ (f >>> 32))。
>>> * iv. 如果该域是float类型，则计算Float.floatToIntBits(f)。
>>> * v. 如果该域是double类型，则计算Double.doubleToLongBits(f)，然后再按照第iii步，为得到的long类型值计算散列值。
>>> * vi. 如果该域是一个对象引用，并且该类的equals方法通过递归地调用equals的方式来比较这个域，则同样为这个域递归地调用hashCode。
>>> * vii. 如果该域是一个数组，则要把每一个元素当做单独的域来处理。也就是说，递归地应用上述规则，对每一个重要的元素计算一个散列码，然后根据步骤2.b中的做法把这些散列值组合起来。如果数组域中的每个元素都很重要，可以利用Arrays.hashCode方法。
>>
>> * b. 按照下面的公式，把步骤2-.a中计算得到的散列码c合并到result中。  
		    `result = 31 * result + c;`
>
> * 3- 返回result
> 
> * 4- 写完了hashCode方法后，问问自己“相等的实例是否都具有相等的散列码”。要编写单元测试来验证你的推断。如果相等的实例有着不同的散列码，则要找出原因，并修正错误。
> 

为了更清晰的说明这几个步骤具体如何实现，我们举一个简单的例子。考虑如下Person类的hashCode实现。

	```java
	public class Person {
	
	  private boolean gender;
	  private int age;
	  private String name;
	
	  public Person(boolean gender, int age, String name) {
	    this.gender = gender;
	    this.age = age;
	    this.name = name;
	  }
	    
	  @Override
	  public boolean equals(Object obj) {
	    if (obj == this) return true;
	    if (!(obj instanceof Person)) return false;
	    Person p = (Person) obj;
	    return p.gender == gender && p.age == age && p.name == name;
	  }
	    
	  @Override
	  public int hashCode() {
	    int result = 17;
	    int c = gender ? 1 : 0;
	    result = 31 * result + c;
	    c = age;
	    result = 31 * result + c;
	    c = name.hashCode();
	    result = 31 * result + c;
	    return result;
	  }
	}
	```

Person类有三个关键域gender、age和name，而且分别属于不同的类型boolean、int和String。按照前面介绍的步骤很容易实现hashCode，经过测试，相等的实例都具有相等的散列码。

### 简单用法

上面介绍的有点复杂，其实从JDK1.8以来提供了更简单的方法：

* 最好使用null安全的方法`Objects.hashCode`,如果其参数是null，则会返回0
* 最好使用静态方法Double.hashCode来计算double数据的hashCode以避免创建Double对象，同样的有Integer，Float，Short等静态方法hashCode的使用。
* 如果存在数组类型的域，那么可以使用静态的Arrays.hashCode方法计算一个散列码，这个散列码由数组元素的散列码组成。
* 最简单的做法就是调用静态方法**Objects.hash(Object... values)**。 它可以非常方便的得到多个域组合后的散列值。 比如上面Person类的hashCode方法可以简单的写为：
	```java  
	@Override
	public int hashCode() {
		return Objects.hash(gender, age, name);
	}
	```  

# 3. toString方法(CoreJava1-P172)：

  