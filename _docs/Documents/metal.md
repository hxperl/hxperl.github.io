---
title: Metal
permalink: /docs/metal/
---

# Metal

Render advanced 3D graphics and perform data-parallel computations using graphics processors.

### Overview

Graphics processros (GPU) are designed to quickly render graphics and perform data-parallel calculations. Use the Metal framework when you need to communicate directly with the GPUs available on a device. Apps that render complex scenes or that perform advanced scientific calculations can use this power to achieve maximum performance. Such apps include:

- Games that render sophisticated 3D environments
- Video processing apps, like Final Cut Pro
- Data-crunching apps, such as those used to perform scientific research

Metal works hand-in-hand with other frameworks that supplement its capability. Use *MetalKit* to simplify the task of getting your Metal content onscreen. Use *Metal Performance Shaders* to implement custom rendering functions or to take advantage of a large library of existing functions.
Many high level Apple frameworks are built on top of Metal to take advantage of its performance, including *Core Image*, *SpriteKit* and *SceneKit*. Using one of these high-level frameworks shields you from the details of GPU programming, but writing custom Metal code enables you to achieve the highest level of performance. 

### Basic Tasks

##### Performing Calculations on GPU

- Converting a simple function written in C to Metal Shading Language (MSL), so that it can be run on a GPU
- Finding a GPU
- Preparing the MSL function to run on the GPU by creating a pipeline.
- Creating memory allocations accessible to the GPU to hold data
- Creating a command buffer and encoding GPU commands to manipulate the data
- Committing the buffer to a command queue to make the GPU execute the encoded commands

###### Write a GPU Function to Perform Calculations

To illustrate GPU programming, this app adds corresponding elements of two arrays together, writing the results to a third array. Listing 1 shows a function tht performs this calculation on the CPU, written in C. it loops over the index, calculating one value per iteration of the loop.

**Listing 1** Array addition, written in C

```c
void add_arrays(const float* inA, const float* inB, float* result, int length) {
    for (int index = 0; index < length ; index++)
    {
        result[index] = inA[index] + inB[index];
    }
}
```

Each value is calculated independently, so the values can be safely calculated concurrently. To perform the calculation on the GPU, you need to rewrite this function in Metal Shading Language (MSL). MSL is a variant of C++ designed for GPU programming. In Metal, code that runs on GPUs is called a *shader*, because historically they were first used to calculate colors in 3D graphics. Listing 2 shows a shader in MSL that performs the same calculation as Listing 1. The sample project defines this function in the add.*metal* file. Xcode builds all .*metal* files in the application target and creates a default Metal library, which it embeds in your app. You'll see how to load the default library later in this sample.

**Listing 2** Array addition, written in MSL

```c++
kernel void add_arrays(device const float* inA, device const float* inB, device float* result, uint index [[thread_position_in_grid]])
{
    // the for-loop is replcaed with a collection of threads, each of which
    // calls this function.
    result[index] = inA[index] + inB[index];
}
```

Listing 1 and Listing 2 are similar, but there are some important differences in the MSL version.
Take a closer look at Listing 2.

First, the function adds the kernel keyword, which declares that the function is:
- A *public GPU function*. Public functions are the only functions that your app can see. Public functions also can't be called by other shader functions.
- A *compute function* (also known as a compute kernel), which performs a parallel calculation using a grid of threads.

See Using a Render Pipeline to Render Primitives to learn the other function keywords used to declare public graphics functions.

The *add_arrays* function declares three of its arguments with the device keyword, which says that these pointers are in the device address space. MSL defines several disjoint address spaces for memory. Whenever you declare a pointer in MSL, you must supply a keyword to declare its address space. Use the device address space to declare persistent memory that the GPU can read from and write to.

Listing 2 removes the for-loop from Listing 1, because the function is now going to be called by multiple threads in the compute grid. This sample creates a 1D grid of threads that exactly matches the array's dimensions, so that each entry in the array is calculated by a different thread.

