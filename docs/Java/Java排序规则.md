# Java的排序



## 基本类型数据

基本类型数据的数组排序，从小到大，只需要`Arrays.sort(int[])`方法即可，但从大到小排序，有三种方法

1. 排序算法，冒泡、快速等排序方法
2. 转换为包装类排序，会开辟新的数组空间，浪费内存
3. 转换为List集合，原理同2

```java
public static void main(String[] args) {
        //一维数组的排序
        int[] arr = {1,3,5,4,2};

        //从小到大
        Arrays.sort(arr);
        System.out.println(Arrays.toString(arr));

        //从大到小
        //因为Comparable接口只能使用于类对象，所以Arrays不允许定制排序
        //采用1.排序算法 2.转换为包装类再转换基本数据类型 3.转换为List集合，原理同2
        //1.排序算法，使数组从大到小排序
        for(int i = 0; i < arr.length-1; ++i){
            for(int j = 0; j < arr.length - 1-i; ++j){
                if(arr[j] < arr[j+1]){
                    int tmp = arr[j];
                    arr[j] = arr[j+1];
                    arr[j+1] = tmp;
                }
            }
        }
        //2.转换为包装类再转换基本数据类型,缺点很明显，会开辟一个新的数组空间。
        Integer[] arr1 = new Integer[arr.length];
        for(int i = 0; i < arr.length; ++i){
            arr1[i] = arr[i];
        }
        Arrays.sort(arr1, new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        });

        //3.转换为List集合，原理同2
        List<Integer> list = new ArrayList<>();
        for(int i : arr){
            list.add(i);
        }
        Collections.sort(list, new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        });
    }
```



## Comparable接口的 - 自然排序

### **String、包装类**

本身实现了Comparable接口，并且重写了compareTo(obj)方法，给出了比较两个对象大小的方法

String：实现了接口，重写方法。

![image-20220926172516518](https://cdn.jsdelivr.net/gh/Bionic-Sheep/typora/images/image-20220926172516518.png)

Integer包装类：实现接口，重写方法，重写的方法是`compare(this.value, anotherInteger.value)`

![image-20220926172532718](https://cdn.jsdelivr.net/gh/Bionic-Sheep/typora/images/image-20220926172532718.png)

### **自定义类**

对于自定义类来说，如果需要排序：

- 首先实现 Comparable接口
- 其次重写CompareTo(obj)方法

例如：一个Goods类，属性有姓名和分数，按照分数排序

tip：如果排序的属性是包装类的话，那么重写的CompareTo方法最好是 `return 包装类.compare(this.xx, o.xx)`;

一定注意是this本类和传入的obj类比较

```java
 public class CompareTest {
 @Test
     public void test2(){
         Goods[] arr = new Goods[4];
         arr[0] = new Goods("apple", 34);
         arr[1] = new Goods("xiaomi", 12);
         arr[2] = new Goods("huawei", 24);
         arr[3] = new Goods("lenven",22);
 
         Arrays.sort(arr);
         System.out.println(Arrays.toString(arr));
 
     }
 }
 public class Goods implements Comparable{
 
     ...//省略类的属性、构造方法、getset方法

      //重写compareTo(obj)方法
     @Override
     public int compareTo(Object o) {
         if( o instanceof  Goods){//强转
             Goods goods =(Goods)o;
             if(this.price < goods.price){
                 return 1; //返回正数 goods更大
             }else if(this.price > goods.price){
                 return -1;
             }else{
                 return 0;
             }
             //方式二
		     return Double.compare(this.price, goods.price); 
         }
         throw new RuntimeException("传入的数据类型不一致！");
     }
 
 }
```





## Comparato接口-定制排序

如果想重新排序T[]数组的大小关系，重写compare()方法。

1. 背景：当元素的类型没有实现java.lang.Comparable接口但又不方便修改代码，或者**当前接口的排序规则不符合当前操作，就使用Comparato的对象来排序**
    1.  `Arrays.sort(arr, new Comparator<xxx>() { … }`
2. 重写compare(Object o1, Object o2)方法，比较o1和o2的大小：如果方法返回正整数，o1 大于 o2; 0 则相等 否则o1 小于 o2

### String、包装类

```java
public static void main(String[] args) {
        String[] strs = {"ab", "abc", "abb", "abcd"};

        Arrays.sort(strs); // ab abb abc abcd
        Arrays.sort(strs, new Comparator<String>() { 
            @Override
            public int compare(String o1, String o2) {
               // return o2.compareTo(o1); // abcd abc abb ab
                return o1.compareTo(o2); // ab abb abc abcd
            }
        });
    }
```



### 自定义类

重写方法中的实现基本同Comparable接口的重写方法实现一样，不过上一个是在自定义类重写方法，这个是Arrays.sort()里重写方法

```java
 //自定义类
 @Test
     public void test4(){
         Goods[] arr = new Goods[6];
         arr[0] = new Goods("apple", 34);
         arr[1] = new Goods("xiaomi", 12);
         arr[2] = new Goods("huawei", 34);
         arr[3] = new Goods("lenven",22);
         arr[4] = new Goods("lenven",223);
         arr[5] = new Goods("lenven1",12);
 
         **Arrays.sort(arr, new Comparator<Goods>() {**
 
             //按照产品名称从低到高排序，再按价格从高到低
             @Override
             **public int compare(Goods o1, Goods o2) {
                 if(o1 instanceof  Goods && o2 instanceof  Goods){
                     Goods g1 = (Goods) o1;
                     Goods g2 = (Goods) o2;
                     if(g1.getName().equals(g2.getName())){
                         //品牌一样
                         return -Double.compare(g1.getPrice(), g2.getPrice());
                     }else{
                         return g1.getName().compareTo((g2.getName()));
                     }
                 }
                 throw new RuntimeException("数据类型输入错误！");**
             }
         });
         System.out.println(Arrays.toString(arr));
     }

```



## 例题

有一个学生类Student，有姓名、年龄、分数三个属性，按照年龄从小到大，分数从高到低的顺序输出。

回答：实现该排序一般由两种方法，一种是实现该类了Comparable接口，然后重写CompareTo方法，一种是该类没有实现Comparable接口，并且不方便修改代码，则使用Comparator接口来，重写compare方法来比较。

如果是第一种方法呢：首先Student类继承Comparable接口，然后重写CompareTo()方法，在ComapreTo方法中先比较年龄，按小到大，当年龄相等时，再比较分数，按从大到小。如果是第二种方法呢：则在Arrays.sort()的对象数组里重写compare方法，比较方式同第一种，先比较年龄，当年龄相等再比较分数。

```java
public class testStudent {
    public static void main(String[] args) {
        String str = "123";
        student[] stus = new student[4];
        stus[0] = new student("A",19,89);
        stus[1] = new student("B",17,99);
        stus[2] = new student("C", 17,78);
        stus[3] = new student("D", 18, 88);
        //这三个孩子排序，按照年龄从小到大，分数从高到低来排序
        Arrays.sort(stus);

        for(student stu : stus){
            System.out.println(stu.getAge() + " " + stu.getName() + " " + stu.getScore());
        }
    }
}

//学生类，姓名、年龄、成绩
class student implements Comparable<student>{
    
	//	...属性和构造方法

    @Override
    public int compareTo(student o) {
						//首先按年龄从小到大排序
            if(this.age < o.age){ 
                return -1;
            }else if(this.age > o.age){
                return 1;
            }else{
						//当年龄相等，将成绩按从大到小排序
                if(this.score < o.score){
                    return 1;
                }else if(this.score > o.score){
                    return -1;
                }else{
                    return 0;
                }
            }
        
    }
}
```