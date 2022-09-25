原版就提供的实体属性全都可以在`SharedMonsterAttributes`下找到. 如果想要添加自己的属性词条也只需要添加`IAttribute`下的字段即可.\
对于向实体设定词条则需要在实体类中重写对应的方法后调用修改方法即可.
```java
@Override  
protected void applyEntityAttributes() {  
    super.applyEntityAttributes();  
    this.getEntityAttribute(SharedMonsterAttributes.MOVEMENT_SPEED).setBaseValue(0.4);  
    this.getEntityAttribute(SharedMonsterAttributes.MAX_HEALTH).setBaseValue(20);  
    this.getEntityAttribute(SharedMonsterAttributes.ATTACK_DAMAGE).setBaseValue(0.3);  
}
```
但是如同`Item`和`ItemStack`之间的关系一样. 当我们试图修改属性词条的时候修改的并不是`IAttribute`接口下的内容. 而实体身上的具体属性词条是`IAttributeInstance`下的实例.
因此`getEntityAttribute`方法实际上就是返回了`IAttributeInstance`的实例.

## 添加新的属性词条
没有技术含量的部分, 看一眼原版是怎么做的就知道了.
```java
public class ModAttribute {  
    public static final IAttribute MANA = new RangedAttribute(null, "testmod.attribute.mana", 0.0, 0.0, 20.0)  
            .setDescription("Mana")  
            .setShouldWatch(true);  
}
```
而最后面的`setShouldWatch`则决定了这个值是否会向客户端同步, 如果你想把他隐藏起来不让玩家看到设为false即可. 不过因为词条是我们添加进去的, 所以套用在实体的时候得"注册"一下.
```java
@Override  
protected void applyEntityAttributes() {  
    super.applyEntityAttributes();  
    this.getEntityAttribute(SharedMonsterAttributes.MOVEMENT_SPEED).setBaseValue(0.4);  
    this.getEntityAttribute(SharedMonsterAttributes.MAX_HEALTH).setBaseValue(20);  
    this.getEntityAttribute(SharedMonsterAttributes.ATTACK_DAMAGE).setBaseValue(0.3);  
  
    this.getAttributeMap().registerAttribute(ModAttribute.MANA).setBaseValue(20);  
}
```

## 属性修饰符(Attribute Modifier)
如果你的属性词条是属于会动态变化的, 而不是实体生成一瞬间固定下来的那种. 虽然你可以不停的`setBaseValue`来改变数值, 但这不是推荐的行为. 对于这种情况更应该使用属性修饰符.

每个属性修饰符也是一个实例, 因此只要声明新的实例即可. 但是属性修饰符的实例还需要UUID参数和决定运算方式. 一般来讲还是推荐通过第三方网站生成一个固定UUID.
```java
AttributeModifier SPEED_MODIFIER = new AttributeModifier(UUID.randomUUID(), "testmod_speed_modifier", 1.0 ,0);
```
倒数第二个为修改数值, 而最后一个`operationIn`则是最为重要的运算方式. 不同的选择会影响不同的数据结果. 假设有个属性数值为20, 且两个修饰符数值是0.5和1.5, 则不同运算方式有以下结果:
- `operationIn` == 0: 标记为0的修饰符会直接叠加, 因此结果会是$20 + 0.5 + 1.5 = 22$
- `operationIn` == 1: 标记为1的修饰符是对属性数值百分比增加, 但是修饰符的数值会先进行叠加: $20 * (1 + 0.5 + 1.5) = 60$
- `operationIn` == 2: 标记为2的修饰符也是对属性数值百分比增加, 但是修饰符的数值不会相互影响: $20 * (1 + 0.5) * (1 + 1.5) = 75$

## 套用修饰符
首先得要获得实体的`IAttributeInstance`
```java
IAttributeInstance attributeInstance = this.getEntityAttribute(SharedMonsterAttributes.MAX_HEALTH);
```
随后使用`applyModifier`方法即可套用修饰符
```java
attributeInstance.applyModifier(new AttributeModifier(ModAttribute.HEALTH_UP_ID, "testmod_speed_modifier", new Random().nextInt(10), Constants.AttributeModifierOperation.ADD));
```