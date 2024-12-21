简介
--

### 什么是xml?

xml（Extensive Makeup Language）是可扩展的标记性语言

### xml的作用

xml的主要作用:

1.  用来保存数据,而且这些数据具有自我描述性.
2.  它还可以作为项目或者模板的配置文件
3.  还可以作为网络传输数据的格式(现在已JSON为主)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--声明xml的版本，和xml中的编码-->    
<students>    
  <student sno="1"><!--sno:代表的是student标签的属性-->        
    <name>pjp</name><!--pjp代表是name标签的文本内容/文本值-->        
    <age>21</age><!--元素节点-->        
    <sex>男</sex>    
  </student>
</students>
```

### 细节

1.在xml中只可以定义一个根节点，并且是一个双标签

2.xml中定义的标签名称可以任意定义，建议遵循标识符命名规范

3.在书写属性的时候必须要使用`""`引出

4.在xml中严格区分大小写

5.xml中转义字符

*   `&lt; `小于号转义
*   `&gt;` 大于号转义
*   `&amp;` &符号转义
*   `&apos; `单引号转义
*   `&quot; `双引号转义

6.转义标签

`<![CDATA[ 中间的内容会原封不动输出 ]]>`

DTD
---

### 1.简介

DTD（document type definition）文档类型定义

DTD:文档约束，类似于集合中的泛型 作用就是限制保存到xml中标签

约束XML文件中可以包含哪些元素，哪些属性，及元素个数和元素中间的关系和元素顺序

在包含DTD的XML文件中，如果XML内容不满足DTD要求，则会报错

### 2.分类

1.内部DTD

2.外部DTD

3.公共DTD（引入网络中DTD）

#### 2.1内部DTD

```xml
<!DOCTYPE students[
        <!ELEMENT students (student*)>
        <!ELEMENT student (name,age,sex)>
        <!ELEMENT name (#PCDATA)>
        <!ELEMENT age (#PCDATA)>
        <!ELEMENT sex (#PCDATA)>
 
        <!ATTLIST student id CDATA #REQUIRED>
        <!ATTLIST name class CDATA #FIXED "qwe">
        <!ATTLIST age test CDATA #IMPLIED>]>
 
<students>
    <student id="1">
        <name class="qwe"></name>
        <age test=""></age>
        <sex></sex>
    </student>
</students>
```

#### 细节

students:只能有一对students标签

​	student标签出现的次数：

​	student？：0~1次

​	student+：至少1次

​	student\*:0~多次



#PCDATA(parsed character data):元素是字符串类型（不能再有子元素）

ATTLIST:属性

student:哪个标签指定属性

​	id:指定的属性名

​	CDATA：属性控制

​	#REQUIRED：必须有

​	#FIXED"值"：固定值

​	#IMPLIED：可有可无

#### 2.2外部DTD

新建一个student.dtd文件，编写外部DTD

```xml-dtd
<!ELEMENT students (student*)>
        <!ELEMENT student (name,age,sex)>
        <!ELEMENT name (#PCDATA)>
        <!ELEMENT age (#PCDATA)>
        <!ELEMENT sex (#PCDATA)>
 
        <!ATTLIST student id CDATA #REQUIRED>
        <!ATTLIST name class CDATA #FIXED "qwe">
        <!ATTLIST age test CDATA #IMPLIED>
```

在xml文件中引入外部DTD

```xml
<!DOCTYPE students SYSTEM "student.dtd">
    <!--student.dtd 对应外部dtd文件的名称-->
<students>
    <student id="">
        <name></name>
        <age></age>
        <sex></sex>
    </student>
</students>
```

#### 2.3公共DTD（使用最多）

公共DTD是一些开源组织编写的DTD，并且已经发布在互联网中

**语法：**

```
<!DOCTYPE 根元素

                 PUBLIC "DTD标识名"

                 "公共DTD的URI">
```



例如MyBatis使用的config.xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="jdbc.properties"/>
 
    <typeAliases>
        <package name="com.pjp.mybatis.POJO"/>
    </typeAliases>
    
 
    <environments default="development">
 
    
    </environments>
 
    <mappers>
        <package name="com.pjp.mybatis.mapper"/>
    </mappers>
</configuration>
```

### 3.总结

DTD是较简单的语法检查机制，整体语法较简单，功能较单一

当需要对xml文件结构跟新时，需要修改整个DTD文件，不灵活

XSD
---

### 1.简介

XSD(XML Schema Definition)：XML模式定义

DTD的升级版，解决了DTD使用时的不宜扩展问题，并且提供更强大的功能

### 2.定义XSD

新建xxx.xsd文件

```xml
<!--
    声明xsd约束
    a:自定义名称
    schema：约束（固定）
-->
<a:schema xmlns:a="http://www.w3.org/2001/XMLSchema">
    <!--element:指定元素-->
    <a:element name="students">
        <!--设置标签类型是复杂类型-->
        <a:complexType>
            <!-- 指定内部标签的顺序-->
            <a:sequence>
                <!--   maxOccurs：指定可以有多少个标签
                       maxOccurs="2": student标签最多两次
                       maxOccurs="unbounded":  0~多次
                       不写maxOccurs默认只能一次
                              -->
                <a:element name="studnet" maxOccurs="unbounded">
                    <!--设置stuent标签类型是复杂类型-->
                    <a:complexType>
                        <!-- 指定student标签的顺序-->
                        <a:sequence>
                            <!--        type="a:string"  name的属性                  -->
                            <a:element name="name" type="a:string"></a:element>
                            <a:element name="age" type="a:int"></a:element>
                            <a:element name="sex" type="a:string"></a:element>
                        </a:sequence>
                        <!--
                              给student标签指定属性id
                              use：属性的设置
                               optional:可选的（可写可不写）
                               required：必须存在
                               prohibited：禁用
                               fixed="值"：固定值
                                              -->
                        <a:attribute name="id" use="required" fixed=""></a:attribute>
                    </a:complexType>
                </a:element>
            </a:sequence>
        </a:complexType>
    </a:element>
 
</a:schema>
```

### 3.引用XSD

在xml文件中引用xsd文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<students xmlns:xsi="http://www.w3.org/2001/XMLSchema"
          xsi:noNamespaceSchemaLocation="user.xsd">
</students>
```

XML解析
--------------------------------------------------------------------------------------

### 1.简介

在java中提供了两种XML解析方式：DOM，SAX

### 2.DOM解析

#### 2.1DOM方式

DOM：Document Object Model，文档对象模型。在应用程序中，基于DOM的XML分析器将一个XML文档转换成一个对象模型的集合（通常称DOM树），应用程序正是通过对这个对象模型的操作，来实现对XML文档数据的操作。通过DOM接口，应用程序可以在任何时候访问XML文档中的任何一部分数据，因此，这种利用DOM接口的机制也被称作随机访问机制。

```java
 @Test
    public void DOMtest() throws ParserConfigurationException, IOException, SAXException {
//      DocumentBuilderFactory:定义了一个API，使应用程序工厂获得解析器生成DOM对象树的XML文档
//        newInstance()
//        获得一个 DocumentBuilderFactory新实例。
        DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
//        newDocumentBuilder()
//        创建一个新的实例的一个 DocumentBuilder使用当前的配置参数。
        DocumentBuilder db = dbf.newDocumentBuilder();
//        parse(File f)
//        解析给定的文件作为XML文档的内容并返回一个新的 Document DOM对象。
        Document document = db.parse(new File("src/students2.xml"));
//        getElementsByTagName(String tagname)
//        返回与给定的标签名称按文档顺序的一 NodeList所有 Elements和包含在文档。
        NodeList rootList = document.getElementsByTagName("students");
 
//        xml中仅会存在一个跟标签，获取这个标签
        Node root = rootList.item(0);
 
//        System.out.println(root.getNodeName());
        NodeList childNodes1 = root.getChildNodes();
        for (int i = 0; i < childNodes1.getLength(); i++) {
//            System.out.println(childNodes.item(i));
            Node node1 = childNodes1.item(i);
            if (node1.getNodeType() == 1) {
//                getNodeName()
//                这个节点的名称，取决于它的类型  Text-->"#text" 。
                System.out.println(node1.getNodeName());
                NodeList childNodes2 = node1.getChildNodes();
                for (int j = 0; j < childNodes2.getLength(); j++) {
                    Node node2 = childNodes2.item(j);
                    if (node2.getNodeType() == 1) {
//                        getTextContent()
//                        此属性返回此节点及其子节点的文本内容
                        System.out.println("\t"+node2.getNodeName() + "  " + node2.getTextContent());
                    }
                }
            }
        }
    }
```

 输出

    student
    	name  pjp
    	age  21
    	sex  男
    student
    	name  jack
    	age  22
    	sex  男

#### 2.2DOM优缺点

优点：

1、形成了树结构，有助于更好的理解、掌握，且代码容易编写。

2、解析过程中，树结构保存在内存中，方便修改。

缺点：

1、由于文件是一次性读取，所以对内存的耗费比较大。

2、如果XML文件比较大，容易影响解析性能且可能会造成内存溢出。

#### 2.3主要的三种节点

获取每一个子节点 getNodeType() 节点类型

```java
/**
     * The node is an <code>Element</code>.
     */
    public static final short ELEMENT_NODE              = 1; //元素节点
    /**
     * The node is an <code>Attr</code>.
     */
    public static final short ATTRIBUTE_NODE            = 2;//属性节点
    /**
     * The node is a <code>Text</code> node.
     */
    public static final short TEXT_NODE                 = 3;//文本节点
```

#### 2.4DOM生成XMl（不常用）

```java
@Test
public void domCreateTest() throws ParserConfigurationException, TransformerException, FileNotFoundException {
        //1.获取文档构建器工厂对象
        DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
        //2.构建器工厂对象 获取 构建器对象
        DocumentBuilder db = dbf.newDocumentBuilder();
        //3.创建文档对象
        Document document = db.newDocument();
        //4.创建teachers标签
        Element teachers = document.createElement("teachers");
        //5.创建teacher标签
        Element teacher = document.createElement("teacher");
        teacher.setAttribute("id", "1"); //设置属性
        //6.创建name标签
        Element name = document.createElement("name");
        name.setAttribute("class", " "); //设置属性
        name.setTextContent("pjp");
        //7.创建age标签
        Element age = document.createElement("age");
        age.setTextContent("21");
        //8.创建sex标签
        Element sex = document.createElement("sex");
        sex.setTextContent("男");
 
        //9.设置标签之间的关系
        /* 添加teacher的子标签 */
        teacher.appendChild(name);
        teacher.appendChild(age);
        teacher.appendChild(sex);
        /* 添加teachers的子标签 */
        teachers.appendChild(teacher);
        /* 添加文档的子标签 */
        document.appendChild(teachers);
        /* 设置为独立的xml */
        document.setXmlStandalone(true);
 
        //10.将document对象变为xml
        //10.1 创建转换器工厂对象
        TransformerFactory tff = TransformerFactory.newInstance();
        //10.1 根据转换器工厂对象 获取 转换器
        Transformer tf = tff.newTransformer();
        tf.setOutputProperty(OutputKeys.INDENT, "yes");
        tf.setOutputProperty("{http://xml.apache.org/xslt}indent-amount", "2");
        //10.3 将document转化为xml  通过流输出到指定的位置
        /*
         * 参数1: 指定document源
         * 参数2: 输出的位置
         * */
        tf.transform(new DOMSource(document), new StreamResult(new FileOutputStream("src\\teacher.xml")));
    }
```

### 3.SAX解析

#### 3.1SAX方式

SAX：Simple API for XML。XML简单应用程序接口。SAX提供的访问模式是一种顺序模式，这是一种快速读写XML数据的方式。当使用SAX分析器对XML文档进行分析时，会触发一系列事件，并激活相应的事件处理函数，应用程序通过这些事件处理函数实现对XML文档的访问，因而SAX接口也被称作事件驱动接口。

```java
@Test
    public void saxTest() throws ParserConfigurationException, SAXException, IOException {
        SAXParserFactory saxParserFactory = SAXParserFactory.newInstance();
        SAXParser saxParser = saxParserFactory.newSAXParser();
        saxParser.parse(new File("src/students2.xml"),new MyHandler());
    }
 
class MyHandler extends DefaultHandler{
    @Override
    public void startDocument() throws SAXException {
        System.out.println("文档解析开始");
    }
 
    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
        System.out.println(qName+"标签解析开始");
    }
    @Override
    public void characters(char[] ch, int start, int length) throws SAXException {
        String s = new String(ch,start,length);
        System.out.println("解析文本值为："+s);
    }
 
    @Override
    public void endElement(String uri, String localName, String qName) throws SAXException {
        System.out.println(qName+"标签解析结束");
    }
 
    @Override
    public void endDocument() throws SAXException {
        System.out.println("文档解析结束");
    }
}
```

 输出

    文档解析开始
    students标签解析开始
    解析文本值为：
        
    student标签解析开始
    解析文本值为：
            
    name标签解析开始
    解析文本值为：pjp
    name标签解析结束
    解析文本值为：
            
    age标签解析开始
    解析文本值为：21
    age标签解析结束
    解析文本值为：
            
    sex标签解析开始
    解析文本值为：男
    sex标签解析结束
    解析文本值为：
        
    student标签解析结束
    解析文本值为：
     
    students标签解析结束
    文档解析结束

解析时将换行视为Text

#### 3.2SAX方式优缺点

优点：

1、采用事件驱动模式，对内存耗费比较小。

2、适用于只处理XML文件中的数据时。

缺点：

1、编码比较麻烦。

2、很难同时访问XML文件中的多处不同数据。

#### 3.3SAX生成XML

    @Test
    public void saxCreateTest() throws TransformerConfigurationException, FileNotFoundException, SAXException {
        //1.创建转换器工厂
        SAXTransformerFactory stff = (SAXTransformerFactory) SAXTransformerFactory.newInstance();
        //2.根据工厂获取转换器
        /*
         * 传输
         * 创建xml
         * */
        TransformerHandler th = stff.newTransformerHandler();
     
        Transformer transformer = th.getTransformer();
        transformer.setOutputProperty(OutputKeys.INDENT, "yes");
        transformer.setOutputProperty(OutputKeys.ENCODING, "utf-8");
        transformer.setOutputProperty("{http://xml.apache.org/xslt}indent-amount", "2");
     
        th.setResult(new StreamResult(new FileOutputStream("src\\emp.xml")));
        th.startElement(null, null, "emps", null);
        AttributesImpl attributes = new AttributesImpl();
        attributes.addAttribute(null, null, "id", null, "1");
        th.startElement(null, null, "emp", attributes);
        th.startElement(null, null, "name", null);
        char[] chars = "pjp".toCharArray();
        th.characters(chars, 0, chars.length);
        th.endElement(null, null, "name");
        th.startElement(null, null, "age", null);
        char[] chars1 = "21".toCharArray();
        th.characters(chars1, 0, chars1.length);
        th.endElement(null, null, "age");
        th.startElement(null, null, "sex", null);
        char[] chars2 = "男".toCharArray();
        th.characters(chars2, 0, chars2.length);
        th.endElement(null, null, "sex");
        th.endElement(null, null, "emp");
        th.endElement(null, null, "emps");
        th.endDocument();
    }

通过DOM/SAX解析XMl到实体对象
-------------------

xml

```
<?xml version="1.0" encoding="UTF-8" ?>
 
<students>
    <student sno="1">
        <name>pjp</name>
        <age>21</age>
        <sex>男</sex>
    </student>
</students>
```

 写一个实体student类

```java
public class student {
    private String name;
    private int age;
    private String sex;
 
    public student(String name, int age, String sex) {
        this.name = name;
        this.age = age;
        this.sex = sex;
    }
 
    public student() {
    }
 
    public String getName() {
        return name;
    }
 
    public void setName(String name) {
        this.name = name;
    }
 
    public int getAge() {
        return age;
    }
 
    public void setAge(int age) {
        this.age = age;
    }
 
    public String getSex() {
        return sex;
    }
 
    public void setSex(String sex) {
        this.sex = sex;
    }
 
    @Override
    public String toString() {
        return "student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", sex='" + sex + '\'' +
                '}';
    }
}
```

### DOM

```java
@Test
    public void test() throws ParserConfigurationException, IOException, SAXException {
        DocumentBuilderFactory dbd = DocumentBuilderFactory.newInstance();
        DocumentBuilder documentBuilder = dbd.newDocumentBuilder();
        Document document = documentBuilder.parse(new File("src\\students2.xml"));
        NodeList rootlist = document.getElementsByTagName("students");
        Node root1 = rootlist.item(0);
        NodeList childNodes1 = root1.getChildNodes();
        List<student> students = new ArrayList<>();
        for (int i = 0; i < childNodes1.getLength(); i++) {
            Node node1 = childNodes1.item(i);
            if (node1.getNodeType() == 1) {
                student student = new student();
                NodeList childNodes2 = node1.getChildNodes();
                for (int j = 0; j < childNodes2.getLength(); j++) {
                    Node node2 = childNodes2.item(j);
                    if (node2.getNodeType() == 1) {
                        if ("name".equals(node2.getNodeName())) {
                            student.setName(node2.getTextContent());
                        } else if ("age".equals(node2.getNodeName())) {
                            student.setAge(Integer.parseInt(node2.getTextContent()));
                        } else if ("sex".equals(node2.getNodeName())) {
                            student.setSex(node2.getTextContent());
                        }
                    }
                }
                students.add(student);
            }
        }
        System.out.println(students);
    }
```

 输出

    [student{name='pjp', age=21, sex='男'}]

### SAX

```java
@Test
    public void saxTest() throws ParserConfigurationException, SAXException, IOException {
        SAXParserFactory saxParserFactory = SAXParserFactory.newInstance();
        SAXParser saxParser = saxParserFactory.newSAXParser();
        saxParser.parse(new File("src/students2.xml"), new MyHandler());
    }
```

```java
class MyHandler extends DefaultHandler {
    private List<student> list = new ArrayList<>();
    private student student;
    private String tagName;//定义当前标签名称
 
    @Override
    public void startDocument() throws SAXException {
        System.out.println("文档解析开始");
    }
 
    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
        if ("student".equals(qName)) {
            student = new student();
        }
        tagName = qName;
        System.out.println(qName + "标签解析开始");
    }
 
    @Override
    public void characters(char[] ch, int start, int length) throws SAXException {
        String s = new String(ch, start, length);
        System.out.println("解析文本值为：" + s);
//        &&后面的是判断student对象name，age，sex是否存在，如果存在则新new一个student对象，不存在则向里面set内容
        if ("name".equals(tagName)&&student.getName()==null) {
            student.setName(s);
        } else if ("age".equals(tagName)&&student.getAge()==0) {
            student.setAge(Integer.parseInt(s));
        } else if ("sex".equals(tagName)&&student.getSex()==null) {
            student.setSex(s);
        }
    }
 
    @Override
    public void endElement(String uri, String localName, String qName) throws SAXException {
//        解析结束将student对象添加到集合
        if ("student".equals(qName)){
            list.add(student);
        }
        System.out.println(qName + "标签解析结束");
    }
 
    @Override
    public void endDocument() throws SAXException {
        System.out.println("文档解析结束"+list);
    }
}
```

 输出

    [student{name='pjp', age=21, sex='男'}]

