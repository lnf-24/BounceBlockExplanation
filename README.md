# Celeste Bounce Block Explanation
(aka core block, magma block, lava block)
Did some code reading when I worked on 8B and 8C TAS, and want to document this before I forget everything
## State 1: Waiting
Check if player is interacting with block, i.e. standing on the block with y speed >=0 or grabbing the block. If so, set `MoveSpeed` to 80 (px/f) and enter WindingUp state.
## State 2: WindingUp
First, `BounceDirection` is calculated by the center position of the block and center position of player. These centers are based on integer pixel positions. (So if you crouch the direction changes)<br>

From `StartPosition` of the block, move 10 px in the opposite direction of `BounceDirection`, to get `TargetPosition` (with subpixel) of this state.<br>
The block will move from `ExactPosition` (with subpixel) to `TargetPosition` by `MoveSpeed`. `MoveSpeed` decreases by 10 per frame until it reaches 40.<br>
`BounceDirection` and `TargetPosition` is recalculated every frame, unless player has left the block. So if player moves on the block, the movement of the block also changes.<br>
The liftboost of the block is `MoveSpeed` (in opposite direction), x component multiplied by 0.75. (Therefore it’s not the same direction from block’s center to player’s center.)<br>
If the distance between `ExactPosition` and `TargetPosition` <= 1.414, set `MoveSpeed` to 0 and enter Bouncing state. (So if you move too much on the block, this state would be longer)<br>
All in one picture<br>
## State 3: Bouncing
Keep the `BounceDirection` of the last frame in WindingUp state and won’t recalculate anymore. (So player position on last WindingUp frame determines final liftboost)
From `StartPosition` of the block, move 24 px in `BounceDirection` to get `TargetPosition`. The block will move from `ExactPosition` to `TargetPosition` by `min(3 * MoveSpeed, 200)`. `MoveSpeed` increases by 13.33 per frame until it reaches 140.
The liftboost of the block is `min(3 * MoveSpeed, 200)`, x component multiplied by 0.75.
If player leaves the block, or `ExactPosition` is same as `TargetPosition`, enter BounceEnd state. If player is still on the block, the block cancles dash, sets player’s speed to liftboost, and gives coyote time. (In this coyote time you can core hyper/super or jump with 2 boosts)
## State 4: BounceEnd
The block stays in place for 2 frames then breaks.
## State 5: Broken
Respawn after 96 frames and back to Waiting state.
