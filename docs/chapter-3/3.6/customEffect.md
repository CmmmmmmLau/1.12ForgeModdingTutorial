Minecraft中的药水系统从源码角度上来讲分三层.
- `Potion` 类的实例. 这个类中的实例代表着状态效果, 可以理解为这个类下所有的实例就是游戏中所包含的所有状态效果的种类. 原版中所有种类的状态效果可以在`net.minecraft.init.MobEffects`中找到.
- `PotionEffect`的实例. 被具体施加到玩家身上的状态效果, 包含了持续时间, 效果等级等信息.
- `PotionType`的实例. 该类下的所有实例都是药水, 只需要一个实例游戏内便会自动出现**普通药水**, **喷溅药水**和**滞留药水**三种. 同时原版实际上是支持一瓶药水同时包含多种状态效果的.

## 添加新的状态效果
```java
public class PotionFallProtection extends Potion {  
    protected PotionFallProtection() {  
        super(false, 0xe30e0e);  
        setRegistryName(Testmod.MOD_ID + ":fall_protection");  
        setPotionName("effect." + Testmod.MOD_ID + ".fallProtection");
        //这个方法作用和setTranslationKey类似.  
    }
}
```
超类构造函数的两个参数分别是:
- `isBadEffectIn` 是否对玩家有害的状态效果. 决定了其文字介绍的时候是红字还是蓝字
- `liquidColorIn` 药水和其粒子效果的颜色, 传入的是想要颜色的十六进制.

## 状态效果的实现
状态效果实现主要分三种途径, 是否为即时状态或持续状态和事件监听.

### 即时状态
即时状态指的是如瞬间治疗和瞬间伤害这种药水, 一旦施加就立刻会触发. 只要重写`Potion`类中的两个方法即可.
```java
@Override  
public boolean isInstant() {  
    return true;
    //默认返回false, 当为true时就为即时状态
}  
  
@Override  
public void affectEntity(@Nullable Entity source, @Nullable Entity indirectSource, EntityLivingBase entityLivingBaseIn, int amplifier, double health) {  
	//source参数是伤害的直接来源
	//indirectSource参数是间接来源, 既是谁利用这个状态造成了这个伤害
	//entityLivingBaseIn, 受到伤害的实体
	//amplifier, 所施加状态效果的等级
	//health, 未知, 经过实际测试也不是HP
	//当isInstant()返回true的时候, 每次状态效果施加便会调用一次此方法
}
```
需要注意的是当一种状态效果被设定为即时状态, 那么所有持续时间设定都会作废. 

### 持续状态
持续状态效果也给予了个对应的方法去实现效果.
```java
@Override  
public boolean isReady(int duration, int amplifier) {  
    return true;
    //这个方法在每个tick都会调用一次, 并且当该方法返回true时会调用下方的performEffect
    //因为参数列表中还带有持续时间和效果等级
    //因此能写出很具体的true&false机制
    //比如
    //return duration % 100 == 0
    //每过5秒转true
}  
  
@Override  
public void performEffect(EntityLivingBase entityLivingBaseIn, int amplifier) {  
    //效果具体实现部分
}
```
除了上面两个, 还提供了两个会在效果刚开始和结束的时候触发的
```java
  
@Override  
public void applyAttributesModifiersToEntity(EntityLivingBase entityLivingBaseIn, AbstractAttributeMap attributeMapIn, int amplifier) {  
  
}  
  
@Override  
public void removeAttributesModifiersFromEntity(EntityLivingBase entityLivingBaseIn, AbstractAttributeMap attributeMapIn, int amplifier) {  
  
}
```

### 事件监听
`EntityLivingBase`类下有个方法`isPotionActive`, 传入`Potion`的实例便可获得实体身上是否有该状态效果. 还有个`getAtivePotionEffect`, 传入`Potion`的实例便会返回实体身上对应的`PotionEffect`.
利用这两个方法便可以写出判断实体身上是否有该效果以及根据不同的等级实现不同的细节.