---
layout: post
title: 使用JavaConfig形式配置Spring框架，了解一下
date: 2018-07-14 21:29:33 +0800
categories: spring-framework spring-core
tags: [spring,spring-framework,spring-core]
---

本文将介绍Spring 3.0后一种新的配置方式：JavaConfig，使用这种配置方式，既可以作为xml配置的补充，使xml配置精简化，也可以完全替代xml配置，实现无xml配置项目。

本文将先介绍这种新功能的实现方法，再动手实现一个无xml配置(包括web.xml)的Web项目。

同时Spring-Boot就是通过JavaConfig来实现“约定大于配置”的功能，因此学习一下JavaConfig方式对于理解Spring-Boot加载配置的方式也是很有必要的。

## JavaConfig:Spring 3.0的新功能

从Spring3.0开始，一个新的@Configuration注解被添加进框架，使用该注解可以快速标识一个类成为Java Class配置类，官方称这种方式为JavaConfig，这种方式旨在使用Java类对Spring框架进行配置，实现bean定义等功能，是xml配置文件的一种替代或协同工作的形式。

也就是说使用JavaConfig，其各个功能都是可以在原本的xml配置里找到对应的。

配合新的基于注解的AnnotationConfigApplicationContext，可以实现将xml配置转移到代码中的特性，这种配置方式既可以完全替代xml配置，也可以和xml配置相结合，使xml配置更加精简化。

## 新的JavaConfig注解和基于注解的ApplicationContext介绍

从Spring3.0开始，新增的与JavaConfig有关的注解主要为@Configuration、@Bean，新增的ApplicationContext主要为AnnotationConfigApplicationContext。

_此外，与配置有关的注解还有@Import、@ImportSource、@ComponentScan、@Profile、@Conditional、@Scope、@Lazy、@DependsOn、@Primary、@Order，这里既有4.0后新的注解，也有2.5前就已经存在的注解，同时Spring3.0中新增了一个AnnotationConfigWebApplicationContext，作为一个新的Web项目ApplicationContext被添加进框架，后面我会一一介绍它们的。_

