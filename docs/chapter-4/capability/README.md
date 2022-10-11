在Mod开发中, 附加数据是非常常见的需求.\
虽然有原版已经提供了的`EntityDataManager`, 但该功能局限性很大. 只能夠使用在实体身上, 且无法修改原版或者其他Mod的内容. Forge提供了对`TileEntity`(方块实体), `Entity`(实体), `ItemStack`(物品), `Level`(世界) 和`LevelChunk`(区块)的能力支持. 你可以通过使用Forge提供的事件机制将你的能力附加上去. 与此同时Forge也自带提供了三种能力`IItemHandler`(背包处理), `IFluidHandler`(流体处理)和`IEnergyStorage`(能源容器处理)\
能力系统出现的同时也解决了跨Mod联动时相互之间代码过于耦合的问题.\
假设我们有一个实体方块, 需要和另一个模组进行联动. 那么首先我们的代码就需要使用对方所提供的接口.
```java
Class ModTileEntity extend TileEntity implements Something, AnotherThing ,...
```
而在交互逻辑的代码中我们也要检查被交互的对象是否实现了某个接口
```java
if (tileEntity instanceof Something){
	//do something
}
if (tileEntity instanceof AnotherThing){
	//do another thing
}
if (...)
```

这段代码虽然没有什么问题但却有着非常强的代码耦合. 因为`Something`和`AnotherThing`是来自其他Mod的接口, 这么写会将你的Mod就会变成依赖对方Mod的拓展Mod而不是添加联动功能.\
而能力系统出现之后解决了这个棘手的问题. 解决了需要实现一连串的接口和代码过强的耦合.
```java
class ModTileEntity extends CapabilityProvider
```
随后询问Forge系统是否有该能力
```java
if (tileEntity.hasCapability(CapabilitySomething.ModCap, null)) {
    Something sth = tileEntity.getCapability(CapabilitySomething.ModCap, null);
}
```

## 能力系统的运作
一个能力系统的运作主要分成3个角色. **访问者/调用方**, **能力提供者(CapabilityProvider)** 和 **能力(Capability)** .\
在游戏逻辑中, 首先访问者会对被访问对象的能力提供者询问该对象是否拥有某个能力. 如果有, 则直接返回该能力的实例. 若没有则返回null.\
而将能力附着在目标上的过程则是通过Forge的事件系统完成.\
但遗憾的是能力系统只涉及到数据方面的运作, 而对于数据存储的要求在不单独实现新功能的情况下依旧得返回到NBT标签上.

其实能力系统的过程和`EntityDataManager`非常相似. 后者的运作是先`getDataManager()`从目标身上获得对应的**数据管理器**, 前者是`hasCapability()`判定是否拥有后再`getCapability()`获取**能力**. 两者在都获得各自想要的东西之后都能够从中调取出数据.\
但是后者只能使用在实体上, 而前者拓展了范围能够交互的对象大大变多的同时因为Forge自身的支持能实现的效果大大变多而不是单纯的数据上的存储.