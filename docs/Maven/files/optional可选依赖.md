#### maven optional可选依赖
> 应用场景：projectA 依赖projectB,  projectB 依赖projectC时

```xml
<dependency>
  <groupId>com.itear.projectC</groupId>
  <artifactId>projectC</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <optional>true</optional>
</dependency>
```

当projectB的<optional>true</optional>时, projectA中如果没有显式的引入projectC, 则projectA不依赖projectC, 即projectA可以自己选择是否依赖projectC


默认<optional>的值为false, 及子项目必须依赖