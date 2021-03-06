<h2><center>A framework to render human face images</center></h2>

This [paper](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=927464&tag=1) may help a lot!

## Ⅰ. Introduction

Given the surface reflectivity of an object, normal factor directions and other data, we are able to get the render results of various illumination conditions and observation views, based on some **Surface Reflection Model**(Lambertian).

With the development of 3D Rendering Technologies, more accurate models of reflection have been proposed, to make the render results as close to the real world images as possible. We consider the reverse process of the above process, that is, given the rendering results of different illumination and views, the surface reflectance and normal vector information of the object can be inferred through the surface reflection model, which can further obtain depth information for 3D reconstruction. The results of 3D reconstruction can be verified by the forward rendering effects.

However, in the general natural environment, illumination information is extremely complex and difficult to obtain, so it is not easy to realize the above prediction.


## Ⅱ. Principles

Under the condition of a single constant intensity light source in a fixed direction, the luminance of each point on the human face is closely related to the direction of the normal vector of the point.

Firstly, to simplify the problem, we consider a complete diffuse model. For each point of the forward face image $$(x,y)$$:

$$
\textbf{b}(x,y)=k_d(x,y)\frac{(z_x(x,y),z_y(x,y),-1)}{\sqrt{z_x^2(x,y)+z_y^2(x,y)+1}}
$$


$$z(x,y),k_d(x,y)$$​ are the depth and albedo of corresponding point $$(x,y)$$.

Let the direction of light be $$\bf s$$​, when s is not at a large angle to the z axis, the face image $$m$$ obtained almost contains no shadows and highlights, so its result should be close to the rendering result obtained by diffuse reflection, that is:

$$
m(x,y)=\max(\textbf{b}^T(x,y){\bf {s}},0)
$$

Therefore, when the number of images in the training set exceeds $$3$$, the optimization algorithm can be used to optimize $$\textbf {b}$$, supplemented by the continuity constraint of $$z$$​​​, and the depth of each point in the face image can be estimated to achieve 3D reconstruction of the face.

On the basis of the 3D reconstruction results, we can further render the face images under the new light angles.


#### Ⅱ-1

For the images, the amplitude array of the 2D Fourier transform contains the gray information, while the phase array carries more information about the location of distinguishable objects in the images. In general, phases contain more visual information.

The two-dimensional FFT decomposes the image into several waves in the complex plane.


$$
e^{j2\pi(ux+vy)}
$$


The translation in the time domain is equivalent to the phase shift in the frequency domain. The spatial position information of the image is contained in the phase, the amplitude in the space determines the signal strength after frequency domain transformation, and the phase determines the position information.

![RENDER-1](/imgs/Projects/RENDER-1.jpg)

![RENDER-2](/imgs/Projects/RENDER-2.jpg)

2D Fourier transform:


$$
F(x,v)=\int^\infty_{-\infty}\int^\infty_{-\infty}f(x,y)e^{-j2\pi(ux+vy)}dxdy
$$


Analogous to a one-dimensional sine wave, a two-dimensional sine wave can be determined by frequency $$\omega$$, amplitude $$A$$, phase $$\varphi$$ and normal vectors $$\vec n$$ in different directions.

![RENDER-3](/imgs/Projects/RENDER-3.jpg)

To know more details, click [here](https://www.robots.ox.ac.uk/~az/lectures/ia/lect2.pdf).


#### Ⅱ-2

Perform a 2D Fourier transform on the partial derivative information obtained from $$\textbf b(x,y)$$​. The information stored in the k-space is a complex number. When restoring the image information from the k-space, the plane wave is multiplied by the corresponding coefficient. The image information is a real number, and it is complex conjugate about the origin in the k-space. When adding, the imaginary parts cancel each other out, leaving the real part to recover the image information.


## Optimization

#### Invalid point filtering and background point determination

In the image used for training, for each pixel, if the illuminance is less than the average value or greater than the set threshold, we consider that point as a highlight point or an invalid point in the shadow, and mark it as invalid (not involved $$\textbf b$$​ matrix operation). If the effective value of the pixel point is less than the threshold value, it will be regarded as a background point.



#### Shadow optimization

Consider that if the angle between the incident direction of the light source and the X-Y plane is small enough, the image will be shaded by the convex part. Therefore, it is judged whether each point in the depth matrix will be occluded by the point in the incident direction of the light, and a shadow matrix is generated. Finally, blur the edges of the shadows.

#### Symmetric optimization

For the rendered face images, based on the high degree of symmetry of the face in reality, we optimize the depth matrix to compensate for the asymmetry caused by the error in the calculation of the $$\textbf b$$ matrix.

#### Dark background optimization

The original rendered image is very sharp at the edges of the background. The dark background optimization is based on the pixels that are close to the non-shadows. The closer the distance, the greater the weight.

## Results

![RENDER-4](/imgs/Projects/RENDER-4.jpg)
<h4><center>Optimization effect</center></h4>

![RENDER-5](/imgs/Projects/RENDER-5.jpg)
<h4><center>3D reconstruction</center></h4>

![RENDER-6](/imgs/Projects/RENDER-6.jpg)
<h4><center>Rendering effect</center></h4>
