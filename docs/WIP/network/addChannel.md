一个基本的通讯类包含一下四个基础部分:
- 给你的信道命名的字符串
- 用这个字符串分配一个信道
- 往这个信道发送信息的方法
- 当客户端从信道收到信息后的处理方法

## 分配信道

```java
public class Power {  
    private static final String NAME = "DIRTBALLPOWER";  
    public static final FMLEventChannel CHANNEL = NetworkRegistry.INSTANCE.newEventDrivenChannel(NAME);  
}
```

## 发送信息
```java
public static void sendClientPacket(EntityPlayer player) {  
    PacketBuffer buffer = new PacketBuffer(Unpooled.buffer());  
    CapabilityDirtPower power = player.getCapability(ModCapability.DIRT_BALL_POWER, null);  
  
    buffer.writeFloat(power.getBluePower());  
    buffer.writeFloat(power.getGreenPower());  
    buffer.writeFloat(power.getOrangePower());  
  
    CHANNEL.sendTo(new FMLProxyPacket(buffer, NAME), (EntityPlayerMP) player);  
}
```

## 接受信息
信息的接受并处理由Forge的事件系统自动触发
```java
@SubscribeEvent  
@SideOnly(Side.CLIENT)  
public static void onClientCustomPacket(FMLNetworkEvent.ClientCustomPacketEvent event) {  
    ByteBuf buf = event.getPacket().payload();  
    float blue = buf.readFloat(), green = buf.readFloat(), orange = buf.readFloat();  
  
    Minecraft minecraft = Minecraft.getMinecraft();  
    minecraft.addScheduledTask(() -> {  
        EntityPlayer entityPlayer = minecraft.player;  
        CapabilityDirtPower power = entityPlayer.getCapability(ModCapability.DIRT_BALL_POWER, null);  
  
        power.setBluePower(blue);  
        power.setGreenPower(green);  
        power.setOrangePower(orange);  
    });
}
```