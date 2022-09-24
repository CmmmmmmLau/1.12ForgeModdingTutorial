动画制作方面如果不介意使用第三方库或者你想要实现比较复杂的动画则推荐使用`GeckoLib`:https://www.curseforge.com/minecraft/mc-mods/geckolib

如果想要实现的动画效果比较简单, 那么原版也提供了接口.
```java
@Override  
public void setRotationAngles(float limbSwing, float limbSwingAmount, float ageInTicks, float netHeadYaw, float headPitch, float scaleFactor, Entity entityIn)  
{  
}
```
其中倒数第一个参数为被渲染的实体, 倒数第二个参数作用未知(查到的说法是长方体相对实体的体积大小). 

而`ageInTicks`参数可以理解为一种模拟时间流逝的数字, 因为模型的渲染也是每tick调用. 因此这个传入的参数数字也会不断变大. 可以配合一些数学函数实现一些循环摆动效果等.

## 头部转动
`headPitch`代表的是头部相对水平的俯仰角. `netHeadYaw`则是水平方向的左右旋转角度. 但不知道什么原因使用这两个参数首先得要将其转换为弧度单位.
```java
@Override  
public void setRotationAngles(float limbSwing, float limbSwingAmount, float ageInTicks, float netHeadYaw, float headPitch, float scaleFactor, Entity entityIn)  
{  
    this.head.rotateAngleX = (float) (Math.PI / 180) * headPitch;  
    this.head.rotateAngleY = (float) (Math.PI / 180) * netHeadYaw;   
}
```

## 手臂摆动
先前提到过`ageInTicks`是一种模拟时间流动的数字, 那么我们可以配合余弦函数实现手臂来回摆动:
$$
A * cos(\omega * \text{ageInTicks} + \varphi)
$$

但如果这时候直接进入游戏就会发现不管什么状态四肢都会在疯狂摆动, 这时候就需要使用所提供的"开关"参数`limbSwingAmount`:
$$
A * cos(\omega * \text{ageInTicks} + \varphi) * \text{limbSwingAmount}
$$
系统会根据实体的运动状态传入适当的数字. 当实体停止运动的时候就会传入0, 这样四肢就会归位. 而不是停在当前运动位置. \
虽然这时候手臂的摆动已经实现了, 但实际上摆动效果稍微有点不自然. 因为当实体停止运动之后, 四肢会有稍微的来回再摆动一会才停下. \
但是原版却提供了一个现成的摆动参数`limbSwing`. 这个参数解决了实体停止运动后四肢还会来回摆动的问题, 直接套入余弦函数即可.
```java
@Override  
public void setRotationAngles(float limbSwing, float limbSwingAmount, float ageInTicks, float netHeadYaw, float headPitch, float scaleFactor, Entity entityIn)  
{  
    this.head.rotateAngleX = (float) (Math.PI / 180) * headPitch;  
    this.head.rotateAngleY = (float) (Math.PI / 180) * netHeadYaw;  
    this.right_leg.rotateAngleX = MathHelper.cos(limbSwing * 0.6662F) * limbSwingAmount * 1.25F;  
    this.left_leg.rotateAngleX = MathHelper.cos(limbSwing * 0.6662F + (float) Math.PI) * limbSwingAmount * 1.25F;  
}
```

## 另一个方法
原版之中还提供了另一个只在签名上些许不同的方法, 推测作用是用来做动画分类用.
```java
@Override  
public void setLivingAnimations(EntityLivingBase entitylivingbaseIn, float limbSwing, float limbSwingAmount, float partialTickTime) {  
	//注意这里partialTickTime传入的是分数刻. 即x/20
}
```