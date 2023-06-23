## 9.战利品表

---

推荐阅读：[Minecraft 原版模组入门教程 - 战利品表(作者ruhuasiyu)](https://www.mcbbs.net/plugin.php?id=link_redirect&target=https%3A%2F%2Fzhangshenxing.gitee.io%2Fvanillamodtutorial%2F%23战利品表)

### 一、事件监听

`ServerEvents`下有多个用于修改战利品表的子事件：

| 事件                             | 描述                       |
| -------------------------------- | -------------------------- |
| `ServerEvents.blockLootTables`   | 方块战利品表修改           |
| `ServerEvents.chestLootTables`   | 宝箱战利品表修改           |
| `ServerEvents.giftLootTables`    | 礼物战利品表修改[1]        |
| `ServerEvents.entityLootTables`  | 实体战利品表修改           |
| `ServerEvents.fishingLootTables` | 钓鱼战利品表修改           |
| `ServerEvents.genericLootTables` | 战利品表修改（按命名空间） |

[1]如猫、村民(村庄英雄)等。

以修改方块战利品表为例：

```
ServerEvents.blockLootTables(event =>{
    // code here
})
```

### 二、战利品表修改

KubeJS添加了一些方法来简化战利品表的修改。

这里以修改方块战利品表为例：

| 事件方法                                                   | 功能                       | 补充说明              |
| ---------------------------------------------------------- | -------------------------- | --------------------- |
| addSimpleBlock(BlockStatePredicate 方块)                   | 快捷添加掉落自身的战利品表 | 可以传入tag、方块ID等 |
| addBlock(BlockStatePredicate 方块,LootBuilder 战利品表)    | 设置战利品表               | 见下                  |
| modifyBlock(BlockStatePredicate 方块,LootBuilder 战利品表) | 修改战利品表               | 见下                  |
| addJson(命名空间 战利品表ID, JsonObject 战利品表)          | 直接添加json格式的战利品表 | -                     |

对于其他类型的战利品表修改，你只需要修改方法名，其他内容大同小异。例如实体对应的战利品表设置方法为`addEntity`，而钓鱼的则为`addFishing`，以此类推。

### 三、LootBuilder

LootBuilder添加了一系列用于修改战利品表的方法：

| 🔎方法                                                     | 功能                     | 补充说明                       |
| --------------------------------------------------------- | ------------------------ | ------------------------------ |
| addCondition(JsonObject 战利品表条件)                     | 添加战利品表条件         | -                              |
| addFunction(JsonObject 战利品表函数)                      | 添加战利品表函数         | -                              |
| addPool(LootBuilderPool 战利品池)                         | 添加战利品池             | -                              |
| randomChance(概率)                                        | 设置触发概率             | -                              |
| randomChanceWithLooting(概率, 倍率)                       | 设置抢夺附魔掉落概率     | -                              |
| survivesExplosion()                                       | 设置战利品表不受爆炸影响 | -                              |
| enchantWithLevels(NumberProvider 等级, 布尔值 是否为宝藏) | 设置附魔                 | NumberProvider[2]              |
| enchantRandomly(命名空间[] 附魔项)                        | 随机附魔                 | 随机从传入的附魔中选择进行附魔 |
| killedByPlayer()                                          | 设置由玩家击杀才掉落     | -                              |
| ...                                                       | ...                      | ...                            |

[2] NumberProvider可以接受：`{min: 最小值, max: 最大值}`、`单个数字`

注意：上述附魔效果必定生效，如需对其进行限制，你需要添加战利品表条件。

### 四、LootBuilderPool

| 🔎方法 / 属性                                              | 功能                     | 补充说明                               |
| --------------------------------------------------------- | ------------------------ | -------------------------------------- |
| addItem(Itemstack 物品, 权重, 数量)                       | 添加目标物品             | 以下各项均对之生效。权重, 数量可留空。 |
| 属性 rolls                                                | 设置抽取次数             | -                                      |
| setBinomialRolls(n,p)                                     | 设置抽取次数(二项分布)   | n为总试验次数，p为单次概率             |
| setUniformRolls(最小值, 最大值)                           | 设置抽取次数(均匀分布)   | 最小->最大机会均等                     |
| addEntry(JsonObject 项目)                                 | 添加项目                 | -                                      |
| enchantWithLevels(NumberProvider 等级, 布尔值 是否为宝藏) | 设置附魔                 | NumberProvider[2]                      |
| enchantRandomly(命名空间[] 附魔项)                        | 随机附魔                 | 随机从传入的附魔中选择进行附魔         |
| randomChanceWithLooting(概率, 倍率)                       | 设置抢夺附魔掉落概率     | -                                      |
| survivesExplosion()                                       | 设置战利品表不受爆炸影响 | -                                      |
| killedByPlayer()                                          | 设置由玩家击杀才掉落     | -                                      |
| randomChance(概率)                                        | 设置触发概率             | -                                      |
| randomChanceWithLooting(概率, 倍率)                       | 设置抢夺附魔掉落概率     | -                                      |

注意，`LootBuilder`和`LootBuilderPool`中的条件在不相符时不会生效，例如挖掘方块时游戏不会检查是否是玩家击杀。

注意：上述附魔效果必定生效，如需对其进行限制，你需要添加战利品表条件。

### 五、示例

```
ServerEvents.blockLootTables(event =>{

    // 简单方块战利品表（掉落自身）（覆盖原战利品表）
    event.addSimpleBlock("minecraft:spawner");

    // 所有带有标签的树叶被破坏时掉落自身（覆盖原战利品表）
    event.addSimpleBlock("#minecraft:leaves");

    // 修改泥土战利品表
    // 效果：在玩家使用带有标签的锹挖掘泥土时，抽取战利品池1和3；使用钻石锹挖掘时，抽取战利品池1,2和3；使用其他挖掘时，抽取战利品池3。
    event.addBlock("minecraft:dirt", loot=>{

        // 添加战利品池，注意：每个战利品池会分别抽取
        // 添加战利品池 1
        loot.addPool(pool=>{

            // 添加物品、函数、条件
            pool.addItem("minecraft:iron_nugget")
            pool.addFunction({
                "function": "minecraft:set_count",
                "count": {
                  "min": 1,
                  "max": 2
                }
              })
            pool.addCondition({
                "condition": "minecraft:match_tool",
                "predicate": {
                  "tag": "forge:tools/shovels"
                }
              })
        })

    // 添加战利品池 2
    loot.addPool(pool=>{
        // 添加物品（带权重和数量）
        pool.addItem("minecraft:flint",2,1);
        pool.addItem("minecraft:iron_ingot",1,1);
        pool.addCondition({
          "condition": "minecraft:match_tool",
          "predicate": {
            "items": [
              "minecraft:diamond_shovel"
            ]
          }
        })
        // 设置生效概率和抽取次数。
        pool.randomChance(0.5);
        pool.setBinomialRolls(3,0.3);
    })

    // 添加战利品池 3
    loot.addPool(pool=>{
      pool.addItem("minecraft:dirt");
    })

    })
})

ServerEvents.genericLootTables(event =>{

    // 你可以通过指令/loot give <玩家ID> loot wudji:kubejs_tutorial/example_loot_table 来获取此战利品表。
    event.addGeneric("wudji:kubejs_tutorial/example_loot_table",loot=>{

        // 添加战利品池
        loot.addPool(pool=>{
            
            // 设置抽取次数
            pool.rolls = 1;

            // 添加物品
            pool.addItem("minecraft:iron_axe",1,1)
            pool.addItem("minecraft:iron_pickaxe",1,1)
            pool.addItem("minecraft:iron_sword",1,1)

            // 随机附魔
            pool.enchantRandomly(["minecraft:sharpness","minecraft:unbreaking","minecraft:fortune"]);
        })
        loot.addPool(pool=>{
            pool.rolls = 3;

            // 添加物品（随机数量）
            pool.addItem("minecraft:experience_bottle",2,{min:1,max:3});
            pool.addItem("minecraft:golden_apple",1,1);
            
            // 设置概率
            pool.randomChance(0.5)
        })
    })
})
```

