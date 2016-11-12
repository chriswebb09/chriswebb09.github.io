---
layout: post
title: Adventures in Objective-C and Cocos2D
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

### Brief Overview
So back into the land of Objective-C. It has definitely been an interesting foray after having spent the last three months in Swiftland. 
Why not just dive right in with Cocos2D? I’ll admit the adjustment took some time, but the muscle memory comes back to life after a bit coding.

### Struggles and Observations  
What was the hardest part? Collision detection was easily the part that I struggle with the most, although that is mainly be due more to my approach to solving it at first. The actual solution was relatively straight forward. The initial approach to solving it led to a dead end. I tried to find the the position of the projectile while it was in motion and check it against the enemy location.


### The case of the hard to access position ivar 
The first problem I ran into was the projectile ivar for position was either null or when I tried to access it inside the animation was protected. Just as I was about to try calculating the geometry for the intersection between the projectile and the enemy I found a simpler solution. If the I added physics to the scene and gave the bodies physics properties I could access the collision detection delegate. 

### Wrap up 
After that it’s simply a matter of creating this method (BOOL)ccPhysicsCollisionBegin:(CCPhysicsCollisionPair *)pair enemyCollision:(CCNode *)enemy bulletCollision:(CCNode *)bullet and implementing code I wanted to run on collision. 
