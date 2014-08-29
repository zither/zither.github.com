{
    "type": "article",
    "title": "尘埃开发记录之战斗数据结构",
    "url": "data-structure-of-battle",
    "tag": "Game",
    "category": "Dust",
    "keywords": null,
    "date": "2014-08-29",
    "time": "15:51:02",
    "message": true,
    "publish": true
}

前面的日志中已经说过，尘埃现在采用的是自动回合制的战斗模式，玩家在战斗中不需要手动干涉。由于网页游戏的特性，尘埃的所有战斗过程全部在服务端完成，玩家是无法看到整个过程的。这就意味着需要在服务端生成一个完整的战斗记录，玩家要看到战斗过程就必须将记录传递到前端的战斗记录播放器中进行回放。

回合制大大的简化了战斗模式，同时也简化的战斗记录数据的结构。尘埃暂时还没有考虑各种复杂的装备和技能特效，在 Demo 中直接采用了最简单的处理方法，将战斗记录分为两部分：战斗成员（包括攻击和防御两方）以及回合数据。战斗成员就是参战双方的详细属性，比如名称、血量等，而回合数据是简化后的操作步骤。

```
0: {status:{attacker:[{id:2, name:勇敢的小鬼, hp:30, attack:3, defence:2,…}],…}}
1: {type:battle, data:[attacker, 0, 0, 0, 3, 7]}
2: {type:battle, data:[defender, 0, 0, 0, 1, 29]}
3: {type:battle, data:[attacker, 0, 0, 0, 3, 4]}
4: {type:battle, data:[defender, 0, 0, 0, 1, 28]}
5: {type:battle, data:[attacker, 0, 0, 0, 3, 1]}
6: {type:battle, data:[defender, 0, 0, 0, 1, 27]}
7: {type:battle, data:[attacker, 0, 0, 0, 3, -2]}
8: {type:message, data:勇敢的小鬼打败了老鼠。}
```

上面是一个简单的战斗记录示例，第 0 行是参战成员的详细信息，其中分为攻击方和防御方两个部分。第 1-7 行就是整个战斗的操作数据。整个战斗记录的回放过程可以点击图片查看：[战斗记录](http://ww2.sinaimg.cn/large/86e701f8gw1ejtkyk2521j20za0hm0v9.jpg)。

战斗记录的回放基本都在前端进行控制，目前尘埃 Demo 的战斗回放采用的是 2 秒一个回合，当然如果有需要的话也可以把整个过程直接打印出来。不过随着之后各种战斗因素的加入，这个过程肯定会变得复杂起来。 
