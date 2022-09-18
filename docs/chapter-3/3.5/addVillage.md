1.12版的村民机制稍微有点反直觉. 村民的职业分成了大类`Profession`, 大类又细分成了各种具体的职业`Career`. 
```java
VillagerProfession prof = new VillagerProfession("minecraft:farmer",  
        "minecraft:textures/entity/villager/farmer.png",  
        "minecraft:textures/entity/zombie_villager/zombie_farmer.png");  
{  
    register(prof, 0);  
    (new VillagerCareer(prof, "farmer")).init(VanillaTrades.trades[0][0]);  
    (new VillagerCareer(prof, "fisherman")).init(VanillaTrades.trades[0][1]);  
    (new VillagerCareer(prof, "shepherd")).init(VanillaTrades.trades[0][2]);  
    (new VillagerCareer(prof, "fletcher")).init(VanillaTrades.trades[0][3]);  
}
```
Forge的源码里可以看到原版村民里大类`farmer`里细分出了四个职业`farmer`, `fisherman`, `shepherd`和`fletcher`. 因此在游戏里和村民交互的实际部分是`VillagerCareer`的实例而不是`VillagerProfession`的. 

```java
@Mod.EventBusSubscriber  
public class ModVillageClass {  
    public static final VillagerRegistry.VillagerProfession TESTMOD_VILLAGE_CLASS = new VillagerRegistry.VillagerProfession(  
            Testmod.MOD_ID + ":myprofession",  
            Testmod.MOD_ID + ":textures/entity/villager/testmod_worker.png",  
            Testmod.MOD_ID + ":textures/entity/zombie_villager/zombie_testmod_worker.png");  
  
    public static final VillagerRegistry.VillagerCareer TESTMOD_VILLAGE_COPPER_WORKER = new VillagerRegistry.VillagerCareer(TESTMOD_VILLAGE_CLASS, Testmod.MOD_ID + ".testmodWorker");  
  
    @SubscribeEvent  
    public static void onVillagerProfessionRegistration(RegistryEvent.Register<VillagerRegistry.VillagerProfession> event) { 
	    event.getRegistry().registerAll(TESTMOD_VILLAGE_CLASS);  
    }
}
```

注意调用注册的时候注册对象是`VillagerProfession`的实例.

## 添加交易内容
原版自带了六种交易模板, 可以用来快速添加交易内容:
- `EmeraldForItem` : 用物品换一个绿宝石
- `ItemAndEmeraldToItem` : 特定数量物品+一个绿宝石换一个指定物品
- `ListEnchantedBookForEmeralds` : 用绿宝石换附魔书
- `ListEnchantedItemForEmeralds` : 用绿宝石换附魔物品
- `ListItemForEmeralds` : 用绿宝石换物品
- `TreasureMapForEmeralds` : 用绿宝石换宝藏图

不同的模板要的参数不一样, 以`EmeraldForItem`为例子
```java
public EmeraldForItems(Item itemIn, EntityVillager.PriceInfo priceIn)  
{  
    this.buyingItem = itemIn;  
    this.price = priceIn;  
}
```
第一个传入参数是被交易的物品, 第二个参数则是物品数量.

随后调用forge添加的方法来增加交易内容
```java
public VillagerCareer addTrade(int level, ITradeList... trades)  
{  
 ///...
}
```
第一个参数为该交易配方的所需的村民职业等级, 第二个则为交易具体内容.
简单添加一个一级村民就能交易的配方, 用1~2个铜锭换一个绿宝石.
```java
@SubscribeEvent  
public static void onVillagerProfessionRegistry(RegistryEvent.Register<VillagerRegistry.VillagerProfession> event) {  
    event.getRegistry().registerAll(TESTMOD_VILLAGE_CLASS);  
  
    TESTMOD_VILLAGE_COPPER_WORKER.addTrade(1, 
    new EntityVillager.EmeraldForItems(ModItemList.INGOT_COPPER, new EntityVillager.PriceInfo(1,2)));  
}
```

## 可变长度参数
`addTrade`方法里的第二个参数声明了是可变长度的参数, 因此可以传入数组
```java
TESTMOD_VILLAGE_COPPER_WORKER.addTrade(1,  
        new EntityVillager.EmeraldForItems(ModItemList.INGOT_COPPER, new EntityVillager.PriceInfo(1,2)),  
        new EntityVillager.ItemAndEmeraldToItem(ModItemList.INGOT_COPPER, new EntityVillager.PriceInfo(1,2), Item.getItemFromBlock(ModBlockList.BLOCK
```
