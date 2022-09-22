实体的添加部分代码稍有不同, 但是在注册方面是一样的.
```java
public class EntityCopperGolem extends EntityGolem {  
    public static final String ID = "copper_golem";  
    public static final String NAME = Testmod.MOD_ID + ".CopperGolem";  
	//注意这里单独将id和名字分离了
    public EntityCopperGolem(World worldIn) {  
        super(worldIn);  
    }
    //必须要有的构造器.
}
```
可以继承的类有很多, 基本上`entity`包下各种类(除了`EntityPlayer`)都能继承.

## 构造工厂
Forge后面的更新中推出了`EntityEntryBuilder`用来快速创建实例, 只要链式调用一下就可. 当然别忘了注册.
```java
@Mod.EventBusSubscriber  
public class ModEntity {   
    public static final EntityEntry COPPER_GOLEM = EntityEntryBuilder  
            .create()  
            .entity(EntityCopperGolem.class)//这个实体对应的class文件  
            .id(EntityCopperGolem.ID,0)//就是前面物品和方块用到的setRegistryName()  
            .name(EntityCopperGolem.NAME)//就是前面物品和方块用到的setTranslationKey()
            .tracker(80, 3, true)
            //这个实体的仇恨范围, 状态刷新速度(tick), 是否向服务器发送数据
            .build();
    //必须要以create()开头build()结尾.  
  
    @SubscribeEvent  
    public static void onRegistryEntity(RegistryEvent.Register<EntityEntry> event) {  
        event.getRegistry().registerAll(  
                COPPER_GOLEM  
        );  
    }
}
```
上文代码中所用到的链式调用是必须要有的部分, 缺少任意一个都会导致游戏闪退. 但除此之外还有两个没有使用到:
- `.egg()`: 用来设定刷怪蛋的颜色
- `.spawn()`: 用来设定生成信息, 可以传入数组.

## 旧方法
有个以前使用的但是现在已经不被推荐使用的旧方法. 该方法来自原版.
```java
@Mod.EventBusSubscriber  
public class ModEntity {  
    public static void registryEntities() {  
        registryEntity("dirt_ball_king", EntityDirtBallKing.class, 1, 128, 0xffffff, 0xff33ff);  
    }  
  
    public static void registryEntity(String name, Class<? extends Entity> entity, int id, int range,int color1,int color2) {  
        EntityRegistry.registerModEntity(new ResourceLocation(Testmod.MOD_ID + ":" + name), entity, name, id, Testmod.INSTANCE, range, 1, true, color1, color2);  
    }  
}
```
