Java设计模式

1.java反射技术

Java的反射内容繁多，包括对象构建、反射方法、注解、参数、接口等。

2.JDK动态代理

定义接口：

```java
public interface HelloWorld {
    public void sayHelloWorld();
}
```

实现接口：

```java
public class HelloWorldImpl implements HelloWorld {
    @Override
    public void sayHelloWorld() {
        System.out.println("hello world");
    }
}
动态代理绑定和代理逻辑实现：public class JdkProxyExample implements InvocationHandler {
    //真实对象
    private Object target = null;

    /**
     * @param target  代理对象
     * @return
     */
    public Object bind(Object target) {
        this.target = target;
        return Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("进入代理逻辑方法");
        System.out.println("在调度真实对象之前的服务");
        Object obj =method.invoke(target,args);
        System.out.println("在调度真实对象之后的服务");
        return obj;
    }


```

第一步，建立代理对象和真实对象的关系。这里使用了bind方法去完成的，方法里面首先用类的属性target保存了真实对象，然后通过如下代码建立并生成代理对象。

```java
Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
```

其中newProxyInstance方法包含了3个参数。

- 第1个是类加载器，我们才用了target本身的类加载器。
- 第2个是把生成的动态代理对象下挂在哪些接口下，这个写法就是放在target实现的接口下。HelloWorldImpl对象的接口显然就是HelloWorld，代理对象可以这样声明：HelloWorld proxy =xxxx；。
- 第3个是定义实现方法逻辑的代理类，this表示当前对象，它必须需实现InvocationHanfler接口的invoke方法，它就是代理逻辑方法的现实方法。

第二步，实现代理逻辑方法。invoke方法可以实现代理逻辑，invoke方法的3个参数的含义如下：

- proxy，代理对象，就是bind方法生成的对象。
- method，当前调度的方法。
- args，调度方法的参数。

当我们调用了代理对象调度方法后，它就会进入到invoke方法里面。

```java
Object obj =method.invoke(target,args);
```

3.CGLIB动态代理

JDK动态代理必须提供接口才能使用，而CGLIB的优势在于不需要提供接口，只要一个非抽象类就能实现动态代理。

代码：

```java
public class CglibProxyExample implements MethodInterceptor {
    /**
     * 生成cglib代理对象
     *
     * @param cls-----Class类
     * @return Class的类的CGLIB代理对象
     */
    public Object getProxy(Class cls) {
        //CGLIB enhancer增强类对象
        Enhancer enhancer = new Enhancer();
        //设置增强类型
        enhancer.setSuperclass(cls);
        //定义代理逻辑对象为当前对象，要求当前对象实现MethodInterceptor方法
        enhancer.setCallback(this);
        //生成并返回代理对象
        return enhancer.create();
    }

    /**
     * 代理逻辑方法
     *
     * @param o           代理对象
     * @param method      方法
     * @param objects     方法参数
     * @param methodProxy 方法代理
     * @return 代理逻辑返回
     * @throws Throwable 异常
     */
    @Override
    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        System.out.println("调用真实对象前");
        //cglib反射调用真实对象方法
        Object result = methodProxy.invokeSuper(o, objects);
        System.out.println("调用真实对象后");
        return result;
    }
}
```

这里用了CGLIB的加强者Enhancer，通过设置超类的方法(setSuperclass),然后通过setCallback方法设置哪个类为它的代理类。其中，参数this就意味着当前对象，那就要求用this这个对象实现接口MethodInterceptor的方法-------intercept，然后返回代理对象。

4.拦截器

定义拦截器接口Interceptor

```java
public interface Interceptor {
    public boolean before(Object proxy, Object target, Method method, Object[] args);

    public boolean around(Object proxy, Object target, Method method, Object[] args);

    public boolean after(Object proxy, Object target, Method method, Object[] args);
}
```

- 3个方法的参数：proxy代理对象，target真实对象，method方法，args运行方法参数。

- before方法返回boolean值，它在真实对象前调用。当返回true时，则返回真实对象的方法；当返回false时，则调用around方法

- 在反射真是对象或者around方法执行之后，调用after方法。

  

实现类MyInterceptor

