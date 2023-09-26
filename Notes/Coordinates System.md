# Different Coordinates
1. Object Coords
2. World Coords
3. Projection Coords
4. Clip Coords
5. Screen Coords
	- Transform the normalized coordinates into the resolution preset in `glViewport(width, height)`
![coordinates system](https://learnopengl-cn.github.io/img/01/08/coordinate_systems.png)
# Projection Matrix
To **clip** the view coordinates into the limited range.
Ortho: the same as view transform.
```c
glm::mat4 ortho =
	glm::ortho(0.0f, 800.0f, 0.0f, 600.0f, 0.1f, 100.0f);
	// the range of [left, right], [top, bottom], [near, far]
```
Perspective: human eyes.

