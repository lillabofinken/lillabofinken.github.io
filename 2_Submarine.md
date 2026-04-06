---
layout: post
title: Imaging Sonar Game Prototype
description:
image: assets/images/Sonar/ActiveSonar.png
owner: assets/images/TGA.png
nav-menu: true
---
<p> I used Pipe models made by <a href="https://www.artstation.com/kim_betsgren">Kim Betsgren </a>.</p>

<img src="{% link assets/images/Sonar/AllThree.png %}" alt="Image 1" />

<h4>Intro</h4>
This is a submarine game prototype I made with imaging sonar, passive sonar and a topographic map.

The idea is that the imaging sonar ( Center ) is your eyes and to figure out where you are you'd need to compare the map ( Left ) to the imaging sonar. The passive sonar's ( Right ) purpose would be to locate points of interests or dangers.

The imaging sonar transforms a 360° panorama into a top down view.

This is a remake of a project I did during my time at playground squad. I wanted to see what I could do differently with what I’ve learned at TGA. The old project was done almost entirely on the cpu which gave cool results but was terrible for performance. My goal with the remake was to have it run on the gpu with compute shaders to get better performance.

The main focus is the Imaging Sonar while the Passive Sonar and Map are supporting pieces.

The project was originally inspired by the torpedo scene in hunt for red october and a smarter every day’s video about passive sonar.



<h2>Imaging Sonar</h2>
<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4> Depth Panorama </h4>
    I needed to generate a 360° panorama.
    <br>
    Using a Scene Capture Cube would not allow for independent control over horizontal and vertical resolution. It was also too expensive as it renders a full 360° view while I only need to update a small slice each frame.
    <br><br>
    Instead, I captured the panorama incrementally by rotating a narrow-FOV Scene Capture 2D and stitching the slices together.
    <br><br>
    However, the default Scene Capture 2D does not support independent control over  horizontal and vertical FOV. To solve this, I implemented a custom Scene Capture 2D with a modified projection matrix, enabling full control over both axes.

  </div>
  <div class="6u$ 12u$(small)">
    <span class="image fit">
      <img src="{% link assets/images/Sonar/SonarScan.gif %}" alt="Image 1" />
      <img src="{% link assets/images/Sonar/Panorama.png %}" alt="Image 1" />

    </span>
  </div>
</div>
<br><br><br><br>




<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4> Separating horizontal and vertical distance </h4>

    When using standard depth, the distance is taken along the view ray. This means that deeper areas of the ocean floor appear farther away, even if they are directly below the submarine.
    <br><br>
    For a top-down view, this creates distortion. Points that are deeper are pushed outward when sorting pixels by distance. Standard depth also does not give enough information to determine how deep a point is relative to the submarine.
    <br><br>
    To address this, I separate horizontal and vertical distance into two channels using a post-process material. I get the vector from the camera to each pixel in world space. 
    <br><br>
    I store the horizontal distance, which is the length of the XY components, in the red channel. 
    I store the vertical distance, which is the Z component, in the green channel.
    <br><br>
    By doing this, I can sort pixels based purely on horizontal distance from the submarine, producing an undistorted top-down view, while using the vertical distance for the color.
    <br><br>
    I divide both distances by the maximum sonar range to normalize them.

  </div>
  <div class="6u$ 12u$(small)">
    <div class="row 50%" style="margin-top:0;">
      <div class="6u 12u$(small)">
        <b>Post Process</b>
        <img src="{% link assets/images/Sonar/PostPrecess_Combined.png %}" alt="Image 1" />
        <b>Combined distance</b>
        <img src="{% link assets/images/Sonar/Distortion_Pre.png %}" alt="Image 1" />
      </div>      
      <div class="6u$ 12u$(small)">
      <b>Distortion Example</b>
        <img src="{% link assets/images/Sonar/Distortion.gif %}" alt="Image 1" />
        <b>Seperated distance</b>
        <img src="{% link assets/images/Sonar/Distortion_Post.png %}" alt="Image 1" />
      </div>
    </div>


  </div>
</div>
<br><br><br><br>