```java
public class MyInterceptoer implements Interceptor {
    @Override
    public boolean before(Object proxy, Object target, Method method, Object[] args) {
        System.out.println("反射方法前逻辑");
        return false;
    }

    @Override
    public boolean around(Object proxy, Object target, Method method, Object[] args) {
        System.out.println("取代了被代理对象的方法");
        return false;
    }

    @Override
    public boolean after(Object proxy, Object target, Method method, Object[] args) {
        System.out.println("反射方法后逻辑");
        return false;
    }
}
```

在JDK动态代理中使用拦截器

```java
public class InterceptorJdkProxy implements InvocationHandler {
    private Object target; //真实对象
    private String interceptorClass = null; //拦截器全限定名

    public InterceptorJdkProxy(Object target, String interceptorClass) {
        this.target = target;
        this.interceptorClass = interceptorClass;
    }

    /**
     * 绑定委托对象并返回一个【代理占位】
     * @param target 真实对象
     * @param interceptorClass  代理对象【占位】
     * @return
     */
    public static Object bind(Object target, String interceptorClass) {
        //取得代理对象
        return Proxy.newProxyInstance(target.getClass().getClassLoader(),
                target.getClass().getInterfaces(),
                new InterceptorJdkProxy(target, interceptorClass));

    }

    /**
     * 通过代理对象调用方法，首先进入这个方法
     * @param proxy 代理对象
     * @param method 方法，被调用方法
     * @param args 方法的参数
     * @return
     * @throws Throwable
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        if (interceptorClass == null) {
            //没有设置拦截器则直接反射原有方法
            return method.invoke(target, args);
        }
        Object result = null;
        //通过反射生成拦截器
        Interceptor interceptor = (Interceptor) Class.forName(interceptorClass).
                getConstructor().
                newInstance();
        //调用前置方法
        if (interceptor.before(proxy, target, method, args)) {
            //反射原有对象方法
            result = method.invoke(target, args);
        } else {//返回false执行around方法
            interceptor.around(proxy, target, method, args);
        }
        //调用后置方法
        interceptor.after(proxy, target, method, args);
        return result;
    }
```

执行步骤：

1. 在bind方法中用jdk动态代理绑定了一个对象，然后返回代理对象。
2. 如果没有设置拦截器，则直接反射真实对象的方法，然后结束，否则进行第3步。
3. 通过反射生成拦截器，并准备使用它
4. 调用拦截器的before方法，如果返回true，反射原来的方法；否则运行拦截器的around方法。
5. 调用拦截器的after方法。
6. 返回结果。

