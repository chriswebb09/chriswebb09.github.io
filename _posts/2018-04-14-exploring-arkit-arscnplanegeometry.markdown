---
title: 'Exploring ARKit: ARSCNPlaneGeometry'
layout: post
date: 2018-03-28 22:48
image: /assets/images/ar.png
headerImage: true
tag:
- iOS
- swift
- application data
category: blog
author: chriswebb
description: New blog
---

![](/assets/scan.gif)

If you’ve heard, Apple’ recently released a major update to ARKit, which it has dubbed ARKit 1.5. While much of the focus has been on the new vertical plane detection capabilities, which had been a glaring omission from ARKit’s initial release, much less has been said about other new plane detection features. Lost in among all the excitement were some deeply powerful new features. It’s arguable that the coolest features in Apple’s ARKit release are the the ones that have barely been mentioned.

# So what were they?

Tucked away, we’re some new property additions to ARPlaneAnchor. Take a moment to look these over. Before we were detecting the presence and location/dimensions of a surface, in ARKit 1.5 we are now getting the contours and the lay of the land.

**ARPlaneGeometry** — A 3D mesh describing the shape of a detected plane in world-tracking AR sessions.

This class provides the estimated general shape of a detected plane, in the form of a detailed 3D mesh appropriate for use with various rendering technologies or for exporting 3D assets.
Unlike the ARPlaneAnchor center and extent properties, which estimate only a rectangular area for a detected plane, a plane anchor's geometry property provides a more detailed estimate of the 2D area covered by that plane. For example, if ARKit detects a circular tabletop, the resulting ARPlaneGeometry objects roughly match the general shape of the table. As the session continues to run, ARKit provides updated plane anchors whose associated geometry refines the estimated shape of the plane.
You can use this model to more precisely place 3D content that should appear only on a detected flat surface — for example, to ensure that virtual objects don’t fall off the edge of a table. You can also use this model to create occlusion geometry, which hides other virtual content behind the detected surface in the camera image.
The shape of a plane geometry is always convex. (That is, the boundary polygon for a plane geometry is a minimal convex hull enclosing all points that ARKit recognizes or estimates are part of the plane.)
vertices-A buffer of vertex positions for each point in the plane mesh.

Each float3 value in this buffer represents the position of a vertex in the mesh, in a coordinate system whose origin is defined by the owning plane anchor's transform matrix.
The vertexCount property provides the number of elements in the buffer.
This buffer, together with the triangleIndices buffer, describes a mesh covering the entire surface of the plane. Use this mesh for purposes that involve the filled shape, such as rendering a solid 3D representation of the surface. If instead you only need to know the outline of the shape, see the boundaryVertices property.
boundaryVertices — An array of vertex positions for each point along the plane’s boundary.

Each float3 value in this array represents the position of a vertex along the boundary polygon of the estimated plane, in a coordinate system whose origin is defined by the owning plane anchor's transform matrix.
This array defines the boundary polygon of the plane. Use it for purposes that require only that polygon's definition, such as rendering an outline of the plane's estimated shape or testing whether a point is inside the bounded region. If instead you need the filled shape (for example, to render a solid 3D representation of the surface), see the vertices property.
textureCoordinates — A buffer of texture coordinate values for each point in the plane mesh.

Each float2 value in this buffer represents the UV texture coordinates for the vertex at the corresponding index in the vertices buffer.

**ARSCNPlaneGeometry** — A SceneKit representation of the 2D shape of a plane, for use with plane detection results in an AR session.

This class is a subclass of SCNGeometry that wraps the mesh data provided by the ARPlaneGeometry class. You can use ARSCNPlaneGeometry to quickly and easily visualize the plane shape estimates provided by ARKit in a SceneKit view.
As your AR session continues to run, ARKit provides refined estimates of a detected plane's 2D shape. Use the updateFromPlaneGeometry: method to incorporate those refinements into the plane's SceneKit representation.
It’s a rudimentary 3D scanner, albeit with less accuracy than you would expect from one with laser sensors.
Regardless, this is huge. For years people have talked about this moment when people could quickly and conveniently create a 3D scan from their environment, and the possibilities that could bring. Well now we’re here (albeit with some caveats)

