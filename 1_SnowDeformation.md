---
layout: post
title: Snow Deformation
description:
image: assets/images/Snow/HeaderShot.png
owner: assets/images/TGA.png
nav-menu: true
---

<img src="{% link assets/images/Snow/HeaderShot.png %}" alt="Image 1" style="width: 100%"/>
 
<p> For the cabin and props I used <a href="https://www.fab.com/listings/508fe84a-4976-4cfe-9a40-c2b9533da601
"> Modular Rural Cabin by Maarten Hof </a> <br>
For the landscape I used heightmaps from <a href="https://www.fab.com/listings/11388bc3-c13a-4c2f-b7d0-578022969a60
"> StampIT! Collection - FREE Examples by Rowlan </a> </p>

<p> My main insperations for using Parallax Occlution Mapping for snow deformation was The last of us part 2 and God of War 2018 <br><a href="https://media.gdcvault.com/gdc2023/Slides/Re-inventing+the+wheel+for+snow+rendering_Surricchio_Paolo.pdf
">Paolo Surricchio GDC talk about GoW Ragnarok's Snow Deformation </a> inspired me to make sure that my snow was not limited to flat ground </p>





<h4>Shader Setup</h4>
<p> The shader setup was the same as it was in <br><a href=https://lillabofinken.github.io/0_Submarine.html#:~:text=Shader%20Setup> my supmarine project </a> </p>

Setting up compute shaders in Unreal can be a bit tedious. Normally, I need to create several files, define the required classes and functions, and make sure everything is properly connected before I can even begin writing the shader.
To save time on the setup, I use Shadeup. I start by creating an empty plugin and telling Shadeup to use it for the shaders. I then define the module and compute shader names, and choose a template that renders to a render target. Shadeup generates the necessary files with the correct structure and naming automatically.
Since Shadeup is built for Unreal Engine 5.3 and I am working in 5.6, some manual adjustments are needed. The generated code includes small issues, such as missing semicolons and incomplete dependency links, so I go through and clean those up to make sure everything compiles.
After that, I can start sending parameters to the shader and focus on writing the actual logic.

<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h2> Decision to use Parallax Occlusion Mapping over Nanite </h2>
    At the start of the project, I wasn’t sure if I wanted to use Nanite displacement or Parallax Occlusion Mapping (POM).
  <br><br>
  POM felt like the more interesting option. It gets overshadowed by Nanite/Tessellation, but God of War (2018) and The Last of Us Part II use parallax techniques for their snow very effectively, which got me interested in using POM for this project. It also seemed like it would be more interesting to work with, since Nanite deformation is basically just driving a single float.
  <br><br>
  Nanite was also appealing because it is straightforward and works well for adding snow buildup on objects. I did early experiments using Nanite displacement and bevels, and the results looked promising. The main issue was that the displacement does not follow the vertical axis as it follows the vertex normal. I tried forcing normals upward and storing the originals as vertex colors, but this approach would not work on landscapes without either modifying engine code or using the landscape as a static mesh.
  <br><br>
  With POM, the deformation direction issue was much less noticeable and modifications to POM would not require a custom engine build which caused me to make the final decision of focusing on POM.
  <br><br>
  I did end up adding support for nanite too and I found out that once the project was finished and the pileup and detail textures were applied to the deformation, the displacement direction issue ended up being a lot less noticeable.

  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
      <b>Quick test for adding snow on objects with a bevel and nanite</b>
      <img src="{% link assets/images/Snow/NaniteTest_1.png %}" alt="Image 1" style="width: 100%"/>
      <b>Test with multiple bevels</b>
      <img src="{% link assets/images/Snow/NaniteTest_2.png %}" alt="Image 1" style="width: 100%"/>
    </span>
  </div>
</div>
<br><br><br><br>









<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4>Defining the deformable area</h4>
    
    I needed to define my area for the deformable snow in world space.
    Just using two positions works but would be annoying to work with because editing world positions with sliders would be very finicky.
    <br><br>
    To make the process of defining the area simpler, I created two Snow Corners( Red objects in the picture ) that I place in the world. To resize the area I just reposition the Snow Corners in world space.
  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
      <b>The red objects define the deformable area</b>
      <img src="{% link assets/images/Snow/SnowCorners.png %}" alt="Image 1" style="width: 100%"/>
    </span>
  </div>
</div>
<br><br><br><br>






<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4>Deformation Manager</h4>
    
    I need the snow corner positions, the max snow height and all the matrices of objects that can interact with the snow to be sent to a compute shader.
    <br><br>
    To do this, I created a manager that kept track of all the data the compute shader needed access to.
    <br><br>
    To get it to work on slopes I would need some way of getting the height of the ground. 
    <br><br>
    I decided to bake this information into the matrices vertical positions. To achieve this the manager does linetraces downwards from the object and replaces the vertical position of the matrices with the hit distance.
    This means that the vertical position becomes local to the ground below.
    <br><br>
    This isn’t perfect though, because it’s basically just raising or lowering a flat plane with the terrain. I planned to improve the collision by using the hit normal to rotate the plane but since the issue wasn't that noticeable I felt like my time could be better spent elsewhere.
    <br><br>

    The reason why I decided to send entire matrices is because I wanted to add support for different shapes like capsules and boxes which in the end I did not have time to do. I currently only have support for spheres.


  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
      <b>The line trace that gets the foots distance from the ground</b>
      <img src="{% link assets/images/Snow/FootExample.png %}" alt="Image 1" style="width: 100%"/>

    </span>
  </div>
