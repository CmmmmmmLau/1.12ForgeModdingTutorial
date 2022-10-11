吸血攻击附魔的实现代码.
```java
@SubscribeEvent  
public static void onEnchantmentLifeStealUpdate(LivingEvent.LivingUpdateEvent event){  
    Entity entity = event.getEntity();  
    if (entity instanceof EntityLivingBase) {  
        EntityLivingBase entityLivingBase = (EntityLivingBase) entity;  
        if (!entity.world.isRemote) {  
            ItemStack MainHandItem = entityLivingBase.getHeldItemMainhand();  
            int level = EnchantmentHelper.getEnchantmentLevel(ModEnchantmentList.LIFESTEAL_ATTACK, MainHandItem);  
            if (level > 0) {  
                CapabilityLifeSteal capabilityLifeSteal = entityLivingBase.getCapability(ModCapability.LIFE_STEAL, null);  
                int timer = capabilityLifeSteal.getTimer();  
                if (timer > 0) {  
                    capabilityLifeSteal.setTimer(timer - 1);  
                }                entityLivingBase.sendMessage(new TextComponentString(String.valueOf(timer)));  
            }        }  
    }
}  
  
  
@SubscribeEvent  
public static void onEnchantmentLifeStealAttack(LivingHurtEvent event){  
    Entity source = event.getSource().getImmediateSource();  
    if (source instanceof EntityLivingBase) {  
        EntityLivingBase entityLivingBase = (EntityLivingBase) source;  
        if (!source.world.isRemote) {  
            ItemStack MainHandItem = entityLivingBase.getHeldItemMainhand();  
            int level = EnchantmentHelper.getEnchantmentLevel(ModEnchantmentList.LIFESTEAL_ATTACK, MainHandItem);  
            if (level > 0) {  
                CapabilityLifeSteal capabilityLifeSteal = entityLivingBase.getCapability(ModCapability.LIFE_STEAL, null);  
                int timer = capabilityLifeSteal.getTimer();  
                if (timer == 0) {  
                    int healAmount = (int) (level > 1 ? event.getAmount() : event.getAmount() / 2);  
                    entityLivingBase.setHealth(entityLivingBase.getHealth() + healAmount);  
                    capabilityLifeSteal.resetTimer(level);  
                }            
            }        
        }    
    }
}
```