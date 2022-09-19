实例化, 然后注册. 就这么简单.
```java
@Mod.EventBusSubscriber  
public class ModPotionType {  
    public static final PotionType FALL_PROTECTION_0 = new PotionType(  
            Testmod.MOD_ID + ".fallProtection",  
            new PotionEffect(ModPotion.PotionList.FALL_PROTECTION, 600, 0)  
    ).setRegistryName("fall_protection_0");  
  
    public static final PotionType FALL_PROTECTION_1 = new PotionType(  
            Testmod.MOD_ID + ".fallProtection",  
            new PotionEffect(ModPotion.PotionList.FALL_PROTECTION, 1200, 1)  
    ).setRegistryName("fall_protection_1");  
  
    @SubscribeEvent  
    public static void onPotionTypeRegistry(RegistryEvent.Register<PotionType> event) {  
        event.getRegistry().registerAll(  
                FALL_PROTECTION_0,  
                FALL_PROTECTION_1  
        );  
    }
}
```

## 添加酿造配方
原版和Forge都添加了方法用来快速添加酿造配方

### 原版
```java
public class ModBrewingRecipe {  
    public static void onBrewingRecipeRegistry(){  
        PotionHelper.addMix(PotionTypes.WATER, ModItemList.INGOT_COPPER, ModPotionType.FALL_PROTECTION_0);  
    }
}
```
`PotionHelper`下有方法可以快捷添加, 但是原版提供的方法功能较为有限. 其第一个参数是酿造台下方的三个输入槽, 第三个参数则为输出物, 并且**必须**得要是药水形式. 第二个则为酿造台上方放的配料.
写完记得在预注册阶段调用即可.

### Forge
```java
public class ModBrewingRecipe {  
    public static void addBrewingRecipe(){  
        BrewingRecipeRegistry.addRecipe(new ItemStack(ModItemList.INGOT_COPPER), new ItemStack(ModItemList.INGOT_COPPER), new ItemStack(ModBlockList.BLOCK_ORE_COPPER));  
    }
}
```
Forge提供的接口, 输入输出和配料都能传入`ItemStack`. 同时还有另一个重载的方法是配料能传入矿物词典. 缺点是因为不包含NBT的检查, 因此无法传入药水. 如果试图使用药水会出现所有不论附带任何状态效果的药水都能使用的问题. 
也可以实现接口`IBrewingRecipe`来做到输入, 输出和配料的具体控制.
如果不需要这么细节的话可以拓展`AbstractBrewingRecipe`做到只对配料输入的控制.

一个实现例子, 能用特定药水当作配料
```java
public class CustomBrewingRecipe extends AbstractBrewingRecipe {  
    protected CustomBrewingRecipe(@Nonnull ItemStack input, @Nonnull Object ingredient, @Nonnull ItemStack output) {  
        super(input, ingredient, output);  
    }  
    @Override  
    public boolean isIngredient(@Nonnull ItemStack ingredient) {  
        return  ingredient.getItem().equals(PotionUtils.addPotionToItemStack(new ItemStack(Items.POTIONITEM), ModPotionType.FALL_PROTECTION_0).getItem());  
    }
}
```
