# Tip of The Spear

## Mission start soft ceiling hole


At the very beginning of the mission there is a *"hole"* that acrophobia speedrunners use to immediately get outside of the map, but it's really not an actual hole in the soft ceiling, due to an oversight the mesh normals at that specific part cancel each other out creating a pseudo "hole" allowing players to freely escape the area.

<img src="wiki\reach\campaign\tip-of-the-spear\_media\barrier_ingame_1.png" alt="in-game_hole_1" width="852" height="480">
<img src="wiki\reach\campaign\tip-of-the-spear\_media\barrier_normals_1.png" alt="normals_hole_1" width="852" height="480">

<img src="wiki\reach\campaign\tip-of-the-spear\_media\barrier_ingame_2.png" alt="in-game_hole_2" width="852" height="480">
<img src="wiki\reach\campaign\tip-of-the-spear\_media\barrier_normals_2.png" alt="normals_hole_2" width="852" height="480">

<img src="wiki\reach\campaign\tip-of-the-spear\_media\barrier_ingame_3.png" alt="in-game_hole_3" width="852" height="480">
<img src="wiki\reach\campaign\tip-of-the-spear\_media\barrier_normals_3.png" alt="normals_hole_3" width="852" height="480">


## Health Regeneration
In extremely specific instances it is possible for a player to be granted a checkpoint for health regeneration. 

<img src="wiki\reach\campaign\tip-of-the-spear\_media\health_regen_checkpoint.png" alt="Health re-gen debug" width="852" height="480">

There is a dormant script `f_global_health_saves` in the campaign, but it goes for the most part unused because most missions don't trigger `(wake f_global_health_saves)` in the mission script startup.

Missions that use `f_global_health_saves`:
- Winter Contingency 
- Tip of The Spear

Both missions use different implementations of `f_global_health_saves`. 

*Winter Contingency has a check in the script start-up to see if the game is in `Solo` or `Co-op`. If a player is in a `Co-op` game `f_global_health_saves` will be left dormant, unable to be used for checkpoint exploits.*

`(if (not (game_is_cooperative)) (wake f_global_health_saves))`

*Tip of The Spear does not differentiate and proceeds to run the wake script along with all the other crucial startup scripts.*

`(wake f_global_health_saves)`

> *With the differences of the mission specific initializations established, how does `f_global_health_saves` actually work?*

The game checks to see if the health of `player0` drops below 100% `object_get_health player0 < [1.0]`. Once `player0`'s health reaches ~66% they can no longer regenerate health without assistance. 

If the player restores their health back to 100% within a few seconds of the initial regeneration `(= (object_get_health player0) 1.0)`, the game will grant a checkpoint if the player is not considered to be in 'danger'.
```css
(script dormant void f_global_health_saves
    (sleep_until (> (player_count) 0))
    (sleep_until 
        (begin
            (sleep_until (< (object_get_health player0) 1.0))
            (sleep (* 30.0 7.0))
            (if (< (object_get_health player0) 1.0) 
                (begin
                    (sleep_until (= (object_get_health player0) 1.0))
                    (print "global health: health pack aquired")
                    (game_save)
                ) (print "global health: re-gen"))
            false
        )
    )
)
```
> *From looking at the script above, it is clear that the developers intended for a checkpoint to drop after picking up a Health Pack, but the `f_global_health_saves` script does not actually check how the player gains the health back. `Player0` can use various methods for health regeneration such as, sitting in a dropshield, hitting loadzones that alter health, quickly dying then staying safe after a respawn, and of course using the intended health pack.*