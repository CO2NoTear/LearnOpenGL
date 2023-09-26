# Camera Coordinates
Need for 3 vectors:
- CameraPos: the position of the camera
- CameraFront: the looking direction of the camera
- CameraUp (usually WorldUp with \[0,0,1]): the up direction of the camera
Generating a **look at** matrix with the three vectors by invoking `glm::lookAt()` function.
`lookAt` takes 3 parameters: `eye`, the position of the camera, `center`, the point where the camera is looking at, and `up`, literally.
therefore, in the form of camera coordinates, the `lookat` matrix should be generated like this:
```c
  view_ = glm::lookAt(cameraPos_, cameraPos_ + cameraFront_, cameraUp_);
```

# Callback Functions
## Movement
6 directions movement is implemented:
- UP - DOWN
	- `cameraPos_ += cameraSpeed * cameraUp_;`
	- `cameraPos_ -= cameraSpeed * cameraUp_;`
- LEFT - RIGHT (cross of FRONT and UP is RIGHT)
	- ```
	  cameraPos_ -=
        glm::normalize(glm::cross(cameraFront_, cameraUp_)) * cameraSpeed;
	- ```
	  cameraPos_ +=
        glm::normalize(glm::cross(cameraFront_, cameraUp_)) * cameraSpeed;
- FORWARD - BACKWARD
	- `cameraPos_ += cameraSpeed * cameraFront_;`
	- `cameraPos_ -= cameraSpeed * cameraFront_;`
# Mouse
Receiving the **x and y offsets** from `mouse_callback()`, where the GL process the current cursor position and compute the difference, multiply with **sensitivity**, and produce the **offsets**.
**Offsets** is basically how the camera change its **yaw and pitch angle**. We ignore the roll angle, which is not used in FPS-like view. We will need it in aero combatting game, but using 3 all angles may cause **Gimbal Lock**, which is tough to deal with, and then we might need a quaternion.
Pitch angle is regulated in $\pm 89.0\degree$ to avoid over crossing the sky.
The point of changing the view is to change the cameraFront_ to the new direction vector we want.
```c++
  glm::vec3 front;
  front.y = sin(glm::radians(pitch));
  front.x = cos(glm::radians(pitch)) * cos(glm::radians(yaw));
  front.z = cos(glm::radians(pitch)) * sin(glm::radians(yaw));
  cameraFront_ = glm::normalize(front);
```
![[截屏2023-09-26 19.54.11.png|300]] ![[截屏2023-09-26 19.55.14.png|300]]
