# GLSL
## Language Structure
version, input, output, uniform function, main function
```c
#version
version_number in type in_variable_name;
in type in_variable_name;
out type out_variable_name;
uniform type uniform_name;
int main() { 
// 处理输入并进行一些图形操作 ... 
// 输出处理过的结果到输出变量 
out_variable_name = weird_stuff_we_processed;
}
```
input variables are also called Vertex Attributes, the same as we mentioned in [[Drawing triangle]].
limitations: minimum 16 vec4 is affordable, `GL_MAX_VERTEX_ATTRIBS` records the real max limitation. *(got 16 on my Laptop)*
## Data Structure
### vectors
starting with the basic type:

| prefix | type |
| :- | :- |
| *empty, which starts with v* | vertical |
| b | bool |
| i | int |
|u|uint|
|d|double|

concatenate with "vec", then followed by a integer 2, 3, or 4, indicating the dimension.
an example is "vec3". "bvec4" is also a legal one, with 4d 0-1 coordinates

vectors can be **swizzling** in an interesting way, like:
```c
vec2 someVec;
vec4 differentVec = someVec.xyxx;
vec3 anotherVec = differentVec.zyw;
vec4 otherVec = someVec.xxxx + anotherVec.yxzy;
```

it allows a convenient way to computing the coordinates.

## Data Transferring : I/O
Location is where a Shader variable been built on, and any data that goes into the shader must specify a location. Typically, `layout (location = x) in vec3 var` can set `var` at the location of `x`, so that Vertex Attributes at location `x` is parsing correctly to `var`.
```c
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);// 颜色属性
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)(3* sizeof(float)));  // offset
glEnableVertexAttribArray(1);
```
![](https://learnopengl-cn.github.io/img/01/05/vertex_attribute_pointer_interleaved.png)
### Local Function-like I/O format
Shader receives data from a previous shader by `in` and `out` settings. Only when both the type and the name fit exactly the same can the data be transferred.
### Global Uniform Variable
It is global in all shader programs. The value of uniform variable can be set in CPU context, which is not in GLSL.
Uniform Variable has an undetermined location in shader, so `glGetUniformLocation()` is needed to get the location of the variable. Also, uniform variable is activated when `useProgram(shaderProgram)` is called, so no updating options can be done before using the shader.

## Custom Class Implement
