# Synchronized & Locks

#### synchronized

* [深入浅出synchronized](http://blog.jobbole.com/104725/)

#### Volatile & Synchronized

* [Difference between volatile and synchronized in Java - stackoverflow](http://stackoverflow.com/questions/3519664/difference-between-volatile-and-synchronized-in-java)
* [Double-Checked Locking](https://en.wikipedia.org/wiki/Double-checked_locking)
  * [What Volatile Means in Java](http://jeremymanson.blogspot.com/2008/11/what-volatile-means-in-java.html)
  * [Double-Checked Locking is Broken](http://www.cs.umd.edu/~pugh/java/memoryModel/DoubleCheckedLocking.html)

```java
class Foo {
  private volatile Helper helper = null;
  public Helper getHelper() {
    if (helper == null) {
      synchronized(this) {
        if (helper == null) {
          helper = new Helper();
        }
      }
    }
  return helper;
}
```

