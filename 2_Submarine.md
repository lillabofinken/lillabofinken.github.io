---
layout: post
title: Submarine
description:
image: assets/images/Sonar/ActiveSonar.png
owner: assets/images/TGA.png
nav-menu: true
---

<img src="{% link assets/images/Sonar/AllThree.png %}" alt="Image 1" />

I used Pipe models made by Kim Betsgren https://www.artstation.com/kim_betsgren

<h4>Imaging Sonar</h4>
<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <b> I have a rotating camera that I use to capture a small 2 degree slice each frame.<br>
    It uses a custom projection matrix to allow me to control vertical and horizontal FOV separetly.<br>
    To get the data I want from the capture I apply a post process material before it's saved to a render texture</b>
  </div>
  <div class="0.1u$ 12u$(small)">
    <span class="image fit">
      <img src="{% link assets/images/Sonar/SonarSlice.png %}" alt="Image 1" />
    </span>
  </div>
</div>

<div class="row 50%" style="margin-top:0;">
  <div class="4u 12u$(small)">
    <b>In the Red Channel I save the horizontal distance.</b>
  </div>
  <div class="4u 12u$(small)">
    <b>In the Green Channel I save the vertical distance.</b>
  </div>
  <div class="4u$ 12u$(small)">
    <b>This is the result.</b>
  </div>
</div>

<div class="row 50%" style="margin-top:0;">

  <div class="4u 12u$(small)">
    <span class="image fit">
      <img src="{% link assets/images/Sonar/PostProcess_R.png %}" alt="Image 1" />
    </span>
  </div>

  <div class="4u 12u$(small)">
    <span class="image fit">
      <img src="{% link assets/images/Sonar/PostProcess_G.png %}" alt="Image 2" />
    </span>
  </div>
  
  <div class="4u$ 12u$(small)">
    <span class="image fit">
      <img src="{% link assets/images/Sonar/PostProcess_RG.png %}" alt="Image 2" />
    </span>
  </div>



<div class="split-grid">
  <div class="column"><b> Each frame I use the slice to update a panorama. I find the correct pixels to update by using the camera's rotation and the horizontal fov</b></div>
  <div class="column"><b>I send the Panorama to my compute and sort a slice of it.<br>
  When figuring out whee a pixel belongs I replace the UVs V value with the red channel value<br>
  To the red channel I add 1 divided by the panoramas vertical resolution.<br>
  On the green channel I save the height difference if it's lower than the current value</b></div>
</div>

<div class="split-grid bottom-gap">
  <div class="column">
    <span class="image fit"><img src="{% link assets/images/Sonar/Panorama.png %}" /></span>
  </div>
  <div class="column">
    <span class="image fit"><img src="{% link assets/images/Sonar/PS1_SortedTexture.png %}" /></span>
  </div>
</div>




<div class="split-grid">
  <div class="column"><b>I start by unwraping it with a circular UV.</b></div>
  <div class="column"><b>I use the red channel as the emissive strength</b></div>
</div>

<div class="split-grid bottom-gap">
  <div class="column">
    <span class="image fit"><img src="{% link assets/images/Sonar/PS2_UvCircle.png %}" /></span>
  </div>
  <div class="column">
    <span class="image fit"><img src="{% link assets/images/Sonar/PS3_RedAndCircle.png %}" /></span>
  </div>
</div>

<div class="split-grid">
  <div class="column"><b>I put the green channel through a color ramp </b></div>
  <div class="column"><b>I multiply them together</b></div>
</div>

<div class="split-grid bottom-gap">
  <div class="column">
    <span class="image fit"><img src="{% link assets/images/Sonar/PS3_GreenAndColor.png %}" /></span>
  </div>
  <div class="column">
    <span class="image fit"><img src="{% link assets/images/Sonar/PS4_Combined.png %}" /></span>
  </div>
</div>

<div class="split-grid">
  <div class="column"><b>As a final artistic touch I create a scan sweep mask </b></div>
  <div class="column"><b>Final result</b></div>
</div>

<div class="split-grid bottom-gap">
  <div class="column">
    <span class="image fit"><img src="{% link assets/images/Sonar/ScanLine.png %}" /></span>
  </div>
  <div class="column">
    <span class="image fit"><img src="{% link assets/images/Sonar/ActiveSonarFinal.png %}" /></span>
  </div>
</div>


<h4>Topographic Map</h4>

<img src="{% link assets/images/Sonar/Map.png %}" alt="Image 1" />


<div class="split-grid">
  <div class="column"><b>To generate the map texture I send the heightmap to a compute shader.<br>
  </b></div>
  <div class="column"><b>Final result</b></div>
</div>

<div class="split-grid bottom-gap">
  <div class="column">
    <span class="image fit"><img src="{% link assets/images/Sonar/MapTexture.png %}" /></span>
  </div>
  <div class="column">
    <span class="image fit"><img src="{% link assets/images/Sonar/MapTextureChannels.png %}" /></span>
  </div>
</div>