<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4> Turning the panorama into a top down image </h4>
    I already have the data I need in the panorama, but it isn’t arranged as a top-down view. So I rearrange it into one.
    <br><br>
    I do this by sorting the pixels based on their horizontal distance, stored in the red channel. Because this value is already normalized, I can use it directly to set each pixel’s V coordinate.
    <br><br>
    For each pixel, I add 1.0 / PanoramaVerticalResolution to the red channel and store the vertical distance from the panorama in the green channel.
    <br><br>
    Using 1.0 / PanoramaVerticalResolution means the final red channel values would still be similar if I were to change the panorama resolution.
    <br><br>
    When multiple pixels map to the same coordinate, their red values accumulate. This naturally highlights surfaces like walls or steep slopes, where many pixels share the same horizontal distance.
    <br><br>
    For the green channel, I keep the lowest vertical distance from the pixels that end up at the same coordinate. This saves the information about the point closest to the submarine.

  </div>
  <div class="6u$ 12u$(small)">
  <b>Panorama Sorted in to a top down view</b>
  <img src="{% link assets/images/Sonar/PS1_SortedTexture.png %}" alt="Image 1" />
    <div class="row 50%" style="margin-top:0;">
      <div class="6u 12u$(small)">
        <b>Red Channel</b> <br>
        <b>Accumulation Highlight</b>
        <img src="{% link assets/images/Sonar/Red_Sorted.png %}" alt="Image 1" />
      </div>     
      <div class="6u$ 12u$(small)">
        <b>Green Channel</b> <br>
        <b>Vertical Distance</b>
        <img src="{% link assets/images/Sonar/Green_Sorted.png %}" alt="Image 1" />

      </div>
    </div>


  </div>
</div>
<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    
    To make the top-down view intuitive, I warp the UV into a disc shape which puts the submarine at the center. This way, distances radiate outward from the center, making it easy to understand what’s forward, behind, or to the sides of the submarine.


  </div>
  <div class="6u$ 12u$(small)">
    <div class="row 50%" style="margin-top:0;">
      <div class="6u 12u$(small)">
        <b>Disc shaped UV</b>
        <img src="{% link assets/images/Sonar/UV.png %}" alt="Image 1" />
      </div>     
      <div class="6u$ 12u$(small)">
        <b>Sorted Panorama with disc UV</b>
        <img src="{% link assets/images/Sonar/PS2_UvCircle.png %}" alt="Image 1" />

      </div>
    </div>


  </div>
</div>
<br><br><br><br>




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


<div>
  <h4>Topographic Map</h4>
  <img src="{% link assets/images/Sonar/Map.png %}" alt="Image 1" />
</div>
<div></div>

<div class="split-grid">
  <div class="column"><b>To generate the map texture I send the landscape heightmap to a compute shader.<br>
  I force the height map in to steps of the distance between contour line<br>
  To figure out if a pixel shoud be a line I check it's neighbour within the distance of the line thickness to see if any of them have a different value.
  </b></div>
  <div class="column"><b>I do the same for the index lines but the contour line distance is multiplied by a whole number<br>
  I save the contour lines and red, index lines as green, heightmap as blue and the stepped heightmap as alpha since I already have it</b></div>
</div>

<div class="split-grid bottom-gap">
  <div class="column">
    <span class="image fit"><img src="{% link assets/images/Sonar/MapTexture.png %}" /></span>
  </div>
  <div class="column">
    <span class="image fit"><img src="{% link assets/images/Sonar/MapTextureChannels.png %}" /></span>
  </div>
</div>

<div>
  <h4>Passive Sonar</h4>
  <img src="{% link assets/images/Sonar/PasiveSonar.png %}" alt="Image 1" />
</div>
<div><b>The passive sonar is quite simple. It's a waterfall graff, vertical being time, new information being on top, old at the bottom and horizontal being angle. <br>
It's done in a compute shader that constantly scrolls the texture down and updates the top row of pixels.<br>
On the top row I first add just an ambient noise that's supposed to be the ambient noise of the sea.<br>
Secondly I send an array of positions of noise emitting objects that I use to add lines of noise to the top row. The strenght and thickness being dependent on distance from the player. The position being dependent on angle from the players forward direction. ( 90° left = U0 ) ( 0° = U0.5 ) ( 90° right = U1 ) </b></div>
