# System.getProperty Access

```java
String value;
if (System.getSecurityManager() == null) {
    value = System.getProperty(key);
} else {
    value = AccessController.doPrivileged(new PrivilegedAction<String>() {
        @Override
        public String run() {
            return System.getProperty(key);
        }
    });
}
return value;
```

```java
/*
 * Always available
 */
ROWSET_PROPERTIES = "javax" + strFileSep + "sql" +
        strFileSep + "rowset" + strFileSep +
        "rowset.properties";

final ClassLoader cl = Thread.currentThread().getContextClassLoader();

try {
    AccessController.doPrivileged(new PrivilegedExceptionAction<Void>() {
        @Override
        public Void run()  throws SyncFactoryException, IOException, FileNotFoundException {
            try (InputStream stream  = (cl == null) ?
                    ClassLoader.getSystemResourceAsStream(ROWSET_PROPERTIES)
                    : cl.getResourceAsStream(ROWSET_PROPERTIES)) {
                if (stream == null) {
                    throw new SyncFactoryException("Resource " + ROWSET_PROPERTIES + " not found");
                }
                properties.load(stream);
            }
            return null;
        }

    });
} catch (PrivilegedActionException ex) {
    Throwable e = ex.getException();
    if (e instanceof SyncFactoryException) {
      throw (SyncFactoryException) e;
    } else {
        SyncFactoryException sfe = new SyncFactoryException();
        sfe.initCause(ex.getException());
        throw sfe;
    }
}
```

