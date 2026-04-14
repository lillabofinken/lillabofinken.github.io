---
layout: post
title: Steel Effigy
description:
image: assets/images/SteelEffigy/SteelEffigyWallpaperRed.png
owner: assets/images/CampGroundLogo.png
nav-menu: true
---

<img src="{% link assets/images/SteelEffigy/SteelEffigy.png %}" alt="Image 1" style="width: 100%"/>
 
<p style="font-style: italic; font-size: 0.8em;"><a href="https://store.steampowered.com/app/2903980/Steel_Effigy/">Steel Effigy Steam Link</a> <br>

<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4>Intro</h4>

Temp 
<br> <br> 
Text

  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
      <br><br><img src="{% link assets/images/Snow/Video/SnowWalk.gif %}" alt="Image 1" style="width: 100%"/>
    </span>
  </div>
</div>
<br> <br> <br> <br> 




<div class="6u$ 12u$(small)">
<h2>Cutout Effect</h2>
</div>
<img src="{% link assets/images/SteelEffigy/EffigyLoop.gif %}" alt="Image 1" style="width: 70%" />
<br>
<br>
<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
  <br>
  Objects between the camera and the player would sometimes block the view, especially in areas with a lot of props or vertical elements. Using camera collision to solve this wasn’t ideal, since the camera would constantly move in and out, which could feel disorienting.
  <br>
  <br>
  The existing solution was a simple depth fade that faded out anything between the camera and the player. It worked, but it was hard to tune. If the fade distance was short it could feel abrupt and almost unintentional, and if it was longer it wouldn’t fully solve the problem, since objects could still obscure the player in certain situations. 

  </div>
  <div class="4u$ 12u$(small)">
    <b>No cutout</b>
    <img src="{% link assets/images/SteelEffigy/Breakdown_0.png %}" alt="Image 1" style="width: 100%"/>
    <b>Cone Mask</b>
    <img src="{% link assets/images/SteelEffigy/Breakdown_1.png %}" alt="Image 1" style="width: 100%"/> 
  </div>
</div>

<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
  <br>
  To get more control over this, I replaced the depth fade with a cone-shaped mask projected from the camera toward the player in a material function. This made it possible to control where the cutout happens, either closer to the camera or tighter around the player. 
  <br>
  <br>
  I also added a depth check so only geometry in front of the player is affected, and a height-based mask that keeps anything below knee height. This avoids cutting into the ground and leaves a small “stump” of the objects, which helps keep a sense of where things are in the environment.

  </div>
  <div class="4u$ 12u$(small)">
    <b>Depth Mask</b>
    <img src="{% link assets/images/SteelEffigy/Breakdown_2.png %}" alt="Image 1" style="width: 100%"/>
    <b>Stump Mask </b>
    <img src="{% link assets/images/SteelEffigy/Breakdown_Final.png %}" alt="Image 1" style="width: 100%"/>  
  </div>
</div>