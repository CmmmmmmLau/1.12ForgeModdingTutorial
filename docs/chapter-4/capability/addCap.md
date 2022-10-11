接下来的代码是为了配合自己添加的新附魔增加一个冷却效果. 当附魔冷却中的时候无法触发效果, 并且只有拿在手上的时候才会冷却.

## 实例数据类
每个能力同样也是一个实例, 既然是实例也需要一个类来处理一下. 除了构造函数还需要对应数据字段的Getter和Setter. 然后再加一个方法用来进入CD.
```java
public class CapabilityLifeSteal {  
    private int Timer;  
    static  int COOLDOWN = 600;
    
    public CapabilityLifeSteal() {  
        Timer = 0;  
    }  
    public int getTimer() {  
        return Timer;  
    }  
    public void setTimer(int timer) {  
        Timer = timer;  
    }  
    public void resetTimer(int level) {   
        Timer = level == 3 ? COOLDOWN / 2 : COOLDOWN;  
    }  
}
```

## 数据存储
在数据类里也实现一下数据的存储.
```java
public class CapabilityLifeSteal implements INBTSerializable<NBTTagCompound> {  
    private int Timer;  
    static  int COOLDOWN = 600;  
  
    public CapabilityLifeSteal() {  
        Timer = 0;  
    }  
    public int getTimer() {  
        return Timer;  
    }  
    public void setTimer(int timer) {  
        Timer = timer;  
    }  
    public void resetTimer(int level) {  
        Timer = level == 3 ? COOLDOWN / 2 : COOLDOWN;  
    }  
    @Override  
    public NBTTagCompound serializeNBT() {  
        NBTTagCompound nbtTagCompound = new NBTTagCompound();  
        nbtTagCompound.setInteger("LifeStealCD", Timer);  
        return nbtTagCompound;  
    }  
    @Override  
    public void deserializeNBT(NBTTagCompound nbt) {  
        Timer = nbt.getInteger("LifeStealCD");  
    }
}
```