---
layout: post
title: Snow Deformation
description:
image: assets/images/SnowDeformationRamp.png
owner: assets/images/TGA.png
nav-menu: true
---

 
<p> For the cabin and props I used <a href="https://www.fab.com/listings/508fe84a-4976-4cfe-9a40-c2b9533da601
"> Modular Rural Cabin by Maarten Hof </a>  </p>
<p> For the landscape I used heightmaps from <a href="https://www.fab.com/listings/11388bc3-c13a-4c2f-b7d0-578022969a60
"> StampIT! Collection - FREE Examples by Rowlan </a> </p>

My main insperations for using Parallax Occlution Mapping for snow deformation was The last of us part 2 and God of War 2018

<p> <a href="https://media.gdcvault.com/gdc2023/Slides/Re-inventing+the+wheel+for+snow+rendering_Surricchio_Paolo.pdf
">Paolo Surricchio GDC talk about GoW Ragnarok's Snow Deformation </a> inspiered me to make sure that my snow was not limited to flat ground </p>

<b>I have 2 objects, Snow Corners, that are used to define the area for deformable snow. </b>
<img src="/assets/images/SnowCorners.png" alt="Description">

<b>I have a deformation manager that keeps track of all objects that can deform the snow.<br>
It sends the Snow corner positions, max snow depth and the matrices of the object. <br>
The manager replaces the vertical position in the matrecies with how high the object is from deformable ground which I get from a line trace.</b>
<img src="/assets/images/FootExample.png" alt="Description">

<b>I have a render texture used as a heightmap for the snow that on startup has its red channel filled with noise between 0.8-1.0  to make undisturbed snow more interesting.</b>

<b>In the compute shader I use the matrices positions and scales to calculate sphere intersections with the snow and lower the heightmap value accordingly. In the green channel I paint a larger area around it that will be used for the pileup on the edges of the tracks.</b>

<h3>Now for the material</h3>


<div style="margin:0; padding:0;">
  <b style="margin:0; padding:0; display:block;">
    I start by raising the vertices by the max snow height.
  </b>

  <div class="row 50%" style="margin-top:0;">
    <div class="6u 12u$(small)">
      <span class="image fit">
        <img src="{% link assets/images/Snow0_Original.png %}" alt="Image 1" />
      </span>
    </div>
    <div class="6u$ 12u$(small)">
      <span class="image fit">
        <img src="{% link assets/images/Snow1_WPO.png %}" alt="Image 2" />
      </span>
    </div>
  </div>
</div>


<b>I use my own modified version of Unreal’s Parallax Occlusion Mapping ( POM ) node.<br>
I use pixel depth offset to get a much more convincing 3D effect by revealing things below the snow.</b>
<img src="/assets/images/Snow3_POM.png" alt="Description">

<b>In the custom POM node I have an input for a detail texture and I use the blue channel to add some small tiling detail and I use the same texture to break up the color a bit.</b>
<img src="/assets/images/Snow5_DetailNoiseColor.png" alt="Description">



<b>I take the green channel, multiply it by the red channel and a strength to only keep the edges and control the height. <br> 
I add the result and lower the red channel so that combined it’s still within a 0-1 range.<br> 
In the picture it’s an even split at 0.5 pileup strength.</b>
<img src="/assets/images/Snow6_Pileup.png" alt="Description">

<b>In the custom POM function I use the green channel as a mask for the green channel of the detail texture.</b>
<img src="/assets/images/Snow7_PileuptDetail.png" alt="Description">

<b>Same for the red channels.</b>
<img src="/assets/images/Snow8_DeformationDetail.png" alt="Description">