![](http://ww1.sinaimg.cn/large/005WjvZYly1g1v6jfg3wpj30l00iggm4.jpg)

5.责任链模式

当一个对象在一条链上被多个拦截器拦截处理(拦截器也可以选择不拦截处理它)时，我们把这样的设计模式称为责任链模式。

责任链拦截器接口定义：

```java
public class Interceptor1 implements Interceptor {
    @Override
    public boolean before(Object proxy, Object target, Method method, Object[] args) {
        System.out.println("拦截器1的before方法");
        return true;
    }

    @Override
    public void around(Object proxy, Object target, Method method, Object[] args) {

    }

    @Override
    public void after(Object proxy, Object target, Method method, Object[] args) {
        System.out.println("拦截器1的after方法");
    }
}
```

测试责任链模式上的多拦截器：

```java
public static void main(String[] args) {
    HelloWorld proxy1 = (HelloWorld) InterceptorJdkProxy.bind(new HelloWorldImpl(),
            "cn.calvin.test.Interceptor1");
    HelloWorld proxy2 = (HelloWorld) InterceptorJdkProxy.bind(proxy1,
            "cn.calvin.test.Interceptor2");
    HelloWorld proxy3 = (HelloWorld) InterceptorJdkProxy.bind(proxy2,
            "cn.calvin.test.Interceptor3");
    proxy3.sayHelloWorld();
}
```

责任链模式的优点在于我们可以在传递链上加入新的拦截器，增加拦截逻辑，其缺点是会增加代理和反射，而代理和反射的性能不高。

6.观察者模式

在现实中，有些条件发生了变化。其他的行为也需要发生变化。举个例子，一个商家有一些产品，它和一些电商合作，每当有新产品时，就会把这些产品推送到电商，现在只和淘宝、京东合作，于是有如下伪代码：

if(产品库有新产品){

推送产品到淘宝；

推送产品到京东；

}

如果公司又和国美、苏宁、当当签订合作协议，那么就需要改变这些伪代码。

if(产品库有新产品){

推送产品到淘宝；

推送产品到京东；

推送产品到国美；

推送产品到苏宁；

推送产品到当当；

}

按照这种方法，if的逻辑就异常复杂了。观察者模式更易于扩展，首先，把每一个电商接口看成一个观察者，每一个观察者都能观察到产品列表(被监听对象)。当公司发布新产品时，就会发送到这个产品列表上，于是产品列表就发生了变化，这时就可以触发各个电商接口(观察者)发送新产品到对应的合作电商那里。

代码：

被观察的产品列表：

```java
public class ProductList extends Observable {
    private List<String> productList = null;
    private static ProductList instance;

    private ProductList() {
    }

    /**
     * 单例模式
     * 取得唯一实例
     *
     * @return
     */
    public static ProductList getInstance() {
        if (instance == null) {
            instance = new ProductList();
            instance.productList = new ArrayList<>();
        }
        return instance;
    }

    /**
     * 增加观察者
     *
     * @param observable
     */
    public void addProductListObserver(Observable observable) {
        this.addObserver((Observer) observable);
    }

    /**
     * 新增产品
     *
     * @param newProduct
     */
    public void addProduct(String newProduct) {
        productList.add(newProduct);
        System.out.println("产品列表新增了产品：" + newProduct);
        this.setChanged();//设置被观察对象发送变化
        this.notifyObservers(newProduct); //通知观察者，并传递了新产品
    }
}
```

- 构建方法私有化，避免通过new的方式创建对象，而是通过getInstace方法获得产生列表单例，使用的是单例模式。

- addProductListObserver可以增加一个电商接口(观察者)。

- 核心逻辑在addProduct方法上。在产品列表上增加了一个新的产品，然后调用setChanged方法。这个方法用于告知观察者当前被观察者发生了变化，如果没有则无法触发其行为。最后通过notifyObservers告知观察者，让它们发生相应的动作，并将新产品作为参数传递给观察者。

  

京东和淘宝电商接口:

```java
public class JingDongObserver implements Observer {
    @Override
    public void update(Observable o, Object product) {
        String newProduct = (String) product;
        System.out.println("发送新产品【"+newProduct+"】同步到京东商城");
    }
}
```

```java
public class TaoBaoObserver implements Observer {
    @Override
    public void update(Observable o, Object product) {
        String newProduct = (String) product;
        System.out.println("发送新产品【"+newProduct+"】同步到淘宝商城");
    }
}
```

测试观察者模式：

```java
public class test {
    public static void main(String[] args) {
        ProductList observable =ProductList.getInstance();
        TaoBaoObserver taoBaoObserver =new TaoBaoObserver();
        JingDongObserver jingDongObserver =new JingDongObserver();
        observable.addObserver(taoBaoObserver);
        observable.addObserver(jingDongObserver);
        observable.addProduct("新增产品1");
    }
}
```

结果：![](http://ww1.sinaimg.cn/large/005WjvZYly1g1wj3ts02oj30ip05d3yy.jpg)

7.工厂模式和抽象工厂模式

1. 普通工厂模式

   例如：有个Iproduct的产品接口，它下面有5个实现类Product1,Product2,Product3,Product4,Product5。它们属于一个大类，可以通过一个工厂去管理它们的生成，但是由于类型不同，所以初始化有所不同。为了方便使用产品工厂(ProductFactory)类来创建这些产品的对象，用户可以通过产品号来确定需要哪种产品。

   ProductFactory的伪代码：

   ```java
   public class ProductFactory {
       public static IProduct createProduct(String productNo){
           switch (productNo){
               case "1": return new Product1(xxx);
               case "1": return new Product1(xxx);
               case "1": return new Product1(xxx);
               case "1": return new Product1(xxx);
               case "1": return new Product1(xxx);
               default:throw new NotSupportedException("未支持此编号产品生产");
           }
       }
   }
   ```

2. 抽象工厂模式

   抽象工厂模式可以向客户端提供一个接口，使得客户端在不必指定产品的具体情况下，创建多个产品族中的产品对象。

   IProductFactory工厂接口：

   ```java
   public interface IProductFactory {
       public Iproduct createProduct(String productNo);
   }
   ```

实现工厂类：

```java
public class ProductFactory1 implements IProductFactory {
    @Override
    public Iproduct createProduct(String productNo) {
        Iproduct iproduct = xxx;//工厂1生成产品对象规则，可以是一类产品的规则
        return iproduct;
    }
}
```

```java
public class ProductFactory2 implements IProductFactory {
    @Override
    public Iproduct createProduct(String productNo) {
        Iproduct iproduct = xxx;//工厂2生成产品对象规则，可以是一类产品的规则
        return iproduct;
    }
}
```

```java
public class ProductFactory3 implements IProductFactory {
    @Override
    public Iproduct createProduct(String productNo) {
        Iproduct iproduct = xxx;//工厂3生成产品对象规则，可以是一类产品的规则
        return iproduct;
    }
}
```

然后完成一个公共的工厂：

```java
public class ProductFactory implements IProductFactory {
    @Override
    public Iproduct createProduct(String productNo) {
        char ch = productNo.charAt(0);
        IProductFactory factory = null;
        if (ch == '1') {
            factory = new ProductFactory1();
        } else if (ch == '2') {
            factory = new ProductFactory2();
        } else if (ch == '3') {
            factory = new ProductFactory3();
        }
        if (factory != null) {
            return factory.createProduct(productNo);
        }
        return null;
    }
}
```

7.建造者模式

建造者模式属于对象的创建模式。可以将一个产品的内部表象(属性)与产品的生成过程分割开来，从而使一个建造过程生成具有不用的内部表象的产品对象。

**实现**

假设旅游套票可以分为：普通成年人、退休老人、半票有座小孩、免费无座小孩、军人及其家属等，他们有不同的规定和优惠。

第一步，构建普通成年人票

第二步，构建退休老人票

第三步，构建有座儿童票

第四步，构建无座儿童票

第五步，构建军人及其家属票

步骤1：创建一个TicketHelper对象，它是配置类，帮助我们一步一步完成构建对象

```java
public class TicketHelper {
    public void buildAdult(String info) {
        System.out.println("构建成年人票逻辑" + info);
    }

    public void buildChildForSeat(String info) {
        System.out.println("构建有座儿童票逻辑");
    }

    public void buildChildNoSeat(String info) {
        System.out.println("构建无座儿童票逻辑");
    }

    public void buildElderly(String info) {
        System.out.println("构建有老年人票逻辑");
    }

    public void buildSoldier(String info) {
        System.out.println("构建军人及其家属票逻辑");
    }
}
```

我们将创建一个表示食物条目（比如汉堡和冷饮）的 *Item* 接口和实现 *Item* 接口的实体类，以及一个表示食物包装的 *Packing* 接口和实现 *Packing*接口的实体类，汉堡是包在纸盒中，冷饮是装在瓶子中。

然后我们创建一个 *Meal* 类，带有 *Item* 的 *ArrayList* 和一个通过结合 *Item* 来创建不同类型的 *Meal* 对象的 *MealBuilder*。*BuilderPatternDemo*，我们的演示类使用 *MealBuilder* 来创建一个 *Meal*。



步骤2：需要一个构建类------TicketBuilder

```java
public class TicketBuilder {
    public static Object builder(TicketHelper ticketHelper) {
        System.out.println("通过TicketHelper构建套票信息");
        return null;
    }
}
```

步骤3：完成构建

```java
public static void main(String[] args) {
    TicketHelper helper = new TicketHelper();
    helper.buildAdult("成人票");
    helper.buildChildForSeat("有座儿童");
    helper.buildChildNoSeat("无座儿童");
    helper.buildSoldier("军人票");
    helper.buildElderly("老人票");
    Object ticket = TicketBuilder.builder(helper);
}
```