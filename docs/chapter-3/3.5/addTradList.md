如果原版提供的交易模板无法覆盖你的要求, 也可以自己去实现写个模板出来.
实现方法很简单, 实现接口`ITradeList`, 然后在实现的方法内调用`recipeList.add(new MerchantRecipe(...))`
```java
public class ModTradeList{  
    public static class CustomItemStackAndEmeralToItemStack implements EntityVillager.ITradeList {  
        public final Item buyingItem;  
        public final EntityVillager.PriceInfo buyingPrice;  
        public final EntityVillager.PriceInfo emeraldPrice;  
        public final Item sellingItem;  
        public final EntityVillager.PriceInfo sellingPrice;  
  
        public CustomItemStackAndEmeralToItemStack(Item buyingItem, EntityVillager.PriceInfo itemPrice, EntityVillager.PriceInfo emeraldPrice, Item sellingItem, EntityVillager.PriceInfo sellingPrice){  
            this.buyingItem = buyingItem;  
            this.buyingPrice = itemPrice;  
            this.emeraldPrice = emeraldPrice;  
  
            this.sellingItem = sellingItem;  
            this.sellingPrice = sellingPrice;  
        }  
        @Override  
        public void addMerchantRecipe(IMerchant merchant, MerchantRecipeList recipeList, Random random) {  
            int i = this.buyingPrice.getPrice(random);  
            int j = this.emeraldPrice.getPrice(random);  
            int k = this.sellingPrice.getPrice(random);  
  
            recipeList.add(new MerchantRecipe(  
                    new ItemStack(this.buyingItem, i,0),  
                    new ItemStack(Items.EMERALD, j,0),  
                    new ItemStack(this.sellingItem, k, 0)));  
        }    
    }
}
```