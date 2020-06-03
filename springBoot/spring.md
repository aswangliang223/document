# Bean  ico di

对象实例化三种方式：

无参构造

静态工厂

实例工厂

对象di三种

构造器依赖注入

set 方法依赖注入

自动装配注入： @Autowired @Resource @Inject



@Autowired 默认类型是bType  ，是Spring 自带的注解 @Qualifier 添加id 用于多接口多实现类的情况

@Resource 默认按照名称 ，可以指定name，如果没有指定name 属性，当注解写在字段上时候。默认取字段名进行查找。找不到再取 类型查找。推荐使用@Resource ，他是 J2EE的

@Inject 按照类型进行自动装配，如果要按照名称装配需要配合 @Named 注解

@Inject 可以作用在变量、setter方法、构造方法上

## BeanPostProcessor

beanPostProcessor:对象后处理器

先写一个对象

```java
@Component
public class Bean4BBP {

    private static final Logger log = LoggerFactory.getLogger(Bean4BBP.class);

    public Bean4BBP(){
        log.info("construct Bean4BBP");
    }
}
```

然后再写一个BeanPostProcessor，这时发现它是一个接口，没关系，那就写一个类实现它，CustomBeanPostProcessor：

```java
@Component
public class CustomBeanPostProcessor implements BeanPostProcessor {

    private static final Logger log = LoggerFactory.getLogger(CustomBeanPostProcessor.class);

    public CustomBeanPostProcessor() {
        log.info("construct CustomBeanPostProcessor");
    }

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        if (bean instanceof Bean4BBP) {
            log.info("process bean before initialization");
        }
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        if (bean instanceof Bean4BBP) {
            log.info("process bean after initialization");
        }
        return bean;
    }
}
```

BBP对象首先被创建，然后创建Bean4BBP对象，接着再先后执行BBP对象的postProcessBeforeInitialization和postProcessAfterInitialization方法。

结论：“对象后处理器”，指的是“ 对象创建后处理器 ”。

我们可以利用它，在对象创建之后，对对象进行修改（有什么场合需要用到？思考题，文末回答。）

那么，为什么要分postProcessBeforeInitialization和postProcessAfterInitialization呢？这里的Initialization是什么意思



我们只需要在CustomBeanPostProcessor的postProcessBeforeInitialization和postProcessAfterInitialization方法里，打上两个断点，一切自然明了。

断点进来，跟着调用栈这点蛛丝马迹往回走，真相大白： 

```java
protected Object initializeBean(final String beanName, final Object bean, @Nullable RootBeanDefinition mbd) {
		if (System.getSecurityManager() != null) {
			AccessController.doPrivileged((PrivilegedAction<Object>) () -> {
				invokeAwareMethods(beanName, bean);
				return null;
			}, getAccessControlContext());
		}
		else {
			// 调用实现了Aware接口及它的子接口的类的方法
			invokeAwareMethods(beanName, bean);
		}

		Object wrappedBean = bean;
		if (mbd == null || !mbd.isSynthetic()) {
			//应用BeanPostProcessor的postProcessBeforeInitialization方法
			wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
		}

		try {
			//执行初始化方法（先调用InitializingBean的afterPropertiesSet，再调用init-method属性指定的初始化方法）
			invokeInitMethods(beanName, wrappedBean, mbd);
		}
		catch (Throwable ex) {
			throw new BeanCreationException(
					(mbd != null ? mbd.getResourceDescription() : null),
					beanName, "Invocation of init method failed", ex);
		}
		if (mbd == null || !mbd.isSynthetic()) {
			//应用BeanPostProcessor的postProcessAfterInitialization方法(AOP代理对象生成)
			wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
		}

		return wrappedBean;
	}
```

在initializeBean方法里面，先后调用了applyBeanPostProcessorsBeforeInitialization和applyBeanPostProcessorsAfterInitialization方法，这两个方法内部，则分别去遍历系统里所有的BBP，然后逐个执行这些BBP对象的postProcessBeforeInitialization和postProcessAfterInitialization方法，去处理对象，以applyBeanPostProcessorsBeforeInitialization为例：

```java
	@Override
	public Object applyBeanPostProcessorsBeforeInitialization(Object existingBean, String beanName)
			throws BeansException {

		Object result = existingBean;
		for (BeanPostProcessor beanProcessor : getBeanPostProcessors()) {
			Object current = beanProcessor.postProcessBeforeInitialization(result, beanName);
			if (current == null) {
				return result;
			}
			result = current;
		}
		return result;
	}
```



那么夹在applyBeanPostProcessorsBeforeInitialization和applyBeanPostProcessorsAfterInitialization方法中间的invokeInitMethods方法是做什么的呢？

其实这个方法就是Spring提供的，用于对象创建完之后，针对对象的一些初始化操作。这就好比你创建了一个英雄之后，你需要给他进行一些能力属性的初始化、服装初始化一样。

要验证这一点，很简单，只需让Bean4BBP实现InitializingBean接口：

```java
@Component
public class Bean4BBP implements InitializingBean {

    private static final Logger log = LoggerFactory.getLogger(Bean4BBP.class);

    public Bean4BBP(){
        log.info("construct Bean4BBP");
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        log.info("init Bean4BBP");
    }
}
```

