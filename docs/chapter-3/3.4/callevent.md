监听事件在前面其实已经做过许多次了. 不过还是写篇具体的日志记录下.

## 寻找现成的事件
Forge已经提供了非常多的event供开发者调用了. 可以通过IDE的自动补全, 去`net.minecraftforge.event`下翻找或者Google一个网上别人导出提供的JavaDocs.
这边有个Git仓库提供了从1.12.2版本开始的主流版本号的Forge JavaDocs. https://github.com/Nekoyue/ForgeJavaDocs-NG

因为这边想写个拿着铜锭右键铜矿获得铜锭的机制. 用IDE的补全找到了`PlayerInteractEvent.RightClickBlock`

```java
@SubscribeEvent  
void onRightClickIngotCopper(PlayerInteractEvent.RightClickBlock event) {  

}
```

## 获得数据
Forge里各种数据信息获取的方式就是通过实例的引用, 并不是int, String之类的数据字段.
比如想要获得触发这个事件时候所在的维度, 那我们就需要一个`World`类的实例. 而这个实例event会给我们提供, 自己只需要声明引用即可. `World world = event.getWorld()`
需要注意的是根据事件的不同亦或者Forge版本区别, 会提供的Getter也不一样. 有时候某些数据的获取方法可能要绕个大圈子, 如果实在找不到那也有可能是事件找错了.
比如该事件想要获得所右击的方块就得绕个圈子了.
```java
@SubscribeEvent  
void onRightClickIngotCopper(PlayerInteractEvent.RightClickBlock event) {  
    World world = event.getWorld();  
    BlockPos blockPos = event.getPos();
    // 这里的getPos并不是触发事件实体的坐标, 而是被右击方块的坐标  
    IBlockState blockState = event.getWorld().getBlockState(blockPos);  
    Block block = blockState.getBlock();  
    EntityPlayer player = event.getEntityPlayer();  
    ItemStack itemStack = event.getItemStack();  
}
```

## 具体实现
很直观的部分, 直接贴代码.
```java
@SubscribeEvent  
void onRightClickIngotCopper(PlayerInteractEvent.RightClickBlock event) {  
    World world = event.getWorld();  
    BlockPos blockPos = event.getPos();  
    IBlockState blockState = event.getWorld().getBlockState(blockPos);  
    Block block = blockState.getBlock();  
    EntityPlayer player = event.getEntityPlayer();  
    ItemStack itemStack = event.getItemStack();  
  
    if (!world.isRemote) {  
        if (Block.isEqualTo(block, ModBlockList.BLOCK_ORE_COPPER) && ItemStack.areItemsEqual(itemStack, new ItemStack(ModItemList.INGOT_COPPER))) {  
            player.inventory.addItemStackToInventory(new ItemStack(ModItemList.INGOT_COPPER, 1));  
        }    
    }
}
```
事件的触发中都记得要套一层if用来判断`!world.isRemote`, 用来判断实在逻辑客户端还是逻辑服务端. 不加上这个的话会出现一个事件连续触发两次的问题.

另外一点是`PlayerInteractEvent`下和物品/方块相关的事件很多都是一次互动会触发两次事件, 分别是给主手和副手. 如果if没写好的话可能就甚至出现了一次互动事件被触发了四次的场景. 

## 注册事件
很常规的部分, 加了啥都得和Forge通知一声. 实例化然后注册.
```java
public class ModEvent {  
    public static void onRegistryEvent() {  
        MinecraftForge.EVENT_BUS.register(exampleEvent);  
    }  
    public static final ExampleEvent exampleEvent = new ExampleEvent();  
}
```
回到主类触发注册
```java
public class CommonProxy{  
    public void preinit(FMLPreInitializationEvent event) {  
        ModEvent.onRegistryEvent();  
    }    public void init(FMLInitializationEvent event) {  
        ItemSmeltingRecipes.addSmeltingRecipes();  
    }    public void postinit(FMLPostInitializationEvent event) {  
  
    }  
    public void registerItemModel(Item item, int meta, String type) {  
    }
}
```

### 静态方法
如果你写事件的时候, 用的是静态方法. 那么不用实例化了, 直接传入这个类即可.
```java
public static void onRegistryEvent() {  
    MinecraftForge.EVENT_BUS.register(ExampleEvent.class);  
}
```
```java
public class ExampleEvent {  
    @SubscribeEvent  
    static void myEvent(PlayerInteractEvent.RightClickBlock event) {  
		///SOME CODES
    }
}
```

## 自动注册
可以使用`@Mod.EventBusSubscriber`注解来让Forge自动帮你执行`MinecraftForge.EVENT_BUS.register(ExampleEvent.class);`这个步骤.
```java
@Mod.EventBusSubscriber  
public class ExampleEvent {  
    @SubscribeEvent  
    static void onRightClickIngotCopper(PlayerInteractEvent.RightClickBlock event) {  

    }
}
```
注意方法体必须要是静态的.

## 滥用事件
这个效果的实现实际上根本用不到事件系统,`Item`类和`Block`类中本身就已经有很多与交互有关的方法了. 只需要重写该方法即可实现, 一切本身可以不用事件实现的效果却用事件实现都统称为滥用事件. 过分的滥用事件会导致MSPT的增加, 由于MC是单线程游戏且TPS只有20. 因此维持MSPT低于50才能保持游戏流程.
```java
public class BlockOreCopper extends BlockBase {  
    public BlockOreCopper() {  
        super(Material.ROCK, "ore_copper");  
        this.setHarvestLevel("pickaxe", 2);  
        this.setHardness(1.5F);  
    }  
    @Override  
    public boolean onBlockActivated(World world, BlockPos pos, IBlockState state, EntityPlayer player, EnumHand hand, EnumFacing facing, float hitX, float hitY, float hitZ) {  
        ItemStack itemStack = player.getHeldItemMainhand();  
        Block block = state.getBlock();  
        if (Block.isEqualTo(block, ModBlockList.BLOCK_ORE_COPPER) && ItemStack.areItemsEqual(itemStack, new ItemStack(ModItemList.INGOT_COPPER))) {  
            player.inventory.addItemStackToInventory(new ItemStack(ModItemList.INGOT_COPPER, 1));  
            return true;  
        }        
        return false;  
    }
}
```