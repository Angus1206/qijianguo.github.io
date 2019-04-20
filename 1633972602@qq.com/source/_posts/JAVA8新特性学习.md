---
title: JAVA8新特性学习
categories: Java
tags: java
date: 2018-03-11 22:14:14
---

### Lambda表达式
>lambda简称闭包，类似Groovy，Scala等的函数式编程，即将函数作为方法的参数。
> 一个简单的Lambda表达式由逗号分隔开的参数列表、-> 及函数体组成

<!-- more -->
例如：
```
List<String> strList = Arrays.asList("a", "b", "c");
strList.forEach(str -> System.out.println(str));
```
同：（其中str的类型是由编译器推断出来的。可省略）
```
strList.forEach((String str)-> System.out.println(str));
```
如果 **->** 后有多条语句，则需要将代码块放在 **{}** 中，即函数体中的代码块。

```
strList.forEach(str -> {
	System.out.println("输出");
	System.out.println(str);
});
```
#### Lambda可以引用成员变量和局部变量，但是他们会隐含的被 **final** 修饰。

```
String separator = ",";
List<String> strList = Arrays.asList("a", "b", "c");
strList.forEach(str -> {
	System.out.println(str + separator);
});
```
同：
```
final String separator = ",";
List<String> strList = Arrays.asList("a", "b", "c");
strList.forEach(str -> {
	System.out.println(str + separator);
});
```
#### Lambda返回值
如果函数体中只有一条语句，则无需写**return**语句，例如：

```
List<String> list2 = Arrays.asList("a", "b", "c");
list2.sort((e1, e2) -> e1.compareTo(e2));
```
同：

```
 list2.sort((e1, e2) -> {
	int compare = e1.compareTo(e2);
	return compare;
});
```
### 函数式接口（**Functional Interface**）
* 什么样的才算是函数式接口呢？ 
> 首先定义**一个接口**
> 接口中定义 **有且仅有一个** 抽象方法

例如：

```
public interface FunctionalDemo {
	// 抽象方法
	int add(int a, int b);
}
```
使用：

