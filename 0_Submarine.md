---
layout: post
title: Imaging Sonar Game Prototype
description:
image: assets/images/Sonar/ActiveSonar.png
owner: assets/images/TGA.png
nav-menu: true
---

<img src="{% link assets/images/Sonar/AllThree.png %}" alt="Image 1" />
<p> I used Pipe models made by <a href="https://www.artstation.com/kim_betsgren">Kim Betsgren </a>.</p>

<h2>Intro</h2>
This is a submarine game prototype I made with imaging sonar, passive sonar and a topographic map.

The idea is that the imaging sonar ( Center ) is your eyes and to figure out where you are you'd need to compare the map ( Left ) to the imaging sonar. The passive sonar's ( Right ) purpose would be to locate points of interests or dangers.

The imaging sonar transforms a 360° panorama into a top down view.

This is a remake of a project I did during my time at playground squad. I wanted to see what I could do differently with what I’ve learned at TGA. The old project was done almost entirely on the cpu which gave cool results but was terrible for performance. My goal with the remake was to have it run on the gpu with compute shaders to get better performance.

The main focus is the Imaging Sonar while the Passive Sonar and Map are supporting pieces.

The project was originally inspired by the torpedo scene in hunt for red october and a smarter every day’s video about passive sonar.


<h2>Shader Setup</h2>
Setting up compute shaders in Unreal can be a bit tedious. Normally, I need to create several files, define the required classes and functions, and make sure everything is properly connected before I can even begin writing the shader.
To save time on the setup, I use Shadeup. I start by creating an empty plugin and telling Shadeup to use it for the shaders. I then define the module and compute shader names, and choose a template that renders to a render target. Shadeup generates the necessary files with the correct structure and naming automatically.
Since Shadeup is built for Unreal Engine 5.3 and I am working in 5.6, some manual adjustments are needed. The generated code includes small issues, such as missing semicolons and incomplete dependency links, so I go through and clean those up to make sure everything compiles.
After that, I can start sending parameters to the shader and focus on writing the actual logic.


<h2>Imaging Sonar</h2>
<img src="{% link assets/images/Sonar/ActiveSonarFinal.png %}" alt="Image 1" />

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
      <img src="{% link assets/images/Sonar/SonarScan.gif %}" alt="Image 1" style="width: 70%"/>
      <img src="{% link assets/images/Sonar/Panorama.png %}" alt="Image 1" style="width: 70%"/>

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




<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4>Color and final touches</h4>
    The resulting image contains all the information we need, but it’s not very readable or visually clear. To make it easier to interpret, I put the green channel through a color ramp to create a red->green->blue gradient. Red indicates heights similar to the submarine and potential collision risks, while green and blue show safer distances. I then use the red channel to control the emissive strength, highlighting slopes and walls.
    <br><br>
    When the submarine isn't moving, sonar updates show very similar data, making it hard to see what’s being updated. To highlight the current update area, I add a rotating fade using the circular U coordinate to reduce emissive strength over distance. Pixels fade out over time and become bright again once the pixel is updated, giving visual feedback on the area of the map that's being updated.



  </div>
  <div class="6u$ 12u$(small)">
    <div class="row 50%" style="margin-top:0;">
      <div class="6u 12u$(small)">
        <b>Green Channel</b> <br>
        <b>Vertical Distance</b>
        <img src="{% link assets/images/Sonar/Green_Sorted_Disc.png %}" alt="Image 1" />
        <b>Color Ramp</b>
        <img src="{% link assets/images/Sonar/PS3_GreenAndColor.png %}" alt="Image 1" />
        <b>Scan Line</b>
        <img src="{% link assets/images/Sonar/ScanLine.png %}" alt="Image 1" />
      </div>
      <div class="6u$ 12u$(small)">
        <b>Red Channel</b> <br>
        <b>Accumulation Highlight</b>
        <img src="{% link assets/images/Sonar/Red_Sorted_Disc.png %}" alt="Image 1" />
        <b>Color Ramp * Red channel</b>
        <img src="{% link assets/images/Sonar/PS4_Combined.png %}" alt="Image 1" />
        <b>Final result</b>
        <img src="{% link assets/images/Sonar/ActiveSonarFinal.png %}" alt="Image 1" />
      </div>     
    </div>


  </div>
</div>
<br><br><br><br>




<h2>Topographic Map </h2>
<img src="{% link assets/images/Sonar/Map.png %}" alt="Image 1" />

