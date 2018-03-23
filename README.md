## 3/23 Polygons
- There are 2 ways to render a 3D object:
	- wireframe mesh vs. polygon mesh

- **Wireframe Mesh**:
	- shape is defined by connecting points on the surface with **edges/lines**
	- generate_sphere -> add_sphere -> add_edge -> add_point ==> draw_lines -> draw_line
		- "->" means "calls"
	- but doesn't have a surface!
		- so you cannot paint the object

- **Polygon Mesh**:
	- shape is defined by connecting points on the surface with **polygons/planes**
		- usually triangles or quadrilaterals; we will use triangles b/c they're the smallest unit of polygon
	- generate_sphere -> add_sphere -> **add_polygons** -> add_point ==> **draw_polygons** -> draw_line
		- "->" means "calls"
	- easy to tell which objects are in front, etc.
	
- **Polygon (Triangle) Matrix**:
	- our edge matrices store pairs of points
	- our polygon matrices will store triangles (triplets of points)
	- points must be in counterclockwise order


## 3/20, 3/23 Vector Math Review
- Vectors have magnitude and direction
	- <x, y, z> is vector notation
- Magnitude of vector:
	- ||A|| = sqrt(Ax^2 + Ay^2 + Az^2)
	- vector with magnitude = 1 is a unit vector/normalized vector
		- A hat = (1/ ||A||) * <Ax, Ay, Az>

- **Dot Product**:
	- give back scalar value
	- as angle between the two vectors decrease, output will incress
	- A • B = ||A|| ||B|| cosθ
	- or A • B = AxBx + AyBy + AzBz
		
- **Cross Product**:
	- gives back vector value
	- result is perpendicular to A and B
		- order matters because there are 2 results perpendicular to A and B
		- use right hand rule (index = first, middle = second, thumb = result)
	- magnitude = parallelogram created by A and B
	- A x B = ||A|| ||B|| sinθ
		- instead of that, we want to use:
			- <(AyBz - AzBy), (AzBx - AxBz), (AxBy - AyBx)>
			
- Two points can give you two possible vectors:
	- PQ --> <Qxyz - Pxyz>
	- QP --> <Pxyz - Qxyz>


## 3/13, 3/14 3D Shapes (Box, Sphere, Torus)
- z values will be modifiers of x and y coordinates
 - helps to create 3D shapes

