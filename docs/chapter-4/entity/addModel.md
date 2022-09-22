建模部分推荐使用第三方软件进行. 有付费的`Cubik Studio`和免费的`Blockbench`.
但是`Cubik Studio`的风评似乎很糟糕.
关于`Blockbench`的教程各种视频网站一搜就有, 而且有插件支持.\
纯手写代码建模虽然可行但是需要一定的想象力, 并且测试排错起来非常麻烦.\
如果自己想要的模型结构简单可以试一试.

## 匠人精神
虽然说推荐用第三方软件建模, 但还是不能缺少对代码层面的了解. 毕竟当涉及到动画方面的制作的时候不可避免的还是要去看代码手动写代码. 虽然有第三方的动画制作方法但现在不展开讲

### 模型类
每个实体的模型都需要单独的一个类进行负责, 并且该类要继承`ModelBase`
```java
import net.minecraft.client.model.ModelBase;  
  
public class ModelCopperGolem extends ModelBase {  
    public ModelCopperGolem() {  
        //...  
    }  
}
```

### 设定纹理图大小并绑定材质
纹理图默认大小是64 * 32. 但是我们也可以更改成自己想要的大小.
```java
public ModelCopperGolem() {  
    textureWidth = 128;  
    textureHeight = 128;

	this.body = new ModelRenderer(this,0 ,0);  
	this.head = new ModelRenderer(this, 16, 0);  
	this.right_leg = new ModelRenderer(this, 32, 0);  
	this.left_leg = new ModelRenderer(this, 32, 0);
    //后面两个参数是设定材质框选大小 
}
```

### 添加长方体
Minecraft中所有的模型都是由各种长方体组合而成的\.\
首先就是将自己想要的生物模型按照结构进行拆分. 
```java
public ModelCopperGolem() {  
    textureWidth = 128;  
    textureHeight = 128;  
  
    this.body = new ModelRenderer(this,0 ,0);  
    this.head = new ModelRenderer(this, 16, 0);  
    this.right_leg = new ModelRenderer(this, 32, 0);  
    this.left_leg = new ModelRenderer(this, 32, 0);  
  
    this.body.addBox(2,-4, 5, 4,6,8).setRotationPoint(0,8,0);
    //addBox就是添加长方体前三个参数则为起点坐标, 后面三个则为该长方体的长宽高.
    //setRotationPoint则是旋转点
}
```

### render方法
在覆盖一下`render`方法, 此处内容通常较为固定.
```java
@Override  
public void render(Entity entityIn, float limbSwing, float limbSwingAmount, float ageInTicks, float netHeadYaw, float headPitch, float scale) {  
    this.left_leg.render(scale);  
    this.head.render(scale);  
    this.right_leg.render(scale);  
    this.body.render(scale);  
}
```

### 绑定模型和材质
下一步要告诉游戏应该渲染一个怎样的实体, 也是比较固定的一部分
```java
public class RenderCopperGolem extends RenderLiving {  
    private static final ResourceLocation COPPER_GOLEM = new ResourceLocation(Testmod.MOD_ID + ":textures/entity/copper_golem" + EntityCopperGolem.ID + ".png");  
  
    public RenderCopperGolem(RenderManager manager) {  
        super(manager, new ModelCopperGolem(), 1f);  
    }  
    @Nullable  
    @Override    
    protected ResourceLocation getEntityTexture(Entity entity) {  
        return COPPER_GOLEM;  
    }
}
```

### 注册渲染
一样, 固定的部分. 将实体类和模型类一并传入绑定即可. 记得在`ClientProxy`里调用
```java
public static void onEntityRender() {  
    RenderingRegistry.registerEntityRenderingHandler(EntityCopperGolem.class, RenderCopperGolem::new);  
}
```

```java
public void preinit(FMLPreInitializationEvent event) {  
    super.preinit(event);  
    ModelRender.onEntityRender();  
}
```