## Premise
We're given a zip file with 1,210 images. Presumably theres a flag hidden in one of these images.
## Solution
I immediately checked the low hanging fruit and ran the command `file *`.
All of the resulting files were exactly the same except for one-- 1145.jpg.
```
114.jpg:  JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 2444x1718, frames 3
1140.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 2444x1718, frames 3
1141.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 2444x1718, frames 3
1142.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 2444x1718, frames 3
1143.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 2444x1718, frames 3
1144.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 2444x1718, frames 3
1145.jpg: JPEG image data, Exif standard: [TIFF image data, big-endian, direntries=4, orientation=upper-left, xresolution=62, yresolution=70, resolutionunit=2], baseline, precision 8, 2444x1718, frames 3
1146.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 2444x1718, frames 3
1147.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 2444x1718, frames 3
1148.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 2444x1718, frames 3
1149.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 2444x1718, frames 3
115.jpg:  JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 2444x1718, frames 3
1150.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 2444x1718, frames 3
```
Looking at that file revealed the flag.
This was definitely not the intended solution but it's good to know that there are alternative ways of finding unique items in large directories.
