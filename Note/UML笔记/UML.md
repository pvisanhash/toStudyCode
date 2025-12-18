Unified Modeling Language，UML统一建模语言，是用来设计软件的可视化建模语言。

>UML相关知识: [UML](https://www.fynote.com/d/2979 "UML")

# 类图 Class Diagram

## 单个类的表示

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202212161408630.png)

类分为类名(class name)、属性(field) 和方法(method) 且带有分割线的矩形来表示。

常用的可见性的符号分为三种

- `-` ：private
- `#` ：protected
- `+` ：public

属性的完整表示方式是： `可见性 名称 ：类型 [ = 缺省值]`

方法的完整表示方式是： `可见性 名称(参数列表) [ ： 返回类型]`

## 多个类的关系表示

### 关联关系

关联关系是对象之间的一种**引用关系（相当于成员变量）**，用于表示一类对象与另一类对象之间的联系，如老师和学生、师傅和徒弟、丈夫和妻子等。关联关系是类与类之间最常用的一种关系，分为一般关联关系、聚合关系和组合关系。我们先介绍。

1. 一般关联

一般关联又可以分为单向关联，双向关联，自关联。

- 单向关联

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202212161423604.png)

在UML类图中，单向关联用一个带箭头的实线表示。上图表示每个顾客都有一个地址，这通过让Customer类持有一个类型为Address的成员变量类实现。

- 双向关联

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202212161424304.png)

在UML类图中，双向关联用一个不带箭头的直线表示。双向关联就是双方各自持有对方类型的成员变量。上图中在Customer类中维护一个 `List<Product>`，表示一个顾客可以购买多个商品；在Product类中维护一个Customer类型的成员变量表示这个产品被哪个顾客所购买。

- 自关联

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202212161425385.png)

在UML类图中，用一个带有箭头且指向自身的线表示。自关联上图的意思就是Node类包含类型为Node的成员变量，也就是“自己包含自己”。

2. 聚合关联

聚合关联是关联关系的一种，是强关联关系，聚合关联也是通过成员对象来实现的，是整体和部分之间的关系（成员对象是整体对象的一部分，但是成员对象可以脱离整体对象而独立存在）。例如，学校与老师的关系，学校包含老师，但如果学校停办了，老师依然存在。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202212161428848.png)

在 UML 类图中，聚合关系可以用带空心菱形的实线来表示，菱形指向整体。上图所示是大学和教师的关系图。

3. 组合关联

组合关联是关联关系的一种，是强关联关系，组合关联也是通过成员对象来实现的，组合关联表示类之间的整体与部分的关系，但它是一种**更强烈**的聚合关系。在组合关系中，整体对象可以控制部分对象的生命周期，一旦整体对象不存在，部分对象也将不存在，部分对象不能脱离整体对象而存在。例如，头和嘴的关系，没有了头，嘴也就不存在了。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202212161432602.png)

在 UML 类图中，组合关系用带实心菱形的实线来表示，菱形指向整体。上图所示是头和嘴的关系图。

### 依赖关系

依赖关系是一种使用关系，它是对象之间耦合度最弱的一种关联方式，是临时性的关联。在代码中，某个类的方法通过局部变量、方法的参数或者对静态方法的调用来访问另一个类（被依赖类）中的某些方法来完成一些职责。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202212161434917.png)

在 UML 类图中，依赖关系使用带箭头的虚线来表示，箭头从使用类指向被依赖的类。上图所示是司机和汽车的关系图，司机驾驶汽车。

### 继承关系

继承关系是对象之间耦合度最大的一种关系，表示一般与特殊的关系，是父类与子类之间的关系，是一种继承关系。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202212161436724.png)

在 UML 类图中，泛化关系用带空心三角箭头的实线来表示，箭头从子类指向父类。在代码实现时，使用面向对象的继承机制来实现泛化关系。例如，Student 类和 Teacher 类都是 Person 类的子类，其类图如上图所示。

### 实现关系

实现关系是接口与实现类之间的关系。在这种关系中，类实现了接口，类中的操作实现了接口中所声明的所有的抽象操作。

![](https://raw.githubusercontent.com/pvisanhash/PicSiteRepo1/main/note/img/202212161437128.png)

在 UML 类图中，实现关系使用带空心三角箭头的虚线来表示，箭头从实现类指向接口。例如，汽车和船实现了交通工具，其类图如上图所示。
