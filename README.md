## 2/5, 2/6 Drawing lines

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
  - d = 2A + B
  - while (x <= x1)
    - plot the point (x,y)
    - if (d > 0)
      - y++
      - d = d + 2B
    - x++
    - d = d + 2A


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
