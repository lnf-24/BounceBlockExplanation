# Celeste Bounce Block Explanation<br>
*(aka core block, magma block, lava block)*<br>
Did some code reading when I worked on 8B and 8C TAS, and want to document this before I forget everything.<br>
## State 1: Waiting<br>
* Check if player is interacting with block, i.e. on top of the block with y speed >=0 or grabbing the block. If so, set `MoveSpeed` to 80 (px/f) and enter WindingUp state.<br>
## State 2: WindingUp<br>
* First, `BounceDirection` is calculated by the center position of the block and center position of player. These centers are based on integer pixel positions. (If you crouch, the direction changes)<br>
<img src="https://github.com/lnf-24/BounceBlockExplanation/blob/main/Images/1.png" height="118" ><br>
* From `StartPosition` of the block, move 10 px in the opposite direction of `BounceDirection`, to get `TargetPosition` (with subpixel) of this state.<br>
<img src="https://github.com/lnf-24/BounceBlockExplanation/blob/main/Images/2.png" height="232" ><br>
* The block will move from `ExactPosition` (with subpixel) to `TargetPosition` by `MoveSpeed`. `MoveSpeed` decreases by 10 per frame until it reaches 40.<br>
<img src="https://github.com/lnf-24/BounceBlockExplanation/blob/main/Images/3.png" height="179" ><br>
`BounceDirection` and `TargetPosition` is recalculated every frame, unless player has left the block. So if player moves on the block, the movement of the block also changes.<br>
* The liftboost of the block is `MoveSpeed` in opposite direction, x component multiplied by 0.75. (not the direction from block’s center to player’s center)<br>
<img src="https://github.com/lnf-24/BounceBlockExplanation/blob/main/Images/4.png" height="131" ><br>
* If the distance between `ExactPosition` and `TargetPosition` <= 1.414, set `MoveSpeed` to 0 and enter Bouncing state. (If you move too much on the block, this state would be longer)<br>
* All in one picture<br>
<img src="https://github.com/lnf-24/BounceBlockExplanation/blob/main/Images/5.png" height="279" ><br>
## State 3: Bouncing<br>
<img src="https://github.com/lnf-24/BounceBlockExplanation/blob/main/Images/6.png" height="310" ><br>
* Keep the `BounceDirection` of the last frame in WindingUp state and won’t recalculate anymore.<br>
* From `StartPosition` of the block, move 24 px in `BounceDirection` to get `TargetPosition`. The block will move from `ExactPosition` to `TargetPosition` by `min(3 * MoveSpeed, 200)`. `MoveSpeed` increases by 13.33 per frame until it reaches 140.<br>
* The liftboost of the block is `min(3 * MoveSpeed, 200)`, x component multiplied by 0.75.<br>
* If player leaves the block, or `ExactPosition` is same as `TargetPosition`, enter BounceEnd state. If player is still on the block, the block cancles dash, sets player’s speed to liftboost, and gives coyote time. (In this coyote time you can core hyper/super or jump to get second boost)<br>
## State 4: BounceEnd<br>
* The block stays in place for 2 frames then breaks.<br>
## State 5: Broken<br>
* Respawn after 96 frames and back to Waiting state.<br>
## Some Notes<br>
* In CelesteTAS 3.3.0 you can customize info hud to show information of bounce blocks. However, current version only shows information of first bounce block on screen. Here are some examples:<br>
```
state: {BounceBlock.state}
ExactPosition: {BounceBlock.ExactPosition}
bounceDir: {BounceBlock.bounceDir}
moveSpeed: {BounceBlock.moveSpeed}
playerCenter: {Player.Center}
Center: {BounceBlock.Center}
startPos: {BounceBlock.startPos}
```
* Generally you don't want to walk or climb too much in WindingUp state, or the state could become frames longer. In Bouncing state, walking or climbing no longer affect the block.<br>
* For horizontal boosts, the last frame of WindingUp state determines the bouncing back direction, and final liftboost. So the position on that frame is what you want to manip. If you are on the top, you can crouch to make the direction more horizontal.<br>
* If the block didn't push you 1 px vertically in a frame, the vertical boost on that frame would be zero. This could be annoying when you are grabbing the side of a bounce block.<br>
* When the block throws player off, it *sets* your speed to liftboost, not adding liftboost to the speed. So we can't to use it to cancle dash to preserve dash speed.<br>
* When doing core demohyper/super, you can extend your dash in coyote time, because coyote jump cancles horizontal dashes into hyper/super.<br>
