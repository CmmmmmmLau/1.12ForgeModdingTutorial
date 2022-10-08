## 刷怪蛋
声明实体实例的时候链式调用再加一条即可.
```java
public static final EntityEntry COPPER_GOLEM = EntityEntryBuilder  
        .create()  
        .entity(EntityCopperGolem.class)  
        .id(EntityCopperGolem.ID,0)  
        .name(EntityCopperGolem.NAME)  
        .tracker(80, 3, true)  
        .egg(0xA233FF, 0x3333)
        //第一个参数是刷怪蛋的主颜色, 第二个是斑点的颜色  
        .build();
```

## 自然生成
所有群系同样也都是实例, 原版所有群系所对应的群系都可以在路径`net/minecraft/init/Biomes.java`下找到. 写一段`Biome`类的数组字段, 将想要生成的群系都加进去就好了. 链式调用再设定一下生成.
```java
public static final Biome BIOMES[] = new Biome[]{Biomes.DESERT, Biomes.BEACH, Biomes.BEACH};
```
```java
public static final EntityEntry COPPER_GOLEM = EntityEntryBuilder  
        .create()  
        .entity(EntityCopperGolem.class)  
        .id(EntityCopperGolem.ID,0)  
        .name(EntityCopperGolem.NAME)  
        .tracker(80, 3, true)  
        .egg(0xA233FF, 3333)  
        .spawn(EnumCreatureType.MONSTER, 100, 1, 3, EntityCopperGolem.BIOMES)  
        .build();
```
说明一下需要的5的参数:
- `EnumCreatureType`: 生成类型, 原版可供使用的有`MONSTER`(怪物), `CREATURE`(动物), `AMBIENT`(飞行生物) 和`WATER_CREATURE`(水中生物).
- `weight`: 生成权重, 越高概率越大. 最大100最小0.
- `min`: 每次生成时的最小数量
- `max`: 每次生成时的最大数量
- `biomes`: 会自然生成的群系. 因为是可变参数能够传入数组或者单个.

Forge也将原版的各种群系都打上了各种`Type`, 所有的`Type`也都是实例, 并提供了对应的方法.
- `BiomeDictionary.getBiomes(BiomeDictionary.Type.PLAINS)`: 使用类型获得对应群系
- ` BiomeDictionary.getTypes(Biomes.BEACH)`: 传入某个群系获得其包含的类型\

虽然说类名是叫`Type`, 但我个人更倾向于叫**标签**.

除了链式调用的时候能够设定自然生成, Forge也提供了一个单独的方法`EntityRegistry.addSpawn`. 可以用来配合`BiomeDictionary`下的各种方法实现更加复合的生成群系设定
```java
public static void EntitySpawnInit() {  
    List<Biome> spawnList = Lists.newArrayList();  
    for (Biome biome : ForgeRegistries.BIOMES.getValuesCollection()) {  
        if (BiomeDictionary.hasType(biome, BiomeDictionary.Type.PLAINS) || BiomeDictionary.hasType(biome, BiomeDictionary.Type.COLD)) {  
            spawnList.add(biome);  
        }    
    }    
    EntityRegistry.addSpawn(EntityDirtBallKing.class, 100, 1, 3, EnumCreatureType.MONSTER, spawnList.toArray(new Biome[0]));  
}
```

当然你也可以使用事件来实现特定条件下的生成而不是自然生成.