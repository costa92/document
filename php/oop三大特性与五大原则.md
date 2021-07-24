# oop 三大特性与五大原则

### OOP的核心思想 封装 、继承 、 多态

对象由数据和容许的操作组成的封装体，与客观实体有直接对应关系。一个对象类定义了一组具有相似性质的对象。而继承性是具有层次关系的类的属性和操作进行共享的一种方式。所谓面向对象就是基于对象概念，以对象为中心，以类和继承为构造机制，来认识、理解、刻画客观世界和设计，构造响应的软件系统。

###OOP的基本思想：

把组件的实现和接口分开，并且让组件具有多态性

OOP强调对象的抽象、封装、继承、多态。我们说的程序设计是由 数据结构+算法 组成的。OOP下的对象是以编程为中心，是面向程序的对象。

对象的产生：①以原型（prototype）对象为基础产生新的对象 ② 是以类（class）为基础产生对象

**封装：** 也就是把客观事物封装成抽象类，而且类可以把自己的数据和方法只让可信的类或对象操作，对不可行的进行信息隐藏。简单的说，一个类就是封装了一数据及一些操作这些数据代码的逻辑实体。再一个对象内部，某些代码或某些数据可以时私有的，不能被外界访问

```php
<?php 
  class MyClass {
  	public $age;
  	protected $address;
  	private $ider;
  
  	public function hello(){
      	echo "hello,my age is:".$this->age;
    }
  }

 $myClass = new MyClass();
 $myClass->age = 10;
 $myClass->hello();

```



**继承：** 可以让一个类型的对象获得另一个类型对象属性的方法，他支持按级分类的概念。它可以使用现有类的所有功能，并在无需重新编写原来类的情况下对这些功能进行扩展。通过继承创建的新类称为 派生类 或 子类 ，被继承的类称为 基类 、父类、超类。继承有两类：实现继承和接口继承，实现继承：直接使用基类的属性和方法无需额外的编码能力，接口继承是仅使用属性和方法名，必须提供实现的能力

实例：

```php
<?php
  class Person {
  	public function arm(){
      echo "这是父类的手臂".PHP_EOL;
    }
  }

	class Child extends  Person {
    public function footer(){
      echo "这是子类的脚".PHP_EOL;
    }
  }
$child = new Child();
$child->arm(); // 这是父类的手臂
$child->footer(); //这是子类的脚

```



**多态：** 一个类实例的相同方法在不同情形有不同表现形式。同一操作（方法）作用于不同的对象时，可以有不同的解释，产生不同的执行结果。多态机制使具有不同内部结构的对象可以共享相同的外部接口。这意味着，虽然针对不同对象的具体操作不同，但通过一个公共的类，它们（那些操作）可以通过相同的方式予以调用。

```php
<?php
  class animal{
  	public function can(){
       echo "this function weill be re-write in the children";
    }  
  }

	class cat extends animal{
    	public function can(){
       echo "I can climb";
    }  
  }
  
  class dog extends animal{
     	public function can(){
       echo "I can swim";
    }  
  }

function test($obj){
  $obj->can();
}

test(new cat());
test(new dog());
```



## **五大基本原则**

**单一职责原则SRP(Single Respinsiblity principle)：** 是指一个类的功能要单一，不能包罗万象。

**开放封闭原则OCP（Open-close principle）：** 一个模块在扩展性方面应该是开放的而再更改性方面应该是封闭。比如：一个网络模块，原来只服务端功能，而现在要加入客户端功能，那么应当在不用修改服务端功能代码的前提下，就能够增加客户端功能的实现代码，这要求在设计之初，就应当将服务端和客户端分开，公共部分抽象出来。

**替换原则（the Liskov Substitution Principle LSP**  子类应当可以替换父类并出现在父类出现的地方。

**依赖原则 ** 具体依赖抽象，上层依赖下层：假设B是较A低的模块，但B需要使用到A的功能，这个时候，B不应当直接使用A中的具体类： 而应当由B定义一抽象接口，并由A来实现这个抽象接口，B只使用这个抽象接口：这样就达到
了依赖倒置的目的，B也解除了对A的依赖，反过来是A依赖于B定义的抽象接口。通过上层模块难以避免依赖下层模块，假如B也直接依赖A的实现，那么就可能造成循环依赖。一个常见的问题就是编译A模块时需要直接包含到B模块的cpp文件，而编译B时同样要直接包含到A的cpp文件

**接口分离原则：** 模块之间要通过抽象接口隔离开，而不是通过类强耦合起来