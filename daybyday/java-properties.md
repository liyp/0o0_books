# Java properties

java properties 处理封装

* [profiles spring-boot](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-profiles.html)
* [apache common configuration](http://commons.apache.org/proper/commons-configuration/)
* [properties j2se tutorial](https://docs.oracle.com/javase/tutorial/essential/environment/properties.html)
* [Properties jdk7 API](https://docs.oracle.com/javase/7/docs/api/java/util/Properties.html)

## [owner](http://owner.aeonbits.org/docs/)

* loading stategies
* importing properties
* parametrized properties
* type conversion
* variables expansion
* reload and hot reload
* accessible and mutable
* debugging
* disabling features
* metaconfiguring
* xml support
* event support
* singleton

## [.properties wikipedia](https://en.wikipedia.org/wiki/.properties)

Each line in a .properties file normally stores a single property. Several formats are possible for each line, including key=value, key = value, key:value, and key value. Note that single-quotes or double-quotes are considered part of the string. Trailing space is significant and presumed to be trimmed as required by the consumer.

Comment lines in .properties files are denoted by the number sign \(\#\) or the exclamation mark \(!\) as the first non blank character, in which all remaining text on that line is ignored. The backwards slash is used to escape a character. An example of a properties file is provided below.

```text
# You are reading the ".properties" entry.
! The exclamation mark can also mark text as comments.
# The key and element characters #, !, =, and : are written with
# a preceding backslash to ensure that they are properly loaded.
website = http\://en.wikipedia.org/
language = English
# The backslash below tells the application to continue reading
# the value onto the next line.
message = Welcome to \
          Wikipedia!
# Add spaces to the key
key\ with\ spaces = This is the value that could be looked up with the key "key with spaces".
# Unicode
tab : \u0009
```

