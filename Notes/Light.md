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
### Fragment Shader:
Uniform variables in main .cpp file.
```c
//FILE: Light.cpp

lightShader.setVec3("objectColor", 1.0f, 0.5f, 0.31f);
lightShader.setVec3("lightColor", 1.0f, 1.0f, 1.0f);
lightShader.setVec3("lightPos", lightPos.x, lightPos.y, lightPos.z);
lightShader.setVec3("viewPos", camera->getPos());
```

Fragment shader:
for specular: shininess determines how shine it is.
![[Pasted image 20231012221207.png]]
```c
#version 330 core
out vec4 FragColor;
in vec3 Normal;
in vec3 FragPos;
uniform vec3 lightPos;
uniform vec3 objectColor;
uniform vec3 lightColor;
uniform vec3 viewPos;

void main()
{
    // ambient lighting
    float ambientStrength = 0.1;
    vec3 ambient = ambientStrength * lightColor;

    // normalization reduces the computation wightload
    vec3 norm = normalize(Normal);
    float specularStrength = 0.5;
    vec3 lightDir = normalize(lightPos - FragPos);
    // specular lighting
    vec3 viewDir = normalize(viewPos - FragPos);
    // utility function: reflect(dirFromLightToFrag, normal)
    vec3 reflectDir = reflect(-lightDir, norm);
    // power value is how specular is the surface 
    float spec = pow(max(0.0, dot(viewDir, reflectDir)), 64);
    // specular lighting is basically considering
    // how much source light can be seen by the viewer
    vec3 specular = specularStrength * spec * lightColor;

    // diffuse lighting
    // compute the angle (negative for no light, so 0 is set)
    float diff = max(dot(norm, lightDir), 0.0);
    vec3 diffuse = diff * lightColor;


    vec3 result = (ambient + diffuse + specular) * objectColor;
    FragColor = vec4(result, 1.0);
}
```
btw, the lamp has a specified fragment shader to make it always to be bright.
```c
#version 330 core
out vec4 FragColor;

void main(){
  FragColor = vec4(1.0);
}
```

# Texture
Different textures indicate different diffuse strength, ambient strength, and shininess strength.
A uniform variable is OK for this task.
### Fragment Shader:
```c
struct Material {
    vec3 ambient;
    vec3 diffuse;
    vec3 specular;
    float shininess;
}; 
struct Light {
    vec3 position;
    vec3 ambient;
    vec3 diffuse;
    vec3 specular;
};
uniform Material material;
uniform Light light;

void main()
{
    // ambient lighting
    // float ambientStrength = 0.1;
    vec3 ambient = material.ambient * light.ambient;

    // normalization reduces the computation wightload
    vec3 norm = normalize(Normal);
    // float specularStrength = 0.5;
    vec3 lightDir = normalize(lightPos - FragPos);
    // specular lighting
    vec3 viewDir = normalize(viewPos - FragPos);
    // utility function: reflect(dirFromLightToFrag, normal)
    vec3 reflectDir = reflect(-lightDir, norm);
    // 32 is how specular is the surface 
    float spec = pow(max(0.0, dot(viewDir, reflectDir)), material.shininess);
    // specular lighting is basically considering
    // how much source light can be seen by the viewer
    vec3 specular = light.specular * (material.specular * spec);

    // diffuse lighting
    // compute the angle (negative for no light, so 0 is set)
    float diff = max(dot(norm, lightDir), 0.0);
    vec3 diffuse = light.diffuse * (diff * material.diffuse);


    vec3 result = (ambient + diffuse + specular); 
    FragColor = vec4(result, 1.0);
}
```