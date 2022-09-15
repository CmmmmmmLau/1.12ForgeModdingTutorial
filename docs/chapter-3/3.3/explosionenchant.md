和上一章没啥太大区别, 略过一部分
```java
public class EnchantmentExplosion extends Enchantment {  
    protected EnchantmentExplosion() {  
        super(Rarity.RARE, EnumEnchantmentType.WEAPON, new EntityEquipmentSlot[]{EntityEquipmentSlot.MAINHAND});  
        setName(Testmod.MOD_ID + ".explosion");  
        setRegistryName("explosion");  
  
        ModEnchantmentList.enchantments.add(this);  
            }  
  
    @Override  
    public int getMinEnchantability(int enchantmentLevel) {  
        return 20 * enchantmentLevel;  
    }  
    @Override  
    public int getMaxEnchantability(int enchantmentLevel) {  
        return  this.getMinEnchantability(enchantmentLevel) + 10;  
    }
}
```

## 效果实现
```java
@Mod.EventBusSubscriber(modid = Testmod.MOD_ID)  
public class ModEnchantmentEvent {  
    @SubscribeEvent  
    public static void onEnchantmentExplosion(LivingDeathEvent event){  
        Entity source = event.getSource().getImmediateSource();  
        EntityLivingBase entityLivingBase = (EntityLivingBase) source;  
        if (source instanceof EntityLivingBase && !source.world.isRemote){  
            ItemStack MainHandItem = entityLivingBase.getHeldItemMainhand();  
            int i = EnchantmentHelper.getEnchantmentLevel(ModEnchantmentList.EXPLOSION_ATTACK, MainHandItem);  
            if (i > 0) {  
                Entity target = event.getEntity();  
                target.world.createExplosion(null, target.posX, target.posY, target.posZ, 1, true);  
            }        
        }    
    }  
}
```
因为具体实现是要生物死亡后才能触发, 因此没办法通过重写`onEntityDamaged`来实现. 因为这个方法是每次攻击后触发.