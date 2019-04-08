SSM(springmvc+spring+mybatis) + Redis实现

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