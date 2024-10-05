# クライアントごとにエンティティの表示方法を切り替える方法
動作確認: **v1.21.30**

## 仕組み
- ScriptAPIで表示を切り替えるエンティティに\<Entity>.playAnimation()を実行。
- \<Entity>.playAnimation()のoptionsで再生中のアニメーションを表示するプレイヤーを指定。


## 作成方法
リンゴをメインハンドに持ったプレイヤーだけ防具立てが見えなくなるサンプルを作成します。

1. **アニメーションを作成**  
   リソースのanimationフォルダに防具立てを非表示にするアニメーションを作成します。
   ```json
    {
        "format_version": "1.21.0",
        "animations": {
            "animation.armor_stand.hide": {
            "loop": true,
            "bones": {
                "baseplate": {
                    "position": [0, -160000, 0],
                    "scale": 0
                }
            }
        }
    }
   ```

2. **ScriptAPIで処理**  
   防具立てを取得して先ほど作成したアニメーションを再生させます。このときに再生しているアニメーションを表示するプレイヤーを指定してあげる。
   ```javascript
   import { world, system } from "@minecraft/server";
   
   system.runInterval(() => {
       const players = world.getPlayers();

       //りんごを持っているプレイヤーのネームタグを取得
       const targets = players.filter(p => { 
         const itemStack = p.getComponent("inventory").container.getItem(p.selectedSlotIndex);
         if(itemStack?.typeId == "minecraft:apple")return p;
       });

       //防具立てを取得
       for(const entity of world.getDimension("overworld").getEntities({ type:"armor_stand" })){
           //作成したアニメーションを再生
           entity.playAnimation("animation.armor_stand.hide", {
               blendOutTime: 0.25, //0.5~0.0がおすすめ アニメーションによって変わるかも
               players: targets.map(p => { return p.name; }) //アニメーションを再生するプレイヤーを指定
           });
       };
   });
   ```

3. 完成
   ![img1]
