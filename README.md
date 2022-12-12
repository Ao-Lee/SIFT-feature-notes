#### SIFT feature

SIFT features are invariant to image scaling and rotation, and partially invariant to change in illumination and 3D camera viewpoint.

major stages: Scale-space extrema detection, Key point localization, Orientation assignment, Key point descriptor

##### stage 1: Scale-space extrema detection

* apply Gaussian kernel with different sigma to original image and resized version of original image (different resized version of the original image is called different octive), the output are blurring with different sigma 
* compute difference of Gaussian(DoG) by subtracting nearby blurred images. DOG is an approximation of Laplacian of Gaussian (LoG). LoG acts as a blob detector which detects blobs in various sizes due to change in sigma. 
* ![DoG](https://github.com/Ao-Lee/SIFT-feature-notes/blob/main/imgs/DoG.png?raw=true)

* Once this DoG are found, images are searched for local extrema over scale and space. For eg, one pixel in an image is compared with its 8 neighbours as well as 9 pixels in next scale and 9 pixels in previous scales. If it is a local extrema, it is a potential keypoint.
* ![local_extrema](https://github.com/Ao-Lee/SIFT-feature-notes/blob/main/imgs/local_extrema.png?raw=true)



##### stage 2: Key point localization

eliminates any low-contrast key points and edge key points and what remains is strong interest points.

edge key point rejection: by computing 2x2 Hessian matrix at that point (second order derivative w.r.t Dxx,  Dxy, Dyx, Dyy)

low-contrast key point rejection: using Taylor series expansion of DoG to get more accurate location of extrema

DoG has higher response for edges, so edges need to be removed. A 2x2 Hessian matrix (H) is used to compute the pricipal curvature. The eigenvalues of H are proportional to the principal curvatures. If Tr(H)^2/Det(H) is greater than a threshold, that key point is discarded.

##### stage 3: Orientation assignment

Orientation assignment is used to achieve rotation invariance

The scale of the key point is used to select the Gaussian smoothed image, L. For each image sample, L(x, y), at this scale, the gradient magnitude, m(x, y), and orientation, theta(x, y), is precomputed using pixel differences

![gradient_magnitude_and_orientation](https://github.com/Ao-Lee/SIFT-feature-notes/blob/main/imgs/gradient_magnitude_and_orientation.png?raw=true)

Create a weighted direction histogram in a neighborhood of the key point (36 bins). It is weighted by gradient magnitude and Gaussian-weighted circular window.

![direction_histogram](https://github.com/Ao-Lee/SIFT-feature-notes/blob/main/imgs/direction_histogram.png?raw=true)




The highest peak in the histogram is taken and any peak above 80% of it is also considered to calculate the orientation. It creates key points with same location and scale, but different directions. It contribute to stability of matching.

#### stage 4: Key point descriptor

A 16x16 neighborhood around the key point is taken. It is divided into 16 sub-blocks of 4x4 size. For each sub-block, 8 bin orientation histogram is created. Concatenate 16 histograms in one long vector of 128 dims.

![descriptor](https://github.com/Ao-Lee/SIFT-feature-notes/blob/main/imgs/descriptor.png?raw=true)

