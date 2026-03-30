---
layout: post
title: Imaging Sonar
description:
image: assets/images/sonar/ActiveSonar.png
owner: assets/images/TGA.png
nav-menu: true
---
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



<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <b> Each frame I use the slice to update a panorama. I find the correct pixels to update by using the camera's rotation and the horizontal fov</b>
  </div>
  <div class="6u$ 12u$(small)">
    <b> Each frame I use the slice to update a panorama. I find the correct pixels to update by using the camera's rotation and the horizontal fov</b>
  </div>
</div>

<div class="row" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <span class="image fit">
      <img src="{% link assets/images/Sonar/Panorama.png %}" alt="Image 1" />
    </span>
  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
      <img src="{% link assets/images/Sonar/PS1_SortedTexture.png %}" alt="Image 2" />
    </span>
  </div>
</div>



<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <b>I start by unwraping it with a circular UV.</b>
  </div>
  <div class="6u$ 12u$(small)">
    <b>I use the red channel as strenght</b>
  </div>
</div>

<div class="row" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <span class="image fit">
      <img src="{% link assets/images/Sonar/PS2_UvCircle.png %}" alt="Image 1" />
    </span>
  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
      <img src="{% link assets/images/Sonar/PS3_RedAndCircle.png %}" alt="Image 2" />
    </span>
  </div>
</div>




<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <b>SampleText</b>
  </div>
  <div class="6u$ 12u$(small)">
    <b> SampleText<br></b>
  </div>
</div>

<div class="row" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <span class="image fit">
      <img src="{% link assets/images/Sonar/PS3_GreenAndColor.png %}" alt="Image 1" />
    </span>
  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
      <img src="{% link assets/images/Sonar/PS4_Combined.png %}" alt="Image 2" />
    </span>
  </div>
</div>