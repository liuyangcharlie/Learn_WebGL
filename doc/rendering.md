# Start hacking WebGL

## Rendering

After initializing the shaders, we could start working on the objects that we want to draw in the 3D space.

### Concepts

#### Camera
In a 3D space, the "camera" is a concept that we could never neglect. To render a scene, we always to specify where the the "camera" is in the scene first. Because the position of the "camera" determines what we could see in the 3D space, which is what to be renderred to the screen eventually.

The code related to the "camera" below shows the properties of a "camera".
```javascript
// Create a perspective matrix, a special matrix that is
// used to simulate the distortion of perspective in a camera.
// Our field of view is 45 degrees, with a width/height
// ratio that matches the display size of the canvas
// and we only want to see objects between 0.1 units
// and 100 units away from the camera.

const fieldOfView = 45 * Math.PI / 180;   // in radians
const aspect = gl.canvas.clientWidth / gl.canvas.clientHeight;
const zNear = 0.1;
const zFar = 100.0;
const projectionMatrix = mat4.create();

// note: glmatrix.js always has the first argument
// as the destination to receive the result.
mat4.perspective(projectionMatrix,
               fieldOfView,
               aspect,
               zNear,
               zFar);

```
From the code above, we could make a further conclusion that **the settings of a "camera" determines how the vertex shader would calculate the projection on the original vertex data**.

> We set a field of view of 45°, with a width to height ratio that match the display dimensions of our canvas. We also specify that we only want objects between 0.1 and 100 units from the camera to be rendered.

#### 3D Space

Let's start from a [2D content](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API/Tutorial/Adding_2D_content_to_a_WebGL_context) first.

The first example in rendering section is to draw a simple *square untextured plane*. As the words in italic style says, it is **a plane with the shape of square and wihout any textures**.

Note that, even though we are gonna draw a 2D content(the square plane), we are still doing this in the 3D drawing context(3D space) created in last steps.

That is:
> we're putting it directly **in front of the camera** perpendicular to the **view direction**.

Vertices go though a series of transformations in order to determine where they are on the screen. The stages are generally:

model space
world space
camera space (aka view space and eye space)
clip space
screen space


##### Create objects

- We need to firstly create the buffer that contains its **vertex positions** and put the vertex positions in it.
	```javascript
    // initBuffers()
    function initBuffers(gl) {

        // Create a buffer for the square's positions.
        const positionBuffer = gl.createBuffer();

        // Select the positionBuffer as the one to apply buffer
        // operations to from here out.
        gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);

        // Now create an array of positions for the square.
        const positions = [
            -1.0,  1.0,
             1.0,  1.0,
            -1.0, -1.0,
             1.0, -1.0,
        ];

        // Now pass the list of positions into WebGL to build the
        // shape. We do this by creating a Float32Array from the
        // JavaScript array, then use it to fill the current buffer.
        gl.bufferData(gl.ARRAY_BUFFER, jnew Float32Array(positions), gl.STATIC_DRAW);

        return {
            position: positionBuffer,
        };
    }
    ```
    This is an important step.
    > this routine will be augmented to create more -- and more complex -- 3D objects.

- Then, we should specify how the vertex shader should pull data out of the buffers.
    There are 4 sources a shader can receive data.
    - Attributes and Buffers
    - Uniforms
    - Textures
    - Varyings

- After we've created a shader program we need to look up the locations that WebGL assigned to our inputs.

- 
    > Once the shaders are established, the locations are looked up, and the square plane's vertex positions put in a buffer, we can actually render the scene.

    3 elements are mentioned in above quotes:
        1. shaders
        2. locations
        3. vertex data(vertex coordinates)

- locations

- perspective matrix

- field of view

#### Matrix utility
As the matrix operations are unavoidable when we do the projection, some matrix unitlity becomes more and more helpful in this case. [glMatrix](http://glmatrix.net/) is a popular library handling those operations.


#### Render API

> Nearly all of the entire WebGL API is about setting up state for these pairs of functions(vertex shaders & fragment shaders, the shader program) to run. For each thing you want to draw you setup a bunch of state then execute a pair of functions by calling gl.drawArrays or gl.drawElements which executes your shaders on the GPU.

- gl.getAttribLocation
- gl.getUniformLocation
- gl.depthFunc
- mat4.perspective
- gl.bindBuffer
- gl.vertexAttribPointer
    specify how the vertex shader pull data out of the buffer
    ```javascript
    gl.vertexAttribPointer(
        programInfo.attribLocations.vertexPosition,
        // pull out 2 values per iteration, only 2 value per position in this example
        numComponents,
        // how the data is stored in the buffer, in this example, the data in the buffer is 32bit floats(gl.FLOAT)
        type,
        // whether the vertex shader should handle lightings, etc. with the normals
        normalize,
        // how many bytes to get from one set of values to the next, stride = 0 means use type and numComponents above
        stride,
        // how many bytes inside the buffer to start from
        offset);
    ```
- gl.enableVertexAttribArray
- gl.useProgram
    Tell the WebGL engine which shader program(vertex shader & fragment shader) would be used when drawing.
- gl.uniformMatrix4fv
- gl.drawArrays
    > Finally we draw the object by calling the drawArrays() method.
    ```javascript
    gl.drawArrays(gl.TRIANGLE_STRIP, offset, vertexCount);
    ```
