To replace the index previously provided by the for-loop, the function takes a new index argument, with another MSL keyword, *thread_position_in_grid*, specified using C++ attribute syntax. This keyword declares that Metal should calculate a unique index for each thread and pass that index in this argument. Because add_arrays uses a 1D grid, the index is defined as a scalar integer. Even though the loop was removed, Listing 1 and Listing 2 use the same line of code to add the two numbers together. If you want to convert similar code from C or C++ to MSL, replace the loop logic with a grid in the same way.

###### Find a GPU

In your app, a *MTLDevice* object is a thin abstraction for a GPU; you use it to communicate with a GPU. Metal creates a MTLDevice for each GPU. You get the default device object by calling *MTLCreateSystemDefaultDevice()*. 

```c++
id<MTLDevice> device = MTLCreateSystemDefaultDevice();
```

###### Initialize Metal Objects

Metal represents other GPU-related entities, like compiled shaders, memory buffers and textures, as objects. To create these GPU-specific objects, you call methods on a MTLDevice or you call methods on objects created by a MTLDevice. All objects created directly or indirectly by a device object are usable only with that device object. Apps that use multiple GPUs will use multiple device objects and create a similar hierarchy of Metal objects for each.

The sample app uses a custom MetalAdder class to manage the objects it needs to communicate with the GPU. The class's initializer creates these objects and stores them in its proprties. The app creates an instance of this class, passing in the Metal device object to use to create the secondary objects. The MetalAdder object keeps strong references to the Metal objects until it finishes executing.

```c++
MetalAdder* adder = [[MetalAdder alloc] initWithDevice:device];
```

In Metal, expensive initialization tasks can be run once and the results retained and used inexpensively. You rarely need to run such tasks in performance-sensitive code.

###### Get a Reference to the Metal Function

The first thing the initializer does is load the function and prepare it to run on the GPU. When you build the app, Xcode compiles the *add_arrays* function and adds it to a default Metal library that it embeds in the app. You use *MTLLibrary* and *MTLFunction* objects to get information about Metal libraries and the functions contained in them. To get an object representing the *add_arrays* function, ask the MTLDevice to create a MTLLibrary object for the default library, and then ask the library for a MTLFunction object that represents the shader function.

```c++
- (instancetype) initWithDevice: (id<MTLDevice>) device
{
    self = [super init];
    if (self)
    {
        _mDevice = device;

        NSError* error = nil

        // Load the shader files with a .metal file extention in the project

        id<MTLLibrary> defaultLibrary = [_mDevice newDefaultLibrary];
        if (defaultLibrary == nil)
        {
            NSLog(@"Failed to find the default library.");
            return nil;
        }

        id<MTLFunction> addFunction = [defaultLibrary newFunctionWithName:@"add_arrays"];
        if (addFunction == nil)
        {
            NSLog(@"Failed to find the adder function");
            return nil;
        }
    }
}
```

###### Prepare a Metal Pipeline

The function object is a proxy for the MSL function, but it's not executable code. You convert the function into executable code by creating a *pipeline*. A pipeline specifies the steps that the GPU performs to complete a specific task. In Metal, a pipeline is represented by a *pipeline state object*. Because this sample uses a compute function, the app creates a MTLComputePipelineState object.

```c++
_mAddFunctionPSO = [_mDevice newComputePipelineStateWithFunction: addFunction error:&error];
```

A compute pipeline runs a single compute function, optionally manipulating the input data before running the function, and the output data afterwards.

When you create a pipeline state object, the device object finishes compiling the function for this specific GPU. This sample creates the pipeline state object synchronously and returns it directly to the app. Because compiling does take a while, avoid creating pipeline state objects synchronously in performance-sensitive code.

> **Note**
> All of the objects returned by Metal in the code you've seen so far are returned as objects that conform to protocols. Metal defines most GPU-specific objects using protocols to abstract away the underlying implementation classes, which may vary for different GPUs. Metal defines GPU-independent objects using classes. The reference documentation for any given Metal protocol make it clear whether you can implement that protocol in your app.

