# Celeste Bounce Block Explanation<br>
*(aka core block, magma block, lava block)*<br>
Did some code reading while working on 8B and 8C TAS, and want to document this before I forget everything<br>
## State 1: Waiting<br>
* Check if player is interacting with block, i.e. on top of the block with y speed >=0 or grabbing the block. If so, set `MoveSpeed` to 80 (px/f) and enter WindingUp state.<br>
## State 2: WindingUp<br>
* First, `BounceDirection` is calculated by the center position of the block and center position of player. Here the game uses integer pixel positions.<br>
<img src="https://github.com/lnf-24/BounceBlockExplanation/blob/main/Images/1.png" height="118" ><br>
* Second, from `StartPosition` of the block, move 10 px in the opposite direction of `BounceDirection` to calculate `TargetPosition` (with subpixel) of windingup state.<br>
<img src="https://github.com/lnf-24/BounceBlockExplanation/blob/main/Images/2.png" height="232" ><br>
* The block moves from `ExactPosition` (with subpixel) to `TargetPosition` by `MoveSpeed`. `MoveSpeed` decreases by 10 per frame until it reaches 40.<br>
<img src="https://github.com/lnf-24/BounceBlockExplanation/blob/main/Images/3.png" height="179" ><br>
`BounceDirection` and `TargetPosition` is recalculated every frame, unless player has left the block.<br>
* The liftboost of the block is `MoveSpeed` (in opposite direction) x component multiplied by 0.75.<br>
<img src="https://github.com/lnf-24/BounceBlockExplanation/blob/main/Images/4.png" height="131" ><br>
* If the distance between `ExactPosition` and `TargetPosition` <= 1.414, set `MoveSpeed` to 0 and enter Bouncing state. <br>
* All in one picture<br>
<img src="https://github.com/lnf-24/BounceBlockExplanation/blob/main/Images/5.png" height="279" ><br>
## State 3: Bouncing<br>
<img src="https://github.com/lnf-24/BounceBlockExplanation/blob/main/Images/6.png" height="310" ><br>
* This state uses the `BounceDirection` of the last frame in WindingUp state, doesn't recalculate anymore.<br>
* From `StartPosition` of the block, move 24 px in `BounceDirection` to get `TargetPosition`.<br>
* The block moves from `ExactPosition` to `TargetPosition` by `min(3 * MoveSpeed, 200)`. `MoveSpeed` increases by 13.33 per frame until it reaches 140.<br>
* The liftboost of the block is `min(3 * MoveSpeed, 200)` x component multiplied by 0.75.<br>
* If player leaves the block, or `ExactPosition` is same as `TargetPosition`, enter BounceEnd state.
* If player is still on the block, the block cancles dash, sets player’s speed to liftboost, and gives coyote time.<br>
## State 4: BounceEnd<br>
* The block stays in place for 2 frames then breaks.<br>
## State 5: Broken<br>
* Respawn after 96 frames and back to Waiting state.<br>
## Some Notes<br>
* In windingup state, since `BounceDirection` and `TargetPosition` is recalculated every frame, if player moves on the block, the movement of the block will also change. Generally you don't want to move too much in windingup state, or this state could become frames longer.<br>
* If player crouches on the block, her center position would be lower, so `BounceDirection` changes, which affects the block's movement.<br>
* For horizontal boosts, the last frame of WindingUp state decides the final liftboost, so you want to manip player's position on that frame. If you are on the top, you can crouch to make the liftboost direction a bit more close to horizontal.<br>
* In Bouncing state, walking or climbing no longer affects the block so you can move as you want.<br>
* Liftbooost isn't in the direction from block’s center to player’s center.<br>
* If the block didn't moves 1 integer px vertically in a frame, the vertical boost on that frame would be zero. This has something to do with the way the game calculates liftboost, and could be annoying when you are grabbing the side of a bounce block.<br>
* In the coyote time after bouncing state you can do a core hyper/super, or just jump to get 40 + 2 liftboost.<br>
* In latest CelesteTAS version you can use Ctrl + Click on the BounceBlock or customize info hud to show its informations, such as `state`, `bounceDir`, `moveSpeed`, etc.<br>
