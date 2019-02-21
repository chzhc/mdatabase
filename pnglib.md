Libpng-1.6 is more stringent about checking ICC profiles than previous versions. You can ignore the warning. To get rid of it, remove the iCCP chunk from the PNG image.

Some applications treat warnings as errors; if you are using such an application you do have to remove the chunk. You can do that with any of a variety of PNG editors such as ImageMagick's `convert in.png out.png`.

To remove the invalid iCCP chunk from all of the PNG files in a folder (directory), you can use ImageMagick's `mogrify *.png`, provided that your ImageMagick was built with libpng16 (run `convert -list format | grep PNG` to be sure of that).

If you'd like to find out which files need to be fixed instead of blindly processing all of them, you can run my `pngcrush -n -q *.png` where the "`-n`" means don't rewrite the files and "`-q`" means suppress most of the output except for warnings. Sorry, there's no option yet in pngcrush to suppress everything but the warnings.