---
layout: post
title: TGA Projects
description:
image: assets/images/FindersKeepers.png
owner: assets/images/TGA.png
nav-menu: true
---

<h2>Tools</h2>

<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4>Unreal Landscape Export</h4>
    My goal with the landscape export was to be able to get the landscapes from unreal with the texture painting data by just exporting a level without any additional work from anyone.
	<br><br>
	We needed the landscape to be divided into chunks so I exported it as multiple pieces for our occlusion culling. I found the easiest way of doing this was to use world partitioning in unreal as it divides landscapes automatically and then I can just export each chunk as individual FBX files. 
	<br><br>
	For the texture painting layers data I decided to save it as vertex color in the FBX files which made it very easy to access the data when writing the landscape shader for our game.
	<br><br>
	One issue that came up was that one of the landscapes had black lines across it which at first we could not figure out why. I tried to do some quick bandaid fixes for it which helped but did not solve the issue. Once I figured out it was because the landscape was rotated a bit I set the landscape rotation to 0,0,0 before reading the texture paint data and once finished rotate it back which worked perfectly.

  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
		<b>The landscape in unreal</b><br>
    	<img src="{% link assets/images/Bean/LandscapeExport_UE.png %}" alt="Image 1" style="width: 80%"/>
    	<b>The landscape in blender after exporting<br>
		The paint data is saved as vertex color</b>
		<img src="{% link assets/images/Bean/LandscapeExport.png %}" alt="Image 1" style="width: 80%"/>

    </span>
  </div>
</div>
<br><br>





<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4>Blender Exporter</h4>
    Because we use Unreal as a level editor, our assets need to exist in both the Unreal project and  the Bean ( our engine ) project.
<br><br> 
To simplify this, I built an exporter that sends assets to both the Unreal and Bean asset folders at the same time. 
<br><br> 
Since Perforce sets tracked files as read-only until they’re checked out, we previously had to manually locate both the Bean and Unreal files in Perforce and check them out before exporting, which was tedious. 
<br><br> 
To avoid this I added perforce integration to the plugin using p4 commands. I added the ability to checkout files or make them writable through the blender plugin. It also will display the file status like if the file is unlocked, writable or checked out by you or someone else. 
<br><br> 
This allowed us to work in blender without touching the Perforce application until it was time to push our changes.



  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
    <br><br> 
		<b>The export window</b><br>
    	<img src="{% link assets/images/Bean/beanExport.png %}" alt="Image 1" style="width: 80%"/>

    </span>
  </div>
</div>
<br><br><br><br>

<h2>Shaders</h2>

<div class="row 50%" style="margin-top:0;">
  <div class="7u 12u$(small)">
    <h4> Two Planar </h4>
	To make the level designers work easier I made a 2 planar uv function. Why not triplanar? Because we wanted to use 45° walls and tri planar causes stretching on anything not perfectly aligned with the world axis. 
	<br><br>
	The ground was done by using the two horizontal world coordinates ( X & Z ).
	<br><br>
	For the walls, to avoid any stretching, I rotate the world coordinates to face the normal direction, I then use the right and up ( X & Y ) from the rotated coordinates.


  </div>
  <div class="5u$ 12u$(small)">
    <span class="image fit">
      <img src="{% link assets/images/Bean/TwoPlanarUv.png %}" alt="Image 1" style="width: 70%"/>

    </span>
  </div>
</div>
<br><br><br><br>


<div class="row 50%" style="margin-top:0;">
  <div class="7u 12u$(small)">
    <h4>Godrays</h4>

	I made a godray shader and godray meshes that was just the window planes extruded and shaped like gordays.
	<br><br>
	Once it came time to iterate on the godray direction and length I realized how time consuming it would be to tweak all the meshes.
	<br><br>
	To save time on iteration I changed the godray meshes to just be extruded 1 meter straight forward instead. To control the length and direction I used a vertex shader that moves the extruded vertices, allowing us to control all godrays by changing a few numbers instead of updating multiple meshes.



  </div>
  <div class="5u$ 12u$(small)">
    <span class="image fit">
      <img src="{% link assets/images/Bean/Godray_Blue.png %}" alt="Image 1" style="width: 70%"/>

    </span>
  </div>
</div>
<br><br><br><img src="{% link assets/images/Bean/Credits.png %}" alt="Image 1" style="width: 100%"/>

