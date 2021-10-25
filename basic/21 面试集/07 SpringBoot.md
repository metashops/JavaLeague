#### 自动配置原理

1、依赖管理

```xml
<!--依赖管理-->
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.5</version>
        <relativePath/> <!-- lookup parent from repository -->
 </parent>
<!--他的父项目>
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.5.5</version>
  </parent>
/**
*几乎不用写版本号，在所有开中的依赖版本号都在
/
<!--如果需要修改版本号,如修改mysql版本号（也可以直接导入全部依赖）-->
<properties>
  <mysql.version>5.7.0</mysql.version>
</properties>
```

2、自动配置好Tomcat

只要引入web依赖，web依赖里面自动配置好Tomcat的

3、自动配置好了SpringMVC组件

4、自动配置好web常见的：符编码问题

5、默认的包结构

* 只要你加入相关注解，比controller层，加入@controller注解即可
* 想要改变扫面配置路径`@SpringbootAppcation(scanbasePackage="xxx")` 或 `@ComponentScan`指定路径



#### 底层注解

@Import：如（`@Import({User.class, DBHelper.class})`）给容器中自动创建出这两个类型的组件

@Conditional：条件组配，满足Conditional指定的条件，则进行组件注入



#### SpringBoot自动配置原理

1、注解 @SpringBootConfiguration，其实就是一个@configuration代表是一个配置类，源码如下： 

```java
@Configuration
@Indexed
public @interface SpringBootConfiguration {
    @AliasFor(annotation = Configuration.class)
    boolean proxyBeanMethods() default true;}
```

2、注解 @ComponentScan，指定扫描哪些，Spring注解

3、注解 @EnableAutoConfiguration

```java
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";
    Class<?>[] exclude() default {};
    String[] excludeName() default {};
}
```

（3.1）核心注解@AutoConfigurationPackage，自动配置包，源码如下：

```java
@Import({Registrar.class}) //注解 @Import 就是给容器中倒入组件
public @interface AutoConfigurationPackage {
    String[] basePackages() default {};
    Class<?>[] basePackageClasses() default {};}
```

解释：注解 @Import 就是给容器中倒入组件，利用Registrar给容器中导入一系列组件，将指定的一个包的所有组件导入进来

```java
static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {
        Registrar() {}
        public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
            AutoConfigurationPackages.register(registry, (String[])(
              new AutoConfigurationPackages.PackageImports(metadata))
                                               .getPackageNames().toArray(new String[0]));
        }
        public Set<Object> determineImports(AnnotationMetadata metadata) {
            return Collections.singleton(new AutoConfigurationPackages.PackageImports(metadata));
        }
    }
```

（3.2）核心注解@Import({AutoConfigurationImportSelector.class})

* 调用 getAutoConfigurationEntry(annotationMetadata);给容器中批量导入一些组件
* 利用this.getCandidateConfigurations(annotationMetadata, attributes);得到所有的组件
* 从META-INF/spring.factories位置来加载一个文件。默认扫描我们当前系统里面所有META-INF/spring.factories位置的文件，spring-boot-autoconfigure-2.3.4.RELEASE.jar包里面也有META-INF/spring.factories

```java
public String[] selectImports(AnnotationMetadata annotationMetadata) {
        if (!this.isEnabled(annotationMetadata)) {
            return NO_IMPORTS;
        } else {
            AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry(annotationMetadata);
            return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
        }
    }
    protected AutoConfigurationImportSelector.AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
        if (!this.isEnabled(annotationMetadata)) {
            return EMPTY_ENTRY;
        } else {
            AnnotationAttributes attributes = this.getAttributes(annotationMetadata);
            List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
            configurations = this.removeDuplicates(configurations);
            Set<String> exclusions = this.getExclusions(annotationMetadata, attributes);
            this.checkExcludedClasses(configurations, exclusions);
            configurations.removeAll(exclusions);
            configurations = this.getConfigurationClassFilter().filter(configurations);
            this.fireAutoConfigurationImportEvents(configurations, exclusions);
            return new AutoConfigurationImportSelector.AutoConfigurationEntry(configurations, exclusions);
        }
    }
```

#### 静态资源配置原理

SpringBoot启动默认加载xxxAutoConfiguration类（自动配置类）

