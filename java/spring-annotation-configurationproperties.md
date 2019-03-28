# Spring Annotation ConfigurationProperties

`@ConfigurationProperties` 可以作用于 `ElementType.TYPE` 即 Class 上也可以作用在 `ElementType.METHOD` 即 Bean 上，在最终初始化选择上是 Bean 会覆盖 Class，实现上是 `org.springframework.boot.context.properties.ConfigurationPropertiesBindingPostProcessor#postProcessBeforeInitialization`

```text
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName)
            throws BeansException {
        // 先 Class
        ConfigurationProperties annotation = AnnotationUtils
                .findAnnotation(bean.getClass(), ConfigurationProperties.class);
        if (annotation != null) {
            postProcessBeforeInitialization(bean, beanName, annotation);
        }
        // 后 Bean
        annotation = this.beans.findFactoryAnnotation(beanName,
                ConfigurationProperties.class);
        if (annotation != null) {
            postProcessBeforeInitialization(bean, beanName, annotation);
        }
        return bean;
    }
```

