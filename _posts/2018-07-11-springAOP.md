---
layout:     post
title:      "springAOP"
subtitle:   " \"AOP实现原理\""
date:       2018-07-08 23:00:00
author:     "Mr.Xu"
header-img: "img/lianpangcunzhuang/v2-d6f1be19bf915ba2dacd958682a8bb0d_b.jpg"
catalog: true
tags:
    - sping
    - java
---

**1.谈谈你所理解AOP?**

- AOP （Aspect Oriented Programming ）编程思想是基于 OOP（Object Oriented Programming）面向对象思想的，是在此基础的一种延续。OOP是把重复的逻辑代码抽取出来形成方法，以此达到简化代码的编写，而AOP是把重复的模块功能抽离出来，形成一个切面，只写一次，其他的模块功能在执行前或执行后都会调用该切面的逻辑代码。降低了模块功能的耦合度，提高了开发效率。

![img](/img/aop_hd.jpg)

**2.什么是目标对象、通知、切点、切面？**

- **目标对象（target）：**

指的就是需要被代理的对象，由于Spring AOP就是通过代理模式实现的，因而这个对象永远是一个代理对象。

- **连接点（joint point）：**

连接点指的就是被拦截到的点，在spring中这些点指的就是方法，因为spring只支持方法类型的连接点。

- **切入点（pointcut）：**

表示一组joint point，这些joint point是通过逻辑关系组合起来，或者是通过统配、正则表达式组合起来，它定义了相应的advice 将要作用的地方，简单说切入点就是指我们要对哪些连接点进行拦截的定义

- **通知（advice）：**

通知是指拦截到连接点之后所要做的事情就是通知，通知分为前置通知，后置通知，异常通知，最终通知，环绕通知，advice定义了在pointcut里面定义的程序具体要做的操作。

- **切面（aspect）：**

是切入点和通知的结合

- **织入（weaving）：**

是一个过程**，**是将切面应用到目标对象从而创建出AOP代理对象的过程，织入可以在编译期，类装载期，运行期进行。

**3.AOP实现原理？**

1. Spring AOP分为静态AOP和动态AOP；
2. 静态AOP是指AspectJ实现的AOP，它是将切面代码直接编译到Java类文件中；
3. 动态AOP是指将切面代码进行动态织入实现的AOP；
4. Spring 的AOP为动态AOP，实现的技术为：

JDK提供的动态代理技术及CGLIB（动态字节码增强技术）

**Spring AOP-cglib 动态代理介绍：**

- cglib可以为没有实现接口的类做代理，也可以为接口做代理；

**Spring采用的动态代理机制：**

- 如果目标对象有接口，优先使用jdk动态代理；
- 如果目标对象无接口，使用cglib动态代理。

**cglib代理：**目标对象和代理对象是父子关系

**jdk代理：**目标对象和代理对象是兄弟关系

**4.AOP开发流程?**

- **定义目标**
- **定义切点**
- **定义通知**
- **定义切面**
- **织入生成代理**

**5.谈谈你知道的通知类型?**

- **前置通知：目标方法执行前增强 -------->**org.springframework.aop.MethodBeforeAdvice
- **后置通知：目标方法执行后增强-------->**org.springframework.aop.AfterReturningAdvice
- **环绕通知:目标方法执行前后增强------->**org.aopalliance.intercept.MethodInterceptor
- **异常抛出通知:目标方法抛出异常后增强-->**org.springframework.aop.ThrowsAdvice
- **引介通知:在目标类中添加一些新的方法或属性**

**6.后置通知和最终通知区别?**

- **后置通知目标方法抛出异常后不会执行，而最终通知会执行**

**7.后置通知怎么获取目标方法返回值，及通知的信息?**

```java
// 1.通知上可以添加JoinPoint参数,通过它可以获取目标相关的信息

public void before(JoinPoint jp) {
System.out.println("拦截的目标类:" +jp.getSignature().getDeclaringTypeName());
System.out.println("拦截的方法名称:" + jp.getSignature().getName());
System.out.println("前置通知");
}

//2.后置通知获取目标方法返回值  需要配置returning="val" 和参数对应
public void afterReturning(Object val) {
System.out.println("目标方法返回值:" + val);
System.out.println("后置通知");
}

// 3.异常通知获取异常信息,需要配置throwing="ex" 和参数对应

public void afterThrowing(Throwable ex) {
	System.out.println("发现了异常。。。。"+ex);
}
```

**8.环绕通知的参数可以做哪些事情?**

可以在目标行为执行前后执行你想进行的操作，并且还可以改变返回值。

```java
// 环绕通知
public Object around(ProceedingJoinPoint pjp) throws Throwable {
System.out.println("环绕前....");
Object value = pjp.proceed(); // 执行目标行为
System.out.println("环绕后....");
return value;
}
```