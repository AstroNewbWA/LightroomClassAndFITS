# LightroomClassAndFITS
### two great tastes that don't go together, like, at all

How can I integrate FITS files into Lightroom Classic and get all that great cataloging goodness? The short answer is, you really can't. Adobe LrC doesn't support FITS formats, period. People who have lots of FITS files generally figure out some other way to organize their files. This is the sensible path.

But what if you are, against all reasonable advice, determined to make this work? And you also have at least a moderate level of pain tolerance? Well then, a hearty welcome to my world. Here's what I've learned.

### The General Strategy

My approach is to: duplicate my FITS files into a format LrC will handle; copy the FITS metadata and the orignal FITS file name into this new format; and import the new files into LrC, adding keywords as needed. When I need the FITS file for further processing, I export the imported files in their original format, retrieve the associated original FITS file names, and grab the matching FITS files.

There are a couple of drawbacks to this. The big one is that you have just doubled the amount of disk space you need to store your files. Another one is that you're not going to see the FITS metadata in LrC's metadata display, at least without some work. Yah, not a great solution.

### Creating the duplicates

There are many ways to do this. I used ImageMagick (https://imagemagick.org/) and its mogrify command: mogrify -format tif *.fits. ***IMPORTANT**: by default, mogrify does the conversion in place, overwriting the source files! By using the -format option, a new set of files is created. You really want to use that -format option. You can also supply a different target folder with the -path option.
  
### Copying the FITS metadata

Now you have matching tif files, but you don't have the fits metadata in them. This is where the excellent exiftool (https://exiftools.org) comes in.

Exiftool provides a handy facility for copying blocks of metadata from one image file to another. They can even be different image format types. But: you can only write a tag into a image file's metadata if exiftool thinks the tagname is 'writable.' At the present time, fits tagnames aren't writable: you can see them when you dump a fit's file's metadata, but you can't change them. Exiftool does allow users to define 'user-defined tags' in a special config file, and when you add this config file as a parameter to exiftool, then the tags become writable. So that's what I do.

My config file is the other file in this project, FITS_tag_config. One thing to note is that it only contains definitions for the tags I get out of my astrorig. That's a QHY minicam8 camera, plus what gets added on by the data acquisition tool I use, NINA. If you use a different setup, you might have a different set of tags, and if you want them carried over to your new tif file, you will need to modify the config file. By the way, I love pull requests. :)

Let's get to an example of using this config file to write a tagname:
```
exiftool -config FITS_tag_config -Telescope="askar 71f with reducer"  demo.tif
```
Now here's how to copy all the fits metadata into a new tif file:
```
exiftool -config FITS_tag_config -TagsFromFile demo.fits demo.tif
```
And now here's how to copy all the fits metadata into their corresponding tif files for all the fits files in a directory:
```
exiftool -config FITS_tag_config -TagsFromFile %f.fits -ext tif .
```

There are a couple of other things to note here. Exiftool groups image file metadata together in groups. By default, exiftool places fits metadata as the "FITS family" in "group 0," which is also the "information group." You can run ```exiftool -listg``` to see a list of all the families in the information group, and ```exiftool -G0 -S demo.fits``` to see all the tags in it in a given file. When you copy the fits metadata into the tif with my config file, you'll see that the fits metadata has been moved into the "XMP family." That's still in the information group, but now it's in the embedded sidecar file. See https://manpages.ubuntu.com/manpages/jammy/man3/Image::ExifTool::TagNames.3pm.html for the gory details.