- **Box**:

  ![box](http://www.clker.com/cliparts/J/T/5/i/Z/j/dado.svg)
  - Defining points (points in the edge matrix): Vertices
  - Givens (points needed to draw it):
    - Starting point P0 (top left front)
    - Dimensions: height, width, depth --> (x, y, z)

- **Sphere**:

  ![sphere](https://static1.squarespace.com/static/57eb17d4cd0f68f1cb3917ed/t/58e30eb5e4fcb56f58926499/1490917386536/?format=300w)
  - Defining points: Points along the surface
  - Givens:
    - Center
    - Radius
  - Generate a sphere by rotating a circle
	
		- x-axis
		- y-axis
		- z-axis: (doesn't work b/c it becomes a steering wheel)
	- x-axis rotation matrix:
		- ```
				[1    0     0] * [rcosθ] = [  rcosθ  ] x (becomes x,y,z after adding cx,cy,cz)
				[0 cosϕ -sinϕ]   [rsinθ]   [rsinθcosϕ] y
				[0 sinϕ  cosϕ]   [  0  ]   [rsinθsinϕ] z
			```
			- θ = angle of circle creation
				- goes from 0 to 2pi
			- ϕ = angle of circle rotation
				- goes from 0 to pi
			
			OR (rotating semicircle)
			- θ goes from 0 to pi
			- ϕ goes from 0 to 2pi
			
	- **Sphere pseudocode**:
		- (semicircle)
		- for ϕ: 0->2pi
			- for θ: 0->pi
				- x = rcosθ + cx
				- y = rsinθcosϕ + cy
				- z = rsinθsinϕ + cz
				
- **Torus**:
	
	![torus](http://paulbourke.net/geometry/torus/torus4.gif)
	- Defining points: points on the surface
	- Given:
		- radius of tube
		- radius of donut
		- center
		
	- Generate torus by translating then rotating circle around axis:
		- x-axis: will make a sphere
		- y-axis: torus!
		- z-axis: makes a washer
	
		- if we translate over x (move left/right), we have to rotate about y-axis
		- if we translate over y (move up/down), we have to rotate about x-axis
		
	- Starting circle (translate over x, y-axis rotation):
		- x = rcosθ + R; R = radius of donut
		- y = rsinθ
		- z = 0
		
		- y-axis rotation matrix:
		- ```
				[cosϕ  0 sinϕ] * [rcosθ + R] = cosϕ(rcosθ + R)  --> x (becomes x,y,z after adding cx,cy,cz)
				[  0   1   0 ]   [  rsinθ  ]   rsinθ						--> y
				[-sinϕ 0 cosϕ]   [    0    ]   -sinϕ(rcosθ + R) --> z
			```
		
		- θ goes from 0 to 2pi
		- ϕ goes from 0 to 2pi
		

## 3/7, 3/8 Bezier Curves
- **Inputs**: end points, control points (that pull curve in directions)
  - curve of degree N needs N + 1 input points

- **Line**: needs 2 points (2 end points)
  - P0 -> P1
  - Pt = P0 + ∆Pt
       = (1-t)P0 + tP1
    - when t = 0, Pt = P0
    - when t = 1, Pt = P1
    - as t goes from 0 to 1, P1 gains more control over the shape from P0
      - as t goes from 0 -> 1, P0 -> P1
      
  ![bezier line](https://upload.wikimedia.org/wikipedia/commons/0/00/B%C3%A9zier_1_big.gif)

- **Quadratic**: needs 3 points
  - Q0 moves along line P0P1
  - Q1 moves along line P1P2
  - Qt moves along line Q0Q1
  - Q0 = (1-t)P0 + tP1
  - Q1 = (1-t)P1 + tP2
  - Qt = (1-t)Q0 + tQ1
    - Qt = (1-t)((1-t)P0 + tP1) + t((1-t)P1 + tP2)
    -    = (1-t)^2 P0 + t(1-t)P1 + t(1-t)P1 + t^2 P2
    - Qt = (1-t)^2 P0 + 2t(1-t)P1 + t^2 P2
      - behaves like binomial expansion
      
  ![bezier quadratic](https://upload.wikimedia.org/wikipedia/commons/3/3d/B%C3%A9zier_2_big.gif)
  
- **Cubic**: needs 4 points
  - boils down to quadratic then line, etc.
  - Rt moves along line R0R1
  - R0 moves along quadratic Q0Q1
  - R1 moves along quadratic Q1Q2
  - Rt = (1-t)^3 P0 + 3t(1-t)^2 P1 + 3t^2 (1-t)P2 + t^3 P3
    - binomial expansion again!
    
  ![bezier cubic](https://upload.wikimedia.org/wikipedia/commons/d/db/B%C3%A9zier_3_big.gif)
  
- **Making the equations more efficient**
  - cubic Rt becomes (-P0 + 3P1 - 3P2 + P3)t^3 + (3P0 - 6P1 + 3P2)t^2 + (-3P0 + 3P1)t + P0
    - becomes At^3 + Bt^2 + Ct + D
    - you can then create a Bezier matrix * Input point matrix = Coefficients matrix
    - ```
       [-1 3 -3 1] * [P0] = [A]
       [3 -6  3 0]   [P1]   [B]
       [-3 3  0 0]   [P2]   [C]
       [ 1 0  0 0]   [P3]   [D]
      ```
- to create a smooth connection between two Bezier curves, have the same endpoint and same control point
  

## 3/6 Hermite Curves
* ie: drawing y=x^3 given information:
* p0 and p1 --> endpoint
* R0 and R1 --> rates of change
* f(t) = at^3 + bt^2 + ct + d --> points on the curve
* f'(t) = 3at^2 + 2bt + c --> rates of change
* when t = 0, f(t) = d = p0, f'(t) = c
* when t = 1, f'(t) = c = R0
* matrices H , C, G:
*  [0001]   times [a] eq  [p0]
*  [1111]   times [b]  eq [p1]
*  [0010]   times [c] eq  [r0]
*  [3210]   times [d] eq [r1]
* H • C = G
* H'• G = C
 
 
 ## 3/5 Parametric Equations
 * Define a curve as a system of equations with an independent variable (t)
 * x=f(t) y=g(t) z=h(t)
 * line (x0, y0) --> (x1, y1) 
 * f(t) = (∆x)t + x0, t: 0 --> 1
 * g(t) = (∆y)t + y0
 
 ### General Parametric Framework
 * for t: 0 --> 1
 * x=f(t) y=g(t) z=h(t) 
 * add(x,y,z)
 
 ### Circles 
 * center (xc, yc)
 * x = rcos(theta) + xc
 * y = rsin(theta) + yc
 * theta: 0 --> 2pi
 * theta = 2pi(t)
 
 ### Splines
 * special curves that can be designed to connect smoothly
 * we will only use cubic splines
 

## 2/27 Combining Transformations
- E = our edge matrix
- T = translation matrix
- R = rotation matrix
- D = dilation matrix

- T x E = E1 ; moving image
- R x E1 = E2 ; rotating image
- D x E2 = E3 ; dilating image
- order of transformations: translate, rotate, dilate
  - the order is important; matrix multiplication isn't commutative
  
- E3 = D x R x T x E
  - read transformations from right to left
  - matrix multiplication is associative; you can group transformations together
  - E3 = (D x R x T) x E
    - this saves work when E is a big edge matrix
- we will create a "master" transformation matrix that contains all the transformations we want
  - keep adding onto it when wanting new transformations
  - multiply by edge matrix at very end


## 2/26, 2/27 Transformations > Rotations
- (x0, y0) --rotation--> (x1, y1)
- Use polar coordinates:
  - x0 = rcosϕ
  - y0 = rsinϕ
  - x1 = rcos(ϕ + θ)
    - x1 = rcosϕcosθ + rsinϕsinθ
         = x0cosθ + y0sinθ
  - y1 = rsin(ϕ + θ)
    - y1 = rsinϕcosθ + rcosϕsinθ
         = y0cosθ + xsinθ
- **(x0, y0, z) --> (x0cosθ + y0sinθ, y0cosθ + xsinθ, z)**'

- **rotation matrix**
- ```
  {cosθ, -sinθ, 0, 0} * {x} = {xcosθ - ysinθ}
  {sinθ, cosθ, 0,  0}   {y}   {ycosθ + xsinθ}
  {   0,   0,  1,  0}   {z}   {     z       }
  {   0,   0,  0,  1}   {1}   {     1       }
  ```
  
- Axis of rotation is the coordinate that you aren't changing (x, y, or z)
  - in this case, we are rotating about the z-axis
  - if we rotate about a different axis, we will have a different rotation matrix
  - when rotating about different axis, we want to rotate around the xyz coordinate grid
  - ex: **when rotating about x-axis**
    - y0 = rcosϕ
    - z0 = rsinϕ
    - y1 = ycosθ - zsinθ
    - z1 = zcosθ + ysinθ
    - x stays the same
  - ex: **when rotating about y-axis**
    - z0 = rcosϕ
    - x0 = rsinϕ
    - z1 = zcosθ - xsinθ
    - x1 = xcosθ + zsinθ
    - y stays the same
- Rotating shapes that aren't on origin can be done by translating to origin, rotating about origin, translating back


## 2/26 Transformations > Dilations
- Enlarging or shrinking
- (x, y, z) * scale factor a = (ax, ay, az)
- ```
  {a, 0, 0, 0} * {x} = {ax}
  {0, a, 0, 0}   {y}   {ay}
  {0, 0, a, 0}   {z}   {az}
  {0, 0, 0, a}   {1}   {1 }
  ```
 - this assumes dilation with respect to the origin

- You can dilate by different scale factors for each coordinate
 - (ax, by, cz)


## 2/13 Transformations > Translations
Translation, dilation, rotation (affine transformations)
 - preserves # vertices & orientation
 - reversable
  
- E = edge matrix, T = transformation matrix
  - should be T * E
  - T is square matrix
  
***Translation***
- move an image somewhere
- (x,y,z) --T(a,b,c)--> (x+a, y+b, z+c)
- ```
  {1, 0, 0, a} * {x} = {x+a}
  {0, 1, 0, b}   {y}   {y+b}
  {0, 0, 1, c}   {z}   {z+c}
  {0, 0, 0, 1}   {1}   { 1 }
  ```
- always going to be 4x4 transformation matrix * 4xN edge matrix


## 2/12, 2/13 Representing Image Data (Edge Lists)
To transform/scale/etc. images, we need to store the endpoints of our lines in something.
We can't just store the points, because we need to know how they connect to each other.

***Edge List (aka Edge Matrix)***
- Each point (pair of coordinates) is added to a list where every 2 points defines a line
- 2D arrays/lists
- should have an even number of elements
- Advantages: simple, easy to draw disjoint/separate shapes
- Disadvantages: repeating values

***Matrix Mathematicals***
- Matrix Multiplication:
  - (A rows x B columns) * (B rows x C columns)
    - the # rows in second matrix must equal # of columns in first matrix
    - final dimension = A rows x C columns
  - not commutative
  - multiply elements of each row in first matrix by elements of each corresponding column in second matrix
- Multiplicative Identity:
  - M * I = M
  - square matrix with a diagonal of all 1's, and 0's everywhere else
  - (1 0) (a) = (a)
    (0 1) (b)   (b)


## 2/5, 2/6, 2/7 Drawing lines

- Drawing lines on a computer is hard because everything has to be integers
- We use octants instead of quadrants
- One pixel is the smallest unit; so no half pixels

***Bresenham's Line Algorithm***
- Assume drawing a line in octant 1
  - therefore ***slopes are between 0 and 1***
- Assume integer endpoints of line
- Assume drawing left to right
- First pixel: given
- Second pixel:
  - must be adjacent to first pixel
  - must be to the right (x + 1)
  - must be either y + 1 or stay y
  - pixel options: (x + 1, y) or (x + 1, y + 1)
    - to see which option is better, plug both points into the line equation and see which one is closer
    - use midpoint (x+1, y+ 1/2); plug into function that tells you how far point is from the line equation
      - if = 0 (midpoint is on the line), choose either pixel
      - if < 0 (midpoint is below the line), choose top pixel (x+1, y+1)
      - if > 0 (midpoint is above the line), choose bottom pixel (x+1, y)
    - how to test midpoint (x+1, y+ 1/2):
      - Start with line equation y = mx + b
        - 0 = mx - y + b
        - 0 = (△y)x - (△x)y + (△x)b
          - let A = △y, B = -△x, C = (△x)b
        - 0 = Ax + By + C
      - f(x,y) = Ax + By + C
        - if point is on the line, f = 0
        - if point is above line, f < 0 (y is bigger than it should be)
        - if point is below line, f > 0 (y is smaller than it should be)
      - Plug in midpoint (x+1, y+ 1/2) into f(x,y) and see whether it is above/below the line
- ***Our octant 1 algorithm:***
  - line from (x0, y0) -> (x1, y1)
  - x = x0, y = y0
  - d0 = 2A + B
  - while (x <= x1)
    - plot the point (x,y)
    - if (d > 0)
      - y++
      - d = d + 2B
    - x++
    - d = d + 2A
    
- ***Octant 2 lines***
  - Possible options: (x, y+1) or (x+1, y+1)
  - Midpoint: (x+ 1/2, y+1)
  - d0 = A + 2B
  - while (y <= y1)
    - plot the point (x,y)
    - if (d < 0)
      - x++
      - d = d + 2A
    - y++
    - d = d + 2B
  
- ***Octant 8 lines***
  - Possible options: (x+1, y) or (x+1, y-1)
  - Midpoint: (x+1, y- 1/2)
  - d0 = 2A - B
  - if (d < 0)
  
- We only care about drawing octants 1, 2, 7, 8 because other octants are just reflections


## 1/31, 2/1 Peering into the depths of color
***Color Depth***
- the amount of data used to represent a single pixel
  - 1 bit: 1 color, on or off
  - 2 bit: 1 color, on or off + 4 levels of intensity
  - 3 bit: RGB, on or off
  - 4 bit: RGB + intensity
  - 6 bit: RGB, each has its own intensity
  - 3 byte: RGB, each with 256 intensities
    - our current color depth

***Other Color Spaces***
- RGBa: alpha for transparency
- HSB: hue, saturation, brightness
  - shown as color cone
  
***Image File Formats***
- Raster vs. Vector:
  - Raster images are grids of pixels
  - Vector images are series of drawing instructions
    - infinitely scalable
    - geometric shapes
    - hard to use for real-world objects
    - ex: SVG (Scalable Vector Graphics)
- Uncompressed vs. Compressed (Raster):
  - uncompressed formats contain data for each pixel
    - are large
    - easily mappable to the original image
    - ex: RAW, BMP, TIFF
  - compressed formats use a compression algorithm to reduce file size
    - no direct mapping to the original image
- Lossless vs. Lossy compression
  - Lossless compression allows you to uncompress & recreate the original image
    - ex: PNG (portable network graphics), GIF (graphics interchange format)
  - Lossy compression doesn't retain all the details of the original image; can't uncompress
    - ex: JPEG (joint photographic experts group)
    - blurs similar nearby colors
    
***Portable PixMap (.ppm)***
- Lossless, uncompressed raster format
- pixel data represented by RGB triplets in ASCII or binary
- all whitespace is equivalent
- ex:
```
P3 (type)
4 3 (dimensions)
255 (colors)
0 255 255 ...
... (image pixel data)
```
- use Image Magick convert utility to convert .ppm to another format
