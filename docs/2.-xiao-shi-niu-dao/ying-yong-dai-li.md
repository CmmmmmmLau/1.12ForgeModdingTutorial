# 2.4 建立代理系统

代理系统其实在之前添加物品和方块的时候已经应用上了.\
翻看注册模型部分的时候可以看到一个注解`@SideOnly(Side.CLIENT)` \
这个注解代表了在它下面的那一个代码块只需要在客户端一方运行即可,这也很合理.因为服务端没有去管模型和材质方面的必要.\
如果是只需要在服务端运行的部分则是填`@SideOnly(Side.SERVER)` \
但当你的项目发展到几十上百个类之后,一个一个的去添加注解就太过麻烦了.这里就需要使用到第二种代理系统的使用方法了.

## 声明代理

首先在主类添加以下代码用以声明代理系统

```java
@SidedProxy(serverSide = "CommonProxy",
 clientSide = "ClientProxy") 
 public static CommonProxy proxy;
```

`CommonProxy`处是填上服务端行为的类,因为服务端的代码通常都会在客户端也执行.所以是CommonProxy而不是ServerProxy

`ClientProxy`则是填上客户端行为的类

通常来讲`CommonProxy`类会在`common.proxy`包里.而`ClientProxy`则是在`client.proxy`包里.也有人会在给这`proxy`包前面再套一个`core`包之类的.但这些完全只是个人喜好,不必太过纠结.

示例代码

```java
@SidedProxy(serverSide = "cmmmmmm.testmod.common.CommonProxy",
    clientSide = "cmmmmmm.testmod.client.ClientProxy")
public static IProxy proxy;
```

## 接口类

此时IDEA会报错,因为我们得把`IProxy` 补上

```java
public interface IProxy {
    public void preinit(FMLPreInitializationEvent event);

    public void init(FMLInitializationEvent event);

    public void postinit(FMLPostInitializationEvent event);
}
```

## 应用代理

先前讲过一个Mod的开始就是从主类的三大初始化阶段开始.我们需要让初始化内容先转移到代理类中.

```java
@Mod.EventHandler
public void preinit(FMLPreInitializationEvent event) {
    proxy.preinit(event);
}

@Mod.EventHandler
public void init(FMLInitializationEvent event) {
    proxy.init(event);
}

@Mod.EventHandler
public void postinit(FMLPostInitializationEvent event) {
    proxy.postinit(event);
}
```

同样的,我们也要CommonProxy类上实现接口`IProxy`.这样就能完成转移了

```java
package cmmmmmm.testmod.common;

import net.minecraftforge.fml.common.Mod;
import net.minecraftforge.fml.common.event.FMLInitializationEvent;
import net.minecraftforge.fml.common.event.FMLPostInitializationEvent;
import net.minecraftforge.fml.common.event.FMLPreInitializationEvent;

public class CommonProxy {
    public void preinit(FMLPreInitializationEvent event) {

    }

    public void init(FMLInitializationEvent event) {

    }

    public void postinit(FMLPostInitializationEvent event) {

    }
}
```

随后让`ClientProxy`类同样实现接口`IProxy`类

```java
public class ClientProxy implements IProxy {

    public void preinit(FMLPreInitializationEvent event) {

    }

    public void init(FMLInitializationEvent event) {

    }

    public void postinit(FMLPostInitializationEvent event) {

    }
}
```

这样一来代理系统的应用就完成了.时刻记住将只需要客户端执行的代码放到ClientProxy中是个好习惯
