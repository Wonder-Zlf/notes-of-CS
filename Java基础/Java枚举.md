# Java枚举

枚举是一个特殊的类，一般表示一组常量，比如一年中的4个季节。各个常量使用逗号分割。

每个枚举都是通过class在内部实现，且所有的枚举值都是public static final的。

枚举和普通类一样可以用自己的变量、方法和构造函数，构造函数只能用private访问修饰符，所以外部无法调用。

枚举既可以包含具体方法，也可以包含抽象方法。如果枚举包含抽象方法，则每个枚举类的实例都必须实现它。

## 1、构建枚举

### 1.1 枚举常量

```
/**
 * 枚举使用练习
 *
 * @author z00465759
 * @since 2020-08-03
 */
class enumTest {

    enum Color{
        Red, Green, Blue;
    }
}

```

### 1.2 枚举类成员

```
/**
 * 枚举使用练习
 *
 * @author z00465759
 * @since 2020-08-03
 */
class enumTest {

    enum Color{
        Red, Green, Blue;

        // 构造函数
        private Color(){
            System.out.println("this is "+this.toString());
        }

        // 成员函数
        public void colorInfo(){
            System.out.println("that col");
        }
    }

    public static void main(String[] args){

        Color coco = Color.Blue;
        coco.colorInfo();
    }

}

```

## 2、使用枚举

### 2.1 for迭代

```
/**
 * 枚举使用练习
 *
 * @author z00465759
 * @since 2020-08-03
 */
class enumTest {

    enum Color{
        Red, Green, Blue;
    }

    public static void main(String[] args){
        for (Color ele : Color.values()){
            System.out.println(ele);
        }
    }

}
```

### 2.2 switch

```
 * 枚举使用练习
 *
 * @author z00465759
 * @since 2020-08-03
 */
class enumTest {

    enum Color{
        Red, Green, Blue;
    }

    public static void main(String[] args){
        Color myEle = Color.Red;

        switch (myEle){
            case Red:
                System.out.println(1);
            case Green:
                System.out.println(2);
            case Blue:
                System.out.println(3);
        }
    }

}
```

### 2.3 常用方法

```
/**
 * 枚举使用练习
 *
 * @author z00465759
 * @since 2020-08-03
 */
class enumTest {

    enum Color{
        Red, Green, Blue;
    }

    public static void main(String[] args){

        // 迭代枚举
        for (Color ele: Color.values())
        {
            // 查看索引
            System.out.println(ele + " at index " + ele.ordinal());
        }

        // 使用 valueOf() 返回枚举常量，不存在的会报错 IllegalArgumentException
        System.out.println(Color.valueOf("Red"));
        // System.out.println(Color.valueOf("WHITE"));
    }

}
```

