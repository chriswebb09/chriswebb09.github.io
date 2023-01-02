---
title: "ARKit and Core Location: Part One"
layout: post
date: 2017-08-15 22:48
image: /assets/images/ARKit.png
headerImage: true
tag:
- iOS
- swift
- Augmented Reality
- navigation
- core location
category: blog
author: chriswebb
description: Adventures in Augmented Reality
---

## Navigation With Linear Algebra

![]("/assets/images/drone-demo3.gif")


[Demo Code](https://github.com/chriswebb09/ARKitNavigationDemo)

[ARKit and CoreLocation: Part One](http://chriswebb09.github.io/2017/08/15/arkit-corelocation-part-one/)

[ARKit and CoreLocation: Part Two](https://medium.com/journey-of-one-thousand-apps/arkit-and-corelocation-part-two-7b045fb1d7a1)

[ARKit and CoreLocation: Part Three](https://medium.com/journey-of-one-thousand-apps/arkit-and-corelocation-part-three-98b1d51e2eac)


#### **Background**

It has been awhile since I’ve written a new blog post so hopefully, this makes up the difference. This post and the next will be a two part series on my experiments with [ARKit](https://developer.apple.com/documentation/arkit) and [CoreLocation](https://developer.apple.com/documentation/corelocation)! The first part will deal with the basics of ARKit, getting the directions from MapKit as well as touch on the basics of matrix transformations. The [**second part**](https://medium.com/@chris.webb5249/arkit-and-corelocation-part-two-7b045fb1d7a1) will deal with calculating the bearing between two locations and how to take the location data and translate that into a position in the ARKit&nbsp;Scene.

### Introduction

![](https://cdn-images-1.medium.com/max/292/1*pTwz78YrgP5-I17T8AvNYA.jpeg)

Mention “augmented reality” and the first thing that jumps into most people’s head is [PokemonGO](http://www.pokemongo.com). If you’re like most people, you’ve probably played it once or twice (or obsessively.) PokemonGO proved that when it comes to setting, nothing beats our world. As awesome as PokemonGO was, it was a slight glimpse into the depth and potential of the augmented reality experience.

[**Apple Documentation**](https://developer.apple.com/documentation/arkit) **:**

> _Augmented reality_ (AR) describes user experiences that add 2D or 3D elements to the live view from a device’s camera in a way that makes those elements appear to inhabit the real world. ARKit combines device motion tracking, camera scene capture, advanced scene processing, and display conveniences to simplify the task of building an AR experience.

With [iOS 11](https://www.apple.com/ios/ios-11-preview/), Apple has unleashed the power of ARKit onto the iOS development community. We’re still several weeks out of iOS 11 going live, but what we’re already seeing looks likely to redefine what is possible for the mobile user experience.

### **First, Some Fundamentals**

![](https://cdn-images-1.medium.com/max/281/1*z2dEldME3fyddalmq8fu2g.gif)

Personal Project — August 20th

So, it’s magic right? I hate to be the one to say this but no, it’s just math. So if it isn’t magic how do they pull it off? Visual Inertial Odometry! (Say it ten times&nbsp;fast.)

#### Definitions

[**Visual Inertial Odometry (VIO)**](https://developer.apple.com/documentation/arkit/understanding_augmented_reality): ARKit analyzes the phone camera and motion data in order to keep track of the world around it. The computer vision logs noticeable features in the environment and is able to maintain awareness of their location in the real world regardless of the iPhone’s movement.

Apple is a huge fan of organizing code around sessions. **Sessions** are a way of encapsulating the logic and data contained within a defined period of the applications activity. With URLSession this is the logic and data when your application sends network requests and receives data in back in&nbsp;return.

[**ARSession**](https://developer.apple.com/documentation/arkit/arsession) **:** In ARKit the **ARSession** coordinates the logic and data necessary to create the augmented reality experience. This includes camera and motion data and calculations required to keep track of the world as it moves&nbsp;around.

[**ARFrame**](https://developer.apple.com/documentation/arkit/arframe): An **ARFrame** contains a video frame data and position tracking data which gets passed along to the ARSession in the currentFrame property. ARKit marries that image data with the motion tracking data to calculate the iPhone’s position.

[**ARAnchor**](https://developer.apple.com/documentation/arkit/aranchor) **:** An **ARAnchor** is a position in the real world that maintained regardless of motion or position of the camera (theoretically). It’s anchored to a specific position, and for the most part will remain&nbsp;there.

[**ARConfiguration**](https://developer.apple.com/documentation/arkit/arconfiguration)

![](https://cdn-images-1.medium.com/max/500/1*BBInz1XxPnD4nviPGetDKQ.png)

[**ARWorldTrackingConfiguration**](https://developer.apple.com/documentation/arkit/arworldtrackingconfiguration) **:** is a configuration for tracking the devices orientation, position and for detecting feature points like surfaces that are recorded by the camera. ARConfigurations connect the physical world which you and the phone exist in with the virtual coordinate space generated by your phone based on the camera and motion&nbsp;data.

```
worldAlignment: The worldAlignment property on ARSession defines how the ARSession interprets the ARFrame’s motion data on a 3D coordinate mapping system that is uses to keep track of the world and build the Augmented Reality experience.
```

![](https://cdn-images-1.medium.com/max/1024/1*j12eVe8hKvXq-CrbBzHMXA.png)<a href="https://docs-assets.developer.apple.com/published/b99f86dcfb/f76d63a3-7620-40d1-9e52-0d9ad6329678.png">Source</a>

[**worldAlignment — Apple&nbsp;Docs**](https://developer.apple.com/documentation/arkit/arconfiguration/2923550-worldalignment)

> Creating an AR experience depends on being able to construct a coordinate system for placing objects in a virtual 3D world that maps to the real-world position and motion of the device. When you run a session configuration, ARKit creates a scene coordinate system based on the position and orientation of the device; any ARAnchor objects you create or that the AR session detects are positioned relative to that coordinate system_._
```
gravity: By setting the alignment to gravity ARKit aligns the y-axis parallel to gravity and the z and x axes are oriented along the original heading of the device.
```

![](https://cdn-images-1.medium.com/max/500/1*IRvOJvHSBOpLxbdGCzck-g.png)<a href="https://docs-assets.developer.apple.com/published/ffb3831f78/d937c9e9-05fd-46e3-b72d-eb353b79bfbd.png">Source</a>
{% highlight html %}
{% endhighlight %}

[**worldAlignment.gravity — Apple&nbsp;Docs**](https://developer.apple.com/documentation/arkit/arconfiguration.worldalignment/2873778-gravity)

> The position and orientation of the device as of when the session configuration is first run determine the rest of the coordinate system: For the z-axis, ARKit chooses a basis vector (0,0,-1) pointing in the direction the device camera faces and perpendicular to the gravity axis. ARKit chooses a x-axis based on the z- and y-axes using the right hand rule—that is, the basis vector (1,0,0) is orthogonal to the other two axes, and (for a viewer looking in the negative-z direction) points toward the&nbsp;right.
```
gravityAndHeading: By setting the alignment to gravityAndHeading ARKit aligns the y-axis parallel to gravity and the z and x axes orient towards compass heading. The origin is placed at the initial location of the device. While this is accurate most of the time, it’s precision is not incredibly high so creating an immersive augmented reality experience while solely relying on this data can be tricky.
```

![](https://cdn-images-1.medium.com/max/815/1*Q-2ce-YwHsHTDCaAFWcL9g.png)
{% highlight html %}
{% endhighlight %}

<a href="https://docs-assets.developer.apple.com/published/ffb3831f78/0a0ac708-2357-4418-ad41-88711d290f8e.png">Source</a>
{% highlight html %}
{% endhighlight %}

[**worldAlignment.gravityAndHeading — Apple&nbsp;Docs**](https://developer.apple.com/documentation/arkit/arconfiguration.worldalignment/2873776-gravityandheading)

{% highlight html %}
{% endhighlight %}

> Although this option fixes the _directions_ of the three coordinate axes to real-world directions, the _location_ of the coordinate system’s origin is still relative to the device, matching the device’s position as of when the session configuration is first&nbsp;run.

{% highlight html %}
{% endhighlight %}

### SceneKit

One of the coolest things about ARKit is that it integrates well with Apple’s existing graphics rendering engines: SpriteKit, Metal and SceneKit. The one I’ve used the most has been SceneKit which is used for rendering 3D&nbsp;objects.

![](https://cdn-images-1.medium.com/max/281/1*gPCWMK3EAYHTNGTVNc2bOQ.gif)

{% highlight html %}
{% endhighlight %}

Personal project — August 11th

#### Definition

[**ARSCNView**](https://developer.apple.com/documentation/arkit/arscnview) **:** ARSCNView is a subclass of SCNView which is the standard SceneKit view for rendering 3D content. Because it is specialized for ARKit, it has some really cool features already baked in. For one, it provides seamless access to the phone’s camera. Even cooler, the world coordinate system for the view’s SceneKit scene directly responds to the AR world coordinate system established by the session configuration. It also automatically moves the SceneKit camera to match the real movement of the&nbsp;iPhone.
{% highlight html %}

{% endhighlight %}
![](https://cdn-images-1.medium.com/max/281/1*aPX9VBgkpO0en1reGyS0Mw.gif)
{% highlight html %}

{% endhighlight %}

Personal project — August 12th

[**ARSCNView Docs**](https://developer.apple.com/documentation/arkit/arscnview) **:**

> Because ARKit automatically matches SceneKit space to the real world, placing a virtual object such that it appears to maintain a real-world position requires only setting that object’s SceneKit position appropriately.> You don’t necessarily need to use the ARAnchor class to track positions of objects you add to the scene, but by implementing ARSCNViewDelegate methods, you can add SceneKit content to any anchors that are automatically detected by&nbsp;ARKit.
### Adding Node To&nbsp;Scene
{% highlight html %}

{% endhighlight %}
![](https://cdn-images-1.medium.com/max/633/1*WLaPipp1LqIdcwbWqCCtZg.png)
{% highlight html %}

{% endhighlight %}
<a href="https://developer.apple.com/documentation/scenekit/scnsphere">Source</a>

Before we go any further let’s get something basic out of the way. Let’s build our first augmented reality experience! To do this, we’re going to place a blue orb 1 meter in front of the&nbsp;camera.

#### Definitions

[**SCNSphere**](https://developer.apple.com/documentation/scenekit/scnsphere) **:** A sphere defines a surface whose every point is equidistant from its center, which is placed at the origin of its local coordinate space. You define the size of the sphere in all three dimensions using its [radius](https://developer.apple.com/documentation/scenekit/scnsphere/1523787-radius) property.

[**SCNGeometry**](https://developer.apple.com/documentation/scenekit/scngeometry) **:**
{% highlight html %}

{% endhighlight %}
A three-dimensional shape (also called a model or mesh) that can be displayed in a scene, with attached materials that define its appearance.

#### SphereNode Sphere&nbsp;Code


{% highlight swift %}
import UIKit
import SceneKit
import ARKit

class ViewController: UIViewController, ARSCNViewDelegate {

   @IBOutlet var sceneView: ARSCNView!

    override func viewDidLoad() {
        super.viewDidLoad()
        // setup
    }

    // viewWillAppear & viewWillDisappear

    func createSphereNode(with radius: CGFloat, color: UIColor) -> SCNNode {
        let geometry = SCNSphere(radius: radius)
        geometry.firstMaterial?.diffuse.contents = color
        let sphereNode = SCNNode(geometry: geometry)
        return sphereNode
    }
}
{% endhighlight %}

```
sceneView.scene = SCNScene()        
// Add Scene

let circleNode = createSphereNode(with: 0.2, color: .blue)  
// Add Sphere

circleNode.position = SCNVector3(0, 0, -1) // 1 meter in front
// Give sphere position    

sceneView.scene.rootNode.addChildNode(circleNode)
// Add to scene as childNode of rootNode
```

**Putting It&nbsp;Together**

{% highlight swift %}
import UIKit
import SceneKit
import ARKit

class ViewController: UIViewController, ARSCNViewDelegate {

    @IBOutlet var sceneView: ARSCNView!

    override func viewDidLoad() {
        super.viewDidLoad()
        sceneView.delegate = self
        sceneView.showsStatistics = true
        sceneView.scene = SCNScene()
        let circleNode = createSphereNode(with: 0.2, color: .blue)
        circleNode.position = SCNVector3(0, 0, -1) // 1 meter in front of camera
        sceneView.scene.rootNode.addChildNode(circleNode)
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        let configuration = ARWorldTrackingConfiguration()
        sceneView.session.run(configuration)
    }

    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        sceneView.session.pause()
    }

    func createSphereNode(with radius: CGFloat, color: UIColor) -> SCNNode {
        let geometry = SCNSphere(radius: radius)
        geometry.firstMaterial?.diffuse.contents = color
        let sphereNode = SCNNode(geometry: geometry)
        return sphereNode
    }
}
{% endhighlight %}

SceneKit and ARKit coordinates are quantified in meters. When we set the the last property on the SCNVector3 to -1 we set the z axis to to one meter in front of the camera. If everything goes according to plan (it should) the screen will display something like&nbsp;this:
{% highlight html %}

{% endhighlight %}

![](https://cdn-images-1.medium.com/max/281/1*tcUiqA6_xsMcVMSu9-7sRQ.jpeg)

{% highlight html %}

{% endhighlight %}

For now this approach works fine. Our sphere will automatically appear to track a real-world position because ARKit matches SceneKit space to real-world space. If we want to use coordinates, we’re probably going to need to find something durable to anchor \* hint \* our node to in the&nbsp;future.

### Vectors and Matrice and Linear Algebra, Oh&nbsp;No!

![](https://cdn-images-1.medium.com/max/200/1*MinQCMoywXYBW4FsDWt80A.png)

{% highlight html %}

{% endhighlight %}

A two by four matrix.

If you remember back to math class, a vector has a magnitude and a direction.

> In mathematics, physics, and engineering, a Euclidean vector (sometimes called a geometric or spatial vector, or — as here — simply a vector) is a geometric object that has magnitude (or length) and direction.> — [Wikipedia](https://en.wikipedia.org/wiki/Euclidean_vector#Vectors_as_directional_derivatives)> When it comes to programming, a vector is just an array of numbers. Each number is a “dimension” of the&nbsp;vector.

To start off simply, well use a 2 by 1 matrix for our vector. Let’s give it a value of x = 1. The vector (1, 0) graphed looks&nbsp;like:

{% highlight html %}

{% endhighlight %}

![](https://cdn-images-1.medium.com/max/400/1*m0mZ1CshuJ2VpcvCEIjtIQ.png)

{% highlight html %}

{% endhighlight %}

We can express that same vector (1, 0) in a very simple&nbsp;matrix:

![](https://cdn-images-1.medium.com/max/196/1*BbhJ9I91WMTrsbON2L4AZg.png)

{% highlight html %}

{% endhighlight %}
X over Y

As stated&nbsp;above:

> a vector is just an array of&nbsp;numbers

As you can see, a matrix looks similar to an array of numbers. While they can seem intimidating, under the hood, matrices are quite a simple concept and simple to work with after you’ve practiced a&nbsp;bit.

**Definition from&nbsp;OpenGL** :

> Simply put, a matrix is an array of numbers with a predefined number of rows and&nbsp;columns

Matrices are used to transform 3D coordinates. These&nbsp;include:

- Rotation (changing orientation)
- Scaling (size&nbsp;changes)
- Translation (moving position)

### Transformations

In most cases a transformed point can be expressed in this equation:

```
Transformed Point = Transformation Matrix × Original Point
```

If you’ve worked with CoreGraphics before you’ve probably seen something called CGAffineTransform. It makes for some pretty cool animations. In fact, CGAffineTransform is just a different type of matrix transformation.

[**Affine transformation**](https://www.mathworks.com/discovery/affine-transformation.html) is a linear mapping method that preserves points, straight lines, and&nbsp;planes.

![](https://cdn-images-1.medium.com/max/430/1*tpTWHF7Uu1qCdDl2sKudEg.gif)

{% highlight html %}

{% endhighlight %}

<a href="http://smashinghub.com/wp-content/uploads/2014/09/menu-animation-effects-3.gif">source</a>

### Rotating A Space&nbsp;Ship

![](https://cdn-images-1.medium.com/max/281/1*YaX3B6nINy0LypdU4D9LHQ.jpeg)

{% highlight html %}

{% endhighlight %}

Let’s give transformations a try! While this isn’t the same way that they are used for the location nodes, they are close enough that you can start thinking about the principles in action. To do this create a new ARKit project with SceneKit. When you run it, there should be a spaceship floating in front of your screen like in the screenshot above.

#### Loop-T-Loops

Add the following lines below your viewDidLoad:


{% highlight swift %}

class ViewController: UIViewController, ARSCNViewDelegate {
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        sceneView.scene.rootNode.childNodes[0].transform = SCNMatrix4Mult(sceneView.scene.rootNode.childNodes[0].transform, SCNMatrix4MakeRotation(Float(Double.pi) / 2, 1, 0, 0))
        sceneView.scene.rootNode.childNodes[0].transform = SCNMatrix4Mult(sceneView.scene.rootNode.childNodes[0].transform, SCNMatrix4MakeTranslation(0, 0, -2))
    }
}
{% endhighlight %}

Now when you re-run it, the spaceship should still appear on your screen, however, when you tap it, it should loop around. Keep tapping it until it is back in the position it started (roughly).

![](https://cdn-images-1.medium.com/max/281/1*FJc-Q9IYhnVDX1mDH9RsVQ.jpeg)

Here is the full ViewController code:

{% highlight swift %}
class ViewController: UIViewController, ARSCNViewDelegate {

    @IBOutlet var sceneView: ARSCNView!

    override func viewDidLoad() {
        super.viewDidLoad()

        // Set the view's delegate
        sceneView.delegate = self

        // Show statistics such as fps and timing information
        sceneView.showsStatistics = true

        // Create a new scene
        let scene = SCNScene(named: "art.scnassets/ship.scn")!
        sceneView.scene = scene
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)

        // Create a session configuration
        let configuration = ARWorldTrackingConfiguration()

        // Run the view's session
        sceneView.session.run(configuration)
    }

    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        sceneView.scene.rootNode.childNodes[0].transform = SCNMatrix4Mult(sceneView.scene.rootNode.childNodes[0].transform, SCNMatrix4MakeRotation(Float(Double.pi) / 2, 1, 0, 0))
        sceneView.scene.rootNode.childNodes[0].transform = SCNMatrix4Mult(sceneView.scene.rootNode.childNodes[0].transform, SCNMatrix4MakeTranslation(0, 0, -2))
    }
}
{% endhighlight %}

If everything goes according to plan, after a few touchs, your spaceship will look like it is making glorious touch-down:

![](https://cdn-images-1.medium.com/max/281/1*YjgmjKDUQOZ9I4ftVVddNQ.jpeg)

### Navigation

Now that we have a bit of a handle on the basics of ARKit let’s move on to navigation and location services. If we want to get be guided to our destination, we’ll need a bit of help from a navigation service.

MapKit comes with a handy turn-by-turn directions API. Using CoreLocation a destination and MKDirectionsRequest we can get an array of navigational steps to follow that will lead us to a specific location.

{% highlight swift %}
import MapKit
import CoreLocation

struct NavigationService {

   func getDirections(destinationLocation: CLLocationCoordinate2D, request: MKDirectionsRequest, completion: @escaping ([MKRouteStep]) -> Void) {
        var steps: [MKRouteStep] = []

        let placeMark = MKPlacemark(coordinate: CLLocationCoordinate2D(latitude: destinationLocation.coordinate.latitude, longitude: destinationLocation.coordinate.longitude))

        request.destination = MKMapItem.init(placemark: placeMark)
        request.source = MKMapItem.forCurrentLocation()
        request.requestsAlternateRoutes = false
        request.transportType = .walking

        let directions = MKDirections(request: request)

        directions.calculate { response, error in
            if error != nil {
                print("Error getting directions")
            } else {
                guard let response = response else { return }
                for route in response.routes {
                    steps.append(contentsOf: route.steps)
                }
                completion(steps)
            }
        }
    }
}
{% endhighlight %}

#### Definitions

[**MKPlacemark**](https://developer.apple.com/documentation/mapkit/mkplacemark) **s:** contain information like city, state, county or street address that associated with a specific coordinate.

[**MKRoute**](https://developer.apple.com/documentation/mapkit/mkroute) **:**
{% highlight html %}

{% endhighlight %}
A single route between a requested start and end point.An MKRoute object defines the geometry for the route—that is, it contains line segments associated with specific map coordinates. A route object may also include other information, such as the name of the route, its distance, and the expected travel&nbsp;time.

```
print(route.name)

// Broadway

print(route.advisoryNotices)

// []

print(route.expectedTravelTime)

// 2500.0
```

[**MKRouteStep**](https://developer.apple.com/documentation/mapkit/mkroutestep) **:**
{% highlight html %}

{% endhighlight %}
is one segment of a route. Each step contains a single instruction that should be completed by a user navigating between two points in order for them to successfully complete their&nbsp;route.

```
print(step.distance)

// 1.0

print(step.instructions)

// Proceed to 7th Ave
```

[**MKMapItem**](https://developer.apple.com/documentation/mapkit/mkmapitem) **:** A point of interest on the map. A map item includes a geographic location and any interesting data that might apply to that location, such as the address at that location and the name of a business at that&nbsp;address.

[**MKDirections**](https://developer.apple.com/documentation/mapkit/mkdirections) **:** A utility object that computes directions and travel-time information based on the route information you&nbsp;provide.

{% highlight swift %}
import SceneKit
import ARKit
import CoreLocation
import MapKit

class ViewController: UIViewController, ARSCNViewDelegate, ARSessionDelegate, LocationServiceDelegate {

    @IBOutlet weak var sceneView: ARSCNView!

    var steps: [MKRouteStep] = []
    var destinationLocation: CLLocationCoordinate2D!
    var locationService = LocationService()

    override func viewDidLoad() {
        super.viewDidLoad()

        locationService.delegate = self
        var navService = NavigationService()

        self.destinationLocation = CLLocationCoordinate2D(latitude: 40.737512, longitude: -73.980767)
        var request = MKDirectionsRequest()

        if destinationLocation != nil {
            navService.getDirections(destinationLocation: destinationLocation, request: request) { steps in
                for step in steps {
                    self.steps.append(step)
                }
            }
        }
    }
}

{% endhighlight %}


![](https://cdn-images-1.medium.com/max/281/1*4bNiloIDw6WN2Uc5gvv7jg.gif)
{% highlight html %}

{% endhighlight %}
Personal project — August 13th

#### Sources:

[medium.com — Yat&nbsp;Choi](https://medium.com/@yatchoi/getting-started-with-arkit-real-life-waypoints-1707e3cb1da2)

[aviation.stackexchange.com](https://aviation.stackexchange.com/questions/8000/what-are-the-differences-between-bearing-vs-course-vs-direction-vs-heading-vs-tr)

[github.com/ProjectDent/ARKit-CoreLocation](https://github.com/ProjectDent/ARKit-CoreLocation)

[movable-type.co.uk/scripts/latlong.html](http://www.movable-type.co.uk/scripts/latlong.html)

[gis.stackexchange.com](https://gis.stackexchange.com/questions/4906/why-is-law-of-cosines-more-preferable-than-haversine-when-calculating-distance-b)

[opengl-tutorial.org](http://www.opengl-tutorial.org/beginners-tutorials/tutorial-3-matrices)

[math.stackexchange.com](https://math.stackexchange.com/questions/128514/solving-the-arctan-of-an-angle-radians-by-hand)