To begin with, we were already headed here, with or without the latest update to plane geometry. Apple’s has reportedly been looking to add back facing 3D cameras to its next iPhone models to enhance augmented reality, so this is just a preview of what we are likely to see this summer. It’s also not exactly the smoothest user experience and to get objects fully detected would be time-consuming. But, it looks like there is a lot to look forward to this summer! If I had to guess, Apple might be rolling out usable surface detection for all types of surfaces/shapes. Let’s hope that 2018 sees the end of all occlusion dilemmas in ARKit as well!

=====

# Setup



{% highlight swift %}

import UIKit
import SceneKit
import ARKit

class ViewController: UIViewController {

    @IBOutlet var sceneView: ARSCNView!

    private var planeId: Int = 0

    let standardConfiguration: ARWorldTrackingConfiguration = {
        let configuration = ARWorldTrackingConfiguration()
        configuration.planeDetection = [.horizontal, .vertical]
        configuration.isLightEstimationEnabled = true
        return configuration
    }()

    override func viewDidLoad() {
        super.viewDidLoad()
    }

    func runSession() {
        sceneView.delegate = self
        sceneView.session.run(standardConfiguration)
        #if DEBUG
        sceneView.showsStatistics = true
        sceneView.debugOptions = [
            ARSCNDebugOptions.showFeaturePoints,
            ARSCNDebugOptions.showWorldOrigin
        ]
        #endif
    }

    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        runSession()
    }
}

{% endhighlight %}




## SCNNode Extension




{% highlight swift %}

extension SCNNode {

    static func createPlaneNode(planeAnchor: ARPlaneAnchor, id: Int) -> SCNNode {
        let scenePlaneGeometry = ARSCNPlaneGeometry(device: MTLCreateSystemDefaultDevice()!)
        scenePlaneGeometry?.update(from: planeAnchor.geometry)
        let planeNode = SCNNode(geometry: scenePlaneGeometry)
        planeNode.name = "\(id)"
        switch planeAnchor.alignment {
        case .horizontal:
            planeNode.geometry?.firstMaterial?.diffuse.contents = UIColor.blue.withAlphaComponent(0.3)
        case .vertical:
            planeNode.geometry?.firstMaterial?.diffuse.contents = UIColor.cyan.withAlphaComponent(0.5)

        }
        return planeNode
    }

}

{% endhighlight %}



## ARSCNViewDelegate



{% highlight swift %}

// MARK: - ARSCNViewDelegate


extension ViewController: ARSCNViewDelegate {


func renderer(_ renderer: SCNSceneRenderer, didAdd node: SCNNode, for anchor: ARAnchor) {
        guard let planeAnchor = anchor as? ARPlaneAnchor else { return }
        let planeNode = SCNNode.createPlaneNode(planeAnchor: planeAnchor, id: planeId)
        planeId += 1
        node.addChildNode(planeNode)
    }

    func renderer(_ renderer: SCNSceneRenderer, didUpdate node: SCNNode, for anchor: ARAnchor) {
        guard let planeAnchor = anchor as? ARPlaneAnchor else { return }
        node.enumerateChildNodes { child, _ in
            child.removeFromParentNode()
        }
        let planeNode = SCNNode.createPlaneNode(planeAnchor: planeAnchor, id: planeId)
        planeId += 1
        node.addChildNode(planeNode)
    }

    func renderer(_ renderer: SCNSceneRenderer, didRemove node: SCNNode, for anchor: ARAnchor) {
        guard let _ = anchor as? ARPlaneAnchor else { return }
        node.enumerateChildNodes { child, _ in
            child.removeFromParentNode()
        }
    }
}
{% endhighlight %}
