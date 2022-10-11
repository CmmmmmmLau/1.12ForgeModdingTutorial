已经有了能力, 接下来就是**能力提供者(CapabilityProvider)**. 身为一个能力提供者的类需要实现一个`ICapabilityProvider`接口, 如果你的数据存储方式是通过NBT的话直接去实现`ICapabilitySerializable`即可.
```java
public class ProviderLifeSteal implements ICapabilitySerializable<NBTTagCompound> {  
    private final CapabilityLifeSteal instance;  
    private final Capability<CapabilityLifeSteal> capability;  
  
    public ProviderLifeSteal() {  
        this.instance = new CapabilityLifeSteal();  
        this.capability = ModCapability.LIFE_STEAL;  
    }  
    @Override  
    public boolean hasCapability(@Nonnull Capability<?> capability, @Nullable EnumFacing facing) {  
        return this.capability.equals(capability);  
    }  
    @Nullable  
    @Override    
    public <T> T getCapability(@Nonnull Capability<T> capability, @Nullable EnumFacing facing) {  
        return this.capability.equals(capability) ? this.capability.cast(this.instance) : null;  
    }  
    @Override  
    public NBTTagCompound serializeNBT() {  
        return this.instance.serializeNBT();  
    }  
    @Override  
    public void deserializeNBT(NBTTagCompound nbt) {  
        this.instance.deserializeNBT(nbt);  
    }
}
```

## 附加能力
万事具备, 只差应用. 
所有对象的能力附加都是通过`AttachCapabilityEvent`事件来完成, 该事件有以下五个可用的泛型
-   `AttachCapabilityEvent<Entity>`: 仅对实体触发
-   `AttachCapabilityEvent<TileEntity>`: 仅对TileEntity触发
-   `AttachCapabilityEvent<Item>`: 仅对ItemStack触发
-   `AttachCapabilityEvent<World>`: 仅对世界触发
-   `AttachCapabilityEvent<Chunk>`：仅对区块触发

泛型只有以上五种可用且无法更加细化. 比如玩家所在的类`EntityPlayer`虽然是`Entity`的子类但你还是必须只用`Entity`.

```java
@SubscribeEvent  
public static void onAttachCap(AttachCapabilitiesEvent<Entity> event) {  
    if (event.getObject() instanceof EntityLivingBase) {  
        ProviderLifeSteal providerLifeSteal = new ProviderLifeSteal();  
        event.addCapability(new ResourceLocation(   Testmod.MOD_ID + ":lifeSteal"), providerLifeSteal);  
    }
}
```
神奇的是在能力附加的过程中我们只提供了能力提供者. Forge会从特定层面, 如魔法一般的获得这个提供者对应的能力.

## 数据持久化
能力系统也有着和实体数据管理器类似的毛病. 当玩家死亡, 从末地回来等等. 系统会将当前的玩家实体销毁, 然后重生生成一个. 这个过程会将数据给初始化, 因此我们得要将数据给还原. 幸运的是Forge有对应的方法可以便捷的完成这个过程.
```java
@SubscribeEvent  
public static void onPlayerClone(PlayerEvent.Clone event) {  
    CapabilityLifeSteal instance = event.getEntity().getCapability(ModCapability.LIFE_STEAL, null);
    //获取生成出来的新实体, 并获取能力  
    CapabilityLifeSteal original = event.getOriginal().getCapability(ModCapability.LIFE_STEAL, null);
    //获取被销毁的旧实体, 并获取能力
  
    instance.setTimer(original.getTimer());  
}
```
除此之外该事件还有个方法`isWasDeath()`. 该方法会返回`True`若玩家是因为死亡, 否则返回`false`