BeanPostProcessor是什么时间初始化的 ，AbstractApplicationContext

```java
// 完成IoC容器的创建及初始化工作
	@Override
	public void refresh() throws BeansException, IllegalStateException {
		synchronized (this.startupShutdownMonitor) {
			// Prepare this context for refreshing.
            // STEP 1： 刷新预处理
			prepareRefresh();

			// Tell the subclass to refresh the internal bean factory.
            // STEP 2：
            // 		a） 创建IoC容器（DefaultListableBeanFactory）
            //		b） 加载解析XML文件（最终存储到Document对象中）
            //		c） 读取Document对象，并完成BeanDefinition的加载和注册工作
			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

			// Prepare the bean factory for use in this context.
            // STEP 3： 对IoC容器进行一些预处理（设置一些公共属性）
			prepareBeanFactory(beanFactory);

			try {
				// Allows post-processing of the bean factory in context subclasses.
                // STEP 4： 由子类继承去实现该类，当前方法为空
				postProcessBeanFactory(beanFactory);

				// Invoke factory processors registered as beans in the context.
				// STEP 5： 调用BeanFactoryPostProcessor后置处理器对BeanDefinition处理
                invokeBeanFactoryPostProcessors(beanFactory);

				// Register bean processors that intercept bean creation.
				// STEP 6： 注册BeanPostProcessor后置处理器
                registerBeanPostProcessors(beanFactory);

				// Initialize message source for this context.
				// STEP 7： 初始化一些消息源（比如处理国际化的i18n等消息源）
                initMessageSource();

				// Initialize event multicaster for this context.
				// STEP 8： 初始化应用事件广播器
                initApplicationEventMulticaster();

				// Initialize other special beans in specific context subclasses.
				// STEP 9： 初始化一些特殊的bean
                onRefresh();

				// Check for listener beans and register them.
				// STEP 10： 注册一些监听器
                registerListeners();

				// Instantiate all remaining (non-lazy-init) singletons.
				// STEP 11： 实例化剩余的单例bean（非懒加载方式）
                // 注意事项：Bean的IoC、DI和AOP都是发生在此步骤
                finishBeanFactoryInitialization(beanFactory);

				// Last step: publish corresponding event.
				// STEP 12： 完成刷新时，需要发布对应的事件
                finishRefresh();
			}

			catch (BeansException ex) {
				if (logger.isWarnEnabled()) {
					logger.warn("Exception encountered during context initialization - " +
							"cancelling refresh attempt: " + ex);
				}

				// Destroy already created singletons to avoid dangling resources.
				destroyBeans();

				// Reset 'active' flag.
				cancelRefresh(ex);

				// Propagate exception to caller.
				throw ex;
			}

			finally {
				// Reset common introspection caches in Spring's core, since we
				// might not ever need metadata for singleton beans anymore...
				resetCommonCaches();
			}
		}
	}
```






目录 前言

Spring中提供了一个Ordered接口。Ordered接口，顾名思义，就是用来排序的。

Spring是一个大量使用策略设计模式的框架，这意味着有很多相同接口的实现类，那么必定会有优先级的问题。

于是，Spring就提供了Ordered这个接口，来处理相同接口实现类的优先级问题。

Ordered接口介绍

首先，我们来看下Ordered接口的定义：

{ int HIGHEST_PRECEDENCE = Integer.MIN_VALUE; intLOWEST_PRECEDENCE = Integer.MAX_VALUE; int getOrder(); }

只有1个方法：getOrder()；  2个变量：最高级(数值最小)和最低级(数值最大)。

 

OrderComparator类：实现了Comparator的一个比较器。

提供了3个静态排序方法：sort(List<?> list)、sort(Object[] array)、sortIfNecessary(Object value)。根据OrderComparator对数组和集合进行排序。

sortIfNecessary方法内部会判断value参数是Object[]还是List类型，然后使用Object[]参数的sort方法和List参数的sort方法进行排序。

我们看下这个比较器的compare方法：

public int compare(Object o1, Object o2) { boolean p1 = (o1 instanceof PriorityOrdered); boolean p2 = (o2instanceof PriorityOrdered); if (p1 && !p2) { return -1; } else if (p2 && !p1) { return 1; } int i1 = getOrder(o1); inti2 = getOrder(o2); return (i1 < i2) ? -1 : (i1 > i2) ? 1 : 0; }

PriorityOrdered是个接口，继承自Ordered接口，未定义任何方法。

这段代码的逻辑：

　　1. 若对象o1是Ordered接口类型，o2是PriorityOrdered接口类型，那么o2的优先级高于o1

　　2. 若对象o1是PriorityOrdered接口类型，o2是Ordered接口类型，那么o1的优先级高于o2

　　3. 其他情况，若两者都是Ordered接口类型或两者都是PriorityOrdered接口类型，调用Ordered接口的getOrder方法得到order值，order值越大，优先级越小

简单概括就是：

　　OrderComparator比较器进行排序的时候，若2个对象中有一个对象实现了PriorityOrdered接口，那么这个对象的优先级更高。

　　若2个对象都是PriorityOrdered或Ordered接口的实现类，那么比较Ordered接口的getOrder方法得到order值，值越低，优先级越高。



