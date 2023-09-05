+++
title = 'Sony Dslr Mirrorless Camera Control [Part 1]'
date = 2020-01-18T15:20:17-06:00
draft = false
+++
As mentioned in my [ASCOM driver post](https://retro.kiwi/ascom-driver-for-sony-cameras/), Sony doesn't seem particularly interested in allowing third-party access when it comes to remote control of their cameras.

This was very disappointing to me after I forked out US$1,000 for a new a6400, only to find that the only way to control the camera remotely was the Sony "Imaging Edge" suite.

It seems they (Sony) definitely thought about letting people control their own cameras for a while, as they released a [camera SDK](https://developer.sony.com/develop/cameras) (it can still be downloaded) that allows control of a number of cameras over their built-in WiFi.  Sadly it appears they have officially "Archived" the SDK and no longer offer active support.  The last post on their website announcing newly added cameras was around mid-2015, and prominently has the following message on every page:

> * Camera Remote API has now been archived. Developer World no longer offers updates and active support for Camera Remote API.
https://developer.sony.com/develop/cameras

After hunting around, I was unable to find any suitable software that would let me directly interface with the camera.  I found one piece that uses the above SDK, another simulates mouse clicks/etc on the Imaging Edge Remote application, and [one that replays captured USB packets](https://github.com/tuyanshuai/alphamote/) for a very specific combination of camera model and software.

As such, I decided to try writing my own pseudo-driver that connects to the camera via USB using the same protocol as the Imaging Edge Remote software.  The packet-replay project above gave some insight, and some rummaging around on the Internet surfaced the USB specification that Sony have based their protocol upon [[pdf]](https://people.ece.cornell.edu/land/courses/ece4760/FinalProjects/f2012/jmv87/site/files/pima15740-2000.pdf).

The last time I wrote any C/C++ code for Windows was around 2006-2007.  Since then my time has been spent developing systems using what some would argue to be higher-level languages, mainly on *nix platforms.  This project has proved to be a good refresher.

## Plug and play, right?

Yeah, so Windows is very protective of USB devices, and refused to let me communicate directly with the camera.  I tried opening the device using its internal filename, success - but failed when trying to read/write.  Trying to access it using WinUSB failed - I can't remember why.

Finally, I stumbled upon a [Microsoft MTP](https://docs.microsoft.com/en-us/windows/win32/wpd_sdk/supporting-mtp-extensions) page.  MTP is an extension to PTP (Picture Transfer Protocol).  It sits atop the USB subsystem and manages the communication with the device.  Everything goes through COM, requires many function calls, and many HRESULT checks.  However, using this interface I have been able to locate, open, and talk to my camera.

## Hex dumps are your friend

Sony appears to have taken a leaf from Microsoft's book [(embrace and extend)](https://en.wikipedia.org/wiki/Embrace,_extend,_and_extinguish), embrace the standard, but then add your own secret sauce (extend) such that code that follows the standard will only get very limited access.

The basic "must implement" functions are indeed supported by the camera.  It responds appropriately, though, without the secret Sony sauce, they either don't function properly, or they fail altogether.

This is where the previously mentioned GitHub example (a) helped, and (b) hindered development.  The code in that project reads the current settings from the camera, and I was able to reproduce the request and get a nice (1.5kB) chunk of data back.  However, the data made absolutely no sense.  The function designed to print out the settings would print garbage (even taking into account the different offsets that need to be used due to MTP processing).

At this point, I dumped the received data into the log file as hex.  Hex dumps are a great way to look at data as it's usually pretty easy to see patterns emerge in (uncompressed) data.  It was immediately obvious that there was some structure to the returned data, and I shouldn't just be picking out specific offsets.

The data turned out to be an extended form of the PIMA structure, with one extra byte per "property".

|Field       |Size    |
|------------|--------|
|Property ID |2 bytes |
|Data Type   |2 bytes |
|Get/Set Access|1 byte|
|Extra Sony Property (unknown use)|1 byte|
|Factory Default Value|n bytes (type dependent)|
|Current Value|n bytes (type dependent)|
|Form Flag (valid values)|1 byte|
|FORM Data|m bytes (depends on Form Flag). Can contain a list of valid values, specfy a range of valid values, (or none)|

As soon as I was able to decode the property info I wrote a quick app that constantly polled camera settings, and when it saw a change it would log details of the change.  Then it was time to start changing things on the camera so I could map them to property IDs.
