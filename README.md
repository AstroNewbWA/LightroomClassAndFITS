# LightroomClassAndFITS
### two great tastes that don't go together, like, at all

How can I integrate FITS files into Lightroom Classic and get all that great cataloging goodness? The short answer is, you really can't. Adobe LrC doesn't support FITS formats, period. People who have lots of FITS files generally figure out some other way to organize their files. This is the sensible path.

But what if you are, against all reasonable advice, determined to make this work? And you also have at least a moderate level of pain tolerance? Well then, a hearty welcome to my world. Here's what I've learned.

### A General Strategy

My approach is to: duplicate my FITS files into a format LrC will handle; copy the FITS metadata and the orignal FITS file name into this new format; and import the new files into LrC, adding keywords as needed. When I need the FITS file for further processing, I export the imported files in their original format, retrieve the associated original FITS file names, and grab the matching FITS files.

There are a couple of drawbacks to this. The big one is that you have just doubled the amount of disk space you need to store your files. Another one is that you're not going to see the FITS metadata in LrC's metadata display, at least without some work. Yah, not a great solution.

### Creating the duplicates

There are many ways to do this. I used ImageMagick (https://imagemagick.org/) and its mogrify command: mogrify -format tif *.fits. ***IMPORTANT**: by default, mogrify does the conversion in place, overwriting the source files! By using the -format option, a new set of files is created. You really want to use that -format option. You can also supply a different target folder with the -path option.
  
### Copying the FITS metadata




