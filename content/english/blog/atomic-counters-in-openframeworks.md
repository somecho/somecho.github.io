---
title: "Atomic Counters in OpenFrameworks"
subtitle: "Counting inside shaders"
date: "2021-03-27"
tags: ["c++","OpenFrameworks","GLSL","OpenGL"]
---
Since OpenGL 4.2, [atomic
counters](https://www.khronos.org/opengl/wiki/Atomic_Counter) have been a core
feature. They help solve a variety of problems, like counting the number of red
pixels in a fragment shader or acting as a form of shared memory that can be
altered by shaders.

The use case I’m interested in is to create a particle system using compute
shaders. This means that I need to keep track of the number of particles that
are alive or dead at any given time. To do that in the GPU, I can use atomic
counters.

Looking online, there were only a handful of tutorials that taught how to use
atomic counters in OpenFrameworks like [this
one](https://cvl-robot.hateblo.jp/entry/2017/05/19/214241). But it required the
use of the verbose OpenGL api. [This
writer](https://note.com/omakazu/n/n1a264820972c) even says that OpenFrameworks
doesn’t have an API for it. But as I found out, there is! This article is just
a short and sweet walk through on how to set that up.

{{< image 
src="https://cdn.sanity.io/images/4hxtp24c/production/3655b59ffa97a0c164b9b81ca2ccad9bc386ee39-600x600.gif"
alt="A GIF of a slime mode simulation, also known as physarum simulation. It is rainbow colored, depiciting the direction in which the particles are moving."
caption="slime mold simulation, hue mapped to angle of movement"
>}}

## Setting up the buffers

There is only one data type used with atomic counters and that is `atomic_uint` . The data type that maps to this is `GLuint`. So we need to create a `GLuint` vector of size 1.

```cpp
vector<GLuint> counter;  
counter.resize(1);
```

The reason we’re using a vector is because OpenFrameworks’ `ofBufferObject` class allows us to allocate a buffer with the data itself. So let’s create an `ofBufferObject` and allocate it. After allocating it, we bind it, telling OpenGL that it is an atomic counter buffer at the binding point 0.

```cpp
ofBufferObject counterBuffer;  
counterBuffer.allocate(counter, GL_DYNAMIC_DRAW);  
counterBuffer.bindBase(GL_ATOMIC_COUNTER_BUFFER, 0);
```

## Setting up the shader

I’m going to make a particle system, so I will be using compute shaders. The setup looks like this:

```cpp
ofShader compute;  
compute.setupShaderFromFile(GL_COMPUTE_SHADER, "shader.comp");  
compute.linkProgram();
```

For this article, I’m making a fake particle system with 69 particles.

```cpp
compute.begin();  
compute.dispatchCompute(69,1,1);  
compute.end();
```

## Writing the shader

Since we bound the atomic counter buffer to the binding point 0 earlier, we can access it in the shader. To access data at binding points, we can use the `layout` qualifier in GLSL. One thing to note is that the atomic counter type is opaque. This means that we need to declare it as a uniform in the shader.

After doing this, we increment the counter. We can only use special functions for the atomic counter. A full list is found [here](https://www.khronos.org/opengl/wiki/Atomic_Counter).

```glsl 
// fragment shader

layout(binding = 0) uniform atomic_uint counter;  
layout(local_size_x = 1, local_size_y = 1, local_size_z = 1) in;
void main(){  
    atomicCounterIncrement(counter);  
}
```

This is our fake particle.

## Reading the data from ofBufferObject

For debugging, we might want to check the counter in our host application. Since we created 69 particles, we expect that the counter reads 69 since we incremented it 69 times.

There are many ways of reading data from an `ofBufferObject`, but the easiest way I’ve found is this:

```cpp
GLuint result[1];  
glGetNamedBufferSubData(counterBuffer.getId(), 0, sizeof(GLuint), result);
```

The method `glGetNamedBufferSubData` loads the data from the GPU into RAM. Since we only have a buffer with a single unsigned integer (4 bytes), this shouldn’t affect our performance. But if you’re reading a lot of data, this can bottleneck your application!

Anyways, the method gives you a pointer to the data. I’ve found that initializing the output data as an array instead of pointer works best. After calling the method, we can read the data with a simple `cout << result[0] << endl;`. The output should be 69.

## Summary

That’s it for atomic counters! This is a simple way to get atomic counters
working without resorting to the verbose OpenGL API. I hope this has helped!
