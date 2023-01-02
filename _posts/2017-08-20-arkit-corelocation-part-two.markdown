---
title: "ARKit and CoreLocation: Part Two"
layout: post
date: 2017-08-20 01:48
image: /assets/images/ARKit.png
headerImage: false
tag:
- Swift
- UIView
- iOS
- ARKit
- Core Location
category: blog
author: chriswebb
description: "ARKit and CoreLocation: Part Two"
---


## Navigation With Linear Algebra


![](https://cdn-images-1.medium.com/max/281/1*d1q0THvPu5mTH9_9U2T6Hg.gif)


Demo Code

ARKit and CoreLocation: Part One

ARKit and CoreLocation: Part Two

ARKit and CoreLocation: Part Three


### Maths and Calculating Bearing Between Coordinates

![](https://cdn-images-1.medium.com/max/704/1*GgodwdTWvPkNw53Etw49mA.png)<figcaption><a href="https://www.khanacademy.org/math/precalculus/trig-equations-and-identities-precalc">Source</a></figcaption>

_If you haven’t had a chance, checkout_ [**_part one_**](https://dev.to/chriswebb09/arkit-and-corelocation-part-one-gij-temp-slug-4475628)_&nbsp;first._

Now we need to figure out how to get the bearing (the angle) between two coordinates. Finding the bearing set us up to create a rotation transformation to orient our node in the proper direction.

![](https://cdn-images-1.medium.com/max/500/1*_NesYbqz4HqX-it5ieB0Lw.png)<figcaption><a href="https://betterexplained.com/wp-content/uploads/angles/degrees_vs_radians.png">Source</a></figcaption>

#### Definition

**radian:** The **radian** is a unit of angular measure defined such that an angle of one **radian** subtended from the center of a unit circle produces an arc with arc length 1. One radian is equal to 180/π degrees so to convert from radians to degrees, multiply by&nbsp;180/π.

![](https://cdn-images-1.medium.com/max/252/1*qTGgCClcy08LejChY6X35Q.gif)

{% highlight swift %}
extension Double {
    func toRadians() -> Double {
        return self * .pi / 180.0
    }

    func toDegrees() -> Double {
        return self * 180.0 / .pi
    }
}
{% endhighlight %}

#### Haversine

One of the drawbacks of the Haversine formula is that it can get less accurate over longer distances. If we were designing a navigation system for a commercial airliner that might be a problem, but it is unlikely that the distances will be long enough to make a difference for an ARKit&nbsp;demo.

#### Definition

**Azimuth:** is a angular measurement on spherical coordinate system.

![](https://cdn-images-1.medium.com/max/440/1*xHKkiU8ExCzAU7PaO0eMKw.png)<figcaption>Spherical triangle solved by the law of haversines — <a href="https://en.wikipedia.org/wiki/Haversine_formula">source</a></figcaption>

> If you have two different latitude — longitude values of two different point on earth, then with the help of **Haversine Formula** , you can easily compute the great-circle distance (The shortest distance between two points on the surface of a&nbsp;Sphere).

— [Movable-Type](http://www.movable-type.co.uk/scripts/latlong.html)

![](https://cdn-images-1.medium.com/max/596/1*ollegMltPGgZfsDNGdC_Dg.png)<figcaption>Great circle distance — <a href="https://en.wikipedia.org/wiki/Great-circle_distance">source</a></figcaption>

```
sin = opposite/hypotenuse

cos = adjacent/hypotenuse

tan = opposite/adjacent
```

**atan2:** An arctangent or inverse tangent function with two arguments.

```
tan 30 = 0.577

Means: The tangent of 30 degrees is 0.577

arctan 0.577 = 30

Means: The angle whose tangent is 0.577 is 30 degrees.
```

![](https://cdn-images-1.medium.com/max/554/1*hPuVkyMJNrd41pFHj7r2Fw.png)

#### Keys

```
‘R’ is the radius of Earth

‘L’ is the longitude

‘θ’ is latitude

‘β‘ is bearing

‘∆‘ is delta / change in
```
> In general, your current heading will vary as you follow a great circle path (orthodrome); the final heading will differ from the initial heading by varying degrees according to distance and latitude (if you were to go from say 35°N,45°E (≈ Baghdad) to 35°N,135°E (≈ Osaka), you would start on a heading of 60° and end up on a heading of&nbsp;120°!).> This formula is for the initial bearing (sometimes referred to as forward azimuth) which if followed in a straight line along a great-circle arc will take you from the start point to the end&nbsp;point
#### Formula

```
β = atan2(X,Y)

where, X and Y are two quantities and can be calculated as:

X = cos θb * sin ∆L

Y = cos θa * sin θb — sin θa * cos θb * cos ∆L
```

{% gist https://gist.github.com/chriswebb09/cb77fdaad1fa09551e523f79de5d416d %}
{% highlight swift %}
{% endhighlight %}

### Getting Coordinates For&nbsp;Distance

![](https://cdn-images-1.medium.com/max/226/1*dlgcCYgkhmbFgRpHrvO8jA.png)

While [**MKRoute**](https://developer.apple.com/documentation/mapkit/mkroute) gives us a good framework for building an ARKit navigation experience, the steps along the route can be space far enough apart that it ruins the experience. To mitigate this we need to iterate through our steps and generate coordinate for distance intervals between&nbsp;them.

> Given a start point, initial bearing, and distance, this will calculate the destina­tion point and final bearing travelling along a (shortest distance) great circle&nbsp;arc.
```
‘d‘ being the distance travelled

‘R’ is the radius of Earth

‘L’ is the longitude

‘φ’ is latitude

‘θ‘ is bearing (clockwise from north)

‘δ‘ is the angular distance d/R
```

#### Formula

```
φ2 = asin( sin φ1 ⋅ cos δ + cos φ1 ⋅ sin δ ⋅ cos θ )

L2 = L1 + atan2( sin θ ⋅ sin δ ⋅ cos φ1, cos δ − sin φ1 ⋅ sin φ2 )
```

{% highlight swift %}
let metersPerRadianLat: Double = 6373000.0
let metersPerRadianLon: Double = 5602900.0

extension CLLocationCoordinate2D {

  // adapted from https://github.com/ProjectDent/ARKit-CoreLocation/blob/master/ARKit%2BCoreLocation/Source/CLLocation%2BExtensions.swift

    func coordinate(with bearing: Double, and distance: Double) -> CLLocationCoordinate2D {

        let distRadiansLat = distance / metersPerRadianLat  // earth radius in meters latitude
        let distRadiansLong = distance / metersPerRadianLon // earth radius in meters longitude

        let lat1 = self.latitude.toRadians()
        let lon1 = self.longitude.toRadians()

        let lat2 = asin(sin(lat1) * cos(distRadiansLat) + cos(lat1) * sin(distRadiansLat) * cos(bearing))
        let lon2 = lon1 + atan2(sin(bearing) * sin(distRadiansLong) * cos(lat1), cos(distRadiansLong) - sin(lat1) * sin(lat2))

        return CLLocationCoordinate2D(latitude: lat2.toDegrees(), longitude: lon2.toDegrees())
    }   
}
{% endhighlight %}

### Three Dimensional Transformations

```
matrix × matrix = combined matrix

matrix × coordinate = transformed coordinate
```

Intuitively, it might seem obvious that three dimensions should be represented in [3x3] matrices ([x, y, z]). However there is an extra matrix row, so three-dimensional graphic use [4x4] matrices: [x, y, z,&nbsp;w].

#### Really…W?

Yup W. This fourth dimension is called “projective space,” and the coordinates in the projective space are called “homogeneous coordinates.” When w is equal to 1, it does not affect x, y or z because the vector is a position in space. When W=0, the coordinate represents a point at infinity (a vector with infinite length) which is used to represent a direction.

#### Rotation Matrix

To get our objects points in the right direction we need to implement a rotation transformation.

> A rotation transformation rotates a vector around the origin _(0,0,0)_ using a given _axis_ and&nbsp;_angle_

![](https://cdn-images-1.medium.com/max/204/1*71mr0tiJmZNpy3VJCWczpw.png)

{% highlight swift %}
import GLKit.GLKMatrix4
import SceneKit

class MatrixHelper {

    //    column 0  column 1  column 2  column 3
    //        cosθ      0       sinθ      0    
    //         0        1         0       0    
    //       −sinθ      0       cosθ      0    
    //         0        0         0       1    

    static func rotateAroundY(with matrix: matrix_float4x4, for degrees: Float) -> matrix_float4x4 {
        var matrix : matrix_float4x4 = matrix

        matrix.columns.0.x = cos(degrees)
        matrix.columns.0.z = -sin(degrees)

        matrix.columns.2.x = sin(degrees)
        matrix.columns.2.z = cos(degrees)
        return matrix.inverse
    }
}
{% endhighlight %}

Most rotations in with 3D graphics and ARKit in particular revolve around the camera transform. However, we’re not concerned about placing our object in relation to the POV, we are interested in placing it in relation to our current location and rotate based on the&nbsp;compass.

#### Translation Matrix
> Rotation and scaling transformation matrices only require three columns. But, in order to do translation, the matrices need to have at least four columns. This is why transformations are often 4x4 matrices. However, a matrix with four columns can not be multiplied with a 3D vector, due to the rules of matrix multiplication. A four-column matrix can only be multiplied with a four-element vector, which is why we often use homogeneous 4D vectors instead of 3D&nbsp;vectors.

![](https://cdn-images-1.medium.com/max/221/1*9XT1QlNvlvjOS9VGOeydpg.png)

{% highlight swift %}
class MatrixHelper {

    //    column 0  column 1  column 2  column 3
    //         1        0         0       X          x        x + X*w 
    //         0        1         0       Y      x   y    =   y + Y*w 
    //         0        0         1       Z          z        z + Z*w 
    //         0        0         0       1          w           w    

    static func translationMatrix(translation : vector_float4) -> matrix_float4x4 {
        var matrix = matrix_identity_float4x4
        matrix.columns.3 = translation
        return matrix
    }
}
{% endhighlight %}

### Putting It All&nbsp;Together

#### Combining Matrix Transforms

The order in which you combine your transforms matters. When you combine your transforms you should do so in following order:

```
Transform = Scaling * Rotation * Translation
```

#### SIMD (Single Instruction Multiple&nbsp;Data)

So you may have seen simd\_mul operation before in regards to matrices. So what is it? It’s pretty straight forward: simd\_mul: single instruction multiple data multiplications. In iOS 8 and OS X Yosemite, Apple tacked on a library called simd for implementing SIMD (single-instruction, multiple-data) arithmetic for scalars, vectors, and matrices.

> Enter _simd.h_: This built-in library gives us a standard interface for working with 2D, 3D, and 4D vector and matrix operations across various processors on OS X and iOS. It automatically falls back to software routines if the CPU doesn't natively support the given operation (for example splitting up a 4-lane vector into two 2-lane operations). It also has the bonus of easily transferring data between the GPU and CPU using&nbsp;Metal.> SIMD is a technology that spans the gap between GPU shaders and old-fashioned CPU instructions, allowing the CPU to issue a single instruction that crunches chunks of data in&nbsp;parallel> — [www.russbishop.net](http://www.russbishop.net/swift-2-simd)

So when you see sims\_mul being performed, that’s what it means. One thing you should note: **simd\_mul** performs operations in the right to left&nbsp;order.

{% gist https://gist.github.com/chriswebb09/3405c42c17bb768ba5cb6c42ba4c7c48 %}
{% highlight swift %}
{% endhighlight %}

### Creating Our SCNNode&nbsp;Subclass

![](https://cdn-images-1.medium.com/max/750/1*lMco_E0NNQ7jFrqKT7Wvsg.jpeg)

The next thing we should do is create our node class. We’ll subclass SCNNode and give it a **title** property which is a _string_, an **anchor** property that is an **_optional_** _ARAnchor_ that updates the position when it is set. Finally, we’ll give our BaseNode class a **location** property which is a _CLLocation_.

{% highlight swift %}
import SceneKit
import ARKit
import CoreLocation

class BaseNode: SCNNode {

    let title: String

     var anchor: ARAnchor? {
        didSet {
            guard let transform = anchor?.transform else { return }
            self.position = positionFromTransform(transform)
        }
    }

    var location: CLLocation!

    init(title: String, location: CLLocation) {
        self.title = title
        super.init()
        let billboardConstraint = SCNBillboardConstraint()
        billboardConstraint.freeAxes = SCNBillboardAxis.Y
        constraints = [billboardConstraint]
    }

    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
{% endhighlight %}

We’ll need to add methods to create the sphere graphics. We’ll implement something similar to the sphere in part one, but modified for our new conditions. Since we only want text over the spheres from MKRouteStep instructions we should create to&nbsp;methods:

{% highlight swift %}
import SceneKit
import ARKit
import CoreLocation

class BaseNode: SCNNode {

  // Basic sphere graphic

   func createSphereNode(with radius: CGFloat, color: UIColor) -> SCNNode {
        let geometry = SCNSphere(radius: radius)
        geometry.firstMaterial?.diffuse.contents = color
        let sphereNode = SCNNode(geometry: geometry)
        let trailEmitter = createTrail(color: color, geometry: geometry)
        addParticleSystem(trailEmitter)
        return sphereNode
   }

  // Add graphic as child node - basic

   func addSphere(with radius: CGFloat, and color: UIColor) {
        let sphereNode = createSphereNode(with: radius, color: color)
        addChildNode(sphereNode)
    }

   // Add graphic as child node - with text

    func addNode(with radius: CGFloat, and color: UIColor, and text: String) {
        let sphereNode = createSphereNode(with: radius, color: color)
        let newText = SCNText(string: title, extrusionDepth: 0.05)
        newText.font = UIFont (name: "AvenirNext-Medium", size: 1)
        newText.firstMaterial?.diffuse.contents = UIColor.red
        let _textNode = SCNNode(geometry: newText)
        let annotationNode = SCNNode()
        annotationNode.addChildNode(_textNode)
        annotationNode.position = sphereNode.position
        addChildNode(sphereNode)
        addChildNode(annotationNode)
    }
}
{% endhighlight %}

When we update our position, we take the anchor’s matrix transform and use the x, y and z values from the last column, which are the values of the position transform.

{% highlight swift %}
class BaseNode: SCNNode {

    var anchor: ARAnchor? {
        didSet {
            guard let transform = anchor?.transform else { return }
            self.position = positionFromTransform(transform)
        }
    }

    // Setup

    func positionFromTransform(_ transform: matrix_float4x4) -> SCNVector3 {

           //    column 0  column 1  column 2  column 3
           //         1        0         0       X       
           //         0        1         0       Y      
           //         0        0         1       Z       
           //         0        0         0       1    

        return SCNVector3Make(transform.columns.3.x, transform.columns.3.y, transform.columns.3.z)
    }
}
{% endhighlight %}

### Sources:

[sites.math.washington.edu/~king/coursedir/m308a01/Projects/m308a01-pdf/yip.pdf](https://sites.math.washington.edu/~king/coursedir/m308a01/Projects/m308a01-pdf/yip.pdf)

[khanacademy.org/math/linear-algebra/matrix-transformations/linear-transformations/a/visualizing-linear-transformations](https://www.khanacademy.org/math/linear-algebra/matrix-transformations/linear-transformations/a/visualizing-linear-transformations)

[edwilliams.org/avform.htm#LL](http://www.edwilliams.org/avform.htm#LL)

[tomdalling.com/blog/modern-opengl/04-cameras-vectors-and-input/](https://www.tomdalling.com/blog/modern-opengl/04-cameras-vectors-and-input/)

[movable-type.co.uk](http://www.movable-type.co.uk/scripts/latlong.html)

[tomdalling.com](http://www.tomdalling.com/blog/modern-opengl/explaining-homogenous-coordinates-and-projective-geometry/)

[Medium: Yat&nbsp;Choi](https://medium.com/@yatchoi/getting-started-with-arkit-real-life-waypoints-1707e3cb1da2)

[opengl-tutorial.org](http://www.opengl-tutorial.org/beginners-tutorials/tutorial-3-matrices/#cumulating-transformations)

[open.gl/transformations](https://open.gl/transformations)

* * *