```
public static void main(String[] args) {
	// Java8之前是用匿名函数来实现的。
       FunctionalDemo methods = (a, b) -> {
            return a + b;
       };
       int sum = methods.add(1, 2);
       System.out.println(sum);
 }
```
函数式接口在实际应用中容易出错，当其他开发人员在你的函数式接口中添加了其他抽象方法，此时这个接口就不再是函数式接口了。如何避免这种事情的发生呢？在声明的函数式接口上添加注解 ***@FunctionalInterface***。添加注解后如果添加其他抽象方法则编译器报错。
![!在这里插入图片描述\](https://img-blog.csdnimg.cn/20181204132900553.png)](https://img-blog.csdnimg.cn/2018120413311650.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1OTc0NzU5,size_16,color_FFFFFF,t_70)在上述描述中多次提到抽象方法，主要是因为函数式接口中允许添加 

1. 默认方法，默认方法不是抽象方法。
2. 静态方法，静态方法也不是抽象方法，它已经是一个实现了的方法。静态方法，静态方法也不是抽象方法，它已经是一个实现了的方法。
3. 重写Object中的Public方法重写Object中的Public方法。

```
@FunctionalInterface
public interface FunctionalDemo {

	int add(int a, int b);
    
	/** 默认方法 */
	default void defaultMethod() {
		System.out.println("default method 1");
	}
	default void defaultMethod2(String message) {
		System.out.println(message + "：default method 2 ");
	}
	// 静态方法	
	static void printMessage(String message) {
		System.out.println(message);
	}
	// 重写Object中的方法。
	@Override
	public int hashCode();

	public boolean equals(Object obj);
}
```
使用：

```
public static void main(String[] args) {
        FunctionalDemo methods = (a, b) -> {
            return a + b;
        };
        int sum = methods.add(1, 2);
        System.out.println(sum);

        // 调用默认方法
        methods.defaultMethod();
        methods.defaultMethod2("默认方法加参数");
        // 静态方法
        FunctionalDemo.printMessage("静态方法");
}
```
结果：

```
3
default method 1
默认方法加参数：default method 2 
静态方法**
```
JDK中的栗子：

```
java.lang.Runnable,

java.util.Comparator,

java.util.concurrent.Callable
```

#### 接口的默认方法
Java8之后允许在接口中添加默认方法，默认方法不需要实现，如果有需要则可以覆盖默认方法，而抽象方法必须要实现。

接口
```
public interface Function {
    // 抽象方法
    void required();
    
    // 默认方法
    default void notRequired() {
        // 函数体
    }
}
```
实现类
```
public class FunctionalImpl implements Function {

    @Override
    public void required() {
        System.out.println("interface abstract method must override");
    }

    @Override
    public void notRequired() {
        System.out.println("interface default method not required");
    }
}

```
使用：

```
 public static void main(String[] args) {
	 Function function = FunctionalFactory.crate(FunctionalImpl::new);
        function.required();
        function.notRequired();
}
```
运行结果：

```
interface abstract method must override
interface default method not required
```
思考：怎么优雅的使用默认方法呢？
> 尽管默认方法非常强大，但是在使用默认方法时我们需要小心注意一个地方：在声明一个默认方法前，请仔细思考是不是真的有必要使用默认方法，因为默认方法会带给程序歧义，并且在复杂的继承体系中容易产生编译错误

#### 方法引用
> 方法引用提供了非常有用的语法，可以直接引用已有Java类或对象（实例）的方法或构造器。与lambda联合使用，方法引用可以使语言的构造更紧凑简洁，减少冗余代码。

例如：
```
public class Person {

    /**
     * 构造器引用
     * @param supplier 该引用接收一个空构造参数
     * @return
     */
    public static Person constructMethod(final Supplier<Person> supplier) {
        System.out.println("构造器引用, 语法是：Class::new， 构造器无参");
        return supplier.get();
    }

    /**
     * 静态方法引用
     * @param person 该引用接收一个Person参数
     */
    public static void staticMethod(Person person) {
        System.out.println("构造器引用，语法是Class::static_method, 接收到的person：" + person.toString());
    }

    /**
     * 特定方法的引用
     * @param person 该引用接收一个Person参数
     */
    public void instanceMethod(Person person) {
        System.out.println("特定对象的方法引用，语法是：Class::method, 接收到的person: " + person.toString());
    }

    /**
     * 任意方法的引用
     * 注意：无参
     */
    public void otherMethod() {
        System.out.println("任意对象的方法引用，语法是：Class::method， 无参" + this.toString());
    }

}
```
使用：

```
public class LambdaTest {

    public static void main(String[] args) {
        // 构造器引入
        Person person = Person.constructMethod(Person::new);

        List<Person> list = new ArrayList<>(Arrays.asList(person));

        // 静态方法引入
        list.forEach(Person::staticMethod);

        list.forEach(person::instanceMethod);

        list.forEach(Person::otherMethod);
    }

}

```
运行结果
```
构造器引用, 语法是：Class::new， 构造器无参
构造器引用，语法是Class::static_method, 接收到的person：domain.Person@d70c109
特定对象的方法引用，语法是：Class::method, 接收到的person: domain.Person@d70c109
任意对象的方法引用，语法是：Class::method， 无参domain.Person@d70c109
```
### Optional [官方文档](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)
> 优雅的判断 ***null***， 不再进行 ***if（obj == null） {......}***

####  慎重使用以下方式：
>    调用 isPresent() 方法时
>    调用 get() 方法时
>    Optional 类型作为类/实例属性时
>    Optional 类型作为方法参数时

```
User user = new User();
Optional<User> optional = Optional.ofNullable(user); 
if (optional.isPresent()) {
	User u = optional.get();
}
```
上面代码跟以前写的null判断有什么区别呢？

```
User user = new User();
if (user != null) {
	System.out.println(user.getName());
}
```
如何优雅的使用Optional呢？

Optional中的方法

```
    public<U> Optional<U> map(Function<? super T, ? extends U> mapper)
    public T orElse(T other)
    public T orElseGet(Supplier<? extends T> other)
    public void ifPresent(Consumer<? super T> consumer)
    public Optional<T> filter(Predicate<? super T> predicate)
    public<U> Optional<U> flatMap(Function<? super T, Optional<U>> mapper)
    public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X
```

例如：
```
User zs = new User("zs", 23, new ArrayList<>("篮球", "旅行"));   
Optional.of(zs).map(user -> user.getName()).map(name -> name.toUpperCase()).orElse(null);
```

### Stream

```
public class Streams {

    private enum Status{
        OPEN, CLOSED
    }

    private final static class Task {
        private Status status;
        private Integer points;

        Task(final Status status, final Integer points) {
            this.status  =status;
            this.points = points;
        }

        public Status getStatus() {
            return status;
        }

        public Integer getPoints() {
            return points;
        }
    }


    public static void main(String[] args) {
        Collection<Task> tasks = Arrays.asList(
            new Task(Status.OPEN, 5),
            new Task(Status.OPEN, 13),
            new Task(Status.CLOSED, 8)
        );

        // 所有OPEN的总points
        Integer openSum =
                tasks.stream()          // 将集合转化为stream
                .filter(task -> task.getStatus() == Status.OPEN)   // 过滤掉CLOSED的选项
                .mapToInt(Task::getPoints)      // mapToInt操作通过Task::getPoints这种方式调用每个task实例的getPoints方法把Task的stream转化为Integer的stream
                .sum();         // 用sum函数把所有的分数加起来
        System.out.println(" OPEN points " + openSum);


        Integer totalPoints = tasks.stream()
                .parallel()                 // 并行操作
                .map(Task::getPoints)
                .reduce(0, Integer::sum);
        System.out.println("total points " + totalPoints);

        // 按某属性分组
        Map<Status, List<Task>> group =
                tasks.stream().collect(Collectors.groupingBy(Task::getStatus));
        System.out.println("group by status: " + group);

        // 计算元素的权重
        final Collection< String > result = tasks
                .stream()                                        // Stream< String >
                .mapToInt( Task::getPoints )                     // IntStream
                .asLongStream()                                  // LongStream
                .mapToDouble( points -> points / totalPoints )   // DoubleStream
                .boxed()                                         // Stream< Double >
                .mapToLong( weigth -> ( long )( weigth * 100 ) ) // LongStream
                .mapToObj( percentage -> percentage + "%" )      // Stream< String>
                .collect( Collectors.toList() );                 // List< String >
        System.out.println("权重为：" + result);


    }
}

```

#### Time

```
public class DateUtil {
    public static final String DATE_FORMAT_FULL = "yyyy-MM-dd HH:mm:ss";
    public static final String DATE_FORMAT_SHORT = "yyyy-MM-dd";
    public static final String DATE_FORMAT_COMPACT = "yyyyMMdd";
    public static final String DATE_FORMAT_COMPACTFULL = "yyyyMMddHHmmss";
    public static final String DATE_FORMAT_FULL_MSEL = "yyyyMMddHHmmssSSSS";
    public static final String DATE_YEAR_MONTH = "yyyyMM";
    public static final String DATE_FORMAT_FULL_MSE = "yyyyMMddHHmmssSSS";

    /**
     * 获取系统当前日期
     *
     * @return
     */
    public static Date getCurrentDate() {
        return new Date();
    }

    /**
     * 获取系统当前日期
     *
     * @return
     */
    public static LocalDateTime getCurrentLocalDateTime() {
        return LocalDateTime.now();
    }

    /**
     * 根据时间格式返回对应的String类型的时间
     *
     * @param format
     * @return
     */
    public static String getCurDateTime(String format) {
        LocalDateTime now = LocalDateTime.now();
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern(format);
        String dataTime = now.format(dateTimeFormatter);
        return dataTime;
    }

    /**
     * 得到当前日期
     *
     * @return String 当前日期 yyyy-MM-dd HH:mm:ss格式
     * @author kevin
     */
    public static String getCurDateTimeFull() {
        return getCurDateTime(DATE_FORMAT_FULL);
    }

    /**
     * 得到当前日期
     *
     * @return String 当前日期 yyyyMMddHHmmss格式
     * @author kevin
     */
    public static String getCurDateTime1() {
        return getCurDateTime(DATE_FORMAT_FULL);
    }

    /**
     * 得到当前日期YYYYMM格式
     *
     * @return String 当前日期 yyyyMM格式
     * @author kevin
     */
    public static String getCurDateYYYYMM() {
        return getCurDateTime(DATE_YEAR_MONTH);
    }

    /**
     * 得到当前日期
     *
     * @return String 当前日期 yyyyMMdd格式
     * @author kevin
     */
    public static String getCurDateYYYYMMDD() {
        return getCurDateTime(DATE_FORMAT_COMPACT);
    }

    /**
     * 判断是否是今天
     *
     * @param strDate
     * @return
     */
    public static boolean isCurrentDay(String strDate) {
        boolean bRet = false;
        LocalDate strLocalDate = LocalDate.parse(strDate);
        if (LocalDate.now().getYear() == strLocalDate.getYear()) {
            MonthDay monthDay = MonthDay.from(strLocalDate);
            MonthDay today = MonthDay.from(LocalDate.now());
            return monthDay.equals(today);
        }
        return bRet;
    }

    /**
     * 获取几小时后的时间
     *
     * @param hour
     * @param format
     * @return
     */
    public static String getAfterDateTime(int hour, String format) {
        LocalTime localTime = LocalTime.now().plusHours(hour);
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern(format);
        String dataTime = localTime.format(dateTimeFormatter);
        return dataTime;
    }


    /**
     * 当前日期时间戳(yyyyMMddHHmmssSSSS)
     *
     * @return
     * @author liangxuekai
     */
    public static String getTimeStamp() {
        LocalDateTime now = LocalDateTime.now();
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern(DATE_FORMAT_FULL_MSEL);
        return now.format(dateTimeFormatter);
    }

    /**
     * 日期转字符串
     *
     * @return String
     * @author kevin
     */
    public static String parseDateToString(Date thedate, String format) {
        if (thedate != null) {
            Instant instant = thedate.toInstant();
            ZoneId zone = ZoneId.systemDefault();
            LocalDateTime localDateTime = LocalDateTime.ofInstant(instant, zone);
            DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern(format);
            return localDateTime.format(dateTimeFormatter);
        }
        return null;
    }

    /**
     * parseDateToString(Date thedate, String format)的重载方法
     *
     * @param thedate
     * @return
     */
    public static String parseDateToString(Date thedate) {
        // String format = "yyyy-MM-dd";
        return parseDateToString(thedate, DATE_FORMAT_FULL);
    }

    /**
     * 字符串转日期
     *
     * @return Date
     * @author kevin
     */
    public static Date parseStringToDate(String thedate, String format) {
        DateFormat sdf = new SimpleDateFormat(format);
        Date dd1 = null;
        try {
            dd1 = sdf.parse(thedate);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        return dd1;
    }

    /**
     * 由String型日期转成format形式String
     *
     * @param format1 原先格式
     * @param format2 转化格式
     * @return String
     * @author kevin
     */
    public static String changeFormatDateString(String format1, String format2, String strDate) {
        if (strDate == null)
            return "";
        if (strDate.length() >= format1.length() && format1.length() >= format2.length()) {
            return parseDateToString(parseStringToDate(strDate, format1), format2);
        }
        return strDate;
    }

    /**
     * 得到当前日期的前N天时间 yyyymmdd
     *
     * @param format
     * @param day
     * @return
     */
    public static String beforeNDaysDate(String format, int day) {
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern(format);
        if (day > 0) {
            return LocalDateTime.now().minusDays(day).format(dateTimeFormatter);
        }
        return null;
    }

    /**
     * 获得N个月后的日期
     * <p>
     * theDate 日期
     * <p>
     * int month 月数
     * <p>
     * format 格式
     */
    public static String afterNMonthDate(String theDate, int month, String format) {

        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern(format);
        return LocalDateTime.parse(theDate,dateTimeFormatter)
                .plusMonths(month)
                .format(dateTimeFormatter);

    }

    /**
     * 得到N天后的日期
     *
     * @param theDate 某日期
     *                格式 yyyy-MM-dd
     * @param nDayNum N天
     * @return String N天后的日期
     * @author kevin
     */
    public static String afterNDaysDate(String theDate, Integer nDayNum, String format) {
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern(format);

        return LocalDateTime.parse(theDate,dateTimeFormatter)
                .plusDays(nDayNum)
                .format(dateTimeFormatter);
    }

    /**
     * 得到N小时后的日期
     *
     * @param theDate  时间
     * @param nHourNum N小时数
     * @param format   时间格式
     * @return
     */
    public static String afterNHoursDate(String theDate, Integer nHourNum, String format) {
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern(format);
        return LocalDateTime.parse(theDate,dateTimeFormatter)
                .plusHours(nHourNum)
                .format(dateTimeFormatter);
    }

    /**
     * 得到N分钟后的日期
     *
     * @param theDate
     * @param nMinNum
     * @param format
     * @return
     */
    public static String afterNMinsDate(String theDate, Integer nMinNum, String format) {
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern(format);
        return LocalDateTime.parse(theDate,dateTimeFormatter)
                .plusMinutes(nMinNum)
                .format(dateTimeFormatter);
    }

    /**
     * 得到N秒后的日期
     * @param theDate
     * @param nSecNum
     * @param format
     * @return
     */
    public static String afterNSecondsDate(String theDate, Integer nSecNum, String format) {
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern(format);
        return LocalDateTime.parse(theDate,dateTimeFormatter)
                .plusSeconds(nSecNum)
                .format(dateTimeFormatter);
    }

    // 比较两个字符串格式日期大小,带格式的日期
    public static boolean isBefore(String strdat1, String strdat2, String format) {
        try {
            Date dat1 = parseStringToDate(strdat1, format);
            Date dat2 = parseStringToDate(strdat2, format);
            return dat1.before(dat2);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    // 比较两个字符串格式日期大小,带格式的日期,返回int
    public static long isBefore_int(String strdat1, String strdat2, String format) {
        long result = 0;
        try {
            Date dat1 = parseStringToDate(strdat1, format);
            Date dat2 = parseStringToDate(strdat2, format);
            return dat2.getTime() - dat1.getTime();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return result;
    }


    /*
     * 得到上一个月或者下一个月的日期
     */
    public static String getDayafterMonth(String theDate, int month, String formatStr) {
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern(formatStr);
        return LocalDateTime.parse(theDate).plusMonths(month).format(dateTimeFormatter);
    }

    /**
     * 将秒转换为小时分秒等
     *
     * @param sec
     * @return
     */
    public String changeTime(int sec) {
        String temp = "";
        if (sec < 60) {
            temp = "" + sec + "秒";
        } else if (sec < 3600) {
            temp = "" + sec / 60 + "分" + sec % 60 + "秒";
        } else {
            temp = "" + sec / 3600 + "小时" + (sec % 3600) / 60 + "分" + sec % 60 + "秒";
        }
        return temp;
    }

    /**
     * 方法描述:
     * 计算两个日期相差天数
     *
     * @param end   结束日期
     * @param start 开始日期
     * @return 作者：liangxuekai
     * 日期：2018 2018年1月31日
     */
    public static int getSubDays(String end, String start) {
        LocalDate startDate = LocalDate.parse(start);
        LocalDate endDate = LocalDate.parse(end);
        Long between = ChronoUnit.DAYS.between(startDate, endDate);
        return between.intValue();
    }


    public static String getTimeDiff(Date time1, Date time2) throws Exception {
        long l = time1.getTime() - time2.getTime();
        String returnStr = "";
        long day = l / (24 * 60 * 60 * 1000);
        if (day > 0) {
            returnStr += (day + "天");
        }
        long hour = (l / (60 * 60 * 1000) - day * 24);
        if (hour > 0) {
            returnStr += (hour + "小时");
        }
        long min = ((l / (60 * 1000)) - day * 24 * 60 - hour * 60);
        if (min > 0) {
            returnStr += (min + "分");
        }
        long s = (l / 1000 - day * 24 * 60 * 60 - hour * 60 * 60 - min * 60);
        if (s > 0) {
            returnStr += (s + "秒");
        }
        return returnStr;
    }
}

```

























### 参考文章：

#### Java 8新特性终极指南  [http://www.importnew.com/11908.html](http://www.importnew.com/11908.html)

#### 优雅的使用Optional  [https://blog.csdn.net/zhang89xiao/article/details/76596046](https://blog.csdn.net/zhang89xiao/article/details/76596046)

#### 深入浅出Stream和parallelStream[https://blog.csdn.net/darrensty/article/details/79283146](https://blog.csdn.net/darrensty/article/details/79283146)

#### java8时间工具类[https://www.jianshu.com/p/6261f4162cfe](https://www.jianshu.com/p/6261f4162cfe)
