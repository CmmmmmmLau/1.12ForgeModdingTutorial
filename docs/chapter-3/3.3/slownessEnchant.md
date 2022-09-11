写一个攻击时有概率使目标减速的附魔.\
首先依旧是继承类并调用超类构造函数
```java
public class EnchantmentSlowness extends Enchantment {  
    protected EnchantmentSlowness() {  
        super(Rarity.RARE, EnumEnchantmentType.WEAPON, new EntityEquipmentSlot[]{EntityEquipmentSlot.MAINHAND});  
        setName(Testmod.MOD_ID + ".slowness");  
        setRegistryName("slowness");  
  
        ModEnchantmentList.enchantments.add(this);  
    }  
}
```
其中往超类构造函数中传入的三个参数分别是:
- rarityIn: 需要传入的是`Rarity`类的实例. 用来描述该附魔的稀有程度, 分别有`COMMON`, `UNCOMMON`, `RARE`和`VERY_RARE`. 越靠后越稀有, 越稀有附魔时获得的概率越低. 
- typeIn:  需要传入的是`EnumEnchantmentType`类的实例, 用来描述可以附魔在什么种类的物品上.
- slots: 需要传入的是`EntityEquipmentSlot`类的**数组**. 指明该附魔在什么情况的穿戴下才能生效, 因为是数组, 可以指定多个情况. 比如主手+副手或者特定组合的防具.

## 附魔等级
重写下面两个方法便可以指定该附魔最高和最低等级. 不重写的情况下则是不带等级的附魔.
```java
@Override  
public int getMinLevel() {  
    return 1;  
}  
  
@Override  
public int getMaxLevel() {  
    return 3;  
}
```

## 等级消耗
应该是要获得该附魔所要消耗的最低和最高等级
```java
@Override  
public int getMinEnchantability(int enchantmentLevel) {  
    return 20 * enchantmentLevel;  
}  
  
@Override  
public int getMaxEnchantability(int enchantmentLevel) {  
    return  this.getMinEnchantability(enchantmentLevel) + 10;  
}
```

## 攻击效果
想要获得攻击效果只要重写方法`onEntityDamaged`即可
```java
@Override  
public void onEntityDamaged(EntityLivingBase user, Entity target, int level) {  
    if (target instanceof EntityLivingBase){  
        int i = user.getRNG().nextInt(100);//获得随机数来取得触发概率  
  
        if (level == 1 && i < 50){  
            ((EntityLivingBase) target).addPotionEffect(new PotionEffect(MobEffects.SLOWNESS, 100, 1));  
        } else if (level == 2 && i < 75) {  
            ((EntityLivingBase) target).addPotionEffect(new PotionEffect(MobEffects.SLOWNESS, 100, 2));  
        } else if (level == 3) {  
            ((EntityLivingBase) target).addPotionEffect(new PotionEffect(MobEffects.SLOWNESS, 100, 3));  
        }   
    }
}
```
方法的三个参数就是使用者, 攻击目标和附魔的等级了.

## 防御效果
想要写出类似荆棘护甲效果的话也提供了对应的方法, 直接重写就好
```java
@Override  
public void onUserHurt(EntityLivingBase user, Entity attacker, int level) {  
    super.onUserHurt(user, attacker, level);  
}
```