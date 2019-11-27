---


---

<h1 id="localization-and-mapping">Localization and Mapping</h1>
<h2 id="d-data-representations">3D Data Representations</h2>
<p>Some of the desired characteristics of an optimal representation:</p>
<ul>
<li>
<p>Probabilistic data representations can be used to accommodate for sensor noise and dynamic environments.</p>
</li>
<li>
<p>It is important to be able to distinguish data that represents an area that is free space versus an area that is unknown or not yet mapped. This will enable the robot to plan an unobstructed path and build a complete map.</p>
</li>
<li>
<p>Memory on a mobile robot is typically a limited resource, so memory efficiency is very important. The map should also be accessible in the robot’s main memory, while mapping a large area over a long period of time. To accomplish this, we need a data representation that is compact and allows for efficient updates and queries.</p>
</li>
</ul>
<p><strong>2.5D maps</strong>, also known as height maps, store the surface of the entire environment as the maximum height measured at every point. They are memory efficient, with constant access time. This type of mapping is not very useful if you have terrain with trees or overhang structures, where the robot could move underneath. Also, height maps are non-probabilistic. Similar to point clouds, there is also no distinction between free and unknown space.</p>
<p><strong>Elevation maps</strong>  are 2D grids that store an estimated height, or elevation, for each cell. A Kalman filter is used to estimate the height, and can also incorporate the uncertainty of the measurement process itself, which typically increases with the measured distance. One problem with elevation maps is the vertical wall - you can tell there is a vertical object but don’t know exactly how tall it is.</p>
<p><strong>Extended elevation maps</strong>  store a set of estimated heights for every cell, and include cells that contain gaps. You can check whether the variance of the height of all data points within each cell is large. If so, you can investigate whether the corresponding set of points contains a gap exceeding the height of the robot (known as a “gap cell”), and ultimately use gap cells to determine traversability.</p>
<p>In  <strong>multi-level surface (MLS)</strong>  map representations, each 2D cell stores “patches”, of which there can be multiple per cell. Each patch contains 3 key pieces of information - the height mean, the height variance, and the depth value. The height mean is the estimated height of the individual vertical area, also referred to as an interval. The uncertainty of the height is stored as the height variance, with the assumption that the error is represented by a Gaussian distribution. The depth value is defined by the difference between height of the surface patch and the height of the lowest measurement that is considered as belonging to that vertical object (ex the depth of the floor would be 0). Individual surfaces can be directly calculated, allowing the robot to deal with vertical and overhanging objects. This method also works very well with multi-level traversable surfaces, such as a bridge that you could travel over top of, or underneath, or a structure like a parking garage. An MLS map isn’t a volumetric representation, but a discretization in the vertical dimension. Unknown areas are not represented, and localization for this method is not straightforward.</p>

