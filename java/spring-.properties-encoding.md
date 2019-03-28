# Spring .properties encoding

Spring Boot对于配置文件的支持非常完善，在配置文件中的内容可以很方便的在程序中应用。在配置文件中可能会出现中文的情况，而这个也是一个可能出现乱码的地方。

首先Spring Boot支持两大类配置文件，一类是java properties文件，即application.properties文件；一类是yaml文件，即application.yml文件。

这两种配置文件分别使用 PropertiesPropertySourceLoader 和YamlPropertySourceLoader加载。这两种加载器对于编码的处理是不同的。

PropertiesPropertySourceLoader 会首先检查配置文件后缀，如果是xml文件，那么是可以支持UTF-8的，但是如果是非xml文件，那么它调用的实际上是java的Properties类的load方法，而这个方法默认配置文件的编码是ISO 8859-1的。参考下面的Java API文档：

> The load\(Reader\) / store\(Writer, String\) methods load and store properties from and to a character based stream in a simple line-oriented format specified below. The load\(InputStream\) / store\(OutputStream, String\) methods work the same way as the load\(Reader\)/store\(Writer, String\) pair, except the input/output stream is encoded in ISO 8859-1 character encoding. Characters that cannot be directly represented in this encoding can be written using Unicode escapes as defined in section 3.3 of The Java™ Language Specification; only a single ‘u’ character is allowed in an escape sequence. The native2ascii tool can be used to convert property files to and from other character encodings. 可以看到这里提供了一个选项，如果不是ISO 8859-1编码的，那么就需要使用Unicode escapes。

而 YamlPropertySourceLoader 专门针对yaml格式，调用的是org.yaml.snakeyaml.Yaml实现，默认是支持utf-8的。

所以综上所述，如果遇到Spring Boot配置文件中文乱码的有两个选项：

1. 使用Unicode escapes
2. 使用yaml格式或者xml格式