<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4>Generating the map texture</h4>
    I generate a topographic map so the player can use it together with the imaging sonar to figure out their position relative to the terrain.
    <br><br>
    To do this, I take the landscape heightmap and process it in a compute shader. I also send in parameters for the contour line spacing, index line spacing, and the thickness of the lines.
    <br><br>
    To place the contour lines, I step the height values based on the contour line spacing so I can detect edges between levels. For example, with a spacing of 1.5 meters, the values become 0, 1.5, 3.0, 4.5…
    <br><br>
    To detect where lines should appear, I sample neighboring pixels within a radius defined by the line thickness. If a neighbor has a different stepped value,an edge has been found and the pixel contributes to a line. The closer the neighbor is, the stronger the contribution, and I keep the highest value. This produces a soft edge that effectively works as anti-aliasing.
    <br><br>
    I generate both contour lines and index lines at the same time so I can reuse the sampled values and avoid duplicate work.
    <br><br>
    Index lines use a larger step interval, based on a multiple of the contour spacing. The contour lines are stored in the red channel and index lines in green.
    <br><br>
    Since I already have the data, I also store the stepped heightmap and the original heightmap in the blue and alpha channels. This gives me flexibility later when making the display material for the map, without needing to go back and change the compute shader.
    <br><br>
    


  </div>
  <div class="6u$ 12u$(small)">
    <b>Map Texture</b><br>
    <img src="{% link assets/images/Sonar/MapTexture.png %}" alt="Image 1" style="width: 70%" />
    <br>
    <b>Map Texture 4 channels</b><br>
    <img src="{% link assets/images/Sonar/MapTextureChannels.png %}" alt="Image 1" style="width: 70%" />
  </div>
</div>

<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <b>Heightmap Resolution Issue</b>
    <br>
    The original heightmap is lower resolution than the final map texture, which makes the source resolution visible in the result. Without any changes, the contour lines appear blocky and follow the original pixel grid.
    <br><br>
    To reduce this, I sample a few neighboring pixels and average the result to blur the data slightly. This removes the stair-stepping effect, giving me smooth lines.


  </div>
  <div class="6u$ 12u$(small)">
    <div class="row 50%" style="margin-top:0;">
      <div class="6u 12u$(small)">
        <b>Low resolution issue</b>
        <img src="{% link assets/images/Sonar/Resolution_Issue.png %}" alt="Image 1" />
      </div>     
      <div class="6u$ 12u$(small)">
        <b>Resolution fixed</b>
        <img src="{% link assets/images/Sonar/ResIssue_Fix.png %}" alt="Image 1" />
      </div>
    </div>
  </div>
</div>
<br><br><br><br>


<h2>Passive Sonar</h2>
<img src="{% link assets/images/Sonar/PasiveSonar.png %}" alt="Image 1" />
<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4>What is is passive sonar</h4>
    The passive sonar is a waterfall graph that shows the direction towards certain objects in the world. The idea is that these objects would be points of interest or dangers like torpedoes.
    <br><br>
    On the graph the vertical axis is time, where new information is at the top and scrolls down with time.
    <br><br>
    The horizontal axis is the angle to the object. 0° is the center of the center and 90° right is the right edge of the image.
    <br><br>
    In the video, you can see the submarine ( Green Box ), 3 targets ( Red ) and the passive sonar waterfall graph that’s tracking the 3 targets.


  </div>

  
  <div class="6u$ 12u$(small)">
    <b>Video demonstration</b>
    <video autoplay muted loop controls style="width: 70%;">
      <source src="{{ '/assets/images/Sonar/PasiveSonar_Example_Short2X.mp4' | relative_url }}" type="video/mp4">
    </video>
  </div>
</div>
<div class="row 50%" style="margin-top:0;">
  <div class="6u 12u$(small)">
    <h4>How the passive sonar works</h4>
    I use noise to give a hint about the distance from the objects. 
    <br><br>
    I send the submarine position and object positions to a compute shader and calculate the angle to each object to place the signal along the horizontal axis.
    <br><br>
    At that position, I add noise to the top row of the texture. The noise is strongest at the object’s angle and fades outward from that point. The spread depends on distance, so closer objects produce tighter and stronger signals.
    <br><br>
    Each frame, the texture scrolls downward to create the waterfall effect. I also add a layer of ambient noise so the display remains active even when there are no strong signals present.



  </div>

  
  <div class="6u$ 12u$(small)">
    <b>Tracked object at 30° left</b>
    <img src="{% link assets/images/Sonar/PasiveSonar_AngleExample.png %}" alt="Image 1" style="width: 70%" />
    
  </div>
</div>
<br><br><br><br>

<video controls class="image fit">
  <source src="https://github.com/lillabofinken/lillabofinken.github.io/blob/main/assets/videos/SonarScan.mp4" type="video/mp4">
</video>

<video controls="" width="800" height="500" muted="" loop="" autoplay="">
<source src="https://github.com/lillabofinken/lillabofinken.github.io/raw/main/assets/videos/SonarScan.mp4" type="video/mp4">
</video>