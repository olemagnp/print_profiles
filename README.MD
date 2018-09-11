# Printer Profiles

A collection of printer profiles for linux.
All profiles are made using an X-rite ColorMunki Photo and ArgyllCMS, following the procedure below:

All profile-folders includes all files used in the creation-process, as well as a README with settings and description of the printer/paper used

## Profile creation procedure
### Target Generation

Generate target information file with

```bash
targen -v2 -d2 -G -e8 -g128 -f675 -t <profile name>
```

Param | Explanation
------|------------
v2 | Set verbose mode to 2
d2 | Choose colorant combination _Print RGB_
G | Generate high-quality points instead of fast
e8 | Use 8 white test patches
g128 | Set grey axis rgb steps
f675 | 675 patches
t | Use incremental far point for full speed

Convert the ti-file to a TIFF with

```bash
printtarg -v2 -ii1 -a0.89 -b -T500 -m0 -M0 -P -p210x297 <profile name> 
```

Param | Explanation
------|------------
v2 | Set verbose mode to 2
ii1 | Choose i1Pro as measurement device
a0.89 | Scale patches to fit A4
b | Force B&W spacers
T500 | Output 16-bit TIFF with 500dpi resolution
m0 | Set page margin to 0
M0 | Set page margin to 0
p210x297 | Set page size to A4

Open the resulting TIFF in an image editor (e.g. GIMP), crop the margins, and scale to A4-size

### Target Printing
Print the generated target, making sure to __disable all color management of the printer driver__
