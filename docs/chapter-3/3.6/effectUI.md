如果你翻看过`Potion`类里原版的代码就会发现原版也附带了图标渲染相关的方法. 但如果重写它的话会发现没有作用. 因为原版代码里能使用的纹理是写死的, 因此无法更改. 但Forge也给出了对应的方法来解决, 只要重写即可.

```java
public static final ResourceLocation POTION_TEXTURE = new ResourceLocation(Testmod.MOD_ID + ":textures/gui/potion.png");

@Override  
public boolean hasStatusIcon() {  
    return false;
    //这个方法是原版自带的, 但是如果我们重写改成返回false
    //forge便会随即调用下方的两个方法  
}  
  
@Override  
public void renderInventoryEffect(PotionEffect effect, Gui gui, int x, int y, float z) {  
    Minecraft.getMinecraft().getTextureManager().bindTexture(POTION_TEXTURE);  
    gui.drawTexturedModalRect(x + 6, y + 7, 0, 0, 18, 18);
    //打开背包时左侧的状态图标渲染  
}  
  
@Override  
public void renderHUDEffect(PotionEffect effect, Gui gui, int x, int y, float z, float alpha) {  
    Minecraft.getMinecraft().getTextureManager().bindTexture(POTION_TEXTURE);  
    gui.drawTexturedModalRect(x + 3, y + 3, 0, 0, 18, 18);  
    //玩家右上角的状态图标渲染
```
其中`drawTexturedModalRect`方法是绘制GUI时常用的方法. \
其中头两个参数是以屏幕左上角为原点的坐标. 这时候我们直接传入形参里的x和y便会将坐标自动对齐到状态效果背景框的左上角.\
第二,三个参数则是我们材质起始点的坐标, 单位是像素点.\
最后两个参数则为所要框选的材质大小.\
默认的材质画布大小是256 * 256像素. 如果你觉得不需要这么大的, 也有另一个方法可以自定义整个纹理大小.
```java
@Override  
public void renderInventoryEffect(PotionEffect effect, Gui gui, int x, int y, float z) {  
    Minecraft.getMinecraft().getTextureManager().bindTexture(POTION_TEXTURE);  
    //gui.drawTexturedModalRect(x + 6, y + 7, 0, 0, 18, 18);  
    Gui.drawModalRectWithCustomSizedTexture(x + 6, y + 7, 0, 0, 18, 18, 256, 18);  
}  
  
@Override  
public void renderHUDEffect(PotionEffect effect, Gui gui, int x, int y, float z, float alpha) {  
    Minecraft.getMinecraft().getTextureManager().bindTexture(POTION_TEXTURE);  
    //gui.drawTexturedModalRect(x + 3, y + 3, 0, 0, 18, 18);  
    Gui.drawModalRectWithCustomSizedTexture(x + 3, y + 3, 0, 0, 18, 18, 256, 18);  
}
```
最后两个参数则为锁使用纹理的宽度和高度.

## 动态图标
因为图标的渲染是每tick都会调用, 而形参中也有传入`PotionEffect`的实例. 因此可以用来获取状态效果的持续时间, 并用来更新图标.
```java
@Override  
public void renderInventoryEffect(PotionEffect effect, Gui gui, int x, int y, float z) {  
    Minecraft.getMinecraft().getTextureManager().bindTexture(POTION_TEXTURE);  
    int duration = effect.getDuration();  
    int index = (duration % 20) / 2;  
    Gui.drawModalRectWithCustomSizedTexture(x + 6, y + 7, index * 18, 0, 18, 18, 256, 18);  
}  
  
@Override  
public void renderHUDEffect(PotionEffect effect, Gui gui, int x, int y, float z, float alpha) {  
    Minecraft.getMinecraft().getTextureManager().bindTexture(POTION_TEXTURE);  
    int duration = effect.getDuration();  
    int index = (duration % 20) / 2;  
    Gui.drawModalRectWithCustomSizedTexture(x + 3, y + 3, index * 18, 0, 18, 18, 256, 18);  
}
```

## 已被废弃的方法
在旧的Forge 1.12教程里, 使用的是已经被Forge标注废弃的方法.(可能还能用, 但也没测试过. 用起来也麻烦)
```java
@Override  
public boolean hasStatusIcon() { 
	Minecraft.getMinecraft().getTextureManager().bindTexture(POTION_TEXTURE);
    return true;  
}
```
在原版逻辑里状态效果图标和背包共享的是同一张纹理图, 状态效果图标都放在背包材质的下方.
![](../../assets/Pasted%20image%2020220920141038.png)

因此使用该方法实现效果的话所使用的材质也必须要准备一张一模一样大小的纹理图, 并将上方的位置空出. 只有下方区域能够使用.





