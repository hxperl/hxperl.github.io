---
title: Metal
permalink: /docs/metal/
---

Render advanced 3D graphics and perform data-parallel computations using graphics processors.

### Overview

Graphics processros (GPU) are designed to quickly render graphics and perform data-parallel calculations. Use the Metal framework when you need to communicate directly with the GPUs available on a device. Apps that render complex scenes or that perform advanced scientific calculations can use this power to achieve maximum performance. Such apps include:

- Games that render sophisticated 3D environments
- Video processing apps, like Final Cut Pro
- Data-crunching apps, such as those used to perform scientific research

Metal works hand-in-hand with other frameworks that supplement its capability. Use *MetalKit* to simplify the task of getting your Metal content onscreen. Use *Metal Performance Shaders* to implement custom rendering functions or to take advantage of a large library of existing functions.
Many high level Apple frameworks are built on top of Metal to take advantage of its performance, including *Core Image*, *SpriteKit* and *SceneKit*. Using one of these high-level frameworks shields you from the details of GPU programming, but writing custom Metal code enables you to achieve the highest level of performance. 

### Getting the Default GPU

To use the Metal framework, you start by getting a GPU device. All of the objects your app needs to interact with Metal come from MTLDevice that you acquire at runtime. iOS nad tvOS devices have only one GPU that you access by calling MTLCreateSystemDefaultDevice():

```swift
guard let device = MTLCreateSystemDefaultDevice() else {
    fatalError( "Failed to get the system's default Metal device." ) 
}
```

### Setting Up a Command Structure

To get the GPU to perform work on your behalf, you send commands to it. A command performs the drawing, parallel computation, or resource management work your app requires.
The relationship between Metal apps and a GPU is that of a client-server pattern:

- Your Metal app is the client.
- The GPU is the server.
- You make requests by sending commands to the GPU.
- After processing the commands, the GPU can notify your app when it's ready for more work.

![]('https://docs-assets.developer.apple.com/published/861974e544/6411df7f-4f5c-46d6-9573-c2c6dd10fffb.png')