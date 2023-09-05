+++
title = 'ASCOM Driver for Sony Cameras'
date = 2020-01-18T15:20:17-06:00
draft = true
+++
Recently I moved to an area of the country where the night skies are much clearer. I can now actually see celestial objects bigger than the Sun and the Moon!

After purchasing a [relatively cheap tracking telescope bundle](https://www.highpointscientific.com/telescopes/meade-etx-80-observer-telescope-with-backpack-and-audiostar-205002) I discovered that although my friends outwardly *appeared* to be interested, that didn't translate to a desire to spend their evening stargazing - especially as temperatures dropped towards, and then below, freezing. So I thought I could at least show them the awesomeness from the comfort of their living room (or, more likely, their cellphone).

The telescope I purchased is advertised as having a camera port[^1]. As I am regularly "wowed" when viewing photos taken by amateur astro-photographers I thought I'd take some photos and see what I could accomplish.

Enter the Sony a6400 camera - it had just been launched, and from all reports it is a pretty kick-ass camera. I bought one, thinking I'd hook it up to this "[APT](https://www.ideiki.com/astro/)" software I keep seeing.

That's there things fell off the rails. The APT software has built-in functionality to connect to Nikon and Canon cameras, and via [ASCOM](https://ascom-standards.org/) it can connect to a number of others... but no Sony support in sight.

## A PARTIAL SOLUTION
The a6400 camera is supported by Sony's proprietary "[Imaging Edge](https://imagingedge.sony.net/en-us/ie-desktop.html)" software, and through that it is possible to get captures longer than 30 seconds. It also supports automated capture, although for undocumented reasons the setup of "number of shots/time between consecutive shots" is separate from the "how long should each shot be" setup.

Using the *Remote* software in imaging-edge I've been able to capture a number of photos that have been "wow" worthy - at least for me.

Sony also have an official Web API for their cameras, although when visiting the site they take great pains to point out that the API is no longer officially supported. Additionally it requires direct connection to the Camera's WiFi access-point. My a6400 isn't officially supported, and I was concerned that if I spent any time writing software against this API I might find Sony removes it from a later version of software.

{{< figure src="/images/Orion-Nebula.png" title="Orion Nebula, taken using Sony a6400. 6 x 4-minute exposures" >}}

The above (cropped) image was taken using the Imaging Edge Remote software. The image was stacked using [Deep Sky Stacker](http://deepskystacker.free.fr/), and level adjusted using Photoshop. The Telescope used was a Willam Optics Redcat 51 with an Optolong L-enHance Light Pollution Filter.

## REVERSE ENGINEERING
I found a project on [GitHub](https://github.com/tuyanshuai/alphamote/) written by someone who wanted a remote trigger that would work for Linux. The code essentially replays captured USB packets that were captured using a packet sniffer.
*Note that this code wouldn't work for me - but it provided a hint that it might be possible to automate the camera without having to rely on the Sony software.*

## SONY FOLLOW A STANDARD, SORT-OF
After a little snooping of USB traffic, I was able to figure out the basics of communication.

There is a standard written by the Photography and Imaging Manufacturers Association (PIMA) known as PTP (Picture Transfer Protocol) [[pdf](https://people.ece.cornell.edu/land/courses/ece4760/FinalProjects/f2012/jmv87/site/files/pima15740-2000.pdf)]. In a very Microsoft-esque manner, Sony have added support in their cameras for the "required" part of the specification, which is essentially what Windows (and other OS's) can use to download images/etc. (The camera also exposes the data as a USB "Mass Storage Device"). The other functionality of PTP that supports control of the device and capture of images/video/etc has been left out.

The *Remote* software makes use of some Sony-specific "vendor extensions" to PTP. The extensions mimic some of the official standard features with a Sony flavor.

## WHAT I'VE BEEN ABLE TO FIND
My a6400 has something in the order of 63 different knobs (properties) that can be tweaked, pressed or queried.

These include things like:

* What mode is the camera in (from the Dial on the top). i.e. "Auto", "M", "Movie", etc
* What are things like Aperture, ISO, Shutter Speed set to
* What are various settings like White Balance, Shooting Mode, etc set to

Many of the settings can be changed, some via a list of possibilities, others via a simple "up/down" type rocker.

At this point, I've not really bothered to spend much time changing settings remotely - though I've done enough to confirm it is possible. My main focus (no pun intended) has been to figure out how to push the shutter button and then extract the photo once it is ready.

ALPHA SOFTWARE
Beta software is generally "it should work, but we may not have all the wrinkles out of it yet".

Alpha software isn't generally at that point yet :) It's generally "try it at your own risk, we may make some major changes based on feedback".

What I have on offer is *Alpha* quality. It was developed on a laptop running Windows 10 connecting specifically to [APT](https://ideiki.com/astro/Default.aspx).

*Update: The driver now includes support (limited testing) for [SharpCap](https://www.sharpcap.co.uk/) and [N.I.N.A](https://nighttime-imaging.eu/). (Feature availability differs between applications)*

It consists of an installer that will install a basic ASCOM camera driver for Sony Cameras. The driver currently supports the following cameras - *if you have a Sony Camera not listed here, and it works with the Sony Imaging Edge Remote software, contact me and we can get it added.*

**Over 30 Sony models are currently supported** - rather than constantly update this post, there is a page on the driver's [GitHub Wiki](https://github.com/dougforpres/ASCOMSonyCameraDriver/wiki/Supported-Cameras) that lists currently supported models.

Due to the way ASCOM drivers work and the finnicky way Sony allows some settings to be changed, only the following functionality is currently exposed:

* *Live Preview* (at 1024x680): This is the same live-preview as the Remote software provides (*if supported by your camera*). It allows the use of the digital zoom "focus assist" feature of the camera, and responds to changes in ISO/Shutter Speed made on the camera. This worked reasonably well with the Bahtinov mask assist feature in APT. (note that this feature requires APT 3.82 or later)
* *Full Resolution Photos*: If the camera is set to BULB mode then APT (or other app) can control the exposure. You should manually set up the camera to the desired settings as no other control is currently available.
* *RGB or RGGB images* (for full-res photos): The driver will extract the RAW data from the camera and either return an interpolated RGB image (based on some non-controllable defaults) or it can return an RGGB image which contains the actual un-debayered data from the camera. Note that in this mode it is up to you to debayer the image data. No exposure/gamma/white-balance meta-data is provided thru ASCOM, and [the debayering option in APT just needs to be configured](https://github.com/dougforpres/ASCOMSonyCameraDriver/wiki/Hints-and-Tips-for-Astrophotography-Software#raw-previews-too-green).
* (Optional) Save the RAW (ARW/JPEG) file directly from the camera to disk (this includes the most recent preview frame, stored as JPEG)

At the moment I'm working on exposing a simple API that will allow software like APT to support a more direct (i.e. not via ASCOM) connection to the camera, thus allowing some control/knowledge of things like ISO/Shooting-Mode/etc in addition to speeding things up. ~~Once I have something workable I plan on making the code open-source so that others can contribute/enhance/participate.~~

*Update April 26, 2020: Both the ASCOM driver module and the core driver DLL are now available on GitHub*

[ASCOM Driver](https://github.com/dougforpres/ASCOMSonyCameraDriver)

[Camera Driver DLL](https://github.com/dougforpres/SonyCamera)

## OBTAINING THE DRIVER
A Windows installer is available and instructions on downloading and installing the driver are on the driver's [GitHub Wiki](https://github.com/dougforpres/ASCOMSonyCameraDriver/wiki/Installation).
If you have any trouble or questions, please reach out to me. I can't fix it if I don't know it is broken!
That also goes for feature requests or suggestions.

If you end up taking any nice photos, I'd love to see them - feel free to mail me a link!

[^1]: Note on "advertised as having a camera port". This is 100% accurate. Where it falls down is that the additional weight of a camera on the rear of the telescope plays havoc with the auto-guiding/auto-tracking feature. I solved that by using electrical-tape to attach an equivalent weight of AA batteries to the dew-shield on the front of the telescope. I also found that once my camera has been attached to scope, it prevents the scope from pointing high in the sky, as the camera ends up knocking against the mount.
