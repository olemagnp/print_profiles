# Printer Profiles
Guide followed: https://www.ludd.ltu.se/~torger/photography/argyll-print.html 

A collection of printer profiles for linux.
All profiles are made using an X-rite ColorMunki Photo and ArgyllCMS, following the procedure below:

All profile-folders includes all files used in the creation-process, as well as a README with settings and description of the printer/paper used

## Profile creation procedure
### Target Generation

Generate target information file with

```bash
targen -v2 -d2 -G -e<number of white patches> [-c <precond.icc>] -g<number of grey patches> -f<number of patches> <profile name>
```

Param | Explanation
------|------------
v2 | Set verbose mode to 2
d2 | Choose colorant combination _Print RGB_
G | Generate high-quality points instead of fast
e<number of white patches> | Set number of white test patches
c <precond.icc> | Select a precondition-profile to choose the targets that matter most for the printer
g<number of grey patches> | Set grey axis rgb steps
f<number of patches> | Set the number of patches. See below table
t | Use incremental far point for full speed

Number of patches | Number of A4 pages | Comment (from guide)
------------------|--------------------|---------------------
210 | 1 | Good enough for "standard printing"
420 | 2 | Good start point, probably good enough for photo printing
840 | 4 | More than good enough, might be overkill
1680 | 8 | Probably overkill
3360 | 16 | Overkill

Convert the ti-file to a TIFF with

```bash
printtarg -v2 -iCM -h -T<res> -p <paper size> <name>
```

Param | Explanation
------|------------
v2 | Set verbose mode to 2
iCM | Choose ColorMunki as measurement device
h | Use half-size patches for the colormunki
T<res> | Output 16-bit TIFF with chosen resolution
p <paper size> | Set output paper size

### Target Printing
Print the generated target, making sure to __disable all color management of the printer driver_

## Target Reading
Read the target using
```bash
chartread -v -H -T0.4 <name>
```

Param | Explanation
------|------------
v | Verbose mode
H | Use Hig-res spectrum mode
T0.4 | Set required strip patch consistency tolerance ratio

## Profile generation

Create a profile using

```bash
colprof -v -qh -r0.7  -A "Epson" -M "Expression Photo XP-960" -D "<Description of profile, including paper type" <name>
```
The resulting profile will only be applicable to Relative Colorimetric rendering intent. To be able to use perceptual intent, add the following params:
```bash
-S <path/to/input profile, e.g. AdobeRGB.icc> -cmt -dpp
```

Param | Explanation
------|------------
v | Verbose mode
qh | High quality
r 0.7 | Set the average deviation of the instrument as a percentage
A | Set manufacturer of device
M | Set model of device
D | Set description of profile
S | Set path to the colorspace of the input images for perceptual rendering intent
cmt | Set viewing condition of input images to monitor in typical work environment, perceptual intent
dpp | Set viewing condition of output to Practical Reflection Print (ISO-3664 P2), perceptual intent

## Check the resulting profile

Run
```bash
profcheck -k <name.ti3> <name.icc>
```

If everything is OK, average and max DE2000 should probably be below 1.5 and 3, if they are higher there might have been a measurement error

If there are problems (i.2. too high DE2000), run
```bash
profcheck -k -v2 -s <name.ti3> <name.icc>
```

To get a more detailed view, sorted by deltaE-values. Take note of the ones with an abnormally large dE, and run
```bash
chartread -v -H -T0.4 -p -r <name>
```

Param | Explanation
------|------------
p | Read patch by patch instead of strip by strip
r | Continue on existing chart

Navigate to the patch(es) that needs rereading by using `g`, and read them again, then repeat the profile generation process

### Visual Inspection
To inspect the new profile visually, two procedures are possible.
First, to check the gamut plots against AdobeRGB and AppleRGB, perform the following steps:
```bash
iccgamut -v [-w] -ff -ir <name.icc>
viewgam -t .3 -cn <path_to_AdobeRGB.gam> -cm <path_to_AppleRGB.gam> -cw <name.gam> <out_name>
```
This creates a `<name.gam>` file and a `<out_name.x3d.html>` file. Open the last one in a browser to inspect.

Param | Explanation
------|------------
v | Verbose
w | Output both .x3d.html and .gam files (optional)
ff | Set function to forward
ir | Set intent to relative colorimetric

Param | Explanation
------|------------
t | Set transparency
cn | Set natural color
cm | Set magenta
cw | Set white

The second procedure consists of creating a test image and soft-proofing it with the profile:
```bash
timage -x [-t] -g 0 test-image.tif
tificc -v -w16 -t1 -b -i <path_to_AdobeRGB.icc> -o <name.icc> test-image.tif print-image.tif
```
Now open `test-image.tif` and `print-image.tif` and inspect the differences.
If there are non-smooth gradients in the in-gamut areas, there is likely a problem.

Param | Explanation
------|------------
x | 16 bit output
g | Set percentage towards grey
t | Create rectangular boundary gamut test chart

Param | Explanation
------|------------
v | Verbose
w | Set output depth
t1 | Set intent to relative colorimetric
b | Use black point compensation
i | Input profile
o | Output profile
