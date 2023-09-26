# Rotation
## Matrix Transform
```c
	// init a new identity mat
	glm::mat4 trans = glm::mat4(1.0f)

	// shift position with (0.5, -0.5, 0)
	trans = glm::translate(trans, glm::vec3(0.5f, -0.5f, 0));

	// rotation with time towards z-axis
	trans = glm::rotate(trans, (float)glfwGetTime(), glm::vec3(0.0f, 0.0f, 1.0f));
	// to specify the degree, use this to replace the second parameter
	glm::radians(float:degrees)

	// use uniform to transmit the trans mat into shader.
    glUniformMatrix4fv(transform_location, 1, GL_FALSE, glm::value_ptr(trans));
```
