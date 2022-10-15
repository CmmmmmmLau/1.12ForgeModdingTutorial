- 目录

	- [Minecraft 1.12 Forge Mod开发学习笔记](README.md)
	- [返回Cm's Blog](http://cmmmmmm.com/)

---

- 开发前的准备
	- [1 配置MinecraftForge开发环境](README.md)
		- [1.1 手动配置](chapter-1/1.1/1.1.1.md)
		- [1.2 使用插件](chapter-1/1.1/1.1.2.md)
		- [1.3 更改编码](chapter-1/1.1/1.2.md)
	- [2 了解运行机制](chapter-1/1.3/README.md)
		- [3.1 加载阶段](chapter-1/1.3/1.3.1.md)
		- [3.2 注册系统](chapter-1/1.3/1.3.2.md)
		- [3.3 事件系统](chapter-1/1.3/1.3.3.md)
		- [3.4 能力系统](chapter-1/1.3/1.3.4.md)
		- [3.5 代理系统](chapter-1/1.3/1.3.5.md)
		- [3.6 游戏主循环](chapter-1/1.3/1.3.6.md)
	- [3 解析主类](chapter-1/1.4.md)

---

- 小试牛刀
	- [4 第一个物品](chapter-2/2.1.md)
	- [5 第一个方块](chapter-2/2.2.md)
	- [6 自定义创造模式物品栏](chapter-2/2.3/README.md)
		- [一定要用内部匿名类?](chapter-2/2.3/Anonymous%20Inner%20Class.md)
	- [7 建立代理系统](chapter-2/2.4.md)
	- [8 高效化开发](chapter-2/2.5/README.md)
		- [8.1 目录结构优化](chapter-2/2.5/2.5.1.md)
		- [8.2 物品注册优化](chapter-2/2.5/2.5.2.md)
		- [8.3 方块注册优化](chapter-2/2.5/2.5.3.md)
		- [8.4 模型注册优化](chapter-2/2.5/2.5.4.md)
	- 9 第一套装备
		- [9.1 第一套工具](chapter-2/2.6/2.6.1%20first%20tools.md) 
		- [9.2 第一套防具](chapter-2/2.6/2.6.2%20first%20armor.md)
	- [?. 救救我,我的物品材质永远都是紫黑块](chapter-2/2.Missing%20Texture.md)

---

- 更加深入
	- 10 自定义配方
		- [10.1 自定义合成](chapter-3/3.1/3.1.1.md) 
		- [10.2 自定义烧炼配方和燃料](chapter-3/3.1/3.1.2.md)
	- 11 自定义作物和食物
		- [11.1 自定食物](chapter-3/3.2/3.2.1.md)
		- [11.2 自定作物](chapter-3/3.2/3.2.2.md)
	- 12 自定义附魔
		- [12.1 缓速攻击附魔](chapter-3/3.3/slownessEnchant.md)
		- [12.2 爆炸攻击附魔](chapter-3/3.3/explosionenchant.md)
	- [13 自定义事件](chapter-3/3.4/callevent.md)
	- 14 自定村民和交易
		- [14.1 添加村民](chapter-3/3.5/addVillage.md)
		- [14.2 添加交易](chapter-3/3.5/addTradList.md)
	- 15 自定义状态效果和药水
		- [15.1 新状态效果](chapter-3/3.6/customEffect.md)
		- [15.2 新的药水与酿造](chapter-3/3.6/newPotionType.md)
		- [15.2 图标渲染](chapter-3/3.6/effectUI.md)
	- [16 服务端和客户端](chapter-3/3.7/side.md)

---

- 与世界的交互
	- 17 自定实体
		- [17.1 添加实体](chapter-4/entity/addEntity.md)
		- [17.2 添加模型](chapter-4/entity/addModel.md)
		- [17.3 添加动画](chapter-4/entity/customAnimation.md)
		- [17.4 添加AI](chapter-4/entity/entityAI.md)
		- [17.5 属性和修饰符](chapter-4/entity/attribute.md)
		- [17.6 数据管理器](chapter-4/entity/dataManager.md)
		- [17.7 添加生成](chapter-4/entity/spawn.md)
	- [18 能力系统](chapter-4/capability/README.md)
		- [18.1 新能力](chapter-4/capability/addCap.md)
		- [18.2 注册能力](chapter-4/capability/registryCap.md)
		- [18.3 能力提供者](chapter-4/capability/capProvider.md)
		- [18.4 附魔的实现](chapter-4/capability/enchImp.md)
	-  19 实体方块
		- 基础
		- 初级
		- 中级
		- 高级

---
- TODO
	- 模型文件JSON
	- NBT
	- Loot Table
	- TileEntity - Basic
	- TileEntity - Chest
	- TileEntity - Machine Block
	- TileEntity - Energy