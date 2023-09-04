# Rutine 
## Data input into Vertex Buffer Object (VBO)
```	c
	unsigned int VBO_id;
	glGenBuffers(1, &VBO_id);
	glBindBuffer(GL_ARRAY_BUFFER, VBO_id);
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```
`vertices` contains the input data
- can be cordinates or other attributes (e.g. alpha val or coloum number etc.)
- `GL_STATIC_DRAW` indicates that the rendering is static and only need to draw once.
## Attributes are arranged by Vertex Array Object (VAO)
Attributes is about the way you read the input, containing **offest**, **strides**, etc.
![VAO](https://learnopengl-cn.github.io/img/01/04/vertex_array_objects.png)
### Different VAOs represent different way to "pick" the data in VBO.
```c
	unsigned int VAO;
	glGenVertexArrays(1, &VAO);
	glBindVertexArray(VAO);
```
After init, anything done to ArrtibutePointer will be implement on VAO binded.
```c
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);
```

## Reuse of the vertices data will be done by Element Buffer Object (EBO)
EBO accept a indices array, which indicates the drawing order of vertices in the VBO.
```c
    unsigned int EBO;
	glGenBuffers(1, &EBO);
	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
	glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);

```
changing `GL_ARRAY_BUFFER` into `GL_ELEMENT_ARRAY_BUFFER` to announce a EBO.
## Shader
Basically, only 2 specific kinds of shader is needed to be defined by developer. One is `VertexShader`, and another is `FragmentShader`
### Writing a Shader with GL Shader Language (GLSL)
```c
const char* vertexShaderSource = 
"#version 330 core\n"
 "layout (location = 0) in vec3 aPos;\n" // in vec3 aPos means it takes a input of 3d vec
 "void main()\n"
 "{\n"
 "   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
								 "}\0";
const char* fragmentShaderSource = 
"#version 330 core\n"
"out vec4 FragColor;\n" // out vec4 means it gives out a 4d vec as output
"void main()\n"
"{\n"
"FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n"
"}\0";
```
### Compile Shader
Basically using `glCreateShader` to build an empty shader, then use `glShaderSource` to inject the GLSL code, and `glCompileShader` to compile it.
```c
    // compile vertex shader
    unsigned int vertexShader;
    vertexShader = glCreateShader(GL_VERTEX_SHADER);
    // get source code
	glShaderSource(vertexShader, 1, &vertexShaderSource, NULL); // 1 is to say only 1 string source to process
	glCompileShader(vertexShader);
```
*Same oprations on Fragment Shader*
### Build a Shader Program by Linking Shaders
When drawing a window, a shader program is called to do the shading task, and OpenGL need a full shader program which involves VertexShader, FragmentShader, and GeometricShader (optional, built-in), so we use the following code to create such a program.
```c
	unsigned int shaderProgram;
	shaderProgram = glCreateProgram();

    glAttachShader(shaderProgram, vertexShader);
    glAttachShader(shaderProgram, fragmentShader);
	glLinkProgram(shaderProgram);       // easy to be comprehended
```
once compilation is done, we can use it in the drawing state:
```c
while(/*Window is not about to close*/){
	glUseProgram(shaderProgram);
	glBindVertexArray(VAO);
	
	// Draw the object.
	// Option 1: VBO draw, use data in VBO directly in order.
	//glDrawArrays(GL_TRIANGLES, 0, 3); // location 0, 3 vertices

	// Option 2: EBO draw, use data in the order of indices in the EBO
	glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

	// unbind
	glBindVertexArray(0);

	// event catcher or sth.
	// ...
}
```