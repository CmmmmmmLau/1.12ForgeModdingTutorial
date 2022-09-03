# 一定要用内部匿名类?

使用内部匿名类来加入创造分类的好处是实际上这段代码

```java
    public static final CreativeTabs TEST_MOD_TABS = new CreativeTabs(Testmod.MOD_ID) {
        @Override
        @SideOnly(Side.CLIENT)
        public ItemStack createIcon() {
            return new ItemStack(ItemRegistryHandler.INGOT_COPPER);
        }
    };
```

你可以把它放在任何地方,而不用单独新建一个类来处理. 比如你可以分别放在`ItemRegistryHandler` 和`BlockRegistryHandler` 里, 然后进一步分类成`TEST_MOD_TABS_ITEM` 和`TEST_MOD_TABS_`BLOCK .

如果你依旧想要开新类来处理并不想使用内部匿名类. 那么只要继承`CreativeTabs` 类,并实现抽象方法和构造函数即可.

```java
public class CreativeTabRegistry extends CreativeTabs{

    public static final CreativeTabRegistry TEST_MOD_TABS = new CreativeTabRegistry();

    public CreativeTabRegistry() {
        super(Testmod.MOD_ID);
    }

    @Override
    @SideOnly(Side.CLIENT)
    public ItemStack createIcon() {
        return new ItemStack(ItemRegistryHandler.INGOT_COPPER);
    }
}
```

