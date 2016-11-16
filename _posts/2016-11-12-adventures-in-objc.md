---
layout: post
title: Adventures in Cocos2D
---

<p class="message">
The journey of one thousand apps starts with a single key press...
</p>

---

### Brief Overview
So back into the land of Objective-C. It was definitely been an interesting foray after having spent the last three months in Swiftland. 
Why not just dive right in with Cocos2D? I’ll admit, the adjustment took some time, but the muscle memory comes back to life after a bit coding.

### Struggles and Observations  
Overall, I think the trickiest thing I've run across was making the sprite shoot a projectile and having it do some action on intersection with the enemy.

 {% highlight objc linenos %}


- (void)shootBulletsOnTouch:(UITouch *)touch {
    
    CCSprite *bullet = [CCSprite spriteWithImageNamed:@"bullet.png"];
    [bullet setPosition:ccp(self.screenWidth/2, self.screenHeight/2)];
    
    id shoot = [CCActionMoveTo actionWithDuration:0.5 position:ccp(self.screenHeight, self.screenWidth/2)];
    id ease = [CCActionEaseOut actionWithAction:shoot rate:1];
    id speed = [CCActionSpeed actionWithAction:ease speed:1];
    
    [bullet runAction:speed];
    
    bullet.physicsBody = [CCPhysicsBody bodyWithCircleOfRadius:bullet.contentSize.width/2.0f andCenter:bullet.anchorPointInPoints];
    bullet.physicsBody.collisionGroup = @"playerGroup";
    bullet.physicsBody.collisionType  = @"projectileCollision";
    [self.physics addChild:bullet];
}

{% endhighlight %}

It was one of those problems where the part that seems hard is easy, and the little things are where hangups happen. Making the sprite shoot a projectile, what I would call the flashy aspect of the problem, was relatively straightforward to figure out. What was the hardest part? Collision detection was easily the part that I struggled with the most. Although I would attribute that mainly to my approach to solving the problem at the first go around. The actual solution was relatively straight forward. The initial approach to solving it led to a dead end. I tried to find the the position of the projectile while it was in motion and check it against the enemy location. 

### The case of the hard to access position ivar 
The first problem I ran into was the projectile ivar for position was either null, or when I tried to access it inside the animation was protected. Some additional complications that I ran into were that a lot of the functionality revolved around C++, which is a language I haven't explored yet. Just as I was about to try calculating the geometry for the intersection between the projectile and the enemy I found a simpler solution. If I added physics to the scene, and gave the bodies physics properties, I could access the collision detection delegate. 

{% highlight objc linenos %}

self.physics = [CCPhysicsNode node];
self.physics.gravity = ccp(0,0);
self.physics.collisionDelegate = self;

{% endhighlight %}

### Wrap up 
After that, it was simply a matter of creating this method:

{% highlight objc linenos %}

-(BOOL)ccPhysicsCollisionBegin:(CCPhysicsCollisionPair *)pair enemyCollision:(CCNode *)enemy bulletCollision:(CCNode *)bullet;

{% endhighlight %}

and implementing code I wanted to run on collision. 
As weird as the adjustment can be, overall Objective-C-land is an interesting place to explore. It is definitely a destination that I will make an effort to travel to more, from now on. 

Sources: 

- [https://www.raywenderlich.com/61391/how-to-make-a-simple-iphone-game-with-cocos2d-3-0-tutorial](https://www.raywenderlich.com/61391/how-to-make-a-simple-iphone-game-with-cocos2d-3-0-tutorial)
- [http://cocos2d.spritebuilder.com/docs/api/Classes/CCPhysicsBody.html](http://cocos2d.spritebuilder.com/docs/api/Classes/CCPhysicsBody.html)
- [http://kirillmuzykov.com/cocos2d-iphone-easing-examples/](http://kirillmuzykov.com/cocos2d-iphone-easing-examples/) 


