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

