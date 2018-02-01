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
... (image pixel data)
```
- use Image Magick convert utility to convert .ppm to another format
