---
layout: post
title: Snow Deformation
description: Collision done with a compute shader
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

I have two objects that I call snow corneres that I place in the level to decide the area of deformable snow. I use the corner positions later to turn world space positions in to a 0-1 uv position.

I have a deformation manager that keeps track of all objects that can interact with the snow. I send the matrecies of these objects to a compute shader but before, I replace the vertical position with the hit distance of a linetrace gowing down from the object to the ground so I know the relative verticle position from the snow to the object. I have collision layers so the line trace can only hit objects marked as Deformable.

I have a render target that I use ads a heightmap for the snow that starts filled with red subtracted by some noise to make it look more intresting when undisturbed. In the compute shader I use the object matrecies and the heightmap to calculate the intersection and lower the heightmap with the intersection, casuing the deformation. The objects are calulated as spheres with that scale with their matrix.

With the deformation I also draw a green circle as well for the pileup that's a bit bigger than the deformation area.

