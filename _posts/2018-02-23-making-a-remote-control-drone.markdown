---
title: "Making A Remote Control Drone"
layout: post
date: 2018-01-23 22:48
image: /11w5lxGBI8zAAIgSD1vJAEw.png
headerImage: true
tag:
- iOS
- swift
- Augmented Reality
- remote control drone
category: blog
author: chriswebb
description: Adventures in Augmented Reality
---
#### Making A Remote Control&nbsp;Drone

![](https://cdn-images-1.medium.com/max/300/1*LpNpWVlYHaa00I_66QAfAg.gif)

<p class="message">
   <a href="https://github.com/chriswebb09/ARKitDrone">Demo Code</a>
</p>


In the last few posts on ARKit, most of the content we placed was pretty static. For this example, I want to switch gears and make something in ARKit that is a bit more interactive.

#### **Send in the&nbsp;drones!**

If you’ve ever wanted to play around with a remote-controlled drone but have never had the opportunity to do so, this tutorial is for you. I know the controls look a bit stupid at the moment, but the point of this post isn’t to create a genuinely impressive interface, it’s to get you started down the road where you’re interacting with the augmented world.

### Getting Started

![](https://cdn-images-1.medium.com/max/1024/1*zCcFVio1j-FzFhXGzVUPfA.jpeg)

Given the complexity presented to us in rendering a drone, the best place for us to start would be by finding a good 3D drone model to use. I’ve found that COLLADA files are the easiest for interfacing with Apple’s SceneKit and are the easiest file type to convert to&nbsp;.scn files. COLLADA files use the file extension&nbsp;.dae and can be viewed in Apple’s preview application.

> A file with the DAE [file extension](https://www.lifewire.com/what-is-a-file-extension-2625879) is a Digital Asset Exchange file. As the name implies, it’s used by various graphics programs to exchange digital assets under the same format. They may be images, textures, 3D models, etc. — [Source](https://www.lifewire.com/dae-file-2620544)

For this example, we’re going to be using a 3D model of a drone that found on [free3d.com](http://free3d.com). If you’re wary of signing up, you can download the&nbsp;.dae file from the link I will post below, or alternatively, you can you can get the file that I’ve converted to&nbsp;.scn in the finished project&nbsp;files.

Here is a link to the&nbsp;.dae&nbsp;file:

[DropBox](https://www.dropbox.com/s/ba7c2ha18cy6uwa/Drone_dae.dae)

#### Converting From&nbsp;.dae To&nbsp;.scn

![](https://cdn-images-1.medium.com/max/1024/1*tIRXN3MaY3uRsCrFFrAOgA.png)

While SceneKit can handle working with&nbsp;.dae files just fine, for simplicity, we’ll just go ahead and convert it to a&nbsp;.scn file. There might be a better way to do this, I stumbled across this feature while playing around with SceneKit’s settings. To convert to a&nbsp;.scn file, select the&nbsp;.dae file, and in the inspector, on the right side of the screen, there should be a tab for the physics inspector like&nbsp;below:

![](https://cdn-images-1.medium.com/max/516/1*t0v0USS2M_rf2JsgiIg1OQ.png)

Once you select a physics body, XCode should pop up a prompt asking you whether you want to convert the document to SCN&nbsp;format.

![](https://cdn-images-1.medium.com/max/904/1*LPVrqLe7dR2vuQ2b9dbkFw.png)

### DroneSceneView

Now that we have our model we can start coding our drone. I want to start off by creating a subclass of ARSCNView called DroneSceneView. In this view, we’ll try to encapsulate the behaviors and properties of our drone. We’ll use the controller to connect these behaviors to the&nbsp;UI.

#### Setup

We should give our DroneSceneView five properties which will be of type SCNNodes. The helicopterNode gives us a hook into the main body of the drone, the blade1 and blade2 nodes hook us into the the framework surrounding the drones left or right rotors and the rotorR and rotorL nodes hooks into the blade themselves.

{% highlight swift %}
import ARKit
import SceneKit

class DroneSceneView: ARSCNView {

    var helicopterNode: SCNNode!
    var blade1Node: SCNNode!
    var blade2Node: SCNNode!
    var rotorR: SCNNode!
    var rotorL: SCNNode!

       func setupDrone() {
        scene = SCNScene(named: "art.scnassets/Drone.scn")!
        helicopterNode = scene.rootNode.childNode(withName: "helicopter", recursively: false)
        helicopterNode.transform = SCNMatrix4Mult(helicopterNode.transform, SCNMatrix4MakeRotation(Float(Double.pi) / 2, 1, 0, 0))
        helicopterNode.position = SCNVector3(helicopterNode.position.x, helicopterNode.position.y, helicopterNode.position.z - 1)
        blade1Node = helicopterNode?.childNode(withName: "Rotor_R_2", recursively: true)
        blade2Node = helicopterNode?.childNode(withName: "Rotor_L_2", recursively: true)
        rotorR = blade1Node?.childNode(withName: "Rotor_R", recursively: true)
        rotorL = blade2Node?.childNode(withName: "Rotor_L", recursively: true)
        styleDrone()
        spinBlades()
    }
}

{% endhighlight %}

The first method we should create will be the overall setup method which I called setupDrone. In setupDrone, we should assign the appropriate parts of our drone model to these nodes. The names passed to the method childNode(withName:, recursively:) match up with the names in our model’s scene&nbsp;graph.

![](https://cdn-images-1.medium.com/max/1024/1*cS13I5c9A8WTbjp_QLD5Ng.png)

#### Style

Next we’ll add a bit of styling to our drone. This part isn’t hugely important but it can help make it seem more realistic, which is generally a key aspect of augmented reality and it gives you a better visual sense of the different parts.

{% highlight swift %}
import ARKit
import SceneKit

class DroneSceneView: ARSCNView {

    // Setup

     func styleDrone() {
        let bodyMaterial = SCNMaterial()
        bodyMaterial.diffuse.contents = UIColor.black
        helicopterNode.geometry?.materials = [bodyMaterial]
        scene.rootNode.geometry?.materials = [bodyMaterial]
        let bladeMaterial = SCNMaterial()
        bladeMaterial.diffuse.contents = UIColor.gray
        let rotorMaterial = SCNMaterial()
        rotorMaterial.diffuse.contents = UIColor.darkGray
        blade1Node.geometry?.materials = [rotorMaterial]
        blade2Node.geometry?.materials = [rotorMaterial]
        rotorR.geometry?.materials = [bladeMaterial]
        rotorL.geometry?.materials = [bladeMaterial]
    }  
}

{% endhighlight %}

#### SpinBlades

Because we want our drone to look like the real thing, we’ll need to animate the rotors to rotate. To simplify things, I’m setting it so that the drones rotors are going constantly. To do this need to access the blades themselves, and manipulate them along their&nbsp;z-axis.

```
let rotate = SCNAction.rotateBy(x: 0, y: 0, z: 200, duration: 0.5)
```

{% highlight swift %}
import ARKit
import SceneKit

class DroneSceneView: ARSCNView {

    // Setup & style

    func spinBlades() {
        let rotate = SCNAction.rotateBy(x: 0, y: 0, z: 200, duration: 0.5)
        let moveSequence = SCNAction.sequence([rotate])
        let moveLoop = SCNAction.repeatForever(moveSequence)
        rotorL.runAction(moveLoop)
        rotorR.runAction(moveLoop)
    }
}

{% endhighlight %}

The drones rotators will now spin, but it doesn’t actually make any difference in terms of&nbsp;motion.

### Controls

The spinning blades are purely cosmetic. Let’s actually make it do something. To create an animation sequence we’re going to call SCNTransaction.begin


SCNTransaction.begin()
SCNTransaction.animationDuration = 0.5

// Animation sequence

SCNTransaction.commit()


#### Lateral

![](https://cdn-images-1.medium.com/max/225/1*4ijWIzcTOG3TjqXif7WStA.gif)


// Right
helicopterNode.position = SCNVector3(helicopterNode.position.x + 0.5, helicopterNode.position.y, helicopterNode.position.z)

// Left
helicopterNode.position = SCNVector3(helicopterNode.position.x - 0.5, helicopterNode.position.y, helicopterNode.position.z)


To make our drone move to left or right we’ll manipulate the x element in its position property. To move to the left we’ll subtract a float value of -0.5 from x and conversely to move to the right we’ll add a float value of 0.5 to&nbsp;x.

{% highlight swift %}
import ARKit
import SceneKit

class DroneSceneView: ARSCNView {

    func moveLeft() {
        SCNTransaction.begin()
        SCNTransaction.animationDuration = 0.5
        helicopterNode.position = SCNVector3(helicopterNode.position.x - 0.5, helicopterNode.position.y, helicopterNode.position.z)
        blade2Node.runAction(SCNAction.rotateBy(x: 0.3, y: -0.1, z: 0, duration: 1.5))
        blade1Node.runAction(SCNAction.rotateBy(x: 0.3, y: 0, z: 0, duration: 1.5))
        SCNTransaction.commit()
        blade2Node.runAction(SCNAction.rotateBy(x: -0.3, y: 0.1, z: 0, duration: 0.25))
        blade1Node.runAction(SCNAction.rotateBy(x: -0.3, y: 0, z: 0, duration: 0.25))
    }

    func moveRight() {
        SCNTransaction.begin()
        SCNTransaction.animationDuration = 0.5
        print(scene.rootNode.childNodes[0].position)
        helicopterNode.position = SCNVector3(helicopterNode.position.x + 0.5, helicopterNode.position.y, helicopterNode.position.z)
        blade2Node.runAction(SCNAction.rotateBy(x: 0.3, y: 0, z: 0, duration: 1.5))
        blade1Node.runAction(SCNAction.rotateBy(x: 0.3, y: 0.1, z: 0, duration: 1.5))
        SCNTransaction.commit()
        blade2Node.runAction(SCNAction.rotateBy(x: -0.3, y: 0, z: 0, duration: 0.25))
        blade1Node.runAction(SCNAction.rotateBy(x: -0.3, y: -0.1, z: 0, duration: 0.25))
    }
}
{% endhighlight %}

#### Forward/Back

![](https://cdn-images-1.medium.com/max/225/1*9N2crnX-5jfzyIKzPFurww.gif)

// Forward
helicopterNode.position = SCNVector3(helicopterNode.position.x, helicopterNode.position.y, helicopterNode.position.z - 0.5)

// Backward
helicopterNode.position = SCNVector3(helicopterNode.position.x, helicopterNode.position.y, helicopterNode.position.z + 0.5)


To make our drone move forward or backward we’ll manipulate the z element in its position property. To move to the left we’ll subtract a float value of -0.5 from z and conversely to move to the right we’ll add a float value of 0.5 to&nbsp;z.


{% highlight swift %}
import ARKit
import SceneKit

class DroneSceneView: ARSCNView {

    func moveForward() {
        SCNTransaction.begin()
        SCNTransaction.animationDuration = 0.5
        helicopterNode.position = SCNVector3(helicopterNode.position.x, helicopterNode.position.y, helicopterNode.position.z - 0.5)
        blade2Node.runAction(SCNAction.rotateBy(x: 0.3, y: 0, z: 0, duration: 1.5))
        blade1Node.runAction(SCNAction.rotateBy(x: 0.3, y: 0, z: 0, duration: 1.5))
        SCNTransaction.commit()
        blade2Node.runAction(SCNAction.rotateBy(x: -0.3, y: 0, z: 0, duration: 0.25))
        blade1Node.runAction(SCNAction.rotateBy(x: -0.3, y: 0, z: 0, duration: 0.25))
    }

    func reverse() {
        SCNTransaction.begin()
        SCNTransaction.animationDuration = 0.5
        helicopterNode.position = SCNVector3(helicopterNode.position.x, helicopterNode.position.y, helicopterNode.position.z + 0.5)
        blade2Node.runAction(SCNAction.rotateBy(x: 0.3, y: 0, z: 0, duration: 1.5))
        blade1Node.runAction(SCNAction.rotateBy(x: 0.3, y: 0, z: 0, duration: 1.5))
        SCNTransaction.commit()
        blade2Node.runAction(SCNAction.rotateBy(x: -0.3, y: 0, z: 0, duration: 0.25))
        blade1Node.runAction(SCNAction.rotateBy(x: -0.3, y: 0, z: 0, duration: 0.25))
    }
}
{% endhighlight %}

#### Altitude

![](https://cdn-images-1.medium.com/max/225/1*AuCC5cLuwIZ9QvHeWjXu0A.gif)

```
helicopterNode.position = SCNVector3(helicopterNode.position.x, value, helicopterNode.position.z)
```

To make our drone move up or down we’ll manipulate the y element in its position property. By add a flow value to the drones y position property value the drone will move up, if you subtract a float value from the drones y position property value it will&nbsp;descend.

{% highlight swift %}
import ARKit
import SceneKit

class DroneSceneView: ARSCNView {

     func changeAltitude(value: Float) {
        SCNTransaction.begin()
        SCNTransaction.animationDuration = 0.5
        helicopterNode.position = SCNVector3(helicopterNode.position.x, value, helicopterNode.position.z)
        SCNTransaction.commit()
    }
}
{% endhighlight %}


#### Special Effects

These animations are mainly for decoration. They tilt the drone when it moves and then resets it to normal resting position. This is more an experiment of mine to try to make the flight look as natural as possible.

```
blade2Node.runAction(SCNAction.rotateBy(x: 0.3, y: 0, z: 0, duration: 1.5))
blade1Node.runAction(SCNAction.rotateBy(x: 0.3, y: 0, z: 0, duration: 1.5))
SCNTransaction.commit()
blade2Node.runAction(SCNAction.rotateBy(x: -0.3, y: 0, z: 0, duration: 0.25))
blade1Node.runAction(SCNAction.rotateBy(x: -0.3, y: 0, z: 0, duration: 0.25))
```

### Controls UI

![](https://cdn-images-1.medium.com/max/472/1*w2eZTn7WbzunABoy7iRmAw.png)

As you can tell from the picture, I’ve constructed rudmentary UI to get us going. It’s basically some arrows and a slider. We’ll use the slider to control our altitude and the arrows to control the direction that our drone moves&nbsp;in.

{% highlight swift %}
import UIKit
import SceneKit
import ARKit

// MARK: - ARSCNViewDelegate
extension ViewController: ARSCNViewDelegate {

    @IBAction func altitudeValueChanged(_ sender: Any) {
        guard let slider = sender as? UISlider else { return }
        sceneView.changeAltitude(value: slider.value)
    }

    @IBAction func forwardButtonTapped(_ sender: Any) {
        sceneView.moveForward()
    }

    @IBAction func rightButtonTapped(_ sender: Any) {
        sceneView.moveRight()
    }

    @IBAction func reverseButtonTapped(_ sender: Any) {
        sceneView.reverse()
    }

    @IBAction func leftButtonTapped(_ sender: Any) {
        sceneView.moveLeft()
    }
}
{% endhighlight %}

#### Sources:

- [3DHaupt](https://3dhaupt.com)
- [Controllable Drone - Blender Game Engine - 3d model - .3ds, .obj, .dae, .blend, .fbx, .mtl, .dxf, .stl](https://free3d.com/3d-model/controllable-drone-blender-game-engine-67381.html)
