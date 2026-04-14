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
  The existing solution used a simple depth-based dithering effect that gradually removed objects between the camera and the player. It worked by defining a fade range where objects would go from fully visible to fully dithered out. In practice, this was difficult to tune. A short fade range made the dithering feel abrupt and unintentional, while a longer fade range would either leave objects still blocking the view, or cause them to start dithering while they were still in front of the player in order to fade out in time.

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
  To fix these issues, I replaced the depth fade with a cone-shaped mask between the camera and the player. I also added a depth mask to ensure only geometry between the camera and the player is affected, and noise to make the fade less uniform and more visually interesting. This gave more control over how much of the area around the player is cut out, and allowed a more aggressive fade without it feeling like camera clipping.
  <br>
  <br>
  One issue was that fully dithered objects could disappear completely, which meant the player could lose awareness of what was blocking their movement. In some cases, parts of the floor could also be cut out when they shouldn’t be. To address this, I added a height mask that keeps anything below knee height. This prevents the ground from being cut away and leaves a small stump of the objects, helping with readability of the environment while still keeping the player visible.

  </div>
  <div class="4u$ 12u$(small)">
    <b>Depth Mask</b>
    <img src="{% link assets/images/SteelEffigy/Breakdown_2.png %}" alt="Image 1" style="width: 100%"/>
    <b>Stump Mask </b>
    <img src="{% link assets/images/SteelEffigy/Breakdown_Final.png %}" alt="Image 1" style="width: 100%"/>  
  </div>
</div>