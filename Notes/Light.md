# Model
Phong Lighting Model
![[Pasted image 20231012205908.png]]
### Ambient
Base lighting value
no directions

### Diffuse
Get maximum value when perpendicular to the light direction.
![[Pasted image 20231012210106.png|400]]
#### **How to calculate?**
We get a surface with 3 vertex, so a **normal vector** (hand-made) is given to every vertex corresponding to its surface.
Then, calculate the angle $\theta$ , we will need it for $\cos \theta$ as the diffuse strength.
### Specular
Normal vector is needed, too. Besides, we have to calculate the reflect direction based on the normal vetor.
![[Pasted image 20231012211020.png|400]]
Here, $\bar R$ is the reflect direction, and $\theta$ is what we need: the angle between the reflection light and our looking direction. Similarly, $\cos \theta$ is the strength of specular light.

# Implement
## Shader side:
### Vertex Shader:
Basically, give Fragment Shader the **normal vector** as it was. but here we do a better transformation with **[[Normal Matrix]]** in order to preserve the normal vector when a transformation is applied on this object.
```c
layout (location = 1) in vec3 aNormal;
out vec3 Normal;
void main()
{
    //...
    Normal = mat3(transpose(inverse(model))) * aNormal;
    //...
}
```
