# 2.3 自定义创造模式物品栏

## 新建物品栏

所有的物品都是`Item`类的实例,所有方块都是`Block`类的实例.\
同理,创造模式下所有分页都是某个类的实例.而这个类则是`CreativeTabs`

我们可以像之前一样新建一个物品栏类,在`CreativeTabs`类上实例化一下

首先新建一个类,为了方便识别我命名成`CreativeTabRegistry`然后给这个类实例化一个新对象

```java
public static final CreativeTabs TUTOR_MOD_TABS = new CreativeTabs();
```

这时候IDEA会报错,通过报错我们可以得知`CreativeTabs`类是抽象类.于是我们要利用内部匿名类的方法来实现它的抽象方法.

```java
public static final CreativeTabs TEST_MOD_TABS = new CreativeTabs() {
    @Override
    public ItemStack createIcon() {
        return null;
    }
};
```

通过这个方法名我们可以猜测的出该方法设定的是物品栏的图标.将它改成我们想当作图标的物品.

```java
public static final CreativeTabs TUTOR_MOD_TABS = new CreativeTabs() {
    @Override
    public ItemStack createIcon() {
        return new ItemStack(ItemRegistryHandler.ITEM_ORE_COPPER);
    }
};
```

但这时候依旧有报错,因为我们缺少了形参.而`CreativeTabs`类中的两个构造函数都是带参的,因此IDEA不知道我们要用那个构造函数.\
通过查阅该类可以得知有两个构造函数,它们分别是:

1. `public CreativeTabs(String label)`
2. `public CreativeTabs(int index, String label)`

第二个构造函数的区别在于多了一个形参用来手动设定该物品栏的排序,但我们没有必要.因为第一个构造函数中调用了一个`getNextID()`方法用来获得最后一个位置,然后自己又调用了一次第二个构造函数.

```java
public CreativeTabs(String label)
{
    this(getNextID(), label);
}
```

```java
public static int getNextID()
{
    return CREATIVE_TAB_ARRAY.length;
}
```

我们需要传入形参到label,可以推测得出是物品栏的id.因为我们只需要一个物品栏,直接用ModID吧.

```java
public static final CreativeTabs TUTOR_MOD_TABS = new CreativeTabs(TestMod.MOD_ID) {
    @Override
    public ItemStack createIcon() {
        return new ItemStack(ItemRegistryHandler.ITEM_ORE_COPPER);
    }
};
```

## 应用物品栏

现在回到物品类和方块类中应用一下.\
只需要在构造函数中加上`this.setCreativeTab(CreativeTabRegistry.TEST_MOD_TAB);`即可\
比如

```java
public class ItemIngotCopper extends Item {
    public ItemIngotCopper(){
        this.setRegistryName("ingot_copper");
        this.setTranslationKey(TestMod.MOD_ID + ".ingot_copper");
        this.setCreativeTab(CreativeTabRegistry.TEST_MOD_TAB);
    }
}
```

## 本地化

物品栏的本地化Key是:\
itemGroup.物品栏label=TAB NAME\
我们将label设定成和ModID一样.因此是\
itemGroup.testmod=Test Mod

## 测试

![](<../../assets/image (51).png>)



## 自定义物品栏

通过翻阅`CreativeTabs`类可以发现这个类里自带了两个给开发者自定义用的方法

1. `setBackgroundImageName(String texture)`
2. `hasSearchBar()`

### setBackgroundImageName()

把要当作背景的png名字当作参数传入即可,比如tab\_testmod.png.然后将材质放到\
`assets/minecraft/textures/gui/container/creative_inventory/`\
这个目录下即可.这里为了方便直接用Minecraft自带的材质`item_search.png`

### hasSearchBar()

添加一个小号搜索框.没啥好说明的

![](<../../assets/image (39).png>)

## 整体代码

```java
package cmmmmmm.testmod.item;

import cmmmmmm.testmod.TestMod;
import net.minecraft.creativetab.CreativeTabs;
import net.minecraft.item.ItemStack;

public class CreativeTabRegistry {
    public static final CreativeTabs TEST_MOD_TAB = new CreativeTabs(TestMod.MOD_ID) {

        @Override
        public ItemStack createIcon() {
            return new ItemStack(ItemRegistryHandler.ITEM_ORE_COPPER);
        }

        @Override
        public boolean hasSearchBar() {
            return true;
        }
    }.setBackgroundImageName("item_search.png");
}
```

