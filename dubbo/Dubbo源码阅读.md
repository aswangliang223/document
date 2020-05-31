  dubbo内核源码阅读

@SPI

 serviceConfig.java

```java
private Protocol protocol = ExtensionLoader.getExtensionLoader(Protocol.class).getAdaptiveExtension();
```

