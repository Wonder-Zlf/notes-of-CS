# Java反射及实例

## 1、什么是java反射

反射，可以理解为将一个对象的所有信息暴露出去。相应的，反射也包含了对应的这个类的动态构建。

每个类都有一个class对象，包含了与类有关的信息。当编一个一个新类时，会产生一个同名的.class文件，该文件内容保存着Class对象。类加载相当于Class对象的加载，类在第一次使用时才动态加载到JVM中。也可以使用class.forname()这种方式来控制类的加载，该方法会返回一个class对象。

class和java.lang.reflec一起对反射提供支持，java.lang.reflect类库主要包含了Field\method\constructor三个类。

反射的作用：运行时判断对象、构造类的对象、判断一个类具有的成员变量和方法、调用对象的方法、生成动态代理

tips: 反射像一个镜子，类过来照镜子就会有一个“对象”生成，“对象”有手有脚有成员方法和属性。

## 2、通过反射获取类的方法

```
//1、第一种方式-->Class.forName("类名字符串");
//注:类名字符串是"包名+类名" 返回Class的对象。(这种是最常用的方法)
Class c1=Class.forName("csdn.Student");
//2、第二种方式-->先创建对象,再用对象调用getClass()方法，即实例对象.getClass().返回运行时类。
//任何一个java对象都有getClass()方法
Student s=new Student();
Class c2 = s.getClass();
//3、第三种方式-->类名.class。返回Class的对象。(每个类都有class属性)
Class c3=Student.class;

```

## 3、反射应用实例

### 3.1 基本对象类

```
public class Person {
    public String name;// 姓名
    public int age;// 年龄

    public Person() {
        super();
    }

    public Person(String name, int age) {
        super();
        this.name = name;
        this.age = age;
    }

    public String showInfo() {
        return "name=" + name + ", age=" + age;
    }
}


public class Student extends Person implements Study {
    public String className;// 班级
    private String address;// 住址

    public Student() {
        super();
    }

    public Student(String name, int age, String className, String address) {
        super(name, age);
        this.className = className;
        this.address = address;
    }

    public Student(String className) {
        this.className = className;
    }

    public String toString() {
        return "姓名：" + name + ",年龄：" + age + ",班级：" + className + ",住址："
                + address;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public void printStudent(){
        System.out.println("this is Class Student");
    }

}


public interface Study {
}

```

### 3.2 反射获得属性

```
package reflection;

import java.lang.reflect.Field;

public class TestAttributes {

    public static void main(String[] args) {
        Class stu = null;
        try {
            stu = Class.forName("reflection.Student");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }

        // 获取对象的所有公共属性
        Field[] fields = stu.getFields();
        for (Field f: fields) {
            System.out.println(f);
        }
        System.out.println("分割线--------------------------");
        // 获取对象所有属性，但不包含继承的
        Field[] declaredFields = stu.getDeclaredFields();
        for (Field df :  declaredFields){
            System.out.println(df);
        }
    }
}


// 运行结果
public java.lang.String reflection.Student.className
public java.lang.String reflection.Person.name
public int reflection.Person.age
分割线--------------------------
public java.lang.String reflection.Student.className
private java.lang.String reflection.Student.address

```

### 3.3 反射获得方法

```
package reflection;

import java.lang.reflect.Method;

public class TestMethod {

    public static void main(String[] args){
        Class stu = null;
        try {
            stu = Class.forName("reflection.Student");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        // 获取对象的所有公共方法
        Method[] methods = stu.getMethods();
        for (Method m : methods) {
            System.out.println(m);
        }
        System.out.println("分割线--------------------------");
        // 获取对象所有方法，但不包含继承的
        Method[] declaredMethods = stu.getDeclaredMethods();
        for (Method dm : declaredMethods) {
            System.out.println(dm);
        }
    }
}

// 运行结果
public java.lang.String reflection.Student.toString()
public java.lang.String reflection.Student.getAddress()
public void reflection.Student.printStudent()
public void reflection.Student.setAddress(java.lang.String)
public java.lang.String reflection.Person.showInfo()
public final void java.lang.Object.wait(long,int) throws java.lang.InterruptedException
public final native void java.lang.Object.wait(long) throws java.lang.InterruptedException
public final void java.lang.Object.wait() throws java.lang.InterruptedException
public boolean java.lang.Object.equals(java.lang.Object)
public native int java.lang.Object.hashCode()
public final native java.lang.Class java.lang.Object.getClass()
public final native void java.lang.Object.notify()
public final native void java.lang.Object.notifyAll()
分割线--------------------------
public java.lang.String reflection.Student.toString()
public java.lang.String reflection.Student.getAddress()
public void reflection.Student.printStudent()
public void reflection.Student.setAddress(java.lang.String)

```

### 3.4 反射调用对象方法

```
package reflection;

import java.lang.reflect.Constructor;
import java.lang.reflect.Method;

public class TestReflect {

    public static void main(String[] args){
        try{
            // 获取class对象
            Class c = Class.forName("reflection.Student");
            Student stu1 = (Student) c.newInstance();
            stu1.printStudent();

            // 第一种方法，实例化默认构造方法，调用set赋值
            stu1.setAddress("纽约布鲁克林");
            System.out.println(stu1);

            // 第二种方法，取得全部的构造函数，使用构造函数赋值
            Constructor<Student> constructor = c.getConstructor(String.class, int.class, String.class, String.class);
            Student stu2 = (Student) constructor.newInstance("Mart",87,"Class 0","纽约");
            System.out.println(stu2);

            // 获取方法并执行方法
            Method show = c.getMethod("showInfo");
            Object obj = show.invoke(stu2);
            System.out.println(obj);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

// 运行结果
this is Class Student
姓名：null,年龄：0,班级：null,住址：纽约布鲁克林
姓名：Mart,年龄：87,班级：Class 0,住址：纽约
name=Mart, age=87

```

