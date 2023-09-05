+++
title = 'Sony Dslr Mirrorless Camera Control [Part 2]'
date = 2020-01-26T16:50:25-06:00
draft = false
+++
At this time, I've been able to map around half of the reported properties to their actual camera setting. I've deciphered the meaning of the different values for most of these. However, some values will be missing as my camera doesn't allow me to use some of the possible values (i.e. Some of the flash settings appear permanently disabled on my camera - either that or they only become options when other settings are set "just so")

## PROPERTIES
### STANDARD (PTP) CAMERA PROPERTIES
Sony only support a small number of these (at least on my camera)

|Camera Property|Readable|Writable|Use|
|---------------|:------:|:------:|---|
|Battery Level|-|-| |
|Functional Mode|-|-| |
|Image Size|-|-| |
|Compression Setting|Y|Y|Whether camera is set to JPEG, RAW, or RAW+JPEG (Includes JPEG Super Fine, Fine, Standard)|
|White Balance|Y|Y|Auto, Daylight, Flash, Cloudy, etc|
|RGB Gain|-|-| |
|F Number|Y|N|F-Stop setting|
|Focal Length|-|-| |
|Focal Distance|-|-| |
|Focus Mode|Y|N|Manual, AF-S, AF-C, etc|
|Exposure Metering Mode|Y|N|Metering Mode setting from camera|
|Flash Mode||N|Fill, Redeye, Rear-Sync, etc|
|Exposure Time|-|-| |
|Exposure Program Mode|Y|N|AUTO, Movie, Panorama, M, P, etc|
|Exposure Index|-|-| |
|Exposure Bias Compensation|Y|N| |
|Date Time|-|-| |
|Capture Delay|-|-| |
|Still Capture Mode|Y||Single Shoot, Continuous, etc|
|Contrast|-|-| |
|Sharpness|-|-| |
|Digital Zoom|-|-| |
|Effect Mode|-|-| |
|Burst Number|-|-| |
|Burst Interval|-|-| |
|Timelapse Number|-|-| |
|Timelapse Interval|-|-| |
|Focus Metering Mode|-|-| |
|Upload URL|-|-| |
|Artist|-|-| |
|Copyright Info|-|-| |
### SONY CUSTOM PROPERTIES
As you can see, there are a lot of properties still to be deciphered.

|Camera Property|Readable|Writeable|Use|
|---------------|:------:|:-------:|---|
|Flash Compensation|Y|N| |
|DRO Auto HDR|Y|Y|Off, Auto|
|JPEG Image Size|Y|Y|S, M, L|
|Shutter Speed|Y|N| |
|\<unknown property>| | | |
|Custom White Balance|Y|Y| |
|\<unknown property>| | | |
|Aspect Ratio|Y|Y|1:1, 3:2, 16:9|
|\<unknown property>| | | |
|Shutter Button Status|Y|N|Up, Half Depressed, Fully Depressed|
|\<unknown property>| | | |
|Photo Buffer Status|Y|N|Reports status of internal processing when a photo is taken as well as the number of photos queued for downloading|
|Auto Exposure Lock|Y|Y| |
|Battery|Y|N|Percentage Charge of battery|
|Flash Exposure Lock|Y|Y| |
|\<unknown property>| | | |
|\<unknown property>| | | |
|Aperture|Y|N|Aperture setting (if lens reports it)|
|ISO|Y|N|Current ISO setting|
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
Focus Area|Y|Y|Wide, Zone, Center, etc|
|Focus Assist Mode|Y||Off, On, Zoom|
|\<unknown property>| | | |
|Focus Assist Zoom|Y|N|1x, 5.9x, etc|
|Focus Assist Coord|Y|N|Relates to selection box position on screen|
|Live View|Y|Y|On, Off (enabled)|
|Focus Assist Size/Area|Y| |Relates to selection box size on screen|
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|Auto White Balance Lock|Y|Y| |
|\<unknown property>| | | |
|\<unknown property>| | | |
|Shutter Half Down|Y|Y|Used to set "push button half down/engage auto-focus"|
|Shutter Full Down|Y|Y|Used to take photo|
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
|\<unknown property>| | | |
## SETTING/UPDATING PROPERTIES
The definition of a property includes data that describes (a) whether the property can be written to, and (b) what values are acceptable.

### ENUMERATION
The property specifies a fixed list of "acceptable values" that it can be set to. A good example of this is the **Compression Setting** value. It can only be one of three values: *RAW*, *JPEG*, or *RAW+JPEG*.

### RANGE
Range properties are trickier - they specify a lower limit, an upper limit, and a step size. The camera tends to use this type for properties that are more fluid in their list of possible values. Example properties that use this type are **ISO**, **F-Stop**, **Aperture**, **Shutter Speed**, etc. The camera doesn't appear to allow setting of arbitrary values, and instead seems to force a simple *next/previous* type of setter. This means that operations such as changing to BULB shooting requires the **Shutter Speed** property to be set multiple times with the *previous* value until the reported shutter speed is **BULB**. Additionally, there appears to be a speed limit that these changes can be applied. (This actually mirrors the behavior of the *Imaging Edge Remote* software).