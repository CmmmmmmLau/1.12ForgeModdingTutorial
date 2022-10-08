属性词条系统本身依旧具有一定局限性, 其最主要的问题是只能接受数字形式的内容作为数据. 但实体本身存储的数据远不止数字内容, 同时也有字符串/布尔值/物品/方块等内容. 对于这种需要经常双端同步的数据Minecraft使用了自己造的`EntityDataManager`实体数据管理器系统.\
每个实体内都可以含有一个或者多个的`EntityDataManager`实例, 而每个实例中又添加了不同的`DataParameter<>`字段.
```java
public static final DataParameter<Integer> MYDATA = EntityDataManager.createKey(EntityDirtBallKing.class, DataSerializers.VARINT);
```
其中`DataParameter`中可用的数据类型种类非常多, 全都可以在`DataSerializers`类中找到.\
而第一个参数则是该数据管理器所负责的实体Class, 第二个参数则为该数据类型

## 初始化数据
有了`DataParameter`字段之后接着就是需要在实体上初始化该数据, 通常来讲都是放在`entityInit()`中处理. 也可以根据自己需求放在对应的方法体中, 只要确保使用数据前已经过初始化即可.
```java
@Override  
protected void entityInit() {  
    super.entityInit();  
    this.getDataManager().register(MYDATA, 0);  
}
```

## 读写数据
```java
this.getDataManager().set(MYDATA, this.rand.nextInt(3));
//写入数据
this.getDataManager().get(MYDATA)
//读取数据
```

## 序列化与反序列化
和属性词条不同的是额外数据并不会直接写入存档也没办法写入存档, 因此只要退出游戏数据便会消失. 需要将数据资料转换为另一种可保存且能日后读取的另一种各种, 这个过程成为**序列化(Serialization)**. 而这个过程的逆操作称为**反序列化(Deserialization)**. 对与Minecraft中, 所要转换成的数据形式便是**NBT标签**.\
对于调用实体的序列化和反序列化操作, 只需要简单的重写对应的方法即可.
```java
@Override  
public void writeEntityToNBT(NBTTagCompound compound) {  
    super.writeEntityToNBT(compound);  
    compound.setInteger("myData", this.getDataManager().get(MYDATA));  
}  
  
@Override  
public void readEntityFromNBT(NBTTagCompound compound) {  
    super.readEntityFromNBT(compound);  
    this.getDataManager().set(MYDATA, compound.getInteger("myData"));  
}
```