</div>
<br><br><br><br>








<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4>Height map render target from Compute Shader</h4>
    
    The issue with just using one channel for the snow is that the deformation and the pileup that I want around the deformed area would need to be baked into the same channel. This would make it harder to separate the deformed area and the pileup for applying the detailed textures. It would also mean I would be unable to iterate on the pileup settings on the fly through the material.
    <br><br>
    That’s why I decided to use two channels instead, red for deformation and green for pileup. This means that I could have control over how the channels get applied. It also gives me two perfect masks for the deformation and pileup that I can use to apply the detail textures later.
    <br><br>
    When the snow remained untouched it looked really flat and boring. To solve this I added some random noise to the heightmap ( Red channel ) at startup to get some subtle height variation in the untouched snow.
    <br><br>
    
    When calculating the collision I treat all objects as spheres and use the matrices for position and scale.
    <br><br>
    I calculate the intersection between the spheres and the snow, if an intersection is found I lower the heightmap (Red channel) by the intersection amount.
    <br><br>
    For the pileup I raise the value in the green channel around the spheres at a larger distance than the radius of the sphere to get it around the deformed snow.



  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
      <b>The render target result from the compute shader<br>
      Red: Snow heightmap<br>
      Green: Pileup mask</b>
      <img src="{% link assets/images/Snow/Heightmap.png %}" alt="Image 1" style="width: 100%"/>

    </span>
  </div>
</div>
<br><br><br><br>










<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4> Raising the vertices.
 </h4>
    Because POM can’t occlude more than the mesh I raise the vertex positions by the max snow height.
  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
      <b>Vertecies being raised</b>
      <img src="{% link assets/images/Snow/Video/VertRaise.gif %}" alt="Image 1" style="width: 100%"/>
      

    </span>
  </div>
</div>
<br><br><br><br>




<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4> Parallax Occlusion Mapping. </h4>
    
    I use the red channel from the render target as the heightmap for my POM function.
    <br><br>
    To avoid other objects looking like they are floating over the deformed snow I use the pixel depth offset to reveal objects below the snow to get a more convincing 3d effect.
    <br><br>

    To avoid the snow looking like smooth plastic I add in the blue channel from the detail texture to the height in the POM function to get some fine grain detail. I also use the blue channel from the detail texture to add some color variation as well on top of the small bumps.



  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
      <b>Parallax Occlusion Mapping with pixel depth offse</b>
      <img src="{% link assets/images/Snow/Video/POM.gif %}" alt="Image 1" style="width: 100%"/>
    </span>
  </div>
</div>
<br><br><br><br>







<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4> Pileup </h4>
    So far the red channel has been using 100% of the POM’s 0-1 range which leaves no range left for the pileup. 
    <br><br>
    To maintain a 0-1 range I added an input to the POM function that controls what amount of the range is used for the pileup and deformation. In the image it’s evenly split at a value of 0.5.
    <br><br>
    To avoid the pileup creeping in too much in the deformed area I multiply the pileup (Green Channel) with the heightmap (Red Channel) before adding the pileup.

  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
      <b>Detail for height and color<br>
      Pileup now share the 0-1 range with the heightmap</b>
      <img src="{% link assets/images/Snow/Video/PomToPileup.gif %}" alt="Image 1" style="width: 100%"/>
    </span>
  </div>
</div>
<br><br><br><br>






<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4>Applying the detail texture</h4>
    The snow currently has a very smooth and boring appearance. Also the resolution and distance the objects moved per frame is clearly visible in the deformation.
    <br><br>
    To make it look less boring while also hiding the issues I add in the Red and Green channel from the detail texture. I use the red channel of the detail texture to add detail to the deformation (Red channel). To add detail to the pileup (Green Channel) I use the green channel of the detail texture.

    <br><br>
    Because the values need to stay within a 0-1 range I need to combine the details instead of just adding it.
    <br><br>
    To do this I start by using the render texture channels to mask out the areas that should not have detail like outside of the deformation/pileup areas. Now I have 2 separate 0-1 values, the render target and the detail value. I do this for each channel separately.
    <br><br>
    To be able to have control over how much range is used for the overall shape and how much is used for the details I lerp between the detail value and render target value.

  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
      <b>The detail texture being applied</b>
      <img src="{% link assets/images/Snow/Video/PileupToDetail.gif %}" alt="Image 1" style="width: 100%"/>
      <img src="{% link assets/images/Snow/DetailTexture.png %}" alt="Image 1" style="width: 100%"/>
    </span>
  </div>
</div>
<br><br><br><br>
<img src="{% link assets/images/Snow/SnowPhilip.png %}" alt="Image 1" style="width: 100%"/>