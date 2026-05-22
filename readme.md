# Context

The keyence microscope [VHX-7000](https://upvedues.sharepoint.com/:b:/s/lab_devices/IQAC1DNAdHTbRq04cbV3kWioATsuavzCq_7Ix8EvOmFZSog?email=ruben.alemany%40upv.es&e=pQqG2X) is not directly programmable (no software development kit - SDK).
To be able to create automatic microscope routines, the keyence microscope is creating a file couple composed of an xml file with a .bin extension and a jpg image sharing the same uuid. Thus, if there is an uuid unmatch between those two file, the microscope won't recognize the routine.

This information make us able to edit routines via another language (python for the beginning) with selected library ([lxml](https://lxml.de/capi.html) for management, the parsing and verification of xml) and [pydantic-xml](https://pydantic-xml.readthedocs.io/en/latest/) to make a dynamic xml schema (XSD) customisable to validate the correct structure of each generated xml.

# Description of "src" repertory:

The software is based on Pydantic xml module and [lxml](https://lxml.de/capi.html).
Thus pydantic is used to make an pydantic schema which is the equivalent of xsd file in xml world.

pydantic-schema: dynamic_xsd.py

from the schema is built the following methods:


- validation method: validation_xml.py


- serialization method: serialization_xml.py


- deserialization method: deserialization_xml.py

from those basics methods are built:


- edit method: edit_xml.py


- serialization via [typer](https://typer.tiangolo.com/): serialization_typer.py

Then, the other python file are dedicated methods:

- duplicate_dummy_img.py: which works with "dummy.png"


- csv_reader.py: which works with the file "task.csv"


- exif_tool.py: file able to read the metadata of an image - [indev]

## sum-up

all the functionalities of the **KeyenceMicroscopeControl** software are concentrated in the "cli.exe" file
which is a cli tool built on "[typer](https://typer.tiangolo.com/)" module.

# Commands
## 0. Working with the csv file

- open the repertory "xmlParser"
- edit a csv file in "src/tasks.csv" and save the modifications
- open a command terminal in the "xmlParser" repertory and run the command:
```bash
cli.exe serialize csv --output "output3/tti.bin"
```
```bash
cli.exe serialize csv --output "E:/dummy_bin/tti.bin"
```
```bash
cli.exe serialize csv
```

## 1. Deserialization

```bash
>>> cli.exe show .\keyence_scripts\tti_0.bin

Task 0
ID: d744af95-6052-4ecf-95f5-b0b709b60232
X: -566.0, Y: 2033.0, Z: 6853.699999999997
ImageType: Standard
Lens: X100

Task 1
ID: 76a17c58-6ce4-4593-a4c9-2d43208402b2
X: 43392.0, Y: 2434.0, Z: 6824.699999999997
ImageType: Standard
Lens: X100

Task 2
ID: 09f86ea0-0bb7-41de-93e4-d0b41bc6b68f
X: -44522.0, Y: 1601.0, Z: 6824.699999999997
ImageType: Standard
Lens: X100

Task 3
ID: df5b5216-3584-4edc-85c5-877ddfa1f922
X: -145.0, Y: -41976.0, Z: 6824.699999999997
ImageType: Standard
Lens: X100

Task 4
ID: dd787a42-0495-47b0-83ae-365aa5b8cfb0
X: -996.0, Y: 46017.0, Z: 6808.699999999997
ImageType: Standard
Lens: X100
```

```bash
>>> cli.exe show .\keyence_scripts\tti_0.bin

Task 0
ID: 6bd9b546-7af3-4459-ac88-018a609cc172
X: 5482.0, Y: 8647.0, Z: 2267.899999999994
ImageType: Standard
Lens: X1000

Task 1
ID: 26b6ee8a-8f20-4a66-ba1d-58e7c67ab138
X: 7693.0, Y: 14399.0, Z: 2260.699999999997
ImageType: Standard
Lens: X1500

```

## 2. Serialization

```bash
cli.exe serialize clear # clear all task
cli.exe serialize add # task 0
cli.exe serialize add --x 43392 --y 2434 --z 6824.6999999999971  # task 1
cli.exe serialize add --x -44522 --y 1601 --z 6824.6999999999971  # task 2
cli.exe serialize add --x -145 --y -41976 --z 6824.6999999999971  # task 3
cli.exe serialize add --x -996 --y 46017 --z 6808.6999999999971  # task 4

# cli.exe serialize build
cli.exe export --output output/recreate0.bin # this command used alone doesnt work well
```

```bash
cli.exe serialize clear # clear all task
cli.exe serialize add --x 5482 --y 8647 --z 2267.8999999999942 --under-z 42401.284999999996 --lensmagnification "X1000" # task 0
cli.exe serialize add --x 7693 --y 14399 --z 2260.6999999999971 --under-z 42401.284999999996 --lensmagnification "X1500" # task 1

# cli.exe serialize build
cli.exe export --output output/recreate1.bin # this command used alone doesnt work well
```

# Issues

It remains a float issue for the UnderstageZ a value ...
It created an extra empty line at the end of the xml.bin file 

# Example of GIUS

| Stage X [um] | Stage Y [um] | Lens Z [um] | Stage Z [um] | Auto Focus | Lens Magnification |
|--------------|--------------|-------------|--------------|------------|--------------------|
| 0            |	0         |	47000       |	6900       | No         |	X100             |
| 20000        |	0         |	47000       |	6900       | No         |	X100             |
| -20000       |	0         |	47000       |	6900       | No         |	X100             |
| 0            |	20000     |	47000       |	6900       | No         |	X100             |
| 0            |	-20000    |	47000       |	6900       | No         |	X100             |

## without autofocus

```bash
cli.exe serialize clear # clear all task
cli.exe serialize add --x 0 --y 0 --z 6900  --under-z 47000 --lensmagnification "X100" --no-enableautofocus # task 0
cli.exe serialize add --x 20000 --y 0 --z 6900  --under-z 47000 --lensmagnification "X100" --no-enableautofocus # task 1
cli.exe serialize add --x -20000 --y 0 --z 6900  --under-z 47000 --lensmagnification "X100" --no-enableautofocus # task 2
cli.exe serialize add --x 0 --y 20000 --z 6900  --under-z 47000 --lensmagnification "X100" --no-enableautofocus # task 3
cli.exe serialize add --x 0 --y -20000 --z 6900  --under-z 47000 --lensmagnification "X100" --no-enableautofocus # task 4

# cli.exe serialize build
cli.exe serialize export --output output/giuseppe.bin # this command used alone doesnt work well
```

## with autofocus example
```bash
cli.exe serialize clear # clear all task
cli.exe serialize add --x 0 --y -20000 --z 6900  --under-z 47000 --lensmagnification "X100" --enableautofocus # task 4

# cli.exe serialize build
cli.exe serialize export --output output/giuseppe.bin # this command used alone doesnt work well
```

## others

- [float issue](https://stackoverflow.com/questions/12956333/printing-numpy-float64-with-full-precision)


- [python in usb key](https://share.google/aimode/x593ZSA29ZnmUOAYF)

# Work with xmlstarlet a professional xml cli editor

```bash
wsl --install
sudo apt update
sudo apt install xmlstarlet
xmlstarlet --version
```

### using xmlstarlet from the wsl

```bash
wsl xmlstarlet --help
.\xmlstarlet.bat --help
```

#### validate xml
```bash
.\xmlstarlet.bat val --xsd src/vhx.xsd keyence_scripts/tti_0.bin # VALIDATE
.\xmlstarlet.bat val --xsd src/vhx.xsd updated_tti_0.xml # INVALIDATE
```

#### replace element xml
```bash
.\xmlstarlet.bat sel -t -v """(//Task)[2]//LensMagnificationId""" keyence_scripts/tti_0.xml
.\xmlstarlet.bat ed -u """(//Task)[2]//LensMagnificationId""" -v "NEW_VALUE" keyence_scripts/tti_0.xml > updated_tti_0.bin
```