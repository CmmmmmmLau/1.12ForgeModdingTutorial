
能力注册简单使用Forge提供的方法即可, 但其中需要的三个参数较为复杂. 
```java
public <T> void register(Class<T> type, Capability.IStorage<T> storage, Callable<? extends T> factory)
```
- 首个参数是该能力对应的数据类, 如果你的实现的能力数量很大且都是基于你的接口实现的, 那这里也可以传入该接口类或者抽象类
- 第二个参数则是`IStorage`类的一个实现. 是数据存储方面的一个实现类, 如果数据的存储是通过非NBT方式实现的话直接将方法体留空就行了. 需要注意这里数据存储功能只有在缺省状态下调用, 实际的数据存储方法是基于 **能力提供者(CapabilityProvider)** 里的
- 第三个参数是用来构造默认实例的对象, 此处需要的是`Callable`接口的实例. 因为这里是一个函数式接口, 我们可以直接引用能力类的构造函数. 主要作用是给第一个参数中传入的类提供一个默认的构造函数/默认实现.

```java
CapabilityManager.INSTANCE.register(CapabilityLifeSteal.class, new Capability.IStorage<CapabilityLifeSteal>() {  
    @Nullable  
    @Override    
    public NBTBase writeNBT(Capability<CapabilityLifeSteal> capability, CapabilityLifeSteal instance, EnumFacing side) {  
        return instance.serializeNBT();  
    }  
    @Override  
    public void readNBT(Capability<CapabilityLifeSteal> capability, CapabilityLifeSteal instance, EnumFacing side, NBTBase nbt) {  
        if (nbt instanceof NBTTagCompound) {  
            instance.deserializeNBT((NBTTagCompound) nbt);  
        }    
    }
}, CapabilityLifeSteal::new);
```

同样我们还需要一个用来获取该能力时需要使用到的字段, 并配合注解使用. Forge在注册Capability的时候会自动将该字段关联到对应的实例上.
```java
@CapabilityInject(CapabilityLifeSteal.class)  
public static Capability<CapabilityLifeSteal> LIFE_STEAL;
```

当然别忘了在预注册阶段接口调用.
```java
public void init(FMLInitializationEvent event) {  
    ModSpawn.EntitySpawnInit();  
    ItemSmeltingRecipes.addSmeltingRecipes();  
    ModBrewingRecipe.addBrewingRecipe();  
    ModCapability.register();  
}
```