本节先来介绍一下主要注解和新的AnnotationConfigApplicationContext的功能，注意本文在撰写时是以Spring当前版本`5.0.7 Release`为准，相关文档可以在[Java-based container configuration](https://docs.spring.io/spring/docs/5.0.7.RELEASE/spring-framework-reference/core.html#beans-java)找到，同时每小段的标题都附上了current（最新正式）版本api的连接，如果想了解更多细节，可以点进去阅读，如果新版本Spring api文档与下面的内容有出入，请根据你使用的Spring版本来决定内容的可靠性。

#### [@Configuration](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html)

@Configuration只能标记在类上，表示该类为JavaConfig类，使其内部定义为@Bean的方法可以被Spring IOC容器识别并将相关的Bean加入到容器中。也就是相当于以往的一个xml文件。

下面是一个Configuration注解官方文档的例子：

```java
@Configuration
public class AppConfig{
    @Bean
    public MyBean myBean(){
        // instantiate, configure and return bean ...
        return new MyBean();
    }
}
```

这个JavaConfig就相当于原来的xml配置：

```xml
<beans>
    <bean id="myBean" class="...MyBean"></bean>
</beans>
```

也就是该类实现了定义一个id为myBean，类名为MyBean，由Spring容器托管的bean。

需要注意的是，Spring规定了几条使用@Configuration注解类的约束，这些约束多半是由于Spring要使用AOP来增强Configuration类所引起的，不过我个人觉得实践中会出现这些问题的情况几乎很少吧：

1. Configuration类必须以类的方式提供，比如不能是通过工厂方法返回的实例，这样在运行时框架可以通过创建其子类来实现增强功能。
2. Configuration类不能被标记为final。
3. Configuration类不能是局部类(本地类，英文为local class)，比如代码块里的局域内部类。
4. 嵌套的configuration类必须被定义为static类型。
5. @Bean方法不能返回一个Configuration类的实例，如果你这么做了，那么它只是按照一个正常的bean被返回，Configuration类内部的配置相关注解如@Bean是无法被识别处理的。

@Configuration本身也是一个复合的注解，它还整合了@Component，也就是说它可以被Component-Scan识别，并以一个Bean的形式由Spring IOC容器托管，当然在大部分情况下开发者并不会需要使用这个bean，但也因此我们可以使用@Autowired注解、构造器注入等方式实现对该JavaConfig类的依赖注入。

#### [@Bean](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/annotation/Bean.html)

@Bean只能标记在方法上，表示该方法返回一个Spring Bean，可以被IOC容器托管，相当于以前在xml文件中写的`<bean/>`元素。

在上面的例子中，已经出现了@Bean的用法。

@Bean注解有几个属性可以配置：

- name：指定一个或者多个bean的名字，当没有设置name时，Spring容器会默认将@Bean方法名作为bean name，当设置了name后，就不会再使用方法名，同时设置多个name时，除第一个name外，其他的都会作为bean的别名。相当于xml配置中<bean>的name属性。
- initMethod：指定容器在初始化完bean后调用的方法。相当于xml配置中<bean>的init-method属性。
- destroyMethod：指定在容器在销毁bean前调用的方法。相当于xml配置中<bean>的 destroy-method。
- autowire：指定bean在自动装配时依赖注入使用的策略，取值可以参考Enum类[Autowire](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/beans/factory/annotation/Autowire.html) 的三个常量：Autowire.BY_NAME，Autowire.BY_TYPE，Autowire.NO。

同时如果我们创建bean时，需要注入依赖的话，可以在方法参数里指定要注入的bean，或是在同一个@Configuration类里直接调用@Bean标记的方法：

```java
@Bean
public MyBean2 myBean2(MyBean myBean){
    return new MyBean2(myBean);
}

@Bean
public MyBean3 myBean3(){
    return new MyBean3(mybean2());
}
```

Spring容器会自动将依赖的bean注入，在这里的注入就类似于构造器注入了，当然我们也可以在代码实现set方法注入，和以往的用代码创建Bean是一致的。

你可能注意到创建MyBean3时，是调用了同类里的其他@Bean方法，在这种情况下，Spring会利用CGLIB实现的AOP，在调用方法前到IOC容器里去找到对应的myBean2并返回。因此在创建MyBean3时，它被注入的myBean2是IOC容器里的myBean2，而非直接调用myBean2()方法，同时由于Bean默认的Scope是singleton，因此myBean2被注入时也以单例形式注入，如果你不太明白，那么可以看看下面这个例子：

```java
@Configuration
public class AppConfig {
    @Bean
    public ClientService clientService1() {
        ClientServiceImpl clientService = new ClientServiceImpl();
        clientService.setClientDao(clientDao());
        return clientService;
    }

    @Bean
    public ClientService clientService2() {
        ClientServiceImpl clientService = new ClientServiceImpl();
        clientService.setClientDao(clientDao());
        return clientService;
    }

    @Bean
    public ClientDao clientDao() {
        return new ClientDaoImpl();
    }
}
```

在该例中，clientService1()和clientService2()方法都调用了clientDao()方法，并且clientDao()方法是被@Bean标注的方法，因此被注入到clientService1和clientService2的clientDao此时指向的同一个bean对象。

同时@Bean并不一定非要标记在@Configuration类里，当标记在普通的类的方法上时，Spring容器会使用精简模式来加载bean，此时调用其他@Bean方法只是普通的方法调用，而非通过AOP方式进行依赖注入，并且由于没有@Configuration注解，该类无法通过扫描的方式装配bean，必须手动注册到ApplicationContext中才能装配bean。

#### [AnnotationConfigApplicationContext](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/annotation/AnnotationConfigApplicationContext.html)

下文我将以"ACAC"来代替称呼"AnnotationConfigApplicationContext"。

ACAC的主要功能是识别类中的Spring注解，然后自动装配bean，不同于ClassPathXmlApplicationContext使用xml作为框架配置的入口，并直接在xml文件里定义bean，ACAC需要使用@Configuration注解标记的类作为框架配置入口，并且ACAC提供了完全基于注解的形式来处理bean的方式，因此它能识别出@Configuration类并进行处理。

通过使用AnnotationConfigApplicationContext取代原来的ClassPathXmlApplicationContext，可以达到使用JavaConfig替代XmlConfig的目的。

该类的主要方法如下：

- 构造方法：ACAC的构造方法主要有三种，无参、(Class<?>... annotatedClasses)、(String... basePackages)
- register(java.lang.Class<?>... annotatedClasses)：传入一个或多个被Spring注解标记的类，比如@Configuration标记的JavaConfig类、@Service、@Controller、@Repository等注解标记的类。
- scan(java.lang.String... basePackages)：类似component-scan，可以传入一个或多个包路径，Spring容器会扫描这些包路径内的所有注解并进行处理，其中包括了@Configuration以及其他我们常用的如@Service、@Controller、@Repository等。

我们先来看看ACAC的几个构造方法：

无参ACAC()：

```java
public AnnotationConfigApplicationContext() {
    this.reader = new AnnotatedBeanDefinitionReader(this);
    this.scanner = new ClassPathBeanDefinitionScanner(this);
}
```

ACAC(Class<?>... annotatedClasses):

```java
public AnnotationConfigApplicationContext(Class<?>... annotatedClasses) {
    this();
    register(annotatedClasses);
    refresh();
}
```

ACAC(String... basePackages):

```java
public AnnotationConfigApplicationContext(String... basePackages) {
    this();
    scan(basePackages);
    refresh();
}
```

我们观察到在构造ACAC时，一个reader和一个scaner被同时创建，其中reader为AnnotatedBeanDefinitionReader对象，scanner为ClassPathBeanDefinitionScanner对象，它们的作用实际上很简单，我们先来看看register()和scan()方法的代码：

register(Class<?>... annotatedClasses):

```java
public void register(Class<?>... annotatedClasses) {
    Assert.notEmpty(annotatedClasses, "At least one annotated class must be specified");
    this.reader.register(annotatedClasses);
}
```

scan(String... bashPackages):

```java
public void scan(String... basePackages) {
    Assert.notEmpty(basePackages, "At least one base package must be specified");
    this.scanner.scan(basePackages);
}
```

可以看到上面两个方法分别调用了AnnotatedBeanDefinitionReader的register()和ClassPathBeanDefinitionScanner的scan()方法。实际上，二者的目标是一致的，都是去找到Spring注解标记的类，来生成并注册bean到容器内。不同的是，AnnotatedBeanDefinitionReader的register是指定要注册bean的类，而ClassPathBeanDefinitionScanner的scan则会自动扫描给定包路径下的所有的类，依次处理被注解标记的类。

我们前面说过@Configuration也整合了@Component注解，因此它也是会被scan方法扫描到或是register方法直接处理的。

需要注意的是，这里并不是特殊处理@Configuration注解的地方，至于Spring框架具体处理@Configuration注解的类，就不在此深挖了，有兴趣的话大家可以自己从ACAC的注册、扫描bean方法作为切入点研究一下。

下面是一段以AppConfig作为ApplicationContext配置的容器初始化启动代码：

```java
public class Chapter2s1Application {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
    }
}
```

其中AppConfig.class就是前面在讲注解时，使用@Configuration和@Bean配合使用来替代applicationContext.xml内容的JavaConfig类，至于内容大家可以参照前面的使用方法随便写。

如果非要写一个与之相对使用appConfig.xml作为配置的ClassPathXmlApplicationContext来作为对比，那么内容也是类似的：

```java
public class Chapter2s1Application {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("classpath:appConfig.xml");
    }
}
```

当然你可能会想，既然ACAC提供了注册、扫描的功能，那么我在xml里使用`<context:component-scan base-package="..."/>`或是定义好AppConfig的bean然后使用`<context:annotation-config/>`，然后使用ClassPathXmlApplicationContext来启动是不是也一样？没错，这种用法是没有任何问题的，并且JavaConfig还可以作为xml配置的补充存在，比如下面的两段xml配置，是可以处理@Configuration标记的JavaConfig类，并根据@Bean注解方法装配Bean的：

```xml
<beans>
    <context:annotation-config/>
    <bean class="...AppConfig"/>
</beans>
```

```xml
<beans>
    <conetext:component-scan base-package="AppConfig所在包"/>
</beans>
```

这里的用法就是xml结合JavaConfig，同时对框架进行配置，在后面我会详细地介绍这些内容。

另外，我们观察到在调用register()和scan()方法后，代码都使用了refresh()方法来刷新，refresh()方法主要是进行所有bean工厂的预处理、创建等工作，由于ACAC继承的org.springframework.context.support.GenericApplicationContext不支持多次多次调用refresh()方法，因此多次调用refresh()时是会报错的，如下代码：

```java
public class Chapter2s1Application {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        context.register(WebConfig.class);
        context.refresh();//报错：GenericApplicationContext does not support multiple refresh attempts: just call 'refresh' once
    }
}
```

如果项目里有多次调用register()或scan()的需求，可以通过使用一个无参构造器，加上多次调用register()或scan()，最后调用refresh()实现，如下代码：

```java
public class Chapter2s1Application {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext();

        context.register(AppConfig.class，WebConfig.class);
        context.scan("com.mcsoft.service","com.mcsoft.dao");
        context.refresh();
    }
}
```

## 与JavaConfig紧密相关的其他注解

与配置有关的注解还有@Import、@ImportSource、@ComponentScan、@Profile、@Conditional、@Scope、@Lazy、@DependsOn、@Primary、@Order，这里会一一介绍它们，可能会有遗漏，如果未来学到了会再补充的。

#### [@Import](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/annotation/Import.html)

我们在使用xml类型的配置时，为了配置文件的整洁，一般会分出多个面向不同方向不同模块的xml配置文件，然后再在入口xml配置里使用`<import/>`来集中起来，这个@Import对应的就是xml配置里的`<import/>`元素。使用时可以指定一个或多个其他JavaConfig类，Spring容器会对@Bean标记的方法进行处理，注意Spring处理的是@Bean方法，所以@Import指定的类不是必需要使用@Configuration注解标记。

@Import只能标记在类上或是被整合到其他注解里。

@Import只有一个属性：Class<?>[] value()。因此使用时以数组形式指定好要引入的其他@Configuration类就行了。

下面是一则使用方法示例：

```java
@Configuration
@Import({WebConfig.class})
public class AppConfig {
    //bean definition...
}
```

该JavaConfig类似于xml配置的

```xml
<beans>
    <import resource="WebConfig.xml"/>
</beans>
```

同时由于Spring容器是以bean形式管理@Configuration标记的类，因此可以将被import的类的bean注入进来，并且引用其他类定义好的bean：

```java
//注意这是官方示例，实例里使用了构造器注入，实际上也可以使用set注入或是@Resource或@Autowired等注解标记实例变量来进行注入。
@Configuration
@Import({DatabaseConfig.class})
public class AppConfig {

    private final DatabaseConfig dataConfig;

    public AppConfig(DatabaseConfig dataConfig) {
        this.dataConfig = dataConfig;
    }

    @Bean
    public MyBean myBean() {
        return new MyBean(dataConfig.dataSource());
    }
}

@Configuration
public class DatabaseConfig{
    @Bean
    public DataSource dataSource(){
        return new DataSource();
    }
}
```

前面我们讲过，Spring容器会利用AOP功能，在调用其他@Bean方法时去容器内查找相关的bean，再根据@Scope设定来返回一个bean对象，这里虽然跨越了不同的类，但也是同理的。

当然，我们也可以直接将@Bean方法创建的bean注入进来，如下代码：

```java
@Configuration
@Import({DatabaseConfig.class})
public class AppConfig {

    @Resource
    private final DataSource dataSource;

    @Bean
    public MyBean myBean() {
        return new MyBean(dataSource);
    }
}

@Configuration
public class DatabaseConfig{
    @Bean
    public DataSource dataSource(){
        return new DataSource();
    }
}
```

#### [@ImportSource](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/annotation/ImportResource.html)

@ImportSource只能标记在类上，用于直接引入xml配置，Spring容器会处理这些xml配置，这也是JavaConfig和XmlConfig相结合的实现方式之一。

路径可以使用'classpath:'、'file:'。被引入的xml配置里的bean可以直接通过依赖注入的方式注入到bean内。

@ImportSource可以指定一个或多个xml配置文件。

官方示例如下:

```java
@Configuration
@ImportResource("classpath:/com/acme/database-config.xml")//database-config.xml定义了一个名为dataSource的bean
public class AppConfig {

    @Inject DataSource dataSource; // from XML

    @Bean
    public MyBean myBean() {
        // inject the XML-defined dataSource bean
        return new MyBean(this.dataSource);
    }
}
```

#### [@ComponentScan](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/annotation/ComponentScan.html)

@ComponentScan只能标记在类上，类似xml配置的`<context:component-scan/>`，该注解能指定一个或多个要扫描的包路径，当没有指定路径时，会从该类所在的包开始进行扫描。

@ComponentScan主要有三个属性：value，注解的默认属性，是basePackages属性的别名； basePackages，指定一个或多个静态字符串形式的包路径；basePackageClasses，指定一个或多个Class，会从指定的Class所在包开始扫描，这种方式可以保证包路径必然存在，否则在编译时就会提示找不到Class，是一种类型安全的做法。

示例如下，下面三条用法结果是一致的：

```java
@ComponentScan({"com.mcsoft.services1","com.mcsoft.services2"})
@ComponentScan(basePackages = {"com.mcsoft.services1","com.mcsoft.services2"})
@ComponentScan(basePackageClasses = {com.mcsoft.services1.Service1.class,com.mcsoft.services2.Service2.class})
```

如果你有使用basePackageClasses的需求，官方推荐的做法是在包内定义一个空的仅用于指定包路径的Class，这样不会影响代码，也能保证包路径安全。

该注解官方示例如下：

```java
@Configuration
@ComponentScan("com.acme.app.services")
public class AppConfig {
    // various @Bean definitions ...
}
```

注意，由于@Configuration也能被包扫描感知到并由Spring容器将其作为配置类正确处理，因此同时使用@Import和@ComponentScan可能会显得有些冗余，但@ComponentScan主要功能是扫描被Spring注解标记的类，而@Import主要功能是引入有@Bean定义的类，二者的功能在逻辑上还是有所不同的，我个人觉得都保留下来是比较好的做法。

#### [@Profile](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/annotation/Profile.html)

@Profile及[Environment接口](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/core/env/Environment.html)是Spring里一个较抽象的概念和一个完整的体系，在这里我以自己理解尽量解释清楚，并且紧密围绕着配置来讲。

几乎所有程序都需要进行配置，我们可以认为这里的Environment就是程序运行环境的抽象，包括内部、外部配置的一个抽象，而@Profile是用于指定多套配置的配置名时使用的注解。

举个例子，我们有一套开发环境的配置和一套生产环境的配置，假如我们希望能通过一个简单的方式，在开发时使用开发配置，在上线后使用生产环境配置，那么通过@Profile和Environment相结合，使用@Profile来标记配置名，再用Environment指定使用哪套配置就可以实现这种方式。

@Profile只有一个默认的java.lang.String[]类型的属性value，因此它可以指定一个或多个配置名。

@Profile有三种使用方式：

1. 标记在@Configuration类上。
2. 标记在@Bean方法上。
3. 与其他注解整合使用，类似@Configuration整合了@Component的方式。

下面是@Profile使用方式的例子，这里假设有两套配置应用于开发和生产环境，分别对应配置名为"development"和"production"：

```java
//标记在@Configuration类上
@Configuration
@Profile("development")
public class StandaloneDataConfig {
    @Bean
    public DataSource dataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.HSQL)
            .addScript("classpath:com/bank/config/sql/schema.sql")
            .addScript("classpath:com/bank/config/sql/test-data.sql")
            .build();
    }
}

@Configuration
@Profile("production")
public class JndiDataConfig {
    @Bean(destroyMethod="")
    public DataSource dataSource() throws Exception {
        Context ctx = new InitialContext();
        return (DataSource) ctx.lookup("java:comp/env/jdbc/datasource");
    }
}

//标记在@Bean方法上
@Configuration
public class AppConfig{
    @Bean("dataSource")
    @Profile("development")
    public DataSource standaloneDataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.HSQL)
            .addScript("classpath:com/bank/config/sql/schema.sql")
            .addScript("classpath:com/bank/config/sql/test-data.sql")
            .build();
    }

    @Bean("dataSource")
    @Profile("production")
    public DataSource jndiDataSource() throws Exception {
        Context ctx = new InitialContext();
        return (DataSource) ctx.lookup("java:comp/env/jdbc/datasource");
    }
}

//与其他注解整合
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Profile("production")
public @interface Production {
}
```

并且@Profile也有一个与之对应的xml配置项"profile"，可以作为属性配置在`<beans>`内，如下例：

```xml
<beans profile="development">
    <!-- bean definitions -->
</beans>

<beans profile="production">
    <!-- bean definitions -->
</beans>
```

现在已经指定好配置的名字了，下面是启用配置的方法。

启用配置有两种形式，一种是通过[ConfigurableEnvironment](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/core/env/ConfigurableEnvironment.html)的setActiveProfiles(java.lang.String... profiles)方法来指定一个或多个配置名，另一种是通过程序运行参数来指定一个或多个配置名。

注意，不管是哪种方法，当需要有多个配置同时生效时，都可以使用逗号","来分割多个配置名。

首先我们来看通过编程的方式来指定运行配置：

在这种方式下，我们需要使用一个无参的AnnotationConfigApplicationContext构造器，然后在通过的ConfigurableEnvironment.setActiveProfiles()方法设定好配置名后，使用AnnotationConfigApplicationContext的register()或scan()方法注册beans，最后调用refresh()方法。

一定要注意先后顺序，ConfigurableEnvironment.setActiveProfiles()方法一定要在register()、scan()、refresh()之前，因为ACAC不支持多次调用refresh()方法，所以我们要用无参构造器。如果我们配置好了@Profile，但先调用了register()或scan()再调用setActiveProfiles()，那么除了配置名为"default"的配置或没有配置@Profile的配置外，其他的配置名都不会被Spring容器处理。

```java
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext();

ConfigurableEnvironment env = context.getEnvironment();
env.setActiveProfiles("development");
ctx.register(SomeConfig.class, StandaloneDataConfig.class, JndiDataConfig.class);
ctx.refresh();
```

在这里，我们把所有配置都塞进register()方法里，但由于已经设定了启用的配置名，因此框架会检测@Profile注解，匹配的名字才会去处理，没有匹配的则会被抛弃。

注意，只有使用了@Profile注解的部分才会被匹配处理，没有使用@Profile注解的部分则会被正常处理，利用这个特性，我们只需要用@Profile标记多个环境下不同的配置部分，而共用的配置则不用@Profile标记，来避免相同配置重复多次。

再来看看使用程序运行参数来指定配置的方式：

我们需要通过参数`spring.profiles.active`来指定运行时使用的配置，这个参数可以配置在操作系统环境变量、JVM运行参数、web.xml里的Servlet Context Param等处。

比如在运行项目时，使用下面的参数来指定：

```
java -Dspring.profiles.active="p1,p2" ... 程序名
```

另外，在集成测试时，使用spring-test组件的@ActiveProfiles注解也可以指定运行时的配置名。

@Profile还有另外两个特性，在这里简单描述下：

1. 如果没有设置启用的配置名，则默认配置名为"default"，此时标为"default"和没有@Profile注解的配置会被启用。
2. 如果配置名前加了叹号'!'，则表示当指定的配置名没有启用时，则启用该配置。比如@Profile({"p1","!p2"})，当指定配置名为p1或配置名p2没有启用时，该配置就会被启用。

@Conditional

@Scope

@Lazy

@DependsOn

@Primary

@Order


## AnnotationConfigWebApplicationContext介绍

## 四种配置形式

JavaConfig-centric Xml-centric full-JavaConfig full-xml

## 一个简单的无xml化Web项目

JavaConfig实例
